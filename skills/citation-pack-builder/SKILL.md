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
4. Emit one entry per record carrying at least: portal id, title, the claim it supports, evidence tier, `summary_source`, `redistribution`, `published_at`, portal URL, original source URL, and `summary_omitted_reason` when present. Mark whether the claim is confirmed by document text, by a summary, or by metadata alone.

## `full` does not guarantee document text

A `full` record's body is a wrapper — title, the summary as `TL;DR`, tags as `Key Points`, then `Details`. Only `Details` can hold document text, and for news and government records it is empty. Check it before treating a record as quotable evidence.

- **Security advisories** carry real text: impact, patches, workarounds, affected and fixed versions, and upstream references. These are the pack's strongest entries.
- **Government records** hold no document text. Their value is the official abstract, and only when `summary_source` is `official`.
- **The portal's own articles** hold no document text either; the summary is the substance.
- **External news** is mostly `link-only` and contributes pointers with metadata.

State this composition in the pack. A reader must be able to see how much of the pack is quotable document text, how much is abstracts, and how much is references only, rather than inferring it entry by entry.

Never present a wrapper body as though it were the document. If an entry's `Details` is empty, its claim is supported by a summary, not by the source text, and the entry must say so.

Numeric facts are available even on `link-only` records and may be reported with attribution, since they are metadata rather than reconstructed content.

## Output

The portal publishes a citation document per record, and the change feed exposes its `citation_url`. Prefer that document as an entry's canonical citation when it is available, and record its URL in the entry rather than restating fields the portal already publishes.

Offer the pack as Markdown, JSON, or a BibTeX-style list, defaulting to the format the caller will consume. Keep the field names stable across formats so a downstream agent can parse them.

Include only text that comes from the upstream source. Any deployment-specific note that is not part of the original advisory, article, or document is out of scope for a pack and must not be redistributed.

Never upgrade an entry's tier to make a pack more useful. An honest `link-only` entry is correct output; a reconstructed one is not.
