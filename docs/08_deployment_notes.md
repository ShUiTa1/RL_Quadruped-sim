# Deployment Notes

V1 is deployment-aware, not real-robot deployment.

## Required

- export policy to TorchScript or ONNX
- standalone inference script
- fixed control frequency simulation
- frozen observation normalization
- action clipping
- safety fallback

## Safety Fallback

Define what happens when:

- observation contains NaN or Inf
- action exceeds configured bounds
- base orientation exceeds safe thresholds
- policy inference fails

## Export Metadata

Each exported policy should record:

```text
checkpoint:
training run:
observation definition:
action scale:
control frequency:
normalization:
robot asset:
```
