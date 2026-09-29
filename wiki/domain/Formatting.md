---
type: domain
tags: [domain]
---

# Formatting

Formatting turns an event into text. Values display by kind: text quoted in messages unless the
`l` format is given, numbers and dates under format strings such as `0.00`, `P1`, and
`yyyy-MM-dd HH:mm`, structures as `Tag { Name: value }`, and collections in brackets ([[src/PuduLangLog/Domain/Display]]). JSON
formatting escapes text and tags structures ([[src/PuduLangLog/Domain/Json]]). The subsystem is
described in [[subsystems/Formatting]].

## Referenced by

[[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Formatting/Compact]] · [[src/PuduLangLog/Formatting/Json]] · [[src/PuduLangLog/Formatting/Text]]
