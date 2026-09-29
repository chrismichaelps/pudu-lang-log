---
type: module
path: "@root/src/PuduLangLog/Expressions/Syntax.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module, expressions]
aliases: [PuduLangLog.Expressions.Syntax]
---

# PuduLangLog.Expressions.Syntax

## Purpose

The tree an expression parses to, and the walks that list the names it reads and the functions it calls.

## Interface

### Signatures

```pudu
export type Expression = Constant(Log.Value)
  | Name(Str)
  | Member(Expression, Str)
  | Index(Expression, Expression)
  | Wildcard(Expression, Quantifier)
  | Call(Str, Array[Expression], Bool)
  | Unary(Str, Expression)
  | Binary(Str, Expression, Expression, Bool)
  | ArrayOf(Array[Element])
  | ObjectOf(Array[Field])
  | Conditional(Expression, Expression, Expression)

export type Quantifier = AnyOf | AllOf

export type Element = Item(Expression) | SpreadItems(Expression)

export type Field = Pair(Str, Expression) | SpreadMembers(Expression)

export fn namesIn(expression: &Expression) -> Array[Str]

export fn callsIn(expression: &Expression) -> Array[Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]].
- **Consumed by:** [[src/PuduLangLog/Expressions/Parser]], [[src/PuduLangLog/Expressions/Evaluator]], [[src/PuduLangLog/Expressions/Template]], and [[src/PuduLangLog/Expressions]].

## Algorithm

1. `Binary` and `Call` carry a flag for `ci`, the case-insensitive modifier.
2. `Wildcard` wraps the target of `[?]` (any element) and `[*]` (every element).
3. `namesIn` and `callsIn` walk the tree outermost first; names keep first appearance and are unique.

## Negative Logic (Prohibited Paths)

- The tree holds no evaluated values other than literal constants.

## Edge Cases

- An array or object literal with spreads holds `SpreadItems` or `SpreadMembers` elements.

## Depth

DEPTH 0.4 (MEDIUM). Tested by `test/PuduLangLog/ExpressionsTest`.

## Grill Log

- **Q:** Why keep operators as text in the tree?
  **A:** The evaluator dispatches on a small fixed set, and the parser already refuses anything else. _Rejected:_ one variant per operator, which doubles the tree for no new check.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Parser]] · [[src/PuduLangLog/Expressions/Template]] · [[subsystems/Expressions]]
