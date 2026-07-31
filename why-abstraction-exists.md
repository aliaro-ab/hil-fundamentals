---
type: Concept
related_to:
  - "[[mil-sil-hil-one-story]]"
  - "[[one-signal-five-names]]"
  - "[[pitfall-hardcoded-bench-names]]"
_organized: true
---
# Why abstraction exists

Everything else in this vault follows from one observation.

**Test cases outlive test benches.**

A test that says `drive the throttle to 30% and confirm the controller trips the overspeed fault` is a statement about the product. It is true in 2026 and it is true in 2032. It is true on a laptop running a pure simulation and true on a test bench with real wiring.

The test bench that runs it is not durable in the same way. Test benches get replaced, chassis get swapped, a vendor gets acquired, the I/O card is discontinued, the project moves from one site to another. If your test is centered around the test bench, you have to rewrite it every time the test bench changes.

## Sources of change

| What changes | How it changes | Why it hurts |
| --- | --- | --- |
| **The device** | Starts as a model, becomes compiled software, ends up as silicon in a housing. | Same behavior, three different things to poke at. See [[mil-sil-hil-one-story]]. |
| **The bench** | Different vendors, different I/O, different naming, different units. | It's a purchase decision made by someone who wasn't thinking about your test. |
| **The signal names** | The model, the controller's symbol file, the wiring diagram, and the network database each have their own name for one physical quantity. | Nothing lines up by default. See [[one-signal-five-names]]. |

## Benefits

The return is not elegance; it is three concrete things:

| What you get | Why it matters |
| --- | --- |
| **Portability** | The same test runs on the next bench, so a bench refresh doesn't torch your regression suite. |
| **Reuse across development stages** | The test written against a model gets rerun against hardware. See [[mil-sil-hil-one-story]]. |
| **A shared vocabulary** | When a test engineer and a bench engineer both say "capture," they mean the same thing. Half of onboarding is just this. |

## Costs

Abstraction is not free, and pretending otherwise breeds cynicism the first time an engineer hits its limits:

- Every layer hides something.
- Abstractions leak hardest around timing. See [[time-is-the-hard-part]].
- A generic interface may have limitations that specific hardware does not. 

## The review question

When you read a line of a test case, ask *is this describing the product, or the bench?*

Product statements belong in the test. Bench statements belong in configuration. Most bad test code is the two of them mixed together.
