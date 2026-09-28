---
type: module
path: "@root/src/PuduLangLog/Enrichers/Environment.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, enricher]
aliases: [PuduLangLog.Enrichers.Environment]
---

# PuduLangLog.Enrichers.Environment

## Purpose

[[domain/Enrichment|Enrichers]] describing where an event came from — the machine, the user, the
deployment environment, and the process — and one that stores an event's failure as structured
data.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Enricher]], `Std.Env`, `Std.Process`,
  `Std.Result`.
- **Consumed by:** package users and settings.

## Algorithm

1. `machineName` reads `HOSTNAME` or `COMPUTERNAME`, falling back to the `hostname` command.
2. `environmentUserName` reads `USER` or `USERNAME`; `environmentName` reads the named variable,
   `Production` when unset or blank.
3. `processId` asks a short-lived shell for its parent's identifier, which is this process.
4. Each value is read once, when the enricher is made, and added unless the event already names it.
5. `failureDetail` adds `FailureDetail`, the failure as a structure tagged with its kind, holding
   `Kind`, `Message`, `Trace`, and `Causes`, or null when there is none.

## Negative Logic (Prohibited Paths)

- No enricher runs a command or reads a variable per event.

## Edge Cases

- A process identifier that cannot be read is null.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/Enrichers/EnvironmentTest`.

## Grill Log

- **Q:** Why no thread identifier or process name?
  **A:** Pudu exposes neither a thread's identity nor the executable's name; an enricher inventing
  one would mislead. _Rejected:_ a counter posing as a thread id.
- **Q:** Why read values once?
  **A:** They do not change while a process runs, and reading per event would run a command per
  event. _Rejected:_ lazy per-event reads.

## Referenced by

(none)
