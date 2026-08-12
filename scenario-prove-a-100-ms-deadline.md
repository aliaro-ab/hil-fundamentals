---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[capture]]"
  - "[[stimulate]]"
related_to:
  - "[[time-is-the-hard-part]]"
  - "[[avoid-undersampling]]"
---
# Scenario: prove a 100 ms deadline

## The goal

```
When the operator issues a stop request:
    the controller shall command zero output within 100 ms
```

It sounds trivial, and it is the card most people get incorrect.

Work out what you need before reading on.

---

## A good answer

### The incorrect instinct

The incorrect instinct is worth naming first.
Send the request, `sleep(0.1)`, read the output, assert it is zero.
This proves almost nothing.
Your script's timing is good to maybe tens of milliseconds.
You cannot measure a 100 ms deadline with a 30 ms ruler.
And a *pass* here is even more misleading than a fail.
See [[avoid-immediately-reading-after-writing]].

### Take both timestamps from the real-time side

Both timestamps must come from the real-time side.
That is the entire point of this card.

### Set up the capture

[[capture]] on both the stop request and the commanded output, **in the same capture** so they share a time base.
Separate captures can be skewed, and the skew can easily be a large fraction of your 100 ms budget.
Sample at 1 kHz or better, at least 10× the deadline.
See [[avoid-undersampling]].

### Trigger on the request

Use a pre-trigger so you can see the steady state before it and confirm the system was actually in a state where the requirement applies.
See [[triggers-and-windows]].

### Issue the request

[[stimulate]] at whichever boundary the operator interface really uses.
That can be [[the-network|the network]], a discrete at [[physical-pins|physical pins]], or a call at [[the-service-layer|the service layer]].
It matters.
A network request inherits the bus cycle time.
If the frame carrying it is on a 50 ms cycle, the bus can consume half your budget before the DUT even hears you.
That is a real finding, not a bench artifact.

### Measure from the trace

Find the request edge, find where the output first meets the zero criterion, subtract.
Assert the difference is under 100 ms.

### Define "zero" precisely

Exactly zero, or below a threshold?
Reached, or reached and stayed?
A command that dips to zero and rebounds satisfies a naive check and violates the intent.
Write the criterion down.

## Key questions

| Question | Answer |
| --- | --- |
| Where does the clock come from? | The real-time side. Always. |
| Are the two signals in one capture? | They must be. |
| Is the sample rate adequate? | 10× minimum. |
| Where does the deadline start? At the function call, or when the signal physically changed? | The latter, and they differ by more than you expect. |

## Limitations

- **Worst case.** One run gives you one number. A deadline is a claim about *each* run. Repeat it dozens of times, under varying load and at different phases relative to the DUT's task schedule, and assert on the maximum. A response that is 40 ms typical and 130 ms once in fifty is a failing design and a passing test.
- **Whether the deadline is met when the DUT is busy.** Try it during heavy bus load or a fault storm.
