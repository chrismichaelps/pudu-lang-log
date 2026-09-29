---
type: module
path: "@root/src/PuduLangLog/Bridge.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module, adapter]
aliases: [PuduLangLog.Bridge]
---

# PuduLangLog.Bridge

## Purpose

Adapts the standard library's `Std.Log` logger so lines written through it become events of a pudu-lang-log logger.

## Interface

### Signatures

```pudu
export fn standard(logger: &Logger.Logger) -> Standard.Logger

export fn levelOf(level: Standard.Level) -> Log.Level
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Constants/Names]], [[src/PuduLangLog/Domain/Parser]], [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Pipeline]], `Std.Log`, `Std.Option`, `Std.Out`.
- **Consumed by:** package users with libraries that log through `Std.Log`.

## Algorithm

1. The standard logger keeps every level and prints nothing; its format function forwards each line.
2. The line's name becomes the source context, so overrides apply; each field becomes a text property.
3. The message becomes an event template with braces escaped, so it renders as written.
4. Timestamps come from this logger's clock; the trace of the logger is kept.

## Negative Logic (Prohibited Paths)

- A field named `SourceContext` or with a blank name is dropped.

## Edge Cases

- `Silent` lines never reach the format.

## Depth

DEPTH 0.4 (MEDIUM). Tested by `test/PuduLangLog/BridgeTest`.

## Grill Log

- **Q:** Why forward from the format function?
  **A:** It is the one hook `Std.Log` hands every line, with its level, name, and fields intact. _Rejected:_ parsing the printed text back.

## Referenced by

[[CHANGELOG]] · [[decisions/ADR-0002-explicit-logger]] · [[src/PuduLangLog/_MOC]] · [[subsystems/Configuration]]
