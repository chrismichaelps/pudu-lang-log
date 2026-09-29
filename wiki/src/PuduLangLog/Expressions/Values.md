---
type: module
path: "@root/src/PuduLangLog/Expressions/Values.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, expressions]
aliases: [PuduLangLog.Expressions.Values]
---

# PuduLangLog.Expressions.Values

## Purpose

The meaning of values inside expressions: truth, numbers across kinds, equality, order, `like` patterns, and rendering.

## Interface

### Signatures

```pudu
export fn boolean(flag: Bool) -> Option[Log.Value]

export fn number(held: Decimal) -> Option[Log.Value]

export fn text(held: Str) -> Option[Log.Value]

export fn isTrue(held: &Option[Log.Value]) -> Bool

export fn decimalOf(held: &Option[Log.Value]) -> Option[Decimal]

export fn textOf(held: &Option[Log.Value]) -> Option[Str]

export fn itemsOf(held: &Option[Log.Value]) -> Option[Array[Log.Value]]

export fn equal(left: &Log.Value, right: &Log.Value, ignoring: Bool) -> Bool

export fn equals(left: &Option[Log.Value], right: &Option[Log.Value], ignoring: Bool) -> Option[Log.Value]

export fn compare(operator: Str, left: &Option[Log.Value], right: &Option[Log.Value]) -> Option[Log.Value]

export fn like(subject: Str, pattern: Str, ignoring: Bool) -> Bool

export fn render(held: &Log.Value, format: Option[Str]) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]], `Std.Decimal`, `Std.List`.
- **Consumed by:** [[src/PuduLangLog/Expressions/Evaluator]], [[src/PuduLangLog/Expressions/Functions]], [[src/PuduLangLog/Expressions/Template]], and [[src/PuduLangLog/Expressions]].

## Algorithm

1. Only the boolean `true` is true.
2. Integers, floats, and decimals compare as decimals, so `3 = 3.0`.
3. `number` stores a result at the smallest scale that keeps it, so `1 / 4` is `0.25`.
4. Equality with an undefined side is undefined; ordering is defined only between numbers.
5. `like` matches `%` against any run and `_` against one character; doubled wildcards are literal.

## Negative Logic (Prohibited Paths)

- Text is never ordered; `'a' < 'b'` is undefined.

## Edge Cases

- Structures are equal when they hold the same members, in any order.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/ExpressionsTest`.

## Grill Log

- **Q:** Why normalise the scale of results?
  **A:** Division answers 28 places, and a result compared or printed with trailing zeros differs from the literal a user wrote. _Rejected:_ comparing by value only, which still prints `0.2500…`.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Functions]] · [[src/PuduLangLog/Expressions/Template]] · [[subsystems/Expressions]]
