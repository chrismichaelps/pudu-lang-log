---
type: module
path: "@root/src/PuduLangLog/LevelSwitch.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangLog.LevelSwitch]
---

# PuduLangLog.LevelSwitch

## Purpose

A [[domain/Level|minimum level]] shared by loggers, sinks, and enrichers and changed while the
program runs, with listeners told of each change.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Levels]], `Std.Sync`.
- **Consumed by:** [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Pipeline]],
  [[src/PuduLangLog/Sink]], [[src/PuduLangLog/Enricher]], and settings.

## Algorithm

1. The level lives in a cell; `set` compares and stores under the switch's lock.
2. When the level changed, every listener is called with the old and new levels after the lock is
   released.

## Negative Logic (Prohibited Paths)

- Setting the level it already has calls no listener.

## Edge Cases

- A listener that sets the switch again runs after the first change is stored, so no lock is held
  twice.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/LoggerTest`.

## Grill Log

- **Q:** Why call listeners outside the lock?
  **A:** A listener may log or change the switch; holding the lock would deadlock it.
  _Rejected:_ calling inside the lock.

## Referenced by

(none)
