---
name: cold-start-interview
description: >
  教授の 1 回限りクリニックセットアップ — プラクティスエリア、法域、監督スタイル
  (formal review queue / configurable flags / lighter-touch)、ハンドブック/規則の
  アップロード。CLAUDE.md を書き出し、その後すべてのスキルおよび /ramp を
  実行する各学生が同じクリニックコンテキストから読みます。新規インストール時、
  CLAUDE.md にプレースホルダーがある場合、--redo でセットアップをやり直す場合、
  または --check-integrations で統合を再確認する場合に使用。
  (Use on fresh
  install, when CLAUDE.md has placeholders, when re-doing setup with --redo,
  or when re-checking integrations with --check-integrations.
   日本語トリガー: コールドスタート、初期設定、クリニックセットアップ、設定変更)
argument-hint: "[--redo] [--check-integrations]"
---

# /cold-start-interview

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を確認。populated で `--redo` なしなら、上書き前に確認。
2. 下記の教授インタビューを実行、Part 0(指導弁護士役割確認 → 倫理前提条件 → 統合可用性)から開始。ユーザーが指導弁護士でなければ停止しリダイレクト。
3. シード文書: クリニックハンドブック、提出ガイド、ローカル裁判所規則、インテイクフォーム、スクラブ済みサンプルファイル 1 件。
4. 主要判断: 監督スタイル(formal queue / flags / lighter-touch)。
5. マイグレーション: populated な CLAUDE.md(`[PLACEHOLDER]` マーカーなし)が `~/.claude/plugins/cache/claude-for-legal-ja/legal-clinic-ja/*/CLAUDE.md` に存在するが config パスにない場合、config パスにコピーし、何がマイグレートされたかをユーザーに示す。
6. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を書き出す(`## Who's using this` と `## Available integrations` を含む)。確認用に監督選択とプラクティスエリアテンプレートを表示。
7. `/legal-clinic-ja:ramp` プレビューを提案。

```
/legal-clinic-ja:cold-start-interview
```

**`--check-integrations`:** Part 0 の統合可用性チェック(Clio、文書ストレージ)のみを再実行。役割、倫理前提条件、監督スタイル、プラクティスエリアテンプレートに触れずに `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の `## Available integrations` を更新。MCP コネクターの追加/削除後に使用。

プロービング時: MCP ツール呼び出しが実際に成功した場合のみ ✓ を報告。設定済みだがテストされていないコネクターは ⚪ でマークし、確認方法を 1 行で。`.mcp.json` の宣言だけに基づいて ✓ を報告してはいけない — それはユーザーに、実際には接続されていないものが接続されているという誤解を与える。

---

# Cold-Start Interview: ロースクール・リーガルクリニック

## 目的

クリニックは構造的に容量制約を受けています。指導教授は 5 ~ 10 名の学生を管理し、各学生は授業との両立のなかで複数案件を抱え、学生は学期ごとに入れ替わります。待機リストは伸び、人々は待つことを諦めます。

このプラグインの仕事は、ロイヤリングの周辺(インテイク書き起こし、初稿、リサーチ起点、ステータス更新)の時間コストを削減することで、同じ学生と教授がより多くのクライアントを支援できるようにし、学生は臨床教育を価値あるものとする分析と戦略により多くの時間を割けるようにすることです。

このインタビューはクリニックコンテキストを一度設定するので、`/ramp` でオンボーディングする各学生およびその後実行される各スキルが、*この* クリニックがどう運営されているかについて同じ理解で動作します。

**対象: 指導教授。** 学生はこれを実行しない — 彼らは `/ramp` を実行する。

## コールドスタートチェック

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読む:
- **存在しない** → インタビューを開始。
- **`<!-- SETUP PAUSED AT: -->` を含む** → ユーザーに挨拶し、そのセクションから再開を提案。
- **`[PLACEHOLDER]` マーカーを含むが pause comment なし** → テンプレートが完了しなかった; ゼロから始めるか、プレースホルダーが始まる場所から再開するかを提案。
- **Populated(プレースホルダーなし、pause comment なし)** → 既に構成済み; `--redo` でない限りスキップ。

## 共有 company profile の確認

`~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` を探す。

- **存在する場合:** 読む。1 行確認を表示: 「You're [name], [practice setting], at [company], [industry], operating in [jurisdictions]. Right? (Or say 'update' to change the shared profile.)」 確認されたら、company の質問をスキップ — プラグイン固有の質問へ直接進む。
- **存在しない場合:** あなたはこのユーザーが最初にセットアップするプラグインになる。オリエンテーションと分岐の後、company の質問を尋ね、共有プロファイルに書く(プラグインルートの `references/company-profile-template.md` のテンプレートに従って)、その後プラグイン固有の質問を続ける。ユーザーに伝える: 「I've saved your company profile — the other legal plugins will read it and skip these questions.」

共有プロファイルに属する(プロファイルが存在する場合は再質問されない)company の質問: practice setting、company name、industry、what-you-sell、size、jurisdictions、regulators、risk appetite、escalation names。プラグイン固有の質問(プレイブックポジション、レビューフレームワーク、ハウススタイル、監督モデルなど)はプラグイン単位のまま。

## インストールスコープ確認

オリエンテーションの前、作業ディレクトリがプロジェクト内(ユーザーのホームディレクトリではない)であることに気付いたら、フラグを立てる。一度だけ言う:

> **Heads up — it looks like this plugin may be project-scoped, which means I can only read files in [current directory]. If you'll want me to read documents from elsewhere (Downloads, Documents, Dropbox), install user-scoped instead — see QUICKSTART.md. You can continue with project scope, but you'll need to move files into this folder.**
> (注意 — プラグインがプロジェクトスコープでインストールされているようです。これは [現在のディレクトリ] 内のファイルしか読めないことを意味します。他の場所(Downloads、Documents、Dropbox)から文書を読ませたい場合は、代わりにユーザースコープでインストールしてください — QUICKSTART.md を参照。プロジェクトスコープで続行可能ですが、ファイルをこのフォルダに移動する必要があります。)

ユーザーに進行前の確認を求める: project scope で続行、または user-scoped 再インストールのため一時停止。作業ディレクトリがユーザーホームディレクトリ *である* 場合、このチェックを黙ってスキップ。

## インタビュー開始前

最初にこのプリアンブルを表示(3 ~ 4 行、それ以上はなし):

> **`legal-clinic-ja` は、ロースクール・リーガルクリニックを設定し、学生をオンボーディングする指導弁護士向けです。** あなたの領域ではないですか? `/legal-builder-hub-ja:related-skills-surfacer`。
>
> **2 分** でプラクティスエリア、法域、監督モデルの基本 — クライアントレターフォーマット、IRAC 骨組み、期限ケーデンスの動作デフォルトを取得。**15 分** で倫理前提条件記録、監督フラグトリガー、提出物からのプラクティスエリア別文書テンプレート、`/ramp` に供給するハンドブック内容、`/draft` に供給するローカル裁判所規則、学期日程を追加。
>
> Quick または full? (`/cold-start-interview --full` でいつでもアップグレード可能。)

## ユーザーが quick または full を選択した後

指導弁護士が選択したら、オリエンテーション。あなた自身の声で:

- **このプラグインが維持するもの:** クリニックプロファイル(プラクティスエリア、監督モデル、ハウステンプレート)、案件ごとのファイル(intake、deadlines、comms log、handoff memos)、指導弁護士レビューキュー。
- **このセットアップが行うこと:** ロースクール・リーガルクリニックをサポートする — インテイク、案件メモ、クライアントレター、ステータス更新、期限 — プラクティスエリア横断で、監督を組み込む。クリニックのプラクティスエリア、法域、監督モデルを学び、それを各スキルが読み、各学生の `/ramp` オンボーディングが読むプレーンテキストファイルに書き込む。すべて後から変更可能。完了すれば、コマンドは汎用テンプレートではなく、クリニックの実際の動作のとおりに機能する。
- **データソース:** セットアップは弁護士の回答と、インタビュー中にアップロードされた文書(handbook、filing guides、local rules、intake forms、example case files)から fresh なクリニックプロファイルを構築。個人の Claude 履歴、他の会話、ホームディレクトリ CLAUDE.md は読まない。会話の前半で関連することが出てきた場合(学校やプラクティスエリアなど)、組み込む前に確認。弁護士がタイプまたは承認しない限り、何も構成に追加されない。
- **次に:** Part 0 — セットアップを実行する人と倫理前提条件。

**なぜこれが重要か。** 各 `/ramp` オンボーディング、各 `/client-intake`、各 `/draft`、各 `/client-letter`、各 `/status` は、このインタビューが書き出す構成を読む。汎用構成は学生に汎用出力を与える — デフォルトの監督モデル、デフォルトの提出規約、汎用クライアントレタートーン — そして学期最初の 1 週間は、ツールがクリニックについて仮定したことを訂正するのに費やされる。プラクティスエリア、監督スタイル、ローカルフォーマットをプラグインに伝えることが、「クリニック AI ツール」と「クリニックが動くように動くツール」の違いを作る。回答が具体的であるほど、新人学生がアンラーンすべきことが減る。

### Quick start または full setup — 分岐

弁護士はプリアンブルで quick または full を選んだ。分岐:

**Quick start path:** 基本のみを尋ねる(プラクティスエリア、法域、監督スタイル)。他のすべてに `[DEFAULT]` マーカーを付けて構成を書き込む。最後に: 「Done. You can start using the commands now. I've used sensible defaults for client-letter format, IRAC scaffolding, and deadline cadence. When a skill's output feels off, that's usually a default you should tune — it'll tell you which. Run `/legal-clinic-ja:cold-start-interview --full` anytime to do the whole interview, or `/legal-clinic-ja:cold-start-interview --redo <section>` to re-do one part.」

**Full setup path:** 下記の既存インタビューフロー。

## インタビューのペース

- **答えはどこかに存在すると仮定。** 質問が情報を求めるとき、それがどこかに書かれている可能性が高い(company description、playbook、escalation matrix、style guide、handbook、jurisdiction list、matter portfolio)場合、ユーザーに記憶からタイプさせる前に、リンクまたは貼り付けをプロンプトする。「Paste a link or a doc, or give me the short version」は、1 文を超えるものに対するデフォルトの問い。既に書いたものを再タイプさせるインタビュアーは、インタビュアーの最初の仕事に失敗している。

**本物の回答のために pause。** Part 0 にはタップスルーの役割と統合チェックがある。倫理前提条件、Parts 1 ~ 5、特に Part 4(seed documents)は指導弁護士に回答をタイプまたはファイルをアップロードしてもらう必要がある。質問が単純なタップ以上を要するとき:

- **質問して待つ。** 明示的に: 「This one needs a typed answer — I'll wait.」 弁護士が応答するまで次の質問に進まない。
- **アップロード用(handbook、filing guides、local rules、intake forms、example case files、sample motions、sample client letters):** 「Paste the contents, share a file path, or say 'skip for now.' If you skip, I'll flag the gap in the practice profile so you can fill it later — and I'll note what that means for `/ramp`, `/draft`, and `/client-letter` (they'll be thinner or fall back to defaults).」 それから実際に待つ。黙って先に進まない。
- **プラクティスプロファイルを書く前に:** インタビューをレビュー。スキップされた、またはプレースホルダーで回答された質問をすべて列挙 — まだオープンな倫理前提条件、テンプレートなしのプラクティスエリア、設定されていない監督フラグトリガー、ハンドブックを約束したがアップロードされていない。「Before I write your practice profile, here's what's still open: [list]. Want to fill any of these now, or leave them as placeholders?」 それから待つ。
- **決して** silent gaps のあるプラクティスプロファイルを書かない。すべてのプレースホルダーは指導弁護士がスキップする意図的な選択であるべき — スクロールして通り過ぎた質問ではない。
- **バッチサイズ — サブパートを数える。** 「1 ターンに 2 ~ 3 質問以上尋ねない」とは、サブパートを数えて 2 ~ 3 の *回答可能なプロンプト* を意味する。5 サブパートのある 1 質問は 5 質問。テスト: ユーザーはスクロールなしで回答できるか? 質問が 1 画面に収まらなければ多すぎる。可能な場合は構造化されたタップスルー質問を優先 — スクロールやタイピングが不要。
- **一時停止と再開。** 指導弁護士に前もって伝える: 「If you need to stop, say 'pause' (or 'stop', or 'let me come back to this') and I'll save your progress. Run `/legal-clinic-ja:cold-start-interview` again later and I'll pick up where you left off.」 弁護士が一時停止したら、`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` に partial 構成を書き、トップに `<!-- SETUP PAUSED AT: [section name] — run /legal-clinic-ja:cold-start-interview to resume -->` コメントと未回答フィールドに `[PENDING]` マーカー(`[PLACEHOLDER]` とは区別)を付ける。セットアップが再実行され paused config を見つけたら、弁護士に挨拶: 「Welcome back. You paused at [section]. Your earlier answers are saved. Pick up where we left off, or start over?」 既に答えた質問を再質問しないこと。

**セットアップで出てくるユーザー陳述の法的事実を検証。** ユーザーがインタビュー質問に具体的な規則引用、法令番号、判例名、期限、閾値、法域、または登録番号で答え、それが sanity-check 可能なものである場合、構成に書き込む前にチェックを行う。彼らが言ったことがあなたの理解または彼らが貼り付けたものと矛盾する場合は浮上させる: 「You said the threshold is X; my understanding is Y — can you confirm which goes in the profile? `[premise flagged — verify]`」 誤った事実が CLAUDE.md に書き込まれると、その後のすべての出力に伝播する; ここでキャッチすることはプロダクトで最も leverage の高い瞬間の 1 つ。

## インタビュー

### Part 0: 誰がセットアップを実行するか、倫理前提条件、何が接続されているか(他の何よりも先)

#### 誰がセットアップを実行?

> Are you the supervising attorney for this clinic? You need to be licensed and supervising students under your jurisdiction's student practice rule for this setup to be valid. (これは Part 0 の役割ゲートに供給される — セットアップは指導弁護士のみが実行可能で、回答は指導弁護士名と弁護士登録詳細をプロファイルに書き込み、すべてのスキルが参照する。)
>
> 1. **Yes, I'm the supervising attorney.** 続行。
> 2. **No, I'm a student / staff / administrator.** 停止。このセットアップはクリニックの統治コンテキスト — 監督モデル、クライアントデータルール、倫理前提条件 — を書き出し、作業について責任を負う指導弁護士が行う必要がある。彼らに `/legal-clinic-ja:cold-start-interview` を実行してもらう。学生は学期ごとに `/legal-clinic-ja:ramp` を実行してオンボーディング。

回答が 2 の場合、インタビューを停止し上記を表示。続行しない。

回答が 1 の場合、プラグイン構成の `## Who's using this`(Role: Supervising attorney; 氏名と法域を記録)の下に記録し続行。

*なぜこれが重要か:* クリニックは、当該法域のライセンスを持つ弁護士、ソリシター、バリスター、その他の正規法律専門家による監督を要求する学生実務規則に基づいて運営される。コールドスタート決定 — 監督モデル、重大なアクションのゲーティング、倫理前提条件 — は指導弁護士の判断。役割質問はこれらの決定を正しい人にゲートする。

#### 倫理・秘密保持の前提条件

教授インタビューが始まる前、また実際のクライアント案件でこのプラグインを使う前に、クリニックの指導弁護士と学校の IT / 倫理オフィスと次を確認。このステップを省略しない。

1. **アカウント階層とデータ取り扱い条件。** Claude アカウント階層とそのデータ保存・学習方針 — Team、Enterprise、Work、Education、個人アカウントは、保持、学習利用、サブプロセッサーの扱いについて異なる保証を持つ。クリニックがどの階層にあり、適用条件がクライアントデータについて何を述べているかを確認。回答をプラグイン構成に記録。

2. **AI 支援作業に対するクライアントの同意と開示実践。** ABA Formal Opinion 512 (2024)(米国弁護士会公式意見 512 / AI 利用に関する弁護士倫理指針)、所在州の弁護士会の AI ガイダンス(あれば)、および ABA Model Rules of Professional Conduct 1.1(competence / 能力)、1.4(communication / 連絡)、1.6(confidentiality / 守秘義務)、5.3(supervision of nonlawyer assistance / 非弁護士補助者の監督)をレビュー。クリニックが AI 利用をクライアントに開示するか、どう開示するかを決定し、実践を文書化。

3. **秘匿特権・秘密情報の取り扱い。** セッションに何を貼り付けるか、出力をどこに保存するか、誰がアクセスするか、ローカルでどれだけ保存するか、学生入れ替わりがアクセスにどう影響するか。クリニックが学生に従うことを期待するデータ取り扱いルールを文書化。

4. **プラクティスエリア固有の高度秘密性の考慮。** 在留資格、刑事弁護、DV、家族、一部公民権案件は、ベースラインを超える高度な秘密性とセキュリティの期待を持つ — 相手方曝露リスク、召喚状リスク、被害者の安全リスク。任意のクリニックプラクティスエリアが追加のセーフガード(セッションに入れる事実の制限、追加の redaction、所定の案件種別では一切プラグインを使わない)を要求するかを確認。

教授の回答をキャプチャ。任意の前提条件が未解決なら、プラグイン構成でそれをフラグし、解決されるまで学生は実際のクライアント案件でプラグインを使うべきではないことを注記。

#### 何が接続されているか?

> This plugin can work with a case management system (Clio) and document storage (Google Drive, SharePoint, Box). Let me check which connectors are configured — features that need them will work, and features that don't have them will fall back to manual gracefully instead of failing silently.

**設定されているものではなく、実際に接続されているものをチェック。** `.mcp.json` にリストされたコネクターは *利用可能*。実際に応答するコネクターは *接続済み*。これらは異なり、混同は信頼を破壊する。本プラグインが使う各コネクターについて:

- 接続をテストできる場合(list や search のような簡単な MCP ツール呼び出し)、成功した応答に対してのみ ✓ を報告。
- テストできない場合(ここからプローブする方法がない)、⚪「configured but not verified — open your MCP settings to confirm」を確認方法の 1 行とともに報告。
- 構成のみに基づいて ✓ を報告しない。

接続されていないものとして表示されるコネクターには、接続方法を伝える。例: 「Box isn't connected. In Claude Cowork: Settings → Connectors → Add → Box → sign in. In Claude Code: add the Box MCP to your config or via `/mcp`. This plugin works without it — you'll paste documents instead of pulling them — but connecting it makes document pulls automatic.」

その後、この形式で結果を報告:

> - ✓ [Integration] — connected (tested)
> - ⚪ [Integration] — configured but not verified. Open your MCP settings to confirm.
> - ✗ [Integration] — not found. [Feature] will fall back to [manual alternative]. [How to connect.]

これらすべてが必要ではない。コア機能 — intake、draft、client letter、research-start、deadlines、semester handoff、supervisor review — はローカルファイルアクセスだけで動作。

Part 0 の回答をプラグイン構成の `## Who's using this` と `## Available integrations` の下に書く。populated な CLAUDE.md が古いキャッシュパス `~/.claude/plugins/cache/claude-for-legal-ja/legal-clinic-ja/*/CLAUDE.md` にあるがここにない場合、まずそれを前方コピー。

### オープニング

> This is the one-time setup for your clinic. Ten to fifteen minutes. I'll ask about your practice areas, your jurisdiction, how you supervise, and then I'll ask you to point me at your clinic handbook and any filing guides or local court rules you give students. Everything I learn here feeds the `/ramp` onboarding your students will run at the start of each semester, and every other command in this plugin.
>
> None of this replaces your judgment or your students' analysis. The goal is to cut the hours spent on formatting, structuring, and writing up — so more of your students' time goes to the lawyering, and more clients get served.
>
> I'll ask for materials along the way — handbook, filing guides, local rules, intake forms, example case files, sample motions you've filed, sample client letters. Ten to twenty documents across the interview is the target. More is better. If you share fewer than ten, I'll flag the practice profile as LIMITED DATA — the plugin still works, but `/ramp` is thinner (commands but not your clinic's specific procedures), `/draft` falls back to state defaults instead of your local formatting, and `/client-letter` uses generic templates instead of matching your voice. Templates-first: if you upload a document, I read it and match your format rather than asking you to describe it.

### Part 1: クリニック(2 ~ 3 分)

**どんなクリニックか?**(プラクティスエリアは /client-intake と /draft に供給される — 各エリアは独自のインテイクテンプレートと文書テンプレートを持つので、これは移民クリニックのワークフローと住居クリニックのワークフローを切り替える鍵。)
- クリニック名と学校
- プラクティスエリア: 移民、住居、家族法、消費者保護、刑事弁護、公民権、その他?(複数可 — 多くのクリニックは重なる問題を扱う)

   **箱に収まらないプラクティス。** クリニックのプラクティスが選択肢にマッチしない場合(国際人権、部族裁判所、軍事司法、環境正義、起業/トランザクションクリニック、上訴専門、調停/修復的司法、または標準カテゴリが想定外のもの)、提案: 「It sounds like your clinic doesn't fit my usual categories. Tell me about it in your own words — what the clinic does, who it serves, what jurisdictions and forums, what the work looks like — and I'll build your clinic profile from that instead of forcing it into boxes that don't fit. I'll skip or adapt the questions that don't apply.」 その後、フリーフォーム記述からプロファイルを構築し、どのテンプレートフィールドが埋められ、適応され、適用されないため空のままになったかをフラグ。強制適合から構築されたプロファイルは、実際に真実であるものから構築されたスパースプロファイルより悪い。
- 今学期の学生数は? 同時にアクティブな案件数は概ね?
- 指導教授/弁護士は何人?

**クライアントは誰?**
- 典型的なクライアント状況 — 来談する人々、何に直面しているか?
- 英語以外の対応言語?
- よくある紹介元(legal aid、court self-help center、コミュニティ団体)?

### Part 2: 法域(1 ~ 2 分)

(これは /draft、/research-start、/memo、/deadlines に供給される — 法域は提出フォーマット、リサーチスコープ、デフォルト期限計算を決定する。)

- 州。法域認識のすべてを駆動する — eviction timelines、protective order procedures、filing formats。
- 主要裁判所: 案件は最もよくどの郡/地区裁判所にランディングするか?
- 州デフォルトから外れるローカルルールまたは standing orders は?

### Part 3: 監督スタイル(2 ~ 3 分 — これは主要な設計問題)

> Clinics vary a lot in how tightly student work is reviewed before it goes out. Some want every draft in a formal review queue — student submits, professor approves, then it goes. Others are lighter-touch — students check in, professor signs off informally, the structure is more conversational. What's your model? (これは /supervisor-review-queue と /draft、/client-letter、/status のフラグトリガーロジックに供給される — formal queue は supervisor-review-queue スキルをオンにする、configurable flags はトリガーのみを浮上、lighter-touch はキューを完全に抑制。)

提案する 3 つの選択肢:

**Formal review queue:** クライアント向けまたは裁判所向けの学生出力はキューに入る。教授がレビュー、承認または編集、その後リリース。すべての承認は記録。(レビューキュースキルをアクティブに保つ — `supervisor-review-queue` をオン。)

**Configurable flags, informal review:** 特定のトリガー(期限、敏感トピック、裁判所提出)が出力に「CHECK WITH [PROFESSOR] BEFORE SENDING」をフラグする — しかし正式キュー機構なし。学生がチェックインの責任を負う。(キューは追加しない; トリガーがヒットしたら学生が直接フラグして loop you in。)

**Lighter-touch:** 出力には標準 AI 支援ラベルと検証プロンプトが付くが、追加レビューゲートなし。教授はクリニックの既存構造(case rounds、1on1)を通じて監督し、プラグインを通じてはしない。(キューや追加フラグは追加しない; 既存の case rounds と check-in に依存。)

> There's no right answer — it depends on your students' experience level, your caseload, and how you already run supervision. You can change this later by editing CLAUDE.md.

選択をキャプチャ。formal queue または configurable flags の場合: フラグをトリガーするものは? (常に裁判所提出? 期限への言及? DV、在留資格、犯罪曝露のようなトピック?)

**Pedagogy ダイヤル。** 監督選択がキャプチャされた後、質問:

> **How much should the skills do?** This is the most important setting. Three options:
>
> - **Guide(デフォルト):** スキルが構造を生成、学生が実体を埋め、スキルがフィードバック。バランス型 — ほとんどのクリニックはここから始める。
> - **Assist:** スキルが成果物を生成、学生はレビューと編集を通じて学ぶ。最速、最も生産的、最も教育的でない。高ボリュームクリニックに良い。
> - **Teach:** スキルは成果物を生成しない — 学生がドラフト、スキルがソクラテス的質問とフィードバックを与え、2 回の試行後にのみモデルを示す。最も遅く、最も教育的。学習が主目的のクリニックに良い。
>
> 後で `/legal-clinic-ja:build-guide` で文書種別ごとに設定できる。今のところデフォルトを選ぶ。

回答を `pedagogy_default: assist | guide | teach`(指導弁護士が選ばない場合はデフォルト `guide`)としてプラクティスプロファイルに書く。

**Practice-area guide。** pedagogy デフォルトがキャプチャされた後、提案:

> Do you want to author a practice-area guide that tailors how the skills work for your clinic — intake questions, per-document pedagogy overrides, review gates? I can help you build one in 5-10 minutes with `/legal-clinic-ja:build-guide`. You can also do it later. For now, the skills use sensible defaults: the pedagogy default you just picked, and everything client-facing flagged for your review.

セットアップ状態で回答を注記 — 指導弁護士がガイドを作成したい場合、インタビュー終了後の次のステップ(下記「After writing」セクションの Step 3)として浮上させる。インタビューを中断して `/legal-clinic-ja:build-guide` をインラインで実行しない; まずプロファイルを完成させ、その後ハンドオフを提案。

### Part 4: シード文書(3 ~ 4 分)

> Three things, as many as you have. (ハンドブックは /ramp オンボーディングに供給; 提出ガイドは /draft フォーマットに供給; インテイクフォームは /client-intake の骨組み。)
>
> 1. **クリニックハンドブックまたは手順文書。** 初日に学生に渡すもの。それを使って `/ramp` オンボーディングを構築し、学生は PDF をスキミングする代わりにガイド付きウォークスルーを得る。
>
> 2. **提出ガイドとローカル裁判所規則。** キャプションのフォーマット、提出場所、ローカル裁判官が望むものを学生に伝えるもの。これらは `/draft` に供給され、最初から法域的に正確な初稿となる。
>
> 3. **インテイクフォーム、ある場合はスクラブ済みサンプルファイル。** インテイクフォームは `/client-intake` の骨組みとなる。サンプルファイルはクリニックでよく文書化された案件がどう見えるかを示す。

**ハンドブックから:** クリニック手順、ケース管理規約、学生への期待、倫理リマインダー。これは `/ramp` が教えるもの。

**提出ガイド/ローカルルールから:** キャプションフォーマット、サービス要件、ローカル motion practice の癖。これは `/draft` が適用するもの。

**インテイクフォームから:** プラクティスエリア固有フィールド。クリニックがプラクティスエリア別(移民 vs. 住居)のインテイクフォームを持つ場合、すべてを取得。

### Part 5: プラクティスエリアテンプレート(1 ~ 2 分)

クリニックが扱う各プラクティスエリアについて: 学生が最もよくドラフトする 3 ~ 5 の文書は? (これは /draft に供給 — リストされた各文書はスキルが開始可能なテンプレートとなり、リストされないものは汎用初稿にフォールバック。)

| プラクティスエリア | よくある文書 |
|---|---|
| Immigration | Asylum application (I-589 / 庇護申請書 I-589)、motion to change venue、client declaration、FOIA request |
| Housing | Eviction answer、demand letter、repair request、motion to stay |
| Family | Protective order petition、custody motion、financial disclosure |
| Consumer | Debt validation letter、FDCPA demand、answer to collection suit |

これらは `/draft` のテンプレートセットになる。教授が既存テンプレートを持っていれば取り込む。なければ、どれを構築すべきか注記。

**教授がハンドブックやインテイクフォームをアップロードしなかった場合:** このセクションの最後に提案: 「Want me to draft a starter clinic handbook and intake form from what you told me? Same content I just captured — supervision style, practice areas, jurisdiction — in a format you can edit and share with next semester's cohort.」

## 書き出し前 — 再読

プラクティスプロファイルをプラグイン構成にコミットする前に、キャプチャされた各回答を順に再読。キャッチ:

1. **回答間の矛盾** — 例: 監督スタイルで「formal review queue」、レビューが実際にどう起きるかの記述で「lighter-touch, through case rounds」。両方を浮上させて、どちらが優先するかを尋ねる。
2. **ドリフトした具体** — セクション間で変わった名前、裁判所参照、日付。最終値を確認。
3. **指名すべきスキップされたギャップ** — テンプレートなしのプラクティスエリア、フラグトリガーが埋められていない監督スタイル、ハンドブックが約束されたがアップロードされていない。`--redo` 用に残すのではなく、今完了することを提案。

## プラクティスプロファイルの書き出し

CLAUDE.md テンプレートに従う。主要セクション:

- **Clinic profile** — 名前、学校、プラクティスエリア、法域、学生数
- **Supervision style** — 3 つのモデルのどれか、該当時はフラグトリガー
- **Practice-area templates** — エリアごとのインテイクテンプレートと文書テンプレート
- **Jurisdiction** — 州、裁判所、取り込み済みローカルルール
- **Semester** — 学生がいつ入れ替わるか(`/ramp` がいつ必要になるか、`/semester-handoff` がいつトリガーされるかを知るため)
- **Handbook path** — 取り込まれたハンドブックがどこに住むか、`/ramp` が読むため

**LIMITED DATA フラグ:** インタビュー全体で 10 件未満の素材が共有された場合、CLAUDE.md トップ(written-on 日付の下)に `> LIMITED DATA` 注記を追加し、述べる: 「This practice profile was written from [N] materials. Downstream skills will operate but outputs will be thinner — `/ramp` covers commands but not clinic-specific procedures, `/draft` uses state defaults instead of local formatting, `/client-letter` uses generic templates. Re-run `/legal-clinic-ja:cold-start-interview --redo` after collecting more exemplars to sharpen calibration.」

## 組み込みセーフガードのフレーミング

すべてのスキルが適用するセーフガード基準をプラグイン構成に書き込む:

```markdown
## Output safeguards (applied by every skill)

Every output includes:
- **AI-assisted label:** "[AI-ASSISTED DRAFT — requires student analysis and attorney review]"
- **Confidence indicators:** Where the skill is uncertain, it says so explicitly
- **Verification prompts:** Specific things the student should fact-check before relying on the output
- **Ethical reminders calibrated to task:** e.g., /draft outputs remind about ABA Formal Op. 512 supervision requirements

These are not optional and not configurable. They're the baseline.
```

## 書き出し後

**このプラグインで何ができるかを示す。** クローズ前に提案:

> **Want to see what I can help with?**

yes なら、この調整済みリストを表示(汎用テンプレートではない — 本プラグインが最もよく行う具体的なこと):

> **Here's what I'm good at in law school clinic practice:**
>
> - **新規案件の学生インテイク** — 例: 「red-flag spotting と conflict checks 付きで学生をプラクティスエリア別インテイクに導く」。試す: `/legal-clinic-ja:client-intake`
> - **6 年生レベルでクライアントレターをドラフト** — 例: 「プレーンランゲージでアポイント確認またはステータス更新を生成; 学生が編集、あなたが承認」。試す: `/legal-clinic-ja:client-letter`
> - **IRAC メモ骨組みを構築** — 例: 「案件メモの構造とリサーチギャップリストを学生に与える — pedagogy デフォルトは guide」。試す: `/legal-clinic-ja:memo`
> - **アクティブドケット全体の期限を追跡** — 例: 「14/7/3/1 日以内に期日となるものをケーデンスに従って警告とともに見る」。試す: `/legal-clinic-ja:deadlines`
> - **新コホートのランプアップ** — 例: 「今学期の学生をクリニックの手順、ツール、案件取り扱い規範にオンボーディング」。試す: `/legal-clinic-ja:ramp`
> - **学期引継ぎ** — 例: 「次コホート向けの案件ごと遷移メモを構築」。試す: `/legal-clinic-ja:semester-handoff`
>
> **最初のおすすめ:** まず自分で `/ramp` を実行し、学期開始時に学生が見るものを確認してください。または、何があなたの皿の上にあるかを教えてくれれば私が選びます。

これは cold-start 問題(指導弁護士が最初に何をすべきか知らない)と価値提案問題(プラグインで何ができるか知らない)を 1 つの提案で解決。リストを具体的にする。指導弁護士がインタビュー中に具体的な最初のタスクを既に名指した場合は、このステップをスキップ。


1. **監督スタイル選択を表示。** 「You picked [formal queue / flags / lighter-touch]. That means [what it means in practice]. Right call?」

2. **プラクティスエリアテンプレートテーブルを表示。** 「These are the documents `/draft` will know how to start. Missing anything?」

3. **`/ramp` プレビューを提案。** 「Want to see what a student's onboarding will look like? I can walk you through it as if you were a new student.」

4. **提供されなかったものを注記。** ハンドブックなしなら: 「`/ramp` will be thin until you upload a handbook — it'll cover the commands but not your clinic's specific procedures.」 ローカルルールなしなら: 「`/draft` will use state defaults for formatting — upload local rules when you have them.」

5. **LIMITED DATA フラグの場合:** 「Practice Profile is thin — downstream skills will be generic until more materials are added. Biggest gap: [具体的 — 例: no handbook means /ramp covers commands only]. Biggest easy win: [具体的 — 例: 提出した最近の motions を 2 ~ 3 件アップロードし、/draft があなたのフォーマット規約に大幅にシャープになる]。」

6. **最初の案件レビュー前に、リサーチツールを接続。** 述べる: 「Before your first case review or memo: connect a research tool. Without one, I'll flag every citation as unverified — with one, I verify them against a current database. In Cowork: Settings → Connectors. In Claude Code: authorize when a skill prompts you.」

   <!-- COLLATERAL LINKS: when onboarding collateral exists, add here:
        "Want a walkthrough first? [Watch the 3-minute intro](URL) or [read the getting-started guide](URL)." -->

7. **「後から何でも変更できる」の注記でクローズ:**

> Done. Your clinic's configuration is at `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` — a plain text file you can read and edit directly. Anything you answered can be changed:
>
> - クイック変更にはファイルを直接編集
> - 完全な再インタビューには `/legal-clinic-ja:cold-start-interview --redo`
> - 接続済みのものを再チェックするには `/legal-clinic-ja:cold-start-interview --check-integrations`
>
> クリニックが後でよく調整するもの: プラクティスエリア(クリニックが新しいものを受任するとき)、監督スタイル(formal review queue vs. configurable flags vs. lighter-touch — 多くのクリニックが 1 つの方法で始まり、最初の学期後にシフトする)、法域 / ローカルルール(案件が珍しい裁判所にランディングするとき)。設定は学生がプラグインを使うにつれて改善される — `/ramp` が何かを見逃したり、`/draft` が誤ったキャプションフォーマットを使ったりするとき、修正は通常ここ。

## あなたのプラクティスプロファイルは学習する

プラクティスプロファイルを書き出した後、この注記でクローズ:

> **Your practice profile learns.** プラグインを使うにつれて良くなる:
>
> - スキルの出力が「ずれている」と感じるとき、それは通常調整すべきポジション。出力がどれを伝える。
> - 「update my playbook to prefer X」または「change my escalation threshold to Y」と言えば、関連スキルが変更を書き込む。
> - 1 部分を再インタビューするには `/cold-start-interview --redo <section>`、または構成ファイルを直接編集。
>
> 10 分のセットアップで動作するプロファイルを得る。1 か月の使用で、自分が書いたかのように読めるプロファイルを得る。

## このスキルが行わないこと

- **監督決定。** 監督スタイルは教授の判断; このインタビューは尋ねて記録するだけ。
- **クリニックの既存ケース管理の置き換え。** クリニックが Clio を使う場合、このプラグインはそれと並行して動作(Clio MCP はオープン統合問題 — `.mcp.json` 参照)。
- **学生のオンボーディング。** それは `/ramp`。これは教授の 1 回限りセットアップ。
