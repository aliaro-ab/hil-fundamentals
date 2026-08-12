---
type: Guide
status: Active
related_to:
  - "[[the-capability-grid]]"
  - "[[why-abstraction-exists]]"
_favorite: true
_favorite_index: 0
_organized: true
---
# Start here

This vault teaches the concepts behind hardware-in-the-loop test systems.
It is deliberately vendor-neutral, with no product names, no APIs, and no code.
When you eventually read a real standard or a real product manual, this reference will help you to navigate vendor and industry terminology.

## Audience

Engineers who are competent in embedded software, controls, or test automation, but new to HIL specifically.

If your background is automated software testing, skim [[coming-from-software-testing]] first.
It maps this world onto the one you already know, and flags where the analogies break.

## Summary

A test bench is a machine that can observe, stimulate, capture, replay, disturb, and interrogate a device across six different boundaries.

The entire discipline is about choosing the right verb at the right boundary, then exercising that on a real-time controller.

## Reading order

Plan for half a day, in this order:

| Note | Why it is here |
| --- | --- |
| [[what-a-hil-test-bench-is]] | The physical and logical structure of a HIL test bench. |
| [[what-hil-testing-produces]] | The value that the discipline delivers, and who benefits. |
| [[why-abstraction-exists]] | Why tests are written around the product, not the bench. |
| [[open-loop-vs-closed-loop]] | An important distinction that decides how you write your tests |
| [[the-capability-grid]] | A conceptual guide for what behaviors a HIL test bench is capable of. |
| [[time-is-the-hard-part]] | Why understanding the importance of time is critical to writing effective tests. |
| [[triggers-and-windows]] | How to set up and capture the right samples at the right time. |
| [[five-names-for-one-signal]] | How to trace a signal from the DUT, through the hardware and to the HIL test bench. |
| [[the-test-bench-resource-model]] | Understanding the resource model of how resources are reserved during a test. |
| [[one-test-on-three-benches]] | The payoff: one test, three benches. |

Then work the [[scenario-cards|scenario cards]] with someone who did this before.

## Next steps

| Note | Why it is here |
| --- | --- |
| [[bench-evaluation-checklist]] | What to ask when you are starting with your test bench. |
| [[glossary]] | Terms, and vernacular that is common in the field. |

## Vault organization

| Type | What it is for |
| --- | --- |
| [[guide]] | Step-by-step instructions and guidance |
| [[concept]] | Foundational ideas for understanding HIL |
| [[boundary]] | Core interfaces and surfaces where the test system and the DUT exchange information |
| [[capability]] | Actions that the test system can perform at one or more boundaries |
| [[pitfall]] | Common mistakes and missteps, and how to avoid them |
| [[scenario]] | Practice cards |
