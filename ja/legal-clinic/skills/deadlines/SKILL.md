---
name: deadlines
description: >
  案件期限の追跡 — 追加、案件横断のロールアップレポート、更新、完了、クローズ。
  構成可能な閾値(デフォルト 14/7/3/1 日)で警告し、超過項目は解決まで
  フラグされ続けます。クリニックワークロードの運用上の記録です。
  学生または指導弁護士が期限を追加、今週の期日を確認、期限レポートを取得、
  または案件期限を更新したいときに使用。
  (Use when a student or supervisor needs to add a deadline,
  ask what's due this week, get a deadline report, or update a case deadline.
   日本語トリガー: 期限管理、deadlines、期限追加、期限レポート、期日確認)
argument-hint: "[--add | --report(デフォルト) | --update [id] | --complete [id] | --close [id] | --horizon=N]"
---

# /deadlines

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → 法域、プラクティスエリア、警告日ケーデンス。
2. 下記のワークフローを使用。
3. フラグでルーティング:
   - `--add`: 案件、種別、説明、期日、ソース、所有者をキャプチャ。`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` に書き込む。まず重複をチェック。
   - `--report`(デフォルト): 案件横断ロールアップ — 超過、次の 3 日、次の 7 日、次の 14 日; 所有者別; プラクティスエリア別; 未割当フラグ。
   - `--update [id]`: フィールドを変更; 日付付き注記をログ。
   - `--complete [id]`: 完了マーク; 作業が実際に提出/サブミットされたかを学生と確認。
   - `--close [id]`: 完了せずクローズ; 注記に根拠を要求。
4. コミット前に書き込みを確認。

---

# Deadlines(期限管理)

## 目的

クリニックの最大の運用リスクは期限の見落とし。学生は複数案件を抱え、パートタイムで働き、学期ごとに入れ替わる。個々の学生の頭の中にだけ存在する期限は、引継ぎで落ち、ファイナル週間中に忘れられ、学生が予期せずクリニックを離脱すると見落とされる。このスキルは中央運用記録。

期限が見落とされた場合、指導弁護士が責任を負う。本スキルはそのリスクレベルに合わせて調整 — 警告は早く発火、超過項目は明示的に解決されるまで見え続ける、引継ぎ(`/semester-handoff` 経由)は期限リストを次の学生に持ち越す。

## コンテキストの読み込み

- `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → 法域、プラクティスエリア、期限警告日(デフォルト 14/7/3/1)、指導弁護士
- `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` — 台帳

**法域の前提。** 期限計算と警告閾値は CLAUDE.md で設定された法域を前提とする。期限、tolling(時効停止)規則、期間計算規則、ローカル裁判所実務は法域と特定裁判所によって実質的に異なる。案件が異なる州、特定裁判所のローカルルール、または連邦 vs. 州フォーラム問題を含む場合、依拠する前に統治規則に照らして指導弁護士と期限を確認。

## モード

フラグ: `--add | --report | --update | --complete | --close`(デフォルト: report)

### `--add` — 新規期限をログ

**入力:**
- Case ID + 名前(どの案件か)
- プラクティスエリア
- 種別(filing / hearing / statute-of-limitations / discovery / cure-period / response / notice / other)
- 説明 — 期日となるものの 1 行
- 期日(該当時は時刻 + タイムゾーン)
- ソース — 期限の出所(2026-04-20 に送達された裁判所命令、法令 8 USC § 1229a、契約 §7 の治癒期間)
- Owner student — 担当する学生

スキルは `id` slug を自動生成: `[case]-[short-desc]-[YYYY-MM]`。

**他スキルからの抽出:** `/client-intake`、`/draft`、`/status` が出力で期限を浮上させたとき、事前入力されたフィールドでこのスキルにハンドオフすべき。学生が確認して追加。

**事前追加チェック:** 同じ case_id + 種別 + 期日の期限が既にあれば、重複の可能性をフラグして追加前に尋ねる。

**Plausibility sanity band(妥当性チェックバンド)。** 学生が期日を入力した後、計算や検証は **しない** が、提出種別の典型的レンジに対するおおまかな妥当性チェックを適用し、日付がレンジから大きく外れる場合に学生にフラグ。これは学生自身の計算の重大なエラーを捕捉する足場であり、規則に照らした計算の代替ではない。

**バンドは法域キー。** 本クリニックの法域用のバンドファイルを `references/plausibility-bands/{state}.md` から読み込む(`{state}` は `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → クリニック法域の 2 文字コード、連邦は常時並行ロード)。legal-clinic-ja プラグインは出発点として `references/plausibility-bands/CA.md`(完全 populated)と `references/plausibility-bands/IL.md`(プレースホルダー構造)を同梱。

**バンドファイルがない場合のコールドスタート時のハードストップ。** クリニックの法域用の `references/plausibility-bands/{state}.md` が存在しない場合、妥当性チェックなしで黙って実行しない。コールドスタートで、指導弁護士に伝える:

> 「I don't have deadline plausibility checks for [state] — the sanity band for this clinic's jurisdiction isn't in the shipped reference files. I can still track deadlines (add, report, update, complete, close), but I cannot sanity-check them against typical ranges. Here's how to build the band file from your state's rules: copy `references/plausibility-bands/IL.md` as a template, fill in one row per deadline type your clinic sees most (typical range, triggering-event handling, computation-of-time rule, short cite), save at `references/plausibility-bands/{state}.md`, and re-run `/legal-clinic-ja:deadlines`. Until then, every deadline I accept will carry `warnings: no-plausibility-band` and your review should treat dates as unchecked.」

非 CA クリニックに対して CA テーブルにフォールバックしないこと。silent-degradation のケース — Illinois クリニックに California sanity check を出荷する — がこの修正で塞ぎたい失敗。

**Sanity check ロジック:**

1. このクリニックの法域用のバンドテーブルを `references/plausibility-bands/{state}.md` から読み込む(プラス federal-always)。
2. 学生が `due:` を入力した後、ロードされたバンドファイル内に提出種別の典型的レンジが存在する場合、`type:` 用の triggering-event date + 典型的レンジと比較。
3. レンジ内なら、エントリを書く。何も言わない — バンドは正しい計算を祝福するためではなく、エラーを捕捉するため。
4. 実質的なマージンでレンジ外なら、書き込み前に停止して言う:
   > The date you entered falls outside the typical range for [type] in [jurisdiction]. [Type] deadlines for [filing type] typically fall ~[range] after [triggering event]. Your entry: [date], which is [N] days from [triggering event]. Re-check your calculation against [cited rule from the band file] and the jurisdiction's computation-of-time rule. If your calculation is correct (local rule exception, atypical triggering event, tolling, waiver), confirm and I will add the entry as-is. Otherwise, recompute and re-run `/deadlines --add`.
5. この `type:` のバンドが既知でない場合(変わった提出、非標準期限)、sanity-check しない — エントリを書き、`warnings:` フィールドに妥当性バンドが適用されないことを注記。
6. バンドファイルがこの法域用に完全に欠落している場合、上記のハードストップがコールドスタートで適用される; steady-state(指導弁護士がギャップを認めて続行した場合)では、すべてのエントリが `warnings: no-plausibility-band` で書かれる。

**スキルは計算しない。** 学生がまだ計算していないため `due:` フィールドに `[VERIFY]` を入れる場合、`due: [VERIFY]` でエントリを書く — sanity band は学生が具体的な日付を供給したときのみ動作。計算は学生と指導弁護士の手元に残る。

### `--report`(デフォルト) — 案件横断ロールアップ

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` を読む。生成:

```markdown
# Deadline Report — [today]

**Active deadlines:** [N]
**Overdue:** [N] ⚠️
**Due this week (next 7 days):** [N]

---

## ⚠️ Overdue (即時対応用にフラグ)

| ID | Case | Type | Due | Owner | Days overdue |
|---|---|---|---|---|---|

## 🔴 Due today / next 3 days

| ID | Case | Type | Due | Owner |
|---|---|---|---|---|

## 🟡 Due in 4-7 days

| ID | Case | Type | Due | Owner |
|---|---|---|---|---|

## 🟢 Due in 8-14 days

[list]

## Beyond 14 days

[count only — expand with `/deadlines --report --horizon=30` for details]

---

## By owner student (workload distribution)

| Student | Overdue | Next 7d | Next 14d | Total active |
|---|---|---|---|---|

## By practice area

[same table, grouped by area]

## Unassigned deadlines

[アクティブ期限に owner_student がない場合フラグ]
```

### `--update` — 既存期限を変更

よくある更新: 期日変更(裁判所継続)、所有者変更(再割当)、注記追加。

各更新は日付付き注記をインラインで書く; 履歴はエントリで見える。

### `--complete` — 完了マーク

- `status: completed`、`completed_date: [today]` を設定。
- 実際の作業が完了し、提出/サブミットされたことを学生と確認。
- アクティブレポートから削除されるが、yaml には残る。

### `--close` — 完了せずクローズ

もはや適用されない期限用 — 案件和解、申立て取り下げ、クライアントが案件を降りた。理由を説明する `notes:` エントリを要求。

## 警告ケーデンス

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の期限警告日に従う。デフォルト 14、7、3、1。

警告は自動的に浮上しない — 本プラグインはスケジュール/エージェント挙動を持たない。しかし、`/deadlines` が呼び出されたとき(または `/status` が deadline チェックのため本スキルにルーティングしたとき)、レポートは警告閾値にヒットしたものを引き出す。

期限が完了マークされずに期日を過ぎた場合、`status: overdue` に移動し、明示的に解決されるまですべてのレポートに表示され続ける。Overdue 期限は自動クローズされない。

## インテグレーション

- **`/client-intake`:** インテイクがタイムライン緊急性(退去通知日、庇護申請期限、聴聞日)を浮上させたとき、事前入力フィールドで `/deadlines --add` を提案。
- **`/draft`:** 提出ドラフトが期限(答弁期日、異議申立てウィンドウ)を参照するとき、追加を提案。
- **`/status`:** ステータススキルは関連案件の `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` を読み、出力に来るべき期限を含める。
- **`/semester-handoff`:** deadlines.yaml を読んで、引退する学生の案件全体のアクティブ期限を特定する; 各引継ぎメモが期限を持ち越す。
- **`/supervisor-review-queue`(正式レビュー有効時):** 期日近くの期限はレビューキューで優先される。

## このスキルが行わないこと

- **起算事由から期限を計算。** 訴状が今日送達され、答弁書がローカルルールで 21 日後である場合、スキルはその計算をしない — 学生が規則を使って行い、結果の日付を記録。(自律的に計算することは、スキルが負うべきでない責任を作る; 規則は法域と裁判所によって異なる。)
- **提出または送達。** スキルは日付を追跡する; 提出はプラグイン外で行う。
- **自動通知。** スケジュール通知なし。レポートは呼び出されたときに警告を浮上させる; プッシュしない。スケジュール cron は後で追加可能だが、クリニック単位で教授の明示的オプトインが必要。
- **ローカルルールのオーバーライド。** 学生がローカルルールと矛盾する期日をログした場合、スキルは捕捉しない。これはルーチンでない期限に対して `[VERIFY: confirm against local rule]` を付けてカレンダー化する別の理由。
