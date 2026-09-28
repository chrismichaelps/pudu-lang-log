---
type: module
path: "@root/src/PuduLangLog/Domain/Capture.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, pure, deep]
aliases: [PuduLangLog.Domain.Capture, binder, destructuring]
---

# PuduLangLog.Domain.Capture

## Purpose

Binds a call's arguments to its template's holes and captures each value under the pipeline's
[[domain/Capture|capture policy]]: the hint of its hole, limits on depth, text length, and
collection size, and the destructuring rules the configuration added. Problems found while
binding are returned as text for the self-log, never raised.

## Interface

### Signatures

```pudu
export type Policy = {
  maximumDepth: Int,
  maximumStringLength: Int,
  maximumCollectionCount: Int,
  scalars: Array[Str],
  transforms: Array[Transform],
  custom: Array[fn(&Log.Value) -> Option[Log.Value]]
}

export type Transform = { tag: Str, apply: fn(Log.Value) -> Log.Value }

export type Bound = { properties: Array[Log.Property], problems: Array[Str] }

export const UNLIMITED: Int = 2147483647

export fn defaults() -> Policy

export fn capture(policy: &Policy, held: &Log.Value, hint: Log.Hint) -> Log.Value

export fn bind(policy: &Policy, template: &Log.Template, arguments: &Array[Log.Value]) -> Bound
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]],
  [[src/PuduLangLog/Domain/Json]], [[src/PuduLangLog/Domain/Properties]], `Std.List`,
  `Std.Math`, `Std.Option`.
- **Consumed by:** [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Enricher]],
  [[src/PuduLangLog/Configuration]], and the request logger.

## Algorithm

1. A value at depth greater than `maximumDepth` (the top value is depth 1) becomes `null`.
2. `Stringify` renders the value to text (text stays as it is; anything else renders as displayed
   with the `l` format) and cuts it to length.
3. Scalars keep their value; text longer than `maximumStringLength` keeps
   `maximumStringLength - 1` characters and gains `…`; byte strings over 1024 bytes become the
   hexadecimal of their first 16 bytes followed by `... (N bytes)`.
4. Sequences and dictionaries keep their first `maximumCollectionCount` members, each captured one
   level deeper under the same hint.
5. A structure renders to text unless the hint is `Destructure` and its tag is not listed as a
   scalar. Destructured, the first custom rule that answers a replacement wins, then a transform
   registered for its tag. A replacement structure is not replaced again: its members are
   captured one level deeper, as an unreplaced structure's are. A replacement of another kind is
   captured in the structure's place.
6. `bind`:
   - No arguments: no properties, and a problem when the template has holes.
   - `Unbound` template with arguments: no properties and a problem.
   - `Positional`: each numbered hole takes the argument at its number; a number past the
     arguments is a problem; properties come out in argument order, and unused arguments are
     dropped with a count problem.
   - `Named`: holes take arguments left to right, a repeated name keeps its last value, and
     arguments beyond the holes are kept under `__<index>`; any count mismatch is a problem, and
     so is a numbered hole in a named template.

## Negative Logic (Prohibited Paths)

- Binding never fails a call: every mismatch is a problem string and the event is still written.
- No value escapes the limits, however deeply it is nested.

## Edge Cases

- A template repeating one numbered hole binds it once.
- Transforms apply only under `@`; a plain hole renders the original structure to text.

## Depth

DEPTH 0.85 (DEEP). Tested by `test/PuduLangLog/Domain/CaptureTest`.

## Grill Log

- **Q:** Why render structures to text unless `@` is used?
  **A:** A structure is often a large record whose shape nobody asked to keep; `@` is the explicit
  request to store it as data. Collections stay collections because their elements are the
  interesting part. _Rejected:_ destructuring everything.
- **Q:** Why return problems instead of writing to the self-log here?
  **A:** `Domain` performs no effects; the logger decides where problems go.
  _Rejected:_ a self-log parameter.
- **Q:** Why keep unmatched arguments as `__0`, `__1`?
  **A:** Dropping them loses data the caller passed; the index names make the mismatch visible in
  structured output. _Rejected:_ discarding extras.
- **Q:** Why is a replacement never replaced again?
  **A:** A transform that keeps the tag (a card with its number masked is still a card) would
  otherwise apply to its own answer until the depth limit turned it into `null`. _Rejected:_
  requiring every transform to change the tag.
- **Q:** Why do custom rules come before tag transforms?
  **A:** A custom rule can match on anything, including several tags; it is the more deliberate
  choice. _Rejected:_ transforms first.

## Referenced by

[[src/PuduLangLog/Domain/Parser]] · [[src/PuduLangLog/Domain/Properties]]
