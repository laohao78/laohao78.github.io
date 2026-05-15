---
layout: notes-post
title: Gazebo 仿真地图建模全流程——从 Blender 到 Pose Graph
date: 2026-05-15
excerpt: 在 Gazebo 仿真中搭建自定义场景，通过 SLAM 生成 pgm 地图与 yaml 配置，精修后发布到 Nav2。完整流程：Blender 建模 → world 配置 → sim 建图 → pgm 精修 → pose graph 生成。
---

目标是：在 Gazebo 中用自己的仿真场景跑 SLAM，生成 pgm 地图 + yaml 配置，精修后发布到 ROS2 Nav2 使用。以下流程在 ROS2 Humble + Gazebo Fortress 环境下验证通过。

## 1. Blender 建模 → Gazebo

用 Blender 搭建自定义模型，导出为 STL。在 Gazebo 中通过 SDF 引用：

```xml
<geometry>
  <mesh>
    <uri>model://my_obstacle/meshes/obstacle.stl</uri>
  </mesh>
</geometry>
```

参考：[Blender + Gazebo 建模教程](https://www.bilibili.com/video/BV1rT4y1P7HN)

## 2. 编写 world 文件

将模型组装成 world，放入带小车的场景中跑起来。Gazebo world 文件指定场景中的静态模型、光照和物理参数。

## 3. Sim 模式建图

在仿真中启动 SLAM 节点，遥控小车遍历场景，生成初始地图。输出四个文件：

- `pgm` — 占据栅格地图（灰度图）
- `yaml` — 地图元信息（分辨率、原点、阈值）
- `data` — 序列化的地图数据
- `posegraph` — 位姿图（用于后续优化）

参考：[pb_rm_simulation 建图流程](https://gitee.com/SMBU-POLARBEAR/pb_rm_simulation/issues/I9427I)

### 验证地图

用 `map_server` 发布地图并在 RViz 中检查：

```sh
ros2 run nav2_map_server map_server --ros-args --param yaml_filename:=map/RMUL2026H.yaml
rviz2
ros2 lifecycle set /map_server configure
ros2 lifecycle set /map_server activate
```

在 RViz 中订阅 `/map` 话题。QoS 的 Durability Policy 必须选 **Transient Local**——选成 Volatile 会错过地图数据。

## 4. 精修 pgm

仿真生成的 pgm 经常有噪点、边界不清晰或需要手动编辑的地方。

在线编辑 pgm 可以使用 [Photopea](https://www.photopea.com/)。编辑后在 [png-to-pgm](https://convertfree.com/cn/png-to-pgm) 转回 pgm 格式。

编辑要点：

- 白色（255）= 自由空间
- 黑色（0）= 障碍物
- 灰色 = 未知区域

同步更新 yaml 中的 `image` 路径指向新的 pgm 文件。如果地图尺寸改变，更新 `resolution` 和原点参数。

## 5. 从精修 pgm 生成 Pose Graph

精修后的 pgm + yaml 需要重新生成 `data` 和 `posegraph` 文件才能用于 Nav2 定位。使用 SLAM Toolbox 的 localization 模式加载精修地图，驱动小车在场景中走一圈即可生成新的 pose graph。

参考：[精修地图后生成 posegraph](https://flowus.cn/lihanchen/share/cd6f2a40-4376-4d57-b9fa-5f22c866b0ba?code=4PP1RS)

## 附：ROS1 Bag → ROS2 DB3

如果需要从 ROS1 的 `.bag` 文件中提取建图数据，需要桥接到 ROS2：

**终端 1** — ROS1 core：
```sh
source /opt/ros/noetic/setup.bash
roscore
```

**终端 2** — ros1_bridge：
```sh
source /opt/ros/noetic/setup.bash
source /opt/ros/foxy/setup.bash
source ~/ros2_ws/install/setup.bash
ros2 run ros1_bridge dynamic_bridge --bridge-all-1to2-topics
```

**终端 3** — 播放 bag（低速率避免丢包）：
```sh
source /opt/ros/noetic/setup.bash
rosbag play --loop --rate 0.3 /path/to/你的bag文件.bag
```

**终端 4** — 录制为 ROS2 格式：
```sh
source /opt/ros/humble/setup.bash
ros2 bag record -a -o output_db3
```

完成。重新编译下载，启动即可使用精修后的地图进行导航。

---

*实践中每一步都可能踩坑——pgm 格式、QoS 配置、坐标原点偏移。遇到问题优先检查 yaml 中的参数与实际 pgm 是否匹配。*
