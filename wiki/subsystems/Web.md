---
type: subsystem
tags: [subsystem]
---

# Web

[[src/PuduLangLog/Web/RequestLogging]] writes one completion event per HTTP request, with
properties handlers add through a [[src/PuduLangLog/Web/Diagnostic]] collector and the trace of a
`traceparent` header. [[src/PuduLangLog/Web/Correlation]] gives each request one identifier that
reaches the handler's events, the completion event, and the response.

## Referenced by

[[subsystems/_MOC]]
