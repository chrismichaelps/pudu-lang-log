---
type: module
path: "@root/src/PuduLangLog/Domain/Properties.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: SHALLOW
tags: [module, leaf, pure]
aliases: [PuduLangLog.Domain.Properties]
---

# PuduLangLog.Domain.Properties

## Purpose

Reads and changes the ordered property list of an [[domain/Event|event]] by name.

## Interface

### Signatures

```pudu
export fn find(properties: &Array[Log.Property], name: Str) -> Option[Log.Value]

export fn has(properties: &Array[Log.Property], name: Str) -> Bool

export fn put(properties: &Array[Log.Property], name: Str, value: Log.Value) -> Array[Log.Property]

export fn putIfAbsent(properties: &Array[Log.Property], name: Str, value: Log.Value) -> Array[Log.Property]

export fn remove(properties: &Array[Log.Property], name: Str) -> Array[Log.Property]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], `Std.List`.
- **Consumed by:** [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Capture]],
  [[src/PuduLangLog/Event]], and every enricher and formatter that reads properties.

## Algorithm

1. `find` and `has` compare names exactly.
2. `put` replaces the value of an existing name where it stands, or appends a new name.
3. `putIfAbsent` appends only a new name; `remove` drops the name.

## Negative Logic (Prohibited Paths)

- No operation leaves a name bound twice.

## Edge Cases

- Names are case-sensitive: `User` and `user` are different properties.

## Depth

DEPTH 0.4 (SHALLOW). Tested by `test/PuduLangLog/Domain/DisplayTest`.

## Grill Log

- **Q:** Why an ordered array instead of a map?
  **A:** Formatters write properties in the order they were attached, and events carry a handful
  of them, so a scan costs less than hashing. _Rejected:_ `Map[Str, Value]`, which loses order.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Capture]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/Output]] · [[src/PuduLangLog/Enricher]] · [[src/PuduLangLog/Event]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Functions]] · [[src/PuduLangLog/Filter]] · [[src/PuduLangLog/Sinks/Map]]
