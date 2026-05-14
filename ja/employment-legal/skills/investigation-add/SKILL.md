---
name: investigation-add
description: >
  開設中の調査にデータを追加 — 文書、インタビューノート、観察。文書化された
  プル基準に対してバッチを処理し、重要な項目を浮上させ、カバレッジ検証用に
  レビューしたものすべてをログ。開設中の調査に新しい証拠、インタビュー
  ノート、または文書提出が入ってくるときに使用。
  (Add data to an open investigation — documents, interview notes, or
  observations. Use when new evidence, interview notes, or document
  productions come in for an open investigation. 日本語トリガー: 「調査に
  追加」「インタビューノートを追加」「文書を追加」「証拠を追加」)
argument-hint: "[matter name or slug, then paste or attach data]"
---

# /investigation-add

開設中の調査ログにデータを追加。文書化されたプル基準を使って文書バッチを処理し、重要な項目を浮上させ、カバレッジ検証用にレビューしたものすべてをログ。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` をロード。
2. `internal-investigation` 参照スキルをロードし、モード 2(データ追加)を実行。
3. 処理後、浮上比率と浮上項目リストを表示。
4. データがチェックリスト項目をカバーするなら、ソースチェックリストを更新するよう促す。

## 例

```
/employment-legal-ja:investigation-add [matter name]
[paste interview notes]
```

```
/employment-legal-ja:investigation-add [matter name]
[attach email export]
```

> 詳細な針探しプロセス、ログエントリ・フォーマット、浮上比率ルール、ソースチェックリスト追跡は `internal-investigation` 参照スキルにあります — 実質的な作業前にロードしてください。
