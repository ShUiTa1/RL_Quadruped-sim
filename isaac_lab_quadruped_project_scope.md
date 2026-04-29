# Robust Quadruped Locomotion Benchmark in Isaac Lab

## 0. 项目定位

项目名称建议：

```text
Robust Quadruped Locomotion Benchmark in Isaac Lab
```

中文：

```text
基于 Isaac Lab 的四足机器人鲁棒运动控制基准项目
```

这个项目不是单纯“训练一个四足机器人会走”，而是构建一个完整的四足 locomotion 项目框架：

```text
robot asset
→ Isaac Lab environment
→ PPO training
→ domain randomization
→ terrain curriculum
→ disturbance recovery
→ evaluation benchmark
→ policy export
→ documentation/demo
```

核心目标是：训练一个速度指令条件下的四足机器人运动策略，并系统评估它在地形变化、动力学失配、观测噪声、控制延迟和外部推扰下的鲁棒性。

---

## 1. 参考的主流项目和方法路线

这个项目可以参考以下主流开源项目和技术路线来 define。

| 参考项目/框架 | 借鉴内容 |
|---|---|
| `legged_gym` | 四足 locomotion 经典结构：PPO、rough terrain、domain randomization、push disturbance、noisy observation、sim-to-real 组件 |
| `Isaac Lab` | 当前更现代的机器人学习框架，支持 RL、imitation learning、motion planning、并行仿真和机器人任务构建 |
| `Isaac Lab + RSL-RL` | 训练接口，适合四足 locomotion 的 PPO 训练和策略导出 |
| `unitree_rl_lab` | 贴近 Unitree 机器人生态，可参考 Go2、H1、G1 等机器人在 Isaac Lab 中的组织方式 |
| 原 PyBullet 项目 | 可保留 observation、reward、CPG/PD 思路，但不建议机械迁移旧代码结构 |

原项目如果是：

```text
PyBullet + Gym + Stable-Baselines3 + A1 + CPG/reward/Cartesian PD
```

那么迁移后不应只是“换仿真器”，而应升级为：

```text
Isaac Lab + PPO + domain randomization + terrain curriculum
+ disturbance recovery + deployment-aware policy
```

---

## 2. Project Goal

构建一个基于 Isaac Lab 的四足机器人鲁棒 locomotion 项目，使机器人能够根据速度指令完成稳定运动，并在不同地形、动力学参数变化、观测噪声、控制延迟和外部推扰下保持较好的速度跟踪与稳定性。

最终项目不只是训练一个 walking policy，而是形成一个完整 pipeline：

```text
1. 机器人模型与仿真资产配置
2. Isaac Lab locomotion task 构建
3. PPO policy training
4. domain randomization
5. terrain curriculum
6. external disturbance recovery
7. robustness evaluation benchmark
8. policy export / deployment-aware inference
9. README、实验表格、demo 视频、failure case 分析
```

---

## 3. Scope

### 3.1 In Scope

项目内明确要做：

#### 1. Isaac Lab 四足环境

- 支持至少一个四足机器人。
- 推荐优先级：
  - Unitree Go2
  - Unitree A1
  - ANYmal
- 支持平地和复杂地形。
- 支持 vectorized simulation。
- 支持 reset、randomization、evaluation script。

#### 2. Command-conditioned locomotion

速度指令：

```text
command = [v_x, v_y, ω_z]
```

其中：

- `v_x`：前向速度；
- `v_y`：侧向速度；
- `ω_z`：偏航角速度。

策略目标：

```text
track command velocity while maintaining stable locomotion
```

#### 3. PPO policy training

使用现成主流 RL 框架，不从零写 PPO。

推荐：

```text
Isaac Lab + RSL-RL PPO
```

可选：

```text
rl_games
skrl
```

#### 4. Domain randomization

至少包含：

```text
friction randomization
base mass / payload randomization
motor strength randomization
joint damping randomization
observation noise
action latency
external push
```

#### 5. Terrain curriculum

地形难度逐步提升：

```text
flat
rough terrain
slope
stairs
random blocks
gaps optional
```

#### 6. Evaluation benchmark

评估指标包括：

```text
velocity tracking error
yaw tracking error
fall rate
episode length
distance traveled
energy / torque cost
action smoothness
recovery time after push
terrain success rate
```

#### 7. Policy export / deployment awareness

至少包含：

```text
export policy to TorchScript or ONNX
standalone inference script
fixed control frequency simulation
freeze observation normalization
action clipping
safety fallback
```

---

### 3.2 Out of Scope

第一版不做：

#### 1. 不做真机部署

可以保留 deployment notes，但不要求真实 Go2/A1 上机。

#### 2. 不做视觉导航

不加入：

```text
RGB
depth
VLA
visual navigation
```

第一版只做 proprioceptive locomotion。

#### 3. 不做人形

G1/H1 可以作为 future work，不作为第一版目标。

#### 4. 不从零写 PPO

PPO 使用现成主流库，重点放在四足任务构建、随机化、地形、评估和工程闭环。

#### 5. 不做 extreme parkour

以下内容可作为未来扩展：

```text
gap jumping
extreme parkour
loco-manipulation
perceptive locomotion with depth
```

---

## 4. Core Problem Definition

英文定义：

```text
Given a desired base velocity command, train a quadruped locomotion policy
that tracks the command while maintaining stability and robustness under
terrain variations, dynamics mismatch, sensor noise, control latency,
and external disturbances.
```

中文定义：

```text
给定目标机体速度指令，训练一个四足机器人运动策略，使其能够在地形变化、
动力学参数失配、传感器噪声、控制延迟和外部扰动下稳定跟踪速度指令。
```

该问题定义对应的模块关系如下：

| 问题元素 | 对应模块 |
|---|---|
| desired velocity command | command sampler |
| quadruped locomotion policy | PPO policy |
| tracks command | tracking reward / evaluation |
| stability | orientation / height / termination |
| terrain variations | terrain curriculum |
| dynamics mismatch | domain randomization |
| sensor noise | observation noise |
| control latency | action delay |
| external disturbances | push recovery |

---

## 5. 推荐项目结构

建议 repo 结构：

```text
RL_Quadruped-sim/
├── README.md
├── docs/
│   ├── 00_project_scope.md
│   ├── 01_setup.md
│   ├── 02_robot_asset.md
│   ├── 03_observation_action.md
│   ├── 04_reward_design.md
│   ├── 05_domain_randomization.md
│   ├── 06_terrain_curriculum.md
│   ├── 07_evaluation_protocol.md
│   └── 08_deployment_notes.md
├── scripts/
│   ├── train_flat.sh
│   ├── train_rough.sh
│   ├── play_policy.sh
│   ├── eval_flat.sh
│   ├── eval_robustness.sh
│   └── export_policy.sh
├── source/
│   └── quadruped_rl/
│       ├── quadruped_rl/
│       │   ├── tasks/
│       │   │   ├── locomotion/
│       │   │   │   ├── env_cfg.py
│       │   │   │   ├── rewards.py
│       │   │   │   ├── observations.py
│       │   │   │   ├── terminations.py
│       │   │   │   └── curriculum.py
│       │   ├── assets/
│       │   ├── configs/
│       │   ├── randomization/
│       │   ├── evaluation/
│       │   └── utils/
│       └── setup.py
├── results/
│   ├── training_curves/
│   ├── flat_eval.csv
│   ├── robustness_eval.csv
│   ├── terrain_eval.csv
│   └── ablations.md
└── media/
    ├── flat_walk.gif
    ├── rough_terrain.gif
    ├── stairs.gif
    └── push_recovery.gif
```

---

# 6. Work Packages

## WP0：项目初始化与基线复现

### 目标

先不改算法，先保证 Isaac Lab 能跑，官方或现成 locomotion 示例能训练和回放。

### 任务

1. 安装 Isaac Sim / Isaac Lab。
2. 跑通 Isaac Lab 官方 locomotion task。
3. 跑通 RSL-RL 训练流程。
4. 确认 GPU 并行仿真正常。
5. 记录安装和运行命令。
6. 确认 `train`、`play`、`eval` 基本 workflow。

### 产出

```text
docs/01_setup.md
scripts/train_official_example.sh
scripts/play_official_example.sh
media/official_example.gif
```

### 完成标准

```text
能训练一个官方 locomotion policy
能 play checkpoint
能看到 TensorBoard / log
能复现一次完整训练命令
```

---

## WP1：机器人模型与任务选择

### 目标

确定项目机器人对象。

推荐：

```text
Primary target: Unitree Go2
Fallback: A1 / ANYmal
```

### 为什么推荐 Go2

Go2 更贴近当前四足机器人产业生态，尤其是 Unitree 生态。A1 更适合快速起步，但作为最终展示，Go2 更有现实意义。

### 任务

1. 确定 robot asset。
2. 检查关节命名。
3. 确认默认站姿。
4. 配置 PD gains。
5. 确认 joint limits / torque limits。
6. 确认 foot body names。
7. 确认 reset pose。
8. 写 robot asset 文档。

### 产出

```text
docs/02_robot_asset.md
source/quadruped_rl/assets/go2/
```

### 完成标准

```text
机器人能正常 spawn
reset 不炸
关节方向正确
PD target 能控制站姿
无明显穿模或爆炸
```

---

## WP2：定义最小平地 Locomotion 任务

### 目标

完成最小 RL 任务：平地速度跟踪。

### Task Definition

```text
Command:
  c = [v_x, v_y, ω_z]

Policy input:
  proprioceptive observation + command

Policy output:
  12-dim joint position target offset

Low-level control:
  PD tracking
```

### Observation Scope

第一版 observation：

```text
base angular velocity
projected gravity
command velocity
joint position error
joint velocity
previous action
```

不建议第一版加入：

```text
vision
height map
history encoder
privileged information
```

### Action Scope

使用 residual joint position target：

```text
target_q = default_q + action_scale * action
```

### Reward Scope

最小 reward：

```text
linear velocity tracking
yaw rate tracking
orientation penalty
base height penalty
torque penalty
joint velocity penalty
action rate penalty
termination penalty
```

### 产出

```text
docs/03_observation_action.md
docs/04_reward_design.md
source/quadruped_rl/tasks/locomotion/env_cfg.py
source/quadruped_rl/tasks/locomotion/rewards.py
scripts/train_flat.sh
scripts/play_policy.sh
```

### 完成标准

```text
flat terrain 上能稳定站立和前进
能跟踪至少 vx 和 yaw_rate
训练曲线收敛
play 里能看到自然步态
```

---

## WP3：训练基线 PPO Policy

### 目标

建立第一个可用 baseline。

### 方法

使用：

```text
Isaac Lab + RSL-RL PPO
```

### 任务

1. 设置 `num_envs`。
2. 设置 PPO config：
   ```text
   learning rate
   num steps per env
   entropy coefficient
   clip range
   gamma
   lambda
   network size
   max iterations
   ```
3. 记录训练曲线。
4. 保存 checkpoint。
5. 写 play script。
6. 做第一版 flat evaluation。

### 产出

```text
scripts/train_flat.sh
scripts/play_policy.sh
results/training_curves/flat_baseline.png
results/flat_eval.csv
media/flat_walk.gif
```

### 完成标准

```text
能完成 3 个 command:
  forward walking
  turning
  mixed velocity command

速度跟踪误差可计算
fall rate 可计算
```

---

## WP4：Domain Randomization

### 目标

让 policy 不只适配固定仿真参数。

### Randomization Scope

第一批必须做：

```text
friction randomization
base mass / payload randomization
motor strength randomization
observation noise
```

第二批做：

```text
joint damping randomization
action latency
external push
```

第三批可选：

```text
terrain property randomization
sensor dropout
actuator model
```

### 每项 randomization 必须定义

不要只写“加 randomization”。每项都要写清楚：

```text
randomized variable
range
when applied: reset / interval / every step
training only or eval also
```

示例：

```text
friction:
  range: [0.4, 1.2]
  applied: at reset
  eval: fixed bins [0.3, 0.5, 0.8, 1.2]

payload:
  range: [0, 3 kg]
  applied: at reset
  eval: [0 kg, 1 kg, 2 kg, 3 kg]
```

### 产出

```text
docs/05_domain_randomization.md
source/quadruped_rl/randomization/events.py
scripts/train_flat_dr.sh
results/robustness_eval.csv
```

### 完成标准

```text
baseline PPO vs PPO + DR 有对比
至少测试 friction / payload / motor strength / obs noise
结果有表格
```

---

## WP5：Terrain Curriculum

### 目标

从平地 locomotion 扩展到复杂地形。

### Terrain Scope

按阶段加：

```text
Level 0: flat
Level 1: rough terrain
Level 2: slopes
Level 3: stairs
Level 4: random blocks
Level 5: gaps optional
```

### Curriculum Rule

可以采用简单规则：

```text
if robot travels enough distance without falling:
    move to harder terrain
elif robot falls too often:
    move to easier terrain
else:
    keep same level
```

### 任务

1. 生成 terrain levels。
2. 设置 terrain difficulty。
3. 写 terrain evaluation。
4. 对比：
   ```text
   without curriculum
   with curriculum
   ```
5. 做 demo 视频。

### 产出

```text
docs/06_terrain_curriculum.md
source/quadruped_rl/tasks/locomotion/curriculum.py
scripts/train_rough.sh
scripts/eval_terrain.sh
results/terrain_eval.csv
media/rough_terrain.gif
media/stairs.gif
```

### 完成标准

```text
flat / rough / slope / stairs 至少 4 类地形有评估
terrain success rate 有表格
至少一个 rough terrain demo
```

---

## WP6：External Disturbance and Recovery

### 目标

验证 policy 被推扰后能否恢复。

### Disturbance Scope

```text
random push direction
random push magnitude
random push duration
random push timing
```

### Evaluation Metrics

```text
fall rate after push
recovery time
post-push velocity tracking error
base attitude deviation
```

### 产出

```text
scripts/eval_push_recovery.sh
results/push_recovery_eval.csv
media/push_recovery.gif
```

### 完成标准

至少比较：

```text
PPO baseline
PPO + DR
PPO + DR + terrain curriculum
```

并在同样 push 条件下统计：

```text
fall rate
recovery time
post-push tracking error
```

---

## WP7：系统化 Evaluation Benchmark

### 目标

这是项目核心。要把它做成 benchmark，而不是 demo。

### Evaluation Matrix

测试维度：

```text
E0 nominal flat
E1 friction variation
E2 payload variation
E3 motor weakness
E4 observation noise
E5 action latency
E6 external push
E7 rough terrain
E8 slope
E9 stairs
```

指标：

```text
velocity tracking error
yaw tracking error
fall rate
episode length
distance traveled
mean torque
energy proxy
action smoothness
recovery time
terrain success rate
```

### Baselines

至少三组：

```text
B0: PPO flat baseline
B1: PPO + domain randomization
B2: PPO + domain randomization + terrain curriculum
```

可选第四组：

```text
B3: PPO + DR + curriculum + history encoder
```

### 产出

```text
docs/07_evaluation_protocol.md
scripts/eval_robustness.sh
results/robustness_eval.csv
results/terrain_eval.csv
results/ablations.md
```

### 完成标准

```text
每个 baseline 至少多 episode 评估
至少一张 summary table
至少一张 radar/bar plot
有 failure case 分析
```

---

## WP8：Deployment-aware Policy Export

### 目标

体现 policy 不只是仿真里 play，还要能导出、固定频率推理、控制 action 范围。

### Scope

第一版：

```text
export policy to TorchScript
standalone inference script
fixed control frequency simulation
freeze observation normalization
action clipping
safety fallback
```

第二版可选：

```text
ONNX export
ROS2 node skeleton
MuJoCo sim2sim validation
```

### 产出

```text
scripts/export_policy.sh
source/quadruped_rl/evaluation/inference.py
docs/08_deployment_notes.md
exported/policy.pt
exported/policy.onnx optional
```

### 完成标准

```text
不用训练框架也能 load policy
能给 observation 输出 action
控制频率和 action scaling 写清楚
```

---

# 7. Advanced Scope：更前沿的扩展方向

基础项目完成后，再做高级分支。不要一开始就做。

## Advanced Option A：History Encoder / Adaptation Module

### 目标

让 policy 从历史 proprioception 中隐式估计当前 dynamics。

### 方法定义

```text
history = [obs_{t-k}, action_{t-k}, ..., obs_t]
z = encoder(history)
action = policy(obs_t, z)
```

### 对比

```text
B0 PPO
B1 PPO + DR
B2 PPO + DR + history encoder
```

### 测试

```text
unseen payload
unseen friction
motor weakness
latency
push recovery
```

### 价值

这个方向比普通 domain randomization 更高级，因为它让 policy 具备某种隐式 dynamics adaptation 能力。

---

## Advanced Option B：Teacher-Student Privileged Learning

### Teacher 输入

```text
obs + privileged_info
```

privileged info 包括：

```text
friction
payload
motor strength
terrain height
external disturbance
```

### Student 输入

```text
obs history only
```

### 目标

```text
student learns to infer hidden dynamics from history
```

### 产出

```text
teacher checkpoint
student checkpoint
distillation loss curve
unseen dynamics evaluation
```

---

## Advanced Option C：Perceptive Locomotion

### 输入

```text
proprioception + height scan / depth map
```

### 任务

```text
stairs
gaps
stepping stones
rough terrain
```

### 注意

这个方向更难，会拉长周期。建议作为 future work，不作为主线 MVP。

---

# 8. 推荐项目里程碑

## Milestone 1：MVP Locomotion

时间：1–2 周

目标：

```text
Go2/A1 flat PPO 能走
```

产出：

```text
flat_walk.gif
training curve
basic reward doc
```

---

## Milestone 2：Robustness Version

时间：2–3 周

目标：

```text
加 domain randomization
完成 robustness eval
```

产出：

```text
baseline_vs_DR table
friction / payload / motor / noise tests
```

---

## Milestone 3：Terrain Version

时间：2–3 周

目标：

```text
rough / slope / stairs
curriculum
terrain eval
```

产出：

```text
rough terrain demo
stairs demo
terrain success table
```

---

## Milestone 4：Benchmark Version

时间：1–2 周

目标：

```text
系统评估 + ablation + README
```

产出：

```text
robustness_eval.csv
ablations.md
README
demo videos
```

---

## Milestone 5：Advanced Version

时间：可选 2–4 周

目标：

```text
history encoder / teacher-student
```

产出：

```text
adaptation policy
unseen dynamics comparison
```

---

# 9. 最终 Deliverables

## Code

```text
Isaac Lab task extension
training scripts
evaluation scripts
policy export scripts
```

## Models

```text
flat baseline checkpoint
DR checkpoint
terrain curriculum checkpoint
optional adaptation checkpoint
```

## Results

```text
training curves
robustness tables
terrain evaluation
push recovery evaluation
ablation study
```

## Media

```text
flat walking demo
rough terrain demo
stairs demo
push recovery demo
failure cases
```

## Docs

```text
setup
problem/scope
observation/action
reward design
domain randomization
terrain curriculum
evaluation protocol
deployment notes
```

---

# 10. 最推荐的最终 Scope 版本

建议主线 scope 固定成：

```text
Main scope:
  Isaac Lab + Go2/A1 quadruped
  PPO command-conditioned locomotion
  joint position target action
  flat + rough + slope + stairs
  domain randomization
  external push recovery
  robustness benchmark
  policy export

Advanced optional:
  history encoder / teacher-student adaptation
```

不要做：

```text
vision
VLA
real robot deployment
humanoid
extreme parkour
from-scratch PPO
```

---

# 11. 最终一句话项目定义

英文：

```text
A complete Isaac Lab-based quadruped locomotion benchmark that trains,
randomizes, evaluates, and exports PPO policies for command-conditioned
locomotion under terrain and dynamics variations.
```

中文：

```text
一个完整的 Isaac Lab 四足机器人运动控制基准项目：训练速度指令条件下的 PPO locomotion 策略，并在地形变化、动力学随机化和外部推扰下系统评估鲁棒性，最终支持策略导出和部署意识验证。
```

---

# 12. 简历表述建议

## 项目标题

```text
Robust Quadruped Locomotion Benchmark in Isaac Lab
```

## Bullet 版本

```text
- Built an Isaac Lab-based quadruped locomotion benchmark with PPO training,
  command-conditioned velocity tracking, terrain curriculum, and domain randomization.

- Designed proprioceptive observations, residual joint position target actions,
  and reward terms for velocity tracking, stability, smoothness, energy efficiency,
  and contact behavior.

- Added friction, payload, motor strength, observation noise, action latency,
  and external push randomization to evaluate deployment-relevant robustness.

- Developed evaluation scripts for tracking error, fall rate, energy proxy,
  terrain success rate, and push recovery time across flat, rough, slope,
  and stair terrains.

- Exported trained policies for standalone inference with fixed control frequency,
  action clipping, and deployment-oriented safety checks.
```

## 如果加入高级模块

```text
- Implemented a history-based adaptation module that infers latent dynamics
  from proprioceptive trajectories to improve robustness under unseen payload,
  friction, and actuator mismatch.
```
