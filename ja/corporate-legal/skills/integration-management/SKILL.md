---
name: integration-management
description: >
  クロージング後 M&A インテグレーション・トラッカー — 段階別ワークプラン、consent 追跡、契約譲渡の大規模実行、週次ステータスレポート。
  利用可能なディール成果物(株式譲渡契約、ディールサマリー、クロージング・チェックリスト)から初期化し、
  M&A コールドスタートの deal-context.md と closing-checklist.yaml と連携します。
  Use when user says "integration", "post-close", "post-closing", "consents outstanding",
  "contract assignment", "integration status", or "what's left on the deal".
  日本語トリガー: 「インテグレーション」「クロージング後」「ポストクローズ」「未取得 consent」「契約譲渡」「統合状況」「ディール残務」。
argument-hint: "[--init | --contracts | --report | --update | --export [--format csv|table] [--section all|consents|contracts|workplan]] [--deal [code]]"
---

# /integration-management

1. `deal-context.md` をロード:ディールコード、ターゲット、クローズ日、ディールリード。
2. `integration-tracker.yaml` をロード(あれば)、または `--init` で作成。
3. 以下のワークフローを使用。
4. フラグでルーティング:
   - `--init`: Mode 1 — PA を読み、段階別ワークプラン、consent トラッカーを構築
   - `--contracts`: Mode 2 — 契約リスト取り込み(リポジトリまたはアップロード)、tier 化と分類
   - `--report`: Mode 3 — ステータスレポート生成
   - `--update`: Mode 4 — 手動更新またはアップロードされたステータス文書のパース
   - `--export`: Mode 5 — CSV またはテーブルエクスポート
5. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/integration-tracker.yaml` を読み書き。
6. 書込み後:変更サマリーを表示し、新規フラグを表面化。

---

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

外部弁護士がディールをクローズします。法務は混乱を引き継ぎます。本スキルはクロージング後インテグレーションのプログラムマネジメントレイヤー — ビジネスインテグレーション、IT システム、HR 組織設計ではありません。法務ワークストリーム:consent、契約譲渡、法人合理化、IP recordal、PA 義務。何が完了、何が期限、何がブロック、何が判断要かを追跡します。

---

## トラッカーファイル

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/integration-tracker.yaml` に保存。ディールコード、ターゲット名、クローズ日、ディールリードについては `deal-context.md` を読む。`closing-checklist.yaml` が存在すれば post-close 項目を継承。

```yaml
# integration-tracker.yaml

metadata:
  deal_code: "[code]"
  target: "[company name]"
  close_date: "[YYYY-MM-DD]"
  deal_lead: "[name]"
  outside_counsel: "[firm and lead attorney]"
  last_updated: "[date]"
  last_status_report: "[date or null]"

pa_dates:
  required_consents_deadline: "[YYYY-MM-DD — extract from PA]"
  rep_survival_expires: "[YYYY-MM-DD]"
  escrow_release: "[YYYY-MM-DD or null]"
  earnout_milestones:
    - description: "[milestone]"
      measurement_date: "[YYYY-MM-DD]"
      payment_date: "[YYYY-MM-DD]"
      owner: "finance"   # always finance — legal tracks date only

workplan:
  day_1:
    target_date: "[close_date + 7 days]"
    items: []
  day_30:
    target_date: "[close_date + 30 days]"
    items: []
  day_90:
    target_date: "[close_date + 90 days]"
    items: []
  day_180:
    target_date: "[close_date + 180 days]"
    items: []

required_consents: []
desired_consents: []

contracts:
  source: "[repository / manual-upload / disclosure-schedule]"
  repository_path: "[path or null]"
  last_imported: "[date]"
  total: 0
  tier_1: []
  tier_2: []
  tier_3: []
  tier_4: []
```

**ワークプラン項目の構造:**
```yaml
- id: "W-001"
  description: "[action item]"
  phase: "[day_1 / day_30 / day_90 / day_180]"
  owner: "[legal-owns / legal-supports]"
  workstream: "[legal / hr / it / finance / real-estate / other]"
  priority: "[critical / high / medium / low]"
  deadline: "[YYYY-MM-DD or null]"
  deadline_basis: "[pa-obligation / regulatory / best-practice]"
  status: "[not_started / in_progress / complete / blocked / deferred]"
  blocker: "[description or null]"
  depends_on: "[item id or null]"
  notes: ""
```

**Consent エントリ構造:**
```yaml
- id: "CON-001"
  counterparty: "[name]"
  contract_type: "[customer / vendor / lease / IP-license / financial / other]"
  required_consent: true        # true = named in PA Required Consents schedule
  pa_deadline: "[YYYY-MM-DD]"   # only for required_consent: true
  status: "[not_started / outreach_sent / in_negotiation / obtained / waived / refused]"
  assigned_to: "[name or null]"
  outreach_date: "[date or null]"
  obtained_date: "[date or null]"
  notes: ""
```

**Contract エントリ構造:**
```yaml
- id: "C-001"
  name: "[contract name or filename]"
  counterparty: "[party name]"
  contract_type: "[MSA / SaaS / lease / IP-license / employment / NDA / other]"
  annual_value: "[amount or unknown]"
  assignment_mechanism: "[auto-assign / consent-required / coc-provision / silent]"
  tier: 1   # 1=Required Consent, 2=material+consent-required, 3=CoC, 4=auto-assign
  required_consent: false
  pa_deadline: "[YYYY-MM-DD or null]"
  status: "[not_reviewed / no_action / consent_pending / outreach_sent / in_negotiation / consent_obtained / assignment_complete / waived / refused / coc_triggered]"
  assigned_to: "[name or null]"
  notes: ""
  last_updated: "[date]"
```

---

## Mode 1: 初期化

```
/corporate-legal-ja:integration-management --init [--deal [code]]
```

### Step 1: ディールコンテキストをロード

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/deal-context.md` を読む。見つからない:ディールコード名、ターゲット会社、クローズ日、ディールリード、外部弁護士を尋ねる。存在しなければ deal-context.md に書き出す。

存在すれば `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/closing-checklist.yaml` を読む。post-closing とマークされた項目は Day 1 または Day 30 ワークプラン項目になる(ステータスを closing-checklist から継承)。

### Step 2: ディール入力を読む

**完全な株式譲渡契約が最も完全なトラッカーを生成。** PA の Required Consents 別紙とクロージング後 covenants セクションがハード期限と法的義務の正典ソース。ただし利用可能なものから有用に初期化可能 — 部分入力は空のページではなく弁護士が埋めるスタータトラッカーを生成。

> 利用可能なディール成果物は何ですか? 存在するものを共有:
>
> **理想:** 株式譲渡契約(アップロードまたは接続文書パス)。クロージング後 covenants、Required Consents 別紙、survival 期間、エスクロー条件、earn-out 条項を読みます。
>
> **また有用 — 以下の組合せを共有:**
> - ディールサマリーまたは term sheet(主要 economics とタイムラインを得る)
> - 外部弁護士からのインテグレーション to-do リストまたは post-close チェックリスト
> - 既存ワークプランまたはインテグレーション・トラッカー(取り込み、続行します)
> - クロージング・チェックリスト — M&A コールドスタートスキルが生成したものは `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/closing-checklist.yaml` から自動継承
> - Required Consents リストのみ(PA を外部弁護士が保持している場合)
>
> **書き留めたものが何もない場合:** ディールを平易な言葉で教えてください — 誰を買収、いつクローズ、主なオープン項目は何 — 標準 Day 1/30/90/180 ワークプランからスタータトラッカーを構築、編集してください。

**何が提供されたかに基づき変わるもの:**

| 入力 | 得られるもの |
|---|---|
| Full PA | 完全ワークプラン + 期限付き Required Consents + PA 日付 |
| PA + 契約リスト | フルトラッカー + 契約譲渡 tier リスト |
| ディールサマリー / to-do リスト | 標準ワークプラン骨格、Required Consents プレースホルダー |
| なし | 標準ワークプランの足場;弁護士が consent と契約リストを埋める |

トラッカーは段階的に構築されるよう設計 — 今日の骨格、情報が増えるごとに埋められる。

**PA から抽出:**

*Required Consents 別紙:*
- 各 consent について:相手方名、契約タイプ、契約上の期限。required_consent: true、pa_deadline populated で設定。

*クロージング後義務:*
- 各義務をワークプラン項目にマップ。期限に基づき正しい phase に割当。deadline_basis に pa-obligation タグ。

*主要日付:*
- Required Consents 期限 — PA から抽出
- 表明保証 survival 期限 — PA から具体的 survival 期間を引く。一般、fundamental、税務 reps は通常異なる survival 期間を持つ;PA が定義する各々を引き、別々に記録。デフォルトを仮定しないこと。
- エスクロー解放日 — PA から抽出
- earn-out 測定日と支払日 — pa_dates.earnout_milestones に追加、owner は常に "finance" に設定

### Step 3: 段階別ワークプランを構築

各 phase の標準ワークプラン項目を生成。Step 2 で抽出した PA 義務を追加。クロージング・チェックリストから継承した項目は pre-populated。

**Day 1 — legal-owns:**
- Entity name change filing(取得エンティティが renamed される場合)[priority: critical]
- 銀行口座 signatory 更新 — クロージング書類で銀行に通知 [priority: critical]
- 登録代理人への所有権変更通知 [priority: high]
- 主要 IP assignment 実行 — クロージングから繰延された IP assignment があれば [priority: critical]
- ドメイン名およびソーシャルメディアアカウントの移転 [priority: high]
- D&O 保険 — 取得エンティティ取締役向け tail policy が bound されたことを確認 [priority: critical]
- 州法で要求される Secretary of State 所有権通知 [priority: high]

**Day 1 — legal-supports:**
- 従業員アナウンスとコミュニケーション(HR が所有、法務がレビュー)[priority: critical]
- Benefits day-1 補償確認(HR が所有、法務が COBRA とプラン条件についてアドバイス)
- 顧客コミュニケーションレター(ビジネスが所有、法務が正確性をレビュー)

**Day 30 — legal-owns:**
- Required Consents 初期プッシュ — 全相手方に連絡、アウトリーチを記録 [priority: critical]
- USPTO での IP assignment recordal(特許、商標)[priority: high]
- 著作権 assignment 申請 [priority: medium]
- 商標 assignment 記録 [priority: high]
- 重要契約レビュー — tier 1 と tier 2 の契約譲渡分析を完了 [priority: high]
- 保険 tail policy 最終確認 [priority: high]

**Day 30 — legal-supports:**
- データ移行プライバシーレビュー(IT が所有、法務がデータ移転メカニズムについてアドバイス)
- assignment 規定に関する不動産リースレビュー(ファシリティが所有、法務がアドバイス)

**Day 90 — legal-owns:**
- Required Consents 期限 — すべての Required Consents は取得またはエスカレートされなければならない [priority: critical, deadline: pa_dates.required_consents_deadline]
- 法人合理化判断 — keep separate / merge / dissolve を推奨 [priority: high]
- Benefits plan 引継または終了文書 [priority: high]
- セカンダリ consent プッシュ — 残りの未取得 consent [priority: high]
- Tier 3 change of control 契約解決 [priority: critical]

**Day 90 — legal-supports:**
- フル HR 調和文書(HR が所有、法務が雇用法についてアドバイス)

**Day 180 — legal-owns:**
- Entity merger filing — 合理化判断が merge の場合 [priority: high]
- Entity dissolution filing — 合理化判断が wind down の場合 [priority: high]
- フル契約 novation — 買収者名を要する契約 [priority: high]
- Rep survival 追跡 — 今後の expiry 日を記録 [priority: medium]

生成後にサマリーを表示:

```
Integration tracker initialized — [Deal code] / [Target]

Close date: [date]
Required Consents deadline: [date] ([N] days from today)
Rep survival expires: [date]

Workplan items: [N] ([N] legal-owns, [N] legal-supports)
Required Consents: [N] (from PA schedule)
Desired Consents: [N] (from diligence — no PA deadline)

Contract assignment: not yet imported — run --contracts to populate

Next step: run /corporate-legal-ja:integration-management --contracts to import the
contract list, then --report to see your first status summary.
```

---

## Mode 2: 契約譲渡

```
/corporate-legal-ja:integration-management --contracts [--deal [code]]
```

これは専用の契約譲渡初期化。メインの init とは分離されているので、独立実行可能、契約リストが変わったら再実行可能。

### Step 1: 契約リストを取得

2 つのパス — 該当するものを使う:

**Path A: 接続済みリポジトリ**

> 契約リポジトリが接続済みですか?(Google Drive、Box、SharePoint、または post-close でもアクセス可能な VDR?)
>
> Yes なら:取得会社の契約のフォルダパスまたはフォルダ名を教えてください。そこにあるもののリストを引き、各契約の assignment 条項と相手方を読みます。

接続済みリポジトリを検索。見つかった各文書について:
- ファイル名とファイルパスを抽出
- 文書を読み、特定:contract party(相手方名)、contract type(ヘッダーまたは subject matter から)、assignment 条項テキスト、change of control 条項テキスト(あれば)、年額(記載があれば)。

**Path B: 手動リストアップロード**

> 契約リストをアップロード:
> - PA 開示別紙からの Material Contracts schedule
> - 契約管理システムからの CSV または Excel エクスポート
> - 手動で準備したリスト
>
> 最低必要カラム:Contract Name、Counterparty。あれば便利:Contract Type、Annual Value、Assignment 条項テキスト。

アップロードされたリストを読む。assignment 条項テキストが提供されていない契約は、assignment_mechanism を "not_reviewed" に設定し、フォローアップフラグ。

**Path C: 開示別紙**

リポジトリもリストも利用不可なら、PA 開示別紙(--init でアップロードされた PA)から Material Contracts schedule を読む。これは最低必要リスト — 当事者と契約タイプ — を提供。Assignment 条項は手動レビューが必要。

### Step 2: Assignment メカニズムを判定

各契約について、assignment メカニズムを分類:

| Mechanism | 定義 | Tier |
|---|---|---|
| `consent-required` | 相手方の consent なしの assignment を禁止する明示条項 | 1 or 2 |
| `coc-provision` | ディールによりトリガーされる相手方の解除権または consent 権を与える change of control 条項 | 3 |
| `auto-assign` | 制限なし、または affiliate / successor への assignment の明示許可 | 4 |
| `silent` | Assignment 条項なし — 準拠法のデフォルトに依存。契約が沈黙時の契約譲渡の準拠法デフォルトを調査し、controlling rule を引用。弁護士レビュー用フラグ。 | 2 |
| `not_reviewed` | Assignment 条項を読めない / 見つけられない | 手動レビュー用フラグ |

PA Required Consents 別紙にフラグされた契約:assignment メカニズム分類に関わらず tier 1 に上書き。

### Step 3: Tier 割当

```
Tier 1 — Required Consents: [N] contracts
  PA 別紙に名指し、ハード期限 [date]、consent 取得必須

Tier 2 — Material, consent required: [N] contracts
  Assignment 制限あり、PA 別紙にはない
  推奨タイムライン:Day 90 以内に取得

Tier 3 — Change of control provisions: [N] contracts ⚠️
  Counterparty がクローズによりトリガーされる解除権または consent 権を持つ
  ACTION REQUIRED: 即座に相手方に連絡 — CoC はすでにトリガーされている可能性

Tier 4 — Auto-assign / no action: [N] contracts
  Affiliate/successor 条項により自動譲渡
  追跡のみ — アウトリーチ不要

Not reviewed: [N] contracts
  Assignment メカニズムを判定できず — 手動レビュー要求
```

Tier 3 を別途、目立つように表示。Change of control 条項はクローズ日にすでにトリガー済みの可能性 — 相手方が今この瞬間に解除権を行使可能。

### Step 4: ステータスエントリを生成

各契約について、トラッカーエントリを作成:
- 抽出した全フィールド(相手方、タイプ、金額、メカニズム、tier)
- 初期ステータス:tier 4 → `no_action`;tier 3 → `coc_triggered`;tier 1/2 → `consent_pending`;not_reviewed → `not_reviewed`
- tier 1 については Required Consents 別紙から pa_deadline populated

---

## Mode 3: ステータスレポート

```
/corporate-legal-ja:integration-management --report [--deal [code]]
```

現在のトラッカー状態を読む。生成:

```
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

> 本ステータスレポートは株式譲渡契約、DD 所見、クロージング後インテグレーション記録から派生。それらの特権・秘密ステータスを継承 — 特権の輪を超えた配布は特権放棄を引き起こす可能性。送信前に受信者リストを確認してください。

INTEGRATION STATUS — [Deal code] / [Target]
[Date] — Day [N] post-close

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

EXECUTIVE SUMMARY
[2-3 sentence paragraph: overall status, biggest risk, key win since last report]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

REQUIRED CONSENTS  [deadline: DATE — N days remaining]
  Obtained:        [N] of [total]  ████████░░  [%]
  In negotiation:  [N]
  Outreach sent:   [N]
  Not started:     [N]
  Refused:         [N] ⚠️

⚠️ AT RISK: [counterparty] — deadline in [N] days, no response to outreach
⚠️ REFUSED: [counterparty] — PA obligation not met; escalate to outside counsel

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CONTRACT ASSIGNMENT
  Tier 1 (Required Consents):   [N] complete / [N] in progress / [N] pending
  Tier 2 (Material contracts):  [N] complete / [N] in progress / [N] pending
  Tier 3 (CoC provisions):      [N] resolved / [N] outstanding ⚠️
  Tier 4 (Auto-assign):         [N] — no action required

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

WORKPLAN — LEGAL OWNS
  🔴 OVERDUE ([N]):
    [item] — was due [date]

  ⏰ DUE THIS WEEK ([N]):
    [item] — due [date]

  ✅ COMPLETED SINCE LAST REPORT ([N]):
    [item] — completed [date]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

BLOCKERS & DECISIONS NEEDED
  [item] — blocked on: [description] — owner: [name]
  [item] — decision needed: [description] — recommend: [option]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

KEY DATES COMING UP
  [date] — [milestone / deadline]
  [date] — Rep survival expires — confirm no pending indemnification claims

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Mode 4: 更新

```
/corporate-legal-ja:integration-management --update [--deal [code]]
```

**手動更新:** 弁護士が Claude に何が変わったか伝える。

> 「Salesforce consent を取得。obtained とマーク、[name] に割当、日付は本日。」
> 「法人合理化判断は merge。ステータスを更新し、Day 180 に merger filing を追加。」
> 「[Counterparty] が consent を拒否。フラグして、これが PA 補償クレームをトリガーするかについて外部弁護士が必要と注記。」

Claude は関連トラッカーエントリを更新、下流ステータスを再計算(例:全 tier 1 consents が取得済みになったら、PA 義務を met としてフラグ)、何が変わったかを表示。

**アップロード更新:** ワークストリームオーナーまたは外部弁護士がステータス文書を送る。

> [外部弁護士 / HR リード / コーポレートディベロップメントチーム] からのステータス更新をアップロード。パースしてトラッカーを更新。

アップロード文書を読む。記述された項目を相手方名またはワークプラン項目記述でトラッカーエントリにマッチ。ステータスフィールドを更新。既存トラッカーエントリと合致しない更新内の項目をフラグ — 追加すべき新項目の可能性。

各更新後に表示:
```
Updated [N] items.

Changes:
  CON-003 Salesforce: not_started → obtained
  W-014 Entity rationalization: in_progress → complete

New flags:
  CON-007 [Counterparty]: refused — PA obligation may be unmet. Consider:
  outside counsel review of indemnification claim. ⚠️
```

---

## Mode 5: エクスポート

```
/corporate-legal-ja:integration-management --export [--format csv|table] [--section all|consents|contracts|workplan]
```

フラット CSV または markdown テーブルを生成。デフォルト:全セクション、CSV。

CSV フォーマット — 項目あたり 1 行、セクションは `section` カラムで表示。カラムはセクションで異なる:

*Workplan:* id, phase, description, owner, workstream, priority, deadline, status, blocker

*Consents:* id, counterparty, contract_type, required_consent, pa_deadline, status, assigned_to, obtained_date, notes

*Contracts:* id, name, counterparty, contract_type, annual_value, assignment_mechanism, tier, required_consent, pa_deadline, status, assigned_to, notes

エクスポートは共有可能フォーマット — 外部弁護士、corp dev、取締役会インテグレーション更新に適す。

---

## 本スキルがしないこと

- ビジネスインテグレーション・ワークストリーム(IT、HR、財務、不動産)を管理しない。それらのワークストリーム内の法務タッチポイントを追跡し、法務インプットが必要な時にフラグ。所有権はビジネス機能に残る。
- consent request letters または novation 契約をドラフトしない — それらは written-consent スキルまたは外部弁護士が生成。
- 補償クレームまたは PA 違反についてアドバイスしない。consent が拒否されたり期限を逃した時、状況をフラグ — 帰結の法的分析は弁護士の仕事。
- earn-out のパフォーマンスを追跡しない。earn-out マイルストーンと支払日は finance に owner 設定された参照日付としてトラッカーに表示。数値はビジネスが駆動。
- ステータスレポート時にリアルタイムで契約を読まない。契約ステータスは弁護士がトラッカーで更新したもの。スキルはレポート時に契約ではなくトラッカーを読む。


## Formula injection 対策

Excel、Sheets、CSV 出力でセルを書き込む前に、formula injection を無害化する。相手方由来テキスト(契約引用、当事者名、登録代理人データ、CLM エクスポート)は攻撃者制御。`=`、`+`、`-`、`@`、`	`、`
`、または `
` で始まるセルは数式として解釈されるか、行構造を壊す。

- **シングルクオートで prefix:** `'=SUM(A1:A10)` → `=SUM(A1:A10)`(実行されずテキストとして表示)
- **文書、ツール結果、ユーザー貼り付けからのテキストを含むすべてのセルに適用。** あなたが制御するカラムヘッダーと生成する計算値は安全。
- **CSV: 埋め込みコンマ、ダブルクォート、改行もエスケープ**(RFC 4180 quoting)。
- これはオプションではない。マクロをトリガーするか DDE 経由でデータを exfiltrate するスプレッドシートをユーザーが Excel で開く事態は、ユーザーへのサプライチェーン攻撃。
