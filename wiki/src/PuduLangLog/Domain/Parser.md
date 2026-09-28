---
type: module
path: "@root/src/PuduLangLog/Domain/Parser.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, pure, deep]
aliases: [PuduLangLog.Domain.Parser, template parser]
---

# PuduLangLog.Domain.Parser

## Purpose

Turns message template text into a [[domain/Template|template]]: literal text and holes, each hole
with its name, capture hint, format, alignment, and position. Output templates use the same
parser.

## Interface

### Signatures

```pudu
export fn parse(text: Str) -> Log.Template

export fn isValidName(name: Str) -> Bool
```

### Linkage

- **Requires:** [[src/PuduLangLog]], `Std.Char`, `Std.List`, `Std.Option`, `Std.Text`.
- **Consumed by:** [[src/PuduLangLog/Domain/Capture]], [[src/PuduLangLog/Logger]], the output and
  JSON formatters, and the request logger.

## Algorithm

1. Walk the characters. Literal text runs to the next single `{`; `{{` and `}}` each add one brace,
   and a single `}` is kept as text.
2. From a `{`, find the next `}`. Without one, the rest of the text is literal. The content between
   them is split: a `:` starts the format (everything after it, colons included); a `,` before any
   `:` starts the alignment, which runs to the `:` or the end.
3. The name may start with `@` (destructure) or `$` (stringify). A name starting with a digit must
   be all digits and gets a position; otherwise it must be identifiers joined by single dots, each
   starting with a letter or `_` and continuing with letters, digits, or `_`.
4. The alignment is digits with an optional leading `-` for left alignment, at most 2147483647.
5. Any rule broken makes the whole `{…}` literal text. An empty format is no format; an empty
   alignment is invalid.
6. `binding` is `Positional` when every hole has a position, `Unbound` without holes, and `Named`
   otherwise.

## Negative Logic (Prohibited Paths)

- Parsing never fails: malformed holes become literal text, and an unclosed `{` keeps the rest of
  the text.
- No hole name contains spaces, punctuation other than single inner dots, or a leading digit mixed
  with letters.

## Edge Cases

- `{{{Name}}}` is `{`, the hole, `}`.
- A number too large for a position is a named hole, so `{99999999999}` binds by name.
- `{Name:u,3}` has the format `u,3` and no alignment.
- Characters beyond ASCII count as letters, so non-English names parse.

## Depth

DEPTH 0.85 (DEEP). A one-function interface over the whole template grammar. Tested by
`test/PuduLangLog/Domain/ParserTest`.

## Grill Log

- **Q:** Why keep malformed holes as text instead of reporting them?
  **A:** A log call must never fail because of its message; the raw text shows the mistake in the
  output where it is noticed. _Rejected:_ a `Result` from `parse`.
- **Q:** Why is a template mixing numbered and named holes `Named`?
  **A:** Binding left to right is the only reading that uses every argument; the binder reports the
  mix to the self-log. _Rejected:_ positional binding of the numbered subset.
- **Q:** Why treat every non-ASCII character as a letter?
  **A:** The standard `Char.isLetter` is ASCII-only, and names in other scripts are legitimate.
  _Rejected:_ ASCII-only names.
- **Q:** Why a separate `isValidName`?
  **A:** Enrichers and `forContext` accept property names that never appear in a template, such
  as `Request Id`; only blank names are refused. _Rejected:_ the template identifier rule
  everywhere.
- **Q:** Why split a hole's content with `breakOn` rather than comparing the positions of `:` and `,`?
  **A:** Cutting at the first `:` and then at the first `,` of what precedes it expresses the rule
  directly; the position comparisons it replaces differed only for an empty name, which is refused
  anyway, so mutation testing could not tell them apart. _Rejected:_ index arithmetic.

## Referenced by

(none)
