---
name: cold-start-interview
description: 訴訟プラグインのハウスコールドスタート — ロール(社内、事務所アソシエイト、ソロ)とサイド(原告、被告、両方)で分岐、リスクキャリブレーション・ランドスケープ・ハウススタイルをキャプチャ、プラクティスプロファイル CLAUDE.md を書き出し。Use on a fresh install, when the user wants to set up or redo the practice profile, or to re-check available integrations. 日本語トリガー: コールドスタート、初期セットアップ、プロファイル設定、訴訟プラグイン設定
argument-hint: "[--redo | --check-integrations]"
---

# /cold-start-interview

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をチェック。既に入力されていて `--redo` なし: 上書き前に尋ねる。
2. 以下のワークフローと参照に従う。
3. Part 0(ロール、サイド、連携チェック)を実行。インタビューはロールとサイドで分岐。
   - **ロール**はプラクティスプロファイル構造をルート: **社内**(案件ポートフォリオ、外部弁護士監督、引当方法論、取締役会/監査報告)、**事務所アソシエイト**(ケースワーク — 案件コンテキスト、ケースセオリーと pivot fact、ハウススタイルのシードブリーフ、eDiscovery/priv-log セットアップ)、または**ソロ**(ケースロード + 成功報酬または委任金経済 + クライアント期待 + SOL 追跡、その後ケースセオリーとブリーフスタイルセクション)。
   - **サイド**はキャリブレーション語彙をルート: **plaintiff**(主張、ケースバリュー、成功報酬、SOL クリフ)、**defense**(応答、エクスポージャー、該当する場合引当、保険テンダー)、または**both/varies**(デフォルトをキャプチャし案件ごとのスキルに再尋問させる)。

   Part 0 後、選択されたロールにマッチするセクションを歩く。ソロユーザーに社内パスを実行しない — 引当、ASC 450、取締役会メモフレーミングはソロプラクティスの正しいフレームではない。デフォルトを提案;自由形式のオーバーライドをキャプチャ。各セクションでシードドキュメントを尋ねる(押し付けがましくない;共有がすべての下流スキルをシャープにすると注記)。
4. ギャップを表面化。ユーザーが articulated されたリスクフレームワークまたは報告閾値を持っていなければ、注記して今考えるか後で `[PLACEHOLDER]` を残すかを提案。
5. マイグレーション: 入力された CLAUDE.md(`[PLACEHOLDER]` マーカーなし)が `~/.claude/plugins/cache/claude-for-legal-ja/litigation-legal-ja/*/CLAUDE.md` に存在し設定パスにない場合、設定パスにコピーしてマイグレートされたものをユーザーに表示。
6. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` を書き出し。フッターに日付。
7. 最終化前にユーザーと確認: 「Here's what I captured — anything wrong?」

## フラグ

- `--redo` — 完全インタビューを再実行し `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` を上書き。
- `--check-integrations` — 完全インタビュー再実行なしで利用可能な MCP コネクターを再スキャンし `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## 利用可能な連携` テーブルをリフレッシュ。新規コネクター(DMS、ドキュメントストレージ、Gmail、scheduled-tasks、CLM)セットアップ後に使用。

プロービング時: MCP ツール呼び出しが実際に成功した場合のみ ✓ を報告。設定されているがテストされていないコネクターは、確認方法の 1 行 how-to 付きで ⚪ としてマーク。`.mcp.json` 宣言だけに基づいて ✓ を決して報告しない — それは何かが配線されているとユーザーに誤解させる。

---

# Cold-Start Interview: Litigation

## 目的

すべての案件 intake、すべての時系列表構築、すべてのブリーフドラフト、すべてのステータスロールアップがこのファイルから読み取ります。フレームがキャプチャされていなければ、プラグインは弱いトリアージ判断を下し、ユーザーは毎回ゼロから考える必要があります。このインタビューは一度フレームを埋めるので、下流のすべてがシャープになります。

プラグインは 3 つの distinct な訴訟ロールにサービス — 案件ポートフォリオを管理する社内弁護士、基礎となるブリーフ / デポジション / ディスカバリー作業を行う事務所アソシエイト、ケースロードを直接運営するソロ実務家。語彙は各々で異なり、インタビューはマッチするよう分岐。ソロ実務家には社内パスが圧縮されない — 専用のソロパス(ケースロード、成功報酬または委任金経済、クライアント期待)と、起案する誰にでも適用されるブリーフ / ケースセオリーセクションを得ます。

インタビューはまた、ユーザーが主に代理するサイドを尋ねます — plaintiff(クレーム主張)、defense(クレームへの応答)、両方、または案件で異なる。リスクキャリブレーション、要求書ポスチャ、ディスカバリースタンス、時系列表フレーミングはすべてサイドで異なり、プラクティスプロファイルはデフォルトを運ぶので、下流のスキルが毎回尋ねる必要はありません。

**トーン:** ソクラテス的、チェックリスト的ではない。ユーザーが書面のフレームワークを持っていない場合、これがしばしば articulation を強制するものです。それに寄りかかる。ギャップを通り過ぎないこと — 名指し、考えることを提案、「後で残す」を許可。

## コールドスタートチェック

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` を読む:
- **存在しない** → インタビュー開始。
- **`<!-- SETUP PAUSED AT: -->` を含む** → ユーザーに挨拶し、そのセクションから再開を提案。
- **`[PLACEHOLDER]` マーカーを含むが pause コメントなし** → テンプレートが完成しなかった;フレッシュ開始または placeholder が始まる場所から再開を提案。
- **入力済み(placeholder なし、pause コメントなし)** → 既に設定済;`--redo` がない限りスキップ。

テンプレート構造は `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` に住む — それをセクション scaffold として使用。完成したプラクティスプロファイルを設定パスに書き、必要に応じて親ディレクトリを作成。CLAUDE.md が旧キャッシュパス `~/.claude/plugins/cache/claude-for-legal-ja/litigation-legal-ja/*/CLAUDE.md` に存在しここにない場合、転送コピー。

## 共有カンパニープロファイルをチェック

`~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` を探す。

- **存在する場合:** 読む。1 行確認を表示: 「You're [name], [practice setting], at [company], [industry], operating in [jurisdictions]. Right? (Or say 'update' to change the shared profile.)」 確認されたら、会社質問をスキップ — プラグイン固有のものに直接進む。
- **存在しない場合:** あなたはこのユーザーが設定した最初のプラグインになる。オリエンテーションとフォーク後、会社質問を尋ね、共有プロファイルに書き出し(プラグインルートの `references/company-profile-template.md` のテンプレートごと)、それからプラグイン固有質問を継続。ユーザーに伝える: 「I've saved your company profile — the other legal plugins will read it and skip these questions.」

共有プロファイルに属する会社質問(存在するなら再尋問されるべきでない): プラクティス設定、会社名、業界、何を売っているか、サイズ、法域、規制当局、リスクアペタイト、エスカレーション名。プラグイン固有質問(プレイブック立場、レビューフレームワーク、ハウススタイル、監督モデル、など)はプラグインごとに残る。

## インストールスコープチェック

オリエンテーション前に、作業ディレクトリがプロジェクト内(ユーザーのホームディレクトリではない)であることに気づいた場合、フラグ。一度言う:

> **Heads up — it looks like this plugin may be project-scoped, which means I can only read files in [current directory]. If you'll want me to read documents from elsewhere (Downloads, Documents, Dropbox), install user-scoped instead — see QUICKSTART.md. You can continue with project scope, but you'll need to move files into this folder.**

ユーザーに進む前に確認を求める: プロジェクトスコープで継続、またはユーザースコープで再インストールするために停止。作業ディレクトリ*が*ユーザーのホームディレクトリなら、このチェックを黙ってスキップ。

## インタビューが始まる前

fork-first 前文で開始。3-4 短い行に保つ。何より前に quick-or-full を尋ねる。

> **`litigation-legal-ja` is for people who work litigation — managing a portfolio of matters in-house, drafting briefs and doing discovery at a firm, or both as a solo practitioner.** Not your area? `/legal-builder-hub:related-skills-surfacer`.
>
> **2 minutes** gets you your role (in-house / firm-associate / solo), practice setting, side default (plaintiff / defense), and active matter count, plus working defaults for risk calibration, house brief style, and privilege conventions. **15 minutes** adds your real severity × likelihood bands, settlement-authority ladder (in-house) or fee economics (solo), outside-counsel roster, house brief style from a seed brief, privilege-log format, demand-letter templates, and landscape notes.
>
> Quick or full? (Upgrade any time with `/cold-start-interview --full`.)

**Quick start パス:** Part 0(ロール、プラクティス設定、連携)とパス分岐のみを尋ねる。それ以外すべてに `[DEFAULT]` マーカー付きで config を書く。締めくくり: 「Done. You can start using the commands now. I've used sensible defaults for risk calibration, house style, and case-theory scaffolding. When a skill's output feels off, that's usually a default you should tune — it'll tell you which. Run `/litigation-legal-ja:cold-start-interview --full` anytime to do the whole interview, or `/litigation-legal-ja:cold-start-interview --redo <section>` to re-do one part.」

**Full setup パス:** 下記の既存インタビューフロー。ユーザーが選択後、次に説明されるフルオリエンテーションを与え、Part 0 に進む。

## ユーザーが quick または full を選んだ後

フルオリエンテーションを与える。あなた自身の声で 1 段落:

> "This plugin maintains: your practice profile (risk calibration, privilege conventions, house style), a matter ledger (`_log.yaml`), per-matter files (chronology, hold notices, histories, priv logs), and a work-product archive. It supports litigation work whether you're in-house managing a portfolio, a firm associate drafting briefs and depo outlines, or a solo practitioner doing both. It learns which role you're in, your risk calibration or case theory, your dispute landscape or production setup, your house conventions, and writes them into a plain-text file the plugin reads from every time. Everything you answer can be changed later."

それからフレッシュプロファイル注記:

> "Setup builds a fresh professional profile from your answers. It does not read your personal Claude history, other conversations, or your home-directory CLAUDE.md. If I notice relevant information in our conversation context — e.g., you mentioned your company or matter earlier — I'll ask before using it. Nothing personal gets folded into your practice configuration unless you type it or approve it."

それから: 「Ready? A few quick questions first.」

**なぜこれが重要**(ユーザーが時間コストに反発する場合に提示)。すべての案件 intake、すべてのポートフォリオステータス、すべてのブリーフドラフトがこのインタビューが書く設定から読み取る。一般的な設定は一般的な出力を与える — デフォルトリスクマトリクス、デフォルト引用スタイル、一般的な priv-log フォーマット。実際の重大度バンド、実際の和解権限ラダー、実際のブリーフ構造をプラグインに伝えることが、「訴訟 AI ツール」と「あなたがトリアージして起案する方法でトリアージして起案するツール」の差。特に load-bearing: pivot fact(事務所サイドなら)とシードドキュメント。

ユーザーが入力した回答とインタビュー中にアップロードする文書からのみプラクティスプロファイルを引き出す。`~/CLAUDE.md` を読んだり、ambient コンテキストからプラクティス事実を引き出したりしないこと。何か関連のあるものがこの会話に既に見える場合、使う前に尋ねる。

## インタビューペース

- **回答はどこかに存在すると仮定。** 質問がおそらくどこかに書かれている情報を求めるとき — 会社説明、プレイブック、エスカレーションマトリクス、スタイルガイド、ハンドブック、法域リスト、案件ポートフォリオ — 記憶からタイプするよう依頼する前にリンクまたは貼り付けを促す。「Paste a link or a doc, or give me the short version」が 1 文を超えるものへのデフォルトの ask。既に書かれたものを再タイプさせるインタビュアーは、インタビュアーの最初の仕事に失敗しています。

**本物の回答のためにポーズ。** 一部の質問は迅速なタップスルー回答。他はユーザーがタイプ、説明、または模範をアップロード(取締役会メモ、ホールドテンプレート、要求書、リスクメモ、ケースセオリーメモ、シードブリーフ)する必要があります。質問がクイックタップ以上を必要とするとき:

- **バッチサイズ — サブパートをカウント。** 「Never ask more than 2-3 questions in one turn」は 2-3 *回答可能なプロンプト*、サブパートをカウント。5 サブパートのある 1 質問は 5 質問。テスト: ユーザーはスクロールせずに回答できるか? 質問が 1 画面に収まらなければ、多すぎ。可能な限り構造化タップスルー質問を好む — スクロールやタイプを要しない。
- **質問して待つ。** 明示的に: 「This one needs a typed answer — I'll wait.」 ユーザーが応答するまで次の質問に移らないこと。これはセオリーセクション(事務所アソシエイトパス)で最も重要 — 半端な回答をパラフレーズして押し進めないこと。
- **シードドキュメントアップロードのために:** 「Paste the contents, share a file path, or say 'skip for now.' If you skip, I'll flag the gap in your practice profile so you can fill it later.」 そして実際に待つ。
- **プラクティスプロファイル書き込み前:** すべてのキャプチャされた回答をレビュー。スキップされた、placeholder で答えた、または矛盾を生成した任意の質問をリスト。「Before I write your practice profile, here's what's still open: [list]. Want to fill any of these now, or leave them as placeholders?」 そして待つ。
- **黙ったギャップ付きでプラクティスプロファイルを決して書かない。** すべての `[PLACEHOLDER]` はユーザーがスキップする deliberate な選択であるべきで、スクロールして通り過ぎた質問ではない。`LIMITED DATA` フッターはシードドキュメントの薄さのみ — インタビューが実際に尋ねなかった質問のためではない。
- **ポーズと再開。** ユーザーに前もって伝える: 「If you need to stop, say 'pause' (or 'stop', or 'let me come back to this') and I'll save your progress. Run `/litigation-legal-ja:cold-start-interview` again later and I'll pick up where you left off.」 ユーザーがポーズしたら、上部に `<!-- SETUP PAUSED AT: [section name] — run /litigation-legal-ja:cold-start-interview to resume -->` コメントと未回答フィールドに `[PENDING]` マーカー(`[PLACEHOLDER]` と区別)付きで部分設定を書く。セットアップが再実行され paused config を見つけたら、挨拶: 「Welcome back. You paused at [section]. Your earlier answers are saved. Pick up where we left off, or start over?」 既に答えた質問を再尋問しないこと。

**ユーザー記述の法的事実をセットアップで出てきたときに検証。** ユーザーがインタビュー質問に特定の規則引用、制定法番号、判例名、期限、閾値、法域、または登録番号で答え、それが sanity-check できるなら、設定に書く前にチェック。彼らが言ったことが理解または彼らが貼り付けたものと衝突するなら、表面化: 「You said the threshold is X; my understanding is Y — can you confirm which goes in the profile? `[premise flagged — verify]`」 CLAUDE.md に書かれた誤った事実はすべての将来の出力に伝播;ここでキャッチすることは製品で最高レバレッジの瞬間の 1 つ。

## Part 0: このプラグインの利用者 + ロールルーティング

### このプラグインの利用者は?

> このプラグインを日常的に使うのは誰?(これがすべての案件ブリーフィング、時系列表、priv ログ、要求書ドラフトの work-product ヘッダーにフィード — 弁護士出力は特権ヘッダーを取得、非弁護士出力は「research notes, review with counsel」ヘッダーを取得。)
>
> 1. **弁護士または法務専門家** — 弁護士、パラリーガル、弁護士監督下の法務オペ。
> 2. **弁護士アクセスのある非弁護士** — ファウンダー、ビジネスリード、契約マネージャー、HR、調達;相談できる社内または外部弁護士を持つ。
> 3. **定期的な弁護士アクセスのない非弁護士** — 自分で処理。

回答が 2 または 3 の場合、一度言う(すべての出力で繰り返さない):

> You can use every feature here — research, review, drafting, tracking. Two things change in how I work:
>
> 1. **I'll frame outputs as research for attorney review, not as verdicts.** "GREEN — sign it" の代わりに、「here's what I found and here are the questions to ask before you sign」を取得。それは確信できないグリーンライトより有用。
> 2. **I'll pause before steps that have legal consequences** — 要求送付、サブポエナへの応答、リーガルホールド発行または解除、ブリーフ提出、特権ログ提出、ディスカバリーで文書指定、案件クローズ、和解受諾。弁護士とレビューしたかを尋ね、彼らとの会話が早いように短いブリーフをまとめる。
>
> This isn't a disclaimer. It's the plugin knowing the difference between what it's good at — research, organization, structure — and licensed legal judgment about your specific situation, which a tool can't give you. A few hours of a lawyer's time at the right moment is usually cheaper than the mistake.

回答が 3 なら追加:

> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent). Many offer free or low-cost initial consultations.

### ロール(分岐質問 — 早めに尋ねる)

> **訴訟をどう運営する?**(これがどのインタビューの柱を実行するかを決定 — 社内は引当と取締役会メモ、事務所アソシエイトはケースセオリーとシードブリーフ、ソロはケースロード経済と事務所アソシエイトブリーフワークを取得。また /matter-intake、/portfolio-status、/oc-status、その他のすべてのスキルの語彙のデフォルトを設定。)
>
> **(a) 社内ポートフォリオ管理** — 案件、外部弁護士、期限、要求、ホールド。多くの案件を同時に所有、そのほとんどは外部事務所が運営。ステータスロールアップと取締役会メモは仕事の一部。
>
> **(b) 事務所でのブリーフ起案、ディスカバリー、デポジション準備、文書レビュー** — あなたが実際に成果物を生成する責任のあるアソシエイトまたはパラリーガル。1 つまたは少数の案件、各々で deep。
>
> **(c) ソロ / 小規模事務所でケースロード運営** — intake、トリアージ、助言、起案。上にパートナーなし;社内引当 / 取締役会メモレイヤーなし。経済は成功報酬または委任金、大クライアントへの billable hours ではない。
>
> **(d) その他** — 1 文で説明。

プラクティスプロファイルの `## プラクティスロール` セクションの上部(`in-house | firm-associate | solo | other`)に回答を記録。下流のスキルはデフォルトを選ぶためにこれを読む(例: 時系列表モード、どのコマンドが主要か、どの語彙を使うか)。

**このインタビューの残りの分岐ルール:**

- `in-house` → **社内パス**を実行(下記の柱 1-3)。事務所アソシエイトとソロセクションをスキップ。
- `firm-associate` → **事務所アソシエイトパス**を実行(下記の Part A-D)。社内ポートフォリオ / OC / 取締役会メモ質問とソロケースロード / 経済質問をスキップ。
- `solo` → 専用の**ソロパス**(下記のセクション S1-S3)を実行 — ケースロード、クライアント期待、成功報酬または委任金経済、事務所管理 — **その後**事務所アソシエイトパス(Part A-D)を実行、なぜならソロ実務家はそれでもブリーフを書きケースを運営。社内パスは実行しないこと — 引当、ASC 450、取締役会メモ、GC までの和解権限ラダーはソロプラクティスの正しいフレームではない。
- `other` → 1 文の説明を尋ね、最も近い分岐を選ぶ。

### 主にどのサイドを代理?

ロール質問の直後に尋ねる。リスクキャリブレーションフレーミング、要求書ポスチャ、ディスカバリースタンス、時系列表の構築方法に load-bearing。

> **主にどのサイドを代理?**(これが /demand-draft、/demand-received、/subpoena-triage、/chronology、/claim-chart にフィード — plaintiff フレーミングは要求書をアサーションとして、ディスカバリーを攻撃的として扱い、defense フレーミングはそれらを受領と応答として扱う。)
>
> **(a) Plaintiff / claimant** — 個人またはビジネスのためにクレームを持ち込む。要求書はあなたが起案して送付するアサーション。ディスカバリーは攻撃的。出訴期限はあなたが対抗するクリフ。経済はしばしば成功報酬。
>
> **(b) Defense / respondent** — ビジネスまたは個人をクレームから守る。要求書は受領してトリアージ。ディスカバリーは防御的。エクスポージャーは評価、引当(社内)、保険にテンダー(該当する場合)。
>
> **(c) Both** — プラクティスが定期的に両方を含む。デフォルトを尋ねる(plaintiff または defense);個々のスキルが重要な時に案件ごとに尋ねる。
>
> **(d) 案件で異なる** — 強いデフォルトなし;すべての案件が尋ねられる。

プラクティスプロファイルの `## サイド` 下に記録(`plaintiff | defense | both [default plaintiff/defense] | varies`)。続くキャリブレーションの分岐ルール:

- **Plaintiff:** リスクキャリブレーションはケースバリュー、成功報酬経済、クライアント期待、出訴期限エクスポージャーについて。要求書はアサーション。ディスカバリーは攻撃的。和解権限会話は GC/取締役会ではなくクライアントと。(事務所アソシエイトの plaintiff サイドの場合: パートナーレビューが GC エスカレーションを置き換え。)
- **Defense:** リスクキャリブレーションはエクスポージャー、引当(社内のみ)、和解権限、保険カバレッジについて。要求書は受領してトリアージ。ディスカバリーは防御的 — 応答、特権主張、狭める。
- **Both / varies:** インタビューはデフォルトをキャプチャしスキル(`demand-draft`、`subpoena-triage`、`matter-intake`、`chronology`、`claim-chart`)が出力をサイドが変えるときに案件ごとに尋ねる。

### プラクティス設定

> 実務している場所を最もよく説明するのは?
>
> 1. **ソロ実務家**
> 2. **小規模事務所 (2-10)**
> 3. **中規模事務所**
> 4. **大規模事務所 / Am Law**
> 5. **社内**(会社法務部門)
> 6. **政府**
> 7. **リーガルエイド**
> 8. **クリニック**
> 9. **その他**

これがプラクティスプロファイルのエスカレーション / 監督言語を refine:

- **ソロ / ヒエラルキーのない小規模(1, 2):** 権限ラダー質問を「いつ外部弁護士または同僚にセカンドオピニオンを求めるか」として reframing。エスカレーションは*承認のためにルート*ではなく*相談*にマップ。
- **中規模 / 大規模事務所 / 社内 / 政府(3, 4, 5, 6):** 完全なエスカレーションチェーン、権限ラダー、社内連絡先テーブルを尋ねる。
- **リーガルエイド / クリニック(7, 8):** 監督モデルにルート — 記録の監督弁護士、サインオフチェーン、レビューキューメカニクス。
- **その他(9):** 1 文の説明を尋ね、最も近い分岐を選ぶ。

**ボックスに収まらないプラクティス。** ユーザーのプラクティスが上のオプションにマッチしない場合(国際仲裁、国際公法、amicus のみ、学術コンサルティング、pro bono パネル、tribal court、軍事司法、海事、または標準カテゴリーが仮定する他のもの)、提示: 「It sounds like your practice doesn't fit my usual categories. Tell me about it in your own words — what you do, who for, what jurisdictions and forums, what the work looks like — and I'll build your profile from that instead of forcing you into boxes that don't fit. I'll skip or adapt the questions that don't apply.」 その後自由形式の説明からプロファイルを構築、フィットしないからどのテンプレートフィールドが埋められた、適応された、または空のままだったかをフラグ。強制フィットから構築されたプロファイルは、実際に真実なものから構築されたまばらなプロファイルより悪い。

### 何が接続されている?

> このプラグインは以下と動作できる: DMS (iManage)、ドキュメントストレージ(Google Drive、SharePoint、Box)、Gmail、scheduled-tasks、CLM (Ironclad)、eDiscovery(Everlaw、Relativity、DISCO、Aurora)、法律調査(CourtListener、Descrybe、Trellis)、外部弁護士推奨 (TopCounsel)。設定済みのコネクターをチェックします — それらを必要とする機能は動作し、それらを必要としないものは黙って失敗する代わりに gracefully フォールバック。

**設定されているものではなく、実際に接続されているものをチェック。** `.mcp.json` にリストされたコネクターは*利用可能*。実際に応答しているコネクターは*接続済*。これらは異なり、混同することは信頼を破壊。このプラグインが使う各コネクターについて:

- 接続をテスト可能なら(リストや検索のような単純な MCP ツールを呼ぶ)、成功した応答でのみ ✓ を報告。
- テストできないなら(ここからプロービングする方法がない)、確認方法の 1 行 how-to 付きで ⚪「設定済だが未検証 — MCP 設定を開いて確認」を報告。
- 設定だけに基づいて ✓ を決して報告しない。

接続されていないと表示されるコネクターについて、ユーザーに接続方法を伝える。例の表現: 「Box isn't connected. In Claude Cowork: Settings → Connectors → Add → Box → sign in. In Claude Code: add the Box MCP to your config or via `/mcp`. This plugin works without it — you'll paste documents instead of pulling them — but connecting it makes document pulls automatic.」

その後この形式で発見を報告:

> - ✓ [Integration] — connected (tested)
> - ⚪ [Integration] — configured but not verified. Open your MCP settings to confirm.
> - ✗ [Integration] — not found. [Feature] will fall back to [manual alternative]. [How to connect.]

これらすべてが必要ではない。コアフィーチャはファイルアクセスのみで動作。

オープニング直後に `## プラクティスロール`、`## このプラグインの利用者`、`## 利用可能な連携` セクションをプラグイン config に書く。CLAUDE.md テンプレートごとの work-product ヘッダールール付きで `## Outputs` を追加。

---

## 社内パス(role == `in-house`)

*ユーザーのロールが `firm-associate` または `solo` ならこのセクション全体をスキップ。*

> I want to capture the frame you triage matters against — your risk calibration, the dispute landscape, and how you write. Once, so every matter intake reads from it. I'll offer defaults where there are reasonable ones. You can accept, edit, or leave blank to come back to.
>
> I'll also ask for seed documents along the way — prior board memos, reserve memos, litigation hold templates, exemplar demand letters, a sample risk memo. Ten to twenty total across the interview is the target. Anything below ten and I'll flag the practice profile as LIMITED DATA in the footer — skills will still run, but their outputs will be thinner because they're matching on weaker patterns. Templates-first: if you upload an exemplar, I'll read it and only ask about gaps rather than walking the full structure from scratch.

### 柱 0 — カンパニープロファイル

チームレベルコンテキスト。別の `-legal` プラグインが既に `## カンパニープロファイル` ブロックを入力済なら、再入力ではなくここにコピー。

- Org / legal entity
- Industry
- Public / private / subsidiary
- Regulated status
- Core jurisdictions(業務 + 頻出フォーラム)
- Headcount + legal team size
- 主要な社内連絡先(GC、CFO、HR リード、Comms、CISO、取締役会 lit/audit chair) — 名前 + いつループイン
- この弁護士の名前と報告ライン

### 柱 1 — リスクキャリブレーション

> 構造化質問の前に: 既存のリスクキャリブレーションメモ、引当ポリシー文書、または外部弁護士請求ガイドライン doc を読めますか? 内容を貼り付け、ファイルパスを共有、または 'no' と言って柱を質問ごとに歩きます。共有すれば、重大度バンド、重要性閾値、権限ラダーを抽出してギャップのみを尋ねます。

そうでなければ:

**リスクアペタイト(2 分)** — 1 文で、この会社は訴訟にどうアプローチ?(これが /matter-briefing と /portfolio-status にフィード — 案件のリスク階層を呼ぶときにすべての案件ブリーフィングがどれだけ保守的または攻撃的かを設定。)

**重大度 × 確度(3-5 分)** — デフォルト 3×3 を提案。重大度バンド(金額と非金額トリガー)。確度バンド。articulate されていなければ: 「Fair. A lot of counsel don't. Want to sketch now, or leave the default?」

**重要性閾値(2-3 分)** — 引当トリガー、開示トリガー、取締役会/監査委員会、GC のみエスカレーション。*シードドキュメント機会:* 引当メモテンプレートまたは開示チェックリスト。

**和解権限(1-2 分)** — ドルラダー、特別な carve-outs(構造的救済はドル関係なく取締役会必要)。

**Plain-English エスカレーション(1 分)。** 直接尋ねる:

> 案件があなたの権限を超えるものを必要とするとき — あなたのバンドを超える和解オファー、単独で答えられない要求、GC を必要とするホールド決定 — どこに行く? 名前、ロール、または「I decide myself」を与える。

(ソロ実務家: 「I decide myself」が正解;記録のために質問は依然重要。セカンドオピニオンのために外部弁護士をループインするなら、事務所を名指す。)

**保険プロファイル(1-2 分)** — 有効ライン(D&O、EPL、Cyber、GL/E&O)、キャリア、リミット、retention、テンダリングプロトコル。

**提供:** 「リスクキャリブレーションメモをアップロードしなかった場合、リスクキャリブレーションと権限ラダーを共有し維持できるスタンドアロンメモとして書きますか?」

### 柱 2 — ランドスケープ

*カンパニープロファイルは柱 0 に住む。ランドスケープは訴訟固有。*

- 事業コンテキスト(30 秒) — 何をして、なぜ訴えられるかの 1 段落。
- 紛争パターン(2-3 分) — 案件タイプ、頻度、ポスチャ。
- 頻出敵対者(1-2 分)。
- 外部弁護士ベンチ(2-3 分) — 事務所、リードパートナー、案件タイプ、レートポスチャ、エンゲージメントレターステータス。*シードドキュメント:* 外部弁護士ガイドライン。(これが /oc-status にフィード — スキルが後でこれらの事務所への週次ステータスリクエストを起案。)
- 頻出フォーラム(30 秒)。
- 文書ストレージ(2-3 分) — 案件 docs が住む場所(ファイルシステム、Drive、SharePoint、Box、Gmail、CLM、DMS、eDiscovery)、デフォルト案件フォルダパターン、docs が OC とどう共有されるか。
- コンフリクトクリアランス(1-2 分) — このショップがコンフリクトをどう運営;誰がそれをするか;intake でハードブロックまたは並行。

### 柱 3 — ハウススタイル

> 構造化質問の前に: ハウススタイルガイド、テンプレート取締役会メモ、ホールド通知テンプレート、または模範要求書を読めますか? 内容を貼り付け、ファイルパスを共有、または 'no' と言って質問を歩きます。

そうでなければ:

- 取締役会 / 監査委員会メモ(2 分) — フォーマット、トーン、ケイデンス。*シードドキュメント:* 最近の取締役会メモ(編集 OK)。
- 引当メモ — フォーマットと承認者。*シードドキュメント:* サンプル引当メモ。
- 外部弁護士指示 — メールフォーマット、ケイデンス、予算ポスチャ。
- 特権慣行 — マーキング;デフォルト主観的判断ポスチャ(マークしてフラグ);レビューメカニック(インライン / キュー / 両方)。(これが /privilege-log-review にフィード — スキルが各 priv-log パスでマーキングルールとレビューメカニックを適用。)
- リーガルホールド — テンプレート、発行プロトコル、更新ケイデンス。*シードドキュメント:* ホールドテンプレート。(これが /legal-hold にフィード — スキルがハウステンプレートを使ってホールドを発行、更新、解除。)
- エスカレーション — チャネル規範、件名規約。
- 要求書プラクティス — *ここでは尋ねない。* 要求ポスチャ(トーン、期限、マーキング、署名者)はプラクティスではなく案件ごとに設定。`/litigation-legal-ja:demand-intake` と `/litigation-legal-ja:demand-draft` が必要なときに尋ねる — それらの判断は関係、金額、訴訟の可能性に依存し、プラクティスレベルデフォルトは特定のレターをミスキャリブレートする傾向。セットアップインタビューがここで*欲しい*もの: 保険テンダータイミング(送付前に誰にいつ通知するか)と案件作成のための重要性閾値(<$X、記録のみ;>$X、案件作成)。それらはプラクティスレベル。

**提供:** 「ハウススタイルガイドまたはテンプレートをアップロードしなかった場合、ハウススタイルルールをスタンドアロンスタイルメモとして書きますか?」

---

## ソロパス(role == `solo`)

*ユーザーのロールが `in-house` または `firm-associate` ならこのセクション全体をスキップ。ソロユーザーはこのパス**と**続く事務所アソシエイトパスを実行。*

> Solo practice is its own frame — caseload, client expectations, retainer or contingency economics, office management. The in-house world (ASC 450 reserves, board memos, outside-counsel oversight, settlement-authority ladders up to a GC) doesn't apply here, and I'm not going to pretend it does. The firm-world reserves questions don't apply either. What I need from you is the shape of your actual caseload and how you run your practice.
>
> A few seed documents help — a prior demand letter, a retainer agreement, a client-update email you'd be willing to share as an exemplar. Anything we can learn from saves a round trip later.

### セクション S1 — プラクティスシェイプとケースロード

- **ケースロードサイズ** — 一度に何件のアクティブ案件を持つか? 多すぎるのは?
- **案件ミックス** — 大まかなパーセンテージ: plaintiff vs defense、プラクティスエリア(例: PI、家族、雇用、小規模ビジネス紛争、地主/テナント)。正確である必要なし;1 文で十分。
- **法域** — 主に実務する州と裁判所。関連なら連邦を含む。
- **典型的なケース期間** — 週、月、年? 下流のスキルが努力と期限ホライゾンをスケールするのに有用。
- **キャパシティフラグ** — ケース受諾を停止するポイントはあるか? キャパシティを超えたとどう知る?

### セクション S2 — クライアント期待と経済

*これは社内パスが「リスクキャリブレーション / 引当方法論 / 和解権限ラダー」と呼ぶものを置き換える。ソロは引当を運営せず GC にエスカレートしない;同じ決定がクライアント向け経済として現れる。*

**料金構造(メインドライバ)。** 仕事のほとんどにフィットするものを選ぶ:

- **成功報酬**(plaintiff サイド PI、雇用、消費者のためのデフォルト仮定): 標準パーセンテージは? 訴訟前 vs 訴訟後? コスト前進ポスチャは — クライアント、事務所、ハイブリッド? どのエクスポージャーで成功報酬でケースを取るのを止めるか?
- **時間制 / 委任金**: 時間レート、標準委任金、信託口座メカニクス。
- **定額**: どの案件タイプ、料金範囲。
- **混合**: ミックスを説明。

**クライアント期待(2 分)。** 直接尋ねる:

- どれくらいの頻度でクライアントに案件を更新する(週次、月次、イベントベース)?
- 更新はどんな形を取る — 電話、メール、レター、クライアントポータル?
- クライアントとの和解会話のデフォルトポスチャは(和解への積極的プッシュ、クライアントが運転、ケース依存)?

**エクスポージャー / ケースバリュー読み(plaintiff サイド)。** ケースが取る価値があると判断する迅速なメンタルフレームワークは? 例: 「liability clear, damages > $50K, statute has a year or more, client credible」 — 詳細に判断なし;あなたのものをキャプチャ。

**エクスポージャー読み(defense サイドソロ — 少ないが可能)。** 受容可能なエクスポージャー vs クライアントへの報告のメンタルモデルは? ソロ defense は通常保険レイヤーのない個人または小規模ビジネスのため — 実際にどう考えるかをキャプチャ。

**助けを呼ぶとき。** ソロは GC や上のパートナーがいないが、ほとんどが誰かを持つ — 共同弁護士、メンター、ローカルリストサーブ、弁護士会委員会。セカンドオピニオンのために誰を呼ぶか、どの種類の案件で?

> 名前、ロール、または「nobody — I decide on my own」を与える。

**書面でのクライアント更新(1 分)。** *シードドキュメント機会:* 最近のクライアント更新メールまたはレター(編集済)。これは社内取締役会メモのソロ等価物 — ステークホルダーにステータスを通信する方法。ユーザーが共有したら、それを読みハウススタイルセクションのために構造とトーンを抽出。

### セクション S3 — 事務所管理とランドスケープ

*回答が先行コンテキストから明らかな質問はスキップ。*

- **出訴期限追跡** — ケースロードを跨いでどう SOL カットオフを追跡?(カレンダー、ケース管理ソフトウェア、ペーパードケット、記憶 — 何が現実か。)これは社内「重要性 / 引当トリガー」のソロ等価物、なぜなら SOL ミスはソロキャリアを終わらせる失敗モード。
- **ケース管理ソフトウェア** — Clio、MyCase、PracticePanther、Smokeball、Rocket Matter、ペーパーファイル、スプレッドシート、その他。
- **文書ストレージ** — Google Drive、Dropbox、OneDrive、ローカルファイルシステム、ケース管理ツールのストレージ。案件文書は実際にどこに住む?
- **頻出フォーラム** — 実際に出廷する裁判所。
- **頻出敵対当事者 / 弁護士** — 反対側で定期的に見る繰り返しプレーヤー。
- **共同弁護士 / 紹介弁護士のベンチ** — コンフォートゾーン外のケースのために誰を associate in するか? 誰があなたに紹介するか?
- **コンフリクトクリアランス** — コンフリクトをどう運営? ソロのバージョンは通常非公式(記憶 + クライアントリストチェック)で、それで OK — それが何かをキャプチャ。

### ソロハウススタイル

取締役会メモ / 引当メモ / 外部弁護士指示質問を完全にスキップ。ソロハウススタイルは:

- **クライアント更新** — フォーマット、トーン、ケイデンス。*シードドキュメント:* 最近の更新レターまたはメール。
- **委任金 / エンゲージメント契約** — テンプレート。*シードドキュメント:* 模範(編集 OK)。
- **特権慣行** — マーキング;レビューメカニック。
- **リーガルホールド** — ソロでも、訴訟が予期されるとき保全は重要。テンプレート、あれば。*シードドキュメント:* 発行されていればホールド通知。
- **要求書プラクティス** — *ここでは尋ねない。* 要求ポスチャ(トーン、期限、マーキング、署名者)はプラクティスではなく案件ごとに設定 — 「誰が署名」のソロ等価物は自明(あなた)で、トーン/マーキング/タイミングは特定の紛争に依存。`/litigation-legal-ja:demand-intake` が起案時に尋ねる。

**提供:** 「クライアント更新模範または委任金をアップロードしなかった場合、ハウススタイルルールを再利用可能なスタンドアロンメモとして書きますか?」

セクション S3 の後、下の**事務所アソシエイトパス**に継続。ソロ実務家は事務所アソシエイトと同様にブリーフを書き、時系列表を構築し、デポを準備する — ケースセオリーとシードブリーフ作業が適用。

---

## 事務所アソシエイトパス(role == `firm-associate` または `solo`)

> Before I touch a document, I need the theory. What's our story? What's theirs? What does the case turn on? Then I need to see how your firm writes — a brief you're proud of — so my drafts don't look like they came from somewhere else.

### Part A: 案件(2 分)

- 案件名、クライアント、ケース番号、裁判所
- 我々のサイド(plaintiff / defendant)
- パートナーとシニアアソシエイト(ヒエラルキーのないソロ / 小規模ならスキップ)
- ステージ(訴答、ディスカバリー、サマリージャッジメント、トライアル準備)
- 来る予定のキー日付

### Part B: セオリー — これがすべて(3-4 分)

> ケースのセオリーを教える。訴状ではない — 物語。陪審にこのケースで勝つ理由を 2 文で伝えるなら、それらは何?

- 我々のセオリーを 1 段落
- 彼らのセオリーを 1 段落(相手を知る)
- **Pivot fact** — ケースが turn する事実
- 我々のためのキーファクト
- 我々に反するキーファクト(心配しているもの)
- 最も重要な法的問題

### Part C: シードドキュメント(3-4 分)

> 2 つのこと:
>
> 1. **ケースセオリーメモ**、存在すれば。セオリーが誰かの頭にあって紙ではないなら、それで OK — 上でキャプチャしました。
>
> 2. **ハウススタイルの先行ブリーフ。** このケースからではない — 任意のケース。あなたが持つ最高のもの。引用スタイル、構造、トーン、議論をどう整理するかを学びます。(これが /brief-section-drafter にフィード — すべての将来のブリーフセクションが、一般的なテンプレートではなく、抽出した引用フォーマット、見出し構造、トーンで起案される。)

**ブリーフから:** 引用フォーマット(Bluebook、ALWD、ローカルルール)、セクション構造、見出し規約、トーン(攻撃的 / 抑制的)、長さ規範。

### Part D: 文書レビューセットアップ(1-2 分)

> 質問の前に: 特権ログフォーマット、時系列表フォーマット、またはレビュープロトコル doc を読めますか? 内容を貼り付け、ファイルパスを共有、または 'no' と言って 1 つずつ尋ねます。

そうでなければ:
- eDiscovery プラットフォーム(Everlaw、Relativity、DISCO、Aurora)
- レビュープロトコル — コーディングカテゴリー、誰が priv 判断
- 特権ログフォーマット
- キー custodian と日付範囲

**提供:** 「priv-log または時系列表フォーマットをアップロードしなかった場合、レビュープロトコルと priv-log フォーマットをレビューチームと共有できるスタンドアロンリファレンスとして書きますか?」

---

## 書き込み前 — 再読

プラグイン config をコミットする前に、すべてのキャプチャされた回答を順に再読。これは 3 つのカテゴリーのミスをキャッチ:

1. **回答間の矛盾** — 例: ユーザーがリスクアペタイトで「fight everything」、要求書デフォルトで「settle quickly」と言った。両方を表面化、どちらが支配するか尋ねる。
2. **drift した具体性** — セクション間で変わった名前、日付、閾値。最終値を確認。
3. **名指す価値のあるスキップされたギャップ** — `--redo` ではなく今完成したいかもしれないユーザーが残した空白セクション。

また: ロールが `firm-associate` なら、pivot fact とシードブリーフがキャプチャされたかをダブルチェック。これらは load-bearing。いずれかが欠落していれば、書く前に明示的に名指す。

## プラクティスプロファイルを書く

完成したプラクティスプロファイルをプラグイン config に書き、`${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` のテンプレートをセクション scaffold として使用。キャプチャされた各セクションを埋める;ユーザーがスキップしたセクションには `[PLACEHOLDER]` を残す。フッターに日付。

**ロールによるセクションゲーティング:**

- `in-house` → 完全な社内構造(カンパニープロファイル、ASC 450 / 引当 / 取締役会メモ行付きリスクキャリブレーション、外部弁護士ベンチ、取締役会/監査委員会メモ)。ソロ専用セクション(料金構造、委任金、成功報酬)は省略または N/A マーク。
- `firm-associate` → 事務所ワールド構造(ケースセオリー、pivot fact、パートナーレビュー、シードブリーフ)。引当 / 取締役会メモ / ASC 450 セクションを省略;ソロ料金 / 委任金セクションを省略。
- `solo` → ソロ構造(ケースロード、料金構造、クライアント期待、SOL 追跡、委任金または成功報酬、事務所管理)**プラス**事務所アソシエイトセクション(ケースセオリー、シードブリーフ)。社内引当 / ASC 450 / 取締役会メモ / GC への和解権限ラダーセクションを完全に省略 — それらはソロプラクティスの正しいフレームではなく、placeholder として含めることは構造ではなくノイズを追加。

テンプレートセクションが社内専用語彙(「ASC 450 reserves」、「board / audit committee memo」)を運ぶ場合、非社内ロールには省略するか、語彙を等価のソロまたは事務所アソシエイトコンセプトに翻訳。「取締役会メモ」のソロ等価物は「クライアント更新レター」。「引当方法論」のソロ等価物は「ケースバリュー読み」(plaintiff)または「エクスポージャー読み」(defense)。会計基準言語をソロプロファイルに持ち越さないこと。

**LIMITED DATA フラグ:** インタビュー全体で 10 未満のシードドキュメントが共有された場合、上部に `> LIMITED DATA` 注記を追加(書かれた日付の下): 「This practice profile was written from [N] seed documents and interview answers. Downstream skills will operate but outputs will be thinner until more exemplars are added. Re-run `/cold-start-interview --redo` after collecting more templates to sharpen calibration.」

## ギャップ表面化

インタビュー後、書き込み前にサマライズして**回答を待つ**:

> Here's what I captured. Gaps I noticed:
> - [スキップされたセクション、空白のままにされた placeholder、ユーザーが「come back later」と言った質問のリスト]
>
> Want to fill any of these now, or leave them as placeholders? You can also fill them later via `/litigation-legal-ja:cold-start-interview --redo` or by editing the plugin config directly. This one is worth thinking about before I write: [最も重要なギャップと理由を名指す].

ユーザーが答えるまで書き込みに進まないこと。

## 書き込み後

**このプラグインができることを表示。** 締めくくり前に、提供:

> **Want to see what I can help with?**

Yes なら、このテーラリングされたリスト(一般的なテンプレートではない — これらはこのプラグインが最高にできる具体的なもの)を表示:

> **Here's what I'm good at in litigation practice:**
>
> - **新規案件を intake** — 例: 「Uniform intake questions, writes matter.md + history.md, appends to the portfolio log.」 試す: `/litigation-legal-ja:matter-intake`
> - **受領した要求書をトリアージ** — 例: 「Options analysis, portfolio cross-check, handoff to matter intake if it graduates.」 試す: `/litigation-legal-ja:demand-received`
> - **要求書を起案** — 例: 「Privilege / FRE 408 gate, .docx output, post-send checklist, matter-creation offer.」 試す: `/litigation-legal-ja:demand-draft`
> - **デポジションアウトラインを構築** — 例: 「Docs + topics + impeachment + exhibits, tied to case theory.」 試す: `/litigation-legal-ja:deposition-prep`
> - **リーガルホールドを発行または更新** — 例: 「Draft the hold memo, update the log, schedule a refresh.」 試す: `/litigation-legal-ja:legal-hold`
> - **ポートフォリオロールアップ** — 例: 「Risk distribution, upcoming deadlines, stale matters across the active portfolio.」 試す: `/litigation-legal-ja:portfolio-status`
>
> **私のあなたの最初の提案:** `/portfolio-status` を実行 — それは一目でポートフォリオの所在を表示し、試すのにゼロ入力。または何が手にあるか教えて、選びます。

これがコールドスタート問題(スーパーバイザーが最初に何をすべきか分からない)と価値命題問題(プラグインができることを知らない)を 1 つの提供で解決。リストを具体的に。スーパーバイザーがインタビュー中に具体的な最初のタスクを名指したらこのステップをスキップ。


- `in-house` の場合: 「社内プラクティスプロファイルが書かれました。すべての案件 intake がそれから読みます。最も live な案件で `/litigation-legal-ja:matter-intake` を実行してアクションを見ますか?」
- `firm-associate` の場合: 「私がキャプチャしたセオリーです。pivot fact を読む — 正しく取得しましたか? 次の期限は? そこから始めましょう。」
- `solo` の場合: 「ソロプラクティスプロファイルが書かれました — ケースロードの形、料金経済、事務所運営方法 — プラス live 案件のケースセオリーとブリーフスタイル作業。あなたの設定で intake がどう見えるかを見るために、最も live な案件で `/litigation-legal-ja:matter-intake` を実行しますか?」

### 「後で何でも変更できる」注記で締めくくる

> "あなたのプラクティスプロファイルは `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` にあります — 直接読み・編集できるプレーンテキストファイル。回答したものはすべて変更可能:
>
> - クイック変更にはファイルを直接編集
> - 完全な再インタビューには `/litigation-legal-ja:cold-start-interview --redo`
> - 新規案件でプラクティスプロファイルを再利用するには `/litigation-legal-ja:cold-start-interview --new-matter`(事務所アソシエイト / ソロ)
> - 接続されているものを再チェックするには `/litigation-legal-ja:cold-start-interview --check-integrations`
>
> 最も調整するセクション: 社内には**重大度 × 確度閾値**と**外部弁護士ベンチ**;事務所アソシエイトには**ケースセオリー**(特に pivot fact)とシードブリーフから抽出された**ハウスブリーフスタイル**;ソロには**料金構造**(成功報酬パーセンテージまたは時間レート)と**サイドデフォルト**(plaintiff / defense) — そこでの間違ったデフォルトはすべての要求書と時系列表出力を歪める。出力が off と感じるとき、修正は通常ここ。"

### 最初の案件の前に

**リサーチツールを接続。** ツールなしでは、すべての引用を未検証としてフラグ — ツールありでは現行データベースに対して検証。Cowork で: 設定 → コネクター。Claude Code で: スキルが促したら authorize。

<!-- COLLATERAL LINKS: when onboarding collateral exists, add here:
     "Want a walkthrough? [Watch the 3-minute intro](URL) or [read the getting-started guide](URL)." -->

### プラクティスプロファイルは学ぶ

プラクティスプロファイルを書いた後、この注記で締めくくる:

> **プラクティスプロファイルは学びます。** プラグインを使うほど良くなります:
>
> - スキルの出力が off と感じるとき、それは通常調整する立場。出力がどれを伝える。
> - 「私のプレイブックを X を優先するように更新」または「私のエスカレーション閾値を Y に変更」と言えば、関連スキルが変更を書く。
> - 1 部分を再インタビューするには `/cold-start-interview --redo <section>`、または config ファイルを直接編集。
>
> 10 分のセットアップで動作するプロファイルを取得。1 ヶ月の使用で自分で書いたように読めるものを取得。

## このスキルがしないこと

- ユーザーのためにフレームワークを決定。デフォルトは出発点;ユーザーの判断が実際のコンテンツ。
- ギャップを ignore する。閾値を捏造するより `[PLACEHOLDER]` を正直に残す方が良い。
- ユーザーと戦う。「I don't have that yet」と言うなら、注記して移動。
- 尋ねずに個人 `~/CLAUDE.md` または他の ambient コンテキストを読む。
