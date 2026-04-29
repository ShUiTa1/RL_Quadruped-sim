# Evaluation Protocol

## Evaluation Matrix

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

## Baselines

```text
B0: PPO flat baseline
B1: PPO + domain randomization
B2: PPO + domain randomization + terrain curriculum
```

Optional:

```text
B3: PPO + DR + curriculum + history encoder
```

## Metrics

- velocity tracking error
- yaw tracking error
- fall rate
- episode length
- distance traveled
- mean torque
- energy proxy
- action smoothness
- recovery time after push
- terrain success rate

## Reporting

Each evaluation should write a CSV row with:

```text
run_id, baseline, eval_case, seed, command, terrain, randomized_condition, metric_name, metric_value
```
