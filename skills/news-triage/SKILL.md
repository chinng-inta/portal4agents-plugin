---
name: news-triage
description: Research and compare news from the Chinng AI-Agent Portal. Use when finding relevant articles, investigating a topic, or preparing a sourced news summary.
---

# News triage

Use the `portal` MCP server declared by this plugin.

1. Discover candidates with the portal's listing or search tools. Inspect metadata and summaries before requesting bodies.
2. Select only the records needed for the task, then retrieve those records individually.
3. Respect each record's `license_note` and omission metadata. Never reconstruct content withheld by the portal.
4. Cite the portal record and retain its original source link when presenting findings.

Keep discovery token-light: list or search first, retrieve bodies only when they materially improve the answer.
