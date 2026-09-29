---
type: module
path: "@root/src/PuduLangLog/Sinks/File.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.85
depth_status: DEEP
tags: [module, sink]
aliases: [PuduLangLog.Sinks.File]
---

# PuduLangLog.Sinks.File

## Purpose

Appends events to files: one file, or one per [[domain/Rolling|period]], with a size limit that
either drops events or starts the next sequence, retention by count and age, optional buffering
flushed on demand or on a timer, and hooks when a file is opened or deleted.

## Interface

### Signatures

```pudu
export type Options = {
  path: Str,
  formatter: fn(&Log.Event) -> Str,
  fileSizeLimitBytes: Option[Int],
  rollingInterval: Log.RollingInterval,
  rollOnFileSizeLimit: Bool,
  retainedFileCountLimit: Option[Int],
  retainedFileTimeLimit: Option[Int],
  buffered: Bool,
  flushInterval: Option[Int],
  onOpened: Option[fn(Str) -> Option[Str]],
  onDeleting: Option[fn(Str) -> ()]
}

export fn defaults(path: Str) -> Options

export fn sink(options: Options) -> Sink.Sink
```

### Linkage

- **Requires:** [[src/PuduLangLog]], [[src/PuduLangLog/Constants/Names]],
  [[src/PuduLangLog/Domain/Rolling]], [[src/PuduLangLog/Formatting/Text]],
  [[src/PuduLangLog/Sink]], `Std.Concurrent`, `Std.Io`, `Std.Option`, `Std.Result`, `Std.Sync`.
- **Consumed by:** package users and settings.

## Algorithm

1. Every write holds the sink's lock. The event's period (on its own timestamp) is compared with
   the open file's; a new period, or no file yet, opens a file.
2. Opening lists the directory, reads back its rolled files, and picks the requested sequence or
   the latest one already on disk for that period. It creates missing directories, calls
   `onOpened` for an empty file and writes the header it answers, records the file's size, and
   deletes the files [[src/PuduLangLog/Domain/Rolling]] retires, calling `onDeleting` first.
3. A file at or past its size limit takes no more events; with `rollOnFileSizeLimit` the next
   sequence opens instead and takes the event.
4. Unbuffered text is appended at once; buffered text waits for `flush`, `close`, a period change,
   or the flushing thread.
5. `close` writes what is buffered and refuses later events with an error.

## Negative Logic (Prohibited Paths)

- No file grows past its limit by more than the one event that crossed it.
- The current file is never deleted by retention.

## Edge Cases

- A path without a directory writes in the working directory.
- A file larger than the limit when the sink starts takes no events until the period changes,
  unless rolling on size.

## Depth

DEPTH 0.85 (DEEP). Tested by `test/PuduLangLog/Sinks/FileTest`.

## Grill Log

- **Q:** Why roll on the event's timestamp rather than the time of writing?
  **A:** Events carry the time they happened; rolling by it keeps each event in the file of its own
  period and makes rolling testable with a fixed clock. _Rejected:_ reading the clock again.
- **Q:** Why append per write instead of holding the file open?
  **A:** Pudu's append is one call that opens, writes, and closes, so several processes can share a
  file and nothing is lost if the program stops without closing. Buffering is the option for
  throughput. _Rejected:_ a long-lived handle without a shared mode.
- **Q:** Why drop events at the size limit without rolling?
  **A:** The limit protects the disk; writing past it would defeat it. _Rejected:_ truncating the
  file.

## Referenced by

[[domain/Rolling]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Constants/Names]] · [[src/PuduLangLog/Domain/Rolling]] · [[src/PuduLangLog/Formatting/Text]] · [[src/PuduLangLog/Settings/Registry]] · [[subsystems/Sinks]]
