---
name: entity-compliance
description: >
  法人コンプライアンス・トラッカー — 初期化、今後の期限レポート、ステータス更新、ヘルス監査、CSV エクスポート。
  法人テーブルから構築した compliance-tracker.yaml を維持し、法人別・法域別の申請期限を計算し、
  今後 30/60/90 日に期限が来るものを表面化します。
  Use when user says "entity compliance", "filing deadlines", "annual reports due", "entity tracker",
  "what filings are due", "entity health", or "good standing".
  日本語トリガー: 「法人コンプライアンス」「申請期限」「年次報告書の期限」「法人トラッカー」「期限の確認」「法人ヘルス」「good standing」。
argument-hint: "[--init | --report [--days N] | --update [--from-report] | --sweep | --audit | --export [--format csv|table]]"
---

# /entity-compliance

1. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → `## Entity Management`(法人テーブル、法域、登録代理人)をロード。
2. フラグに基づき以下の正しいモードにルーティング:
   - フラグなしまたは `--init`: Mode 1 — 法人テーブルからトラッカーを初期化
   - `--report`: Mode 2 — 今後の期限と期限超過項目を表面化
   - `--update`: Mode 3a(手動)または 3b(--from-report アップロード) — ステータス更新
   - `--sweep`: Mode 3c — unknown/overdue 項目を 1 件ずつ歩く
   - `--audit`: Mode 4 — フルヘルス監査
   - `--export`: Mode 5 — CSV またはテーブルエクスポートを生成
3. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/entities/compliance-tracker.yaml` を読み/書き。
4. 各更新後:変更サマリーと次のアクションを表示。

---

## 目的

年次報告書、franchise tax(法人特権税)、Statements of Information(SOI / 情報届出書)、隔年申請 — どの州のどの法人もそれぞれの予定と期限超過の帰結を持ちます。本スキルは何が、いつ、どの法人について期限なのかを知る単一の YAML トラッカーを維持します。設計上軽量です:トラッカーはあなたが所有するファイル、Claude はコマンドで更新、共有が必要な時にエクスポートします。

## 重要:期限参照の注意

> 本スキルの参照テーブルの申請期限は、スキルのビルド日時点で公開されている要件を反映しています。州の申請要件と期限日は変更され得ます。**コンプライアンス目的で依拠する前に、必ず登録代理人または関連 Secretary of State に直接期限を確認してください。** CT Corp、National Registered Agents、その他の登録代理人サービスを使用する場合、彼らのコンプライアンスカレンダーがあなたの具体的法人に対する正典です — 彼らのデータを整理・表面化するために本トラッカーを使い、置き換えないこと。

## 法域の前提

> 本トラッカーは、法人ごとに記録された formation / qualification の州または国に対して期限を計算します。申請ルール、期限日の仕組み、手数料構造は法域により大きく異なります。法人の実際のフットプリントが `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` のものと異なる場合(未開示の外国資格、解散済み法人、法域再ドメスティケーション、ローカル代理人が管理する国際申請)、出力は記述通りに当てはまらない可能性があります — 当該法域の登録代理人または現地弁護士に確認してください。

## 法人類型の曖昧性除去(特に Delaware)

> 申請カレンダーは法域だけでなく**法人類型**に依存します。「Delaware エンティティ」を単一のバケットとして扱うのはよくある、かつ結果重大なエラー — DE corporation、DE LLC、DE LP は異なる申請、異なる期限、異なる miss の帰結を持ちます。期限を計算または報告する前に法人テーブルから法人類型を確認し、同じ州内で 1 つの法人類型の期限を別の法人類型にコピーしないこと。
>
> **Delaware — 重要な分岐:**
>
> - **DE Corporation (Inc., Corp.):** 年次報告書 AND franchise tax、両方とも **3 月 1 日**期限。Franchise tax は authorized-shares 方式または assumed-par-value capital 方式のいずれか低い方で計算;年次報告書は director / officer 情報を取得。Statutory basis: 8 Del. C. §§ 501–502(米国デラウェア州一般会社法 501-502 条)[verify current]。
> - **DE LLC:** 年次報告書不要。年次税は**定額 $300**、**6 月 1 日**期限。Statutory basis: 6 Del. C. § 18-1107(d) [verify current fee and date]。
> - **DE LP:** 年次報告書不要。年次税は**定額 $300**、**6 月 1 日**期限(LLC ルールと平行)。Statutory basis: 6 Del. C. § 17-1109 [verify current]。
>
> DE LLC は 3 月 1 日の年次報告書提出を**要求されません** — LLC に対してその期限を書くことは実リスクを伴う(実際の 6 月 1 日エクスポージャーを覆い隠す偽の「overdue」フラグ、または逆に、3 月 1 日の corporation ルールが普遍と扱い 6 月 1 日の LLC 期限を逃すユーザー)。法人テーブルが Delaware エンティティを type なしで記録している場合、`type_unknown` でフラグしてユーザーに確認を求め、どちらの期限を計算する前にも確認してください。
>
> 同じ法人類型ディシプリンは、法人類型ごとに分岐する申請レジームを持つ他の法域すべてに適用される(例:CA corp Statement of Information vs CA LLC SOI のケイデンス;TX franchise tax は corporation、LLC、LP に適用されるが、異なる no-tax-due 閾値で)。法域の参照テーブルが populated されている場合、州だけでなく法人類型で index されていることを確認。

---

## トラッカーファイル

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/entities/compliance-tracker.yaml` に保存。構造:

```yaml
# Entity Compliance Tracker
# Generated: [date]
# Last updated: [date]
# Disclaimer: deadlines are reference only — confirm with registered agent or Secretary of State

metadata:
  company: "[Company Name]"
  generated: "[date]"
  last_updated: "[date]"
  last_audit: "[date or null]"

custom_jurisdictions:   # manually added — US states or countries not in built-in reference table
  []                    # populated when a new jurisdiction is encountered

entities:
  - name: "[Entity Name]"
    type: "[Corporation / LLC / LP / other]"
    state_of_formation: "[state]"
    formation_date: "[date or null]"
    status: "[active / dormant / dissolving]"
    registered_agent: "[CT Corp / National / in-house / other]"
    notes: ""

    jurisdictions:
      - state: "[state]"
        qualification: "[domestic / foreign]"
        qualified_date: "[date or null]"
        agent_managed: false   # set true for international entities where a local agent handles compliance
        local_agent: "[name or null]"
        filings:
          - type: "[Annual Report / Franchise Tax / Statement of Information / Biennial Statement / other]"
            due_date: "[YYYY-MM-DD]"
            due_basis: "[fixed date / anniversary month / other]"
            last_filed: "[date or null]"
            last_fee: "[amount or null]"
            status: "[current / due_soon / overdue / unknown]"
            confirmed_good_standing: "[date or null]"
            notes: ""
```

Status 値:
- `current` — 現期間について提出済み、90 日以内に期限なし
- `due_soon` — 90 日以内に期限
- `overdue` — 期限超過で last_filed 日付が記録されていない
- `unknown` — 情報なし;手動確認要

---

## Mode 1: 初期化

トラッカーが存在しない時、または `--rebuild` でゼロから再生成する時に実行。

### Step 1: 法人テーブルをロード

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → `## Entity Management` → 法人テーブルを読む。法人テーブルが populated されていれば(コールドスタート時の組織図アップロードから)、直接使う。されていなければ、ユーザーにコールドスタートモジュールを実行するか、法人リストを提供するよう求める。

### Step 2: 各エンティティ × 法域について、申請要件を確認

各エンティティについて、現在の申請スケジュールを登録代理人または関連 Secretary of State で確認。州の申請スケジュールは変動する(一部の州は固定日付から記念日ベースのスケジュールへ、またその逆へ移行、手数料構造改定、申請カテゴリ再分類)。キャッシュされたスケジュールに依拠しないこと。下記トラッカーはあなたが確認した日付を記録;登録代理人がリマインダーを送ってきたら更新する。

エンティティが登録されている各法域について(domestic または foreign):

1. ユーザーに、登録代理人からの現行コンプライアンスレポートがあるか尋ねる — それが最も信頼できるソース。
2. なければ、ユーザーに知っていることを尋ねる(申請タイプ、期限基準、最終提出日、典型手数料)。提供されたものを記録。
3. ユーザーが知らないものについては、エンティティ × 法域エントリを `unknown` としてフラグ — キャッシュ参照から日付を populate しない。ユーザーの次のステップは登録代理人または Secretary of State に確認すること。

**参照テーブルではなくトラッカーに詳細をキャプチャ:**

> [Jurisdiction] の申請要件が参照テーブルにありません。今後追跡できるよう取得しましょう。
>
> [Entity] / [Jurisdiction] について:
> 1. 必要な申請タイプは?(年次報告書、franchise tax、confirmation statement、annual return、その他?)
> 2. 期限は?(May 1 のような固定日付、記念月、その他?)
> 3. 典型手数料は?(概算で OK — または「unknown」。)
> 4. そこでの登録代理人またはローカル申請代理人は誰?

回答をトラッカーの `custom_jurisdictions` ブロックに保存:

```yaml
custom_jurisdictions:
  - jurisdiction: "[State / Country]"
    jurisdiction_type: "[US state / Canada province / EU member state / other]"
    filings:
      - type: "[filing type]"
        due_basis: "[fixed: MM-DD / anniversary month / other description]"
        typical_fee: "[amount or unknown]"
        notes: "[any other relevant information — e.g., local agent required, filing in local language]"
    added_by: "manual"
    added_date: "[date]"
```

このカスタム定義はその後その法域内のすべてのエンティティに適用される。将来の `--init` 実行とエンティティ追加は自動でそれを使用する。

**国際法域は特に:**

国際申請は法域により大きく異なる。常に上記のカスタム定義フローを通る — トラッカーを populate する前にローカル申請代理人または registered office agent で申請タイプ、ケイデンス、手数料を確認。

国際エンティティについて、追加で尋ねる:
- コンプライアンスを処理するローカル申請代理人または registered office agent はいるか? いれば、代理人名を記録 — 独立に期限日を計算せずに、彼らとフォローアップするタイミングをトラッカーがフラグできる。
- この法域でグループレベルレポートの申請(例:国別報告、実質的所有権登記、経済実体申請)が要求されるか?

ローカル代理人を持つ国際エンティティをトラッカー内で `agent_managed: true` でフラグ。Report モードはそれらを別途リストし、計算された期限日を表示するのではなく、ローカル代理人にステータスを確認するよう注記する。

記念日ベースの申請の場合:トラッカーの formation_date から計算。formation_date が null:ステータスを `unknown` にしフラグして確認要。

### Step 3: トラッカーを書き出し

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/entities/compliance-tracker.yaml` を、全エンティティと計算された申請要件で生成。初期ステータスを設定:
- `current`、last_filed が現在の申請期間内
- `due_soon`、90 日以内に期限で現在期間の last_filed がない
- `overdue`、期限が過ぎ現在期間の last_filed がない
- `unknown`、formation_date が欠落または州が参照テーブルにない

生成後にサマリーを表示:

```
Entity compliance tracker initialized.

Entities: [N]
Total jurisdictions: [N]
Filings tracked: [N]

Status summary:
  ✅ Current:   [N]
  ⏰ Due soon:  [N] (next 90 days)
  🔴 Overdue:   [N]
  ❓ Unknown:   [N] (confirm with registered agent)

Run /corporate-legal-ja:entity-compliance --report to see what's due.
```

---

## Mode 2: レポート

今後の期限を表面化し、期限超過項目をフラグ。デフォルト:今後 90 日。

```
/corporate-legal-ja:entity-compliance --report [--days 30|60|90|180]
```

出力フォーマット:

```
ENTITY COMPLIANCE REPORT — [date]
[Company Name]

🔴 OVERDUE ([N]):
  [Entity] / [State] / [Filing type] — was due [date]

⏰ DUE WITHIN [N] DAYS ([N]):
  [Entity] / [State] / [Filing type] — due [date]  [registered agent]
  [Entity] / [State] / [Filing type] — due [date]

✅ RECENTLY FILED ([N] in last 90 days):
  [Entity] / [State] / [Filing type] — filed [date]

❓ UNKNOWN STATUS ([N]):
  [Entity] / [State] / [Filing type] — no information; confirm with registered agent

🌐 AGENT-MANAGED ([N]):
  [Entity] / [Country] / [Filing type] — managed by [local agent]; confirm status directly
  [Entity] / [Country] — no local agent recorded; add one with --update

GOOD STANDING:
  Last confirmed: [date]
  Entities with confirmed good standing: [N] of [total]
  Entities not confirmed in last 12 months: [list]
```

トラッカーが約 10 エンティティ超をカバーするか、ユーザーが求めた時:ダッシュボードを提案(CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs` 参照)。本出力向けに提案を形成する — 申請ステータス別カウント(overdue / due soon / filed / unknown)、good-standing 州別カウント、法域・申請タイプ・次回期限日付きのソート可能法人テーブル。

---

## Mode 3: 更新

トラッカー内の 1 つまたは複数のエンティティを更新。3 つのサブモード:

### Consequential-action gate(SOI / 年次報告書を提出)

**提出を指示または確認する前に:** `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の `## Who's using this` を読む。Role が **Non-lawyer** の場合:

> Secretary of State に Statement of Information、年次報告書、franchise tax 申告書を提出することは法的帰結を持ちます — エンティティからの公式表明であり、手数料を伴い、提出漏れや不正確な提出は good standing の喪失や franchise tax デフォルトを引き起こす可能性があります。提出前に弁護士(または有資格の登録代理人)とレビュー済みですか? Yes なら進めて申請を記録。No なら、彼らに持って行くブリーフ:
>
> - エンティティ、法域、申請タイプ、期限日
> - 最後の提出についてトラッカーが言うこと(日付、手数料、最後に報告された役員 / 取締役情報)
> - オープンな質問(役員 / 取締役情報はまだ正確か、登録代理人は変わったか、主たる事務所は変わったか)
> - 何が悪い結果になり得るか(古い役員情報、franchise tax または解散をトリガーする期限超過、手数料計算エラー)
> - 弁護士に聞くこと(今年実際に提出が必要か、反映が必要な定款修正や役員変更があるか、誰が署名すべきか)
>
> 弁護士、ソリシター、バリスター、その他認可された法律専門家を見つける必要があれば:あなたの専門規制者(米国の州弁護士会、英国(イングランド・ウェールズ)の SRA / Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの Law Society、または当該法域の同等機関)に紹介サービスを問い合わせてください。

このゲートを明示的な Yes なしに通過した新規 `last_filed` 日付の記録は行わないこと。トラッカー読込、期限レポート、「what's due soon」出力はゲート不要。

### 3a: 手動更新

```
/corporate-legal-ja:entity-compliance --update
```

弁護士が Claude に何を提出したか伝える:
> 「[Entity] の Delaware 年次報告書を 3 月 1 日に提出。手数料は $450。」

Claude が更新:
- `last_filed` → 3 月 1 日
- `last_fee` → $450
- `status` → `current`
- メタデータの `last_updated`

### 3b: 登録代理人レポートのアップロード

```
/corporate-legal-ja:entity-compliance --update --from-report
```

ユーザーが CT Corp、National Registered Agents、または同等のコンプライアンスレポート(PDF、CSV、Excel)をアップロード。Claude が読み、合致するエンティティを更新:

レポートから各エンティティについて抽出:
- 申請タイプと期限
- 最終提出日(あれば)
- Good standing ステータスと確認日
- 代理人からのフラグまたは警告

レポートエンティティをトラッカーエンティティに名前でマッチ(近似マッチは確認用にフラグ — "Acme Holdings LLC" vs "Acme Holdings, LLC" は同じエンティティの可能性)。

処理後:
```
Updated [N] entities from report.

Matched: [N]
Unmatched (in report, not in tracker): [list — may need to add to entity table]
Not in report (in tracker, no update): [list — status unchanged]
```

### 3c: 一括ステータススイープ

```
/corporate-legal-ja:entity-compliance --sweep
```

`unknown` または `overdue` ステータスの各エンティティを 1 件ずつ歩き、現在情報を尋ねる:

> [Entity] / [State] / [Filing type] — 現在 [status] と表示。
> 提出済みですか? Yes ならいつで手数料は何でしたか?

各確認後にトラッカーを更新。完了サマリーを生成。

---

## Mode 4: ヘルス監査

```
/corporate-legal-ja:entity-compliance --audit
```

申請ステータスを超えるより広いレビュー。表面化:

**Filing compliance:**
- Overdue 項目(report モードから)
- Unknown ステータス項目

**Entity health:**
- `dormant` とマークされたエンティティ — レビュー用にフラグ:解散すべきか? 休眠エンティティの保有はコスト(年次手数料、登録代理人手数料)を生み、継続的コンプライアンス義務を作る。
- formation_date が 5 年以上前で status が `dormant` のエンティティ — 解散候補としてフラグ。
- formation_date 欠落のエンティティ — データギャップとしてフラグ。

**Good standing ギャップ:**
- `confirmed_good_standing` 日付のないエンティティ — good standing にあるか不明;取引が短い予告で証明書を要求する場合のリスク。
- `confirmed_good_standing` が 12 か月超のエンティティ — 古い;特に M&A やファイナンスが見込まれる場合は更新する価値あり。

**外国資格ギャップ:**
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` 法人テーブルに基づく:会社の運営フットプリント(オフィス、従業員)にある州で、エンティティが外国資格を持っていない州はあるか? これは弁護士による運営プレゼンスの確認を要求 — Claude は質問をフラグできるが、独立にプレゼンスを判断できない。

**社内契約ギャップ:**
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` から:社内契約が partial または no とマークされている場合、どのエンティティ関係が契約を要する可能性があるかをフラグ(親子サービス、IP ライセンス、ローン)。

出力フォーマット:

```
ENTITY HEALTH AUDIT — [date]

FILING COMPLIANCE
  Overdue: [N]
  Unknown status: [N]
  Action: run --sweep to confirm unknown items

DORMANT ENTITIES ([N])
  [List of dormant entities with age and annual carrying cost if known]
  Dissolution candidates (>5 years dormant): [list]

GOOD STANDING
  No record: [N] entities
  Stale (>12 months): [N] entities
  Consider refreshing before: [any upcoming transactions or contract renewals if known]

POTENTIAL GAPS
  Foreign qualification: [flag question — confirm operational presence in:]
    [list of states from `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` footprint not in tracker as qualified]
  Intercompany agreements: [status from `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md`]

RECOMMENDED ACTIONS
  1. [Highest priority action]
  2. [etc.]
```

---

## Mode 5: エクスポート

```
/corporate-legal-ja:entity-compliance --export [--format csv|table]
```

フィナンス、リーガルオペ、外部登録代理人と共有可能なフラットエクスポートを生成。デフォルト:CSV。

CSV カラム:
`Entity Name, Entity Type, State of Formation, Formation Date, Status, Registered Agent, Jurisdiction, Qualification Type, Filing Type, Due Date, Last Filed, Last Fee, Good Standing Confirmed, Notes`

法域あたり 1 申請 1 行。エンティティあたり複数行(法域 × 申請タイプ組合せあたり 1 行)。

`--format table` の場合:レポートまたは Slack メッセージに貼り付け可能な markdown テーブルを生成、今後 90 日の申請のみを表示。

---

## 本スキルがしないこと

- 何も提出しない。出力はトラッカーと to-do リスト;提出は弁護士、外部弁護士、または登録代理人が行う。
- good standing certificates を引かない。最終確認日を追跡;取得は手動または登録代理人経由。
- 特定州で外国資格が必要かを判断しない。その分析は事業活動の事実に依存し、弁護士が確認する必要がある。
- 複雑な多エンティティ構造を持つ会社の登録代理人サービスを置き換えない。CT Corp、National Registered Agents、同等のサービスは専任コンプライアンスチームと直接の州関係を持つ。本スキルは代理人サポートのない小規模組織、または代理人サポートを持つ組織向けの代理人データ上の軽量レイヤーに最適。
- 申請期限の参照テーブルは法的助言ではなく、現行要件を反映していない可能性がある。依拠する前にすべての期限を確認。


## Formula injection 対策

Excel、Sheets、CSV 出力でセルを書き込む前に、formula injection を無害化する。相手方由来テキスト(契約引用、当事者名、登録代理人データ、CLM エクスポート)は攻撃者制御。`=`、`+`、`-`、`@`、`	`、`
`、または `
` で始まるセルは数式として解釈されるか、行構造を壊す。

- **シングルクオートで prefix:** `'=SUM(A1:A10)` → `=SUM(A1:A10)`(実行されずテキストとして表示)
- **文書、ツール結果、ユーザー貼り付けからのテキストを含むすべてのセルに適用。** あなたが制御するカラムヘッダーと生成する計算値は安全。
- **CSV: 埋め込みコンマ、ダブルクォート、改行もエスケープ**(RFC 4180 quoting)。
- これはオプションではない。マクロをトリガーするか DDE 経由でデータを exfiltrate するスプレッドシートをユーザーが Excel で開く事態は、ユーザーへのサプライチェーン攻撃。
