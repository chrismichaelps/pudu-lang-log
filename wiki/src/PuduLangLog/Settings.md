---
type: module
path: "@root/src/PuduLangLog/Settings.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module, configuration]
aliases: [PuduLangLog.Settings]
---

# PuduLangLog.Settings

## Purpose

Changes a configuration from key-value settings — a JSON document, environment variables, or pairs — and hands back the level switches the settings declared.

## Interface

### Signatures

```pudu
export type Loaded = { configuration: Configuration.Configuration, switches: Array[(Str, LevelSwitch.LevelSwitch)] }

export fn apply(base: &Configuration.Configuration, pairs: &Array[(Str, Str)], registry: &Registry.Registry) -> Result[Loaded, Array[Str]]

export fn switchNamed(loaded: &Loaded, name: Str) -> Option[LevelSwitch.LevelSwitch]

export fn fromJson(text: Str, section: Str) -> Result[Array[(Str, Str)], Str]

export fn fromJsonFile(path: Str, section: Str) -> Result[Array[(Str, Str)], Str]

export fn fromEnvironment(prefix: Str) -> Array[(Str, Str)]

export fn argument(arguments: &Array[(Str, Str)], name: Str) -> Option[Str]
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Configuration]], [[src/PuduLangLog/Domain/Settings]], [[src/PuduLangLog/Expressions]], [[src/PuduLangLog/LevelSwitch]], [[src/PuduLangLog/Settings/Registry]], [[src/PuduLangLog/Sink]], `Std.Env`, `Std.Io`, `Std.List`.
- **Consumed by:** package users.

## Algorithm

1. The pairs are read into a plan by [[src/PuduLangLog/Domain/Settings]].
2. Each declared switch is created once and shared by every setting naming it.
3. Enrichers and sinks are built by the factories the registry holds under their names; `restrictedToMinimumLevel` and `levelSwitch` wrap any sink.
4. Filter expressions are compiled with [[src/PuduLangLog/Expressions]].
5. Every problem is collected; any problem refuses the whole configuration.

## Negative Logic (Prohibited Paths)

- A configuration is never answered with some settings silently skipped.

## Edge Cases

- No settings leave the base configuration unchanged.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangLog/SettingsTest`.

## Grill Log

- **Q:** Why refuse everything on one problem?
  **A:** A logging setup that half applies hides the very events needed to find out why. _Rejected:_ applying what can be applied and reporting the rest to the self-log.

## Referenced by

[[architecture/_MOC]] · [[CHANGELOG]] · [[decisions/ADR-0002-explicit-logger]] · [[decisions/ADR-0004-settings-through-a-registry]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Settings]] · [[src/PuduLangLog/Expressions]] · [[src/PuduLangLog/Settings/Registry]] · [[subsystems/Configuration]]
