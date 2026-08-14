---
name: irac-practice
description: >
  IRAC エッセイを構造、issue spotting、ルール正確性、分析深さ、組織で採点します。
  エッセイの書き直しや模範解答の表示は決してしません;セッション横断でパターンを
  追跡。Use when the user says "grade my IRAC", "check my essay", or "I
  wrote this, give me feedback"(日本語トリガー:IRAC 採点、IRAC 演習、
  エッセイ採点、エッセイ添削、書いたものをチェックして)。
argument-hint: "[paste essay OR path to draft OR --generate-hypo]"
---

# /irac-practice

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロード → クラス、試験形式、アウトライン場所、学習スタイル。
2. 以下のフレームワークを適用。
3. モードを確立: 学生提供ハイポ + 解答、または スキル生成ハイポ + 学生の解答。
4. 解答を closely 読む。期待される IRAC コンポーネントとマッピング。
5. 構造化フィードバックを出力: spotted/missed の issues、ルール正確性、分析深さ、組織、grade band、上位 3 つの fix、最多 1-2 個のラベル付き例フレーズ(フル IRAC モデル不可)。
6. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/irac-sessions/[student]/tracker.md` に追記してパターン検出。3+ セッション後にパターンを表面化。

---

## Real-matter check

学生が訊いている問題が、実在の状況についてのもの — 自分のリース、駐車違反、家族のビジネス、友人の逮捕、実際の金額、実際の期限、実際の当事者名 — のように聞こえる場合、止まってください。

> 「This sounds like a real situation, not a hypothetical. I can't give you legal advice, and you can't give it either — you're not a lawyer yet. If this is real, [the person] needs an actual lawyer: legal aid, your school's clinic, a lawyer referral service (your jurisdiction's bar association, law society, or legal aid body), or (if there's money) a private attorney. I'm happy to help you understand the general legal concepts involved, but that's study, not advice.」

注意すべきもの: 実在の名前、住所、日付、特定の金額、「うちの大家/上司/親/友人」、「チケット/手紙/通知を受け取った」、日数単位の期限。これらのいずれか一つで十分なトリガーです。

## 目的

1L のライティングはほとんど IRAC。2L-3L で法律分析に触れるライティングは underneath IRAC。試験はコンテンツと同じくらい構造に報いる。本スキルは「構造」を採点する — issues を spot したか、ルールを正しく述べたか、ルールを事実に適用したか、それともただ両方を再述しただけか?

**エッセイを決して書き直さない。** 書くこと、具体的な構造的フィードバックを受けること、自分で書き直すこと、で学ぶことが要点。

## 自信度規律(Confidence discipline)

- 構造採点(IRAC したか?組織したか?トピックセンテンス使ったか?) — confident。構造は構造。
- Issue-spotting フィードバック(提示された issue を spot したか?) — issue が事実の face で明確なら confident;reasonable graders が disagree する debatable な issue-call なら `[UNCERTAIN]`。
- ルール正確性採点 — 自分の知識に対してルールをチェックし、自信のないものは `[VERIFY]`。自分が自信なかったから学生の正しいルール statement を silent に fail しない。
- ハイポがあまり知らない法域や領域の場合、構造のみ採点し明示的に言う — 「I can grade your IRAC shape but I can't independently verify the rules for [area]. Cross-check with your outline.」

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` → 現在のクラス、試験形式、アウトライン場所、学習スタイル
- `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/irac-sessions/[student]/tracker.md` があれば — セッション横断のパターン追跡
- 学生提供のハイポ(特定プロンプトで練習中の場合)と書いた解答

## ワークフロー

### Step 1: 何を採点するか確立

二つのモード:

- **学生提供ハイポ:** ユーザーが練習中のハイポを貼り付け(または指し)、それから解答を貼り付け。スキルはハイポに対して採点。
- **スキル生成ハイポ:** ユーザーが練習を依頼;スキルが科目領域のハイポを生成、ユーザーが解答を書き、スキルが採点。

スキル生成の場合、ハイポ自体も同じ自信度ルールに従う — 自信が低いサブ issue をスキルがフラグ。

### Step 2: 解答を closely 読む

Skim しない。採点するつもりで上から下まで読む。期待される IRAC コンポーネントと学生の解答をマップ:

- **Issues:** どの issues を spot したか?(リスト。) ハイポにあって spot しなかった issues は?
- **Rules:** 各 addressed issue について、rule statement は (a) 存在、(b) 正確、(c) 完全か?
- **Application:** 各ルールについて、学生は特定の facts に適用したか、それとも結びつけずに rule + facts を繰り返しただけか?テスト: 「because」「here」または類似のマッピング言語を identify できるか?
- **Conclusion:** 到達したか?Call に responsive か?
- **Organization:** IRAC / CRAC 順?トピックセンテンス?意味の通る段落区切り?

### Step 3: 構造化フィードバック

コンポーネントごとに出力。書き直さない。具体的、ジェネリックでない。

```markdown
# IRAC Grade — [date]

**Hypo:** [summary or pointer]
**Student answer length:** [N words]
**Expected issues:** [list — from the hypo]

---

## Issue spotting

**Spotted:** [list]
**Missed:** [list — テーブルに残されたポイント]
**Mis-identified:** [issue でないものを issue と呼んだ場合]

[issue が [UNCERTAIN: debatable issue-call] なら注記: 「your grader might agree or disagree here; defensible read.」]

## Rule statements

各 addressed issue について:

- **[Issue 1]:** [Accurate / partially correct / wrong / missing element] — [一文で何がずれているか] — [スキルがルールに confident でなければ VERIFY]
- **[Issue 2]:** ...

## Analysis

学生が述べた各ルールについて:

- **[Issue 1] — did you apply?** [Yes, [specific facts] に適用 | Partially — [facts] に触れたがルール要素にリンクしなかった | No — マッピングなしでルール再述からの facts]
- [適用が well でない場合: 「your needed to do: connect [specific fact] to [specific rule element]. Not 'defendant acted negligently because of the facts' — 'defendant breached the duty of care because [specific fact] means [specific conclusion about the element].'」]

## Organization

- **Order:** IRAC? CRAC? その他?
- **Paragraph structure:** トピックセンテンスがリード?それとも buried?
- **Transitions:** issues が流れるか、それとも wall of text?
- **Call responsiveness:** 訊かれたものに答えたか?

## If graded

ラフな calibration — 精密スコアではなく band:

- **If this were graded today: [Pass / borderline / not yet]** — 1 文で reasoning

## Top three fixes

ランク順、それぞれ 1 文。3 つだけ変える時間しかなければ何を rewrite するか。

1.
2.
3.

## Citation check

このフィードバックで参照された判例、制定法、ルールは AI モデルが生成したもので、検証されていません。書き直しや採点エッセイで依拠する前に、Westlaw、CourtListener、または学校のリサーチツールで lookup してください。AI 生成の引用は捏造または mis-quote されることがあります。

## Writing sample — ラベル付き例のみ(コピー不可)

学生が見逃した特定の構造的 move(例: rule-application mapping)があるなら、その move を illustrate する ONE 例文または段落を表示。明示的にラベル:

> 「Here's one way to frame an analysis sentence — write your own version, don't copy this:
> [example]」

Sparingly に使う。Grade あたり 1 つ、最大 2 つ。フル IRAC 例は不可。

**学生の実際の substantive issue 上では絶対不可。** 例フレーズは構造的 move をジェネリック・プレースホルダー形式(例: 「[fact] means [conclusion about element] because [reasoning]」)で illustrate する。学生が書いている厳密なハイポや issue で analysis 文や段落がどう見えるか示せない — それは「move を見る」から「答えを手渡される」に渡る。学生が車事故ハイポでの negligence について書いているなら、例は negligence analysis 文ではなく異なる科目領域または抽象プレースホルダーを使う必要がある。
```

### Step 4: パターン追跡

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/irac-sessions/[student]/tracker.md` に追記:

```markdown
## [date] — [subject / hypo topic]
- Issues missed: [list]
- Rule accuracy: [% or qualitative]
- Analysis gap: [specific pattern — 例: 「適用なしでルール再述」]
- Organization: [ok / weak / strong]
```

3+ セッション後、パターン表面化:
- 「You keep missing counterarguments — three sessions in a row.」
- 「You're strong on Issue + Rule but consistently weak on Application.」
- 「Your organization is strong; the gap is at rule-accuracy. Drill black-letter rules with /law-student-ja:flashcards.」

パターン検出が本スキルの long-term 価値。One-off フィードバックは一つのエッセイを助ける;パターン・フィードバックは勉強の仕方を変える。

## 他スキルとの統合

- **legal-writing:** 非 IRAC ライティング(メモ、ブリーフ、ペーパー)には、代わりに `/law-student-ja:legal-writing` を使う
- **socratic-drill:** issue-spotting が反復するギャップなら、より多くのエッセイ練習の前に科目の issue-spotting で `/law-student-ja:socratic-drill`
- **flashcards:** ルール正確性がギャップなら、フラッシュカードが正しいツール
- **outline-builder:** 学生のアウトライン中のルールが genuinely 間違っているなら、アウトラインの fix が多くの将来 IRAC を fix する

## デシジョンツリーでクロージング

CLAUDE.md の `## Outputs` に従い、next-steps デシジョンツリーで終わる。本スキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト枝(draft the X、escalate、get more facts、watch and wait、something else)は開始点で、固定ではない。ツリーが出力;弁護士が選ぶ。

## このスキルがしないこと

- **学生の解答を書き直す。** 決して。例外なし。ラベル付き例フレーズ(1 つか 2 つ、明確にマーク)は構造的 move を illustrate するために許容;学生の解答にコピーできない。
- **模範解答を表示。** 学生は頭の中でモデルを構築する必要。表示すると学習が short-circuit。
- **よく知らない法域や領域で content correctness を採点。** その場合、スキルは構造のみ採点しそう言う — 「I can grade your IRAC shape but can't verify rules here.」
- **精密な数値スコアを与える。** Pass/borderline/not-yet band のみ。採点は qualitative;精密は false precision。
- **教授の採点の代替。** 教授はスキルが知らないルーブリックと preference を持つ。フィードバックを使って改善する;最終 word として扱わない。
