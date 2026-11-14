# portal4agents-plugin

[日本語](README.md) | [English](README.en.md) | [简体中文](README.zh-CN.md)

[Chinng AIエージェント向けニュース＆アドバイザリーポータル](https://portal.chinng-lab-srv.dev/)を利用するための、ポータブルなAgent SkillsとRemote MCP接続設定です。

このリポジトリは[Agent Plugins 1.0.0](https://agent-plugins.org/)準拠のパッケージです。対応するエージェントに、ポータルのレコードを発見し、必要な結果を選び、必要な場合だけ詳細を取得するトークン効率のよいワークフローを提供します。

## 収録機能

- `news-triage` — 本文を一括取得せず、ポータルのニュースを調査・比較します。
- `daily-tech-brief` — 新規・更新レコードから定期的な技術ニュースブリーフを作成します。
- `security-watch` — CVEとセキュリティアドバイザリーを差分監視します。
- `agent-readiness-compare` — Webサイト間で検証済みのAgent-Readiness評価を比較します。
- `company-news-monitor` — 正規化されたエンティティとコンテンツ変更から企業を追跡します。
- `government-policy-watch` — 米国Federal Register・EU EUR-Lex・日本の省庁文書を一次資料として追跡します。
- `citation-pack-builder` — ライセンスと根拠段階を明示した引用パックを出力します。
- `vendor-risk-brief` — 1つのベンダーや製品をCVE・企業ニュース・政府文書の3系統で束ねます。
- `portal-related-research` — 1件のレコードを起点に、検証済みのエンティティを辿って関連を探索します。
- `portal-a2ui-surfaces` — ポータルのA2UI v1.0サーフェスを取得・描画し、イベントをGETに変換して操作します。

このプラグインは、これらのSkillをポータルの公開読み取り専用MCPエンドポイントへ接続します。

```text
https://portal.chinng-lab-srv.dev/mcp
```

`portal-a2ui-surfaces` のみ、MCPではなく認証不要のHTTPS GET（`application/a2ui+json`）を使用するため、クライアント側のフェッチ機能が必要です。

## パッケージ構成

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
    ├── portal-related-research/SKILL.md
    └── portal-a2ui-surfaces/SKILL.md
```

`plugin.json` と `mcp.json` はAgent Plugins 1.0.0のスキーマを対象としています。`skills/` 直下の各ディレクトリが1つのAgent Skillであり、ディレクトリ名は `SKILL.md` のfrontmatterにある `name` と一致します。

## インストール

Agent Plugins 1.0.0対応クライアントを使用して、このリポジトリをプラグインとしてインストールしてください。インストール、承認、権限、資格情報の管理はクライアントが担当するため、このパッケージでは定義していません。

宣言されたMCPサーバーはstreamable HTTPを使用し、資格情報や環境固有のヘッダーを埋め込みません。現在の引数とレスポンスフィールドについては、実行時にポータルのMCP server instructionsとtool descriptionsを参照してください。

## コンテンツへのアクセス

ポータルのレコードには、ライセンスと省略理由のメタデータが含まれます。このパッケージのSkillは、それらのフィールドを守り、出典を保持し、ポータルが非公開にした内容を復元しないようエージェントへ指示します。

## バージョン管理

リリースにはSemantic Versioningのタグを使用します。本番環境では `main` を直接追跡せず、リリースタグまたはcommitを固定してください。

## ライセンス

プラグインパッケージは[MIT License](LICENSE)で提供します。ポータルから返されるコンテンツには、各レコードに付与されたライセンスと再利用条件が引き続き適用されます。
