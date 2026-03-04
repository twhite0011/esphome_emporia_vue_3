# emporia_esphome

ESPHome configuration and notes for an **Emporia Vue 3** energy monitor.

## Scope
- Hardware: Emporia Vue 3
- Region/panel type: United States split-phase panel (**2 x 120V legs / 240V service**)
- Network transport: **Ethernet via RJ-45** (not Wi-Fi)
- Monitoring model: **Branch-circuit CTs only**
  - No CTs are installed on the main incoming service conductors

## Current Calibration Choices
- Voltage calibration: set correctly and considered accurate
- Power calibration: scaled **+2.9%** to align with the SDG&E utility meter

## Circuit Layout
- Total monitored branch circuits: **13**
- Main total and leg totals are **computed from the sum of branch circuits**
  - This is intentional because there are no main service CTs

## How totals are derived
Because mains are not directly measured:
- `total_power` = sum of all 13 branch circuit powers
- `phase_a_power` and `phase_b_power` = sums of branch circuits assigned to each leg

This provides practical whole-home estimates and per-leg balancing visibility, but note:
- Any branch not instrumented by a CT is not included
- Aggregate accuracy is bounded by per-circuit CT placement and calibration quality

## Recommended Validation Workflow
1. Compare `total_power` against the SDG&E meter during stable loads.
2. Validate each leg against expected panel balance (major 120V loads on correct leg).
3. Re-check calibration after any CT repositioning or panel changes.
4. Track error over multiple load levels (light, medium, heavy), not a single snapshot.

## Practical Notes
- Keep CT orientation consistent; reversed CTs can invert power signs.
- Label each circuit clearly in ESPHome/Home Assistant to avoid leg assignment mistakes.
- If a circuit is moved in the panel, update mapping so leg totals remain valid.
- Document any future calibration changes with date and reason.

## Project Files
- Primary configuration file: `emporia.yaml`

## Credits
- YAML template and setup guidance adapted from: https://emporia-vue-local.github.io/docs/tutorial/intro/

## Secret Safety
- Keep credentials in `secrets.yaml` and reference them via `!secret` in `emporia.yaml`.
- `.gitignore` excludes local secret files by default.

### Pre-commit Secret Scan
This repo includes a local pre-commit hook at `.githooks/pre-commit` that blocks likely secrets in staged changes.

Enable it once per clone:

```bash
git config core.hooksPath .githooks
```

If you intentionally commit non-secret test data that matches patterns, bypass once:

```bash
SKIP_SECRET_SCAN=1 git commit -m "..."
```
