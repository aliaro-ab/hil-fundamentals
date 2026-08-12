---
type: Guide
related_to:
  - "[[the-capability-grid]]"
  - "[[five-names-for-one-signal]]"
_organized: true
_favorite: true
_favorite_index: 2
---
# Bench evaluation checklist

What to ask when evaluating a test bench: [[the-capability-grid|the capability grid]] turned into questions to guide you through discovery.
Work through it with whoever owns the bench and write the answers down.

The document you produce is more useful than most bench documentation you will receive.

## Orientation

1. What is the DUT, and what does the bench simulate?
2. What does the plant model represent, and what does it deliberately leave out?
3. What rate does the real-time loop run at?
4. Is this bench closed loop, open loop, or configurable per test?
5. Who owns it, who else uses it, and how is time allocated?
6. What state does it start in, and how do I return it to that state?

## Per boundary

For each of the six boundaries ([[the-plant-model|the plant model]], [[device-internals|device internals]], [[physical-pins|physical pins]], [[the-network|the network]], [[the-service-layer|the service layer]], [[the-diagnostic-channel|the diagnostic channel]]) ask:

1. Is it present on this bench at all?
2. Can I observe? At what rate? With what latency?
3. Can I stimulate? Set values only, or generate real waveforms?
4. Can I capture? At what rate, how many channels, how long a window?
5. Can I inject faults? Which kinds, and what is prohibited?
6. Does its data share a clock with the other boundaries?

## Naming and units

1. Where does the signal mapping live, and who owns it?
2. How is it regenerated when the software or network database changes?
3. How do we know when it goes stale?
4. Which units does each layer use, and where does conversion occur?
5. Are there product variants, and how does the mapping handle them?

See [[five-names-for-one-signal]].

## Timing

1. What is the fastest event I can reliably observe?
2. What is the finest timing assertion I can honestly make?
3. Can the real-time side evaluate triggers, or only my script?
4. Is pre-trigger capture available, and how deep is the buffer?
5. What is the latency between my script acting and the bench acting?

See [[time-is-the-hard-part]].

## Lifecycle and safety

1. How do I acquire and release each resource?
2. What occurs if my test crashes mid-run? Does anything auto-release?
3. Which operations persist across a power cycle? (Calibrations especially.)
4. What are the interlocks, and what combinations can damage the DUT?
5. What is the recovery procedure when the bench is in an unknown state?

See [[the-test-bench-resource-model]].

## Reproducibility

1. Is the bench configuration under version control?
2. Are the plant model, mapping, and network databases versioned alongside the tests?
3. Can I tell, from a result six months old, exactly what configuration produced it?
4. Is there a second bench, and do results agree between them?

## Known limitations

1. What can this bench *not* test? Get a real answer.
2. What is known to be inaccurate in the model?
3. Which tests are known to be flaky, and why?
4. What is the most common reason a test fails here for a reason that is not the product?

This last question is the highest-value question on this list.
Ask it of the most experienced person on the team, and take notes.
