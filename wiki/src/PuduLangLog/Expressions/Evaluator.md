---
type: module
path: "@root/src/PuduLangLog/Expressions/Evaluator.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, expressions]
aliases: [PuduLangLog.Expressions.Evaluator]
---

# PuduLangLog.Expressions.Evaluator

## Purpose

Evaluates an expression tree against an event: names, built-in properties, members, indexers, wildcards, operators, literals, and conditionals.

## Interface

### Signatures

```pudu
export type Scope = { event: Log.Event, locals: Array[(Str, Log.Value)], now: Log.Timestamp, referenced: Array[Str] }

export fn scopeOf(event: &Log.Event, now: Log.Timestamp) -> Scope

export fn evaluate(expression: &Syntax.Expression, within: &Scope) -> Option[Log.Value]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/EventId]], [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Domain/Properties]], [[src/PuduLangLog/Expressions/Functions]], [[src/PuduLangLog/Expressions/Syntax]], [[src/PuduLangLog/Expressions/Values]], `Std.Decimal`, `Std.List`, `Std.Math.Float`, `Std.Option`.
- **Consumed by:** [[src/PuduLangLog/Expressions]] and [[src/PuduLangLog/Expressions/Template]].

## Algorithm

1. A name is a bound local first, then a built-in (`@t`, `@m`, `@mt`, `@l`, `@x`, `@p`, `@i`, `@r`, `@tr`, `@sp`), then an event property.
2. `and` and `or` treat anything but `true` as false and are never undefined.
3. A comparison or call over `[?]` or `[*]` is evaluated once per element and joined with any or all.
4. Arithmetic is exact except `^`, which answers a float.
5. `Rest()` answers the properties the template does not name; `Rest(true)` also leaves out those the message names.

## Negative Logic (Prohibited Paths)

- Division or remainder by zero is undefined.

## Edge Cases

- A wildcard over something that is not a collection is false.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/ExpressionsTest`.

## Grill Log

- **Q:** Why evaluate wildcards by rewriting the node per element?
  **A:** It keeps one evaluator for every operator and function. _Rejected:_ a separate quantified evaluator per operator.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Functions]] · [[src/PuduLangLog/Expressions/Syntax]] · [[src/PuduLangLog/Expressions/Template]] · [[src/PuduLangLog/Expressions/Values]] · [[subsystems/Expressions]]
