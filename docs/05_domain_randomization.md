# Domain Randomization

## Batch 1

- friction randomization
- base mass / payload randomization
- motor strength randomization
- observation noise

## Batch 2

- joint damping randomization
- action latency
- external push

## Batch 3

- terrain property randomization
- sensor dropout
- actuator model

## Definition Template

Each randomization must define:

```text
name:
  randomized variable:
  range:
  applied: reset / interval / every step
  train:
  eval:
```

## Initial Evaluation Bins

```text
friction: [0.3, 0.5, 0.8, 1.2]
payload: [0 kg, 1 kg, 2 kg, 3 kg]
motor strength: [0.6, 0.8, 1.0]
observation noise: [0.0, 0.01, 0.03, 0.05]
```
