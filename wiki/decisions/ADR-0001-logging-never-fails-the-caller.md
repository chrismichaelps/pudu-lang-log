---
type: decision
status: accepted
tags: [decision]
---

# ADR-0001 — Logging never fails the caller

## Context

A program logs because something happened that matters. If writing that event could stop or
fail the program — a full disk, an unreachable collector, a malformed template — logging would
become a new way for the program to fail.

## Decision

Every write answers nothing to its caller. A problem inside the pipeline — a template whose
arguments do not match, a sink that fails, a sink factory that throws away a batch — is reported
to the [[src/PuduLangLog/SelfLog]] and the event continues to every other sink
([[src/PuduLangLog/Sink]] `aggregate`). Audit sinks are the single exception: their failure is
the answer of `Logger.tryWrite`, for trails whose writes must be confirmed.

## Consequences

- A sink can fail without the program noticing unless the self-log is enabled or a listener is
  attached with `writeToFallible`.
- Tests of logging code read the self-log to prove problems were reported.

## Referenced by

[[decisions/_MOC]] · [[handoffs/2026-09-29-complete-api]]
