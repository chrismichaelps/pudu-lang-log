---
type: module
path: "@root/src/PuduLangLog/Sinks/Observable.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module, sink]
aliases: [PuduLangLog.Sinks.Observable]
---

# PuduLangLog.Sinks.Observable

## Purpose

Hands every event to subscribed observers, for in-process consumers such as live views and tests.

## Interface

### Signatures

```pudu
export type Observer = { next: fn(&Log.Event) -> (), completed: fn() -> () }

export type Subscription = { id: Int }

export type Observable = { observers: Sync.Cell[Array[(Int, Observer)]], issued: Sync.Counter, ended: Sync.Cell[Bool], lock: Sync.Mutex }

export fn create() -> Observable

export fn observer(next: fn(&Log.Event) -> ()) -> Observer

export fn subscribe(subject: &Observable, watcher: Observer) -> Subscription

export fn unsubscribe(subject: &Observable, subscription: Subscription) -> ()

export fn count(subject: &Observable) -> Int

export fn sink(subject: &Observable) -> Sink.Sink
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Sink]], `Std.Sync`.
- **Consumed by:** package users.

## Algorithm

1. Subscriptions are numbered; unsubscribing removes that observer only.
2. Events reach observers in subscription order.
3. Closing the sink ends the stream: each observer's `completed` runs once and the observers are removed.

## Negative Logic (Prohibited Paths)

- An observer subscribing after the end is told at once and never receives events.

## Edge Cases

- Unsubscribing twice does nothing.

## Depth

DEPTH 0.4 (MEDIUM). Tested by `test/PuduLangLog/Sinks/ObservableTest`.

## Grill Log

- **Q:** Why `completed` on close?
  **A:** A consumer waiting for more events needs to know none will come. _Rejected:_ closing silently.

## Referenced by

[[CHANGELOG]] · [[seams/Sink]] · [[src/PuduLangLog/_MOC]] · [[subsystems/Sinks]]
