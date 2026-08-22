---
name: vendor-risk-brief
description: Build a single-vendor or single-product risk brief that spans CVE advisories, company news, and government documents in the Chinng AI-Agent Portal. Use for supplier assessment, dependency review, and procurement or renewal decisions.
---

# Vendor risk brief

Use the `portal` MCP server declared by this plugin.

This skill assembles one picture of a vendor, product, or dependency from all three portal content families. Its value is the cross-family view, so state explicitly which families produced evidence and which returned nothing.

1. Fix the subject before searching. Record the vendor name, the product or package name, and any known aliases, then search each family with those terms rather than with one blended query.
2. Gather the security family for CVE and advisory records naming the subject. These records carry real advisory text — impact, patches, workarounds, affected and fixed versions, upstream references — and are the only family that supplies technical evidence. Retrieve their bodies.
3. Gather the news family for company and product records. Most external news is `link-only`, so treat these as dated pointers with metadata unless the record permits more.
4. Gather the government family for rules, notices, and enforcement or procurement documents naming the subject. These records hold no document text; report the official abstract only when `summary_source` is `official`, and otherwise cite the document by its identifiers and official URL.
5. Produce a brief that separates confirmed impact from inference, and that gives each finding a date, a family, and a source link.

## Evidence discipline

Do not extend affected version ranges beyond what an advisory states. If an advisory names specific versions, the brief names those versions; an untested adjacent version is unknown, not affected.

Do not let one family's evidence upgrade another's. A news report of an incident is not confirmation of a CVE's exploitability, and a government document naming a vendor is not evidence of a security defect.

Name coincidence is a real hazard in cross-family search. Confirm that a matched record concerns the subject before including it, and drop matches that rest only on an entity or keyword field without support from the title or body.

Ignore `related_auto`. Treat `entities` as a search hint whose reliability varies by source, never as proof that a record concerns the subject.

## Reporting

Report the absence of findings as a finding. State the families and time window searched, so a reader can tell a clean subject from an unexamined one.

Follow each record's license and omission metadata, and retain both the portal link and the original source link for every item cited.
