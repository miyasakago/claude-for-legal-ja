---
name: status
description: >
  聴衆別の案件ステータスサマリー — クライアント向け(プレーンランゲージ)、
  内部向け(教授用)、または裁判所提出可(ローカルルールに従う正式
  キャプションフォーマット)。同じ事実、異なる枠付けと深度。学生がクライアントを
  更新する、教授に簡潔報告する、または裁判所ステータスレポートを準備する
  ときに使用。
  (Use when a student needs to update the client, brief the professor, or prepare
  a court status report.
   日本語トリガー: ステータス、status、案件進捗、裁判所ステータス、進捗報告)
argument-hint: "[client | internal | court]"
---

# /status

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → 監督スタイル、プレーンランゲージ基準、法域。
2. 下記のワークフローを使用。案件ノートを読む。
3. 指定された聴衆向けに生成:
   - `client` — プレーンランゲージ、何が起きたか/次/あなたがすること/連絡方法
   - `internal` — 手続的状態、前回チェックイン以降に行ったこと、今後、教授の入力が必要な事項、学生の評価
   - `court` — ローカルルールに従ったキャプションフォーマットの正式ステータスレポート
4. 聴衆ごとの監督ルーティング(クライアント向けと裁判所提出可は通常フラグ)。

```
/legal-clinic-ja:status client
```

```
/legal-clinic-ja:status internal
```

```
/legal-clinic-ja:status court
```

---

# Status: 聴衆認識の案件サマリー

## 目的

クリニックは膨大な数のステータス更新を生成する — クライアントに、教授に、co-counsel に、裁判所に。同じ案件、同じ事実、完全に異なる文書。本スキルは案件ノートを取り、正しい読者向けに正しいサマリーを生成する。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → 監督スタイル、プレーンランゲージ基準(クライアント向け用)、法域。
事実は案件ノートから。

## 聴衆モード

### Client-facing(クライアント向け)

**読者:** クライアント。おそらくストレスを感じている。法手続に不慣れかもしれない。読解レベルは `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` のプレーンランゲージ基準に従う(デフォルト 6 年生レベル)。

**含めるもの:**
- 前回クリニックから聞いて以降に起きたこと
- 次に何が起きるか、いつ
- 彼らがする必要があるもの(あれば)
- クリニックへの連絡方法

**含めないもの:**
- 法的分析(IRAC を知る必要はない)
- 案件の弱点(その会話の時でない限り — そしてそれは教授の判断であって、ステータス更新ではない)
- 専門用語

*学生向けレビューラベル(クライアント向けではない — 送付前に剥がす):*
`[AI-ASSISTED DRAFT — requires student review and supervision step per plugin config]`

所在法域の学生実務規則で required law-student sign-off language を確認; 一部の法域は特定の様式を要求。

```markdown
Dear [Client],

I wanted to update you on your case.

**What's happened:** [プレーン英語。「The responsive pleading was submitted」
ではなく「We filed your answer with the court on [date]」。]

**What's next:** [何と、いつ。「The court scheduled a hearing for [date] at
[time]. You need to be there.」 または: 「We're waiting for the landlord's lawyer
to respond. That could take a few weeks.」]

**What you need to do:** [具体的で明確。または: 「Nothing right now — we'll
let you know when we need something from you.」]

**How to reach us:** [Clinic phone, hours, student name]

[Student name]
Law Student, Certified Legal Intern
Under the supervision of [Supervising Attorney]
[Clinic name]
```

**送付前:** クライアントステータス更新を送ることは重大なアクション。ゲートは `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の `## Supervision style` の監督ワークフローで、ライセンスを持つ指導弁護士がセットアップを所有することを確認する Part 0 役割チェックで補強。ドラフトが監督プロトコル(queue / flag / lighter-touch)に従ってレビューされ、すべての内部レビューラベル(`[AI-ASSISTED DRAFT]`、`[VERIFY]` など)がクライアント向けコピーから削除されていることを確認。

### Internal(教授向け)

**読者:** 指導教授。法律を知っている。案件がどこに立っているか、学生が彼らから何を必要としているかを知りたい。

**含めるもの:**
- 手続的状態(案件のライフサイクル上の位置)
- 前回チェックイン以降に行われたこと
- 来るもの(期限、聴聞)
- 教授の入力が必要な論点
- 学生の評価(どう進んでいるか、懸念)

```markdown
# Status: [Client] — [Matter] — [date]

**Student:** [name] | **Procedural posture:** [pre-filing / answer filed /
discovery / motion pending / etc.]

## Since last check-in

- [行われたこと]

## Upcoming

| Date | What | Action needed by |
|---|---|---|
| [date] | [deadline/hearing] | [date] |

## Needs professor input

- [質問または決定点 — 具体的]

## Student's assessment

[どう進んでいるか。強み、懸念、戦略的質問。これは学生の
思考が表れる場所。]

---
[AI-ASSISTED DRAFT — student should revise the assessment section especially;
that's your thinking, not a summary of notes]
```

### Court-ready(裁判所提出可)

**読者:** 裁判官または書記官。正式。裁判所が必要とするもの(しばしば裁判所により命じられたステータスレポート、または status conference に先立つ陳述)に特化。

**含めるもの:**
- 手続履歴(簡潔に)
- discovery / motions / settlement の現在状態
- 未済事項
- 提案される次のステップまたはスケジューリング

**フォーマット:** ローカルルールに従う。キャプション、署名ブロック、提出する場合は certificate of service。

```markdown
═══════════════════════════════════════════════════════════════════════
  AI-ASSISTED DRAFT — requires student analysis and attorney review
  Court filings ALWAYS require professor review before filing
═══════════════════════════════════════════════════════════════════════

[法域に従ったキャプション — 現行ローカルルールに照らして VERIFY]

STATUS REPORT

[Party] respectfully submits this status report pursuant to [the court's
order of [date] / local rule [X] / in advance of the status conference
scheduled for [date]].

1. Procedural history: [簡潔]

2. Current status: [discovery status / motion status / settlement status]

3. Outstanding matters: [係属中のもの]

4. Proposed next steps: [スケジューリング、裁判所が入力を望む場合]

[署名ブロック — [Professor] の監督下の student attorney]

[提出時は certificate of service]

---

[VERIFY: キャプションフォーマット、ローカル status report 要件、送達
要件 — 現行 [Court] 規則に従って]
```

## 監督ルーティング

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` に従う:
- Client-facing → 通常フラグトリガー(クライアントコミュニケーション)
- Internal → フラグなし(どうせ教授に行く)
- Court-ready → 正式キュー有効時は常にフラグ(裁判所提出)

## このスキルが行わないこと

- **クライアントに何を伝えるかを決定。** 特に悪いニュースまたは案件の弱点 — それは学生と教授の会話であり、その後学生がクライアントと持つもの。ステータス更新はステータスであり、戦略的助言ではない。
- **裁判所に何かを提出。** 文書をドラフトする; 教授がレビュー; 提出はクリニック手順に従う。
- **内部ステータスの学生評価を置き換える。** 「学生の評価」セクションは学生の思考 — ドラフトはそれを骨組み化できるが、書くことはできない。

## 次のステップのデシジョンツリーで締めくくる

CLAUDE.md `## Outputs` に従って次のステップのデシジョンツリーで終わる。このスキルが今生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X、escalate、get more facts、watch and wait、something else)は出発点であって固定ではない。ツリーが出力であり、弁護士が選ぶ。
