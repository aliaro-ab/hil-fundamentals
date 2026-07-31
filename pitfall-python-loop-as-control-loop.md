---
type: Pitfall
related_to:
  - "[[time-is-the-hard-part]]"
  - "[[capability-stimulate]]"
---
# Avoid relying on scripting for time-sensitive tasks

## The symptom

You need a 5 Hz sine on an input, so you write the obvious thing:

```
for t in timesteps:
    set(input, amplitude * sin(2*pi*5*t))
    sleep(0.001)
```

What the DUT receives is not a sine wave. It's a staircase with randomly-sized steps, occasionally frozen for 40 ms by a garbage-collection pause, sometimes for 200 ms by competing load on the machine.

Then you spend a day investigating why the controller's response looks nothing like the frequency-response test in the requirement.

## Why it can't work

Your test script runs on a general-purpose operating system. It is scheduled at the convenience of a kernel that is also handling networking, disk, a desktop environment, and whatever else is logged in. `sleep(0.001)` guarantees only a minimum. The pause lasts not less than a millisecond, with no upper bound.

Jitter of tens of milliseconds is normal. Occasional pauses of hundreds of milliseconds are normal. For a 5 Hz signal, that jitter is a substantial fraction of a period. You are generating noise with a sinusoidal trend.

Meanwhile the bench's real-time side is doing exactly this job, at a fixed rate, with microsecond determinism, because that's what it's for.

## The fix

**Describe the signal; let the bench generate it.**

```
sig = sine(freq=5, amplitude=a, offset=o, duration=10)
stimulus = create_stimulus(target=input, signal=sig)
stimulus.start()
stimulus.wait_until_done(timeout=15)
```

Your script's job is to *specify, arm, start, and query*. The real-time side executes. This is the correct division of labor for everything time-critical on a bench:

| Time-critical thing | Where it belongs |
| --- | --- |
| Generating a waveform | real-time side, described by you |
| Sampling at rate | real-time side, configured by you |
| Trigger conditions | real-time side, defined by you |
| Closing the physics loop | real-time side, always |
| Deciding what to run, and pass/fail | your script |

See [[capability-stimulate]] and [[time-is-the-hard-part]].

## Where the boundary lies

As a rule of thumb, **if the timing of an event matters to the DUT's behavior, it cannot originate in your script.**

Your script may legitimately:

- Set slow values such as ambient temperature, configuration, or a mode request
- Sequence phases of a test
- Wait for conditions with generous timeouts
- Fetch and analyze results

It may not:

- Generate waveforms
- Implement a feedback loop
- Time anything you intend to assert on
- React to an event within a deadline

## Unsupported stimuli

Occasionally you need a stimulus the bench's built-in generators don't cover. The answer is still not a loop in your script. It's a table of samples, uploaded as an arbitrary waveform and played by the real-time side. Compute it offline, play it in real time. See [[capability-replay]].
