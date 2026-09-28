---
type: module
path: "@root/src/PuduLangLog/Failure.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
tags: [module, leaf]
aliases: [PuduLangLog.Failure]
---

# PuduLangLog.Failure

## Purpose

Builds the [[domain/Failure|failure]] attached to an event: a kind, a message, trace lines, and
causes.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]].
- **Consumed by:** package users, [[src/PuduLangLog/Timing]], and the request logger.

## Algorithm

1. `from` names the kind and takes the message from `show` of any error value, without the
   quotes `show` puts around text.
2. `withTrace` appends trace lines; `causedBy` appends a cause; `describe` renders all of it.

## Negative Logic (Prohibited Paths)

- Nothing here raises or unwinds; a failure is only data.

## Edge Cases

- An empty message renders as the kind alone.

## Depth

DEPTH 0.3 (SHALLOW). Tested by `test/PuduLangLog/ValueTest`.

## Grill Log

- **Q:** Why name the kind explicitly in `from`?
  **A:** Pudu cannot name a value's type at run time; the caller knows it. _Rejected:_ a generic
  kind such as `Error`.

## Referenced by

(none)
