---
name: claim-chart
description: 要素チャートの構築またはレビュー — 特許クレームチャート(侵害、無効、レビュー)または任意の請求原因または防御のための民事要素チャート — すべてのセルが pin-cited されギャップ検出が優先出力。Use when the user asks for a claim chart, element chart, proof chart, infringement or invalidity contention, element-by-element mapping, or asks "what are we missing to prove [claim]". 日本語トリガー: クレームチャート、要素チャート、侵害論証、要素ごとマッピング
argument-hint: '[--patent | --civil] [--infringement | --invalidity | --review] [--claim <n>] [--count <name>] [--target <slug>]'
---

# /claim-chart

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → ロール、成果物ヘッダー、意思決定ポスチャ、文書ストレージ。
2. 案件ワークスペースが有効化されていれば、アクティブ案件を確認または選択;`matter.md` をロード(サイド、法域、フェーズ、セオリー、訴答)。
3. 以下のワークフローと参照に従う。
4. モード選択:
   - `--patent` → 特許クレームチャート。特許番号と少なくとも 1 つの主張クレームが必要。サブモード: `--infringement`、`--invalidity`、`--review`。
   - `--civil` → 民事要素チャート。請求原因(または防御)とサイドが必要。
   - フラグなし → ユーザーに尋ねる。
5. 民事モード: スキルディレクトリの `references/element-templates.md` で baseline 要素リストを参照。マッピング前に支配的なパターン指示または制定法をユーザーと確認。
6. 特許モード: 主張クレームを要素に解析、construction のために争いのある用語をフラグ、任意の Markman 命令(*Markman v. Westview Instruments*, 517 U.S. 370 (1996) / 特許クレーム解釈手続)を適用。
7. ターゲット(疑似製品 / 先行技術 / 証拠コーパス / レビュー対象のチャート)に対して要素をマップ。すべてのセルが pin-cited。`=`、`+`、`-`、`@`、tab、または CR で始まる任意のセル値を書く前にアポストロフィプレフィックス中和を適用。
8. ギャップリスト(民事)または needs-evidence リスト(特許)を生成 — 優先出力。
9. ユーザー好みに応じてマークダウン、CSV(値 + `_sources` コンパニオン)、Excel または Sheets を書き出し。すべての出力に成果物ヘッダー。
10. 案件がアクティブなら案件の `claim-charts/` フォルダ;それ以外はプラクティスレベル `claim-charts/` フォルダに書き込み。案件がアクティブなら `history.md` に 1 行エントリを追記。
11. サマリー readout を返す: クレーム、ターゲット、法域、フェーズ、状態別要素数、ギャップリスト、ファイルパス、すべてのセルがリードであるリマインダー。

---

# Claim Chart

## ディスクローズ文書使用制限

訴訟文書セットで作業を開始する前に尋ねる: 「これらの文書のいずれかが法的手続きでのディスクローズまたはディスカバリーを通じて取得されましたか?」 Yes なら:

- **イングランド&ウェールズ(CPR 31.22):** ディスクローズを通じて取得した文書には、暗黙の約束が適用される — 裁判所が許可を与える、ディスクローズする当事者が同意する、または文書が公開法廷で読まれた場合を除き、ディスクローズされた手続きの目的のためにのみ使用できる。許可なしに別の案件、別の請求、または商業目的のために使うことは contempt です。
- **米国:** 保護命令と Rule 26(c) が類似の制限を課す可能性。命令を確認。
- **その他の法域:** 類似の制限が一般的に適用。ローカルルールを確認。

確認: 「この使用はディスクローズされた手続き内、または許可 / 同意あり、または文書は現在公開」。確認できなければフラグ: 「⚠️ ディスクローズ文書には使用制限がある可能性。進む前にこの使用が許可されていることを確認。」

## チャートはドラフトであり、発見または論証ではない

**すべての出力の上部にこれを置く。落とさない。柔らかくしない。**

> このチャートは弁護士分析と検証のためのドラフトであり、提出された論証、MSJ ブリーフ、開廷陳述、または法的意見ではない。すべてのマッピングは弁護士が出典に対して検証しなければならないリード。リストされた要素はパターン陪審指示、Restatement、または解析されたクレーム言語から来る — ユーザーの法域の**支配的**権威(CACI / NYPJI / サーキットのパターンチャージ / 支配的制定法 / Markman 命令)は異なる可能性があり、常に支配する。ギャップ検出はディスカバリーまたは申立てのための出発点;メリットに関する結論ではない。

ギャップを under-flagging することは一方通行のドア — 要素について plausibility なしに提出された訴状、争われる要素の証拠なしに送達された MSJ 応答、または損害賠償の証明なしに試みられたケース。Over-flagging は両開きドア — 弁護士がレビューでフラグをクリア。デフォルトは両開きドアにバイアス。

---

## 案件コンテキスト

プラクティスレベル CLAUDE.md の `## 案件ワークスペース` をチェック。`Enabled` が `✗`(社内ユーザーのデフォルト)なら、この段落の残りをスキップ — スキルはプラクティスレベルコンテキストを使用し案件マシナリーは見えない。有効化されてアクティブ案件がなければ尋ねる: 「Which matter is this for? Run `/litigation-legal-ja:matter-workspace switch <slug>` or say `practice-level`.」 アクティブ案件の `matter.md` をロード — 特にケースセオリー、訴状 / 訴訟提起(実際に主張された要素のため)、法域、任意の Markman 命令または stipulated 解釈(特許モード)、ケースのフェーズ。出力を `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/<matter-slug>/claim-charts/` の案件フォルダに書き込み。`Cross-matter context` が `on` でない限り、別の案件のファイルを決して読まない。

---

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → ロール、成果物ヘッダー、意思決定ポスチャ、文書ストレージ、ケースセオリー scaffolding
- アクティブ案件の `matter.md` — クレーム、防御、サイド、法域、フェーズ、セオリー
- 民事モード: 訴状またはカウンタークレーム(実際に主張されたカウントのため)、任意の答弁書(実際に主張された積極的防御のため)、関連するパターン陪審指示出典、制定法的なら支配的制定法。また証拠コーパス — デポジション transcript、宣言、生成文書、エキスパートレポート。
- 特許モード: 特許、主張クレーム、明細書、利用可能なら prosecution history、疑似製品素材または先行技術参照、任意の Markman 命令または stipulated 解釈。

`CLAUDE.md` に `[PLACEHOLDER]` マーカーがあれば、このバウンスを表面化:

> I notice you haven't configured your practice profile yet — that's how I tailor risk calibration, landscape, and house style to your practice.
>
> **Two choices:**
> - Run `/litigation-legal-ja:cold-start-interview` (2 minutes) to configure your profile, then I'll run this tailored to YOUR practice.
> - Say **"provisional"** and I'll run this against generic defaults — US jurisdiction, middle risk appetite, lawyer role, no playbook — and tag every output `[PROVISIONAL — configure your profile for tailored output]` so you can see what I do before committing.

### Provisional モード

ユーザーが「provisional」と言ったら、これらの一般的デフォルトを使ってクレームチャートを通常通り構築: middle リスクアペタイト、弁護士ロール、米国法域、プラクティスレベルプレイブックなし(案件の訴答と主張通りのクレームの要素から作業)。レビュアー注記とチャートの各行に `[PROVISIONAL]` をタグ付け。出力の最後に追記:

> "That was a generic run against default assumptions. Run `/litigation-legal-ja:cold-start-interview` to get output calibrated to YOUR practice — your risk calibration, your landscape, your house style. 2 minutes."

**コンフリクトゲート — バイパス不可。** クレームチャートを構築する前に、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace is set up. I won't build a claim chart on a matter that hasn't been intaken — the conflicts check is the gate."

intake されていない案件で進まないこと。intake はコンフリクトを実行しこのスキルが読み取る `_log.yaml` 行を書きます。

---

## モード選択

何より前に上部で尋ねる:

> どの種類のチャート?
>
> 1. **特許クレームチャート** — 疑似製品(`--infringement`)、先行技術(`--invalidity`)、または別の当事者のチャート(`--review`)に対するクレーム限定要素ごとのマッピング。特許論証、IPR petitions / responses、FTO チャートのため。
> 2. **民事要素チャート** — 証拠に対する請求原因(または積極的防御)の要素。訴状 plausibility チェック、ディスカバリー計画、MSJ 準備、order-of-proof アウトラインのため。

プラス intake(両方共通):

- **サイド。** 主張または防御? (民事モードでは負担を反転;特許モードでは侵害/無効フレーミングを反転。)
- **法域 / フォーラム。** 州と裁判所 — パターン指示は異なる(カリフォルニアの CACI、ニューヨークの NYPJI、連邦サーキットのパターンチャージ、州固有のバリエーション)。特許モードでは、Patent Local Rules が異なる(N.D. Cal.、E.D. Tex.、D. Del.、ITC、PTAB)。どれが支配するかをフラグ。
- **フェーズ。** Pre-filing、訴答、ディスカバリー、MSJ、トライアル準備、ポストトライアル。チャートは同じ;出力のフレーミングが変わる。
- **既存チャート?** `--review` なら、ロード。

---

# モード 1 — 特許クレームチャート

## サブモード

- `--infringement` — クレーム要素 vs 疑似製品(PLR 3-1 / Patent Local Rule 3-1 / 侵害論証、IPR/PGR response exhibits、訴状 exhibits)
- `--invalidity` — クレーム要素 vs 先行技術(PLR 3-3 無効論証、IPR/PGR petition exhibits、§102/§103 防御)
- `--review` — 別の人が生成したチャートを監査

## 追加の特許モード intake

- **特許番号と主張クレーム。** どれが独立、どれが従属。(尋ねられない限り未主張クレームをチャートしない。)
- **優先日。** §102 bar と AIA / pre-AIA 体制の有効出願日を確立。
- **既存の解釈。** Markman 命令、stipulated 解釈、briefing で提案された解釈。

## 特許モードワークフロー

### Step 1: クレームを解析

主張独立クレームを番号付き要素に解析。処理:

- **Preamble。** 限定的かどうかを注記 — クレーム解釈の問題(*Catalina Marketing Int'l, Inc. v. Coolsavings.com, Inc.*, 289 F.3d 801 (Fed. Cir. 2002))。解釈命令が解決しない限り `preamble-limiting: unresolved` をフラグ。
- **移行句。** 「Comprising」(オープン) / 「consisting of」(クローズ) / 「consisting essentially of」(セミオープン)。追加の未記載要素が侵害を打ち破るかに影響。
- **要素** がカンマ / セミコロンで区切られ、`[1a]`、`[1b]`、`[1c]` と番号付け。番号付けを安定に保つ — それがチャートのスパイン。
- **Means-plus-function (§112(f))** — すべての「means for [function]」または非構造機能的用語。スコープは明細書で開示された構造プラス等価物。col./line で対応する構造を引用。明細書が構造を開示しない場合、`indefinite-112f` をフラグ。
- **Markush groups、Jepson claims、product-by-process、method-step order dependencies** — 異常な解釈ルールに関する注記でフラグ。
- **従属クレーム** — 親を参照;追加限定のみをチャート。**実行、ジェスチャーではない。** 主張クレームに従属が含まれる場合、Step 4 で各従属の実際の追加限定行を生成 — 従属が「チャートされるべき」と注記しないこと。
- **構造用語コグネート — `construction-dependent` にデフォルト。** 構造名詞を field の先行技術で一般的なコグネート付きで記載する各要素について、明細書が用語を明示的に定義するか既存の Markman 命令が曖昧さを foreclose する場合を除き、行の状態を `literal-construction-dependent`(`literal` ではない)にデフォルト。これらは Markman で最も一般的に争われる用語 — クリーンな literal 読みを推定するとリスクを under-flag。proactively にフラグする一般的なコグネートファミリー:

  | Field | コグネートファミリー(`structural-term-cognate` としてフラグ) |
  |---|---|
  | Fasteners / anchors | barb / thread / projection / ridge / fin / tooth |
  | Fluidics / catheters | lumen / channel / bore / passage / conduit |
  | Mechanical housings | hub / boss / flange / collar / shoulder |
  | Fasteners / joints | socket / recess / pocket / cavity |
  | Electrical / electronic | contact / terminal / pad / lead |
  | Optical | lens / reflector / window / aperture |
  | Structural | wall / member / support / strut / rib |
  | Surfaces | surface / face / interface |

  このリストは網羅的ではない — クレームが narrowly に読まれ得る(pointed barb vs 任意の projection)または broadly に(channel vs 任意の passage)構造名詞を記載する場合、`_constructions` で `structural-term-cognate` をフラグし行を `construction-dependent` にデフォルト。Markman 命令または明細書の定義が曖昧さを foreclose した後、弁護士が `literal` にデモートできる。

ユーザーに解析を表示。マッピング前に確認。間違った解析は下のすべての行を毒する。

### Step 2: クレーム解釈チェック

争いのある用語をフラグ:

- 造語または明細書で定義された用語
- prosecution history のある用語(修正、議論、放棄 — *Phillips v. AWH Corp.*, 415 F.3d 1303 (Fed. Cir. 2005);*Festo* estoppel)
- 機能言語(「configured to」、「adapted to」、「operable to」)
- 相対用語(「substantially」、「about」) — *Nautilus, Inc. v. Biosig Instruments, Inc.*, 572 U.S. 898 (2014) 下の definiteness リスク
- コンピュータ実装用語 — 無効性のための Alice / §101 エクスポージャー

各フラグ用語について、マッピングが動作する解釈と失敗する解釈を述べる。Markman 命令が存在すれば適用。Briefing 進行中なら、各サイドの提案解釈下でチャート。

### Step 3: マップ

各要素について、各ターゲットについて:

1. **証拠を見つける。** 疑似製品: 文書、マニュアル、データシート、ソースコード、teardowns、デポジション証言、エキスパートレポート。先行技術: 米国特許の column/line、published apps の段落、NPL のページ/図。先行技術について、参照が qualify するかをフラグ(§102(a)(1)、(a)(2)、(b);AIA vs pre-AIA カットオフ)。先行技術ステータスが明らかでなければ `prior-art-status: needs-evidence` とマーク。
2. **逐語引用。** 文字ごと。言い換えなし。文境界でカットし elision をマーク。
3. **マッピングを性格付け。**

   | Mapping | 意味 | Where |
   |---|---|---|
   | `literal` | クレーム言語が疑似フィーチャ / 先行技術開示に読み込む | Both |
   | `literal-construction-dependent` | X 下では literal;Y 下では失敗 | Both |
   | `doe` | 等価物(function-way-result または insubstantial differences) | 侵害のみ |
   | `anticipation` | すべての要素が単一参照内、クレーム通り配置(*Net MoneyIN, Inc. v. VeriSign, Inc.*, 545 F.3d 1359 (Fed. Cir. 2008)) | 無効のみ |
   | `obviousness-combination` | 二次参照が欠落要素を供給;*KSR Int'l Co. v. Teleflex Inc.*, 550 U.S. 398 (2007) 下で combine する motivation 必要 | 無効のみ |
   | `partial` | 要素の一部が存在 | Both |
   | `not-found` | 要素が存在しない | Both |
   | `needs-evidence` | 利用可能な素材から判断できない | Both |
   | `construction-dependent` | 争いのある用語がどう解釈されるかに依存 | Both |

4. **セルごとに状態。** `mapped` / `mapped-doe` / `partial` / `not-found` / `needs-evidence` / `construction-dependent` / `anticipation` / `obviousness-combination`。
5. **未解決質問をフラグ。** 「This maps if [X]. Need [teardown / source code / deposition / expert] to confirm.」

**No silent supplement.** 薄い文書化は `needs-evidence` を意味し、類似製品からの外挿ではない。

### Step 4: 従属クレーム — 実行、ジェスチャーではない

主張従属クレームごとに、ターゲットに対する追加限定をチャートする実際の行(または行のセット)を生成。親依存性が注記され、従属の侵害 / 無効は親のものを要する。**プレースホルダ注記ではなく実際の行を生成。**

ユーザーが従属を含む主張クレームのリストを提供したら、チャートの出力は各々の行を含む必要があります。ユーザーが独立クレームのみを与え「とりあえず独立をチャート」と言った場合 OK — その場合出力は従属をチャートしないが、ドロップされたものを明示的に表面化(「Asserted dependents [X, Y, Z] not charted in this run — request: rerun with `--include-dependents` or paste the dependent claim text」)。従属を黙ってスキップしないこと。

従属クレーム行フォーマット:

```markdown
| [#] | Element (verbatim) | Accused feature (or prior-art disclosure) | Evidence (pin-cited) | Mapping | State | Verified |
|---|---|---|---|---|---|---|
| 2 [add'l] | "wherein the barb extends at an angle of 15° to 30° from the body axis" | AnchorFast Mini barb angle 18° per [CM-AM-2026-03 Fig. 4 + §2.3] | [CM-AM-2026-03 §2.3] "barb angle 18° ±2°" | literal-construction-dependent | mapped | ☐ |
```

### Step 4.5: DOE 補足 — 実行、ジェスチャーではない

疑似フィーチャが構造的に類似だが文字通り同一でない `literal` としてチャートされたすべての要素について — または `literal` マッピングが争われる解釈に turn するすべての要素について — **paired DOE candidacy 行**を生成(侵害モード)。実際の DOE マッピングを生成せずに「DOE analysis is separate」をフットノートしないこと。

DOE candidacy 行は、1 段落の function-way-result スケッチを追加、要素ごとに prosecution history estoppel と dedication-to-the-public リスクをフラグし、等価物を支える証拠を引用。DOE が適用不能(要素が疑似製品に争いなく文字通り読み込む)ならスキップ。`literal` が construction-dependent で、DOE が narrower 解釈下での弁護士のフォールバックなら、DOE 行を生成。

フォーマット:

```markdown
| [#-DOE] | Element | Accused feature | Function-way-result | PH estoppel? | Dedication risk? | State |
|---|---|---|---|---|---|---|
| 1b-DOE | "at least one barb" | three-barb opposing-face array | function: resist withdrawal; way: mechanical engagement with cancellous bone; result: anchor remains seated under tensile load. | [needs-evidence: prosecution history] | [needs-evidence: disclosed-but-unclaimed alternatives in spec] | construction-dependent |
```

従属と同様: スキルが DOE 行を生成できない理由があれば(function-way-result を基礎付ける疑似製品証拠なし、prosecution history が利用可能でない)、明示的にそう言い `needs-evidence` にルート。DOE を黙ってスキップしないこと。

### Step 5: Indirect、Divided、Willfulness(侵害のみ)

フラグ、意見しない:

- **Induced (§271(b))** — *Commil USA, LLC v. Cisco Systems, Inc.*, 575 U.S. 632 (2015);*Global-Tech Appliances, Inc. v. SEB S.A.*, 563 U.S. 754 (2011)
- **Contributory (§271(c))** — 侵害使用のために特に作られたコンポーネント
- **Divided / joint (§271(a))** — *Akamai Techs., Inc. v. Limelight Networks, Inc.*, 797 F.3d 1020 (Fed. Cir. 2015) (en banc) directs/controls テスト
- **Willfulness** — *Halo Elecs., Inc. v. Pulse Elecs., Inc.*, 579 U.S. 93 (2016);§284 下の treble damages

### Step 6: 無効閾値(無効のみ)

§102 について: 単一参照内のすべての要素。参照を跨ぐ部分は §103。

§103 について: プライマリ参照 + セカンダリ参照 + *KSR* 下の文書化された motivation。明示的な teaching/suggestion/motivation、市場または design-need motivation、reasonable expectation of success、**secondary considerations**(*Graham v. John Deere Co.*, 383 U.S. 1 (1966))をフラグ — 商業的成功、long-felt need、failure of others、業界の称賛、コピー。

また以下をフラグ:
- **§101** — *Alice Corp. Pty. Ltd. v. CLS Bank Int'l*, 573 U.S. 208 (2014);*Mayo Collaborative Servs. v. Prometheus Labs., Inc.*, 566 U.S. 66 (2012)
- **§112 ¶ 1** — written description、enablement(*Amgen Inc. v. Sanofi*, 598 U.S. 594 (2023))
- **§112 ¶ 2** — definiteness(*Nautilus*, supra)
- **§112 ¶ 6** — means-plus-function 構造
- **Unenforceability** — inequitable conduct、prosecution laches、assignor/licensee estoppel(弁護士のみのフラグ)

無効は clear and convincing 証拠で示される必要 — *Microsoft Corp. v. i4i Ltd. P'ship*, 564 U.S. 91 (2011)。チャートの prima facie はトライアルでの証明ではない。

### Step 7(レビューサブモード): 監査

各行について: マッピングは支持されているか? Pin cite は正確か? 要素は完全に account for されているか? 最強の counter は? 反駁機会は? 行ごとの判定(`supported` / `weak` / `unsupported`)とチャートの脆弱性を出力。

## 特許モードガードレール(共有ガードレールに加えて)

- **Rule 11 / Patent Local Rule.** 侵害と無効論証は reasonable inquiry と non-frivolous 基礎を要する。このスキルからのチャートはドラフトであり、論証ではない。
- **クレーム解釈率直さ。** すべての construction-dependent 行が仮定された解釈とマッピングが失敗する解釈を述べる。
- **DOE 率直さ。** DOE マッピングは literal と等価ではない。要素ごとに prosecution history estoppel と dedication-to-the-public リスクをフラグ。
- **Indirect は別個。** induced / contributory を direct-infringement 行に折り込まない。
- **無効のチャート上の負担。** clear-and-convincing 基準を述べる。

---

# モード 2 — 民事要素チャート

請求原因(または積極的防御)の要素を証拠に対してマップ。killer outputs は (a) どの証拠がどの要素と行くかを述べるチャート、(b) 何が欠落しているかを弁護士に伝えるギャップリスト。

## ワークフロー

### Step 1: クレームを特定

- どの請求原因?(または防御?)複数カウントなら、各々を別個にチャート。
- どのサイド? Plaintiff の prima facie ケース、defendant の積極的防御、defendant の plaintiff の prima facie ケースへの挑戦(MSJ モード)。プラクティスプロファイルの `## サイド` をデフォルトとして読む — `plaintiff` は prima facie ケースのマッピング(要素を立証)にデフォルト;`defense` はギャップと積極的防御のマッピング(要素を反証または回避)にデフォルト。開始前にこの案件でポスチャがマッチすることを確認。
- どの法域? 州と裁判所。**要素とパターン指示言語は法域で異なる。** テンプレートライブラリは baseline;支配的なパターン指示または制定法が支配。
- どの訴答? チャートが実際に主張されたカウントを追跡するよう、訴状 / カウンタークレーム / 答弁書をロード、一般的バージョンではなく。

### Step 2: 要素をロード

3 つのパス:

**(a) テンプレートライブラリ。** `references/element-templates.md`(このスキルのディレクトリ)を参照。一般的な請求原因と一般的な積極的防御の baseline 要素、Restatement / パターン指示への引用と法域 caveat 付き。主張されたカウントにマッチするテンプレートを選択。

**(b) カスタム。** ユーザーが要素を定義、または陪審指示 / 制定法 / 訴状のカウントを貼り付けて解析。番号付き要素に解析。

**(c) 積極的防御。** マッピング防御もサポート — 出訴期限、laches、estoppel、放棄、unclean hands、免責、accord and satisfaction、failure to mitigate、比較過失、寄与過失、リスク引受、など。防御は被告が立証しなければならない独自の要素を持つ(または一部については、上げられたら原告が否定する必要)。

**法域固有定式 — proactively に表面化。** プラクティスプロファイルの `## カンパニープロファイル → Core jurisdictions` またはアクティブ案件の `matter.md` が **デラウェア、ニューヨーク、またはカリフォルニア**(最も一般的な 3 つの商事フォーラム)を名指す場合、baseline と並んで proactively に州固有定式を表面化 — 「法域は追加/削除/言い換えますか」を先に尋ねない。ユーザーはスキルにローカルルールを教える必要がなく、スキルが提供してユーザーが選ぶ。

尋ねずに表面化する逸脱(非網羅的 — パターンが繰り返されるにつれてこのリストに追加):

| 請求原因 / 防御 | Baseline (Restatement / pattern) | 法域固有定式 |
|---|---|---|
| 契約違反 | 4 要素(契約、履行、違反、損害;CACI 303) | **DE:** 3 要素 — 契約義務、違反、損害(因果関係が違反に折り込まれる)、*VLIW Tech., LLC v. Hewlett-Packard Co.*, 840 A.2d 606 (Del. 2003) に従って。**DE は 5 番目の要素を追加** — クレームが specific performance を求める場合、no adequate remedy at law。 |
| 契約違反 — 商品 | コモンロー違反要素 | **商品 + U.C.C. Article 2 法域(ルイジアナを除く全 50 州):** U.C.C. 違反要素をロード(適合する tender、受諾 / 拒否 / 撤回、cure、cover、売り手の救済)。両方を提示;ユーザーに選ばせる。 |
| 契約違反 — マルチロット商品 / 分割払い契約 | コモンロー違反または U.C.C. § 2-711(単一配達違反フレームワーク) | **U.C.C. § 2-612 下の分割払い契約** — 「substantial impairment of the value of the installment」が perfect-tender ルールを置き換え;集約違反は「substantial impairment of the value of the whole contract」を要求。契約が商品を別個のロット(複数の出荷、配達)で配達する場合、§ 2-612 フレーミングにデフォルト — それが支配的体制で、分析は単一配達違反と材料的に異なる。署名者のためにフラグ: 「This is drafted as an installment contract under § 2-612 — confirm that characterization matches the contract's delivery structure.」 |
| 過失 | 4 要素(義務、違反、因果関係、損害;Restatement (Second) Torts § 281) | **CA:** CACI No. 400 定式に従う(該当する場合 CACI 418 ごとの negligence per se)。**NY:** PJI 2:10 定式 — proximate cause について若干異なる言語。 |
| 過失誤表示 | Restatement (Second) Torts § 552 — justifiable reliance、金銭的損失 | **NY:** *Credit Alliance Corp. v. Arthur Andersen & Co.*, 65 N.Y.2d 536 (1985) ごとに **contemporaneous privity** または「privity に接近するほど近い」関係を要求。 |
| 詐欺 | 9 要素(しばしば 5 に凝縮 — 表明、materiality、虚偽性の知識、reliance を誘導する意図、justifiable reliance、損害) | **DE:** *Stephenson v. Capano Dev.*, 462 A.2d 1069 (Del. 1983) ごとに 5 要素。**CA:** CACI 1900 定式 — reliance が「justifiable」の 5 要素。**NY:** CPLR 3016(b) 下で特殊性を持って訴答が必要、scienter は distinct な要素。 |
| 信認義務違反 | Restatement / コモンロー — 信認義務、違反、損害 | **DE:** 最も発展した信認義務法体系(*Aronson v. Lewis*、*Cede & Co. v. Technicolor*、*In re Trados*) — フォーラム関係なくすべての DE エンティティ案件にデラウェア定式をデフォルト。 |

法域固有定式が baseline と材料的に異なる場合、チャートは 1 行の callout で開く:

> **Jurisdiction note:** You told me this is a [DE/NY/CA] matter. Here's how [jurisdiction]'s formulation differs from the baseline: [divergence]. The chart below uses the [jurisdiction] formulation. If that's wrong, say so and I'll reload.

マッピング前にユーザーと要素リストを確認。ユーザーの法域が DE/NY/CA でなければ尋ねる: 「Does your jurisdiction's pattern instruction add / drop / reword any of these?」 Yes ならそのバージョンを使用。

### Step 3: マップ

各要素について:

- **支持証拠** — 何がこの要素を立証するか? pin cite で出典を引用。
  - デポジション証言 — `[Doe Dep. 42:15–43:7]`
  - 宣言 — `[Smith Decl. ¶ 12]`
  - 生成文書 — `[DEF00012345 at 3]`
  - 承認 — `[Def.'s Resp. to RFA No. 5]`
  - Exhibit — `[Trial Ex. 14 at 2]`
  - エキスパートレポート — `[Jones Expert Rep. at 18]`
  - ディスカバリー応答 — `[Pl.'s Resp. to Interrog. No. 8]`
  - 制定法 / 判例 — 純粋に法的な要素のため
- **逐語引用** 証拠が証言的または文書的な場合。言い換えなし。
- **反証証拠** — 何が他方に切るか? 引用。これが行の脆弱性。
- **強さ** — `strong` / `moderate` / `weak` / `none`。シンプルに保つ。Over-calibrated 強さスコアはノイズ;`weak` と `none` が重要な行。
- **セルごとの状態** — `supported` / `partial` / `disputed` / `gap` / `needs-discovery`。

### Step 4: ギャップ検出 — killer 出力

マッピング後、ギャップリストを生成。これがチャートのポイント。

> **薄いまたは証拠のない要素:** [リスト]
>
> - 主張する場合(plaintiff): これらが訴状の plausibility(Iqbal / *Ashcroft v. Iqbal*、Twombly / *Bell Atl. Corp. v. Twombly*)、MSJ 反対、またはトライアルでのケースを敗北させる。次の申立て前にそれらを閉じる。
> - 防御する場合: これらが MSJ ターゲットと directed-verdict 申立て。Plaintiff は各要素を立証する必要があり;ギャップは防御。
> - Pre-discovery の場合: これらがディスカバリー優先順位 — ギャップを `supported` に変えるか `none` を確認するデポジション、文書リクエスト、interrogatories。

ギャップ検出はメリットに関する結論ではない。ケースが薄い場所のマップ。

### Step 5: フェーズ認識フレーミング

フェーズを尋ねる。同じチャート;出力の異なるフレーミング:

- **Pre-filing / 訴答。** 訴状が plausibility と各要素を主張するか(*Ashcroft v. Iqbal*, 556 U.S. 662 (2009);*Bell Atl. Corp. v. Twombly*, 550 U.S. 544 (2007))? 事実支持なしに information and belief で訴答された任意の要素は 12(b)(6) ターゲット。
- **Discovery。** 各 `gap` または `needs-discovery` 要素について、どのディスカバリーが必要? どの証人、どの文書 custodian、どの interrogatories、どの RFA。
- **MSJ。** 各要素について、material fact の genuine な争いはあるか? movant の `supported` セルで反証証拠なしは summary-judgment ammunition;`disputed` セルは MSJ-defeating。
- **Trial。** Order of proof。どの証人が要素 1 を証明、どの exhibit が要素 2 を証明、誰が認証、何が foundation。チャートがトライアルアウトラインになる。

### Step 6(レビューサブモード): 監査

相手当事者の MSJ ブリーフ、却下申立て、または外部弁護士のドラフトについて: 各要素について、引用された証拠は実際に証明するか? チャートのどこが薄い? あなたの最強の counter は?

## 民事モードガードレール(共有ガードレールに加えて)

- **法域。** 要素リストは baseline。常に支配的パターン指示(CACI、NYPJI、連邦サーキットパターンチャージ、など)または制定法を確認。チャートの `_elements` シートに出典を述べる。
- **訴答カウントのみ。** 実際に訴答されたものをチャート。事実が支えるかもしれないからといって訴状が主張しないカウントを追加しない — それは異なる分析。
- **積極的防御。** 防御をマッピングする場合、被告に負担があるか(ほとんど)、または防御を上げることが原告に負担をシフトするかを注記。
- **「ギャップ」≠「ケース終了」。** ギャップはリード。ディスカバリー、宣言、エキスパートレポートが閉じる可能性。チャートが掘る場所を示す。

---

# 共有シャシー(両モード)

## 出力

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` `## Outputs` から成果物ヘッダーを prepend。

### マークダウンテーブル(常に)

クレーム / 防御 / 特許クレームごとにターゲットごとに 1 テーブル。

**特許モード例:**

```markdown
| [#] | Element (verbatim) | Accused feature | Evidence (pin-cited) | Mapping | State | Verified |
|---|---|---|---|---|---|---|
| 1a | "a processor configured to..." | SoC per datasheet | [Datasheet p. 7] "..." | literal-construction-dependent | mapped | ☐ |
| 1b | "means for [function]" (§112(f)) | [alleged equiv.] | [source, file.c:124] "..." | needs-evidence | needs-evidence | ☐ |
```

**民事モード例:**

```markdown
| [#] | Element | Evidence supporting (pin-cited) | Evidence contradicting | Strength | State | Verified |
|---|---|---|---|---|---|---|
| 1 | Existence of a contract | [Ex. 3, MSA § 1; Smith Dep. 22:4–14] | none | strong | supported | ☐ |
| 2 | Plaintiff's performance | [Jones Decl. ¶¶ 4–9] | [Doe Dep. 101:3–11: "they never delivered Phase 2"] | moderate | disputed | ☐ |
| 3 | Defendant's breach | — | [Doe Dep. 101:3–11] | none | gap | ☐ |
| 4 | Causation | — | — | none | needs-discovery | ☐ |
| 5 | Damages | [Expert Rep. at 18 — $2.4M lost profits] | [Def.'s Expert Rep. at 6 — critiques methodology] | moderate | disputed | ☐ |
```

その後:
- **防御 / 閾値**(特許モード: 無効 / indirect / willfulness フラグ;民事モード: 積極的防御フラグ、訴答前 Iqbal/Twombly フラグ)
- **ギャップリスト**(民事モード) / **needs-evidence リスト**(特許モード) — **優先出力**
- **何がどちらに切るか — サマリー** — 最強要素、最弱要素
- **結論行** — *「This skill does not conclude.」* マップ/支持要素: [リスト]。証拠が必要 / ギャップ状態の要素: [リスト]。Construction-dependent(特許) / disputed(民事)要素: [リスト]。弁護士判断必要。
- **引用検証** — すべての pin cite、判例、column/line、デポジション page:line は出典に対して検証されなければならない。

### CSV(常に)

チャートごとに 2 ファイル:
- `[chart-slug].csv` — 値
- `[chart-slug]_sources.csv` — 逐語引用、pin cites、ノート

**CSV / スプレッドシートセル安全。** 任意のセル値を書く前に、最初の文字をチェック。`=`、`+`、`-`、`@`、tab(`\t`)、または carriage return(`\r`)なら、Excel/Sheets formula 解釈を中和するために単一のアポストロフィ(`'`)を prepend。敵対出典(相手方論証、競合製品マニュアル、第三者先行技術、scrape された web ページ、デポジション transcript、ディスカバリー生成)からの逐語証拠は、スプレッドシートが formula として実行する文字列(`=HYPERLINK(...)`、`=cmd|...!A1`、`+WEBSERVICE(...)`)を含む可能性、弁護士が開いたときにチャートをデータ流出または RCE ベクターに変える。RFC 4180 quoting 単独ではこれを defeat しない — leading `=` は依然解釈される。CSV、XLSX、Sheets 出力にアポストロフィプレフィックスを適用。レビュアーがどの引用が中和されたかを見られるように、これが適用されたセルをログ。

### スプレッドシート(Excel または Sheets)

チームが作業するものを尋ねる。`corporate-legal` の `tabular-review` スキルからのパターンを使用 — 同じセルレベル引用モデル、同じ状態ベースカラーコーディング、同じ `Verified` 列、同じスキーマシート:

- 要素ごとに 1 行(または複数ターゲット比較なら要素 × ターゲット)
- 各証拠列が逐語引用と pin cite を含む隠し出典列とペア;セルコメント(Excel)またはノート(Sheets)がホバーで引用を表面化
- 状態別カラーコーディング:
  - *特許:* 白 = `mapped`、黄 = `construction-dependent` / `partial` / DOE、橙 = `needs-evidence`、赤 = `not-found`
  - *民事:* 白 = `supported`、黄 = `partial` / `disputed`、橙 = `needs-discovery`、赤 = `gap`
- 各証拠列ごとに `Verified` 列、デフォルトで空白 — レビュアーがマーク
- 要素出典を文書化する `_elements` シート: パターン陪審指示(CACI No. X、NYPJI §Y、連邦サーキットパターンチャージ)、制定法(引用)、Restatement セクション、または特許クレーム解析。これがチャートを auditable にする — 読者が要素がどこから来たかを見られる。
- すべての `gap`、`needs-evidence`、または `needs-discovery` 行を、まだ必要なものと共にリストする `_gaps` シート
- 特許モードのみ: `_claim-parse` シート(要素分解)、`_constructions` シート(争いのある用語と仮定された解釈)

スプレッドシートに書かれるすべてのセルにアポストロフィプレフィックス中和を適用。

成果物ヘッダーを最上部行として prepend。それと並んで含める:

> このチャートは特権、機密、または両方の出典文書から派生。出典の特権と機密ステータスを継承 — 特権サークルを超える流通は特権を放棄する可能性。案件の特権ファイルと共に保管し、流通決定を deliberately に行う。このチャートの何も提出または送達されていない;これは弁護士レビューのためのドラフト。

### ファイル名と場所

- 特許侵害: `claim-chart-infringement-[patent#]-claim[#]-[target]-YYYY-MM-DD.{md,csv,xlsx}`
- 特許無効: `claim-chart-invalidity-[patent#]-claim[#]-[ref]-YYYY-MM-DD.{md,csv,xlsx}`
- 民事: `element-chart-[count-slug]-[side]-YYYY-MM-DD.{md,csv,xlsx}`
- レビュー: `chart-review-[subject]-YYYY-MM-DD.{md,csv,xlsx}`

案件ワークスペース有効化と案件がアクティブ: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/<matter-slug>/claim-charts/`。それ以外: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/claim-charts/`。パスを表面化。案件の `history.md` に 1 行エントリを追記。

## サマリー readout

チャート書き込み後、1 スクリーン readout を与える:

- クレーム / カウント / 特許クレーム、ターゲット、法域、フェーズ
- チャートされた要素 · 支持/マップ · partial · disputed · gap / needs-evidence · not-found
- ギャップリスト(民事)または needs-evidence リスト(特許) — **優先リスト**
- 出力ファイルの場所
- リマインダー: すべてのセルがリード。チャートはドラフトであり、論証 / ブリーフ / order of proof ではない。

## 非弁護士ゲート

`## このプラグインの利用者` Role が Non-lawyer なら:

> このチャートはリサーチドラフトであり、法的提出物ではない。論証を送達、ブリーフを提出、またはメリット意見にこれに依拠することは Rule 11 と実質的な法的結果を伴う。関連法域の弁護士が任意の法的目的で使用される前にレビューしなければならない。
>
> 弁護士に持っていく 1 ページのブリーフ:
>
> [生成: クレーム / 特許、サイド、法域、フェーズ、要素、支持 / ギャップ / needs-discovery カウント、最も load-bearing な 3 つの未解決質問。]

ブリーフと並んでチャートを配信。

## 共有ガードレール — チェックリスト

- **引用検証。** すべての pin cite(column/line、ページ、デポジション page:line、Bates、¶)は出典に関する主張。弁護士が検証。スキルは引用を捏造しない — 引用が生成できなければセルは `needs-evidence` または `gap`。
- **Source attribution.** すべての逐語引用はコンパニオン CSV とスプレッドシートの隠し出典列に出典を持つ。出典のない引用は証拠ではない。
- **No silent supplement.** 薄い証拠は `needs-evidence` / `gap` を意味し、「extrapolate」ではない。web 検索、トレーニングデータ、または「これらのケースが通常進む方法」からギャップを閉じない。
- **案件ワークスペースチェック。** 書き込み前にアクティブ案件を確認。決して案件 A のチャートを案件 B のフォルダに書き込まないこと。
- **意思決定ポスチャ。** 要素が満たされているか不確実なときフラグ;決定しない。`partial` が何の部分が欠落しているかを弁護士に伝える。
- **Formula injection.** CSV / XLSX / Sheets に書かれるすべてのセルが leading `=`、`+`、`-`、`@`、`\t`、`\r` をチェックされ `'` でプレフィックス。デフォルト: neutralize-then-write。
- **要素は法域固有。** テンプレートライブラリは baseline。支配的パターン指示または制定法が支配。
- **チャートはブリーフ、提出物、または論証ではない。** すべての出力はドラフト。

---

## 他のスキルとの関係

- `ip-legal:infringement-triage`(特許モード) — 最初のパスフラグリスト。このスキルが次に来る完全なチャート。
- `ip-legal:fto-triage` — FTO は potentially-accused ポスチャから同じメカニクスを使用。自社製品 vs 第三者特許を評価する場合、FTO にルートしこのスキルのフォーマットを使用。
- `corporate-legal:tabular-review` — 基礎となるセルレベル引用と検証状態パターン。クレーム / 要素チャートは specialized tabular review。
- `litigation-legal-ja:chronology` — 時系列表はタイムライン;要素チャートは proof matrix。時系列表エントリはしばしばセルの証拠引用になる。
- `litigation-legal-ja:deposition-prep` — `needs-discovery` セルはしばしば depo トピックになる。depo 後、新規証言がセルを埋める。
- `litigation-legal-ja:brief-section-drafter` — MSJ ブリーフの事実セクションはしばしば要素チャートの支持された行から直接構築される。

---

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` ごとに次のステップの決定ツリーで締めくくる。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではない。ツリー**が**出力;弁護士が選ぶ。

## このスキルがしないこと

- **結論を出さない。** 侵害でも、非侵害でも、責任でも、非責任でもない。決して。
- **クレーム解釈**(特許)または**支配的要素**(民事)を決定しない。争いのある用語 / baseline 要素をフラグし、述べた仮定下でチャート。
- **無効の clear-and-convincing 負担を満たさない**または**トライアルでの preponderance**を満たさない。弁護士レビューのための prima facie ドラフトを生成。
- **エキスパート分析の代替ではない。** ソースコードレビュー、teardowns、テクニカルエキスパート、損害賠償エキスパートは、このチャートがルートする(置き換えない)別個の成果物。
- **何も送達、提出、署名しない。** すべての出力はドラフト。弁護士が送達して提出。
- **外挿しない。** 証拠がなければ、セルは `needs-evidence` / `gap` — 決して推測しない。
