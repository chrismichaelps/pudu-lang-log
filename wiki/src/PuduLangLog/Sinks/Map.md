---
type: module
path: "@root/src/PuduLangLog/Sinks/Map.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, sink]
aliases: [PuduLangLog.Sinks.Map]
---

# PuduLangLog.Sinks.Map

## Purpose

Routes each event to a sink of its own key — a tenant, a source, a level — opening sinks on first use and closing the least recently used beyond a limit.

## Interface

### Signatures

```pudu
export type Options = { keyOf: fn(&Log.Event) -> Str, create: fn(Str) -> Sink.Sink, limit: Option[Int] }

export fn defaults(keyOf: fn(&Log.Event) -> Str, create: fn(Str) -> Sink.Sink) -> Options

export fn byProperty(name: Str, fallback: Str) -> fn(&Log.Event) -> Str

export fn sink(options: Options) -> Sink.Sink
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Properties]], [[src/PuduLangLog/Domain/Recency]], [[src/PuduLangLog/Sink]], `Std.List`, `Std.Sync`.
- **Consumed by:** package users.

## Algorithm

1. The key function answers text for every event; `byProperty` reads a property with a fallback.
2. Under the map's lock the key's sink is found or opened, the event emitted, and sinks beyond the limit flushed and closed.
3. Listeners attached to the map are attached to every sink it opens later.

## Negative Logic (Prohibited Paths)

- A closed map drops events rather than reopening sinks.

## Edge Cases

- A limit of zero closes each sink after its event.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/Sinks/MapTest`.

## Grill Log

- **Q:** Why emit under the lock?
  **A:** A sink retired by one thread must not be written by another. _Rejected:_ emitting outside the lock.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Recency]] · [[subsystems/Sinks]]
