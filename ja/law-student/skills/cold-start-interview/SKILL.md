---
name: cold-start-interview
description: >
  あなた自身に関するインタビューと素材取り込み — クラス、バー法域、学習スタイル
  (drill-me vs explain-to-me)、過去のアウトライン、採点済みエッセイ、過去の
  試験問題、MBE セット、シラバス、ペーパー。Use on a fresh install, when the
  user says "set up" or "get started", or with --check-integrations to
  re-probe connectors(日本語トリガー:セットアップ、初期設定、コネクター
  再チェック、はじめる、オンボーディング)。
argument-hint: "[--redo] [--check-integrations]"
---

# /cold-start-interview

1. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` を確認。すでに populated で `--redo` がないなら、上書き前に確認。populated な `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md`(`[PLACEHOLDER]` マーカーなし)が `~/.claude/plugins/cache/claude-for-legal-ja/law-student-ja/*/CLAUDE.md` には存在するが config パスにない場合、それを config パスにコピーし、何が移行されたかをユーザーに伝える。
2. 以下のインタビュー・ワークフローを適用。
3. Part 0(誰が使うか / 何が接続されているか — 学生 vs. 卒業生 vs. その他;ドキュメントストレージの可用性)、Part 1(どこにいるか)、Part 2(どう学ぶか — drill-me vs explain-to-me)、Part 3(strong/shaky/avoid)、Part 4(素材取り込み — 10〜20 アイテムを目標)を順に。
4. 捕捉した答えを再読。矛盾、ドリフトした specifics、今呼ぶ価値のあるギャップを catch。
5. `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` を書く(必要に応じて親ディレクトリを作成)、`## このプラグインを使うのは` と `## 利用可能な統合` を含める。素材が 10 未満なら `LIMITED DATA` フラグを追加。
6. ユーザーに確認: 「捕捉したのはこちら — 間違っているところは?」

**`--check-integrations`:** Part 0 の統合可用性チェックだけを再実行。Role や残りのプロファイルに触れずに、`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` の `## 利用可能な統合` を更新。MCP コネクターの追加・削除後に使用。

プローブ時: MCP ツール呼び出しが実際に成功した場合のみ ✓ を報告。設定済みだが未テストのコネクターは ⚪ でマーク、確認方法を 1 行で。`.mcp.json` の宣言だけに基づいて ✓ を報告しないこと — それは何かが繋がっているとユーザーに誤解させます。

---

## 目的

他の cold-start は組織を学ぶ。これはあなたを学ぶ。どう勉強するか、何を避けるか、プッシュされたいか足場を組まれたいか。

## Cold-start チェック

`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` を読む:
- **存在しない** → インタビューを始める。
- **`<!-- SETUP PAUSED AT: -->` を含む** → 学生に挨拶し、そのセクションから再開を提案。
- **`[PLACEHOLDER]` マーカーを含むが pause コメントなし** → テンプレートが完了されなかった;最初からやり直すか、placeholder の始まる箇所から再開するかを提案。
- **populated(placeholder なし、pause コメントなし)** → すでに設定済み;`--redo` なければスキップ。

テンプレート構造は `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` にある — セクション足場として使う。完成したプラクティスプロファイルを config パスに書き、必要に応じて親ディレクトリを作成。旧キャッシュパス `~/.claude/plugins/cache/claude-for-legal-ja/law-student-ja/*/CLAUDE.md` に CLAUDE.md があるがここにない場合、コピー転送する。

## 共有 company profile の確認

`~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` を探す。

- **存在する場合:** 読む。1 行確認を表示: 「あなたは [name], [practice setting], at [company], [industry], operating in [jurisdictions]. 合っていますか?(共有プロファイルを変更するなら 'update' と言ってください。)」 確認されたら、会社についての質問はスキップ — プラグイン固有の質問へ直行。
- **存在しない場合:** あなたがこのユーザーが最初にセットアップするプラグイン。オリエンテーションと分岐の後、会社についての質問をして共有プロファイル(プラグインルートの `references/company-profile-template.md` のテンプレートに従う)に書き、プラグイン固有の質問を続ける。ユーザーに伝える: 「あなたの company profile を保存しました — 他の legal プラグインはこれを読み、これらの質問をスキップします。」

共有プロファイルに属する会社の質問(存在すれば再度問わない): practice setting、会社名、業界、何を売るか、サイズ、法域、規制当局、リスク許容度、エスカレーション先の名前。プラグイン固有の質問(プレイブックの立場、レビューフレームワーク、ハウススタイル、監督モデルなど)はプラグイン単位で残す。

## インストールスコープのチェック

オリエンテーションの前に、作業ディレクトリがプロジェクト内(ユーザーのホームディレクトリではない)であることに気づいたら、フラグを立てる。1 回だけ言う:

> **Heads up — このプラグインは project-scoped かもしれません、つまり [current directory] 内のファイルしか読めない可能性があります。Downloads、Documents、Dropbox など別の場所からドキュメントを読んでほしいなら、代わりに user-scoped でインストールしてください — QUICKSTART.md を参照。プロジェクトスコープのまま続けることもできますが、ファイルをこのフォルダに移動する必要があります。**

進める前にユーザーに確認するよう求める: project scope で続ける、または user-scoped で再インストールするために一時停止。作業ディレクトリが「ユーザーのホームディレクトリそのもの」なら、このチェックを黙ってスキップ。

## インタビューの前に

最初にこのプリアンブルを表示(短い 3〜4 行、それ以上は不要):

> **`law-student-ja` is for law students studying for class or the bar.** あなたの領域ではない場合は `/legal-builder-hub:related-skills-surfacer`.
>
> **2 minutes** で、学年(1L/2L/3L/bar prep)、現在のクラス、該当する場合のバー試験日を捕捉します。**15 minutes** で、学習スタイル・デフォルト(drill-me vs. explain-to-me)、弱点領域、過去の素材(アウトライン、採点済みエッセイ、過去の試験問題)、アップロードからの教授試験履歴、フラッシュカード科目を追加で捕捉。
>
> Quick か Full か?(`/law-student-ja:cold-start-interview --full` でいつでもアップグレード可能。)

## ユーザーが quick または full を選んだ後

学生が選んだら、オリエンテーション。次を自分の声でカバー:

- **本プラグインが維持するもの:** あなたのプロファイル(クラス、試験日、弱点領域、学習スタイル)、学習プラン、科目別アウトライン、フラッシュカード・バケット、模擬試験ログ。
- **このセットアップが行うこと:** 学生が法律を勉強するのを助ける — アウトライン、ケースブリーフ、コールドコール対策、エグザム・フォーキャスト、バー試験対策 — を、実際の学び方に合うフォーマットで。学習スタイル、科目、試験スケジュールを学習し、プラグインが毎回読むプレーンテキスト・ファイルに書く。すべて後で変更可能。完了後、コマンドはジェネリックなテンプレートのやり方ではなく、学生の勉強のやり方で動く。
- **データソース:** セットアップは学生の答えだけから新しい学習プロファイルを構築する。個人の Claude 履歴、他の会話、ホームディレクトリ CLAUDE.md は読まない。会話の前に出てきた関連情報(例: クラスやバー試験日)があれば、組み込む前に問う。学生がタイプまたは承認しなければ、何も設定に追加されない。

**なぜこれが重要か。** 本プラグインのすべてのコマンドは、このインタビューが書いた設定から読む。ジェネリックな設定はジェネリックな出力を与える — デフォルトのアウトライン形式、デフォルトのドリル強度、誰の実際のクラスにも合わせていないエグザム・フォーキャスト。プラグインに、学生が実際にどう勉強するか — drill-me vs. explain-to-me、科目、教授、避けがちなもの — を伝えることが、「a study AI tool」と「a tool that pushes you the way you need to be pushed」の違いを生む。答えが具体的で、アップロードされる素材(アウトライン、採点済みエッセイ、過去の試験問題)が多いほど、出力は学生のクラスに合う。

### Quick start または Full setup — 分岐

学生は preamble で quick または full を選んだ。分岐:

**Quick start パス:** 基本だけを訊く(あなたは誰、何を勉強しているか、該当ならバー法域)。他すべてに `[DEFAULT]` マーカーを付けて config を書く。クロージング: 「完了。これでコマンドを使えます。ケースブリーフ形式、フラッシュカード・スタイル、アウトライン規約には sensible なデフォルトを使いました。スキルの出力が変に感じるとき、それは通常チューニングすべきデフォルトです — スキルがどれかを伝えます。`/law-student-ja:cold-start-interview --full` でいつでもインタビュー全体を実行、または `/law-student-ja:cold-start-interview --redo <section>` で一部分だけやり直し。」

**Full setup パス:** 以下の既存インタビュー・フロー。

## インタビューのペーシング

- **答えはどこかにあると仮定する。** 質問が、どこかに既に書かれている可能性のある情報を求めるとき — 会社概要、プレイブック、エスカレーション・マトリクス、スタイルガイド、ハンドブック、法域リスト、案件ポートフォリオ — メモリーからのタイプを求める前にリンクや貼り付けを促す。「Paste a link or a doc, or give me the short version」が、1 文を超えるものに対するデフォルトの依頼。インタビュアーの第一の仕事は、既に書いてあるものを再タイプさせないこと。

**本当の答えを待つ。** Part 1 はクイックなタップスルー回答。Part 4(素材)と Part 2-3 の難しい部分は、学生がタイプ、説明、またはアップロードする必要がある。クイック・タップ以上のものが必要な質問:

- **質問して待つ。** 明示的に言う: 「This one needs a typed answer — I'll wait.」 学生が応答するまで次の質問に進まないこと。
- **アップロード(シラバス、アウトライン、採点済みエッセイ、過去の試験問題、MBE セット)について:** 「Paste the contents, share a file path, or say 'skip for now.' If you skip, I'll flag the gap in the practice profile so you can fill it later.」 そして実際に待つ。黙って先に進まないこと。
- **プラクティスプロファイルを書く前に:** インタビューをレビュー。スキップされた、またはプレースホルダーで答えられた質問をすべてリスト。「Before I write your practice profile, here's what's still open: [list]. Want to fill any of these now, or leave them as placeholders?」 そして答えを待つ。
- **決して** silent ギャップのままプラクティスプロファイルを書かない。すべての placeholder は、学生がスキップした熟考の選択であるべき — 考えるために一時停止した間にスクロールして流れた質問ではない。
- **一時停止と再開。** 学生に前もって伝える: 「If you need to stop, say 'pause' (or 'stop', or 'let me come back to this') and I'll save your progress. Run `/law-student-ja:cold-start-interview` again later and I'll pick up where you left off.」 学生が一時停止したら、`~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` に部分設定を書き、ファイル上部に `<!-- SETUP PAUSED AT: [section name] — run /law-student-ja:cold-start-interview to resume -->` コメントを置き、未回答フィールドに `[PENDING]` マーカー(`[PLACEHOLDER]` とは区別)を付ける。セットアップが再実行され paused config を見つけたら、学生に挨拶: 「Welcome back. You paused at [section]. Your earlier answers are saved. Pick up where we left off, or start over?」 既に回答された質問を再度問わないこと。
- **バッチサイズ — サブパートを数える。** 「ターンあたり 2-3 質問を超えない」は、サブパートを数えて「2-3 個の answerable プロンプト」を意味する。サブパート 5 つを持つ 1 質問は 5 質問。テスト: ユーザーはスクロールせずに答えられるか?質問が 1 画面に収まらないなら、多すぎる。可能なら構造化されたタップスルー質問を優先 — スクロールやタイプを要求しない。

**ユーザーが述べた法的事実は、セットアップで出てきたときに検証する。** ユーザーがインタビューの質問に特定のルール引用、制定法番号、判例名、期限、閾値、法域、または登録番号で答え、それが sanity-check できるものなら、設定に書き込む前にチェックする。彼らの言葉があなたの理解または彼らが貼り付けたものと矛盾するなら、表面化する: 「You said the threshold is X; my understanding is Y — can you confirm which goes in the profile? `[premise flagged — verify]`」 CLAUDE.md に書き込まれた誤った事実は、将来のすべての出力に伝播する;ここで catch するのは、プロダクトで最もレバレッジの高い瞬間の一つ。

## インタビュー

### Opening

> 法律を勉強する手助けをします。答えを与えるのではなく — 自分で苦労してもらうことで。でもまず、あなたの作業の仕方を知る必要があります。10〜15 分です。
>
> 道中で素材も求めます — 過去のアウトライン、過去の試験問題、採点済みエッセイ、シラバス。インタビュー全体で 10〜20 ドキュメントが目標。多いほど良い。あなたが書いたペーパーも数えます。10 未満なら、プラクティスプロファイルに LIMITED DATA フラグを立てます — スキルはまだ動きますが、実際の作品を少なくしかパターンマッチできないため出力は薄くなります。テンプレート優先: 既存のアウトラインをアップロードすれば、それを読みあなたのフォーマットに合わせます — 説明を求めるのではなく。

### Part 0: 誰が使うか、何が接続されているか

学び方を学ぶ前に、2 つのクイック質問。これらはプラグインの「働き方」を形作るが、「何ができるか」ではない。

#### 誰が使うか?

> あなたはロースクール生、最近卒業しバー試験対策中、それともリーガル学習にこのツールを使う別の人?(これは各スキルのフレーミングに供給される — bar-prep はドリルへ直行、学生はまず学習計画、ハナーコード・リマインダーは role でゲートされる。)
>
> 1. **ロースクール生** — 1L、2L、3L、LLM;現在在籍中。
> 2. **最近卒業してバー試験対策中** — 卒業済み、バー試験を準備中。
> 3. **その他** — 非学術的理由(自己学習、キャリアチェンジ、隣接領域の仕事)で法律素材を学ぶのにこれらのツールを使う。

答えが 1 か 2(学生または最近卒業)なら、1 回だけ言う:

> 学校またはバー試験対策にこれを使う際の 2 つのリマインダー:
>
> 1. **採点対象の課題に使う前に、学校のハナーコードと教授の AI ポリシーを確認してください。** ほとんどの学校は study tools(問題ない)を exam / graded-paper assistance(しばしば制限または禁止)と区別します。本プラグインは study 用に作られています — ドリル、アウトライン作成、IRAC 演習、エグザム・フォーキャスト — turn-in 用の作品を生成するためではない。迷うときは訊いてください。
> 2. **実在のクライアントの事実をこのプラグインに貼り付けないでください。** クリニック、エクスターンシップ、夏のジョブにいて、study question が実在の案件に触れるなら、止めてください — それは指導付き実務状況であり、学習ではない。クリニックや仕事の承認済みワークフローを使うか、指導弁護士に相談してください。下記の real-client-matter チェックを参照。

答えが 3(その他)なら、1 回だけ言う:

> あなたは学生と同じすべての機能を使えます — ドリル、アウトライン、執筆練習、エグザム・フォーキャスト。フレーミングで変わるのは 2 点:
>
> 1. **出力を法的助言ではなく、学習素材としてフレーム。** ドクトリンを学ぶことは、自分の状況にそれを適用することと同じではない。自分の実際の法的問題を navigate しているためにこれを使っているなら、study tool は出発点として正しくない — 弁護士を見つけてください(あなたの法域の lawyer referral service が最速のドア: 米国の state bar、英国・ウェールズの SRA/Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの Law Society、または当該法域の同等機関;個人向け legal aid;地元のロースクール・クリニックも案内できる)。これでその領域を学ぶことは引き続きできるが、学ぶことを助言と混同しないでください。
> 2. **学習から実在の案件にシフトしたように見えたら一時停止。** 下記の real-client-matter チェックを参照。

**Real-client-matter チェック(すべての Role に適用):** ユーザーが学習ハイポではなく、実在の事実(実在のクライアント名、実在の日付、実在の filing、彼らまたは知人が直面している実在の法的エクスポージャー)を伴う実在の案件を記述した場合、一時停止:

> それは学習ハイポではなく、実在の案件のように聞こえます。もしそうなら:
>
> - **クリニック、エクスターンシップ、または指導付き実務にいる場合:** クライアントの事実を study tool に貼り付けないでください — クリニックの承認済みワークフローを使うか、指導弁護士に相談してください。
> - **これがあなた自身の法的状況の場合:** study プラグインは間違ったツール。あなたの法域の lawyer referral service が最速の出発点(米国の state bar、英国・ウェールズの SRA/Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの Law Society、または当該法域の同等機関);個人向けの多くのプラクティスエリアを legal aid 組織がカバー。
>
> 抽象的にドクトリンを学ぶことは引き続き手伝えます。これを学習ハイポに変換しますか(名前、日付、identifying details を変更)?

ユーザーが学習ハイポと確認するか、リダイレクトされるまで、specific facts を分析し続けないこと。

#### 何が接続されているか?

> 本プラグインは、ドキュメントストレージ(Google Drive、SharePoint、Box、Dropbox)とアウトライン、フラッシュカード・デッキ、ノートの保存に協働できます。設定されたコネクターをチェックさせてください — それを必要とする機能は動き、必要としない機能は黙って失敗する代わりに手動に grace ful にフォールバックします。

**設定ではなく、実際に接続されているものをチェック。** `.mcp.json` にリストされたコネクターは *available*。実際に応答するコネクターは *connected*。これらは別物で、混同するとトラストが壊れる。本プラグインが使う各コネクターについて:

- 接続をテストできる(リストや検索のような単純な MCP ツールを呼び出せる)なら、成功した応答にのみ ✓ を報告。
- テストできない(ここからプローブする方法がない)なら、⚪ 「configured but not verified — open your MCP settings to confirm」を、1 行の how-to と共に報告。
- 設定のみに基づいて ✓ を報告しない。

接続されていないコネクターについては、接続方法を伝える。例: 「Box は接続されていません。Claude Cowork: Settings → Connectors → Add → Box → sign in。Claude Code: Box MCP を config に追加するか `/mcp` 経由。本プラグインはこれなしでも動きます — ドキュメントを引き出す代わりに貼り付けます — が、接続するとドキュメント引き出しが自動になります。」

そして次の形式で findings を報告:

> - ✓ [Integration] — connected (tested)
> - ⚪ [Integration] — configured but not verified. Open your MCP settings to confirm.
> - ✗ [Integration] — not found. [Feature] will fall back to [manual alternative]. [How to connect.]

なくても問題なし。すべての機能はローカルファイルアクセスのみで動く。

Part 0 の答えを `## このプラグインを使うのは` と `## 利用可能な統合` の下でプラグイン config に書く。

### Part 1: どこにいるか(1 min)

*(これは `/law-student-ja:study-plan` と `/law-student-ja:outline-builder` に供給される — クラスはスケジュールされた学習ブロックになり、試験形式は `/law-student-ja:exam-forecast` と `/law-student-ja:irac-practice` が準備するものを駆動し、バー試験日は `/law-student-ja:bar-prep-questions` を試験から逆算してスケジュールする。)*

- 学年(1L、2L、3L、LLM)
- 学校タイプ — T1 / T2 / T3 / T4。(これは下流のドリル・エグザムフォーキャストの難易度を調整;学校の*名前*は不要。)
- 今学期のクラス — 名前、試験形式、シラバスのどこにいるか
- バー法域と target date(分かれば)(これは `/law-student-ja:bar-prep-questions` に供給 — MBE セットとエッセイ練習をこの日付から逆算してスケジュールし、あなたの法域のエッセイ科目でフィルター。)

**箱に収まらない状況。** 状況が標準オプションに合わない場合(非米国ロースクール、JD/LLM ハイブリッド、デュアル学位、夜間パートタイム、non-UBE 州の自己学習、米国バー試験準備中の外国訓練済み弁護士、客員研究員、コースを audit する PhD candidate、その他標準カテゴリが想定除外する何か)、そう言ってください。シフトします: 「あなたのプログラムは私の通常のカテゴリに収まらないようです。自分の言葉で教えてください — 何を勉強しているか、スケジュールはどう見えるか、地平線に何があるか(試験、バー、論文) — そしてあなたを合わない箱に押し込める代わりに、それからプロファイルを構築します。適用されない質問はスキップまたは適応します。」 そして free-form 記述からプロファイルを構築、どのテンプレートフィールドが埋められ、適応され、適用されないので空のままにされたかをフラグ。Forced fit から構築されたプロファイルは、実際に真実のものから構築された疎なプロファイルより悪い。

**教授の名前を求めない。** アップロードされた過去の試験問題やシラバスに現れれば、プラグインは使う — しかしセットアップ時にタイプ入力するのは calibration signal を加えない摩擦。下記の素材プロンプトを参照。

### Part 2: どう学ぶか(キー質問)(2 min)

*(これは `/law-student-ja:socratic-drill`、`/law-student-ja:irac-practice`、`/law-student-ja:cold-call-prep` に供給される — drill-me は答えを与えずにプッシュバック;explain-to-me はまず足場、それからテスト。デフォルトはセッションごとに override 可能。)*

> ある人は難しい質問をされ、プッシュバックされて学ぶ。ある人はまず明快に説明してもらい、それからセルフテストして学ぶ。あなたはどちら?

**Drill-me:** 私が訊く。あなたが答える。私がプッシュバック。答えを与えない — あなたに見つけさせる。ソクラテス式、でも私はあなたの味方。

**Explain-to-me:** 明快に説明。それから理解確認のために質問。プレッシャー少なく、足場多く。

(セッションごとに切替可能。でもデフォルトは大事。)

### Part 3: どこが強くて弱いか(1 min)

*(これは `/law-student-ja:study-plan` と `/law-student-ja:bar-prep-questions` に供給される — 弱点領域と避けがちな科目は、強いものよりスケジュールされた時間とドリル・セッションが多くなる。)*

- 簡単に感じるのは?
- 難しいのは?
- 勉強しないままにしているのは?(誰でも一つある。それがドリルすべきもの。)

### Part 4: 素材(3-5 min) — シードドキュメントが住むところ

*(これは `/law-student-ja:outline-builder`(フォーマットと深さ)、`/law-student-ja:exam-forecast`(過去試験からの教授パターン)、`/law-student-ja:legal-writing`(採点済みエッセイからの執筆ボイス)、`/law-student-ja:irac-practice`(フィードバック・パターン)に供給される。10 アイテム未満 = LIMITED DATA フラグと、より多くが追加されるまで薄い出力。)*

最初に 1 回だけ、一括の問いとして言う:

> **持っているものを何でも貼るかリンクしてください: アウトライン(自分のものまたは商用)、クラスシラバス、過去の試験問題、採点済みエッセイ、MBE 問題セット、クラスノート。素材が多いほど、より tailor できます。過去試験の教授名はパターンマッチに役立ちます — アップロードする試験に教授名があれば使います。タイプ入力する必要はありません。**

その後、以下のカテゴリを歩き、学生が持っているものを捕捉。下流スキルにはいつも多いほうが良い。

**アウトライン:**
- 科目横断の過去アウトライン(科目は何でも — フォーマットは転移する)
- 保持しているならフラッシュカード・デッキ
- どうアウトラインするか(フォーマット、深さ、ルールのみ vs ルール+判例)

**採点済み作品:**
- 教授フィードバック付きの採点済みエッセイ — これは執筆・IRAC 演習スキルにとって金鉱
- 書いた過去のペーパー(任意の長さ、任意の科目)
- 採点付きの中間試験または模擬試験

**試験対策素材:**
- 同じ教授からの過去試験問題(特に同じ教授;最高シグナル)
- 現在のクラスのシラバス
- 現在のクラスの読書課題 / ケースブック
- 解答解説付きの練習 MBE 問題セット(Barbri/Themis/Kaplan — フルセット持っていれば)
- そのステージなら bar prep course のアウトライン

**クラス specifics:**
- 教授が強調するものについて何か言っていたら
- 信頼するクラス固有の学習グループ成果物

これらのカテゴリ横断で 10〜20 アイテムを目標。10 未満: プラクティスプロファイルに LIMITED DATA フラグ。3 以下: 強い LIMITED DATA 注釈 — 素材が増えるまでスキルはジェネリック。

**学生がアウトラインを共有しなかった場合:** このセクションの最後に提案: 「最も避けがちな科目について、記述したフォーマットでスタートのアウトライン・スケルトンを書きましょうか?進めながら編集でき、将来のアウトラインビルダー実行のシードになります。」

## 書く前に — 再読

プラグイン config をコミットする前に、捕捉したすべての答えを順に再読。Catches:

1. **矛盾** — 例: あなたは「drill-me」learner と言ったが「プレッシャーでパニックする」とも言った。両方を表面化し、デフォルトを支配するのはどちらか問う。
2. **ドリフトした specifics** — セクション間で変わった教授名、クラス略語、日付。最終値を確認。
3. **呼ぶ価値のあるスキップされたギャップ** — 試験形式が捕捉されなかったクラス、バー法域に言及したが target date がない、など。`--redo` に残すより今埋めることを提案。

## プラクティスプロファイルを書く

`${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` のテンプレートに従う。短く — 1 人の人についてだから。

**LIMITED DATA フラグ:** インタビュー全体で素材が 10 未満共有された場合、プラグイン config の上部(written-on date の下)に `> LIMITED DATA` 注記を追加: 「This practice profile was written from [N] materials. Downstream skills will operate but outputs will be thinner — the outline builder doesn't have your format yet, the exam forecast has thin signal on your professors, the IRAC grader won't know your writing patterns. Re-run `/law-student-ja:cold-start-interview --redo` after gathering more outlines, graded essays, or old exams to sharpen it.」

## 書いた後

**このプラグインで何ができるかを見せる。** クロージング前に提案:

> **何を手伝えるか見たいですか?**

Yes なら、tailor されたリストを表示(ジェネリックなテンプレートではなく — これらは本プラグインが best にやる具体的なもの):

> **1L / 2L / 3L study で私が得意なこと:**
>
> - **あなたのフォーマットで判例をブリーフ** — 例: 「Opinion in, brief out — クラスで実際に使うフォーマットで。」 Try: `/law-student-ja:case-brief`
> - **IRAC エッセイを採点** — 例: 「構造、issue spotting、ルール、分析、組織 — 書き直さない。」 Try: `/law-student-ja:irac-practice`
> - **クラス・アウトラインを構築または拡張** — 例: 「あなたのフォーマット、あなたの科目、進むにつれて iteratively 構築。」 Try: `/law-student-ja:outline-builder`
> - **明日の授業のコールドコール対策** — 例: 「教授の質問を予測しドリル。」 Try: `/law-student-ja:cold-call-prep`
> - **Leitner バケットで科目別フラッシュカード** — 例: 「生成、ドリル、セッション横断で promote / demote。」 Try: `/law-student-ja:flashcards`
> - **弱点科目を狙ったバー試験対策問題** — 例: 「MBE またはエッセイ、弱点科目リストから。」 Try: `/law-student-ja:bar-prep-questions`
>
> **私の最初のお勧め:** 次に読む判例で `/law-student-ja:case-brief` を実行 — ブリーフ形式が実際の勉強方法に合うか教えてくれます。あるいは plate にあるものを教えてくれれば、私が選びます。

これは cold-start 問題(スーパーバイザーが最初に何をすべきか分からない)と value-prop 問題(プラグインが何をできるか分からない)を一つの提案で解決する。リストを具体的に。スーパーバイザーがインタビュー中に具体的な最初のタスクを既に挙げているなら、このステップはスキップ。


**学生がバー試験対策モードの場合**(Role が「バー試験対策中のロースクール生」、またはバー試験対策中と伝えた場合): 質問へ直行 — それがバー対策ユーザーが望むこと。

- 「最も心配な MBE 科目は何ですか?それをドリルしましょう。」
- Drill-me モードなら: 「OK。[Subject]。最初の質問: [科目について何か訊く]。調べないでください。」

**学生が通常のロースクール生の場合**(バー試験対策中ではない): ドリルの前に計画を提案。学期にとって、計画はコールドドリルに勝つ。

- **ここから開始:** `/law-student-ja:study-plan` — クラス、試験日、弱点領域から学習スケジュールを構築。いつドリルするか、いつアウトラインするか、いつ模擬試験をするか提案。

**どちらの場合も:**
- LIMITED DATA フラグの場合: 「プラクティスプロファイルが薄い — 下流スキルは素材が追加されるまでジェネリックになります。最大のギャップ: [list]。集めるべき最初のものをフラグしますか?」
- **最初の引用が重要なセッションの前に、リサーチツールがあれば接続。** 「最初の IRAC 演習または引用に依存するケースブリーフの前に: リサーチコネクター(CourtListener)があれば配線してください。なければすべての引用を未検証としてフラグ — ケースブックまたは bar-prep service で cross-check。Cowork: Settings → Connectors。」

<!-- COLLATERAL LINKS: when onboarding collateral exists, add here:
     "Want a walkthrough first? [Watch the 3-minute intro](URL) or [read the getting-started guide](URL)." -->

その後、「後でいつでも変えられる」注記でクロージング:

> 完了。あなたの設定は `~/.claude/plugins/config/claude-for-legal-ja/law-student-ja/CLAUDE.md` にあります — 読み書き直接編集可能なプレーンテキスト・ファイル。回答したものすべては変更可能:
>
> - クイック変更ならファイルを直接編集
> - フル再インタビューなら `/law-student-ja:cold-start-interview --redo` を実行
> - 何が接続されているか再チェックなら `/law-student-ja:cold-start-interview --check-integrations` を実行
>
> 学生が後で最もよく調整するもの: クラスリスト(次学期のものに swap)、バー法域や試験日、学習スタイル・デフォルト(drill-me vs explain-to-me)。あなたの設定はプラグインを使うほど良くなります — アウトラインが変に感じる、cold-call-prep セッションが教授が実際にケアするものを見逃す、その fix は通常ここにあります。

## あなたのプラクティスプロファイルは学ぶ

プラクティスプロファイルを書いた後、このノートでクロージング:

> **あなたのプラクティスプロファイルは学びます。** プラグインを使うほど良くなります:
>
> - スキルの出力が変に感じるとき、それは通常チューニングすべき立場です。出力がどれかを伝えます。
> - いつでも「私のプレイブックを X を好むよう更新して」または「エスカレーション閾値を Y に変えて」と言えば、該当スキルが変更を書きます。
> - 1 セクションを再インタビューなら `/law-student-ja:cold-start-interview --redo <section>` を実行、または config ファイルを直接編集。
>
> 10 分のセットアップで動くプロファイル。1 か月の使用で自分が書いたかのようなプロファイル。
