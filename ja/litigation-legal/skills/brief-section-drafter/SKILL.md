---
name: brief-section-drafter
description: ハウススタイルでブリーフセクションを起案 — ケースセオリーと一貫し、すべての事実に引用、すべての判例をチェック、すべての議論をセオリーに紐付け。Use when the user says "draft the [section]", "write the statement of facts", "argument section on [issue]", or needs a first draft of a brief section. 日本語トリガー: ブリーフ起案、準備書面起案、事実陳述書、議論セクション
argument-hint: "[section — e.g., 'statement of facts', 'argument II']"
---

# /brief-section-drafter

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → ケースセオリー、ハウススタイル。
2. 以下のワークフローと参照に従う。
3. ハウスフォーマット/トーン/引用スタイルで起案。セオリーと一貫。
4. 出力: ドラフトセクション。事実または引用が検証を要する各場所をフラグ。

---

# Brief Section Drafter

## イングランド&ウェールズの証人陳述書 — PD 57AC

ユーザーの法域がイングランド&ウェールズを含み、Business & Property Courts(または任意の CPR 規律手続)のためのトライアル証人陳述書を求めている場合、PD 57AC が適用されます。陳述書は証人自身の言葉で、議論を含まず、証人が記憶をリフレッシュするのに使った文書を特定し、要求されるコンプライアンス確認と法定代理人の証明書を運ぶ必要があります。

**時系列表、文書セット、またはケースの説明から「証人として」物語を起案することは、PD 57AC が防ぐために設計されたことそのものです。** 裁判所は AI 支援証人陳述書起案を積極的に制裁しています。それを依頼されたら、私は実行しません。

私が実行すること: 証人の実際の記憶を引き出すための質問プロンプトを準備;証人が言うこと(彼らの言葉、私のではない)をキャプチャして整理;見せられた文書のリストを生成;証人が起案した陳述書に対して PD 57AC コンプライアンスチェックリストを実行;solicitor のコンプライアンス証明書を起案。証人の証拠を陳述書に入れるのを助けます。証拠を書きません。

米国デポジション、宣言、宣誓供述書: 異なる規則ですが、同じ規律が適用。証人が書いていない証人の声での宣言は、せいぜい信用問題。

## 目的

良いブリーフセクションはセオリーと一貫し、レコードに引用され、ハウススタイルで書かれ、チェック可能です。このスキルは最初のドラフトを生成 — *draft* を強調。パートナーが編集。

## 書面または口頭?

起案前に尋ねる: 「これは書面提出のためですか、口頭弁論のためですか?」 これらは異なる工芸:

- **書面:** 徹底的。ポイントをカバー、権威を発展、応答を予期。
- **口頭(反駁、結審、弁論):** 戦略的。最も重要な 3-4 ポイントを選ぶ。弱いものは譲歩または無視。最強で先導。法廷は最初の 2 分と最後の 2 分を覚える。口頭弁論で「徹底的すぎる」と unfocused に読まれます。複数問題の提出に応答する場合、どの問題を press しどれを手放すかをユーザーに伝える — それが言葉だけでなく戦略のドラフト。

## レコード忠実性 — 引用とピンポイント

アドボカシー起案でのすべての引用とすべての引用を支配する 2 つのルール。標準的なステートメントはプラグインの `CLAUDE.md` 共有ガードレールに住む;ここに繰り返し、なぜならこのスキルはルールがテストされる最も一般的な場所だからです。

**レコードからの逐語引用は逐語的でなければならない。** 相手方弁護士、証人、裁判所、または任意のレコード文書に帰属する言葉に、正確な箇所が目の前にあり引用先を示せる場合を除き、引用符を付けないこと。ほぼ正しい引用は言い換えより悪い — レコードを歪曲し、提出すれば制裁対象になり、必ず発見されます。誰かが言ったことを性格付けたいが正確な言葉を見つけられないとき:

- **引用符なしで言い換え**、明確に帰属: 「Opposing counsel argued that X `[verify against record — Tr. p. __]`.」
- **プレースホルダをマーク:** `[verify exact quote — record cite pending]`
- **絶対に穴を埋めない。** 作り出した引用は、たとえ 1 単語でも捏造です。レビュアー注記は出力中のすべての `[verify exact quote]` をフラグする必要があります。

引用符付きで一節を引用する前に、出典を開く。記憶や要約から作業しているなら、引用符なし。

**ピンポイント引用は命題全体を支持しなければならない。** 議論が「相手方弁護士は X、Y、Z と言った」で 1 つのピンポイントを引用している場合、そのピンポイントが X **かつ** Y **かつ** Z を支持することを検証。Z だけしか支持しない場合、(a) 引用を分割 — 「said X (Tr. p. 10), Y (Tr. p. 12), and Z (Tr. p. 15)」 — または (b) ピンポイントが実際に支持するものに命題を狭める。クレームの一部しか支持しない引用は、裁判所がストレッチを発見する経路。裁判所の前で弁護士の信用性が崩れる最も一般的な単一の方法です。これは「misgrounded citation」失敗モード: 引用は存在し、箇所も存在しますが、記述通りの命題を支持しません。

## 弱い議論に対する率直さ

法があなたに対して反するなら、そう言う。議論が弱いとき — 権威が他方を切る、事実が支持しない、推論がストレッチ — 揺れる議論を構築してしっかりしているかのように提示しないでください。フラグ:

> "This point is weak — [authority] cuts the other way. Consider whether to press it (here's how you'd frame it), concede and pivot to [stronger point], or drop it. `[review — strategic call]`."

弱い議論をフラグなしで主張することは法廷との弁護士の信用を erode し、率直さ問題を作る(MR 3.1 — 弁護士は法と事実の基礎を持たねばならない)。ドラフトは弁護士をスマートに見せるべきで、悪い立場について自信を持って見せるべきではない。

## 引用抽出カバレッジ

このドラフトが引用チェックされるとき — あなたによって、別のスキルによって、または生成物を通すレビュアーによって — チェックは網羅的でなければならず、選択的ではない:

1. **最初のパス: 抽出。** 文書全体を読み、すべての引用 — 判例、制定法、規制、レコード引用、二次権威 — のリストを構築。カウントを報告: 「Found [N] citations.」
2. **2 回目のパス: チェック。** 各々を出典に対してチェック。サンプリングしない。疲れたときに止まらない。
3. **カバレッジを報告。** 最後に: 「Checked [N] of [M] citations. [K] could not be retrieved — verify manually. [J] confirmed. [I] flagged as potential miscitations. [H] flagged as misgrounded (cite exists but doesn't support the proposition).」
4. **出典テキストが利用できないとき、「could not check」と言い、決して「confirmed」とは言わない。** false positive(出典が読めなかったのに「この引用は問題ない」)は「couldn't check this one」より悪い。
5. **最も困難なエラーは部分支持。** クレームの一部を支持するが全体ではない引用。ブリーフが行う命題を読み、出典が実際に判示することを読み、要素ごとに比較。

## エコー vs 繰り返し

キーフレーミングをエコー;文を持ち上げない。先行する提出物との一貫性は良い — それはケースセオリーを強化し、レコードを一貫させる。しかし、エコーと繰り返しの間に線があります。

- **エコー:** 同じキー用語、中心問題の同じフレーミング、相手方のセオリーの同じ性格付けを使用。
- **しない:** 文全体を持ち上げる、法廷が気づくほど頻繁に区別ある表現を再使用、進めずに同じ議論を逐語的に繰り返す。

開廷の再読のように聞こえる反駁は基盤を失います。ドラフトは議論を進めるべきで、再述すべきではない。

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → ケースセオリー、ハウススタイル(引用フォーマット、構造、トーン、長さ規範)。

**コンフリクトゲート — バイパス不可。** 起案前に、このスキルが呼び出されている案件 slug を `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` でチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace is set up. I won't draft substantive work product on a matter that hasn't been intaken — the conflicts check is the gate."

intake されていない案件で進まないこと。intake はコンフリクトを実行し、`matter.md` / `history.md` をセットアップし、このスキルが読み取る `_log.yaml` 行を書きます。スキップは管理されていない場所に作業を生成し、事務所のコンフリクト規律をバイパス。

## ワークフロー

### Step 1: どのセクション?

| Section | 何をするか | 必要な入力 |
|---|---|---|
| Statement of facts | 我々のフレームで物語を語る、レコードに引用 | 時系列、キー文書、depo 引用 |
| Standard of review | 裁判所が適用するバーを設定 | 手続的ポスチャ |
| Argument | 法的ケースを作る | 問題、権威、事実 |
| Conclusion | 救済を求める | 我々が望むもの |

### Step 2: セオリーチェック

書く前に: このセクションはセオリーのために何を達成する必要があるか?

- 事実陳述: 我々のセオリーが自然な読みになるよう物語をフレーミング。
- 議論: セオリーを支える方法で法を事実に接続。

起案しようとしているセクションがセオリーと矛盾する場合 — 停止。セオリーが間違っているかセクションのアプローチが間違っているか。フラグ、ごまかさない。

### Step 3: ハウススタイルで起案

**フォーラムのローカルルールと裁判官のスタンディングオーダーを長さ、フォーマット、引用、提出要件についてリサーチ;好みに頼らないこと。起案ノートでプライマリソース(ローカルルール番号、スタンディングオーダーセクション)を引用。通貨を確認 — ローカルルールは変わる。**

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` に従って:

- **引用フォーマット:** Bluebook、ALWD、またはローカル — 正確にマッチ。シグナル、ピンサイト、括弧書きはハウスプラクティスに従い、ローカルルールに対して確認。
- **構造:** この事務所はどう議論を整理する? CRAC? トピックセンテンスが先? 議論する見出し vs 説明する見出し?
- **トーン:** 攻撃的(「Defendants' argument is meritless」)または抑制的(「The evidence does not support Defendants' position」)? シードブリーフにマッチ。
- **長さ:** ローカルルール / スタンディングオーダーに従って — 規則がチェック可能なときに「この裁判官が通常望むもの」に頼らない。

### Step 4: すべてを引用

すべての事実 → レコード引用(Bates、depo page:line、exhibit)。
すべての法的命題 → ピンサイト付き判例引用。

**マーカー規律 — 寛大に使用:**
- `[VERIFY: specific factual assertion]` — レコードに対して未確認のもの
- `[UNCERTAIN: specific legal proposition]` — 現行権威に対して未確認のもの
- `[CITE NEEDED: specific cite — fact/rule believed but cite not yet pinned]`

未解決マーカー付きのドラフトは最終ではありません。マーカーが検証ステップを明示。

**No silent supplement.** 設定された法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)へのリサーチクエリが、ドラフトが必要とする権威について少数または結果なしを返す場合、見つかったものを報告して停止。尋ねずに web 検索やモデル知識からギャップを埋めないこと。こう言う: 「The search returned [N] results from [tool]. Coverage appears thin for [issue / holding]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) leave the `[CITE NEEDED]` marker and stop here. Which would you like?」 パートナーが低い自信の出典を受け入れるかを決定;スキルは決定しない。

**Source attribution.** ドラフトの各引用に出処をタグ付け: 法律調査コネクター経由で取得された引用には `[Westlaw]`、`[CourtListener]`、`[Trellis]`、`[Descrybe]`、または MCP ツール名;web 検索引用には `[web search — verify]`;トレーニングデータから思い出された引用には `[model knowledge — verify]`;パートナーまたはシニアアソシエイトが供給した引用には `[user provided]`。`verify` タグの引用は、ツール取得引用より高い捏造リスクを運び、最初にチェックすべき。タグを絶対に剥がしたり折りたたんだりしないこと — それらは、ブリーフが提出される前にどの引用を最初に Shepardize するかについてのレビューする弁護士への最速のシグナル。

### Step 5: 出力

**ブリーフが提出される前(重大な行為 — このスキルは起案するが、ゲートは誰がトリガーするかに関わらず提出ステップで走る):** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Filing a brief has legal consequences — it becomes the record, binds the client on arguments and facts asserted, and a Rule 11 / equivalent certification attaches to signature. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: 起案されたセクション、セオリー紐付け、依拠した権威、未解決の `[VERIFY]` / `[UNCERTAIN]` / `[CITE NEEDED]` マーカー、何が悪くなり得るか(事実誤述、未支持引用、セオリー外議論)、提出前に弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしにドラフトを提出可能として扱わない。起案自体にゲート不要 — 提出には必要。

セクション、ハウススタイルで、マーカーインライン。

序文(ブリーフには含まれない — レビューする弁護士への注記):

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

## Drafting Notes — [Section] — [date]

**Theory tie-in:** [このセクションがケースセオリーをどう支えるか]
**Authorities relied on:** [リスト — すべて Shepardize 必要]
**Record cites to verify:** [N] 件インラインフラグ
**Open questions for the partner:** [ドラフトが仮定し、確認すべきもの]
**Length:** [単語数/ページ vs ハウス規範]

---

**Cite check before filing.** Citations in this draft were generated by an AI model and have not been verified against a primary source. Run every case, statute, and regulation through Westlaw, CourtListener, or your firm's research platform for accuracy, good-law status, and subsequent history. Fabricated or misquoted citations in filed briefs have resulted in Rule 11 sanctions.

**Draft only — not a filing.** Filing this section initiates (or participates in) a proceeding and carries Rule 11 / Rule 3.3 exposure. A licensed attorney reviews, edits, and takes professional responsibility before it goes on the docket. Do not file unreviewed.
```

## 事実陳述の詳細

事実陳述は議論ではなく、選択と順序によるアドボカシー。

- 理由がない限り時系列順
- **事実陳述の各事実はレコードに引用 — ページとライン参照、ドケットエントリ、exhibit。** 「Or conceded」はレコード引用の代替ではない。事実が譲歩または stipulation によって確立されている場合、stipulation 文書または譲歩がなされた審理 transcript を引用。
- 選択によってフレーミング: どの事実が先導し、どれが 1 行を取得し、どれが省略されるか(必要でなく役立たない場合)
- 議論なし。「The contract unambiguously required X」は議論。「The contract stated 'X.'」は事実。

## 議論セクションの詳細

- 事実ではなくルールで先導(通常 — ハウススタイルは異なる可能性)
- セクションごとに 1 議論。本当に 2 議論なら、2 セクション。
- 相手の最強の反論に対処。隠れない — 明白なカウンターを無視するブリーフは裁判官が信頼しないブリーフ。
- 括弧書きはスペースに値する。括弧書きが引用単独で追加するもの以上を加えないなら、カット。

## このスキルがしないこと

- 最終ブリーフを生成。ドラフトを生成。すべての引用が検証必要、すべての議論がパートナーの目を必要。
- 戦略を決定。問題を議論する 2 つの方法があるなら、両方をフラグしてパートナーに選ばせる。
- 何かを提出する。決して。
