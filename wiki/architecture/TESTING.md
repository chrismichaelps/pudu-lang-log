---
type: architecture
tags: [architecture, test]
aliases: [Testing]
---

# Testing

Every suite is a file under `test/`, mirroring the module it covers; `pudu test test` runs them all
and each suite names its failed checks on stderr.

| Level | Suites | What they prove |
| --- | --- | --- |
| Domain | `test/PuduLangLog/Domain/**` | template parsing, capture and binding, display and formatting of every value kind, JSON, dates, numbers, rolling file names, retry schedules, masking, settings plans, compact JSON reading, recency order |
| Expressions | `ExpressionsTest`, `Expressions/TemplateTest` | literals, names, built-ins, operators, wildcards, functions, syntax errors, templates and directives |
| Pipeline | `LoggerTest`, `TopologyTest`, `ValueTest`, `TimingTest` | levels, switches, overrides, context, enrichment, filters, sub-loggers, reloading, audit and fallible sinks, timed operations |
| Sinks | `test/PuduLangLog/Sinks/**` | console, file rolling and retention, HTTP batching, async queues, memory, map, observable |
| Formatting | `Formatting/**` | text, JSON, and compact JSON output, and reading compact JSON back |
| Configuration | `SettingsTest`, `BridgeTest` | settings applied end to end, standard factories, and the standard library bridge |
| Web | `test/PuduLangLog/Web/**` | request completion events and correlation identifiers |
| Package | `test/Package/LayoutTest` | every shipped module is the root `PuduLangLog` or under it, is named after its path, and agrees with the manifest |
| Vault | `test/Package/VaultTest` | the vault mirrors `src/` page for page, every page has a Grill Log, every exported function is in its page's signatures, every link resolves, and every page lists the pages linking to it |
| Examples | `examples/*.pudu`, run by CI | the documented programs compile and answer 0 |
| Mutation | [[tools/Mutate]] | the suites notice single-point changes to the pure layer |

The mutation gate runs on pull requests over `Domain/` with a threshold of 100: every valid mutant
is killed. A mutant that cannot change behaviour is removed by simplifying the code rather than
excused.

## Referenced by

[[architecture/_MOC]] · [[CHANGELOG]] · [[handoffs/2026-09-29-complete-api]]
