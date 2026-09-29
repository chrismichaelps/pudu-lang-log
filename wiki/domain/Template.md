---
type: domain
tags: [domain]
---

# Template

A message template is text with holes: `{Name}`, `{@Name}` to destructure, `{$Name}` to stringify,
`{Name,10}` or `{Name,-10}` to align, `{Name:0.00}` to format, and `{0}` for positional holes.
Doubled braces are literal. [[src/PuduLangLog/Domain/Parser]] parses a template once; the pipeline
keeps up to a thousand parsed templates.

Binding ([[src/PuduLangLog/Domain/Capture]] `bind`) matches arguments to holes: numbered templates
by number, named templates left to right. Extra arguments are kept as `__0`, `__1`, and so on;
missing ones are reported to the self-log. The template's text identifies the kind of event, and
[[src/PuduLangLog/Domain/EventId]] hashes it to a stable identifier.

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[src/PuduLangLog]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Parser]] · [[subsystems/Pipeline]]
