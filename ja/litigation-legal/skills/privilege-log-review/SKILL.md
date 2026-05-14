---
name: privilege-log-review
description: 特権ログの初回レビュー — 明白な特権判断を下し、難しいものは弁護士レビュー用にフラグ、際どい判断は下さない。Use when the user says "review the privilege log", "priv log", "check privilege on these docs", or has a log to QA before production. 日本語トリガー: 特権ログレビュー、priv log、特権判断、生産前 QA
argument-hint: "[log file, or document set]"
---

# /privilege-log-review

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → レビュープロトコル、特権ログフォーマット。
2. 以下のワークフローと参照に従う。
3. 各エントリについて: 明白な特権 / 明白に非特権 / 弁護士レビュー必要。フラグの理由。
4. 出力: フラグ付きのレビューされたログ。生成前に弁護士がすべてのフラグをレビュー。

---

# Privilege Log Review

## ディスクローズ文書使用制限

訴訟文書セットで作業を開始する前に尋ねる: 「これらの文書のいずれかが法的手続きでのディスクローズまたはディスカバリーを通じて取得されましたか?」 Yes なら:

- **イングランド&ウェールズ(CPR 31.22 / Civil Procedure Rules 31.22):** ディスクローズを通じて取得した文書には、暗黙の約束が適用される — 裁判所が許可を与える、ディスクローズする当事者が同意する、または文書が公開法廷で読まれた場合を除き、ディスクローズされた手続きの目的のためにのみ使用できる。許可なしに別の案件、別の請求、または商業目的のために使うことは contempt です。
- **米国:** 保護命令と Rule 26(c)(連邦民事訴訟規則 26(c) / 機密保持命令)が類似の制限を課す可能性。命令を確認。
- **その他の法域:** 類似の制限が一般的に適用。ローカルルールを確認。

確認: 「この使用はディスクローズされた手続き内、または許可 / 同意あり、または文書は現在公開」。確認できなければフラグ: 「⚠️ ディスクローズ文書には使用制限がある可能性。進む前にこの使用が許可されていることを確認。」

## 案件コンテキスト

**案件コンテキスト。** プラクティスレベル CLAUDE.md の `## 案件ワークスペース` をチェック。litigation-legal-ja のデフォルトは `Enabled: ✓` — すべてのケースが独自の案件ワークスペースを取得。`Enabled` が `✗`(一度に 1 ケースで作業しているため off)なら、この段落の残りをスキップしてプラクティスレベルコンテキストを使用。有効化されていてアクティブ案件がない場合、尋ねる: 「Which matter is this for? Run `/litigation-legal-ja:matter-workspace switch <slug>` or say `practice-level`.」 案件固有コンテキストとオーバーライドのためにアクティブ案件の `matter.md` をロード。出力を `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/<matter-slug>/` の案件フォルダに書き込み。`Cross-matter context` が `on` でない限り、別の案件のファイルを決して読まない。

---

## 目的

特権ログには 3 種類のエントリがあります: 明白に特権あり、明白にない、考える必要があるもの。このスキルは最初の 2 つの種類をソートし、弁護士の時間がまったく 3 つ目に行くようにします。

**これは初回パス。弁護士はすべてのフラグをレビュー。例外なし。**

## レコード忠実性 — ピンポイントと引用カバレッジ

このスキルが特権判断のために規則、ローカルバリアント、または権威(FRCP 26(b)(5)(A) / 連邦民事訴訟規則 26(b)(5)(A) / 特権主張、州規則、ローカルルール、放棄スコープに関する判例、支配的目的に関する判例)を引用するとき、2 つのルールが適用されます。

**ピンポイント引用は命題全体を支持しなければならない。** レビューが 1 つの規則または判例を引用して複数部分の命題を支持する場合 — 「ログは各文書を記述し、訴訟の予見で準備された素材のみを差し控える必要がある」 — ピンポイントがすべての要素をカバーすることを検証。1 つしかカバーしないなら、引用を分割するか命題を狭める。特権立場の一部を支持する引用は、相手方弁護士が引用を読んで競合する要素に届かないと指摘したときに立場を拒絶されます。これは「misgrounded citation」失敗モード: 引用は存在し、箇所も存在しますが、記述通りの命題を支持しません。

**チェック前にすべての引用を抽出。** このレビューが権威を引用する — またはログ、関連ブリーフ、または支持申立てで別の引用チェックがリクエストされる場合:

1. **最初のパス: 抽出。** 文書を読みすべての引用(規則、判例、制定法、ローカルオーダー、レコード引用)のリストを構築。カウントを報告: 「Found [N] citations.」
2. **2 回目のパス: チェック。** 各々を出典に対してチェック。サンプリングしない。最初の 5 つで止まらない。
3. **カバレッジを報告。** 「Checked [N] of [M] citations. [K] could not be retrieved — verify manually. [J] confirmed. [I] flagged as potential miscitations. [H] flagged as misgrounded (cite exists but doesn't support the proposition).」
4. **出典テキストが利用できないとき、「could not check」と言い、決して「confirmed」とは言わない。** false positive は「couldn't check」より悪い — 悪い引用を通します。
5. **最も困難なエラーは部分支持。** 命題を読み、出典を読み、要素ごとに比較。

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → 特権ログフォーマット、レビュープロトコル。

**コンフリクトゲート — バイパス不可。** 特権ログをレビューする前に、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace is set up. I won't review a privilege log on a matter that hasn't been intaken — the conflicts check is the gate, and a privilege log review is work product that needs to live in the matter file."

**法域が重要。** 特権スコープ(A/C と成果物)、放棄法理、ログフォーム要件は連邦サーキットと州裁判所で大きく異なります。このレビューは config で指定されたフォーラムの規則を適用。案件が異なるフォーラム、転送されたケース、多法域生成、または特権に関する準拠法問題を伴う場合、ここでの判断は転送されないかも — 支配的フォーラムに対して再実行。

## Step 0: フォーラムの特権ログ規則を調査

**エントリをレビューする前に、フォーラムの特権ログ要件(FRCP 26(b)(5)(A) または州相当)、ローカルルールバリアント、裁判官のスタンディングオーダーを調査。必要なフィールド、記述レベル、カテゴリーログまたはメタデータログ accommodation を特定。プライマリソースを引用。**

**No silent supplement.** 設定された法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)へのリサーチクエリが、フォーラムの規則、放棄法理、またはローカルバリアントについて少数または結果なしを返す場合、見つかったものを報告して停止。尋ねずに web 検索やモデル知識からギャップを埋めないこと。こう言う: 「The search returned [N] results from [tool]. Coverage appears thin for [rule / doctrine]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) leave the `[UNCERTAIN]` marker and stop here. Which would you like?」 弁護士が低い自信の出典を受け入れるかを決定;スキルは決定しません。

**Source attribution.** レビュー出力のすべての規則参照と権威に出処をタグ付け: 法律調査コネクター経由で取得された引用には `[Westlaw]`、`[CourtListener]`、`[Trellis]`、`[Descrybe]`、または MCP ツール名;web 検索引用には `[web search — verify]`;トレーニングデータから思い出された引用には `[model knowledge — verify]`;レビューする弁護士が供給した引用には `[user provided]`。`verify` タグの引用は高い捏造リスクを運び、最初にチェックすべき。タグを絶対に剥がしたり折りたたんだりしないこと — それらは、サービス前にどの権威を再確認するかについてのレビューする弁護士へのシグナル。

**放棄法理は特権タイプで異なる:**

- **弁護士・依頼者特権放棄**はしばしば広い: 主題放棄は同じトピックの関連通信を一掃する可能性。
- **成果物放棄**はより狭い: 裁判所は典型的に意見成果物(より強い保護)を事実成果物と区別。事実成果物の放棄は自動的に意見成果物を放棄しません。

主張された各特権についてフォーラムの放棄法理を確認 — 弁護士が確認するまで `[UNCERTAIN]` フラグは放棄判断に残る。

## 判断

**3 状態ルール。スキルは主観的閾値が満たされないと黙って決定しない。** 不確実な判断 — 支配的目的不明、訴訟検討境界、混在の法的/事業コンテンツ、曖昧な第三者の存在 — について、スキルは特権指定を on のまま保ち、弁護士のための ⚠️ フラグを追加。Under-marking は特権を放棄(一方通行ドア);over-marking はレビューで弁護士が修正(両開きドア)。回復可能なエラーを優先。

**社内弁護士特権は法域固有で contested。** 社内弁護士との任意の通信を特権として分類する前に、法域をチェック:

- **米国:** 社内弁護士通信は、法的助言を取得または提供する目的のためになされ、弁護士が(事業ではなく)法的能力で行動しているとき一般的に特権。法的 vs 事業の区別は事実固有で contested。
- **EU(競争法 / DG COMP 手続):** *Akzo Nobel Chemicals v. Commission*(C-550/07 P)下、社内弁護士との通信は EU 競争法手続では特権ではない。CJEU は特権が独立した外部弁護士との通信にのみ適用すると判示。案件が EU 競争または国家援助を伴う場合、社内弁護士文書は強制可能。
- **ドイツ(Syndikusanwalt):** ドイツの Syndikusanwalt はハイブリッドステータスを持つ。特権は弁護士が行動した能力と、通信が「advocate」または「employee」役割にあるかに依存。2016 年以降の登録ルールが分析を変えた。
- **英国:** 社内弁護士特権は一般的に認められるが、「支配的目的」テストが適用され、法的 vs 商業助言の区別は scrutinize される。
- **フランス、ベルギー、その他の EU:** 社内弁護士は弁護士会のメンバーでない可能性があり、通信に特権がまったくない可能性。

**どの特権体制が適用されるかを述べずに、社内弁護士通信を「自信を持って特権」として決して分類しないこと。** 案件が非米国法域を伴う、特に EU 競争または任意の EU 規制当局: 「Documents from in-house counsel may have NO privilege in [jurisdiction]. Under *Akzo Nobel*, in-house communications are compellable in EU competition proceedings. Flag for review by a [jurisdiction] litigation specialist before asserting privilege.」

下の ✅「自信を持って特権、フラグなし」ティアは弁護士レビューをバイパスするように設計されたもの。それがまさに *Akzo Nobel* リスクが住むところ。法域が非米国か案件が EU 規制当局に触れるとき、社内通信に ✅ ティアはない — すべてが 🟡「法域注記付きで弁護士レビュー用にフラグ」に行く。

### 自信を持って特権(✅) — 指定を保持、フラグなし

- クライアントと外部弁護士間の通信、法的助言を求める/提供する、第三者 CC なし
- クライアントと社内弁護士間の通信、明白に法的(事業ではない)助言、第三者なし
- 弁護士によって/のために訴訟の予見で作成された成果物
- 法的戦略についての control group 内の通信

### 不確実 — 指定を保持 AND フラグ(✅ + ⚠️)

✅ または ❌ に自信を持って入らないもののデフォルト。スキルは主観的テストの自身の評価に基づき特権指定を差し控えません。例:

- **法的と事業の両方を行う社内弁護士** — この通信は法的助言か事業助言か? 支配的目的判断は弁護士のもので、スキルのものではない。
- **第三者が存在** — 第三者は特権内(共通利益、エージェント)か、彼らの存在が放棄するか? 指定を保持;弁護士用にフラグ。
- **混在目的文書** — 一部法的、一部事業。部分編集? 完全差し控え? 生成? 指定を保持;扱いを決定するために弁護士用にフラグ。
- **添付ファイル** — 別々に分析し、自信を持って ❌ でない限り各添付の指定を保持;特権が主観的判断に依存するものをフラグ。
- **訴訟前成果物** — 「訴訟の合理的検討」は事実固有;指定を保持;フラグ。
- **放棄リスク** — 後の共有履歴が曖昧;指定を保持;放棄質問をフラグ。

各フラグは特定の未解決質問と各方向に切る証拠を記録、弁護士が文書を再読せずに決定できるように。

### 自信を持って非特権(❌) — 削除を推奨、ただし評価を注記

明白なケースのみ。出力は弁護士がスポットチェックできるよう依然評価根拠を記録;単独でログから指定を削除しません。

- どこにも弁護士関与なし
- 弁護士 CC 付きの事業助言(リーガル CC は特権を作らない)
- 基礎事実(事実は特権ではない — 事実*について*の通信は特権の可能性)
- 明らかに特権外の第三者がコピー(機密性を破る)
- 独立して非特権の添付ファイル(メールが特権の可能性;添付された売上数値のスプレッドシートはそうでない)

これらのいずれかが*際どい*なら — 第三者はエージェントかも、弁護士の CC は実際に法的リクエストにあるかも — それは不確実、❌ ではない。不確実バケットにルートしてフラグ。

## ワークフロー

### Step 1: フォーマットチェック

ログは必要なものを持っているか?

| Field | Present? |
|---|---|
| Date | |
| Author | |
| Recipients (all — TO, CC, BCC) | |
| Document type | |
| Privilege claimed (A/C, WP, both) | |
| Description (特権コンテンツを明かさず評価するのに十分) | |

欠落フィールド → 実質的レビュー前に完成のためにフラグ。

### Step 2: エントリごと

各エントリについて:

```
Entry [N] ([Bates]): [✅ Priv | ✅ Priv + ⚠️ Flag | ❌ Not priv (assessed)]
[✅(フラグなし)なら: 1 行の理由]
[✅ + ⚠️ なら: 指定を保持;弁護士が答える必要のある特定の質問;各方向に切る証拠]
[❌ なら: 1 行の理由 — ただし指定は弁護士が削除するまでログに残る]
```

**スキルの自身の主観的判断に基づいて特権指定を黙って剥がすエントリを決して生成しないこと。** ❌ はフラグと共にログされる推奨;弁護士が行動する。

### Step 3: パターンフラグ

ログ全体で:

- 同じ問題が繰り返される?(例: 50 エントリに同じ第三者 — 1 つの決定が 50 フラグを解決)
- 過剰指定パターン?(すべてが区別なく指定されているなら、弁護士用に表面化 — ただしログを狭める判断は弁護士のもので、スキルのものではない。Under-designation は放棄;over-designation は修正可能。)
- 記述不足?(裁判所が in camera レビューを命じるほど曖昧な記述)

## 出力

**特権ログが相手当事者に提供される前(重大な行為 — これはログの提供 AND ディスカバリーで文書を指定すること、例えば Confidential / Highly Confidential / AEO のような保護命令指定で差し控えられたまたは生成された文書の指定を含む):** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Submitting a privilege log and designating documents in discovery both have legal consequences — over-designation risks sanctions and loss of credibility; under-designation risks waiver; a misdesignated production may be unrecallable. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: 案件、ログエントリ数、⚠️ フラグと際どい判断、パターン観察(過剰指定、曖昧な記述)、特権タイプ別の放棄法理ポスチャ、サービスまたは指定で何が悪くなり得るか、弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしにログをサービス可能として扱わない。初回パスレビュー、ソート、フラグにゲート不要 — サービスと指定には必要。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

## Privilege Log Review: [Matter] — [date]

**Applicable rule:** [FRCP 26(b)(5)(A) / 州規則 / ローカルルール / スタンディングオーダー — ピンポイント引用] `[UNCERTAIN — verify currency]`
**Entries reviewed:** [N]
**Results:** [N] ✅ 自信ある priv / [N] ✅+⚠️ priv 保持&フラグ / [N] ❌ 削除推奨(弁護士が確認)

### ✅ + ⚠️ フラグ — 指定保持、弁護士が決定

| Entry | Bates | Issue | Evidence for priv | Evidence against | Question |
|---|---|---|---|---|---|
| [N] | [範囲] | [何が主観的か] | [1 行] | [1 行] | [下す具体的判断] |

### ❌ 指定削除を推奨(弁護士が剥がす前に確認)

| Entry | Bates | Reason |
|---|---|---|

*記録、実行ではない。スキルはログから特権指定を削除しない — 弁護士が、根拠をレビュー後に。*

### ✅ 特権(アクションなし)

[カウント。リクエストでリストが利用可能。]

### パターン観察

[繰り返される問題、過剰指定、記述問題]

### マーカー規律

- `[VERIFY: factual assertion about document/custodian/date]`
- `[UNCERTAIN: 際どい特権判断 / 放棄スコープ / 法理質問]`
- `[CITE NEEDED: rule, local variant, or authority supporting a call]`

---

**弁護士はすべての ⚠️ と ❌ を任意のアクション前にレビューしなければならない。**

**特権の出典素材。** このレビューはエントリと基礎となる文書を読み、これらは定義により特権候補素材です。レビュー出力はそのステータスを継承 — 特権素材と共に保管し、適切にマーキングし、特権サークル外に流通させないこと。流通自体が保護を放棄する可能性。
```

## このスキルが断固としてしないこと

- 際どい判断を下す。⚠️ は「人間が決定する」を意味します。任意の主観的テスト(支配的目的、合理的検討、共通利益スコープ、後の共有による放棄)について、スキルは特権指定を on のまま保ちフラグ。
- 自身の評価に基づいてログから特権指定を剥がす。❌ は弁護士のために記録された*推奨*で、ログに対して取られたアクションではない。
- 文書を生成または差し控える。助言;弁護士が決定;弁護士が行動。
- ✅ 判断の正確性を保証。弁護士がログに対して責任。これは初回パス。

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` に従って、次ステップの決定ツリーで締めくくります。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではありません。ツリーが出力です;弁護士が選びます。

