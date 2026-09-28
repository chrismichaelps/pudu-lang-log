---
type: module
path: "@root/src/PuduLangLog/Logger.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, backbone]
aliases: [PuduLangLog.Logger]
---

# PuduLangLog.Logger

## Purpose

What a program writes through: `logger.information("Disk {Disk} is {Percent}% full", args)` and the
other level methods, contextual loggers from `forContext` and `forSource`, trace identifiers, and
the lifecycle of flushing, closing, and [[domain/Bootstrap|reloading]].

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Constants/Names]],
  [[src/PuduLangLog/Domain/Capture]], [[src/PuduLangLog/Domain/Parser]],
  [[src/PuduLangLog/Enricher]], [[src/PuduLangLog/Pipeline]], [[src/PuduLangLog/SelfLog]],
  [[src/PuduLangLog/Sink]], `Std.Sync`.
- **Consumed by:** package users, [[src/PuduLangLog/Configuration]], and every integration.

## Algorithm

1. A logger is a root pipeline (fixed, or reloadable through a shared cell), the enrichers of its
   context, its source context, and its trace and span.
2. A write checks the level first and does nothing more when it fails. Otherwise it parses the
   template (through the pipeline's store), binds the arguments, reports binding problems to the
   self-log, stamps the event with the pipeline's clock and the logger's trace, and processes it.
3. `forContext` captures its value immediately under the pipeline's policy and adds a property
   enricher; a blank name is reported and ignored. `forSource` adds `SourceContext`, which also
   selects level overrides.
4. `tryWrite` answers an audit sink's failure; every other write discards it.
5. `reload` swaps a reloadable root's pipeline, then flushes and closes the old one; every logger
   derived from the root follows.
6. `asSink` makes a logger a sink of another pipeline, applying its own level check, enrichers,
   and filters.

## Negative Logic (Prohibited Paths)

- A disabled level costs one comparison: nothing is parsed or captured.
- No write fails the program except through `tryWrite` and an audit sink.

## Edge Cases

- A message property wins over a context property of the same name, and an inner context over an
  outer one.

## Depth

DEPTH 0.85 (DEEP). Tested by `test/PuduLangLog/LoggerTest` and `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why methods on the logger rather than module functions?
  **A:** `logger.forSource("App.Api").information(...)` reads in the order it happens, without
  nesting calls. _Rejected:_ `Logger.information(&Logger.forSource(&logger, …), …)`.
- **Q:** Why no global default logger?
  **A:** Pudu has no mutable module state; a program passes its logger, or a reloadable one built
  at startup, to the code that writes. See [[decisions/ADR-0001-explicit-loggers]].
  _Rejected:_ hidden global state.
- **Q:** Why capture `forContext` values at once?
  **A:** The value is the one current when the context was made; capturing later could observe a
  changed value. _Rejected:_ capturing per event.

## Referenced by

(none)
