---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[inject-a-fault]]"
  - "[[capability-capture]]"
  - "[[capability-interrogate]]"
related_to:
  - "[[boundary-physical-pins]]"
  - "[[triggers-and-windows]]"
---
# Scenario: reset at the worst moment

## The goal

The controller writes accumulated operating hours to non-volatile memory periodically.

```
When power is interrupted during that write:
    the stored data shall not be corrupted
    the controller shall start correctly afterwards
```

This is the hardest card here, because the fault has to land in a window you don't directly control.

---

## A good answer

**The core problem is hitting the window.** The write takes a few milliseconds and happens every few minutes. Cutting power at a random time will almost never coincide with it. Three approaches, roughly in order of preference:

| Approach | What it involves |
| --- | --- |
| **Trigger on an observable** | If the DUT toggles a pin, sends a frame, or sets an internal flag around the write, use that as a hardware trigger for the power cut, with a configurable delay to sweep across the window. Precise and repeatable. This is the real-time side doing what it's for. See [[triggers-and-windows]]. |
| **Use a debug or test build** | A build that signals the write externally, or lets you command one on demand. Legitimate, but you're then testing an artifact of a build you don't ship. Note that limitation honestly. |
| **Brute force** | Cut power at randomized offsets, thousands of times, overnight. Inelegant and often the most practical thing available. Statistically you'll land in the window; log the offset each time so a failure is reproducible. |

**Sweep the delay.** The interesting failures cluster at specific microseconds inside the write (start of erase, mid-write, just before the commit). A single fixed offset tests one point in a window you should be scanning. Step the delay across the whole write with fine granularity.

**Cut the power for real.** [[inject-a-fault]] at [[boundary-physical-pins|physical pins]]. And test both shapes, a **clean cut** to zero and a **brownout** (a sag to some intermediate voltage). Brownouts are nastier and more common. The processor may run erratically at a voltage where its brown-out detector hasn't yet tripped, and that's where the genuinely bad corruption happens. Sweep the sag voltage too.

**Capture the supply rail** along with everything else, so you know precisely what the DUT experienced. Assumed power profiles are frequently wrong.

**Restore power and check the aftermath:**

- Does it boot at all? (Bricking is the failure mode you most need to know about.)
- Are the stored hours valid (either the old value or the new one, never garbage)?
- Does it detect and recover from a partial write, if the design claims to?
- Is there a fault indicating the interruption, if there should be? Use [[capability-interrogate|interrogate]] to check.
- Are *other* stored values intact, or did the corruption spread?

**Repeat.** Run it many times, across offsets and voltages. A one-shot pass here means very little; this is a statistical test by nature.

## Why this card is worth the trouble

Power-interruption corruption is a classic field failure. It is rare per event, but multiplied across a fleet and years of operation it becomes routine, and it presents as inexplicable units that won't start. It is essentially impossible to test any way other than on a bench, and it is the kind of test that quietly justifies the bench's existence.

## What this test does not catch

Slow degradation of the memory itself, corruption from other causes (EMC, radiation, thermal), and whatever happens after ten thousand write cycles. Endurance is a different program.
