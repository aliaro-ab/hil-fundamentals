---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[inject-a-fault]]"
  - "[[capability-capture]]"
  - "[[capability-interrogate]]"
related_to:
  - "[[boundary-physical-pins]]"
---

# Scenario: a sensor wire breaks

## The goal

> When the coolant temperature sensor circuit goes open, the controller shall detect it within 500 ms, substitute a safe default, limit power to 50%, and store the corresponding fault code. When the circuit is restored, normal operation shall resume, and the fault shall remain in history.

Work out what you need before reading on.

---

## A good answer

**Precondition.** Get the system to a steady operating state where the sensor is being read and power is not already limited. Establish this via [[boundary-plant-model]], and *verify* it rather than assuming.

**Arm the capture first.** [[capability-capture]] on: the commanded power output (at [[boundary-network]] or [[boundary-physical-pins]] — wherever the DUT actually publishes it), the DUT's internal reported temperature (at [[boundary-device-internals]], for diagnosis), and the fault flag. Use a pre-trigger so you see the state before the break. Sample fast enough to resolve 500 ms comfortably — 100 Hz or better. See [[triggers-and-windows]].

**Break it.** [[inject-a-fault]] at [[boundary-physical-pins]]: open circuit on the sensor line. Electrically real, not a model-level substitution — the point is to test the DUT's input diagnostics, which a model fault would bypass entirely.

**Assert on the trace.** From the captured timestamps: time from the break to the fault flag < 500 ms; substituted temperature equals the documented default; power limited to 50% within its own spec.

**Interrogate.** [[capability-interrogate]] at [[boundary-diagnostic-channel]]: correct fault code, correct status bits — not merely "present," see the status model discussion there — and a freeze frame with plausible conditions.

**Recover.** Restore the circuit. Confirm the DUT returns to normal, and confirm the fault is now *history* rather than *active*. Whether it self-clears or requires a power cycle is a requirement; check the actual one.

**Clean up.** Relay released, faults cleared, DUT back to a known state. See [[a-bench-is-leased-resources]].

## What this test does not catch

- Intermittent faults. A clean open is the easy case; a flaky connection making and breaking at 50 Hz is the one that actually happens in vehicles, and it's a different test.
- Short-to-ground and short-to-supply, which are different circuit conditions with potentially different detection paths. Three tests, not one.
- Whether 500 ms is the *right* requirement. Nothing on the bench can tell you that.

## Variations worth writing

Short to ground · short to supply · slow drift out of range · plausible-but-wrong value (the hardest to detect, and the most dangerous) · fault during a transient rather than at steady state.
