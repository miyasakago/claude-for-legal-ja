---
name: fto-triage
description: >
  Freedom-to-operate triage — a structured first look at potentially blocking
  patents, not an FTO opinion. Use when a product, process, or feature is
  being evaluated for blocking patents, when asked whether anything stops a
  launch, or to build a claim-chart first pass against the most plausible
  patents before patent counsel review. This skill never concludes a product
  is clear to launch.
  フリーダム・トゥ・オペレート・トリアージ — 潜在的にブロックする特許を構造化された
  最初の視点で見る、FTO 意見ではない。製品、プロセス、または機能がブロック特許に対し
  評価されているとき、ローンチを止めるものがあるかを問われたとき、または特許カウンセル
  ・レビュー前に最も plausible な特許に対するクレームチャートの初回パスを構築する
  ときに使用。本スキルは製品がローンチに clear と決して結論しない。
  日本語トリガー: 「FTO トリアージ」「フリーダムトゥオペレート」「特許リスク調査」「特許侵害リスク」「ローンチ前特許チェック」。
argument-hint: "[製品 / プロセス / 機能と法域を記述 — または主題のみ、こちらから尋ねる]"
---

# /fto-triage

**これはフリーダム・トゥ・オペレート意見ではありません.** 正式な FTO 意見は包括的な調査、完全なクレーム解釈、登録特許カウンセルによる要素単位の侵害分析を要します。特許侵害は厳格責任; 故意侵害は損害賠償を三倍にします。本スキルからの「明らかなブロック特許なし」結果は、トリアージが 1 つも見つけなかったという意味で、製品が clear という意味ではない。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読む。`[PLACEHOLDER]` を含むなら停止して `/ip-legal-ja:cold-start-interview` に誘導。
2. 下記のワークフローに従う。
3. インテーク(製品/プロセス、技術詳細、法域、既知の特許、タイミング)を実行。
4. コネクター(Solve Intelligence Patents、または他の特許リサーチ MCP)が利用可能なら予備特許検索を実行。さもなくば出力でそう述べ、ユーザーが供給した特許で進める。
5. 最も plausible な 2〜5 の特許について、各独立クレームに対するクレームチャート初回パスを構築 — 要素単位。Literal 読みを最初; doctrine-of-equivalents を別々にフラグ; 間接 / divided 侵害をフラグ。
6. 実際の FTO 研究が解決するオープン・クエスチョンをリスト(エンフォース可能性、出願履歴、IPR 結果、ライセンス可用性、譲受人のエンフォース履歴)。
7. トリアージ・メモを案件フォルダまたはプラクティス出力フォルダに書く。Role に応じた work-product ヘッダーを適用。
8. 推奨される次のステップ、willfulness 注記(会社がさらなるカウンセル・レビューなしで進むと、特定特許の知識が willfulness にファクターとなる)、Role が非弁護士なら非弁護士ゲートで終了。

本スキルは製品がローンチに clear と決して結論しない。不確実なら、フラグ — 特許カウンセルが決定。

## 例

```
/ip-legal-ja:fto-triage "an on-device speech recognition model for consumer wearables, US launch first"
```

```
/ip-legal-ja:fto-triage
```

---

## THIS IS NOT A FREEDOM-TO-OPERATE OPINION

**プラグイン中最大の声のガードレール。毎回の出力の上部でこれを述べてください。落とさない、和らげない、読者にスキップさせない.**

> **This is not a freedom-to-operate opinion.** FTO 意見は通常登録特許カウンセル
> によるプロフェッショナルな法的判断で、包括的な調査、完全なクレーム解釈、関連
> する特許の各クレームに対する要素単位の侵害分析に基づきます。本トリアージは
> 外にあるかもしれないものを構造化された最初の視点で見たもの。「明らかな
> ブロック特許なし」結果はトリアージが 1 つも見つけなかったという意味で、製品が
> clear という意味ではない。特許侵害は厳格責任; 故意侵害(特許を知って anyway
> 進むことから続きうる)は 35 U.S.C. § 284 下で損害賠償を三倍にします。製造、
> 使用、販売、または輸入の判断は、本トリアージではなく、正式な FTO 研究と
> カウンセルの判断に informed されたビジネス判断です。登録特許弁護士または
> 代理人が、誰かが製品判断のためにこれに依拠する前に評価します。

ブロッキング特許を under-flag するのは一方通行のドア — 製品ローンチ、1 年後の証言録取、テーブル上の三倍損害賠償。Over-flag は両方向のドア — 弁護士が read-through でリストを絞ります。両方向のドア側に留まる。常に。

### Willfulness に関する注記

このトリアージを読むことは特許について何かを読むこと。特許について何かを読むことは、ある状況では、後の willfulness 分析にファクターとなりえます。これが、弁護士が使っているとき出力が特権付きとマークされる理由の 1 つで、非弁護士出力がカウンセルに持参する研究としてフレームされる理由。このトリアージで表面化した特定特許について、特権チャネル外で議論しないこと。

---

## 案件コンテキスト

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(インハウスのデフォルト)なら、この段落の残りをスキップ — スキルはプラクティスレベル・コンテキストを使い、案件機構は不可視。有効でアクティブ案件がなければ問う: 「どの案件向け? `/ip-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください」。アクティブ案件の `matter.md` を案件固有コンテキストとオーバーライド用に読む。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/` に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まないこと。

特許 FTO 案件は **clean-team** または **heightened** 秘匿性が案件オープンに特に当てはまる候補。`matter.md` からの案件の秘匿マーキングを尊重。

---

## まずプラクティスプロファイルをロード

トリアージを実行する前に、`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読む。取得:

- `## Who's using this` から **Role**(弁護士 vs 非弁護士は work-product ヘッダーと下記の非弁護士ゲートを変える)。
- `## IP practice profile` および `## Enforcement posture` から **Registered in** と **enforce where**(防御ポートフォリオのクロスチェックと法域デフォルトに有用)。
- ルーティングステップ用に `## IP practice profile` → `Outside counsel roster` から **Patent OC**。
- `## Available integrations` から **Integrations** — 特に Solve Intelligence、または任意の特許リサーチ MCP。実行可能な調査を決定。
- `## Decision posture on subjective legal calls` から **Decision posture** — 本スキルは「侵害しない」と決して結論しない。

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` が `[PLACEHOLDER]` または `[Your Company Name]` を含むなら、このバウンスを表面化:

> プラクティスプロファイルをまだ設定していないようです — これがスタンス、法域、承認チェーンをあなたのプラクティスに合わせる方法です。
>
> **2 つの選択肢:**
> - `/ip-legal-ja:cold-start-interview`(2 分)を実行してプロファイルを設定、その後 YOUR プラクティスに合わせてこれを実行。
> - **"provisional"** と言えば、これを汎用デフォルト — 米国法域、middle リスク選好、弁護士 Role、プレイブックなし — で実行し、すべての出力を `[PROVISIONAL — configure your profile for tailored output]` でタグするので、コミット前に私の動作が分かります。

### Provisional モード

ユーザーが「provisional」と言ったら、これらの汎用デフォルトで FTO トリアージを通常通り実行: middle リスク選好、弁護士 Role、米国法域、プレイブックなし(立場リストに対するマッチではなく完全分析を実行)。レビューアーノートとすべての発見ブロックを `[PROVISIONAL]` でタグ。出力の終わりに追加:

> 「それはデフォルト前提に対する汎用実行でした。YOUR プラクティスに合わせた出力 — プレイブック、法域、リスク選好 — のために `/ip-legal-ja:cold-start-interview` を実行してください。2 分」。

---

## インテーク

1 バッチで尋ねる:

> FTO トリアージを実行します。まずいくつかの質問:
>
> 1. **製品、プロセス、または機能.** 何が作られ、使用され、販売提供され、販売され、輸入されますか? Plainly に記述 — マーケティング・ピッチではなく技術的本質。
> 2. **技術詳細.** 共有できるアーキテクチャ図、クレーム関連スペック、公開製品ページ、またはスペック・ドキュメントは?(詳細が多いほど、より real なトリアージ。)
> 3. **法域.** どこで作られ、使用され、販売され、販売提供され、輸入されますか?(各々 35 U.S.C. § 271 下で別々の侵害行為。指定がなければ US にデフォルト。)
> 4. **既知の特許.** すでにレーダー上にある特許はありますか — 競合のポートフォリオ、既知の SEP プール、NPE レター、エンジニアが言及したもの?
> 5. **タイミング.** ローンチにどれくらい近いか? 数ヶ月先なら、トリアージは早く、design-around はテーブル上。すでに出荷中なら、cover-our-downside モード。

回答を待つ。記述が漠然(「an AI agent」、「a database」)なら、一度プッシュ:

> 技術的本質を教えて — その物は何をして、どうして、新規かもしれないと思う部分は何? 特許クレームはそのレベルにあります。

---

## スコープ — 実用特許のみ

**本スキルは実用特許を分析する.** レーダー上の特許が `D`、`RE`、または `PP` 接頭辞を持つなら、フラグしてルートアウト、クレームチャートしないこと:

- **`D`(意匠特許).** まったく異なるテスト — *Egyptian Goddess, Inc. v. Swisa, Inc.*, 543 F.3d 665 (Fed. Cir. 2008)(en
  banc)下の ordinary observer、全体的な装飾的外観、クレームチャートなし。`infringement-triage` 意匠特許ブランチと意匠特許カウンセルにルート。
  **意匠特許は本 FTO トリアージで分析されない** — 意匠特許のオーバーラップは別のワークストリームとしてフラグされる必要があります。
- **`RE`(再発行).** §252 介在権と recapture-rule フラグを持つ実用特許として扱う。
- **`PP`(植物特許).** 植物特許カウンセルにルート; スコープ外。

**トレードドレス** もクロスフラグ: 製品の外観がリスクなら、同じ事実が §43(a) 製品構成クレームかもしれない、secondary meaning(*Wal-Mart Stores, Inc. v. Samara Bros., Inc.*, 529 U.S.
205 (2000))と非機能性(*TrafFix Devices, Inc. v. Marketing
Displays, Inc.*, 532 U.S. 23 (2001))を要する。並列トラックとしてフラグ。

---

## 調査

### ユーザーが接続しているもの

`## Available integrations` を読む:

- **Solve Intelligence 接続済み:** 技術記述全体で予備調査を実行。検索日、使用クエリ、対象法域、任意の日付ウィンドウ(現行効力中の特許; 最近公開された出願)を注記。
- **特許リサーチ MCP(Google Patents Public Datasets、PatSnap エクスポート): 利用可能:** 使う。
- **上記なし:** 明示的にそう述べる。モデル知識から特許を推測して検索結果として提示しないこと。

### 特許データベースが接続されていない場合のフォールバック

出力でこの正確な文を書く:

> **No patent database search was run.** This triage did not hit Solve
> Intelligence Patents, USPTO Patents Full-Text, EPO Espacenet,
> Google Patents, PatSnap, or any other patent corpus. A structured search
> across the jurisdictions in scope is required before relying on this triage
> for any launch decision. The analysis below is limited to patents and
> applications the user has named or that come up in the conversation.

それから進む。下記のクレームチャート初回パス業務は依然有用 — スコープを honest にラベル付け。

### 補完シグナル(代替ではない)

利用可能でユーザーが許可するなら、特許の懸念をフラグする非特許シグナルを sweep:

- 製品領域周辺の **競合特許出願**。
- 技術クラスの **既知の NPE ターゲティング**(例: テキサス東部 / デラウェア / テキサス西部の network-coding NPE)。
- 製品が関連標準に触れる **規格必須宣言**(IEEE、ETSI、3GPP)。
- 技術空間の **報告された訴訟**(CourtListener / RECAP、Unified
  Patents、Lex Machina)。

各シグナルはより hard に見る理由で、特許ヒットではない。出力でシグナルとしてマーク、識別された特許としてではない。

---

## 見つかった or 供給された各関連特許について

取得:

- **特許番号**(異なるなら出願番号も)と **法域**
- **タイトル**
- **譲受人と発明者**
- **優先日と発行日**
- **有効期限**(USPTO PAIR / PatentCenter / 外国同等別 —
  term adjustment、term extension、terminal disclaimer をチェック)
- **維持年金ステータス / 効力ステータス** — 米国特許が 3.5/7.5/11.5 年維持年金に失敗していたら、期限切れでバーではない
- **クレーム数 — 独立と従属**
- **発行時の独立クレーム**(および post-grant 手続からの関連修正クレーム)
- **関連手続** — IPR、PGR、再審査、訴訟履歴、
  PTAB 結果
- **ファイル wrapper のハイライト** — 出願時の disclaimer、クレームを狭めた修正、スコープに関する声明

**サイレントに supplement しないこと.** 調査が特許を表面化したら、結果を attribute。ユーザーが特許を言及したら、そう言う。特許番号を発明せず、ファイルがサポートしないクレーム要素を「埋めず」、有効期限を想像しないこと。維持年金ステータスが利用不能なら、「maintenance fee status not verified from search result — confirm in PAIR
before relying on in-force status」と書く。

---

## クレームチャート初回パス

これがトリアージの中核。製品に最も plausible な読みを持つ特許 — 通常最も近い技術マッピングを持つ 2〜5 — を選び、各独立クレームを要素単位で歩く。

**選択した各特許について、独立クレームごとに 1 つのクレームチャートを書く:**

| クレーム要素 | 製品はこれを実施? | 根拠 |
|---|---|---|
| "A [preamble phrase]" | [yes / no / possibly / depends on construction] | [1 文 — 製品で何がマップ; 何がマップしない; 何が曖昧] |
| "comprising [element 1]" | [yes / no / possibly] | [マッピングまたはギャップ] |
| "wherein [element 2]" | [yes / no / possibly] | [マッピングまたはギャップ] |
| [すべての要素について続ける] | | |

**チャートのルール:**

- **すべての要素が重要.** クレームは、被告製品が少なくとも 1 つのクレームのすべての要素を実施するときのみ侵害される(all-elements rule)。1 つの要素を欠くと、そのクレームで literal 侵害なし。スキップしないこと。
- **Doctrine of equivalents は別パス.** Literal 侵害を最初にチャート。それから、任意の「no」要素について、DOE 読みが plausible かを注記(insubstantial differences / function-way-result)。DOE 分析を弁護士判断を要するとしてフラグ — prosecution history estoppel と claim vitiation は一般的バーで、トリアージはそれらを裁定しない。
- **クレーム解釈は弁護士の仕事.** 用語が狭くまたは広く解釈されて答えが侵害読みを変える場所では、用語をフラグし両解釈を注記。サイレントに 1 つを選ばないこと。
- **間接侵害(induced、contributory)と divided 侵害** はフラグのみ。完全な分析を試みないこと; これらが適用しうるかと特許カウンセルを要するかを注記。

> **特許システムは法域別に異なる.** 米国クレームチャート(all-elements rule、doctrine of equivalents、prosecution history estoppel、§284/§289 損害賠償)は他のシステムには移転しない:
> - **ドイツ:** 実用新案(Gebrauchsmuster)、DOE の Schneidmesser/Kunststoffrohrteil 質問、二分化された有効性/侵害手続。
> - **中国:** 実用新案(shiyong xinxing)、CNIPA 審査、異なるクレーム解釈。
> - **日本:** 実用新案、JPO 審査、より狭い DOE。
> - **欧州(統一特許裁判所):** 2023 年時点の UPC 手続。
>
> 米国外法域がスコープにあるとき: 「This analysis uses the US claim-charting framework. A product manufactured in China and sold in the EU needs CNIPA and EP analysis, not a US claim chart. I can flag the issues a US analysis surfaces, but the infringement and validity calls require [jurisdiction]-specific review.」

**決断スタンス:** プラクティスプロファイル別、本スキルは「侵害なし」と決して結論しない。どれか:

- 「Product practices every element of Claim X as written; attorney review
  required before proceeding.」
- 「One or more elements are not clearly present; attorney review required to
  assess literal infringement and doctrine of equivalents.」
- 「Claim construction is dispositive on element [Y]; attorney construction
  required before proceeding.」

---

## オープン・クエスチョン

トリアージで表面化したすべての特許は、実際の FTO 研究が答えるオープン・クエスチョンのリストを生成すべき。例:

- 特許はエンフォース可能か — 譲受人が指名されているか、standing 問題、発明者性欠陥、記録された譲渡?
- 出願人は出願時に用語 [X] について何を言ったか、それはクレームを制限するか?
- このクレームは IPR または再審査の対象だったか — PTAB はスコープまたは有効性について何を言ったか?
- すでに利用可能なライセンスはあるか(規格プール、特許マーキング、open patent non-assertion commitment)?
- この譲受人の現実世界のエンフォース履歴は?

Plainly にリスト。

---

## 推奨される次のステップ

トリアージが発見したものでバケット化:

- **独立クレームのすべての要素が製品にマップする(literal 読み)場合:**
  *停止して特許カウンセルを get.* 選択肢には通常、正式な FTO 意見、design-around、ライセンス、有効性 challenge(IPR/PGR)、または(稀に)リスクを承知で進むがある。選択はカウンセルに informed されたビジネス判断。
- **要素が両方向に切れるかクレーム解釈が dispositive な場合:**
  登録特許カウンセルによる完全な FTO 研究。このトリアージでローンチしないこと。
- **特許が期限切れ、放棄、またはエンフォース不能に見える場合:** 弁護士が
  効力ステータスを確認 — トリアージは確認しない。
- **調査で特許が識別されなかったがデータベース・アクセスが存在しなかった場合:** 正式調査が次のステップ、ローンチ判断ではない。
- **常に:** willfulness リスクをフラグ。トリアージが特定特許を表面化したら、会社は今その知識を持つ。さらなる分析なしで進むのは willfulness 発見をサポートしうる。前進パスはカウンセルが文書化すべき。

---

## 出力フォーマット

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` `## Outputs` から work-product ヘッダーを付与。Role が弁護士なら文書を特権付きとマーク; さもなくば下記の非弁護士ゲートを参照。

```markdown
[WORK-PRODUCT HEADER]

# FTO Triage — First Pass (NOT AN OPINION)

**This is not a freedom-to-operate opinion.** 正式な FTO 意見は包括的調査、
完全なクレーム解釈、登録特許カウンセルによる要素単位の侵害分析を要します。
特許侵害は厳格責任; 故意侵害は損害賠償を三倍にします。「明らかなブロック特許なし」
結果はトリアージが 1 つも見つけなかったという意味で、製品が clear という意味
ではない。登録特許弁護士または代理人が、誰かが製品判断のためにこれに依拠する
前に評価します。

**Triage result:** [GREEN / YELLOW / RED — 1 文でなぜ]

## 主題

- **Product / process / feature:** [記述、技術的本質]
- **Technical detail relied on:** [何がレビューされた — スペック、図、公開
  ページ、コード、エンジニアの記述]
- **Jurisdictions in scope:** [make / use / sell / offer / import — § 271 別]
- **Timing:** [pre-launch / near-launch / shipping]

## 調査スコープ

- **Databases searched:** [Solve Intelligence / Google Patents /
  Espacenet / PatSnap — または「データベース検索なし」]
- **Query / approach:** [クエリーテキスト、技術クラス、キーワード、分類]
- **Date / date window:** [検索日; YYYY-MM-DD 以降に公開された効力中の特許 + 出願]
- **Jurisdictions covered by the search:** [list]
- **What wasn't searched:** [名指された譲受人 sweep、SEP 宣言、NPE
  ポートフォリオ、意匠特許、外国同等 — 該当時]

*データベース検索が実行されなかった場合:* **No patent database search was run.** This
triage did not hit Solve Intelligence Patents, USPTO Patents Full-Text,
EPO Espacenet, Google Patents, PatSnap, or any other patent corpus. A
structured search across the jurisdictions in scope is required before
relying on this triage for any launch decision.

## 識別された特許

| 特許 | 法域 | 譲受人 | 優先 / 発行 | 有効期限 | 効力中? | ソース |
|---|---|---|---|---|---|---|
| [番号] | [US/EP/...] | [譲受人] | [日付] | [日付] | [yes/no/unverified] | [検索結果リンクまたは「user-supplied」] |

## クレームチャート — 初回パス

### [特許番号] — 独立クレーム [N]

> "[クレーム N の正確なテキスト]"

| 要素 | 製品が実施? | 根拠 |
|---|---|---|
| [要素 1] | [yes/no/possibly] | [マッピングまたはギャップ] |
| [要素 2] | [yes/no/possibly] | [マッピングまたはギャップ] |

**Literal 読み:** [すべての要素がマップ / 1 つ以上の要素が明確にマップしない / クレーム解釈が要素 [Y] で dispositive]

**Doctrine of equivalents(フラグのみ):** [要素 [Y] で DOE 読みが plausible — 弁護士構築が必要 / 表面化した要素で plausible でない / 出願履歴が estoppel を示唆]

**間接 / divided 侵害(フラグのみ):** [読みが induced、contributory、または divided 侵害理論に依存するか — 弁護士分析必要]

*(選択した各特許の各独立クレームについて繰り返す。)*

## オープン・クエスチョン

- [質問 1]
- [質問 2]

## シグナル(確認された特許ではない)

- [競合出願 / NPE 活動 / SEP 宣言 / 技術空間の訴訟 — 各々、より hard に検索する理由で、識別された特許ではない]

## 推奨される次のステップ

- [特許カウンセルによる完全な FTO 研究 — 検索が何も見つけず包括的検索がすでに実行された場合を除き、第一線の推奨]
- [Literal 読みが見つかったら design-around 選択肢]
- [カウンセルが指示するライセンス / IPR / PGR / リスク承知の分析]
- [`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` 別ルーティング —
  プラクティスプロファイルで名指された特許 OC]

## Willfulness 注記

このトリアージは特定特許を表面化します。この知識の後、さらなるカウンセル・レビュー
なしで製品を進めることは、willfulness 発見と § 284 下の enhanced damages をサポート
しうる。前進パスは特許カウンセルが文書化すべき; ローンチ、design-around、または
ライセンスのビジネス判断は、本トリアージではなく、正式な FTO 意見とカウンセルの
判断に informed される。

## 引用検証

本メモの各特許番号、クレーム引用、日付、出願事実は、依拠する前に権威ソース
(USPTO PatentCenter / PAIR、EPO 登記簿、国内同等)に対して検証する必要があります。
クレーム引用は最も一般的なエラー部位 — 1 単語が分析を変える。開けない結果を
引用しないこと。
```

---

## 非弁護士ゲート

出力を発行する前に、`## Who's using this` を読む。Role が非弁護士なら:

> この出力は research トリアージで、法的助言ではありません。このトリアージ
> 単独に基づき、製品をローンチ、販売継続、または投資することは、特許侵害の
> 厳格責任、willfulness のための enhanced damages を含む法的帰結を伴います。
> あなたが動く前に特許カウンセルが評価する必要があります。
>
> 弁護士に持参するブリーフ — 会話の時間を削ります:
>
> [1 ページ・サマリーを生成: 製品記述、スコープ内法域、実行した調査(と検索
> されなかったもの)、表面化した特許とクレームチャート初回パスの読み、
> オープン・クエスチョン、弁護士に聞く 3 つの質問]
>
> あなたの法域で認可された弁護士、ソリシター、バリスター、他の認可された
> 法専門家を見つけるなら: 米国特許業務には、登録特許弁護士または特許代理人
> が必要(すべての弁護士が登録されているわけではない — USPTO Office of
> Enrollment and Discipline がディレクトリを維持)。他の法域では、関連特許庁
> 登記簿を使用(EPO、UK IPO など)。あなたの専門職規制当局のリファラル・サービス
> が出発点(米国の州弁護士会、英国イングランド&ウェールズの SRA/Bar Standards
> Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの
> 法曹会、またはあなたの法域の同等機関); 具体的に登録特許カウンセルを依頼。

ブリーフと一緒に完全なトリアージ・メモを配信。分析を withhold しないこと。
トリアージ自体が特権付き研究文書であり、非弁護士第三者に転送すべきでないとフラグ。

---

## 出力場所

案件ワークスペースが有効でアクティブ案件があるなら、出力を
`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/outputs/fto-triage-<subject-slug>-YYYY-MM-DD.md`
に書く。さもなくば
`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/outputs/fto-triage-<subject-slug>-YYYY-MM-DD.md`
に書き、パスを表面化。

案件がアクティブなら、案件の `history.md` に 1 行エントリーを追加。

---

## next-steps decision tree で締めくくる

CLAUDE.md `## Outputs` の next-steps decision tree で締めくくる。本スキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト分岐(draft the X、escalate、get more facts、watch and wait、something else)は出発点で、ロックインではない。ツリーが出力で、弁護士が選びます。

## このスキルが行わないこと

- **FTO 意見を出す.** 決して。プラグイン中最大の声のガードレール。
- **クレームを構築する.** 構築が dispositive な場所では、用語と両 plausible 構築をフラグ。1 つを選ばない。
- **有効性を裁定する.** 既知の PTAB 手続を注記しうる; 新規性、進歩性、§ 112、§ 101、または enablement について意見を述べない。
- **特許クレームをドラフトする.** 本プラグインはそこには行かない; 出願カウンセルにルート。
- **損害賠償エクスポージャーを評価する.** 損害賠償モデリングは専門家の仕事。
- **営業秘密または商標分析を扱う** — 正しいモードで `/ip-legal-ja:infringement-triage` を使う。
- **出力を相手方または非特権オーディエンスに引用する.** これは特権付き研究文書。

---

## トーン

技術的に precise。要素単位。すべてのフラグはクレーム要素または既知の特許に specific。本文に hedging プロースなし — 冒頭と末尾のガードレールがスコープ作業を行い、分析が分析を行う。読者は、トリアージが何を見て、何を見ず、次のステップが何かを知って去るべき。
