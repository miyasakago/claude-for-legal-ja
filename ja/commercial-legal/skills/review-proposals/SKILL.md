---
name: review-proposals
description: >
  playbook-monitor エージェントが提示した保留中のプレイブック更新提案を順にレビューし、
  承認(または却下)してプラクティスプロファイルに反映します。playbook-monitor エージェントが
  提案を提示したとき、ユーザーが "review playbook proposals"、"what playbook updates are pending" と
  言ったとき、または乖離に基づくプレイブック変更を順に確認したいときに使用します。
  日本語トリガー例: プレイブック提案レビュー、保留中のプレイブック更新、提案を確認、プレイブックを更新。
argument-hint: "[引数は不要 — 保留中の proposals ファイルから動作します]"
---

# /review-proposals

モニターエージェントから保留中のプレイブック更新提案を順に確認し、承認された変更を `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` に適用します。

## 指示

1. **playbook-monitor エージェントをロード**して、Step 5(レビュー・承認フロー)を実行します。

2. **proposals ファイルが存在しない、または空の場合:** *「保留中の提案はありません。プレイブックは最新の状態です。」* と応答してください。それ以上は進めません。

3. **提案を 1 件ずつ提示。** 各提案について、提案ブロック全体を表示し、Accept(承認)、Reject(却下)、Edit(編集)、Defer(保留)の 4 つのオプションを提示します。

4. **Accept または Edit の場合:** 書き込みの前に `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` への正確な差分を表示します。弁護士が明示的に確認した後にのみ適用してください。

5. **Reject または Defer の場合:** 判断をログに記録します。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` は変更しないでください。

6. **すべての提案が処理された後:** 変更点のサマリーを表示し、proposals ファイルをアーカイブします。

## 例

```
/commercial-legal-ja:review-proposals
```

```
/commercial-legal-ja:review-proposals
(playbook-monitor が通知した後に自動的に走ります)
```
