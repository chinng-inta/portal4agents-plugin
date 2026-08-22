---
name: citation-pack-builder
description: Build a license-respecting citation pack from Chinng AI-Agent Portal records for reuse in reports, wikis, notebooks, or other agents. Use when research findings must be handed off with sources, evidence tiers, and reuse terms attached.
---

# Citation pack builder

Use the `portal` MCP server declared by this plugin.

This skill converts portal records into a structured set of citations that another agent or person can reuse without re-deriving what may be quoted. The license tier is part of the output, not a filter applied silently.

1. Collect candidates for the topic with the portal's listing and search tools. Inspect metadata before retrieving any body.
2. Sort each candidate into its evidence tier from `license_note`:
   - `full` — body and summary may be used and quoted.
   - `summary-only` — summary and metadata only; no body.
   - `link-only` — link, title, and metadata only. Do not retrieve, quote, paraphrase, or reconstruct the content, and do not substitute an external copy of the same article.
3. Retrieve bodies only for `full` records, and only where the body materially supports a claim in the pack.
4. Emit one entry per record carrying at least: portal id, title, the claim it supports, evidence tier, `redistribution`, `published_at`, portal URL, original source URL, and `summary_omitted_reason` when present. Mark whether the claim is confirmed by a body, by a summary, or by metadata alone.

## Composition

Government records and the portal's own published articles are usually `full` with `redistribution: true`, so a pack drawn from those sources can carry substantive quoted evidence. Most external news is `link-only`, so those entries are pointers with metadata and contribute no quotable text.

State this composition in the pack. A reader must be able to see how much of the pack is quotable evidence and how much is references only, rather than inferring it entry by entry.

Numeric facts are available even on `link-only` records and may be reported with attribution, since they are metadata rather than reconstructed content.

## Output

Offer the pack as Markdown, JSON, or a BibTeX-style list, defaulting to the format the caller will consume. Keep the field names stable across formats so a downstream agent can parse them.

Never upgrade an entry's tier to make a pack more useful. An honest `link-only` entry is correct output; a reconstructed one is not.
