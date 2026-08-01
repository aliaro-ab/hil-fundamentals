---
type: Pitfall
related_to:
  - "[[capability-capture]]"
  - "[[time-is-the-hard-part]]"
  - "[[the-network]]"
---
# Avoid undersampling

## The symptom

The test says the output must never exceed 16 V. It passes, every time. Then a unit fails in the field with a burned-out component, and the oscilloscope trace shows a 2 ms spike to 19 V that your bench never saw.

Or take the inverse case. A signal seems to oscillate at 3 Hz in your captures, so you spend a week hunting a 3 Hz instability that does not exist. It's a 97 Hz oscillation aliased by your 100 Hz capture.

## What's happening

### The half-sample-rate limit

A capture cannot show you anything faster than roughly half its sample rate. Faster content doesn't vanish politely. It folds down and appears as a plausible, entirely fictitious slow signal.

And "never exceeds" is a claim about *every instant*. Sampling at 100 Hz, you observe 1% of the timeline and infer the rest. For slow physics that inference is sound. For anything with fast transients it's a guess.

## The rates you need to know

There's a chain, and the slowest link governs:

| Link in the chain | What sets it |
| --- | --- |
| **The physics** | How fast can the real quantity actually change? |
| **The source** | The model step, the DUT task period, the network cycle time, the I/O update rate |
| **The capture** | What you configured |
| **Your analysis** | Any downsampling or filtering you applied afterwards |

Sampling a network signal at 1 kHz when the sender transmits it every 100 ms gets you a thousand samples a second of a value that changes ten times a second. The extra 990 are decoration. See [[the-network]].

## What to do

### Know the source rate before asserting

For every signal in a test, be able to state its update rate at origin. If you can't, find out. This is a five-minute question that prevents week-long investigations.

### Match the rate to the claim

For steady-state within tolerance, slow capture is fine. For response time, use at least 10× the deadline you're measuring. For peak or overshoot, go as fast as the bench allows, and be honest that you may still miss the true peak. For never-exceeds on a fast signal, sampling may be the wrong tool. Consider a hardware limit detector, a min/max accumulator on the real-time side, or a scope.

### Prefer real-time detection for extremes

Many benches can evaluate a condition every cycle and latch the result. "Did this ever exceed 16 V, checked at 10 kHz" is a far stronger claim than "none of my 100 Hz samples exceeded 16 V", and it costs less bandwidth than capturing everything at 10 kHz.

### Be suspicious of clean low-frequency oscillations

If a signal shows a tidy oscillation near a simple fraction of your sample rate, suspect aliasing before you suspect physics. Recapture at a different rate. Real signals keep their frequency; aliases move.

## Recommended practice

Write the sample rate in the test, next to the assertion, as a comment or a parameter. It forces the question at the moment you can still answer it cheaply, and it tells the next reader what the test actually proved.
