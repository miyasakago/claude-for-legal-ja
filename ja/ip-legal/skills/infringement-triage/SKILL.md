---
name: infringement-triage
description: >
  Infringement triage across trademark, copyright, patent, and trade secret —
  a flag list with the factors cutting each way, not a finding. Use when
  assessing whether someone is infringing your IP or whether you might be
  infringing theirs, when a knockoff or copycat surfaces, or when deciding
  whether a matter is worth pursuing and how.
  商標、著作権、特許、営業秘密にわたる侵害トリアージ — 両方向に切れる要因のフラグ
  リスト、結論ではない。誰かがあなたの IP を侵害しているか、またはあなたが彼らの
  ものを侵害しているかもしれないかを評価するとき、ノックオフまたは copycat が
  表面化したとき、または案件が追求の価値があるかをどう判断するかを決定するときに
  使用。
  日本語トリガー: 「侵害トリアージ」「IP 侵害」「侵害判定」「ノックオフ調査」「侵害リスク評価」。
argument-hint: "[事実とどの権利を記述 — または事実のみ、こちらからどの権利を尋ねる]"
---

# /infringement-triage

**これはトリアージで、侵害または非侵害の発見ではありません.** 侵害分析は事実集約的で法的に複雑。トリアージに基づき行動 — 警告状送付、停止拒否、提訴、または提訴しない決定 — を弁護士レビューなしで行うのは、企業が費用償還、Rule 11 制裁、確認判決アクション、(特許の場合)三倍損害賠償の wrong side になる仕方です。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読む。`[PLACEHOLDER]` を含むなら停止して `/ip-legal-ja:cold-start-interview` に誘導。
2. 下記のワークフローに従う。
3. どの権利が問題かを尋ねる — 商標 / 著作権 / 特許 / 営業秘密 / 混合。混合なら、それぞれを別々に実行; ブレンドしない。
4. 共通インテーク(当事者スタンス — senior または被告、法域、タイミング、証拠)を実行。
5. モード固有の要因を歩く:
   - **商標** — 巡回区の混同テスト + dilution(有名なら) + false advertising(比較クレームなら)。
   - **著作権** — 所有権 + 登録 + アクセス + 実質的類似性 + フェアユース + DMCA セーフハーバー(該当時)。
   - **特許** — クレームチャート初回パス(`fto-triage` 出力構造にルート); literal + DOE; 間接 + divided; 検討すべき無効性防御。
   - **営業秘密** — 秘密性 + 合理的措置 + 不正流用; preemption + reverse-engineering フラグ。
6. 方向付きのフラグリストを生成 — senior 当事者に向けて何が切れる、被告に向けて何が切れる、何が混合。決して結論しない。
7. トリアージ・メモを案件フォルダまたはプラクティス出力フォルダに書く。Role に応じた work-product ヘッダーを適用。
8. 推奨される次のステップ、Role が非弁護士なら非弁護士ゲート、プラクティス・スタンスが主張をサポートするなら `/ip-legal-ja:cease-desist` 経由の C&D または `/ip-legal-ja:takedown` 経由のテイクダウンのドラフト提供で終了。自動的にドラフトしない。

本スキルは決して結論しない。不確実なら、フラグ — 弁護士が決定。

## 例

```
/ip-legal-ja:infringement-triage "competitor launched a tool called APEXSEED in class 9 — we have APEXLEAF registered in class 9; likely confusion?"
```

```
/ip-legal-ja:infringement-triage "former engineer took notes on our model architecture to a competitor — possible trade secret?"
```

```
/ip-legal-ja:infringement-triage
```

(スキルがどの権利と事実を尋ねます。)

---

## THIS IS A TRIAGE, NOT A FINDING

**プラグイン中最大の声のガードレール。毎回の出力の上部でこれを述べてください。落とさない、和らげない.**

> **This is a triage, not a finding of infringement or non-infringement.**
> 侵害分析は事実集約的で法的に複雑。トリアージは要因を識別し、最も重要なものを
> フラグする; 結論しない。何かが侵害するまたはしないという結論は、事実、クレーム
> または権利スコープ、関連法域の法、likely な防御に関する弁護士の判断を要する
> 法的意見。トリアージに基づき行動 — 警告状送付、停止拒否、提訴、または提訴しない
> 決定 — を弁護士レビューなしで行うのは、企業が費用償還、Rule 11 制裁、確認
> 判決アクション、(特許の場合)三倍損害賠償の wrong side になる仕方です。

コンフリクトを under-call するのは一方通行のドア — C&D が送られず、マークが市場で generic になる; クレームが追求されず、出訴期限が走る; コピーされた著作権付き作品がサイトに残る。Over-call は両方向のドア — 弁護士が絞る。両方向のドア側に留まる。

---

## 案件コンテキスト

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(インハウスのデフォルト)なら、この段落の残りをスキップ — スキルはプラクティスレベル・コンテキストを使い、案件機構は不可視。有効でアクティブ案件がなければ問う: 「どの案件向け? `/ip-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください」。アクティブ案件の `matter.md` を案件固有コンテキストとオーバーライド用に読む。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/` に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まないこと。

侵害トリアージはしばしば警告状ドラフトまたはテイクダウン・ルーティングに繋がります。アクティブでなく、プラクティスがプライベートなら案件をオープン — トリアージ、C&D、任意の下流応答は 1 つのワークスペースに属する。

---

## まずプラクティスプロファイルをロード

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読む。取得:

- `## Who's using this` から **Role**。
- `## Enforcement posture` から **Enforcement posture** — トリアージ出力は、述べられたスタンス(aggressive / measured / conservative)と関連レタータイプの名指された承認者と consistent なルーティング提案で終わるべき。
- `## IP practice profile` から **Registered in / enforce where** — デフォルトでどの巡回区 / 法域テストを適用するかを決定。
- `## Available integrations` から **Integrations** — CourtListener、Solve Intelligence 各々がトリアージが判例法、過去の裁定、または先行技術に引用できるかに影響。
- `## Decision posture on subjective legal calls` から **Decision posture** — 本スキルは主観的閾値で決して結論しない。

設定が `[PLACEHOLDER]` を持つなら、このバウンスを表面化:

> プラクティスプロファイルをまだ設定していないようです — これがスタンス、法域、承認チェーンをあなたのプラクティスに合わせる方法です。
>
> **2 つの選択肢:**
> - `/ip-legal-ja:cold-start-interview`(2 分)を実行してプロファイルを設定、その後 YOUR プラクティスに合わせてこれを実行。
> - **"provisional"** と言えば、これを汎用デフォルト — 米国法域、middle リスク選好、弁護士 Role、プレイブックなし — で実行し、すべての出力を `[PROVISIONAL — configure your profile for tailored output]` でタグするので、コミット前に私の動作が分かります。

### Provisional モード

ユーザーが「provisional」と言ったら、これらの汎用デフォルトで侵害トリアージを通常通り実行: middle リスク選好、弁護士 Role、米国法域、プレイブックなし(立場リストに対するマッチではなく完全分析を実行)。レビューアーノートとすべての発見ブロックを `[PROVISIONAL]` でタグ。出力の終わりに追加:

> 「それはデフォルト前提に対する汎用実行でした。YOUR プラクティスに合わせた出力 — プレイブック、法域、リスク選好 — のために `/ip-legal-ja:cold-start-interview` を実行してください。2 分」。

---

## モード選択

何より前に冒頭で尋ねる:

> どの権利をトリアージしますか?
>
> 1. **商標** — 混同、dilution、または false advertising
> 2. **著作権** — 実質的類似性、フェアユース、DMCA セーフハーバー
> 3. **特許** — クレームチャート初回パス、literal 読み + doctrine of equivalents
> 4. **営業秘密** — 秘密性、合理的措置、不正流用
> 5. **混合 / 不確実** — 事実を記述、私が選びます

ユーザーが「不確実」を選んだら、整理を助ける。同じ事実が複数の権利を含みうる(例: 競合製品がロゴを使用 — 商標; かつ製品が我々のもののコピーに近い — 可能性のある特許、パッケージの著作権、可能性のあるトレードドレス; かつ元従業員がローンチした — 営業秘密)。

**複数の権利が関与するなら、各々別々にトリアージを実行.**
一緒に潰さないこと。各権利は異なる要因、異なる法域ルール、異なる救済を持つ。

---

## インテーク(全モード共通)

> 要因を歩く前に:
>
> 1. **スタンス.** あなたは潜在的に senior な当事者(彼らがあなたのを取っている)、または潜在的に被告の当事者(我々が見られている)? 要因は対称だが、出力は異なる — 「mine's being copied」トリアージは主張レターへルート、「we might be exposed」トリアージはリスクメモへルート。
> 2. **法域.** どの国 / 巡回区 / 裁判所? 指定なければ US 連邦デフォルト。外国法が適用しうるならフラグ。
> 3. **タイミング.** 出訴期限または laches クロックが走っているか?
> 4. **どの証拠 / エビデンス / ソース文書がありますか?** スクリーンショット、URL、パッケージ写真、コード抜粋、元従業員契約。

要因を歩く前に回答を待つ。

---

## 商標モード

### 混同

適用される巡回区の多要因テストを使用。テストを引用(du Pont /
Polaroid / Sleekcraft / その他 — 判例引用とピック・ロジックは `clearance` スキル参照)。各要因を歩いて何が両方向に切れるかをフラグ。

- **マークの類似性** — 視覚 / 音 / 意味 / 商業的印象。
- **商品または役務の類似性** — expected-source テスト、同一性ではない。
- **取引チャネル.**
- **カスタマーの洗練度.**
- **senior マークの強さ** — fanciful / arbitrary / suggestive / secondary meaning 付き descriptive / generic。
- **意図** — コピー、knock-off トレードドレス、near-miss マークの証拠。
- **実際の混同** — 任意の証拠(調査、誤誘導された問い合わせ、ソーシャル)。
- **拡張のおそれ / bridge-the-gap** — ゾーンが商業的にオーバーラップするか。

### Dilution

連邦 TDRA(15 U.S.C. § 1125(c))と適用される州制定法を適用。

- **有名性閾値.** Senior マークは一般消費者一般に有名でなければならない — niche-famous マークでは不十分。*Starbucks Corp. v. Wolfe's
  Borough Coffee, Inc.*, 588 F.3d 97 (2d Cir. 2009) は代表的。
- **Blurring vs tarnishment.** Blurring = distinctiveness 害; tarnishment
  = reputation 害。
- **防御** — 比較広告、ニュース報道、フェアユース、非商業的使用。

Senior マークが plainly に国家的に有名でなければ、dilution を stretch としてフラグ。

### False advertising / 比較クレーム

トリアージが競合の比較広告または製品属性に関するクレームによって駆動される場合:

- 物質性、虚偽性または誤誘導性、欺瞞、商業的言論、傷害要素について Lanham Act § 43(a) / 15 U.S.C. § 1125(a) を適用。
- 声明が literally false、implicitly false、または puffery かをフラグ。Puffery は actionable でない。
- 主張者が持つまたは必要とする実証証拠。

### 出力

要因テーブル; 何が両方向に切れる; 「発見ではない」結論行。プラクティスプロファイルのエンフォースメント・スタンスに対するルーティング提案で終わる。

---

## 著作権モード

### 所有権

主張者は所有者(または standing を持つ独占ライセンシー)か? 職務著作物の問題; 共同著作; 譲渡; termination 権がすべてフラグ。

### 登録

17 U.S.C. § 411 は登録(または preregistration)を米国連邦裁判所で侵害アクションを提起する前提条件として要求。*Fourth Estate Public
Benefit Corp. v. Wall-Street.com, LLC*, 586 U.S. 296 (2019) — 登録は実際に発行されたという意味で、出願しただけではない。登録ステータスをフラグ; 登録されていないなら、提訴の practical なバーをフラグ。

### アクセス + 実質的類似性

コピーを証明する 2 つのパス:

- **アクセス + probative 類似性** — 被告がアクセスを持ち、作品がコピーに probative な特徴を共有。
- **顕著な類似性** — アクセスの証明がなくても、類似性が独立創作が unlikely なほど顕著。

実質的類似性については、巡回区のテストを適用(第二巡回区の ordinary-observer; 第九巡回区の *Krofft* と *Swirsky* 下の extrinsic / intrinsic; 第四 / 第七 / 第十一巡回区のバリエーション)。どのテストが適用するかをフラグ。

### フェアユース

17 U.S.C. § 107 の 4 要因、全体として分析:

1. 使用の目的と性質(transformativeness; 商業 vs 非商業)。
2. 著作権付き作品の性質(事実 / 機能的 vs 創造的)。
3. 使用された部分の量と実質性。
4. オリジナルの市場への影響。

最近のタッチストーン: *Google LLC v. Oracle America, Inc.*, 593 U.S. 1 (2021);
*Andy Warhol Found. for the Visual Arts, Inc. v. Goldsmith*, 598 U.S. 508
(2023)。Transformativeness 分析を慎重にフラグ — *Warhol* は transformative 使用のスコープを狭め、依然下級裁判所により適用されています。

### DMCA セーフハーバー

17 U.S.C. § 512。被告がユーザー・コンテンツをホストするサービスプロバイダーなら、§ 512(c) が適用するかをフラグ: 指定エージェント、通知・テイクダウン手続、actual または red-flag 知識なし、プロバイダーが制御できる侵害に attributable な経済的利益なし、有効通知での expeditious テイクダウン。Repeat-infringer ポリシーが必要。セーフハーバーはサービスプロバイダー自身による直接侵害をカバーしない。

### 出力

要因フラグ; 「トリアージは結論しない」付きフェアユース・バランス; 所有権 / 登録 / セーフハーバー閾値注記。スタンス別ルーティング。

---

## 特許モード

**詳細フレームワークは `/ip-legal-ja:fto-triage` にルート.** このモードは FTO スキルの鏡像 — 同じクレームチャート、同じ doctrine-of-equivalents フラグ、同じ all-elements rule — 自身のものではなく被告製品に適用。

### 意匠特許(D 番号) — ワークフロー前にブランチ

**主張された特許の登録番号を FIRST にチェック.** `D`、`RE`、または `PP` 接頭辞(例: `D712,345`)を持つなら、実用特許ではなく、下記のワークフローは適用しない。接頭辞別にブランチ:

- **`D` 接頭辞 — 意匠特許(35 U.S.C. §171).** 異なるテスト、異なるクレーム構造、異なる損害賠償。クレームチャートを構築しない、doctrine of equivalents を実行しない、要素単位のマッピングを行わない。意匠特許は図によって定義される単一クレームを持つ; 図を実用クレーム要素リストのようにチャートするのは間違ったドクトリン。
- **`RE` 接頭辞 — 再発行特許.** 再発行した実用特許として扱うが、再発行固有の防御(§252 下の介在権、recapture rule、original-patent 要件)をフラグ。
- **`PP` 接頭辞 — 植物特許.** 別レジーム(35 U.S.C. §161)。Asexually 再生産された植物品種。植物特許カウンセルにルート; 本スキルは植物特許を分析しない。

**意匠特許侵害テスト — ordinary observer.** *Egyptian Goddess,
Inc. v. Swisa, Inc.*, 543 F.3d 665 (Fed. Cir. 2008)(en banc)。質問は、ordinary observer が **先行技術デザインに familiar** で、被告デザインを特許デザインと同じと欺かれるか。**全体的な装飾的外観** を比較、個別要素ではない。被告製品は、特許デザインを先行技術と区別する **新規性** を appropriate しなければならない(「point of novelty」は別テストではなく、ordinary-observer テスト内の guidepost として survives)。

**機能的 vs 装飾的フィルター.** 意匠特許は装飾的特徴のみを保護; 機能的特徴は保護されない。被告類似性が機能で決まる特徴にあるなら、オーバーラップが特許スコープ外にあるかもしれないとフラグ。

**§289 total-profit damages フラグ.** 35 U.S.C. §289 下の意匠特許損害賠償は侵害者の **「article of manufacture」の total profits**、製品全体またはコンポーネントでありうる。*Samsung Electronics Co. v.
Apple Inc.*, 580 U.S. 53 (2016)。これは実用特許の合理的ロイヤルティ / 失われた利益とは別の分析で、specialist 業務 — 計算しないこと。

**トレードドレス・クロスフラグ.** 同じ装飾的形状の事実は通常、Lanham Act §43(a)(15 U.S.C. §1125(a))下の **トレードドレス** 問題でもある。製品構成トレードドレスは **secondary meaning**(*Wal-Mart
Stores, Inc. v. Samara Bros., Inc.*, 529 U.S. 205 (2000))を要し、**非機能** でなければならない(*TrafFix Devices, Inc. v. Marketing Displays, Inc.*,
532 U.S. 23 (2001))。トレードドレスを並列トラックとしてフラグ; テストは異なるが証拠はオーバーラップ。

### 意匠特許トリアージ — 出力

特許図と被告製品を直接見られないため、意匠特許トリアージはほとんど、素材のリクエストと分析のためのフレーム:

- **図を求める.** 「特許図と被告製品を見ずに ordinary-observer テストを実行できません。貼り付けまたは添付: (a) 特許図(すべての図、broken-line disclaimer 含む)、(b) 比較可能な角度からの被告製品写真、(c) 知っている任意の先行技術デザイン」。
- **先行技術ランドスケープ.** Ordinary observer は *比較* テスト — オブザーバーは「先行技術に familiar」、特許デザインのスコープは先行技術フィールドが crowded すると狭くなる。何の先行技術が知られているかと何が欠けているかをフラグ。
- **機能的 vs 装飾的分析.** 特徴を歩いて、機能的に見える(したがって unprotected)もの vs 装飾的なものをフラグ。
- **Broken lines.** 意匠特許は主張された特徴に solid 線、unclaimed な環境コンテキストに broken 線を使う。alleged コピーが主張(solid 線)領域または unclaimed(broken 線)領域にあるかをフラグ。
- 上記の **§289 損害賠償フラグ**。
- 上記の **トレードドレス・クロスフラグ**。

**初回パス・トリアージを超えるものは意匠特許スペシャリストにルート.**
意匠特許訴訟は subspecialty(Perkins Coie、Sterne Kessler、
Desmarais、Kirkland の意匠チーム、Gibson Dunn の意匠グループが代表; 出発点としてプラクティスプロファイルの IP 訴訟 OC を使う)。本スキルは問題をフラグ; 侵害を評価しない。

### 実用特許ワークフロー

このモードの残りは、主張された特許が **実用特許** であることを仮定
(`D`/`RE`/`PP` 接頭辞なし)。上記の D 番号ブランチが適用するなら、ここで停止。

> **特許システムは法域別に異なる.** 米国クレームチャート(all-elements rule、doctrine of equivalents、prosecution history estoppel、§284/§289 損害賠償)は他のシステムには移転しない:
> - **ドイツ:** 実用新案(Gebrauchsmuster)、DOE の Schneidmesser/Kunststoffrohrteil 質問、二分化された有効性/侵害手続。
> - **中国:** 実用新案(shiyong xinxing)、CNIPA 審査、異なるクレーム解釈。
> - **日本:** 実用新案、JPO 審査、より狭い DOE。
> - **欧州(統一特許裁判所):** 2023 年時点の UPC 手続。
>
> 米国外法域がスコープにあるとき: 「This analysis uses the US claim-charting framework. A product manufactured in China and sold in the EU needs CNIPA and EP analysis, not a US claim chart. I can flag the issues a US analysis surfaces, but the infringement and validity calls require [jurisdiction]-specific review.」

### ワークフロー

- 被告製品 / プロセス / 方法 — 技術的に詳細に記述。
- 問題の識別された特許。
- 各独立クレームのクレームチャート: 被告製品への要素単位マッピング。
- Literal 侵害が最初。DOE はフラグ。
- 間接(induced、contributory)と divided 侵害はフラグ。
- **検討すべき無効性防御** — anticipation(§ 102)、進歩性
  (§ 103)、§ 112 written-description / enablement / definiteness、§ 101
  主題適格性(*Alice* / *Mayo*)。既知の IPR または PGR 結果、既知の先行技術、既知の出願履歴。各々をフラグ; 意見を述べない。
- **エンフォース不能性防御** — inequitable conduct フラグ、出願 laches フラグ、譲渡人 / ライセンシー estoppel フラグ。各々が弁護士のみ。
- **損害賠償スタンス** — 失われた利益 vs 合理的ロイヤルティ(Georgia-Pacific factors)、マーキング、訴前通知、willfulness(本トリアージを読むことが willfulness にファクターしうる — FTO スキルの willfulness 注記参照)。

### 出力

クレームチャート。要素フラグ。防御フラグ。特許カウンセルへのルーティング。完全な出力構造は `fto-triage` スキル参照 — 侵害トリアージの特許モードは「自身の製品」を「被告製品」に置き換えた同じフォーマットを使う。

### 完全クレームチャートへのハンドオフ

侵害または無効性主張に適した詳細な要素単位のクレームチャートには、`/litigation-legal:claim-chart` を実行。本トリアージのクレームチャートは、最も強い・最も弱いマッピングを識別する初回パス; 訴訟クレームチャートは pin cite、クレーム解釈フラグ、従属クレーム、主張が要求する検証ワークフローで完全チャートを構築。

---

## 営業秘密モード

### 秘密だったか?

連邦目的では Defend Trade Secrets Act(18 U.S.C. § 1836 et seq.)、適用される州 UTSA(またはニューヨーク / マサチューセッツ / 他の非 UTSA 法域では州コモンロー・テスト)を適用。フラグ:

- **一般的に知られていない** — 公衆または開示から経済的価値を得られる業界の他者に。
- **秘密性からの経済的価値** — 一般的に知られていないことから派生する実際の or 潜在的独立経済的価値。
- **組み合わせとコンパイル** — 公開要素の組み合わせは営業秘密でありうる(*Altavion v. Konica Minolta*、および Restatement view)。

### 合理的措置

- 従業員、コントラクター、相手方との NDA。スコープ、サイン済み、エンフォース済み?
- アクセス・コントロール — 技術的(role ベース)、物理的(ドア、バッジ)、組織的(need-to-know)。
- マーキング — 文書、コード、データの秘密性凡例。
- Exit インタビュー / termination 時の素材返却。
- 営業秘密ポリシー / トレーニング。

何が in place で何が欠けているかをフラグ。*合理的* は事実固有; トリアージは措置が合理的だったかを決定しない — リスト化する。

### 不正流用

不適切な手段による取得、または duty 違反での開示 / 使用。
不適切な手段にはスチール、賄賂、誤表現、秘密性を維持する duty の違反または違反誘発、または(電子的その他)スパイ活動が含まれる。18 U.S.C. § 1839(6)。

- **元従業員の事実パターン:** 新雇用主、オーバーラップする業務、退職タイミング、取得された文書(返却された?)、アクセス・ログ、リクルーティング・チャネル、譲渡と発明譲渡契約。
- **不注意な開示:** 開示が duty を持つ人によりなされたか? 受領者は違反を知っていたまたは知る理由があったか?
- **リバースエンジニアリング** — 手段が合法だったら防御。事実上リバースエンジニアリングが plausible かをフラグ。

### Preemption

州 tort クレーム(unfair competition、conversion、breach of confidence)が同じ事実に対処する UTSA により preempted されうる場所では、preemption をフラグ。一部法域は契約クレームを保存; 他は同じ事実に対処するほとんどの tort クレームを preempt。

### 出力

3 つのフラグ・グループ — 秘密性、措置、不正流用 — 各々で何が両方向に切れるか。スタンス別ルーティング。

---

## 出力フォーマット(全モード)

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` `## Outputs` から work-product ヘッダーを付与。

```markdown
[WORK-PRODUCT HEADER]

# Infringement Triage — [Trademark | Copyright | Patent | Trade Secret] (NOT A FINDING)

**This is a triage, not a finding of infringement or non-infringement.** トリアージ
は要因を識別し最も重要なものをフラグ; 結論しない。結論は、事実、権利スコープ、
法域、防御に関する弁護士の判断を要する。トリアージに基づき弁護士レビューなしで
行動するのは、企業が費用償還、Rule 11 制裁、確認判決アクション、enhanced damages
の wrong side になる仕方です。

**Triage result:** [GREEN / YELLOW / RED — 1 文でなぜ]

## スタンスとスコープ

- **Party posture:** [senior / 被告]
- **Right at issue:** [trademark / copyright / patent / trade secret]
- **Jurisdiction:** [US federal — 特定巡回区 / 州 / 外国]
- **Legal framework applied:** [規定するテストと制定法を引用]
- **Statute of limitations / laches posture:** [クロックステータス]
- **Exhibits / evidence reviewed:** [list]

## 要因分析

[モード固有の要因テーブル — 混同要因 / フェアユース要因 / クレームチャート
/ 営業秘密要素。各要因はフラグと方向を持つ。これは
フラグリストで、verdict ではない。]

## 防御と閾値

[モード固有: dilution 有名性閾値 / 登録前提条件 /
§ 512 セーフハーバー / 無効性 / inequitable conduct / preemption /
reverse-engineering / 同意 / ライセンス / laches / 出訴期限。
各々をフラグ。]

## 何が両方向に切れる — サマリー

| 要因 | フラグ | 方向(senior / 被告 / 混合) |
|---|---|---|
| [要因 1] | [note] | [方向] |

**Conclusion:** *本スキルは結論しない.* 行動前に弁護士判断が必要。
[方向] に切れる要因は [簡潔なサマリー]; [方向] に切れる要因は [簡潔なサマリー]。

## 推奨される次のステップ

- [カウンセルからの正式な意見 / プラクティスプロファイルで名指された IP OC へルート]
- [証拠保全とホールド — 訴訟クロックが走っているなら]
- [判断前の必要な事実展開 — 例: アクセス・ログ、出願履歴、市場調査、調査証拠]
- [`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md`
  `## Enforcement posture` 別ルーティング、主張するスタンスなら]

## 引用検証

ここで引用された各判例、制定法、登録番号、クレーム引用、証拠は、依拠する前に
権威ソースに対して検証する必要があります。
法域テストは巡回区別に異なり、時間とともに変わる — 現行の規定典拠を確認。
```

---

## 非弁護士ゲート

出力を発行する前に、`## Who's using this` を読む。Role が非弁護士なら:

> この出力は research トリアージで、法的助言ではありません。このトリアージ
> 単独に基づき C&D 送付、停止しない決定、提訴、または「これはフェアユース」に
> 依拠することは、根拠のない主張に対する Rule 11 制裁、脅迫レターに対する
> 確認判決エクスポージャー、特許側の三倍損害賠償、unfair-competition 訴訟での
> 費用償還を含む法的帰結を伴います。弁護士が、あなたが動く前に評価する必要が
> あります。
>
> 弁護士に持参するブリーフ:
>
> [1 ページ・サマリーを生成: 問題の権利、スタンス、事実と証拠、表面化した要因、
> フラグされた防御、弁護士に聞く 3 つの質問]
>
> あなたの法域で認可された弁護士、ソリシター、バリスター、他の認可された
> 法専門家を見つけるなら: あなたの専門職規制当局のリファラル・サービスが
> 出発点(米国の州弁護士会、英国イングランド&ウェールズの SRA/Bar Standards
> Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの
> 法曹会、またはあなたの法域の同等機関)。米国特許には、弁護士は USPTO の前で
> 登録されている必要がある; 他の法域では、関連特許庁登記簿を使用。商標には、
> INTA が世界中の実務家ディレクトリを維持。

ブリーフと一緒にトリアージを配信。

---

## 出力場所

案件ワークスペースが有効でアクティブ案件があるなら、
`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/outputs/infringe-<mode>-<subject-slug>-YYYY-MM-DD.md`
に書く。さもなくば
`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/outputs/infringe-<mode>-<subject-slug>-YYYY-MM-DD.md`
に書き、パスを表面化。

案件がアクティブなら、案件の `history.md` に 1 行エントリーを追加。

---

## エンフォースメント・スキルへのハンドオフ

トリアージ出力が主張を指し、プラクティスプロファイルのスタンスがそれをサポートするなら、提供:

> これに警告状をドラフトしますか? `/ip-legal-ja:cease-desist` を実行。
> 本トリアージのフラグリストを事実根拠として使い、プラクティスプロファイルの
> 承認チェーンを適用 — レターは承認者がサインオフするまでどこにも行きません。

または、モードが著作権で被告がホストされたコンテンツなら:

> DMCA テイクダウンを準備しますか? `/ip-legal-ja:takedown` を実行。

トリアージから自動的にレターをドラフトしないこと。主張する判断は承認者のもので、トリアージのものではない。

---

## next-steps decision tree で締めくくる

CLAUDE.md `## Outputs` の next-steps decision tree で締めくくる。本スキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト分岐(draft the X、escalate、get more facts、watch and wait、something else)は出発点で、ロックインではない。ツリーが出力で、弁護士が選びます。

## このスキルが行わないこと

- **侵害または非侵害を結論する.** 決して。最大の声のガードレール。
- **調査証拠、損害賠償専門家、またはクレーム解釈を代替する.**
- **トリアージの法域スコープ外の法域固有防御を評価する.** 事実が境界を越えるなら、外国法分析が必要とフラグ。
- **フェアユースを法律問題として決定する.** フェアユースは事実集約的で、弁護士、最終的には裁判所のため留保。
- **C&D、テイクダウン、または complaint をドラフトする.** それらは別スキル(`/ip-legal-ja:cease-desist`、`/ip-legal-ja:takedown`)で、プラクティスプロファイルの承認チェーンにゲートされる。
- **出力を相手方に引用する.** ヘッダーが適用されるなら特権付き。

---

## トーン

要因単位、フラグ単位。Hedging プロースなし。冒頭のガードレールがスコープ作業を行い、分析が分析を行う。弁護士は、どの要因がフラグされ、どの防御が適用し、主張または stand down するために次に何をする必要があるかを正確に知って出力を去るべき。
