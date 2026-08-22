---
name: daily-tech-brief
description: Build a concise daily technology brief from the Chinng AI-Agent Portal. Use for recurring tech-news digests that must include new and materially revised items.
---

# Daily technology brief

Use the `portal` MCP server declared by this plugin.

1. Read the technology-news family for newly published items in the requested time window.
2. For recurring runs, use the portal's change feed and the previously saved cursor to find materially revised records.
3. Deduplicate by portal item ID and rank candidates from their metadata before retrieving selected records.
4. Produce a concise brief with links and source attribution. Follow each record's reuse and omission metadata.

Persist a new change cursor only after the page has been processed successfully. Stop when the result set is exhausted or the user's requested scope is satisfied.
