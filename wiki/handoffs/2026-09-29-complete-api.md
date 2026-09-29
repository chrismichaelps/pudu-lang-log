---
type: handoff
from_role: Implementer
to_role: Forensic Guardian
status: active
tags: [handoff, delivery]
---

# Complete public API

## Done

- Issue #1 on `feature/1-initial-log-package`, branched from `dev`.
- Settings, map and observable sinks, compact JSON reading, masking, the `Std.Log` bridge,
  correlation identifiers, process name and variable enrichers, and computed properties
  ([[CHANGELOG]]).
- Every module under `src/` has its mirrored page with a resolved Grill Log ([[src/_MOC]]).

## Decided (do not re-litigate)

- Logging never fails the caller ([[decisions/ADR-0001-logging-never-fails-the-caller]]); loggers
  are passed ([[decisions/ADR-0002-explicit-logger]]); values are captured at write
  ([[decisions/ADR-0003-capture-at-write]]); settings go through a registry
  ([[decisions/ADR-0004-settings-through-a-registry]]).

## Open / Remaining

- Domain mutation at a threshold of 100, the pull request into `dev`, and the release.

## Exact next action

Run `pudu run tools/Mutate.pudu --domain --threshold 100` on the committed tree.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[tools/Mutate]]

## Referenced by

[[handoffs/_MOC]]
