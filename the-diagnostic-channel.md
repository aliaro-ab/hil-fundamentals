---
type: Boundary
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[interrogate]]"
  - "[[device-internals]]"
---
# The diagnostic channel

**The device's own account of itself through the interface a technician uses.**

A standardized request/response channel, usually layered over the network.
External tools use it to ask the device about its health, identity, and history, and occasionally to command it to do things.

## Distinction from device internals

[[device-internals]] is a developer's back door, with raw memory, symbol names, and no contract.
The diagnostic channel is a **published interface with a specification**, used by garages, field tools, fleet systems, and end-of-line testers.

That distinction is the whole reason it is a separate boundary.
A fault correctly set in RAM but incorrectly reported over diagnostics is a shipped defect.
Only a test at this boundary can catch it.

## What you can ask

| Request | What it gives you |
| --- | --- |
| **Fault memory** | Active faults, stored history, occurrence counts, status bits, aging |
| **Freeze frames / snapshots** | The conditions recorded when the device set a fault |
| **Clearing** | Wipe the memory, then verify it stays clear or correctly returns |
| **Identification** | Hardware part number, software version, calibration ID, serial |
| **Live data** | The device's own reported sensor values, in its own units |
| **Routines** | Self-tests, actuator drives, resets, service procedures |
| **Security access** | Unlocking privileged operations behind a challenge/response |

## Capabilities

| Capability | What it means here |
| --- | --- |
| [[observe]] | Poll a live data item, slowly, as a request rather than a continuous read |
| [[stimulate]] | Only what the specification exposes: routines, writes, session and mode changes |
| [[capture]] | The request and response trace, which is evidence in its own right |
| [[disturb]] | Malformed and incorrect-state requests, to check the negative responses are correct |
| [[interrogate]] | The whole point of this boundary, and the only place the verb works properly |

[[replay]] does not apply here.
You cannot blindly play back a diagnostic exchange, because each response depends on the session state at the moment of asking.

## Constraints

### Sessions expire

Many operations require entering a non-default session first, and sessions time out if you stop talking.
Long test steps can silently drop you back to default and the next request fails for reasons that look unrelated.

### Responses are slow

Latencies in the tens to hundreds of milliseconds are normal, and some routines take seconds.
Never place a diagnostic call inside a tight timing assertion.

### Communication is sequential

The channel carries one conversation at a time, which makes diagnostic steps a natural bottleneck in an otherwise parallel test.

### Requests are state-gated

The device refuses some requests unless the machine is stationary, unlocked, unpowered, or in a service mode.
A "rejected" response is frequently correct behavior, not a bug.

### Negative responses are meaningful

A refusal carries a reason code.
Reading it is usually faster than guessing.

## Fault status is not a boolean

This is the most common mistake at this boundary.
A fault is not simply present or absent.
It has status bits distinguishing *confirmed this cycle*, *pending*, *tested this cycle*, *failed since last clear*, and more.
A test that checks only "is this code in the list?" will pass in cases it must fail.
More annoyingly, it will fail intermittently in cases it must pass, because the code is present but not yet confirmed.

Learn the status model on your project before writing fault-memory assertions.
See [[interrogate]].
