---
type: module
path: "@root/src/PuduLangLog/Timing.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangLog.Timing, operations]
---

# PuduLangLog.Timing

## Purpose

Measures a unit of work and writes one event when it ends: `Charging 42 completed in 12.3 ms`, or
`abandoned` when it did not finish its job.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Levels]],
  [[src/PuduLangLog/Failure]], [[src/PuduLangLog/Logger]], `Std.Decimal`, `Std.Sync`, `Std.Time`.
- **Consumed by:** package users.

## Algorithm

1. `begin` records the monotonic time; `beginWith` takes the counter from the caller.
2. Ending writes the operation's template followed by `{Outcome:l} in {Elapsed:0.0} ms`, binding the
   template's arguments, then `Outcome` (`completed` or `abandoned`) and `Elapsed` in milliseconds.
3. Completion writes at `Information` and abandonment at `Warning` unless `at` changes them; with
   `warnAfter`, a completion slower than the limit is raised to at least `Warning`.
4. `enrichWith` and `completeWith` add properties to the outcome event; `abandonWith` attaches a
   failure; `cancel` ends the operation silently.
5. `run` completes around an action; `runResult` completes on `Ok` and abandons with the error on
   `Err`.

## Negative Logic (Prohibited Paths)

- An operation writes at most one event, however many times it is ended.

## Edge Cases

- An operation never ended writes nothing; there is no finalizer to abandon it.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/TimingTest`.

## Grill Log

- **Q:** Why must the caller end an operation explicitly?
  **A:** Pudu has no scope-exit hook, so an operation cannot notice it was dropped. `run` and
  `runResult` end it for the common shapes. _Rejected:_ a background timeout that guesses.

## Referenced by

(none)
