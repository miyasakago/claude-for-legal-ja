---
name: portfolio
description: >
  Track the IP portfolio — registrations, renewals, maintenance fees, and use
  declarations. Use when checking what's renewing, adding or updating an
  asset, recording a maintenance filing, or auditing the register for gaps,
  lapses, and use-in-commerce questions. Receives handoffs from prosecution
  and clearance work.
  IP ポートフォリオを追跡 — 登録、更新、維持年金、使用宣誓書。何が更新されるかの
  確認、資産の追加・更新、維持年金提出の記録、台帳のギャップ・失効・use-in-commerce
  問題の監査時に使用。出願・クリアランス業務からのハンドオフを受領。
  日本語トリガー: 「ポートフォリオ」「更新期限」「維持年金」「IP 台帳」「IP ポートフォリオ監査」。
argument-hint: "[--report [--days N] | --add | --update | --audit]"
---

# /portfolio

何が更新されるかを表面化、資産を追加、提出を記録、台帳を監査します。

## 指示

1. **下記のワークフローに従う**、`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/portfolio.yaml`
   を読みます。

2. **デフォルト(引数なし):** `--report` 相当 — 次の 90 日の期限を緊急度別にグループ表示
   (🔴 lapsed/grace、⏰ ウィンドウ内、🟡 今後、🌐 agent-managed、❓ unknown)。

3. **`--report [--days N]`:** Mode 2。`--days` でウィンドウを変更
   (30 / 60 / 90 / 180 が典型)。常に CLAUDE.md → Outputs の work-product ヘッダーを付与。
   常に検証注意点で締めくくる。

4. **`--add`:** Mode 3。新規資産を対話的に進める — タイプ、法域、番号、日付、
   オーナー、ビジネスオーナー。組み込みでない法域ならカスタムルールを取得。

5. **`--update`:** Mode 4。維持年金提出または支払いが行われたことを記録、IP 管理システム
   と同期、または資産のステータスを変更。任意の期限を `filed` に設定する前に
   重大行為ゲートを強制。

6. **`--audit`:** Mode 5。より広いヘルスチェック — 期限ハイジーン、登録ギャップ、
   §8 が近いマークの use-in-commerce 問題、オーナー不整合、有効期限の地平、
   非監視マーク。

7. **台帳が空で IP 管理システムが接続されている場合:** Mode 1 を提案
   — system of record からポートフォリオを取得して台帳を初期化。

8. **ガードレール・リマインダー:** 計算された期限はリファレンス専用。すべての
   出力は提出または支払い前に USPTO TSDR、WIPO、または関連登記所に対する検証を
   指示する行で締めくくる。ドケットに登録されたが間違った期限は誤った
   安心感を作る。IP 管理システムが同期統合されていない限り、これを system of
   record として扱わせないこと。

## 例

```
/ip-legal-ja:portfolio
```

```
/ip-legal-ja:portfolio --report --days 180
```

```
/ip-legal-ja:portfolio --add
```

```
/ip-legal-ja:portfolio --update
```

```
/ip-legal-ja:portfolio --audit
```

---

## 接続するとより良く動く

本スキルは、伝えられた情報から期限を追跡します。以下に接続すると格段に良く動きます:

- **MCP 経由の IP 管理システム(IPMS)** — Anaqua、Clarivate IPfolio、AppColl、Patrix、Alt Legal、FoundationIP。接続された IPMS は、台帳が弁護士の記憶頼みではなく、フル・ドケット、維持年金スケジュール、受信通信を 1 つの場所で提供します。IPMS ベンダーに MCP コネクターがあるか尋ねるか、リポジトリルートの `CONNECTORS.md` を参照してください。
- **カスタマー番号経由の USPTO 直接接続** — 1 出願ずつではなく、ポートフォリオ全体のステータス、期限、通信を取得。現在 MCP として利用不能、`CONNECTORS.md` のウィッシュリストにあり。

両方なしの場合、ドケットを貼り付けるかスプレッドシートをアップロードしてください。そこから追跡します。

## 目的

期限内に更新されない商標登録はキャンセルされうる。維持年金が支払われていない特許は失効する。期限切れドメインは 1 時間以内に snipe されうる。これらすべては回避可能で、すべては 1 つのことに依存します: 正しい期限が誰かのカレンダーに、正しい登録番号と紐付けて、正しい法域で乗っていること。

本スキルがそのカレンダーを維持します。

## 重要: 期限リファレンス注意点

> 本スキルが適用する期限ルールは、スキルのビルド日時点で公開されている要件を反映します。
> IP 庁の要件、grace 期間、料金体系、維持年金スケジュールは変わります。**計算された
> 期限は提出または支払いの前に常に USPTO TSDR / Patent Center、WIPO Madrid Monitor /
> Patentscope、EUIPO eSearch、UKIPO オンライン・レコード、または関連国内登記所と
> 照合してください.** Anaqua、CPA Global、Clarivate、Alt Legal、または他の IP 管理
> システムを使うなら、彼らのドケットがあなたの資産の権威です — そのデータを置き換える
> のではなく整理・表面化するためにこのトラッカーを使ってください。
>
> ドケットに登録されたが間違った期限は、未登録のものより悪い: 誤った安心感を作ります。
> 「No deadline soon」出力は特に、依拠する前にセカンドルックに値します。

## 法域および資産タイプの前提

維持メカニズムは法域および資産タイプにより異なります:

- **米国商標:** 登録 5 周年から 6 周年の間に §8 使用宣誓書(または Madrid 指定の §71)、その後 10 年で結合 §8/§9 更新、以降 10 年ごと。継続使用 5 年後に §15 不可争性が利用可能。§8 と §9 はサーチャージ付きで 6 ヶ月の grace; underlying 使用自体に grace なし。
- **Madrid 国際商標:** WIPO で更新可能な 10 年登録期間。個別指定国はローカル使用または宣誓書要件(例: 米国 §71)を持つ場合あり。
- **EUIPO 商標:** 10 年更新; サーチャージ付き 6 ヶ月 grace。
- **米国実用特許:** 維持年金は登録から 3.5、7.5、11.5 年で期限。サーチャージ付き 6 ヶ月 grace ウィンドウ; 以降は失効が unintentional の場合の petition による復活の可能性。
- **米国意匠特許:** 維持年金なし — 2015 年 5 月 13 日以降に出願された場合は登録から 15 年期間(早ければ 14 年)。期間中のアクション不要。
- **EPO / 国内特許:** 年金は通常出願または国内移行から年次で期限。国内ルールは異なる — 法域ごとに確認。
- **米国著作権:** 1978 年以降の著作物について維持なし。
  1978 年以前の著作物は更新義務があった可能性。資産が 1964 年以前なら弁護士レビューのためフラグ(現代ポートフォリオでは稀)。
- **ドメイン:** 登録業者ごとに年次または複数年更新; 通常 30 日 grace、その後 redemption 期間(高料金で約 30 日)、その後 drop。

ポートフォリオに上記にない法域の資産が含まれる場合、台帳の `custom_rules` ブロックで維持メカニズムを取得し、レポートが `agent_managed` として表面化します — 本スキルが理解しない日付を計算するのではなく、外国アソシエイトとステータスを確認してください。

---

## 台帳

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/portfolio.yaml` に存在します。
構造:

```yaml
# IP Portfolio Register
# Generated: [date]
# Last updated: [date]
# Disclaimer: 計算された期限はリファレンス専用 — 行動前に USPTO/WIPO/関連登記所
# または system of record の IP 管理システムと確認すること。

metadata:
  company: "[Company Name]"
  generated: "[date]"
  last_updated: "[date]"
  last_audit: "[date or null]"
  source_system: "[Anaqua / CPA Global / manual / none]"

custom_rules:   # 組み込みでない法域、手動取得
  []

assets:
  - id: "TM-US-001"
    type: "trademark"                          # trademark / patent / copyright / design / domain
    jurisdiction: "US"
    mark_or_title: "[Mark or title]"
    owner: "[Record owner — 登録された法人名]"
    status: "registered"                       # pending / registered / lapsed / abandoned / cancelled
    application_number: "[number or null]"
    registration_number: "[number or null]"
    classes: ["9", "42"]                       # TM の Nice クラス; 特許の CPC/IPC; それ以外は null
    filing_date: "[YYYY-MM-DD or null]"
    registration_date: "[YYYY-MM-DD or null]"
    priority_date: "[YYYY-MM-DD or null]"
    grant_date: "[YYYY-MM-DD or null]"         # 特許
    next_deadlines:                            # 計算済; --report と --audit で更新
      - type: "§8 Declaration of Use"
        due_date: "[YYYY-MM-DD]"
        grace_end: "[YYYY-MM-DD or null]"
        basis: "登録 5 周年〜6 周年"
        action: "§8 使用宣誓書(または excusable nonuse)を提出"
        status: "upcoming"                     # upcoming / due_soon / overdue / grace / filed
    use_in_commerce: true                      # TM のみ — §8 分析を駆動
    agent_managed: false                       # 外国アソシエイト / 外部弁護士管理なら true
    local_agent: null
    docket_id: "[IP-mgmt-system ID or null]"
    outside_counsel: "[firm or null]"
    business_owner: "[email or team]"
    notes: ""

  - id: "PAT-US-001"
    type: "patent"
    jurisdiction: "US"
    mark_or_title: "[Invention title]"
    owner: "[Owner]"
    status: "granted"
    application_number: "[number]"
    registration_number: "[patent number]"
    filing_date: "[YYYY-MM-DD]"
    grant_date: "[YYYY-MM-DD]"
    priority_date: "[YYYY-MM-DD or null]"
    expiration_date: "[YYYY-MM-DD]"            # 最早の非仮出願から 20 年
    next_deadlines:
      - type: "3.5-year maintenance fee"
        due_date: "[YYYY-MM-DD]"
        grace_end: "[YYYY-MM-DD]"
        basis: "登録から 3.5 年"
        action: "維持年金を支払う(該当時 small/micro entity)"
        status: "upcoming"
    claims_count: 20
    entity_size: "large"                       # large / small / micro(USPTO 料金を駆動)
    docket_id: null
    outside_counsel: null
    business_owner: null
    notes: ""
```

`next_deadlines` のステータス値:
- `upcoming` — 90 日以上先
- `due_soon` — 90 日以内に期限、未提出
- `overdue` — 主要期日経過、grace ウィンドウ内(あれば)
- `grace` — grace 期間中(明示的フラグ — サーチャージ付き)
- `lapsed` — アクションなしで grace 経過; 復活可能でない限り資産は実質的に失われた
- `filed` — このサイクルでアクション完了

---

## Mode 1: 初期化

台帳が存在しないとき、または `--rebuild` で実行。

### Step 1: ソースを決定

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読む:
- **IP 管理システム接続**(Anaqua、CPA Global など): その統合経由でポートフォリオを取得。IP システムが権威ソース; この台帳はそれをミラーし、システムにない期限を追加しません。
- **IP 管理システムなしだが、スプレッドシート / エクスポート利用可能:** ユーザーにエクスポートの共有を依頼。あるものをインポート; 登録または登録日が欠落する資産は期限計算のため `unknown` とフラグ。
- **手元に何もない:** 資産を対話的に通す — タイプ、法域、番号、主要日付、オーナー。

### Step 2: 各資産について、期限を計算

このファイル冒頭のルールを適用。最も近い 2〜3 のアイテムを `next_deadlines` に投入 — 数十年先の 10 年更新といった遠い期限は、推測的に保存するのではなくレポート時にオンデマンドで計算。

**自信を持ってスケジュールできない資産:**
- 不明な法域ルール → `custom_rules` の下にスタブを追加し、資産を `agent_managed: true` でフラグ、外国アソシエイトとの確認 TODO 付き。
- 計算に必要な日付が欠落(特許に grant date なし、TM に登録日なし)→ `next_deadlines` を空にし、`notes` に注記、初期化サマリーで資産を `unknown` としてリスト。

### Step 3: 台帳を書き込む

config パスに `portfolio.yaml` を生成。サマリーを表示:

```
Portfolio register initialised.

Assets: [N]
  Trademarks: [N]   ([N registered] / [N pending])
  Patents:    [N]   ([N granted] / [N pending])
  Copyrights: [N]
  Designs:    [N]
  Domains:    [N]

Deadlines computed: [N]
Agent-managed / jurisdiction TBC: [N] — 外国アソシエイトと確認
Unknown (missing key dates): [N] — レポートに依拠する前に埋める

/ip-legal-ja:portfolio --report を実行して期限を確認。
```

---

## Mode 2: レポート

```
/ip-legal-ja:portfolio --report [--days 30|60|90|180]
```

デフォルトウィンドウ: 90 日。レポート生成前にすべての資産の計算された期限を更新 — 保存日付だけに頼らない。

出力(`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` → Outputs の work-product ヘッダーを付与):

```
IP PORTFOLIO DEADLINE REPORT — [date]
[Company Name] — window: next [N] days

🔴 LAPSED / IN GRACE ([N])
  [Asset ID] / [Jurisdiction] / [Type] / [Mark or title]
    [Action] — original due [date], grace ends [date]
    Status: [grace / lapsed]

⏰ DUE WITHIN [N] DAYS ([N])
  [Asset ID] / [Jurisdiction] / [Type] / [Mark or title]
    [Action] — due [date]
    Basis: [例: 「登録 5 周年〜6 周年」]
    [Agent: firm / docket: id — 存在すれば]

🟡 UPCOMING (30 日を超え [N] 日以内のウィンドウ)
  [list]

🌐 AGENT-MANAGED ([N])
  [Asset ID] / [Jurisdiction] — [local agent] が管理; 直接確認
  [Asset ID] / [Jurisdiction] — local agent 記録なし; --update で追加

❓ UNKNOWN ([N])
  [Asset ID] — [field] 欠落; 期限計算不可
  本レポートに依拠する前に [IP 管理システム / USPTO TSDR / 関連登記所] と確認。

SUMMARY
  Total assets tracked: [N]
  Deadlines in window: [N]
  Last audit: [date]
```

レポートを注意点行で締めくくる: *「Computed from portfolio register. Verify each deadline against the USPTO/WIPO/registry of record before filing or paying.」*

レポートが約 10 を超える資産をリストするとき、またはユーザーが尋ねたら: ダッシュボードを提案(CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs` 参照)。この出力に合わせて提案を形成 — 登録ステータス別の件数(live / in grace / lapsed / pending)、期限タイムライン、法域・タイプ・次アクション日付付きのソート可能なポートフォリオ・テーブル。

---

## Mode 3: 追加

```
/ip-legal-ja:portfolio --add
```

単一資産の対話的追加。以下を尋ねる:
1. タイプ(trademark / patent / copyright / design / domain)
2. 法域
3. マークまたはタイトル / 発明名
4. オーナー(record owner — §8 提出と譲渡で問題)
5. 主要日付(タイプ別: 出願、登録、登録、優先、有効期限)
6. 番号
7. クラス / クレーム数
8. ソース — IP 管理システムでドケット ID 下で追跡されているか?
9. 外部弁護士 / 外国アソシエイト、いれば
10. ビジネスオーナー(これが誰にとって重要か — 製品ライン、ブランドマネージャー)

取得後:
- このファイル冒頭のルールにより next deadlines を計算。
- 法域ルールが組み込みでなければ、`custom_rules` 取得フロー(下記)を通す。
- `portfolio.yaml` の `assets:` に追加。

### カスタムルール取得

法域が組み込みリストにないとき:

> [Jurisdiction] / [Asset type] の維持ルールは組み込まれていません。
> 今後追跡できるように取得しましょう。
>
> 1. どの維持イベントが適用されますか?(N 年ごとの更新? 年次年金?
>    使用宣誓書? 他の何か?)
> 2. 期日のトリガーは何ですか — 出願日、登録日、grant 日、
>    国内移行、他の何かの記念日?
> 3. grace 期間はありますか? どの料金で?
> 4. これを管理する外国アソシエイトまたは local agent はいますか?

`custom_rules:` の下に保存し、その法域の将来の資産に適用。

---

## Mode 4: 更新

```
/ip-legal-ja:portfolio --update
```

### 重大行為ゲート

**維持年金提出または支払いが行われたと記録する前に:** `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` の `## Who's using this` を読む。Role が **Non-lawyer** なら:

> §8 宣誓書、§9 更新、特許維持年金支払い、または国際年金を「filed」として記録するのは
> 帰結があります。記録が間違っていれば — 期日逸失、誤った entity サイズ、誤った
> 使用見本 — 期限は動かず、資産は依然失効しうる。実際に提出した弁護士または外国
> アソシエイト(または USPTO TSDR / WIPO Madrid Monitor / 関連登記所)とこれを
> 確認しましたか? Yes なら進む。No なら:
>
> - まだ filed と記録しない。
> - 弁護士に持参すべきもの: asset ID、法域、期限タイプ、IP 管理システムが示すもの、
>   何がいつ提出されたと信じるか、その信念のソース。
>
> 認可された弁護士、ソリシター、バリスター、あなたの法域の他の法専門家を見つけるなら:
> あなたの専門職規制当局のリファラル・サービスが最速の出発点(米国の州弁護士会、
> 英国イングランド&ウェールズの SRA/Bar Standards Board、スコットランド/北アイルランド
> /アイルランド/カナダ/オーストラリアの法曹会、またはあなたの法域の同等機関)。

このゲートを通過する明示的な「はい」なしに、期限のステータスを `filed` に設定しないこと。ステータス更新、レポート生成、近づく期限の表面化はゲートを要求しません。

### サブモード

**手動更新:** 「TM-US-001 の §8 を 3 月 4 日に提出、見本添付」。マッチする期限を更新: `status: filed`、`filed_date`、ライフサイクル内の次の期限を計算(§8 なら §9 更新が 10 年先)。

**IP 管理システム同期から:** Anaqua / CPA Global / 類似が接続されているなら、最新ドケットを取得して照合。台帳と system of record の不一致をフラグ — system of record が勝つ; 台帳をそれに合わせて更新し、台帳にあってシステムにないものを表面化。

**ステータス変更:** 「マーク TM-US-004 を放棄としてマーク」。`status` を更新、`next_deadlines` をクリア、放棄日を注記。

---

## Mode 5: 監査

```
/ip-legal-ja:portfolio --audit
```

今月の期限を超えた、より広いヘルスチェック:

**期限ハイジーン**
- 現在 `grace` ステータスの期限はあるか?(進行中だがサーチャージあり。)
- `abandoned` または `cancelled` とマークされていない `lapsed` 資産はあるか? どちらかにし、ステータスを更新するか復活させる。
- `next_deadlines` 計算なしの資産はあるか? データ欠落またはスキルが知らない法域。

**登録ギャップ**
- 18 ヶ月以上前に出願された商標出願で依然 `pending` のものは? ステータス・チェックのためフラグ — 庁のアクションへの応答が必要かもしれない。
- 4 年以上前に出願された特許で依然 `pending` のものは? 出願チェックのためフラグ。

**Use-in-commerce(TM のみ)**
- `use_in_commerce: false` または不確実とフラグされたマークで §8 が近づくものは?
  §8 は使用を要求する; マークは提出前に使用監査が必要、または excusable nonuse 宣誓書が必要。

**オーナーシップ・ハイジーン**
- `owner` が現在アクティブな法人(法人台帳が利用可能なら)でない資産は? フラグ — 譲渡の記録が必要かもしれない。
- 資産間でオーナー名の不整合(同じ法人、異なる名称文字列)? クリーンアップのため表面化。

**有効期限の地平**
- 今後 24 ヶ月以内に期限切れの特許は? 維持年金期限なしでも、ビジネスは知りたいかもしれない — 製品計画、継続戦略、ライセンスウィンドウ。

**非監視資産**
- CLAUDE.md → Brand protection の監視リストにない登録マークは? 弁護士が追加するかを決めるためギャップとしてフラグ。

出力形式:

```
IP PORTFOLIO AUDIT — [date]

DEADLINE HYGIENE
  In grace: [N] — 今行動すれば失効回避
  Lapsed (abandoned とマークされていない): [N] — ステータス確認
  Missing next-deadline computation: [N] — データを埋めるか agent-managed にマーク

REGISTRATION GAPS
  TM applications pending >18 months: [list]
  Patent applications pending >4 years: [list]

USE IN COMMERCE (TM)
  §8 approaching on uncertain-use marks: [list]

OWNERSHIP
  Assets with unrecognised owner strings: [N]
  Owner name inconsistencies: [list]

EXPIRATION HORIZON (24 months)
  Patents expiring: [list]

BRAND WATCH
  Registered marks not on watch list: [list]

RECOMMENDED ACTIONS
  1. [最優先]
  2. [etc.]
```

---

## インテグレーション: ip-renewal-watcher エージェント

本プラグインの `ip-renewal-watcher` エージェントは、スケジュール(デフォルト週次)で本スキルを実行し、Mode 2 レポートを CLAUDE.md → Renewal alerts に名指されたチャンネルに投稿します。🔴 アイテム(grace / lapsed)が現れたら、エージェントはスケジュールに関わらず即時投稿します。

## ハンドオフ

- 受領: 出願スキル(出願が出されたか、マークがクリアしたとき)、クリアランス・スキル(マークが採用されて出願がキューに入ったとき)、譲渡記録から新規資産レコード。
- 送信: 「§8 を今提出」トリガーを弁護士へ — 本スキルは何も提出しない; 期限と何を持参すべきかを弁護士に伝える。

## このスキルが行わないこと

- 何かを提出する。表面化したアクションは弁護士または外国アソシエイトが実行。
- USPTO TSDR、WIPO、他の登記所と期限を検証する。本スキルは与えられた日付から期限を計算する。台帳は作業用コピー; 登記所が真実のソース。
- 更新するかを決める。更新はビジネス判断 — マークはまだ使用中か、特許はまだ価値があるか、ドメインはまだ重要か。本スキルは期限とコストを表面化; ビジネスと弁護士が決める。
- 数百資産のポートフォリオで IP 管理システムを置き換える。Anaqua、CPA Global、Clarivate、Alt Legal、類似システムは直接登記所フィード、期限自動化、年金支払いサービスを持つ。本スキルは小さめのポートフォリオまたは system of record が示すものを表面化する軽量レイヤーに最適。
- 庁の記録を読んでステータスを検証する。ここで「filed」と表示される §8 は、誰かがそう伝えたという意味 — USPTO がそれを受理したわけではない。TSDR または IP 管理システム経由で受理を確認すること。
