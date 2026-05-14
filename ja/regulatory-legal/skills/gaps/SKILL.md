---
name: gaps
description: 未解決ギャップトラッカー — フラグ済みで未クローズのもの。ユーザーが "what gaps are open", "gap tracker", "remediation status" と尋ねたとき、または追跡中ギャップを close(--close GAP-ID)または risk-accept(--accept GAP-ID)したいときに使用。Use when the user asks "what gaps are open", "gap tracker", "remediation status", or wants to close (--close GAP-ID) or risk-accept (--accept GAP-ID) a tracked gap. 日本語トリガー: 未解決ギャップ、ギャップトラッカー、remediation 状況。
argument-hint: "[optional: --close GAP-ID | --accept GAP-ID]"
---

# /gaps

1. ギャップトラッカー `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/gap-tracker.yaml` を読む。
2. `--close` の場合: resolution note と共にギャップを closed としてマーク。
3. `--accept` の場合: risk-acceptance の rationale と acceptor を記録、status → risk-accepted。
4. それ以外: 未解決ギャップを age と materiality でレポート。

> 詳細なトラッカースキーマ、ステータスレポートフォーマット、オーナー通知ロジック(per-send 確認、例外なし)、リマインダー cadence、close/risk-accept モード、consequential-action gate は **gap-surfacer** リファレンススキルにある — 実質作業前にロード。
