---
type: decision
status: accepted
tags: [decision]
---

# ADR-0002 — Loggers are passed, not global

## Context

Pudu module scope holds only constants: there is no module-level mutable state in which a
process-wide logger could live, and a `const` cannot call a function to create one.

## Decision

A logger is a value. A program creates one from a [[src/PuduLangLog/Configuration]] or
[[src/PuduLangLog/Settings]] and passes it to the code that logs; `Logger.forSource` and
`forContext` derive contextual loggers from it. Libraries that cannot take a logger log through
`Std.Log`, which [[src/PuduLangLog/Bridge]] routes into the program's pipeline. A program that
must replace its configuration after startup creates the first logger with
`createReloadableLogger`, and every logger derived from it follows `Logger.reload`.

## Consequences

- There is no hidden dependency on initialisation order.
- Code that logs states its dependency on a logger in its signature.

## Referenced by

[[decisions/_MOC]] · [[domain/Bootstrap]] · [[handoffs/2026-09-29-complete-api]] · [[src/PuduLangLog/Logger]]
