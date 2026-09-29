<p align="center">
  <img src="public/pudu-lang-short.png" alt="Pudu" width="120">
</p>

<p align="center">
  <a href="https://www.pudu-lang.org/">Pudu</a> |
  <a href="https://www.pudu-lang.org/docs">Documentation</a> |
  <a href="https://www.pudu-lang.org/packages">Packages</a> |
  <a href="https://github.com/chrismichaelps/pudu-lang-log/wiki">API docs</a> |
  <a href="CONTRIBUTING.md">Contributing</a>
</p>

# pudu-lang-log

Structured event logging for Pudu. A program writes events through message templates such as
`"Disk {Disk} is {Percent}% full"`; the logger keeps every property as data, enriches and filters
the event, and hands it to sinks that write text or JSON to the console, files, the network, memory,
or another logger. Writing never fails the program: problems inside the pipeline go to a self-log.

```pudu
import PuduLangLog.Configuration as Configuration
import PuduLangLog as Log
import PuduLangLog.Logger as Logger
import PuduLangLog.Sinks.Console as Console
import PuduLangLog.Sinks.File as File
import PuduLangLog.Value as Value

fn main() -> Int {
  let logger = Configuration.create()
    .minimumLevel(Log.Debug)
    .enrichWithProperty("Application", Value.text("Shop"))
    .writeTo(Console.sink(Console.defaults()))
    .writeTo(File.sink(File.Options{..File.defaults("logs/shop.txt"), rollingInterval: Log.Day}))
    .createLogger()
  logger.information("Order \{OrderId\} placed by \{@Customer\}", [Value.int(42), Value.structure("Customer", [("Name", Value.text("Ada"))])])
  logger.forSource("Shop.Payments").warning("Payment took \{Elapsed:0.0\} ms", [Value.float(1234.5)])
  Logger.close(&logger)
  0
}
```

## Installing

```bash
pudu install @chrismichaelps/pudu-lang-log
```

It needs [Pudu 0.1.2 or later](https://www.pudu-lang.org/download). Every module the package ships
is `PuduLangLog` or under it, so it takes no module name from the program that installs it.

## Concepts

| Concept | Module | What it is |
| --- | --- | --- |
| Event | `PuduLangLog`, `Event` | A timestamp, a level, the parsed message template, captured properties, and an optional failure, trace, and span. |
| Level | `PuduLangLog`, `LevelSwitch` | `Verbose`, `Debug`, `Information`, `Warning`, `Error`, `Fatal`; a switch changes the minimum while running and tells listeners. |
| Value | `Value` | What a property holds: scalars, sequences, structures with a tag, and dictionaries. `Value.of` captures any `Capturable` type. |
| Logger | `Logger` | `verbose` through `fatal`, `write`, `writeFailure`, `tryWrite`, `emit`; `forContext`, `forSource`, `forEnricher`, `withTrace`; `flush`, `close`, `reload`. |
| Configuration | `Configuration` | One decision per call: levels, overrides, sinks, audit sinks, enrichers, filters, destructuring, capture limits, self-log, clock. |
| Settings | `Settings`, `Settings.Registry` | The same decisions read from key-value pairs, JSON, or environment variables. |
| Context | `Context` | Properties pushed for a stretch of work and added to every event written meanwhile. |
| Failure | `Failure` | The error attached to an event: kind, message, trace lines, and causes. |
| Self-log | `SelfLog` | Where the pipeline reports its own problems. |

## Message templates

In Pudu source a brace inside a string starts interpolation, so templates escape theirs:
`"Hello \{User\}"`. Holes are named, `{User}`, or numbered, `{0}`. `{@Order}` keeps a structure's members,
`{$Order}` renders it to text, `{Name,10}` and `{Name,-10}` align, and `{Total:0.00}` formats.
Doubled braces are literal. Templates are parsed once and kept; the template's text identifies the
kind of event, and its hash is a stable event identifier.

## Enrichment, filtering, and levels

- `enrichWithProperty`, `enrichWith(Enricher.computed(…))`, and `Enricher.when`, `atLevel`, and
  `atSwitch`; `Context.enricher` adds pushed properties.
- `Enrichers.Environment`: `machineName`, `environmentUserName`, `environmentName`, `processId`,
  `processName`, `environmentVariable`, and `failureDetail`.
- `Enrichers.Masking`: `properties` hides listed properties and members at any depth; `patterns`
  and `sensitive` hide e-mail addresses, card numbers, and account numbers in text and failures.
- `filterExcluding`, `filterIncludingOnly`, and `Filter.fromSource`, `withProperty`,
  `withPropertyValue`, `atLeast`, `withFailure`, `allOf`, `anyOf`, `not`.
- `overrideLevel` and `overrideSwitch` give a source prefix its own minimum; the most specific
  prefix decides.

## Capture and destructuring

Values are captured when an event is written, so sinks never see program data. The capture policy
limits depth (`maximumDepth`, 10 by default), text length (`maximumStringLength`), and collection
size (`maximumCollectionCount`). `destructureByTransforming`, `destructureAsScalar`,
`destructureWithRule`, `destructureByIgnoring`, and `destructureByMasking` shape structures of a tag.

## Sinks

| Sink | Module | What it does |
| --- | --- | --- |
| Console | `Sinks.Console`, `Sinks.Theme` | Output templates, themes (`literate`, `grayscale`, `code`, `sixteen`, `none`), errors from a level to the error stream. |
| File | `Sinks.File` | Rolling by interval and size, retention by count and age, buffering, and hooks when files open or are deleted. |
| HTTP | `Sinks.Http`, `Sinks.Batching` | Batches posted as newline-delimited compact JSON, with retries and a bounded queue. |
| Async | `Sinks.Async` | Writing moved to a worker thread over a bounded buffer, dropping or blocking when full. |
| Memory | `Sinks.Memory` | Events kept for tests and in-process views. |
| Map | `Sinks.Map` | One sink per key, opened on first use, least recently used closed beyond a limit. |
| Observable | `Sinks.Observable` | Events handed to subscribed observers. |

`Sink` wraps any sink: `restricted`, `controlled`, `conditional`, `fallible`, `fallbackChain`,
`aggregate`, and `audited`. `writeToLogger` sends events through another logger; `auditTo` adds a
sink whose failures `tryWrite` reports.

## Formatting

| Formatter | Module | Output |
| --- | --- | --- |
| Text | `Formatting.Text` | `[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}` and any other output template. |
| JSON | `Formatting.Json` | One object per event with properties and renderings. |
| Compact JSON | `Formatting.Compact` | `compact` with the template, `rendered` with the message. |
| Reader | `Formatting.Reader` | Compact JSON read back into events. |
| Expression templates | `Expressions` | `template` with holes, `#if`, `#each`, and `Rest()`. |

## Expressions

```pudu
let slow = Expressions.predicate("Elapsed > 500 and RequestPath like '/api/%'")
let area = Expressions.computed("Area", "Substring(SourceContext, LastIndexOf(SourceContext, '.') + 1)")
let layout = Expressions.template("[\{@l:u3\}] \{@m\}\{#each name, value in Rest(true)\} \{name\}=\{value\}\{#end\}\n")
```

Names are properties or built-ins (`@t`, `@m`, `@mt`, `@l`, `@x`, `@p`, `@i`, `@r`, `@tr`, `@sp`).
Operators: `and`, `or`, `not`, `=`, `<>`, `<`, `<=`, `>`, `>=`, `like`, `in`, `is null`, arithmetic,
`if … then … else`, array and object literals with spreads, and `[?]` or `[*]` over elements;
`ci` compares text ignoring case. Functions include `StartsWith`, `EndsWith`, `Contains`,
`IndexOf`, `Substring`, `Replace`, `IsMatch`, `Length`, `ElementAt`, `TypeOf`, `Coalesce`,
`IsDefined`, `ToString`, `Round`, `Now`, `UtcDateTime`, `Nest`, and `Inspect`.

## Settings

```pudu
let pairs = Settings.fromJsonFile("appsettings.json", "Log") ?
let loaded = Settings.apply(&Configuration.create(), &pairs.concat(Settings.fromEnvironment("APP")), &Registry.standard())
```

```json
{ "Log": {
  "MinimumLevel": { "Default": "Information", "Override": { "App.Http": "Warning" } },
  "LevelSwitches": { "$console": "Debug" },
  "Properties": { "Application": "Shop" },
  "Enrich": ["MachineName", "ProcessId"],
  "Filter": [{ "ByExcluding": "RequestPath like '/health%'" }],
  "WriteTo": [
    { "Name": "Console", "Args": { "levelSwitch": "$console" } },
    { "Name": "File", "Args": { "path": "logs/app.clef", "formatter": "compact", "rollingInterval": "Day" } }
  ]
} }
```

Environment variables use `__` between keys: `APP__MinimumLevel__Default=Debug`. Register your
own sinks and enrichers with `Registry.standard().withSink("Name", factory)`. Every problem is
reported at once and none is skipped.

## Web

`Web.RequestLogging.middleware` writes one event per request, `HTTP GET /orders responded 200 in
12.3456 ms`, with properties handlers add through `Web.Diagnostic`. `Web.Correlation` gives each
request one identifier for its events, its completion event, and its response.

## More

- `Timing.begin`, `run`, and `runResult` write one event when an operation completes or is
  abandoned.
- `Bridge.standard` turns lines written through `Std.Log` into events of a logger.
- `Configuration.createReloadableLogger` and `Logger.reload` log before the full configuration is
  known.

## Examples

`examples/` holds runnable programs: `QuickStart`, `Settings`, `Filtering`, `Files`, `Masking`,
and `Routing`.

```bash
pudu run examples/QuickStart.pudu
```

## Developing

```bash
pudu test test                                        # every suite
pudu fmt --check src test tools examples && pudu lint src test tools examples
pudu run tools/Mutate.pudu --domain --threshold 100   # mutation testing of the pure layer
```

The design lives in the [wiki vault](wiki/00-INDEX.md): one page per source file, the decisions
behind the pipeline, and the Pudu grammar rules the code follows.

## License

[Apache License 2.0](LICENSE).
