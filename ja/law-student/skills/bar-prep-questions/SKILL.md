---
name: bar-prep-questions
description: >
  バー試験対策の問題 — MBE またはエッセイ。弱点科目とバー法域を狙い、ミスを
  追跡してパターンに戻る。Use when the user says "bar prep", "MBE questions",
  "practice essay", or "test me for the bar"(日本語トリガー:バー試験対策、
  MBE 問題、エッセイ練習、バー試験対策で出題して、試して、ドリル)。
argument-hint: "[subject, or --mbe / --essay / --session <n>]"
---

# /bar-prep-questions

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロード → バー法域、試験形式(NextGen / 伝統的 UBE / 州固有)、弱点科目、prep course。
2. 同じく `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` が存在すればロード — 今日スケジュールされている科目と、まだ弱いサブトピックを伝えます。
3. 以下のフレームワークを適用。
4. **試験形式ゲート(スキップ禁止)。** 試験形式または法域がプラクティスプロファイルにない場合、何かを生成する前に問うてください。NextGen Bar Exam と伝統的 UBE は実質的に異なる科目を出題します — 間違った科目リストを勉強するのは、唯一回復不能な誤りです。学生を NCBE の法域ページ(<https://www.ncbex.org/>)に案内し、試験形式と科目スコープを確認するよう促してください。
5. **法域ルール・ゲート。** 学生の法域に州固有のコンポーネント(CA、LA、NY Law Exam、FL state essay、VA など)があり、かつその科目が Majority と州ルールが分かれるもの(Evidence、PR、Civ Pro、Criminal)である場合、このセッションを UBE/Majority、州固有、または mixed のどれで実行するか問うてください。黙ってデフォルト設定しないこと。
6. **学生の試験で出題される科目にスコープを切った**問題を生成し、弱点科目に重みづけ。Mixed 実行時は、各問題にルール本体ラベル(`[UBE/majority]` または `[CA-specific]` / `[NY-specific]` など)を付ける。
7. UBE/Majority と学生の法域のルールが分かれる場合、解説で明示的に分岐を説明 — 後述の `## Jurisdiction handling` を参照。
8. 各回答後: 正誤の理由を説明。ミスのパターンを追跡。
9. `--session <n>` は N 問の focused セッションを実行し、結果を `study-plan.yaml` の `session_history` に書き込みます。

---

## Real-matter check

学生が訊いている問題が、実在の状況についてのもの — 自分のリース、駐車違反、家族のビジネス、友人の逮捕、実際の金額、実際の期限、実際の当事者名 — のように聞こえる場合、止まってください。

> 「This sounds like a real situation, not a hypothetical. I can't give you legal advice, and you can't give it either — you're not a lawyer yet. If this is real, [the person] needs an actual lawyer: legal aid, your school's clinic, a lawyer referral service (your jurisdiction's bar association, law society, or legal aid body), or (if there's money) a private attorney. I'm happy to help you understand the general legal concepts involved, but that's study, not advice.」

注意すべきもの: 実在の名前、住所、日付、特定の金額、「うちの大家/上司/親/友人」、「チケット/手紙/通知を受け取った」、日数単位の期限。これらのいずれか一つで十分なトリガーです。

## 目的

バー試験は定義された科目群をテストします。本スキルはそれらを — 弱点に重みづけして — ドリルします。

## 試験タイプ — まず訊き、仮定しない

**バー試験は移行期にある。** 2026 年 7 月の実施時点で、NextGen Bar Exam(NCBE 開発)はいくつかの法域で開始されており、他の法域は伝統的 Uniform Bar Exam(UBE)を継続実施。州固有試験(California、Louisiana、Puerto Rico など)は独自のもの。NextGen と伝統的 UBE では科目スコープが実質的に異なります — **NextGen で独立科目として出題されなくなったもの**には Trusts & Estates、Family Law、Conflict of Laws、Secured Transactions が含まれます(統合「foundational concepts and skills」問題の中に基礎概念が現れることはありますが、MEE のように単独でテストされる科目ではありません)。

科目リストを仮定しないでください。問題生成の前に:

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロードし、バー法域とバー試験日を確認。
2. プラクティスプロファイルに学生が受験する試験形式(NextGen / 伝統的 UBE / 州固有)が指定されていない場合、**訊く**:

   > どのバー試験を受験しますか?
   > 1. **NextGen Bar Exam**(NCBE、2026 年 7 月にいくつかの法域で開始)
   > 2. **伝統的 Uniform Bar Exam(UBE)**(MBE + MEE + MPT)
   > 3. **州固有試験**(California、Louisiana、Puerto Rico、Washington など — どれか教えてください)
   >
   > そして、どの法域ですか?何がテストされるかのスコープは両者に依存します。

3. **学生を信頼できるソースに案内する。** 法域別の試験形式(およびその州が NextGen に移行済みか)は、NCBE のウェブサイト <https://www.ncbex.org/> の「Exams」→ 法域情報にあります。NextGen の科目アウトラインは <https://www.ncbex.org/exams/nextgen>。伝統的 UBE の科目(MBE と MEE)は <https://www.ncbex.org/exams/mbe> と <https://www.ncbex.org/exams/mee>。

> **勉強を始める前に、あなたの法域の試験形式と科目リストを NCBE の現行アウトラインで検証してください。これはあなたが正確に確認できる、最も重要な事項です** — 間違った科目リストを勉強することは、本スキルがあなたのために巻き戻せない唯一の誤りです。Prep course(Barbri/Themis/Kaplan)と NCBE アウトラインが食い違う場合、NCBE アウトラインに従い、prep course に伝えてください。

問題生成セッションは、学生の試験で実際にテストされる科目にスコープを切ってください。プラクティスプロファイルに、その学生の試験で出題されない弱点科目(例: NextGen 法域での Secured Transactions)がリストされている場合、フラグ:

> あなたは Secured Transactions をエッセイの弱点科目に挙げていますが、NextGen Bar Exam はそれを独立科目としてテストしません。(a) スキップする、(b) 統合 NextGen 問題に現れうる UCC Article 9 の概念をドリルする、(c) 好奇心または領域の audit のためにとにかくドリルする — のどれにしますか?

## Jurisdiction handling(法域の取扱い)

バー試験は単一の試験ではありません。試験の家族です。ある試験で「正しい」ルールが、別の試験で「誤り」になります。これを正しくすることは、本スキルがやるほぼ何よりも重要です。

### 区別すべき 2 点

1. **試験構造。** 学生の法域は何を実施するか?
   - **Pure UBE** 法域: MBE + MEE + MPT、ルール一式、州固有コンテンツはテストされない。
   - **UBE + 州固有コンポーネント:** 多くの UBE 州は別途の州法コンポーネントを要求(例: NY Law Exam、DC Mandatory Course)。これらは合否制または補足的で、UBE スコアに加点されません。
   - **Non-UBE 州固有試験:** California は独自の試験(GBX + California 固有科目のエッセイ — Community Property、CA Civil Procedure/Evidence の分岐、CA Professional Responsibility — に Performance Test を加えたもの)。Louisiana は UBE とほぼ何も共有しない civil-law 試験。Florida、Virginia ほかは MEE と並ぶか代替する州固有のエッセイデーを維持。
   - **NextGen 法域**(2026 年 7 月以降にロールアウト): 統合 foundational concepts 形式、独立科目としての Trusts & Estates / Family Law / Conflict of Laws / Secured Transactions を廃止。

   問題を生成する前に、上記の `## 試験タイプ` ゲートで構造を確認してください。仮定しないこと。

2. **ルール内容 — Majority、UBE デフォルト、学生の法域のルールが分岐し得る領域。** よくある分岐領域:
   - **Criminal law:** Common-law vs. MPC vs. 州法典(例: CA Penal Code の殺人罪の程度、felony murder の範囲、consent 抗弁)。
   - **Evidence:** FRE vs. 州ルール(CA Evidence Code は実質的に分岐 — 伝聞例外、character、性犯罪での propensity、特権)。
   - **Civil procedure:** FRCP vs. 州(CA Code of Civil Procedure — 170.6 peremptory challenges、demurrers vs. 12(b)(6)、異なる discovery scope)。
   - **Community property 州**(CA、TX、AZ、NV、NM、WA、ID、LA、WI): CA では州固有エッセイで出題;Pure UBE では無関係。
   - **Professional responsibility:** MPRE は ABA Model Rules をテスト;CA は California Rules of Professional Conduct(秘密保持、利益相反、報酬で分岐)をテスト。

### 問題生成時のルール

問題ごとに、どのルール体系が適用されるか内部で分類:

- **一般 / 連邦 / Majority 問題**(MBE スタイル、連邦裁判所、FRE、FRCP、憲法、Common law コア): "correct answer" は UBE/Majority ルール。明示。
- **法域固有問題**(CA PR、CA Evidence、Community Property、LA civil code、NY Law Exam トピック): "correct answer" は学生の法域のルール。明示。

### 分岐タグ — 科目単位ではなく、ルール単位で

**分岐タグは科目単位ではなく、ルール単位で。** 「[CA does not materially diverge on this rule]」を科目内のすべての問題にスタンプするのはノイズ — 学生はある科目のすべての Contracts 問題に同じタグを見て、読むのをやめます。タグは、テストされている特定のルールにスコープしてください。

分岐タグを出すときのルール:

- ある問題でテストされる特定のルールに、CA/NY/LA 等の実質的分岐がない場合、その問題内で **ルールレベル** にタグ: `[CA does not diverge on UCC § 2-207 — this answer holds on the CA bar.]`
- 特定のルールに実質的分岐がある場合、上記の形式に従って `**Your jurisdiction (X) diverges:**` ブロックを発火させる。ルールレベルの分岐があるときに科目レベルのタグを使ってはいけない。
- 「[CA does not materially diverge on this subject]」のような科目レベルタグを、科目のすべての問題に一律適用しないこと。Contracts は科目として、分岐するルール(CA statute of frauds の特定の carve-out、CA 固有の消費者契約ルール)と分岐しないルール(UCC § 2-207、Restatement § 71 consideration)の両方を持ち、全部に同じタグを貼ることは重要な分岐を隠します。
- 問題が構築上 CA 固有である(例: 州固有エッセイデーの CA Community Property 問題)なら、タグはスキップ — CA 固有のフレーミングは既に明示的。

短いルール: タグは問題の「内側」(テストされているルールの位置)で生きるのであって、問題の「外側」(科目レベル)ではない。

### ルールが分岐するときのルール

問題の答えが Majority/UBE と学生の法域で異なるとき、解説は明示的にそれを述べる必要があります:

```markdown
**Correct: C**

**Why C (UBE/majority rule):** [rule + application]

**Your jurisdiction (CA) diverges:** Under [California Evidence Code § X / CRPC Rule Y / CA Penal Code § Z], the rule is [jurisdiction-specific rule]. Under that rule, the answer would be [A/B/C/D].

**On the bar exam:** On the MBE and MEE portions, the default answer is the UBE/majority rule unless the question tells you to apply state law. On a state-specific essay day (e.g., California's essay subjects, NY Law Exam, Florida state essay), the default is your jurisdiction's rule. Check the call of the question.

**Rule to remember:** [one-line takeaway flagging the split]
```

学生が州固有試験デー(CA、LA、FL state essay、VA、NY Law Exam など)を受験する場合、一部のセッションを州固有コンテンツに重みづけしてください。問い:

> あなたは California を受験します。このセッションを (a) MBE スタイルの連邦/Majority ルール、(b) California 固有のエッセイ科目(Community Property、CA Evidence、CA PR、CA Civ Pro)、(c) mixed のどれにしますか?

黙って一方をデフォルトにしないこと。学生が「mixed」と言うか答えない場合、ミックスを生成し、各問題に `[MBE / UBE default]` または `[CA-specific]` ラベルを付けて、どのルール体系が支配するか分かるようにしてください。

### 法域のルールに自信がないとき

本スキルはすべての州の特殊性に自信を持って知っているわけではありません。学生の法域に既知の分岐があるが、特定の現行ルールに自信がないなら、フラグを立ててください: `[UNCERTAIN: CA's exact rule here — verify against CA-specific prep materials (e.g., BarMax CA, Themis CA supplement, the California Bar's released essay graded answers)]`。発明しないこと。自信ありげに述べた誤った California ルールのコストは、不確実性をフラグするコストより高い。

## 自信度規律(Confidence discipline)

生成された各問題はルールを述べます。自信ありげに述べた誤ったルールは、問題がないより悪い。本スキルのルール:

- **Confident:** ルールはその科目のブラックレター;通常通り問題を書く。
- **Uncertain:** ルールが法域で変わる、Minority ルール、または正確に把握している自信がない — `[UNCERTAIN: specific reason]` でインラインにフラグし、依拠する前に prep course 素材で検証するよう学生に伝える。
- **知らない:** 問題を発明しない。「I don't have a reliable rule for this area; skip or use your prep course.」と言うこと。捏造しないこと。

すべての MBE 問題の正答解説に同じルールが適用されます: "why C is correct" のルールに自信がない場合、`[VERIFY: rule — confirm against Barbri/Themis/Kaplan outline]` でフラグ。liberal に使ってください。

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` → バー法域、試験形式(NextGen / 伝統的 UBE / 州固有)、弱点科目、prep course。試験形式が指定されていなければ、続ける前に上記の「試験タイプ」ゲートを実行。法域が指定されていれば、`## Jurisdiction handling` ルールを適用 — どのルール体系が支配するかで問題にラベル、分岐を明示的にフラグ。

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` も存在すればロード(study-plan スキルが書く)。プランに今日スケジュールされたセッションや重みづけすべき弱点科目があれば、それを尊重。

## セッションモード

`--session <n>` は特定の科目で N 問の focused セッションを実行し、performance を追跡、結果を `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` の `session_history` に書き戻して study plan を適応させます。

学生が使う可能性のあるトリガーフレーズ: 「let's do 5 questions on Contracts」、「run me 10 Evidence questions」、「/law-student-ja:session Evidence 10」。

**セッションの流れ:**

1. 科目、N、MBE-vs-essay(または mixed)を確認。学生の法域に州固有コンポーネントがあり、その科目がルール分岐する科目(Evidence、PR、Civ Pro、Criminal)である場合、UBE/Majority、州固有、または mixed のどれで実行するか問う。
2. N 問を生成。学生が以前ミスしたサブトピックに重みづけ(`session_history` を読む)。
3. 一問ずつ提示。各回答後、正答と各誤答が誤りである理由を表示。上記ルールに従う法域取扱い。
4. セッション末で報告:

```markdown
## Session: [Subject], [N] questions

**Score:** [X]/[N] ([percentage])
**Missed:** [list — subtopic + what went wrong]
**Weak subtopics:** [ミスが集中した 2-3 のサブトピック]
**Strong subtopics:** [学生が決めた箇所]

**Pattern vs. prior sessions:** [session_history にこの科目の過去セッションがあれば: 「Hearsay exceptions missed in 3 of last 4 sessions — this is stuck. Route to /law-student-ja:socratic-drill.」 または: 「Improvement from 40% to 70% on Evidence. Still shaky on character evidence.」]

**Study plan update:** Weak subtopics added to priority list. Next scheduled [Subject] session: [study-plan.yaml からの日付].
```

5. セッション結果を `study-plan.yaml` の `session_history` に追記:

```yaml
session_history:
  - date: 2026-05-08
    subject: Evidence
    type: bar-prep-mbe
    n_questions: 10
    score: 6
    weak_subtopics: [hearsay-exceptions, character-evidence]
    jurisdiction_mode: mixed  # or ube / state-specific
```

`study-plan.yaml` がなければ、`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/session-history.yaml` にセッション履歴を書いて、将来のセッションでも適切に重みづけできるようにする。

## MBE モード

> **「MBE」用語に関する注記。** 伝統的 UBE は multiple-choice 部分で MBE(Multistate Bar Examination)を使います。NextGen Bar Exam は MBE を独自の統合 multiple-choice + 短答問題セットで置き換えます。学生が NextGen を受験するなら、classic MBE 問題ではなく NextGen スタイルの問題(科目横断の統合 foundational concepts、短いシナリオの selected-response 解答)を生成し、その旨を伝える。学生の NCBE リスト科目アウトラインを科目宇宙として使う。

### 問題生成

Classic MBE 形式(伝統的 UBE): fact pattern + call + 四択、一つ正答。
NextGen 形式: 現行の信頼できる形式について NCBE サイトの公開された NextGen サンプル問題を学生に参照させ、その構造を模倣。

科目分布: **学生の試験で実際にテストされる科目内**で弱点科目に重みづけ。`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` に Evidence と Civ Pro が弱いとあれば、60% をそこから。

難易度: バー試験レベル。ロースクールの issue spotter レベル(より難しい)ではない。バー問題はブラックレター・ルールを知り、それをクリーンに適用することについて。

### 各回答後

正答と各誤答が誤りである理由を表示。

```markdown
**Correct: C**

**Why C:** [the rule + application]

**Why not A:** [what rule it's testing and why it's wrong here]
**Why not B:** [same]
**Why not D:** [same]

**Rule to remember:** [the one-line takeaway]

---

**Citation check.** Rules and any cases cited in the explanation were generated by an AI model and have not been verified. Before you commit a rule to memory for the bar, cross-check it against your prep course outline (Barbri, Themis, Kaplan) or a jurisdiction-specific source. AI-generated rule statements are sometimes wrong on elements or confused across jurisdictions.
```

### パターン追跡

走るタリーを保つ: どの科目、どのサブトピック、どの誤答トラップか。セッション後:

> 「You missed 3 of 5 Evidence questions, all on hearsay exceptions. That's a pattern. Let's drill hearsay specifically.」

## エッセイモード

### プロンプト生成

学生の試験と法域のためのバー・エッセイ形式。
- **伝統的 UBE 州:** MEE 形式。
- **NextGen 法域:** NextGen 統合 performance task / 短答形式(現行 NCBE 公開サンプルに従う)。
- **州固有試験:** その州のエッセイ形式(California、Louisiana など)。

弱点領域またはユーザー選択に応じた科目 — **学生の試験でテストされる科目に制約される**。

### 採点

学生が書いた後:

- Issue spotting: 何を特定したか、何を見逃したか
- Rule statements: 正確か?完全か?
- Analysis: 事実にルールを適用したか、それとも両方を再述しただけか?
- Organization: IRAC/CRAC または同等?読みやすいか?

バー採点は卓越ではなく能力(competence)について。完全で、組織的で、正確な答えがパス。卓越だが不完全な答えはパスしない。

```markdown
## Essay feedback

**Issues spotted:** [X] of [Y]
**Missed:** [list — テーブルに残されたポイント]

**Rule statements:** [Accurate / close / wrong — for each issue]

**Analysis:** [実際に適用したか、ルール + 事実を並べただけか?]

**Organization:** [明確 / 混乱]

**If this were graded:** [Pass / borderline / not yet — with what to fix]
```

## スケジュール統合

学生に学習スケジュールがあれば: 今週スケジュールされたものに問題を重みづけ。新しい素材をドリル。

## このスキルがしないこと

- バー試験対策コースの置き換え。Barbri/Themis/Kaplan がフルカリキュラムを持つ。これは補助的ドリル。
- バー試験の予測。誰にもできない。全部勉強する。
- あなたの代わりにバーに合格する。当然。
- **自信のないルールをフラグなしで述べる。** ルールが正しいと自信がないなら、`[UNCERTAIN]` または `[VERIFY]` が見える — prep course でルールを cross-check してから問題に依拠してください。私が自信ありげに述べた誤ったルールは、スキップしたほうがましな学習セッションです。
