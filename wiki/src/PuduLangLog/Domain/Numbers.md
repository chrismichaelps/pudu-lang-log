---
type: module
path: "@root/src/PuduLangLog/Domain/Numbers.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, pure, deep]
aliases: [PuduLangLog.Domain.Numbers, number formats]
---

# PuduLangLog.Domain.Numbers

## Purpose

Renders integers, exact decimals, and floats under the format a hole or output template names,
such as `{Elapsed:0.000}`, `{Count:N0}`, or `{Id:X8}`, and gives floats their shortest
round-trip text.

## Interface

### Signatures

```pudu
export type Digits = { negative: Bool, digits: Str, exponent: Int }

export fn digitsOf(value: Decimal) -> Digits

export fn digitsOfReal(value: Float64) -> Option[Digits]

export fn realText(value: Float64) -> Str

export fn formatInteger(value: Int, format: Str) -> Option[Str]

export fn formatExact(value: Decimal, format: Str) -> Option[Str]

export fn formatReal(value: Float64, format: Str) -> Option[Str]

export fn fixed(number: &Digits, places: Int) -> (Str, Str)
```

### Linkage

- **Requires:** `Std.Char`, `Std.Decimal`, `Std.List`, `Std.Math`, `Std.Option`, `Std.Text`.
- **Consumed by:** [[src/PuduLangLog/Domain/Display]], [[src/PuduLangLog/Domain/Json]].

## Algorithm

1. Every number becomes `Digits`: a sign, its significant digits, and the decimal exponent of the
   point. A float goes through its shortest text (`show`), so no binary noise is added. Rounding
   is done on the digit string, half away from zero.
2. A standard format is one letter and up to two digits of precision:

   | Letter | Meaning | Default precision |
   | --- | --- | --- |
   | `C` | `¤` currency with thousands | 2 |
   | `D` | integer digits padded with zeros (integers only) | none |
   | `E` | `d.dddE+ddd` | 6 |
   | `F` | fixed decimals | 2 |
   | `G` | general: shortest text, or that many significant digits | shortest |
   | `N` | fixed decimals with thousands | 2 |
   | `P` | times one hundred, fixed, with ` %` | 2 |
   | `R` | shortest round-trip text | — |
   | `X` | hexadecimal, case from the letter (integers only) | none |

3. Anything else is a custom format of `0` (digit or zero), `#` (digit if significant), `.`, `,`
   (thousands), `%` (times one hundred), quoted or escaped literals, and up to three
   `;`-separated sections for positive, negative, and zero values. Integer digits are laid right
   to left over the integer placeholders, the first taking any excess, so `000-00-0000` spreads
   digits around its literals.
4. A float's default text is fixed for decimal exponents from -4 to 14 and `1E+30` style outside,
   keeping every digit of the shortest text.

## Negative Logic (Prohibited Paths)

- No format ever fails a log call: an unknown standard letter answers `None`, and the caller
  renders the default text.
- Negative zero never shows a sign; `-0.001` under `F2` is `0.00`.
- No intermediate value leaves `Int` range; hexadecimal digits of negative numbers are taken one
  nibble at a time.

## Edge Cases

- `D` and `X` apply to integers only; on a float or decimal they answer `None`.
- A negative integer under `X` is its 64-bit two's complement.
- Infinities and NaN render as `Infinity`, `-Infinity`, and `NaN` under every format.
- A format without placeholders is literal text; one without integer placeholders puts the
  integer digits before the point (`.00` renders 5.5 as `5.50` and 0.5 as `.50`).

## Depth

DEPTH 0.85 (DEEP). Three entry points over a small numeric language. Tested by
`test/PuduLangLog/Domain/NumbersTest`.

## Grill Log

- **Q:** Why digit strings instead of float arithmetic?
  **A:** Formatting must round what a person reads: 2.345 to two places is 2.35. Floats would
  round their binary value, and `Int` checks overflow. _Rejected:_ `Float64` scaling.
- **Q:** Why the invariant `¤` for currency?
  **A:** A logger has no locale; the generic currency sign says what the number is without
  guessing a country. _Rejected:_ `$`.
- **Q:** Why `Infinity` rather than `∞`?
  **A:** It is the text Pudu itself shows and reads back, and it survives every terminal and file
  encoding. _Rejected:_ the symbol.
- **Q:** Why is a thousands comma that ends the integer part not a scaling divisor?
  **A:** Scaling by a thousand per trailing comma is rarely used and easily mistaken; the comma
  always means grouping here. _Rejected:_ implementing scaling.
- **Q:** Why is zero built as `digitsOf(Decimal.zero())` wherever rounding empties the digits?
  **A:** One canonical zero, whose sign is never negative; spelling the record out in each place
  gave a sign that no caller reads, which mutation testing reported. _Rejected:_ a record literal
  per site.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/Json]] · [[subsystems/Formatting]]
