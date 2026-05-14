---
name: draft
description: >
  クリニックで一般的な文書の初稿 — プラクティスエリア別テンプレート(庇護申請、
  立退き答弁、保護命令、要求書)、法域認識フォーマット、明示的に学生の分析と
  弁護士のレビューを要する出発点です。学生が motion、レター、petition、
  declaration、その他クリニック文書の初稿を必要とするときに使用。
  (Use when a student needs a first draft of a motion, letter, petition,
  declaration, or other clinic document.
   日本語トリガー: ドラフト、初稿、書面ドラフト、答弁書、要求書、petition)
argument-hint: "[文書種別 — 例: 'eviction-answer'、'asylum-declaration'、'demand-letter']"
---

# /draft

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → プラクティスエリアテンプレート、法域、ローカルルール、監督スタイル。
2. 下記のワークフローを使用。
3. 文書種別をテンプレートにマッチ。案件ノートから事実を収集 — 欠けているものをフラグ、決して推測しない。
4. 法域フォーマットを適用。`[FACT NEEDED]`、`[VERIFY]`、`[UNCERTAIN]` フラグをインラインで付けてドラフト。
5. 顕著な AI 支援ラベル、学生レビューチェックリスト、監督ルーティング付きで出力。

```
/legal-clinic-ja:draft eviction-answer
```

```
/legal-clinic-ja:draft asylum-declaration
```

---

# Draft: 初稿文書生成

## 目的

学生は、教育的価値が分析と戦略にあって、キャプションのフォーマットや「Dear Judge」を書くことにはない文書の初稿に莫大な時間を費やす。本スキルは案件ノートとプラクティスエリアテンプレートから初稿を生成し、学生の時間が思考に向かうようにする。

**すべてのドラフトは明示的に出発点。** 完成成果物ではない。学生が分析・改訂し、教授が何かが出る前にレビュー。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → プラクティスエリア、プラクティスエリアテンプレート、法域(州 + ローカル裁判所 + 取り込まれたローカルルール)、監督スタイル。

事実は案件ノートまたはインテイクサマリーから。

## 教育ポスチャーチェック

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` の指導弁護士ガイドを読む。`pedagogy_posture` 設定を確認:

- **`guide`(デフォルト):** 構造とチェックリストを生成。学生に各セクションをドラフトするよう求める。彼らのドラフトについてフィードバック(レジスター、読解レベル、必須要素、見逃したもの)。学生が一度試した後にのみセクション埋め込みを提案。
- **`assist`:** 成果物を生成。学生レビュー用にアイテムをフラグ。学生は編集を通じて学ぶ。
- **`teach`:** 成果物を生成しない。学生にドラフトを求める。フィードバックを与える。詰まっているときは誘導質問。2 回の試行後にのみ、彼らが詰まっているセクションだけのモデル段落を表示。学生が正解/不正解だったものを追跡し、指導弁護士が進捗を見えるようにする。

ガイドが存在しなければ `guide` を使用。ガイドが存在するがポスチャーが設定されていない場合も `guide`。

どのポスチャーでも、出力には常に: 「**Pedagogy mode: [assist/guide/teach]** — set by your supervisor's guide. This means I [学生が行ったこと vs. スキルが行ったことの説明].」

**法域の前提。** ドラフトは CLAUDE.md で設定された州、裁判所、ローカルルールを前提とする。キャプションフォーマット、送達要件、ページ制限、提出ウィンドウ、実体的規則は、法域および同州内の裁判所間でも実質的に異なる。案件が異なる裁判所または異なる州にある場合、ドラフトのフォーマット、期限、または主張に依拠する前に指導弁護士と確認。

## ワークフロー

### Step 1: どの文書?

要求をクリニックのテンプレートセット(`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` から)にマッチ。プラクティスエリア別の一般セット:

| プラクティスエリア | 文書 |
|---|---|
| **Immigration** | I-589(庇護申請書 I-589)narrative、client declaration、motion to change venue、motion to continue、FOIA request、country conditions summary |
| **Housing** | Eviction answer、demand letter(repairs/deposit)、motion to stay execution、discovery requests |
| **Family** | Protective order petition、custody declaration、motion to modify、financial affidavit |
| **Consumer** | Debt validation letter、FDCPA(米国公正債権回収業務法)demand letter、answer to collection complaint、motion to vacate default |
| **General litigation** | Motion template、notice of appearance、certificate of service |

要求された文書がテンプレートセットにない場合: 「The clinic's templates don't include [X]. I can attempt a draft from general principles, but flag this heavily — it hasn't been tuned for your practice area or jurisdiction. Better to ask [Professor] if there's an existing template.」

### Step 2: 事実を集める

インテイクサマリーまたは案件ノートを読む。文書が必要とする各事実について: 持っているか?

| 文書が必要とするもの | 持っているか? | ソース |
|---|---|---|
| [fact] | ✓ / ✗ | [intake / client doc / need to get] |

必要な事実が欠けている → 推測しない。マーク: `[FACT NEEDED: client's entry date — get from I-94 or ask client]`。

### Step 3: 法域を適用

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の法域に従う:

- **キャプションフォーマット:** 州とローカル裁判所規則。コールドスタートでローカルルールが取り込まれていれば使う。なければ州デフォルトを使ってフラグ: `[VERIFY CAPTION: local rules not loaded — confirm format against [Court]'s current rules]`
- **送達要件:** 裁判所規則に従って誰に、どのように、いつまでに送達するか
- **ローカルの癖:** ページ制限、フォント要件、standing orders。取り込まれているものを適用; 取り込まれていないものをフラグ。

### Step 4: ドラフト

プラクティスエリアテンプレートを使用。事実から埋められるものを埋める。プレースホルダーを明示的に残す — もっともらしく聞こえる発明で埋めない。

**ドラフトが法的主張をする場所すべて:** その主張は学生が検証する仮説であり、ドラフトが保証する結論ではない。それに応じてマーク。

### Step 5: 不確実性をフラグ

3 種類のフラグ、インラインで:

- `[FACT NEEDED: ...]` — 文書が、案件ノートが持たない事実を必要とする
- `[VERIFY: ...]` — 提出前にチェックが必要な法的または事実の主張
- `[UNCERTAIN: ...]` — スキルが本当に不確かで、推測せずそう言う

### Step 6: 監督ルーティング

裁判所または行政機関に文書を提出することは重大なアクション。ゲートは `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の `## Supervision style` の監督ワークフローで、ライセンスを持つ指導弁護士がクリニックセットアップを所有することを確認する Part 0 役割チェックで補強される。裁判所提出は、監督スタイル選択に関係なく、常に提出前に監督を経由。

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の監督スタイルに従う:
- **Formal queue:** ドラフトはキューに、学生は「queued for [Professor]」を見る
- **Configurable flags:** この文書種別がフラグトリガーなら(裁判所提出は通常そう)、出力には「CHECK WITH [PROFESSOR] BEFORE FILING」を含める
- **Lighter-touch:** 標準セーフガードラベル、追加ゲートなし — ただし裁判所提出はクリニックの既存監督構造に従い、提出前に教授に行く

## 出力

```markdown
═══════════════════════════════════════════════════════════════════════
  AI-ASSISTED DRAFT — REQUIRES STUDENT ANALYSIS AND ATTORNEY REVIEW
  This is a starting point, not final work product.
  Every [VERIFY] and [FACT NEEDED] flag must be resolved before filing.
═══════════════════════════════════════════════════════════════════════

[文書 — プラクティスエリアテンプレートフォーマット、法域認識、
フラグはインライン]

═══════════════════════════════════════════════════════════════════════

## Student review checklist

[Professor] に見せる前に:

- [ ] 全体を読む。言いたいことを言っているか?
- [ ] 各事実: インテイクノートでなく、クライアントの実際の書類に照らして正確か?
- [ ] 各 [VERIFY] フラグ: リサーチで解決または削除
- [ ] 各 [FACT NEEDED] フラグ: 検証された情報で埋まる、またはセクション削除
- [ ] 法理論: これが正しい主張か? より良いものはあるか?(それは君の分析であって、ドラフトのではない。)
- [ ] 法域: 現行ローカルルールに照らしてキャプション、送達、フォーマットが正しい
- [ ] [CLAUDE.md スタイル下の監督ステップ]

## What this draft does NOT do

- 戦略は決定しない。ドラフトはこの文書種別の最も一般的なアプローチに従う — それがこのクライアントに正しいかは君が決める。
- 自身の法的主張を検証しない。上記の各法的結論は君がリサーチするまで仮説。
- 自分で提出しない。[Professor] がレビュー、君がクリニック手順に従って提出。

---

**Before this leaves the clinic.** これは指導弁護士レビュー用の学生ドラフトであって、完成したレター、提出書類、フォームではない。裁判所または行政機関への提出、またはクライアントや相手方への送付は、クライアントにとって法的結果を持つ。ライセンスを持つ指導弁護士がレビュー、編集、サインオフしてからクリニックを出る。そのサインオフ後にのみ AI 支援ドラフトヘッダーを剥がす。指導弁護士の承認なしには送付または提出しない。

*ABA Formal Opinion 512(2024): 生成 AI 利用には能力、監督、検証が必要。
このドラフトは監督・検証されるよう設計されている — その伴いなく
信頼されるよう設計されていない。*
```

## このスキルが行わないこと

- **完成成果物の生成。** 初稿のみ。学生が改訂、教授がレビュー。
- **欠けた事実の推測。** 学生が取得するためにフラグ。
- **法理論の決定。** 一般的アプローチを使う; 学生がそれがこの案件に正しいか決める。
- **法域固有リサーチの置き換え。** 取り込まれたローカルルールを適用; 規則が取り込まれていない、または変わっている可能性をフラグ。
