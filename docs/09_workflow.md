# Workflow

## Branching

Use short feature branches:

```text
codex/wp0-official-baseline
codex/wp1-robot-asset
codex/wp2-flat-locomotion
```

## Daily Loop

1. Pick one small task from `docs/10_task_breakdown.md`.
2. State the expected output before coding.
3. Make the smallest change that can be tested.
4. Run the relevant command.
5. Record result paths, metrics, and failure cases.
6. Update docs when behavior or commands change.

## Run Naming

Use stable run names:

```text
YYYYMMDD_baseline_case_seed
```

Example:

```text
20260429_wp0_official_flat_seed0
```

## Definition of Done

A task is done only when it has:

- code or config change, if needed
- command used to verify it
- output path or result
- short note in the relevant doc

## Development Order

1. Reproduce official Isaac Lab locomotion.
2. Spawn selected robot asset.
3. Build minimal flat locomotion.
4. Train PPO flat baseline.
5. Add domain randomization.
6. Add terrain curriculum.
7. Add push recovery.
8. Run full benchmark.
9. Export policy.
