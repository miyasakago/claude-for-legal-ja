---
name: research-start
description: >
  法的論点のリサーチロードマップ — チェックする法令、調査する判例領域、規制
  フレームワーク、Westlaw 検索語。リードとフレームワークであって、典拠
  引用ではありません; 学生がすべてを検証・発展させます。学生がリサーチの
  起点を尋ねたとき、論点のリサーチロードマップを欲するとき、または
  既存リサーチのギャップを特定したいときに使用。
  (Use when a student asks where to start researching, wants a
  research roadmap for an issue, or needs gaps identified in existing research.
   日本語トリガー: リサーチ起点、research roadmap、調査開始、リサーチギャップ)
argument-hint: "[法的論点]"
---

# /research-start

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → 法域、プラクティスエリア。
2. 下記のワークフローを使用。
3. 論点を具体的に枠付け。ロードマップを構築: 法令の出発点(未検証)、判例領域(判例ではない)、二次ソース、検索語。
4. 学生が既存リサーチをアップロードしている場合: 統合し、ギャップを特定。
5. 顕著な「typeは典拠ではない」ヘッダー付きで出力。すべては学生が検証する出発点。

```
/legal-clinic-ja:research-start "habitability defense to nonpayment eviction in [State]"
```

---

# Research Start: リサーチではなくロードマップ

## 目的

法的リサーチは臨床教育に不可欠。しかし初期段階 — *何を* リサーチすべきか、正しい法令を見つけ、フレームワークを理解することを把握する段階 — はしばしば最も時間がかかり、最も教育的でない部分。学生は実際のリサーチを始める前に、出発点を見つけるのに何時間も費やす。

このスキルは出発点を生成する: チェックする法令、調査する判例領域、Westlaw と CourtListener の検索語。**何も検証されていない。何も典拠ではない。すべて学生が追跡するリードである。**

**これは倫理的セーフガードであるだけでなく、教育的セーフガードでもある。** 学生はリサーチを依然学ぶ。ただ、より良い場所から始められるだけ。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → 法域(州)、プラクティスエリア。

## ワークフロー

### Step 0: まずシード文書

**ロードマップを構築する前に、クリニック自身のシード文書を読む。** 指導弁護士がコールドスタートでアップロードしたもの(handbook、filing guides、local court rules、intake forms、example case files、prior memos) — これらは事前検証済み、法域固有で、学生のリサーチ最初の 20 分について Westlaw クエリに勝つ。

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → `## Seed documents` を読む。リサーチエリアと用途またはファイル名が一致する任意の項目を特定(例: UD habitability 問題のための「Alameda UD filing guide」、同じプラクティスエリアの redacted サンプル案件ファイル、同じ問題の prior メモ)。
2. 各マッチについて、ロードマップ出力のトップに **Seed documents to read first** ブロックとして浮上させる。ファイルに名前を付け、なぜそれがこの特定の質問に重要かを言い、それがおそらくカバーすること vs. 外部リサーチが依然必要な場所を述べる。
3. 論点にマッチするシード文書がなければ、率直に述べる(「No clinic seed documents match this issue — proceeding straight to primary sources」)。マッチを fabricate しない。
4. クリニックが `## Seed documents` で `LIMITED DATA` フラグを設定している場合、1 行注記を追加: 「Clinic has fewer than 10 seed docs; your professor's precedent bank is thin — lean harder on primary sources and flag what's missing for your supervisor.」

ロードマップは依然法令、判例領域、二次ソース、検索語をカバー — シード文書は最初のリードであって、他の代替ではない。しかしすべての上に浮上させ、学生が指導弁護士の precedent が始まる場所から始められるようにする。

### Step 1: 論点を枠付け

リサーチ質問は何か? 具体的に。「eviction defenses」ではなく — 「habitability defense to nonpayment eviction in [State], specifically whether a broken heater qualifies and whether the tenant had to give written notice.」

質問が広すぎる場合、学生と狭める: 「That's three research questions. Let's take them one at a time. Which first?」

### Step 2: ロードマップを構築

**法令の出発点:**
*関連がありそうな* 法令を列挙。これらは「ありそう」であって「確認済み」ではないことを明示。

> **Likely relevant statutes** (UNVERIFIED — confirm currency and applicability):
> - [State] Landlord-Tenant Act, likely at [State Code Title X] — look for "warranty of habitability" or "repair and deduct"
> - Local housing code for [City/County] — may define specific conditions (heat, water) as required
> - `[VERIFY each citation is current and correct — codes get renumbered]`

**調査する判例領域:**
判例ではなく — *領域*。学生が判例を見つける。

> **Case law areas:**
> - [State] Supreme Court or appellate decisions on implied warranty of habitability — look for the leading case establishing the doctrine
> - Cases on what conditions qualify — heat specifically, if any
> - Cases on procedural prerequisites — did tenant have to give notice? withhold rent? escrow?
> - Cases on the remedy — offset against rent owed, or a separate damages claim?

**規制 / 行政ソース:**
該当時(特に在留資格)。

> **Administrative sources:**
> - [Agency] regulations at [CFR cite area]
> - Agency guidance or policy manuals — often more current than regs
> - For immigration: USCIS Policy Manual, BIA precedent decisions

**オリエンテーション用二次ソース:**
プライマリに飛び込む前にフレームワークを得る場所。

> **Secondary sources (for framework, not to cite):**
> - [State] practice guide on landlord-tenant (check clinic library)
> - Relevant CLE materials
> - Law review notes on the specific issue if it's contested

**検索語:**
Westlaw、またはクリニックが使うもの用。

> **Search terms to try:**
> - Westlaw: `"warranty of habitability" /s heat! & [State]`
> - CourtListener: `implied warranty of habitability AND (heat OR heater) AND [State]`
> - Refine based on what comes back — these are starting queries

### Step 3: 不確かなものをフラグ

スキルがソースの関連性または現行性について不確かな場合:

> `[UNCERTAIN: whether [State] has a specific statute on this vs. common-law
> doctrine only — the search will tell you]`

不確実性は述べる、隠さない。

> **No silent supplement.** このスキルはリードを生成し、典拠引用ではない — 設計により学生が引用を自分で追跡する。しかし設定済みリサーチツール(Westlaw、CourtListener)へのクエリが特定のルールまたは判例についてほとんどまたは結果ゼロを返す場合、そう述べて停止。尋ねずに web 検索またはモデル知識から引用を製造して薄い結果セットを埋めないこと。述べる: 「The search returned [N] results from [tool]. Coverage appears thin for [rule]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) stop here and flag the gap for your supervisor. Which would you like?」 指導弁護士が低信頼度ソースを受け入れるかを決める。
>
> **Source attribution.** 各提案引用に、出所をタグ付け: 法律リサーチコネクターから取得した引用には `[Westlaw]`、`[CourtListener]`、または MCP ツール名; web 検索引用には `[web search — verify]`; トレーニングデータから想起された引用には `[model knowledge — verify]`; 指導弁護士または案件ファイルから提供された引用には `[user provided]`。`verify` タグ付き引用は高い fabrication リスクを持ち、最初にチェックすべき。タグを剥がしたり折りたたんだりしない — それらは、どのリードが raw research でどれが一次ソースに照らして検証すべきモデルの推測かを学生に伝える。

### Step 4: アップロードされたリサーチを統合(あれば)

学生が既にリサーチを行い、アップロードしていれば: それを読み、カバーされているもの、欠けているものを特定。

> **From your research so far:**
> - You have: [カバーされていることのサマリー]
> - Gap: [上記ロードマップが提案するが、まだ見つけていないもの]
> - `[VERIFY: 引用された判例 — [name] — citator(現行 good law か検証する仕組み)を通して実行、distinguished または limited されている可能性]`

## 出力

```markdown
═══════════════════════════════════════════════════════════════════════
  RESEARCH ROADMAP — LEADS, NOT AUTHORITIES
  Nothing below is a verified citation. Every statute, every case area,
  every search term is a starting point for YOUR research. You verify
  currency, applicability, and accuracy. You find the actual cases.
  If something below turns out to be wrong or outdated, that's expected —
  this is a map of where to look, not a substitute for looking.
═══════════════════════════════════════════════════════════════════════

# Research Roadmap: [Issue]

**Jurisdiction:** [State] | **Practice area:** [area]

## Seed documents to read first

[Step 0 に従って。論点にマッチする任意のクリニックシード文書を、1 行の
「what this likely covers」注記付きでリスト。マッチがなければ: 「No clinic
seed documents match this issue — proceeding to primary sources」。]

## Statutory starting points (UNVERIFIED)

[VERIFY フラグ付きリスト]

## Case law areas to investigate

[領域、判例ではない]

## Administrative / regulatory sources

[該当時]

## Secondary sources (for framework, not citation)

[リスト]

## Search terms

**Westlaw:** [クエリ]

## Uncertainty flags

[ロードマップが本当に不確かなあらゆる場所]

---

## What to do with this

1. 二次ソースから始めてフレームワークを得る
2. プライマリ法令を見つけて読む — 上記引用が現行か確認
3. 検索を実行、leading cases を見つける
4. 依拠する前に citator を通してすべてを実行(現行 good law か検証する仕組み)
5. ルールを得たら、戻ってきて `/memo` を実行し分析を骨組み化

## What this roadmap does NOT do

- **使える引用を君に与えない。** 上記のすべての引用は検証するリードであって、依拠する典拠ではない。
- **リサーチをしない。** 君がリサーチをする。これは出発線への到達を速くするだけ。
- **Westlaw を置き換えない。** 彼らが実際の判例を持つ。これは君が彼らをどこに向けるかを伝える。

---

**Cite verification — required before use.** 上記引用は AI モデルによって生成され、検証されていない。任意の判例、法令、ルールに依拠する前に — またはクライアント業務に含める前に — 正確性と現行 good-law ステータスについて Westlaw、CourtListener、またはクリニックのリサーチプラットフォームで確認。未検証引用は指導弁護士にフラグ。
```

## このスキルが行わないこと

- **典拠引用を提供。** 明示的に、設計により。学生は使う前に各引用を検証。
- **法的リサーチを置き換える。** 「どこから始めるか」段階を加速; リサーチ自体は依然学生のもの。
- **ロードマップが完全であることを保証。** 出発点のリードセット。リサーチがロードマップが見逃したソースを明らかにするかもしれない — それは fine、それがリサーチ。

## 次のステップのデシジョンツリーで締めくくる

CLAUDE.md `## Outputs` に従って次のステップのデシジョンツリーで終わる。このスキルが今生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X、escalate、get more facts、watch and wait、something else)は出発点であって固定ではない。ツリーが出力であり、弁護士が選ぶ。
