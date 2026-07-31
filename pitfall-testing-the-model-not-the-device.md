---
type: Pitfall
related_to:
  - "[[boundary-plant-model]]"
  - "[[what-a-hil-bench-is]]"
---
# Avoid testing the model instead of the device

## The symptom

A large, green, well-maintained test suite. And a product that fails in the field in ways the suite never hinted at.

Dig into the tests and you find a pattern. The stimulus is a model variable, the assertion is a model variable, and the DUT is technically present but is not really the subject of the experiment.

## How it happens

Nobody decides to do this. It's the path of least resistance. [[boundary-plant-model]] is by far the nicest boundary to work at, with clean names, real units, high rates, perfect timestamps, no wiring, and no electrical risk. So tests drift toward it, one convenient shortcut at a time.

The specific moves:

- Asserting on a model variable that mirrors a DUT output, instead of measuring the DUT's actual output
- Setting a model variable that represents something the DUT computes, rather than driving the DUT to compute it
- Building the plant model and the control logic from the same assumptions, so they agree with each other and both disagree with reality
- Tuning the model until the tests pass, which is a real thing that really happens under schedule pressure

## Why it persists

The tests aren't wrong, exactly. They do verify that the control logic behaves correctly *given the model's view of the world*. That's genuinely valuable, and it's the whole point of the MIL stage. See [[mil-sil-hil-one-story]].

The problem is claiming more than that. Everything between the control logic and physical reality (sensor scaling, ADC behavior, filter lag, driver diagnostics, task scheduling, wiring) is exactly what the HIL stage was supposed to cover, and a model-to-model test skips all of it.

## How to detect it

For each assertion, ask one question. **If the DUT were unplugged, would this still pass?**

Uncomfortably often, the answer is yes. Those tests are measuring the bench.

Then ask a second question. **Which boundary owns the value I'm asserting on?** If the model computes it, you're asserting about the model. If the DUT produces it (as a pin state, a network signal, a diagnostic response), you're asserting about the product.

## Correct approach

### Stimulate at the model and assert at the device

Use [[boundary-plant-model|the plant model]] to create conditions, because that's what it's good at. Then make the assertion somewhere the DUT actually owns, at [[boundary-physical-pins|physical pins]], [[boundary-network|the network]], or [[boundary-diagnostic-channel|the diagnostic channel]].

### Use the model as ground truth

Do not treat its output as the answer. The model knows the true value; the DUT has an estimate. Asserting that the DUT's estimate tracks the model's truth within tolerance is a strong test, and it's a different thing from asserting the model agrees with itself.

### Keep the model honest

If a model change makes a failing test pass, that's an event worth a conversation, not a commit. Model changes should be justified by physics, not by test results.

### Know what your model doesn't do

Every model is a simplification. Get the author to tell you what's missing (thermal effects, saturation, backlash, noise, sensor dynamics). Tests that depend on missing behavior can't be trusted, and knowing the list is how you avoid writing them.
