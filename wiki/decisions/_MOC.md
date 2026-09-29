---
type: moc
tags: [moc, decision]
---

# Decisions

- [[decisions/ADR-0001-logging-never-fails-the-caller]] — problems inside the pipeline go to the self-log; only audit sinks report to the caller.
- [[decisions/ADR-0002-explicit-logger]] — loggers are values passed to the code that logs; there is no process-wide logger.
- [[decisions/ADR-0003-capture-at-write]] — arguments are captured into immutable values when the event is written.
- [[decisions/ADR-0004-settings-through-a-registry]] — settings name sinks and enrichers registered by the program.

## Referenced by

[[00-INDEX]]
