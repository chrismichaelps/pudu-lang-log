---
type: seam
tags: [seam]
---

# Sink seam

A [[src/PuduLangLog/Sink]] is a record of four functions: `emit` an event, `flush` what is
buffered, `close` for good, and `attach` a listener for failures found later. Everything that
delivers events — [[subsystems/Sinks]] — is built on it, and so is everything that wraps a sink:
level restriction, switches, conditions, fallible and fallback chains, sub-loggers, maps, and
batching. Tests substitute [[src/PuduLangLog/Sinks/Memory]] or
[[src/PuduLangLog/Sinks/Observable]].

## Referenced by

[[architecture/_MOC]] · [[architecture/LANGUAGE]] · [[seams/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Sink]] · [[subsystems/Sinks]]
