---
type: moc
tags: [moc]
---

# Source map

One page per file under `src/`, mirroring it depth for depth. Tools outside `src/`: [[tools/Mutate]].

- [[src/PuduLangLog]] — The package root and its vocabulary: the level of an event, the values a property can hold, the parsed message template, the failure attached to an event, and the event record itself. Every other module speaks in these types; the root holds no behaviour.
- [[src/PuduLangLog/_MOC]] — the modules under `PuduLangLog`.

## Referenced by

[[00-INDEX]] · [[handoffs/2026-09-29-complete-api]]
