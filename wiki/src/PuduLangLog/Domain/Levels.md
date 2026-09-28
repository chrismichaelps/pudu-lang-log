---
type: module
path: "@root/src/PuduLangLog/Domain/Levels.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, pure]
aliases: [PuduLangLog.Domain.Levels]
---

# PuduLangLog.Domain.Levels

## Purpose

Orders the six [[domain/Level|levels]], names them, reads them from text, and renders the short
monikers output templates ask for with formats such as `u3`.

## Interface

### Signatures

```pudu
export fn rank(level: Log.Level) -> Int

export fn passes(level: Log.Level, minimum: Log.Level) -> Bool

export fn stricter(left: Log.Level, right: Log.Level) -> Log.Level

export fn name(level: Log.Level) -> Str

export fn parse(text: Str) -> Option[Log.Level]

export fn moniker(level: Log.Level, format: &Option[Str]) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Padding]], `Std.Char`, `Std.List`,
  `Std.Map`, `Std.Math`.
- **Consumed by:** the level checks of [[src/PuduLangLog/Logger]], the renderers under
  `Domain`, settings, and expressions.

## Algorithm

1. `rank` numbers the levels 0 to 5; `passes` compares ranks.
2. `parse` trims and lower-cases the text and looks it up in a constant table of names and the
   digits 0 to 5.
3. `moniker` with no format gives the full title-case name. A format whose tail after the first
   character is one or two digits takes that many characters from a constant table of
   abbreviations (`Inf`, `Wrn`, `Ftl`…), cased by the first character: `u` upper, `w` lower,
   `t` title. A width past the longest entry gives the full name; a width of zero gives empty text.
4. Any other format falls back to the full name under `Padding.cased`, so `u` and `w` alone upper-
   or lower-case it.

## Negative Logic (Prohibited Paths)

- No moniker is computed by truncating the name: `Dbug`, `Eror`, and `Fatl` are table entries.

## Edge Cases

- `x3` (an unknown case letter with a width) gives the full title-case name.
- `u123` and `u3x` are not widths and give the full name.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/Domain/LevelsTest`.

## Grill Log

- **Q:** Why abbreviations from a table rather than prefixes?
  **A:** Three-letter monikers are read by people scanning columns; `Wrn` and `Ftl` are
  recognisable where `War` and `Fat` are not. _Rejected:_ `take(width)`.
- **Q:** Why accept digits in `parse`?
  **A:** Settings written by other tools store levels as numbers. _Rejected:_ names only.
- **Q:** Why no `Off` level?
  **A:** A source is silenced by a filter, which needs no seventh level that every renderer and
  table must handle. _Rejected:_ a pseudo-level that no event can carry.

## Referenced by

[[src/PuduLangLog/Domain/Output]] · [[src/PuduLangLog/Domain/Padding]]
