---
name: reg-gap-analysis
description: >
  Diff a new AI regulation or guidance against your current governance posture —
  surfaces gaps, priorities, and a remediation plan with owners and deadlines.
  Use when an AI regulation moves (or you learn about one you missed), or when
  user says "new reg just dropped", "does [regulation] affect us", "gap analysis
  for EU AI Act", "compliance check against [AI law or guidance]", or pastes
  regulatory text.
  新しい AI 規制またはガイダンスを現行ガバナンススタンスに対して差分化 — ギャップ、
  優先順位、所有者と期限付きの修復計画を表面化します。
  日本語トリガー: 新規制が出た、[規制] は我々に影響するか、EU AI Act のギャップ分析、
  [AI 法またはガイダンス] に対するコンプライアンスチェック、規制テキスト貼り付け。
argument-hint: "[regulation name, or paste regulatory text, or attach a document]"
---

# /reg-gap-analysis

1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます。規制フットプリントとユースケース台帳がポピュレートされていることを確認します。
2. 下記のフレームワークを使用。
3. スコープ: この規制は適用されるか?(法域、閾値、builder/deployer、セクター。) 適用されない場合、1 行で完了。
4. 要件を抽出。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の現状に対して差分化します。
5. ギャップを優先順位付け。出力: must-do / should-do / already compliant / accepted gaps を伴う修復計画。
6. ファイル用に日付付き markdown ドキュメントとして保存。

```
/ai-governance-legal-ja:reg-gap-analysis "EU AI Act 高リスク条項"
```

---

## 目的

EU AI Act が施行される。Colorado が AI 法を成立させる。CFPB がモデルリスクガイダンスを発行する。FTC が AI 執行ポリシーを公開する。何かが動く — そして今、何があれば何を変える必要があるかを知る必要があります。

このスキルは新しい要件を現行 AI ガバナンススタンスに対して差分化(`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` ごと — ユースケース台帳、ベンダーポジション、影響評価プラクティス、AI ポリシーコミットメント)し、修復計画付きのギャップリストを生成します。

AI 規制のランドスケープは現在、他のどの法的領域よりも速く動いています。規制が本当に曖昧な場合、そう述べます。不確実性を覆い隠さないこと — リーガルチームは堅固な地盤にいるときと判断コールを行っているときを知る必要があります。

## 現状をロード

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます:
- `## Regulatory footprint` — 既に何が適用されるか
- `## Use case registry` — 実際に実行している AI、どの条件の下か
- `## AI policy commitments` — 公的または契約的にコミットしたもの
- `## Vendor AI governance` — どのベンダーポジションが設定されているか
- `## Impact assessment house style` — どの評価プラクティスが存在するか

規制が明らかに適用されない場合(間違った法域、閾値以下、間違ったセクター、builder/deployer の区別でスコープから除外される)、直接そう述べます: 「適用されない。理由: [reason]。アクション不要。」

---

## 最初にリサーチ、その後ワークフロー

ギャップ分析を実行する前に、ユーザーのフットプリントの法域について現在運用中の AI 規制レジームを調査します。各レジームについて以下を識別:

- **Scope** — 誰がカバーされるか(provider/builder vs. deployer vs. distributor vs. user; セクター carve-outs)。
- **Applicability thresholds** — 収益、ユーザー数、人員数、計算量、モデルカテゴリ、影響を受ける人口サイズ。
- **Risk-tier definitions** — レジームが階層をどのように区別するか(prohibited / high-risk / limited-risk / minimal)、各々に何があるか。
- **Substantive obligations** — 透明性、文書化、人間監督、バイアステスト、登録、インシデント報告、ベンダー flow-down。
- **Enforcement mechanism** — どの規制者、どの罰則、私的訴権の有無。
- **Effective dates** — 多くの AI 法は 2〜4 年で義務を段階的に施行 — どの義務が稼働中か、来るかを注記。

規制テキストを pinpoint 参照で引用します。継続的な解釈、delegated acts、または係属中の規則制定の対象となる条項をフラグします。AI 規制のランドスケープは急速に変化します — 助言する前に最新性を検証します。

ハードコードされた参照表からではなく、調査された要件からギャップ分析を構築します。

## ワークフロー

### Step 1: 規制をスコープ

差分化前に、答えます:

- **適用されるか?** 法域、閾値、セクター carve-outs、builder vs. deployer の区別。規制の特定のスコーピングルールを調査 — 仮定しないこと。

  *Builder/deployer がここで非常に重要。* 多くの AI レジームは AI システムを開発/提供するエンティティと、デプロイ/使用するエンティティに異なる義務を課します。各レジームの定義の下で会社が占めるロールを調査します。最初にスコープ; 適用されない法律をギャップ分析しないこと。

- **いつ?** 施行日。執行日(しばしば異なる)。特定の条項の段階的施行期間。最新性を検証。

- **実際に何が新しいか?** 一部の「新しい」AI 法は、AI に適用される既存の法的原則(消費者保護、反差別、セクターリスク管理)を大部分言い直しています。他は本当に新しい義務です。法律のフルテキストではなく、既に行っていることからのデルタを識別します。

### Step 2: 要件を抽出

規制、ガイダンス、またはサマリーを読みます。すべての実質的要件をリストします:

| # | Requirement | Citation | Category |
|---|---|---|---|
| 1 | [requirement] | [section] | [下記カテゴリ参照] |

**カテゴリ:**
- **Transparency** — AI 利用についてユーザー、従業員、または影響を受ける関係者への開示
- **Impact assessment** — デプロイ前の必要な文書
- **Human oversight** — 必須の人間レビュー、オーバーライド、または appeals メカニズム
- **Accuracy / testing** — バイアステスト、精度文書化、検証
- **Governance** — 登録、記録保持、指名された責任者
- **Vendor flow-down** — AI ベンダーに渡すか、AI ベンダーから上に渡す義務
- **Prohibited practices** — 特定の AI 能力または使用の outright 禁止
- **Rights** — 影響を受ける関係者が要求または invoke できること

### Step 3: 現状に対して差分

各要件について:

```markdown
### [Requirement #N]: [short name]

**Regulation says:** [requirement, 引用または言い換え]

**We currently:** [`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` / AI ポリシー / ユースケース台帳 / 評価プラクティスが示すこと]

**Gap:** [None | Partial | Full]

**If partial/full — what's missing:** [具体的 — 「より多くの文書」ではなく
「[use case category] に人間レビューステップが文書化されていない」]

**Effort to close:** [Policy update only | Process change | Product/system change |
New assessment required | Vendor renegotiation | Registration / filing]

**Risk of non-compliance:** [罰則範囲、執行可能性、評判]
```

### Step 4: 優先順位付け

すべてのギャップが同等ではありません。以下でソート:

1. **歯のあるハード期限** — 施行日 + アクティブな執行 + 実際の罰則
2. **Prohibited practice** — ギャップが禁止(プロセス要件ではなく)の場合、それは執行日に関係なく最初の優先順位
3. **Effort-to-impact 比** — ポリシー言語の更新は安価; デプロイ済みシステムに人間監督を追加するのはそうではない
4. **ユースケースの重複** — 台帳の複数のユースケースに影響するギャップは、単一ユースケースのギャップより高い優先順位

### Step 5: 修復計画

```markdown
[WORK-PRODUCT HEADER — plugin config ## Outputs ごと — ロールにより異なる; `## Who's using this` を参照]

## Remediation Plan: [Regulation name]

**Effective date:** [date]
**Enforcement begins:** [date if different]
**Applies to us as:** [Builder / Deployer / Both]

### Must-do before enforcement

| Gap | Fix | Owner | Due | Status |
|---|---|---|---|---|
| [gap] | [specific fix] | [name] | [date] | [ ] |

### Should-do (重要だが執行をブロックしない)

[same table]

### Already compliant

[gap = None の要件のリスト — 実際にどこにいるかの法務/エグゼクティブサマリーの有用なコンテキスト]

### Accepted gaps (リスク受諾、修正しない)

[あれば — 文書化された理論的根拠と誰がリスクを受諾したか。受諾されたリスクの文書化は、サイレントに未対処のままにすることよりも良いガバナンス。]
```

---

## ギャップ分析を構築する前に規制をリサーチ

特定のレジームのためのハードコードされた参照表に依拠しないこと。スコープ内の各規制について、現在運用中のテキストを調査:

- 会社のロール(provider/builder, deployer, importer, distributor)にどの義務が適用されるか?
- レジームの独自の分類(prohibited / high-risk / limited-risk / minimal、またはレジームの相当物)の下で、システムはどの階層に該当するか?
- 各義務の稼働 vs. 段階的施行の日付は何か?
- 解釈に影響する delegated acts、implementing acts、または規制者ガイダンスはあるか?
- builder コンテキストの場合: モデルレベルの義務(技術文書、訓練データ透明性、著作権遵守、systemic-risk テスト)はあるか?
- prohibited-practice カテゴリの場合: 触れる可能性のある台帳のユースケースをチェックし、執行日に関係なく critical としてフラグ。

pinpoint 参照で一次ソースを引用します。弁護士判断のために曖昧さをフラグします。

> **No silent supplement。** 設定された法務リサーチツール(Westlaw, EUR-Lex, 規制者サイト, またはファームのプラットフォーム)へのリサーチクエリがレジームのテキスト、delegated act、またはガイダンスについて結果をほとんど、または全く返さない場合、見つけたものを報告し、停止します。尋ねずに web 検索やモデル知識からギャップを埋めないこと。次のように述べます: 「検索は [tool] から [N] 件の結果を返しました。[regime / topic] についてカバレッジが薄いように見えます。オプション: (1) 検索クエリを広げる、(2) 別のリサーチツールを試す、(3) Web を検索する — 結果は `[web search — verify]` でタグ付けされ、依拠する前に発行機関に対してチェックすべきです、または (4) unverified としてフラグして停止する。どれを希望しますか?」より低い信頼度のソースを受け入れるかは弁護士が決めます。
>
> **Source attribution tiering。** ギャップ分析のすべての引用をソースでタグ付けします。モデル知識による引用については、単一の包括的な「verify」タグではなく、3 つの階層のいずれかを使用します:
>
> - `[settled]` — 安定し、変わっていない可能性が高い、よく知られた法令・規制参照(例: GDPR Art. 22、EU AI Act としての Regulation (EU) 2024/1689 の存在、C.R.S. § 6-1-1701 et seq. としての Colorado AI Act)。提出前に検証するが、優先度は低い。
> - `[verify]` — 実在するが検証すべきモデル知識引用: 特定の delegated / implementing acts、規制者ガイダンス、標準、執行アクション、判例 holdings、閾値、施行日、段階的施行条項、調和標準参照。
> - `[verify-pinpoint]` — pinpoint 引用(特定の条項番号、annex 参照、サブセクションレター、パラグラフ番号、標準条項参照)は最高の捏造リスクを抱え、常に一次ソースに対して検証すべきです。特に EU AI Act の条項番号は consolidation 中にシフトしました。Act への pinpoint 引用は、Official Journal テキストに対してすべて検証されるべきです。
>
> ツール取得引用はそのソースタグを保持(`[Westlaw]`, `[EUR-Lex]`, `[regulator site]`, または MCP ツール名)、web 検索引用は `[web search — verify]` のまま、ユーザー提供引用は `[user provided]` のまま。ティアリングは実際の検証作業を表面化します — すべてを検証する読者は何も検証しません。タグをストリップしたり折り畳んだりしないこと。
>
> **非弁護士ユーザーの場合、不確実な日付、閾値、段階的施行条項はインラインではなく confirm-list に。** 「effective February 1, 2026」に `[verify]` タグが付いていると、タグの意味を知らない非弁護士には「effective February 1, 2026」と読まれます。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Who's using this` を読みます。Role が **Non-lawyer** で、日付、期限、段階的施行、閾値、または施行日アサーションが不確実(インラインなら `[verify]` または `[verify-pinpoint]` を伴うもの)である場合、インラインアサーションを「effective date: confirm with counsel」(または「threshold: confirm with counsel」)に置き換え、すべての不確実な項目を最後のギャップ分析セクションに集めます: 「**Things I'm not certain about — ask your attorney to confirm before relying on this:**」、各項目(私が言ったこと、何が不確実か、なぜそれがギャップに重要か)をリスト。弁護士ロールユーザーはインライン `[verify]` の扱いを保持します。

---

## 他のスキルとの統合

**aia-generation から:** AIA は特定のシステムの規制義務をフラグ → 規制が新しいか、カバレッジが不確実な場合、ここにフィードします。

**use case triage から:** 規制トリガーに該当する新しくトリアージされたユースケース → そのユースケースタイプの特定の要件に対してギャップ分析を実行します。

**regulatory-legal プラグインへ(プラグインがインストールされている場合):** このスキルは手動版です。モニタープラグインはフィードを監視し、関連するものが変化したときにこの分析を自動的にトリガーします。

---

## 出力

日付付き markdown ドキュメントとして保存。修復計画表はトラッカーになります — 項目がクローズするにつれてステータスを更新します。

ギャップ分析が「ギャップなし、我々はコンプライアンス」と結論した場合でも、ドキュメントを書きます。あなたが見たという有用な証拠であり、規制が修正されたときの有用なベースラインです。

**依拠する前の Cite チェック。** ここの引用は AI モデルによって生成され、一次ソースに対して検証されていません。任意の引用 — 法令、規制、delegated act、ガイダンス、または判例 — に依拠する前に、精度、最新性、後続履歴のために法務リサーチツール(Westlaw, CourtListener, またはファームのプラットフォーム)に対する検証パスを実行します。提出資料における捏造または誤引用された引用は制裁につながっています。各引用のソースタグ(例: `[EUR-Lex]`, `[web search — verify]`)はどこから来たかを示します。`verify` タグはより高い捏造リスクを持ち、最初にチェックすべきです。

---

## Close with the next-steps decision tree

CLAUDE.md `## Outputs` のデシジョンツリーで終わります。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X, escalate, get more facts, watch and wait, something else)は出発点であり、ロックインではありません。ツリーが出力です; 弁護士が選びます。

## このスキルがしないこと

- 曖昧な規制言語を権威的に解釈しないこと。EU AI Act は特に、まだ解決されていない重要な解釈上の問題を持っています。規制が本当に曖昧な場合: そう述べ、保守的な読み方を述べ、論点が実質的であれば外部弁護士のためにフラグします。
- 規制変更を proactive に追跡しないこと。あなたが変更を指し示したときに実行します。proactive モニタリングのためには、プラグインがインストールされていれば `regulatory-legal` プラグインを参照。
- 修正を実装しないこと。計画します。
- 専門知識が必要なセクター固有の法律相談(ヘルスケア AI、金融サービス・モデルリスク管理など)の代替にならないこと。
