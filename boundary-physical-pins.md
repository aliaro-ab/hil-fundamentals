---
type: Boundary
belongs_to: "[[capability-grid]]"
related_to:
  - "[[inject-a-fault]]"
  - "[[one-signal-five-names]]"
---
# Physical pins

At the end of the day, we are testing electro-mechanical systems. The true boundary between the device under test and it's environment is the wiring harness, and the physical pins it contains. 

The connector. Voltages, currents, resistances, frequencies, duty cycles — the only boundary the DUT cannot tell apart from the real machine, because at this boundary the bench is not simulating the interface, it *is* the interface.

## What lives here

**Inputs to the DUT** — the bench pretends to be a sensor. A voltage divider standing in for a potentiometer, a programmable resistance standing in for a thermistor, a current source for a 4–20 mA loop, a pulse train for a wheel-speed sensor, a synthesised waveform for a resolver.

**Outputs from the DUT** — the bench pretends to be a load. A PWM output driving a solenoid, an H-bridge driving a motor, a low-side switch driving a lamp. Real loads or emulated ones, but the DUT must see a plausible impedance or its own driver diagnostics will complain.

## Why this boundary is special

It's the only place you can test what happens **between** the DUT's software and the world:

- Sensor scaling and ADC behaviour
- Input filtering and its lag
- Signal integrity, noise, and ground offsets
- Driver-level diagnostics — open load, short circuit, over-current
- Genuine electrical faults, via [[inject-a-fault]]

If your test is about whether the DUT interprets the physical world correctly, it has to happen here. No other boundary can answer that question.

## What it costs

**Wiring is real and wiring is wrong.** A meaningful fraction of early bench failures are the harness, not the software. Before you debug a controller, confirm the pin is connected to what the documentation claims.

**Everything is a conversion.** The physical quantity, the electrical quantity, the raw count, and the engineering unit are four different numbers, and the mapping between them is bench configuration that someone typed in. See [[one-signal-five-names]].

**Loads matter.** Emulated loads that are "close enough" for the control law are frequently not close enough for the DUT's diagnostics. A controller that trips an open-load fault against your resistor bank is telling you something true about your bench, not about itself.

**Bandwidth is finite.** Analogue I/O has an update rate and a settling time. Neither is infinite, and both are usually slower than you assumed.

## Safety

This is the boundary where mistakes cost hardware. Supply voltages, inductive kickback, and fault-injection matrices that can deliberately connect battery voltage to a signal pin. Learn the interlocks before you learn the API.
