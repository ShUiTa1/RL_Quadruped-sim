# Terrain Curriculum

## Terrain Levels

```text
Level 0: flat
Level 1: rough terrain
Level 2: slopes
Level 3: stairs
Level 4: random blocks
Level 5: gaps optional
```

## Simple Curriculum Rule

```text
if robot travels enough distance without falling:
    move to harder terrain
elif robot falls too often:
    move to easier terrain
else:
    keep same level
```

## V1 Completion Standard

- flat, rough, slope, and stairs are evaluated.
- terrain success rate is reported.
- at least one rough-terrain demo is recorded.
