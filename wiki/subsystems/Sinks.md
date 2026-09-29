---
type: subsystem
tags: [subsystem]
---

# Sinks

Destinations: [[src/PuduLangLog/Sinks/Console]] with [[src/PuduLangLog/Sinks/Theme|themes]],
[[src/PuduLangLog/Sinks/File]] with rolling and retention, [[src/PuduLangLog/Sinks/Http]] over
[[src/PuduLangLog/Sinks/Batching]], [[src/PuduLangLog/Sinks/Memory]], and
[[src/PuduLangLog/Sinks/Observable]].

Wrappers: [[src/PuduLangLog/Sinks/Async]] moves writing to a worker thread,
[[src/PuduLangLog/Sinks/Map]] opens one sink per key, and [[src/PuduLangLog/Sink]] restricts,
switches, conditions, joins, audits, and chains sinks. All of them share the [[seams/Sink]].

## Referenced by

[[seams/Sink]] · [[src/PuduLangLog/Sink]] · [[subsystems/_MOC]]
