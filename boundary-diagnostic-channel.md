---
type: Boundary
belongs_to: "[[capability-grid]]"
related_to:
  - "[[capability-interrogate]]"
  - "[[boundary-device-internals]]"
---
# The diagnostic channel

**The device's own account of itself through the interface a technician uses.**

A standardized request/response channel, usually layered over the network, through which external tools ask the device about its health, identity, and history, and occasionally command it to do things.

## What makes it distinct from device internals

[[boundary-device-internals]] is a developer's back door, with raw memory, symbol names, and no contract. The diagnostic channel is a **published interface with a specification**, used by garages, field tools, fleet systems, and end-of-line testers.

That distinction is the whole reason it's a separate boundary. A fault correctly set in RAM but incorrectly reported over diagnostics is a shipped defect. Only a test at this boundary can catch it.

## What you can ask

| Request | What it gives you |
| --- | --- |
| **Fault memory** | Active faults, stored history, occurrence counts, status bits, aging |
| **Freeze frames / snapshots** | The conditions recorded when a fault was set |
| **Clearing** | Wipe the memory, then verify it stays clear or correctly returns |
| **Identification** | Hardware part number, software version, calibration ID, serial |
| **Live data** | The device's own reported sensor values, in its own units |
| **Routines** | Self-tests, actuator drives, resets, service procedures |
| **Security access** | Unlocking privileged operations behind a challenge/response |

## The behavior that surprises people

**Sessions.** Many operations require entering a non-default session first, and sessions time out if you stop talking. Long test steps can silently drop you back to default and the next request fails for reasons that look unrelated.

**Responses are slow.** Latencies in the tens to hundreds of milliseconds are normal, and some routines take seconds. Never place a diagnostic call inside a tight timing assertion.

**Communication is sequential.** The channel carries one conversation at a time, which makes diagnostic steps a natural bottleneck in an otherwise parallel test.

**Requests are state-gated.** Some requests are refused unless the machine is stationary, unlocked, unpowered, or in a service mode. A "rejected" response is often correct behavior, not a bug.

**Negative responses are meaningful.** A refusal carries a reason code. Reading it is usually faster than guessing.

## Fault status is not a boolean

This is the most common mistake at this boundary. A fault isn't simply present or absent. It has status bits distinguishing *confirmed this cycle*, *pending*, *tested this cycle*, *failed since last clear*, and more. A test that checks only "is this code in the list?" will pass in cases it should fail and, more annoyingly, fail intermittently in cases it should pass, because the code is present but not yet confirmed.

Learn the status model on your project before writing fault-memory assertions. See [[capability-interrogate]].
