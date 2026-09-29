---
type: domain
tags: [domain]
---

# Pipeline

The pipeline is the fixed order every event passes: level check, template parse and capture,
enrichment, filtering, and emission to the sinks and audit sinks ([[src/PuduLangLog/Pipeline]]).
The level check comes first so disabled events cost nothing more; see [[subsystems/Pipeline]].

## Referenced by

[[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Pipeline]]
