---
name: cold-start-interview
description: コールドスタート・インタビュー — ウォッチリストを構築し、ポリシーライブラリを索引化し、重要性閾値を学習することで、モニターがノイズではなくシグナルを表面化するようにします。新規インストール時、再設定時(--redo)、または接続中のコネクターが実際に応答しているかを再確認する場合(--check-integrations)に使用。Use on fresh install, when reconfiguring (--redo), or when re-checking what connectors are actually responding (--check-integrations). 日本語トリガー: 初期設定、コールドスタート、セットアップ、規制プラグインを設定。
argument-hint: "[--redo | --check-integrations]"
---

# /cold-start-interview

1. `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` を確認する。populated な CLAUDE.md(`[PLACEHOLDER]` マーカーなし)が `~/.claude/plugins/cache/claude-for-legal-ja/regulatory-legal-ja/*/CLAUDE.md` に存在し、config パスに存在しない場合は、config パスへコピーし、何が移行されたかをユーザーに伝える。`--check-integrations` の場合は、インタビューをスキップ — Part 0 の `What's connected?` チェックだけを再実行し、`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` の `## Available integrations` テーブルを書き直す。
2. 下記のインタビューワークフローを使用。インタビュー(まず Part 0 — role + integrations — 次にウォッチリスト): どの規制機関か、ポリシーがどこにあるか、何が material か。
3. ポリシーフォルダを接続。ポリシーを索引化。
4. `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` を書く(必要に応じて親ディレクトリを作成)。ウォッチリスト + 重要性閾値を含む。

統合をプローブするときは、MCP ツール呼び出しが実際に成功した場合にのみ ✓ を報告する。configured-but-untested なコネクターは ⚪ でマークし、確認方法の 1 行 how-to を付ける。`.mcp.json` の宣言だけに基づいて ✓ を報告しない — それは「接続されていないのに接続されている」とユーザーを誤認させる。

---

## 目的

すべての規制機関は絶えず公開しています。そのほとんどはあなたに関係ありません。このインタビューは、どの規制機関を監視すべきか、そして重要なことに、ここで「material」とは何を意味するかを学習し、モニターがノイズではなくシグナルを表面化するようにします。

## コールドスタートチェック

`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` を読む:
- **存在しない** → インタビューを開始。
- **`<!-- SETUP PAUSED AT: -->` を含む** → ユーザーに挨拶し、そのセクションから再開するか提案。
- **`[PLACEHOLDER]` マーカーを含むが pause コメントなし** → テンプレートが完成しなかった;最初から始めるか、プレースホルダーが始まる場所から再開するか提案。
- **Populated(プレースホルダーなし、pause コメントなし)** → すでに設定済み;`--redo` でない限りスキップ。

テンプレート構造は `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` にある — セクションの足場として使う。完成したプラクティスプロファイルを config パスに書き、必要に応じて親ディレクトリを作成する。

古いキャッシュパス `~/.claude/plugins/cache/claude-for-legal-ja/regulatory-legal-ja/*/CLAUDE.md` に CLAUDE.md が存在し、config パスに存在しない場合は前方にコピーする。

## 共有 company profile のチェック

`~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` を探す。

- **存在する場合:** 読む。1 行の確認を表示: "You're [name], [practice setting], at [company], [industry], operating in [jurisdictions]. Right? (Or say 'update' to change the shared profile.)" 確認されたら、会社の質問をスキップ — プラグイン固有の質問に直行。
- **存在しない場合:** あなたがこのユーザーが設定する最初のプラグインになる。オリエンテーションと分岐の後、会社の質問を聞き、共有プロファイル(プラグインルートの `references/company-profile-template.md` のテンプレートに従う)に書き、その後プラグイン固有の質問を続ける。ユーザーに伝える: "I've saved your company profile — the other legal plugins will read it and skip these questions."

共有プロファイルに属する(存在する場合は再質問しない)会社の質問: practice setting、会社名、業界、what-you-sell、規模、法域、規制機関、リスク許容度、エスカレーション担当者名。プラグイン固有の質問(プレイブックのポジション、レビューフレームワーク、ハウススタイル、監督モデル等)はプラグインごとに残す。

## インストールスコープチェック

オリエンテーションの前に、作業ディレクトリがプロジェクト内(ユーザーのホームディレクトリではない)であることに気づいたら、フラグを立てる。一度だけ言う:

> **Heads up — このプラグインは project-scoped に見えるので、[現在のディレクトリ] のファイルしか読めません。ほかの場所(Downloads、Documents、Dropbox)のドキュメントを読ませたいなら、代わりに user-scoped でインストールしてください — QUICKSTART.md を参照。project スコープで継続することもできますが、ファイルをこのフォルダに移動する必要があります。**

ユーザーが進む前に確認するよう求める: project スコープで継続するか、user-scoped で再インストールするために一時停止するか。作業ディレクトリ *が* ユーザーのホームディレクトリの場合、このチェックは黙ってスキップ。

## インタビュー開始前

最初にこの前文を表示(3-4 行程度、それ以上は不要):

> **`regulatory-legal-ja` は、規制動向を追跡し、ポリシーギャップを評価し、コンプライアンス義務を管理する人のためのものです。** あなたの領域ではない? `/legal-builder-hub:related-skills-surfacer`。
>
> **2 分**で role、practice setting、主要な規制レジームが設定できます。**15 分**で完全な watchlist、重要性閾値、フィード実行頻度、ポリシーライブラリ索引、コメント期間ソースまで設定できます。
>
> Quick または full?(いつでも `/regulatory-legal-ja:cold-start-interview --full` でアップグレード可能。)

ユーザーのホームディレクトリの `~/CLAUDE.md`、`~/user.md`、または他の個人メモリは、インタビューを事前に埋めるために読まない。唯一の入力はユーザーが入力した回答と、ユーザーが指したか貼ったドキュメントだけ。

## ユーザーが quick または full を選んだ後

選択が済んだら、オリエンテーション。自分の言葉で次をカバー:

- **このプラグインが維持するもの:** プラクティスプロファイル(watchlist、重要性閾値、フィード実行頻度)、ギャップトラッカー、ポリシー diff アーカイブ、コメント期間カレンダー。
- **このセットアップが行うこと:** どの規制機関を実際に監視するか、あなたにとって「material」とは何か、ポリシーがどこにあるかを学習し、プラグインが毎回読むプレーンテキストファイルに書き込む。回答したすべては後で変更可能。完了すると、プラグインのコマンドはユーザーの働き方に合わせて機能し、汎用テンプレートのようには動かなくなる。
- **データソース:** セットアップはユーザーの回答のみから新しいプロフェッショナルプロファイルを構築する。ユーザーの個人 Claude 履歴、他の会話、またはホームディレクトリの CLAUDE.md は読まない。この会話の前に関連することが出てきたら(例えばユーザーが規制機関やセクターに言及した)、使う前に聞く。ユーザーが入力するか承認しない限り、設定に取り込まれない。

**これが重要な理由。** すべてのダイジェスト、diff、ギャップレポートは、このインタビューが書く設定から読む。汎用設定は汎用出力を生む — デフォルトの watchlist、デフォルトの重要性閾値、そしてあらゆる agency speech(規制機関のスピーチ)を執行措置のように扱うダイジェスト。ユーザーが実際に監視する規制機関と、ここで「material」が何を意味するかをプラグインに伝えることが、「規制 AI ツール」と「ノイズではなくシグナルを送るツール」の違いを生む。回答が具体的であればあるほど、ダイジェストは静かで有用になる。

## インタビューのペース

- **答えはどこかに存在すると仮定する。** 質問が、おそらくどこかに書かれている情報を尋ねるとき(会社概要、プレイブック、エスカレーションマトリックス、スタイルガイド、ハンドブック、法域リスト、案件ポートフォリオ)、ユーザーに記憶からタイプさせる前に、リンクや貼り付けを促す。「Paste a link or a doc, or give me the short version」が、1 文を超えるものに対するデフォルトの聞き方。すでに書かれたものを再入力させる面接官は、面接官の最初の仕事に失敗している。
- **バッチサイズ — サブパートを数える。** 「1 回のターンで 2-3 個を超える質問をしない」とは、サブパートを含めて 2-3 個の *答えられるプロンプト* を意味する。5 個のサブパートを持つ 1 つの質問は 5 つの質問。判定基準: ユーザーはスクロールせずに答えられるか?質問が 1 画面に収まらないなら、多すぎる。可能なら構造化された tap-through 質問を優先 — スクロールも入力も要らない。

**実際の答えを待つ。** 一部の質問は quick tap-through で答えられる。他のものは、ユーザーがリストをタイプ(どの規制機関か)、calibration judgment を記述、またはポリシーフォルダを指し示す必要がある。質問が quick tap を超えるものを必要とするとき:

- **質問をして待つ。** 平易に言う: "This one needs a typed answer — I'll wait." 答えるまで次の質問をキューに入れない。
- **アップロードまたはパスポインタの場合(ポリシーフォルダ、既存 watchlist、フィード URL):** "Paste the contents, share a file path, or say 'skip for now.' If you skip, I'll flag the gap in your configuration so you can fill it later." それから実際に待つ。
- **プラクティスプロファイルを書く前:** インタビューをレビュー。スキップされたかプレースホルダーで答えられたすべての質問をリスト。"Before I write your configuration, here's what's still open: [list]. Want to fill any of these now, or leave them as placeholders?" 書く前に答えを待つ。
- **決して** silent gap でプラクティスプロファイルを書かない。すべてのプレースホルダーは、スクロールして通り過ぎた未答質問ではなく、スキップする意図的なユーザー選択であるべき。
- **Pause and resume(中断と再開)。** 最初にユーザーに伝える: "If you need to stop, say 'pause' (or 'stop', or 'let me come back to this') and I'll save your progress. Run `/regulatory-legal-ja:cold-start-interview` again later and I'll pick up where you left off." ユーザーが pause したら、partial configuration を `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` に書き、上部に `<!-- SETUP PAUSED AT: [section name] — run /regulatory-legal-ja:cold-start-interview to resume -->` コメントを付け、未回答フィールドに `[PENDING]` マーカー(`[PLACEHOLDER]` とは異なる)を付ける。セットアップが再実行され、paused config を見つけたら、ユーザーに挨拶: "Welcome back. You paused at [section]. Your earlier answers are saved. Pick up where we left off, or start over?" 既に答えた質問を再質問しない。

**セットアップ中に出てきた user-stated legal facts を検証する。** ユーザーがインタビュー質問に特定のルール引用、statute 番号、判例名、deadline、閾値、法域、または登録番号で答えたら — そして sanity-check できるものなら — 設定に書き込む前にチェックする。ユーザーが言ったことが理解と矛盾するか、貼ったものと矛盾する場合は、表面化する: "You said the threshold is X; my understanding is Y — can you confirm which goes in the profile? `[premise flagged — verify]`" CLAUDE.md に書かれた間違った事実は将来のすべての出力に伝播する;ここでそれを捕まえることはプロダクトで最も leverage が高い瞬間の一つです。

## インタビュー

### 開始

> I'm going to watch your regulators and tell you when something moves. But "something moves" happens daily. I need to know what actually matters to you so I'm not crying wolf.

### Quick start or full setup — 分岐

ユーザーは前文で quick または full を選んだ。分岐:

**Quick start path:** Part 0(role、practice setting、integrations)と watchlist の範囲だけを聞く。他のすべてに `[DEFAULT]` マーカーを付けて config を書く。次で締める: "Done. You can start using the commands now. I've used sensible defaults for materiality threshold, digest cadence, and policy library structure. When a skill's output feels off, that's usually a default you should tune — it'll tell you which. Run `/regulatory-legal-ja:cold-start-interview --full` anytime to do the whole interview, or `/regulatory-legal-ja:cold-start-interview --redo <section>` to re-do one part."

**Full setup path:** 下記の既存のインタビューフロー。

### Part 0: 利用者と接続状況

規制の詳細に入る前にクイック 2 問。これらはプラグインができることではなく、どう動くかを形作る。

#### 利用者は?

> このプラグインを日常的に使うのは誰?(これは各スキルの work-product header と出力フレーミングに反映される — 弁護士には「ATTORNEY WORK PRODUCT」、非弁護士には research framing と規制機関向けステップ前の attorney review チェックポイント。)
>
> 1. **Lawyer or legal professional** — 弁護士、パラリーガル、弁護士監督下で働く legal ops。
> 2. **Non-lawyer with attorney access** — 創業者、ビジネスリード、契約マネージャー、HR、procurement; 相談できる in-house または外部弁護士がいる。
> 3. **Non-lawyer without regular attorney access** — 自分で対応している。

答えが 2 または 3 の場合、一度だけこれを言う(各出力で繰り返さない):

> あなたはここでのすべての機能を使えます — リサーチ、レビュー、ドラフト、トラッキング。私の動きは 2 つ変わります:
>
> 1. **私は出力を「verdict(裁定)」ではなく「弁護士レビュー用のリサーチ」としてフレーミングします。** 「GREEN — sign it」ではなく、「here's what I found and here are the questions to ask before you sign」を得る。確信できないグリーンライトより有用です。
> 2. **法的影響を持つステップの前で一時停止します** — 契約への署名、誰かの解雇、demand の送信、提出、ローンチクリア、規制機関への回答。弁護士とレビュー済みかを尋ね、相談を迅速にするための短いブリーフを用意します。
>
> これは disclaimer ではない。プラグインが、自分が得意なこと(リサーチ、整理、構造化)と、ツールが与えられない、あなたの特定状況に関するライセンスされた法的判断との違いを知っているということです。適切な瞬間に弁護士に数時間を払うのは、ミスより通常安いです。

答えが 3 の場合、追加で:

> もし弁護士を見つける必要があるなら: あなたのプロフェッショナル規制機関の referral service が最速の出発点(米国は state bar、英国(イングランド&ウェールズ)は SRA / Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアは Law Society、または法域の同等機関)。多くは無料または低コストの初回相談を提供。中小企業向け: ローカルロースクールクリニックと(米国の場合)SCORE メンターが方向性を示してくれる。個人向け: legal aid 組織が多くのプラクティスエリアをカバーする。

#### 何が接続されている?

> このプラグインは以下と連携できます: 規制フィード(Thomson Reuters Regulatory Intelligence)、ドキュメントストレージ(Google Drive、SharePoint、Box)、Slack。どのコネクターが設定済みかチェックさせてください — それを必要とする機能は動作し、そうでない機能は黙って失敗するのではなく manual に gracefully フォールバックします。

**設定済みではなく、実際に接続されているものをチェックする。** `.mcp.json` にリストされているコネクターは *available* です。実際に応答しているコネクターは *connected* です。これらは違い、混同すると信頼が失われる。このプラグインが使う各コネクターについて:

- 接続をテストできる場合(list や search などの単純な MCP ツールを呼ぶ)、成功応答にのみ ✓ を報告。
- テストできない場合(ここからプローブできない)、⚪ "configured but not verified — open your MCP settings to confirm" を確認方法 1 行付きで報告。
- 設定だけに基づいて ✓ を報告しない。

Federal Register API は無料の公開エンドポイントで常に利用可能 — MCP コネクターは不要。

接続されていないと表示されたコネクターについて、接続方法をユーザーに伝える。例の言い回し: "TR Regulatory Intelligence isn't connected. In Claude Cowork: Settings → Connectors → Add → Thomson Reuters → sign in. In Claude Code: add the TR MCP to your config or via `/mcp`. This plugin works without it — Federal Register + manual paste covers US federal coverage — but connecting it adds enrichment and alert import."

それから次の形で findings を報告:

> - ✓ [Integration] — connected (tested)
> - ⚪ [Integration] — configured but not verified. Open your MCP settings to confirm.
> - ✗ [Integration] — not found. [Feature] will fall back to [manual alternative]. [How to connect.]

これらすべてが必要なわけではない。中核機能は無料フィード(Federal Register API)とファイルアクセスだけで動く。Paid フィードは enrichment を加える;manual paste-in は常に動作する。後で何かを設定したら `/regulatory-legal-ja:cold-start-interview --check-integrations` を再実行。

#### config に書く

`## Who's using this`、`## Available integrations`、`## Outputs` セクションをテンプレートに従い、config の最初のセクション直後に書く。`## Outputs` セクションは既に存在 — マージして work-product header を role に条件付ける。

#### Practice setting

> ウォッチリストの前にもう 1 つ:
>
> どの設定?(これはギャップ対応プロセスとエスカレーションエントリを形作る — in-house は GC ルーティング、solo は「escalate」を「外部弁護士相談」にマップ、clinic は監督弁護士にルーティング。)
>
> - **Solo / 小規模事務所(hierarchy なし)** — 承認チェーンの質問をスキップし、代わりに同僚や外部弁護士をいつ巻き込むかを尋ねる。
> - **中規模 / 大規模事務所** — 承認チェーン、請求閾値、あなたの上で誰がサインオフするかを尋ねる。
> - **In-house** — エスカレーションマトリックス、GC/CLO は誰か、何がビジネス側に上がるかを尋ねる。
> - **政府 / legal aid / clinic** — 監督構造とプラクティスに関する制限を尋ねる。
> - **私のプラクティスはどれにも当てはまらない** — そう言ってください。適応します。

**枠に当てはまらないプラクティス。** ユーザーのプラクティスが上記の選択肢に一致しない場合(国際仲裁、公国際法、amicus 専門、学術コンサルティング、pro bono panel、tribal court、軍事司法、海事、または標準カテゴリーが想定しないもの)、提示する: "It sounds like your practice doesn't fit my usual categories. Tell me about it in your own words — what you do, who for, what jurisdictions and forums, what the work looks like — and I'll build your profile from that instead of forcing you into boxes that don't fit. I'll skip or adapt the questions that don't apply." それから自由記述からプロファイルを構築し、テンプレートフィールドが埋まったか、適応されたか、または適用されないため空のままかをフラグ付け。強引なフィットから構築されたプロファイルは、実際に真実であるものから構築された sparse プロファイルより悪い。

これを使って、プラクティスプロファイルのギャップ対応プロセスとエスカレーションエントリを形作る:

- **Solo / 小規模事務所(hierarchy なし):** エスカレーションチェーン質問をスキップ。再構成: 「material gap response を誰が承認するか」ではなく、「セカンドオピニオンで外部弁護士や同僚をいつ引き入れるか」を尋ねる。プラクティスプロファイルのエスカレーションフィールドは *consult*(相談)であって *route for approval*(承認ルーティング)ではない、にマップ。
- **中規模 / 大規模事務所:** 承認チェーン、請求閾値、誰がサインオフするかを尋ねる。
- **In-house:** エスカレーションマトリックス、GC/CLO は誰か、何がビジネス側に上がるかを尋ねる。
- **政府 / legal aid / clinic:** その設定の監督チェーン(監督弁護士、ディレクター、oversight committee)を代用。構造を保ち、ロールを再ラベル。

それからエスカレーション質問を平易な言葉で:

> "When a review finds something that needs someone more senior to sign off — a policy gap that needs a company decision, a comment letter that takes a position on behalf of the company, a material regulatory change that rewrites practice, or a decision that's above your authority — who does that go to? Give me a name or a role (the GC, the CCO, your boss), or say 'I decide myself.' This is how the plugin knows when to say 'you can handle this' versus 'loop in [X].'"

practice setting を `## Who's using this` の下のプラクティスプロファイルに記録。

### Part 1: ウォッチリスト (2-3 分)

*(これは `/regulatory-legal-ja:reg-feed-watcher` と `reg-change-monitor` エージェントに反映される — フィードはこのリストの規制機関からのみ pull する。リストにないものは、`/regulatory-legal-ja:policy-diff` で貼り付けない限りプラグインに見えない。)*

**[あなたの会社]は何をしているか?** これは最も重要なコンテキストです — SaaS ベンダーのプレイブック、ハードウェア販売店のプレイブック、サービス企業のプレイブックは完全に異なる。タイプする必要はない: 会社のウェブサイト、"about" ページ、Wikipedia 記事、または最新の 10-K のリンクを貼ってください。必要なものを抽出します。または 1 文版を: 何を売っているか、誰に、どうやって(direct sales / channel / marketplace / subscription)。これは、どの規制機関がウォッチリストに plausible かも教えてくれる。

> 聞く前に: 既存のウォッチリスト、規制トラッキングスプレッドシート、または事前のギャップ分析メモを読めるものはありますか?内容を貼るか、ファイルパスを共有するか、'no' と言ってください。質問を一つずつ進めます。共有してくれたら、再度リストさせるのではなく、規制機関と重要性基準を抽出します。

なければ:

- どの規制機関?名前を挙げてください。(FTC(米国連邦取引委員会)、SEC(米国証券取引委員会)、CFPB(米国消費者金融保護局)、州 AG(司法長官)、CPPA(カリフォルニア州プライバシー保護機関)、EU DPA(データ保護当局)、セクター固有?)
  *Coverage note: このプラグインは US 連邦機関(Federal Register API)、SEC、FTC、CFPB の構造化フィードサポートを持つ。州規制機関と EU DPA はユーザー提供 RSS URL または manual entry でサポートされる — それらの自動フィードはない。EU DPA テーブルの外の non-US 規制機関は manual entry またはユーザー提供フィードを必要とする。*
- 各規制機関の理由は?("We're a fintech, CFPB is obvious" vs. "FTC because of the consent decree")
- 監視していないが、すべきかもしれないものは?

**ユーザーがウォッチリストや事前ギャップ分析をアップロードしなかった場合:** このセクションの最後で提案: "Want me to write this up as a standalone watchlist memo you can share and maintain? Same content I just captured — your regulators, why you watch each, and the feeds behind them — in a format you can circulate or hand to a new hire."

### Part 2: Materiality(キーとなる質問)(3-4 分)

*(これは `/regulatory-legal-ja:reg-feed-watcher` と `reg-change-monitor` エージェントに反映される — 重要性閾値は、新しい動向が即座に表示されるか、週次ダイジェストに入るか、まったく入らないかを決めるフィルター。ここで calibration が間違うと = 読まなくなる noisy ダイジェスト。)*

例を walk through。それぞれについて、即座に知りたいか、週次ダイジェストで、それともまったく要らないか?

- watchlist の規制機関からの final rule(最終規則)
- proposed rule(規則案、NPRM) — comment period が開いている
- 自社セクター内の企業に対する執行措置
- 自社セクターでない企業に対する執行措置だが、自社がやっていることに関する
- 優先事項を示唆する commissioner(委員)のスピーチ
- 規制機関からの blog post
- wrongdoing の認定なしの settlement
- 新しいガイダンス(sub-regulatory(規則未満)、拘束力なし)

これが重要性閾値を構築する。会社によって calibration は大きく異なる — consent decree(同意命令)下の企業はスピーチを気にする;規制機関が聞いたこともない企業は無視できる。

**ユーザーが重要性基準をアップロードしなかった場合:** このセクションの最後で提案: "Want me to write this materiality rubric up as a standalone doc you can share and maintain? Same content I just captured — immediate vs. digest vs. FYI, with your examples — so the team reads the same calibration."

### Part 3: ポリシーライブラリ (2-3 分)

*(これは `/regulatory-legal-ja:policy-diff` と `/regulatory-legal-ja:gaps` に反映される — 入ってくる規制変更はこのライブラリと突合され、どのポリシーに触れ、誰が所有するかを発見する。)*

> 聞く前に: 既存のポリシーライブラリインデックス — スプレッドシート、目次、wiki ページ — があり、各ポリシーをオーナーにマップしていますか?内容を貼るか、ファイルパスを共有するか、'no' と言ってください。質問を一つずつ進めます。共有してくれたら、記憶から再構築させるのではなく、インポートします。

なければ:

> ポリシーフォルダを指してください。そこにあるものをインデックス化し、規制が変わったときにどのポリシーに触れるかを伝えられるようにします。

- ポリシーはどこにあるか?(Drive、SharePoint、Confluence、Notion)
- 命名規則や index はあるか、それともファイルだけか?
- どのポリシーは誰が所有するか?(ギャップを正しい人にルーティングするため)

**ユーザーがポリシーインデックスをアップロードしなかった場合:** このセクションの最後で提案: "Want me to write this up as a standalone policy-ownership index you can share and maintain? Same content I just captured, formatted so a new GC or compliance hire gets the landscape on day one."

### Part 4: フィードソース (2-3 分)

無料フィードがベースライン — サブスクリプションに関わらずすべてのチームが監視を得る。
有料フィードは持つチームにエンリッチメントを加える。

**Step 1: 名指しされた watchlist の無料フィードをマップ**

Federal Register API(federalregister.gov/api)は米国連邦機関の安定した primary source — 機関、ドキュメントタイプ、施行日、コメント締切で構造化データを返す。watchlist の任意の連邦規制機関のデフォルトとして使う。

他のすべての名指しされた規制機関(state AG、CPPA(カリフォルニア)、EU DPA、セクター固有規制機関、non-US 規制機関)については、優先するフィード URL をユーザーに尋ねるか、現在のフィードを探すために agency website に案内する。フィード URL は変わる;キャッシュされたリストに頼らない。

名指しされた規制機関で既知の無料フィードがない場合: フラグを立て、ユーザーがその規制機関を現在どう追跡しているかを尋ね、manual-entry フォールバックを記録(下記参照)。

**Step 2: Paid サブスクリプションを尋ねる(additive、required ではない)**

- TR Regulatory Intelligence サブスクリプション?どのアラートが設定されているか?
- CourtListener?どのトラッカーか?

yes なら: 無料フィードの上に enrichment layer として設定。no なら: 無料フィードで進めるのに十分。

**Step 3: Manual entry フォールバック**

> Law360、ニュースレター、外部弁護士から何かを見て、システムを通したいとき — ただ貼り付けてください。ポリシーと突合し、ギャップを追跡します。そのためにフィードサブスクリプションは不要です。

manual entry が有効であることを config に記録。

**Step 4: Comment period トラッキング** *(これは `/regulatory-legal-ja:comments` に反映される — comment-period カレンダーは deadline をログし、コメントウィンドウが開いたときに意思決定を表面化する。)*

> watchlist から proposed rule(NPRM)を見たら、自動的にコメント締切をログします。コメントを提出するかどうか判断できるように、これらをフラグしますか?

yes なら: comment-tracker を有効化。コメント決定のデフォルトオーナーを config に記録。

## プラクティスプロファイルを書く

テンプレートに従う。鍵: 重要性閾値テーブル。

```markdown
## 重要性閾値

**常に Material(即時アラート):**
- [特定の規制機関] からの final rule
- 自社セクターでの執行措置
- [会社名] に言及するもの

**Review-worthy(週次ダイジェスト):**
- watched 規制機関からの proposed rules
- セクター外だが自社の practice に関連する執行措置
- 新しいガイダンス文書

**FYI(月次ラウンドアップまたは無し):**
- スピーチ、ブログ投稿、学術コメント
- 新規 theory のない settlements
```

## フィード設定ブロック(config に追加)

```markdown
## Feed configuration

**無料フィード(常時アクティブ):**
| 規制機関 | ソース | URL/方法 |
|---|---|---|
| [name] | Federal Register API / RSS / manual | [endpoint or "manual entry"] |

**有料フィード(設定されている場合):**
| サービス | サブスクリプション | アラート |
|---|---|---|
| TR Regulatory Intelligence | [yes/no] | [alert names] |
| CourtListener | [yes/no] | [tracker names] |

**Manual entry:** Enabled — 任意の規制動向を貼ると diff + ギャップトラッキングをトリガー。

**Comment tracking:** [Enabled / Disabled]
**Default comment decision owner:** [name]
**Check cadence:** [daily / weekly]
```

## 書いた後

**このプラグインで何ができるかを見せる。** 締める前に、提案する:

> **私が手伝えることを見たい?**

yes なら、このテーラード・リスト(汎用テンプレートではなく、このプラグインが最も得意な具体的な事項)を表示:

> **規制プラクティスで私が得意なこと:**
>
> - **規制フィードで新着をチェック** — 例: "Filtered digest of rulemaking, guidance, and enforcement against your watchlist." 試す: `/regulatory-legal-ja:reg-feed-watcher`
> - **規制変更をポリシーライブラリに突合** — 例: "See exactly which internal policies a new rule impacts and what needs updating." 試す: `/regulatory-legal-ja:policy-diff`
> - **未解決ギャップトラッカー** — 例: "What's flagged and not yet closed across your portfolio, with owner and deadline." 試す: `/regulatory-legal-ja:gaps`
> - **NPRM コメント期間を追跡** — 例: "What's open, comment deadlines, and a decision log on whether to file." 試す: `/regulatory-legal-ja:comments`
>
> **最初の 1 つの私の提案:** `/regulatory-legal-ja:reg-feed-watcher` を実行 — フィードが重要性閾値に calibrate されているか即座に分かる。または今あなたが抱えているものを伝えてくれれば、私が選びます。

これはコールドスタート問題(スーパーバイザーは何から始めるべきか分からない)と value-prop 問題(プラグインに何ができるか分からない)を 1 つの提案で解決する。リストは具体的に。インタビュー中にスーパーバイザーが具体的な最初のタスクを既に挙げていたら、このステップをスキップ。


- "Here's the watchlist and the threshold. The threshold is the part to tune — too tight and you miss things, too loose and you stop reading the digests."
- ポリシーライブラリを今すぐ索引化することを提案。
- 最初のフィードチェックを提案: "Want to see what's happened in the last 30 days as a test?"
- **最初のダイジェストやギャップチェックの前に、調査ツールを接続する。** 言う: "Before your first digest or gap check: connect a research tool. Without one, I'll flag every citation as unverified — with one, I verify them against a current database. In Cowork: Settings → Connectors. In Claude Code: authorize when a skill prompts you."

<!-- COLLATERAL LINKS: onboarding 用の素材があれば、ここに追加:
     "Want a walkthrough first? [Watch the 3-minute intro](URL) or [read the getting-started guide](URL)." -->

- changeability note で締める:

  > "Done. Your configuration is at `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` — a plain-text file you can read and edit directly. Anything you answered can be changed:
  >
  > - Edit the file directly for a quick change
  > - Run `/regulatory-legal-ja:cold-start-interview --redo` for a full re-interview
  > - Run `/regulatory-legal-ja:cold-start-interview --check-integrations` to re-check what's connected
  >
  > The settings people tune most often: the watchlist (which regulators you actually care about), the materiality threshold (what's immediate vs. digest vs. FYI), and the check cadence. Your configuration will improve as you use the plugin — when a digest feels off (too noisy, too quiet), the fix is usually here."

## プラクティスプロファイルは学習する

プラクティスプロファイルを書いた後、この注記で締める:

> **Your practice profile learns.** プラグインを使うほど改善されます:
>
> - スキルの出力が違和感を感じたら、それは通常チューニングすべきポジション。出力がどれかを伝えます。
> - `reg-change-monitor` エージェントは規制フィードを監視;変更がポリシーライブラリの何かと一致すると、gap-check 用にフラグ。
> - 「私の playbook を X に更新」または「エスカレーション閾値を Y に変更」と言えば、該当スキルが変更を書き込みます。
> - 一部だけ再インタビューするには `/regulatory-legal-ja:cold-start-interview --redo <section>`、または config ファイルを直接編集。
>
> 10 分のセットアップで動作するプロファイル。1 ヶ月の使用で、自分で書いたかのようなプロファイル。
