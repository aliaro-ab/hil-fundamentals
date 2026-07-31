---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[capability-stimulate]]"
  - "[[capability-observe]]"
  - "[[capability-capture]]"
related_to:
  - "[[boundary-device-internals]]"
  - "[[pitfall-leaked-bench-resources]]"
---
# Scenario: verify a tunable threshold

## The goal

The overtemperature warning threshold is a calibratable constant.

```
When the threshold is set to any value within the allowed range:
    the warning shall trigger at the calibrated value
    and not at a hardcoded number someone left in the source
```

This is a test *about the calibratability itself*, which is a category new engineers rarely think to write.

---

## A good answer

### Read and save the original value first

[[capability-observe]] the calibration at [[boundary-device-internals|device internals]]. You will restore exactly this, not a guess at the default. Do it before anything else.

### Wrap everything in guaranteed cleanup

Before you write a single calibration, make sure the restore runs on the failure path too. A test that fails partway and leaves a modified threshold in the DUT will poison every test that follows, persistently and invisibly. See [[pitfall-leaked-bench-resources]].

### Test at least three values

For each value (low end of range, nominal, high end):

1. Write the calibration. [[capability-stimulate]] at [[boundary-device-internals|device internals]].
2. Read it back and confirm it took. Calibration writes can be silently rejected (wrong memory page, insufficient access, value out of range).
3. Drive the temperature through the threshold using [[boundary-plant-model|the plant model]] or [[boundary-physical-pins|physical pins]]. Use a slow ramp, not a step. A ramp lets you measure *where* it tripped, while a step only tells you it eventually did.
4. [[capability-capture]] temperature and the warning flag together, so you can read off the trip point.
5. Assert the trip point matches the calibrated value within tolerance.

### Restore the original value

Write the original back, read it back, confirm.

## Why the ramp matters

A step from 90 to 120 through a threshold of 110 proves the warning fires somewhere in that interval. It would pass equally well against a hardcoded 100, which is exactly the bug you're hunting. A slow ramp with a synchronized capture gives you the actual trip temperature, and only that distinguishes "calibratable" from "coincidentally correct."

## Additional checks

| Check | What to look for |
| --- | --- |
| **Hysteresis** | Where does it clear? Usually a bit below the trip point, and usually also calibratable. Ramp back down and measure. |
| **Out-of-range writes** | Try a value outside the allowed range. It should be rejected cleanly, not accepted and acted upon. |
| **Persistence** | Does the calibration survive a power cycle? Should it? Both answers are legitimate designs; verify the intended one. |
| **Interaction** | Does changing this threshold above another related limit produce sensible behavior, or an inconsistent state? |

## Limitations

Whether the *shipped* calibration set is correct. You've verified the mechanism works for arbitrary values, which is a different claim from "the value we're shipping is the right one." That's a review question, not a bench question.
