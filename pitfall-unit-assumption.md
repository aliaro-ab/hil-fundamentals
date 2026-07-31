---
type: Pitfall
related_to:
  - "[[one-signal-five-names]]"
_width: normal
---
# Avoid unit and scaling assumptions

## The symptom

The test asserts the pressure stays below 5. It passes. Nobody notices that the signal is in kPa and the requirement was in bar, so the real limit was 500 and the test has been asserting nothing for eight months.

Or the value reads 0.3 where you expected 30. Someone "fixes" it by multiplying by 100 in the test. Six weeks later a different variant reports the same signal as a percentage already, and now two tests disagree about reality.

## Why it's so hard to catch

**A units error does not throw.** It returns a number. Numbers are plausible. Nothing in the system knows what a kilopascal is. There is a float, and there is your expectation, and the only place they're reconciled is your head.

Worse, wrong-unit tests often *pass*, which means they're never investigated. A test that fails loudly gets fixed on day one. A test that passes vacuously survives for years and gives false confidence to the people relying on it.

## The usual sources

| Source | What it looks like |
| --- | --- |
| **Scale factor of 10, 100, or 1000** | %, per-unit, and fraction; Pa, kPa, and bar; mV and V; ms and s |
| **Angle conventions** | Degrees vs radians, and which direction is positive |
| **Sign conventions** | Is torque positive when driving or when braking? Is current positive into or out of the battery? |
| **Offset units** | °C vs K. Adding two temperatures in °C is a specific kind of wrong. |
| **Raw vs physical** | An ADC count is not a voltage. See [[one-signal-five-names]]. |
| **Variant drift** | The same signal in different units on two product variants, with one mapping file used for both. |

## What to do

**Never write a bare number.** Put the unit in the name, the constant, or the comment. `MAX_PRESSURE_BAR = 5` cannot be misread. `MAX_PRESSURE = 5` can, and eventually will.

**Check the range at setup.** If a percentage reads 0.42, it's a fraction. If it reads 4200, it's raw counts. A one-time sanity read of every signal a test uses, at a known condition, catches most of these in the first five minutes.

**Make the test say the intent.** `set_throttle_percent(30)` is checkable by a reviewer. `write("ThrPosRaw", 1229)` is not.

**Sanity-check against something independent.** If the model has ground truth and the DUT has an estimate, compare them once at a known condition. Wildly different numbers for the same physical quantity is a units error until proven otherwise.

**Be skeptical of a passing test you've never seen fail.** Break it on purpose once. Inject a condition that *should* trip it. If it still passes, the assertion isn't doing anything. This is worth doing for every safety-relevant limit check in the suite.
