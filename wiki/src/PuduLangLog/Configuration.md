---
type: module
path: "@root/src/PuduLangLog/Configuration.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, backbone]
aliases: [PuduLangLog.Configuration]
---

# PuduLangLog.Configuration

## Purpose

Assembles a pipeline one decision at a time — minimum level, switches and overrides, sinks and
their restrictions, audit sinks, enrichers, filters, destructuring rules and limits, self-log, and
clock — and creates the logger.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Clock]],
  [[src/PuduLangLog/Domain/Capture]], [[src/PuduLangLog/Enricher]], [[src/PuduLangLog/Filter]], [[src/PuduLangLog/LevelSwitch]],
  [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Pipeline]], [[src/PuduLangLog/SelfLog]],
  [[src/PuduLangLog/Sink]], `Std.List`.
- **Consumed by:** package users, settings, and the bootstrap logger.

## Algorithm

1. `create` keeps `Information` and above; `nested` keeps everything, for sub-loggers.
2. `minimumLevel` replaces any switch; `controlledBy` sets the minimum to `Verbose` and lets the
   switch decide. `overrideLevel` and `overrideSwitch` register one override per source prefix,
   a later one replacing an earlier one of the same prefix in any case.
3. Sink methods wrap with [[src/PuduLangLog/Sink]]: `writeToAtLeast`, `writeToControlled`,
   `writeToWhen`, `writeToLogger`, `writeToFallible`, and `writeToFallbackChain`.
4. Destructuring methods change the capture policy.
5. `createLogger` builds a logger over the pipeline; `createReloadableLogger` builds one whose
   pipeline `Logger.reload` can replace, for logging before the full configuration is known.
6. `pipelineOf` joins the sinks with `Sink.aggregate`, joins audit sinks with `Sink.audited`, and
   sorts overrides by lower-cased prefix, descending, so `App.Web` is tried before `App`.

## Negative Logic (Prohibited Paths)

- A sink restriction can raise the level a sink sees, never lower it below the pipeline's.

## Edge Cases

- A configuration without sinks builds a logger that accepts events and writes them nowhere.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/LoggerTest` and `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why a trait of methods over a record?
  **A:** The configuration reads top to bottom, one decision per line. _Rejected:_ nested
  sub-configuration objects.
- **Q:** Why does each call answer a new configuration?
  **A:** A base configuration can be shared and extended for several loggers without one affecting
  another. _Rejected:_ a mutable builder.

## Referenced by

(none)
