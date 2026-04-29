# 两个月项目计划

本项目按 8 周推进，原则是先跑通完整闭环，再逐步增加随机化、复杂地形、扰动恢复和系统评估。

核心闭环：

```text
能训练 -> 能回放 -> 能评估 -> 能比较 -> 能导出
```

## 总体安排

| 周次 | 目标 | 主要任务 | 交付物 |
|---|---|---|---|
| Week 1 | 环境与官方基线复现 | 安装 Isaac Sim / Isaac Lab；跑通官方 locomotion 示例；确认 RSL-RL train/play；记录命令 | `docs/01_setup.md`；`scripts/train_official_example.sh`；`scripts/play_official_example.sh`；官方 baseline 运行记录 |
| Week 2 | 机器人资产确认 | 选择 Go2 或 fallback A1/ANYmal；检查 joint names、foot names、default pose、PD gains；确认 reset 稳定 | `docs/02_robot_asset.md`；机器人能正常 spawn 和站立 |
| Week 3 | 最小平地任务 | 定义 command、observation、action、termination；实现 flat locomotion env cfg；写最小 reward | `env_cfg.py`；`observations.py`；`rewards.py`；`terminations.py` |
| Week 4 | PPO 平地 baseline | 配置 RSL-RL PPO；训练 flat baseline；play checkpoint；记录训练曲线和第一版评估 | `scripts/train_flat.sh`；`scripts/play_policy.sh`；`results/flat_eval.csv` |
| Week 5 | Domain randomization | 加 friction、payload、motor strength、observation noise；比较 baseline vs DR policy | `docs/05_domain_randomization.md`；`randomization/events.py`；`results/robustness_eval.csv` |
| Week 6 | Terrain curriculum | 加 rough、slope、stairs；实现简单 curriculum；训练 rough policy；跑 terrain evaluation | `curriculum.py`；`scripts/train_rough.sh`；`results/terrain_eval.csv` |
| Week 7 | Push recovery 与 benchmark | 加 external push；定义 E0-E9 benchmark；比较 B0/B1/B2；整理 failure cases | `scripts/eval_robustness.sh`；benchmark CSV；失败案例记录 |
| Week 8 | 导出与收尾 | 导出 TorchScript/ONNX；实现 standalone inference；整理 README、demo、结果表格 | `scripts/export_policy.sh`；`exported/`；`docs/08_deployment_notes.md`；最终 README/demo |

## 每周工作节奏

建议每周按固定节奏推进：

```text
Mon-Tue: 实现本周核心功能
Wed: 跑训练或验证
Thu: 跑 evaluation / debug
Fri: 整理 docs、results、media
Weekend: 留给长训练或补救
```

## 关键里程碑

### 第 2 周结束

机器人能在 Isaac Lab 中正常 spawn、reset，并保持合理默认站姿。

如果 Go2 asset 在这一阶段阻塞，应及时切换到 A1 或 Isaac Lab 内置 ANYmal，避免资产问题拖垮后续训练。

### 第 4 周结束

必须有第一个 flat walking policy。

要求不一定完美，但必须具备：

- train 命令
- play 命令
- checkpoint
- 基础评估指标
- 初步训练曲线

### 第 6 周结束

必须有复杂地形初步结果。

至少覆盖：

- flat
- rough terrain
- slope 或 stairs

### 第 8 周结束

项目应能展示：

- flat walking
- rough terrain walking
- domain randomization 对比
- push recovery 测试
- benchmark 表格
- policy export

## 风险控制

### 1. 机器人资产阻塞

风险：Go2 asset、关节名、foot body name、PD gains 或 reset pose 花费过多时间。

处理：

- Week 2 内必须做出资产选择。
- Go2 卡住时切换 A1 或 ANYmal。
- 不要为了最终机器人选择破坏整体项目闭环。

### 2. Reward 设计发散

风险：平地任务还没稳定，就加入太多 reward、randomization 或 terrain。

处理：

- Week 3-4 只做最小 flat locomotion。
- 先让机器人稳定站立和前进。
- 每新增一个 reward term，都要能对应一个明确失败模式。

### 3. 训练结果不可复现

风险：命令、seed、config、checkpoint 没有记录。

处理：

- 所有训练入口写进 `scripts/`。
- 关键命令记录到 `docs/01_setup.md` 或对应实验文档。
- 评估结果统一写入 `results/`。

### 4. 只做 demo，没有 benchmark

风险：机器人能走，但无法说明鲁棒性提升。

处理：

- Week 7 必须完成 benchmark matrix。
- 至少比较三组 baseline：

```text
B0: PPO flat baseline
B1: PPO + domain randomization
B2: PPO + domain randomization + terrain curriculum
```

### 5. 导出阶段太晚发现接口问题

风险：policy 只能在训练框架里 play，无法 standalone inference。

处理：

- Week 8 前确认 observation normalization、action scaling、control frequency。
- 导出时必须记录 policy metadata。

## 最小成功标准

两个月结束时，项目至少应满足：

- 能跑通 Isaac Lab train/play/eval。
- 至少一个四足机器人能完成平地速度跟踪。
- 有 domain randomization 对比实验。
- 有至少一种复杂地形评估。
- 有 push recovery 测试。
- 有统一 benchmark CSV。
- 能导出 TorchScript policy。
- README 和 docs 能说明如何复现主要结果。

## 推荐优先级

如果时间紧张，按以下顺序保留功能：

```text
1. flat locomotion train/play/eval
2. domain randomization
3. terrain evaluation
4. push recovery
5. policy export
6. advanced extensions
```

高级方向如 history encoder、teacher-student privileged learning、视觉导航、真机部署都不进入两个月 V1 范围。
