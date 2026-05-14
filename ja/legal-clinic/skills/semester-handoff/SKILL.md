---
name: semester-handoff
description: >
  学期末の案件引継ぎメモ — /ramp のミラー。引退するコホートが入ってくる
  コホートへ作業をきれいに引き継げるよう、案件ごとの遷移メモとコホート
  サマリーを生成します。deadlines、client-comms、案件履歴を読みます。
  教授または引退する学生が学期を締めたい、遷移メモを構築したい、または
  卒業/離脱する学生をオフボーディングしたいときに使用。
  (Use when the professor or departing students need to wrap up the semester,
  build transition memos, or offboard a graduating/withdrawing student.
   日本語トリガー: 学期引継ぎ、semester handoff、コホート遷移、卒業オフボーディング)
argument-hint: "[--semester=YYYY-term(デフォルト: current)] [--case=[case_id](単一案件用)]"
---

# /semester-handoff

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → クリニックプロファイル、学期日、監督スタイル。
2. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` および案件ごとの `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/client-comms/[case-id]/log.md` を読み込む。
3. 下記のワークフローを使用。
4. アクティブ案件リストを入力として取得(クリニックが中央リストを持たない場合は尋ねる)。outgoing → incoming オーナーをマップ。
5. 案件ごとの引継ぎメモを `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/handoffs/[semester]/[case_id].md` に生成。
6. コホートサマリーを `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/handoffs/[semester]/_summary.md` に生成。
7. 監督モデルに従ってルーティング — formal queue / configurable flags / lighter-touch。

---

# Semester Handoff(学期引継ぎ)

## 目的

学期ごとに、クリニックはワークフォース全体を失い、再構築する。`/ramp` は問題の半分を解決する — 新コホートをオンボーディングする。本スキルは他の半分を解決する: 引退するコホートをオフボーディングし、各アクティブ案件について次の学生が知るべきことをキャプチャした引継ぎメモを生成する。

これなしでは、案件知識が学生と一緒にドアの外に歩いて出る。新しい学生は案件ファイルとインテイクサマリーから始めるが、それは決して十分でない。新しい学生が何か有用なことができるようになる前に、案件を再学習する 2 週間が無駄になる。クライアントは再学習を退行として体験する — 新しい学生が追いつくまで電話は未応答、既に答えた質問が再度尋ねられる。

## 対象

教授または引退する学生。教授は full cohort オフボーディングを統括するために実行; 個別学生は学期中遷移(卒業、離脱)する自分の案件で実行可能。

## コンテキストの読み込み

- `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → クリニックプロファイル、学期、プラクティスエリア、監督スタイル
- `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` → 案件別にグループ化されたすべてのアクティブ期限
- `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/client-comms/[case-id]/log.md`(案件ごと) → コミュニケーション履歴
- クリニックが維持する案件ファイル / インテイクサマリー
- 学生名簿 — 引継ぎに入る時点で誰が何を所有するか

## ワークフロー

### Step 1: 案件と所有者を特定

- すべてのアクティブ案件を引き出す(インテイク記録 + `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` の case_ids + client-comms フォルダから)
- 各案件について: 現在のオーナー学生は誰? 残るか離れるか?
- マップ: outgoing オーナー → incoming オーナー(分かっていれば; そうでなければ「TBD — 教授が割当」とマーク)

クリニックが中央アクティブ案件リストを維持しない場合、スキルは 1 つの入力を必要とする: アクティブ案件のリスト。それを求める。推測しない。

### Step 2: 案件ごとの引継ぎメモ

各案件について:

```markdown
# Case Handoff — [case name] — [semester ending]

**Case ID:** [case_id]
**Practice area:** [area]
**Outgoing student:** [name]
**Incoming student:** [name or "TBD"]
**Supervising attorney:** [professor]
**Client:** [name or client ID]

---

## Where we are

[1 段落: 現在の状態。何が行われ、何が係属中で、案件がどこに向かっているか。案件が自然な pause point または filings の間なら、そう述べる。]

## Pending deadlines

*`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/deadlines.yaml` から引き出された。incoming 学生の最初の仕事はこれらが正確で所有されていることを確認。*

| Due | Type | Description | Notes |
|---|---|---|---|
| [date] | [type] | [1 行] | [タイトなら: 「URGENT — due within [N] days of semester start」] |

## What's been done

- [今学期の主要アクション: intake、filings、hearings、主要コレスポンデンス]
- [作成された文書 — どこに住むかへのポインター]

## What's open

- [係属中の決定: 例: 「クライアントが和解 offer を受け入れるか未決定」]
- [リサーチギャップ: 例: 「[jurisdiction] が [remedy] を許可するか確認必要」]
- [オープンコミュニケーション: 例: 「相手方弁護士事務所からの応答待ち」]

## Client relationship

- [学生はどれだけ連絡を取ったか? 電話、メール、対面?]
- [次の学生が知るべき関係コンテキスト: 言語の好み、信頼構築の注記、スケジュールに影響する状況]
- [次の予定された連絡またはアポイント]

## Documents drafted / filed

*ポインター、内容ではない。*

- [Date] [Document type] — [パスまたはファイル参照] — [status: filed / drafted / in review queue]

## Communications history summary

*`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/client-comms/[case-id]/log.md` から。ここでは 3 行サマリー; incoming 学生が完全ログを読む。*

[最近の連絡パターンの短いサマリー — 例: 「intake 以来 3 通話、すべてスペイン語、クライアントは夜を好む。最終連絡: 2026-04-15、聴聞通知用住所確認。」]

## Professor's flags for incoming student

*引継ぎメモが incoming 学生に行く前に教授レビューによって追加される。含まれ得るもの: 「この案件には敏感な家族関係がある — クライアントに電話する前にインテイクを注意深く読む」、「クライアントはすべての郵便を自宅住所でなく PO box に送ることを要求した」、「ここに未解決のスコープ問題がある — 第 1 週に私と確認」。*

[flags、または「none」]

## First-week priorities for incoming student

1. [具体的 — 例: 「案件を取った後 48 時間以内に [client] に電話。自己紹介。案件ファイルを受け取ったことを確認。」]
2. [期限駆動 — 例: 「立退き訴状への答弁書が [date] 期日。outgoing 学生のドラフトをレビュー、改訂、提出。」]
3. [知識ギャップ — 例: 「4/28 status conference の前に habitability 抗弁に関する outgoing 学生のメモを読む。」]

---

**Handoff prepared by:** [outgoing student]
**Date:** [YYYY-MM-DD]
**Reviewed by:** [supervising attorney, if applicable per supervision model]
```

### Step 3: コホートサマリー

すべての案件ごとメモの後、`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/handoffs/[semester]/_summary.md` を生成:

```markdown
# Cohort Handoff Summary — [semester ending]

**Departing students:** [N]
**Incoming students:** [N]
**Active cases transitioning:** [N]
**Cases closing at semester end (no transition):** [N]

---

## Transitions

| Case | Outgoing | Incoming | Practice area | Urgency |
|---|---|---|---|---|
| [case_id] | [name] | [name or TBD] | [area] | [standard / deadline within 2 weeks / urgent] |

## Unassigned

[incoming 学生が「TBD」の案件 — 教授が次学期前に割当]

## Deadlines within 30 days of semester start

[deadlines.yaml から引き出された — 新コホートが走り出す案件]

## Notes for professor

- [学生パフォーマンスについて懸念を惹起したあらゆる案件、より密な監督用にフラグ]
- [outgoing 学生がコンサルティングのため残る意思のある案件 — 例: 引き継ぐ 2L を mentor したい卒業する 3L]
- [引継ぎ全体のパターン — 例: 「6 案件のうち 3 つが最初の 14 日にアクティブ期限を持つ; それらのプラクティスエリアで ramp エクササイズを前倒し検討」]
```

### Step 4: 教授レビュー(監督モデルが要求する場合)

案件をクローズしたり、新しい学生に遷移したりすることは重大なアクション。ゲートは `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の `## Supervision style` の監督ワークフローで、ライセンスを持つ指導弁護士がセットアップを所有することを確認する Part 0 役割チェックで補強。Case-closing メモは、監督スタイル選択に関係なく、引継ぎ文書で案件が closed とマークされる前に常に教授サインオフを得る。

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の監督スタイルに従う:

- **Formal review queue:** 各引継ぎメモは incoming 学生へのリリース前にレビューキューに入る。教授が承認、編集、または返却。
- **Configurable flags:** メモは「CHECK WITH [PROFESSOR] BEFORE RELYING」を保持 — 教授が非公式にレビュー、学生がチェックインの責任。
- **Lighter-touch:** メモは標準 AI 支援ラベルを保持; 教授は既存構造を通じてレビュー。Case-closing メモは依然クローズ前に教授にルーティング。

### Step 5: 引き継ぐ

レビュー後、引継ぎメモは `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/handoffs/[semester]/[case_id].md` に住む。incoming 学生は次学期開始時の `/ramp` 実行中にそれを読む — `/ramp` は新しい学生が割り当てられた案件のメモを浮上させるべき。

## インテグレーション

- **`/ramp`:** 次学期開始時に `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/handoffs/[most-recent-semester]/` を読み、各新しい学生が引き受ける案件のメモを浮上させる。
- **`/deadlines`:** 各メモの pending-deadlines セクションに供給。
- **`/client-comms-log`:** コミュニケーション履歴サマリーに供給。
- **`/supervisor-review-queue`(正式レビュー有効時):** 引継ぎメモは教授承認用にここにルーティング。

## このスキルが行わないこと

- **案件をクローズする。** 引継ぎは次コホートに遷移する案件用。学期末にクローズする案件は最終内部ステータスメモ(`/legal-clinic-ja:status internal`)をファイル用に得て、引継ぎ文書で closed とマークされる; ステータススキルは `client | internal | court` 聴衆をサポート。
- **Incoming 学生を割当。** 教授が割当。スキルは割当が何かを記録; 選ばない。
- **クリニックデータなしにゼロから引継ぎを生成。** アクティブ案件リストが入力として必要。クリニックがそれを維持しない場合、スキルはそのギャップを発明するのではなくブロッカーとして浮上。
- **会話を置き換える。** 書かれたメモは記録。outgoing 学生は可能なら incoming 学生と会話もすべき — メモは事実をキャプチャし、会話はメモがキャプチャできない判断と関係コンテキストをキャプチャする。
