---
type: module
path: "@root/src/PuduLangLog/Sinks/Http.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, sink]
aliases: [PuduLangLog.Sinks.Http]
---

# PuduLangLog.Sinks.Http

## Purpose

Posts batches of events to an HTTP endpoint, one formatted event per line (compact JSON by
default), with custom headers such as an API key.

## Interface

### Signatures

```pudu
export type Options = {
  endpoint: Str,
  headers: Array[(Str, Str)],
  contentType: Str,
  formatter: fn(&Log.Event) -> Str,
  batching: Batching.Options,
  transport: fn(&Web.Request) -> Result[Int, Str]
}

export fn defaults(endpoint: Str) -> Options

export fn transport(limits: Client.Limits) -> fn(&Web.Request) -> Result[Int, Str]

export fn requestOf(options: &Options, batch: &Array[Log.Event]) -> Web.Request

export fn sink(options: Options) -> Sink.Sink
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Formatting/Compact]],
  [[src/PuduLangLog/Sink]], [[src/PuduLangLog/Sinks/Batching]], `Std.Http`, `Std.Http.Client`.
- **Consumed by:** package users and settings.

## Algorithm

1. `requestOf` builds a `POST` to the endpoint whose body is every event's formatted text joined,
   with the content type and each configured header.
2. The transport sends it and answers the status; the default uses the standard client under its
   limits, which refuse internal hosts not named with `reaching`.
3. A status from 200 to 299 is success; anything else, or a transport error, fails the batch, which
   [[src/PuduLangLog/Sinks/Batching]] retries.

## Negative Logic (Prohibited Paths)

- No request reaches an internal address unless the caller named it.

## Edge Cases

- A formatter without trailing line breaks produces a body of concatenated documents; the default
  formatter ends each one.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangLog/Sinks/HttpTest`, including a round trip to a local
server.

## Grill Log

- **Q:** Why a replaceable transport?
  **A:** Tests and unusual networks need to see or route requests; the default stays the
  standard client. _Rejected:_ a hard-wired client.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Formatting/Compact]] · [[src/PuduLangLog/Settings/Registry]] · [[src/PuduLangLog/Sinks/Batching]] · [[subsystems/Sinks]]
