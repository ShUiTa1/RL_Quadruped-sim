# Observation and Action

## Command

```text
command = [v_x, v_y, omega_z]
```

## Initial Observation

Use proprioception only for V1:

- base angular velocity
- projected gravity
- command velocity
- joint position error
- joint velocity
- previous action

Avoid in V1:

- RGB
- depth
- height map
- history encoder
- privileged information

## Action

Use residual joint position targets:

```text
target_q = default_q + action_scale * action
```

Expected action dimension: 12 for a standard quadruped.

## Control

Low-level control should be PD tracking through Isaac Lab actuator configuration.
