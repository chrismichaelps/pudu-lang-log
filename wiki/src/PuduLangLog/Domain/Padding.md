---
type: module
path: "@root/src/PuduLangLog/Domain/Padding.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: SHALLOW
tags: [module, leaf, pure]
aliases: [PuduLangLog.Domain.Padding]
---

# PuduLangLog.Domain.Padding

## Purpose

Applies a hole's alignment and the `u` / `w` case formats that text renderers share.

## Interface

### Signatures

```pudu
export fn padded(text: Str, alignment: &Option[Log.Alignment]) -> Str

export fn cased(text: Str, format: &Option[Str]) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]].
- **Consumed by:** [[src/PuduLangLog/Domain/Levels]], [[src/PuduLangLog/Domain/Display]].

## Algorithm

1. `padded` measures the text in characters; when it is shorter than the width, spaces make up
   the difference on the right of a left-aligned value and on the left otherwise.
2. `cased` upper-cases for `u`, lower-cases for `w`, and returns other text unchanged.

## Negative Logic (Prohibited Paths)

- Text is never truncated to fit a width.

## Edge Cases

- A width of zero or less, or text already as wide, returns the text unchanged.

## Depth

DEPTH 0.4 (SHALLOW). A leaf shared by the level and value renderers. Tested by
`test/PuduLangLog/Domain/LevelsTest`.

## Grill Log

- **Q:** Why a module of two functions?
  **A:** Level monikers and value rendering both need them, and neither owns the other.
  _Rejected:_ a copy in each renderer.
- **Q:** Why `Math.max(0, …)` rather than an early return for text already wide enough?
  **A:** Padding by zero spaces returns the same text, so the `<=` against `<` choice in such a
  test could never change the answer; mutation testing showed it. _Rejected:_ the extra branch.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/Levels]] · [[src/PuduLangLog/Domain/Output]]
