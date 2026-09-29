---
type: module
path: "@root/src/PuduLangLog/Domain/Display.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, pure, deep]
aliases: [PuduLangLog.Domain.Display]
---

# PuduLangLog.Domain.Display

## Purpose

Renders values, messages, and failures as text split into styled pieces. Plain text sinks join
the pieces; the console sink colours each piece by its style.

## Interface

### Signatures

```pudu
export type Style = Plain | Secondary | Tertiary | Invalid | NullStyle | NameStyle | StringStyle | NumberStyle | BooleanStyle | ScalarStyle | LevelStyle(Log.Level)

export type Piece = { style: Style, text: Str }

export const TYPE_TAG: Str = "$type"

export fn plain(pieces: &Array[Piece]) -> Str

export fn scalar(atom: &Log.Scalar, format: &Option[Str]) -> Piece

export fn value(held: &Log.Value, format: &Option[Str]) -> Array[Piece]

export fn json(held: &Log.Value) -> Piece

export fn message(template: &Log.Template, properties: &Array[Log.Property], format: &Option[Str]) -> Array[Piece]

export fn hole(placeholder: &Log.Hole, properties: &Array[Log.Property], literal: Bool, asJson: Bool) -> Array[Piece]

export fn aligned(pieces: &Array[Piece], alignment: &Option[Log.Alignment]) -> Array[Piece]

export fn failure(problem: &Log.Failure) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Dates]],
  [[src/PuduLangLog/Domain/Json]], [[src/PuduLangLog/Domain/Numbers]],
  [[src/PuduLangLog/Domain/Padding]], [[src/PuduLangLog/Domain/Properties]], `Std.Decimal`.
- **Consumed by:** [[src/PuduLangLog/Domain/Output]], [[src/PuduLangLog/Event]], and every
  formatter.

## Algorithm

1. Scalars: `null`; `true` or `false`; numbers through [[src/PuduLangLog/Domain/Numbers]] with the
   hole's format, or their default text; text in double quotes with inner quotes escaped, or
   bare under the format `l`; moments and spans through [[src/PuduLangLog/Domain/Dates]]; bytes as
   hexadecimal.
2. Sequences render as `[a, b]`, passing their format to each element; structures as
   `Tag { Name: value }` (`{ }` when empty); dictionaries as `[("key": value), …]`. Members of
   structures and dictionaries take no format.
3. `message` walks the template: literal tokens as they are; each hole from its property. Under a
   message format containing `l`, text values lose their quotes; containing `j`, values whose hole
   has no format of its own render as JSON with the `$type` tag. A missing property keeps the
   hole's raw text in the `Invalid` style.
4. Alignment pads the rendered hole with a plain piece of spaces.
5. `failure` writes `Kind: message`, each trace line as `   at …`, and each cause after ` ---> `.

## Negative Logic (Prohibited Paths)

- Rendering never fails; an unknown format falls back to the default text.
- Styles never change the text: `plain` of the pieces is the same text a plain sink writes.

## Edge Cases

- A hole's own format wins over `j`, so `{Price:0.00}` stays formatted in a JSON message.
- Alignment counts characters, and a value wider than its width is not cut.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangLog/Domain/DisplayTest`.

## Grill Log

- **Q:** Why styled pieces everywhere instead of a second, themed renderer?
  **A:** One renderer means themed and plain output can never disagree about text. _Rejected:_ a
  console-only copy of the rules.
- **Q:** Why quote text values in messages by default?
  **A:** Quotes show where a value starts and ends and that it is text, not a number or a word of
  the message. `{Name:l}` or the `l` message format drops them. _Rejected:_ bare text by default.
- **Q:** Why `{ }` for an empty structure?
  **A:** It reads as empty; `{  }` reads as a formatting slip. _Rejected:_ two spaces.

## Referenced by

[[domain/Event]] · [[domain/Formatting]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Capture]] · [[src/PuduLangLog/Domain/Clef]] · [[src/PuduLangLog/Domain/Dates]] · [[src/PuduLangLog/Domain/Json]] · [[src/PuduLangLog/Domain/Numbers]] · [[src/PuduLangLog/Domain/Output]] · [[src/PuduLangLog/Domain/Padding]] · [[src/PuduLangLog/Domain/Properties]] · [[src/PuduLangLog/Event]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Template]] · [[src/PuduLangLog/Expressions/Values]] · [[src/PuduLangLog/Failure]] · [[src/PuduLangLog/Formatting/Compact]] · [[src/PuduLangLog/Formatting/Json]] · [[src/PuduLangLog/Formatting/Text]] · [[src/PuduLangLog/Sinks/Map]] · [[src/PuduLangLog/Sinks/Memory]] · [[src/PuduLangLog/Sinks/Theme]] · [[src/PuduLangLog/Value]] · [[subsystems/Formatting]]
