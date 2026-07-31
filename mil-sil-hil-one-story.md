---
type: Guide
related_to:
  - "[[capability-grid]]"
  - "[[why-abstraction-exists]]"
_organized: true
---
# One test, three benches

The acronyms MIL, SIL, and HIL are usually taught as vocabulary, but it's worth considering them as a single story about one test run three times.

## The test

```
When the coolant temperature sensor reads above 110 °C for more than two seconds, the controller shall reduce commanded power to 50% and set a fault flag.
```

That sentence is the test. Note what it does not mention:

- no bench
- no wiring
- no signal names
- no units conversion
- no tool

## Round 1: The controller is a model (MIL)

The control logic exists only as a block diagram or model. There is no code and no hardware.

The plant model and the controller model run together on one desktop machine. Execution is synchronous but not real-time, meaning the controller and plant step together, but a simulated second may take more or less than a wall-clock second.

- You set temperature by driving the plant model. See [[boundary-plant-model]].
- You observe the power command as a model variable.

```mermaid
flowchart LR
    T["the test"] --> P["plant model"]
    P -- "sensor values" --> C["controller:<br/>a model"]
    C -- "commands" --> P

    classDef yours fill:#dbeafe,stroke:#3b82f6,color:#1e3a8a
    classDef dut fill:#fef3c7,stroke:#d97706,color:#78350f
    classDef model fill:#dcfce7,stroke:#16a34a,color:#14532d
    class T yours
    class P model
    class C dut
```

### **What you learn**

Is the control *logic* right? Is 110 °C the correct threshold? Is two seconds the right debounce?

### **What you cannot learn**

Anything about code, timing, hardware, or wiring.

## Round 2: The controller is compiled software (SIL)

The model has become C. The real production source is compiled for a host machine and runs against the same plant model, often still not in real time. Same test intent and plant model, but the DUT is now a binary.

```mermaid
flowchart LR
    T["the test"] --> P["plant model"]
    P -- "sensor values" --> C["controller:<br/>a compiled binary"]
    C -- "commands" --> P

    classDef yours fill:#dbeafe,stroke:#3b82f6,color:#1e3a8a
    classDef dut fill:#fef3c7,stroke:#d97706,color:#78350f
    classDef model fill:#dcfce7,stroke:#16a34a,color:#14532d
    class T yours
    class P model
    class C dut
```

### What you learn

Did the code generation or hand-implementation preserve the logic? Fixed-point rounding, integer overflow, initialisation order, etc.

### **What you cannot learn**

Anything requiring the target processor or real I/O.

## Round 3: The controller is hardware (HIL)

The production software is flashed onto the real controller, in its real housing, and wired to a bench. Now everything is in real time and executes as it would in-vehicle.

- Temperature is no longer a number you write. It's a resistance the bench presents across two pins, which the controller's ADC samples, scales, and filters. See [[boundary-physical-pins]].
- The power command is no longer a variable. It's a PWM duty cycle on an output pin, or a value in a network frame. See [[boundary-network]].

```mermaid
flowchart LR
    T["the test"] --> P["plant model<br/>real-time build"]
    P -- "resistances, voltages" --> C["controller:<br/>real hardware"]
    C -- "PWM, network frames" --> P

    classDef yours fill:#dbeafe,stroke:#3b82f6,color:#1e3a8a
    classDef dut fill:#fef3c7,stroke:#d97706,color:#78350f
    classDef model fill:#dcfce7,stroke:#16a34a,color:#14532d
    class T yours
    class P model
    class C dut
```

### **What you learn**

Does the whole thing work? Sensor scaling, ADC noise, filter lag, task scheduling, driver bugs, wiring.

### **What you can now also do**

Cut the sensor wire mid-test and confirm the controller notices. See [[inject-a-fault]].

## What actually changes

|  | MIL | SIL | HIL |
| --- | --- | --- | --- |
| Test intent | same | same | same |
| Plant model | same | same | same (usually real-time build) |
| DUT | model | binary | hardware |
| Timing | free | free | real, hard |
| Signal names | model names | model names | model + symbol + pin + network names |
| Fault injection | logical only | logical only | electrical too |

**The intent never moved. The plumbing moved three times.**

That is the entire argument for [[why-abstraction-exists]], and it is why a well-built test suite has its intent in one place and its bench specifics in another.

## The honest caveats

- Test reuse across stages is the dream, but very hard to realize. Expect the structure to survive and the details to need work.
- Some tests only make sense at one stage. Electrical fault injection has no MIL equivalent; exhaustive parameter sweeps are usually only affordable in SIL.
- A test that passed in MIL and fails in HIL has *not* wasted your time. That delta is exactly the information you paid for.

## Other letters you'll hear

PIL (processor-in-the-loop, the real chip but not the real board), VIL (vehicle-in-the-loop), and various house-specific coinages.

The pattern is always the same: X-in-the-loop means X is real and everything else is simulated.
