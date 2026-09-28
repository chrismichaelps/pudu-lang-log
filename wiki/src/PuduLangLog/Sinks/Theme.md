---
type: module
path: "@root/src/PuduLangLog/Sinks/Theme.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: SHALLOW
tags: [module, sink]
aliases: [PuduLangLog.Sinks.Theme]
---

# PuduLangLog.Sinks.Theme

## Purpose

Terminal colour themes for the [[src/PuduLangLog/Sinks/Console|console sink]]: the escape sequence
each [[src/PuduLangLog/Domain/Display|display style]] starts with, and the painting of styled
pieces.

## Interface

### Signatures

```pudu
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]].
- **Consumed by:** [[src/PuduLangLog/Sinks/Console]] and settings.

## Algorithm

1. A theme holds one escape sequence per style and level: `literate` (256 colours, the default),
   `grayscale`, `code`, `sixteen` (basic colours), and `none`.
2. `paint` wraps each non-empty styled piece in its sequence and a reset, line by line, so a line
   break never carries colour onto the next line.

## Negative Logic (Prohibited Paths)

- A style with an empty sequence writes no escape and no reset.

## Edge Cases

- Painting with `none` gives exactly the plain text.

## Depth

DEPTH 0.4 (SHALLOW). Tested by `test/PuduLangLog/Sinks/ConsoleTest`.

## Grill Log

- **Q:** Why reset after every piece?
  **A:** A piece's colour must not leak into the next piece or into whatever the terminal prints
  after the program. _Rejected:_ resetting once per line.

## Referenced by

(none)
