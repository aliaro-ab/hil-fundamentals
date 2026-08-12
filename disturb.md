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

**What occurs when something fails?**

The ability to inject fault conditions is the capability that most justifies the existence of a HIL bench.
It is the one you genuinely cannot exercise any other way.

Most of a controller's software is not the control algorithms.
It is the error handling:

- detection of a failed sensor
- graceful degradation
- entry into a safe state
- notification of the user or technician

Behavior on an error is the hardest to get right, the most safety-relevant, and by far the least exercised in normal operation.
A bench lets you run it a thousand times a day.

## Where it works

| Layer | Boundary | Faults you can inject | Why it matters |
| --- | --- | --- | --- |
| **Logically** | [[the-plant-model]] | Stuck actuator, leak, seized bearing, depleted battery | The sensor still works. The thing it measures is incorrect. Tests the *plausibility* logic rather than the hardware diagnostics. |
| **In calibration** | [[device-internals]] | A deliberately incorrect threshold, gain, or lookup table | Tests how the software behaves when its own tuning data is incorrect, which is what a bad flash or a partial calibration load produces. |
| **Electrically** | [[physical-pins]] | Open circuit, short to supply, short to ground, short between two pins, added series resistance, degraded contact | The physically real one. Relays and switch matrices sit between the bench and the DUT connector, so the controller experiences an actual broken wire. |
| **On power** | [[physical-pins]] | Brownouts, sags, cranking profiles, hard resets at awkward moments | The supply rails are pins too. However, they usually get their own programmable supply rather than the fault matrix. Reset-during-write is a rich source of real bugs. |
| **On the network** | [[the-network]] | Stop transmitting a frame, send it late, send a stale counter, corrupt a checksum, flood the bus, send an out-of-range value | Communication faults are extremely common in the field and cheap to inject. |
| **At the service layer** | [[the-service-layer]] | Return an error, time out, respond out of order, disappear mid-session | Exercises the client-side error paths that only appear when a peer misbehaves. |
| **In diagnostics** | [[the-diagnostic-channel]] | Malformed requests, and requests made in the incorrect session or machine state | Checks that a refusal carries the correct negative response code instead of failing silently. |

## The four-part fault test

Give each fault test the same shape:

| Part | What you check |
| --- | --- |
| **Detection** | Does the DUT notice? Within what time? |
| **Reaction** | Does it enter the correct degraded state? |
| **Reporting** | Does it log the right fault? Use [[interrogate]]. |
| **Recovery** | When the fault clears, does the DUT return to normal? Does the fault stay logged as history? Does it need a power cycle? Is that intended? |

> [!tip] > Part 4 is where the good bugs live.
Latching a fault that must self-clear, and self-clearing one that must latch, are both classic field failures.

## Safety

Fault injection hardware can put supply voltage onto a pin that was never designed to see it.

Know your bench's interlocks, know which combinations are prohibited, and never invent a new short by hand on a bench you do not yet understand.

> [!danger] > This is one of the few places on a test bench where you can destroy an expensive DUT or the bench itself in a millisecond.
