---
type: module
path: "@root/src/PuduLangLog/Web/Correlation.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, web]
aliases: [PuduLangLog.Web.Correlation]
---

# PuduLangLog.Web.Correlation

## Purpose

Gives every HTTP request one identifier — the one its header brings or a generated one — for the handler's events, the completion event, and the response.

## Interface

### Signatures

```pudu
export type Options = { header: Str, property: Str, echo: Bool, generate: fn() -> Str }

export const HEADER: Str = "x-correlation-id"

export const PROPERTY: Str = "CorrelationId"

export fn defaults() -> Options

export fn randomIdentifier() -> Str

export fn identifierOf(request: &Route.Request, options: &Options) -> Option[Str]

export fn middleware(options: Options) -> Route.Middleware

export fn loggerFor(logger: &Logger.Logger, request: &Route.Request, options: &Options) -> Logger.Logger

export fn enrich(options: Options) -> fn(&Diagnostic.Collector, &Route.Request, &Web.Response) -> ()
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Json]], [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Web/Diagnostic]], `Std.Http`, `Std.Http.Server.Route`, `Std.Random`, `Std.Time`.
- **Consumed by:** package users and [[src/PuduLangLog/Web/RequestLogging]] options.

## Algorithm

1. A blank header counts as missing; a generated identifier replaces it on the request.
2. The response echoes the identifier unless the handler set the header.
3. `loggerFor` gives a handler a logger carrying the identifier; `enrich` adds it to request logging.

## Negative Logic (Prohibited Paths)

- The identifier lives on the request, never in shared state, so concurrent requests cannot mix.

## Edge Cases

- Without a secure random source the identifier comes from the clock.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangLog/Web/CorrelationTest`.

## Grill Log

- **Q:** Why not push the identifier into an ambient context?
  **A:** A server handles requests concurrently and a shared context would mix them. _Rejected:_ a context push in the middleware.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangLog/_MOC]] · [[subsystems/Web]]
