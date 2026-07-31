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
# Scenario: another node stops talking

## The goal

```
When the message carrying vehicle speed is absent for more than 300 ms:
    the controller shall enter a degraded mode
    use a safe substitute value
    store a communication fault

When the message returns:
    the controller shall recover automatically
```

Work out what you need before reading on.

---

## A good answer

### Understand how the bus is simulated

The message in question is being sent by the bench, standing in for a controller that isn't on this rack. See [[boundary-network]] for details.

### Establish the precondition

Establish normal operation, with the restbus healthy, the speed signal present at its normal cycle time, and the DUT not already degraded. Verify it.

### Arm the capture

[[capability-capture]] at frame level, not just signal level. You need to see the *absence* of a message, and a decoded signal view will happily keep showing you the last received value forever. Capture the frame in question, the DUT's mode indication, and its output. Turn the pre-trigger on.

### Stop the frame

[[inject-a-fault]] at [[boundary-network|the network]] by stopping transmission of that frame. Note the timing subtlety. The deadline starts at the *last successfully received* frame, not at the moment you called stop. Read that from the capture.

### Assert on the capture

Verify detection within 300 ms of the last received frame, the correct substitute value, and the correct degraded mode. And check the boundary. Does it correctly *not* trip at 290 ms? A timeout that fires too early is as much a defect as one that fires too late, and only testing the far side of the boundary leaves half the requirement unverified.

### Interrogate the diagnostics

[[capability-interrogate]] and confirm the correct communication fault code and the correct status.

### Verify recovery

Resume transmission. Confirm automatic recovery, and confirm the fault becomes history rather than remaining active.

## Variations

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

## Limitations

Bus-level problems, such as arbitration under load, error frames, bus-off recovery, and wiring faults on the bus itself. Those are separate tests at [[boundary-physical-pins|physical pins]] and at the frame level.
