---
type: module
path: "@root/src/PuduLangLog/Context.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangLog.Context]
---

# PuduLangLog.Context

## Purpose

An ambient [[domain/Enrichment|context]]: properties pushed for a stretch of work and applied to
every event written meanwhile by loggers that enrich from it.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Capture]],
  [[src/PuduLangLog/Enricher]], `Std.Sync`.
- **Consumed by:** package users through `Configuration.enrichWith(Context.enricher(&context))`.

## Algorithm

1. The context is a stack of enrichers in a cell. `push` stores the stack with one more and answers
   a bookmark holding the stack as it was; `restore` puts it back.
2. `using` pushes, runs an action, and restores. `suspend` empties the stack until its bookmark is
   restored; `reset` empties it; `clone` copies it into an independent context.
3. `enricher` applies the stack newest first, so the innermost push of a name wins.

## Negative Logic (Prohibited Paths)

- A clone never sees pushes made to its source afterwards, nor the reverse.

## Edge Cases

- Restoring an older bookmark pops every push made after it.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why an explicit context value instead of one per thread?
  **A:** Pudu has no thread-local storage or thread identity; a context passed to each piece of
  work, or cloned for a new thread, is explicit about what it covers. _Rejected:_ a shared global
  stack, which threads would corrupt.

## Referenced by

(none)
