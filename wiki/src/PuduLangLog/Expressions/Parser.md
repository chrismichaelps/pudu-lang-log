---
type: module
path: "@root/src/PuduLangLog/Expressions/Parser.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, expressions]
aliases: [PuduLangLog.Expressions.Parser]
---

# PuduLangLog.Expressions.Parser

## Purpose

Reads tokens into an expression tree by precedence: conditionals, `or`, `and`, `not`, comparisons and `like`/`in`/`is null`, sums, products, powers, unary minus, and member access.

## Interface

### Signatures

```pudu
export fn parse(text: Str) -> Result[Syntax.Expression, Str]

export fn expressionAt(found: &Array[Lexer.Token], start: Int) -> Step
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Expressions/Lexer]], [[src/PuduLangLog/Expressions/Syntax]], `Std.Decimal`, `Std.List`, `Std.Set`.
- **Consumed by:** [[src/PuduLangLog/Expressions]] and [[src/PuduLangLog/Expressions/Template]].

## Algorithm

1. `parse` reads one expression and refuses any token left after it.
2. `^` is right-associative; `-2 ^ 2` is `(-2) ^ 2`.
3. Whole numbers become integers; numbers with a point become exact decimals.
4. `expressionAt` reads one expression from any token, which is how template holes stop at their format's `:`.

## Negative Logic (Prohibited Paths)

- No expression is answered for text with unread tokens.

## Edge Cases

- An empty text ends too soon.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/ExpressionsTest`.

## Grill Log

- **Q:** Why recursive descent?
  **A:** Precedence reads directly off the function chain, and each level is small enough to test through the public entry point. _Rejected:_ an operator-precedence table.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Lexer]] · [[src/PuduLangLog/Expressions/Syntax]] · [[src/PuduLangLog/Expressions/Template]] · [[subsystems/Expressions]]
