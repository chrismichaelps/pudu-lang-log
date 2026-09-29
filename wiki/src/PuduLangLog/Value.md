---
type: module
path: "@root/src/PuduLangLog/Value.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangLog.Value]
---

# PuduLangLog.Value

## Purpose

Builds the [[domain/Value|values]] a program passes to a log call: scalars, sequences,
structures, and dictionaries, and turns any `Capturable` type into one with `Value.of`.

## Interface

### Signatures

```pudu
export trait Capturable {
  fn capture(self: &Self) -> Log.Value
}

export fn of[T: Capturable](held: T) -> Log.Value

export fn text(held: Str) -> Log.Value

export fn int(held: Int) -> Log.Value

export fn float(held: Float64) -> Log.Value

export fn decimal(held: Decimal) -> Log.Value

export fn bool(held: Bool) -> Log.Value

export fn nothing() -> Log.Value

export fn moment(held: Log.Timestamp) -> Log.Value

export fn duration(millis: Int) -> Log.Value

export fn bytes(held: Bytes) -> Log.Value

export fn list(items: Array[Log.Value]) -> Log.Value

export fn structure(tag: Str, members: Array[(Str, Log.Value)]) -> Log.Value

export fn object(members: Array[(Str, Log.Value)]) -> Log.Value

export fn dictionary(entries: Array[(Str, Log.Value)]) -> Log.Value

export fn keyed(entries: Array[(Log.Scalar, Log.Value)]) -> Log.Value

export fn property(name: Str, held: Log.Value) -> Log.Property

export fn render(held: &Log.Value) -> Str

export fn json(held: &Log.Value) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Display]],
  [[src/PuduLangLog/Domain/Json]].
- **Consumed by:** package users, the enrichers, and the request logger.

## Algorithm

1. `Capturable` is implemented for `Log.Value`, `Int`, `Str`, `Bool`, `Float64`, `Char`, `Bytes`, `Log.Timestamp`, and for `Array[T]` and `Option[T]` of any capturable `T`
   (arrays become sequences, `None` becomes null). A program implements it for its own types.
2. `structure` and `object` build tagged and untagged structures from name–value pairs;
   `dictionary` keys by text and `keyed` by any scalar.
3. `render` and `json` show a value as a message or a JSON formatter would.

## Negative Logic (Prohibited Paths)

- No `Capturable` implementation for `Decimal`: the 0.1.2 runtime cannot dispatch a trait method
  on a decimal ([pudu-lang#371](https://github.com/chrismichaelps/pudu-lang/issues/371)), so
  `Value.decimal` builds exact values.
- No builder captures: limits and hints apply only when a value is bound to a hole or a property.

## Edge Cases

- `Value.of(None)` is null whatever the option's type.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/ValueTest`.

## Grill Log

- **Q:** Why a trait rather than reflection?
  **A:** Pudu has no runtime type inspection; a trait lets each type decide which of its fields are
  worth logging, which is also the safe default for secrets. _Rejected:_ `show` for everything
  (loses structure).
- **Q:** Why `nothing()` rather than `null()`?
  **A:** `null` is reserved for the foreign interface. _Rejected:_ `none()` (reads as an option).

## Referenced by

[[domain/Value]] · [[src/PuduLangLog/_MOC]]
