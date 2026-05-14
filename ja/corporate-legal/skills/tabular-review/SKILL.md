---
name: tabular-review
description: >
  タブラーレビュー — 1 文書 1 行、1 データポイント 1 列、すべてのセルにソース引用付き。
  M&A DD(「これら 200 件のターゲット契約を change-of-control、assignment、MAC 条項についてレビュー」)向けに構築されていますが、
  スプレッドシート出力が必要なバッチレビューならどんなものでも機能します。
  Use when user says "tabular review", "review grid", "build a grid",
  "extract these fields from these contracts", "review these documents for X, Y, Z",
  "give me a spreadsheet of", "batch review", or points at a folder of documents and asks to compare them.
  日本語トリガー: 「タブラーレビュー」「レビューグリッド」「グリッドを構築」「これらの契約からフィールドを抽出」
  「これらの文書を X, Y, Z についてレビュー」「スプレッドシートで」「バッチレビュー」。
---

# /tabular-review

1. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` をロード → DD 構造、閾値、ハウスフォーマット。
2. 確認:どの文書、どの列、出力先。
3. 型付きスキーマを構築。`.review-schema.yaml` を書く。ユーザーと確認。
4. サンプル実行(3〜5 文書)。スキーマを調整。確認。
5. ファンアウト — 文書あたり 1 サブエージェント、並列。各セル:値 + state + verbatim 引用 + 位置。
6. 正規化パス。外れ値と不整合をフラグ。
7. 出力:`.xlsx` または Google Sheets(どちらか確認)、加えて常に `.csv` + `_sources.csv` + markdown。Work-product ヘッダー。
8. サマリー:検証ワークロード(列ごとの not_present / unclear / needs_review カウント)、フラグされた列、ファイルの場所、すべてのセルは finding ではなく lead であることを再認識。

```
/corporate-legal-ja:tabular-review
/corporate-legal-ja:tabular-review --schema .review-schema.yaml --docs ./vdr/02-Contracts/
/corporate-legal-ja:tabular-review --template ma-diligence
```

**`--schema <path>`:** 構築の代わりに既存スキーマファイルを使用。再実行や増分追加に有用。

**`--template <name>`:** `references/` のテンプレートから開始。現在:`ma-diligence`。

**`--docs <path>`:** 文書ソース。ローカルフォルダ、Drive フォルダ ID、または VDR パス。省略時は尋ねる。

**`--output <xlsx|gsheets|csv>`:** 出力フォーマット。省略時は尋ねる。

**`--sample <n>`:** スキーマチェック用のサンプルサイズ。デフォルト 5。

---

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

文書の山と、すべてに対して一貫して答えてほしい質問のリストがあります。DD リクエストリスト。ベンダー契約監査。リースポートフォリオレビュー。出力はテーブル:文書行、データポイント列、各セルはソースの正確な文言にトレース可能。

これは issue spotting ではありません。`diligence-issue-extraction` は 2,000 文書の中に潜む 30 件の問題を見つけます。本スキルは 2,000 文書すべてについて同じ 15 個の質問に答えます。両方とも正当;異なる質問に答えます。

これは人間が文書を読むことの代替でもありません。本スキルが生成する各セルは finding ではなく**検証を要する lead**です。出力は検証をスキップさせるためではなく、検証を高速化するために設計されています。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → DD 構造、重要性基準、ハウスフォーマットの選好
- 特定ディール作業中なら `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/deal-context.md`
- ユーザーが既存スキーマファイル(`.review-schema.yaml`)を持っていればそれ

## 列タイプシステム

タブラーレビューを有用にするのは、Column C が行 1 でも行 200 でも同じ意味を持つこと。フリーテキストはドリフトする。型は保持する。

各列には**型**があり、回答フォーマットを制約:

| Type | What it returns | Use for |
|---|---|---|
| `verbatim` | 文書からの正確な引用、character-for-character | 定義語、運用条項の文言、文言が重要なもの |
| `classify` | 定義した固定リストからの 1 つの値 | Yes/No、present/absent、条項バリアント(例:"sole consent" / "consent not unreasonably withheld" / "silent") |
| `date` | ISO 日付 | 発効日、満了日、解除通知期限 |
| `duration` | 数値 + 単位 | 契約期間、通知期間、survival 期間 |
| `currency` | 数値 + 通貨コード | Caps、閾値、手数料、購入価格参照 |
| `number` | 数値のみ | カウント、パーセンテージ、ページ参照 |
| `free` | 短い自由テキスト要約 | 慎重に使用 — これはドリフトする型。他が真にフィットしない時のみ。 |

**Verbatim ルール:** `verbatim` 以外のすべての列も、回答を支持する正確なソース引用をコンパニオンフィールドとしてキャプチャ。セル内の回答は解釈;引用は証拠。"consent not unreasonably withheld" と言う `classify` セルは、由来文なしでは無価値、レビューアーの仕事はその読みが正しいかチェックすることだから。

## "Not found" の 3 つの状態

空白セルは情報を隠す。陽性回答が生成できない場合、3 つの明示的状態のいずれかを強制:

| State | Meaning | When to use |
|---|---|---|
| `not_present` | 文書を読んだが該当条項がない | 主題が扱われていないことを確信 |
| `unclear` | 何かはあるが確信を持って分類できない | 曖昧な起草、部分的条項、矛盾する規定 |
| `needs_review` | 何か見つけたが人間が判断すべき | エッジケース、異常な起草、回答がスキーマでキャプチャされない判断に依存 |

これらは 3 つの異なる情報。「契約は assignment について沈黙」と「assignment 条項は曖昧」をディールチームは非常に異なる扱い。1 つの空白セルに圧縮すると区別を失う。

## ワークフロー

### Step 0: 何を、どこに

確認:
1. **文書。** どこに? VDR MCP(Box, Datasite, iManage)、ローカルフォルダ、Google Drive フォルダ、ファイルリスト。何件? 200 超なら時間がかかると警告し、重要性フィルタしたサブセットで開始するよう提案。
2. **スキーマ。** どの列? 2 パス:
   - `references/` のテンプレートを選ぶ(M&A diligence standard がデフォルト)
   - 自然言語で列を記述、構造化された型付きスキーマに変換
3. **出力。** Excel(`.xlsx`)または Google Sheets — チームが使うものを尋ねる。CSV と markdown は常にフォールバックとして書かれる。出力はディールフォルダ、Drive、またはユーザー指定先へ。

### Step 1: スキーマを構築・確認

ユーザーの列リストを構造化スキーマに変換。各列について:安定した `id`、人間用 `label`、`type`、`prompt`(文書を読むレビューアーが尋ねる質問)、`classify` 列には `options` リスト。

出力の隣の `.review-schema.yaml` に書く。本ファイルは再利用可能な成果物 — ユーザーは編集、列追加、新文書で再実行可能。ユーザーに表示し、ファンアウト前に確認。

```yaml
schema:
  name: "M&A Diligence — Project [Code]"
  created: 2026-05-07
  columns:
    - id: counterparty
      label: "Counterparty"
      type: verbatim
      prompt: "Who is the contracting party other than the target?"
    - id: effective_date
      label: "Effective Date"
      type: date
      prompt: "When did the agreement become effective?"
    - id: change_of_control
      label: "Change of Control"
      type: classify
      options: [silent, consent_required, consent_not_unreasonably_withheld, automatic_termination, notice_only]
      prompt: "Does the agreement address a change of control of the target? What does it require?"
    - id: assignment
      label: "Assignment Restrictions"
      type: classify
      options: [silent, consent_required, consent_not_unreasonably_withheld, freely_assignable, assignable_to_affiliates]
      prompt: "Can the target assign this agreement? What restrictions apply?"
    # ... more columns
```

### Step 2: サンプル実行

テストされていないスキーマで 200 文書にファンアウトしないこと。先に 3〜5 文書実行。ユーザーに行を表示。チェック:
- 大多数の回答が `unclear` の列 — プロンプトが曖昧、書き直し
- 回答がオプションに合致しない `classify` 列 — オプション追加または `free` に変更
- 言い換えを返している `verbatim` 列 — character-for-character でなければならないと強調

スキーマ調整、サンプル再実行、確認。捨てなければならないフル実行からユーザーを救う。

### Step 3: ファンアウト

文書あたり 1 サブエージェント、並列。各サブエージェント:

1. 文書全体を読む(RAG チャンクではなく — 全体)。
2. 各列について、関連規定を見つける。
3. 構造化された行を返す:各列について `{value, state, quote, location}`。
   - `value` は型付き回答(または `state` が `answered` でなければ null)
   - `state` は `answered | not_present | unclear | needs_review`
   - `quote` は verbatim な支持テキスト(正確、言い換えなし、文内に省略記号なし — カットする場合、文境界でカットしマーク)
   - `location` は引用がどこにあるか(セクション番号、見出し、ページ — 文書が提供するもの)

**引用はオプションではない、verbatim ルールは機械的で、勧告ではない。** 各サブエージェントは `state: answered` のセルを返す前に以下すべてに従わなければならない:

- `quote` はソース文書の連続テキストの character-for-character コピーで、サブエージェントが引用する `location` で取得可能でなければならない。セクション見出しと標準ボイラープレートを合成して引用を作らないこと。言い換えを verbatim と呼ばないこと。そのような条項が「通常」どう読まれるかの記憶から引用を再構築しないこと。非連続テキスト横断で省略記号で繋いでソースのギャップを埋めないこと。
- `location` は正規化パスが文書を再オープンし同じスパンを再読込できるほど具体的でなければならない — レビューアーがナビゲートできるセクション番号、見出し、ページ参照。
- サブエージェントが正確なテキストを見つけてコピーできない場合(ソース切り詰め、OCR ゴミ、規定が暗示されたが書かれていない、セクション見出しは見えるが本体がロードされていない)、セルの state は `needs_review`、`value` は null、`notes` には `quote_unavailable: <reason>` を**必ず**含む。合成または再構築された引用で `state: answered` を設定することは**絶対に**許されない。
- 同じルールが `verbatim` 型列**および** `classify` / `date` / `duration` / `currency` / `number` / `free` セルに付随するソース引用に適用される。支持引用はセル値と同じ verbatim 義務を負う。

Step 4 の正規化パスは、引用された `location` でソースを再読込し、保存された `quote` をソーステキストと character-for-character で比較してスポットチェックする。不一致はセルを `needs_review` にダウングレード、`quote_mismatch` を記録、列全体をより広いスポットチェック用にフラグ — 1 つのサブエージェントが引用を合成したなら、同じ実行内の他者もそうした可能性。

### Step 4: 正規化

ファンアウト後、テーブルを列単位で全体読み。これはすべてのタブラーレビューツールの失敗モードを捕まえるパス:同じ条項が文書間で一貫しないように解釈される。

各 `classify` 列について:
- すべての `answered` 値がオプションリスト内かチェック。外れ値は再分類または `needs_review` に昇格。
- クラスターをチェック:180 文書が `consent_required` と言い 20 が `consent_not_unreasonably_withheld` と言うなら、それはおそらく本物。195 が `consent_required` で 5 が `freely_assignable` と言うなら、5 を見る — 本当に違うか誤分類か。

各 `date` / `duration` / `currency` 列について:
- フォーマット一貫性をチェック。正規化。
- 不合理な値(99 年契約期間、$1 cap)を `needs_review` にフラグ。

各 `verbatim` 列**および**他のすべての列のコンパニオンソース引用について:
- ランダムサンプル(列あたり少なくとも 3〜5 行、または行の 10%、大きい方)について、引用された `location` でソース文書を再オープンし、保存された `quote` をソースと character-for-character で比較してスポットチェック。
- 引用が合成、言い換え、再構築、または引用スパンで見つからない場合:そのセルを `needs_review` にダウングレード、notes に `quote_mismatch`、列全体をフラグ — 他の行をクリーンと仮定するのではなく、スポットチェックを列の残りに拡大。1 つの捏造引用はチェック拡大を正当化するに十分。
- `state: answered` で引用が不一致のセルは、`unclear` または `needs_review` セルより高重大度の失敗 — 証拠トレイルを誤表現している。積極的にダウングレード。

### Step 5: 出力

テーブルを 3 つのフォーマットで書く:

**Markdown**(常時、セッション内レビュー用):
```markdown
| Document | Counterparty | Effective Date | Change of Control | Assignment | ⚠️ Flags |
|---|---|---|---|---|---|
| Vendor MSA — Acme | Acme Corp | 2023-04-01 | consent_required | consent_required | — |
| Supply Agmt — Beta | Beta LLC | 2021-11-15 | ⚠️ unclear | silent | CoC ambiguous §14.2 |
```

**CSV**(`.csv`、常時):
値用ファイル 1 つ、引用と位置のコンパニオンファイル 1 つ(`_sources.csv`)。メインファイルをクリーンに、証拠トレイルを完全に保つ。

**Excel**(`.xlsx`)または **Google Sheets** — ユーザーが使うもの。尋ねる、推測しない。両方とも同じワークブック構造に従う(`references/excel-output.md` と `references/gsheets-output.md` 参照)。Excel:利用可能なら Claude in Excel(Office agent)、`openpyxl` フォールバック。Sheets:利用可能なら Sheets MCP、ADC 経由の Sheets API、CSV インポートフォールバック。スプレッドシート出力では:
- 各データ列は引用と位置を含む隠しソース列とペアになる。可視列のセルコメント(Excel)またはノート(Sheets)がホバー時に引用を表示。
- 状態でカラーコード:白 = answered、黄 = unclear または needs_review、灰 = not_present。
- データ列ごとの `Verified` 列、デフォルトは空。レビューアーがマーク。これはテーブルを auditable にする verify/flag パターン — 人間が実際にチェックしたものをディールチームは一目で見える。
- 列定義の `_schema` シート、ファイルは自己文書化。

プラグイン設定 `## Outputs` の work-product ヘッダーをトップ行として prepend。並べて、distribution note を含める:

> 本レビューは特権・秘密(またはその両方)の可能性があるソース文書から派生。ソースの特権・秘密ステータスを継承 — 特権の輪を超えた配布は特権放棄を引き起こす可能性。案件の特権ファイルと共に保管し、配布判断は意図的に行うこと。

### Step 6: サマリー

テーブル書き出し後、ユーザーに 1 画面の読み出しを与える:
- 文書数、列数、完了行数
- 列ごとの `not_present`、`unclear`、`needs_review` のカウント — これは検証ワークロード
- 正規化パスが 10% 超の行をフラグした列
- 出力ファイルの場所
- リマインダー:各セルは finding ではなく lead。これが rep、別紙、メモに反映される前に検証必要。

## next-steps デシジョンツリーで締めくくる

CLAUDE.md の `## Outputs` に従い、next-steps デシジョンツリーで終わる。選択肢は本スキルが今生成したものに合わせてカスタマイズ — デフォルト 5 分岐(draft the X、escalate、get more facts、watch and wait、something else)は出発点で固定ではない。ツリーが出力;弁護士が選ぶ。

## 本スキルがしないこと

- **文書を読むことを置き換えない。** どこを見るかを伝える。
- **信頼度スコアを生成しない。** 0.73 は情報ではない。`unclear` / `needs_review` 状態と verbatim 引用が信頼度シグナル — 引用が値を支持しなければフラグ。
- **黙って文書をスキップしない。** ユーザーが指したすべての文書に行が割り当てられる。読めなかった文書はノート付き `needs_review` 行を得る。
- **言い換えを引用として通さない。** 証拠トレイル全体がポイント。

## 他スキルとの関係

- `diligence-issue-extraction` は論点を見つける;本スキルはデータポイントを抽出。抽出が論点(特定収益目標を参照する MAC 条項、poison pill)を明らかにすれば、それを記録し、当該文書で diligence-issue-extraction を実行することを提案。
- `material-contract-schedule` は特定のテーブル 1 つ(開示別紙)を構築。本スキルの出力を直接消費可能 — 別紙はタブラーレビューのフィルタ・再フォーマットビュー。
- `ai-tool-handoff` はコーパスが大きすぎる、またはチームが専用プラットフォームを好む場合、Luminance/Kira にバルクレビューを引き渡す。本スキルは社内オプション、扱えるものは何でも — 先に実行し、残りを引き渡す。

## 出力セーフガード

各出力は work-product ヘッダーを得る。各セルはソース引用またはフラグされた状態を得る。サマリーは検証必要を明示。Excel の `Verified` 列は検証状態を auditable にする。これは読むことをスキップさせるツールではなく、読みを高速化するツール。
