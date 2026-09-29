---
type: subsystem
tags: [subsystem]
---

# Configuration

In code, [[src/PuduLangLog/Configuration]] chains one decision per call and creates the logger.
From text, [[src/PuduLangLog/Settings]] applies settings read from pairs, JSON, or environment
variables, using the names a [[src/PuduLangLog/Settings/Registry]] holds
([[decisions/ADR-0004-settings-through-a-registry]]); the key rules are pure, in
[[src/PuduLangLog/Domain/Settings]]. [[src/PuduLangLog/Bridge]] connects `Std.Log` to a
configured logger.

## Referenced by

[[architecture/LANGUAGE]] · [[subsystems/_MOC]]
