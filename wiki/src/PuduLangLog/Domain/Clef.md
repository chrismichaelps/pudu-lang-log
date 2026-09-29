---
type: module
path: "@root/src/PuduLangLog/Domain/Clef.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, domain]
aliases: [PuduLangLog.Domain.Clef]
---

# PuduLangLog.Domain.Clef

## Purpose

Reads one line of compact JSON — as the compact formatters write it — back into an event.

## Interface

### Signatures

```pudu
export fn line(text: Str) -> Result[Log.Event, Str]

export fn event(document: &Json.Json) -> Result[Log.Event, Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Domain/Parser]], `Std.Decimal`, `Std.Json`, `Std.List`, `Std.Option`, `Std.Time.Format`.
- **Consumed by:** [[src/PuduLangLog/Formatting/Reader]].

## Algorithm

1. `@t` is required and read as RFC 3339, keeping its offset in minutes.
2. `@mt` is the template; without it `@m` becomes a template with its braces escaped.
3. `@l` defaults to `Information`; `@x` is read back as kind, message, trace, and a chain of causes.
4. Members starting `@@` are properties named with one `@`; other reserved members are skipped.
5. Whole numbers become integers, fractions exact decimals, and objects structures tagged by `$type`.

## Negative Logic (Prohibited Paths)

- A reserved member that is not text refuses the line.

## Edge Cases

- A line with no template reads with an empty template.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/Domain/ClefTest` and `test/PuduLangLog/Formatting/ReaderTest`.

## Grill Log

- **Q:** Why read causes as a chain?
  **A:** The text form flattens nesting, and a chain of inner failures is by far the common shape. _Rejected:_ reading every cause as a sibling.

## Referenced by

[[CHANGELOG]] · [[domain/Failure]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Formatting/Reader]]
