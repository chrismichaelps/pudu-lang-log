---
type: domain
tags: [domain]
---

# Capture

An argument becomes a [[src/PuduLangLog|`Value`]] under a hint and a policy
([[decisions/ADR-0003-capture-at-write]]):

- `Default` keeps scalars and collections and renders structures to text.
- `Destructure` keeps structures, after the first custom rule or tag transform that answers.
- `Stringify` renders anything to text.

The policy cuts nesting beyond `maximumDepth`, text beyond `maximumStringLength` (ending in `…`),
and collections beyond `maximumCollectionCount`. Tags listed as scalars always render to text.
[[src/PuduLangLog/Domain/Masking]] supplies rules that leave out or mask members of a tag, and
[[src/PuduLangLog/Enrichers/Masking]] hides sensitive values after capture.

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[domain/Value]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Capture]] · [[subsystems/Pipeline]]
