---
name: company-news-monitor
description: Track company and organization news through normalized entities and incremental changes in the Chinng AI-Agent Portal. Use for recurring company monitoring and event timelines.
---

# Company news monitor

Use the `portal` MCP server declared by this plugin.

1. Discover candidates with an exact normalized entity match when available, then broaden to portal search only as needed.
2. For recurring runs, combine newly published records with the portal's change feed and the previously saved cursor.
3. Deduplicate by portal item ID, select relevant records from metadata, and retrieve only those records needed for the report.
4. Preserve dates, entity evidence, portal links, and original source links. Follow each record's license and omission metadata.

## An entity match is a candidate, not a finding

Entity and keyword fields are extracted signals whose precision varies by source, so an entity match alone is not evidence that a record concerns the tracked company.

Confirm each match against the record's title, `tags`, or summary before including it. Drop matches supported only by the entity or keyword fields. When a record's license leaves no summary to check, the title is the only available evidence — include it only if the title itself names the company, and mark it as title-only evidence.

`related_auto` is not a verified signal and is not a basis for adding a record to a company's timeline.

## First run of the change feed

The change feed starts from the beginning of history when called without a cursor. On a first run, or whenever the saved cursor is lost, bound the feed by date rather than calling it bare, then save the returned cursor.

Persist a new change cursor only after successful processing. Do not quote or reconstruct summaries or bodies withheld by the portal.
