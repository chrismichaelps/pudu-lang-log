---
type: module
path: "@root/src/PuduLangLog/Sinks/Async.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, sink, concurrency]
aliases: [PuduLangLog.Sinks.Async]
---

# PuduLangLog.Sinks.Async

## Purpose

Moves the cost of a slow sink off the calling thread: events are queued and written to the inner
sink by a worker thread, dropped or waited for when the queue is full.

## Interface

### Signatures

```pudu
export type Options = { bufferSize: Int, blockWhenFull: Bool, selfLog: SelfLog.SelfLog }

export type Async = {
  inner: Sink.Sink,
  options: Options,
  queue: Channel.Channel[Log.Event],
  accepted: Sync.Counter,
  written: Sync.Counter,
  dropped: Sync.Counter,
  listener: Sync.Cell[Option[fn(&Sink.Report) -> ()]],
  worker: Option[Concurrent.Task]
}

export fn defaults() -> Options

export fn start(inner: Sink.Sink, options: Options) -> Async

export fn sinkOf(queue: &Async) -> Sink.Sink

export fn pending(queue: &Async) -> Int

export fn dropped(queue: &Async) -> Int
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/SelfLog]], [[src/PuduLangLog/Sink]],
  `Std.Channel`, `Std.Concurrent`, `Std.Sync`.
- **Consumed by:** package users and settings.

## Algorithm

1. `start` opens a channel of `bufferSize` and a worker that writes each received event to the
   inner sink until the channel closes; a failure goes to the self-log and the attached listener.
2. Emitting counts the event as accepted after the channel takes it. Without `blockWhenFull`, an
   event arriving while the queue is full is dropped and counted.
3. `flush` waits until every accepted event was written, then flushes the inner sink; `close`
   closes the channel, joins the worker, and closes the inner sink.

## Negative Logic (Prohibited Paths)

- No event accepted before `close` is lost; the worker drains the channel before it ends.
- The queue never holds more than `bufferSize` events.

## Edge Cases

- An event emitted after `close` answers an error.
- The drop decision reads the queue's length just before sending; a race can let one sender wait
  briefly instead of dropping.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/Sinks/AsyncTest`.

## Grill Log

- **Q:** Why drop by default rather than block?
  **A:** Logging must not stall the program behind a slow disk or network; a counted drop is
  visible, a stall is not. `blockWhenFull` chooses completeness instead. _Rejected:_ blocking by
  default.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[subsystems/Sinks]]
