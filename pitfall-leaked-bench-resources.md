---
type: Pitfall
related_to:
  - "[[the-test-bench-resource-model]]"
  - "[[boundary-device-internals]]"
---
# Avoid leaving state behind

## The symptom

The symptom takes several forms:

- The test passes alone and fails in the suite
- The first run passes, the second fails, the third passes again
- Tests begin failing partway through the day with no code change
- It works for you and not for the colleague who runs it after you
- The bench "needs a reboot every morning"

Every one of these is the same disease. **Something left state behind.**

## What gets left behind

### Overridden variables

You forced a sensor value and never released it. Every subsequent test runs against a frozen sensor and has no idea.

### Modified calibrations

You changed a threshold to check the logic, the test failed before the restore line, and now the DUT has a wrong threshold that survives reboots. This is the worst one, because it's persistent and invisible. See [[boundary-device-internals]].

### Injected faults

A relay is still open. The next test's DUT genuinely has a broken sensor, and its failure looks like a real bug.

### Running stimuli

A waveform generator is still playing into an input nobody's watching.

### Held resources

Captures, channels, sessions, and locks never released. Eventually the bench runs out and refuses to start anything.

### DUT state

A latched fault, a degraded mode, a diagnostic session, a non-default operating mode.

## Why it happens

Almost always the same shape:

```
setup()
do_the_thing()
assert something      # ← raises here
cleanup()             # ← never runs
```

Cleanup on the success path only isn't cleanup. And during development, tests fail far more often than they pass, so the cleanup you wrote is the code path least likely to execute.

## The fix

### Guaranteed teardown

Always use whatever your language provides (`try/finally`, context managers, test fixtures with teardown, RAII). Never a bare cleanup call at the end of a function.

### Restore the original value

Don't reset to a guess. Read the original value before you change it, and put *that* back. Writing a hardcoded "default" just moves the corruption around.

### Assume you arrived dirty

Don't trust that the previous test cleaned up. Establish your preconditions explicitly at the start, with a known mode, faults cleared, and no active overrides. Cheap insurance, and it turns "mysterious failure" into "clear precondition failure."

### Verify the cleanup

Confirm the fault actually cleared and the calibration actually restored. A cleanup that silently failed is worse than none, because you believe it worked.

## Debugging a suspected leak

Stop debugging the failing test. It's probably innocent. Instead:

1. Run it in isolation. If it passes, you've confirmed the diagnosis.
2. Bisect the suite to find what runs before it.
3. Look at that test's teardown, especially its failure path.

And when the bench is in an unknown state, do the full reset (power cycle the DUT, restore calibrations from a known-good set, clear faults) rather than poking at symptoms. See [[the-test-bench-resource-model]].
