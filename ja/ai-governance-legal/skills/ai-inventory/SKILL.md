---
name: ai-inventory
description: >
  EU AI Act per-system inventory — track each AI system's role (provider,
  deployer, importer, distributor, authorized representative, product
  manufacturer) and risk tier (prohibited, high-risk, limited, minimal,
  GPAI, GPAI+systemic). Role and tier are assessed per system, not per
  company. Use when the user says "ai inventory", "add an ai system",
  "what systems do we have", "classify this ai system", "eu ai act
  register", or "ai system registry".
  EU AI Act のシステム別インベントリ — 各 AI システムのロール(provider / deployer /
  importer / distributor / authorized representative / product manufacturer)と
  リスク階層(prohibited / high-risk / limited / minimal / GPAI / GPAI+systemic)を
  追跡します。ロールと階層は会社ごとではなくシステムごとに評価されます。
  日本語トリガー: AI インベントリ、AI システム追加、AI システム一覧、AI システムを分類、
  EU AI Act 登録簿、AI システム台帳。
argument-hint: "[list | add | edit <id> | classify <id> | show <id>]"
---

# /ai-inventory

## このスキルが実行される条件

ユーザーが EU AI Act の下で AI システムインベントリを管理したいと考えています。このスキルが存在する核心的なアイデア: **ロールと階層は会社ごとではなくシステムごとに評価される。** 1 つの組織は、システム A の *provider*、システム B の *deployer*、システム C の *importer* になり得ます。AI Act の下では、各組み合わせが異なる義務セットを発生させます。インベントリは、それらの評価が見つけられる場所に追跡されるために存在します — 義務自体は会話の中で導出され、表からは導出されません。

## やるべきこと

1. **設定を読む。**
   `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます。
   存在しないか `[PLACEHOLDER]` マーカーが残っている場合、まず
   `/ai-governance-legal-ja:cold-start-interview` にユーザーを誘導します。

2. **インベントリを読む。** インベントリは
   `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/ai-systems.yaml` にあります。
   存在しない場合、最初の `add` が実行されたときに空の `systems:` リストで作成します。

3. **引数で分岐:**

   - 引数なし、または `list` → インベントリ表を表示(下記 **List** 参照)。
   - `add` → **Add** フローを実行。
   - `edit <id>` → 現在のレコードを表示し、何を変更するかを尋ね、1 フィールドを更新、確認、書き込み。
   - `classify <id>` → 既存レコードに対して **分類ウォークスルー** を実行し、role、tier、role_basis、tier_basis を更新。
   - `show <id>` → 完全なレコードを表示。

4. **list 時、ダッシュボードを提供:**
   「フルダッシュボードを見ますか? ステータス / 階層 / EU nexus / 所有者でフィルタリング。お声がけください。」

5. **すべてのアクションを、弁護士の作業へのフックで閉じる。**
   書き込み後、次のように述べます:
   > 記録しました。このシステムの義務を walk through する準備ができたら、お声がけください — 会話の中で行い、AI Act の条項マッピングがあなたの検証を必要とする場所にフラグを立てます。条項マッピングは複雑で変化しているため、表から義務を導出することはしません。

## List フォーマット

コンパクトな表としてレンダリング:

| ID | Name | Owner | Status | EU nexus | Role | Tier | Next review |
|----|------|-------|--------|----------|------|------|-------------|
| sys-001 | Resume screening | HR / Jamie | in_production | yes | deployer | high_risk | 2026-08-01 |
| sys-002 | Email drafting assistant | IT / Priya | in_production | no | deployer | limited | 2026-12-01 |

表の下に、階層別のカウントと「30 日以内にレビュー予定のシステム N 件」の行を表示します。

## Add フロー(インタビュー)

1 フィールドずつ尋ねます(またはペーストを受け取ります)。必須フィールドは `name`、`owner`、`description`、`status`、`eu_nexus` です。それ以外は後回しにできます — 明示的にそう述べます: 「分類には `/ai-governance-legal-ja:ai-inventory classify <id>` で後で戻ってこられます。」

1. **Name.** システムの短いラベル。
2. **Owner.** 日々の責任を負う人物またはチーム。
3. **Description.** 1〜2 文。何をするか、どのデータに対して?
4. **Status.** `planned | in_development | in_production | deprecated`。
5. **EU nexus.** システムが EU/EEA でデプロイされているか、EU/EEA のユーザーに提供されているか、または EU/EEA の人々に影響する出力を生成するために使用されているか? これらのいずれかが真の場合、EU AI Act の分析が適用されます。
6. **分類に進みますか?** 今ウォークスルーを実行するか、スキップして後で戻るかを提供します。

ID を割り当てます: `sys-NNN`、ここで NNN はファイル内の次の整数です。

## 分類ウォークスルー

ウォークスルーは `role`、`role_basis`、`tier`、`tier_basis` を生成します。両方の basis は `[verify against current AI Act text]` でタグ付けされます — スキルがヘッジしているからではなく、条項マッピングが複雑で AI Act がまだ段階的に施行中だからです。弁護士が検証を所有します。

### Step 1: Role

> **このシステムに対して誰が何をするか?**

オプションと判別テスト:

- **Provider** — あなたが開発する(または開発させる)、自身の名前または商標で EU 市場に置く、または使用に供します。
- **Deployer** — 個人的な非専門的使用ではなく、自身の権限の下でそれを使用します。(企業内で最も一般的。)
- **Importer** — EU 外で設立された provider から EU に AI システムを持ち込みます。
- **Distributor** — provider または importer ではないが、EU 市場で AI システムを利用可能にします。
- **Authorized representative** — 非 EU provider を代表して行動し、EU で設立されています。
- **Product manufacturer** — 汎用 AI システム(または別の AI システム)を自身の名前/商標の下で製品に組み込みます。製品の provider として扱われます。

**Dual-role フラグ。** ユーザーがベンダーシステムを実質的に変更する場合(自身のデータでファインチューニング、意図された目的を変更、リブランド)、deployer として開始したとしても、変更されたシステムの **provider** になる可能性があります。設定を超える任意の変更を記述するときにこれをコールアウトします。 `[verify against current AI Act text — Article 25, provider obligations and substantial modification]`

role を書きます。`role_basis` を 1 文で書きます。

### Step 2: Tier

> **システムは何をするか、そしてユースケースは規制カテゴリに該当するか?**

順番にチェック:

**A. Article 5 prohibited practices(禁止プラクティス)。** `[verify against current AI Act text — Article 5]`

要約、決定的なテキストではない:
- 行動を実質的に歪める subliminal または欺瞞的技術
- 行動を実質的に歪める脆弱性の悪用(年齢、障害、社会経済的地位)
- 不利益な扱いにつながる公的機関による social scoring
- 法執行のための公にアクセス可能な空間での real-time remote biometric ID(狭い例外あり)
- 人種、政治的意見、組合員資格、宗教的または哲学的信念、性生活、または性的指向を推論する生体分類
- 職場または教育における感情認識(医療および安全例外)
- インターネットまたは CCTV からの顔画像データベース・スクレイピング
- 性格特性のみに基づく予測的取り締まり

マッチした場合 → tier は `prohibited`。ユースケースを stop としてフラグし、ガバナンスチームの禁止プラクティス・ワークフローにルーティングします。

**B. Annex III high-risk areas(高リスク領域)。** `[verify against current AI Act text — Annex III]`

要約:
1. 生体識別と分類
2. 重要インフラ(デジタルインフラ、道路交通、水・ガス・暖房・電気の供給)
3. 教育と職業訓練(アクセス、評価、プロクタリング、禁止行動の監視)
4. 雇用、労働者管理、自営アクセス — 採用、選考、昇進、解雇、タスク配分、監視、パフォーマンス
5. 必須の民間および公共サービス(公的給付、個人向け信用スコアリング、生命/健康保険のリスク評価と価格設定、緊急派遣)
6. 法執行(リスク評価、ポリグラフ、ディープフェイク検出、証拠の信頼性、プロファイリング)
7. 移民、亡命、国境管理(リスク評価、旅券確認、申請審査)
8. 司法行政と民主プロセス(調査と解釈、選挙への影響)

マッチした場合 → tier は `high_risk`。Annex III の領域とサブセクションを注記します。

**C. GPAI(汎用 AI モデル)。** `[verify against current AI Act text — Article 51 and surrounding]`

- **GPAI:** 広範なデータで大規模に訓練され、汎用性のために設計され、幅広い異なるタスクをコンピテントに実行できるモデル。
- **GPAI + systemic risk:** 累積計算量 > 10^25 FLOPs、または Commission による指定。

**D. Limited risk(限定リスク)。** 自然人と相互作用するチャットボット、ディープフェイク、Article 5 範囲外の感情認識および生体分類システム — 透明性義務が適用されます。

**E. Minimal risk(最小リスク)。** それ以外すべて。

tier を書きます。`tier_basis` を 1 文で書き、マッチした条項または Annex エントリを引用し、`[verify against current AI Act text]` でタグ付けします。

### Step 3: 推奨事項

3 つの次のステップを提供:
1. 「このシステムの義務を walk through しましょうか? 会話の中で行います — 表からは導出しません。」
2. 「`/ai-governance-legal-ja:aia-generation` を実行して完全な影響評価を生成しますか?」
3. 「次回レビュー日を設定しますか? インベントリに追加します。」

## レコード形式

```yaml
systems:
  - id: sys-001
    name: "Resume screening tool"
    owner: "HR / Jamie"
    description: "Filters inbound CVs against job criteria"
    status: in_production          # planned | in_development | in_production | deprecated
    eu_nexus: true                 # deployed, offered, or affects people in the EU/EEA
    role: deployer                 # provider | deployer | importer | distributor | authorized_rep | product_manufacturer
    role_basis: "We license from VendorX and deploy internally [verify against current AI Act text]"
    tier: high_risk                # prohibited | high_risk | limited | minimal | gpai | gpai_systemic
    tier_basis: "Annex III(4)(a) — employment, recruitment selection [verify against current AI Act text]"
    obligations_assessed: false
    obligations_note: "To assess: as deployer of a high-risk system — human oversight, input data quality, monitoring, record-keeping, informing workers, FRIA if public body/service — see Article 26 [verify against current AI Act text]"
    next_review: "2026-08-01"
    review_trigger: "on substantial modification or annually"
    created: "2026-05-11"
    updated: "2026-05-11"
```

## なぜこのスキルは義務を自動導出しないか

インベントリは role、tier、各々の basis を格納します。ハードコードされた role × tier → obligations 表は含みません。

ユーザーが「システム X についての義務は何ですか?」と尋ねた場合、スキルは **会話の中で** 分析を行い、`[verify]` でタグ付けし、正式な影響評価が必要な場合は `/ai-governance-legal-ja:aia-generation` にルーティングします。

これは意図的なものです:
- 条項マッピングは複雑で、AI Act は 2027 年まで段階的に施行中。
- Confident-and-wrong なコンプライアンス義務は取締役会メモに紛れ込みます。
- インベントリは弁護士のためのレジストリです。弁護士が義務分析を所有します。

## ガードレール

- **暗黙のうちに分類しないこと。** 分類ウォークスルーは可視でなければなりません — システムの記述から自動分類しないこと。
- **`[verify]` タグは残します。** ヘッジではなく — 要点です。出力でストリップしないこと。
- **実質的変更をフラグ付けします。** システムが設定を超えて変更されたときはいつでも、ユーザーに `/ai-inventory classify` を再実行するよう促します — 変更は role を変える可能性があります。
- **表から義務を宣言しないこと。** 尋ねられた場合、会話の中で分析を行い、正式な記録が必要なものは `/aia-generation` にルーティングします。
