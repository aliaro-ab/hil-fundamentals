---
type: Capability
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[triggers-and-windows]]"
  - "[[observe]]"
  - "[[avoid-undersampling]]"
_organized: true
---
# Capture

**What did this value do over that window?**

Capture is [[observe]] done over many values, at a known rate, over a defined window, with known time alignment.

Almost all interesting test assertions are statements about a captured trace rather than a single reading.

## What defines a capture

| Dimension | What it means |
| --- | --- |
| **Which signals** | A named set, captured together so they share a time base. You cannot reliably compare signals captured separately. |
| **At what rate** | Fast enough to see what you test. See [[avoid-undersampling]]. |
| **Over what window** | Bounded by time, by sample count, or by conditions. See [[triggers-and-windows]]. |
| **Aligned to what clock** | A shared time base for each signal you intend to compare. |

The clock dimension is the one people miss.
If your test asserts `output responded within 50 ms of input`, the input and output samples need to share a time base.
Cross-boundary captures (like a model variable against a network signal) frequently do not, and the skew is a potential source of failures.

## Why a trace instead of a number

Requirements are almost always temporal:

| Requirement | What answering it needs |
| --- | --- |
| `shall respond within 100 ms` | Two timestamps |
| `shall not overshoot by more than 5%` | The peak |
| `shall remain within band during the maneuver` | Each sample in the window |
| `shall debounce for 2 seconds before reacting` | The whole transition |

A polling loop cannot answer any of these, because your loop's sampling is neither fast enough nor evenly spaced.

## Trigger before you need it

The most common capture mistake is to run the test, see the failure, and realize you were not recording.
Configure the capture *before* the stimulus, arm it, then act.
Storage is cheap, test bench time is not.

Better still, use a pre-trigger.
Most benches continuously buffer so that when the trigger fires it keeps some window from *before* it.
That is how you catch the cause of an event rather than only its aftermath.

## Where it works

Capture is available at each boundary.

| Boundary | What you capture there |
| --- | --- |
| [[the-plant-model]] | Model variables |
| [[device-internals]] | Internal values |
| [[physical-pins]] | Analog and digital lines |
| [[the-network]] | Frames or decoded signals |
| [[the-service-layer]] | Message traces |

The important question at each boundary is **what rate, and does it share a clock with the others?**

## The output is data rather than a verdict

A capture gives you a table of samples.
Deciding pass or fail is your analysis code's job.
Keep that code separate from the test that produced the data.
Then you can re-analyze an old capture when someone changes their mind about the tolerance.
