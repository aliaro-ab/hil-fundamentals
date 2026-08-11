---
type: Capability
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[physical-pins]]"
  - "[[interrogate]]"
  - "[[scenario-a-sensor-wire-breaks]]"
_organized: true
---
# Disturb

**What happens when something goes wrong?**

The ability to inject fault conditions is the capability that most justifies the existence of a HIL bench because it is the one you genuinely cannot exercise any other way.

Most of a controller's software is not the control algorithms. It's the error handling:

- detecting a failed sensor
- degrading gracefully
- entering a safe state
- notifying the user or technician

Behavior on an error is the hardest to get right, the most safety-relevant, and by far the least exercised in normal operation. A bench lets you run it a thousand times a day.

## The layers you can break

| Layer | Boundary | Faults you can inject | Why it matters |
| --- | --- | --- | --- |
| **Electrically** | [[physical-pins]] | Open circuit, short to supply, short to ground, short between two pins, added series resistance, degraded contact | The physically real one. Relays and switch matrices sit between the bench and the DUT connector, so the controller experiences an actual broken wire |
| **Logically** | [[the-plant-model]] | Stuck actuator, leak, seized bearing, depleted battery | The sensor still works; the thing it's measuring is wrong. Tests the *plausibility* logic rather than the hardware diagnostics |
| **On the network** | [[the-network]] | Stop transmitting a frame, send it late, send a stale counter, corrupt a checksum, flood the bus, send an out-of-range value | Communication faults are extremely common in the field and cheap to inject |
| **At the service layer** | [[the-service-layer]] | Return an error, time out, respond out of order, disappear mid-session | Exercises the client-side error paths that only appear when a peer misbehaves |
| **On power** | [[physical-pins]] | Brownouts, sags, cranking profiles, hard resets at awkward moments | The supply rails are pins too, though they usually get their own programmable supply rather than the fault matrix. Reset-during-write is a rich source of real bugs |

## The four-part fault test

Every fault test should have the same shape:

| Part | What you check |
| --- | --- |
| **Detection** | Does the DUT notice? Within what time? |
| **Reaction** | Does it enter the correct degraded state? |
| **Reporting** | Does it log the right fault? Use [[interrogate\\|interrogate]]. |
| **Recovery** | When the fault clears, does the DUT return to normal? Does the fault stay logged as history? Does it need a power cycle, and is that intended? |

> [!tip]
> Part 4 is where the good bugs live. Latching a fault that should self-clear, or self-clearing one that should latch, are both classic field failures.

## Safety

Fault injection hardware can put supply voltage onto a pin that was never designed to see it.

Know your bench's interlocks, know which combinations are prohibited, and never invent a new short by hand on a bench you don't yet understand.

> [!danger]
> This is one of the few places on a test bench where you can destroy an expensive DUT or the bench itself in a millisecond.
