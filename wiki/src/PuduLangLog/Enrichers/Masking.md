---
type: module
path: "@root/src/PuduLangLog/Enrichers/Masking.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, enricher]
aliases: [PuduLangLog.Enrichers.Masking]
---

# PuduLangLog.Enrichers.Masking

## Purpose

Enrichers that hide sensitive property values before any sink sees them: by name, by pattern, and a ready set of patterns for addresses, card numbers, and account numbers.

## Interface

### Signatures

```pudu
export const MASK: Str = "***"

export fn properties(names: Array[Str], mask: Str) -> Enricher.Enricher

export fn patterns(expressions: Array[Str], mask: Str) -> Result[Enricher.Enricher, Str]

export fn sensitive(mask: Str) -> Result[Enricher.Enricher, Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Masking]], [[src/PuduLangLog/Enricher]], `Std.Regex`.
- **Consumed by:** package users.

## Algorithm

1. `properties` hides listed properties and listed members at any depth.
2. `patterns` compiles every expression first and refuses the set if one does not compile; it also hides matches in the failure's message and its causes.
3. `sensitive` is `patterns` over e-mail addresses, card numbers, and IBANs.

## Negative Logic (Prohibited Paths)

- Masking runs as an enricher, so sinks never see the original value.

## Edge Cases

- Template text is not masked; it is written by the program, not captured.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/Enrichers/MaskingTest`.

## Grill Log

- **Q:** Why an enricher rather than a sink wrapper?
  **A:** Every sink, including audit sinks, then receives the same masked event. _Rejected:_ masking per sink, which leaves one forgotten sink unmasked.

## Referenced by

[[CHANGELOG]] · [[domain/Capture]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Masking]] · [[subsystems/Pipeline]]
