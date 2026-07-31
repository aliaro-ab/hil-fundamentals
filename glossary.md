---
type: Guide
related_to:
  - "[[start-here]]"
_organized: true
---
# Glossary

This list covers common terms, plus what people actually say out loud.

Projects, applications, and industries will have their own words for several of these, so this list should not be considered exhaustive.

## The system

| Term | Meaning |
| --- | --- |
| **DUT / UUT/SUT** | Device, unit, or system under test. This is the thing you're testing. Everything else is scaffolding. |
| **Bench / rig / test system** | The integrated test system. These terms are used interchangeably, but "rig" tends to imply more hardware. |
| **Plant model** | The simulation of the world around the DUT. Also "the model," "the simulation", "the vehicle model". See [[boundary-plant-model]]. |
| **Real-time target** | The computer running the plant model on a hard schedule. Also "the RT target," "the controller", or "the target". |
| **Host / automation PC** | A PC that is connected to the real-time target for monitoring, control, and sequencing of tests. |
| **Harness** | The wiring between bench and DUT. A frequent and underestimated source of failure. |
| **Breakout box** | Hardware exposing DUT pins for manual measurement or fault injection. |
| **Load box** | Real or emulated electrical loads standing in for actuators. |

## Stages

| Term | Meaning |
| --- | --- |
| **MIL** | Model-in-the-loop. The controller is a model. |
| **SIL** | Software-in-the-loop. The controller is compiled code on a host. |
| **PIL** | Processor-in-the-loop. Real target processor, not the real board. |
| **HIL** | Hardware-in-the-loop. Real controller hardware. |
| **XIL / X-in-the-loop** | The general pattern. X is real; everything else is simulated. See [[mil-sil-hil-one-story]]. |

## Signals and data

| Term | Meaning |
| --- | --- |
| **Signal** | A named value that varies with time. This is ambiguous, and a major source of confusion. It could be a model variable, a decoded bus field, or a physical waveform. Always ask which. |
| **Variable** | A named value inside the model or the DUT. |
| **Label** | A signal's name in a particular naming scheme. The same signal can be represented by multiple labels in multiple schemes. See [[one-signal-five-names]]. |
| **Mapping** | The translation table between naming schemes, with units and scaling. |
| **Raw vs physical** | The measured value versus what that value represents, for example `Counts → degrees` or `volts → newton-meters`. |
| **Capture / acquisition / measurement** | The real-time system records many samples over a window. See [[capability-capture]]. |
| **Stimulus / stimulation** | The real-time system drives a value or waveform in. See [[capability-stimulate]]. |
| **Trigger** | A condition that starts or stops something, evaluated on the real-time side. |
| **Pre-trigger** | Keeping buffered samples from *before* the trigger. See [[triggers-and-windows]]. |

## Timing

| Term | Meaning |
| --- | --- |
| **Step / cycle / tick** | One iteration of the real-time loop. |
| **Task / raster** | A group of computations running at one rate. A system typically has several. |
| **Cycle time** | How often a task, a bus frame, or other periodic thing happens. |
| **Jitter** | Variation in when something actually happens vs when it should. |
| **Latency** | Delay between cause and observed effect. |
| **Overrun** | The real-time loop failed to finish before its next step was due. This is fatal, and should always be investigated. |

## Networks

| Term | Meaning |
| --- | --- |
| **Frame / message** | One transmission on a bus. This typically includes an identifier, payload, timestamp. |
| **Bus database** | The file describing how to decode frames into signals. Names vary by bus type. |
| **Restbus simulation** | The bench pretending to be all the controllers that aren't on the bench. See [[boundary-network]]. |
| **Rolling counter** | A field that increments each transmission so receivers can detect staleness. |
| **Bus load** | Percentage of bus capacity in use. |

## Faults and diagnostics

| Term | Meaning |
| --- | --- |
| **Fault injection** | Deliberately breaking something by simulating an electrical or logical error. See [[capability-break]]. |
| **FIU / fault injection unit** | The switching hardware that creates electrical faults. |
| **Open / short-to-ground / short-to-battery** | The three basic electrical faults. These cover 90% of fault requirements. |
| **Fault code / DTC** | A stored identifier for a detected problem. |
| **Freeze frame / snapshot** | Conditions recorded when a fault was set. |
| **Degraded mode / limp mode / safe state** | Reduced-capability operation after a fault. |
| **Diagnostic session** | A stateful, timing-out mode required for many diagnostic operations. See [[boundary-diagnostic-channel]]. |

## Calibration

| Term | Meaning |
| --- | --- |
| **Calibration value / cal / parameter** | A tunable constant in the DUT. See [[boundary-device-internals]]. |
| **Calibration set / dataset** | The complete collection of calibration values. These typically need to be versioned. |
| **Measurement variable** | A read-only internal value. |
| **Symbol file** | The build artifact mapping names to addresses. This is changed every build. |

## Colloquialisms

| Phrase | Meaning |
| --- | --- |
| **"It's green"** | The suite passed. Not the same as "it works." |
| **"Flaky"** | Fails intermittently. Usually a timing or cleanup bug in the test, not the product. See [[pitfall-leaked-bench-resources]]. |
| **"Bench issue"** | The failure was the test system, not the product. True more often than anyone likes. |
| **"Works on the bench"** | The HIL equivalent of "works on my machine," and carries the same caveat. |
| **"Did you power cycle it?"** | A first-line check that resolves problems more often than expected. |
