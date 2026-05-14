---
name: build-guide
description: >
  クリニックの指導弁護士がプラクティスエリア別ガイドを作成するのを支援します。
  学生向けスキルの挙動を構成 — インテイク質問、教育ポスチャー(assist /
  guide / teach)、レビューゲート、クロスプラグインチェック、ローカルルール。
  指導弁護士がプラクティスエリア別ガイドを作成/改訂したい、クリニック種別に
  合わせてスキルの挙動を調整したい、または教育哲学をプラグイン構成として
  設定したいときに使用します。
  (Use when a supervising attorney wants to build or revise a per-practice-area
  guide, tune how the clinic skills behave for their clinic type, or set their
  teaching philosophy as plugin configuration.
   日本語トリガー: ガイド作成、プラクティスエリア別ガイド、指導方針設定、ペダゴジー設定)
argument-hint: "[任意: プラクティスエリア — 例: 'immigration'、'housing']"
---

# /build-guide

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → 役割(Supervising attorney であること必須)、プラクティスエリア、法域。
2. 下記のワークフローを使用。
3. ユーザーが指導弁護士でない場合は停止しリダイレクト(学生は `/legal-clinic-ja:ramp` を実行)。
4. ウォークスルー: プラクティスエリア → インテイク質問 → 教育ポスチャー → レビューゲート → クロスプラグインチェック → ローカルルール。
5. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` を書き出す。必要に応じて `guides/` ディレクトリを作成。
6. テスト実行を提案 — 構成されたポスチャー下で `/legal-clinic-ja:draft` を実行し、指導弁護士が学生の見るものを体験できるようにする。

```
/legal-clinic-ja:build-guide
```

複数のガイドを持つことは問題ありません — プラクティスエリアごとに 1 つ。改訂はこのコマンドを再実行。クイック変更にはガイドファイルを直接編集。

---

# Build Guide: 指導弁護士が作成するプラクティスエリア別ガイド

## 目的

指導弁護士ガイドは、学生向けスキルを「作業をこなす」から「学生に作業の仕方を教える」へと切り替えるダイヤルです。本プラグインの各学生向けスキルは、出力を生成する前にガイドを読みます: インテイクは指導弁護士が望む質問を尋ね、ドラフトスキルは教育ポスチャー(assist / guide / teach)を選び、レビューゲートは指導弁護士が関心を持つ項目について指導弁護士にルーティングし、クロスプラグインチェックは他プラグインのスキルを監督層でラップします。

本スキルは、指導弁護士がそのガイドを 1 つのプラクティスエリアあたり 5 ~ 10 分で作成するのを助けます。ガイドはよく知られたパスにあるプレーンマークダウンで、いつでも手で編集できます。

**対象: 指導弁護士。** 学生ではありません。学生は `/legal-clinic-ja:ramp` を実行し、その後学生向けスキルを実行 — ガイドを作成しません。

## Work-product header

このスキルの出力は学生成果物ではなく、指導弁護士向け構成アーティファクトです。本スキルの出力には `[AI-ASSISTED DRAFT — requires student analysis and attorney review]` を付与 **しません** — そのラベルは学生出力用です。このスキルが書くガイドファイルは指導弁護士の構成文書であり、プラグイン構成ディレクトリの CLAUDE.md と同列に位置します — 案件ワークスペース内ではありません。

## ガイドで扱うべき主な事項

指導弁護士がスキップしながら使える、またはインタビューの目次として使えるチェックリスト:

- 学生が案件に触れる前に知っておくべきこと(倫理規則、秘密保持、権限の範囲)
- このプラクティスエリアで学生がよく犯す上位 3 ~ 5 のミスとスキルがそれをどう捕捉すべきか
- 学生がいつ停止して指導弁護士の承認を得るべきか(提出、クライアント送付、表明、戦略助言)
- クライアントコミュニケーションの読解レベル(6 年生レベルが法律扶助の典型ターゲット)
- 学生全員が知るべきローカルルール、フォーム、または期限
- スキルが教えるべきとき vs. 実行すべきとき(文書種別ごと — デフォルトを設定し、種別ごとにオーバーライド可能)

インタビュー冒頭にチェックリストをウォークスルーし、指導弁護士が何が来るか把握し、既に強い見解を持っている項目と考えたい項目をフラグできるようにします。指導弁護士が手を振る項目はスキップ; ガイドに「指定なし — スキルはデフォルトを使用」と注記。

## ワークフロー

### Step 1: 役割確認

これは指導弁護士向けスキル。`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → `## Who's using this` → Role を読みます。役割が "Supervising attorney" でない場合:

> This skill is for supervisors — it configures how the student-facing skills behave. If you're the supervisor, make sure your practice profile role is set to "Supervising attorney" in `/legal-clinic-ja:cold-start-interview`. If you're a student, this isn't the right skill for you — run `/legal-clinic-ja:ramp` to onboard, or ask your supervisor to author a guide for your clinic.
> (本スキルは指導弁護士向け — 学生向けスキルの挙動を構成します。あなたが指導弁護士の場合は、プラクティスプロファイルの役割が `/legal-clinic-ja:cold-start-interview` で「Supervising attorney」に設定されていることを確認してください。学生の場合、これは正しいスキルではありません — `/legal-clinic-ja:ramp` を実行してオンボーディングするか、指導弁護士にガイドを作成してもらってください。)

役割が指導弁護士でなければ停止。

### Step 2: どのプラクティスエリアか?

> What clinic is this guide for? (Immigration / Housing / Family / Transactional / Criminal defense / Consumer / Other)
> (このガイドはどのクリニック用?(移民/住居/家族/トランザクション/刑事弁護/消費者/その他))

答えが "Other" の場合、短い名前を尋ねる — その名前がファイル名になる(小文字、ハイフン区切り: `immigration-removal-defense.md`、`transactional-nonprofit.md` など)。

`CLAUDE.md` → `## Clinic profile` → Practice areas のリストを確認。選ばれたプラクティスエリアがリストになければ注記: 「I'll write this guide, but your practice profile doesn't list [area] as one of your clinic's practice areas. That's fine — you can add it later with `/legal-clinic-ja:cold-start-interview --redo` — but the student-facing skills won't route intakes to this area until the profile lists it.(ガイドは書きますが、プラクティスプロファイルに [area] がクリニックのプラクティスエリアとして列挙されていません。問題ありません — `/legal-clinic-ja:cold-start-interview --redo` で後から追加できます — ただし、プロファイルに列挙されるまでは学生向けスキルはこのエリアにインテイクをルーティングしません。)」

既に `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` にガイドがある場合、提案: 「A guide for [area] already exists at [path]. Do you want to (a) revise it section-by-section, (b) start fresh and overwrite, or (c) see what's there first?([area] のガイドが [path] に既にあります。(a) セクションごとに改訂、(b) ゼロから上書き、(c) 先に既存内容を見る、のどれにしますか?)」

### Step 3: インテイク質問

> What should students ask a new client for this clinic type? I'll start with a generic intake for [practice area] — tell me what to add, remove, or change. What red flags should students look for? What makes a case a good fit for your clinic vs. a referral out?
> (このクリニック種別で学生が新規クライアントに尋ねるべきことは? [practice area] の汎用インテイクから開始 — 追加・削除・変更を教えてください。学生が探すべきレッドフラグは? このクリニックが受任すべき案件 vs. 紹介すべき案件の判断基準は?)

プラクティスエリアの汎用インテイクのデフォルトを表示 — `client-intake` が使うのと同じデフォルト(Immigration: 在留資格、入国、過去の申請、出身国情勢、家族、犯罪歴、タイムラインの緊急性。Housing: 住居種別、何があったか、賃貸契約、居住適合性、タイムライン。Family: 関係、論点、子ども、安全性、命令、聴聞会。Consumer: 債務種別、連絡、書類、提訴、期限)。それら以外のプラクティスエリアでは、指導弁護士にゼロからインテイクを記述してもらう。

キャプチャするもの: 追加する質問、削除する質問、言い換える質問、レッドフラグ(リスト)、良い適合の基準(クリニックが受任 vs. 紹介する案件の判断基準)。

### Step 4: 教育ポスチャー(Pedagogy posture)

> How much should the skills do vs. how much should the student do?
> (スキルが行うべき量 vs. 学生が行うべき量は?)
>
> - **Guide(デフォルト):** スキルが構造を生成、学生が実体を埋め、スキルがフィードバック。バランス型 — ほとんどのクリニックはここから始める。
> - **Assist:** スキルが成果物を生成、学生がレビュー・編集で学ぶ。最速、最も教育的でない。高ボリュームクリニックや期限が厳しいときに良い。
> - **Teach:** スキルは成果物を生成しない — 学生がドラフトし、スキルがソクラテス的フィードバックを返し、2 回の試行後にのみモデルを示す。最も遅く、最も教育的。セミナースタイルのクリニックや学習が主目的のときに良い。
>
> 文書種別ごとに設定可能(例: クライアントレターには teach、ファイルメモには assist)。

プラクティスエリアのデフォルトポスチャーと、文書ごとのオーバーライドをキャプチャ。スキルが読む文書別設定:

- `pedagogy_posture_default: assist | guide | teach`
- `pedagogy_posture_client_letter: [override]`
- `pedagogy_posture_memo: [override]`
- `pedagogy_posture_draft: [override]`

指導弁護士がスキル群が現在持たない文書種別を名前で挙げた場合、`pedagogy_posture_other:` ブロックに意図したポスチャーと注記を記録 — 将来のスキルが読める。

### Step 5: レビューゲート

> Which work product needs your review before it goes to a client? Which can students send directly? Default: everything client-facing needs review.
> (どの成果物がクライアント送付前にあなたのレビューを必要とするか? 学生が直接送ってよいものは? デフォルト: クライアント向けはすべてレビュー必要。)

選択肢を指導弁護士が埋めるテーブルとして提示:

| 成果物 | ゲート |
|---|---|
| インテイクサマリー | [学生が書く; 指導弁護士が case rounds でレビュー / クライアントが見る前に指導弁護士がレビュー / 学生が保持] |
| メモ(内部) | [指導弁護士がレビュー / 学生が保持] |
| クライアントレター(予約 / 書類請求 / 簡易ステータス) | [指導弁護士がレビュー / 学生が直接送付] |
| クライアントレター(実体的助言 / 悪いニュース) | [常に指導弁護士 — オーバーライド不可] |
| 提出ドラフト(裁判所 / 行政機関) | [常に指導弁護士 — オーバーライド不可] |
| 裁判所向けステータス更新 | [常に指導弁護士 — オーバーライド不可] |
| Research-start ロードマップ | [学生が直接これを使う] |

一部のゲートは交渉不能: 実体的助言を与えるクライアントレター、裁判所への提出書類、裁判所へのステータスは、クリニックの監督構造により常に指導弁護士を経由する。それらを固定としてフラグ; 構成可能なゲートは定型的なもの。

### Step 6: クロスプラグインチェック

> Do you want students to use skills from other plugins (defined-terms checks, doc consistency, section references, research verification)? I can wrap them in supervision — the student runs the check, the output flags uncertainty for your review, nothing goes out without your sign-off.
> (学生に他プラグインのスキル(定義語チェック、文書整合性、セクション参照、リサーチ検証)を使わせますか? 監督でラップできます — 学生がチェックを実行、出力は不確実性をあなたのレビュー用にフラグ、あなたの承認なしには何も出ない。)

プラクティスエリアに紐づいた具体例を提示:

- **トランザクションクリニック:** `commercial-legal-ja:review`(NDA トリアージ、ベンダーレビュー)をラップし、学生がレビュー実行、出力はクライアント送付前に指導弁護士レビュー用にフラグ。
- **移民クリニック:** `litigation-legal-ja:chronology` でクライアント文書からタイムラインを構築、提出書類に供給する前に指導弁護士レビュー用にフラグ。
- **住居クリニック:** クライアントが召喚状を持参した場合の `litigation-legal-ja:subpoena-triage`、学生が対応プランをドラフト、指導弁護士が承認するようラップ。
- **あらゆるクリニック:** 学生が個人データをクリニック外で共有する案件を扱う場合の `privacy-legal-ja:triage`。

指導弁護士が希望するクロスプラグインスキルを指名したら記録: スキル名、学生が使う状況、適用される監督ラッパー(常にレビュー、フラグされたときのみ、指導弁護士なしには決して)。

### Step 7: ローカルルールと法域

> What court(s) does your clinic practice in? Any local rules or forms students need to use?
> (クリニックはどの裁判所で活動? 学生が使う必要があるローカルルールやフォームは?)

`CLAUDE.md` → `## Jurisdiction` を確認 — 州と主要裁判所はコールドスタートで既に設定済み。このステップはプラクティスエリア固有のローカルルール・フォーム用(例: 「Housing Court の summary process answers に関する standing order」「USCIS のローカルフィールドオフィス向け提出住所」「Family Court の自助センターのフォームと取得場所」)。学生向けスキルがドラフトや助言時に使うべき短いポインターリストを記録すると提案。

### Step 8: ガイドを書く

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` に書き出す。`guides/` ディレクトリが存在しない場合は作成。次の構造を使用:

```markdown
# Practice-area guide: [Practice area]

*Authored by the supervising attorney via `/legal-clinic-ja:build-guide`. Student-facing skills read this before producing output. Edit directly anytime.*

**Last updated:** [date]
**Authored by:** [supervising attorney name from CLAUDE.md]

---

## Intake

**Questions to ask** (supplement/replace the generic defaults):
- [question 1]
- [question 2]
- ...

**Red flags** (surface these in the intake summary if present):
- [flag 1]
- [flag 2]

**Good-fit criteria** (cases this clinic takes):
- [criterion 1]
- [criterion 2]

**Refer-out criteria** (cases this clinic does not take):
- [criterion 1]
- [criterion 2]

---

## Pedagogy posture

`pedagogy_posture_default: [assist | guide | teach]`

Per-document overrides (optional):
- `pedagogy_posture_client_letter: [assist | guide | teach]`
- `pedagogy_posture_memo: [assist | guide | teach]`
- `pedagogy_posture_draft: [assist | guide | teach]`

**Rationale:** [指導弁護士からこのポスチャーの理由を 1 ~ 2 文で — 次学期の指導弁護士が選択を理解する助け]

---

## Review gates

| Work product | Gate |
|---|---|
| Intake summary | [gate] |
| Memo (internal) | [gate] |
| Client letter — routine | [gate] |
| Client letter — substantive | supervisor (fixed) |
| Draft filing | supervisor (fixed) |
| Court-facing status | supervisor (fixed) |
| Research roadmap | [gate] |

---

## Cross-plugin checks

| Skill | When students use it | Supervision wrapper |
|---|---|---|
| [plugin:skill] | [状況] | [ラッパー] |

---

## Local rules and jurisdiction

**Court(s):** [CLAUDE.md からまたは本プラクティスエリア用の追加裁判所]
**Practice-area-specific local rules and forms:**
- [ポインター 1]
- [ポインター 2]
```

指導弁護士の回答からすべてのセクションを埋める。指導弁護士がそう言った場合のみセクションを空のままにする — 内容を発明しない。

その後、指導弁護士に伝える:

> Your guide is at `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md`. Every student who uses the clinic plugin for [practice area] will have skills that follow it. Edit the file directly to change anything, or re-run `/legal-clinic-ja:build-guide` to revise a section. You can have multiple guides — one per practice area.
> (ガイドは `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` にあります。[practice area] のクリニックプラグインを使う各学生のスキルがこれに従います。何かを変更するにはファイルを直接編集するか、セクションを改訂するには `/legal-clinic-ja:build-guide` を再実行します。複数のガイドを持てます — プラクティスエリアごとに 1 つ。)

### Step 9: テスト実行を提案

> Want to see how the pedagogy posture changes the experience? I'll run `/legal-clinic-ja:draft` with a sample client letter under [posture] — you'll see what the student sees.
> (教育ポスチャーが体験をどう変えるか見たいですか? [posture] でサンプルクライアントレターの `/legal-clinic-ja:draft` を実行します — 学生が見るものをご覧いただけます。)

指導弁護士が yes と言えば、書き上げたばかりのガイドを読みドラフトスキルが構成されたポスチャー下で出力する流れをシミュレート。学生が見るものと同じものを 1 サイクル分通す。

## 出力

スキルの「出力」は `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` に書かれるファイル。指導弁護士との会話がインタビューで、書かれたガイドがアーティファクト。

書き込み後、簡潔な確認を表示:

> **Guide written.** `[practice-area]` is now configured:
>
> - Intake: [N] custom questions, [N] red flags, [N] refer-out criteria
> - Pedagogy: [posture default], with overrides for [list if any]
> - Review gates: [summary of what routes to supervisor vs. student]
> - Cross-plugin: [N] skills wired in
>
> Students will see these changes the next time they run a clinic command for this practice area. Edit `[path]` anytime to change anything, or re-run `/legal-clinic-ja:build-guide` to revise.
> (ガイドが書かれました。`[practice-area]` が構成されました: Intake: [N] のカスタム質問、[N] のレッドフラグ、[N] の紹介基準 / Pedagogy: [ポスチャーデフォルト]、[該当時にリスト] のオーバーライド付き / Review gates: [指導弁護士 vs. 学生のルーティングサマリー] / Cross-plugin: [N] のスキル接続。学生は次回このプラクティスエリアのクリニックコマンドを実行したときにこれらの変更を見ます。何かを変更するには `[path]` を直接編集するか、`/legal-clinic-ja:build-guide` を再実行してください。)

## このスキルが行わないこと

- **プラグインのグローバル設定。** ガイドはプラクティスエリア単位。プラグイン全体設定(監督スタイル、法域、プラクティスエリア)は `/legal-clinic-ja:cold-start-interview`。
- **学生成果物の作成。** これはクライアント向けドラフトではなく、指導弁護士向け構成。
- **コールドスタートの監督スタイルのオーバーライド。** 監督モデル(formal queue / configurable flags / lighter-touch)はセットアップで設定。ガイドのレビューゲートはそのモデルをプラクティスエリア用に精錬する; 置き換えるものではない。
- **学生スキルが AI 支援ヘッダー、信頼度フラグ、検証プロンプトをスキップするようにする。** これらは共有ガードレールのベースライン。ガイドはポスチャーを変えるが、ガードレールは変えない。
