---
type: module
path: "@root/src/PuduLangLog/Domain/Dates.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, pure, deep]
aliases: [PuduLangLog.Domain.Dates, date formats]
---

# PuduLangLog.Domain.Dates

## Purpose

Renders a `Timestamp` under the format an output template or hole names, such as
`{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz}`, and a duration (`Span`) under its own formats.

## Interface

### Signatures

```pudu
export fn formatMoment(moment: &Log.Timestamp, format: &Option[Str]) -> Str

export fn roundTrip(moment: &Log.Timestamp) -> Str

export fn formatSpan(millis: Int, format: &Option[Str]) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]], `Std.Map`, `Std.Math`, `Std.Text`, `Std.Time.Format`.
- **Consumed by:** [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Json]], and the
  file sink's rolling names through [[src/PuduLangLog/Domain/Rolling]].

## Algorithm

1. Local fields come from `millis + offset × 60000`, taken apart by `Std.Time.Format.partsOf`.
2. A one-character format that names a standard format is replaced by its pattern from a
   constant table (`o` round trip, `s` sortable, `u` universal, `d`, `D`, `f`, `F`, `g`, `G`,
   `m`, `M`, `r`, `R`, `t`, `T`, `U`, `y`, `Y`); `r`, `R`, `u`, and `U` render UTC. No format
   means `MM/dd/yyyy HH:mm:ss zzz`.
3. A pattern is read left to right in runs of one repeated character: `y` year (two digits for
   one or two), `M` month (number, `MMM` abbreviation, `MMMM` name), `d` day (number, `ddd`
   weekday abbreviation, `dddd` weekday), `H` and `h` hours, `m` minutes, `s` seconds, `f` and
   `F` fraction digits (up to seven; `F` drops trailing zeros), `t` AM/PM, `z` offset (`+2`,
   `+02`, `+02:00`), `K` offset, `g` era. Quoted text and `\x` are literal; `%x` is a single
   specifier; any other character is literal.
4. Durations: `c` (the default) `[-][d.]hh:mm:ss[.fffffff]`, `g` `[-][d:]h:mm:ss[.FFFFFFF]`,
   `G` `[-]d:hh:mm:ss.fffffff`, or a custom pattern of `d`, `h`, `m`, `s`, `f`, `F`.

## Negative Logic (Prohibited Paths)

- No specifier reads the machine's zone; only the event's recorded offset is used.
- A pattern never fails: an unclosed quote runs to the end of the pattern.

## Edge Cases

- A one-letter format is always standard; `%y` and `%f` render a single specifier.
- Fractions beyond milliseconds are zeros, so `fffffff` of 123 ms is `1230000`.
- Midnight is `12 AM` on a twelve-hour clock.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangLog/Domain/DatesTest`.

## Grill Log

- **Q:** Why this pattern language rather than `%Y-%m-%d`?
  **A:** Output templates put the format inside the hole, `{Timestamp:HH:mm:ss}`, where letter
  runs read naturally and need no escape character. _Rejected:_ percent directives, which would
  need a second escape inside templates.
- **Q:** Why does no format show the offset?
  **A:** A timestamp without its offset is ambiguous across machines. _Rejected:_ a local time
  alone.
- **Q:** Why milliseconds only?
  **A:** Pudu clocks report milliseconds; inventing finer digits would be false precision.
  _Rejected:_ microsecond fields.

## Referenced by

[[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/Json]] · [[src/PuduLangLog/Domain/Rolling]]
