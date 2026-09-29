---
type: moc
tags: [moc]
aliases: [Index, Home]
---

# pudu-lang-log

Structured event logging for Pudu. A program writes events through a message template; the
logger captures each property as data, enriches and filters the event, and hands it to sinks
that format it as text or JSON and write it to the console, files, queues, or the network.

## Maps of content

- [[architecture/_MOC]] — layers, dependency direction, vocabulary, and testing.
- [[grammar/pudu]] — the Pudu idioms this repository is written in, and the traps to avoid.
- [[domain/_MOC]] — event, template, value, level, capture, and sink concepts.
- [[seams/_MOC]] — the sink and clock boundaries.
- [[decisions/_MOC]] — architecture decision records.
- [[src/_MOC]] — one page per source file, mirroring `src/`.
- [[subsystems/_MOC]] — bounded contexts.
- [[handoffs/_MOC]] — continuity pages for work in flight.
- [[CHANGELOG]] — the ledger of logic deltas.

## Referenced by

(root)
