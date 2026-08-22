---
name: agent-readiness-compare
description: Compare websites using the Chinng Agent-Readiness Directory. Use when evaluating verified agent discovery, content, bot-control, API, MCP, skill, authentication, or commerce surfaces.
---

# Agent-Readiness comparison

Use the `portal` MCP server declared by this plugin.

1. List Agent-Readiness entries to identify the requested or comparable domains.
2. Retrieve the detailed assessment for every domain included in the comparison.
3. Compare overall level, total score, category scores, and the underlying observed checks.
4. Cite only evidence present in the assessment. Treat missing or neutral checks as unknown, not as verified failures.

## Category percentages are not comparable across domains

A category's maximum excludes its neutral checks, so the denominator differs from domain to domain — one site's Discoverability may be scored out of six while another's is out of five, and the same is true of the other categories. Two equal percentages can therefore represent different numbers of passed checks.

Report each category as `earned/max` alongside its percentage, and compare the underlying check identifiers rather than the percentages when the denominators differ. The overall score (0-100) and the level are comparable and may be used directly.

## Only scanned domains exist

The directory holds the domains the portal has scanned, drawn from crawl-derived candidate sources. A requested domain may simply never have been scanned, and the lookup then reports that the domain was not found.

Report a not-found domain as unscanned. Never present it as a zero score, a Level 0, or a site lacking agent surfaces — nothing was observed either way. When a comparison set mixes scanned and unscanned domains, say which is which before comparing.

State the scan time or freshness information when available so readers do not mistake a historical observation for current behavior.
