# Plumbing

## Identify the Gardyn version first

Sticker under the base, or garden-of-eden's `/system` endpoint once it runs (`curl localhost:5000/system`).
Hose layouts differ between 1.0/2.0, 3.0 and 4.0. Version is **TBD**, see [open-questions.md](../open-questions.md).

## Hose runs

| Run | Gardyn part | Notes |
|---|---|---|
| Pump to divider | [4.0 full single column](https://mygardyn.com/products/4-0-full-single-column) listing | Version fit TBD |
| Divider to column | [3.0 divider-to-column irrigation hose](https://mygardyn.com/products/3-0-divider-to-column-irrigation-hose) | |
| Column irrigation | [3.0 column irrigation hose](https://mygardyn.com/products/3-0-column-irrigation-hose) | |

How the pump end comes apart: [Gardyn pump replacement article](https://help.mygardyn.com/en/articles/1788801).
These links are from the planning guide and were not re-checked for fit by version.

## Measure and replace

| Step | Do | Record |
|---|---|---|
| 1 | Measure the old hose inner and outer diameter with calipers | ID: TBD, OD: TBD |
| 2 | Buy food-safe silicone or vinyl tubing of the same ID. Silicone resists kinking at bends | No published tube size was found |
| 3 | Fit, avoid sharp bends, no kinks | |

## Clean (while apart)

| Step | Do |
|---|---|
| 1 | Soak divider, columns and pump in diluted white vinegar (scale) |
| 2 | Rinse, then a mild hydrogen peroxide rinse (biofilm) |
| 3 | Clear each drip outlet with a toothpick |
| 4 | Pull the pump impeller, remove debris |

## Leak test (before electronics go back in)

1. Pump in a bucket of water, through garden-of-eden, short run only: `water 5`.
2. Expected: water moves and the pump stops after 5 s.
3. Reassemble columns, repeat with the full plumbing.
4. Check every joint dry after the run.
5. **Never run the pump dry.**

## Pump current check

garden-of-eden's `/pump/stats` endpoint reports pump current from the INA219.

| Reading | Meaning |
|---|---|
| Normal range | TBD: record a baseline with a known-good pump in water ([open-questions.md](../open-questions.md)) |
| High current | Stalled pump or blocked impeller |
| Very low current | Dry or disconnected pump |

```bash
curl localhost:5000/pump/stats   # JSON from the INA219; exact fields upstream, record yours
```
