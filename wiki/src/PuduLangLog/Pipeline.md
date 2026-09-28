---
type: module
path: "@root/src/PuduLangLog/Pipeline.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, backbone]
aliases: [PuduLangLog.Pipeline]
---

# PuduLangLog.Pipeline

## Purpose

The [[domain/Pipeline|pipeline]] every event of one configuration passes: the level decision with
its switch and per-source overrides, the store of parsed templates, enrichment, filtering, and
emission to the joined sinks and audit sinks.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Clock]],
  [[src/PuduLangLog/Domain/Capture]], [[src/PuduLangLog/Domain/Levels]],
  [[src/PuduLangLog/Domain/Parser]], [[src/PuduLangLog/Domain/Sources]],
  [[src/PuduLangLog/Enricher]], [[src/PuduLangLog/Filter]], [[src/PuduLangLog/LevelSwitch]],
  [[src/PuduLangLog/SelfLog]], [[src/PuduLangLog/Sink]], `Std.List`, `Std.Map`, `Std.Sync`.
- **Consumed by:** [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Logger]], and the
  bootstrap logger.

## Algorithm

1. `enabled`: a silent pipeline accepts nothing. For a logger with a source context, the first
   override (most specific first) covering it decides through its switch. Otherwise the level must
   pass the minimum and, when present, the pipeline's switch.
2. `templateOf` answers a stored parse, or parses and stores it while fewer than 1000 templates of
   at most 1024 characters are held.
3. `process` applies the logger's own enrichers newest first, then the pipeline's in order; drops
   the event at the first filter that rejects it; emits to the joined sink; and answers an audit
   sink's failure.

## Negative Logic (Prohibited Paths)

- No event reaches a sink before every enricher ran, nor after a filter rejected it.
- The template store never grows past its bound, whatever templates a program invents.

## Edge Cases

- A filtered event is not audited either.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangLog/LoggerTest` and `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why enrich before filtering?
  **A:** Filters often test enriched properties such as the source context or a request id.
  _Rejected:_ filtering first.
- **Q:** Why bound the template store?
  **A:** A program that builds templates from data would otherwise grow it forever.
  _Rejected:_ an unbounded map.

## Referenced by

(none)
