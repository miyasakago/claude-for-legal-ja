---
name: cold-call-prep
description: >
  コールドコール(指名質問)の準備 — 教授が問う可能性の高い質問を予測し、
  ソクラテス式でドリル。授業前に再読すべき箇所が分かるよう、不確かな箇所を
  フラグします。Use when the user says "prep for class tomorrow", "cold call
  [case]", "what might [professor] ask on", or points at assigned reading
  (日本語トリガー:コールドコール対策、明日の授業準備、教授が訊きそうな質問、
  指名質問対策)。
argument-hint: "[case name, or paste case text, or path to reading]"
---

# /cold-call-prep

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロード → クラスリスト、教授、学習スタイル。
2. 以下のワークフローを適用。
3. 読書を特定(判例名 + 引用、教授、クラス、シラバス上の文脈)。
4. カテゴリ横断で 6〜10 個の likely な質問を予測(Facts / Holding / Reasoning / Application / Policy)、教授の既知の傾向に重みづけ。
5. ソクラテス式パターンでドリル — 訊く、待つ、プッシュバック、詰まったら narrow。答えを与えない。
6. ドリル後サマリー: strong/shaky/missed;授業前に再確認すべきもの。

---

## Real-matter check

学生が訊いている問題が、実在の状況についてのもの — 自分のリース、駐車違反、家族のビジネス、友人の逮捕、実際の金額、実際の期限、実際の当事者名 — のように聞こえる場合、止まってください。

> 「This sounds like a real situation, not a hypothetical. I can't give you legal advice, and you can't give it either — you're not a lawyer yet. If this is real, [the person] needs an actual lawyer: legal aid, your school's clinic, a lawyer referral service (your jurisdiction's bar association, law society, or legal aid body), or (if there's money) a private attorney. I'm happy to help you understand the general legal concepts involved, but that's study, not advice.」

注意すべきもの: 実在の名前、住所、日付、特定の金額、「うちの大家/上司/親/友人」、「チケット/手紙/通知を受け取った」、日数単位の期限。これらのいずれか一つで十分なトリガーです。

## 目的

コールドコールは準備で生き死にする。教授は判例を何十回も読んでいて質問を知っているが、学生は 1 回だけ読んでいる。本スキルはそのギャップを縮める — その判例に対する likely な質問パターンを予測し、学生にそれをドリルし、ロックダウンできていないものを surface する。

判例を読むことの代替ではない。読んだことを確認するテスト。

## 自信度規律(Confidence discipline)

- 学生が判例本文やケースブック抜粋を提供した場合: 実際のテキストに基づいて質問を予測。Confident。
- 学生が判例名だけを提供した場合: 判例について知っていることに基づいて予測。確信のない判例詳細に依存する質問には `[UNCERTAIN]` をフラグ。判例本文またはケースブックの treatment を貼り付けるよう強く推奨。
- 判例をよく知らない場合: そう言う。「I don't have a reliable read on this case — paste the text or casebook treatment and I can work from that. Otherwise my questions are educated guesses.」

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` → 現在のクラス、教授、学習スタイル
- ユーザー提供: 判例名 / 判例本文 / ケースブックページ / 読書リスト

## ワークフロー

### Step 1: 読書 + 教授を特定

- 判例名と引用
- 教授(`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` のクラスリストから — トーンとフォーカスは教授で変わる)
- クラス / 科目
- この判例がシラバスのどこにあるか(文脈のため — このトピックの最初の判例、絞り込み判例、反例?)

### Step 2: 質問の予測

教授は反復的なパターンでコールドコールします。次のカテゴリ横断で予測:

**Facts レベル(ウォームアップ):**
- 当事者は誰?何が起きた?Procedural posture?
- Trial court は何をした?その下の appellate court は?
- なぜこの判例はケースブックにあるか?どの科目を illustrate しているか?

**Holding / rule:**
- Holding は?1 文。
- この判例から出てくる rule は — 可搬な takeaway は?
- アウトラインに入れるとしたら、ルールをどう表現する?

**Reasoning:**
- なぜ court はこう決定したか?
- Court はどんな argument を拒否したか?
- Dissent はあったか?何を主張した?

**Application / hypos:**
- もし [fact X] が違ったら — 同じ結果?
- この判例はシラバス上の [前の判例] とどう比較される?
- Limiting principle は何?このルールはどこで止まる?

**Policy / theory:**
- Court が守っている policy は?
- このルールは意味をなすか?代替アプローチは?

**教授固有の風味(`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` のノートから):**
- 教授が hypo 中心のコールで知られているなら、Application/Hypo 質問に重みづけ
- Policy 中心なら、Policy/Theory に重みづけ
- Fact 中心のソクラテス式(『Paper Chase』スタイル)なら、Facts + Holding に重みづけ

カテゴリ横断で 6〜10 個の質問を選ぶ。最初に問われる可能性でランクづけ(Facts が通常最初、それから Holding、それから harder カテゴリ)。

### Step 3: ドリル

`socratic-drill` パターンを使用:

1. Question 1 を訊く。答えを待つ。
2. 正しく、よく推論されている場合: acknowledge、Question 2 へ。
3. 正しいが雑な場合: 流させない。「You got there, but explain — why does the court's reasoning support that?」
4. 間違っている場合: 答えを与えない。Narrowing question を訊く。「What facts does the court rely on?」 そこに歩かせる。
5. 詰まった場合: さらに narrow。「Before we go to the holding — what's the procedural posture?」
6. 本当に迷っている場合: もう一度読むよう伝える。「This is a re-read, not a guess-your-way-through. Come back when you've read it again.」

### Step 4: ドリル後サマリー

最後に:

```markdown
# Cold-Call Prep — [case] — [date]

**Questions drilled:** [N]
**Strong:** [自信を持って正しく答えた質問]
**Shaky:** [推測した、または hedge した質問]
**Missed:** [知らなかった質問]

## 明日の授業前に:
- [再確認すべき具体的なもの — 間違えた事実、述べられなかったルール]
- [policy/theory で不安なら: "read the dissent again — that's usually where policy questions come from"]

## 授業で出る可能性の高い質問:
- [10 のうち上位 3 — 教授が最初に問う可能性が最も高いもの]
```

## 統合

- **case-brief:** 学生がまだ判例をブリーフしていなければ、cold-call prep の前に `/law-student-ja:case-brief` を実行することを提案。ブリーフはコールドコール対策ツールでもある。
- **socratic-drill:** prep が(この判例だけでなく)科目の弱点を surface したら、`/law-student-ja:socratic-drill [subject]` で続ける。
- **flashcards:** 判例の rule が学生が記憶すべきものなら、フラッシュカード・デッキへの追加を提案。

## このスキルがしないこと

- **教授になる。** 実際のコールドコールはどこへでも行ける。本スキルはパターンを予測する;教授は驚かせる。
- **判例を読むことの代替。** 読んでいなければスキルは助けられない — 質問は吸収したテキストを要求する。
- **学生にまず訊かずに判例の holding を与える。** Drill-me パターン: 私が訊き、あなたが答える。
- **法域固有のニッチな質問を予測する。** 教授に既知の hobby horse があれば、`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` のクラスノートに捕捉し、スキルがそれに応じて重みづけできる;それ以外は一般的なパターンから動く。
