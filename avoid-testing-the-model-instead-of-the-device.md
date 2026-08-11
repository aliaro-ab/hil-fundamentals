---
type: Pitfall
related_to:
  - "[[the-plant-model]]"
  - "[[what-a-hil-test-bench-is]]"
---
# Avoid testing the model instead of the device

## The symptom

A large, green, well-maintained test suite. And a product that fails in the field in ways the suite never hinted at.

Dig into the tests and you find a pattern. The stimulus is a model variable, and the assertion is a model variable. The DUT is technically present but is not really the subject of the experiment.

## How it occurs

Nobody decides to do this. It is the path of least resistance. [[the-plant-model]] is by far the nicest boundary to work at, with clean names, real units, high rates, perfect timestamps, no wiring, and no electrical risk. So tests drift toward it, one convenient shortcut at a time.

The specific moves:

- An assertion on a model variable that mirrors a DUT output, instead of a measurement of the DUT's actual output
- A write to a model variable that represents something the DUT computes, rather than a stimulus that makes the DUT compute it
- A plant model and control logic built from the same assumptions, so they agree with each other and both disagree with reality
- A model tuned until the tests pass, which is a real thing that really occurs under schedule pressure

## Why it persists

The tests are not incorrect, exactly. They do verify that the control logic behaves correctly *given the model's view of the world*. That is genuinely valuable, and it is the whole point of the MIL stage. See [[one-test-on-three-benches]].

The problem is to claim more than that. Everything between the control logic and physical reality (sensor scaling, ADC behavior, filter lag, driver diagnostics, task scheduling, wiring) is exactly what the HIL stage exists to cover. A model-to-model test skips all of it.

## How to detect it

For each assertion, ask one question. **If the DUT is unplugged, does this still pass?**

Uncomfortably frequently, the answer is yes. Those tests measure the bench.

Then ask a second question. **Which boundary owns the value in my assertion?** If the model computes it, your assertion is about the model. If the DUT produces it (as a pin state, a network signal, a diagnostic response), your assertion is about the product.

## The fix

### Stimulate at the model and assert at the device

Use [[the-plant-model|the plant model]] to create conditions, because that is what it is good at. Then make the assertion somewhere the DUT actually owns, at [[physical-pins|physical pins]], [[the-network|the network]], or [[the-diagnostic-channel|the diagnostic channel]].

### Use the model as ground truth

Do not treat its output as the answer. The model knows the true value. The DUT has an estimate. Asserting that the DUT's estimate tracks the model's truth within tolerance is a strong test. It is a different thing from asserting the model agrees with itself.

### Keep the model honest

If a model change makes a failing test pass, that is an event worth a conversation, not a commit. Physics, not test results, must justify model changes.

### Know what your model does not do

Each model is a simplification. Get the author to tell you what is absent (thermal effects, saturation, backlash, noise, sensor dynamics). You cannot trust tests that depend on missing behavior, and knowing the list is how you avoid writing them.
