# Task Breakdown

## WP0: Project Initialization and Official Baseline

- [ ] Confirm Isaac Sim / Isaac Lab versions.
- [ ] Run an official Isaac Lab locomotion task.
- [ ] Train at least one short official PPO run.
- [ ] Play a checkpoint.
- [ ] Record all commands in `docs/01_setup.md`.
- [ ] Add `scripts/train_official_example.sh`.
- [ ] Add `scripts/play_official_example.sh`.

Done when: official train/play works and the result path is documented.

## WP1: Robot Asset

- [ ] Choose Go2 or fallback A1.
- [ ] Spawn robot in Isaac Lab.
- [ ] Document joint names and foot body names.
- [ ] Configure default pose and PD gains.
- [ ] Verify reset stability.

Done when: robot spawns and holds a sane default pose.

## WP2: Minimal Flat Locomotion Task

- [ ] Define command sampler `[v_x, v_y, omega_z]`.
- [ ] Define observation vector.
- [ ] Define residual joint-position action.
- [ ] Define termination conditions.
- [ ] Implement minimal reward terms.
- [ ] Add `scripts/train_flat.sh`.
- [ ] Add `scripts/play_policy.sh`.

Done when: policy can stand and move forward on flat terrain.

## WP3: PPO Baseline

- [ ] Configure RSL-RL PPO.
- [ ] Train flat baseline.
- [ ] Save checkpoint.
- [ ] Record training curve.
- [ ] Evaluate forward, turning, and mixed command cases.
- [ ] Write `results/flat_eval.csv`.

Done when: velocity tracking error and fall rate are computed.

## WP4: Domain Randomization

- [ ] Add friction randomization.
- [ ] Add payload randomization.
- [ ] Add motor strength randomization.
- [ ] Add observation noise.
- [ ] Compare baseline vs DR policy.
- [ ] Write `results/robustness_eval.csv`.

Done when: at least four randomization dimensions have evaluation bins.

## WP5: Terrain Curriculum

- [ ] Add rough terrain.
- [ ] Add slopes.
- [ ] Add stairs.
- [ ] Implement terrain level update rule.
- [ ] Compare with and without curriculum.
- [ ] Write `results/terrain_eval.csv`.

Done when: flat, rough, slope, and stairs are evaluated.

## WP6: External Disturbance Recovery

- [ ] Add random push direction.
- [ ] Add random push magnitude.
- [ ] Add random push duration.
- [ ] Add random push timing.
- [ ] Measure recovery time and fall rate.

Done when: B0, B1, and B2 are compared under identical push settings.

## WP7: Evaluation Benchmark

- [ ] Implement evaluation cases E0-E9.
- [ ] Standardize CSV schema.
- [ ] Generate summary table.
- [ ] Generate at least one plot.
- [ ] Write failure-case notes.

Done when: benchmark compares at least B0, B1, and B2.

## WP8: Policy Export

- [ ] Export TorchScript policy.
- [ ] Add standalone inference script.
- [ ] Freeze observation normalization.
- [ ] Document action scaling and clipping.
- [ ] Add safety fallback.

Done when: inference runs without the training framework.
