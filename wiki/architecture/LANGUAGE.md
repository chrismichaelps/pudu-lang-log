---
type: architecture
tags: [architecture, vocabulary]
aliases: [Language]
---

# Language

| Term | Meaning |
| --- | --- |
| Event | One record written by a program: timestamp, level, message template, properties, and optionally a failure, trace, and span ([[domain/Event]]). |
| Message template | Text with named or numbered holes, `{User}` or `{0}`, parsed once and reused ([[domain/Template]]). |
| Hole | One placeholder: a name, an optional capture hint (`@` destructure, `$` stringify), alignment, and format. |
| Property | A name bound to a captured value. |
| Capture | Turning an argument into a value under the capture policy: limits on depth, text length, and collection size, and destructuring rules ([[domain/Capture]]). |
| Level | How much an event matters, from `Verbose` to `Fatal` ([[domain/Level]]). |
| Level switch | A shared, changeable minimum level. |
| Override | A minimum level for one source prefix, most specific prefix first. |
| Enricher | A step adding or changing properties of every event it sees. |
| Filter | A predicate deciding whether an event continues. |
| Sink | Where events go: console, file, network, memory, another logger ([[seams/Sink]]). |
| Audit sink | A sink whose failure is reported to the caller of `tryWrite`. |
| Self-log | Where the pipeline reports its own problems. |
| Expression | A filter or computed value written in the expression language ([[subsystems/Expressions]]). |
| Settings | Key-value text that configures a logger ([[subsystems/Configuration]]). |

## Referenced by

[[architecture/_MOC]]
