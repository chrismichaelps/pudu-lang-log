---
type: module
path: "@root/src/PuduLangLog/Event.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangLog.Event]
---

# PuduLangLog.Event

## Purpose

Builds, reads, and changes [[domain/Event|events]] for enrichers, filters, custom sinks, and
tests.

## Interface

### Signatures

```pudu
export fn create(timestamp: Log.Timestamp, level: Log.Level, template: Str, properties: Array[Log.Property]) -> Log.Event

export fn message(event: &Log.Event) -> Str

export fn property(event: &Log.Event, name: Str) -> Option[Log.Value]

export fn sourceContext(event: &Log.Event) -> Option[Str]

export fn withProperty(event: &Log.Event, name: Str, held: Log.Value) -> Log.Event

export fn withPropertyIfAbsent(event: &Log.Event, name: Str, held: Log.Value) -> Log.Event

export fn withoutProperty(event: &Log.Event, name: Str) -> Log.Event

export fn withFailure(event: &Log.Event, problem: Log.Failure) -> Log.Event

export fn withTrace(event: &Log.Event, traceId: Str, spanId: Str) -> Log.Event
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Constants/Names]],
  [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Parser]],
  [[src/PuduLangLog/Domain/Properties]].
- **Consumed by:** package users and the enrichers that read properties.

## Algorithm

1. `create` parses the template; the event has no failure, trace, or span until given one.
2. `message` renders the template with the event's properties, quoting text values.
3. `withProperty` replaces, `withPropertyIfAbsent` keeps an existing value, and `withoutProperty`
   removes; each answers a new event.

## Negative Logic (Prohibited Paths)

- Events are values: no function changes an event another holder can see.

## Edge Cases

- `sourceContext` answers only a text source context.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/LoggerTest` and `test/PuduLangLog/TopologyTest`.

## Grill Log

- **Q:** Why answer new events instead of changing one in place?
  **A:** A sub-logger's enrichment must not leak to its parent's other sinks; values make that
  automatic. _Rejected:_ copying events before each sub-logger.

## Referenced by

[[domain/Event]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Constants/Names]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/Properties]]
