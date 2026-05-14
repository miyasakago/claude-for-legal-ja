# Law Student プラグイン(日本語版)

学習モードであり、解答モードではありません。ソクラテス式の問答で「あなた」に質問し、雑な推論にプッシュバックします。ケースブリーフ、アウトライン構築、フラッシュカード、IRAC 採点、コールドコール対策、書き直しは決してしないライティング・フィードバック、過去の同教授試験からのエグザム・フォーキャスト。あなた自身に合わせて調整 — 受講中のクラス、バー試験法域、Drill-me で鍛えられたいか Explain-to-me で足場づくりされたいか。

**すべての出力は学習用の足場であり、模範解答ではありません。プラグインはあなたの思考を構造化し、ソクラテス式で鍛え、間違っていた点をフラグします。アウトライン、ブリーフ、エッセイをあなたの代わりに書くことはありません — それでは目的が損なわれるからです。学習素材中の引用は検証用にタグ付けされます。**

## このプラグインを使うのは

ロースクール生。1L からバー試験対策まで。

## 初回実行: cold-start

これは組織についてではなく、あなたについてです。あなたの受講中のクラス、バー試験法域、学習スタイル — Drill-me vs. Explain-to-me。素材を持ち寄ってください: 過去のアウトライン、採点済みエッセイ、過去の試験問題(特に同じ教授)、MBE セット、シラバス、ペーパー。10〜20 アイテムが目標で、それを下回るとプラクティスプロファイルに `LIMITED DATA` がフラグされ、下流のスキルは素材が増えるまでは薄くなります。

```
/law-student-ja:cold-start-interview
```

## スキル一覧

すべてのスキルは `/law-student-ja:<skill-name>` で呼び出します。

| スキル | 役割 |
|---|---|
| `/law-student-ja:cold-start-interview` | あなた自身に関するインタビューと素材取り込み — クラス、バー試験、学習スタイル、素材 |
| `/law-student-ja:socratic-drill [subject]` | ソクラテス式の問答 — こちらが問い、あなたが答え、こちらがプッシュバック。答えは与えない |
| `/law-student-ja:case-brief [case]` | あなたの好みのフォーマットでのケースブリーフ |
| `/law-student-ja:outline-builder [subject]` | クラスの素材からあなたのフォーマットでアウトラインを構築・拡張 |
| `/law-student-ja:bar-prep-questions [subject]` | バー試験対策の問題、MBE またはエッセイ — 法域認識(UBE / NextGen / 州固有)あり、Majority/UBE と州ルールの分岐をフラグ |
| `/law-student-ja:flashcards [subject]` | フラッシュカードを生成または出題;Leitner 式バケット;科目別 markdown;`--session <n>` モード |
| `/law-student-ja:study-plan` | 長期の学習プランを構築・更新 — フェーズ、弱点に応じた科目、セッション履歴から適応する日次スケジュール |
| `/law-student-ja:session <subject> <n>` | 科目を絞った N 問セッション;結果でプランを更新 |
| `/law-student-ja:irac-practice` | あなたの IRAC エッセイを採点 — 構造、issues、ルール、分析。セッション横断でパターンを追跡。書き直しは決してしない |
| `/law-student-ja:cold-call-prep [case]` | コールドコール対策 — 教授の質問を予測し、ソクラテス式で鍛える |
| `/law-student-ja:legal-writing [path-or-paste]` | どんな下書きにも構造的フィードバック — 決して書き直さない |
| `/law-student-ja:exam-forecast [class]` | 同じ教授の過去問題を解析;次回試験をフォーキャスト |

## 「学習モード」が意味すること

ここにあるいくつかのスキル(socratic-drill、Drill-me モードの case-brief、cold-call-prep、irac-practice、legal-writing)は、意図的に「答えを与えない」「あなたに代わって書かない」ように作られています。要点は、実際にやることで学ぶこと。答えやドラフトが欲しいなら、別のツールを使ってください。本プラグインは「もがき」のためのものです。

**legal-writing は最も厳格です。** あなたの下書きを読み、何が弱いかを伝えますが、書き直しはしません。書き直しを頼まれた場合は、丁寧にお断りしつつ、より具体的な構造的フィードバックを提案します。これは仕様です。

**outline-builder と case-brief も、より柔らかい形式で同じルールに従います。** Outline builder は足場を組みます — トピックツリー、サブトピックの枠、判例プレースホルダー — そしてあなたが自分のノートやケースブックからルールを書き入れる際にソクラテス式の質問をします。シラバスだけから populated なアウトラインを生成することはしません。Case brief は両モード(Drill-me と Explain-to-me)で同じルールに従います: スキルはテンプレートを提供し、あなたが書いたものにプッシュバックしますが、判例をあなたの代わりにブリーフすることはしません。判例本文を貼り付けると、court 自身の言葉を枠に抽出することはできます — それは「ソースを指し示している」のであって、「あなたの代わりに書く」ことではありません。

## 学術的誠実性(Academic integrity)

採点対象の課題 — 持ち帰り試験、採点対象のライティング課題、ジャーナル・ノート、ペーパー — に本プラグインを使う前に、学校のハナーコードと教授のシラバス上の AI ツール・ポリシーを確認してください。多くの学校は採点対象の課題での AI 使用を禁止または制限しており、ルールはコース・教授によって異なります。本プラグインは「学習と練習」のために設計されており、学校が禁止している場所で使うのはハナーコード違反です。結果は、ツールのものではなくあなたのものです。迷うときは、教授に書面で確認してください。

ここにある学習モードのスキル(socratic-drill、irac-practice、legal-writing、cold-call-prep)は、意図的にあなたに答えを与えず、あなたに代わって書くこともしません — それが教育法です。これはまた、許される使い方(誰も使わない見た目の練習ドリル)と禁止される使い方(採点対象のメモのゴーストライティング)を区別する設計上の前提でもあります。ガードレールを迂回しないでください。

## 自信度マーカー(Confidence markers)

コンテンツ生成系スキルは自信度をインラインでフラグします。マーカーのない rule statement やカードは、スキルが自信を持っているもの(ただし試験前の自分でのソース確認の代替にはなりません)。プラグイン全体で使うマーカー:

- `[VERIFY: claim — check source]` — おそらく正しいと述べているが、依拠する前に自分のアウトライン、ケースブック、prep course、または一次資料に対して確認すべき。bar-prep-questions、case-brief、flashcards、legal-writing、irac-practice で liberal に使われる。
- `[UNCERTAIN: specific reason]` — この特定のコールについてスキルが自信を持っていない(minority rule、議論の余地がある issue-spot、スキルが詳しくない法域)。自分で判断し、ソースを確認すること。
- `[GAP — fill from class notes]` — outline-builder のマーカーで、スキルが信頼できるソースを持たず、ルールを発明しないトピック。あなたがノートから埋める。
- `[NEEDS CASES — rule stated but no illustrating case]` — outline-builder のマーカーで、ルールはあるが illustrating case が欠けている。
- `[CHECK CLASS NOTES — professor may have emphasized something here]` — outline-builder のマーカーで、教授固有の強調点が重要だがスキルがそれを知り得ない領域。
- `[EXCEPTION UNCLEAR — casebook mentions an exception, find the rule]` — outline-builder のマーカーで、既知の例外があるが詳細が未解決。
- `[UNCERTAIN — framing]` — exam-forecast のマーカーで、forecast は学習時間の重み付けであって予測ではないことを注記。

フラグの「存在」は、フラグの「不在」より信用してください — 未フラグのルールは「スキルが自信を持っている」もので、それでも試験対策にはソース確認が必要です。

## コネクターと引用検証

**最初にリサーチツールを接続してください — 引用ガードレールはそれに依存します。** 接続なしでは、すべての引用は `[verify]` タグが付き、各成果物の上の Reviewer note に「ソースは検証されなかった」と記録されます。プラグインはどちらでも動きますが、リサーチツールが接続されていれば、より多くの検証が自動で行われます。

本プラグインのリーガルリサーチ・コネクターは単なるデータソースではなく、「検証された引用」と「あなたが確認しなければならない引用」の違いそのものです。**CourtListener**(米国の判例、PACER ドケット、引用検証)または **Descrybe**(primary law 検索、引用の取り扱い、引用文言の検証)から取得した引用はソースタグが付き、追跡可能です。モデル知識または Web 検索からの引用は `[verify]` または `[verify-pinpoint]` のタグが付き、依拠する前に一次資料に対して確認すべきです。プラグインは引用をティアリングし、検証時間が最も重要な箇所に向けられるようにします。

## ストレージ

プラクティスプロファイルは `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` に保存され、プラグインのアップデートを超えて保持されます。それ以外は作業ディレクトリにあります:

```
law-student/
├── flashcards/
│   └── [subject]/cards.md             # 科目別フラッシュカード・デッキ
├── irac-sessions/
│   └── [student]/
│       ├── [date]-[topic].md          # 個別セッションのフィードバック
│       └── tracker.md                 # セッション横断のパターン追跡
├── writing-feedback/
│   └── [student]/
│       ├── [date]-[assignment].md     # 個別セッションのフィードバック
│       └── tracker.md                 # セッション横断のパターン追跡
└── exam-forecasts/
    └── [class]/
        └── forecast-[YYYY-MM-DD].md   # バージョン付きフォーキャスト
```

## テストと QA


## どのように学習するか

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` のあなたの学習プロファイルは静的ではありません — プラグインを使うほど良くなります。スキルは、チューニングすべき既定値を使ったときに通知します。セットアップを再実行する、ファイルを直接編集する、あるいはスキルに新しい立場を記録するよう伝えることができます。

## 注意

- Drill-me vs. Explain-to-me は cold-start で設定;セッションごとに切替可能。
- ケースブリーフとアウトラインは「あなたの」フォーマットを使います。既存のアウトラインがあれば、cold-start で指し示してください。
- バー試験対策は `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` のあなたの弱点科目を狙います。繰り返し戻ってきます。
- すべてのコンテンツ生成スキルは、不確実なときにフラグします。フラグの「存在」を、フラグの「不在」より信用してください — 未フラグのルールは私が自信を持っているもの;それでも試験前にはソースを確認してください。
