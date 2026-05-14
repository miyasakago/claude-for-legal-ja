---
name: study-plan
description: >
  長期のバー試験対策(またはエグザム対策)学習プランを構築・更新します — フェーズ、
  弱点による重みづけ、日次セッション・スケジュール、`study-plan.yaml` のセッション
  履歴から適応。Use when the user says "build a study plan", "plan my bar
  prep", "schedule my studying", or "how should I study for [X]"(日本語
  トリガー:学習プラン作成、バー対策のスケジュール、勉強スケジュール、X の
  勉強法)。
argument-hint: "[--build | --update | --status | --cram]"
---

# /study-plan

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロード → バー法域、試験形式、バー試験日、弱点科目、目標 study hours/day、prep course。
2. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` が存在すればロード。
3. 以下のフレームワークを適用。
4. フラグでルーティング:
   - `--build`(プランなしならデフォルト): 入力ゲート(試験、科目、hours/week、休日、メソッド)を歩く。最初の 2 週間のフェーズ構造 + 日次スケジュールを構築。`study-plan.yaml` を書く。
   - `--update`(プランありならデフォルト): `session_history` を再読、科目優先と weekly_hours を調整、次の日次スケジュール・ストレッチを埋める。
   - `--status`: 今日 / 今週何がスケジュール、score トレンド、滑り落ちている科目、各科目の次回スケジュールされたセッション。
   - `--cram`: cram モード強制 — 80/20 高 yield 優先、日次 MBE volume、最後 2-3 日 taper。
5. 書く前: プランを散文で要約し学生と確認。彼らの答えに基づいて調整。
6. 必ず hours/week を学生の述べた人生制約に sanity-check。野心的すぎるプランは失敗する。

---

## 目的

座って勉強し、何を勉強するか分からないことが週が消える方法。本スキルはプランを構築 — 試験までの週、日あたりセッション、週あたり科目、セッション・タイプ — し、学生が実際にセッションをするにつれ適応する。これは生きたプランで、カレンダー・エクスポートではない。

下流スキル(bar-prep、flashcards、drill、irac)に共有スケジュールも honor させ、学生が毎回セッションを開くたびに「今日何を勉強したい?」と訊かれないようにする。

## 自信度規律(Confidence discipline)

プランは意見であり、ドクトリンではない。スキルは何が estimate かを明確に述べる:

- **Time-per-topic estimates** は一般ガイダンス(典型的な Barbri/Themis/Kaplan 重みづけに基づく)。Estimate としてフラグ — 学生の実際ペースは異なる。
- **Subject weightings** は学生自身の述べた弱点科目とセッション履歴から導出。Confident。
- **Cram モードの high-yield-topic 優先付け** は複数年バー試験 release パターン(MBE/MEE 科目頻度)に基づく。「これが試験に definitely 出る」主張には `[UNCERTAIN — past frequency is not a prediction]` をフラグ。

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md`:
- バー法域、試験形式、バー試験日
- 現在のクラス(非バー利用)
- 弱点科目(MBE、エッセイ)
- Prep course
- 目標 study hours/day

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` が存在すれば — 拡張、上書きしない。

## ワークフロー

### Step 1: 何のためのプランか

> 何のためのプランを構築しますか?
>
> 1. **バー試験**(バー試験日が頭にある)
> 2. **特定のロースクール試験または finals セット**
> 3. **一般学期学習ケイデンス**(全クラス横断のアウトライン、読書、ドリル)

(1) バー: プラクティスプロファイルからバー試験日を読み、確認。バー試験日が捕捉されていなければ問う。
(2) ロースクール試験: どのクラス、何の日付、何の形式かを問う。
(3) 学期: アンカーとして term-end date を問う。

### Step 2: 入力 — 一つずつ、各々を待つ

**訊いて待つ。** 一つのプロンプトにすべての質問をバルクして進まない。

- **試験日:** 確認済み?(バーの場合: プラクティスプロファイルになければ法域を訊く — 学習コンテンツは依存する。)
- **カバーする科目:** バーなら、試験形式(NextGen / 伝統的 UBE / 州固有)の NCBE 科目アウトラインから読む。クラスなら、シラバス。学生と確認 — 「追加・削除すべき科目は?」
- **最強科目:** 最低優先。レビューはするが、heavily にはドリルしない。
- **最弱科目:** 最高優先。より多くのセッション。
- **週あたり利用可能時間:** 現実的、aspirational ではない。「I can do 20 hours」は「I will do 20 hours for 8 weeks」と違う。実際 sustain できる量を訊く。
- **Life-context sanity check — force it。** 学生が数を与えたら問う(一質問ずつ — スキップしない):

  > [N] hours per week とのこと。これを構築する前に、週に何があるか教えてください — 仕事(hours/week)、家族(子供、介護)、通勤、ワークアウト、セラピー、クリニック、何でも meaningful なもの。プランは life に fit すべきで、その逆ではない。フォローできないプランは、フォローできる lighter なプランより悪い。

  答えを待つ。それから報告された load に対して述べられた hours を sanity-check:

  > That's ~[X] hours/day across [N] study days, on top of [job + family + commute + other]. In my experience that's [realistic / tight / unsustainable]. Hours/week target を構築前に調整しますか、それともキープして week 1 を見ますか?

  プラクティスプロファイルの target hours が cold-start で既に捕捉されていてもこのステップをスキップしない。プロファイルは学生が言ったものを捕捉;life-context チェックは sustainable かを捕捉。チェックがより低い数を生成したら、プランにそれを使い、`confidence_flags` ブロックで調整を注記。

  学生が life context を共有することを拒否(「just build it」)したら、respect する — ただし `confidence_flags` エントリを追加: 「Life-context check declined; plan assumes [N] hours/week is sustainable. Revisit at end of week 2 if adherence is below [X]%.」
- **好みの学習メソッド:** マルチセレクト。MBE 練習 / エッセイ / フラッシュカード / アウトライン / ドリル / 再読。実際にやると言ったメソッドにスケジュールを重みづけ。
- **週あたり休日数:** 休息日は重要。7/7 日スケジュールするプランは week 3 で失敗する。

### Step 2.5: Supplement vs. replace(prep-course ユーザー)

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` → `Prep course` が **Barbri**、**Themis**、**Kaplan**、または他の structured prep course(つまり `self` または `N/A` ではない)の場合、学生はすでに prep-course カレンダーを持つ。本スキルのプランは二つの役割のどちらかを選ぶ必要 — prep course と並行してフルな並列カリキュラムは学生を burn out させずに走らせられない。

問う、一質問、待つ:

> プロファイルでは [Barbri / Themis / Kaplan] と。彼らは各科目とタスクをスケジュールした day-by-day カレンダーを公開しています。このプランの動き方は二通り — 一つ選んでください:
>
> 1. **Supplement。** Prep course が primary カリキュラム。このプランはギャップを埋める: 弱点科目の追加 MBE ドリル、targeted エッセイ練習、見逃したトピックのフラッシュカード・ループ。Prep course カレンダーを再構築せず、その上にレイヤー。
> 2. **Replace。** Prep course カレンダーに従っていない(pacing が life に合わない等)。プラン全体を構築 — 科目、時間、フェーズ、スケジュール — そして prep course カレンダーをドロップ。
>
> 両方は選ばない。二つのフルカリキュラムを互いに走らせるのが、学生が week 4 で blow up する方法。

答えを待つ。Yaml に `prep_course_mode: supplement | replace` として記録。

**Supplement** の場合: プランの日次スケジュールは lighter — 弱点科目ドリルと targeted 練習だけ追加し、prep-course カバレッジを duplicate しない。`confidence_flags` にフラグ: 「Supplement mode — このプランは primary カバレッジで [prep course] に track だと仮定。Prep course から落ちたら教えてください、再プランします。」

**Replace** の場合: 以下に specified された通りにフルプラン構築。

学生の prep course が `self` または `N/A` なら、このステップをスキップ — supplement するものがない。

### Step 3: スケジュールを構築

今日の日付から試験までの週を計算。それから:

**Normal mode(4+ 週前):**
- 週をフェーズに分割:
  - **Learning phase**(時間の最初 ~60%): 科目あたり ~3-5 日、アウトライン化/読書を新素材のフラッシュカードといくつかの MBE/エッセイ問題と mix。
  - **Drilling phase**(次の ~30%): より多くの MBE volume、より多くのエッセイ練習、模擬条件、全科目ローテーション。
  - **Review phase**(最後の ~10%): session_history からの最弱サブトピックにフォーカス、フル模擬試験、strong 領域の軽いレビュー。
- 科目を弱さで重みづけ: 弱点科目は strong の ~2x 時間。
- 日ごとスケジュール: どの科目、どのメソッド、どれくらい長く。学生の実際の life に slack を残す。

**Cram mode(< 4 週前):**
- フラグ: 「You're less than four weeks out. This is cram mode — the plan prioritizes high-yield topics over full coverage. You will leave gaps. That's the tradeoff at this point.」
- 80/20 優先付け: 歴史的に最も現れる MBE 科目(Civ Pro、Evidence、Con Law、Contracts)が lion's share。Narrower 科目は minimum viable カバレッジ。
- 日次スケジュール: 毎日 MBE ブロック(volume が今重要)、隔日エッセイ練習、週あたり一つの模擬試験。
- 最後 2-3 日は sleep と taper。試験前日に hard ドリルをスケジュールしない。これは real — 前夜まで cram する学生はスコアが悪い。

### Step 4: 書く

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/study-plan.yaml` に書く:

```yaml
plan_type: bar  # or law-school-exam or semester
exam_date: 2026-07-28
jurisdiction: CA
exam_format: state-specific  # or NextGen / UBE
created: 2026-05-08
last_updated: 2026-05-08
weeks_to_exam: 12
hours_per_week: 25
days_per_week: 6
mode: normal  # or cram
phases:
  - name: learning
    start: 2026-05-08
    end: 2026-06-20
    focus: outlining, flashcards, introductory MBE
  - name: drilling
    start: 2026-06-21
    end: 2026-07-18
    focus: MBE volume, essay practice, simulated conditions
  - name: review
    start: 2026-07-19
    end: 2026-07-27
    focus: weak-subtopic review, full practice exams
subjects:
  evidence:
    priority: high  # weak
    weekly_hours: 5
    methods: [mbe, flashcards, essay]
  con-law:
    priority: medium
    weekly_hours: 3
    methods: [mbe, outline-review]
  # etc.
schedule:
  - date: 2026-05-08
    day: Thursday
    sessions:
      - subject: Evidence
        method: outline-review
        duration_min: 90
      - subject: Evidence
        method: mbe
        duration_min: 60
        n_questions: 25
  - date: 2026-05-09
    day: Friday
    sessions:
      - subject: Contracts
        method: flashcards
        duration_min: 45
      - subject: Contracts
        method: essay
        duration_min: 60
  # etc.
session_history: []  # セッション完了時に bar-prep、flashcards、drill、irac が追記
```

### Step 5: 学生と確認

**ヘッダー — チャット内のあらゆる提示と、YAML と並んで書かれるあらゆる別の散文プラン・ドキュメントに必須。** 要約の最初の行(およびあらゆる `study-plan.md` companion ファイルの最初の行)は、プラグイン config の `## Outputs` の verbatim ヘッダーでなければならない:

```
STUDY NOTES — NOT LEGAL ADVICE
```

ヘッダーは YAML 自体の中には入らない(データファイルだから)が、学生に表示する散文要約と、YAML の隣に保存する人間可読プラン・ドキュメントには属する。これは免責の afterthought ではない — 出力の identity。省略・言い換え・移動しない。

YAML を保存する前にプランを散文で要約(raw YAML ではない)、ヘッダーを上に:

> STUDY NOTES — NOT LEGAL ADVICE
>
> ここに構築したものを示します。[X] 週で [exam] まで。[Y] hours/week を [Z] 日横断。弱点科目(Evidence、Contracts)は 2x 時間。三つのフェーズ: learning は [date] まで、drilling は [date] まで、review は最後 [N] 日。最初の 2 週を日ごとにスケジュール済。それを越えると週ごとに割り当て — セッション完了に応じて日次スケジュールを埋め、プランが実際の場所に適応します。
>
> Feel right? Too ambitious? Too light? Missing a subject?

答えに基づいて調整。それから書く。

## プランの適応

各セッション後(bar-prep-questions、flashcards、drill、irac 経由)、対応するスキルが `session_history` に追記:

```yaml
session_history:
  - date: 2026-05-08
    subject: Evidence
    type: bar-prep-mbe
    n_questions: 10
    score: 6
    weak_subtopics: [hearsay-exceptions, character-evidence]
```

次の `/law-student-ja:study-plan --update` 実行(またはスキルがプランが古いと検出したとき):
- Consistent に低スコアの科目は `priority` と `weekly_hours` で promote。
- 科目内の弱点サブトピックは、その科目の次回スケジュール・セッションでフラグ。
- 学生が遅れている場合(スケジュールされたセッションが history に現れない)、調整: カバレッジを圧縮するかギャップを注記して問う。
- 学生が ahead なら、deeper 弱点科目ドリルのために時間を開ける。

## モード

`--build`(デフォルト) — 新しいプラン
`--update` — session_history を再読、重みづけ調整、来る日次スケジュールを埋める
`--status` — 今日 / 今週何が deck、score トレンド、何が滑っている
`--cram` — 4 週超でも cram モード強制(ユーザー override)

## 統合

- `/law-student-ja:session <subject> <n>` は結果をこのプランの `session_history` に書く。
- `/law-student-ja:bar-prep-questions` はプランを読み、今日スケジュールされた科目を知る。
- `/law-student-ja:flashcards` は `--session <n>` でき、結果はプランに着地。
- `/law-student-ja:socratic-drill` と `/law-student-ja:irac-practice` のセッション完了も追記。

## このスキルがしないこと

- **合格を保証。** プランは足場。仕事はあなた。
- **試験を予測。** Cram モードは歴史的科目頻度を使う;high-yield ≠ guaranteed-tested。
- **Prep course スケジュールの代替。** Barbri/Themis/Kaplan に乗っているなら、このプランは supplement 可能 — 二つのフルカリキュラムを互いに走らせない。一つを primary として使う。
- **Life をスケジュール。** 利用可能時間はあなたが伝えるもの。Overstate すれば、プランは week 2 で壊れる。Honest に。
