---
type: module
path: "@root/src/PuduLangLog/Clock.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: SHALLOW
tags: [module, seam]
aliases: [PuduLangLog.Clock]
---

# PuduLangLog.Clock

## Purpose

The [[seams/Clock|clock seam]]: where every event's timestamp comes from, replaceable for tests.

## Interface

### Signatures

```pudu
export type Clock = fn() -> Log.Timestamp

export fn system() -> Clock

export fn utc() -> Clock

export fn fixed(moment: Log.Timestamp) -> Clock

export fn stepping(start: Log.Timestamp, step: Int) -> Clock
```

### Linkage

- **Requires:** [[src/PuduLangLog]], `Std.Sync`, `Std.Time`.
- **Consumed by:** [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Pipeline]],
  [[src/PuduLangLog/Timing]], and the request logger.

## Algorithm

1. `system` reads the current instant and the machine's offset on each call; `utc` uses offset 0.
2. `fixed` always answers one moment; `stepping` answers a moment that advances by a step on
   every reading, under a lock.

## Negative Logic (Prohibited Paths)

- No other module reads the current time for an event.

## Edge Cases

- A `stepping` clock shared by threads never answers the same moment twice.

## Depth

DEPTH 0.4 (SHALLOW). Tested by `test/PuduLangLog/ValueTest`.

## Grill Log

- **Q:** Why a function rather than a record of operations?
  **A:** A timestamp is the only thing asked of it. _Rejected:_ a trait object.

## Referenced by

[[seams/Clock]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Configuration]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Pipeline]]
