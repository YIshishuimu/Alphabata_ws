AAAmodel: 基于 YOLO 的井字棋视觉识别与博弈决策系统
本项目是一个集成了 深度学习物体检测 与 传统博弈算法 的 ROS 2 智能决策系统。系统能够通过摄像头实时识别井字棋盘状态（九宫格），并利用 Alpha-Beta 剪枝算法计算出 AI 的最优下一步走法。

🌟 核心功能
双模型视觉推理：采用 YOLOv8 掩码模型（Mask）检测棋盘网格，并结合 OBB（旋转框）模型检测棋子。

实时颜色分析：在 HSV 空间下对棋子进行颜色识别（红/蓝），并精准映射到 3x3 矩阵。

高阶 AI 决策：内置 Alpha-Beta 剪枝算法，支持深层搜索以确保 AI 做出最优落子选择。

完全 ROS 2 集成：基于 ROS 2 Jazzy 架构，实现标准化的节点间通信。

🛠️ 环境准备
由于 ROS 2 Jazzy (Ubuntu 24.04) 对 Python 环境有严格要求，请确保按照以下步骤配置：

1. 解决 NumPy 版本冲突 (核心)
ROS 2 的 cv_bridge 依赖 NumPy 1.x。如果系统自动安装了 NumPy 2.x，会导致节点崩溃。

Bash

# 强制降级系统 NumPy 以修复 ABI 冲突
/usr/bin/python3 -m pip install "numpy<2" --break-system-packages
2. 安装必要库
Bash

/usr/bin/python3 -m pip install ultralytics opencv-python --break-system-packages
📥 安装与编译
在你的工作空间（例如 Alphabata_ws）下执行：

Bash

# 进入工作空间
cd ~/Alphabata_ws

# 清理旧的编译产物（推荐）
rm -rf build/ install/ log/

# 编译项目
colcon build --packages-select AAAmodel

# 刷新环境变量
source install/setup.bash
🚀 启动指南
1. 启动完整系统
运行 Launch 文件将同时开启视觉处理节点和 AI 决策节点：

Bash

ros2 launch AAAmodel start_game.launch.py
2. 验证输出
视觉图像：查看话题 /vision/result_image 获取检测结果画面。

矩阵数据：终端查看话题 /vision/matrix 获取 1x9 棋盘数组。

AI 决策：AI 节点（ai_engine）会在终端直接打印推荐的落子坐标（Row, Col）。

📂 项目目录结构
Plaintext

Alphabata_ws/src/AAAmodel/
├── AAAmodel/               # Python 源代码目录
│   ├── vision_node.py      # 视觉处理：图像 -> 矩阵
│   ├── ai_node.py          # AI 包装：接收矩阵 -> 调用算法
│   └── AlphaBataBot.py     # 核心算法：Alpha-Beta 剪枝实现
├── launch/                 # ROS 2 Launch 文件
├── model/                  # YOLO 权重文件 (.pt)
│   ├── shell_best-set.pt   # 棋盘网格检测模型
│   └── kfs_best-set.pt     # 棋子 OBB 检测模型
├── package.xml             # 依赖声明
└── setup.py                # 资源安装配置（包含模型路径映射）
⚠️ 开发者笔记
Python 版本一致性：请确保在 Python 3.12（系统默认）下编译和运行。若激活了 Python 3.10 的 Conda 环境，会导致 C 扩展库加载失败。

路径加载：本项目使用 get_package_share_directory 动态获取模型路径，请勿修改 model/ 文件夹的相对位置。

坐标映射：矩阵下标 0-8 分别对应九宫格从左到右、从上到下的位置。