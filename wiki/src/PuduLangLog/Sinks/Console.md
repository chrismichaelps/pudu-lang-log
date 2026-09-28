---
type: module
path: "@root/src/PuduLangLog/Sinks/Console.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, sink]
aliases: [PuduLangLog.Sinks.Console]
---

# PuduLangLog.Sinks.Console

## Purpose

Writes events to the terminal through an output template, coloured by a theme when the terminal
takes colour, sending events from a chosen level to standard error.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Constants/Names]],
  [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Domain/Output]],
  [[src/PuduLangLog/Domain/Parser]], [[src/PuduLangLog/Sink]], [[src/PuduLangLog/Sinks/Theme]],
  `Std.Out`, `Std.Sync`, `Std.Term`.
- **Consumed by:** package users, settings, and the examples.

## Algorithm

1. The defaults use the console template and the literate theme, coloured when `Std.Term`
   reports colour (not `NO_COLOR`, not a dumb terminal, or forced by `FORCE_COLOR`).
2. A custom formatter, when set, replaces the template and the theme.
3. Each event's text is written without an added line break, under a lock, to standard output or,
   from `standardErrorFromLevel` up, to standard error.

## Negative Logic (Prohibited Paths)

- Two threads never interleave the text of their events.

## Edge Cases

- With colour off, the text is exactly what the plain text formatter writes.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/Sinks/ConsoleTest`.

## Grill Log

- **Q:** Why follow `NO_COLOR` and `FORCE_COLOR`?
  **A:** They are how people and CI systems say what their terminal wants; escape codes in a piped
  log file are noise. _Rejected:_ always colouring.

## Referenced by

(none)
