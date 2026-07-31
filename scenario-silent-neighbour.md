---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[inject-a-fault]]"
  - "[[capability-capture]]"
  - "[[capability-interrogate]]"
related_to:
  - "[[boundary-network]]"
---

# Scenario: a neighbour stops talking

## The goal

> If the message carrying vehicle speed is absent for more than 300 ms, the controller shall enter a degraded mode, use a safe substitute value, and store a communication fault. It shall recover automatically when the message returns.

---

## A good answer

**Know your restbus.** The message in question is being sent by the bench, standing in for a controller that isn't on this rack. If you don't know that, none of this makes sense. See [[boundary-network]].

**Precondition.** Normal operation, restbus healthy, speed signal present at its normal cycle time, DUT not already degraded. Verify it.

**Arm the capture.** [[capability-capture]] at frame level, not just signal level — you need to see the *absence* of a message, and a decoded signal view will happily keep showing you the last received value forever. Capture the frame in question, the DUT's mode indication, and its output. Pre-trigger on.

**Break it.** [[inject-a-fault]] at [[boundary-network]]: stop transmitting that frame. Note the timing subtlety — the deadline starts at the *last successfully received* frame, not at the moment you called stop. Read that from the capture.

**Assert.** Detection within 300 ms of the last received frame. Correct substitute value. Correct degraded mode. And check the boundary: does it correctly *not* trip at 290 ms? A timeout that fires too early is as much a defect as one that fires too late, and only testing the far side of the boundary leaves half the requirement unverified.

**Interrogate.** [[capability-interrogate]]: correct communication fault code, correct status.

**Recover.** Resume transmission. Confirm automatic recovery, and confirm the fault becomes history rather than remaining active.

## The variations that find real bugs

This is the boundary where fault injection is cheapest and most productive. Each of these is a distinct failure mode and each has shipped as a field defect somewhere:

| Variation | What it asks of the DUT |
| --- | --- |
| **Intermittent** | Message drops for 200 ms repeatedly. Does the timeout reset correctly, or does it accumulate? |
| **Stale but present** | The frame keeps arriving with an unchanging value. Does the DUT notice a frozen signal, or trust it forever? |
| **Frozen rolling counter** | Frame arrives on time, counter doesn't increment. This is precisely what the counter is for; does the DUT check it? |
| **Bad checksum** | Arrives, decodes, fails integrity. Rejected, or used anyway? |
| **Out-of-range value** | 900 km/h. Range-checked, or propagated into the control law? |
| **Too fast** | Sent at 10× the cycle time. Some receivers overflow. |
| **Recovery timing** | One good frame to recover, or several? Is there hysteresis, and should there be? |

## What this test does not catch

Bus-level problems: arbitration under load, error frames, bus-off recovery, wiring faults on the bus itself. Those are separate tests at [[boundary-physical-pins]] and at the frame level.
