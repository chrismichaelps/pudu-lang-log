---
type: domain
tags: [domain]
---

# Failure

A failure is the error attached to an event ([[src/PuduLangLog/Failure]]): a kind, a message,
trace lines innermost first, and the failures that caused it. Formatters write it as
`Kind: message`, trace lines indented with `at`, and each cause after ` ---> `;
[[src/PuduLangLog/Domain/Clef]] reads that text back.

## Referenced by

[[domain/_MOC]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Failure]]
