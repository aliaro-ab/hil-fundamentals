---
type: Boundary
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[replay]]"
  - "[[five-names-for-one-signal]]"
  - "[[the-service-layer]]"
_organized: true
---
# The network

**What the device says to its neighbors and what they say back.**

Modern controllers are rarely alone. They sit on a bus alongside other controllers, and a large share of their behavior is a reaction to messages from devices that, on your bench, do not exist.

## Frames and signals

**Frames** are the raw messages. Each has an identifier, a payload of bytes, a timestamp, and a direction. At this level you see everything, including malformed traffic, timing, bus load, and errors.

**Signals** are the decoded fields inside frames. "Engine speed = 2400 rpm" rather than "ID 0x1A0, bytes 09 60 …". Decoding requires a **database file** that describes the bus layout.

Work at signal level for readability; drop to frame level when you need to test something the database can't express, such as a bad checksum, a stalled rolling counter, or a frame that shouldn't exist.

## Capabilities

| Capability | What it means here |
| --- | --- |
| [[observe]] | The last received value of a signal (remembering that "last" may be very old) |
| [[stimulate]] | Transmit frames, usually with the bench **restbus-simulating** the absent controllers so the DUT sees a plausible network |
| [[capability-capture]] | Frames or decoded signals, with timestamps (often the best-timestamped data on the bench) |
| [[replay]] | A recorded log, which is the highest-value use of this boundary |
| [[fault-injection]] | Rich support, including stopping a frame, delaying it, corrupting a checksum, freezing a counter, sending out-of-range values, and overloading the bus |

## Restbus simulation

Restbus simulation is worth naming explicitly because it is the part of bench setup that engineers new to HIL don't expect. Your DUT may need to hear from twenty other controllers before it will even leave its startup state. The bench synthesizes all of them. That's the "rest" of the bus. Restbus configuration is usually the single largest chunk of bench setup, and when a DUT "doesn't do anything," a misconfigured restbus is the first suspect.

## The database is a contract that drifts

Everything at signal level depends on a database file that defines names, scaling, offsets, byte order, and cycle times. Three failure modes to know:

- **Stale database.** The software moved a signal and nobody updated the file. Your test reads a plausible, wrong number.
- **Multiple databases.** Different teams hold different revisions. Whose is authoritative?
- **Silent decode.** A wrong scaling factor doesn't throw an error. It gives you a number that's off by 10.

See [[five-names-for-one-signal]].

## Timing

Networks have cycle times, latency, and jitter. A signal updated every 100 ms cannot be used to assert a 20 ms response, no matter how fast you poll it. Always know the cycle time of any signal you assert on.

For request/response service traffic layered on top of a network, see [[the-service-layer|the service layer]].
