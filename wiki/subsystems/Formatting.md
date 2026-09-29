---
type: subsystem
tags: [subsystem]
---

# Formatting

- [[src/PuduLangLog/Formatting/Text]] — output templates such as
  `[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}`.
- [[src/PuduLangLog/Formatting/Json]] — one JSON object per event with properties and renderings.
- [[src/PuduLangLog/Formatting/Compact]] — compact newline-delimited JSON, with the template or
  the rendered message.
- [[src/PuduLangLog/Formatting/Reader]] — compact JSON read back into events.
- [[src/PuduLangLog/Expressions]] `template` — expression templates with conditionals and loops.

Rendering rules live in `Domain/`: [[src/PuduLangLog/Domain/Display]],
[[src/PuduLangLog/Domain/Output]], [[src/PuduLangLog/Domain/Json]],
[[src/PuduLangLog/Domain/Dates]], and [[src/PuduLangLog/Domain/Numbers]].

## Referenced by

[[domain/Formatting]] · [[subsystems/_MOC]]
