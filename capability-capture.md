---
type: Capability
belongs_to: "[[capability-grid]]"
related_to:
  - "[[triggers-and-windows]]"
  - "[[capability-observe]]"
  - "[[pitfall-sample-rate-blindness]]"
_organized: true
---
# Capture

**What did this value do over that window?**

Capture is [[capability-observe]] done over many values, at a known rate, over a defined window, with known time alignment.

Almost every interesting test assertion is a statement about a captured trace rather than a single reading.

## The four things that define a capture

| Dimension | What it means | See also |
| --- | --- | --- |
| **Which signals** | A named set, captured together so they share a time base. Signals captured separately cannot be reliably compared against each other. |  |
| **At what rate** | Fast enough to see what you're testing. | [[pitfall-sample-rate-blindness]] |
| **Over what window** | Bounded by time, by sample count, or by conditions. | [[triggers-and-windows]] |
| **Aligned to what clock** | If your test asserts `output responded within 50 ms of input` the input and output samples need to share a time base. Cross-boundary captures (like a model variable against a network signal) often don't, and the skew is a potential source of failures. |  |

## Why the trace, not the number

Requirements are almost always temporal:

| Requirement | What answering it needs |
| --- | --- |
| `shall respond within 100 ms` | Two timestamps |
| `shall not overshoot by more than 5%` | The peak |
| `shall remain within band during the maneuver` | Every sample in the window |
| `shall debounce for 2 seconds before reacting` | The whole transition |

None of these can be answered by polling in a loop, because your loop's sampling is neither fast enough nor evenly spaced.

## Trigger before you need it

The most common capture mistake is run the test, see the failure, and realise you weren't recording. Configure the capture *before* the stimulus, arm it, then act. Storage is cheap, test bench time is not.

Better still, use a pre-trigger. Most benches continuously buffer so that when the trigger fires it keeps some window from *before* it. That's how you catch the cause of an event rather than only its aftermath.

## Where it works

Everywhere.

| Boundary | What you capture there |
| --- | --- |
| [[boundary-plant-model]] | Model variables |
| [[boundary-device-internals]] | Internal values |
| [[boundary-physical-pins]] | Analog and digital lines |
| [[boundary-network]] | Frames or decoded signals |
| [[boundary-service-layer]] | Message traces |

The important question at each: **what rate, and does it share a clock with the others?**

## The output is data, not a verdict

A capture gives you a table of samples. Deciding pass or fail is your analysis code's job, and it should be separate from the test that produced the data — so you can re-analyse an old capture when someone changes their mind about the tolerance.
