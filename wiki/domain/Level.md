---
type: domain
tags: [domain]
---

# Level

Six levels, least severe first: `Verbose`, `Debug`, `Information`, `Warning`, `Error`, `Fatal`
([[src/PuduLangLog/Domain/Levels]]). A pipeline keeps events at or above its minimum; a
[[src/PuduLangLog/LevelSwitch]] makes the minimum changeable while running. Overrides give a
source prefix its own level or switch; the most specific prefix covering an event's source decides
([[src/PuduLangLog/Domain/Sources]]), matched against the source as given before any truncation.
A sink can be restricted to a higher level than the pipeline's, never a lower one.

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[domain/SourceContext]] · [[src/PuduLangLog]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Levels]] · [[src/PuduLangLog/LevelSwitch]] · [[subsystems/Pipeline]]
