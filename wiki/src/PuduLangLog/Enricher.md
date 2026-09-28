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

(none)
