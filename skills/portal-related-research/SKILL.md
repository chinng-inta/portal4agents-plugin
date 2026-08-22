---
name: portal-related-research
description: Explore the Chinng AI-Agent Portal outward from one record, following verified entities and keywords to related articles, advisories, and official documents. Use to trace an incident, technology, or organization across the portal and decide what to read next.
---

# Portal related research

Use the `portal` MCP server declared by this plugin.

Existing portal skills choose which records to read. This skill decides where to go next: it starts at one record and builds an explored map of what connects to it, with the basis for each connection shown.

1. Retrieve the starting record and extract its candidate connection terms from `entities_en`, `keywords_en`, and `topics_en`. Keep the record's own title and body available for verification.
2. Verify each candidate term before following it. Keep a term only when it appears in the starting record's title, summary, or body. Discard terms that appear in the entity or keyword fields alone.
3. Search the portal with each verified term, across families where the request warrants it, and classify every hit by relation type: same organization, same product or technology, related advisory, or official document on the same matter.
4. Verify each hit the same way. A record belongs on the map only when the connecting term is supported by its title or body, not by its metadata alone.
5. Retrieve bodies only for the records that the caller will actually read, and present the map with each node's portal id, title, date, relation type, the evidence for that relation, source link, and license tier.

## Do not use `related_auto`

The `related_auto` field is unreliable across every source in the portal, including records the portal publishes itself, and has been observed to return terms unrelated to the record and terms damaged by parsing. Do not read it, follow it, or report it.

Entity quality also varies by source. Entities on the portal's own published articles are generally sound; entities on external news and on government documents frequently contain fragments belonging to other records. The verification step above is what makes this skill safe on those sources — do not skip it to widen the map.

## Boundaries

Separate what the records establish from what remains a hypothesis. An unexplored branch, a term that failed verification, or a gap where no record exists is useful output; record it as an open question rather than filling it with inference.

Respect each record's license tier while exploring. A `link-only` record may appear on the map with its title, metadata, and links, and must not be read or paraphrased as if its body were available.
