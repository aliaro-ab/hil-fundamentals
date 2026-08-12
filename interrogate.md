---
type: Capability
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[the-diagnostic-channel]]"
  - "[[disturb]]"
_organized: true
---

# Interrogate

**What does the device think failed?**

Each other verb observes the device from outside.
Interrogate asks the device for its own account of itself, through the standardized channel that a service technician uses.

## Why it is a separate verb

You can, in principle, read the same fault flags out of memory with [[observe]] at [[device-internals|device internals]].
That is a mistake.

The diagnostic channel is what a garage, a fleet system, or a field engineer will actually use.
If the internal flag is set correctly but the diagnostic response is malformed, mislabeled, or missing, the product is broken.
Reading memory directly never catches that.

Testing through the diagnostic channel tests the thing the customer touches.
Reading memory tests the thing the developer touches.
You want both, and you must know which one you use.

## What you can ask for

In outline: fault memory, freeze frames, identification, live data, routines and modes, and the security access that unlocks the privileged ones.

The full request catalogue belongs to the boundary rather than the verb, so it lives in one place.
See the table in [[the-diagnostic-channel]].

## Where it works

Unlike the other five verbs, interrogate works at essentially one boundary.

| Boundary | What interrogating means there |
| --- | --- |
| [[the-diagnostic-channel]] | The home of this verb. A published request/response interface for the device's own account of itself. |
| [[the-network]] | Only as the transport. Diagnostic traffic is usually layered on the bus. The frames are visible here while the meaning lives at the diagnostic channel. |

At [[the-plant-model]], [[device-internals]], [[physical-pins]], and [[the-service-layer]] there is nothing to interrogate.
None of them holds an opinion about its own health.

## The characteristic shape

The diagnostic channel is **request/response and stateful**, which makes it feel unlike everything else on a bench:

- You ask, then wait. Responses can take hundreds of milliseconds.
- Many operations require entering a non-default mode first, and sessions time out if you go quiet.
- Some operations are locked behind a security handshake.
- The device permits some only in certain vehicle or machine states.

So diagnostic steps are slow and sequential, while the rest of your test is fast and parallel.
Plan for that, and do not put a diagnostic query inside a tight timing assertion.

## Where it fits in a test

Almost always as the **reporting** step, the third of the four parts of a fault test described in [[disturb]]:

1. Break something
2. Confirm the reaction with [[capture]]
3. **Interrogate** to confirm the correct fault code, with the right status bits and a sane freeze frame
4. Clear the fault, remove the cause, confirm the device agrees it is healthy

Step 3 is where you catch the classic bug of the right behavior reported under the incorrect code.
That bug is harmless on the bench but expensive in a service network.
