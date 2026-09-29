---
type: module
path: "@root/src/PuduLangLog/Expressions/Functions.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, expressions]
aliases: [PuduLangLog.Expressions.Functions]
---

# PuduLangLog.Expressions.Functions

## Purpose

The built-in functions: text search and slicing, regular expressions, `Length`, `ElementAt`, `TypeOf`, `TagOf`, `ToString`, `Round`, `Coalesce`, `IsDefined`, `Undefined`, `Now`, `UtcDateTime`, `Nest`, and `Inspect`.

## Interface

### Signatures

```pudu
export fn isKnown(name: Str) -> Bool

export fn call(name: Str, arguments: &Array[Option[Log.Value]], ignoring: Bool, now: &Log.Timestamp) -> Option[Log.Value]

export fn elementAt(target: &Log.Value, key: &Log.Value, ignoring: Bool) -> Option[Log.Value]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Properties]], [[src/PuduLangLog/Expressions/Values]], `Std.Decimal`, `Std.List`, `Std.Math`, `Std.Option`, `Std.Regex`, `Std.Text`.
- **Consumed by:** [[src/PuduLangLog/Expressions/Evaluator]] and [[src/PuduLangLog/Expressions]].

## Algorithm

1. Names are matched in lower case; `isKnown` answers for compile-time checks.
2. An undefined argument makes the call undefined, except for `Coalesce`, `IsDefined`, `TypeOf`, `Undefined`, and `Now`.
3. `ci` lowers both sides of text comparisons and lowers a pattern's literal letters while keeping its escapes.
4. `TypeOf` names scalars by their Pudu type and collections as `array`, `object`, or `dictionary`; an undefined argument is `undefined`.

## Negative Logic (Prohibited Paths)

- A regular expression that does not compile is undefined, never an error.

## Edge Cases

- `Substring` with a start past the end is undefined.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/ExpressionsTest`.

## Grill Log

- **Q:** Why does `TypeOf` accept undefined?
  **A:** Asking whether something is missing is the question most filters on optional properties ask. _Rejected:_ undefined in, undefined out, which makes the check impossible.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Values]] · [[subsystems/Expressions]]
