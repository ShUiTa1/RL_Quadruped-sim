# 具身智能入围技术栈补充计划

## 1. 总体判断

你这学期的两个项目组合是合理的：

1. **ForceVLA-like 力控 + VLA 项目**
   - 面向 Seed、银河通用、星尘、自变量、智元、商汤、阿里具身智能等团队。
   - 核心信号是：VLA、多模态策略、力觉/触觉、接触丰富操作、机器人数据闭环。

2. **四足项目迁移到 Isaac Lab**
   - 面向宇树、智元、众擎、傅里叶、NVIDIA、腾讯 Robotics X、机器人仿真/RL 运控岗位。
   - 核心信号是：Isaac Lab、PPO locomotion、domain randomization、鲁棒性评测、deployment-aware policy。

这两个项目不要做成两个孤立方向。建议统一成一条主线：

> 具身策略学习：把视觉、语言、力觉、本体感知转成可部署的机器人动作，并在仿真和真实物理约束下验证。

英文简历上的定位可以是：

> Embodied AI / Robot Learning: multimodal policy learning, force-aware manipulation, and robust locomotion in simulation.

---

## 2. ForceVLA-like 项目需要补什么

这个项目的目标不是简单接一个 VLM，也不是做一个普通机械臂 demo，而是证明你能做：

> VLA + force/tactile modality + contact-rich manipulation + imitation/RL + robot data pipeline

### 2.1 必须补：数据闭环

招聘方最想看到的是你是否理解机器人数据，而不是只会调模型。

你需要做出或至少清楚设计：

- 同步数据集：RGB、深度可选、本体状态、6 轴力/力矩、动作、语言指令。
- 时间戳对齐：不同频率传感器如何同步。
- episode replay / visualization 工具。
- 成功、失败、接触阶段标注。
- force threshold / contact event detection。
- train / validation / test split。
- 按任务、物体、初始位姿做泛化测试。

简历可写：

```text
Built a synchronized multimodal robot dataset with vision, proprioception,
6-axis force-torque feedback, language instructions, and action trajectories
for contact-rich manipulation.
```

### 2.2 必须补：baseline 对比

没有 baseline，项目容易像 demo。建议至少做三组：

- vision-only policy：图像 + 语言 + 本体状态，不加力。
- force late-fusion policy：简单拼接 force feature。
- force-aware policy：MoE、gated fusion、force-conditioned action head 或 fast-slow adapter。

如果时间允许，再加：

- ACT baseline。
- Diffusion Policy baseline。

你不一定要完整复现 ForceVLA，但一定要体现核心问题：

> 视觉和语言不足以解决接触丰富操作，力觉反馈能提供视觉遮挡、接触不确定性和微小卡滞时的关键修正信号。

简历可写：

```text
Compared vision-only, force late-fusion, and force-gated VLA policies;
evaluated contact recovery, insertion success, peak force, and failure modes.
```

### 2.3 任务选择：必须体现力控价值

不要选普通 pick-place。普通抓放任务里，force modality 的价值不明显。

优先选：

- peg-in-hole
- USB / plug insertion
- drawer opening with contact uncertainty
- wiping / polishing
- button pressing
- cap twisting
- cable / deformable object manipulation

推荐优先级：

1. peg-in-hole / 插孔类任务：最容易体现接触和力反馈。
2. wiping / polishing：容易设计 force tracking 指标。
3. drawer opening / button pressing：任务直观，视频展示效果好。

### 2.4 评估指标

不要只写 success rate。建议加入：

- success rate
- peak contact force
- force overshoot
- contact recovery time
- insertion depth error
- number of retries
- trajectory smoothness
- task completion time
- under visual occlusion success rate
- under object pose perturbation success rate

这些指标会让项目从“VLA demo”变成“机器人操作研究”。

### 2.5 最小可行版本

如果时间有限，ForceVLA-like 项目可以这样收敛：

1. 一个 contact-rich 任务，例如 peg-in-hole。
2. 一个 multimodal dataset：RGB + proprioception + force/torque + action + language。
3. 三个模型：vision-only、force concat、force-gated。
4. 指标：success rate、peak force、recovery time、occlusion robustness。
5. 做一段清晰视频：无力反馈失败，加力反馈恢复。

---

## 3. 四足 Isaac Lab 项目需要补什么

当前文件夹里的项目是 PyBullet + Gym + Stable-Baselines3 + A1，包含 CPG、reward、observation、Cartesian PD 等内容。迁移到 Isaac Lab 后，不要只是“换了仿真器”，而要升级成现代 RL locomotion 项目。

目标是证明：

> Isaac Lab + PPO + domain randomization + terrain curriculum + disturbance recovery + deployment-aware policy

### 3.1 必须补：Isaac Lab 训练管线

需要明确包含：

- Isaac Lab environment。
- vectorized simulation。
- PPO training。
- command-conditioned velocity tracking。
- joint position target 或 PD target action。
- observation noise。
- reward terms 清晰可解释。
- policy checkpoint / logging。
- evaluation script。

简历可写：

```text
Migrated a PyBullet A1 quadruped environment to Isaac Lab and trained
PPO locomotion policies with vectorized simulation and command-conditioned
velocity tracking.
```

### 3.2 必须补：domain randomization

这是四足/RL 运控项目的硬信号。

建议加入：

- friction randomization
- base mass / payload randomization
- motor strength randomization
- joint damping randomization
- observation noise
- action delay / latency
- push disturbance
- terrain randomization

简历可写：

```text
Added friction, payload, motor strength, observation noise, latency,
and push randomization to improve policy robustness.
```

### 3.3 必须补：terrain curriculum

训练和评估至少覆盖：

- flat terrain
- rough terrain
- slopes
- stairs
- random blocks
- gaps 可选

重点不是地形数量，而是要有 curriculum 或系统评估。

### 3.4 必须补：鲁棒性评测

训练成功不是卖点，鲁棒性评测才是卖点。

建议做一张表，横轴是扰动条件，纵轴是指标：

- velocity tracking error
- fall rate
- distance before fall
- energy / cost of transport
- recovery time after push
- success rate across terrains

评估条件：

- different friction
- added payload
- external push
- observation noise
- action latency
- rough terrain
- slope / stair

简历可写：

```text
Evaluated policy robustness under rough terrain, payload shifts,
friction variation, observation noise, action latency, and external pushes.
```

### 3.5 关于 sim2sim

sim2sim 可以作为可选项，但不是核心卖点。

它的作用是检查策略是否只适配 Isaac/PhysX 的物理特性，也可以作为没有真机时的中间验证。但如果时间紧，优先级低于：

1. Isaac Lab 稳定训练。
2. domain randomization。
3. terrain curriculum。
4. push recovery。
5. policy export。
6. C++ / ROS2 / deployment-aware inference。

简历中不建议重点写 sim2sim，可以弱化成：

```text
Added deployment-oriented robustness tests across dynamics randomization
and optional MuJoCo validation.
```

### 3.6 是否继续用 A1

如果只是课程项目，A1 可以。但如果目标是宇树或更贴近当前行业生态，建议尽量迁到：

- Unitree Go2
- Unitree G1
- Unitree H1

优先级建议：

1. Go2：四足，迁移成本相对低，和宇树生态相关。
2. G1：人形，行业热度高，但难度更大。
3. H1：也很强，但训练和调参成本可能更高。

如果时间有限，可以先完成 A1/Go2 的 Isaac Lab 版本，再把 README 写清楚后续支持 G1/H1。

---

## 4. 你需要补的技术栈优先级

### 第一优先级

这些直接决定是否像“具身智能/机器人学习候选人”：

```text
PyTorch
VLA / VLM
ACT
Diffusion Policy
imitation learning
Isaac Lab
PPO
domain randomization
Sim2Real awareness
```

### 第二优先级

这些决定你是否像“能上机器人系统的人”：

```text
ROS2
C++
MuJoCo
robot dataset pipeline
teleoperation data
force-torque sensing
contact-rich manipulation
```

### 第三优先级

这些是加分项，尤其对 Seed / 大模型 / 部署岗位：

```text
LoRA / SFT
Hugging Face Transformers
ONNX / TensorRT
real-time inference
Docker
distributed training basics
```

---

## 5. 两个项目的推荐简历写法

### 5.1 ForceVLA-like 项目标题

```text
Force-aware VLA for Contact-rich Robotic Manipulation
```

推荐 bullet：

```text
- Built a multimodal manipulation policy using vision, language,
  proprioception, and 6-axis force-torque feedback.
- Constructed a synchronized robot dataset with RGB, force/torque,
  proprioception, action trajectories, and language instructions.
- Compared vision-only, force late-fusion, and force-gated VLA policies
  on contact-rich tasks such as insertion or wiping.
- Evaluated success rate, peak contact force, contact recovery behavior,
  trajectory smoothness, and robustness under visual occlusion/contact uncertainty.
```

### 5.2 四足 Isaac Lab 项目标题

```text
Isaac Lab Quadruped Locomotion with Robustness-oriented RL
```

推荐 bullet：

```text
- Migrated a PyBullet A1 quadruped environment to Isaac Lab and trained
  PPO locomotion policies with vectorized simulation.
- Designed command-conditioned velocity tracking rewards and deployment-aware
  joint target actions for quadruped locomotion.
- Added terrain curriculum, friction/mass randomization, observation noise,
  action latency, and push disturbances.
- Evaluated velocity tracking, fall rate, energy, and robustness across
  rough terrain, payload shift, friction variation, and external pushes.
```

---

## 6. 最推荐的执行路线

### 阶段 1：先把两个项目都做成能跑的版本

ForceVLA-like：

- 选定一个 contact-rich 任务。
- 跑通 dataset collection / replay。
- 跑通 vision-only baseline。

四足 Isaac Lab：

- 跑通 Isaac Lab 环境。
- 跑通 PPO 训练。
- 生成第一版 locomotion policy。

### 阶段 2：补入围信号

ForceVLA-like：

- 加 force late fusion。
- 加 force-gated / MoE / adapter。
- 加 contact-specific metrics。

四足 Isaac Lab：

- 加 domain randomization。
- 加 terrain curriculum。
- 加 push recovery 和鲁棒性评测。

### 阶段 3：补展示和简历资产

两个项目都要有：

- README。
- 方法图。
- 训练曲线。
- 指标表。
- demo 视频。
- failure case 分析。
- ablation study。

这一步很关键。很多项目技术上还可以，但因为没有展示材料，简历筛选时看不出来。

---

## 7. 最终建议

不要再横向加很多新方向。你现在的两个项目已经覆盖了最重要的两个入围面：

1. **VLA / robot learning / contact-rich manipulation**
2. **Isaac Lab / RL locomotion / robust embodied policy**

接下来最重要的是把它们做深：

- ForceVLA-like 项目补：数据集、baseline、contact-rich 指标。
- 四足项目补：Isaac Lab、PPO、domain randomization、鲁棒性评测。
- 工程栈补：ROS2、C++、MuJoCo、ONNX/TensorRT 中至少两个。

这样你的简历可以同时击中：

- Seed：VLA、force-aware manipulation、robot dataset、imitation/RL。
- 宇树：Isaac Lab、PPO locomotion、domain randomization、deployment-aware policy。
- 智元/银河/星尘：VLA + 操作 + 机器人学习 + 具身策略闭环。

一句话总结：

> 你的主线不是 control，也不是纯 LLM，而是具身策略学习：多模态输入、物理反馈、策略训练和机器人动作闭环。
