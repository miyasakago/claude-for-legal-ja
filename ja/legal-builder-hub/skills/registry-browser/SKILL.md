---
name: registry-browser
description: >
  監視対象レジストリでコミュニティ法務スキルを検索し、説明付きでマッチを
  表示し、インストール前に完全な SKILL.md を表示することを提案します。
  ユーザーが「ブラウズ」「スキル検索」「[topic] 用のスキル」「[topic] 用に
  何があるか」と言ったとき、またはウォッチリストに新しいレジストリを
  追加したいときに使用。
  (Use when the user says "browse", "search skills", "find a skill for",
   "what's out there for", or wants to add a new registry to the watchlist.
   日本語トリガー: ブラウズ、スキル検索、スキル一覧、レジストリ追加、スキル探す)
argument-hint: "[検索クエリ]"
---

# /registry-browser

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` をロード → 監視対象レジストリ。
2. 下記のワークフローを使用。
3. 各レジストリを検索。説明付きのマッチを表示。
4. 任意のマッチに対し、完全な SKILL.md の表示を提案。

---

## 目的

監視対象レジストリ横断でスキルを見つける。検索、プレビュー、決定。

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` → 監視対象レジストリリスト。

## ワークフロー

### ステップ 1: レジストリ・インデックスを取得

各監視対象レジストリについて:

- GitHub リポジトリ: `skills/` ディレクトリ一覧と各 `SKILL.md` フロントマター(name + description)を取得。
- マーケットプレイス型レジストリ: インデックスを取得。

ブラウズが速くなるようインデックスをローカル(`references/registry-cache.json`)にキャッシュ。7 日以上古ければ、または要求時にリフレッシュ。

### ステップ 2: 検索

クエリをスキル名と説明にマッチさせる。シンプルなキーワードマッチで十分 — 小さいのでファジー検索はやり過ぎ。

また: レジストリがそのようにスキルを整理しているなら、カテゴリでブラウズ。

### ステップ 3: マッチを表示

```markdown
## Search: "[query]"

**[M] レジストリ横断で [N] スキルを見つけました:**

### [skill-name]
**From:** [registry name]
**Description:** [from frontmatter]
[完全 SKILL.md を表示] [インストール]

### [skill-name]
[...]
```

### ステップ 4: プレビュー

「完全 SKILL.md を表示」: ファイル全体を取得して表示。ユーザーはインストールを決める前に読みます。サプライズなし。

### ステップ 5: レジストリを追加

ユーザーがウォッチリストにない URL を持っている場合:

1. 取得し、スキルリポジトリであることを検証(`skills/` または `.claude-plugin/` あり)
2. 中身を表示
3. 確認時に `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` → 監視対象レジストリに追加

## デフォルトレジストリ

- **lpm-skills** — 14 個の legal project management スキル。プラクティスエリア非依存。良い出発点。
- エコシステムの成長に伴い、他のレジストリが追加される余地あり。

## このスキルが NOT 行うこと

- 何かをインストール。ブラウズする。インストールは skill-installer。
- スキルの評価・レビュー。SKILL.md を見せて、ユーザーが判断する。
- インターネット全体を検索。監視対象レジストリのみ。
