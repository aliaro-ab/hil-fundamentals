---
type: Concept
related_to:
  - "[[what-a-hil-test-bench-is]]"
  - "[[why-abstraction-exists]]"
  - "[[replay]]"
_organized: true
---
# What HIL testing produces

The rest of this vault teaches you how to operate a bench. This note is about what the operating is *for*, because "a green suite" is not the deliverable. A HIL program exists to produce evidence, and it's worth knowing who the evidence is for before you write your first test.

## The deliverables

| Deliverable | What it is | Who consumes it |
| --- | --- | --- |
| **A regression suite** | Repeatable evidence that behavior which worked yesterday still works today, across software releases and bench refreshes. | Developers, every time they change something. See [[why-abstraction-exists]]. |
| **Fault-coverage evidence** | Proof that the error handling, which is most of the software and the least exercised part in normal operation, actually works. | Safety and certification processes, which cannot accept "we never saw it fail" as an answer. See [[fault-injection]]. |
| **A reproducible-failure library** | Field failures converted into on-demand bench reproductions, then into permanent regression tests. | Whoever is on the hook when a unit misbehaves in the field. See [[replay]]. |
| **Requirements verification evidence** | For each requirement, a test that discharges it, with a result traceable to the software version and bench configuration that produced it. | Program and release decisions. "Are we done?" is answered against this. |

## Tests discharge requirements

Every [[scenario-cards|scenario card]] in this vault opens with a requirement-shaped goal, and that shape is not decoration. In this discipline a test exists to verify a requirement, coverage is measured against the requirement set, and a result that can't be traced back to a requirement, a software version, and a bench configuration is an anecdote rather than evidence. The reproducibility questions in the [[bench-evaluation-checklist]] exist for exactly this reason.

This is also half the argument for writing tests in intent rather than bench terms. The requirement is a statement about the product, so the test that discharges it must be too. See [[why-abstraction-exists]].

## What success looks like

A HIL program is working when:

- The suite runs unattended, and people trust a red result enough to act on it without first asking whether the bench is broken.
- A failure points at the product more often than at the bench. The inverse ratio is the most common way suites lose their audience. See [[avoid-leaving-state-behind]].
- A bench refresh, a second bench, or a new product variant does not torch the suite. See [[pitfall-hardcoded-bench-names]].
- A field failure routinely becomes a regression test rather than a war story. See [[replay]].

## What it cannot produce

A passing suite means the controller behaves correctly in the world the bench describes. That is a large and useful claim, and it is not "the product works." See the limitations in [[what-a-hil-test-bench-is]].

And no bench can tell you whether a requirement itself is right. Verification asks "did we build it as specified?" Whether the specification describes a good product is validation, and it happens somewhere other than the rack.
