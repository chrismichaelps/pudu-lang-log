---
type: module
path: "@root/src/PuduLangLog/Filter.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangLog.Filter]
---

# PuduLangLog.Filter

## Purpose

Predicates over events and the [[domain/Filtering|filters]] built from them, deciding which events
reach the sinks.

## Interface

### Signatures

```pudu
export type Predicate = fn(&Log.Event) -> Bool

export type Filter = fn(&Log.Event) -> Bool

export fn byExcluding(predicate: Predicate) -> Filter

export fn byIncludingOnly(predicate: Predicate) -> Filter

export fn fromSource(source: Str) -> Predicate

export fn withProperty(name: Str) -> Predicate

export fn withPropertyValue(name: Str, expected: Log.Value) -> Predicate

export fn withPropertyWhere(name: Str, test: fn(&Log.Value) -> Bool) -> Predicate

export fn atLeast(minimum: Log.Level) -> Predicate

export fn withFailure() -> Predicate

export fn allOf(predicates: Array[Predicate]) -> Predicate

export fn anyOf(predicates: Array[Predicate]) -> Predicate

export fn not(predicate: Predicate) -> Predicate
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Constants/Names]],
  [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Domain/Properties]],
  [[src/PuduLangLog/Domain/Sources]], `Std.List`.
- **Consumed by:** [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Pipeline]], and settings.

## Algorithm

1. `byExcluding` keeps what a predicate rejects; `byIncludingOnly` keeps what it accepts.
2. `fromSource` matches the source context and everything beneath it, with case significant.
3. Property predicates test presence, equality with a value, or an arbitrary test; `atLeast` and
   `withFailure` test the level and the failure; `allOf`, `anyOf`, and `not` combine.

## Negative Logic (Prohibited Paths)

- A property predicate is false for an event without the property.

## Edge Cases

- `allOf([])` accepts everything and `anyOf([])` nothing.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why keep `Predicate` and `Filter` as separate names for one shape?
  **A:** A predicate says whether an event matches; a filter says whether it continues.
  `byExcluding` is where the two differ. _Rejected:_ one name for both meanings.

## Referenced by

[[domain/Filtering]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Configuration]] · [[src/PuduLangLog/Constants/Names]] · [[src/PuduLangLog/Domain/Sources]] · [[src/PuduLangLog/Pipeline]] · [[subsystems/Pipeline]]
