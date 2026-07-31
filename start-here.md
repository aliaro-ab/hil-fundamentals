---
type: Guide
status: Active
related_to:
  - "[[capability-grid]]"
  - "[[why-abstraction-exists]]"
_favorite: true
_favorite_index: 0
---
# Start here

This vault teaches the concepts behind hardware-in-the-loop test systems.
It is deliberately vendor-neutral: no product names, no APIs, no code.
When you eventually read a real standard or a real product manual this reference will help you to navigate vendor and industry terminology.

## Who this is for

Engineers who are competent in embedded software, controls, or test automation, but new to HIL specifically.

## The one-sentence version

A test bench is a machine that can observe, stimulate, capture, replay, break, and interrogate a device across six different boundaries.

The entire discipline is about choosing the right verb at the right boundary, then exercising that on a real-time controller.

## Reading order

Half a day, in this order:

| Note | Why it's here |
| --- | --- |
| [[why-abstraction-exists]] | The motivation for everything else |
| [[what-a-hil-bench-is]] | The physical and logical picture |
| [[open-loop-vs-closed-loop]] | The distinction that decides how you write a test |
| [[capability-grid]] | The one-page map, and the note to print |
| [[time-is-the-hard-part]] | The concept most newcomers are missing |
| [[one-signal-five-names]] | Where most real bugs live |
| [[the-test-bench-resource-model]] | The resource discipline that keeps benches stable |
| [[mil-sil-hil-one-story]] | The payoff: one test, three benches |

Then work the [[scenario-cards]] with someone who has done this before.

## When you're ready for more

| Note | Why it's here |
| --- | --- |
| [[bench-evaluation-checklist]] | What to ask when someone hands you a bench |
| [[glossary]] | Terms, and what people actually say |

## How the vault is organised

| Type | What it's for |
| --- | --- |
| [[guide]] | Read start to finish |
| [[concept]] | Load-bearing ideas |
| [[boundary]] | Surfaces of the device you can touch |
| [[capability]] | The six verbs |
| [[pitfall]] | Mistakes you get to make only once |
| [[scenario]] | Practice cards |
