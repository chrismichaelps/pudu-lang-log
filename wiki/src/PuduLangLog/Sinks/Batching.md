---
type: module
path: "@root/src/PuduLangLog/Sinks/Batching.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, sink, concurrency]
aliases: [PuduLangLog.Sinks.Batching]
---

# PuduLangLog.Sinks.Batching

## Purpose

Collects events into batches for destinations that accept many at once, writes them on a worker
thread when a batch fills, the buffering time passes, or (once) the first event arrives, and
retries failed batches on a growing [[src/PuduLangLog/Domain/Schedule|schedule]].

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Schedule]],
  [[src/PuduLangLog/SelfLog]], [[src/PuduLangLog/Sink]], `Std.Concurrent`, `Std.List`,
  `Std.Sync`, `Std.Time`.
- **Consumed by:** [[src/PuduLangLog/Sinks/Http]], package users.

## Algorithm

1. Emitting appends to the queue under a lock, dropping the event when the queue is at its limit
   and refusing it after `close`.
2. The worker wakes every few milliseconds and writes one batch when the schedule's interval has
   passed, the queue holds a full batch, or the first event waits and eager emission is on.
3. A batch is the one being retried, or up to `batchSizeLimit` events from the head of the queue.
   An empty queue calls `onEmptyBatch`.
4. Success resets the schedule. Failure keeps the batch for the next attempt; the schedule drops it
   once retrying would pass the retry time, and drops the queue after ten dropped batches. Dropped
   events go to the self-log and to the attached listener.
5. `flush` writes batches until the queue is empty or one fails; `close` stops the worker and
   flushes.

## Negative Logic (Prohibited Paths)

- No two batches are written at once: the worker and `flush` share one writing lock.
- A failed batch is never split, reordered, or merged with newer events.

## Edge Cases

- A retry time of zero drops every failing batch at once.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangLog/Sinks/BatchingTest`.

## Grill Log

- **Q:** Why poll on a short tick instead of waiting on the queue?
  **A:** The standard channel has no timed receive, and the worker must wake for time as well as
  for events. _Rejected:_ a second timer thread.
- **Q:** Why hand dropped events to a listener?
  **A:** A fallback chain can then write them elsewhere, which is the point of a fallback.
  _Rejected:_ self-log only.

## Referenced by

(none)
