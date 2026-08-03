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
When you eventually read a real standard or a real product manual this reference will help you to navigate vendor and industry terminology.

## Audience

Engineers who are competent in embedded software, controls, or test automation, but new to HIL specifically.

If your background is automated software testing, skim [[coming-from-software-testing]] first. It maps this world onto the one you already know, and flags where the analogies break.

## Summary

A test bench is a machine that can observe, stimulate, capture, replay, inject faults into, and interrogate a device across six different boundaries.

The entire discipline is about choosing the right verb at the right boundary, then exercising that on a real-time controller.

## Reading order

Plan for half a day, in this order:

| Note | Why it's here |
| --- | --- |
| [[what-a-hil-test-bench-is]] | The physical and logical picture |
| [[what-hil-testing-produces]] | What the discipline delivers, and who consumes it |
| [[why-abstraction-exists]] | Why tests are written around the product, not the bench |
| [[open-loop-vs-closed-loop]] | The distinction that decides how you write a test |
| [[the-capability-grid]] | The one-page map, and the note to print |
| [[time-is-the-hard-part]] | The concept most newcomers are missing |
| [[triggers-and-windows]] | How a capture becomes evidence instead of a pile of samples |
| [[five-names-for-one-signal]] | Where most real bugs live |
| [[the-test-bench-resource-model]] | The resource discipline that keeps benches stable |
| [[one-test-on-three-benches]] | The payoff: one test, three benches |

Then work the [[scenario-cards|scenario cards]] with someone who has done this before.

## Next steps

| Note | Why it's here |
| --- | --- |
| [[bench-evaluation-checklist]] | What to ask when someone hands you a bench |
| [[glossary]] | Terms, and what people actually say |

## Vault organization

| Type | What it's for |
| --- | --- |
| [[guide]] | Step-by-step instructions and guidance |
| [[concept]] | Load-bearing ideas for understand HIL |
| [[boundary]] | Core interfaces and surfaces where the test system and the DUT exchange information |
| [[capability]] | Actions that the test system can perform at one or more boundaries |
| [[pitfall]] | Common mistakes and missteps, and how to avoid them |
| [[scenario]] | Practice cards |
