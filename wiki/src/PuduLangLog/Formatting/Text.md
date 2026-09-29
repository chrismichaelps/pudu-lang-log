---
type: module
path: "@root/src/PuduLangLog/Formatting/Text.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, formatter]
aliases: [PuduLangLog.Formatting.Text]
---

# PuduLangLog.Formatting.Text

## Purpose

A [[domain/Formatting|formatter]] that writes each event through an output template, for text
sinks such as files and custom writers.

## Interface

### Signatures

```pudu
export fn template(output: Str) -> Log.Formatter
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]],
  [[src/PuduLangLog/Domain/Output]], [[src/PuduLangLog/Domain/Parser]].
- **Consumed by:** [[src/PuduLangLog/Sinks/File]], package users, and settings.

## Algorithm

1. The output template is parsed once when the formatter is made.
2. Each event renders through [[src/PuduLangLog/Domain/Output]] and the styled pieces are joined
   without styles.

## Negative Logic (Prohibited Paths)

- No template is parsed per event.

## Edge Cases

- A template without `{NewLine}` writes events without line breaks; the caller chooses.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/Formatting/FormattingTest`.

## Grill Log

- **Q:** Why not add a line break automatically?
  **A:** Some destinations frame events themselves; the template states the layout exactly.
  _Rejected:_ an implicit trailing line break.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Output]] · [[src/PuduLangLog/Settings/Registry]] · [[src/PuduLangLog/Sinks/File]] · [[subsystems/Formatting]]
