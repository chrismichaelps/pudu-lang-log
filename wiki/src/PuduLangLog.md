---
type: module
path: "@root/src/PuduLangLog.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, backbone]
aliases: [PuduLangLog]
---

# PuduLangLog

## Purpose

The package root and its vocabulary: the level of an event, the values a property can hold, the
parsed message template, the failure attached to an event, and the event record itself. Every
other module speaks in these types; the root holds no behaviour.

## Interface

### Signatures

```pudu
export type Level = Verbose | Debug | Information | Warning | Error | Fatal

export const LEVELS: Array[Level] = [Verbose, Debug, Information, Warning, Error, Fatal]

export type Timestamp = { millis: Int, offset: Int }

export type Scalar = Null | Boolean(Bool) | Integer(Int) | Real(Float64) | Exact(Decimal) | Text(Str) | Moment(Timestamp) | Span(Int) | Binary(Bytes)

export type Value = Scalar(Scalar) | Sequence(Array[Value]) | Structure(Structure) | Dictionary(Array[Entry])

export type Structure = { tag: Option[Str], properties: Array[Property] }

export type Entry = { key: Scalar, value: Value }

export type Property = { name: Str, value: Value }

export type Hint = Default | Destructure | Stringify

export type Alignment = { left: Bool, width: Int }

export type Hole = {
  name: Str,
  raw: Str,
  format: Option[Str],
  alignment: Option[Alignment],
  hint: Hint,
  position: Option[Int]
}

export type Token = Literal(Str) | Placeholder(Hole)

export type Binding = Unbound | Named | Positional

export type Template = { text: Str, tokens: Array[Token], holes: Array[Hole], binding: Binding }

export type Failure = { kind: Str, message: Str, trace: Array[Str], causes: Array[Failure] }

export type Event = {
  timestamp: Timestamp,
  level: Level,
  template: Template,
  properties: Array[Property],
  failure: Option[Failure],
  traceId: Option[Str],
  spanId: Option[Str]
}

export type RollingInterval = Infinite | Year | Month | Day | Hour | Minute

export const ROLLING_INTERVALS: Array[RollingInterval] = [Infinite, Year, Month, Day, Hour, Minute]

export type Formatter = fn(&Event) -> Str
```

### Linkage

- **Requires:** nothing.
- **Consumed by:** every module under [[src/PuduLangLog/_MOC]].

## Algorithm

Types only. `Timestamp.millis` counts milliseconds since the start of 1970 in UTC, and
`Timestamp.offset` is the offset in minutes east of UTC at which the event was observed; local
fields are `millis + offset * 60000`. `Span` holds a duration in milliseconds.

`RollingInterval` names how often a file sink starts a new file; `Infinite` never does.

## Negative Logic (Prohibited Paths)

- No function lives here; the vocabulary never depends on behaviour. `LEVELS` lives here because a
  `const` can name only its own module's variants.
- `Event.properties` keeps insertion order and holds each name at most once.

## Edge Cases

- A template without holes has `binding: Unbound`; one mixing numbered and named holes is `Named`.

## Depth

DEPTH 0.5 (MEDIUM). The shared language of [[domain/Event]], [[domain/Template]],
[[domain/Value]], and [[domain/Level]].

## Grill Log

- **Q:** Why one `Value` sum instead of storing text?
  **A:** Formatters decide how data looks; JSON needs numbers as numbers and structures as
  objects. _Rejected:_ rendering at capture time (loses type and structure).
- **Q:** Why is `Failure` a record rather than a trait?
  **A:** Pudu reports errors as values of the program's own types; a record of kind, message,
  trace lines, and causes is what every formatter needs from any of them. _Rejected:_ a generic
  `E` on every event (a logger would need one type parameter per error type it ever sees).
- **Q:** Why milliseconds and an offset rather than `Std.Time.Instant`?
  **A:** Output templates render local wall-clock time with its offset, which an instant alone
  does not carry. _Rejected:_ UTC only (loses the observer's zone in text output).

## Referenced by

[[decisions/ADR-0003-capture-at-write]] · [[domain/Capture]] · [[domain/Event]] · [[domain/Value]] · [[src/_MOC]] · [[src/PuduLangLog/Bridge]] · [[src/PuduLangLog/Clock]] · [[src/PuduLangLog/Configuration]] · [[src/PuduLangLog/Context]] · [[src/PuduLangLog/Domain/Capture]] · [[src/PuduLangLog/Domain/Clef]] · [[src/PuduLangLog/Domain/Dates]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/Json]] · [[src/PuduLangLog/Domain/Levels]] · [[src/PuduLangLog/Domain/Masking]] · [[src/PuduLangLog/Domain/Output]] · [[src/PuduLangLog/Domain/Padding]] · [[src/PuduLangLog/Domain/Parser]] · [[src/PuduLangLog/Domain/Properties]] · [[src/PuduLangLog/Domain/Rolling]] · [[src/PuduLangLog/Domain/Settings]] · [[src/PuduLangLog/Enricher]] · [[src/PuduLangLog/Enrichers/Environment]] · [[src/PuduLangLog/Enrichers/Masking]] · [[src/PuduLangLog/Event]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Functions]] · [[src/PuduLangLog/Expressions/Parser]] · [[src/PuduLangLog/Expressions/Syntax]] · [[src/PuduLangLog/Expressions/Template]] · [[src/PuduLangLog/Expressions/Values]] · [[src/PuduLangLog/Failure]] · [[src/PuduLangLog/Filter]] · [[src/PuduLangLog/Formatting/Compact]] · [[src/PuduLangLog/Formatting/Json]] · [[src/PuduLangLog/Formatting/Reader]] · [[src/PuduLangLog/Formatting/Text]] · [[src/PuduLangLog/LevelSwitch]] · [[src/PuduLangLog/Logger]] · [[src/PuduLangLog/Pipeline]] · [[src/PuduLangLog/SelfLog]] · [[src/PuduLangLog/Settings]] · [[src/PuduLangLog/Settings/Registry]] · [[src/PuduLangLog/Sink]] · [[src/PuduLangLog/Sinks/Async]] · [[src/PuduLangLog/Sinks/Batching]] · [[src/PuduLangLog/Sinks/Console]] · [[src/PuduLangLog/Sinks/File]] · [[src/PuduLangLog/Sinks/Http]] · [[src/PuduLangLog/Sinks/Map]] · [[src/PuduLangLog/Sinks/Memory]] · [[src/PuduLangLog/Sinks/Observable]] · [[src/PuduLangLog/Sinks/Theme]] · [[src/PuduLangLog/Timing]] · [[src/PuduLangLog/Value]] · [[src/PuduLangLog/Web/Correlation]] · [[src/PuduLangLog/Web/Diagnostic]] · [[src/PuduLangLog/Web/RequestLogging]]
