---
type: module
path: "@root/src/PuduLangLog/Domain/Masking.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, domain]
aliases: [PuduLangLog.Domain.Masking]
---

# PuduLangLog.Domain.Masking

## Purpose

Hides sensitive parts of captured values: members of listed names, text matching patterns, and listed members of one structure tag.

## Interface

### Signatures

```pudu
export fn members(held: &Log.Value, names: &Array[Str], mask: Str) -> Log.Value

export fn property(named: &Log.Property, names: &Array[Str], mask: Str) -> Log.Property

export fn text(held: &Log.Value, patterns: &Array[Regex.Regex], mask: Str) -> Log.Value

export fn ignoring(held: &Log.Value, tag: Str, names: &Array[Str]) -> Option[Log.Value]

export fn masking(held: &Log.Value, tag: Str, names: &Array[Str], mask: Str) -> Option[Log.Value]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], `Std.Regex`.
- **Consumed by:** [[src/PuduLangLog/Enrichers/Masking]] and [[src/PuduLangLog/Configuration]].

## Algorithm

1. Names compare ignoring case at every depth, in structures and text-keyed dictionaries.
2. Patterns apply in turn to every text scalar; keys and other scalars are kept.
3. `ignoring` and `masking` answer none for any value that is not a structure of the tag, so they compose as destructuring rules.

## Negative Logic (Prohibited Paths)

- A listed member is replaced whole; nothing below it is kept.

## Edge Cases

- No patterns keep text unchanged.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/Domain/MaskingTest`.

## Grill Log

- **Q:** Why mask values rather than drop them?
  **A:** A reader still sees that the property was present. _Rejected:_ removing listed properties, which reads like a bug in the code that logged them.

## Referenced by

[[CHANGELOG]] · [[domain/Capture]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Enrichers/Masking]]
