---
name: reg-feed-watcher
description: 規制フィードを今すぐチェックし、前回チェック以降の新着を、重要性閾値でフィルタしてレポート。ユーザーが "check the feeds", "what's new", "regulatory update" と言ったとき、スケジュールド・エージェントから動作するとき、または分類と diff のために規制動向を手動貼り付けするときに使用。Use when the user says "check the feeds", "what's new", "regulatory update". 日本語トリガー: フィードチェック、規制最新情報、新着レビュー、規制ダイジェスト。
argument-hint: "[optional: --since DATE]"
---

# /reg-feed-watcher

1. `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` をロード → ウォッチリスト、重要性閾値、フィード設定。
2. 下記のワークフローを使う。
3. 各フィードを pull。重要性でフィルタ。
4. 出力: 何が新しいか、重要性 tier 別に分類。

---

## 目的

フィードを pull する。重要性でフィルタする。残ったものを出力する。フィルターが価値の本体 — 未フィルタフィードはノイズ。

## コンテキストロード

`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` → ウォッチリスト、重要性閾値、フィード設定、ダイジェスト出力パス(セットされていれば)。

`references/source-catalog.md`(このスキルのディレクトリ内) → US 連邦、US 州、EU/UK、国際、二次/aggregator カテゴリーをまたぐ RSS/JSON/HTML ソースの整理済みカタログ。新ソースを設定するとき、またはユーザーのウォッチリストにカバレッジギャップがあるとき(Step 0 参照)に使う。

## ワークフロー

### Step 0: カバレッジチェック(pull 前)

pull を実行する前に、CLAUDE.md のウォッチリスト + フィード設定を `references/source-catalog.md` と比較:

- どのカテゴリー(US 連邦 / US 州 / EU-UK / 国際)をユーザーがウォッチリスト的に気にしているか?
- それらのカテゴリーのうち、ソースがゼロまたは非常に少ないものはどれか?

明らかなギャップ — 例: ユーザーがウォッチリストで「EU regulators」を見るが、設定されているフィードが `edpb.europa.eu` のみで、ICO(英国情報コミッショナー)、CNIL(フランスデータ保護当局)、DPC Ireland(アイルランドデータ保護委員会)が欠けている — があれば、ダイジェストのトップで一度表面化:

> **Coverage gap noticed:** Your watchlist includes [category], but only [N] feeds are configured. The source catalog lists [X] options in this category (e.g., [top 2-3 names]). Want me to suggest additions? Run `/regulatory-legal-ja:cold-start-interview --redo` to update, or edit `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` directly.

同じギャップを繰り返し nag しない — ユーザーが明示的に「state AGs は今はスキップ」と言ったら、尊重する。state を CLAUDE.md に注記して定着させる。

### Step 1: Pull

設定されたすべてのフィード tier から pull。すべてのインストールは Tier 1 を持つ。Tier 2 と 3 は additive — 設定されていれば使い、なければスキップ。

**Tier 1 — 無料フィード(常時アクティブ)**

ウォッチリストの各規制機関について:

- **Federal Register API**(`https://www.federalregister.gov/api/v1/documents`)
  — agency slug、日付範囲(前回チェック以降)、ドキュメントタイプでクエリ。構造化データを返す: ドキュメントタイプ、タイトル、要旨、施行日、コメント締切(NPRM の場合)、citation。すべての US 連邦機関をカバー。
- **規制機関直接 RSS** — ~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md のフィード設定にある任意の RSS URL を fetch とパース(SEC、FTC、CFPB、州機関、EU DPA など)。

共通ウォッチリスト規制機関の Agency slug 参考:
| 規制機関 | API slug |
|---|---|
| FTC | federal-trade-commission |
| SEC | securities-and-exchange-commission |
| CFPB | consumer-financial-protection-bureau |
| CPPA (CA) | RSS only — cppa.ca.gov/feed |
| DOL | labor-department |
| HHS | health-and-human-services-department |
| FCC | federal-communications-commission |

このリストにない規制機関は、federalregister.gov/agencies で正しい slug を確認するか、直接 RSS にフォールバック。

**Tier 2 — 有料フィード(設定されている場合)**

- **TR Regulatory Intelligence MCP:** 前回チェック日以降の更新をクエリ、ウォッチリスト規制機関でフィルタ。
- **CourtListener MCP:** 同じ。

tier をまたいで重複排除 — 同じドキュメントが複数のソースに現れる可能性。enriched 出力には最も豊富なソースを優先。

**No silent supplement.** フィード pull がウォッチリストの規制機関について少ないまたは結果なしを返した場合、見つかったものを報告して停止。質問せずにウェブ検索やモデル知識からギャップを埋めない。言う: "The feed check returned [N] items from [regulators hit]. Coverage appears thin for [regulator / topic]. Options: (1) broaden the date window, (2) try a different feed or MCP, (3) search the web — results will be tagged `[web search — verify]` and should be checked against the issuing authority's website before relying, or (4) stop here. Which would you like?" 弁護士が低信頼度ソースを受け入れるか決める;Claude が代わりに決めない。

**Source attribution.** すべての citation と規制項目に出所をタグ付け: コネクター経由で取得した項目は `[Federal Register]`、`[<regulator> RSS]`、`[TR]`、`[CourtListener]`、または具体的な MCP ツール名;ウェブ検索からの項目は `[web search — verify]`;モデル訓練データから浮上した項目は `[model knowledge — verify]`;手動貼り付け項目は `[user provided]`。`verify` タグの項目は tool-retrieved 項目より捏造リスクが高く、先にチェックすべき。タグを strip または collapse しない — それらはユーザーがどの citation を検証すべきかを最も速く知るシグナル。

**二次ソース。** 一部のカタログエントリ(IAPP(国際プライバシー専門家協会)、FPF(Future of Privacy Forum)、Hogan Lovells、Covington、Lexology、JD Supra、Artificial Lawyer、LawSites、同様のコメンテーター/aggregator)は primary regulatory action を報じるが、primary source ではない。これらのフィードから pull された項目には feed-name タグに加えて `[secondary source]` をタグ付け — 例: `[IAPP Daily Dashboard] [secondary source]`。ダイジェストで、二次ソース項目が規制機関のアクションを記述するとき、注記を追加: "→ Trace to primary: [link to regulator site if known, otherwise 'find on <regulator>.gov before relying']." 二次ソース項目を自身の力で「Always material」と分類しない — primary source が見つかるまで tier を 1 つ下げる。

**Tier 3 — Manual entry**

ユーザーがスケジュールドフィードチェックから呼び出すのではなく規制テキストや要約を貼り付けた場合: 貼り付けコンテンツを単一の項目として扱い、分類のために Step 2 にスキップ、ソースを "manual entry" として記録。フィード pull 不要。このパスはサブスクリプション状態に関わらず動作する。

pull 後にチェックタイムスタンプを記録。次のスケジュールド実行はここから前方に pull する。

### Step 2: 分類

各項目は `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` に従い重要性 tier を得る:

| Item type | 閾値との突合 |
|---|---|
| Final rule | 通常「always material」 |
| Proposed rule / NPRM | 通常「review-worthy」 — そしてコメント締切を常にログ |
| ANPR (Advance Notice of Proposed Rulemaking / 規則案前告示) | **戦略** には review-worthy、コンプライアンスにはまだ — 課された要件はまだないが、方向性を示し real なコメント締切を持つ。コメント締切をログ。`/regulatory-legal-ja:policy-diff` には pre-positioning 分析としてのみルーティング、gap-closure diff としてではない。 |
| RFI (Request for Information / 情報提供要請) | ANPR と同じ — pre-rule、コンプライアンス義務なし、ただしコメント締切は real で方向性シグナリングが価値。 |
| Enforcement action | セクター match → material;related-practice match → review-worthy;いずれでもない → FYI またはスキップ |
| Guidance | review-worthy |
| Speech / blog / statement | 閾値に従い FYI またはスキップ |
| Settlement | depends — 新規 theory または大きな金額 → review-worthy;routine → スキップ |

**ANPR / RFI 処理 — 具体的に。** pre-rule 項目は NPRM と 1 つ重要な点で異なる: 法を変えないが、コメント締切を持ち、規制機関の方向性をシグナリングする。それらを別の分岐として扱う:

- ANPR / RFI を「always material」と **分類しない** — コンプライアンス影響はルールが出るまでゼロ。
- 通知の任意の issue 領域がウォッチリストの always-material カテゴリーに触れるなら(例: fintech ウォッチリストでオープンバンキング ANPR)、review-worthy と **分類する**。
- コメント締切を `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/comment-tracker.yaml` に `item_type: ANPR` または `item_type: RFI` でログ **する** ことで、下流トラッカーがコンプライアンスギャップと区別できる。
- ダイジェストエントリに次の行を明示的に **含める**: "Pre-rule. Comment deadline [date]. Route to `/regulatory-legal-ja:policy-diff` only as a pre-positioning analysis (no compliance gap yet)." これは policy-diff スキルに、フル gap-closure diff ではなく compressed pre-positioning 分岐を使うよう priming する。
- **gap-tracker ではなく comment-tracker にルーティング。** Comment-decision 項目はコンプライアンスギャップではない;それらは comment tracker に属し、`gap-surfacer` は `comment-decision` `gap_type` を使う(またはチームがこれらを別途ルーティングするなら、取り込みを断る)。

**NPRM コメント締切処理:**

「skip」を超える tier で分類された各 NPRM について:
- コメント締切を抽出(Federal Register API が構造化データとして返す)
- ~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md でコメントトラッキングが有効なら: `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/comment-tracker.yaml` に追加、status は "undecided"、デフォルトのコメント決定オーナーは ~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md から
- 出力エントリにコメント締切を含める

### Step 3: Enrich

FYI tier を超える各項目について:

- 1 行サマリ(何が変わった)
- ここでなぜ重要かもしれないか(relevance hook — "this is about [practice you do]")
- ソースへのリンク
- 該当する場合の施行日またはコメント締切

FYI 項目は個別に要約しない — カウントするだけ。

## 出力

ダイジェストはデフォルトでチャットに入る。**1 つ以上の項目が FYI を超える場合、共有可能なファイルにも書き込む**。ただしユーザーの CLAUDE.md が `Digest output → chat only` を明示的に設定している場合を除く。

**ファイル出力の動作:**

1. `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` の `Digest output path` を探す。セットされていればそこに書く。デフォルト(unset の場合): `~/regulatory-legal-digests/reg-digest-YYYY-MM-DD.md`。
2. 必要なら親ディレクトリを作成。
3. ダイジェスト全体を Markdown で書く(チャット出力と同じ内容、work-product header、source tag、verify-citations フッター含む)。
4. 今日のパスにファイルが既に存在する場合、上書きではなくタイムスタンプ付きサブヘッダーで新セクションを追加 — 同日に複数回実行されることがある(朝のダイジェスト、アドホックチェック)。
5. 書き込み後、ユーザーに伝える: "Digest written to `<path>`. Share as-is, or convert to .docx with Pandoc: `pandoc <path> -o <path>.docx`."
6. 書き込みが失敗(permission、ユーザーが作成権限を与えていない欠けたディレクトリ、disk)した場合、chat-only 出力にフォールバックしてそう言う — ファイルリクエストを silently drop しない。

ディスク上のフォーマットはチャットフォーマット(下記)と正確に一致。Markdown は GitHub、Notion、Obsidian、Google Docs(「Import as Markdown」または Pandoc 経由)、ほとんどのメールクライアントで良くレンダリングされる。

```markdown
[WORK-PRODUCT HEADER — プラグイン config ## Outputs に従う — role により異なる;`## Who's using this` 参照]

## 規制フィードチェック — [date]

**Period:** [last check] to [now]
**チェックされたフィード:** [アクティブな tier をリスト — 例: "Federal Register API, FTC RSS, TR"]
**見つかった項目:** [N] 合計

### ボトムライン

[N gaps need action by [date] — top 3: X, Y, Z]

### 🔴 Always material

**[Regulator] — [Title]**
[1 行サマリ]. [Relevance hook]. Effective [date].
[Link]
→ 推奨: [likely affected policy] に対して policy-diff を実行

[各項目について繰り返し]

### 🟡 Review-worthy

**[Regulator] — [Title]**
[1 行]. [Relevance]. [Deadline if any].
[Link]

[NPRMs: コメントトラッキングが有効なら "💬 Comment deadline: [date] — decision pending" を含める]

[繰り返し]

### 📝 FYI

[N] 件 — [タイトル + リンクの expandable リスト、サマリなし]

---

**Last check updated to:** [timestamp]
**Comment tracker:** [N] NPRMs with open comment decisions — run /regulatory-legal-ja:comments to review

---

**Verify citations before relying on them.** ここでの規制 citations は AI 生成され、primary source と照合チェックされていない。上記の任意のルール、ガイダンス、執行措置に基づいて行動する前に、Westlaw、所属事務所の調査プラットフォーム、または発行機関のウェブサイトに対して確認 — accuracy、施行日、現在の status をチェック。AI 生成の規制 citations は時に捏造、誤引用、stale。各項目の source tags(例: `[Federal Register]`、`[web search — verify]`)は citation の出所を示す;`verify` タグは捏造リスクが高く、先にチェックすべき。
```

## Config-dependent fallbacks

このスキルはウォッチリスト、重要性閾値、フィード設定を `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` から読む。必要な値が `[PLACEHOLDER]` のままか空の場合、出力でそう言う — 具体的に、汎用ではなく:

- **ウォッチリスト空:** 停止して言う "The watchlist in your configuration is empty. I can't pull feeds without knowing which regulators to watch. Run `/regulatory-legal-ja:cold-start-interview --redo` or edit `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` and add at least one regulator."
- **重要性閾値空:** デフォルト tier にフォールバックし追加: "This output used the default materiality tiers because your configuration doesn't have custom thresholds set. Tune them with `/regulatory-legal-ja:cold-start-interview --redo` or by editing `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md`."
- **フィード設定空:** Federal Register API のみを実行し追加: "This output used only the free Federal Register API because your configuration doesn't list direct RSS or paid feeds. Add feeds with `/regulatory-legal-ja:cold-start-interview --redo` or by editing `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md`."

関連する値が populated されている場合、config について何も言わない。

FYI を超えるものが何もなければ: "All quiet. [N] FYI items, nothing needing attention."

## Handoff

- **policy-diff へ:** likely なポリシー影響を持つ「always material」項目 → diff の実行を提案。
- **gap-surfacer へ:** diff がギャップを見つけたら → tracked。
- **comment-tracker へ:** 「skip」を超えて分類された任意の NPRM → トラッキング有効ならコメント締切が自動ログ。

## next-steps デシジョンツリーで締める

CLAUDE.md `## Outputs` に従い next-steps デシジョンツリーで終わる。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト分岐(draft the X, escalate, get more facts, watch and wait, something else)は出発点であって lock-in ではない。ツリーが出力本体;弁護士が選ぶ。

## このスキルがやらないこと

- 各項目を full に読む。分類して enrich;フィルターを生き残った項目について深く読むのは別。
- 重要性閾値を変更。フィルターが間違いなら、~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md を編集する。
- TR や CourtListener を要求。無料フィードがベースライン;有料フィードは深さを加える。
