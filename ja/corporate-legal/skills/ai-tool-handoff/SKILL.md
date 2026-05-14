---
name: ai-tool-handoff
description: >
  Luminance、Kira などの一括レビューツールが使用されている場面を検知し、
  大量条項抽出をそれらツールに引き渡し、`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の信頼レベルに従ってその出力を QA します。
  Use when user says "send to Luminance", "bulk review", "AI extraction",
  or when diligence-issue-extraction hits a high-volume category.
  日本語トリガー: 「Luminance に渡す」「Kira に渡す」「一括レビュー」「AI 抽出」「ツールに任せる」、
  diligence-issue-extraction が大量カテゴリに当たった場合。
---

# AI Tool Handoff

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

Luminance と Kira は 1 つのことが得意です:500 件の契約を読み、change-of-control(支配権変動)条項をすべて見つけること。判断 — 特定の CoC 条項がこのディール構造で実際に発動するか決めること — は得意ではありません。

本スキルは、大量抽出を適切なツールに引き渡し、戻ってきたものに対して QA レイヤーを実行します。

**引き渡し前に:** まず `tabular-review`(`/corporate-legal-ja:tabular-review`)を試してください。ユーザー環境が処理可能なもの — 数百文書、定義された列スキーマ — は、ネイティブのタブラーレビューのほうがセットアップが速く、文書あたりコストがなく、成果物がローカルに保たれます。Luminance / Kira への引き渡しは、コーパスが本当に大きすぎる場合、チームがすでにライセンスとワークフローを持っている場合、または案件が検証済みの provenance chain(出自連鎖)を持つツールを要求する場合に行ってください。

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → AI-assisted review:
- 使用中のツール(Luminance / Kira / none)
- 何に使うか(どの条項タイプ)
- 信頼レベル(use as-is / spot-check / full re-review)
- 引き渡しプロセス(誰がロード、誰が QA)

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` が AI ツールなしと言う場合 → 本スキルは no-op です。すべては diligence-issue-extraction を直接通します。

## いつ引き渡すか

次のすべてを満たす場合に引き渡し:
- カテゴリに 50 文書超(それ未満なら直接読むほうが速い)
- 抽出ターゲットがツールが得意な条項タイプ(CoC、譲渡、独占、MFN、解除、自動更新)
- 文書が比較的均一(類似ペーパーの顧客契約ばかり — 契約・レター・取締役会議事録のミックスではない)

引き渡さない:
- bespoke または重く交渉された文書
- サイドレターおよび修正契約(コンテキスト依存、ツールは本契約との相互作用を見落とす)
- 「この条項が存在するか」ではなく「これがディールにとって何を意味するか」が問いになっているもの

## 引き渡し

### Step 1: バッチを準備

- バッチ対象の文書を特定(VDR インベントリから)
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` に従い抽出ターゲット(どの条項タイプ)を指定
- 重要性基準を記録し、ツール出力をフィルタできるようにする

### Step 2: ロード(またはローダーへ指示)

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` に従う — 誰がロードするか。あなたなら、ロード指示を生成。他者なら、依頼を生成:

```markdown
## [Tool] Load Request — [Deal code] — [Category]

**Documents:** [N] docs from VDR folder [path]
**Load to:** [Tool workspace/matter]
**Extraction targets:**
- Change of control / assignment
- Exclusivity
- [etc. per `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md`]

**Filter output:** Flag only where extraction target is present — no need for "no CoC clause found" for every doc.

**Return by:** [date]
```

### Step 3: 出力を QA

ツールが結果を返したら、信頼レベルを適用:

**"Use as-is":** DD 所見に直接取り込み。(これは `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` がそう言う場合のみ — 稀。)

**"Spot-check X%":** フラグされた文書の X% をランダム抽出。それぞれについて実際の条項を読み、ツール抽出と比較。エラー率が低ければバッチを受け入れる。エラーがあれば、サンプルを広げる。

**"Full human review of flagged":** ツールが宇宙を絞る(500 docs → CoC 条項を持つ 80 docs)。人間が 80 全件を読む。420 件のクリーンなものを読む時間をツールが節約。

### Step 4: 判断レイヤー

ツールは条項を見つけました。今度は判断を適用:

フラグされた各 CoC 条項について、これは本当にこのディールで発動するか?
- 株式譲渡 vs 資産譲渡 vs 合併 — トリガーが異なる
- 契約上「Change of control」がどう定義されるか — 議決権過半数? 取締役会の支配? 何か他のもの?
- このタイプの取引にカーブアウト(例外規定)はあるか?

これがツールにはできない部分。出力は DD 所見にハウスフォーマットで送られる。

## 出力

> 下記の QA サマリーは特権・秘密(またはその両方)の VDR 文書から派生しています。ソースの特権・秘密ステータスを継承します — 特権の輪を超えた配布は特権放棄を引き起こす可能性があります。案件の特権ファイルとともに保管してください。

```markdown
## AI Tool Handoff Summary — [Category]

**Tool:** [Luminance / Kira]
**Documents processed:** [N]
**Extraction targets:** [clause types]

### QA

**Trust level:** [per `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md`]
**Sample size:** [N] docs spot-checked
**Error rate:** [X]% — [Accepted / Widened sample / Full re-review triggered]

### Results

| Clause type | Docs flagged | After judgment layer | Material |
|---|---|---|---|
| Change of control | [N] | [N actually triggered by deal structure] | [N above threshold] |
| Assignment | [N] | [N] | [N] |

**→ [N] findings added to diligence issues**
**→ [N] consents added to closing checklist**
```

## next-steps デシジョンツリーで締めくくる

CLAUDE.md の `## Outputs` に従い、next-steps デシジョンツリーで終わってください。選択肢は本スキルが今生成したものに合わせてカスタマイズしてください — デフォルトの 5 分岐(draft the X、escalate、get more facts、watch and wait、something else)は出発点であって、固定ではありません。ツリーが出力です。弁護士が選びます。

## 本スキルがしないこと

- Luminance や Kira を実行しません — 引き渡しと QA を管理します。実際の抽出は人間(またはツールの UI)が実行します。
- ツール出力を自身の判断で完全に置き換えません — `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` が 10% スポットチェックと言うなら 10% をチェックします、100% ではなく。
- 信頼レベルを決定しません — それは `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` にあり、チームのツール経験に基づきコールドスタートで設定されます。
