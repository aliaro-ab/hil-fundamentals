---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[capability-capture]]"
  - "[[capability-stimulate]]"
related_to:
  - "[[time-is-the-hard-part]]"
  - "[[pitfall-sample-rate-blindness]]"
---
# Scenario: prove a 100 ms deadline

## The goal

```
When the operator issues a stop request:
    the controller shall command zero output within 100 ms
```

Sounds trivial. It is the card most people get wrong.

---

## A good answer

**The wrong instinct first**, because it's worth naming: send the request, `sleep(0.1)`, read the output, assert it's zero. This proves almost nothing. Your script's timing is good to maybe tens of milliseconds; you can't measure a 100 ms deadline with a 30 ms ruler. And a *pass* here is even more misleading than a fail. See [[pitfall-write-then-read-immediately]].

**Both timestamps must come from the real-time side.** That's the whole card.

**Set up the capture.** [[capability-capture]] on both the stop request and the commanded output, **in the same capture** so they share a time base. Separate captures may be skewed, and the skew can easily be a large fraction of your 100 ms budget. Sample at 1 kHz or better — at least 10× the deadline. See [[pitfall-sample-rate-blindness]].

**Trigger on the request, with a pre-trigger** so you can see the steady state before it and confirm the system was actually in a state where the requirement applies. See [[triggers-and-windows]].

**Issue the request.** [[capability-stimulate]] at whichever boundary the operator interface really uses — [[boundary-network]], a discrete at [[boundary-physical-pins]], or a call at [[boundary-service-layer]]. It matters: a network request inherits the bus cycle time, and if the frame carrying it is on a 50 ms cycle, half your budget can be consumed before the DUT has even heard you. That's a real finding, not a bench artefact.

**Measure from the trace.** Find the request edge, find where the output first meets the zero criterion, subtract. Assert the difference is under 100 ms.

**Define "zero" precisely.** Exactly zero, or below a threshold? Reached, or reached and stayed? A command that dips to zero and rebounds satisfies a naive check and violates the intent. Write the criterion down.

## The questions this card is really asking

| Question | Answer |
| --- | --- |
| Where does the clock come from? | The real-time side. Always. |
| Are the two signals in one capture? | They must be. |
| Is the sample rate adequate? | 10× minimum. |
| Where does the deadline start — when you called the function, or when the signal physically changed? | The latter, and they differ by more than you'd like. |

## What this test does not catch

- **Worst case.** One run gives you one number. A deadline is a claim about *every* run. Repeat it — dozens of times, under varying load, at different phases relative to the DUT's task schedule — and assert on the maximum. A response that's 40 ms typical and 130 ms once in fifty is a failing design and a passing test.
- **Whether the deadline is met when the DUT is busy.** Try it during heavy bus load or a fault storm.
