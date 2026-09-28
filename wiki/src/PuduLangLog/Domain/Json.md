---
type: module
path: "@root/src/PuduLangLog/Domain/Json.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, pure]
aliases: [PuduLangLog.Domain.Json]
---

# PuduLangLog.Domain.Json

## Purpose

Writes captured [[domain/Value|values]] as JSON text for the JSON formatters, the `:j` message
format, and `{Properties:j}`.

## Interface

### Signatures

```pudu
export fn quote(text: Str) -> Str

export fn scalar(atom: &Log.Scalar) -> Str

export fn value(held: &Log.Value, typeTag: &Option[Str]) -> Str

export fn hexOf(bytes: &Bytes) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Dates]],
  [[src/PuduLangLog/Domain/Numbers]], `Std.Decimal`, `Std.Option`.
- **Consumed by:** [[src/PuduLangLog/Domain/Display]] and the JSON formatters.

## Algorithm

1. `quote` escapes `"` and `\`, writes `\n`, `\r`, `\t`, and `\f` by name, and every other
   character below 32 as `\u00XX`; everything else passes through.
2. Scalars: `null`, `true`, `false`, integers and decimals bare, floats in their shortest text
   (`1E+30` is valid JSON), text quoted, moments in round-trip form, spans in constant form, and
   bytes as upper-case hexadecimal, all quoted. Infinities and NaN are quoted names.
3. Sequences are arrays; structures are objects in property order, followed by the tag under
   `typeTag` when both exist; dictionaries are objects keyed by each key's text.

## Negative Logic (Prohibited Paths)

- No unescaped control character, quote, or backslash ever reaches the output.
- No bare `Infinity` or `NaN`, which JSON cannot read.

## Edge Cases

- A null dictionary key is the string `"null"`; a moment key stays one quoted string.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/Domain/JsonTest`.

## Grill Log

- **Q:** Why append the type tag after the properties?
  **A:** Readers index by property first; the tag is metadata. _Rejected:_ tag first.
- **Q:** Why is the tag's name a parameter?
  **A:** Formatters differ: `$type` for compact and message JSON, `_typeTag` for the plain JSON
  formatter. _Rejected:_ one fixed name.

## Referenced by

[[src/PuduLangLog/Domain/Capture]] · [[src/PuduLangLog/Domain/Dates]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/Numbers]]
