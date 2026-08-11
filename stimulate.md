---
type: Capability
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[open-loop-vs-closed-loop]]"
  - "[[replay]]"
  - "[[avoid-relying-on-scripting-for-time-sensitive-tasks]]"
---

# Stimulate

**Make this value be something.**

Stimulate splits into two very different operations that are easy to conflate, and the split matters more than almost anything else in this vault.

## Set a value

Write a number. The system now uses that number until something changes it.

A simple set is fine for preconditions and slow changes such as ambient temperature, a configuration parameter, a mode request, or an operator setpoint. Your test script can do this directly.

Two constraints:

| Constraint | What it means |
| --- | --- |
| **Who owns the variable?** | If the plant model computes it each tick, your write lasts one tick. See [[open-loop-vs-closed-loop]]. |
| **When does it take effect?** | Somewhere between now and one cycle from now. You do not control which. |

## Play a waveform

Deliver a *time-varying* signal with real timing guarantees. Examples: a 5 Hz sine, a 10 ms pulse, a ramp from 0 to 100% over 3 seconds, or an arbitrary recorded trace.

Your test script cannot do this. A loop of write-and-sleep gives you a staircase with jitter determined by operating system scheduling. See [[avoid-relying-on-scripting-for-time-sensitive-tasks]]. Instead you **describe** the waveform, hand the description to the real-time part of the bench, and tell it to run.

That handoff is the single most important structural idea about stimulation. **You specify, the bench executes.** Your script's role is to arm it, start it, and find out when it finished.

## Where it works

| Boundary | What stimulating means there |
| --- | --- |
| [[the-plant-model]] | Drive a model input or override a model variable |
| [[device-internals]] | Write a calibration value |
| [[physical-pins]] | Generate a voltage, current, resistance, PWM, or encoder pattern |
| [[the-network]] | Transmit a frame or set a signal the bench sends periodically |
| [[the-service-layer]] | Issue a request, or stand in as a service the device calls |

## Questions to ask about any stimulus

1. What is the update rate, and is it fast enough for the physics?
2. Is timing guaranteed, or best-effort?
3. Can a condition trigger it, rather than my script?
4. What occurs to the value when the stimulus ends (hold, zero, or return to model control)?

That last one causes more mysterious follow-on failures than any other detail.
