---
layout: page
title: 项目
permalink: /projects/
description: "6 个机器人落地项目：强化学习、导航与 SLAM、机械臂视觉抓取、具身 AI。"
---

<p class="projects-intro">6 个落地项目，覆盖强化学习、导航与 SLAM、机械臂视觉抓取、具身 AI。每个项目都附可运行的代码与说明。</p>

<div class="card-grid card-grid-2">
  <a class="card" href="https://github.com/laohao78/Lite3_Handstand_RL">
    <span class="card-title">Lite3 Handstand RL</span>
    <span class="card-desc">基于 DeepRobotics 绝影 Lite3 四足机器人的倒立控制。复现自 Legged_gym_handstand，在 Isaac Gym 中完成 RL 训练，sim-to-sim 迁移到 MuJoCo。</span>
    <div class="project-meta">
      <span class="project-tag">RL</span>
      <span class="project-tag">Sim-to-Sim</span>
      <span class="project-tag">MuJoCo</span>
      <span class="project-tag">Isaac Gym</span>
    </div>
    <span class="card-meta">源码 &rarr;</span>
  </a>

  <a class="card" href="https://github.com/laohao78/Lerobot-Mujoco">
    <span class="card-title">Lerobot-Mujoco</span>
    <span class="card-desc">具身 AI 学习框架。基于 LeRobot 和 MuJoCo 的完整教程，包含 ACT、pi0、SmolVLA 模型的复现——数据采集、训练与部署全流程。</span>
    <div class="project-meta">
      <span class="project-tag">LeRobot</span>
      <span class="project-tag">MuJoCo</span>
      <span class="project-tag">ACT</span>
      <span class="project-tag">pi0</span>
      <span class="project-tag">SmolVLA</span>
    </div>
    <span class="card-meta">源码 &rarr;</span>
  </a>

  <a class="card" href="https://github.com/laohao78/ROS2_RM_Navigation">
    <span class="card-title">ROS2 RM Navigation</span>
    <span class="card-desc">专为 RoboMaster 竞赛设计的机器人导航方案。多传感器融合定位（LIO/SLAM）、动态避障、支持多种标准场地，含完整实车适配指南。</span>
    <div class="project-meta">
      <span class="project-tag">ROS2</span>
      <span class="project-tag">Nav2</span>
      <span class="project-tag">SLAM</span>
      <span class="project-tag">Livox</span>
      <span class="project-tag">RoboMaster</span>
    </div>
    <span class="card-meta">源码 &rarr;</span>
  </a>

  <a class="card" href="https://github.com/laohao78/ROS1_ARM_Simulation">
    <span class="card-title">ROS1 ARM Simulation</span>
    <span class="card-desc">完整机械臂仿真平台。从相机标定、手眼标定到视觉抓取全流程，实现多种抓取算法（几何法 + 机器学习 GPD）。</span>
    <div class="project-meta">
      <span class="project-tag">ROS1</span>
      <span class="project-tag">Gazebo</span>
      <span class="project-tag">MoveIt</span>
      <span class="project-tag">OpenCV</span>
    </div>
    <span class="card-meta">源码 &rarr;</span>
  </a>

  <a class="card" href="https://github.com/laohao78/ROS2_LightMap_Delivery">
    <span class="card-title">ROS2 LightMap Delivery</span>
    <span class="card-desc">最后一公里自主配送。不用高精地图，仅用开源 OSM + LLM 实现语义导航和智能门牌识别。</span>
    <div class="project-meta">
      <span class="project-tag">ROS2</span>
      <span class="project-tag">LLM</span>
      <span class="project-tag">OpenStreetMap</span>
    </div>
    <span class="card-meta">源码 &rarr;</span>
  </a>

  <a class="card" href="https://github.com/laohao78/ROS1_SW_Moveit">
    <span class="card-title">ROS1 SW MoveIt</span>
    <span class="card-desc">SolidWorks → Gazebo 端到端机械臂建模与控制。CAD 模型到 URDF 转换，Realsense 相机仿真集成。</span>
    <div class="project-meta">
      <span class="project-tag">ROS1</span>
      <span class="project-tag">MoveIt</span>
      <span class="project-tag">SolidWorks</span>
      <span class="project-tag">URDF</span>
    </div>
    <span class="card-meta">源码 &rarr;</span>
  </a>
</div>
