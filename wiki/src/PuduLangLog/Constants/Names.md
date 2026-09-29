---
type: module
path: "@root/src/PuduLangLog/Constants/Names.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.2
depth_status: SHALLOW
tags: [module, leaf]
aliases: [PuduLangLog.Constants.Names]
---

# PuduLangLog.Constants.Names

## Purpose

Names shared across modules: the source context property and the default output templates of the
console and file sinks.

## Interface

### Signatures

```pudu
export const SOURCE_CONTEXT: Str = "SourceContext"

export const CONSOLE_TEMPLATE: Str = "[\{Timestamp:HH:mm:ss\} \{Level:u3\}] \{Message:lj\}\{NewLine\}\{Exception\}"

export const FILE_TEMPLATE: Str = "\{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz\} [\{Level:u3\}] \{Message:lj\}\{NewLine\}\{Exception\}"
```

### Linkage

- **Consumed by:** [[src/PuduLangLog/Event]], [[src/PuduLangLog/Filter]],
  [[src/PuduLangLog/Logger]], [[src/PuduLangLog/Sinks/Console]], [[src/PuduLangLog/Sinks/File]].

## Algorithm

Constants only. The console template is `[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj}{NewLine}{Exception}`;
the file template is `{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level:u3}] {Message:lj}{NewLine}{Exception}`.

## Negative Logic (Prohibited Paths)

- No module spells `SourceContext` itself.

## Edge Cases

(none)

## Depth

DEPTH 0.2 (SHALLOW).

## Grill Log

- **Q:** Why a constants module?
  **A:** A name spelled in two places drifts. _Rejected:_ literals at each use.

## Referenced by

[[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Bridge]] · [[src/PuduLangLog/Event]] · [[src/PuduLangLog/Filter]] · [[src/PuduLangLog/Logger]] · [[src/PuduLangLog/Sinks/Console]] · [[src/PuduLangLog/Sinks/File]]
