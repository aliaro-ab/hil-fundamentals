---
type: Boundary
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[disturb]]"
  - "[[five-names-for-one-signal]]"
---
# Physical pins

**The wires, voltages, and currents where the device meets the physical world.**

The true boundary between the device under test (DUT) and its environment is the wiring harness, the connectors, and the physical pins they contain.

At this boundary the bench is not simulating the interface, it *is* the interface.
It acquires and generates all of the signals that the DUT defines and expects.

## Inputs and outputs

### Inputs to the DUT

The bench pretends to be a sensor.
A voltage divider standing in for a potentiometer, a programmable resistance standing in for a thermistor, a current source for a 4–20 mA loop.
A pulse train for a wheel-speed sensor, a synthesized waveform for a resolver.

### Outputs from the DUT

The bench pretends to be a load.
A PWM output driving a solenoid, an H-bridge driving a motor, a low-side switch driving a lamp.
Real loads or emulated ones, but the DUT must see a plausible impedance or its own driver diagnostics will complain.

## Capabilities

| Capability | What it means here |
| --- | --- |
| [[observe]] | Measure a voltage, a current, or a resistance |
| [[stimulate]] | Generate a voltage, current, resistance, PWM, or encoder pattern |
| [[capture]] | Record analog and digital lines, usually the fastest data on the bench |
| [[replay]] | Play a recorded analog trace back out as a real waveform |
| [[disturb]] | The genuinely electrical faults, including opens, shorts, and added resistance |

You cannot [[interrogate]] a pin.
Asking the device for its own account of itself occurs at [[the-diagnostic-channel]].

## Why this boundary is special

It is the only place you can test what occurs **between** the DUT's software and the world:

- Sensor scaling and ADC behavior
- Input filtering and its lag
- Signal integrity, noise, and ground offsets
- Driver-level diagnostics (open load, short circuit, over-current)
- Genuine electrical faults, via [[disturb]]

If your test is about whether the DUT interprets the physical world correctly, it has to occur here.
No other boundary can answer that question.

## Constraints

### Wiring is real (and frequently incorrect)

A meaningful fraction of early bench failures are the harness, not the software.
Before you debug a controller, confirm the pin is connected to what the documentation claims.

### Everything is a conversion

The physical quantity, the electrical quantity, the raw count, and the engineering unit are four different numbers.
The mapping between them is bench configuration that someone typed in.
See [[five-names-for-one-signal]].

### Loads matter

Emulated loads that are "close enough" for the control law are frequently not close enough for the DUT's diagnostics.
A controller that trips an open-load fault against your resistor bank tells you something true about your bench, not about itself.

### Bandwidth is finite

Analog I/O has an update rate and a settling time.
Neither is infinite, and both are usually slower than you assumed.

## Safety

This is the boundary where mistakes cost hardware.
Supply voltages, inductive kickback, and fault-injection matrices that can deliberately connect battery voltage to a signal pin.
Learn the interlocks before you learn the API.
