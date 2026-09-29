---
type: moc
tags: [moc]
---

# PuduLangLog

The modules under `PuduLangLog`, by folder.

## Public modules

- [[src/PuduLangLog/Bridge]] — Adapts the standard library's `Std.Log` logger so lines written through it become events of a pudu-lang-log logger.
- [[src/PuduLangLog/Clock]] — The [[seams/Clock|clock seam]]: where every event's timestamp comes from, replaceable for tests.
- [[src/PuduLangLog/Configuration]] — Assembles a pipeline one decision at a time — minimum level, switches and overrides, sinks and their restrictions, audit sinks, enrichers, filters, destructuring rules and limits, self-log, and clock — and creates the logger.
- [[src/PuduLangLog/Context]] — An ambient [[domain/Enrichment|context]]: properties pushed for a stretch of work and applied to every event written meanwhile by loggers that enrich from it.
- [[src/PuduLangLog/Enricher]] — [[domain/Enrichment|Enrichers]] add, change, or remove properties of events as they pass through a pipeline or a contextual logger.
- [[src/PuduLangLog/Event]] — Builds, reads, and changes [[domain/Event|events]] for enrichers, filters, custom sinks, and tests.
- [[src/PuduLangLog/Expressions]] — Compiles filter expressions and expression templates, and turns them into the shapes the pipeline accepts: a predicate for filters, conditional sinks, and conditional enrichers.
- [[src/PuduLangLog/Failure]] — Builds the [[domain/Failure|failure]] attached to an event: a kind, a message, trace lines, and causes.
- [[src/PuduLangLog/Filter]] — Predicates over events and the [[domain/Filtering|filters]] built from them, deciding which events reach the sinks.
- [[src/PuduLangLog/LevelSwitch]] — A [[domain/Level|minimum level]] shared by loggers, sinks, and enrichers and changed while the program runs, with listeners told of each change.
- [[src/PuduLangLog/Logger]] — What a program writes through: `logger.information("Disk {Disk} is {Percent}% full", args)` and the other level methods, contextual loggers from `forContext` and `forSource`, trace identifiers, and the lifecycle of flushing, closing, and [[domain/Bootstrap|reloading]].
- [[src/PuduLangLog/Pipeline]] — The [[domain/Pipeline|pipeline]] every event of one configuration passes: the level decision with its switch and per-source overrides, the store of parsed templates, enrichment, filtering, and emission to the joined sinks and audit sinks.
- [[src/PuduLangLog/SelfLog]] — Reports problems inside the pipeline — a failing sink, a template bound to the wrong number of arguments, an invalid property name — without ever failing the program's own log call.
- [[src/PuduLangLog/Settings]] — Changes a configuration from key-value settings — a JSON document, environment variables, or pairs — and hands back the level switches the settings declared.
- [[src/PuduLangLog/Sink]] — The [[seams/Sink|sink seam]] and the wrappers that shape where events go: restriction by level, switch, or condition.
- [[src/PuduLangLog/Timing]] — Measures a unit of work and writes one event when it ends: `Charging 42 completed in 12.3 ms`, or `abandoned` when it did not finish its job.
- [[src/PuduLangLog/Value]] — Builds the [[domain/Value|values]] a program passes to a log call: scalars, sequences, structures, and dictionaries, and turns any `Capturable` type into one with `Value.of`.

## Constants

- [[src/PuduLangLog/Constants/Names]] — Names shared across modules: the source context property and the default output templates of the console and file sinks.

## Domain

- [[src/PuduLangLog/Domain/Capture]] — Binds a call's arguments to its template's holes and captures each value under the pipeline's [[domain/Capture|capture policy]]: the hint of its hole, limits on depth, text length, and collection size, and the destructuring rules the configuration added.
- [[src/PuduLangLog/Domain/Clef]] — Reads one line of compact JSON — as the compact formatters write it — back into an event.
- [[src/PuduLangLog/Domain/Dates]] — Renders a `Timestamp` under the format an output template or hole names, such as `{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz}`, and a duration (`Span`) under its own formats.
- [[src/PuduLangLog/Domain/Display]] — Renders values, messages, and failures as text split into styled pieces.
- [[src/PuduLangLog/Domain/EventId]] — Gives every message template a stable 32-bit identifier, so all events written from one template can be found together however their values differ.
- [[src/PuduLangLog/Domain/Json]] — Writes captured [[domain/Value|values]] as JSON text for the JSON formatters, the `:j` message format, and `{Properties:j}`.
- [[src/PuduLangLog/Domain/Levels]] — Orders the six [[domain/Level|levels]], names them, reads them from text, and renders the short monikers output templates ask for with formats such as `u3`.
- [[src/PuduLangLog/Domain/Masking]] — Hides sensitive parts of captured values: members of listed names, text matching patterns, and listed members of one structure tag.
- [[src/PuduLangLog/Domain/Numbers]] — Renders integers, exact decimals, and floats under the format a hole or output template names, such as `{Elapsed:0.000}`, `{Count:N0}`, or `{Id:X8}`, and gives floats their shortest round-trip text.
- [[src/PuduLangLog/Domain/Output]] — Renders an event through an output template such as `[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}`, producing styled pieces for the text formatter and the console sink.
- [[src/PuduLangLog/Domain/Padding]] — Applies a hole's alignment and the `u` / `w` case formats that text renderers share.
- [[src/PuduLangLog/Domain/Parser]] — Turns message template text into a [[domain/Template|template]]: literal text and holes, each hole with its name, capture hint, format, alignment, and position.
- [[src/PuduLangLog/Domain/Properties]] — Reads and changes the ordered property list of an [[domain/Event|event]] by name.
- [[src/PuduLangLog/Domain/Recency]] — Orders keys from least to most recently used and names those beyond a limit.
- [[src/PuduLangLog/Domain/Rolling]] — The arithmetic of [[domain/Rolling|rolling files]]: which period a moment falls in, when the next one starts, what the period's file is called, how existing names read back, and which old files retention removes.
- [[src/PuduLangLog/Domain/Schedule]] — Decides how long the [[src/PuduLangLog/Sinks/Batching|batching sink]] waits between batches after failures, when a failing batch is given up, and when the whole queue is dropped.
- [[src/PuduLangLog/Domain/Settings]] — Reads colon-separated key-value settings into a plan of levels, switches, overrides, properties, enrichers, sinks, filters, and capture limits, and flattens JSON and environment names into such keys.
- [[src/PuduLangLog/Domain/Sources]] — Matches [[domain/SourceContext|source contexts]] against prefixes for level overrides and source filters.

## Enrichers

- [[src/PuduLangLog/Enrichers/Environment]] — [[domain/Enrichment|Enrichers]] describing where an event came from — the machine, the user, the deployment environment, and the process — and one that stores an event's failure as structured data.
- [[src/PuduLangLog/Enrichers/Masking]] — Enrichers that hide sensitive property values before any sink sees them: by name, by pattern, and a ready set of patterns for addresses, card numbers, and account numbers.

## Expressions

- [[src/PuduLangLog/Expressions/Evaluator]] — Evaluates an expression tree against an event: names, built-in properties, members, indexers, wildcards, operators, literals, and conditionals.
- [[src/PuduLangLog/Expressions/Functions]] — The built-in functions: text search and slicing, regular expressions, `Length`, `ElementAt`, `TypeOf`, `TagOf`, `ToString`, `Round`, `Coalesce`, `IsDefined`, `Undefined`, `Now`, `UtcDateTime`, `Nest`, and `Inspect`.
- [[src/PuduLangLog/Expressions/Lexer]] — Scans expression text into identifiers, built-in `@` names, numbers, quoted text, keywords, and symbols.
- [[src/PuduLangLog/Expressions/Parser]] — Reads tokens into an expression tree by precedence: conditionals, `or`, `and`, `not`, comparisons and `like`/`in`/`is null`, sums, products, powers, unary minus, and member access.
- [[src/PuduLangLog/Expressions/Syntax]] — The tree an expression parses to, and the walks that list the names it reads and the functions it calls.
- [[src/PuduLangLog/Expressions/Template]] — Parses and renders expression templates: literal text, holes `{expression:format}`, and the directives `#if`, `#else if`, `#else`, `#each … in …`, `#delimit`, and `#end`.
- [[src/PuduLangLog/Expressions/Values]] — The meaning of values inside expressions: truth, numbers across kinds, equality, order, `like` patterns, and rendering.

## Formatting

- [[src/PuduLangLog/Formatting/Compact]] — [[domain/Formatting|Formatters]] writing each event as one line of compact JSON with `@`-prefixed reserved members: `compact` keeps the template, `rendered` writes the message and the template's identifier.
- [[src/PuduLangLog/Formatting/Json]] — A [[domain/Formatting|formatter]] writing each event as one JSON document with named members, for destinations that read events back as documents.
- [[src/PuduLangLog/Formatting/Reader]] — Reads events back from compact JSON: one line, many lines, or a file.
- [[src/PuduLangLog/Formatting/Text]] — A [[domain/Formatting|formatter]] that writes each event through an output template, for text sinks such as files and custom writers.

## Settings

- [[src/PuduLangLog/Settings/Registry]] — Names the sink and enricher factories settings may refer to, with the standard console, file, and HTTP sinks and environment enrichers registered.

## Sinks

- [[src/PuduLangLog/Sinks/Async]] — Moves the cost of a slow sink off the calling thread: events are queued and written to the inner sink by a worker thread, dropped or waited for when the queue is full.
- [[src/PuduLangLog/Sinks/Batching]] — Collects events into batches for destinations that accept many at once, writes them on a worker thread when a batch fills, the buffering time passes, or (once) the first event arrives, and retries failed batches on a growing [[src/PuduLangLog/Domain/Schedule|schedule]].
- [[src/PuduLangLog/Sinks/Console]] — Writes events to the terminal through an output template, coloured by a theme when the terminal takes colour, sending events from a chosen level to standard error.
- [[src/PuduLangLog/Sinks/File]] — Appends events to files: one file, or one per [[domain/Rolling|period]], with a size limit that either drops events or starts the next sequence, retention by count and age, optional buffering flushed on demand or on a timer, and hooks when a file is opened or deleted.
- [[src/PuduLangLog/Sinks/Http]] — Posts batches of events to an HTTP endpoint, one formatted event per line (compact JSON by default), with custom headers such as an API key.
- [[src/PuduLangLog/Sinks/Map]] — Routes each event to a sink of its own key — a tenant, a source, a level — opening sinks on first use and closing the least recently used beyond a limit.
- [[src/PuduLangLog/Sinks/Memory]] — Keeps events in memory, for tests of code that logs and for in-process views of recent events.
- [[src/PuduLangLog/Sinks/Observable]] — Hands every event to subscribed observers, for in-process consumers such as live views and tests.
- [[src/PuduLangLog/Sinks/Theme]] — Terminal colour themes for the [[src/PuduLangLog/Sinks/Console|console sink]]: the escape sequence each [[src/PuduLangLog/Domain/Display|display style]] starts with, and the painting of styled pieces.

## Web

- [[src/PuduLangLog/Web/Correlation]] — Gives every HTTP request one identifier — the one its header brings or a generated one — for the handler's events, the completion event, and the response.
- [[src/PuduLangLog/Web/Diagnostic]] — Collects the properties and the failure a request handler wants on its request's single completion event, instead of writing events of its own.
- [[src/PuduLangLog/Web/RequestLogging]] — Writes one event per HTTP request served with `Std.Http.Server`: `HTTP GET /orders/7 responded 200 in 1.2345 ms`, at `Error` for server errors and failures, with the properties a handler collected and the trace of a `traceparent` header.

## Referenced by

[[src/_MOC]] · [[src/PuduLangLog]]
