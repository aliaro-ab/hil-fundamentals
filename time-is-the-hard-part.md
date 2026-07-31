---
type: Concept
status: Active
related_to:
  - "[[capability-capture]]"
  - "[[capability-stimulate]]"
  - "[[pitfall-python-loop-as-control-loop]]"
  - "[[pitfall-write-then-read-immediately]]"
_organized: true
---
# Time is the hard part

## The mental model you arrive with

In ordinary software, this works:

```
set(throttle, 30)
assert read(power) < limit
```

You wrote a value, so it's true. Read it back, and you expect what you wrote to be returned. 

## The mental model you need

**The system is running whether or not you are looking at it.**

There is a real-time loop executing at a fixed rate like 1 kHz, and it does not pause for your script. 

Your test does not drive the system. It reaches into a running machine, changes something, and observes the consequences.

Rewrite the snippet honestly:

```
set(throttle, 30)          # takes effect sometime in the next cycle or two
                           # ...physics happens for a while...
assert read(power) < limit # a value sampled a few ms ago, maybe
```

Both lines are approximate in time. Neither is wrong; they're just not instantaneous, and the gap between them is the system's actual behaviour.

## Three clocks, and none of them agree

**The real-time clock** — the bench's loop. Precise, fixed, and the only one that matters for physics.

**The DUT's clock** — its own scheduler, free-running, unsynchronised with the bench, and running its tasks at its own rates.

**Your script's clock** — the wall clock on a general-purpose operating system. Coarse, jittery, subject to garbage collection, network hiccups, and someone else logging in. Good to maybe tens of milliseconds on a good day.

Any assertion whose precision depends on your script's clock is worth roughly nothing. Assertions must be made against timestamps that came from the real-time side — which is why [[capability-capture]] exists and why it isn't just "read in a loop."

## The three rules that follow

### **Never assert immediately after acting**

Between cause and effect sit at least: 

- the bench's next cycle
- the transport
- the DUT's next task activation
- the physics settling
- the sampling of the result

Wait for a *condition*, with a timeout, not for a fixed sleep you tuned until it passed. 

See [[pitfall-write-then-read-immediately]].

### **Anything with real timing requirements belongs on the real-time side**

You describe the waveform, the trigger, or the capture window and the bench executes it.

Your script arms and queries, but it does not participate in the loop. 

See [[pitfall-python-loop-as-control-loop]].

### **Every number has a rate, and the rate is part of its meaning**

A signal on a 100 ms cycle carries no information about a 10 ms event, no matter how fast you sample it downstream. Before asserting on any value, know its update rate at its source.

See [[pitfall-sample-rate-blindness]].

## Fixed sleeps: the tell

`sleep(0.5)` scattered through a test suite is the most reliable indicator of an engineer who hasn't made this jump yet. 

Every one of those is a guess. Under load, on a slower bench, or on a bad day, it becomes an intermittent failure, and intermittent failures are how test suites lose the trust of the people who depend on them.

The alternative:

- Wait for the condition
- Bound it with a timeout
- Fail with a message that says what you were waiting for and what you saw instead
