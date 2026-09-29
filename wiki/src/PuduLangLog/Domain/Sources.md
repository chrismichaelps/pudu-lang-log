---
type: module
path: "@root/src/PuduLangLog/Domain/Sources.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, leaf, pure]
aliases: [PuduLangLog.Domain.Sources]
---

# PuduLangLog.Domain.Sources

## Purpose

Matches [[domain/SourceContext|source contexts]] against prefixes for level overrides and source
filters.

## Interface

### Signatures

```pudu
export fn covers(prefix: Str, source: Str) -> Bool

export fn beneath(prefix: Str, source: Str) -> Bool
```

### Linkage

- **Consumed by:** [[src/PuduLangLog/Pipeline]], [[src/PuduLangLog/Filter]].

## Algorithm

1. A prefix covers a source that equals it or continues with a `.` right after it.
2. `covers` ignores case (level overrides); `beneath` keeps it (source filters).

## Negative Logic (Prohibited Paths)

- `App.Web` never covers `App.Website`.

## Edge Cases

- An empty prefix covers only an empty source.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/Domain/RollingTest`.

## Grill Log

- **Q:** Why is override matching case-insensitive but source filtering not?
  **A:** Overrides are often written in settings files by hand; filters are code next to the names
  they match. _Rejected:_ one rule for both.
- **Q:** Why does ordering overrides not live here?
  **A:** The configuration sorts overrides by their lower-cased prefix with `List.sortOn`, which
  needs no comparator of its own. _Rejected:_ a `before` comparator, which left the order of two
  equal prefixes to the sort.

## Referenced by

[[domain/Level]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Filter]] · [[src/PuduLangLog/Pipeline]]
