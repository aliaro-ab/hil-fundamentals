---
type: Boundary
belongs_to: "[[capability-grid]]"
related_to:
  - "[[capability-replay]]"
  - "[[one-signal-five-names]]"
  - "[[boundary-service-layer]]"
---
# The network

**What the device says to its neighbours, and what they say back.**

Modern controllers are rarely alone. They sit on a bus alongside other controllers, and a large share of their behaviour is a reaction to messages from devices that, on your bench, do not exist.

## Two levels, and you need both

**Frames** — the raw messages. An identifier, a payload of bytes, a timestamp, a direction. At this level you see everything, including malformed traffic, timing, bus load, and errors.

**Signals** — the decoded fields inside frames. "Engine speed = 2400 rpm" rather than "ID 0x1A0, bytes 09 60 …". Decoding requires a **database file** that describes the bus layout.

Work at signal level for readability; drop to frame level when you need to test something the database can't express — a bad checksum, a stalled rolling counter, a frame that shouldn't exist.

## What you can do here

| Capability | What it means here |
| --- | --- |
| [[capability-observe]] | The last received value of a signal — remembering that "last" may be very old |
| [[capability-stimulate]] | Transmit frames, usually with the bench **restbus-simulating** the absent controllers so the DUT sees a plausible network |
| [[capability-capture]] | Frames or decoded signals, with timestamps — often the best-timestamped data on the bench |
| [[capability-replay]] | A recorded log, which is the highest-value use of this boundary |
| [[inject-a-fault]] | Richly: stop a frame, delay it, corrupt a checksum, freeze a counter, send out-of-range values, overload the bus |

## Restbus simulation

Restbus simulation is worth naming explicitly because it is the part of bench setup that engineers new to HIL don't expect. Your DUT may need to hear from twenty other controllers before it will even leave its startup state. The bench synthesises all of them — that's the "rest" of the bus. Restbus configuration is usually the single largest chunk of bench setup, and when a DUT "doesn't do anything," a misconfigured restbus is the first suspect.

## The database is the contract, and it drifts

Everything at signal level depends on a database file: names, scaling, offsets, byte order, cycle times. Three failure modes to know:

- **Stale database** — the software moved a signal and nobody updated the file. Your test reads a plausible, wrong number.
- **Multiple databases** — different teams hold different revisions. Whose is authoritative?
- **Silent decode** — a wrong scaling factor doesn't throw an error. It gives you a number that's off by 10.

See [[one-signal-five-names]].

## Timing here is real

Networks have cycle times, latency, and jitter. A signal updated every 100 ms cannot be used to assert a 20 ms response, no matter how fast you poll it. Always know the cycle time of any signal you assert on.

For request/response service traffic layered on top of a network, see [[boundary-service-layer]].
