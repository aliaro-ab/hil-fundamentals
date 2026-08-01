---
type: Capability
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[capability-capture]]"
  - "[[time-is-the-hard-part]]"
  - "[[avoid-immediately-reading-after-writing]]"
---

# Observe

**What is this value right now?**

The simplest verb, and the one that misleads people most. Reading a single value from a running system is not like reading a variable in your own program.

## What you get

What you get is not "the value" but **a value that was true at some moment, delivered to you a little later.** Between the sample and your receipt of it sits a scheduler, a transport, and a queue. On a bench running at 1 kHz, the number in your hand may be up to several milliseconds stale, and nothing about the API will tell you that.

For slow physics (temperature, state of charge, pressure in a big vessel) this is irrelevant. For anything that moves fast, a single observation is close to meaningless. Use [[capability-capture|capture]] instead.

## Where it works

At every boundary, with different meanings:

| Boundary | What observing means there |
| --- | --- |
| [[the-plant-model]] | Read a simulation variable |
| [[device-internals]] | Read a variable out of the device's live memory |
| [[physical-pins]] | Measure a voltage or current |
| [[the-network]] | Read the last received value of a bus signal |
| [[the-service-layer]] | Read a field or call a getter |

Note the network case. "The last value received" may be from 100 ms ago if the sender is slow or has stopped. A stale bus signal reads as a perfectly plausible number.

## Right and wrong questions

| Verdict | Question |
| --- | --- |
| Right | "After the system settles, is the commanded current within tolerance?" |
| Wrong | "Immediately after I write the setpoint, has the output changed?" See [[avoid-immediately-reading-after-writing]]. |
| Wrong | "Did the voltage ever exceed 16 V?" A single observation cannot answer a question about *ever*. That's [[capability-capture|capture]]. |

## Rule of thumb

Observe is for **steady state and preconditions**. If the word "when," "during," "within," "ever," or "how fast" appears in your test requirement, you need capture, not observe.
