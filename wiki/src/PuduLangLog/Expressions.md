---
type: module
path: "@root/src/PuduLangLog/Expressions.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, expressions]
aliases: [PuduLangLog.Expressions]
---

# PuduLangLog.Expressions

## Purpose

Compiles filter expressions and expression templates, and turns them into the shapes the pipeline
accepts: a predicate for filters, conditional sinks, and conditional enrichers; an enricher for a
computed property; and a formatter for sinks.

## Interface

### Signatures

```pudu
export type Compiled = { text: Str, expression: Syntax.Expression }

export fn compile(text: Str) -> Result[Compiled, Str]

export fn evaluate(compiled: &Compiled, event: &Log.Event) -> Option[Log.Value]

export fn predicate(text: Str) -> Result[fn(&Log.Event) -> Bool, Str]

export fn computed(name: Str, text: Str) -> Result[Enricher.Enricher, Str]

export fn template(text: Str) -> Result[Log.Formatter, Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Clock]], [[src/PuduLangLog/Domain/Capture]], [[src/PuduLangLog/Domain/Properties]], [[src/PuduLangLog/Enricher]], [[src/PuduLangLog/Expressions/Evaluator]], [[src/PuduLangLog/Expressions/Functions]], [[src/PuduLangLog/Expressions/Parser]], [[src/PuduLangLog/Expressions/Syntax]], [[src/PuduLangLog/Expressions/Template]], [[src/PuduLangLog/Expressions/Values]].
- **Consumed by:** package users and [[src/PuduLangLog/Settings]] filters.

## Algorithm

1. `compile` parses the text and refuses a call to any function the library does not know, so a typo fails at configuration time rather than evaluating to undefined for every event.
2. `evaluate` runs the tree against one event with the system clock as `Now()`.
3. `predicate` is true only for the boolean `true`; undefined, `null`, and every other value are false.
4. `computed` adds a property holding the expression's value, destructured, unless the event already names it or the value is undefined.
5. `template` parses holes and directives once and records every name the template reads, so `Rest()` can leave those out.

## Negative Logic (Prohibited Paths)

- An expression never raises: every failure while evaluating is an undefined value.
- An unknown function is refused when compiling, never when evaluating.

## Edge Cases

- `computed` of an undefined value adds nothing.
- A template with no holes renders its literal text.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/ExpressionsTest` and `test/PuduLangLog/Expressions/TemplateTest`.

## Grill Log

- **Q:** Why refuse unknown functions at compile time?
  **A:** Every call to an unknown function would be undefined, and a filter built on it would silently keep or drop everything. _Rejected:_ resolving names lazily on first use.

## Referenced by

[[CHANGELOG]] · [[domain/Enrichment]] · [[domain/Filtering]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Functions]] · [[src/PuduLangLog/Expressions/Parser]] · [[src/PuduLangLog/Expressions/Syntax]] · [[src/PuduLangLog/Expressions/Template]] · [[src/PuduLangLog/Expressions/Values]] · [[src/PuduLangLog/Settings]] · [[subsystems/Expressions]] · [[subsystems/Formatting]]
