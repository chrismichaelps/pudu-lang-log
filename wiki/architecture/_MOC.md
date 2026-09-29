---
type: moc
tags: [moc, architecture]
aliases: [Architecture]
---

# Architecture

## Shape

A program writes through a [[src/PuduLangLog/Logger|logger]], which holds a
[[src/PuduLangLog/Pipeline|pipeline]] built by a [[src/PuduLangLog/Configuration|configuration]]
or by [[src/PuduLangLog/Settings|settings]]. A write checks the level first, so a disabled event
costs one comparison; an enabled one parses its template (kept parsed for reuse), captures its
arguments under the capture policy, and passes the enrichers and filters before every sink
receives it. Sinks format and deliver; only they, the clock, and the environment enrichers reach
the outside world ([[seams/Sink]], [[seams/Clock]]).

## Layers

| Layer | Holds | May import |
| --- | --- | --- |
| `PuduLangLog` | the vocabulary: levels, values, templates, failures, events | nothing |
| `Constants/` | property names and default output templates | nothing |
| `Domain/` | parsing, capture, display, JSON, dates, numbers, rolling names, schedules, masking, settings plans, compact JSON reading | the root, Constants, pure std |
| `Expressions/` | the expression language: lexer, parser, evaluator, functions, templates | Domain, the root, std |
| public modules | logger, configuration, pipeline, enrichers, filters, sinks, formatters, settings, web middleware | Domain, Expressions, each other, std |

`Domain/` performs no effects and imports no public module. Public modules import one another
toward the core: sinks use `Sink`, never `Logger`; `Configuration` builds `Pipeline` and `Logger`.

## Pages

- [[architecture/LANGUAGE]] — the vocabulary every page uses.
- [[architecture/TESTING]] — the test levels and what each proves.
- [[grammar/pudu]] — the language rules the code follows.

## Referenced by

[[00-INDEX]]
