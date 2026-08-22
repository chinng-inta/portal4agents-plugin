# portal4agents-plugin

[日本語](README.md) | [English](README.en.md) | [简体中文](README.zh-CN.md)

用于访问 [Chinng AI 智能体新闻与公告门户](https://portal.chinng-lab-srv.dev/)的可移植 Agent Skills 和远程 MCP 连接配置。

本仓库是符合 [Agent Plugins 1.0.0](https://agent-plugins.org/) 规范的软件包。它为兼容的智能体提供节省令牌的工作流程：发现门户记录、筛选有用结果，并仅在需要时获取详细内容。

## 包含的功能

- `news-triage` — 无需批量获取正文，即可研究和比较门户新闻。
- `daily-tech-brief` — 根据新增和已更新的记录制作定期技术简报。
- `security-watch` — 增量监控 CVE 和安全公告。
- `agent-readiness-compare` — 比较不同网站经过验证的 Agent-Readiness 评估。
- `company-news-monitor` — 通过规范化实体和内容变更跟踪公司动态。
- `government-policy-watch` — 将美国 Federal Register、欧盟 EUR-Lex 和日本省厅文件作为一手资料进行追踪。
- `citation-pack-builder` — 输出明确标注许可证和证据层级的引用包。
- `vendor-risk-brief` — 跨 CVE、企业新闻和政府文件汇总单个供应商或产品的风险画像。
- `portal-related-research` — 从单条记录出发，沿经过验证的实体探索关联内容。

本插件将这些 Skill 连接到门户公开的只读 MCP 端点：

```text
https://portal.chinng-lab-srv.dev/mcp
```

## 软件包结构

```text
.
├── plugin.json
├── mcp.json
└── skills/
    ├── news-triage/SKILL.md
    ├── daily-tech-brief/SKILL.md
    ├── security-watch/SKILL.md
    ├── agent-readiness-compare/SKILL.md
    ├── company-news-monitor/SKILL.md
    ├── government-policy-watch/SKILL.md
    ├── citation-pack-builder/SKILL.md
    ├── vendor-risk-brief/SKILL.md
    └── portal-related-research/SKILL.md
```

`plugin.json` 和 `mcp.json` 使用 Agent Plugins 1.0.0 的 schema。`skills/` 下的每个直接子目录都是一个 Agent Skill，其目录名必须与 `SKILL.md` frontmatter中的 `name` 一致。

## 安装

请使用兼容 Agent Plugins 1.0.0 的客户端，将本仓库作为插件安装。安装、审批、权限和凭据管理均由客户端负责，因此不会在本软件包中定义。

声明的 MCP 服务器使用 streamable HTTP，并且不嵌入凭据或环境专用 header。有关当前参数和响应字段，请在运行时参阅门户的 MCP server instructions 和 tool descriptions。

## 内容访问

门户记录包含许可证和内容省略原因的元数据。本软件包中的 Skill 会指示智能体遵守这些字段、保留来源归属，并避免重建门户未提供的内容。

## 版本管理

发布版本使用 Semantic Versioning 标签。生产环境应固定到发布标签或 commit，而不是直接跟踪 `main`。

## 许可证

本插件软件包采用 [MIT License](LICENSE)。门户返回的内容仍受每条记录所附许可证和再利用条件的约束。
