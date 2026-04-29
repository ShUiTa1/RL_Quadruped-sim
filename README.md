# Robust Quadruped Locomotion Benchmark in Isaac Lab

Isaac Lab based project for training and evaluating robust quadruped locomotion policies.

Target pipeline:

```text
robot asset -> Isaac Lab task -> PPO training -> domain randomization
-> terrain curriculum -> robustness evaluation -> policy export
```

The first version focuses on command-conditioned proprioceptive locomotion:

```text
command = [v_x, v_y, omega_z]
```

## Status

This repository is being initialized from a project-scope document and an older PyBullet/A1 reference project.

- Project definition: `isaac_lab_quadruped_project_scope.md`
- New Isaac Lab project code: `source/quadruped_rl/`
- Planning and experiment notes: `docs/`
- Old reference project: `ref/lr-quadruped-sim-master/`

## Repository Layout

```text
RL_Quadruped-sim/
├── docs/       # design notes, setup, task definition, evaluation protocol
├── scripts/    # reproducible train/play/eval/export commands
├── source/     # Isaac Lab extension package and project code
├── results/    # summarized experiment outputs and benchmark tables
├── media/      # demo images/videos for reports and README
├── exported/   # exported policies and inference metadata
├── ref/        # reference material and old PyBullet project
└── README.md
```

## Main Folders

### `docs/`

Project decisions and experiment protocol.

Important files:

- `00_project_scope.md`: short project scope.
- `01_setup.md`: Isaac Sim / Isaac Lab setup and verified commands.
- `02_robot_asset.md`: robot asset, joints, feet, limits, default pose.
- `03_observation_action.md`: observation and action definitions.
- `04_reward_design.md`: reward terms.
- `05_domain_randomization.md`: randomization variables and ranges.
- `06_terrain_curriculum.md`: terrain levels and curriculum rule.
- `07_evaluation_protocol.md`: benchmark cases, baselines, metrics.
- `08_deployment_notes.md`: policy export and inference notes.
- `09_workflow.md`: development workflow.
- `10_task_breakdown.md`: WP0-WP8 backlog.
- `11_two_month_plan.md`: 8-week project schedule and milestones.

### `scripts/`

Thin command wrappers for reproducible runs.

Expected scripts:

- `train_official_example.sh`
- `play_official_example.sh`
- `train_flat.sh`
- `train_rough.sh`
- `play_policy.sh`
- `eval_flat.sh`
- `eval_robustness.sh`
- `export_policy.sh`

Training and evaluation logic should live in `source/`; scripts should mainly hold commands and arguments.

### `source/`

Project implementation as an Isaac Lab extension package.

```text
source/quadruped_rl/
├── setup.py
└── quadruped_rl/
    ├── tasks/          # Isaac Lab task definitions
    ├── assets/         # robot assets and asset configs
    ├── configs/        # training/evaluation configs
    ├── randomization/  # domain randomization events
    ├── evaluation/     # benchmark and inference utilities
    └── utils/          # shared helpers
```

Locomotion task files:

```text
source/quadruped_rl/quadruped_rl/tasks/locomotion/
├── env_cfg.py
├── rewards.py
├── observations.py
├── terminations.py
└── curriculum.py
```

### `results/`

Clean experiment outputs, such as:

- `flat_eval.csv`
- `robustness_eval.csv`
- `terrain_eval.csv`
- `ablations.md`
- `training_curves/`

Large raw logs and checkpoints should usually stay out of Git.

### `media/`

Demo artifacts for documentation:

- flat walking
- rough terrain
- stairs
- push recovery

### `exported/`

Exported policies and metadata:

- TorchScript policy
- optional ONNX policy
- observation/action normalization notes
- control frequency and action scaling

### `ref/`

Reference material only. The old PyBullet project is useful for studying A1 assets, reward ideas, observations, CPG, and PD control, but the new implementation should follow Isaac Lab patterns.

## Work Plan

```text
WP0  Run official Isaac Lab locomotion baseline
WP1  Select and validate robot asset
WP2  Build minimal flat locomotion task
WP3  Train PPO flat baseline
WP4  Add domain randomization
WP5  Add terrain curriculum
WP6  Add push recovery evaluation
WP7  Build robustness benchmark
WP8  Export deployment-aware policy
```

Start with WP0. Do not port the old PyBullet environment before an official Isaac Lab train/play loop works.

## Development Rule

- Put project decisions in `docs/`.
- Put runnable commands in `scripts/`.
- Put implementation code in `source/`.
- Put summarized metrics in `results/`.
- Put demo visuals in `media/`.
- Put exported models in `exported/`.
