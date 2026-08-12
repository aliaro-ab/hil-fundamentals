---
type: Guide
related_to:
  - "[[start-here]]"
  - "[[time-is-the-hard-part]]"
  - "[[the-test-bench-resource-model]]"
---
# Coming from software testing

If you already write automated tests for services or applications, most bench discipline maps onto something you own. "The map" below transfers years of instinct in one sitting.
Then read "Where the analogy misleads," because analogies carried over unmodified will bite.

## The map

| On the bench | The software concept it rhymes with |
| --- | --- |
| The DUT | The unit under test. Same idea, same insistence that everything else is scaffolding. |
| [[the-plant-model]] | A high-fidelity fake of the production environment, with the classic risk of fakes: testing the fake instead of the product. See [[avoid-testing-the-model-instead-of-the-device]]. |
| Restbus simulation, at [[the-network]] | Mocks for collaborators that are not deployed. The DUT will not leave startup without its dependencies answering, so the bench plays all of them. |
| The mapping layer, from [[five-names-for-one-signal]] | The configuration and dependency-injection layer that keeps environment specifics out of test logic. |
| [[the-test-bench-resource-model]] | Fixtures with guaranteed teardown. Leaked state fails the *next* test, same as always, except here it can survive a reboot. |
| [[capture]] | Structured logging and tracing with timestamps, configured before the event rather than grepped after it. |
| Wait-for-condition with timeout | Asserting against an eventually-consistent system. `sleep()` is as incorrect here as in your integration suite, and for the same reason. See [[time-is-the-hard-part]]. |
| [[replay]] | Record-and-replay of production traffic, turned into a regression test. |
| [[the-diagnostic-channel]] vs [[device-internals]] | The public API vs private state. Assert on the contract, use internals for diagnosis. See [[interrogate]]. |
| Calibration values, at [[device-internals]] | Runtime configuration, with the persistence of a database write rather than an environment variable. |
| "Works on the bench" | "Works on my machine." Same caveat, same tone of voice. |

## Where the analogy misleads

### The system never pauses

There is no breakpoint.
The real-time loop runs whether your test executes, sleeps, or crashes.
This makes it closer to testing against live production than to running a unit suite.
Your test does not drive the system.
It reaches into a running machine.
See [[time-is-the-hard-part]].

### Timing is the subject, not an inconvenience

In software, latency is usually noise you tolerate.
Here, "within 100 ms" is frequently the requirement itself, and your script's clock cannot measure it.
Anything with real timing needs belongs on the real-time side.
See [[avoid-relying-on-scripting-for-time-sensitive-tasks]].

### Resources are physical and exclusive

You cannot spin up a second bench in a container, and two tests cannot own the same output pin.
Parallelism is the exception rather than the default, and bench time is scheduled like any other scarce machine.
See [[the-test-bench-resource-model]].

### The type system ends at the wire

An incorrect unit or scaling factor does not throw, it returns a plausible number.
There is no compiler between a kilopascal and a bar, only your expectation.
See [[avoid-unit-and-scaling-assumptions]].

### Mistakes can cost hardware

An incorrect write in software throws an exception.
An incorrect relay closure can put supply voltage on a signal pin and destroy the DUT in a millisecond.
Learn the interlocks before the API.
See [[disturb]] and [[physical-pins]].
