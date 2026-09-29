---
type: domain
tags: [domain]
---

# Source context

The `SourceContext` property names the part of a program an event came from, usually a dotted
module name. `Logger.forSource` sets it and also selects the level override of the most specific
prefix covering it ([[domain/Level]]); the override matches the name as given, before any string
length limit shortens the captured property.

## Referenced by

[[CHANGELOG]] · [[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Sources]]
