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

Persist a new change cursor only after successful processing. Do not quote or reconstruct summaries or bodies withheld by the portal.
