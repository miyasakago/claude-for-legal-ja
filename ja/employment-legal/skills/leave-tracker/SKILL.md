---
name: leave-tracker
description: >
  期日アラートと必要判断について開設中の休職をチェック。行動を要する休職
  のみを浮上させ、理由を説明 — ステータスボードではない。週次で、または
  弁護士が今後の指定、証明、枯渇期日のある休職を知る必要があるときに使用。
  (Check open leaves for deadline alerts and required decisions. Surfaces only
  the leaves that require an action and explains why — not a status board.
  Use weekly, or whenever the attorney needs to know which leaves have
  upcoming designation, certification, or exhaustion deadlines. 日本語
  トリガー: 「休職トラッカー」「開いている休職」「休職期日チェック」
  「FMLA ステータス」「休職をチェック」)
argument-hint: "[no arguments — works from HRIS or leave-register.yaml]"
---

# /leave-tracker

法定期限のある開設中の休職すべてをチェックし、判断または行動を要するもののみを浮上。ステータスボードではない — 何をすべきかとその理由を伝える。

## 指示

1. `leave-tracker` エージェントをロードし、完全なワークフローを実行。

2. HRIS が接続されておらず `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/leave-register.yaml` が存在しない場合、弁護士に休職スプレッドシートをアップロードするか `/employment-legal-ja:log-leave` でエントリを追加するよう促す。

3. 行動を要する休職のみアラート。クリーンな休職は 1 行で要約。

## 例

```
/employment-legal-ja:leave-tracker
```

週次で実行 — 月曜朝のリマインダーを設定して `/employment-legal-ja:leave-tracker` を呼び出す。自動スケジューリングには別途連携(カレンダーリマインダー、cron ジョブ等)が必要; Claude Code エージェントは自己スケジュールしない。
