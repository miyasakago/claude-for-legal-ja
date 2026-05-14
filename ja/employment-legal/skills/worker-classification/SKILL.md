---
name: worker-classification
description: >
  提案された労働者の関与 — 従業員、IC(独立請負人)、派遣、ベンダー — を、
  適用法域テストを実行して分類し、意図された取り決めと事実が実際にサポート
  するものの間の誤分類ギャップをフラグします。事前的使用のみ。誰かが「業務
  委託先を採用したい」「これはベンダーか派遣か」「この人をどう区分すべきか」
  と言うとき、または提案された就業取り決めを説明するときに使用。
  (Classify a proposed worker engagement — employee, IC, temp, or vendor.
  Prospective use only. Use when someone says "we want to bring on a
  contractor", "is this a vendor or a temp", "how should we classify this
  person", or describes a proposed working arrangement. 日本語トリガー:
  「労働者区分」「業務委託先の区分」「IC で採用」「ベンダーか派遣か」
  「区分判断」)
argument-hint: "[describe the proposed arrangement, or just start and I'll ask]"
---

# /worker-classification

法域に適用される分類テストを実行し、提案された取り決めが使おうとしている構造と一致しない箇所をフラグします。事前的のみ — 既存関係については、カウンセルに相談してください。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` をロード → 法域フットプリント、エスカレーション表。
2. 下記の完全なワークフローを実行。
3. 弁護士が事前に詳細を提供する場合、利用可能なものを抽出し、ギャップについてのみ尋ねる。既に提供された情報を再質問しない。

## 例

```
/employment-legal-ja:worker-classification
データサイエンティストを 6 ヶ月、SF オフィスから、当社ツールを使って、当社の
分析チームに組み込んで、採用したいです。
```

```
/employment-legal-ja:worker-classification
当社のリクルーター業務委託の取り決めは大丈夫ですか? 彼女は当社のためだけに
働き、自分の時間を設定、自分のラップトップを使用、配置ごとのプロジェクト
フィー。
```

```
/employment-legal-ja:worker-classification
(スキルが詳細を尋ねます)
```

---

## 案件コンテキスト

**案件コンテキスト。** プラクティスレベル CLAUDE.md の `## 案件ワークスペース` を確認。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、このパラグラフの残りはスキップ — スキルはプラクティスレベルのコンテキストを使用し、案件機構は見えません。有効でアクティブな案件がない場合、尋ねる: 「これはどの案件のものですか? `/employment-legal-ja:matter-workspace switch <slug>` を実行、または `practice-level` と言ってください。」案件固有のコンテキストと上書きについてアクティブ案件の `matter.md` をロード。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/<matter-slug>/` に書き込み。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まない。

---

## 目的

最も高くつく分類判断は、誰も意識的に下さなかったもの。誰かが望むものを記述(「業務委託先」)、関与が始まり、2 年後にファクトが雇用に見える。このスキルは仕事が始まる前に提案された取り決めに適用テストを通し — あなたが記述しているものが使おうとしている構造と一致しないときを伝える。

このスキルは推論パターンを教える。法律は述べない。各テスト定式化、法定引用、閾値、カーブアウトは、適用法域の現行リサーチから来なければならない。

## 事前的のみのハードゲート — インテイク前に実行

**このスキルは仕事が始まる前の提案された関与を分析する。** 実質的なインテイク(Step 1)前に尋ねる:

> この仕事は既に始まっていますか? 労働者は現在関与しているか、または何らかの期間(日、週、月、年)この取り決めの下で仕事を行っていますか?

答えが「はい」の場合 — 関与が任意の形式、任意の期間で既に存在する場合 — **停止**。Step 1 インテイクに進まない。既存取り決めの分類は計画演習ではない; 修復含意のある責任評価: バックペイ(OT、食事/休憩割増)、未払の雇用主側給与税、拒否された給付資格、失業と労災のバック露出、州ペナルティ(CA では PAGA)、IRS § 530 救済分析、— 進行中の仕事のある厳格テスト法域では — もう 1 日走らせる事前的露出。その分析は特権下、カウンセル主導、修復計画とカップリング。

このブロックを正確に出力して応答を待つ:

> **スコープ外 — 既存取り決め。**
>
> このスキルは、分類選択が契約と運用の構造化方法に情報を与えるよう、労働者の関与を*始まる前*に分析するよう設計されています。すでに存在する取り決めを記述されました。既存の関与を遡及的に分析することは異なる演習: 再分類リスク評価と修復計画のカップリング — バックペイ露出、給与税バック露出、ペナルティ露出、給付露出、IRS § 530 救済分析、事前的再構築。その作業は特権下、弁護士主導、ドルと執行露出を考えて外部弁護士レビューとカップリングされる可能性が高い。
>
> 推奨される次のステップ: 設定のエスカレーション表に従ってエスカレート(遡及分類の場合、これは通常 GC + 外部雇用法弁護士にルーティング)。エスカレーションルーティング用にフラグしました。
>
> **計画目的でとにかく事前的スタイル分析を進めたい場合は「proceed anyway」と言ってください — ただし理解してください:**
>
> - 出力は修復計画では**ありません**、そう扱われるべきではありません。
> - 出力はすでに働いた期間のバックペイ、ペナルティ、給与税露出をスコープし**ません**。
> - 出力はこのファクトパターンが実際に求める再分類リスク評価の代替に**なりません**。
> - 出力はこのスコープミスマッチを反映する目立つバナーを運び、結果を伴う行動のゲートは分析が信頼できるものとして扱われる前に弁護士の yes を要求します。
>
> 計画目的(例: 「今日新しく構造化していたら、これについてどう考えるべきか?」)でこのスキルを使用していて、修復質問について別の計画がある場合のみ「proceed anyway」と言ってください。

**明示的な `"proceed anyway"`(または同等のユーザー指示)でのみこのゲートを通過。ためらった「I guess」は数えない — 再プロンプト。ユーザーがとにかく進む場合、このセッションでこのスキルの各出力にこのバナーを前置:**

```
⚠️ SCOPE MISMATCH — OUT-OF-SCOPE USE
This skill analyzes prospective worker engagements. The arrangement here
already exists. This output is the prospective-style analysis the user
requested for planning purposes only — it is NOT a remediation plan, does
NOT scope existing back-pay / penalty / payroll-tax exposure, and does
NOT substitute for the reclassification-risk assessment this fact pattern
requires. The remediation question has been flagged for escalation to
counsel per your config's escalation table.
```

「この仕事は既に始まっていますか?」への答えが「いいえ」(関与が真に事前的、まだ始まっていない)なら、コンテキストロードに進む。

---

## コンテキストをロード

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` を読む → 法域フットプリント、メモされた分類履歴または先の和解、エスカレーション表、チームが記録したハウス分類ポリシー。

## 出力ヘッダー

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → `## 出力` の work-product ヘッダーを前置(ユーザー役割により異なる — `## 利用者` 参照)。

## ワークフロー

### Step 1 — 情報収集

以下を単一ブロックですべて尋ねる。1 つずつ滴下しない。なぜ尋ねるかを簡潔に説明 — 弁護士は質問が何をテストしているかを理解するとよりよく回答。

> 正しい分類テストを実行するために、提案された取り決めを詳細に理解する必要があります。できるだけ多くに答えてください — 絵が完全であるほど、分析が正確になります:
>
> **仕事**
> - この人は日常実際に何をしますか?
> - この仕事は会社のコアビジネスの一部か、周辺か? (例: ソフトウェア会社のソフトウェアエンジニア = コア; 法律事務所の IT 業務委託先 = より周辺)
> - これは明確な終わりのある定義されたプロジェクトか、継続的な無期限の仕事か?
> - スキルはどれほど専門的か? この人はチームが持たない専門知識を持つか?
>
> **コントロール**
> - 時間とスケジュールを設定するのは — 彼らかあなたか?
> - どこで働きますか — あなたのオフィス、彼らの場所、どちらか?
> - 仕事の仕方(方法、プロセス、シーケンス)を指示するか、最終結果が何であるべきかのみか?
> - 彼らはあなたの従業員を監督しますか?
>
> **経済**
> - どう支払いますか — 時給、日給、固定プロジェクトフィー?
> - 機器、ツール、ソフトウェアを提供しますか、それとも彼らが自身のものを使いますか?
> - 他の会社のために働きますか、これは独占的になりますか?
> - 彼らは金融リスクを負いますか — フィーを超えて利益を出せる、または関与で損失を出せますか?
> - 自分のビジネスエンティティ(LLC、S-corp、個人事業主)を持っていますか?
>
> **取り決め**
> - どう構造化したいですか — 直接業務委託、人材派遣会社の派遣、またはベンダー/SOW(会社間)?
> - 人材派遣会社の場合: 労働者に支払うのは誰 — 派遣会社かあなたか? 日常仕事をコントロールするのは誰?
> - 書面契約はありますか? 念頭にあるテンプレートはありますか?
> - おおよそ関与はどれくらい — 週、月、1 年以上?
> - 似た仕事をする従業員と並んで働きますか?
>
> **分類の目的**
> - 分類はどんな法的目的に役立つ必要があるか — 連邦給与税、FLSA 賃金・労働時間、州賃金・労働時間、失業保険、労災、給付資格? 異なる目的はしばしば異なるテストに支配され、答えが分岐する可能性。
>
> **法域**
> - この人は物理的にどこで仕事を行いますか?

進む前に応答を待つ。弁護士が特定の質問に答えられない場合、ギャップをメモ — それらは分析に影響。

### Step 2 — 適用テストを特定

> **進む前に適用テストをリサーチ。** インテイクで特定された法域と目的について、現在運用中の分類テストをリサーチ。法域は一般的に 1 つ以上の: ABC テスト、経済的現実テスト、コモンロー右制御テスト、ハイブリッド、または目的固有の法定テストを使用。連邦給与税を支配するテストは、州賃金・労働時間、失業、労災を支配するテストと同じでないかもしれない — 各目的を独自のトラックで実行。支配的な制定法、規制、または判例を引用。各ルールの施行日と最近改正されたかを注記。適用されるかもしれないカーブアウトまたは例外(例: B2B、専門サービス、建設、紹介機関、企業間契約関係)を特定。最新性を検証。いずれかの法域の法律の現状について不確実なら、弁護士検証用にフラグ — 確認していないテストを述べないこと。

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` が会社のハウス分類ポリシーを記録しているなら、最初に適用し、リサーチされたテストとの緊張をフラグ。

> **サイレント補完なし。** 設定済み法務リサーチツールへの研究クエリが、法域・目的の組み合わせについて結果をほとんどまたはまったく返さない場合、見つかったものを報告し停止。**ウェブ検索やモデル知識でギャップを埋めないで尋ねる。** 言う: 「検索は [tool] から [N] 件の結果を返しました。[jurisdiction / purpose / test] のカバレッジは薄いようです。オプション: (1) 検索クエリを広げる、(2) 別のリサーチツールを試す、(3) ウェブを検索 — 結果は `[web search — verify]` でタグ付けされ、依拠前に原典に対してチェックすべき、(4) 未検証としてフラグ立てて停止。どれにしますか?」弁護士が低信頼度ソースを受け入れるかどうかを決定。
>
> **出典帰属。** 各引用 — 各分類テスト、制定法、規制、または判例 — に出処をタグ: 法務リサーチコネクターから取得した引用は `[Westlaw]`、`[CourtListener]`、または MCP ツール名; ウェブ検索引用は `[web search — verify]`; 訓練データから想起した引用は `[model knowledge — verify]`; 弁護士が提供した引用は `[user provided]`。`verify` タグ付き引用は捏造リスクが高く、最初にチェックされるべき。タグを剥がしたり集約したりしない。

### Step 3 — リサーチされたテストをファクトに適用

Step 2 で特定された各テストについて、インテイクファクトに適用。各ファクターまたは要素を明示的にスコアリング — 要約しない。弁護士はどのファクターがクリーンでどれが問題かを見る必要がある。

下記のような構造を使うが、*ファクター*はこのファイルからではなくリサーチされたテストから populated:

```
Test: [name of test, per research]
Purpose: [what this test governs — federal tax / state wage-hour / UI / etc.]
Source: [pinpoint cite to statute/regulation/case]
Currency: [verified as of date]

| Factor / prong | Intake facts | Signal / pass-fail |
|---|---|---|
| [Factor 1 from researched test] | [from intake] | [direction or pass/fail] |
| [Factor 2] | [from intake] | [direction or pass/fail] |
| ...                            |                |                   |

Structure of the test:
[How the test weighs factors — e.g., a multi-factor balancing test, or a
conjunctive test where each prong must be satisfied, or a hybrid. State this
from research, not from memory.]

Result under this test:
[Employee-leaning / IC-leaning / Fails prong X / Uncertain — contested prong]
```

適用可能な各テストについて繰り返す。

**争われた要素に関する注記。** 一部のテストの一部の要素は判例で激しく争われ、ファクトに敏感。明示的に争われた要素を特定 — papering over しない。テストが述べられているという事実は、これらのファクトへの適用が確定したことを意味しない; 弁護士判断を要求する要素または法域で最近の訴訟を生み出した要素をフラグ。

### Step 4 — 分類しギャップをフラグ

**分類判断**

テスト結果に基づいて、この提案された取り決めの最も正確な分類を述べる:

- **従業員 (W-2):** ファクトは関連目的の 1 つ以上の適用テスト下で雇用をサポート。
- **独立請負人 (1099):** ファクトは関連目的のすべての適用テスト下で IC ステータスをサポート。
- **人材派遣会社経由の派遣:** 労働者は派遣会社の給与計算にいる; 会社はクライアント — 会社が日常コントロールを行使するなら共同雇用リスクが存在。関連なら適用される共同雇用主基準をリサーチ。
- **ベンダー/SOW:** 会社間関与; 労働者はベンダーエンティティに雇用されている — ファクトがサポートするなら最もクリーンな構造。
- **不明確 / 際どい判断:** ファクトが 1 つ以上のテストで両方向に切れる — どのテストが問題でなぜかを述べる。

テストが異なる目的について異なる答えを与える(例: 連邦税では IC として防御可能だが州賃金・労働時間テストに失敗)なら、明示的にそう言い、支配的な目的と法域を名指す。

**ギャップ分析**

これが最も重要な出力。意図された構造とファクトが実際にサポートするものを比較:

```
Intended structure: [what they said they want]
What the facts suggest: [what the researched tests say this actually is]

Gaps — where the arrangement doesn't match the intended structure:
🔴 [Factor]: [What they described] conflicts with [intended classification]
   because [specific researched test language + cite]. This is a significant
   misclassification risk if the engagement proceeds as described.
🟡 [Factor]: [What they described] is a weaker point under [test]. Not
   disqualifying alone, but combined with other factors increases risk.
✅ [Factor]: Supports [intended classification]. No issue.
```

**エスカレーション・トリガー**

以下のいずれか、またはその設定に記録されたチーム固有のトリガーが当てはまる場合、`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` に従ってエスカレート:
- 法域が厳格テストを使用し、提案された仕事が会社のビジネスのコア — カウンセルレビューなしに進まないこと。
- 設定でメモされた先の誤分類和解または監査 — 強化された監視が適用。
- 労働者が従業員を監督するか、有意な予算権限を持つ。
- 関与が明確なプロジェクト終点なしに 12 ヶ月を超えると予想される。
- 結果が分類を変える争われた要素。

### Step 5 — 出力

> **研究コネクター事前チェック。** 分析を発出する前に、このセッションで法務リサーチコネクター — Westlaw、CourtListener、または事務所設定の研究 MCP — が到達可能かをチェック。CLAUDE.md `## 出力` に従ってこれを reviewer note に集約: Step 2 でコネクターが結果を返さない(または実行時に何も設定されていない)場合、reviewer note の **Sources:** 行に記録 — 例: `not connected — cites from training knowledge; the highest-fabrication pinpoints in classification analyses are ABC-test codifications, state carve-out subsections (e.g., CA Lab. Code §§ 2775/2776/2783), element counts in B2B exemptions, and purpose-specific test selection — spot-check those first`。引用ごとの `[model knowledge — verify]` タグはインラインに残る。出力の上に独立したバナーを発出しない。

> **法域前提。** この分析はインテイクで特定された法域で運用中のテストを適用。分類ルールは州・国により実質的に異なり、1 つの目的(例: 連邦給与税)を支配するテストはしばしば別(例: 州賃金・労働時間)を支配するテストと異なる。仕事がここで分析されていない法域で行われる、または新しい目的が後で追加される場合、この分析は記述通り適用されないかもしれない。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

## Worker Classification Analysis
**Proposed arrangement:** [what they described]
**Jurisdiction:** [state/country]
**Purpose(s):** [federal tax / state wage-hour / UI / WC / benefits]
**Tests applied:** [list, each with pinpoint cite and currency date]

---

### Bottom line

[Can you proceed / Need to fix X first / Stop — one-sentence why]

---

### Classification

**Closest classification:** [Employee / IC / Temp via agency / Vendor-SOW / Unclear]

[One paragraph summary of why — test results in plain language, tied to the
cited sources.]

---

### Test results

#### [Test name — per research]
Purpose: [...] | Source: [...] | Currency: [...]
[Scored table from Step 3]
**Result:** [Employee-leaning / IC-leaning / Fails prong X / Mixed]

#### [Additional researched tests — repeat the block]

---

### Gap analysis

[Flags as structured in Step 4 — 🔴 significant risks, 🟡 weaker points,
✅ clean factors]

---

### Escalation

[None needed | Escalate to [name] before proceeding — [reason]]

---

### Next steps

[If IC viable: "Proceed — ensure the written agreement reflects the terms that
support IC status under the researched test."]
[If gaps exist: "Address the following before using IC structure: [list]"]
[If agency/vendor is cleaner: "Consider restructuring as [agency/SOW] — here's
why it's cleaner for this fact pattern."]
[If escalation needed: "Do not proceed until counsel reviews the [specific
issue]."]
[If employee confirmed: "Classification confirmed as W-2 employee — run
`/employment-legal-ja:hiring-review` to review the offer letter, restrictive
covenants, and jurisdiction-specific requirements."]
[If IC confirmed: "Classification confirmed as independent contractor — no
offer letter review needed. Ensure the written agreement reflects IC-supporting
terms before the engagement starts."]
[If agency/vendor: "Engagement should be structured through [agency/vendor
entity] — coordinate with them on worker agreement. No `/hiring-review` needed."]
```

## 結果を伴う行動のゲート(労働者を分類)

**「IC / 従業員 / 派遣 / ベンダーとして進む」最終推奨を生成する前に:** `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` の `## 利用者` を読む。役割が **Non-lawyer** の場合:

> 労働者を分類することは法的結果を持つ — 誤分類はバック賃金、税金、給付、ペナルティ、私人訴訟リスクに会社をさらし、複数の州で厳格責任。この分類判断を弁護士とレビューしましたか? はいなら進める。いいえなら、彼らに持っていくブリーフ:
>
> - 記述された取り決め(仕事、コントロール、経済、構造)
> - 法域と適用されたテスト
> - 引用と最新性付きのテスト別結果
> - 弱い要素を呼び出したギャップ分析(🔴 / 🟡 / ✅)
> - 未解決の質問
> - 何が間違いうるか(この取り決めが最も失敗しそうな誤分類理論; 先の監査/和解オーバーレイがあれば)
> - 弁護士に尋ねること(ここで IC は可能か、機関またはベンダー経由の再構築はリスクを取り除くか、分類をサポートするためにどんな契約条件が必要か)
>
> 弁護士、ソリシター、バリスター、または他の認定法務専門家を見つける必要がある場合: 専門規制機関(米国の州弁護士会、英国 & ウェールズの SRA / Bar Standards Board、スコットランド / NI / アイルランド / カナダ / オーストラリアの Law Society、またはあなたの法域の同等機関)に連絡し、紹介サービスへ。

このゲートを通過した「IC 可能」/「この分類を使う」最終出力を明示的な yes なしに生成しない。弁護士レビュー用 DRAFT 分析は OK。

---

## このスキルが**行わない**こと

- 既存関係を遡及的に分析 — これは事前的のみ。
- 業務委託契約または SOW をドラフト。
- 誤分類が既に発生した場合の修復について助言。
- 任意の法域について独力で法律を述べる — すべてのテスト、ファクター、カーブアウトは検証済みの現行リサーチから来なければならない。
- 際どい判断について外部弁護士を代替 — 厳格テスト法域、争われた要素、先の監査状況は、関与が始まる前に常に人間レビューを得るべき。

## 次のステップ・デシジョンツリーで締めくくる

CLAUDE.md `## 出力` の次のステップ・デシジョンツリーで終わる。このスキルが生成したものにオプションを調整 — 5 つのデフォルト分岐(X をドラフト、エスカレーション、追加情報を取得、様子見、その他)は出発点で、固定ではない。ツリー**こそ**が出力; 弁護士が選ぶ。
