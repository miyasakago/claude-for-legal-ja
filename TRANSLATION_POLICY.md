# 翻訳ポリシー (Translation Policy)

本リポジトリ `claude-for-legal` の日本語翻訳版に関する方針を定めるドキュメントです。日本語訳は `ja/` ディレクトリ配下に配置し、英語原文は本リポジトリのルートに温存しています。

## 1. 目的とスコープ

- **目的:** 日本語話者の法務担当者・弁護士・ロースクール学生が、本リポジトリのプラグイン群を母語で読み・使えるようにする。
- **対象:** Markdown 本文、各種設定ファイルの人間可読フィールド(`description`, `title` 等)。
- **米国法のローカライズはしない:** 本リポジトリは米国法務実務(US in-house / firm / clinic)を題材としており、日本法へのローカライズ(条文の置き換え、判例の差し替え等)は行いません。米国法のドクトリンや判例は**翻訳せずに日本語注釈を添える**スタイルで提示します。日本法務向けの独立した実装は将来の別プロジェクトに委ねます。

## 2. 翻訳する対象

| カテゴリ | 内容 |
|---|---|
| Markdown 本文 | すべて翻訳 |
| `SKILL.md` フロントマターの `description` | 翻訳(トリガー精度を上げるため日本語キーワードも追記) |
| `plugin.json` の `description` | 翻訳 |
| `.mcp.json` の `description` / `title` | 翻訳(`url`, `type` 等識別子はそのまま) |
| YAML 中の人間可読な値(例: コメント、ラベル) | 翻訳 |
| 文書テンプレート | 翻訳 |

## 3. 翻訳しない対象

| カテゴリ | 理由 |
|---|---|
| `LICENSE` | ライセンス原文の改変は不可 |
| ファイル名・ディレクトリ名 | パス互換性のため英語のまま |
| スキル slug(URL パス部分) | 同上、コマンド `/plugin:skill-name` を維持 |
| JSON/YAML のキー(`name`, `version`, `mcpServers` 等) | 構造の互換性維持 |
| URL、API エンドポイント、識別子 | 動作維持 |
| 米国判例名(*Alice*, *Daubert* 等) | 固有名詞のため。必要に応じて日本語注釈を添える |
| 米国条文・規則の名称(FRCP 26(b)(3), GDPR Art. 58 等) | 同上 |
| Python/Shell コードの内部文字列 | 動作維持 |
| `.github/workflows/*.yaml` | CI 動作維持 |
| Git の commit message / branch 名 | 既存のものは温存 |

## 4. 米国法ドクトリンの扱い

米国特有の法概念は**英語表記を保持し、日本語の補足注釈を添える**方針:

- 初出時: `Attorney Work Product(米国訴訟手続上の弁護士成果物の保護法理)`
- 2回目以降: `Attorney Work Product` のまま、または `弁護士成果物` と表記。文脈に応じて判断。
- 日本に対応概念が存在しない場合は英語表記を優先(無理に日本語化しない)。

例:
- `FMLA` → `FMLA(Family and Medical Leave Act / 米国家族・医療休暇法)`
- `Privilege` → `Privilege(秘匿特権)` または「弁護士・依頼者間の秘匿特権」
- `DPA` → `DPA(Data Processing Agreement / データ処理契約)`

## 5. プラグインの命名規則

日本語版プラグインは英語版と共存できるよう、プラグイン名に `-ja` 接尾辞を付与:

| 英語版 | 日本語版 |
|---|---|
| `commercial-legal` | `commercial-legal-ja` |
| `privacy-legal` | `privacy-legal-ja` |
| `product-legal` | `product-legal-ja` |
| ... | ... |

**ただし、スキル slug(`SKILL.md` のあるディレクトリ名)は英語のまま維持します。** コマンドは以下のような形式になります:

```
/commercial-legal-ja:review        # 日本語版
/commercial-legal:review           # 英語版(原文)
```

## 6. 翻訳スタイル

- **敬体(です・ます)** を基本とする。ただし、見出し・箇条書きの短い項目は体言止めも可。
- **専門用語は GLOSSARY.md に従う**(訳語統一)。
- **直訳より自然な日本語**を優先(意味が変わらない範囲で語順・構造を変えてよい)。
- **`>` ブロック引用、表、コードブロック、Markdown 構文** は原文の構造を維持する。
- **インラインタグ**(例: `[verify]`, `[review]`, `[model knowledge — verify]`)は**英語のまま維持**する。これらは Claude がパースする識別子のため、変更すると動作に影響する。
- **コマンド名・スキル名・ファイルパス**は英語のまま、必要に応じて括弧内で意味を添える。
- **Markdown のリンクテキスト**は翻訳するが、リンク先 URL はそのまま。

## 7. フロントマターの翻訳

`SKILL.md` のフロントマター例:

```yaml
---
name: review                                    # ← 英語のまま(slug)
description: >
  契約書をプレイブックに照らしてレビューします。
  (Use when reviewing a contract, MSA, NDA, SaaS agreement...
   日本語トリガー: 契約レビュー、契約書チェック、契約書を見て)
---
```

- `name` フィールドは英語のまま。
- `description` は日本語化。**英語トリガー語も保持**(英語で話しかけるユーザーに対応するため)。**日本語トリガー語を追加**してマッチ精度を上げる。

## 8. ディレクトリ構造

```
claude-for-legal-ja/
├── (英語原文ファイル群 — 一切変更しない)
├── ja/                                    ← 日本語版はすべてここに集約
│   ├── README.md
│   ├── QUICKSTART.md
│   ├── CONNECTORS.md
│   ├── CONTRIBUTING.md
│   ├── CODE_OF_CONDUCT.md
│   ├── .claude-plugin/marketplace.json   ← 日本語マーケットプレイス
│   ├── commercial-legal/...               ← 日本語版プラグイン本体
│   ├── ... (各プラグイン)
│   ├── external_plugins/
│   ├── managed-agent-cookbooks/
│   ├── references/
│   └── scripts/
├── TRANSLATION_POLICY.md                  ← 本ファイル
└── GLOSSARY.md                            ← 用語集
```

## 9. インストール方法(日本語版)

英語版のドキュメントの手順を日本語に置き換えるだけです。マーケットプレイスとして `ja/` ディレクトリを追加し、`-ja` 接尾辞付きのプラグインをインストールします。

```
/plugin marketplace add /path/to/claude-for-legal/ja
/plugin install commercial-legal-ja@claude-for-legal-ja
```

英語版と日本語版を同時にインストールすることも可能です(プラグイン名が異なるため)。

## 10. メンテナンス

- 英語原文に変更があった場合、対応する日本語訳ファイルを更新する。
- `GLOSSARY.md` の用語は段階的に追加・改訂する。新規用語が出てきた場合は追加してから翻訳に着手する。
- 翻訳の不整合・誤訳を見つけた場合は、まず `GLOSSARY.md` を確認し、必要に応じて修正する。

## 11. 法的免責事項

- 翻訳版の出力も**すべて弁護士レビューを前提とする下書き**であり、法的助言ではありません。
- 翻訳の正確性は保証しません。誤訳・意訳のおそれがあるため、最終判断は原文(英語版)を参照してください。
- 翻訳プロセスで日本法に関する解釈・実装は加えていません。日本法務に適用する場合は、日本法に精通した専門家の助言を別途得てください。
