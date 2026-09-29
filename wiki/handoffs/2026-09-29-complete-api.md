---
type: handoff
from_role: Forensic Guardian
to_role: Architect
status: complete
tags: [handoff, delivery]
---

# Complete public API

## Done

- Issue #1 on `feature/1-initial-log-package`, branched from `dev`.
- Settings, map and observable sinks, compact JSON reading, masking, the `Std.Log` bridge,
  correlation identifiers, process name and variable enrichers, and computed properties
  ([[CHANGELOG]]).
- Every module under `src/` has its mirrored page with a resolved Grill Log ([[src/_MOC]]).
- Against the published 0.1.2 compiler: `pudu check`, `pudu fmt --check`, and `pudu lint` are clean
  over `src`, `test`, `tools`, and `examples`; 37 suites pass with 632 assertions; all six
  examples answer 0; domain mutation kills 456 of 456 mutants ([[tools/Mutate]]).
- PR #2 into `dev` and PR #3 into `main` passed `checks` and the four `mutation` shards on Linux.
- Released 0.1.0 as tag `v0.1.0` with a GitHub release; `pudu search` lists
  `@chrismichaelps/pudu-lang-log`, and a clean project installs it and runs every program of the
  API wiki at https://github.com/chrismichaelps/pudu-lang-log/wiki.
- Compiler defects met on the way are reported upstream as pudu-lang#376, #377, and #378
  ([[grammar/pudu]]).

## Decided (do not re-litigate)

- Logging never fails the caller ([[decisions/ADR-0001-logging-never-fails-the-caller]]); loggers
  are passed ([[decisions/ADR-0002-explicit-logger]]); values are captured at write
  ([[decisions/ADR-0003-capture-at-write]]); settings go through a registry
  ([[decisions/ADR-0004-settings-through-a-registry]]).

## Open / Remaining

- None for the initial package.

## Exact next action

None; the initial package is released.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[tools/Mutate]]

## Referenced by

[[CHANGELOG]] · [[handoffs/_MOC]]
