---
type: subsystem
tags: [subsystem]
---

# Expressions

A small language over events: `@l = 'Error' and RequestPath like '/api/%'`,
`Items[?].Price > 100`, `StartsWith(SourceContext, 'App.') ci`. The
[[src/PuduLangLog/Expressions/Lexer]] and [[src/PuduLangLog/Expressions/Parser]] build a
[[src/PuduLangLog/Expressions/Syntax]] tree; the [[src/PuduLangLog/Expressions/Evaluator]] runs it
with the [[src/PuduLangLog/Expressions/Functions]] and the value rules of
[[src/PuduLangLog/Expressions/Values]]; [[src/PuduLangLog/Expressions/Template]] renders expression
templates. [[src/PuduLangLog/Expressions]] is the facade: predicates, computed properties, and
formatters.

## Referenced by

[[architecture/LANGUAGE]] · [[subsystems/_MOC]]
