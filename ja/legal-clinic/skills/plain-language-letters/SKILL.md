---
name: plain-language-letters
description: >
  Reference: DEPRECATED(廃止) — 定型コレスポンデンスには `/client-letter`、
  実体的更新には `/status client` を使用してください。v2 のリビルド時に、より焦点を
  絞った 2 つのスキルに分割されました。マイグレーション用のリダイレクトとして
  残置されています。
user-invocable: false
---

# [DEPRECATED / 廃止] Plain-Language Letters → `/client-letter` および `/status client` を参照

本スキルは v2 のリビルド時に分割されました:

- **定型コレスポンデンス**(アポイント確認、書類請求、簡易な「提出しました」更新)→ `skills/client-letter/` — `/client-letter [type]` を使用

- **実体的なクライアントステータス更新** → クライアント向けモードの `skills/status/` — `/status client` を使用

両方とも CLAUDE.md のプレーンランゲージ基準(読解レベル、専門用語なし)を適用します。

完全なワークフローについては該当する SKILL.md ファイルを参照。
