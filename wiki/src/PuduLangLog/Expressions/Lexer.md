---
type: module
path: "@root/src/PuduLangLog/Expressions/Lexer.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, expressions]
aliases: [PuduLangLog.Expressions.Lexer]
---

# PuduLangLog.Expressions.Lexer

## Purpose

Scans expression text into identifiers, built-in `@` names, numbers, quoted text, keywords, and symbols.

## Interface

### Signatures

```pudu
export type Kind = Identifier | BuiltIn | Number | Text | Keyword | Symbol | End

export type Token = { kind: Kind, text: Str, position: Int }

export fn tokens(text: Str) -> Result[Array[Token], Str]
```

### Linkage

- **Requires:** `Std.Char`, `Std.Set`.
- **Consumed by:** [[src/PuduLangLog/Expressions/Parser]] and [[src/PuduLangLog/Expressions/Template]].

## Algorithm

1. Whitespace separates tokens and is dropped.
2. Quoted text runs between single quotes; a doubled quote is one quote.
3. Keywords match ignoring case and are lowered; identifiers keep their case.
4. Two-character symbols (`<>`, `<=`, `>=`, `..`) are tried before single ones.
5. The token list always ends with an `End` token at the text's length.

## Negative Logic (Prohibited Paths)

- A character that starts no token fails the scan with its position.

## Edge Cases

- Hexadecimal numbers start `0x`.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/ExpressionsTest`.

## Grill Log

- **Q:** Why keep token positions?
  **A:** Every syntax error names where it happened. _Rejected:_ errors without positions.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions/Parser]] · [[src/PuduLangLog/Expressions/Template]] · [[subsystems/Expressions]]
