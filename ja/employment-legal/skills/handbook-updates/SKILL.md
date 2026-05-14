---
name: handbook-updates
description: >
  提案されたハンドブック変更を現行バージョンと差分比較し、波及効果と州別補足
  への影響をフラグします。ユーザーが「ハンドブックを更新」「これをハンド
  ブックに追加」「ハンドブック変更」と言うとき、または挿入準備済みのポリシー
  があるときに使用。
  (Diff a proposed handbook change against the current version, flag ripple
  effects and state supplement impacts. Use when user says "update the
  handbook", "add this to the handbook", "handbook change", or has a policy
  ready for insertion. 日本語トリガー: 「ハンドブック更新」「就業規則変更」
  「ハンドブックに追加」「ポリシー追加」)
---

# ハンドブック更新 (Handbook Updates)

## 案件コンテキスト

**案件コンテキスト。** プラクティスレベル CLAUDE.md の `## 案件ワークスペース` を確認。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、このパラグラフの残りはスキップ — スキルはプラクティスレベルのコンテキストを使用し、案件機構は見えません。有効でアクティブな案件がない場合、尋ねる: 「これはどの案件のものですか? `/employment-legal-ja:matter-workspace switch <slug>` を実行、または `practice-level` と言ってください。」案件固有のコンテキストと上書きについてアクティブ案件の `matter.md` をロード。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/<matter-slug>/` に書き込み。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まない。

---

## 目的

ハンドブック変更は波及効果を持ちます。PTO ポリシーを変更すると、最終給与計算、休職ポリシー・クロスリファレンス、3 つの州別補足に影響しました。このスキルは不整合になる前に波及を見つけます。

## コンテキストをロード

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → ハンドブック所在、州別補足リスト、更新頻度。

## ワークフロー

### Step 1: 変更を取得

- どのセクションが変わるか?
- 新しい文言は何か?
- なぜ? (法的要件、ポリシー判断、整理)

### Step 2: 現行と差分比較

現行ハンドブックセクションを読む。差分を表示:

```diff
- [old language]
+ [new language]
```

### Step 3: クロスリファレンスを見つける

変更されたセクションへの参照をハンドブック内で検索:

- これを引用する他のポリシー(「累積率は PTO ポリシーを参照」)
- このセクションが使用または定義する定義済み用語
- このセクションを修正する州別補足

各クロスリファレンス: 変更後も意味を成すか? 壊れるものをフラグ。

### Step 4: 州別補足への影響

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` の各州別補足について:

- この補足は変更されるセクションを修正するか?
- 変更により補足が陳腐化、誤り、不完全になるか?
- 変更により、補足が*不要だった*州で*新しい*補足が必要になるか?

### Step 5: 約束チェック

変更により旧版が約束したものを縮小するか?

はいなら: それはリスクです。一部の州ではハンドブックポリシーを契約として扱います。給付の縮小は文書を更新するだけでは不十分かもしれない — 事前通知、対価、または場合によっては遡及的に行えないことも。

これをフラグ。ブロックしないが、フラグ。

## 出力

```markdown
## Handbook Update: [Section name]

### Change

[diff]

### Cross-reference impact

| Section | References changed section | Still accurate? | Fix needed |
|---|---|---|---|
| [name] | [how] | ✅/⚠️ | [what] |

### State supplement impact

| State | Current supplement | After change | Action |
|---|---|---|---|
| [state] | [what it says] | [still valid / obsolete / needs update] | [none / update / new supplement needed] |

### Promise check

[If reducing a benefit: flag + jurisdictional risk note]

### Ready to publish

- [ ] Cross-references updated
- [ ] State supplements updated
- [ ] [If benefit reduction: notice/consideration addressed]
- [ ] Version number and date updated
- [ ] Acknowledgment process (if required)
```

## このスキルが行わないこと

- ハンドブック変更を承認。HR / 法務リーダーシップが行う。
- 変更を従業員に伝達。
- 承認確認を追跡。
