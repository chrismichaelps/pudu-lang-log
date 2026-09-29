---
type: subsystem
tags: [subsystem]
---

# Pipeline

[[src/PuduLangLog/Logger]] writes through a [[src/PuduLangLog/Pipeline]]: the level check
([[domain/Level]]), template parsing and capture ([[domain/Template]], [[domain/Capture]]), the
logger's own enrichers, the pipeline's [[src/PuduLangLog/Enricher|enrichers]], the
[[src/PuduLangLog/Filter|filters]], and the sinks. [[src/PuduLangLog/Context]] carries properties
for a stretch of work, [[src/PuduLangLog/Timing]] writes timed operations, and
[[src/PuduLangLog/Enrichers/Environment]] and [[src/PuduLangLog/Enrichers/Masking]] are the
ready-made enrichers.

## Referenced by

[[domain/Pipeline]] · [[subsystems/_MOC]]
