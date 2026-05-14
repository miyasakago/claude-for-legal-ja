---
name: customize
description: >
  規制プラクティスプロファイルをガイド付きでカスタマイズ — コールドスタート・インタビュー
  全体を再実行せずに 1 項目だけ変更します。監視対象規制機関、ポリシーライブラリインデックス、
  重要性閾値、ギャップ対応プロセス、フィード設定、案件ワークスペースパスを調整可能。
  ユーザーが "change my [thing]", "add a regulator", "update my watchlist", "edit my threshold",
  "customize" と言ったときに使用。
  日本語トリガー: 規制プロファイルを変更、ウォッチリスト変更、規制機関を追加、閾値を編集、カスタマイズ。
argument-hint: "[section name, or describe what you want to change]"
---

# /customize

## いつ動くか

ユーザーが `/regulatory-legal-ja:customize` をタイプした。コールドスタート・インタビューを再実行せず、また YAML を手編集せずに、規制プロファイルの何か — 監視中の規制機関、重要性閾値、フィードソース — を変更したい。

## やること

1. **config を読む。** `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md`(および 1 階層上の `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md`)を読む。プラグイン config が存在しないか `[PLACEHOLDER]` 値をまだ含んでいる場合、言う:

   > You haven't run setup yet. Run `/regulatory-legal-ja:cold-start-interview`
   > first — customize is for adjusting a profile you already have.

2. **カスタマイズ可能マップを表示。** プロファイルに何があるかをグループ化してリスト表示、各項目の現在値を 1 行で:

   - **Company / 会社情報** — 名前、業界、法域、ステージ、practice setting *(12 プラグイン全体で共有 — 変更は `company-profile.md` を通して流れる)*
   - **Regulators we watch** — 範囲内の agencies / bodies / SRO / 州規制機関、そしてどれが "leading"(ポリシー影響を最も推進する可能性が高い)vs. "monitor" か
   - **Policy library** — ライブラリが索引化する内部ポリシー、各パス、ポリシーごとのオーナー
   - **Materiality threshold** — 規制変更が "notable" vs. "report" vs. "digest only" に到達するタイミング;この閾値が `/watch` 出力をどうフィルタするか
   - **Gap response process** — 誰がトリアージするか、severity ごとの SLA、下流オーナー(ポリシー、プロダクト、トレーニング)
   - **Feed configuration** — 規制機関フィード、Thomson Reuters コネクター、`/watch` sweep の cadence、ダイジェストチャンネル
   - **People** — 規制弁護士、ポリシーオーナー、コメントドラフター、エスカレーションチェーン
   - **Workflow** — 案件ワークスペース、未解決ギャップトラッカー、コメント deadline トラッカー、ダイジェスト公開 cadence
   - **Integrations** — Thomson Reuters / Slack / ドキュメントストレージのステータス、フォールバック

3. **何を変えたいか尋ねる。**

   > What would you like to adjust? Pick a section, or describe the change in
   > your own words.

4. **変更する。** 現在値を表示、新しい値を尋ねる、下流で何が変わるか説明、確認、config に書く。

   例:
   - *監視リストに規制機関を追加:* "`/watch` will sweep this regulator on its next run. `/diff` will accept inputs from this regulator's rulemaking feed."
   - *重要性閾値を厳しくする:* "`/watch` digest will be shorter — items below the new threshold will drop from the weekly digest but stay searchable."
   - *新しいポリシーをライブラリに追加:* "`/diff` will include this policy when matching new rules against the library. The comment tracker will tag comments affecting this policy."

5. **共有プロファイルの変更**(会社名、業界、法域、practice setting、ステージ): `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` に書き、注記する:

   > This change affects all 12 plugins — any plugin that reads your
   > jurisdiction footprint now sees [new value].

6. **締める。**

   > Done. Your next output will reflect the change. Anything else? You can
   > run `/regulatory-legal-ja:customize` anytime.

## ガードレール

- **セクションを決して削除しない。** ユーザーが規制機関を "drop" したい場合、`[Monitor only]` とマークすることを提案し、monitoring がフィードをアーカイブに保ちつつアクティブダイジェストから外すことを説明する。
- **内部矛盾をフラグ。** 変更がプロファイルを矛盾させる場合(例: 範囲内の規制機関 + その規制機関がカバーする法域が footprint に存在しない;または "weekly digest" + 四半期に 1 件未満しか produce しない重要性閾値)、緊張をフラグ。
- **ガードレールの劣化をフラグ。** 引用された規制への `[verify]` タグ、フィード pull の source attribution、ギャップトリアージの `[review]` フラグは load-bearing — 削除しない。重要性閾値は調整可能だが、ダイジェストがノイズになる点より低くするのは本末転倒 — その方向ならフラグを立てる。
- **一度に 1 つの変更。** インタビュー全体を再質問しない。
