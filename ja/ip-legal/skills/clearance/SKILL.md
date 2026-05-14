---
name: clearance
description: >
  Trademark clearance first pass — knockout + similar-marks check producing a
  flag list, not a clearance opinion. Use when a new mark is proposed, when
  asked whether a mark is available or to run a knockout search, or when
  assessing likelihood-of-confusion factors before a full professional search.
  This skill never concludes a mark is clear.
  商標クリアランス初回パス — knockout + 類似マーク・チェックでフラグリストを生成、
  クリアランス意見ではない。新マークが提案されたとき、マークが利用可能かを問われた
  あるいは knockout 調査の実行を求められたとき、または完全な professional 調査前の
  混同のおそれの要因を評価するときに使用。本スキルはマークが clear と決して結論しない。
  日本語トリガー: 「商標クリアランス」「商標調査」「ノックアウト調査」「商標予備調査」「マーク調査」。
argument-hint: "[提案マーク、商品/役務、法域を記述 — またはマークのみ、こちらから尋ねる]"
---

# /clearance

**これはトリアージで、クリアランス意見ではありません.** 商標クリアランス意見は完全な professional 調査と登録商標カウンセルの判断を要します。「明らかなコンフリクトなし」の結果はトリアージが何も見つけなかったという意味で、マークが clear という意味ではない。Knockout 調査を通過したマークでクライアントが訴えられた事例があります。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読む。`[PLACEHOLDER]` を含むなら停止して `/ip-legal-ja:cold-start-interview` に誘導。
2. 下記のワークフローに従う。
3. インテーク(マーク、商品/役務、クラス、法域、ビジュアル/スタイル化)を実行。
4. 内在的バーの knockout チェック — 普通名称、記述的、欺瞞的、地理的、姓、虚偽の関連、禁止対象、機能的。
5. 接続されているもの(Solve Intelligence、CourtListener、Descrybe、または利用可能な任意の MCP)に対して類似マーク調査。何も接続されていなければ、出力でそう述べ、要因分析のみで進める。
6. 適用される巡回区の混同のおそれの要因を歩く — du Pont / Polaroid / Sleekcraft / その他。各々をフラグ; 決して結論しない。
7. トリアージ・メモを案件フォルダ(案件がアクティブなら)またはプラクティス出力フォルダに書く。Role に応じた work-product ヘッダーを適用。
8. 推奨される次のステップと、Role が非弁護士なら非弁護士ゲートで終了。

本スキルはマークが clear と決して結論しません。不確実なら、フラグ — 弁護士が決める。

## 例

```
/ip-legal-ja:clearance "APEXLEAF for an outdoor apparel line, planned launch US + EU"
```

```
/ip-legal-ja:clearance
```

(スキルがマーク、商品、クラス、法域を尋ねます。)

---

## THIS IS A FIRST PASS, NOT A CLEARANCE OPINION

**毎回の出力の上部でこれを述べてください。落とさない、和らげない.**

> **This is a first pass, not a clearance opinion.** 商標クリアランス意見は
> 完全な professional 調査(TESS、州登記所、コモンロー・ソース、国際登記所、
> ドメインとソーシャル、関連時にトレードドレスとデザイン・マーク)と、構造化
> トリアージが完全に評価できない要因に依存する混同のおそれに関する弁護士判断を
> 要します。本スキルからの「明らかなコンフリクトなし」結果はトリアージが何も
> 見つけなかったという意味であり、マークが clear という意味ではない。
> Knockout 調査を通過したマークでクライアントが訴えられた事例があります。
> 登録商標弁護士が、誰かがこのマークを採用、出願、または投資する前に評価します。

これはプラグイン中で最も声の大きいガードレールです。コンフリクトを under-call するのは一方通行のドア — トラックのロゴ、ローンチされた製品、出願された TM、すべて下に問題を抱える。Over-call は両方向のドア — 弁護士がレビューでリストを絞る。両方向のドア側に留まる。

---

## 案件コンテキスト

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(インハウスのデフォルト)なら、この段落の残りをスキップ — スキルはプラクティスレベル・コンテキストを使い、案件機構は不可視。有効でアクティブ案件がなければ問う: 「どの案件向け? `/ip-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください」。アクティブ案件の `matter.md` を案件固有コンテキストとオーバーライド用に読む。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/` に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まないこと。

---

## まずプラクティスプロファイルをロード

クリアランスを実行する前に、`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読む。取得:

- `## Who's using this` から **Role**(弁護士 vs 非弁護士は work-product ヘッダーと下記の非弁護士ゲートを変える)。
- `## IP practice profile` および `## Enforcement posture` から **Registered in** と **enforce where**(ユーザーが指定しない場合のデフォルト法域)。
- `## Available integrations` から **Integrations**(CourtListener / Solve Intelligence / Descrybe — 各々が実行可能な調査、フォールバック、出力で帰属されるものを決定)。
- `## Decision posture on subjective legal calls` から **Decision posture** — 本スキルは「混同的に類似ではない」と決して結論しない。

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` が `[PLACEHOLDER]` または `[Your Company Name]` を含むなら、このバウンスを表面化:

> プラクティスプロファイルをまだ設定していないようです — これがスタンス、法域、承認チェーンをあなたのプラクティスに合わせる方法です。
>
> **2 つの選択肢:**
> - `/ip-legal-ja:cold-start-interview`(2 分)を実行してプロファイルを設定、その後 YOUR プラクティスに合わせてこれを実行します。
> - **"provisional"** と言えば、これを汎用デフォルト — 米国法域、middle リスク選好、弁護士 Role、プレイブックなし — で実行し、すべての出力を `[PROVISIONAL — configure your profile for tailored output]` でタグするので、コミット前に私の動作が分かります。

### Provisional モード

ユーザーが「provisional」と言ったら、これらの汎用デフォルトでクリアランスを通常通り実行: middle リスク選好、弁護士 Role、米国法域(USPTO + コモンロー)、プレイブックなし(立場リストに対するマッチではなく完全分析を実行)。レビューアーノートとすべての発見ブロックを `[PROVISIONAL]` でタグ。出力の終わりに追加:

> 「それはデフォルト前提に対する汎用実行でした。YOUR プラクティスに合わせた出力 — プレイブック、法域、リスク選好 — のために `/ip-legal-ja:cold-start-interview` を実行してください。2 分」。

---

## インテーク

1 バッチで一度尋ねる(クイックジョブを引き伸ばさない):

> トリアージ前にいくつか質問:
>
> 1. **提案マーク.** 正確なスペル、任意のスタイル化、ワードマーク、ロゴ、または両方か。
> 2. **商品または役務.** このマークの下で実際に何が販売または提供されるか。1〜2 文 — 国際クラスにマッピングします。
> 3. **クラス.** すでに Nice クラスが分かるならリスト。さもなくば商品/役務を記述、可能性の高いクラスを提案して調査前に確認します。
> 4. **法域.** どこで使用、登録、エンフォースを計画? (US / EU / UK / Madrid / 特定国 — 指定なければプラクティスプロファイルの `Registered in` にデフォルト。)
> 5. **使用時にどう現れるか.** 一緒に市場に出るタグライン、隣接製品名、トレードドレス、デザイン要素。

回答を待つ。記述が漠然(「AI tool」、「platform」)なら、一度プッシュ:

> カスタマーが見る実際の物を教えて — コンシューマー・モバイル・アプリ、エンタープライズ API、物理製品、サービス? クラスはこれに依存します。

---

## Knockout チェック

任意のデータベース調査前に、先行登録に関わらずマークを殺す内在的問題を実行。各々について plainly に評価しフラグ。明らかな問題を合理化しないこと。

| バー | 意味 | フラグするとき |
|---|---|---|
| **Generic(普通名称)** | 用語がカテゴリ自体(例: 石鹸の「Soap」) | マークが物の名称 |
| **Descriptive(記述的)** | 特徴、機能、品質、または成分を直接記述 | カスタマーがマークを読み、想像せずに製品が何をするか分かる |
| **Deceptive / deceptively misdescriptive(欺瞞的)** | 物質的特徴を誤表現 | マークが商品が持たない品質を示唆し、その品質が重要 |
| **Primarily geographically descriptive / deceptive** | マークが主に地名で、商品はその場所から来るか(または来ない) | マーク = 地名 + 普通名称; または地名 + 商品でカスタマーが原産地を想定 |
| **Primarily merely a surname(主に単なる姓)** | マークが主に姓 | 関連カスタマーにとって誰かの苗字に読める |
| **False connection(虚偽の関連)** | マークが人、機関、国家シンボルとの関連を虚偽に示唆 | マークが特定の識別可能な人または機関を想起 |
| **Prohibited matter(禁止対象)** | 国旗、紋章、徽章、特定の禁止カテゴリ | マークが禁止要素を含む |
| **Functional(デザインマーク / トレードドレス用)** | 特徴が使用に essential またはコスト/品質に影響 | デザインマーク — かつ特徴が機能を実行 |

スキャンダル/不道徳マークに関する注記: *Iancu v. Brunetti*(2019)および *Matal v.
Tam*(2017)以降、USPTO はこれらの根拠で登録を拒否しない。このゾーンで生き残った制定法バーは §2(a) 下の虚偽の関連。それを適用; 撤回されたバー下でフラグしないこと。

**出力:** 各 knockout カテゴリについて、「問題なし」または具体的フラグと 1 行理由のいずれか。Pass の空白テーブルを生成しないこと。

---

## 類似マーク・チェック

ここの目的は、混同が likely かを決めることではなく、**潜在的に混同的に類似な先行マークを見つける** こと。それは弁護士の判断。

### ユーザーが接続しているもの

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` から `## Available integrations` を読む:

- **商標検索コネクターが利用可能なら**(Solve Intelligence、Descrybe — または TM 登記所検索を公開する任意の MCP): 関連クラスと法域で予備検索を実行。各結果をソースに帰属。検索日とスコープ(どの登記所、どのクラス、完全一致 vs ファジー、デザイン検索か否か)を注記。
- **法律調査コネクターが利用可能なら**(訴訟および TTAB 判断の判例法用 CourtListener): マークまたは近い変形に関わる報告された紛争を sweep。同じ帰属ルール。
- **検索コネクターが利用不能なら:** 出力で明示的にそう述べる。モデル知識から結果を推測して検索発見として提示しないこと。

### データベース・アクセスがない場合のフォールバック

出力で、この正確な文を書く:

> **No database search was run.** This triage did not hit TESS, Solve
> Intelligence, Descrybe, CourtListener, state registries, Madrid/WIPO, or any
> common law / unregistered-mark sources. A knockout or full search across those
> databases is required before any conclusion about availability. The triage
> below is limited to intrinsic-bar analysis and structured confusion factors
> against marks the user has identified or that come up in the conversation.

その後進む — 内在的チェックと要因分析は依然有用、ただ honest にラベル付け。

### 見つかった(または供給された)各類似マークについて

取得:

- **マーク**(正確な文字、任意のスタイル化)
- **ソース**(TESS 登録番号、Madrid 指定、州登記所、判例引用、ドメイン、ソーシャル・ハンドル — どれか)
- 登記所からの **クラス / 商品-役務記述**
- **オーナー**
- **ステータス**(registered / pending / abandoned / cancelled — 死マークはバーではないが、有名性と前任の権利に関連しうる)
- 利用可能なら **First-use 日**

**サイレントに supplement しないこと.** USPTO 登録番号を引用するなら、実行した調査から来た; ユーザーが言及したマークを記述するなら、そう言う。決して登録を発明せず、レコードがサポートしない詳細を「埋めない」こと。検索が first-use 日を返さなかったら、「first-use date not available from search result」と書く — 推測しないこと。

### 隣接ファミリー sweep(結論前に必要)

完全および近接一致のみをチェックするクリアランスは、あなたのマークが取られた*ため*に競合が採用したマークを見逃します。結論前に、実務家も sweep すべき 3〜5 の隣接ワード・ファミリーを特定し、リストの確認または追加をユーザーに依頼。

隣接ファミリーは、直接マークが利用不能なときに合理的な競合が検討するカテゴリ慣習的代替。スマートホーム・ハブ・スペースの `NEXUS HOME` のようなマークについて、隣接ファミリーは最低限以下を含む:

- **NEXUS のカテゴリ類義語**: `HUB`、`NEST`、`CORE`、`LINK`、`CONNECT`、`BRIDGE`、`CENTRAL`、`GATEWAY`。
- 同じ製品カテゴリの **アシスタント・スタイル名**: `ALEXA`、`ECHO`、`SIRI`、`GOOGLE HOME`、`CORTANA`、`HOMEY`、`HOMEBASE`。
- **HOME / HOUSE / SMART バリアント**: `SMART HOME`、`HOUSEHOLD`、`HOUSE`、`ABODE`、`CASA`、`DOM`。
- ルートの **音声学的ツイン**: `NEXIS`、`NEKSUS`、`NEXXUS`、`NECTIS`、`KNOXUS`(市場で単語がどう位置するかによる)。

スキルは類似マーク・セクションで隣接ファミリー・ブロックを確認プロンプト付きで出力すべき:

> **Sweep する隣接ファミリー(確認または追加してください):**
>
> - [family 1 — 例: HUB / NEST / LINK / CONNECT]
> - [family 2 — 例: ALEXA スタイルのアシスタント名]
> - [family 3 — 例: HOME / HOUSE / SMART バリアント]
> - [family 4 — ルートの音声学的ツイン]
>
> 完全および近接一致のみをチェックするクリアランスは、あなたのマークが取られた
> ため競合が採用したマークを見逃します。続行前に、カテゴリについてこのリストが
> 完全であることを確認してください。

> **非英語圏の法域がスコープにある場合**、英語のみの音声学的 sweep は越境コンフリクトの最も一般的なソースを見逃します。追加:
> - **翻訳等価物.** マークを関連言語に翻訳。EU の foreign-equivalents ドクトリンは、混同目的で翻訳を同じマークとして扱います。
> - **音訳.** マークを関連スクリプト(キリル、中国語/日本語/韓国語、アラビア語、ハングル、タイ語)で書く。スクリプト間の音声学的等価性は認められたコンフリクト基盤。
> - **スクリプト・バリエーション.** ローマ字化したときにあなたのマークと同じ音に聞こえる非ラテンスクリプトで登録されたマーク。
>
> 多言語分析を実行できない場合、そう言う: 「Cross-language phonetic and translation-equivalent analysis not performed — this is the most common source of cross-border conflicts. A clearance search in [jurisdiction] should include it.」

実務家が接続済み TM 検索ツールを持つなら、確認された各隣接ファミリーに対して sweep を再実行(完全 + 音声学的 + 関連時に foreign-equivalent 翻訳)し、結果を `Adjacent family` ソース注記付きで類似マーク・テーブルに追加。コネクターが利用不能なら、そう言い、ファミリーを完全な professional 調査への明示的な次ステップ入力としてリスト — サイレントに sweep をスキップしないこと。

---

## 混同のおそれの要因

> **混同フレームワークは法域固有.** 米国と EU は混同のおそれを異なる方法で評価。誤ったものを適用しないこと。
>
> - **米国(連邦巡回区):** 多要因テスト(*du Pont*、*Polaroid*、*Sleekcraft*) — マークの強さ、類似性(視覚/音/意味)、商品の近接性、チャネル、購入者の洗練度、実際の混同、意図。
> - **EU(Art. 8(1)(b) EUTMR):** Global appreciation — 平均的カスタマーの目を通してすべての関連要因を holistically に評価。主な違い: 音声学的類似性により大きな重み; 翻訳等価物が標準(マークを EU 言語に翻訳); 起源の混同を超えた「likelihood of association」; 先行マークの distinctiveness がより重みを運ぶ。
> - **UK(TMA 1994 §5(2)):** Brexit 後の EU global appreciation アプローチに従うが、分岐する判例法。UK 固有の判決を確認。
> - **その他の法域:** インテークが上記フレームワークなしの法域を含む場合、言う: 「[jurisdiction] の混同フレームワークがありません。米国テストを適用すれば、正しく見える間違った答えになります。選択肢: (a) 適用基準を検索、(b) [jurisdiction] 商標スペシャリストにルート、(c) この法域がスコープ外と注記」。米国ドクトリンをサイレントに適用しないこと。

関連巡回区のテストが歩く要因を決定。適用するテストを引用:

- **TTAB / Federal Circuit:** *In re E. I. du Pont de Nemours & Co.*, 476 F.2d
  1357 (C.C.P.A. 1973)(13 要因)。
- **Second Circuit:** *Polaroid Corp. v. Polarad Electronics Corp.*, 287 F.2d 492
  (2d Cir. 1961)(8 要因)。
- **Ninth Circuit:** *AMF Inc. v. Sleekcraft Boats*, 599 F.2d 341 (9th Cir. 1979)
  (8 要因)。
- **その他の巡回区:** 巡回区の名付けられた多要因テストを歩く(例: 第六巡回区の
  *Frisch's Restaurants*、第七巡回区の *Scotch Whisky Association*、第三巡回区の
  *Lapp*)。

ユーザーがエンフォースを計画する場所(プラクティスプロファイル)、直接フォーラムが登録なら TTAB、それ以外なら主要な商業フォーラムに基づき選ぶ。出力で選択を注記。

各要因について、verdict ではなく **フラグ** を生成。各要因は何が両方向に切れるかと不確実性がどこにあるかを述べるべき:

- **マークの類似性**(外観、音、意味 / connotation、商業的印象)。視覚-音-意味を一緒に考慮。
- **商品または役務の類似性.** 商品が同一かではなく — カスタマーが同じソースから来ると期待するか。
- **取引チャネル.** 各サイドが実際に販売する(または販売する)場所。同じ店? 同じ流通? 同じ展示会? オンラインのみ?
- **カスタマーの洗練度.** ガソリンスタンドでの衝動買い vs 検討されたエンタープライズ購買は注意基準を変える。
- **見つかった先行マークの強さ.** Fanciful / arbitrary / suggestive / descriptive / generic、および任意の有名性証拠。強い先行マークはより広い保護を得る。
- **意図.** Goodwill にトレードする意図の証拠 — 隣接クラスでの類似トレードドレス付きの near-copy は、独立した造語とは異なる。
- **実際の混同.** 任意の証拠(誤誘導された問い合わせ、調査、レビュー、ソーシャル投稿)。
- **拡張のおそれ**(bridge-the-gap)。先行ユーザーが junior の lane に拡張する可能性、およびその逆。

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` の決断スタンスに従って:

- **「混同的に類似ではない」と決して結論しない.**
- 不確実なら、書く: 「類似マーク発見 — 採用前に混同評価が必要」。または: 「要因が両方向に切れる; 弁護士判断が必要」。
- 「検索したデータベースで類似マーク発見なし」のクリア・スペースは、実際の検索が実行された場合 *のみ* OK; さもなくば上記の no-search フォールバックを参照。

---

## 推奨される次のステップ

各クリアランス出力は、トリアージが発見したものでバケット化した具体的な次のステップで終わる:

- **Knockout 問題発見の場合:** マークをリフレーム、または記述性バーを受け入れて時間をかけて secondary meaning を計画; 採用前に弁護士レビューにルート。
- **検索したデータベースで類似マーク発見の場合:** 採用、出願、またはマーケティング前に弁護士レビューが必要。多くの場合、次のステップはトリアージが見逃したすべてを見つける完全な professional 調査。
- **類似マーク発見なしだがデータベース検索が実行されなかった場合:** 採用前に完全調査が必要。ヒットすべきデータベースを名指す。
- **類似マーク発見、senior マークが弱い、古い、異なるクラス、または放棄された場合:** 弁護士レビューのためフラグ — トリアージはこの判断をしない。
- **常に:** 登録商標カウンセルからの完全クリアランス意見、マークが運ぶ投資にスケールして。製品ラインと Super Bowl 広告に載せるマークは、ワンオフ・ポップアップ用マークより重みを運ぶ。

---

## 出力フォーマット

`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` `## Outputs` から work-product ヘッダーを付与。

```markdown
[WORK-PRODUCT HEADER]

# Trademark Clearance — First Pass (NOT AN OPINION)

**This is a first pass, not a clearance opinion.** クリアランス意見は
完全な professional 調査と弁護士判断を要します。ここでの「明らかなコンフリクトなし」
結果はトリアージが何も見つけなかったという意味であり、マークが clear という意味
ではない。登録商標弁護士が、誰かがこのマークを採用、出願、または投資する前に評価
します。

**Triage result:** [GREEN / YELLOW / RED — 1 文でなぜ]

## 提案マーク

- **Mark:** [正確なテキスト、スタイル化注記]
- **Mark type:** [word / design / composite]
- **Goods / services:** [記述]
- **Classes:** [Nice クラス番号、1 行記述付き]
- **Jurisdictions:** [US / EU / UK / Madrid / 特定国]
- **Confusion test applied:** [du Pont / Polaroid / Sleekcraft / その他 —
  正しい理由付き]

## Knockout 問題

| バー | フラグ | 注記 |
|---|---|---|
| Generic / descriptive / deceptive / geographic / surname / false connection / prohibited / functional | [none / flagged] | [フラグなら 1 行] |

## 類似マーク・チェック

**Sources searched:** [ヒットした登記所とデータベース、日付付き — または「データベース
検索なし; スコープ注記参照」]
**Scope:** [クラス、法域、完全 vs ファジー、デザイン検索か否か]

**Swept した隣接ファミリー(ユーザー確認済み):**
- [family 1 — 例: HUB / NEST / LINK / CONNECT / BRIDGE / GATEWAY]
- [family 2 — 例: ALEXA スタイルのアシスタント名]
- [family 3 — 例: HOME / HOUSE / SMART バリアント]
- [family 4 — ルートの音声学的ツイン]

*完全および近接一致のみをチェックするクリアランスは、あなたのマークが取られた
ため競合が採用したマークを見逃します。Sweep されなかったファミリーがあれば
(コネクターなし、時間不足)、サイレントにスキップではなく、完全な professional
調査への次ステップ入力として明示的にリストされます。*

| マーク | ソース | クラス / G&S | オーナー | ステータス | First use | 注記 |
|---|---|---|---|---|---|---|
| [完全] | [登録番号 / 引用 / URL] | [クラスリスト] | [レコードのオーナー] | [reg/pending/abandoned/cancelled] | [日付または「利用不能」] | [なぜ重要 — 完全一致 / 隣接ファミリー] |

*検索が実行されなかった場合:* **No database search was run.** This triage did
not hit TESS, Solve Intelligence, Descrybe, CourtListener, state registries,
Madrid/WIPO, or any common law / unregistered-mark sources. A knockout or full
search across those databases is required before any conclusion about availability.

## 混同要因 — 弁護士レビュー用フラグ

適用されたテスト下の各要因について、何が両方向に切れるかを 1 行フラグ。

| 要因 | フラグ | 方向 |
|---|---|---|
| マークの類似性(視覚 / 音 / 意味 / 商業的印象) | [note] | [コンフリクトへ / コンフリクトに対して / mixed] |
| 商品または役務の類似性 | [note] | [方向] |
| 取引チャネル | [note] | [方向] |
| カスタマーの洗練度 | [note] | [方向] |
| 先行マークの強さ | [note] | [方向] |
| 意図 | [note] | [方向] |
| 実際の混同 | [note または「証拠未表面化」] | [方向] |
| 拡張のおそれ / bridge-the-gap | [note] | [方向] |

**混同についての結論:** *本スキルは結論しない.* どれか:
- 「類似マーク発見; 採用前に弁護士の混同評価が必要」。
- 「検索したデータベースで類似マーク発見なし; 採用前に完全クリアランスが必要」。
- 「要因が両方向に切れる; 弁護士判断が必要」。

## 推奨される次のステップ

- [具体的な次ステップ 1 — 例: 「採用前に USPTO、州登記所、コモンロー・ソース、EUIPO、UK IPO を横断する完全な professional 調査」]
- [具体的な次ステップ 2 — 例: 「進めることが意図なら、クラス 25 の `APEXLEAF` マークのデザイン・アラウンド・レビュー」]
- [具体的な次ステップ 3 — 例: 「マークをリフレーム — 現在の形式は記述的で secondary meaning が必要」]
- [`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` 別ルーティング —
  プラクティスプロファイルで名指された商標 OC または社内 IP カウンセル]

## 引用検証

本メモの各判例、登録番号、制定法、データベース結果は、依拠する前に権威ソース
に対して検証する必要があります。登録番号、クラス指定、first-use 日が最も一般的な
エラー部位。開けない結果を引用しないこと。
```

---

## 非弁護士ゲート

出力を発行する前に、`## Who's using this` を読む。Role が非弁護士なら:

> この出力は research トリアージで、法的助言ではありません。このトリアージ
> 単独に基づきマークを採用、出願、または投資することは、この調査を「通過」
> したマークで侵害を訴えられることを含む法的帰結を伴います。あなたが動く前に
> 登録商標弁護士が評価する必要があります。
>
> 弁護士に持参するブリーフ — 会話の時間を削ります:
>
> [1 ページ・サマリーを生成: 提案マーク、商品/役務とクラス、knockout 問題
> (あれば)、表面化した類似マーク(あれば)、何が検索されたか・されなかったか、
> 弁護士に聞く 3 つの質問]
>
> あなたの法域で認可された弁護士、ソリシター、バリスター、他の認可された
> 法専門家を見つけるなら: あなたの専門職規制当局のリファラル・サービスが
> 最速の出発点(米国の州弁護士会、英国イングランド&ウェールズの SRA/Bar Standards
> Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの
> 法曹会、またはあなたの法域の同等機関)。INTA(International Trademark
> Association)は登録商標実務家のメンバー・ディレクトリを維持しています。

ブリーフと一緒に完全なトリアージ・メモを配信。分析を withhold しないこと。

---

## 出力場所

案件ワークスペースが有効でアクティブ案件があるなら、出力を
`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/outputs/clearance-<mark-slug>-YYYY-MM-DD.md`
に書く。さもなくば
`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/outputs/clearance-<mark-slug>-YYYY-MM-DD.md`
に書き、ユーザーにパスを表面化。

案件がアクティブなら、案件の `history.md` に 1 行エントリーを追加。

---

## next-steps decision tree で締めくくる

CLAUDE.md `## Outputs` の next-steps decision tree で締めくくる。本スキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト分岐(draft the X、escalate、get more facts、watch and wait、something else)は出発点で、ロックインではない。ツリーが出力で、弁護士が選びます。

## このスキルが行わないこと

- **マークが clear と結論する.** 決して。プラグイン中最大の声のガードレール。
- **TESS 検索、州登記所検索、コモンロー検索、国際検索、ウォッチサービス・チェック、デザインマーク検索の代替.**
- **商標出願を提出する.** 出願は弁護士のタスク; 本スキルは出願判断を informs。
- **TDRA 下の dilution、商標 dilution、または有名マーク・クレームを予備フラグを超えて評価する.** TDRA 下の dilution は本スキルが試みない有名性分析を要する。
- **外国ローカル法バー(例: 日本の音声学的類似性基準、EU の translation-of-foreign-equivalents)を、外国法域がスコープにあるときに外国分析が必要とフラグする以上に取り扱う.**
- **出力をカスタマー、相手方、またはプレスに引用する.** これは内部研究。上のヘッダーが適用されるなら特権付き。

---

## トーン

クリスプ、コンクリート、スコープについて honest。出力を読む弁護士は、10 秒でトリアージが何を発見し、何を発見せず、誰かがマークを採用する前に何が起こる必要があるかを知るべき。Hedging プロースなし。冒頭のガードレールと混同についての「本スキルは結論しない」行がスコープ作業を行う。
