---
type: module
path: "@root/src/PuduLangLog/Domain/Schedule.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, pure]
aliases: [PuduLangLog.Domain.Schedule]
---

# PuduLangLog.Domain.Schedule

## Purpose

Decides how long the [[src/PuduLangLog/Sinks/Batching|batching sink]] waits between batches after
failures, when a failing batch is given up, and when the whole queue is dropped.

## Interface

### Signatures

```pudu
export type State = { buffering: Int, retryLimit: Int, failures: Int, dropped: Int, firstFailure: Option[Int] }

export type Verdict = { state: State, dropBatch: Bool, dropQueue: Bool }

export fn create(buffering: Int, retryLimit: Int) -> State

export fn succeeded(state: &State) -> State

export fn failed(state: &State, now: Int) -> Verdict

export fn interval(state: &State) -> Int
```

### Linkage

- **Requires:** `Std.Math`, `Std.Option`.
- **Consumed by:** [[src/PuduLangLog/Sinks/Batching]].

## Algorithm

1. With at most one failure since the last success, the wait is the buffering time.
2. From the second consecutive failure, the wait starts at the larger of the buffering time and
   five seconds and doubles per further failure, capped at one minute, and never shorter than the
   buffering time.
3. A failure at `now` records the first failure time; the batch is dropped when
   `now - first + wait >= retryLimit`.
4. Ten batches dropped in a row drop the queue; a success resets everything.

## Negative Logic (Prohibited Paths)

- The wait never overflows: doubling stops at the cap.

## Edge Cases

- A retry limit of zero drops every failing batch at once.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/Domain/RollingTest`.

## Grill Log

- **Q:** Why drop the queue after ten dropped batches?
  **A:** A destination that has refused every batch for that long is down; holding more events only
  grows memory. _Rejected:_ retrying forever.
- **Q:** Why is the cap applied on every doubling?
  **A:** It keeps the value small without a loop condition whose `<` against `<=` choice cannot
  change the answer, which mutation testing reported. _Rejected:_ stopping the loop at the cap.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Sinks/Batching]]
