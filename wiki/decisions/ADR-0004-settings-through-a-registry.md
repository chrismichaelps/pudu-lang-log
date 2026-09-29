---
type: decision
status: accepted
tags: [decision]
---

# ADR-0004 — Settings name registered sinks and enrichers

## Context

Configuration text chooses sinks and enrichers by name. Pudu cannot look up functions by name at
run time, and a configuration file should not reach code the program did not choose to expose.

## Decision

[[src/PuduLangLog/Settings/Registry]] maps names to factories that receive the setting's
arguments. The standard registry names the console, file, and HTTP sinks and the environment
enrichers; a program adds its own with `withSink` and `withEnricher`.
[[src/PuduLangLog/Domain/Settings]] reads keys into a pure plan first, so every key rule is tested
without effects, and [[src/PuduLangLog/Settings]] refuses the whole configuration when any setting
cannot be used.

## Consequences

- An unknown sink name is an error at startup, never a silently missing sink.
- The registry lists exactly what configuration text can create.

## Referenced by

[[CHANGELOG]] · [[decisions/_MOC]] · [[handoffs/2026-09-29-complete-api]] · [[subsystems/Configuration]]
