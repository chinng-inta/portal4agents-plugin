---
name: portal-related-research
description: Explore the Chinng AI-Agent Portal outward from one record, following verified entities and keywords to related articles, advisories, and official documents. Use to trace an incident, technology, or organization across the portal and decide what to read next.
---

# Portal related research

Use the `portal` MCP server declared by this plugin.

Existing portal skills choose which records to read. This skill decides where to go next: it starts at one record and builds an explored map of what connects to it, with the basis for each connection shown.

1. Retrieve the starting record and extract its candidate connection terms from `entities_en`, `keywords_en`, and `topics_en`. Keep the record's title, `tags`, summary, and body available for verification.
2. Verify each candidate term before following it. Keep a term only when it appears in the starting record's title, `tags`, summary, or body. Discard terms that appear in the entity or keyword fields alone. Normalize a malformed term — an unbalanced parenthesis or a truncated phrase — to its verified form before searching, and never search the malformed string.
3. Search the portal with each verified term, across families where the request warrants it, and classify every hit by relation type: same organization, same product or technology, related advisory, or official document on the same matter.
4. Verify each hit the same way. A record belongs on the map only when the connecting term is supported by its title or body, not by its metadata alone.
5. Retrieve bodies only for the records that the caller will actually read, and present the map with each node's portal id, title, date, relation type, the evidence for that relation, source link, and license tier.

## Do not use `related_auto`

This skill builds its map from verified terms only. `related_auto` is not a verified signal and is out of scope: do not read it, follow it, or report it.

Entity and keyword fields are extracted signals, not assertions about the record, and their precision varies by source. The verification step above is what makes the map trustworthy — do not skip it to widen coverage.

Verification depth also varies by family. Security advisories hold real text, so a relation can be verified against the document itself. News and government bodies are wrappers whose `Details` section is empty, so there a relation can only be verified against the title, tags, and summary — mark those relations as metadata-verified rather than text-verified.

## Boundaries

Separate what the records establish from what remains a hypothesis. An unexplored branch, a term that failed verification, or a gap where no record exists is useful output; record it as an open question rather than filling it with inference.

Respect each record's license tier while exploring. A `link-only` record may appear on the map with its title, metadata, and links, and must not be read or paraphrased as if its body were available.
