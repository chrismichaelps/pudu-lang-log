---
type: module
path: "@root/src/PuduLangLog/Formatting/Reader.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module, formatter]
aliases: [PuduLangLog.Formatting.Reader]
---

# PuduLangLog.Formatting.Reader

## Purpose

Reads events back from compact JSON: one line, many lines, or a file.

## Interface

### Signatures

```pudu
export fn readLine(text: Str) -> Result[Log.Event, Str]

export fn readAll(text: Str) -> Result[Array[Log.Event], Str]

export fn readFile(path: Str) -> Result[Array[Log.Event], Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Clef]], `Std.Io`.
- **Consumed by:** package users replaying or inspecting logs.

## Algorithm

1. Blank lines are skipped.
2. The first unreadable line fails the whole text and is named by its number.

## Negative Logic (Prohibited Paths)

- No partial list of events is answered for text with a bad line.

## Edge Cases

- Empty text holds no events.

## Depth

DEPTH 0.4 (MEDIUM). Tested by `test/PuduLangLog/Formatting/ReaderTest`.

## Grill Log

- **Q:** Why fail the whole text on one bad line?
  **A:** A replay that skips lines silently misreports what happened. _Rejected:_ skipping unreadable lines.

## Referenced by

[[CHANGELOG]] · [[domain/Event]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Clef]] · [[subsystems/Formatting]]
