---
type: domain
tags: [domain]
---

# Event

An event is the record [[src/PuduLangLog|`Event`]] holds: a UTC timestamp with its observer's
offset, a level, the parsed message template, the captured properties, and optionally a failure,
a trace identifier, and a span identifier. Its message is not stored: it is the template rendered
with the properties, by every formatter that needs it ([[src/PuduLangLog/Domain/Display]]).

Properties are unique by name. Enrichers add a property only when the event lacks it, so values
from the message template win over context, and the most recent context wins over older context.

[[src/PuduLangLog/Event]] builds and changes events; [[src/PuduLangLog/Formatting/Reader]] reads
them back from compact JSON.

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[src/PuduLangLog]] · [[src/PuduLangLog/_MOC]] · [[src/PuduLangLog/Domain/Properties]] · [[src/PuduLangLog/Event]]
