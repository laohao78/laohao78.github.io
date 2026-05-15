---
layout: notes-post
title: Gazebo 仿真地图建模——从 Blender 到 Pose Graph 的全管道
date: 2026-05-15
excerpt: 在 Gazebo 中搭建自定义仿真场景，跑 SLAM 生成 pgm 地图，精修后发布到 Nav2。每一步标注文件格式流转：stl → sdf → world → pgm/yaml/data/posegraph。
---

## 总览

```
stl → sdf → world → pgm + yaml + data + posegraph
                        ↓
                  精修 pgm + yaml
                        ↓
              pgm + yaml + data + posegraph（精修后）
                        ↓
                  Nav2 可用
```

---

## 1. Blender 建模

`stl`

用 Blender 搭建自定义场景模型，导出为 STL。

[B站教程：Blender + Gazebo 搭建自定义模型](https://www.bilibili.com/video/BV1rT4y1P7HN)

---

## 2. 编写 world，跑通带小车的 Gazebo

`sdf → world`

将 STL 模型通过 SDF 引用到 world 文件中，放入带差分驱动小车的场景。启动 Gazebo，确认模型加载正常、小车可以遥控移动。

---

## 3. Sim 模式建图，拿到初始地图

`world → pgm + yaml + data + posegraph`

启动 SLAM 节点，遥控小车遍历场景，生成四件套：

| 文件 | 说明 |
|------|------|
| `pgm` | 占据栅格地图（灰度图，白=自由，黑=障碍，灰=未知） |
| `yaml` | 地图元信息（resolution、origin、occupied_thresh、free_thresh） |
| `data` | 序列化的地图数据 |
| `posegraph` | SLAM 优化后的位姿图 |

[参考：pb_rm_simulation 建图 Issue](https://gitee.com/SMBU-POLARBEAR/pb_rm_simulation/issues/I9427I)

### 在 RViz 中验证地图

```sh
ros2 run nav2_map_server map_server --ros-args --param yaml_filename:=map/RMUL2026H.yaml
```

打开 RViz 后执行：

```sh
rviz2
ros2 lifecycle set /map_server configure
ros2 lifecycle set /map_server activate
```

在 RViz 中添加 Map display，订阅 `/map` 话题。**QoS Durability Policy 必须选 Transient Local**——选 Volatile 会错过地图数据。

---

## 4. 精修 pgm

`pgm + yaml → 精修 pgm + yaml`

仿真生成的 pgm 可能有噪点或边界不清晰。用以下工具编辑：

| 步骤 | 工具 |
|------|------|
| 编辑 pgm | [Photopea](https://www.photopea.com/)（在线 PS） |
| 格式转换 | [png-to-pgm](https://convertfree.com/cn/png-to-pgm) |

编辑要点：

- 白色（255）= 自由空间
- 黑色（0）= 障碍物
- 灰色（100-200）= 未知区域

精修后更新 yaml 中的 `image:` 路径指向新 pgm。如果地图尺寸改变，同步调整 `resolution` 和 `origin`。

---

## 5. 从精修 pgm 生成 Pose Graph

`精修 pgm + yaml → pgm + yaml + data + posegraph`

精修后的 pgm 需要重新生成 `data` 和 `posegraph`，才能用于 Nav2 的 AMCL 定位。

使用 SLAM Toolbox 的 localization 模式加载精修地图，驱动小车在场景中走一圈，自动生成新的 pose graph。

[参考：精修地图后重新生成 posegraph](https://flowus.cn/lihanchen/share/cd6f2a40-4376-4d57-b9fa-5f22c866b0ba?code=4PP1RS)

---

## 附：ROS1 Bag → ROS2 DB3

`ros1_bag → ros2_db3`

需要从 ROS1 bag 提取建图数据时，用 `ros1_bridge` 桥接转换。

| 终端 | 命令 | 说明 |
|------|------|------|
| 1 | `source /opt/ros/noetic/setup.bash && roscore` | ROS1 core |
| 2 | 加载 noetic + foxy 环境，`ros2 run ros1_bridge dynamic_bridge --bridge-all-1to2-topics` | 桥接 |
| 3 | `source /opt/ros/noetic/setup.bash && rosbag play --loop --rate 0.3 文件.bag` | 低速率播放 |
| 4 | `source /opt/ros/humble/setup.bash && ros2 bag record -a -o output_db3` | 录制 |

---

最终输出：精修后的 `pgm` + `yaml` + `data` + `posegraph`，直接用于 Nav2 的 `map_server` 和 `amcl` 节点。
