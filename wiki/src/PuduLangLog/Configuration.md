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
export type Configuration = {
  minimum: Log.Level,
  control: Option[LevelSwitch.LevelSwitch],
  overrides: Array[Pipeline.Override],
  enrichers: Array[Enricher.Enricher],
  filters: Array[Filter.Filter],
  sinks: Array[Sink.Sink],
  audits: Array[Sink.Sink],
  policy: Capture.Policy,
  selfLog: SelfLog.SelfLog,
  clock: Clock.Clock
}

export fn create() -> Configuration

export fn nested() -> Configuration

export fn pipelineOf(configuration: &Configuration) -> Pipeline.Pipeline

export trait Configuring {
  fn createLogger(self: &Self) -> Logger.Logger
  fn createReloadableLogger(self: &Self) -> Logger.Logger
  fn minimumLevel(self: &Self, level: Log.Level) -> Self
  fn controlledBy(self: &Self, control: &LevelSwitch.LevelSwitch) -> Self
  fn overrideLevel(self: &Self, source: Str, level: Log.Level) -> Self
  fn overrideSwitch(self: &Self, source: Str, control: &LevelSwitch.LevelSwitch) -> Self
  fn writeTo(self: &Self, sink: Sink.Sink) -> Self
  fn writeToAtLeast(self: &Self, sink: Sink.Sink, minimum: Log.Level) -> Self
  fn writeToControlled(self: &Self, sink: Sink.Sink, control: &LevelSwitch.LevelSwitch) -> Self
  fn writeToWhen(self: &Self, condition: fn(&Log.Event) -> Bool, sink: Sink.Sink) -> Self
  fn writeToLogger(self: &Self, logger: &Logger.Logger) -> Self
  fn writeToFallible(self: &Self, sink: Sink.Sink, listener: fn(&Sink.Report) -> ()) -> Self
  fn writeToFallbackChain(self: &Self, sinks: Array[Sink.Sink]) -> Self
  fn auditTo(self: &Self, sink: Sink.Sink) -> Self
  fn enrichWith(self: &Self, enricher: Enricher.Enricher) -> Self
  fn enrichWithProperty(self: &Self, name: Str, held: Log.Value) -> Self
  fn filterWith(self: &Self, filter: Filter.Filter) -> Self
  fn filterExcluding(self: &Self, predicate: fn(&Log.Event) -> Bool) -> Self
  fn filterIncludingOnly(self: &Self, predicate: fn(&Log.Event) -> Bool) -> Self
  fn destructureByTransforming(self: &Self, tag: Str, transform: fn(Log.Value) -> Log.Value) -> Self
  fn destructureAsScalar(self: &Self, tag: Str) -> Self
  fn destructureWithRule(self: &Self, rule: fn(&Log.Value) -> Option[Log.Value]) -> Self
  fn destructureByIgnoring(self: &Self, tag: Str, names: Array[Str]) -> Self
  fn destructureByMasking(self: &Self, tag: Str, names: Array[Str], mask: Str) -> Self
  fn maximumDepth(self: &Self, depth: Int) -> Self
  fn maximumStringLength(self: &Self, length: Int) -> Self
  fn maximumCollectionCount(self: &Self, count: Int) -> Self
  fn selfLog(self: &Self, log: &SelfLog.SelfLog) -> Self
  fn clock(self: &Self, clock: Clock.Clock) -> Self
}
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

[[architecture/_MOC]] · [[CHANGELOG]] · [[decisions/ADR-0002-explicit-logger]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Clock]] · [[src/PuduLangLog/Domain/Capture]] · [[src/PuduLangLog/Domain/Masking]] · [[src/PuduLangLog/Enricher]] · [[src/PuduLangLog/Filter]] · [[src/PuduLangLog/LevelSwitch]] · [[src/PuduLangLog/Logger]] · [[src/PuduLangLog/Pipeline]] · [[src/PuduLangLog/SelfLog]] · [[src/PuduLangLog/Settings]] · [[src/PuduLangLog/Sink]] · [[subsystems/Configuration]]
