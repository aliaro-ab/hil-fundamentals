---
type: Boundary
belongs_to: "[[capability-grid]]"
related_to:
  - "[[open-loop-vs-closed-loop]]"
  - "[[pitfall-testing-the-model-not-the-device]]"
---
# The plant model

**The simulated world the device thinks it lives in.**

The plant model is the motor, the vehicle, the battery pack, the hydraulic circuit — whatever the DUT is trying to control. It consumes the DUT's outputs and produces the inputs the DUT expects to see.

## What you can do here

| Capability | What it means here |
| --- | --- |
| [[capability-observe]] and [[capability-stimulate]] | Any model variable |
| [[capability-capture]] | Model variables at the model's own rate — usually the highest-fidelity, best-aligned data available on the whole bench |
| [[capability-replay]] | Recorded profiles into model inputs |
| [[inject-a-fault]] | Logically: seize a bearing, spring a leak, stick an actuator |

You cannot [[capability-interrogate]] a model. It has no opinion about its own health.

## Why it's the friendliest boundary

Everything is a named number in a known unit at a known rate, with no wiring, no scaling, and no electrical risk. Model variables are also the only place you can see quantities that don't physically exist as signals — internal states, energies, forces, and the "true" value of something the DUT can only estimate.

That last one is enormously useful. You can compare the DUT's *estimate* of a quantity against the model's *truth*, which is an assertion you simply cannot make on a real machine.

## The two things that bite

**Ownership.** The model recomputes its variables every tick. If you write one it owns, your value survives one cycle. To influence a model-owned quantity you change a driver, a load, a parameter, or an explicit override input the model author provided. See [[open-loop-vs-closed-loop]].

**Rate.** The model runs at a fixed step. Nothing you do here happens faster than one step, and no assertion about the model can resolve finer than that. A 1 ms model cannot tell you anything trustworthy about a 200 µs event.

## The trap

The plant model is so pleasant to work with that you'll be tempted to test against it exclusively. But the model is not the product, and it was written by someone with their own assumptions and simplifications. A test that only touches the plant model is at constant risk of confirming that the model does what the model does. See [[pitfall-testing-the-model-not-the-device]].

Use the model to *create conditions* and to *provide ground truth*. Wherever possible, make the assertion at a boundary the DUT actually owns.
