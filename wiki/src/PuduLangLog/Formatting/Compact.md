---
type: module
path: "@root/src/PuduLangLog/Formatting/Compact.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, formatter]
aliases: [PuduLangLog.Formatting.Compact]
---

# PuduLangLog.Formatting.Compact

## Purpose

[[domain/Formatting|Formatters]] writing each event as one line of compact JSON with `@`-prefixed
reserved members: `compact` keeps the template, `rendered` writes the message and the template's
identifier.

## Interface

### Signatures

```pudu
export fn compact() -> Log.Formatter

export fn rendered() -> Log.Formatter
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Dates]],
  [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/EventId]],
  [[src/PuduLangLog/Domain/Json]], [[src/PuduLangLog/Domain/Levels]].
- **Consumed by:** [[src/PuduLangLog/Sinks/Http]], package users, and settings.

## Algorithm

1. `@t` is the UTC timestamp with seven fraction digits and `Z`.
2. `compact` writes `@mt` and, when holes have formats, `@r`: each formatted hole rendered in
   template order. `rendered` writes `@m` and `@i`, eight hexadecimal digits of
   [[src/PuduLangLog/Domain/EventId]].
3. `@l` appears unless the level is `Information`; `@x`, `@tr`, and `@sp` when present.
4. Properties follow at the top level; a name starting with `@` is written with a second `@` so it
   never collides with a reserved member. Structures carry `$type`.

## Negative Logic (Prohibited Paths)

- No property can overwrite a reserved `@` member.

## Edge Cases

- An `Information` event without properties is only `@t` and `@mt`.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/Formatting/FormattingTest`.

## Grill Log

- **Q:** Why leave out the `Information` level?
  **A:** It is the most common level; omitting it keeps the common line short, and readers treat a
  missing `@l` as `Information`. _Rejected:_ always writing it.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/EventId]] · [[src/PuduLangLog/Settings/Registry]] · [[src/PuduLangLog/Sinks/Http]] · [[subsystems/Formatting]]
