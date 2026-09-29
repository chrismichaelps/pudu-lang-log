---
type: module
path: "@root/src/PuduLangLog/Enricher.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangLog.Enricher]
---

# PuduLangLog.Enricher

## Purpose

[[domain/Enrichment|Enrichers]] add, change, or remove properties of events as they pass through a
pipeline or a contextual logger.

## Interface

### Signatures

```pudu
export type Enricher = fn(Log.Event, &Capture.Policy) -> Log.Event

export fn property(name: Str, held: Log.Value) -> Enricher

export fn destructured(name: Str, held: Log.Value) -> Enricher

export fn computed(name: Str, compute: fn(&Log.Event) -> Log.Value, destructure: Bool) -> Enricher

export fn from(change: fn(Log.Event) -> Log.Event) -> Enricher

export fn when(condition: fn(&Log.Event) -> Bool, enricher: Enricher) -> Enricher

export fn atLevel(minimum: Log.Level, enricher: Enricher) -> Enricher

export fn atSwitch(control: &LevelSwitch.LevelSwitch, enricher: Enricher) -> Enricher

export fn all(enrichers: Array[Enricher]) -> Enricher
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Capture]],
  [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Domain/Properties]],
  [[src/PuduLangLog/LevelSwitch]].
- **Consumed by:** [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Context]],
  [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Pipeline]], and the environment enrichers.

## Algorithm

1. An enricher takes an event and the pipeline's capture policy and answers the enriched event.
2. `property`, `destructured`, and `computed` add a property only when the event lacks that name,
   capturing the value under the pipeline's policy; `computed` destructures when asked.
3. `when`, `atLevel`, and `atSwitch` apply another enricher conditionally; `all` applies several in
   order; `from` wraps any function over events.

## Negative Logic (Prohibited Paths)

- A property enricher never overwrites a property the event already has, so message values win.

## Edge Cases

- A computed value is computed only when the property is missing.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why pass the capture policy to enrichers?
  **A:** Enriched values obey the same limits and destructuring rules as message values.
  _Rejected:_ capturing enriched values unchecked.

## Referenced by

[[domain/Enrichment]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Configuration]] · [[src/PuduLangLog/Context]] · [[src/PuduLangLog/Domain/Capture]] · [[src/PuduLangLog/Enrichers/Environment]] · [[src/PuduLangLog/Enrichers/Masking]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/LevelSwitch]] · [[src/PuduLangLog/Logger]] · [[src/PuduLangLog/Pipeline]] · [[src/PuduLangLog/Settings/Registry]] · [[subsystems/Pipeline]]
