---
name: policy-monitor
description: >
  Keep the AI policy current with practice — weekly sweep of saved AIAs, triage
  results, and vendor reviews to find policy drift, or direct query for a proposed
  new AI practice. Use when user says "policy sweep", "does our AI policy cover
  this", "we want to start doing X — does the policy need updating", "run the
  policy monitor", or on a recurring schedule.
  AI ポリシーを実務と最新の状態に保つ — 保存された AIA、トリアージ結果、ベンダーレビューを
  週次でスイープしてポリシードリフトを検出するか、提案された新規 AI プラクティスを
  ダイレクトクエリでチェックします。
  日本語トリガー: ポリシースイープ、AI ポリシーがこれをカバーしているか、X を始めたい —
  ポリシー更新が必要か、ポリシーモニター実行、定期スケジュール。
argument-hint: "[describe a proposed new AI practice — or omit / use --sweep for crawl mode]"
---

# /policy-monitor

**Sweep モード**(引数なし、または `--sweep`):
1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます → 出力フォルダーパス、AI ポリシー文書、最終スイープ日。
2. 下記のフレームワークを使用。最終スイープ以降の出力フォルダーをスキャンします。
3. 各出力について: 承認されたプラクティスを抽出 → 現行ポリシーコミットメントとユースケース台帳に対して差分。
4. ギャップを分類: REQUIRED(ポリシーが現在のプラクティスを誤って表現)vs ADVISABLE(ポリシーが沈黙)。
5. 各ギャップについて: 現行ポリシーを引用、ギャップを記述、推奨言語をドラフト。
6. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` 台帳にまだ追加されていない出力内の任意のユースケースをフラグします。
7. 結果を人間に提示。確認後にのみ、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `Last policy sweep` と `gaps_found` を更新します。

**Direct query モード**(記述引数付き):
1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます → 現行ポリシーコミットメント、ユースケース台帳、実際のポリシー文書。
2. 提案されたプラクティスを解析。ポリシーに対して差分: ユースケースカバレッジ、自動化レベル、影響を受ける関係者、開示、ベンダーデータ使用、監督。
3. 出力: covered / missing / conflicting + 各ギャップの推奨言語 + 必要に応じて台帳エントリ + タイミング推奨。

**定期実行:**
自身のスケジューラーで `/ai-governance-legal-ja:policy-monitor` を週次実行する定期リマインダーをセットアップします。スケジュール実行には scheduled-tasks 統合が必要ですが、このプラグインには同梱されていません。

```
/ai-governance-legal-ja:policy-monitor
/ai-governance-legal-ja:policy-monitor "経費レポートを自動的にレビュー用にフラグするために AI を使いたい"
```

---

## 目的

AI ポリシーは他のほぼすべてのポリシー文書よりも速く実務からドリフトします — フィールドは速く動き、ユースケースは増殖し、各承認 AIA またはトリアージ結果は、ポリシーがまだ追いついていない新しいコミットメントを表します。AIA が人間監督条件付きで新しい AI ユースケースを承認します。ベンダー AI 契約が、ポリシーが言及しないデータ処理を許可します。トリアージ結果が、開示要件付きで conditional として新しいデプロイメントカテゴリをマークします。ポリシーは変わらないままそこに座ります。

このスキルはドリフトを捕まえます — 出力フォルダーを週次でクロールするか、ダイレクトクエスチョン: 「X を始めようとしている、AI ポリシーにとってそれは何を意味するか?」に答えることによって。

出力は常に同じです: ここにギャップ、ここに推奨言語。

---

## 現状をロード

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます:
- `## AI policy commitments` — 公開ポリシーから抽出されたコミットメント
- `## Use case registry` — approved、conditional、never のユースケース
- `## Outputs` — 出力フォルダーパス、AI ポリシー文書の場所、最終スイープ日

`## Outputs` が `[PLACEHOLDER]` を含む場合:
> 「出力はまだ設定されていません。direct-query チェックはまだ実行できます — 何を計画しているか記述すれば、現行 AI ポリシーに対して差分化します。クロールスイープを有効にするには、`/ai-governance-legal-ja:cold-start-interview` を実行し、出力フォルダーパスを提供してください。」

`## Outputs` → **AI policy document** のパスから実際の AI または許容利用ポリシー文書を読みます。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` のコミットメントはサマリー; 実際の文書が編集提案のための authoritative です。

---

## モード検出

**Sweep モード:** 引数なし、`--sweep`、またはスケジュールによってトリガー。
→ 出力フォルダーをスキャン。最終スイープ以降のすべての出力を現行ポリシーに対して差分化。

**Direct query モード:** ユーザーが提案された新しい AI プラクティスの記述を提供。
→ そのプラクティスを現行ポリシーとユースケース台帳に対して差分化。更新を提案。

---

## モード 1: Sweep

### スコープを決定

`## Outputs` → **Last policy sweep** 日を読みます。その日以降の日付の出力フォルダーのファイルをスキャンします。日付が記録されていない場合、すべてのファイルをスキャンし、注記: 「First sweep — scanning all outputs.」

出力フォルダーが空、または最終スイープ以降に新しいファイルがない場合:
> 「[last sweep date] 以降、新しい出力はありません。AI ポリシーは最近の実務と最新のように見えます。次のスケジュールされたスイープ: [date]。」

**`Last policy sweep` または `gaps_found` を自動的に更新しないこと。** スイープ結果が提示された後、人間が結果を確認するのを待ちます(「sweep acknowledged」、「results reviewed」、または相当)。その後にのみ `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を更新:

- `Last policy sweep: [date of acknowledgment]`
- `gaps_found: [N]`(そのスイープで見つかった REQUIRED + ADVISABLE ギャップの数)

確認前にスタンプを更新すると、未レビューのスイープがサイレントに前進し、次のスイープが同じギャップに注意を払うのを抑制します。

### 各出力タイプで読むべきもの

**AIA(AI 影響評価):**
- 抽出: 承認されたユースケース、AI システム記述、デプロイモード(assistive / augmentative / automated)、課された条件、影響を受ける関係者、使用されたベンダー、影響を受ける個人への開示要件
- フラグ: 台帳にないユースケース、ポリシーに反映されていない条件で承認されたユースケース、ポリシーがカバーしないベンダーの追加、ポリシーが人間監督を意味する場所でデプロイされた自動決定

**トリアージ結果(CONDITIONAL / APPROVED の結果):**
- 抽出: 分類されたユースケース、割り当てられた階層、課された条件
- フラグ: 台帳にない新しいユースケースカテゴリ、ポリシーコミットメントを意味する条件(例: 「影響を受ける関係者に開示しなければならない」 — ポリシーはあなたがこれを行うと言っているか?)、ポリシースコープを拡張する新しく承認されたプラクティス

**ベンダー AI レビュー(署名済み / 承認済み):**
- 抽出: 追加されたベンダー、合意したデータ使用条項、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の標準ポジションと異なる AI 固有条項の受諾
- フラグ: ポリシーが参照すべきデータ使用条項を持つ追加されたベンダー(例: 「我々はサードパーティ AI サービスを使用し、データで訓練されないことを確認する」)、ポリシーが保持していると示唆する標準ポジションからの承認された逸脱

**ユースケース台帳の更新:**
- 最終スイープ以降に新しいエントリが台帳に追加された場合(AIA を通じてではなく直接)、ポリシーがそれらの承認カテゴリを反映しているか確認します。

### ギャップ識別

各フラグされた項目について、評価:

**REQUIRED 更新** — ポリシーが出力と矛盾するコミットメントを行う、または承認されたユースケースがポリシーカバレッジを持たず、外部当事者に影響する。更新しないことは実質的な誤表示を作成します。

> 例: AI ポリシーが「我々は雇用決定で AI を使用しない」と述べる。AIA が、人間レビュー必須の AI 支援採用スクリーニングツールを承認した。ポリシー更新が必要 — 人間レビューありでも、AI は今や雇用決定に関与している。「我々は AI を使用しない」はもはや正確ではない。

**ADVISABLE 更新** — ポリシーは沈黙しているが矛盾していない。実務は更新なしでも防御可能だが、それありでよりクリーン。実務が外部当事者に影響する、または合理的な期待を作る場合に重要。

> 例: ポリシーが「我々は AI を使用して製品とサービスを改善する」と述べる。AIA がカスタマーサポートドラフトのための AI 機能を承認した。ポリシーは技術的にカバーするが曖昧。顧客が何と相互作用しているかを知るために、より具体的であることが advisable。

### Sweep 出力フォーマット

```markdown
[WORK-PRODUCT HEADER — plugin config ## Outputs ごと — ロールにより異なる; `## Who's using this` を参照]

*このスイープは、プラグインの privilege/confidentiality マーキングを持つ AIA、トリアージ結果、ベンダー AI レビューから派生しています。スイープはそのステータスを継承します。意図的に配信してください — ギャップの findings を privilege サークル外に転送すると、根底にある評価の privilege を waive する可能性があります。*

# AI Policy Monitor — Sweep Report

**Date:** [date]
**Outputs scanned:** [N files] | **New since last sweep:** [N files]
**Gaps found:** [N] REQUIRED | [N] ADVISABLE

---

## REQUIRED updates

### [Gap 1 short name]

**Source:** [filename / output type that triggered this]
**What's happening:** [新しいプラクティスのプレーンな記述]
**Current policy:** [関連セクションを引用 — または「No coverage」]
**Gap:** [何が欠けているか、または矛盾しているか]

**Suggested language:**
> *Add to / update [section name]:*
> "[ドラフトポリシーテキスト — 具体的、実際のポリシーのハウススタイルと一致]"

---

[各 REQUIRED ギャップについて繰り返し]

---

## ADVISABLE updates

### [Gap name]

**Source:** [filename]
**What's happening:** [description]
**Current policy:** [quote or "Silent"]
**Suggested language:**
> *Add to / update [section]:*
> "[ドラフトテキスト]"

---

## No action needed

[ギャップが見つからなかったスキャンされた出力のリスト]

---

## Use case registry sync

[最終スイープ以降に承認されたが、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` 台帳にまだ追加されていない任意のユースケース — 追加する台帳エントリを提案]

---

## Next steps

- [ ] REQUIRED 更新をレビュー — 関連するユースケースが稼働する前に決定が必要(既に稼働中の場合は直ちに)
- [ ] ADVISABLE 更新をレビュー — 緊急度が低い、次のポリシー更新で対処
- [ ] 新しいユースケースを台帳に追加(上記でフラグされた場合)
- [ ] 次のスケジュールされたスイープ: [date]
```

---

## モード 2: Direct query

### 提案されたプラクティスを解析

ユーザーの記述から抽出:
- どの AI システムまたは能力が導入されているか?
- 何をするか — assistive、自動決定、コンテンツ生成?
- 誰に影響するか — 従業員、顧客、サードパーティ?
- どのベンダーまたはモデルが関与しているか?
- 人間レビューがあるか、完全に自動化されているか?
- 影響を受ける関係者は AI が関与していることを伝えられているか?
- 期待されないベンダーへのデータの流れはあるか?

記述が曖昧な場合、1 つの明確化質問を尋ねます。長いインテークを実行しないこと — direct query モードは速くあるべきです。

### ポリシー差分

提案されたプラクティスを現行ポリシーとユースケース台帳に対してチェック:

| Check | Current policy / registry | Proposed practice | Verdict |
|---|---|---|---|
| Use case category | [registry — approved / conditional / never / not present] | [new use case] | 🟢 Covered / 🟡 Gap / 🔴 Conflict |
| Scope of AI use | [ポリシーが AI が使われると言うこと] | [new use] | |
| Automated decisions | [自動化に関するポリシーポジション] | [is this automated?] | |
| Disclosure to affected parties | [ポリシーがコミットすること] | [これが要求するもの] | |
| Vendor data use | [ベンダー AI に関するポリシーポジション] | [このベンダーの条項] | |
| Human oversight | [ある場合のポリシー声明] | [実際にあるもの] | |

### Direct query 出力フォーマット

```markdown
# AI Policy Check: [Proposed practice in one line]

**Bottom line:** [POLICY UPDATE REQUIRED / ADVISABLE / NO UPDATE NEEDED]

---

## What's covered

[提案されたプラクティスの既に対処されている側面 — 簡潔、変更不要を確認]

## What's missing

### [Gap 1]

**Current policy:** [quote or "Silent"]
**What's needed:** [なぜこのギャップが重要か — 法的、評判的、または期待の理由]

**Suggested language:**
> *Add to [section]:*
> "[ドラフトテキスト]"

### [Gap 2]
[same format]

## What conflicts

### [Conflict 1 — if any]

**Current policy says:** [quote]
**Proposed practice does:** [what conflicts]
**Resolution:** [どちらが変わる必要があるか — 通常、実務がポリシーに合うよう調整される、またはポリシーが防御可能な新しいポジションに更新される; 両方をサイレントに受け入れることはない]

---

## Use case registry

[このユースケースが台帳にない場合: 「`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → ユースケース台帳に追加:」]
```
| [use case] | [Approved/Conditional] | [conditions] | — |
```

---

## Timing

[REQUIRED: 「このプラクティスが稼働する前にポリシー更新が行われるべき — 既に実行中の場合は直ちに。」
ADVISABLE: 「続行可能; 次のポリシー更新で更新。」]
```

---

## 推奨言語の品質基準

AI ポリシー言語は古くなる傾向が異常に強い — フィールドが速く動き、曖昧な言語は具体的なコミットメントよりも年齢に耐えます。ドラフト時:

- 既存ポリシーの声と style に一致させる(実際の文書を読む)
- 耐久性のある言語を優先: 変わる特定のモデルを名前で呼ぶ代わりに「AI 支援」; 技術的記述の代わりに「自動または AI 支援決定」
- チームが守れないコミットメントをドラフトしないこと — 「我々は常に人間が AI 出力をレビューする」は、1 つの自動ワークフローが出荷された瞬間に壊れます
- ポリシーポジションが本当に変わっている(単に拡張ではない)場合、明示的にそう述べる: 「この更新は、我々が今や [新カテゴリ] で AI を使用することを反映する — 以前の言語はこれをカバーしなかった。」
- 開示言語の場合: 影響を受ける関係者(従業員、顧客)が読めるようにドラフト、単に法的に正確であるだけではない

常にどのセクションに追加するかを述べます。正しいセクションが存在しない場合、作成を提案し、ヘッダーをドラフトします。

---

## スケジュール統合

週次スイープは定期ケイデンスで実行するように設計されています。自身のスケジューラーで `/ai-governance-legal-ja:policy-monitor` を週次実行する定期リマインダーをセットアップします。スケジュール実行には scheduled-tasks 統合が必要ですが、このプラグインには同梱されていません。

各スイープの後、`## Outputs` の **Last policy sweep** と **gaps_found** フィールドは、人間がスイープ結果を確認した後にのみ更新されます(上記「スコープを決定」参照)。

---

## Close with the next-steps decision tree

CLAUDE.md `## Outputs` のデシジョンツリーで終わります。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X, escalate, get more facts, watch and wait, something else)は出発点であり、ロックインではありません。ツリーが出力です; 弁護士が選びます。

## このスキルがしないこと

- ポリシー自体を更新しないこと — 推奨言語をドラフトし、決定をフラグしますが、人間がレビューしてすべての変更を承認します。
- 到来する規制を捕まえないこと — それは `reg-gap-analysis` です。このスキルは内部のプラクティスドリフトを監視し、外部の法的変更ではありません。
- 出力が保存されることを強制しないこと — AIA とトリアージ結果が設定されたフォルダーに保存されていない場合、スイープはそれらを見つけません。Direct-query モードは保存された出力なしで動作します。
- メール、Slack、または非公式決定を読まないこと — 設定されたフォルダーに保存された構造化された出力のみ。
- ユースケース台帳を自動更新しないこと — 台帳ギャップをフラグし、人間レビューのためにエントリをドラフトしますが、追加前です。
