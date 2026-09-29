---
type: module
path: "@root/src/PuduLangLog/Domain/Recency.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
tags: [module, domain]
aliases: [PuduLangLog.Domain.Recency]
---

# PuduLangLog.Domain.Recency

## Purpose

Orders keys from least to most recently used and names those beyond a limit.

## Interface

### Signatures

```pudu
export fn touch(keys: &Array[Str], key: Str) -> Array[Str]

export fn overflow(keys: &Array[Str], limit: Int) -> Array[Str]
```

### Linkage

- **Requires:** `Std.List`, `Std.Math`.
- **Consumed by:** [[src/PuduLangLog/Sinks/Map]].

## Algorithm

1. `touch` moves a key to the most recent end.
2. `overflow` answers the least recent keys beyond the limit; a negative limit counts as zero.

## Negative Logic (Prohibited Paths)

- A key never appears twice.

## Edge Cases

- A limit of zero retires every key.

## Depth

DEPTH 0.3 (SHALLOW). Tested by `test/PuduLangLog/Domain/RecencyTest`.

## Grill Log

- **Q:** Why a pure order apart from the sink?
  **A:** The eviction rule is where a mistake closes the wrong file, and it is testable without sinks. _Rejected:_ keeping the order inside the sink's state.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Sinks/Map]]
