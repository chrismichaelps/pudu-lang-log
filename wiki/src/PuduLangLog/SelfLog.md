---
type: module
path: "@root/src/PuduLangLog/SelfLog.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: SHALLOW
tags: [module]
aliases: [PuduLangLog.SelfLog]
---

# PuduLangLog.SelfLog

## Purpose

Reports problems inside the pipeline — a failing sink, a template bound to the wrong number of
arguments, an invalid property name — without ever failing the program's own log call.

## Interface

### Signatures

```pudu
export type SelfLog = { writer: Sync.Cell[Option[fn(Str) -> ()]] }

export fn create() -> SelfLog

export fn toErrorStream() -> SelfLog

export fn enable(log: &SelfLog, writer: fn(Str) -> ()) -> ()

export fn disable(log: &SelfLog) -> ()

export fn isEnabled(log: &SelfLog) -> Bool

export fn write(log: &SelfLog, message: Str) -> ()
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Dates]], `Std.Io`, `Std.Sync`,
  `Std.Time`.
- **Consumed by:** [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Pipeline]],
  [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Sink]], and every sink that can fail.

## Algorithm

1. A self-log holds an optional writer in a shared cell; it starts disabled.
2. `write` prefixes the message with the UTC time in round-trip form and calls the writer, if any.

## Negative Logic (Prohibited Paths)

- The self-log never writes to a configured sink, so a failing sink cannot loop.

## Edge Cases

- Enabling after a logger was built takes effect at once, since the writer is shared.

## Depth

DEPTH 0.4 (SHALLOW). Tested by `test/PuduLangLog/LoggerTest`.

## Grill Log

- **Q:** Why is the self-log part of the configuration instead of global?
  **A:** Pudu has no mutable module state; each pipeline names where its problems go.
  _Rejected:_ a process-wide switch.

## Referenced by

[[decisions/ADR-0001-logging-never-fails-the-caller]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Configuration]] · [[src/PuduLangLog/Logger]] · [[src/PuduLangLog/Pipeline]] · [[src/PuduLangLog/Sink]] · [[src/PuduLangLog/Sinks/Async]] · [[src/PuduLangLog/Sinks/Batching]]
