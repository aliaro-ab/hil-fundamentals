---
type: Capability
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[the-diagnostic-channel]]"
  - "[[fault-injection]]"
_organized: true
---

# Interrogate

**What does the device think is wrong?**

Every other verb observes the device from outside. Interrogate asks the device for its own account of itself, through the standardized channel that a service technician would use.

## Why it is a separate verb

You could, in principle, read the same fault flags out of memory with [[observe|observe]] at [[device-internals|device internals]]. That would be a mistake, for a reason worth internalizing.

### The channel is a product feature

The diagnostic channel is what a garage, a fleet system, or a field engineer will actually use. If the internal flag is set correctly but the diagnostic response is malformed, mislabeled, or missing, the product is broken, and reading memory directly would never have caught it.

Testing through the diagnostic channel tests the thing the customer touches. Reading memory tests the thing the developer touches. You want both, and you should know which one you're doing.

## What you can ask for

| Request | What it gives you |
| --- | --- |
| **Stored faults** | What's currently active, what's historic, how many times, when |
| **Freeze frames** | The conditions captured at the moment a fault was set |
| **Clear** | Wipe the fault memory and confirm it comes back clean |
| **Identification** | Part number, software version, serial, calibration ID |
| **Live values** | The device's own view of its sensors, in its own units |
| **Routines and modes** | Actuator tests, self-tests, unlocking privileged access |

## The characteristic shape

The diagnostic channel is **request/response and stateful**, which makes it feel unlike everything else on a bench:

- You ask, then wait. Responses can take hundreds of milliseconds.
- Many operations require entering a non-default mode first, and sessions time out if you go quiet.
- Some operations are locked behind a security handshake.
- Some are only permitted in certain vehicle or machine states.

So diagnostic steps are slow and sequential, while the rest of your test is fast and parallel. Plan for that, and don't put a diagnostic query inside a tight timing assertion.

## Where it fits in a test

Almost always as the third act of a fault test (see [[fault-injection]]):

1. Break something
2. Confirm the reaction with [[capability-capture|capture]]
3. **Interrogate** to confirm the correct fault code, with the right status bits and a sane freeze frame
4. Clear the fault, remove the cause, confirm the device agrees it's healthy

Step 3 is where you catch the classic bug of the right behavior reported under the wrong code, which is harmless on the bench but expensive in a service network.
