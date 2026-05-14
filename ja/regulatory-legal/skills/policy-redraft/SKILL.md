---
name: policy-redraft
description: /regulatory-legal-ja:gaps または /regulatory-legal-ja:policy-diff で発見されたギャップを解消するマークアップ済みポリシー修正案を produce します。内部レビュー用の初稿 — 承認済みポリシードキュメントへの直接適用ではない。ユーザーが "redraft the policy", "draft the policy fix", "mark up the policy" と言ったとき、または gap-surfacer がドラフト用にギャップを渡したときに使用。日本語トリガー: ポリシー修正案、ポリシー再起草、マークアップ、ポリシー改訂案。
argument-hint: "[GAP-ID or gap description]"
---

# /policy-redraft

1. `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` をロード → ポリシーライブラリインデックス + プラクティスプロファイル。
2. 下記のワークフローを使う。
3. 入力を集める: ギャップ(`/regulatory-legal-ja:gaps` 出力から、または直接記述された)、現在の承認済みポリシーテキスト、ルールテキスト。
4. ルールが current かを検証(policy-diff の rule-status チェックに従う)。検証できない場合、`⚠️ RULE STATUS UNVERIFIED` バナーを出す。
5. 影響を受けるポリシーセクションのマークアップ済み修正案を produce — smallest-possible edit、`[verify]` タグを carry through、各変更の WHY を説明するインラインコメント付き。
6. Policy Redraft Memo を出力。`[policy-name]-proposed-redraft-[YYYY-MM-DD].md` という名前の新ファイルに書く — ソースポリシードキュメントに決して書かない。
7. トラッカーでギャップを close しない。ギャップは修正案が適用 AND 承認されたとき close される — それはポリシーオーナーのアクション。

---

> このスキルは **proposal** を produce し、edit ではない。明確にマークされたドラフト filename の新ファイルに書く。ソースポリシードキュメントを決して上書きせず、トラッカーでギャップを決して close しない — ギャップはポリシーオーナーが修正案を適用 AND 承認したときに close される。

## Matter context

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(in-house ユーザーのデフォルト)の場合、この段落の残りをスキップ — スキルはプラクティスレベルコンテキストを使い、matter machinery は不可視。enabled でアクティブな案件がない場合、聞く: "Which matter is this for? Run `/regulatory-legal-ja:matter-workspace switch <slug>` or say `practice-level`." アクティブな案件の `matter.md` を案件固有コンテキストとオーバーライドのためにロード。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/<matter-slug>/` に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まない。

---

## 目的

gap-surfacer がギャップを見つける。policy-diff が何を変えるべきかを命名する。このスキルは次のステップで、影響を受けるポリシーセクションのマークアップ済み修正案 — 小さく、具体的、フラグ付き — を、ポリシーオーナーレビュー用の初稿として produce する。

## ハードガードレール — 最初にこれらを読む

これらは load-bearing なルール。いずれかが違反されるなら、停止して尋ねる。

1. **これは PROPOSAL で、edit ではない。** ソースポリシードキュメントに決して直接書かない。出力は `[policy-name]-proposed-redraft-[YYYY-MM-DD].md` の新ファイル、または案件ワークスペースに行く。`[policy-name].md` ではない。
2. **トラッカーでギャップを決して close しない。** ギャップは修正案が適用され承認されたときに close される — それはポリシーオーナーのアクションであって、あなたのではない。ユーザーが「redraft したからギャップを今 close して」と言ったら、断る: "I produce the proposal. The gap closes when you've reviewed, applied, and approved the change. When that's done, tell me and I'll update the tracker."
3. **「Apply this for me」はスコープ外。** ユーザーが修正案をソースポリシーに適用するよう求めたら: "I don't apply policy changes — that's the policy owner's action after review and approval. I produce the proposal. When it's been reviewed and approved, tell me and I'll update the gap tracker."
4. **redraft 前にポリシーバージョンを確認。** ユーザーがファイルを与えたら聞く: "Is this the approved version of the policy, and is it the latest? A redraft against an outdated policy creates divergence." テキストを貼った場合は、trust but flag をレビューアー注記に。
5. **Smallest-possible edit。** 文の前に単語をストライク、段落の前に文をストライク、セクションの前に段落をストライク。ギャップに影響を受けるセクションのみ touch。ポリシーを restyle しない。
6. **`[verify]` タグを carry through。** モデル知識または未検証ソースから来た任意の施行日、閾値、citation、または requirement は、メモだけでなく redraft 自体でタグ付け。

## Step 1: 入力を集める

3 つの入力が必要。いずれかが欠けていれば、聞く — 推測しない。

### 1a. ギャップ

次のいずれか:
- ギャップトラッカーからの `GAP-ID` — `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/gap-tracker.yaml`(または matter レベル相当)からエントリをロード。
- ユーザーメッセージで記述されたギャップ — requirement、規制、影響を受けるポリシーをキャプチャ。
- `/regulatory-legal-ja:policy-diff` 出力から貼り付けられた diff サマリ。

### 1b. 現在のポリシーテキスト

次のいずれか:
- ファイルパス — 読んで、聞く: "Is this the approved version of the policy, and is it the latest? A redraft against an outdated policy creates divergence." 回答をレビューアー注記に注記。
- 貼り付けテキスト — trust but flag をレビューアー注記に: "Policy text was pasted directly; I assumed it was the current approved version. Confirm before applying."
- どちらでもない — 求める。ギャップトラッカーやウェブ検索からポリシーテキストを推測しない。

### 1c. ルールテキスト

次のいずれか:
- diff 出力(既にルールが抽出されタグ付けされている)。
- 取得された規制 — provenance タグでソースを注記。
- ユーザーから貼られたルールテキスト — `[user provided]` でタグ付け。

ルールテキストが部分的または曖昧な場合、CLAUDE.md の **no silent supplement** ルールを適用: ユーザーに選択肢を提供(フルテキスト貼り付け、primary source 指示、web-search-with-verify-tag、または停止)、待つ。

## Step 2: ルールが current か検証

`policy-diff` と同じ rule-status チェックパターンを使う。ルールが in force でない可能性のレッドフラグ:

- 適用/コンプライアンス日が 30 日以上過ぎており遅延されていないという確認なし。
- ルールが 12 ヶ月以上前。
- ルールが政治的に争われている final rule(主要な規則制定は頻繁に異議申立てされる)。

レッドフラグを見たら、(調査 MCP、有効ならウェブ検索、または Federal Register docket 経由で)以下をチェック: delays、stays、injunctions、rescission proposals、vacatur、amendments。ルールが in force と検証できたら、進める。検証できない場合:

> `⚠️ RULE STATUS UNVERIFIED — I could not confirm this rule is currently in force. Final rules are frequently stayed, enjoined, delayed, or rescinded after publication. Do not apply this redraft until you confirm the rule's status at the Federal Register docket or with outside counsel.`

そのバナーを work-product header の上に出す。redraft 内のすべての effective/compliance 日を `[effective date per published rule — status unverified]` でタグ付け。

## Step 3: 修正案を produce

影響を受けるポリシーセクションのマークアップ済みバージョン。

### Redline granularity — smallest possible edit

- 文の前に単語をストライク。
- 段落の前に文をストライク。
- セクションの前に段落をストライク。
- ギャップに影響を受けるセクションのみ touch。ポリシー全体を restyle しない。

### 規約

- ストライクされたテキスト: `~~struck text~~`
- 挿入されたテキスト: **inserted text**
- 各変更は WHY を説明するインラインコメントを伴う — ルール、cite、解消されるギャップ:

  > `[Change: added biometric identifiers to the PII definition per COPPA 2025 amendments, 16 CFR 312.2 (effective Apr 22 2026) [verify]]`

- モデル知識または未検証ソースから来た任意の施行日、閾値、citation、または requirement は、変更サマリだけでなくインラインで `[verify]` タグを付与。
- diff から source tag を carry through: `[Federal Register]`、`[web search — verify]`、`[model knowledge — verify]`、`[user provided]`。diff から redraft への移動でタグを strip しない。

### スコープの規律

ポリシーのセクションがギャップに影響を受けないなら、放っておく。ギャップ外のセクションに touch する redraft は、AI が頼まれていないものに意見を述べたように見え、レビューを難しくする。

redraft 中に 2 つ目のギャップを見つけた場合 — 元のギャップにはなかったが明らかにルールと不一致な provision — silently fix しない。レビューアー注記でフラグ: "While redrafting for [GAP-ID], I noticed [other provision] appears to have a related issue with [requirement]. Not included in this redraft. Consider a follow-on gap."

## Step 4: 出力 — Policy Redraft Memo

```markdown
[WORK-PRODUCT HEADER — プラグイン config ## Outputs に従う — role により異なる;`## Who's using this` 参照]

> **⚠️ Reviewer note**
> - **Sources:** [Research connector: CourtListener ✓ verified | not connected — cites from training knowledge, verify before relying]
> - **Read:** [レビューされたポリシーセクション;読まれなかったもの]
> - **Flagged for your judgment:** [N items marked `[review]` inline | none]
> - **Currency:** [rule status verified against [source], [date] | unverified — see banner above]
> - **Before relying:** confirm this is the current approved version of the policy; verify rule status and effective date; get the policy owner's review; follow your policy-change approval process; update the gap tracker only when applied and approved.

## Policy Redraft: [Policy name]

**Gap:** [GAP-ID or short description]
**Regulation:** [name, citation, effective date]
**Policy:** [name, last-updated date]
**Status:** PROPOSAL — まだレビュー・承認されていない

### ボトムライン

[1 文: ギャップは何か。1 文: redraft が何をするか。1 文: レビュー対象。]

### マークアップ済みポリシーセクション

[redlined テキスト、インライン `[Change: ...]` コメント付き。影響を受けるセクションのみ。]

### 変更サマリ

| # | Provision | Current | Proposed | Why | Verify |
|---|---|---|---|---|---|
| 1 | §2.1 PII definition | "…names, addresses, SSNs…" | "…names, addresses, SSNs, biometric identifiers…" | COPPA 2025 amendments expand PII to cover biometrics | [Federal Register] |
| 2 | §4.3 Retention period | "30 days" | "14 days" | New rule imposes 14-day cap | `[verify — model knowledge]` |

### 適用前 — チェックリスト

- [ ] 再起草対象のポリシーが current approved version であることを確認。
- [ ] ルール status と施行日を検証(Federal Register docket、または外部弁護士)。
- [ ] ポリシーオーナーのレビューを取得。
- [ ] ポリシー変更承認プロセスに従う。
- [ ] 適用 AND 承認時にギャップトラッカーを更新 — それより前にではない。

---

**What next? Pick one and I'll help you build it out:**

1. **Apply and get sign-off** — レビューし、ポリシーオーナーに回覧、承認プロセスを通す。承認されたら、私に伝えれば私がギャップを closed としてマーク。
2. **[X] についてより多くの情報を取得** — 特定の変更がより多くの根拠を必要とする場合(cite 検証、閾値チェック、法域問題の解決)、どれかを伝えれば私が掘り下げる。
3. **[owner / GC] にエスカレート** — redraft がポリシーオーナーの authority を超えるものを生じさせる場合、事実、提案される変更、必要な決定を含む短いエスカレーションを起草。
4. **Watch and wait** — ルールの status が不確実、またはポリシーオーナーが利用できない場合、ギャップトラッカーに revisit 注記を追加。
5. **Something else** — それで何をしたいか教えて。
```

## ファイル名

出力ファイル名はドラフトであることを明確にする。使う:

`[policy-name]-proposed-redraft-[YYYY-MM-DD].md`

`[policy-name].md` ではない。`[policy-name]-v2.md` ではない。"proposed-redraft" の単語と日付は load-bearing — ドラフトが current バージョンと誤認されるのを防ぐ。

案件ワークスペースがアクティブならそこに書く;そうでなければ現在の作業ディレクトリかユーザーが指定する場所。ポリシーライブラリソースディレクトリには書かない。

## Config-dependent fallbacks

このスキルはポリシーライブラリインデックスとオーナーを `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` から読む。必要な値が空または `[PLACEHOLDER]` のまま:

- **Policy owner 不在:** それでも redraft を produce。レビューアー注記に注記: "No policy owner is set for [policy] in `## Policy library`. Assign one with `/regulatory-legal-ja:cold-start-interview --redo` so the approval path is routable."
- **Policy library 空でギャップが特定のポリシーを命名していない:** 停止して聞く: "I need the current policy text to redraft. Paste the text of the affected policy, or point me at the file."

値が populated されている場合、config について何も言わない。

## 他のスキルとの相互作用

- **上流入力** は `policy-diff`(requirement 別ギャップ分析)と `gap-surfacer`(トラッカー)から来る。それらの source tag と `[verify]` フラグを carry through。
- **ギャップトラッカー状態:** このスキルはトラッカーを変更しない。ギャップを closed としてマークしない、in-progress としてマークしない、`notified` に touch しない。redraft が存在することのペーパートレイルが欲しければ、ポリシーオーナーまたはユーザーが redraft が適用・承認されたときに resolution note でギャップエントリを更新できる — `/regulatory-legal-ja:gaps --close` を参照。
- **Severity floor:** 上流ギャップが 🔴 または 🟠 なら、メモのボトムラインはその severity を carry。Silent demotion はレビューする弁護士が見えない矛盾。CLAUDE.md `## Cross-skill severity floor` 参照。

## next-steps デシジョンツリーで締める

上記の出力テンプレートに含まれる。redraft が実際に produce したものに合わせて選択肢をカスタマイズ — ルール status が未検証なら option 2(より多くの情報)が上がる;ポリシーオーナーがセットされていなければ option 3(エスカレート)が具体的になる。

## このスキルがやらないこと

- redraft をソースポリシーに適用。それはポリシーオーナーのアクション。
- トラッカーでギャップを close。ギャップは redraft が適用・承認されたとき close される。
- ポリシー全体を書き直す。ギャップを close する smallest-possible edit。
- 複数ポリシー redraft を produce。1 ギャップ、1 ポリシー、1 メモ。multi-policy fan-out 用の `:package` コマンドは将来のスキル。
- "apply" ワークフローを produce。承認ゲート付きの `:apply` コマンドは将来のスキル。
