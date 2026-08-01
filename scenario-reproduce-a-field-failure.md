---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[replay]]"
  - "[[capability-capture]]"
related_to:
  - "[[the-network]]"
  - "[[five-names-for-one-signal]]"
---
# Scenario: reproduce a field failure

## The goal

A unit in the field entered an unexpected shutdown. You have a 90-second bus log from the vehicle, recorded up to and including the event.

```
Given that log:
    reproduce the failure on the bench
    turn it into a regression test
```

Work out what you need before reading on.

---

## A good answer

### Decide what you actually have

A bus log contains what was *said on the bus*. It does not contain analog sensor inputs, internal state, the DUT's own reasoning, or anything on a network that wasn't being logged. Everything absent will be supplied by your bench's defaults, and those defaults are now part of the experiment whether you chose them or not. Write down the gaps before you start.

### Match the software

Reproduce against the same software version the field unit was running. A newer build may fix the bug, not reproduce it, and cost you a day proving nothing.

### Check the database revision

The log decodes correctly only against the database that was current when it was recorded. A signal that moved between revisions will decode into a plausible wrong number. See [[five-names-for-one-signal]].

### Set up the replay

[[replay]] at [[the-network|the network]]. The critical question is whether your bench replays original *timing* or just message order at a nominal rate. For a timing-sensitive bug, order-only replay will not reproduce it, and you'll wrongly conclude the log is useless.

### Handle the restbus conflict

Your bench is probably already simulating those messages. Replaying the log on top produces duplicates and the DUT sees nonsense. You must disable restbus transmission for every ID the log provides, and keep it running for the ones the log doesn't. Getting this wrong is the single most common reason a replay "doesn't work."

### Capture everything

[[capability-capture]] far more widely than the log covers, including internals at [[device-internals|device internals]], outputs, mode, and faults. The whole value of doing this on a bench rather than in the field is that you can see inside.

### Run it

Then decide what you learned:

| Outcome | What it tells you |
| --- | --- |
| **Reproduces** | Excellent. Narrow the window, trim the log to the minimum that still triggers it, and you have a fast regression test. |
| **Doesn't reproduce** | Informative, not a failure. Something outside the log is contributing, such as an analog input, a thermal condition, timing, or a hardware variance. The list of things you defaulted is now your list of suspects. |

### Make it a test

Trimmed log, pinned software version, pinned database revision, explicit assertion on the correct behavior, checked into the suite. This is the payoff. The bug can never silently return.

## Why this card matters

A team that can do this reliably has a different relationship with field failures than one that can't. It converts "we think we fixed it" into "we can demonstrate it's fixed, forever." Almost every organization underinvests in the plumbing that makes it routine.

## Limitations

Whatever wasn't in the log. Which is why the useful follow-up question, after any field failure, is *what would we have needed to record to make this reproducible?* Then go and record it.
