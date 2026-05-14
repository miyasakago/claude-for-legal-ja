---
name: disable
description: >
  ハブ経由でインストールされたコミュニティスキルを、ファイルを削除せずに
  無効化します。コミュニティスキルを一時的に静かにしたい(「[skill] を
  disable」)、設定を残したまま hooks の発火を止めたい、または以前無効化した
  スキルを再有効化したいときに使用。
  (Use when the user wants to temporarily quiet a community skill, stop its
   hooks from firing while keeping its config, or re-enable a previously
   disabled skill.
   日本語トリガー: スキル無効化、disable、一時停止、スキル再有効化、enable)
argument-hint: "[skill name]"
---

# /disable

skill-manager リファレンス・スキルの `disable` ワークフローを、指定されたスキルに対して実行します。

disable が行うこと:

- スキルの `SKILL.md` を `SKILL.md.disabled` にリネームし、Claude がアクティブなスキルとして発見しないようにする。ファイル、参照、テンプレート、設定はそのまま残る。
- スキルが `hooks/hooks.json` で hooks を出荷している場合、そのファイルも `hooks.json.disabled` にリネームし、スキル無効化中に自動トリガーが発火しないようにする。
- アクションを `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/install-log.yaml` にログ。

セーフティルール:

1. **このハブ経由でインストールされたコミュニティスキルのみを disable する。** uninstall と同じチェック — インストールログと CLAUDE.md インストール済みテーブルを参照。
2. **ファーストパーティ・プラグインのスキルは決して disable しない。** Off-limits。
3. **リネーム前に確認。** パスを表示し、明示的な `yes` を取得する。

同じスキル名で再度コマンドを実行すれば再有効化されます — skill-manager ワークフローが無効化されたスキルを認識してリネームを反転します。

> 詳細なアンインストール、無効化、再有効化のワークフローは `skill-manager` リファレンス・スキルにあります — 実質的な作業の前にロードしてください。
