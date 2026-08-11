---
type: Guide
related_to:
  - "[[start-here]]"
_organized: true
---
# Glossary

This list covers common terms, plus what people actually say out loud.

Projects, applications, and industries will have their own words for several of these, so this list is not exhaustive.

This list groups terms by theme rather than alphabetically, so related vocabulary sits together.

## The six capability verbs

This vault uses six controlled verbs for what a test system does to a device: [[observe]], [[stimulate]], [[capture]], [[replay]], [[disturb]], and [[interrogate]].

These six are the technical verbs of this vault, in the sense of ASD-STE100 rule 1.12 (category: computing and test operations). Use them only with the meanings their notes define, and do not use synonyms for them. One verb, one meaning, everywhere.

## The system

| Term | Meaning |
| --- | --- |
| **DUT / UUT / SUT** | Device, unit, or system under test. This is the thing you test. Everything else is scaffolding. |
| **Bench / rig / test system** | The integrated test system. People use these terms interchangeably, but "rig" tends to imply more hardware. |
| **Plant model** | The simulation of the world around the DUT. Also "the model," "the simulation," "the vehicle model." See [[the-plant-model]]. |
| **Real-time target** | The computer running the plant model on a hard schedule. Also "the RT target," "the controller," or "the target." |
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
| **XIL / X-in-the-loop** | The general pattern. X is real. Everything else is simulated. See [[one-test-on-three-benches]]. |

## Signals and data

| Term | Meaning |
| --- | --- |
| **Signal** | A named value that varies with time. This is ambiguous, and a major source of confusion. It can be a model variable, a decoded bus field, or a physical waveform. Always ask which. |
| **Variable** | A named value inside the model or the DUT. |
| **Label** | A signal's name in a particular naming scheme. Multiple labels in multiple schemes can represent the same signal. See [[five-names-for-one-signal]]. |
| **Mapping** | The translation table between naming schemes, with units and scaling. |
| **Raw vs physical** | The measured value versus what that value represents, for example `counts → degrees` or `volts → newton-meters`. |
| **Capture / acquisition / measurement** | The real-time system records many samples over a window. See [[capture]]. |
| **Aliasing** | Content faster than half the sample rate folds down. It appears as a slower signal that was never there. Suspect it before you suspect physics. See [[avoid-undersampling]]. |
| **Stimulus / stimulation** | The real-time system drives a value or waveform in. See [[stimulate]]. |
| **Trigger** | A condition that starts or stops something, evaluated on the real-time side. |
| **Pre-trigger** | Keeping buffered samples from *before* the trigger. See [[triggers-and-windows]]. |

## Timing

| Term | Meaning |
| --- | --- |
| **Real-time** | Guaranteed to finish within a fixed deadline, each cycle. "Hard" real-time treats a missed deadline as a failure. It does not mean "fast." See [[time-is-the-hard-part]]. |
| **Step / cycle / tick** | One iteration of the real-time loop. |
| **Task / raster** | A group of computations running at one rate. A system typically has several. |
| **Cycle time** | How frequently a task, a bus frame, or other periodic thing occurs. |
| **Jitter** | Variation in when something actually occurs vs when it is scheduled to occur. |
| **Latency** | Delay between cause and observed effect. |
| **Overrun** | The real-time loop failed to finish before its next step was due. This is fatal. Always investigate it. |

## Networks

| Term | Meaning |
| --- | --- |
| **Frame / message** | One transmission on a bus. This typically includes an identifier, a payload, and a timestamp. |
| **Bus database** | The file describing how to decode frames into signals. Names vary by bus type. |
| **Restbus simulation** | The bench pretending to be all the controllers that are not on the bench. See [[the-network]]. |
| **Rolling counter** | A field that increments each transmission so receivers can detect staleness. |
| **Bus load** | Percentage of bus capacity in use. |

## Faults and diagnostics

| Term | Meaning |
| --- | --- |
| **Fault injection** | Deliberately breaking something by simulating an electrical or logical error. See [[disturb]]. |
| **FIU / fault injection unit** | The switching hardware that creates electrical faults. |
| **Open / short-to-ground / short-to-battery** | The three basic electrical faults. These cover 90% of fault requirements. |
| **Fault code / DTC** | A stored identifier for a detected problem. |
| **Freeze frame / snapshot** | Conditions recorded when the DUT sets a fault. |
| **Degraded mode / limp mode / safe state** | Reduced-capability operation after a fault. |
| **Diagnostic session** | A stateful, timing-out mode required for many diagnostic operations. See [[the-diagnostic-channel]]. |

## Calibration

| Term | Meaning |
| --- | --- |
| **Calibration value / cal / parameter** | A tunable constant in the DUT. See [[device-internals]]. |
| **Calibration set / dataset** | The complete collection of calibration values. These typically need version control. |
| **Measurement variable** | A read-only internal value. |
| **Symbol file** | The build artifact mapping names to addresses. Each build changes it. |

## Colloquialisms

| Phrase | Meaning |
| --- | --- |
| **"It's green"** | The suite passed. Not the same as "it works." |
| **"Flaky"** | Fails intermittently. Usually a timing or cleanup bug in the test, not the product. See [[avoid-leaving-state-behind]]. |
| **"Bench issue"** | The failure was the test system, not the product. True more frequently than anyone likes. |
| **"Works on the bench"** | The HIL equivalent of "works on my machine," and carries the same caveat. |
| **"Did you power cycle it?"** | A first-line check that resolves problems more frequently than expected. |
