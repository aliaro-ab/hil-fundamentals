---
type: Capability
belongs_to: "[[capability-grid]]"
related_to:
  - "[[open-loop-vs-closed-loop]]"
  - "[[capability-replay]]"
  - "[[pitfall-python-loop-as-control-loop]]"
---

# Stimulate

**Make this value be something.**

Stimulate splits into two very different things that beginners lump together, and the split matters more than almost anything else in this vault.

## Setting a value

Write a number. The system now uses that number until something changes it.

Fine for preconditions and slow changes: ambient temperature, a configuration parameter, a mode request, an operator setpoint. Your test script can do this directly.

Two constraints:

| Constraint | What it means |
| --- | --- |
| **Who owns the variable?** | If the plant model computes it every tick, your write lasts one tick. See [[open-loop-vs-closed-loop]] |
| **When does it take effect?** | Somewhere between now and one cycle from now. You do not control which |

## Playing a waveform

Deliver a *time-varying* signal with real timing guarantees: a 5 Hz sine, a 10 ms pulse, a ramp from 0 to 100% over 3 seconds, an arbitrary recorded trace.

This cannot be done from your test script. A loop of write-and-sleep gives you a staircase with jitter proportional to your operating system's mood — see [[pitfall-python-loop-as-control-loop]]. Instead you **describe** the waveform, hand the description to the real-time part of the bench, and tell it to run.

That handoff is the single most important structural idea about stimulation: **you specify, the bench executes.** Your script's role is to arm it, start it, and find out when it finished.

## Where it works

| Boundary | What stimulating means there |
| --- | --- |
| [[boundary-plant-model]] | Drive a model input or override a model variable |
| [[boundary-device-internals]] | Write a calibration value |
| [[boundary-physical-pins]] | Generate a voltage, current, resistance, PWM, or encoder pattern |
| [[boundary-network]] | Transmit a frame or set a signal the bench sends periodically |
| [[boundary-service-layer]] | Issue a request, or stand in as a service the device calls |

## Questions to ask about any stimulus

1. What's the update rate, and is it fast enough for the physics?
2. Is timing guaranteed, or best-effort?
3. Can it be triggered by a condition rather than by my script?
4. What happens to the value when the stimulus ends — hold, zero, or return to model control?

That last one causes more mysterious follow-on failures than any other detail.
