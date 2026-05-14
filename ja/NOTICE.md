# NOTICE — 派生物に関する告知

本ディレクトリ(`ja/`)に含まれるコンテンツは、Anthropic PBC が公開する [`anthropics/claude-for-legal`](https://github.com/anthropics/claude-for-legal) リポジトリの**非公式日本語翻訳版**(派生物 / Derivative Work)です。

本 NOTICE は [Apache License, Version 2.0](LICENSE) の Section 4(b) に基づき、派生物であることおよび変更内容を明示するために作成されました。

---

## 1. 派生物としての位置づけ

- **元著作物(Upstream):** https://github.com/anthropics/claude-for-legal
- **元著作権者:** Anthropic PBC(Copyright 2026 Anthropic PBC)
- **元ライセンス:** Apache License, Version 2.0
- **本派生物の管理者:** miyasakago(個人による任意翻訳)
- **本派生物のライセンス:** Apache License, Version 2.0(元と同一)
- **公式性:** **本翻訳版は Anthropic PBC が公認または保証するものではありません。**

## 2. 加えた変更点(Modifications)

Apache 2.0 Section 4(b) に基づき、原本に対して加えた主な変更を以下に明示します:

### コンテンツの変更
- すべての Markdown 本文を**日本語に翻訳**(原文のニュアンスを保ちつつ、自然な日本語表現に置き換え)
- スキル `SKILL.md` フロントマターの `description` フィールドを翻訳(英語のトリガー語を保持し、日本語のトリガー語を追記)
- `plugin.json` の `description` フィールドを翻訳
- `.mcp.json` の `description` / `title` フィールドを翻訳

### 構造の変更
- プラグイン名にすべて **`-ja` 接尾辞**を付与(例: `commercial-legal` → `commercial-legal-ja`)
  - 英語版と日本語版の共存を可能にするため
- 本文中のコマンド参照を `-ja` 形式に統一(例: `/commercial-legal:review` → `/commercial-legal-ja:review`)
- 設定パスを `~/.claude/plugins/config/claude-for-legal/` → `~/.claude/plugins/config/claude-for-legal-ja/` に変更
- マーケットプレイス名を `claude-for-legal` → `claude-for-legal-ja` に変更

### 変更しなかったもの(原本のまま保持)
- ファイル名、ディレクトリ名、スキル slug
- JSON/YAML のキー名、URL、API エンドポイント、識別子
- LICENSE ファイル本文
- 米国法・EU 法等の固有の法令引用・判例名(原文維持し日本語注釈を添える方式)
- Python/Shell スクリプトのコード(`scripts/` 配下)
- `.github/workflows/cla.yaml`
- インラインタグ(`[verify]`, `[review]`, `[PLACEHOLDER]` 等)— Claude がパースする識別子のため

詳細は [`TRANSLATION_POLICY.md`](../TRANSLATION_POLICY.md) を参照してください。

## 3. 商標に関する注意

Apache License 2.0 Section 6 のとおり、**本ライセンスは商号・商標・サービスマーク・商品名の使用権を付与するものではありません**。

本翻訳版で言及される以下の名称は各社の商標であり、本派生物はそれらの商標の使用許諾を受けたものではありません。記述上の言及(nominative fair use)として参照しているにとどまります:

- **Anthropic**, **Claude**, **Claude Code**, **Claude Cowork**, **Claude for Legal** — Anthropic PBC の商標
- **CoCounsel**, **CoCounsel Legal**, **Westlaw**, **Practical Law** — Thomson Reuters の商標
- **Ironclad**, **DocuSign**, **iManage**, **Everlaw**, **Box**, **Slack**, **Google Drive**, **Linear**, **Atlassian**, **Jira**, **Asana**, **Aurora**, **Trellis**, **Definely**, **Solve Intelligence**, **TopCounsel**, **Lawve AI**, **Courtroom5**, **Descrybe** — 各社の商標

## 4. 免責(Disclaimer)

本派生物は Apache License 2.0 第 7 条のもと、**"AS IS"(現状有姿)で、いかなる種類の明示または黙示の保証もなく**提供されます。

加えて、以下の点に特にご留意ください:

- **翻訳の正確性は保証しません。** 誤訳・意訳・解釈の相違の可能性があるため、法的判断にあたっては必ず英語原文(upstream)を参照してください。
- **本翻訳版の出力も、原本同様、すべて弁護士レビューを前提とする下書きであり、法的助言ではありません。**
- 本派生物は米国法務実務を題材としており、**日本法へのローカライズは行っていません**。米国法のドクトリンは原文表記を保持し、必要に応じて日本語注釈を添えています。日本法務に適用する場合は、日本法に精通した専門家の助言を別途得てください。
- 本派生物に起因または関連して生じたいかなる損害についても、翻訳者・配布者は責任を負いません。

## 5. 元著作物の信頼性

本翻訳版に問題を見つけた場合、まず元著作物(upstream)で同じ問題が存在するかを確認してください。**翻訳起因の問題**は本リポジトリ(`miyasakago/claude-for-legal-ja`)の Issue で、**原本起因の問題**は upstream の [Issue](https://github.com/anthropics/claude-for-legal/issues) で報告するのが適切です。

## 6. 連絡先

- **翻訳版に関する問い合わせ:** [miyasakago/claude-for-legal-ja](https://github.com/miyasakago/claude-for-legal-ja) の Issue
- **元著作物に関する問い合わせ:** [anthropics/claude-for-legal](https://github.com/anthropics/claude-for-legal) の Issue
- **Anthropic PBC への問い合わせ:** Anthropic の公式チャネルを利用してください
- **CoCounsel Legal(Thomson Reuters)のサポート:** cocounselsupport@tr.com

---

*This NOTICE is provided under Apache License 2.0 Section 4(b) to identify modifications from the upstream work.*
