---
type: Pitfall
related_to:
  - "[[why-abstraction-exists]]"
  - "[[five-names-for-one-signal]]"
---
# Avoid bench specifics in test code

## The symptom

Two years in, the bench gets refreshed. Or a second bench is built at another site. Or a new product variant arrives.

And nothing runs. Not because the product changed, but because four hundred test files contain the old bench's signal names, the old I/O card's channel numbers, the old database's scaling, and a set of sleeps tuned to the old bench's speed.

The team's options are all bad. They can rewrite the suite, keep the obsolete bench in service solely to run the old tests, or quietly stop running them.

## What "bench specifics" means

Anything in a test that would have to change if the bench changed but the product didn't:

- Raw signal or symbol names (`ADC_CH7_RAW`)
- Channel, slot, and pin numbers
- Scaling constants and offsets
- File paths to databases and configuration
- IP addresses, ports, device handles
- Sleeps tuned to one bench's latency
- Assumptions about capture rates or buffer sizes

## The key distinction

Every line of a test is either a statement about **the product** or a statement about **the bench**.

| Product statement | Bench statement |
| --- | --- |
| "throttle at 30%" | "write 1229 to ADC\_CH7" |
| "coolant above 110 °C" | "set resistance on channel 4 to 220 Ω" |
| "responds within 100 ms" | "sleep 0.2 then read" |
| "fault P0128 is stored" | "byte 3 of the response is 0x28" |

Product statements are durable. They stay true across benches, variants, sites, and years. Bench statements are configuration, and configuration belongs in a file, owned by whoever owns the bench, not scattered through test logic owned by whoever owns the product.

See [[why-abstraction-exists]] and [[five-names-for-one-signal]].

## The practical structure

Three layers, and the discipline is just refusing to let them merge:

| # | Layer | What belongs in it |
| --- | --- | --- |
| 1 | **Test logic** | Intent only. Readable by a systems engineer who has never seen the bench. |
| 2 | **A mapping layer** | Intent names to bench identifiers, with units and scaling. One file per bench or variant. |
| 3 | **Bench configuration** | Hardware, channels, paths, databases. |

Swapping benches then means editing layers 2 and 3. Layer 1 is untouched, which is the entire return on the investment.

## The review question

When reviewing a test, ask one question of every line. *Would this still be correct on a different bench?*

If not, it's in the wrong layer. That single question, applied consistently in code review, is most of what keeps a suite portable, and it's much cheaper than the alternative, which is discovering the answer during a bench refresh.

## Acceptable coupling

Some coupling is unavoidable and pretending otherwise is its own trap. A test that deliberately exercises a specific piece of I/O hardware is *supposed* to know about that hardware. The goal isn't zero bench knowledge. It's that bench knowledge appears **deliberately, in one place, and labeled**, rather than accidentally, everywhere, in silence.
