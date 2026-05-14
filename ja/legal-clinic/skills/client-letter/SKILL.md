---
name: client-letter
description: >
  テンプレートからの定型クライアント・コレスポンデンス — アポイント確認、書類請求、
  簡易な「提出しました」進捗。プレーンランゲージ、必須要素、監督ルーティング。
  実体的助言ではありません。学生が定型コレスポンデンス、アポイント確認、
  書類請求レター、クライアントへの簡易ステータス通知を送る必要があるときに使用。
  (Use when a student needs to send routine correspondence, an appointment
  confirmation, a document request letter, or a brief status note to a client.
   日本語トリガー: クライアントレター、アポイント確認、書類請求、定型コレスポンデンス)
argument-hint: "[appointment | doc-request | update]"
---

# /client-letter

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → プレーンランゲージ基準、監督スタイル、クリニック連絡先。
2. 下記のテンプレートとワークフローを使用。
3. 種別をテンプレートにマッチ。プレーンランゲージチェック。
4. AI 支援ラベルと監督ルーティング付きで出力。

スコープ: 定型のみ。実体的助言 → `/status client` または教授との会話。

```
/legal-clinic-ja:client-letter appointment
```

```
/legal-clinic-ja:client-letter doc-request
```

---

# Client Letter: 定型コレスポンデンス

## 目的

クリニックは多くの定型コレスポンデンスを送る: 「火曜 2pm にアポイントメント」「賃貸契約を持参してください」「答弁書を提出しました」。このスキルはそれらをテンプレートから処理し、学生が毎週同じレターをタイプし直さないようにする。

**スコープ: 定型のみ。** 実体的助言、悪いニュース、案件戦略 — それらは `/status client` または会話であって、テンプレートレターではない。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → プレーンランゲージ基準、監督スタイル、クリニック連絡先。

## 教育ポスチャーチェック

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` の指導弁護士ガイドを読む。`pedagogy_posture` 設定を確認:

- **`guide`(デフォルト):** 構造とチェックリスト(必須要素、プレーンランゲージ目標、学生実務規則に基づくサインオフ)を生成。学生に各セクションをドラフトするよう求める。彼らのドラフトについてフィードバック(レジスター、読解レベル、必須要素、見逃したもの)。学生が一度試した後にのみ、セクションの埋め込みを提案。
- **`assist`:** レターを生成。学生レビュー用にアイテムをフラグ。学生は編集を通じて学ぶ。
- **`teach`:** レターを生成しない。学生にドラフトを求める。フィードバックを与える。詰まっている場合は誘導質問。2 回の試行後にのみ、彼らが詰まっているセクションだけのモデル段落を表示。学生が正解/不正解だったものを追跡し、指導弁護士が進捗を見えるようにする。

ガイドが存在しなければ `guide` を使用。ガイドが存在するがポスチャーが設定されていない場合も `guide`。

どのポスチャーでも、出力には常に: 「**Pedagogy mode: [assist/guide/teach]** — set by your supervisor's guide. This means I [学生が行ったこと vs. スキルが行ったことの説明].」

## サインオフと学生・弁護士の開示

学生(law student / certified legal intern)が署名するレターにおいて、所在法域の学生実務規則が要求する開示文言を確認。一部の法域は特定の様式を要求し、ほとんどは学生が自身を法学生/認定法務インターンとして識別し、指導弁護士を識別することを要求する。下記のテンプレートは汎用形式 — 送付前に所在法域の規則に合わせてサインオフを整える。

## レター種別

> **レビューラベルはレターの外側に置く。** `[AI-ASSISTED DRAFT — requires review per plugin config supervision step]` タグは学生への注記であって、レター本文の一部ではない。レンダリングされたテンプレートの上(または学生が送付前に削除するヘッダー)に置き、決して fenced レターコンテンツ内に置かない。クライアント向けコピーに残っていれば、スキルは失敗。

### Appointment confirmation(アポイント確認)

*学生向けレビューラベル(クライアント向けではない — 送付前に剥がす):*
`[AI-ASSISTED DRAFT — requires review per plugin config supervision step]`

```markdown
Dear [Client],

This confirms your appointment with [Clinic name]:

**Date:** [date]
**Time:** [time]
**Where:** [address / room / or "by phone at [number]"]
**With:** [student name]

**Please bring:** [必要書類 — 案件ノートからまたは学生が埋めるプロンプト
として残す]

If you need to reschedule, call us at [clinic phone] at least 24 hours before.

[Student name]
Law Student, Certified Legal Intern
Under the supervision of [Supervising Attorney]
[Clinic name] | [phone] | [hours]
```

### Document request(書類請求)

*学生向けレビューラベル(クライアント向けではない — 送付前に剥がす):*
`[AI-ASSISTED DRAFT — requires review per plugin config supervision step]`

```markdown
Dear [Client],

To move your case forward, we need the following documents from you:

- [Document 1 — 例: 「賃貸契約書」]
- [Document 2 — 例: 「家主から受け取った通知」]
- [Document 3]

**How to get them to us:** [drop off at clinic / email to [address] / bring
to next appointment]

**Please send by:** [date — 期限がある場合は理由を述べる: 「We need these
by [date] so we can file your answer before the court deadline.」]

If you don't have some of these or aren't sure what we mean, call us at
[clinic phone] and we can help.

[Student name]
Law Student, Certified Legal Intern
Under the supervision of [Supervising Attorney]
[Clinic name] | [phone] | [hours]
```

### Brief status update(簡易ステータス更新)

定型の「提出しました」/「待機中」の更新用。(より完全なステータス更新 → `/status client`。)

*学生向けレビューラベル(クライアント向けではない — 送付前に剥がす):*
`[AI-ASSISTED DRAFT — requires review per plugin config supervision step]`

```markdown
Dear [Client],

Quick update: [1 行で起きたこと — 「We filed your answer with the court
on [date]」 / 「We sent the demand letter to your landlord on [date]」]。

**What's next:** [1 行 — 「We're waiting for their response」 / 「The court
will schedule a hearing and let us know the date」]。

You don't need to do anything right now. We'll let you know when we do.

[Student name]
Law Student, Certified Legal Intern
Under the supervision of [Supervising Attorney]
[Clinic name] | [phone] | [hours]
```

## 送付前

クライアントにレターを送ることは consequential action(重大なアクション)。本プラグインのゲートは、`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の `## Supervision style` で記述された監督ワークフローであり、ライセンスを持つ指導弁護士がクリニックセットアップを所有していることを確認する Part 0 役割チェックで補強される。そのゲートは依然有効: すべてのレターはクリニックを出る前にレビューをクリアする。

上記のレターを送る前に、確認:

1. ドラフトが `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の監督プロトコル(queue / flag / lighter-touch)に従ってレビューされている。
2. すべての内部レビューラベル(`[AI-ASSISTED DRAFT]`、`[VERIFY]`、`[FACT NEEDED]` タグなど)がクライアント向けコピーから削除されている。
3. サインオフが所在法域の学生実務規則(law-student-signed correspondence)に適合している。

**これは指導弁護士レビュー用の学生ドラフトであって、完成したレターではない。** 送付はクライアントにとって法的結果を持ち、法的助言またはクライアントを代理するコミュニケーションを構成しうる。ライセンスを持つ指導弁護士がレビュー、編集、承認してからレターはクリニックを出る。指導弁護士の承認なしには送らないこと。

## プレーンランゲージチェック

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の基準に従う。短い文。専門用語なし。読解レベル目標を強制。上記テンプレートにクライアントが知らないかもしれない法的用語が含まれる場合、初回は説明: 「We filed your 'answer' — that's the document that tells the court your side of the story.」

## 監督ルーティング

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` に従う。定型コレスポンデンスは、教授が選択した監督スタイルに応じてフラグトリガーかどうかが変わる。lighter-touch: 学生レビュー後にキューステップなしで送付。formal queue: 定型レターでもキュー。

## このスキルが行わないこと

- **実体的助言。** レターが「I think about your case...」「here's what you should do」を言うなら、それは定型ではない — それは `/status client` または教授との会話。
- **悪いニュース。** 案件クローズ、不利な判決、援助不能 — これらは熟考が必要であり、テンプレートではない。教授にフラグ。
- **相手方弁護士または裁判所への何か。** 異なる聴衆、異なるスキル(`/draft` または `/status court`)。
