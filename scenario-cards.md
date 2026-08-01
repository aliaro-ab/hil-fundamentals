---
type: Guide
related_to:
  - "[[the-capability-grid]]"
  - "[[start-here]]"
_organized: true
---
# Scenario cards

These are provided for practice. Each card states a test goal in plain language so you can work out which [[boundary|boundaries]] and [[capability|capabilities]] you'd need *before* reading the answer.

## How to run this

### Solo

Read the goal, write your answer down, then compare. 

### In a group

Hand out one card each, give people five minutes, then have each person defend their answer to the room. 

Disagreements are the point. Most cards have more than one defensible approach, and the argument about which is better teaches more than the answer does.

### As an interview or check-in

Use one card, allow fifteen minutes, and talk it through.

It's a good indicator of whether someone has the mental model or just the vocabulary.

## The cards

1. [[scenario-a-sensor-wire-breaks]]
2. [[scenario-prove-a-100-ms-deadline]]
3. [[scenario-another-node-stops-communicating]]
4. [[scenario-reproduce-a-field-failure]]
5. [[scenario-verify-a-tunable-threshold]]
6. [[scenario-reset-at-the-worst-moment]]

## Criteria for a good answer

A good answer is not just a list of cells from the grid. It covers:

- Which boundary for the stimulus, and which for the assertion, and why they're different
- What capability at each, with the rate or timing it needs
- What the trigger and window are, if a capture is involved
- What cleanup is required as outlined in [[the-test-bench-resource-model|the test bench resource model]]
- What the test would fail to catch, stated honestly
