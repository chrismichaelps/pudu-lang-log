---
type: module
path: "@root/src/PuduLangLog/Domain/Output.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.75
depth_status: DEEP
tags: [module, pure]
aliases: [PuduLangLog.Domain.Output, output template]
---

# PuduLangLog.Domain.Output

## Purpose

Renders an event through an output template such as
`[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}`, producing styled pieces for
the text formatter and the console sink.

## Interface

### Signatures

```pudu
export const NEW_LINE: Str = "\n"

export fn render(output: &Log.Template, event: &Log.Event) -> Array[Display.Piece]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]],
  [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Domain/Padding]],
  [[src/PuduLangLog/Domain/Properties]], `Std.List`.
- **Consumed by:** [[src/PuduLangLog/Formatting/Text]], [[src/PuduLangLog/Sinks/Console]].

## Algorithm

1. Literal text of the output template renders in the tertiary style.
2. Holes name parts of the event: `Level` (a moniker under the hole's format), `Message` (the
   message under the hole's format, see [[src/PuduLangLog/Domain/Display]]), `Timestamp` and
   `UtcTimestamp` (under a date format), `NewLine`, `Exception` (the failure's text and a line
   break, or nothing), `TraceId`, `SpanId`, and `Properties`.
3. `Properties` renders every property that neither the message template nor the output template
   names, as a structure, or as a JSON object when its format contains `j`.
4. Any other hole names a property: text renders bare, cased by `u` or `w`; other values render
   under the hole's format; an absent property renders nothing.
5. Every part is padded to its hole's alignment.

## Negative Logic (Prohibited Paths)

- No hole ever reads a property called `Level`, `Message`, or the other built-in names; the event
  part wins.

## Edge Cases

- An aligned hole for an absent property still writes its padding.
- An event without trace or span renders those holes as empty text.

## Depth

DEPTH 0.75 (DEEP). Tested by `test/PuduLangLog/Domain/OutputTest`.

## Grill Log

- **Q:** Why render text properties bare in output templates when messages quote them?
  **A:** Output templates place context such as `{SourceContext}` in fixed columns where quotes are
  noise; messages mix values into prose where quotes mark them. _Rejected:_ one rule for both.
- **Q:** Why does `Properties` skip properties the output template names?
  **A:** A property already printed in its own column should not print twice. _Rejected:_ skipping
  only message properties.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Formatting/Text]] · [[src/PuduLangLog/Sinks/Console]] · [[subsystems/Formatting]]
