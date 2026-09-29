---
type: module
path: "@root/src/PuduLangLog/Domain/Settings.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, domain]
aliases: [PuduLangLog.Domain.Settings]
---

# PuduLangLog.Domain.Settings

## Purpose

Reads colon-separated key-value settings into a plan of levels, switches, overrides, properties, enrichers, sinks, filters, and capture limits, and flattens JSON and environment names into such keys.

## Interface

### Signatures

```pudu
export type Target = { name: Str, arguments: Array[(Str, Str)] }

export type Filter = { including: Bool, expression: Str }

export type Plan = {
  minimum: Option[Log.Level],
  controlledBy: Option[Str],
  switches: Array[(Str, Log.Level)],
  overrides: Array[(Str, Str)],
  properties: Array[(Str, Str)],
  enrichers: Array[Target],
  sinks: Array[Target],
  audits: Array[Target],
  filters: Array[Filter],
  maximumDepth: Option[Int],
  maximumStringLength: Option[Int],
  maximumCollectionCount: Option[Int],
  scalars: Array[Str]
}

export type Read = { plan: Plan, problems: Array[Str] }

export const LEVEL_SWITCH_ARGUMENT: Str = "levelSwitch"

export fn empty() -> Plan

export fn read(pairs: &Array[(Str, Str)]) -> Read

export fn switchName(text: Str) -> Str

export fn levelOf(text: Str) -> Option[Log.Level]

export fn intervalOf(text: Str) -> Option[Log.RollingInterval]

export fn count(text: Str) -> Option[Int]

export fn flag(text: Str) -> Option[Bool]

export fn argument(target: &Target, name: Str) -> Option[Str]

export fn fromJson(text: Str, section: Str) -> Result[Array[(Str, Str)], Str]

export fn environmentKey(name: Str, prefix: Str) -> Option[Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Domain/Levels]], `Std.Json`, `Std.List`, `Std.Map`, `Std.Option`.
- **Consumed by:** [[src/PuduLangLog/Settings]] and [[src/PuduLangLog/Settings/Registry]].

## Algorithm

1. Section and argument names ignore case; a later value for a key replaces an earlier one.
2. Numbered or named entries of `Enrich`, `WriteTo`, and `AuditTo` keep the order they first appear.
3. Overrides keep either a level or a `$switch` reference.
4. After reading, entries that name nothing and references to undeclared switches are reported.
5. JSON objects nest with `:` and lists by index; `PREFIX__A__B` names become `A:B`.

## Negative Logic (Prohibited Paths)

- An unknown or malformed key is always reported, never ignored.

## Edge Cases

- A JSON document without the section yields no settings.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangLog/Domain/SettingsTest`.

## Grill Log

- **Q:** Why a pure plan before building anything?
  **A:** Every rule about keys can be tested and mutated without sinks, files, or the environment. _Rejected:_ building the configuration while reading keys.

## Referenced by

[[CHANGELOG]] · [[decisions/ADR-0004-settings-through-a-registry]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Settings]] · [[src/PuduLangLog/Settings/Registry]] · [[subsystems/Configuration]]
