---
type: module
path: "@root/src/PuduLangLog/Sinks/Memory.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, sink]
aliases: [PuduLangLog.Sinks.Memory]
---

# PuduLangLog.Sinks.Memory

## Purpose

Keeps events in memory, for tests of code that logs and for in-process views of recent events.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]],
  [[src/PuduLangLog/Sink]], `Std.List`, `Std.Sync`.
- **Consumed by:** package users and the suites.

## Algorithm

1. The sink appends each event under the store's lock and keeps at most `capacity` of the newest.
2. Queries answer the events, their messages, those a predicate accepts, those from a template,
   and the latest one.

## Negative Logic (Prohibited Paths)

- The store never holds more than its capacity.

## Edge Cases

- `latest` of an empty store is none.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/Sinks/MemoryTest`.

## Grill Log

- **Q:** Why a capacity?
  **A:** An in-process view of recent events must not grow with uptime. _Rejected:_ unbounded
  only.

## Referenced by

(none)
