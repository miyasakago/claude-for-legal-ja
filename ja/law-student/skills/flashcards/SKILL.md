---
name: flashcards
description: >
  ブラックレターの暗記用にフラッシュカードを生成または出題 — Leitner 式
  バケット、科目別 markdown ストレージ、自己評価付きドリルモード。Use when the
  user says "drill flashcards", "make flashcards from", "quiz me on cards",
  or wants to memorize rules(日本語トリガー:フラッシュカード、暗記カード、
  カード出題、ルール暗記、ブラックレター暗記)。
argument-hint: "[subject] [--generate | --drill | --review | --stats | --session <n>]"
---

# /flashcards

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロード → 現在のクラス、弱点科目、アウトライン場所。
2. 以下のフレームワークを適用。
3. フラグでルーティング:
   - `--generate`: ソース(アウトラインパス、ノート、ケースブック)からカード作成ルールに従ってカードを構築。`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/flashcards/[subject]/cards.md` に書く。
   - `--drill`(デフォルト): 期日到来カード + 新カードを優先;Q を表示、答えを待つ、A を表示、自己評価を取る、バケットと次回レビューを更新。
   - `--review`: バケットでデッキをブラウズ。
   - `--stats`: 進捗スナップショット;stuck なカードを口頭ドリル用にフラグ。
   - `--session <n>`: focused N カード・セッション、過去ミス + 期日到来カードで優先;結果を `study-plan.yaml` → `session_history` に追記。
4. 自信度規律を適用: ソースなし知識から生成された各カードに `[VERIFY]` をフラグ。

---

## Real-matter check

学生が訊いている問題が、実在の状況についてのもの — 自分のリース、駐車違反、家族のビジネス、友人の逮捕、実際の金額、実際の期限、実際の当事者名 — のように聞こえる場合、止まってください。

> 「This sounds like a real situation, not a hypothetical. I can't give you legal advice, and you can't give it either — you're not a lawyer yet. If this is real, [the person] needs an actual lawyer: legal aid, your school's clinic, a lawyer referral service (your jurisdiction's bar association, law society, or legal aid body), or (if there's money) a private attorney. I'm happy to help you understand the general legal concepts involved, but that's study, not advice.」

注意すべきもの: 実在の名前、住所、日付、特定の金額、「うちの大家/上司/親/友人」、「チケット/手紙/通知を受け取った」、日数単位の期限。これらのいずれか一つで十分なトリガーです。

## 目的

アウトラインは synthesis のため;フラッシュカードは暗記のため。バー試験とほとんどのロースクール試験は速いルールリコールに報いる。本スキルはアウトライン(またはノートやケースブック抜粋)からカードを生成、light spacing でドリル、何が stuck で何がそうでないかを追跡。

**フル SRS システムではない。** シンプルな Leitner 式バケット。勉強に十分、保守に軽い。Anki が欲しいなら Anki を使う;これはチャット内で素早くドリルしたいとき用。

## 自信度規律(Confidence discipline)

他のコンテンツ生成スキルと同じルール:

- 提供されたソース(アウトライン、ノート、ケースブック抜粋)からカード生成なら、Q と A はそのソースから来る。Confident。
- ソースなしで自分の知識からカード生成なら、完全に自信のないルールを述べる各カードに `[VERIFY: rule — confirm against source]` をフラグ。ターゲットとして commit する前にチェックすべき。
- 領域をよく知らないなら、発明するより少ないカードを生成。20 のうち 5 が間違っているより、8 個の良いカードのほうがいい。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` → 現在のクラス、弱点科目、既存アウトライン
- `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/flashcards/[subject]/cards.md` が存在すれば(incremental ビルド)
- ユーザー提供のソース(アウトラインパス、ノート、ケースブック抜粋)、与えられれば

## モード

フラグ: `--generate | --drill | --review | --stats | --session <n>`(デフォルト: prompt)

### `--session <n>` — focused N カード・セッション

学生が「Contracts のカードを 5 枚やろう」と言ったり `/law-student-ja:session Contracts 5 --flashcards` を実行したとき用。

- `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` が存在すればロードし、この科目の `session_history` を読む。
- 優先: 以前 wrong マークされたカード > 期日到来カード > 新カード。
- `--drill` フローに従って N カードを一つずつ実行。
- セッション末で `study-plan.yaml` → `session_history` に結果を追記:

```yaml
session_history:
  - date: 2026-05-08
    subject: Contracts
    type: flashcards
    n_cards: 5
    right: 3
    partial: 1
    wrong: 1
    stuck_topics: [parol-evidence-rule]
```

- `study-plan.yaml` がなければ、`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/session-history.yaml` に書く。

### `--generate` — カードを作成

**入力:**
- 科目(クラス名またはトピック)
- ソース(アウトラインパス、ノート、または「`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` の既存アウトラインを使って」)
- オプション: カード数ターゲット(デフォルト セッションあたり 10〜20)

**カード構造:**

```markdown
### Card [N]
**Q:** [question — one concept, one card]
**A:** [answer — the rule, one or two sentences]
**Source:** [outline section, casebook page, class note date]
**Bucket:** new
**Last reviewed:** —
**Next review:** [today's date]
**Notes:** [optional — distinctions, exceptions, traps]
```

**カード作成ルール:**
1. **カードあたり 1 概念。** 「Negligence の要素」は 1 つでなく 4 カードになる。
2. **表は質問、トピックではない。** 「Negligence duty」は悪い。「What are the four elements of negligence?」は良い。
3. **裏はルール、段落ではない。** 答えに段落が必要なら、複数カードに分割。
4. **ソースを引用** して、ドリル中に再確認できるように。

**Citation check.** カードが貼り付けたソースではなく私の知識から生成された場合、ルールと裏に引用された判例・制定法は AI モデルが生成したもので、検証されていません。カードを暗記する前に、アウトライン、ケースブック、またはリサーチツール(Westlaw、CourtListener)で確認してください。マスタリーにドリルされた誤ったカードは、カードがないより悪い。

### `--drill` — 学習セッション

**優先順位:**
1. `next_review <= today` AND バケット != mastered のカード
2. まだ試みられていない新カード
3. 期日到来も新カードもない場合: ユーザーに mastered カードのレビューを希望するか問う(decay 防止用)

**カードあたりドリルフロー:**
1. Q 表示。答えを待つ。
2. ユーザーが答える(または「skip」/「don't know」をタイプ)
3. A 表示。
4. ユーザー自己評価: `right` / `partial` / `wrong` / `don't know`
5. 下表に従いバケット + 次回レビューを更新:

| Self-assessment | Bucket change | Next review |
|---|---|---|
| right | one up (new → learning → review → mastered) | +1d new, +3d learning, +7d review, +21d mastered |
| partial | same bucket | +1d |
| wrong | one down (review → learning; learning → new; new stays new) | today +4h |
| don't know | one down | today +4h |

### `--review` — デッキをブラウズ

科目のすべてのカードを表示。バケットでグループ化。デッキの中身をスキャンしカード内容を手動調整するのに有用。

### `--stats` — 進捗スナップショット

科目あたり: 総カード数、バケット分布、今日期日、今週レビュー済み。`new` に 2 回以上落ちたカードをハイライト — それらは口頭で `/law-student-ja:socratic-drill` でドリルする価値のある stuck 概念。

## 他スキルとの統合

- **outline-builder:** アウトライン構築・拡張後、新素材からフラッシュカード生成を提案
- **socratic-drill:** カードが 2 回以上 wrong なら、`/law-student-ja:socratic-drill` に口頭の働きかけのためにルーティング — 実際に理解していない概念にはフラッシュカードでは不十分
- **bar-prep-questions:** フラッシュカード stats の悪いバー対策科目は MBE ドリルで高く重み付け

## ストレージ

```
flashcards/
└── [subject]/
    └── cards.md
```

科目あたり 1 ファイル。カードは markdown。バケット/レビュー・メタデータはカードごとにインライン。非常に大きなデッキ(500 超)には最適でないが、典型的なロースクール・デッキ・サイズには十分。

## このスキルがしないこと

- **Anki の代替。** フラッシュカード習慣があるなら、それを続ける。これはチャット内でアプリを切り替えずにドリルしたいとき用。
- **カウントターゲットに達するためにカードを発明。** ソースから自信を持って生成できるのが 8 枚なら、8 枚を得る。`[VERIFY]` 重い推測でパディングするのは、小さなデッキより悪い。
- **学習規律を強制。** ミスしたレビュー日は複利;スキルは何が期日かを示すだけ。ドリルするかはあなたが決める。
- **ルールを教える。** カードはすでに勉強したものをドリルするため。カードが consistent に wrong なら、問題は upstream — `/law-student-ja:socratic-drill` を使うかソースを再読。
