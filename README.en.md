# portal4agents-plugin

[日本語](README.md) | [English](README.en.md) | [简体中文](README.zh-CN.md)

Portable Agent Skills and a remote MCP connection for the [Chinng AI-Agent News & Advisory Portal](https://portal.chinng-lab-srv.dev/).

This repository is an [Agent Plugins 1.0.0](https://agent-plugins.org/) package. It gives compatible agents a token-efficient workflow for discovering portal records, selecting useful results, and retrieving details only when needed.

## Included capabilities

- `news-triage` — research and compare portal news without bulk-fetching bodies.
- `daily-tech-brief` — prepare recurring technology briefs from new and revised records.
- `security-watch` — monitor CVEs and security advisories incrementally.
- `agent-readiness-compare` — compare verified Agent-Readiness assessments across websites.
- `company-news-monitor` — track companies through normalized entities and content changes.

The plugin connects these skills to the portal's public read-only MCP endpoint:

```text
https://portal.chinng-lab-srv.dev/mcp
```

## Package layout

```text
.
├── plugin.json
├── mcp.json
└── skills/
    ├── news-triage/SKILL.md
    ├── daily-tech-brief/SKILL.md
    ├── security-watch/SKILL.md
    ├── agent-readiness-compare/SKILL.md
    └── company-news-monitor/SKILL.md
```

`plugin.json` and `mcp.json` target the Agent Plugins 1.0.0 schemas. Each immediate child of `skills/` is an Agent Skill whose directory name matches the `name` in its `SKILL.md` frontmatter.

## Installation

Install this repository as a plugin using an Agent Plugins 1.0.0-compatible client. Installation, approval, permissions, and credential handling are client-managed and are intentionally not encoded in this package.

The declared MCP server uses streamable HTTP and does not embed credentials or environment-specific headers. Consult the portal's MCP server instructions and tool descriptions at runtime for the current arguments and response fields.

## Content access

Portal records carry license and omission metadata. Skills in this package instruct agents to honor those fields, retain source attribution, and avoid reconstructing content that the portal withholds.

## Versioning

Releases use semantic version tags. Production consumers should pin a release tag or commit instead of following `main` directly.

## License

The plugin package is licensed under the [MIT License](LICENSE). Content returned by the portal remains subject to the license and reuse metadata attached to each record.
