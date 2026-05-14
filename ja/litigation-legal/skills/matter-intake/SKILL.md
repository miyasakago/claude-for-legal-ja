---
name: matter-intake
description: 新規案件を intake — 識別、コンフリクト、出典、リスクトリアージ、重要性、外部弁護士、オーナー、リーガルホールド、キー日付をカバーする統一的な質問;matter.md と history.md を書き出し、構造化された行を _log.yaml に追記。Use when the user says "new matter", "intake this matter", or wants to bring a new matter into the portfolio. 日本語トリガー: 新規案件、案件取り込み、案件登録、案件 intake
argument-hint: "[optional matter name]"
---

# /matter-intake

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → リスクキャリブレーション(トリアージ用)、ランドスケープ(コンテキスト、コンフリクト方式)、ステークホルダー(ループインする相手)。
2. 以下のワークフローと参照に従う。
3. 統一 intake を実行: 識別、コンフリクトチェック、出典、リスクトリアージ、重要性、外部弁護士、社内オーナー、リーガルホールド、キー日付、初期ポスチャ。
4. 案件名から slug を生成(小文字、ハイフン、年)。
5. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` を作成 — 物語形式の完全な intake。
6. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` を作成 — intake を最初のエントリとして初期化。
7. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` に構造化された行を追記。
8. ユーザーと確認: 「Here's the row I'll write — any edits?」

---

# Matter Intake

## 目的

すべての新規案件は同じ intake を通過してポートフォリオを比較可能に保ちます。`_log.yaml` の統一された行はステータススキルにロールアップさせます。`matter.md` の物語は行がキャプチャできないものをキャプチャ。ここで初期化された履歴ファイルはイベント記録になります。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` — リスクキャリブレーション(トリアージ閾値、重要性、和解ラダー)、ランドスケープ(ステークホルダー、外部弁護士ベンチ)。
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — slug の一意性を確認。

## intake

### 1. 識別

- 案件名(一般的な参照、例: 「Acme v. Us 2026」)
- 相手方
- 案件種別: `contract | employment | ip | regulatory | investigation | product | other`
- 役割: `plaintiff | defendant | claimant | respondent | investigated`
  - プラクティスプロファイルの `## サイド` が `plaintiff`、`defense`、または「both — default X」のバリアントなら、そのデフォルトから役割をプレフィル、確認。`## サイド` が `varies by matter` なら、コールド質問。プラクティスプロファイルが設定していないポスチャを黙って仮定しないこと。
  - 役割が下流のスキルを駆動: plaintiff ポスチャ案件は ケースバリュー / 成功報酬経済へリスクトリアージをルート;defense ポスチャ案件は エクスポージャー / 引当 / 保険テンダーへルート。
- 法域(裁判所、仲裁フォーラム、または規制当局)

### 2. コンフリクトチェック

進む前に、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → コンフリクトクリアランスに従ってコンフリクトステップを実行。

- **Status:** `cleared | pending | not-run | waived`
- **Method:** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` が宣言するものとマッチ(`corporate-legal | outside-counsel | system-check | informal | other`)。宣言された方式が `informal` なら、そう述べる — 記録は依然弁護士判断チェックが基準だったことをキャプチャ。
- **Cleared by:** 名前 / チーム / 事務所
- **Cleared date:** YYYY-MM-DD
- **Checked against:** 実行された特定の名前/エンティティの短いリスト(相手方、既知の関連会社、既知なら相手方代理人、キー証人)。薄くて構わない;「no」はダメ。
- **Notes:** フラグされたがクリアされたもの(例: 「Smith on our board sat on counterparty's board 2019–2021 — cleared as non-overlapping to this matter」)。

ステータス別の挙動:

- `cleared` → 進む。
- `pending` → intake を進める;`matter.md` とログ行で目立つフラグ;解決まで `/matter-update` と `/portfolio-status` で再浮上。
- `waived` → まれ;コンフリクト放棄の根拠を要する(放棄書面の作成はこのスキル外 — 存在することをキャプチャ、署名者、所在を)。
- `not-run` → **STOP。これはゲート。** スキルはコンフリクトポスチャが解決されるまで `matter.md`、`history.md`、または `_log.yaml` エントリを作成しません。3 つの受容可能なパス:

  **Path 1 — 今コンフリクトを実行。** この intake を一時停止。`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` コンフリクトクリアランスに従ってクリア。`status: cleared` または根拠付きの `status: waived` で戻る。

  **Path 2 — オーナー + 期限付きで pending マーク。** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` コンフリクトクリアランスが並行 intake 許容と宣言するときのみ許可。誰がコンフリクトを実行、いつ戻ると予想、何のエンティティをチェックするかをキャプチャ。intake は進む;案件行は `conflicts.status: pending` を運ぶ;`/portfolio-status` は毎回フラグ;`/matter-update` は解決まで再プロンプト。

  **Path 3 — 文書化された根拠付きでバイパス。** ユーザーが明示的にバイパスを認めた場合のみ。`conflicts.override` に記録:

  ```yaml
  conflicts:
    status: not-run               # そのまま保持
    override:
      by: [user name]
      date: [YYYY-MM-DD]
      rationale: [なぜコンフリクトがバイパスされたか — 永続記録;自動失効しない]
  ```

  このフィールドは、コンフリクトが実際にクリアされた後にユーザーが `_log.yaml` を明示的に編集して削除するまで、すべての `/portfolio-status`、すべての `/matter` ブリーフィング、すべての `/matter-update` で表示されます。スキルが削除することはありません。

  **黙って進まないこと。** 「I'll do it later」は受容可能な応答ではありません。Path 1/2/3 のいずれかを選ぶ必要があり、選択は記録にキャプチャされます。

このステップはスキルがコンフリクトの有無を決定することについてではない — それはユーザー/事務所の判断。チェックが起きたこと、記録がそれを反映することを確実にすることについて。

### 3. 出典

どう到着したか?
- `demand-letter | complaint-served | subpoena | regulator-inquiry | internal-report | pre-suit-threat`
- *シードドキュメント機会:* 「開始文書(訴状、要求書、サブポエナ)があれば、添付またはパス共有を。intake がシャープになります。」

### 4. リスクトリアージ — ハウスキャリブレーションに対して

- 重大度: high | medium | low(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 重大度バンド参照)
- 確度: high | medium | low(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 確度バンド参照)
- 結果としてのリスク評価(マトリクスに従って): high | medium | low | critical
- 損害賠償エクスポージャー範囲(ベスト推定)
- 非金銭エクスポージャー(差止? 同意命令? 公開? 先例?)

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` のリスクキャリブレーションが薄ければ、精度をふりまかない。ユーザーの直感を使い、薄さを注記。

### 5. 重要性

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` のハウス閾値に対して:
- `reserved | disclosed | monitored | none`
- `reserved` なら: 引当額とファイナンスへの通知の有無
- `disclosed` なら: 提出と脚注の場所

### 6. 外部弁護士

- 事務所
- リードパートナー
- **リードパートナーメール**(`/oc-status` がステータス依頼を起案するのに使用)
- エンゲージメントレターステータス: `signed | pending | none`
- 予算授権: 金額と承認者
- *シードドキュメント機会:* 「Engagement letter path, if signed.」

リスクが medium 以上で外部弁護士未割当なら — フラグ。

### 7. 社内オーナー

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` ランドスケープから — どの社内ステークホルダーが関与?
- ビジネスリード
- HR パートナー(雇用の場合)
- コミュニケーション連絡先(レピュテーションリスクの場合)
- CISO(データまたはサイバーの場合)
- その他

### 8. リーガルホールド

- 発行済? Yes なら: 日付、スコープ、custodian(名前のリスト)。
- 次更新日(デフォルト: 発行から 6 ヶ月;案件ごとに調整)。
- No でこれがアクティブ訴訟または合理的に予見されるなら: 緊急フラグ;intake 完了後に `/litigation-legal-ja:legal-hold [slug] --issue` を実行する提案。
- *シードドキュメント機会:* 「Hold notice, if issued.」

### 9. キー日付

- 応答期限(answer、objection、opposition)
- 次審理 / 会議
- 出訴期限(該当する場合)
- 規制当局期限

### 10. 初期ポスチャ

1 段落のセオリー:
- 我々の物語は?
- 彼らの物語は?
- pivot fact は?
- 初期ポスチャ: `fight | settle | investigate | wait`

## 出力の書き込み

### Slug

小文字、ハイフン、末尾に年。例: `acme-v-us-2026`、`employment-smith-2026`、`ftc-inquiry-2026`。

書き込む前に `_log.yaml` で slug が一意か確認。

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md`

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

# [Matter Name]

**Slug:** [slug]
**Opened:** [YYYY-MM-DD]
**Our role:** [plaintiff/defendant/etc.]
**Status:** [status]

---

## 識別

[相手方、法域、案件種別、出典]

## コンフリクト

**Status:** [cleared / pending / not-run / waived]
**Method:** [corporate-legal / outside-counsel / system-check / informal / other]
**Cleared by:** [名前]
**Cleared date:** [YYYY-MM-DD]
**Checked against:** [実行されたエンティティ]
**Notes:** [クリアされたフラグ、該当する場合は放棄参照]

## リスクトリアージ

**Severity:** [バンド] — [理由、ハウス重大度定義への参照付き]
**Likelihood:** [バンド] — [理由]
**Risk rating:** [high/medium/low/critical]
**Exposure:** [ドル範囲 + 非金銭]

## 重要性

[reserved/disclosed/monitored/none — 引当額、開示場所、または「none」なら推論]

## 外部弁護士

[事務所、リード、エンゲージメントステータス、予算]

## 社内オーナー

[ステークホルダーと各人が関与する理由]

## リーガルホールド

[ステータス、日付、スコープ]

## キー日付

[リスト]

## 初期セオリー

[1 段落: 我々の物語、彼らの物語、pivot fact、初期ポスチャ] `[SME VERIFY — theory at intake is a working hypothesis; confirm with outside counsel before any filing or material communication that assumes this framing]`

## 未解決の質問

[まだ判明していないが重要なもの — 例: 「insurance tender pending」、「unclear whether we have coverage for X」]

---

## シードドキュメント

| Doc | Path / pointer |
|---|---|
| [例: 訴状] | [パスまたは「未共有」] |
```

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md`

intake をエントリ 0 として履歴ファイルを初期化:

```markdown
# History: [Matter Name]

追記専用イベントログ。最新を上に。

---

## [YYYY-MM-DD] — Matter opened

[出典、誰が持ち込んだか、初期トリアージサマリー、割り当てられた外部弁護士、発行されたリーガルホールド yes/no。]
```

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` に追記

スキーマに従って行を追加。例:

```yaml
- id: acme-v-us-2026
  name: "Acme Corp v. Company"
  type: contract
  role: defendant
  counterparty: "Acme Corp"
  jurisdiction: "N.D. Cal."
  # status は source から派生:
  #   source: pre-suit-threat | demand-letter           → status: threatened
  #   source: complaint-served | subpoena | regulator-inquiry → status: active
  #   source: internal-report                           → status: threatened (デフォルト) または正式プロセス開始済なら active
  status: active
  stage: pleadings
  source: complaint-served
  outside_counsel:
    firm: "Wilson Sonsini"
    lead: "J. Reyes"
    email: "jreyes@wsgr.example.com"
    engagement: signed
  conflicts:
    status: cleared
    method: corporate-legal
    cleared_by: "K. Patel"
    cleared_date: 2026-04-20
    override:                   # Path 3 バイパス時のみ入力
      by: null
      date: null
      rationale: null
  risk: high
  materiality: reserved
  exposure_range: "$2M–$5M"
  internal_owners:
    business_lead: "Jane Smith"
    hr_partner: null
    comms_contact: null
  legal_hold:
    issued: true
    issued_date: 2026-02-15
    scope: "Sales org 2023–2026"
    custodians: ["Jane Smith", "R. Chen", "T. Patel"]
    last_refresh: 2026-02-15
    next_refresh: 2026-08-15
    released: null
  related_matters: []
  opened: 2026-04-20
  next_deadline: 2026-05-15
  last_updated: 2026-04-20
  path: matters/acme-v-us-2026/
```

## 書き込み前に確認

ユーザーに行と matter.md コンテンツを表示:

> Here's what I'll write. Flag anything wrong or thin before I commit.

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` に従って、次ステップの決定ツリーで締めくくります。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではありません。ツリーが出力です;弁護士が選びます。

## このスキルがしないこと

- **コンフリクトチェック自体を実行。** 結果、ステータス、方式、チェックされたエンティティを記録。実際のクリアランスはハウスプラクティスプロファイルが宣言するシステム(または判断)で起きます。ユーザーが「cleared」と言えば、スキルは額面通りに受け取り、メタデータをキャプチャ。
- 初期セオリーを決定。ユーザーが言ったことをキャプチャ;捏造しません。
- リーガルホールドを発行。欠落していればフラグ。ユーザーが発行。
