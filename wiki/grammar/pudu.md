---
type: grammar
language: Pudu
version: "0.1.2"
tags: [grammar]
aliases: [Grammar — Pudu, Pudu Grammar]
---

# Grammar — Pudu

The Pudu surface this repository is written against, pinned to compiler `0.1.2` as published in
its release archive. Where this page and the compiler disagree, the compiler wins and this page is
corrected in the same change.

## SDK Discovery Map

| Need | Module | Entry points |
| --- | --- | --- |
| Console output | `Std.Io` | `writeLine`, `writeErrorLine` |
| Files | `Std.Io` | `append`, `read`, `write`, `exists`, `remove`, `list`, `makeDirectory`, `move`, `directoryOf`, `nameOf`, `join` |
| Threads | `Std.Concurrent` | `start`, `join`, `sleep` |
| Shared state | `Std.Sync` | `mutex`, `withLock`, `cell`, `get`, `set`, `swap` |
| Queues | `Std.Channel` | `channel`, `send`, `receive`, `pending`, `close` |
| Time | `Std.Time` | `currentInstant`, `localOffsetMinutes`, `elapsed` |
| Calendar arithmetic | `Std.Time.Format` | `partsOf`, `millisOf`, `weekdayOf`, `daysFromCivil` |
| Exact numbers | `Std.Decimal` | `fromInt`, `round`, `rescale`, `toText`, `scale` |
| JSON documents | `Std.Json` | `decode`, `field`, `asText`, `asInt`, `asBool`, `asList`, `asObject` |
| RFC 3339 moments | `Std.Time.Format` | `fromRfc3339` |
| Regular expressions | `Std.Regex` | `compile`, `find`, `replaceAll`, `explain` |
| Secure random bytes | `Std.Random` | `secureBytes` |
| Standard library logging | `Std.Log`, `Std.Out` | `Logger`, `Line`, `Level`; `Out.bare` |
| Environment | `Std.Env` | `variable`, `variableOr`, `at` |
| Processes | `Std.Process` | `output` |
| HTTP | `Std.Http`, `Std.Http.Client`, `Std.Http.Server.Route` | `Request`, `Response`, `send`, `Middleware` |
| Collections | `Std.List`, `Std.Map` | `List.get`, `List.first`, `List.find`; `Map.get`, `Map.insert` |
| Tests | `Std.Test` | `suite`, `equals`, `that`, `run`, `failuresOf`, `report` |

## Imports / Namespaces

- One module per file; the module name is the path under its source root with `/` as `.`:
  `src/PuduLangLog/Domain/Parser.pudu` is `module PuduLangLog.Domain.Parser`.
- Every import is qualified and aliased: `import Std.Sync as Sync`. Nothing is imported implicitly.
- A trait's methods are callable on a value wherever its module is imported; the trait itself is
  not imported.
- Suites under `test/` and programs under `examples/` import package modules through the
  manifest's source root.

## Core Primitives

- Records: `export type Property = { name: Str, value: Value }`, built as
  `Property{name: "Id", value: v}`, updated as `Property{..p, name: "Other"}`.
- Sum types may be recursive through arrays and records: `Value` holds `Array[Value]` and
  `Structure`, whose `Property` holds a `Value` again.
- A variant may share its name with a type: `Scalar(Scalar)` is the `Value` variant holding a
  `Scalar`.
- A function stored in a record field is called as `(record.field)(argument)`.
- A trait may be implemented for built-in types (`impl Capturable for Int`), and a generic
  function may require it: `fn of[T: Capturable](held: T) -> Value`.
- `show(value)` and `==` work on values of any type.
- `Option[T]` and `Result[T, E]` helpers are module functions (`Option.unwrapOr(value, fallback)`).
- Module scope holds only `const`. Lookup tables are `const` arrays or maps built with
  `mapOf([...])`. A `const` cannot call a function, so shared state is created by a function and
  passed on.
- Closures: `fn(x: Int) -> Int { x + 1 }`, or the short form `|x: Int| x + 1`. A closure captures
  a copy of every binding it names; state that later calls must observe lives in a `Sync.Cell`.
- Borrowing: `&T` parameters are read-only views; `*view` copies a borrowed value into an owned one.

## Numbers

- `Int` arithmetic is checked: an addition or multiplication past the 64-bit range stops the
  program. Hashes reduce every intermediate result with `%` so it stays in range.
- There is no built-in conversion between `Int` and `Float64`. A float is read as a `Decimal`
  through its text: `show(f).toDecimal()`.
- `show` renders a float in the shortest form that reads back, switching to an exponent for large
  and small magnitudes (`1.0e30`, `1.0e-7`); display formatting goes through `Decimal` instead.
- Infinity and NaN have no `Decimal`; their text is `Infinity` and `NaN`.

## Text

- String indices and lengths count characters, not bytes: `"héllo".length()` is 5.
- `slice`, `take`, `drop`, `indexOf`, `split`, `replace`, `chars`, `toUpper`, `toLower`, `trim`,
  `startsWith`, `endsWith`, `contains` are methods on `Str`.
- Text is assembled as an array of pieces joined once at the end, not by repeated `+` in a loop.

## Architectural Laws

- Dependency direction is inward: public modules under `PuduLangLog.*` use
  `PuduLangLog.Domain.*`, which uses only the root vocabulary `PuduLangLog`,
  `PuduLangLog.Constants.*`, and pure standard modules. `Domain` never imports a public module and
  performs no effects.
- Only sinks and the few modules that read the environment (the clock, the environment enricher,
  settings that read files) touch the outside world.
- Logging never fails its caller: a problem inside the pipeline is reported to the self-log and
  the event continues to every other sink. Audit sinks are the one exception, and they report
  through `Logger.tryWrite`.
- Every module the package ships is `PuduLangLog` or lives under `src/PuduLangLog/`.

## Syntax Rules / Naming

- Types, traits, modules, and variants are `PascalCase`; values `camelCase`; constants
  `UPPER_SNAKE_CASE`.
- Every file header and exported type carries the FMCF anchor, one line:
  `/** @Namespace.Entity.Role — intent */`, five to eight words of intent.
- Every `fn`, `export fn`, and `const` carries a `///` doc comment of one or two lines stating
  what it answers or holds, in the voice of the standard library's own documentation. It states
  the contract, not the steps.
- Rationale belongs in the mirrored page's Grill Log. No narration, history, or explanation of the
  obvious in code.

## Prohibited Patterns (verified against the 0.1.2 compiler)

- **A brace inside a string literal is interpolation.** A literal brace is `\{` or `\}`; message
  templates in source and tests escape every brace.
- **`Array.get(i)` and `items[i]` stop the program when `i` is out of range.** Use `List.get` or
  `List.first` for an `Option`.
- **`scope`, `module`, `where`, and `task` are keywords**; none can name a binding or a field.
- **`as` is not an expression operator.** An empty array gets its type from a typed binding:
  `let none: Array[Str] = []`.
- **Matching a borrowed value binds its parts as owned values.**
- **A unit value is matched as `Ok(_)`, not `Ok(())`.**
- **A comparison that picks the smaller or larger of two values is `Math.min` or `Math.max`**, not
  an `if`: the `>` against `>=` choice there cannot change the answer, and mutation testing reports
  it as a survivor.
- **`&-1` lexes as the operator `&-`**; borrow a negative literal through a named binding.
- **A write that depends on a read of a `Sync.Cell` without the cell's mutex** loses updates
  under concurrent use.
- **An `if` ladder or `match` over strings used as a lookup table**; it is a `const` map or array.
- **`null`, `with`, and `as` are reserved**; none can name a function or a binding.
- **A `return` inside a match arm needs braces**: `case None => { return x }`.
- **Tuple elements are read by index, `pair[0]`, or by destructuring**; `pair.0` does not parse.
- **A `const` can name only its own module's constructors**; a list of another module's variants
  lives in that module ([pudu-lang#373](https://github.com/chrismichaelps/pudu-lang/issues/373)).
- **A record field naming a function alias declared later in its module** refuses that alias in
  importing modules; aliases are declared before the records that use them
  ([pudu-lang#372](https://github.com/chrismichaelps/pudu-lang/issues/372)).
- **A trait implemented for `Decimal`** type-checks but its method cannot be dispatched at run
  time; decimal values are built by a plain function
  ([pudu-lang#371](https://github.com/chrismichaelps/pudu-lang/issues/371)).

- **There is no `??` operator.** An absent option ends a function through
  `match … { case Some(found) => found case None => { return … } }`.
- **Decimals inside options and records compare by representation**: `0.25d == 0.2500d` is true
  but `Some(0.25d) == Some(0.2500d)` is false. Computed decimals are stored at their smallest scale
  ([pudu-lang#376](https://github.com/chrismichaelps/pudu-lang/issues/376)).
- **A tuple match of two options is not seen as exhaustive**; its last arm is `case _`
  ([pudu-lang#377](https://github.com/chrismichaelps/pudu-lang/issues/377)).
- **A program importing `Std.Json` misjudges `Some(...)` arms naming constructors that share
  `Json`'s names** (`Null`, `Boolean`, `Text`) in another module; such matches take the option
  apart first and match the inner value in its own function
  ([pudu-lang#378](https://github.com/chrismichaelps/pudu-lang/issues/378)).

## Senior Definition Needed

(none open)

## Referenced by

[[00-INDEX]] · [[architecture/_MOC]] · [[src/PuduLangLog]] · [[src/PuduLangLog/Bridge]] · [[src/PuduLangLog/Clock]] · [[src/PuduLangLog/Configuration]] · [[src/PuduLangLog/Constants/Names]] · [[src/PuduLangLog/Context]] · [[src/PuduLangLog/Domain/Capture]] · [[src/PuduLangLog/Domain/Clef]] · [[src/PuduLangLog/Domain/Dates]] · [[src/PuduLangLog/Domain/Display]] · [[src/PuduLangLog/Domain/EventId]] · [[src/PuduLangLog/Domain/Json]] · [[src/PuduLangLog/Domain/Levels]] · [[src/PuduLangLog/Domain/Masking]] · [[src/PuduLangLog/Domain/Numbers]] · [[src/PuduLangLog/Domain/Output]] · [[src/PuduLangLog/Domain/Padding]] · [[src/PuduLangLog/Domain/Parser]] · [[src/PuduLangLog/Domain/Properties]] · [[src/PuduLangLog/Domain/Recency]] · [[src/PuduLangLog/Domain/Rolling]] · [[src/PuduLangLog/Domain/Schedule]] · [[src/PuduLangLog/Domain/Settings]] · [[src/PuduLangLog/Domain/Sources]] · [[src/PuduLangLog/Enricher]] · [[src/PuduLangLog/Enrichers/Environment]] · [[src/PuduLangLog/Enrichers/Masking]] · [[src/PuduLangLog/Event]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Expressions/Evaluator]] · [[src/PuduLangLog/Expressions/Functions]] · [[src/PuduLangLog/Expressions/Lexer]] · [[src/PuduLangLog/Expressions/Parser]] · [[src/PuduLangLog/Expressions/Syntax]] · [[src/PuduLangLog/Expressions/Template]] · [[src/PuduLangLog/Expressions/Values]] · [[src/PuduLangLog/Failure]] · [[src/PuduLangLog/Filter]] · [[src/PuduLangLog/Formatting/Compact]] · [[src/PuduLangLog/Formatting/Json]] · [[src/PuduLangLog/Formatting/Reader]] · [[src/PuduLangLog/Formatting/Text]] · [[src/PuduLangLog/LevelSwitch]] · [[src/PuduLangLog/Logger]] · [[src/PuduLangLog/Pipeline]] · [[src/PuduLangLog/SelfLog]] · [[src/PuduLangLog/Settings]] · [[src/PuduLangLog/Settings/Registry]] · [[src/PuduLangLog/Sink]] · [[src/PuduLangLog/Sinks/Async]] · [[src/PuduLangLog/Sinks/Batching]] · [[src/PuduLangLog/Sinks/Console]] · [[src/PuduLangLog/Sinks/File]] · [[src/PuduLangLog/Sinks/Http]] · [[src/PuduLangLog/Sinks/Map]] · [[src/PuduLangLog/Sinks/Memory]] · [[src/PuduLangLog/Sinks/Observable]] · [[src/PuduLangLog/Sinks/Theme]] · [[src/PuduLangLog/Timing]] · [[src/PuduLangLog/Value]] · [[src/PuduLangLog/Web/Correlation]] · [[src/PuduLangLog/Web/Diagnostic]] · [[src/PuduLangLog/Web/RequestLogging]] · [[tools/Mutate]]
