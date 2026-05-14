---
name: aia-generation
description: >
  Run an AI impact assessment — structured intake, risk analysis, regulatory
  classification per regime in scope, policy consistency diff, and recommendation
  with conditions. Uses the house-style structure learned from the seed impact
  assessment in `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md`.
  Use when user says "impact assessment for", "assess this AI use case", "run an
  AIA", "generate an AIA", "we need to document this AI system", "AI risk
  assessment for X", or follows a conditional triage result.
  AI 影響評価を実施 — 構造化されたインテーク、リスク分析、対象範囲のレジームごとの規制分類、
  ポリシー整合性差分、条件付き推奨。
  `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md`
  のシード影響評価から学習したハウススタイル構造を使用します。
  日本語トリガー: 影響評価、AI 影響評価、AIA を実施、この AI ユースケースを評価、
  この AI システムを文書化する必要がある、X の AI リスク評価。
argument-hint: "[describe the use case or system, or pass a triage result]"
---

# /aia-generation

1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます。影響評価のハウススタイルがポピュレートされていることを確認します。
2. ガバナンス階層とユースケースの特性から、下記のフレームワークを用いてリスクトラック(fast または full)を決定します。
3. インテークを実行 — 会話的に、フォームではなく。
4. フットプリント内の各レジームについて規制分類 — 階層、prohibited-practice エクスポージャー、適用される義務を調査し、一次ソースを引用します。
5. ハウススタイル(シード文書から、なければデフォルト)で評価を書きます。
6. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` AI ポリシーコミットメントに対してポリシー差分。
7. 出力: 評価ドキュメント + 条件リスト + ハンドオフフラグ(必要に応じてプライバシー PIA、ベンダーレビュー)。

```
/ai-governance-legal-ja:aia-generation "AI résumé screening for HR"
```

---

## Matter context (案件コンテキスト)

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` を確認します。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、この段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は不可視です。有効でアクティブな案件がない場合、尋ねます: 「どの案件? `/ai-governance-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください。」アクティブ案件の `matter.md` を案件固有のコンテキストとオーバーライドのためにロードします。出力は `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/<matter-slug>/` の案件フォルダーに書き込みます。`Cross-matter context` が `on` でない限り、別の案件のファイルを決して読まないこと。

---

## 目的

AI 影響評価は、文書化された判断であり、フォームではありません。次の問いに答えます: この AI システムは何をするか、どのように出力に到達するか、間違ったときに誰が影響を受けるか、監督はどうか、デプロイしても良いか。このスキルはその会話を構造化し、出力をこのチームのフォーマット — コールドスタート時にシード影響評価から学習したフォーマット — で書きます。

AI 影響評価は PIA と同じではありません。PIA は個人データが合法的に取り扱われるかを問います。AIA は AI システムが責任を持って設計・デプロイされているかを問います。両者はしばしば並行して行う必要があり、互いの代替ではありません。

## ハウススタイルをロード

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → `## Impact assessment house style` を読みます。そこには以下があります:
- この会社で影響評価をトリガーするもの
- シード評価から抽出された構造テンプレート
- 典型的な深さ
- 誰がサインオフするか

シード構造が `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` にある場合、**それを使用します**。要点は、この評価がこのチームが生成する他の評価のように見えることです。

**Jurisdictional scope(法域範囲)。** この評価は、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Regulatory footprint` に列挙された規制レジームを適用します。AI 法的規則、リスク分類、デプロイ義務は法域によって大きく異なり、速く動いています。このシステムがそのフットプリント外でデプロイされる(または将来される)場合、または準拠法問題が関与する場合、この分析は書かれたままでは適用されない可能性があります — フットプリントを再実行または拡張してください。

---

## Step 0: 影響評価が必要か?

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` のトリガー基準を確認します。

**以下も常時確認:**
- この AI は、人に影響する決定(雇用、信用、アクセス、価格、コンテンツモデレーション)を行うか、または実質的に影響しますか?
- この AI は個人に関する個人データを処理しますか?
- これは純粋に内部ではなく、顧客対応の AI システムですか?
- この AI は、会社が deployer であるサードパーティモデルを使用していますか?
- ユースケースは `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の elevated または high ガバナンス階層にありますか?

上記のいずれでもなく、ハウストリガーが満たされない場合:
> 「フル影響評価が必要なように見えません。後で誰かが尋ねる場合に備えて、その理由を説明するファイル用の 1 段落の記録をここに示します。」

---

## Step 1: リスクトラック

インテークの前に、どのトラックを実行するかを決定します。階層定義と fast-track 基準は、ハードコードされたレジーム固有のフレームワークからではなく、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Use case registry` と `## Governance tiers` から来ます。

ユーザーの規制フットプリントの各レジームについて、適用されるリスク分類フレームワークを調査します。多くのレジームはリスク階層、影響を受ける人口、決定の重要性で区別します — 特定の基準を調査します。ほとんどのレジームは従業員データを個人データとして、従業員監視を重要なものとして扱うことに注意してください。内部のみのシステムが範囲外と仮定しないこと。

> **No silent supplement。** 設定された法務リサーチツール(Westlaw, EUR-Lex, 規制者サイト, またはファームのプラットフォーム)へのリサーチクエリがレジームのリスク階層やトリガーについて結果をほとんど、または全く返さない場合、見つけたものを報告し、停止します。尋ねずに web 検索やモデル知識からギャップを埋めないこと。次のように述べます: 「検索は [tool] から [N] 件の結果を返しました。[regime / topic] についてカバレッジが薄いように見えます。オプション: (1) 検索クエリを広げる、(2) 別のリサーチツールを試す、(3) Web を検索する — 結果は `[web search — verify]` でタグ付けされ、依拠する前に発行機関に対してチェックすべきです、または (4) unverified としてフラグして停止する。どれを希望しますか?」より低い信頼度のソースを受け入れるかは弁護士が決めます。
>
> **Source attribution tiering(ソース属性のティアリング)。** AIA のすべての引用 — 規制テキスト、delegated act、ガイダンス、標準 — をソースでタグ付けします。モデル知識による引用については、単一の包括的な「verify」タグではなく、3 つの階層のいずれかを使用します:
>
> - `[settled]` — 安定し、変わっていない可能性が高い、よく知られた法令・規制参照(例: 概念としての GDPR Art. 22、EU AI Act としての Regulation (EU) 2024/1689 の存在)。証明前に検証するが、優先度は低い。
> - `[verify]` — 実在するが検証すべきモデル知識引用: 特定の delegated / implementing acts、規制者ガイダンス、NYC DCWP 規則、Colorado AI Act 条項、調和標準、施行日、EEOC ガイダンス、2023 年以降のすべて。
> - `[verify-pinpoint]` — pinpoint 引用(特定の EU AI Act 条項番号、annex 参照、Colorado AI Act サブセクション、NYC LL 144 規則セクション、サブパラグラフレター)は最高の捏造リスクを抱え、常に一次ソースに対して検証すべきです。特に EU AI Act の条項番号は consolidation 中にシフトしました。Act への pinpoint 引用は、Official Journal テキストに対してすべて検証されるべきです。
>
> ツール取得引用はそのソースタグを保持(`[Westlaw]`, `[EUR-Lex]`, `[regulator site]`, または MCP ツール名)、web 検索引用は `[web search — verify]` のまま、ユーザー提供引用は `[user provided]` のまま。ティアリングは実際の検証作業を表面化します — すべてを検証する読者は何も検証しません。タグをストリップしたり折り畳んだりしないこと。
>
> **非弁護士ユーザーの場合、不確実な日付はインラインではなく、confirm-list に。** 「effective February 1, 2026」に `[verify]` タグが付いていると、`[verify]` の意味を知らない CISO には「effective February 1, 2026」と読まれます。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Who's using this` を読みます。ロールが **Non-lawyer** で、日付、期限、段階的施行、閾値、または施行日アサーションが不確実(インラインなら `[verify]` または `[verify-pinpoint]` を伴うもの)である場合、インラインアサーションを「effective date: confirm with counsel」(または「threshold: confirm with counsel」など)に置き換え、すべての不確実なアサーションを最後の AIA セクションに集めます:
>
> > **Things I'm not certain about — ask your attorney to confirm before relying on this:**
>
> 各不確実な項目について (1) 私が言ったこと、(2) 私が何について不確実か、(3) なぜそれが評価に重要か、を列挙します。これは、非弁護士の読者がフラグ付きベストゲスをチェック済み事実と取り違えるのを防ぎます。弁護士ロールユーザーはインライン `[verify]` の扱いを受けます — 彼らはタグの意味を知っています。

**Fast track vs. full assessment:** `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` が短縮処理の対象となるものを定義します。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` が fast-track 基準を定義していない場合、デフォルトでフル評価に設定し、次回のために何の基準をキャプチャしたいかをユーザーに尋ねます。

迷ったらフル評価を実行します。間違った fast track は、低リスクのものに対する徹底的な評価より悪いものです。

---

## Step 2: インテーク

何かを書く前に、これらに対する答えを得ます。会話的でも問題ありません — これは送るフォームではありません。

### システム

- AI は何をするか? マーケティングコピーではなく、プレーンな言葉で記述します。
- どのモデルまたはベンダーが動かしているか? ファインチューニング済みかオフザシェルフか?
- ワークフローのどこに位置するか — assistive(人間が出力をレビュー)、augmentative(人間がオーバーライドできるが通常はしない)、または automated(ループ内に人間なし)?
- 出力は何か — 生成されたテキスト、スコア、分類、推奨、アクション?

### 誰が影響を受けるか

- AI の出力は誰に作用するか — 従業員、顧客、サードパーティ?
- AI がエラー(false positive、false negative、ハルシネーション)を起こした場合、誰が害を被り、現実的な最悪ケースは何か?
- 脆弱なグループが範囲内に不釣り合いに含まれるか — 未成年者、求職者、財政的困窮にある人々、患者?

### 入力とデータ

- AI はどのようなデータを取り込むか?
- 個人データを取り込むか? 誰の?
- モデルはこの会社のデータで訓練されたか、それとも会社固有の訓練なしのファウンデーションモデルか?
- 入力データはどこへ行くか — サードパーティモデル API に境界を超えるか?

### 決定と監督

- AI 出力は自動的にアクションをトリガーするか、または人間が出力で何をするかを決めるか?
- 人間のレビューがある場合: 人間はどのくらいの頻度で AI の出力を実際に変更するか?(答えが「まれ」の場合 — 人間は本当にレビューしておらず、ラバースタンプを押しています。)
- AI の出力によって影響を受ける人々のための appeals または訂正プロセスはあるか?
- AI システムの出力に対して誰が説明責任を負うか — 指名された所有者はいるか?

### 精度と障害

- 既知または推定エラー率は何か? どのようなテストが行われたか?
- AI が間違ったとき何が起きるか — エラーは表面化、ログ、訂正されるか?
- バイアステストは行われたか? どの人口統計グループに対して?

### デプロイ段階と規模

尋ねます:
- **Stage:** 「このシステムは (a) 提案されたがまだ構築されていない、(b) パイロット中、(c) 本番稼働中、または (d) 本番稼働中でスケール済み、ですか?」
- **Scale:** 「おおよそ何人の個人が [月/年] あたり影響を受けますか? どれくらいの期間稼働していますか?」
- **History:** 「以前に評価されましたか? 異議申立、控訴、または逆転された決定を生成しましたか?」

段階が評価を変えます: 提案されたシステムは設計レビュー(安全に構築できるか?)を受けます。パイロットは設計レビューと「スケール前」ゲートを受けます。本番システムは振り返り影響チェック(害を引き起こしたか?)と将来レビューの両方を受けます。本番稼働中でスケール済みのシステムは上記すべてに加えて、論点が見つかった場合の改善計画を受けます。なぜなら、単にオフにすることはできないからです。

---

## Step 3: 規制分類

**Step 3 事前チェック — フットプリントの鮮度。** キャプチャされた `## Regulatory footprint` を反復処理する前に、ユースケースの影響を受ける人口と決定タイプ(Step 2 から)を、書かれたフットプリントと比較します。フットプリントはコールドスタート時、その時点での会社の運営スタンスに基づいて設定されました。ユースケースが、フットプリントが想定していない影響を受ける人口(例: 子供、新しい州の従業員、EU データ主体)または決定タイプ(例: 採用、信用度、健康診断、法執行、重要インフラ)を導入する場合、**古いリストを反復処理するのではなく、適用されるレジームを再導出します。**

ユーザーに次のように述べます:

> "The practice profile's regulatory footprint was set for [affected populations / decision types captured at cold-start]. This use case affects **[new population or decision type — e.g., employees in Colorado, minors under 13, credit decisions, biometric identification]**, which is not in the captured footprint. I'm going to re-derive the applicable regimes from the company's operating jurisdictions ([list from `## Company profile`]) and this use case's decision type ([Y]), rather than use the stale footprint. If this use case is representative of work you expect to see more of, update `## Regulatory footprint` at the end of this run so the next AIA doesn't have to re-derive."

よくある失敗モード: フットプリントが EU AI Act + GDPR + NYC Local Law 144 を列挙し、ユースケースが Illinois と Colorado にデプロイされる採用システム。フットプリントには Illinois または Colorado のエントリがないため、これを反復処理すると IL AIVIA、新しい Colorado AI Act の deployer 義務、および生体コンポーネントの BIPA 含意を黙って見逃します。再導出します。

2 番目の失敗モード: フットプリントが、今重要になった(または施行された)レジームより前に設定された。再導出がフットプリントにないレジームを表面化させた場合、出力の推奨セクションでフラグし、権威を引用し、フットプリントの更新を推奨します。

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → `## Regulatory footprint` でこのシステムに適用される各レジーム — **加えて上記の再導出によって表面化したレジーム** — について、現在運用中のリスク分類フレームワークを調査し、システムがどこに該当するかを判定します。

調査タスク:
- レジーム自身の階層分類学(例: prohibited / high-risk / limited / minimal、またはレジームの相当物)は何か?
- 各階層の基準は何か? pinpoint 参照付きで一次ソースを引用。
- システムは、その機能、影響を受ける関係者、決定の重要性を考慮して、どの階層に該当するか?
- システムが触れる可能性のある禁止プラクティスはあるか? 可能なマッチを critical として扱い — 直ちにフラグ。
- 階層に関係なく適用される透明性義務はあるか(ユーザーが AI と対話していることの開示、AI 生成コンテンツのラベリング、自動決定の対象となる人々への通知)?
- 会社が汎用またはファウンデーションモデルを提供する builder の場合、provider レベルの義務(技術文書、訓練データ透明性、著作権遵守、systemic-risk テスト)は何か?
- **フットプリント内のレジームは、別個の基本的人権影響評価(FRIA)を要求するか?** EU AI Act Art. 27 は、高リスク AI システムの特定の deployer(公的機関、公共サービスを提供する民間エンティティ、および特定の信用度および保険リスク評価ユースケース)に対して FRIA を要求します。各レジームについて、この AIA とは別個の deliverable である等価な基本的人権または人権影響評価を確認します。FRIA(またはレジーム等価物)が要求される場合、推奨と条件で別個の deliverable としてフラグ — この AIA を代替として扱わないこと。

内部のみのシステムが範囲外と仮定しないこと — ほとんどのレジームは従業員データを個人データとして、従業員監視を重要なものとして扱います。具体的な規則を検証してください。

**Provider-vs-deployer split(`AI role: Both` の場合)。** `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → `## Company profile` → `AI role` が `Both` の場合(会社が provider/builder と deployer の両方)、Section 6 にはレジームごとの provider-vs-deployer マッピング表を含めなければなりません。ほとんどのレジームは provider(または builder)と deployer(または user)に対して実質的に異なる義務を課します — それらを 1 つの未分化リストに集約すると、義務が漏れ、リスクが融合します。provider と deployer の義務を 1 つのセクションに統合しないこと。レジームごとに以下を生成します:

| Obligation | As provider | As deployer |
|---|---|---|
| [specific obligation, pinpoint cite] | [what applies / does not apply / with what carve-outs] | [what applies / does not apply / with what carve-outs] |

**高リスクまたは等価な分類が適用される場合:**
評価でフラグし、具体的な条項とレジームを引用します。この AIA は内部レビューを文書化しますが、レジームが要求する正式な適合性評価の代替にはならないことを注記します。影響を受ける法域でのデプロイ前に外部法務レビューを推奨します。

評価出力で分類と引用された権威をキャプチャします。

---

## Step 4: 評価を書く

**`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` のシード構造を使用します。** キャプチャされていない場合、このデフォルトを使用:

```markdown
[WORK-PRODUCT HEADER — plugin config ## Outputs ごと — ロールにより異なる; `## Who's using this` を参照]

# AI Impact Assessment: [System/Feature Name]

**Prepared by:** [name] | **Date:** [date] | **Status:** DRAFT / APPROVED
**System owner:** [name] | **AI governance reviewer:** [name]
**Governance tier:** [Standard / Elevated / High]
**Track:** [Fast track / Full assessment]

---

## Executive summary

[2 文: この AI が何をするか、デプロイしても良いか。例: "This system uses a third-party LLM to draft initial responses to customer support tickets before human agent review. Processing is consistent with the company's AI policy; three conditions required before production deployment."]

**Overall risk:** 🟢 Low / 🟡 Medium / 🟠 High / 🔴 Very high

---

## 1. System description

**What it does:** [plain English — マーケティングではない]
**Model / vendor:** [AI を提供する人]
**Deployment mode:** [Assistive / Augmentative / Automated]
**Output type:** [text / score / classification / recommendation / action]
**Status:** [Not started / Pilot / Production]

---

## 2. Affected parties

**Who it acts on:** [従業員 / 顧客 / サードパーティ]
**Scale:** [何人、どのくらいの頻度]
**Harm if wrong:** [最も現実的な最悪ケース — 具体的、汎用ではない]
**Vulnerable groups in scope:** [yes — [who] / no]

---

## 3. Data inputs

**Data categories used:** [特定のフィールド、"user data" ではない]
**Personal data:** [yes — [whose] / no]
**Data leaves perimeter?** [yes — to [vendor] / no]
**Model training:** [company data used / foundation model / fine-tuned on [dataset]]

---

## 4. Decision-making and oversight

**Human in the loop:** [Always / Nominally (rubber-stamp risk) / No]
**Override mechanism:** [人間が介入または訂正する方法]
**Appeals / correction for affected parties:** [yes — [how] / no]
**Named owner:** [name or role]

---

## 5. Accuracy and bias

**Error rate:** [known / estimated / untested]
**Failure mode:** [間違ったとき何が起きるか — 表面化? ログ? 訂正?]
**Bias testing:** [done — [results] / not done / not applicable]

---

## 6. Regulatory classification

*[システムに適用される規制フットプリント内の各レジームについて 1 サブセクション。]*

**Regime:** [name]
**Classification under this regime:** [tier、支配する条項への pinpoint 引用付き]
**Prohibited practices triggered:** [none identified / [specific provision and why]]
**Applicable obligations:** [調査されたリスト、引用付き — 透明性、文書化、人間監督、テスト、登録など]
**Fundamental-rights impact assessment required?** [Yes — 例: EU AI Act Art. 27 FRIA 適用 / レジーム等価物 / No / Not applicable. yes の場合、これは別個の deliverable であり、この AIA に包含されない。]
**Effective / enforcement date:** [date(s)]
**Ambiguity or open interpretation:** [まだ解決されていないものをフラグ]

**Provider-vs-deployer obligation split(`AI role: Both` の場合は必須):**

| Obligation | As provider | As deployer |
|---|---|---|
| [specific obligation + pinpoint cite] | [what applies / does not apply] | [what applies / does not apply] |

---

## 7. AI policy consistency

| Policy commitment | Consistent? | Notes |
|---|---|---|
| [`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` AI policy section からのコミットメント] | 🟢 / 🟡 / 🟠 / 🔴 | |

[いずれかが 🟡 以下の場合: デプロイ前にポリシー更新が必要、または設計を変更する必要があります。
どちらかが変わる必要があります — 両方フラグして開いたままにしない。]

---

## 8. Risks and mitigations

| # | Risk | Likelihood | Impact | Mitigation | Status | Owner |
|---|---|---|---|---|---|---|
| 1 | [この設計に固有のリスク — "AI hallucination" のように汎用ではない] | L/M/H | L/M/H | [specific control] | Done / Planned / Gap | [name] |

**Residual risk after mitigations:** [評価]

---

## 9. Recommendation

**[APPROVED / APPROVED WITH CONDITIONS / CHANGES REQUIRED / NOT APPROVED]**

**Conditions (if any):**
- [ ] [デプロイ前の具体的なアクション — 所有者、期限]

**Privacy review required?** [Yes — プラグインがインストールされていれば `/privacy-legal:pia-generation` を実行 /
No]

**Sign-off:** [name, date]

---

## Cite check

Section 6(およびその他の場所)の規制引用は AI モデルによって生成され、一次ソースに対して検証されていません。評価が認証または依拠される前に、引用された各条項について法務リサーチツール(Westlaw, EUR-Lex, またはファームのプラットフォーム)に対する検証パスを実行 — pinpoint、currency、delegated または implementing acts を確認します。AI 規制の状況は急速にシフトします。助言する前に検証してください。各引用のソースタグ(例: `[EUR-Lex]`, `[web search — verify]`)はどこから来たかを示します。`verify` タグはより高い捏造リスクを持ち、最初にチェックすべきです。
```

**AIA を認証する前に(Sign-off ステップ、Status: APPROVED をマーク):** `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Who's using this` を読みます。Role が Non-lawyer の場合:

> この AIA を認証することは法的結果を持ちます — 規制者や影響を受ける関係者がこのユースケースがどのように評価されたかを尋ねた場合、会社が依拠する記録になります。これを弁護士とレビューしましたか? Yes であれば続行。No であれば、彼らに持っていくブリーフはこちらです:
>
> [1 ページの要約を生成: システム、規制分類、特定されたリスク、講じられた緩和策、残存リスク、未解決の質問、認証前に弁護士に尋ねるべきこと。]
>
> 弁護士、ソリシター、バリスター、またはあなたの法域のその他の認可された法律専門家を見つける必要がある場合: あなたの専門規制者の照会サービスが最速の出発点です(米国では state bar、英国では SRA/Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアでは Law Society、またはあなたの法域の相当物)。

このゲートを明示的な yes なしに通過しないこと。弁護士レビュー用の DRAFT 評価はゲートを必要としません — 認証はします。

---

## Risk quality standards (リスク品質基準)

PIA スキルと同じ基準 — リスクは**設計に具体的に結びついている**必要があります。

| Bad risk | Why bad | Better |
|---|---|---|
| "AI hallucination" | すべての LLM に当てはまる; 何も言っていない | "Model may generate plausible but incorrect legal citations — support agents have no current verification step before sending to customers" |
| "Bias" | 漠然としすぎ | "Résumé scoring model trained on historical hires; if historical cohort was demographically homogeneous, underrepresented candidates may be systematically scored lower" |
| "Vendor risk" | 循環的 | "OpenAI's terms permit training on API inputs by default; unless the opt-out is confirmed in the agreement, customer support messages may be used to train the model" |

水増しされた 12 ではなく、現実のリスク 2〜5 件を目指します。

---

## AI policy diff (AI ポリシー差分)

すべての評価は `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の AI ポリシーコミットメントに対してクロスチェックすべきです。
よくあるドリフト:

- ポリシーが [category] での AI 使用を禁止している — このユースケースはそのカテゴリです。停止。
- ポリシーが人間のレビューを要求している — このデプロイには人間のステップがありません。設計を変更する必要があります。
- ポリシーが影響を受ける関係者への開示を要求している — 開示メカニズムが構築されていません。
- 承認済みベンダーリストが存在する — このベンダーはそこにありません。調達ステップが必要です。

すべての不一致をフラグします。デプロイ前にどちらかが変わる必要があります。

---

## Handoffs (ハンドオフ)

- **To product / engineering:** 所有者と期限を持つ条件リスト。「監督を追加」ではなく、「自動メールが送信される前に人間レビューステップを追加、所有者: [product lead]、ローンチ前」。
- **To privacy:** 個人データが関与する場合、フラグ: 「並行して `/privacy-legal:pia-generation [system name]` を実行 — プラグインがインストールされていれば — AIA は PIA の代替ではありません。」
- **To vendor-ai-review:** 新しいベンダーが関与する場合、フラグ: 「[vendor] の AI addendum がレビューされていない場合、本番前に `/ai-governance-legal-ja:vendor-ai-review` を実行。」
- **To reg-gap-analysis:** 新しい規制義務が表面化した場合(EU AI Act high-risk、新しいセクター規則)、そのスキルがギャップを追跡します。

---

## Close with the next-steps decision tree (次のステップのデシジョンツリーで終わる)

CLAUDE.md `## Outputs` のデシジョンツリーで終わります。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X, escalate, get more facts, watch and wait, something else)は出発点であり、ロックインではありません。ツリーが出力です; 弁護士が選びます。

## このスキルがしないこと

- デプロイを承認しないこと。人間が評価にサインします。
- 任意の規制適合性評価を構成しないこと — レジーム(例: EU AI Act)が正式な適合性評価を要求する場合、それはここにあるものを超える外部法務レビューと技術文書を要する別個の演習です。
- 緩和策を設計しないこと。何を緩和すべきかを記述しますが、エンジニアリングが修正を設計します。
- 個人データが関与する場合の PIA を代替しないこと。両方を実行します。
