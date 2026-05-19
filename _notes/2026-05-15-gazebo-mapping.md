---
layout: notes-post
title: Gazebo 仿真建图
date: 2026-05-15
excerpt: 写完一个仿真场景后，接下来是什么？把它变成一张能让机器人导航的地图。这条管线每一步都有坑：pgm 格式、QoS 配置、坐标原点偏移。本文是一次完整的踩坑记录。
---

建完仿真场景只是第一步。真正让机器人能在里面导航，需要把场景变成一张 Nav2 能用的地图——pgm 图片、yaml 参数、以及 AMCL 定位需要的 data 和 posegraph。整个管线的文件流转是这样的：

```
stl → sdf → world → pgm + yaml + data + posegraph（初始）
                         ↓
                   精修 pgm + yaml
                         ↓
               pgm + yaml + data + posegraph（精修后）
                         ↓
                     Nav2 可用
```

下面按照这个顺序，每一步标注用的工具、踩过的坑。

## Blender → STL

如果你需要自定义障碍物或建筑，在 Blender 里建模后导出 STL。Gazebo 的 SDF 格式里可以直接引用 STL 作为 mesh geometry。不需要转成 Collada 或 DAE——STL 足够。

这一步的关键不是建得好看，是**量对尺寸**。Gazebo 用的单位是米，Blender 里设好比例。尺寸不准后面建出来的地图会和实际机器人不匹配。

## STL → World

把模型放进 World 里。一个最小的 World 文件包含：模型的位置和朝向、光照、地面平面。在 World 里放入一个带差分驱动的机器人模型（比如 TurtleBot 或 RM 标准车），确认可以遥控移动——这是建图的前提。

Gazebo 启动后 `ros2 topic list` 能看到 `/cmd_vel` 和 `/odom` 就说明机器人到位了。

## World → pgm + yaml + data + posegraph

这是整个管线最核心的一步。启动 SLAM 节点——SLAM Toolbox 或 Cartographer——遥控小车遍历整个场景。SLAM 一边接收激光雷达数据一边实时建图。

遍历时注意几点：

- **走慢一点**。角速度太大会导致激光数据错位，地图扭曲
- **覆盖全面**。每个角落都要走到，否则地图会有未知区域（灰色）
- **回环闭合**。在起点附近多转几圈，帮助 SLAM 做回环检测减少累积误差

遍历结束后保存地图。SLAM Toolbox 提供了保存的 service call，会生成四个文件：

| 文件 | 内容 | 能否手动编辑 |
|------|------|------------|
| `pgm` | 占据栅格图，灰度图片 | **能**（这是精修的目标） |
| `yaml` | 分辨率、原点坐标、阈值参数 | **能**（编辑 pgm 后需要同步） |
| `data` | 序列化地图数据，C++ 二进制 | 不能 |
| `posegraph` | 位姿图，每个关键帧的位姿和约束 | 不能 |

理解这四个文件的关系很重要：pgm 和 yaml 是"源文件"，data 和 posegraph 是从它们生成的"编译产物"。精修的时候只需要改 pgm 和 yaml，然后用 SLAM Toolbox 的 localization 模式重新生成 data 和 posegraph。

### 验证：在 RViz 中查看

把 yaml 路径传给 map_server：

```sh
ros2 run nav2_map_server map_server --ros-args --param yaml_filename:=map/RMUL2026H.yaml
rviz2
ros2 lifecycle set /map_server configure
ros2 lifecycle set /map_server activate
```

在 RViz 里点 Add → By topic → `/map`。如果看不到地图，检查两件事：(1) yaml 里的 `image:` 路径对不对——**相对路径是相对于 yaml 文件所在目录**，不是相对于工作目录；(2) RViz 的 Map display 的 QoS → Durability 必须选 **Transient Local**，选 Volatile 会错过 map_server 发出的地图数据。这个坑浪费了我半小时。

## 精修 pgm

仿真建的图经常有这些问题：墙的边界不清晰（激光在边缘散射），没走到的区域是灰色未知，走道宽度和实际不一致。

不需要重新跑仿真。直接编辑 pgm。

用 [Photopea](https://www.photopea.com/)——在线的图片编辑器，打开 pgm 直接改：

- 把噪点和杂散像素涂成白色（自由空间）或黑色（障碍物）
- 把没走到但实际是墙的地方手动画黑
- 把走道宽度对齐实际尺寸

编辑完导出。如果 Photopea 导出的是 png，用 [这个转换工具](https://convertfree.com/cn/png-to-pgm) 转回 pgm 格式。如果地图尺寸变了——精修过程中可能裁切了边缘——yaml 里的 `resolution` 和 `origin` 要重新算。**精修后最常见的错误就是 yaml 参数和 pgm 的像素尺寸不匹配。**

## 精修 pgm → 重新生成 Pose Graph

data 和 posegraph 是旧地图生成的，精修后的 pgm 和它们不一致。需要删掉旧的 data 和 posegraph，用 SLAM Toolbox localization 模式从精修 pgm 重新生成。

流程是这样的：让 SLAM Toolbox 加载精修后的 pgm + yaml 作为先验地图，然后在仿真里再开一次小车，SLAM Toolbox 会在已有的地图上进行定位优化，同时记录新的位姿图。走一圈之后保存，输出的 data 和 posegraph 就和精修后的 pgm 匹配了。

## 最终验证

四种文件都有之后，启动 Nav2 全套：

```sh
ros2 launch nav2_bringup navigation_launch.py map:=map/RMUL2026H.yaml
```

在 RViz 里给一个 2D Pose Estimate（初始化位置），然后给 Nav2 Goal（目标点）。如果机器人能规划路径并走到目标点，建图成功。如果 AMCL 的粒子散成一片——回去检查 yaml 里的 `resolution` 对不对。

## 附：ROS1 Bag 转 ROS2 DB3

如果你有 ROS1 的 bag 文件想拿来建图，需要桥接转到 ROS2。四个终端各司其职：

启动 ROS1 roscore、ros1_bridge 桥接所有话题、低速回放 bag、录制为 ROS2 db3 格式。桥接成功的关键是**同时 source noetic 和 foxy 的环境**，并且先把 ros1_bridge 的工作空间编译好。如果桥接后 ros2 topic list 看不到对应的激光雷达话题，99% 是环境变量没 source 对。

---

整条管线走通一遍之后回头看，核心其实就三件事：**建图时走慢一点、精修后重新生成 data 和 posegraph、yaml 参数永远和 pgm 像素对齐。** 剩下都是反复验证。

