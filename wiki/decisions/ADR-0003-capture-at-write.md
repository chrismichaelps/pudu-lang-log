---
type: decision
status: accepted
tags: [decision]
---

# ADR-0003 — Values are captured when the event is written

## Context

Events may be delivered later — by the asynchronous and batching sinks — and by other threads.
Anything an event refers to could have changed by then.

## Decision

[[src/PuduLangLog/Domain/Capture]] turns every argument into an immutable
[[src/PuduLangLog|`Value`]] when the event is written: scalars as they are, structures rendered to
text unless destructured with `@`, and every value cut to the policy's depth, text length, and
collection size. Destructuring rules — transforms by tag, tags kept as text, custom rules,
ignored or masked members — run at the same moment.

## Consequences

- Sinks never see program data, only captured values, so no sink can change or retain it.
- Very large arguments cost their capture even when a filter later drops the event; the level
  check before capture is the cheap guard.

## Referenced by

[[decisions/_MOC]] · [[domain/Capture]] · [[handoffs/2026-09-29-complete-api]]
