---
type: Boundary
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[five-names-for-one-signal]]"
  - "[[interrogate]]"
---
# Device internals

**The device's live memory and calibration constants, through a developer's back door.**

A query for internal state is one of the most useful ways to find out what actually happens.

Many devices provide a separate channel into the DUT that lets you read its internal variables and write its calibration constants without stopping it.
It is the same mechanism a calibration engineer uses to tune a controller on a test track.

Two distinct things live here:

## Measurement variables

Internal state as the DUT sees it.

This can be filtered sensor values, estimator outputs, state machine states, intermediate computations, internal flags.
These are read-only, so you can [[observe]] and [[capture]] these.

This is your window into *why*.
When a test fails, the physical outputs tell you what occurred, and the internal variables tell you what the DUT believed.

The gap between the two is usually the bug.

## Calibration values

Calibration interfaces give you access to read and write thresholds, gains, lookup tables, timeouts, enable flags, and other tuning parameters on the device.
You can [[stimulate]] these to change a threshold mid-test and check that the logic responds.
You can also configure a variant without reflashing, or do any number of other useful things.

> [!warning] > These are dangerous. > Writing calibration data changes the device's behavior persistently and invisibly.

A test that leaves a modified calibration behind will corrupt each test that runs after it, and the failures will look nothing like the cause.
Treat calibration writes like a transaction.
Record the original, always restore it, and never assume a bench arrives in a known state.
See [[the-test-bench-resource-model]].

## Capabilities

| Capability | What it means here |
| --- | --- |
| [[observe]] | Read a measurement variable as the DUT currently sees it |
| [[stimulate]] | Write a calibration value, such as a threshold, gain, or lookup table |
| [[capture]] | Log internal variables over a window, subject to this channel's slower rate |
| [[replay]] | Awkward. Possible for a recorded calibration sequence, rarely worth the effort |
| [[disturb]] | Only logically, by writing a deliberately incorrect calibration value |

You cannot [[interrogate]] through this channel.
It has no contract and no self-report, which is what [[the-diagnostic-channel]] is for.

## Constraints

### Internals are not the product

No customer reads your internal variables.
Asserting on an internal flag is convenient and brittle.
The next refactor renames it and your test breaks without the product changing.
Prefer asserting on externally visible behavior and using internals for *diagnosis*.

### Limited sample rates

This channel usually samples slower than the DUT's own control loop, and frequently slower than [[physical-pins|physical pins]].

### Side effects

The DUT spends real cycles servicing your requests.
Asking for too many variables too fast can change the timing you try to measure.

### Names are volatile

Internal symbol names come from a build artifact and change between software versions.
This is a major contributor to [[five-names-for-one-signal|five names for one signal]].

## Rule of thumb

Internals are for understanding, externals are for asserting.
A test whose only assertion is on an internal variable is cause for slight discomfort.
