---
type: domain
tags: [domain]
---

# Rolling

A rolling file sink starts a new file each period — year, month, day, hour, or minute — and,
optionally, when a file reaches its size limit. File names carry the period and a sequence number
(`log20260928_001.txt`); retention removes the oldest files beyond a count or age
([[src/PuduLangLog/Domain/Rolling]], [[src/PuduLangLog/Sinks/File]]).

## Referenced by

[[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Rolling]] · [[src/PuduLangLog/Sinks/File]]
