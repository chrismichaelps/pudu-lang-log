---
type: module
path: "@root/src/PuduLangLog/Domain/Rolling.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, pure, deep]
aliases: [PuduLangLog.Domain.Rolling]
---

# PuduLangLog.Domain.Rolling

## Purpose

The arithmetic of [[domain/Rolling|rolling files]]: which period a moment falls in, when the next
one starts, what the period's file is called, how existing names read back, and which old files
retention removes. The file sink performs the effects; this module decides them.

## Interface

### Signatures

```pudu
export type Rolled = { name: Str, period: Option[Int], sequence: Option[Int] }

export fn periodFormat(interval: Log.RollingInterval) -> Str

export fn checkpoint(interval: Log.RollingInterval, moment: &Log.Timestamp) -> Option[Int]

export fn next(interval: Log.RollingInterval, moment: &Log.Timestamp) -> Option[Int]

export fn fileName(path: Str, interval: Log.RollingInterval, moment: &Log.Timestamp, sequence: Option[Int]) -> Str

export fn matches(path: Str, interval: Log.RollingInterval, name: Str) -> Option[Rolled]

export fn latestSequence(files: &Array[Rolled], period: Option[Int]) -> Option[Int]

export fn retired(files: &Array[Rolled], current: Str, count: Option[Int], oldest: Option[Int]) -> Array[Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Dates]], `Std.Char`, `Std.List`,
  `Std.Math`, `Std.Option`, `Std.Path`, `Std.Text`, `Std.Time.Format`.
- **Consumed by:** [[src/PuduLangLog/Sinks/File]].

## Algorithm

1. Periods are computed on the local clock (`millis + offset × 60000`): the checkpoint zeroes the
   fields below the interval, and the next checkpoint adds one unit, carrying across months and
   years.
2. A file name inserts the checkpoint's digits (`yyyy`, `yyyyMM`, `yyyyMMdd`, `yyyyMMddHH`, or
   `yyyyMMddHHmm`) and an optional `_NNN` sequence between the path's stem and its extension.
3. `matches` reads a name back: the stem, exactly as many digits as the period format, an optional
   `_` with at least three digits, and the extension. Digits that form no date give no period.
4. `retired` sorts every file except the current one newest first (period, then sequence, then
   name; a missing value is oldest) and removes, from the first file that fails, every file after it: a
   file fails when its index reaches `count - 1` (the current file is the count's first member)
   or its period started before `oldest`.

## Negative Logic (Prohibited Paths)

- The current file is never retired, whatever its case on disk.
- No name outside the stem-period-sequence-extension shape is ever selected for deletion.

## Edge Cases

- `Infinite` has no period; its size-rolled files read back with a sequence and no period.
- A period starting exactly at the age limit is kept.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangLog/Domain/RollingTest`.

## Grill Log

- **Q:** Why roll on the local clock?
  **A:** People look for "today's file" by their own calendar. The offset is the one recorded on
  each event, so a program keeps one zone throughout. _Rejected:_ UTC periods.
- **Q:** Why is there no separate month range check when reading a period back?
  **A:** `Std.Time.Format.daysInMonth` answers zero for a month outside 1 to 12, so the day check
  already refuses such a month; a second check could never change the answer. _Rejected:_ the
  redundant comparison, which mutation testing showed to be unobservable.
- **Q:** Why order files by a text key rather than a comparison?
  **A:** A directory listing has no guaranteed order, so ties must break the same way every time;
  period, sequence, then name, as zero-padded text, gives one total order without a hand-written
  comparator. _Rejected:_ a comparator that left equal periods and sequences in listing order.
- **Q:** Why delete every file after the first one that fails retention?
  **A:** Files are sorted newest first, so once one is too old or past the count, every later one
  is too. _Rejected:_ checking each file independently, which could keep an older file than one
  removed.

## Referenced by

[[src/PuduLangLog/Domain/Dates]]
