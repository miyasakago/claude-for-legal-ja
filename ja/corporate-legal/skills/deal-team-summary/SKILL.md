---
name: deal-team-summary
description: >
  DD 所見をディールチーム向けのブリーフィングに集約します — 読み手に応じた適切な高度で:
  リーダーシップ向けはエグゼクティブ・サマリー、チーム向けは作業サマリー。
  Use when user says "brief the deal team", "what's the state of diligence",
  "summarize findings for [audience]", "deal update", or on the briefing cadence.
  日本語トリガー: 「ディールチームにブリーフ」「DD の進捗」「[読み手] 向けに所見を要約」「ディール更新」、または定期ブリーフィング時。
---

# Deal Team Summary

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

ディールリードは 200 件の所見を読みません。彼らが読むのは:何が重要か、前回ブリーフから何が変わったか、何が判断を要するか。本スキルは DD 出力を読み手に適切なレベルに圧縮します。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → Deal team briefing(ケイデンス、フォーマット、ビジネスが読むもの)
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/deal-context.md` → ディールリード、タイムライン
- diligence-issue-extraction 出力からの現所見

## 読み手の階層

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` に従う — ビジネスが読むもの vs ファイル用のもの。デフォルト階層:

| 読み手 | 受け取る | 受け取らない |
|---|---|---|
| **Board / exec sponsor(取締役会 / エグゼクティブスポンサー)** | 上位 3-5 件の重要論点、価格 / ストラクチャーへの影響、判断項目 | カテゴリ詳細、green 所見、プロセス |
| **Deal lead** | 全 reds、全 yellows、進捗、判断項目、次のステップ | green 所見の詳細 |
| **Working team** | すべて — 全所見、カテゴリ別ステータス、ギャップ | 何も保留しない |

明らかでない場合はどの階層か尋ねる。

## サマリー

### Exec tier

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

> 本ブリーフは特権のある DD 所見を集約し、ソースの特権・秘密ステータスを継承します。特権の輪を超えた配布(より広いビジネスチームへを含む)は特権放棄を引き起こす可能性 — 送信前に配布リストが特権の輪に一致することを確認してください。

# [Deal code] — Diligence Brief — [date]

**Status:** [On track / Issues identified / Material findings]
**Coverage:** [X]% of VDR reviewed

## Material findings

[最大 3-5 件。各 1 段落。それが何か、なぜディールに重要か、何を対応中か。]

## Decisions needed

- [ ] [具体的判断 — 価格調整、補償要求、walk-away トリガー]
  — [decider] — [by when]

## Since last brief

[何が変わったか。新所見、解決した所見、カバレッジ進捗。]
```

### Deal lead tier

上記に加えて:

```markdown
## All open issues by category

### 🔴 Red
[Finding title + one-line — 詳細は full finding にリンク]

### 🟡 Yellow
[same]

## Progress

| Category | Docs reviewed | Coverage | Reds | Yellows | Status |
|---|---|---|---|---|---|
| [name] | [N/M] | [%] | [N] | [N] | [Complete / In progress / Blocked] |

## Gaps and follow-ups

- [補足リクエスト項目で未完のもの]
- [マネジメントへの質問]

## Next 72 hours

[何がレビューされる、どのブリーフィングがスケジュール済み]
```

### Working team tier

完全な所見詳細。上記と同じ構造だが、各所見は 1 行ではなくフルなハウスフォーマットブロックを得る。

## デルタ

これが定期ブリーフ(`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` のケイデンスに従う)の場合、何が変わったかをリード:

- 前回ブリーフ以降の新所見
- 重大度がアップ/ダウングレードされた所見
- 解決した所見(consent 取得、論点が明確化されて消滅)
- カバレッジの動き

ディールリードは state より movement を気にします。「Still 12 yellows」は「2 new yellows, 3 resolved」より有用性が低い。

## ハンドオフ

- **From diligence-issue-extraction:** 本スキルは累積した所見を読む。
- **To closing-checklist:** クロージング条件に解決する「decision needed」項目はチェックリストに載る。

## next-steps デシジョンツリーで締めくくる

CLAUDE.md の `## Outputs` に従い、next-steps デシジョンツリーで終わる。選択肢は本スキルが今生成したものに合わせてカスタマイズ — デフォルト 5 分岐(draft the X、escalate、get more facts、watch and wait、something else)は出発点で固定ではない。ツリーが出力;弁護士が選ぶ。

## 本スキルがしないこと

- 重要性の判断はしない — 抽出時になされた判断を報告する。
- 所見についてディールチームが何をするかを決定しない — 判断を表面化する。
- ブリーフを配布しない — ドラフトする、人間が送る。
