# Setup

This document records the environment setup for the Isaac Lab version of the project.

## Target Stack

- NVIDIA GPU with Isaac Sim support.
- Isaac Sim.
- Isaac Lab.
- RSL-RL training workflow through Isaac Lab.
- Python environment managed the same way Isaac Lab recommends for the installed version.

## First Setup Goal

Do not start by porting the old PyBullet environment. First verify that a known Isaac Lab locomotion task can train and play.

## Setup Checklist

- [x] Install Isaac Sim.
- [x] Install Isaac Lab.
- [x] Run Isaac Lab environment checks.
- [ ] Run an official locomotion training command.
- [ ] Play a checkpoint.
- [ ] Open TensorBoard or equivalent logs.
- [ ] Record exact commands, versions, GPU, and run path.

## Commands to Fill In

```bash
# Isaac Lab root, version, and install command

# Official locomotion training command

# Official locomotion play command

# TensorBoard/log command
```

## Notes

The old project in `../ref/lr-quadruped-sim-master/` is a reference for observation, reward, PD/CPG ideas, and A1 assets. It should not become the structure of the new Isaac Lab package.
