---
name: uninstall
description: >
  ハブ経由でインストールされたコミュニティスキルをアンインストールします。
  ファイル削除前に確認し、ファーストパーティ・プラグインのスキルには触れず、
  すべてのアクションをログします。ユーザーがコミュニティスキルを完全に
  削除したいとき(「[skill] をアンインストール」「このスキルを削除」)、
  単に disable するのではない場合に使用。
  (Use when the user wants to fully remove a community skill ("uninstall
   [skill]", "remove this skill") rather than just disable it.
   日本語トリガー: アンインストール、削除、uninstall、スキル削除、remove)
argument-hint: "[skill name]"
---

# /uninstall

skill-manager リファレンス・スキルの `uninstall` ワークフローを、指名されたスキルに対して実行します。

セーフティルール:

1. **このハブ経由でインストールされたコミュニティスキルのみをアンインストール。** `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/install-log.yaml` と CLAUDE.md のインストール済みスターターパック・テーブルをチェック。スキルがそこに記録されていなければ、拒否してユーザーに伝える。
2. **ファーストパーティ・プラグインのスキルを決してアンインストールしない。** claude-for-legal-ja に同梱される 12 個のコアプラグインは、このコマンドの off-limits。指名されたスキルがそれらのプラグイン内のパスに解決される場合、拒否。
3. **ファイル削除前に確認。** 削除されるすべてのパスをユーザーに表示。明示的な `yes` でのみ進む。
4. **アンインストールをログ。** `install-log.yaml` にアクション `uninstall` とタイムスタンプを追記し、監査トレイルが無傷であるようにする。

ユーザーがスキルの実行を止めたいがファイルを残したい場合(例: 後で再有効化、または設定を保持)、代わりに `/legal-builder-hub-ja:disable` を提案。

> 詳細なアンインストール、無効化、再有効化のワークフローは `skill-manager` リファレンス・スキルにあります — 実質的な作業の前にロードしてください。
