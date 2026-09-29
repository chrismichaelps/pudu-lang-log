---
type: module
path: "@root/src/PuduLangLog/Settings/Registry.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, configuration]
aliases: [PuduLangLog.Settings.Registry]
---

# PuduLangLog.Settings.Registry

## Purpose

Names the sink and enricher factories settings may refer to, with the standard console, file, and HTTP sinks and environment enrichers registered.

## Interface

### Signatures

```pudu
export type SinkFactory = fn(&Array[(Str, Str)]) -> Result[Sink.Sink, Str]

export type EnricherFactory = fn(&Array[(Str, Str)]) -> Result[Enricher.Enricher, Str]

export type Registry = { sinks: Array[(Str, SinkFactory)], enrichers: Array[(Str, EnricherFactory)] }

export fn empty() -> Registry

export fn standard() -> Registry

export trait Registering {
  fn withSink(self: &Self, name: Str, factory: SinkFactory) -> Self
  fn withEnricher(self: &Self, name: Str, factory: EnricherFactory) -> Self
}

export fn sinkNamed(registry: &Registry, name: Str) -> Option[SinkFactory]

export fn enricherNamed(registry: &Registry, name: Str) -> Option[EnricherFactory]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Settings]], [[src/PuduLangLog/Enricher]], [[src/PuduLangLog/Enrichers/Environment]], [[src/PuduLangLog/Formatting/Compact]], [[src/PuduLangLog/Formatting/Json]], [[src/PuduLangLog/Formatting/Text]], [[src/PuduLangLog/Sink]], [[src/PuduLangLog/Sinks/Batching]], [[src/PuduLangLog/Sinks/Console]], [[src/PuduLangLog/Sinks/File]], [[src/PuduLangLog/Sinks/Http]], [[src/PuduLangLog/Sinks/Theme]], `Std.List`.
- **Consumed by:** [[src/PuduLangLog/Settings]] and package users registering their own sinks.

## Algorithm

1. Names compare ignoring case; registering a name again replaces the earlier factory.
2. Console arguments: `outputTemplate`, `theme`, `standardErrorFromLevel`, `formatter`.
3. File arguments: `path`, `outputTemplate`, `formatter`, `rollingInterval`, `fileSizeLimitBytes`, `rollOnFileSizeLimit`, `retainedFileCountLimit`, `buffered`.
4. HTTP arguments: `endpoint`, `formatter`, `batchSizeLimit`, `queueLimit`.

## Negative Logic (Prohibited Paths)

- A malformed argument fails its factory; it is never replaced by a default.

## Edge Cases

- A count argument of `none` removes the limit.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/SettingsTest`.

## Grill Log

- **Q:** Why an explicit registry?
  **A:** Pudu has no reflection over loaded modules, and an explicit list shows what configuration text can reach. _Rejected:_ discovery by naming convention.

## Referenced by

[[CHANGELOG]] · [[decisions/ADR-0004-settings-through-a-registry]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Settings]] · [[src/PuduLangLog/Settings]] · [[subsystems/Configuration]]
