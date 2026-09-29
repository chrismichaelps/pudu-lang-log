---
type: seam
tags: [seam]
---

# Clock seam

A [[src/PuduLangLog/Clock]] is a function answering the present timestamp. The pipeline stamps
every event with it; tests substitute `Clock.fixed` or `Clock.stepping` so output is exact.

## Referenced by

[[architecture/_MOC]] · [[seams/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Clock]]
