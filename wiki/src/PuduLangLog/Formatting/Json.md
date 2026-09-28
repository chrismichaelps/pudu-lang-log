---
type: module
path: "@root/src/PuduLangLog/Formatting/Json.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, formatter]
aliases: [PuduLangLog.Formatting.Json]
---

# PuduLangLog.Formatting.Json

## Purpose

A [[domain/Formatting|formatter]] writing each event as one JSON document with named members, for
destinations that read events back as documents.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Dates]],
  [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Json]],
  [[src/PuduLangLog/Domain/Levels]], `Std.Option`.
- **Consumed by:** package users and settings.

## Algorithm

1. Members in order: `Timestamp` (round trip), `Level` (full name), `MessageTemplate`,
   `RenderedMessage` when asked for, `TraceId`, `SpanId`, `Exception`, `Properties` when there are
   any, and `Renderings` when a hole has a format.
2. `Renderings` groups formatted holes by property name in order of first appearance; each entry is
   `{"Format": …, "Rendering": …}`, rendered with text values unquoted.
3. Structures in properties carry their tag as `_typeTag`; every document ends with the closing
   delimiter, a line break by default.

## Negative Logic (Prohibited Paths)

- No member is written empty: absent parts are left out.

## Edge Cases

- A template repeating one property with two formats lists both renderings under that name.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/Formatting/FormattingTest`.

## Grill Log

- **Q:** Why keep renderings when properties already hold the values?
  **A:** A reader that rebuilds the message needs the formatted text the program saw, which the
  raw value alone does not give. _Rejected:_ values only.

## Referenced by

(none)
