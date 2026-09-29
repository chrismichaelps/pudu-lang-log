---
type: domain
tags: [domain]
---

# Enrichment

An enricher is a step that adds or changes properties of every event it sees
([[src/PuduLangLog/Enricher]]). A pipeline applies the enrichers of a contextual logger first, most
recent first, then its own in the order they were configured. An enricher adds a property only when
the event lacks it, so the message's own properties win. [[src/PuduLangLog/Context]] holds
enrichers pushed for a stretch of work; `when`, `atLevel`, and `atSwitch` apply an enricher to some
events only; [[src/PuduLangLog/Expressions]] `computed` adds a property from an expression.

## Referenced by

[[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Context]] · [[src/PuduLangLog/Enricher]] · [[src/PuduLangLog/Enrichers/Environment]]
