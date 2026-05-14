---
name: session
description: >
  科目を絞った N 問の学習セッションを実行 — MBE、エッセイ、またはフラッシュ
  カード。Performance を追跡し学習プランを更新します。Use when the user says
  "run me 10 questions on [subject]", "do a session on [subject]", "let's
  do 5 cards on [subject]", or wants to drill a fixed number of questions
  and have the plan adapt(日本語トリガー:科目で N 問、セッション実行、
  カードを N 枚やる、固定数のドリル)。
argument-hint: "<subject> <n> [--mbe | --essay | --flashcards]"
---

# /session

1. `$ARGUMENTS` をパース — 科目と N。欠けていたら問う:
   > 科目は?何問?(例: `Evidence 10` または `Contracts 5 --essay`。)
2. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロード → 法域、試験形式、弱点科目。
3. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` が存在すればロード。この科目の `session_history` を読み、学生が弱かったサブトピックに重みづけ。
4. メソッドフラグでルーティング:
   - `--mbe`(バー試験対策科目のデフォルト): `bar-prep-questions` スキルをロード、N 問の MBE スタイル問題を実行。法域取扱いを適用(同スキルの `## Jurisdiction handling` 参照)。各問題に `[UBE/majority]` または `[state-specific]` ラベル。
   - `--essay`: `bar-prep-questions` をロード、N 個のエッセイ・プロンプトを実行。Essay-mode ルーブリックで採点。
   - `--flashcards`: `flashcards` スキルをロード、N カードを `--drill` モードで実行。
5. N 問を一つずつ実行。各回答後、正誤を説明、法域分岐時にルール本体をフラグ。
6. セッション末に結果を書く:
   - `study-plan.yaml` が存在すれば、`study-plan` スキルのスキーマに従い `session_history` に追記。
   - なければ、`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/session-history.yaml` に書く。
7. 報告:
   - Score: X/N(percentage)
   - Missed: サブトピック・タグ付きリスト
   - 今セッションの弱点サブトピック
   - この科目の過去セッションに対するパターン(history に 2+ priors があれば)
   - プランが次に推奨するもの
