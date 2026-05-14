---
name: expansion-update
description: >
  進行中の国際展開プロジェクトのステータスを更新 — 現在ブロック解除されたもの
  を再計算し、期限超過のものをフラグし、次の優先事項を表面化します。前回
  セッション以降に作業が発生し、展開トラッカーが現状を反映する必要があると
  きに使用。
  (Update the status of an in-progress international expansion project —
  recalculates what is now unblocked, flags anything overdue, and surfaces
  the next priorities. Use when work has happened since the last session and
  the expansion tracker needs to reflect the current state. 日本語トリガー:
  「展開ステータス更新」「進捗更新」「[国] 展開の状況」「トラッカー更新」)
argument-hint: "[country name]"
---

# /expansion-update

開設中の展開トラッカーに戻り、前回セッション以降に発生したことに基づいて項目ステータスを更新。現在ブロック解除されたものを再計算し、期限超過のものをフラグし、次の優先事項を表面化。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` をロード。

2. トラッカーファイルを特定: `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/expansion-[slug].yaml`。存在しない場合、応答: 「[country] の展開トラッカーが見つかりません。`/employment-legal-ja:expansion-kickoff [country]` で開始してください。」

3. トラッカーを読み、現在状態を表示:

```
[Country] Expansion — last updated [date]
Open: [N] | In progress: [N] | Done: [N] | Blocked: [N]

Next priorities (open items with earliest due dates or highest-dependency):
  [item] — owner: [owner]
  [item] — owner: [owner]
  [item] — owner: [owner]
```

4. 単一のプロンプトで更新を求める — 各項目について 1 つずつ尋ねないこと:

   > 前回見たときから動いた項目はどれですか? 何が変わったか教えてください(例: 「EOR 決定済み — Deel に決定」「外部弁護士起用済み — 木曜日に通話予定」「PE 分析未解決、税務待ち」)。新規項目を追加したり期日を変更したりすることもできます。

5. 更新をトラッカーファイルに適用。新たに `done` とマークされた項目について、他の項目をブロック解除するかをチェックし、現在実行可能としてフラグ。

6. `open` または `in-progress` の項目で期日が過ぎているものは、フラグ:

```
⚠️ Overdue: [item] — was due [date], owner: [owner]
```

7. 更新されたトラッカーを書き込む。確認:

```
Tracker updated — [N] items closed, [N] still open.
Next priority: [top open item].
```

## 例

```
/employment-legal-ja:expansion-update Germany
```

```
/employment-legal-ja:expansion-update
(複数のトラッカーが存在する場合、どの国かを尋ねます)
```

> 詳細なトラッカースキーマ、項目ステータスルール、依存関係ロジックは `international-expansion` 参照スキルにあります — 実質的な作業前にロードしてください。
