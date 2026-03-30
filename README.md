# Wheeltec 机器人项目 (ROS2 Humble)

## 项目简介

这是一个基于 **ROS2 Humble** 的 Wheeltec 移动机器人完整开发项目，集成了六轴机械臂抓取、SLAM建图、自主导航、视觉识别等多种功能。

## 系统要求

- **操作系统**: Ubuntu 22.04 LTS
- **ROS版本**: ROS2 Humble
- **编程语言**: C++ / Python
- **构建工具**: colcon / ament_cmake

## 项目结构

```
src/
├── wheeltec_arm_pick/          # 六轴机械臂抓取控制
├── mini_4wd_six_arm/           # 机械臂URDF模型
├── mini_4wd_six_arm_moveit_config/  # MoveIt配置
├── wheeltec_robot_nav2/        # Navigation2导航配置
├── wheeltec_robot_slam/        # SLAM建图（GMapping/RTAB-Map/SLAM Toolbox）
├── wheeltec_robot_rtab/        # RTAB-Map专用配置
├── wheeltec_lidar_ros2/        # 激光雷达驱动
├── ros2_astra_camera-master/   # 深度相机驱动
├── usb_cam-ros2/               # USB相机驱动
├── ultralytics_ros2/           # YOLO视觉检测
├── wheeltec_tracker_pkg/       # 目标跟踪
├── wheeltec_joy/               # 手柄遥控
├── turn_on_wheeltec_robot/     # 机器人底层驱动
├── navigation2-humble/          # Navigation2导航栈
├── aruco_ros-humble-devel/     # AR码识别
└── qt_ros_test/                 # Qt图形界面
```

## 主要功能模块

### 1. 机械臂抓取 (wheeltec_arm_pick)

- **MoveIt! 运动规划**：使用MoveIt进行逆运动学求解和轨迹规划
- **视觉抓取**：基于颜色识别的自动抓取功能
- **固定位置抓取**：支持运输车场景下的固定位姿抓取
- **多模式控制**：支持手动、自动、视觉伺服等多种控制模式

### 2. SLAM建图

项目集成了三种主流SLAM算法：

#### GMapping
- 基于粒子滤波的2D激光SLAM
- 适用于小场景室内建图
- 经典成熟，ROS生态完善

#### RTAB-Map
- 基于图优化的多传感器SLAM
- 支持激光+视觉融合
- 强大的回环检测能力，适合大场景
- 可构建3D点云地图

#### SLAM Toolbox
- ROS2时代的现代图优化SLAM
- 专为2D激光雷达设计
- 高精度，低累积误差
- 与Navigation2深度集成

### 3. 自主导航 (Navigation2)

- **路径规划**：支持NavFn、Theta*、SMAC等多种规划器
- **路径跟踪**：Regulated Pure Pursuit、MPPI控制器
- **代价地图**：多层代价地图（静态层、膨胀层、障碍物层）
- **行为树**：基于行为树的导航行为编排

### 4. 视觉感知

- **YOLO目标检测**：使用Ultralytics YOLO进行实时物体检测
- **颜色识别**：基于HSV颜色空间的色块检测与跟踪
- **AR码识别**：使用Aruco进行二维码定位
- **人体姿态识别**：支持人体检测与跟踪

## 快速开始

### 环境配置

```bash
# 安装ROS2 Humble
sudo apt update
sudo apt install ros-humble-desktop

# 安装依赖
sudo apt install ros-humble-navigation2 ros-humble-nav2-bringup
sudo apt install ros-humble-moveit ros-humble-moveit-ros-planning-interface
sudo apt install python3-colcon-common-extensions
```

### 编译项目

```bash
cd ~/your_workspace
colcon build --symlink-install
source install/setup.bash
```

### 运行示例

#### 1. 启动机器人底层
```bash
ros2 launch turn_on_wheeltec_robot turn_on_wheeltec_robot.launch.py
```

#### 2. SLAM建图（以SLAM Toolbox为例）
```bash
ros2 launch wheeltec_robot_slam slam_toolbox.launch.py
```

#### 3. 机械臂抓取
```bash
ros2 launch wheeltec_arm_pick test_param.launch.py
```

#### 4. 自主导航
```bash
ros2 launch wheeltec_robot_nav2 bringup_launch.py
```

## 常用SLAM算法对比

| 特性 | GMapping | RTAB-Map | SLAM Toolbox |
|------|----------|-----------|--------------|
| **算法类型** | 粒子滤波 | 图优化 | 图优化 |
| **传感器** | 2D激光 | 激光+视觉 | 2D激光 |
| **回环检测** | 弱 | 强 | 中 |
| **大场景** | 差 | 优 | 良 |
| **计算资源** | 中 | 高 | 中 |
| **ROS2支持** | 一般 | 好 | 原生支持 |
| **3D地图** | 否 | 是 | 否 |

## 面试知识点整理

### SLAM相关（面试常备）

详见 [SLAM算法面试指南](#slam算法面试指南)

### Navigation2相关

- **规划器**：NavFn（Dijkstra）、Theta*、SMAC（State Lattice）
- **控制器**：Regulated Pure Pursuit、MPPI、DWB
- **代价地图**：分层结构、膨胀半径、通胀成本
- **行为树**：NavigateToPose、Recovery Behaviors

### MoveIt相关

- **运动规划**：OMPL（Open Motion Planning Library）
- **运动学求解**：KDL、TRAC-IK
- **碰撞检测**：FCL（Flexible Collision Library）
- **执行器**：FollowJointTrajectory Action

## 开发工具

- **RViz2**：可视化调试
- **rqt**：ROS图形工具集
- **ros2 bag**：数据录制与回放
- **colcon**：构建工具

## 常见问题

### 1. 机械臂无法运动
- 检查MoveIt是否正确初始化
- 确认关节状态话题 `/joint_states` 是否正常
- 检查控制器是否加载成功

### 2. SLAM建图漂移
- 检查激光雷达数据质量
- 调整里程计协方差参数
- 对于大场景，推荐使用RTAB-Map

### 3. 导航无法规划路径
- 检查代价地图是否正确加载
- 确认初始位姿估计是否准确
- 调整膨胀半径参数

## 维护者

Wheeltec 机器人开发团队

## 许可证

BSD 3-Clause License

---

## SLAM算法面试指南

### GMapping

**核心原理**：基于Rao-Blackwellized粒子滤波(RBPF)的2D激光SLAM

- **粒子滤波**：用多个粒子表示机器人位姿概率分布
- **每个粒子独立维护地图**
- **权重更新+重采样**：根据激光匹配调整权重，淘汰低权重粒子
- **地图类型**：占据栅格地图

**优点**：经典成熟，小场景效果好  
**缺点**：粒子数量随场景增长，大场景性能差，回环弱

---

### RTAB-Map (Real-Time Appearance-Based Mapping)

**核心原理**：基于图优化的多传感器SLAM

- **外观-based回环检测**：词袋模型(BoW)进行高效回环检测
- **内存管理**：工作记忆机制，支持大场景长时运行
- **位姿图优化**：g2o/Ceres优化，修正累积误差
- **多传感器融合**：激光+RGB-D/单目/双目

**优点**：回环强，支持3D地图，传感器灵活  
**缺点**：计算消耗大，参数复杂

---

### SLAM Toolbox

**核心原理**：ROS2时代的现代图优化SLAM

- **非线性最小二乘优化**：建图建模为优化问题
- **位姿图优化**：Ceres Solver后端
- **多种模式**：纯建图/定位/继续建图
- **扫描匹配**：ICP/相关性匹配

**优点**：精度高，ROS2原生，维护活跃  
**缺点**：主要支持2D激光

---

**面试回答技巧**：先讲用过哪些，再分别讲原理特点，最后结合场景说选择理由！
