---
type: module
path: "@root/src/PuduLangLog/Domain/EventId.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, leaf, pure]
aliases: [PuduLangLog.Domain.EventId]
---

# PuduLangLog.Domain.EventId

## Purpose

Gives every message template a stable 32-bit identifier, so all events written from one template
can be found together however their values differ. The rendered compact JSON formatter writes it
as `@i`, and expressions read it as `@i`.

## Interface

### Signatures

```pudu
export fn compute(template: Str) -> UInt32

export fn hex(id: UInt32) -> Str
```

### Linkage

- **Requires:** `Std.Option`, `Std.Text`.
- **Consumed by:** [[src/PuduLangLog/Formatting/Compact]] and the expression evaluator.

## Algorithm

1. For each character: add its code point, add the hash shifted left by 10, and xor the hash
   shifted right by 6, all in wrapping 32-bit arithmetic.
2. Finish by adding the hash shifted left by 3, xoring it shifted right by 11, and adding it
   shifted left by 15.
3. `hex` writes eight lower-case hexadecimal digits.

## Negative Logic (Prohibited Paths)

- The hash depends on the template text only, never on values or time.

## Edge Cases

- The empty template hashes to `00000000`.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/Domain/OutputTest`.

## Grill Log

- **Q:** Why this hash?
  **A:** It is short, has no table, and gives the same value on every platform, which is what an
  identifier stored in log files needs. _Rejected:_ a cryptographic hash (longer than useful).

## Referenced by

(none)
