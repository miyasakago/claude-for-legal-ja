---
name: subpoena-triage
description: 会社に送達されたサブポエナ(召喚状)をトリアージ — 分類、スコープ/負担/特権の分析、ポートフォリオクロスチェック、異議フレームワーク・コンプライアンス計画・期限カレンダーの生成。Use when the user says "we got a subpoena", "served with a subpoena", or shares a subpoena, CID, or third-party document request to evaluate. 日本語トリガー: サブポエナ受領、召喚状トリアージ、CID 評価
argument-hint: "[path-to-subpoena] [--slug=custom-slug]"
---

# /subpoena-triage

1. 提供されたパスからサブポエナを読む。
2. 分類(third-party-docs / third-party-depo / party / CID / grand-jury)。
3. 大陪審の場合 → 停止、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` に従ってエスカレーション。それ以外は継続。
4. クロスチェックのために `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` をロード。`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → ランドスケープ、特権慣行、エスカレーション規範。
5. 以下のワークフローと参照に従う。
6. キーフィールドを抽出、スコープ/負担/特権を分析、異議フレームワーク + コンプライアンス計画 + 期限カレンダーを生成。
7. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/inbound/[slug]/triage.md` を書き出し。サブポエナを `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/inbound/[slug]/incoming.[ext]` にコピーまたはリンク。
8. ハンドオフ: ホールドがなければ `/legal-hold --issue`;重要性がある場合 `/matter-intake`;既存案件のパーティサブポエナなら `/matter-briefing [slug]`。

---

# Subpoena Triage

## 目的

サブポエナは期限と共に届きます。失敗モード: 期限の見落とし、過剰生産(特権放棄、異議すべきだった負担)、過少生産(法廷侮辱エクスポージャー)、または motion-to-quash ウィンドウの見落とし。このスキルは分類、分析、コンプライアンス計画 + 異議フレームワークを生成。

## 法域の前提

Step 0 で引用される規則は、このフォーラムのこのサブポエナの operative なものです。サブポエナプラクティスは大きく異なります: 連邦(FRCP 45 / 連邦民事訴訟規則 45)vs 州相当、州ごとのバリエーション、ローカルルール、裁判所固有のスタンディングオーダー、サブポエナタイプ(トライアル、デポジション、文書生成)、これらすべてが異議期限、コンプライアンス場所制限、特権ログ要件、コストシフトを変えます。ここでの各規則出力は出発点のヒューリスティック — 書面で主張する前に通貨とローカルバリアントを確認。

## サイドコンテキスト

このスキルは本質的に防御的 — サブポエナが受信者に送達され、ポスチャは応答/異議/コンプライアンス。プラクティスプロファイルの `## サイド` を読む。ユーザーのデフォルトサイドが **plaintiff** なら、サブポエナの受領は plaintiff にとっても一般的(証人サブポエナ、原告自身の記録向けの第三者リクエスト)と注記、ただしここでのフレーミングは常に「我々に送達されたサブポエナ、どう応答するか」。ユーザーが **defense**(典型)なら、フレーミングはデフォルトと整合。案件がデフォルトと異なるポスチャ(例: 家族のために pro se で活動中の defense 実務家がサブポエナを受領)なら、進む前にユーザーにポスチャ確認を促す。

## コンテキストのロード

- サブポエナ文書(ユーザーがパス提供またはセッション内ドロップ)
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — 関連案件ルックアップとリーガルホールドステータス用
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → ランドスケープ(対処する規制当局)、ハウス特権慣行、エスカレーション規範

## ワークフロー

### Step 0: 該当規則を調査

**このサブポエナを分析する前に、フォーラムの該当する民事訴訟規則(連邦は FRCP 45、それ以外は州相当)とサブポエナタイプ(トライアル、デポジション、文書生成)を調査。特定: コンプライアンス場所制限、異議期限(これらはしばしばコンプライアンス日または送達後の固定日数の EARLIER から走る)、特権ログ要件、コスト負担者。ピンポイント参照付きで引用。通貨を確認 — 規則とローカルバリアントは変わる。大陪審サブポエナは即時の刑事弁護士エスカレーションのためにフラグ。**

**No silent supplement.** 設定された法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)へのリサーチクエリが、フォーラムの規則、バリアント、またはピンポイントについて少数または結果なしを返す場合、見つかったものを報告して停止。尋ねずに web 検索やモデル知識からギャップを埋めないこと。こう言う: 「The search returned [N] results from [tool]. Coverage appears thin for [rule / forum / variant]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) stop here. Which would you like?」 弁護士が低い自信の出典を受け入れるかを決定;スキルは決定しません。

**Source attribution.** トリアージ出力のすべての規則参照、判例、制定法、規制に出処をタグ付け: 法律調査コネクター経由で取得された引用には `[Westlaw]`、`[CourtListener]`、`[Trellis]`、`[Descrybe]`、または MCP ツール名;web 検索からの引用には `[web search — verify]`;トレーニングデータから思い出された引用には `[model knowledge — verify]`;ユーザーが供給した引用(例: サブポエナから、または先行する案件作業から)には `[user provided]`。`verify` タグの引用は高い捏造リスクを運び、最初にチェックすべき。タグを絶対に剥がしたり折りたたんだりしないこと — それらは、異議や提出物で主張する前にどの引用を検証するかについての弁護士への最速のシグナル。

### Step 1: 分類

サブポエナは異なる規則を持つ flavor で届く;Step 0 で調査した規則に対して詳細を確認:

- **第三者文書サブポエナ(民事)** — 我々は訴訟の当事者ではない;誰かが我々の文書を欲しい。通常の異議カテゴリー: 関連性、負担、特権、コンプライアンス場所 / 地理的範囲。
- **第三者デポジションサブポエナ** — 誰かが従業員に証言を欲しい。スコープ、関連性、負担;motion to quash の可能性;証人準備必要。
- **当事者サブポエナ** — 我々は当事者;これは我々が追跡している訴訟のディスカバリー。インバウンドではなくディスカバリーとして扱う — 既存案件にマップすべき。
- **規制当局民事調査要求(CID)** — FTC、SEC、DOJ、州 AG。異なる規則、異なるポスチャ;しばしば deferential だがしばしばより consequential。
- **大陪審サブポエナ** — 刑事。即時に刑事弁護士にエスカレーション;異なるスキルパス(このスキルのスコープ外 — エスカレーション用にフラグ)。

### Step 2: キーフィールドを抽出

- **発行当局** — 裁判所(どれ)、機関(どれ)、弁護士(民事の場合)
- **発行当事者** — 誰がリクエストしたか(民事の場合)
- **ケース / 案件キャプション** — 我々が質問されている訴訟
- **求められる文書カテゴリー** — 番号付きリスト
- **証言トピック**(depo の場合)— Rule 30(b)(6) 指名
- **応答/異議期限** — 送達日 + 該当規則に従った応答ウィンドウの計算
- **生成日** — 文書を生成しなければならない日
- **地理的スコープ** — 関係する custodian、場所、システム
- **記録 custodian 指名** — 当社の誰が証人/署名者か

### Step 3: ポートフォリオクロスチェック

- **当事者サブポエナ → 既存案件に関連:** `_log.yaml` の案件とキャプションがマッチするか確認。Yes なら、その案件のワークフローにルート;このトリアージは情報的。
- **第三者サブポエナ → 認識しないキャプション:** 当事者をキャプチャ;スタンドアロンインバウンドとしてログ。
- **同じケースからの複数サブポエナ:** 協調発行をフラグ;単一の応答戦略が適用される可能性。

### Step 4: スコープ、負担、特権を分析

**スコープ / 関連性**
- カテゴリーは我々が plausibly に持つ実際の文書にマップするか?
- いずれかのカテゴリーが fishing expedition(過度に広い、基礎となるケースのクレーム/防御にテザーされていない)か?
- コンプライアンス場所 / 地理的範囲 — 調査された規則を適用;限界はサブポエナタイプで異なる(トライアル vs 文書 vs デポジション)。

**負担**
- 関与する custodian、検索されるシステム、期間
- 推定ボリューム(ラフ: 小 / 中 / 大 / 極端)
- コスト — 第三者応答者はコストシフトを利用可能;調査された規則を確認。

**特権**
- 弁護士・依頼者または成果物が関与する可能性?(法的関連のあるものほぼ常に yes;社内または外部弁護士が関与する通信はしばしば yes。)
- その他の特権 — 営業秘密、HIPAA(該当する場合)、州特権、共通利益
- 特権ログが必要 — `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` ごとのフォーマットをフラグ

**その他の異議根拠**
- 機密性 — 保護命令が必要?
- 重複 — 別の当事者から既に持っているか?
- 所有していない — 求められているものを持っていない(具体性で文書化)
- 不適切な送達 — 調査された規則のサービス要件をチェック

### Step 5: 異議フレームワーク

構造化された異議アウトラインを起案 — 最終異議レターではなく、どの異議が適用されなぜかのアウトライン。ユーザー(しばしば外部弁護士と)が最終化。

各異議:
- 法的根拠 — Step 0 で調査した規則からのピンポイントを引用
- このサブポエナへの具体的適用(どのカテゴリー、どの custodian)
- 強さ(強い / 合理的 / 弱い)

### Step 6: コンプライアンス計画

異議の際でも、求められているものの一部をしばしば生成。計画:

- **生成の可能性のあるスコープ** — 異議後、何を生成するか
- **検索する custodian** — 名前とシステム
- **日付範囲**
- **レビュープロトコル** — 誰が特権をレビュー(我々、外部弁護士、契約レビュアー)
- **生成フォーマット** — サブポエナまたは交渉されたプロトコルに従って(TIFF+load file、ネイティブ、PDF)
- **特権ログ要件** — フォーマット、フィールド

### Step 7: 期限

Step 0 リサーチで特定された期限を使用。異議期限はしばしばコンプライアンス日または送達後の固定日数の EARLIER から走ることに注意 — 該当規則とローカルバリアントをチェックせずに単一の数値にデフォルトしないこと。

- **応答期限** — 調査された規則ごと;ユーザーがより多くの時間を必要とすれば注記(延長のための meet-and-confer は標準)
- **異議期限** — 調査された規則ごと(連邦 / 州規則 + 任意のローカルバリアント)
- **生成日** — 異議が成功しない場合
- **Motion to quash ウィンドウ** — そのパスを追求する場合、タイミングがクリティカル

すべてをカレンダー化。即時アクション項目。

### Step 8: トリアージを書く

出力: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/inbound/[slug]/triage.md`。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

# Subpoena Triage

> **外部弁護士の代替ではない。** これは期限、ホールド、エンゲージメントに関する迅速な決定を支えるための構造化された分類とスコーピングの読み取りです。各規則参照は出発点のヒューリスティック;法域固有の分析、異議最終化、申立てプラクティス、特権でのメリット判断は、フォーラムに精通した資格を持つ弁護士を必要とします。ルーチンの第三者文書スコープを超える任意のサブポエナのために外部弁護士を engage。

**Slug:** [slug]
**Served:** [YYYY-MM-DD]
**Served on:** [エンティティ / 登録代理人]
**Incoming file:** [パス]
**Classification:** [third-party-docs / third-party-depo / party / CID / grand-jury]

---

## キーフィールド

- **Issuing authority:** [裁判所/機関]
- **Issuing party:** [名前]
- **Case caption:** [キャプション]
- **Response deadline:** [日付]
- **Production date:** [日付]
- **Motion-to-quash window:** [日付範囲]

## 求められるカテゴリー(サマリー)

[番号付きリスト、簡潔]

## 関与する可能性のある custodian / システム

[リスト]

---

## ポートフォリオクロスチェック

**Related matter:** [slug または "none"]
**If party subpoena:** [既存案件にルートまたは新規案件?]
**If third-party:** [スタンドアロンインバウンド]

---

## スコープ & 負担分析

**Scope:** [カテゴリー別の関連性評価]
**Burden estimate:** [小 / 中 / 大 / 極端 — 推論付き]
**Geographic reach issues:** [あれば]

## 特権分析

*特権スコーピングは最初の読み取り;最終判断は弁護士のものでこのスキルのものではない。*

**Attorney-client / work product likely implicated:** [yes/no + どのカテゴリー] `[SME VERIFY]`
**Other privileges:** [営業秘密、HIPAA、州、共通利益] `[SME VERIFY]`
**Privilege log format required:** [`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` ごと]

---

## 異議フレームワーク

*下の各行は書面で主張する前に `[SME VERIFY]` を要する — 法域、規則通貨、放棄リスク。*

| Objection | Legal basis | Applies to | Strength | SME verified? |
|---|---|---|---|---|
| Relevance | [規則] | [カテゴリー] | [強い/合理的/弱い] | [ ] |
| Burden | [規則] | [カテゴリー] | | [ ] |
| Privilege | A/C, WP | [すべての生成文書] | 強い(常に) | [ ] |
| Duplicative | [規則/法理] | [該当する場合] | | [ ] |
| [その他] | | | | [ ] |

---

## コンプライアンス計画(応答する場合)

- **Scope of likely production:** [異議後]
- **Custodians / systems:** [リスト]
- **Date range:** [範囲]
- **Review protocol:** [誰、どう]
- **Production format:** [フォーマット]
- **Privilege log:** [フォーマット、推定エントリ数]

---

## 期限(これらをカレンダー化)

*下のすべての期限は Step 0 規則リサーチから来ます。`[SME VERIFY]` がこのフォーラムとこのサブポエナタイプの規則、バリアント、計算を確認 — 州バリアントとローカルルールは異なる。*

- **Response deadline:** [日付] `[SME VERIFY]`
- **Objection deadline:** [日付] — 引用: [規則 + ピンポイント] `[SME VERIFY]`
- **Meet-and-confer by:** [日付](通常異議期限前) `[SME VERIFY]`
- **Production date:** [日付]

---

## 即時アクション

- [ ] リーガルホールド発行 — [yes/no] — no なら、サブポエナスコープで `/legal-hold [slug] --issue` を実行
- [ ] 外部弁護士 engage — [yes/誰/TBD]
- [ ] Meet-and-confer 予定 — [日付]
- [ ] ログで案件作成 — [yes/no/TBD — 通常最小の第三者ドキュメントサブポエナを超えるものは yes]
- [ ] 保険 / コストシフト分析 — [負担が大きい場合]
- [ ] 内部エスカレーション — [誰]

---

## 推奨

[2 段落: 何をするか。異議ポスチャ。生成ポスチャ。外部弁護士が異議を処理するか我々がするか。Motion to quash するか。]

---

## 引用検証

このトリアージのすべての規則参照、判例、制定法、規制 — Step 0 リサーチ引用、異議根拠、特権ログフォーマットポインタを含む — は AI 生成で未検証です。任意の引用に依拠する前(特に異議、motion to quash、または発行当事者との通信)、法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)に対して、正確性、good law ステータス、ローカルバリアントの検証パスを実行。提出された文書の捏造または誤引用された引用は制裁になっています。各引用の出典タグ(例: `[Westlaw]`、`[web search — verify]`)は引き出された場所を示す;`verify` タグは高い捏造リスクを運び、最初にチェックすべき。
```

### Step 9: ハンドオフ

**サブポエナへの応答前(異議の提出、文書生成、デポジションへの出席、または motion to quash の提出 — 発行当事者または裁判所への任意の実質的応答):** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Responding to a subpoena has legal consequences — missing a deadline risks contempt, over-producing waives privilege, under-producing risks sanctions. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: サブポエナタイプ、発行当局、期限、求められるスコープ、異議フレームワークと強さ、特権と負担問題、提案された応答ポスチャ、何が悪くなり得るか、弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしにこのゲートを越えて進まないこと。トリアージ、スコーピング、内部カレンダーにゲート不要 — 発行当局への応答には必要。

- **大陪審サブポエナ** に分類されたら → 停止、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` に従ってエスカレーションをフラグ、標準トリアージで進まない。
- **CID** に分類された場合: 規制当局固有の規範が適用されることをフラグ;外部規制弁護士を推奨。
- それ以外: 案件作成を提案(通常 yes — サブポエナは追跡するのに十分実質的なことがほぼ常に)。
- サブポエナスコープでリーガルホールドが発行されていなければ、`/legal-hold --issue` に即時ハンドオフ。

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` に従って、次ステップの決定ツリーで締めくくります。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではありません。ツリーが出力です;弁護士が選びます。

## このスキルがしないこと

- **最終異議レターを起案する。** フレームワークを生成;レターはユーザー + 外部弁護士が起案(将来: 専用の異議起案スキル)。
- **Motion to quash する。** 選択肢を表面化;申立ては法域固有の分析を要する法的作業。
- **法域を跨いで規則を検証する。** Step 0 リサーチがこのサブポエナの operative 規則を生成;スキルは独立に通貨やローカルバリアントを確認しません。行動前に弁護士検証のためにフラグ。
- **大陪審サブポエナを処理する。** エスカレーション。これはトリアージスコープ外。
