---
type: Guide
status: Active
related_to:
  - "[[start-here]]"
  - "[[bench-evaluation-checklist]]"
_width: wide
_organized: true
_favorite: true
_favorite_index: 1
---
# The capability grid

Every test system does a small number of things to a small number of surfaces.
Six capabilities at six boundaries cover the entirety of what HIL does and where it happens.

## The six verbs

| Verb | What it means |
| --- | --- |
| [[observe]] | What is this value right now? |
| [[stimulate]] | Make this value be something. |
| [[capture]] | What did this value do over that window? |
| [[replay]] | Do again exactly what happened before. |
| [[disturb]] | What happens when this goes wrong? |
| [[interrogate]] | What does the device think is wrong? |

## The six boundaries

| Boundary | What it is |
| --- | --- |
| [[the-plant-model]] | The simulated world around the device |
| [[device-internals]] | The device's own memory and calibrations |
| [[physical-pins]] | Wires, voltages, currents, resistances |
| [[the-network]] | Frames and signals on a bus |
| [[the-service-layer]] | Requests, responses, and events between software services |
| [[the-diagnostic-channel]] | The device's self-report |

Six surfaces around one device:

```mermaid
flowchart TB
    PM["plant model"] --- DUT
    DI["device internals"] --- DUT
    PP["physical pins"] --- DUT
    DUT["the DUT"] --- NW["network"]
    DUT --- SL["service layer"]
    DUT --- DC["diagnostic channel"]

    classDef dut fill:#fef3c7,stroke:#d97706,color:#78350f
    classDef boundary fill:#d1fae5,stroke:#10b981,color:#065f46
    class DUT dut
    class PM,DI,PP,NW,SL,DC boundary
```

## The grid

✅ = normal and well supported

⚠️ = possible but awkward, or bench-dependent

❌ = not available

|  | Observe | Stimulate | Capture | Replay | Fault injection | Interrogate |
| --- | --- | --- | --- | --- | --- | --- |
| **Plant model** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| **Device internals** | ✅ | ✅ | ✅ | ⚠️ | ⚠️ | ❌ |
| **Physical pins** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| **Network** | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ |
| **Service layer** | ✅ | ✅ | ✅ | ⚠️ | ✅ | ❌ |
| **Diagnostic channel** | ⚠️ | ⚠️ | ⚠️ | ❌ | ⚠️ | ✅ |

Don't take the marks as definitive.
They vary by bench, and arguing about a specific cell is a good way to learn.
The value is the shape: most cells are filled, which is why a bench feels overwhelming.

## How to use it

### Designing a test

Write the goal in one plain sentence, then find every cell you need.
If a cell you need is ❌ or ⚠️ on your bench, you've found your hard part before you started writing code, which is the cheapest possible time to find it.

### Evaluating a bench

Walk the grid and ask "can it do this, and at what rate?" That's the [[bench-evaluation-checklist|bench evaluation checklist]].

### Reading a standard or a manual

Every concept or instruction is going to be in service of one of the above cells.
When you know which one you're in, a massive standard becomes a lookup table for how to do [Capability] at [Boundary].

## What the grid does not show

It's a static picture of a system whose whole difficulty is dynamic.
Two things live off the grid and matter more than any single cell.

### Timing

Every cell has a rate, a latency, and a jitter, and those numbers decide whether your test is meaningful.
See [[time-is-the-hard-part]].

### Naming

The same physical quantity has a different name at every boundary, and translating between them is where the bugs are.
See [[five-names-for-one-signal]].
