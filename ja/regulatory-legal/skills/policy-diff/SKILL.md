---
name: policy-diff
description: 特定の規制変更を索引化されたポリシーライブラリと突合します。reg が変わり、どのポリシーに触れるか、ギャップは何かを知る必要があるとき、ユーザーが "diff this reg against our policies", "which policy does this affect", "gap analysis" と言ったとき、または reg-feed-watcher が material 項目を渡してきたときに使用。Use when a reg has changed and you need to know which policies it touches and what the gap is. 日本語トリガー: ポリシー差分、規制突合、ギャップ分析、どのポリシーが影響するか。
argument-hint: "[reg name, or paste reg text/summary]"
---

# /policy-diff

1. `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` をロード → ポリシーライブラリインデックス。
2. 下記のワークフローを使う。
3. reg から requirements を抽出。索引化されたポリシーと突合。
4. 出力: requirement ごとのギャップ分析、どのポリシーを更新する必要があるか。

---

## Matter context

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(in-house ユーザーのデフォルト)の場合、この段落の残りをスキップ — スキルはプラクティスレベルコンテキストを使い、matter machinery は不可視。enabled でアクティブな案件がない場合、聞く: "Which matter is this for? Run `/regulatory-legal-ja:matter-workspace switch <slug>` or say `practice-level`." アクティブな案件の `matter.md` を案件固有コンテキストとオーバーライドのためにロード。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/<matter-slug>/` に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まない。

---

## 目的

reg が変わった。あなたはポリシーを持っている。このスキルは、変更がどのポリシーに触れるか、そして「reg が今要求するもの」と「ポリシーが言うこと」のギャップが何かを発見する。

## コンテキストロード

`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` → ポリシーライブラリインデックス(ポリシー、場所、オーナー)。

## Scope integrity(スコープ完全性)

ユーザーが diff からポリシーセクション、requirement、またはカテゴリーを除外するよう求めた場合:

1. やる — ユーザーがスコープのオーナー。
2. ただしフラグを立てる、loudly に、permanently に: "⚠️ SCOPE LIMITATION: Section [X] excluded at user request. This diff does not reflect the full policy. Gaps in the excluded area are NOT identified." ヘッダーの上、すべての下流 artifact に carry する。
3. `gap-surfacer` にフラグを渡す: "This diff was scope-limited. Do not represent it as a complete compliance picture." この diff から派生する任意のギャップトラッカーエントリにスコープ制限バナーをそのまま含める。
4. 除外が何を意味するかを注記: "Excluding vendor management means the diff will show 'no policy addresses vendor management' — which is worse than showing the gap."

undisclosed なスコープ除外の上に構築された compliance artifact は、ディスカバリーで concealment(隠蔽)に見える。フラグが「我々はレビューをスコープした」と「我々は問題を隠した」の違いです。

## ワークフロー

### Step 0: diff 前にルール status を確認

ルールをポリシーと diff する前に、ルールが実際に in force(有効)か確認する。ルールが in force でない可能性のレッドフラグ:

- 適用/コンプライアンス日が 30 日以上過ぎているが、遅延されていないという確認がない
- ルールが 12 ヶ月以上前
- ルールが政治的に争われている final rule(主要な規則制定は頻繁に異議申立てされる)

レッドフラグを見たら、(調査 MCP、有効ならウェブ検索、または Federal Register docket 経由で)以下をチェック: delays、stays、injunctions、rescission proposals、vacatur、amendments。チェックできてルールが in force と確認できたら、進める。検証できない場合(ツール未接続)、ヘッダーの上、任意のコンテンツの前にこのバナーを出す:

> `⚠️ RULE STATUS UNVERIFIED — I could not confirm this rule is currently in force. Final rules are frequently stayed, enjoined, delayed, or rescinded after publication. Do not treat any compliance date below as binding until you confirm the rule's status at the Federal Register docket or with outside counsel.`

出力中のすべての due 日にタグ付け: `[due date per published rule — status unverified]`。

ルール status の不確実性は下流に伝わる。`gap-surfacer` にギャップを渡すとき、項目を `status_verified: false` でマークし、公開日付の強さだけで Overdue バケットに決してルーティングされないようにする。

### Step 1: 新しい requirements を抽出

**No silent supplement.** 規制変更テキストが部分的または曖昧で、より完全なルールが索引化されたソースから入手できない場合、停止して尋ねる。質問せずにウェブ検索やモデル知識からギャップを埋めない。言う: "I have [what you have]. To extract requirements accurately I'd need [what's missing]. Options: (1) paste the full text, (2) point me at the primary source, (3) search the web for the rule — results will be tagged `[web search — verify]` and should be checked against the issuing authority before relying, or (4) stop here. Which would you like?" 弁護士が低信頼度ソースを受け入れるか決める;Claude が代わりに決めない。

**Source attribution.** すべての citation — 規制 citation、cross-references、ポリシー抜粋 — に出所をタグ付け: primary source、ポリシーライブラリ、または MCP から取得したものは `[<regulator or research tool>]`;ウェブ検索からは `[web search — verify]`;モデル訓練データから想起したものは `[model knowledge — verify]`;ユーザーが貼ったものは `[user provided]`。`verify` タグの項目は捏造リスクが高く、先にチェックすべき。出力中でタグを strip または collapse しない。

規制変更を読む。新規または変更された個別 requirement をリスト:

| # | Requirement | 施行 | Citation |
|---|---|---|---|
| 1 | [何を要求] | [date] | [section] |

具体的に。"Enhanced disclosure requirements" は requirement ではない。"Must disclose X in Y format at Z point in the flow" が requirement。

### Step 2: ポリシーに突合

各 requirement について、どの索引化ポリシーが最も近いか?

- Direct hit: ポリシーが明示的にこのトピックをカバー
- Indirect: ポリシーが関連トピックをカバー、これは新しい sub-issue
- No match: このトピックを扱うポリシーなし — ギャップは「ポリシーが存在しない」

### Step 3: Diff

各 direct または indirect hit について、ポリシーを読んで比較:

```markdown
### Requirement [N]: [name]

**New rule requires:** [requirement]

**Our policy ([name], 最終更新 [date]) says:**
> "[relevant excerpt]"

**Gap:** [None — ポリシーは既にこれをカバー | Partial — ポリシーは X を扱うが Y は扱わない | Full — ポリシーは矛盾するか触れない]

**必要な変更:** [具体的に — "update the policy" ではなく "add a paragraph on X"]

**Policy owner:** [from index]
```

### Step 4: No-match ギャップ

ポリシー match のない requirements は別途取り上げる:

```markdown
### New policy needed

Requirement [N]: [requirement]

このトピックをカバーする既存ポリシーなし。選択肢:
- 新ポリシーを起草(提案オーナー: [最も近いトピックのオーナー])
- 既存の [related policy] に新セクションとして追加
- ポリシー不要と判断(one-off compliance、継続的ではない)
```

## 規制入力タイプによる分岐

### Pre-rule branch(ANPR / RFI)

規制入力が ANPR または RFI(課された要件なし)なら、full gap-closure diff を実行 *しない*。代わりに **pre-positioning analysis** を produce:

- final rule が出たらいずれ変更が必要になるポリシーを指名(今日ではない)。
- ANPR の issue 領域が、会社の practice と交差して comment letter に値するかをフラグ。
- コメント deadline と `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` のチームのコメント決定オーナーを注記。
- ANPR には requirement ごとの "no gap" 行を生成 *しない* — diff する requirement がない。将来の exposure と touch するポリシーを命名する 1 段落を produce。

### Negative-finding branch(正しいターゲットではないポリシーに対する final rule / NPRM の diff)

抽出 list の全 requirement が「指名されたポリシーに対してギャップなし」と出る場合、フル requirement 別分析を produce *しない* — 1 つの短い段落に圧縮:

```markdown
## Policy Diff: [Regulation name] — [Policy name]

[REGULATION] は [POLICY NAME] への変更を要求しないように見える。[POLICY NAME]
§[X] は既に [Y] をカバー。この規制が実際に touch するポリシーは
[other-policy-1] と [other-policy-2] — それらに対して `/regulatory-legal-ja:policy-diff` を再実行。

[次のサイクル — 例: "at the next annual policy review"] にレビュー、または
[trigger — 例: "the rule is finalized or amended"] のとき。
```

1 段落、1 推奨、ルーティング注記。requirement ごとに「no gap」findings を繰り返さない — サマリテーブルがそれを処理する。間違ったターゲットポリシーに対する negative finding は、ルーティング問題であって compliance 分析ではない。

### Gap branch(ターゲットポリシーに対して少なくとも 1 つギャップのある final rule / NPRM)

下記の通り完全な requirement 別分析。詳細な diff フォーマットは実際にギャップを見つける diff 用。

## 出力

```markdown
[WORK-PRODUCT HEADER — プラグイン config ## Outputs に従う — role により異なる;`## Who's using this` 参照]

## Policy Diff: [Regulation name]

**Regulation:** [name, link]
**Effective:** [date]
**Requirements extracted:** [N]

### ボトムライン

[N gaps need action by [date] — top 3: X, Y, Z]

### サマリ

| # | Requirement | 影響を受けるポリシー | Gap | Owner |
|---|---|---|---|---|
| 1 | [短く] | [policy name or "none"] | None/Partial/Full | [name] |

### 詳細 diff

[Step 3 の各 requirement ブロック]

### 必要な新ポリシー

[Step 4 から、もしあれば]

### No-gap requirements

[リスト — 既にカバーされていることを知るのに有用]

---

**Verify citations before relying on them.** 上記の規制 citations とポリシー参照は AI 生成され、primary source と照合チェックされていない。任意の requirement に基づいて行動する前に、Westlaw、所属事務所の調査プラットフォーム、または発行機関のウェブサイトに対してルールを確認 — accuracy、施行日、現在の status をチェック。AI 生成の規制 citations は時に捏造、誤引用、stale。各 requirement の source tags(例: `[Federal Register]`、`[web search — verify]`)は citation の出所を示す;`verify` タグは捏造リスクが高く、先にチェックすべき。
```

## Config-dependent fallbacks

このスキルはポリシーライブラリインデックスを `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` から読む。インデックスが空または `[PLACEHOLDER]` のまま:

- **Policy library 空:** デフォルトで全 requirement を "no policy match" としてフラグし、出力に追加: "The policy library in your configuration is empty, so every requirement is flagged as a new-policy gap. If you have policies that address these requirements, add them to the library with `/regulatory-legal-ja:cold-start-interview --redo` or by editing `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md`, then re-run the diff."
- **Matched ポリシーの Owner が不明:** サマリの Owner セルを空白のままにし、追加: "Policy owners aren't set for [list]. Assign them with `/regulatory-legal-ja:cold-start-interview --redo` or by editing the policy library in `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` so gap-surfacer can route."

ライブラリが populated されオーナーがセットされている場合、config について何も言わない。

## Handoff

gap-surfacer へ: すべての Partial または Full ギャップは、オーナーと deadline 付きで追跡される項目になる。

## next-steps デシジョンツリーで締める

CLAUDE.md `## Outputs` に従い next-steps デシジョンツリーで終わる。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト分岐(draft the X, escalate, get more facts, watch and wait, something else)は出発点であって lock-in ではない。ツリーが出力本体;弁護士が選ぶ。

## このスキルがやらないこと

- ポリシー更新を起草。更新が必要なものを特定する;policy-drafting(または人間)が起草する。
- 曖昧な規制テキストを確定的に解釈。reg が 2 通りに読めるなら、そう言い、弁護士用にフラグ。
