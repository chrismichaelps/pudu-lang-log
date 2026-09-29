---
type: domain
tags: [domain]
---

# Filtering

A filter decides whether an event continues to the sinks ([[src/PuduLangLog/Filter]]). Every
filter of a pipeline must keep an event for it to be written. Predicates come from functions, from
the helpers `fromSource`, `withProperty`, `atLeast`, and `withFailure`, or from expressions
([[src/PuduLangLog/Expressions]] `predicate`). A condition on a single sink is the sink wrapper
`conditional`, not a filter.

## Referenced by

[[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Filter]]
