# Changelog

All notable changes to this project will be documented in this file.

## [0.4.0] - 2026-11-23

### Added

- `personalized-topics` — pick topics that match an interest profile the user declared. The skill ships instructions only: the agent keeps `profile.yaml` and the seen list in its own local storage, runs one portal search per interest, and does scoring (interest weights, title bonus, linear recency bonus), same-story folding (title character-set Jaccard plus entity overlap as a candidate signal), and seen-tracking on the client side. The portal receives ordinary search queries and stores no profile or history.

## [0.3.0] - 2026-11-15

### Added

- `portal-a2ui-surfaces` — fetch, render, and drive the portal's A2UI v1.0 surfaces (`application/a2ui+json`) over unauthenticated HTTPS GET: the five surface endpoints and their parameters, the three-message envelope, the data-model paths to read records from instead of walking the component tree, the event-to-GET mapping that stands in for the absent server action endpoint, `deleteSurface` before re-creating `portal-console`, and the surface-specific constraints (`link-only` summaries, Markdown headings inside `Text`, `ui_lang` falling back to `ja`, an unscanned domain answering HTTP 200, and the search rate-limit bucket).

### Changed

- `plugin.json` — describe the A2UI surfaces and add the `a2ui` keyword.
- READMEs — list the new skill and note that it reads HTTPS GET endpoints rather than the MCP server, so the client needs a fetch capability.

## [0.2.1] - 2026-08-22

Corrections from exercising the skills against the live portal. Several instructions in 0.2.0 assumed portal behavior that the data does not support.

### Fixed

- `government-policy-watch` — government records hold no document text, so the instruction to read the body could not be followed. Trust decisions now rest on `summary_source`, with the issuing agency cross-checked against `provenance.issuer` and `agency_names`.
- `citation-pack-builder` — `license_note: full` permits reuse of what the portal stores; it does not mean the portal stores document text. Only security advisories carry real text. Entries now record `summary_source`, prefer the portal's published `citation_url`, and include upstream text only.
- `vendor-risk-brief` — identify the security family as the only source of technical evidence, and cite government documents by identifier and official URL rather than by stored text.
- `portal-related-research` — verify relations against `tags` as well as title, summary, and body; normalize malformed terms before searching; distinguish text-verified from metadata-verified relations.

### Changed

- `news-triage` — describe what a news record actually contains: `link-only` records carry no summary, and a `full` news body is a wrapper with an empty `Details` section.
- `daily-tech-brief` — window on `published_at` with an `updated_at` fallback instead of relying on published-date bounds alone, and define first-run behavior for the change feed.
- `security-watch` — report only what the upstream advisory states, and keep deployment-specific notes out of output.
- `company-news-monitor` — treat an entity match as a candidate requiring confirmation against the title, tags, or summary rather than as a finding.
- `agent-readiness-compare` — report categories as `earned/max` because neutral checks make percentages non-comparable across domains, and report an unscanned domain as unscanned rather than as a zero score.

## [0.2.0] - 2026-08-22

### Added

- `government-policy-watch` — track US Federal Register, EU EUR-Lex, and Japanese agency documents as primary sources.
- `citation-pack-builder` — emit license-respecting citation packs with explicit evidence tiers for handoff to reports, wikis, or other agents.
- `vendor-risk-brief` — assemble one subject's picture across the security, news, and government families, separating confirmed impact from inference.
- `portal-related-research` — explore outward from one record through verified entities and keywords, classifying and evidencing each relation.

## [0.1.0] - 2026-08-22

### Added

- Agent Plugins 1.0.0 manifest.
- Public streamable HTTP MCP server configuration.
- Five Agent Skills for news triage, daily technology briefs, security monitoring, Agent-Readiness comparison, and company news monitoring.

[0.4.0]: https://github.com/chinng-inta/portal4agents-plugin/releases/tag/v0.4.0
[0.3.0]: https://github.com/chinng-inta/portal4agents-plugin/releases/tag/v0.3.0
[0.2.1]: https://github.com/chinng-inta/portal4agents-plugin/releases/tag/v0.2.1
[0.2.0]: https://github.com/chinng-inta/portal4agents-plugin/releases/tag/v0.2.0
[0.1.0]: https://github.com/chinng-inta/portal4agents-plugin/releases/tag/v0.1.0
