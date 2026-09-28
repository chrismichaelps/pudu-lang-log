---
type: module
path: "@root/src/PuduLangLog/Sink.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, seam]
aliases: [PuduLangLog.Sink]
---

# PuduLangLog.Sink

## Purpose

The [[seams/Sink|sink seam]] and the wrappers that shape where events go: restriction by level,
switch, or condition; aggregation that isolates failures; audit aggregation that reports them;
fallible sinks with listeners; and fallback chains.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Levels]],
  [[src/PuduLangLog/LevelSwitch]], [[src/PuduLangLog/SelfLog]], `Std.List`.
- **Consumed by:** [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Logger]],
  [[src/PuduLangLog/Pipeline]], and every module under [[src/PuduLangLog/Sinks/_MOC]].

## Algorithm

1. A sink emits an event and answers `Ok` or the failure's text; it can be flushed, closed, and
   given a listener for failures it finds later (a batch it could not send).
2. `aggregate` emits to every sink; a failure goes to the self-log and the aggregate answers `Ok`.
3. `audited` emits to every sink and answers the first failure.
4. `fallible` reports each failure to a listener as a `Permanent` report with the event, and
   attaches the listener to the sink for later failures.
5. `fallbackChain` emits to each sink until one answers `Ok`. Each sink's listener hands the events
   it later loses to the sinks after it; the last sink's listener is the chain's own.

## Negative Logic (Prohibited Paths)

- A normal sink's failure never reaches the program; only audit sinks can fail a write.

## Edge Cases

- A fallback chain whose every sink fails answers the last failure.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why do sinks answer a `Result` instead of reporting failures themselves?
  **A:** The wrapper decides: aggregation isolates, audit reports, fallback retries elsewhere.
  _Rejected:_ sinks writing to the self-log directly.
- **Q:** Why are `Report` and `Listener` declared before `Sink`?
  **A:** The 0.1.2 checker treats a function alias that a record field names before its
  declaration as a distinct type in importing modules
  ([pudu-lang#372](https://github.com/chrismichaelps/pudu-lang/issues/372)). _Rejected:_ spelling
  the function type out in the field.

## Referenced by

(none)
