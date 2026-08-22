---
name: government-policy-watch
description: Track official government documents in the Chinng AI-Agent Portal — US Federal Register, EU EUR-Lex, and Japanese agency feeds. Use for regulatory monitoring, comment deadlines, effective dates, and primary-source policy reading.
---

# Government policy watch

Use the `portal` MCP server declared by this plugin.

Government records are the portal's primary-source family. Most carry `license_note: full` with `redistribution: true`, so their bodies may be read and quoted. Read the body — do not report from the summary.

1. Narrow candidates with the structured fields of the government listing: `country`, `source`, `document_type`, `agency_names`, and the published-date bounds. Use portal search restricted to the government category only when the structured filters cannot express the request.
2. Retrieve the full record for each selected document. Take the issuing agency, dates, and identifiers from `agency_names`, `document_number`, `document_type`, and `published_at`, and take the substance from the body.
3. For recurring runs, combine newly published documents with the portal's change feed and the previously saved cursor so revised documents are not missed. Distinguish a new document from a revision of one already reported.
4. Report each document with its issuing agency, document type and number, new-or-revised status, what changed, who is affected, any comment deadline or effective date, and both the official source link and the portal link.

## Do not trust the generated summary

The `summary` and `summary_en` fields on government records are model-generated and have been observed to state an issuing agency, jurisdiction, or date that contradicts the document. On these records `summary_en` is also frequently not translated.

Do not use these fields to select candidates and do not repeat them in output. When a summary conflicts with the body or the structured fields, follow the body and the structured fields, and note that the stored summary disagreed.

Preserve dates in the form the source uses. Japanese-era dates, comment deadlines, and effective dates must come from the original document text rather than from a translated or generated restatement.

## Scope and attribution

Ignore `related_auto` and treat `entities` on government records as unreliable; neither is a sound basis for selecting documents.

Persist a new change cursor only after the batch has been processed successfully. Follow each record's license and omission metadata, and always retain the official document URL alongside the portal URL.
