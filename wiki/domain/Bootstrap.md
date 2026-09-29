---
type: domain
tags: [domain]
---

# Bootstrap

A program often needs to log before its configuration is read. It creates a first logger with
`createReloadableLogger` from a minimal configuration, then calls `Logger.reload` with the full
pipeline once settings are loaded; every logger derived from the first follows, and the previous
pipeline is flushed and closed ([[src/PuduLangLog/Logger]], [[decisions/ADR-0002-explicit-logger]]).

## Referenced by

[[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Logger]]
