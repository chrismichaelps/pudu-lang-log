---
type: module
path: "@root/src/PuduLangLog/Expressions/Template.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, expressions]
aliases: [PuduLangLog.Expressions.Template]
---

# PuduLangLog.Expressions.Template

## Purpose

Parses and renders expression templates: literal text, holes `{expression:format}`, and the directives `#if`, `#else if`, `#else`, `#each … in …`, `#delimit`, and `#end`.

## Interface

### Signatures

```pudu
export type Node = TextNode(Str)
  | Hole(Syntax.Expression, Option[Str])
  | Choice(Array[Branch], Array[Node])
  | Each(Str, Option[Str], Syntax.Expression, Array[Node], Array[Node], Array[Node])

export type Branch = { condition: Syntax.Expression, body: Array[Node] }

export fn parse(text: Str) -> Result[Array[Node], Str]

export fn expressionsIn(nodes: &Array[Node]) -> Array[Syntax.Expression]

export fn render(nodes: &Array[Node], within: &Evaluator.Scope) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Json]], [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Expressions/Evaluator]], [[src/PuduLangLog/Expressions/Lexer]], [[src/PuduLangLog/Expressions/Parser]], [[src/PuduLangLog/Expressions/Syntax]], [[src/PuduLangLog/Expressions/Values]], `Std.List`.
- **Consumed by:** [[src/PuduLangLog/Expressions]].

## Algorithm

1. Doubled braces are literal braces; a hole ends at the brace that closes it, skipping nested brackets and quoted text.
2. A hole's format follows its first top-level `:`.
3. `@l` renders through level monikers and `@m` through the message with JSON-style values; other scalars render under the format and collections as JSON.
4. `#each x, i in source` binds each element and its position, or each member name and value.

## Negative Logic (Prohibited Paths)

- An unclosed hole or block is refused when parsing.

## Edge Cases

- An undefined hole renders nothing; an empty `#each` renders its `#else` part.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/Expressions/TemplateTest`.

## Grill Log

- **Q:** Why parse to nodes once?
  **A:** A sink formats every event; parsing per event would repeat the same work each time. _Rejected:_ interpreting the text on each render.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Lexer]] · [[src/PuduLangLog/Expressions/Parser]] · [[src/PuduLangLog/Expressions/Syntax]] · [[src/PuduLangLog/Expressions/Values]] · [[subsystems/Expressions]]
