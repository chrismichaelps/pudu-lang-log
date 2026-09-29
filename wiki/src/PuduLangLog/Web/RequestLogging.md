---
type: module
path: "@root/src/PuduLangLog/Web/RequestLogging.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, web]
aliases: [PuduLangLog.Web.RequestLogging]
---

# PuduLangLog.Web.RequestLogging

## Purpose

Writes one event per HTTP request served with `Std.Http.Server`:
`HTTP GET /orders/7 responded 200 in 1.2345 ms`, at `Error` for server errors and failures, with the
properties a handler collected and the trace of a `traceparent` header.

## Interface

### Signatures

```pudu
export type Options = {
  messageTemplate: Str,
  level: fn(&Route.Request, Int, Float64, Bool) -> Log.Level,
  enrich: fn(&Diagnostic.Collector, &Route.Request, &Web.Response) -> (),
  includeQueryInRequestPath: Bool,
  properties: fn(&Route.Request, Str, Float64, Int) -> Array[Log.Property]
}

export const SOURCE: Str = "PuduLangLog.Web.RequestLogging"

export const MESSAGE: Str = "HTTP \{RequestMethod\} \{RequestPath\} responded \{StatusCode\} in \{Elapsed:0.0000\} ms"

export fn defaults() -> Options

export fn levelFor(status: Int, failed: Bool) -> Log.Level

export fn standardProperties(request: &Route.Request, path: Str, elapsed: Float64, status: Int) -> Array[Log.Property]

export fn middleware(logger: &Logger.Logger, options: Options) -> Route.Middleware

export fn handler(logger: &Logger.Logger, options: Options, inner: fn(Route.Request, &Diagnostic.Collector) -> Web.Response) -> Route.Handler
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Parser]],
  [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Web/Diagnostic]], `Std.Decimal`, `Std.Http`,
  `Std.Http.Server.Route`, `Std.Option`, `Std.Time`.
- **Consumed by:** package users.

## Algorithm

1. `middleware` wraps the rest of the chain; `handler` wraps a handler that also receives a
   [[src/PuduLangLog/Web/Diagnostic|collector]].
2. The request runs and its elapsed milliseconds are measured on the monotonic clock.
3. The level comes from the options (default: `Error` for a collected failure or a status above
   499, `Information` otherwise); nothing more happens when the logger does not keep it.
4. `enrich` may add properties from the request and response. The event carries the collected
   properties, then `RequestMethod`, `RequestPath` (the query kept only when asked), `StatusCode`,
   and `Elapsed`, under the source context `PuduLangLog.Web.RequestLogging`.
5. A `traceparent` header of four parts with a 32-character trace and a 16-character span sets the
   event's trace and span.

## Negative Logic (Prohibited Paths)

- The response is never changed or delayed by logging beyond the event's own write.

## Edge Cases

- A malformed `traceparent` is ignored.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/Web/RequestLoggingTest`.

## Grill Log

- **Q:** Why one completion event instead of a start and an end?
  **A:** One event holds everything known about the request, which halves the volume and puts
  status and timing next to each other. _Rejected:_ paired events.
- **Q:** Why build the event directly instead of binding arguments?
  **A:** The properties are already named; binding them to holes by position would add nothing
  and report a spurious count mismatch. _Rejected:_ positional arguments.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Web/Correlation]] · [[src/PuduLangLog/Web/Diagnostic]] · [[subsystems/Web]]
