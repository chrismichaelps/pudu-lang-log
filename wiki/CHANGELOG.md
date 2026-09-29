---
type: changelog
tags: [changelog]
---

# Changelog

## 2026-09-29 — Complete public API (#1)

- Configuration from settings: [[src/PuduLangLog/Settings]] applies key-value pairs, JSON
  sections, and environment variables through a [[src/PuduLangLog/Settings/Registry]] of named
  sinks and enrichers; the key rules are the pure [[src/PuduLangLog/Domain/Settings]]
  ([[decisions/ADR-0004-settings-through-a-registry]]).
- Sinks: [[src/PuduLangLog/Sinks/Map]] opens one sink per key and closes the least recently used
  beyond a limit ([[src/PuduLangLog/Domain/Recency]]); [[src/PuduLangLog/Sinks/Observable]] hands
  events to subscribers.
- [[src/PuduLangLog/Formatting/Reader]] reads compact JSON back into events
  ([[src/PuduLangLog/Domain/Clef]]).
- Sensitive data: [[src/PuduLangLog/Enrichers/Masking]] hides properties by name or pattern;
  `Configuration.destructureByIgnoring` and `destructureByMasking` leave out or mask members of a
  tag ([[src/PuduLangLog/Domain/Masking]]).
- [[src/PuduLangLog/Bridge]] routes `Std.Log` lines into a pipeline;
  [[src/PuduLangLog/Web/Correlation]] carries one identifier across a request;
  `Environment.processName` and `environmentVariable` enrichers; `Expressions.computed`.
- Expressions: `1 / 4` is `0.25` rather than a 28-place decimal, and `TypeOf` of an undefined
  value is `undefined` ([[src/PuduLangLog/Expressions/Values]],
  [[src/PuduLangLog/Expressions/Functions]]).
- Fix: level overrides match the source context as given, not as cut by `maximumStringLength`
  ([[src/PuduLangLog/Logger]], [[domain/SourceContext]]).
- The vault gained its maps of content, architecture, decisions, domain, seams, and subsystems,
  and `test/Package/VaultTest` keeps it matched to the code ([[architecture/TESTING]]).

## 2026-09-28 — Initial pipeline (#1)

- The package `@chrismichaelps/pudu-lang-log` with the module root `PuduLangLog` and the language
  range `>=0.1.2 <0.2.0`.
- Message templates, capture, and binding ([[src/PuduLangLog/Domain/Parser]],
  [[src/PuduLangLog/Domain/Capture]]); the [[src/PuduLangLog/Logger]],
  [[src/PuduLangLog/Configuration]], and [[src/PuduLangLog/Pipeline]] with level switches,
  overrides, context, enrichers, filters, sub-loggers, audit sinks, and reloading.
- Formatters for text, JSON, and compact JSON; console, file, HTTP, batching, async, and memory
  sinks; environment enrichers; timed operations; request logging; and the expression language
  ([[src/PuduLangLog/Expressions]]).

## Referenced by

[[00-INDEX]] · [[handoffs/2026-09-29-complete-api]]
