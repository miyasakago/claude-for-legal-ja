---
name: memo
description: >
  リサーチギャップをフラグした IRAC 骨組みの案件分析メモ — 骨組みであって
  分析ではありません。Rule ブロックは RESEARCH NEEDED、Application は
  STUDENT ANALYSIS プロンプト、Conclusion は空。学生が案件分析メモを
  骨組み化したい、分析を書き起こしたい、または案件用の IRAC メモを構築したい
  ときに使用します。
  (Use when a student needs to scaffold a case analysis memo, write up their
  analysis, or build an IRAC memo for a case.
   日本語トリガー: メモ、IRAC、案件分析、骨組み)
argument-hint: "[任意: フォーカスする具体的論点]"
---

# /memo

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → プラクティスエリア、法域。
2. 下記のワークフローを使用。インテイクサマリー/案件ノートを読む。
3. 論点を質問として枠付け。各論点に IRAC を骨組み — Rule ブロックは RESEARCH NEEDED、Application は STUDENT ANALYSIS プロンプト、Conclusion は空。
4. 強み/弱み/オープン質問。リサーチギャップサマリー。
5. 顕著な「分析は君のもの」ラベル付きで出力。

```
/legal-clinic-ja:memo
```

---

# Memo: 内部案件分析

## 目的

案件分析メモは学生の思考が宿る場所。本スキルは IRAC 骨組みを提供し、リサーチギャップをフラグする — 学生が分析を埋める。

**分析は学生のもの。** このスキルは構造化する; 結論しない。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → プラクティスエリア、法域、監督スタイル。
事実はインテイクサマリーと案件ノートから。

## 教育ポスチャーチェック

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` の指導弁護士ガイドを読む。`pedagogy_posture` 設定を確認:

- **`guide`(デフォルト):** IRAC 構造とリサーチギャップリストを生成。フレームワークを与える代わりに、学生にリサーチから各 rule statement を自分でドラフトするよう求める。書いたものについてフィードバック。学生が一度試した後にのみセクションのフレームワーク rule の埋め込みを提案。
- **`assist`:** メモ骨組みを生成し、埋められるものを埋める。学生レビュー用にアイテムをフラグ。学生は編集を通じて学ぶ。(注: 本メモスキルは設計上常に `[STUDENT ANALYSIS]` と `[STUDENT CONCLUSION]` ブロックを空のままにする — `assist` はスキルが IRAC 骨組みとフレームワーク rule statement を生成することを意味し、application や conclusion を生成しない。)
- **`teach`:** フレームワークや骨組み内容を生成しない。学生に論点を枠付け、リサーチから rule を述べ、application を行うよう求める。フィードバックを与える。詰まっているときは誘導質問。2 回の試行後にのみ、彼らが詰まっているセクションだけのモデル rule statement またはモデル application paragraph を表示。学生が正解/不正解だったものを追跡し、指導弁護士が進捗を見えるようにする。

ガイドが存在しなければ `guide` を使用。ガイドが存在するがポスチャーが設定されていない場合も `guide`。

どのポスチャーでも、出力には常に: 「**Pedagogy mode: [assist/guide/teach]** — set by your supervisor's guide. This means I [学生が行ったこと vs. スキルが行ったことの説明].」

## ワークフロー

### Step 1: 論点を枠付け

インテイクサマリーと案件ノートから: この案件が呈する法的質問は何か?

各論点を **質問** として記述。「habitability」ではなく — 「Can the client assert a habitability defense to the eviction based on the broken heater, and if so, does it offset the rent owed?(クライアントは壊れたヒーターに基づき立退きに対して habitability の抗弁を主張できるか、できるなら未払賃料を相殺するか?)」

複数の論点があれば、それぞれが独自の IRAC ブロックを得る。

### Step 2: IRAC を骨組み

各論点について:

**Issue:** 質問として記述(Step 1 から)。

**Rule:** これはリサーチギャップであって結論ではない。学生が見つける必要があるものを記述:

> `[RESEARCH NEEDED: [State] habitability doctrine — warranty of habitability
> elements, what conditions qualify, remedies available including rent offset.
> Start with: [State] landlord-tenant statute, then case law on heater/heat
> specifically. See /research-start for a roadmap.]`

スキルが一般的なルールフレームワーク(例: 「ほとんどの州は黙示の居住適合性保証を認める」)に高い自信を持つ場合、それをフレームワーク開始点として述べる — **しかし明示的に未検証としてマーク**:

> *Framework (unverified — confirm for [State]):* Most jurisdictions recognize
> an implied warranty of habitability requiring landlords to maintain
> conditions fit for human occupation. Breach may give rise to rent withholding,
> repair-and-deduct, or rent abatement.
> `[VERIFY: [State]'s specific elements and remedies]`

**Application:** ここに学生の分析が入る。骨組みを構造化し、埋めない:

> `[STUDENT ANALYSIS: 規則を事実に適用。対応すべき主要事実:
> - ヒーターが 11 月から壊れている — どれだけが「不合理」か?
> - クライアントが家主に通知した [いつ? どう? 文書化されている?]
> - 家主の応答または不在
> - [State] 固有: クライアントは書面通知を与える必要があったか?
>   賃料をエスクローに預ける必要があったか? 他の手続要件は?]`

重要な事実を列挙。適用は学生に行わせる。

**Conclusion:** 明示的に空:

> `[STUDENT CONCLUSION: 上記のリサーチと分析に基づき、可能性の高い結果は何か?
> この抗弁はどれだけ強いか? 弱点は何か?]`

### Step 3: 強み、弱み、オープン質問を特定

IRAC ブロックの後の別セクション:

**Strengths(事実から明らかなもの — 学生はこれをテストすべき):**
- [有用そうな事実とその理由]

**Weaknesses(事実から明らかなもの — 学生は深刻さを評価すべき):**
- [有害そうな事実とその理由]
- `[UNCERTAIN: [X] が実際に弱点かどうか — [Y] に関する [State] のルールに依存]`

**Open questions(さらなる情報なしにはメモが答えられないこと):**
- 事実: [クライアントから知らないこと]
- 法律: [リサーチが必要なもの]
- 戦略: [学生/教授の判断]

## 出力

```markdown
═══════════════════════════════════════════════════════════════════════
  AI-ASSISTED SCAFFOLD — THE ANALYSIS IS YOURS TO WRITE
  Every [RESEARCH NEEDED] and [STUDENT ANALYSIS] block is a prompt, not
  a placeholder to delete. The thinking happens when you fill them in.
═══════════════════════════════════════════════════════════════════════

# Case Analysis Memo: [Client] — [Matter]

**Date:** [date] | **By:** [student] | **For:** [Professor]

---

## Bottom line

[Take the case / Decline because X / Need more info on Y — next step is Z]

---

## Issues Presented

1. [Issue as question]
2. [Issue as question]

---

## Issue 1: [Issue]

### Rule

[VERIFY フラグ付きのフレームワーク開始点、および RESEARCH NEEDED ブロック]

### Application

[重要な事実付きの STUDENT ANALYSIS 骨組み]

### Conclusion

[STUDENT CONCLUSION — 空]

---

[各論点について繰り返し]

---

## Strengths

[caveat 付きリスト]

## Weaknesses

[該当時は UNCERTAIN フラグ付きリスト]

## Open Questions

**Factual:** [リスト]
**Legal:** [リスト — これらは /research-start に供給]
**Strategic:** [リスト — これらは Professor との議論用]

---

## Research gaps summary

[各 RESEARCH NEEDED ブロックを 1 リストに抽出。学生が体系的に処理でき、
各項目で /research-start を実行できる]

═══════════════════════════════════════════════════════════════════════

## What this memo is NOT

これは骨組みであって分析ではない。[STUDENT ANALYSIS] ブロックが教育的価値の宿る場所 — それを埋めるのが仕事。それらが空のままのメモは、まだ書かれていないメモ。

---

**Cite verification — required before use.** 上記で提示された任意のフレームワークルール、判例、法令は AI モデルによって生成されており、検証されていない。任意の引用に依拠する前に — またはクライアント業務に含める前に — 正確性と現行 good-law ステータスについて Westlaw、CourtListener、またはクリニックのリサーチプラットフォームで確認。未検証引用は指導弁護士にフラグ。

**Source attribution.** 骨組み内の各提案引用に、出所をタグ付け: 法律リサーチコネクターから取得した引用には `[Westlaw]`、`[CourtListener]`、または MCP ツール名; web 検索引用には `[web search — verify]`; トレーニングデータから想起された引用には `[model knowledge — verify]`; 指導弁護士または案件ファイルから提供された引用には `[user provided]`。`verify` タグ付き引用は、ツール取得引用より高い fabrication リスクを持ち、最初にチェックすべき。タグを剥がしたり折りたたんだりしない — それらはどの引用を検証すべきかについての指導弁護士への最速シグナル。

**No silent supplement.** 設定済みリサーチツールへのクエリが、メモが必要とするルールに対してほとんどまたは結果ゼロを返す場合、そう述べて停止。尋ねずに web 検索またはモデル知識からギャップを埋めないこと。述べる: 「The search returned [N] results from [tool]. Coverage appears thin for [rule / issue]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) leave `[RULE TO VERIFY]` and stop. Which would you like?」 指導弁護士が低信頼度ソースを受け入れるかを決める。
```

## このスキルが行わないこと

- **分析を書く。** IRAC を骨組みし、ギャップをフラグする。学生が application を推論する。
- **検証済みルールを提供する。** 各 rule statement は学生がリサーチするまで明示的に未検証。
- **結論に到達する。** IRAC の C は意図的に空。
- **教授との会話を置き換える。** Open Questions / Strategic セクションはその会話の議題であって、代替ではない。

## 次のステップのデシジョンツリーで締めくくる

CLAUDE.md `## Outputs` に従って次のステップのデシジョンツリーで終わる。このスキルが今生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X、escalate、get more facts、watch and wait、something else)は出発点であって固定ではない。ツリーが出力であり、弁護士が選ぶ。
