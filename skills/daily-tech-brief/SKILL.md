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

## Windowing by date

Not every record has a `published_at`; some carry only `updated_at`. A published-date bound excludes records without that field, so a brief built on date bounds alone can omit part of the feed.

Page the recent listing without date bounds and window on `published_at`, falling back to `updated_at` where it is absent. If a date-bounded query is used for cost reasons, say in the brief that records lacking a publication date were not covered.

## First run of the change feed

The change feed starts from the beginning of history when called without a cursor, so a first run returns the oldest recorded changes rather than recent ones. On a first run, or whenever the saved cursor is lost, bound the feed by date instead of calling it bare, then save the returned cursor for subsequent runs.

Persist a new change cursor only after the page has been processed successfully. Stop when the result set is exhausted or the user's requested scope is satisfied.
