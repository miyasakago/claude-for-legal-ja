---
name: demand-received
description: 受領した要求書をトリアージ — フィールド抽出、ポートフォリオクロスチェック、メリット評価、推奨付き応答選択肢の提示、エスカレーションが必要なら matter-intake または demand-intake へのハンドオフ。Use when the user says "we got a demand letter", "triage this demand", or shares an incoming demand to evaluate. 日本語トリガー: 要求書受領、要求書トリアージ、受領要求の評価
argument-hint: "[path-to-incoming] [--slug=custom-slug]"
---

# /demand-received

1. 提供されたパスから受領文書を読む。
2. ポートフォリオクロスチェックのために `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` をロード。
3. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → リスクキャリブレーション、ランドスケープ、要求書プラクティス。
4. 以下のワークフローと参照に従う。
5. フィールド抽出;ポートフォリオクロスチェック;メリット評価;推奨付き選択肢提示。
6. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/inbound/[slug]/triage.md` を書き出し。受領文書を `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/inbound/[slug]/incoming.[ext]` にコピーまたはリンク。
7. ユーザー選択ごとにハンドオフ:
   - 案件作成 → 事前入力された `matter-intake`
   - カウンターデマンドで応答 → 事前入力された `demand-intake`
   - 既存案件にリンク → ログの `related_matters` を更新
   - スタンドアロン → さらなるアクションなし

---

# Demand Received

## 目的

受領した要求書は社内訴訟プラクティスの主食です。小さな割合がエスカレーションを要し、ほとんどは構造化された応答または保留レターで処理できます。失敗モードはすべて同じように扱うこと。このスキルはトリアージし、ポートフォリオをクロスチェックし、選択肢を生成します。

## コンテキストのロード

- 受領文書(ユーザーがパス提供またはセッション内ドロップ)
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — 関連案件(同じ相手方、エンティティ関係経由の重複相手方、または案件タイプ + 最近の日付)をスキャン
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → リスクキャリブレーション(メリット評価用)、ランドスケープ(送信者は頻出敵対者か?)、要求書プラクティス(ハウストーンと応答デフォルト)

## ワークフロー

### Step 1: 要求を読む

受領物から抽出:

- **送信者** — エンティティ、署名者、弁護士(外部事務所が署名した場合)
- **受信者** — 当社のどのエンティティ/人
- **配信** — certified、メール、courier(期限計算で重要)
- **受領日** vs **署名日**
- **要求タイプ** — 支払い、違反/治癒、C&D、保全、和解、その他
- **具体的リクエスト** — 彼らが何を望むか、いつまでに
- **主張された事実** — 起きたことの彼らのバージョン
- **法的根拠** — 引用する制定法、契約条項、理論
- **脅威** — コンプライアンスしない場合に何をすると言うか
- **和解通信フレーミング** — フォーラム(連邦では FRE 408、それ以外は州相当)に適用可能な和解通信保護を調査。要求が和解通信としてマークされているか注記、ただし覚えておく: 保護は行為とコンテキストからアタッチし、ラベリングだけからではない。ラベル(あれば)と実質が実際に妥協議論であるかの最初の読みの両方をキャプチャ。

### Step 2: ポートフォリオクロスチェック

`_log.yaml` で検索:

- **直接マッチ** — 同じ相手方を持つ案件(slug が送信者にマッチ)
- **タイプマッチ** — 過去にこの相手方との類似案件タイプ(クローズ案件もカウント — パターンを情報提供)
- **対象重複** — 対象が同じ紛争である可能性のある案件(例: 同じ契約、同じ製品、同じプロジェクト)

発見を提示:

- **直接マッチ + アクティブ** ならフラグ: ほぼ確実に同じ案件;新規オープンせず既存案件に追加することを推奨。それが脱線なら `related_matters` を更新。
- **直接マッチ + クローズ** ならフラグ — 相手方が戻ってきた。新規紛争(新規案件オープン)または復活したもの(再オープンまたは修正)。ユーザーが決定。
- **タイプマッチ** なら、先例/コンテキストとして注記;おそらく別個の案件だが応答戦略を情報提供。
- **マッチなし** なら、新規。フレッシュとして扱う。

### Step 3: メリット評価

法的意見ではない — 構造化された読み:

- **事実** — 主張された事実は我々が知っていることと整合するか? 切断はどこか?
- **法的根拠** — 引用された条項/制定法は実際に適用可能か?(ユーザー検証のために引用をフラグ — 法を自律的に検証しようとしないこと。)
- **彼らのサイドの強さ** — 明日法廷に行ったら、彼らの物語は?
- **我々のサイドの強さ** — 我々の可能性のある防御は?
- **要求された損害賠償 vs 可能性** — リクエストは、彼らが勝った場合に裁判所が認める可能性のあるものに比例するか?
- **レバレッジと圧力** — 訴訟する準備が credibly あるか? 能力はあるか? `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` ごとに繰り返し訴訟敵対者か?

トリアージ評価を出力: **実質的メリット / 議論可能 / 弱い / 浮わついている**。率直に。ユーザーはトリアージしている、ブリーフを書いていない。

### Step 4: 応答選択肢

トレードオフ付きで 3-4 選択肢を提示:

**Option A — 実質的応答**
- いつ: 彼らの要求にメリットがある、または少なくとも議論可能;理にかなった返信が記録を保護
- トレードオフ: 書面で立場にコミット
- 次ステップ: カウンター応答レターのためのフィールド事前入力で `/demand-intake`

**Option B — 保留レター**
- いつ: 調査の時間が必要;何も譲歩したくないか彼らの期限計算をトリガーしたくない
- トレードオフ: 何も解決しない;2-4 週間を買う
- 次ステップ: 短い承認ドラフト

**Option C — 和解応答**
- いつ: 早期解決が訴訟より安価;譲歩せずに議論する意欲
- トレードオフ: 和解通信ポスチャが必要 — 適用可能な規則(FRE 408 または州相当)を調査し、実質(ラベルだけでなく)が妥協議論として qualify するよう応答を構造化。クレームを放棄しないよう注意必要。
- 次ステップ: `type: settlement-response` で `/demand-intake`

**Option D — 無視 + 保全**
- いつ: 要求が浮わついている、または期限が法的不利益を作らない
- トレードオフ: 沈黙は一部のコンテキスト(例: 確認された口座)で我々に対して使われ得る;リーガルホールドは依然必要
- 次ステップ: まだなら `/legal-hold --issue` 経由でリーガルホールド発行;要求をログして前進

1 つを推奨。理由について具体的に。

### Step 5: 期限トリアージ

- **彼らの述べた期限** — 注記、ただし我々を拘束しない
- **我々の内部期限** — いつ決定する必要があるか(しばしば: 述べた期限 - 起案 + 承認に 5 営業日)
- **法的期限** — 出訴期限、契約治癒期間、手続要件

タイトな法的期限をフラグ。カレンダー化。

**No silent supplement.** 受領した要求が検証を要する規則、判例、または制定法を引用し、設定された法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)へのリサーチクエリが特定の権威について少数または結果なしを返す場合、見つかったものを報告して停止。尋ねずに web 検索やモデル知識からギャップを埋めないこと。こう言う: 「The search returned [N] results from [tool]. Coverage appears thin for [cite / doctrine]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) leave the `[SME VERIFY]` flag and stop here. Which would you like?」 弁護士が低い自信の出典を受け入れるかを決定;スキルは決定しません。

**Source attribution.** トリアージに持ち越されるすべての引用 — 送信者の引用された権威、応答選択肢の根拠、メリット評価のために引き出されたリサーチを含む — に出処をタグ付け: 法律調査コネクター経由で取得された引用には `[Westlaw]`、`[CourtListener]`、`[Trellis]`、`[Descrybe]`、または MCP ツール名;web 検索引用には `[web search — verify]`;トレーニングデータから思い出された引用には `[model knowledge — verify]`;要求自体で供給された引用には `[user provided]`。`verify` タグの引用は高い捏造リスクを運び、最初にチェックすべき。タグを絶対に剥がしたり折りたたんだりしないこと。

### Step 6: トリアージを書く

出力: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/inbound/[slug]/triage.md`。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

> **Privilege inheritance.** This triage is derived from the inbound demand and from the portfolio log, and it records our first-pass merit read and response posture. Those internal analyses are attorney-client and/or work-product material. Distributing this triage beyond the privilege circle — including forwarding it to the business lead without marking, sharing with the counterparty, or attaching to an insurance tender without scrubbing — can waive protection over both this document and the reasoning inside it. Store with privileged matter material, mark consistently with house privilege conventions, and make distribution decisions deliberately.

# Demand Received — Triage

> **READ FOR TRIAGE, NOT OPINION.** This document is an intake scan and an options analysis — not a legal merit opinion. The `Triage rating` below is a structured read to support the counsel's decision on how to route the demand. It is not a recommendation on the merits and does not substitute for case-specific legal analysis. Every cited statute, rule, or case is flagged for SME verification; every merit call is the counsel's, not this skill's.

**Slug:** [slug]
**Received:** [YYYY-MM-DD]
**Received by:** [エンティティ / 人]
**Incoming file:** [パス]

---

## 要求

**Sender:** [エンティティ、署名者、弁護士]
**Demand type:** [タイプ]
**Specific asks:** [リスト]
**Their stated deadline:** [日付]
**Settlement-communication framing:** [ラベル付き / 実質的に / どちらでもない / 曖昧] — *保護は行為とコンテキストからアタッチし、ラベルからではない;フォーラムの該当規則に対して `[SME VERIFY]`*

## 主張された事実

[彼らのバージョン、1 段落]

## 引用された法的根拠

[引用 — 各々がインラインで `[SME VERIFY: applicability / currency / jurisdiction]` フラグ — 独立したチェックなしにここでのいかなる引用にも依拠しない]

## 脅威 / 彼らが述べる次ステップ

[リスト]

---

## ポートフォリオクロスチェック

**Direct match:** [存在すれば slug、または "none"]
**Type match / precedent:** [リストまたは "none"]
**Subject overlap:** [リストまたは "none"]
**Recommendation:** [新規案件 / 既存に追加 / related_matters でリンク / スタンドアロンインバウンド]

---

## メリット評価

**Facts:** [我々のバージョンとの整合;切断]
**Legal basis:** [適用可能性、フラグ付き]
**Their case if litigated:** [1 段落]
**Our defenses:** [1 段落]
**Damages proportionality:** [評価]
**Credibility of threat:** [訴訟するか? 能力? 繰り返し訴訟者?]

**Triage rating:** [substantial / debatable / weak / frivolous] — *ルーティング用構造化読み、メリット意見ではない;`[SME VERIFY: counsel to confirm before relying on this]`*

---

## 応答選択肢

### A. 実質的応答
[根拠、トレードオフ、次ステップ]

### B. 保留レター
[根拠、トレードオフ、次ステップ]

### C. 和解応答
[根拠、トレードオフ、次ステップ]

### D. 無視 + 保全
[根拠、トレードオフ、次ステップ]

**Recommendation:** [A/B/C/D] — [2 文 why] — `[SME VERIFY: counsel to confirm before executing]`

---

## 期限

- **Their stated deadline:** [日付]
- **Our internal decision deadline:** [日付]
- **Legal deadlines:** [SoL、治癒期間、手続 — 日付付き]

---

## 即時アクション

- [ ] リーガルホールド発行 — [yes/no] — no なら `/legal-hold [slug] --issue` を実行
- [ ] ログで案件作成 — [yes/no/TBD]
- [ ] 弁護士割当 — [誰]
- [ ] 保険テンダー — [yes/no/N-A]
- [ ] 内部エスカレーション(GC/CFO/ビジネスリード) — [誰/いつ]
```

### Step 7: ハンドオフ

推奨とユーザー確認に基づき:

- 案件作成 → 事前入力で `/matter-intake` にハンドオフ: 相手方、タイプ、`source: demand-letter`(インバウンド)、防御的にフレーミングされた初期セオリー。
- カウンター応答をアウトバウンド要求として → 事前入力で `/demand-intake` にハンドオフ: 相手方、トリアージからのコンテキスト、応答としての望む結果。
- 既存案件にリンク → `_log.yaml` のその案件の `related_matters` を更新;その `history.md` にイベント追記。
- スタンドアロン → `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/inbound/` に残す;ポートフォリオ変更なし。

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` に従って、次ステップの決定ツリーで締めくくります。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではありません。ツリーが出力です;弁護士が選びます。

## このスキルがしないこと

- **引用された法を検証する。** ユーザーが citator(good law を検証)を通すまたは外部弁護士でチェックするために引用をフラグ。受領した要求での法的分析の捏造は過誤責任エクスポージャー。
- **応答を送付する。** ドラフトは `demand-draft` で起案;このスキルはトリアージ決定で停止。
- **メリットを最終決定する。** 評価はトリアージのための読み;正式なメリット意見は外部弁護士またはより徹底した分析と共に住む。
- **案件作成判断を下す。** 推奨を表面化;ユーザーが決定。
