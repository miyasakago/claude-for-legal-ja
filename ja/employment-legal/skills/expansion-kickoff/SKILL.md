---
name: expansion-kickoff
description: >
  新規国への国際展開計画のキックオフ — インテイクを収集し、EOR(代行雇用)
  vs 法人のフレーミングを実行し、部門横断の質問をドラフトし、国別フラグを
  表面化し、永続トラッカーを作成。誰かが「[国] で採用したい」「[国] への
  展開」「[国] での初採用」と言うときに使用。
  (Kick off international expansion planning for a new country — gathers
  intake, runs EOR vs. entity framing, drafts cross-functional questions,
  surfaces country-specific flags, and creates a persistent tracker. Use when
  someone says "we're hiring in [country]", "expansion to [country]", or
  "first hire in [country]". 日本語トリガー: 「国際展開」「海外採用」
  「初めて [国] で採用」「[国] 進出」「EOR 検討」)
argument-hint: "[country name]"
---

# /expansion-kickoff

新規国への国際展開プロジェクトを開始 — インテイクを収集、EOR vs 法人のフレーミングを実行、部門横断の質問をドラフト、国別フラグを表面化、永続トラッカーを作成。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` をロード → 法域フットプリント、エスカレーション表。
2. `international-expansion` 参照スキルをロードし、完全なワークフローを実行。
3. この国のトラッカーファイル(`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/expansion-[slug].yaml`)が既に存在する場合、フラグ: 「[country] の展開トラッカーは既に存在します。`/employment-legal-ja:expansion-update [country]` で更新するか、最初からやり直すことを確認してください。」
4. 完了時に `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/expansion-[slug].yaml` を作成。

## 例

```
/employment-legal-ja:expansion-kickoff Germany
```

```
/employment-legal-ja:expansion-kickoff
(スキルがどの国かを尋ねます)
```

> 詳細な EOR vs 法人フレームワーク、部門横断の質問、ブリーフィング・テンプレート、トラッカー・スキーマは `international-expansion` 参照スキルにあります — 実質的な作業前にロードしてください。
