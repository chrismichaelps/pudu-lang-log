---
type: module
path: "@root/src/PuduLangLog/Web/Diagnostic.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: SHALLOW
tags: [module, web]
aliases: [PuduLangLog.Web.Diagnostic]
---

# PuduLangLog.Web.Diagnostic

## Purpose

Collects the properties and the failure a request handler wants on its request's single
completion event, instead of writing events of its own.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], `Std.Sync`.
- **Consumed by:** [[src/PuduLangLog/Web/RequestLogging]] and request handlers.

## Algorithm

1. `set` and `setDestructured` store a value under a name, replacing an earlier one of that name;
   the request logger captures it under the pipeline's policy when the request completes.
2. `setFailure` stores the failure the completion event carries.

## Negative Logic (Prohibited Paths)

- A collector never writes an event itself.

## Edge Cases

- Setting a name twice keeps the last value.

## Depth

DEPTH 0.4 (SHALLOW). Tested by `test/PuduLangLog/Web/RequestLoggingTest`.

## Grill Log

- **Q:** Why is the collector passed to the handler rather than reached ambiently?
  **A:** Pudu has no per-request ambient storage; an explicit collector is visible in the handler's
  signature. _Rejected:_ a shared global keyed by nothing reliable.

## Referenced by

(none)
