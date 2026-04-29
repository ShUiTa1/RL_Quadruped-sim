# Project Scope

Canonical source: `../isaac_lab_quadruped_project_scope.md`.

## Problem Definition

Given a desired base velocity command, train a quadruped locomotion policy that tracks the command while maintaining stability and robustness under terrain variations, dynamics mismatch, sensor noise, control latency, and external disturbances.

## Pipeline

```text
robot asset
-> Isaac Lab environment
-> PPO training
-> domain randomization
-> terrain curriculum
-> disturbance recovery
-> evaluation benchmark
-> policy export
-> documentation/demo
```

## In Scope

- Isaac Lab quadruped environment.
- Command-conditioned locomotion with command `[v_x, v_y, omega_z]`.
- PPO training through Isaac Lab + RSL-RL.
- Domain randomization.
- Terrain curriculum.
- Robustness evaluation benchmark.
- TorchScript or ONNX export with standalone inference notes.

## Out of Scope for V1

- Real robot deployment.
- Vision or depth navigation.
- Humanoid locomotion.
- Writing PPO from scratch.
- Extreme parkour.

## Robot Priority

1. Unitree Go2 as primary target.
2. Unitree A1 as fallback and reference bridge from the old PyBullet project.
3. ANYmal as another fallback if Isaac Lab examples make it faster to bootstrap.
