---
type: Concept
status: Active
related_to:
  - "[[capture]]"
  - "[[stimulate]]"
  - "[[avoid-relying-on-scripting-for-time-sensitive-tasks]]"
  - "[[avoid-immediately-reading-after-writing]]"
_organized: true
---
# Time is the hard part

## The incorrect mental model

In ordinary software, this works:

```
set(throttle, 30)
assert read(power) < limit
```

You wrote a value, so it is true.
Read it back, and you expect the system to return what you wrote.

## The right mental model

**The system runs whether or not you look at it.**

There is a real-time loop executing at a fixed rate like 1 kHz, and it does not pause for your script.

Your test does not drive the system.
It reaches into a running machine, changes something, and observes the consequences.

Rewrite the snippet honestly:

```
set(throttle, 30)          # takes effect sometime in the next cycle or two
                           # ...physics happens for a while...
assert read(power) < limit # a value sampled a few ms ago, maybe
```

Both lines are approximate in time.
Neither is incorrect.
They are just not instantaneous, and the gap between them is the system's actual behavior.

## Three clocks that do not agree

| Clock | Character |
| --- | --- |
| **The real-time clock** | The bench's loop. Precise, fixed, and the only one that matters for physics. |
| **The DUT's clock** | Its own scheduler, which is free-running, unsynchronized with the bench, and running its tasks at its own rates. |
| **Your script's clock** | The wall clock on a general-purpose operating system. Coarse, jittery, subject to garbage collection, network hiccups, and someone else logging in. Good to maybe tens of milliseconds under favorable conditions. |

Any assertion whose precision depends on your script's clock is worth roughly nothing.
Make each assertion against timestamps that came from the real-time side.
That is why [[capture]] exists and why it is not just "read in a loop."

## The three rules

### Never assert immediately after acting

Between cause and effect sit at least:

- the bench's next cycle
- the transport
- the DUT's next task activation
- the physics settling
- the sampling of the result

Wait for a *condition*, with a timeout, not for a fixed sleep you tuned until it passed.

See [[avoid-immediately-reading-after-writing]].

### Anything with real timing requirements belongs on the real-time side

You describe the waveform, the trigger, or the capture window and the bench executes it.

Your script arms and queries, but it does not participate in the loop.

See [[avoid-relying-on-scripting-for-time-sensitive-tasks]].

### Each number has a rate

The rate is part of the number's meaning.
A signal on a 100 ms cycle carries no information about a 10 ms event, no matter how fast you sample it downstream.
Before asserting on any value, know its update rate at its source.

See [[avoid-undersampling]].

## The fixed-sleep warning sign

`sleep(0.5)` scattered through a test suite is the most reliable indicator of an engineer who did not make this jump yet.

Each one of those is a guess.
Under load, on a slower bench, or on a bad day, it becomes an intermittent failure.
Intermittent failures are how test suites lose the trust of the people who depend on them.

The alternative:

- Wait for the condition
- Bound it with a timeout
- Fail with a message that says what you waited for and what you saw instead
