# Reward Design

## Minimal Flat-Terrain Reward

- linear velocity tracking
- yaw rate tracking
- orientation penalty
- base height penalty
- torque penalty
- joint velocity penalty
- action rate penalty
- termination penalty

## Design Rule

Start with the smallest reward set that can produce stable flat walking. Add terms only when there is a clear failure mode.

## Metrics Coupling

Every major reward term should have a matching evaluation metric where possible:

| Reward Term | Evaluation Metric |
|---|---|
| linear velocity tracking | velocity tracking error |
| yaw rate tracking | yaw tracking error |
| orientation/base stability | fall rate, episode length |
| torque penalty | mean torque, energy proxy |
| action rate penalty | action smoothness |
