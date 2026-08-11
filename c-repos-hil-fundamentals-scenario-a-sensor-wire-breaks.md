---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[disturb]]"
  - "[[capture]]"
  - "[[interrogate]]"
related_to:
  - "[[physical-pins]]"
_organized: true
---
# Scenario: A Sensor Wire Breaks

## The goal

```
When the coolant temperature sensor circuit goes open:
    the controller shall detect it within 500 ms
    substitute a safe default
    limit power to 50%
    store the corresponding fault code

When the circuit is restored:
    normal operation shall resume
    the fault shall remain in history
```

Work out what you need before reading on.

---

## A good answer

### Establish the precondition

Get the system to a steady operating state where the sensor is being read and power is not already limited. Establish this via [[the-plant-model|the plant model]], and *verify* it rather than assuming.

### Arm the capture first

[[capture]] the commanded power output (at [[the-network|the network]] or [[physical-pins|physical pins]], wherever the DUT actually publishes it), the DUT's internal reported temperature (at [[device-internals|device internals]], for diagnosis), and the fault flag. Use a pre-trigger so you see the state before the break. Sample fast enough to resolve 500 ms comfortably, at 100 Hz or better. See [[triggers-and-windows]].

### Inject the fault

[[disturb]] at [[physical-pins|physical pins]] by opening the circuit on the sensor line. The fault must be electrically real rather than a model-level substitution, because the point is to test the DUT's input diagnostics, which a model fault would bypass entirely.

### Assert on the trace

From the captured timestamps, verify that the time from the break to the fault flag is under 500 ms, the substituted temperature equals the documented default, and the power is limited to 50% within its own spec.

### Interrogate the diagnostics

[[interrogate]] at [[the-diagnostic-channel|the diagnostic channel]] and confirm the correct fault code, the correct status bits (not merely "present"; see the status model discussion there), and a freeze frame with plausible conditions.

### Verify recovery

Restore the circuit. Confirm the DUT returns to normal, and confirm the fault is now *history* rather than *active*. Whether it self-clears or requires a power cycle is a requirement; check the actual one.

### Clean up

Release the relay, clear the faults, and return the DUT to a known state. See [[the-test-bench-resource-model]].

## Limitations

- Intermittent faults. A clean open is the easy case; a flaky connection making and breaking at 50 Hz is the one that actually happens in vehicles, and it's a different test.
- Short-to-ground and short-to-supply, which are different circuit conditions with potentially different detection paths. Three tests, not one.
- Whether 500 ms is the *right* requirement. Nothing on the bench can tell you that.

## Variations

| Variation | Why it's a separate test |
| --- | --- |
| **Short to ground** | A different circuit condition, with a potentially different detection path and fault code. |
| **Short to supply** | Same again, and the one that stresses the input hardware hardest. |
| **Slow drift out of range** | No sharp edge to detect; exercises range and plausibility checks rather than circuit diagnostics. |
| **Plausible-but-wrong value** | The hardest to detect, and the most dangerous. |
| **Fault during a transient** | Detection logic tuned at steady state can misbehave mid-maneuver. |
