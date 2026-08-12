---
type: Scenario
belongs_to: "[[scenario-cards]]"
has:
  - "[[disturb]]"
  - "[[capture]]"
  - "[[interrogate]]"
related_to:
  - "[[physical-pins]]"
  - "[[triggers-and-windows]]"
_organized: true
---
# Scenario: reset at the worst moment

## The goal

The controller writes accumulated operating hours to non-volatile memory periodically.

```
When power is interrupted during that write:
    the stored data shall not be corrupted
    the controller shall start correctly afterwards
```

This is the hardest card here, because the fault has to land in a window you do not directly control.

Work out what you need before reading on.

---

## A good answer

### Hit the write window

The core problem is to hit the window.
The write takes a few milliseconds and occurs each few minutes, and cutting power at a random time will almost never coincide with it.
There are three approaches, described below roughly in order of preference.

#### Trigger on an observable

If the DUT toggles a pin, sends a frame, or sets an internal flag around the write, use that as the hardware trigger.
Trigger the power cut with a configurable delay to sweep across the window.
This approach is precise and repeatable, and it is the real-time side doing what it is for.
See [[triggers-and-windows]].

#### Use a debug or test build

Use a build that signals the write externally, or lets you command one on demand.
This is legitimate, but you then test an artifact of a build you do not ship.
Note that limitation honestly.

#### Brute force

Cut power at randomized offsets, thousands of times, overnight.
This is inelegant and frequently the most practical thing available.
Statistically you will land in the window.
Log the offset each time so a failure is reproducible.

### Sweep the delay

The interesting failures cluster at specific microseconds inside the write (start of erase, mid-write, just before the commit).
A single fixed offset tests one point in a window you must scan.
Step the delay across the whole write with fine granularity.

### Cut the power for real

[[disturb]] at [[physical-pins|physical pins]].
And test both shapes, a **clean cut** to zero and a **brownout** (a sag to some intermediate voltage).
Brownouts are nastier and more common.
The processor can run erratically at a voltage where its brownout detector has not yet tripped, and that is where the genuinely bad corruption occurs.
Sweep the sag voltage too.

### Capture the supply rail

Capture the rail along with everything else, so you know precisely what the DUT experienced.
Assumed power profiles are frequently incorrect.

### Check the aftermath

Restore power, then check:

- Does it boot at all? (Bricking is the failure mode you most need to know about.)
- Are the stored hours valid (either the old value or the new one, never garbage)?
- Does it detect and recover from a partial write, if the design claims to?
- Is there a fault indicating the interruption, if the design specifies one? Use [[interrogate]] to check.
- Are *other* stored values intact, or did the corruption spread?

### Repeat the test

Run it many times, across offsets and voltages.
A one-shot pass here means very little.
This is a statistical test by nature.

## Why this card matters

Power-interruption corruption is a classic field failure.
It is rare per event, but multiplied across a fleet and years of operation it becomes routine.
It presents as inexplicable units that will not start.
It is essentially impossible to test any way other than on a bench.
It is the kind of test that quietly justifies the bench's existence.

## Limitations

- Slow degradation of the memory itself.
- Corruption from other causes, such as EMC, radiation, or thermal stress.
- Whatever occurs after ten thousand write cycles.

Endurance is a different program.
