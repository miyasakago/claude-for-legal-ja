---
name: investigation-open
description: >
  新規内部調査案件を開設 — インテイクを実行し、ソースチェックリストを生成し、
  永続調査ログを作成。苦情または申立が入ってきて、弁護士が秘匿特権下の調査
  ワークスペースを立ち上げる必要があるときに使用。
  (Open a new internal investigation matter — runs intake, generates the sources
  checklist, and creates the persistent investigation log. Use when a complaint
  or allegation comes in and the attorney needs to stand up a privileged
  investigation workspace. 日本語トリガー: 「調査を開設」「調査を開始」
  「内部調査を立ち上げる」「ハラスメント調査を開始」)
argument-hint: "[brief description of the allegation]"
---

# /investigation-open

新規調査案件を開設 — インテイクを実行し、ソースチェックリストを生成し、永続調査ログを作成。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` をロード。
2. `internal-investigation` 参照スキルをロードし、モード 1(開設)を実行。
3. 同じ slug の案件が既に存在する場合、上書き前に警告。

## 例

```
/employment-legal-ja:investigation-open
オースティン支社のマネージャーに対するハラスメント苦情が提出されました。
```

```
/employment-legal-ja:investigation-open
(スキルが詳細を尋ねます)
```

> 詳細なインテイク、特権形成要件、ソースチェックリスト、ログテンプレートは `internal-investigation` 参照スキルにあります — 実質的な作業前にロードしてください。
