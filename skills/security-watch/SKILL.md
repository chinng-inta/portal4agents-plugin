---
name: security-watch
description: Monitor CVEs and security advisories through the Chinng AI-Agent Portal. Use for incremental vulnerability checks, package watchlists, and actionable security summaries.
---

# Security watch

Use the `portal` MCP server declared by this plugin.

1. Search or list the security family for watched packages, vendors, products, and CVE or OSV identifiers.
2. For recurring runs, combine newly published records with the portal's change feed so revised advisories are not missed.
3. Retrieve details only for relevant records and preserve the advisory's identifiers, affected-version evidence, attribution, and source link.
4. Separate confirmed impact from inference. Do not extend affected version ranges beyond the advisory data.

Persist a new change cursor only after successful processing, and follow each record's license and reuse metadata.
