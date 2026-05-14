---
name: exam-forecast
description: >
  同じ教授の過去の試験問題を解析し、パターン — 科目重み付け、繰り返す
  issue-spot のトラップ、好まれるハイポ・タイプ、Policy vs Doctrine の比率 — を
  surface し、次回試験で重視されそうな箇所を予測します。Use when the user says
  "what's on the exam", "analyze past exams", "predict the exam", or shares
  past exams(日本語トリガー:試験予測、エグザムフォーキャスト、過去問解析、
  試験に何が出る、過去問分析)。
argument-hint: "[class name, with past exams shared or paths to them]"
---

# /exam-forecast

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` をロード → クラス、教授、試験形式、シラバス。
2. 以下のワークフローを適用。
3. 過去試験を取り込み(PDF、貼り付け、またはパス)。サンプルサイズを確認。
4. 各過去試験を解析: 形式、科目カバレッジ、問題スタイル、fact-pattern density、繰り返すトラップ。
5. クロス試験パターン解析 — 安定なもの、変動するもの。
6. 現行シラバスと結合してフォーキャストを生成: 科目重み、形式、hobby horse、学習重点。
7. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/exam-forecasts/[class]/forecast-[YYYY-MM-DD].md` に書く。予測ではなく、重み付けヒューリスティクスとしてフレーム。

---

## 目的

各教授の試験には指紋がある。同じハイポ構造が繰り返される。同じトラップが戻ってくる。同じ科目比率が繰り返される。過去試験を持つ学生は smart に勉強し、持たない学生は harder に勉強する。本スキルは持っている過去試験を解析し、パターンを surface する。

魔法ではない。予測ではなく、フォーキャスト。スキルは試験に何が出るかを伝えられない — 過去試験に何があったかと、シラバス・カバレッジに基づき何が再発する可能性が高いかを伝えられる。

## 自信度規律(Confidence discipline)

- パターン解析(どの科目が現れた、トピックあたり何問、policy vs. rule-application の頻度) — 試験が明確に目の前にある所では confident。
- 次回試験での likely emphasis に関する推論 — `[UNCERTAIN]` がデフォルト;これらはフォーキャストで、確実性ではない。明示的にフレーム: 「あなたが共有した [N] 個の過去試験に基づき、[topic] は [M] 個に現れた。次回試験で重視するかもしれないし、教授がローテートするかもしれない — これを予測ではなく、レビュー時間の重みづけとして使ってください。」
- 利用可能な過去試験が 1〜2 個のみなら、明示的に言う — 1 試験から推論されるパターンはノイズ。
- 教授が新しい(過去試験なし)なら、スキルはフォーキャストできない。そう言い、シラバスベースの「これらが対象科目」のみにフォールバック。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` → 現在のクラス、試験形式、捕捉されていればシラバス
- ユーザー提供の過去試験(PDF、貼り付けテキスト、パス)
- オプション: 現行クラスのシラバス(「これまでにカバーされたもの」のため)

**アップロードされた過去試験に教授名があれば、パターンマッチに使う**(同じ教授の試験は最高シグナル入力)。**なければ、科目と構造でマッチ。** 教授名のタイプ入力をユーザーに求めない — 素材にあるものを使う。会話で自発的に言ってくれれば良い;プロンプトしない。

## ワークフロー

### Step 1: 取り込み

- どのクラスのフォーキャストか?
- この教授からの過去試験はいくつ利用可能?
- 同じコースから、または同じ教授の別コースから?
- いずれかが take-home / open-book / 通常形式と異なる variant か?
- 現行クラスのシラバスは?

過去試験が 3 未満: thin sample としてフラグ。パターン推論は weak。
試験が異なるコース横断: 一部のパターン(問題スタイル、policy vs doctrine 比率)は転移;科目固有のパターンは転移しない。

### Step 2: 各過去試験を読む

各過去試験:

- 形式(問題数、長さ、制限時間、open/closed book)
- 科目カバレッジ(どのトピックがテストされた、どの比率で)
- 問題スタイル(issue-spotter、single-issue deep、policy essay、short-answer MBE スタイル、ミックス)
- Fact pattern density(fact-heavy ハイポ、ドクトリンフォーカスの sparse 事実、または fact のない policy プロンプト)
- 繰り返すトラップ(例: 教授がいつも otherwise-clean な fact pattern に jurisdictional issue を隠す;教授がいつもルールでなく例外について訊く)
- Policy vs doctrine 比率
- Unusual な構造(エッセイ + MBE ハイブリッド、moot court シナリオなど)

### Step 3: クロス試験パターン解析

試験横断で一貫しているものをロールアップ:

**安定パターン(過去試験のほとんど/すべてに現れた):**
- 科目重み(例: 「consideration と modification は試験ポイントの 30% を consistent に占める」)
- 問題スタイル(例: 「いつも一つの long issue-spotter + 二つの short-answer ハイポ」)
- 教授の hobby horse(例: 「クラスで minor topic でも third-party beneficiaries をいつもテスト」)

**変動パターン(一部に現れた、すべてではない):**
- Policy エッセイ(例: 「過去試験 4 のうち 2 に現れた — 通常 policy-heavy トピックが semester 後半でカバーされたとき」)
- Open-book vs. closed-book の違い
- Take-home vs. in-class の違い

**注記する価値のある不在パターン:**
- クラスでカバーされたが過去試験で NEVER テストされていないトピック — スキップしないが、heavy には重み付けしない
- 過去試験でテストされたが現行シラバスにないトピック — おそらく戻らない

### Step 4: 次回試験のフォーキャスト

**ヘッダー — 必須、フォーキャストの最初の行、チャット内と保存ファイル両方。** プラグイン config の `## Outputs` に従い、すべての学習出力は逐語的な学習ノート・ヘッダーを持つ。フォーキャストは学習出力。ヘッダーを省略・言い換え・移動しない。ヘッダーは学生が落としてもいい免責ではない;出力の identity であり、フォーキャストが予測された試験または法的助言と誤解されるのを防ぐ:

```
STUDY NOTES — NOT LEGAL ADVICE
```

パターン解析と現行シラバスを結合:

```markdown
STUDY NOTES — NOT LEGAL ADVICE

# Exam Forecast — [class / professor] — [date]

**Past exams analyzed:** [N]
**Sample confidence:** [thin (<3) / moderate (3-5) / strong (6+)]
**Caveats:** [例: 「過去試験の 1 つは open-book final;次回は closed-book。パターン転移は partial。」]

---

## Subject weighting (historical)

| Topic | Past exam weight (avg) | In current syllabus? | Forecast weight |
|---|---|---|---|
| [topic 1] | [%] | [yes/partial/no] | [heavier / stable / lighter] |

## Question-style forecast

- **Format likely:** [X issue-spotters + Y short answers + Z policy, or similar]
- **Fact-pattern density:** [fact-heavy / sparse / mixed]
- **Call style:** [one broad call / multiple specific calls / bullet sub-parts]

## Professor hobby horses to watch

- [topic A] — 過去試験 [M of N] に現れた。シラバス・シェアの 3-5x で重み付け。
- [topic B] — [pattern]
- [trap pattern] — 例: 「otherwise-clean facts に jurisdictional issue を隠す」

## Topics covered this semester but rarely tested

[list — スキップしないが、over-weight しない]

## Study emphasis recommendation

過去試験パターン AND 現行シラバス・カバレッジに基づく:

**Heavy:** [試験をアンカーする可能性が高いトピック — 学習時間の 40-50%]
**Moderate:** [サポート・トピック — 30-40%]
**Sanity check:** [カバーされたが歴史的に under-represented なトピック — 10-20%, just in case]

## [UNCERTAIN — framing]

このフォーキャストは [N] 個の過去試験から導出。教授は変動。教授はローテート。過去年度に強調されたトピックは、シラバスがシフトすると de-emphasize されうる。これを予測ではなく、学習時間の重みづけヒューリスティクスとして扱う。試験はサプライズを含む。
```

### Step 5: 出力場所

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/exam-forecasts/[class]/forecast-[YYYY-MM-DD].md` に書く。バージョン付き — 学期中に学生が別の過去試験を得たら、再実行して追記。

## 統合

- **outline-builder:** forecast 重みはアウトライン深さ判断に供給される — heavy トピックに深さを重みづけ
- **flashcards:** forecast-heavy トピックには多くのカードが生成される
- **bar-prep-questions:** バー試験対策には無関係(独自のフォーキャスト・モデルがある);exam-forecast はクラス固有の期末用
- **irac-practice:** forecast トピックを IRAC 演習ハイポの科目領域として使う

## デシジョンツリーでクロージング

CLAUDE.md の `## Outputs` に従い、next-steps デシジョンツリーで終わる。本スキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト枝(draft the X、escalate、get more facts、watch and wait、something else)は開始点で、固定ではない。ツリーが出力;弁護士が選ぶ。

## このスキルがしないこと

- **特定の問題を予測する。** 過去試験はパターンを示す;明日のプロンプトを見せない。
- **過去試験なしで動く。** この教授からの過去試験がなければ、スキルはフォーキャストできない — 「シラバスがカバーするもの、それを勉強」にフォールバック。
- **シラバスのすべてを勉強することの代替。** Forecast は重みづけで、除外ではない。歴史的に under-represented だからトピックをスキップするのは、学生が焼かれる方法。
- **知らない変化を説明する。** 教授が今年フォーカスを移していたら(例: 講義で新しい判例を強調)、スキルはあなたが伝えなければそれを見ない。
- **過去試験 1〜2 個で確実に動く。** Thin sample。そうフラグ。
