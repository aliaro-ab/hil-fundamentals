---
type: Guide
related_to:
  - "[[start-here]]"
  - "[[time-is-the-hard-part]]"
  - "[[the-test-bench-resource-model]]"
_organized: true
---
# Coming from software testing

If you already write automated tests for services or applications, most bench discipline maps onto something you own. Use the first table to transfer years of instinct in one sitting, then read the second half to see where the analogies break, because carried over unmodified they will bite.

## The map

| On the bench | The software concept it rhymes with |
| --- | --- |
| The DUT | The unit under test. Same idea, same insistence that everything else is scaffolding. |
| [[boundary-plant-model]] | A high-fidelity fake of the production environment, with the classic risk of fakes: testing the fake instead of the product. See [[pitfall-testing-the-model-not-the-device]]. |
| Restbus simulation, at [[boundary-network]] | Mocks for collaborators that aren't deployed. The DUT won't leave startup without its dependencies answering, so the bench plays all of them. |
| The mapping layer, from [[one-signal-five-names]] | The configuration and dependency-injection layer that keeps environment specifics out of test logic. |
| [[the-test-bench-resource-model]] | Fixtures with guaranteed teardown. Leaked state fails the *next* test, same as always, except here it can survive a reboot. |
| [[capability-capture]] | Structured logging and tracing with timestamps, configured before the event rather than grepped after it. |
| Wait-for-condition with timeout | Asserting against an eventually-consistent system. `sleep()` is as wrong here as in your integration suite, and for the same reason. See [[time-is-the-hard-part]]. |
| [[capability-replay]] | Record-and-replay of production traffic, turned into a regression test. |
| [[boundary-diagnostic-channel]] vs [[boundary-device-internals]] | The public API vs private state. Assert on the contract, use internals for diagnosis. See [[capability-interrogate]]. |
| Calibration values, at [[boundary-device-internals]] | Runtime configuration, with the persistence of a database write rather than an environment variable. |
| "Works on the bench" | "Works on my machine." Same caveat, same tone of voice. |

## Where the analogy misleads

### The system never pauses

There is no breakpoint. The real-time loop runs whether your test is executing, sleeping, or crashed, which makes this closer to testing against live production than to running a unit suite. Your test doesn't drive the system; it reaches into a running machine. See [[time-is-the-hard-part]].

### Timing is the subject, not an inconvenience

In software, latency is usually noise you tolerate. Here, "within 100 ms" is frequently the requirement itself, and your script's clock cannot measure it. Anything with real timing needs belongs on the real-time side. See [[pitfall-python-loop-as-control-loop]].

### Resources are physical and exclusive

You cannot spin up a second bench in a container, and two tests cannot own the same output pin. Parallelism is the exception rather than the default, and bench time is scheduled like any other scarce machine. See [[the-test-bench-resource-model]].

### The type system ends at the wire

A wrong unit or scaling factor doesn't throw, it returns a plausible number. There is no compiler between a kilopascal and a bar, only your expectation. See [[pitfall-unit-assumption]].

### Mistakes can cost hardware

A wrong write in software throws an exception. A wrong relay closure can put supply voltage on a signal pin and destroy the DUT in a millisecond. Learn the interlocks before the API. See [[capability-break]] and [[boundary-physical-pins]].
