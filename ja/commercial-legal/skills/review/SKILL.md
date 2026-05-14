---
name: review
description: >
  ベンダー契約、NDA、または SaaS サブスクリプションをプレイブックに照らしてレビュー
  します。タイトルから契約構造を識別し、適切なレビュースキル
  (vendor-agreement-review、nda-review、saas-msa-review)に振り分け、出力を
  1 つのメモに統合します。ユーザーが "review this contract"、"check this MSA"、
  "is this NDA okay"、"look at this SaaS agreement" と言ったとき、または
  インバウンド契約をレビューのため添付したときに使用。
  日本語トリガー例: 契約レビュー、契約書チェック、契約書を見て、MSA を確認、NDA を確認、SaaS 契約を見て。
argument-hint: '[file path | Drive link | [CLM ID] | テキストペースト]'
---

# /review

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` のプレイブックに照らしてインバウンド契約をレビューします。タイトルから契約構造を識別し、適切なスキルを選択し、confirm_routing が有効ならユーザーに確認してから進行します。

## 指示

1. **`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` をロード。** プレースホルダーが残っていれば停止して促す:「最初に `/commercial-legal-ja:cold-start-interview` を実行してください — プレイブックを学習しないとレビューできません。」

   同時に `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` → `## Review preferences` → `confirm_routing` を読む。フィールドがなければ `true` として扱う。

2. **契約を取得:**ファイルパス、Drive リンク、[CLM ID]、ペーストテキストから。何も提供されなければ尋ねる。

3. **文書構造を読む — タイトルが先。**

   本文を読む前に抽出:
   - メイン契約のタイトル(例:"Master Services Agreement"、"Non-Disclosure Agreement")
   - すべての exhibit、schedule、addendum、attachment のタイトル(例:"Exhibit A — Data Processing Addendum"、"Schedule 1 — Subscription Order Form"、"Annex B — Service Level Agreement")

   これがルーティングのシグナル。本文キーワードだけに依存しない — 全 40 ページで「confidential」と書いてある MSA は NDA ではない。

4. **文書構造に基づいてスキルを選択。**

   識別された各文書または節をスキルにマップ:

   | 文書/節のタイトルに含まれる語 | スキル |
   |---|---|
   | Non-Disclosure、NDA、Confidentiality Agreement(*メイン*契約として) | **nda-review** |
   | Master Services Agreement、Professional Services、Statement of Work、Consulting Agreement | **vendor-agreement-review** |
   | Subscription、SaaS、Cloud Services、自動更新付き Order Form、recurring fees 付き Software License | **saas-msa-review**(vendor-agreement-review のオーバーレイ) |
   | Data Processing Addendum、DPA、Data Processing Agreement(exhibit またはスタンドアロン) | **vendor-agreement-review** の data protection 節への注記 |
   | Service Level Agreement、SLA(exhibit) | **saas-msa-review** の SLA 節への注記 |

   複数のスキルが該当する場合あり。よくある組み合わせ:
   - MSA + DPA exhibit → vendor-agreement-review、DPA は注記
   - SaaS subscription + Order Form + SLA exhibit → saas-msa-review(3 つすべてをカバー)
   - 自動更新付き MSA + Order Form → vendor-agreement-review + saas-msa-review オーバーレイ

   タイトルを読んでも構造が本当に曖昧な場合(例:exhibit リストなしの「Agreement」というタイトルの文書)、本文の最初の 2 ページを読んで解決 — それから停止してルーティング。

5. **有効ならルーティングを確認。**

   `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` で `confirm_routing` が `true`(またはフィールドが存在しない)の場合:

   ```
   これを次として レビューします: [契約タイプ]。

   識別された文書:
   - [メイン契約タイトル] → [スキル]
   - [Exhibit A タイトル] → [どう扱うか]
   - [Exhibit B タイトル] → [どう扱うか]

   合っていますか?(yes / no — または何が違うか教えてください)
   ```

   確認を待ってから進行。ユーザーがルーティングを修正したら指示を適用して進む。

   `confirm_routing` が `false`:静かに進行。レビューメモの冒頭にルーティング判断をログし、ユーザーが何が適用されたかを見られるようにする。

6. **スキルを実行。** 各スキルのワークフローに完全に従う。複数スキルが該当する場合は順次実行し、出力を 1 つのメモに統合 — 別々のメモを作らない。

7. **エスカレーションのチェック:** いずれかの論点が `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` のマトリクスに照らしてレビューアーの権限を超える場合、**escalation-flagger** を呼び出して振り分け、依頼を起草。

8. **フォローアップを提示:**
   - ビジネスオーナー向けのステークホルダーサマリー
   - 変更履歴付きのレッドライン .docx
   - [CLM] レコード作成(接続されていれば)
   - 更新台帳への追加(自動更新が見つかれば)

## confirm_routing の設定

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` → `## Review preferences` に追加:

```markdown
## Review preferences

confirm_routing: true   # ルーティング確認をスキップして自動進行するには false に設定
```

コールドスタート・インタビューでこの設定について尋ねます。デフォルトは `true` — 確認オン。信頼が育てば、ユーザーは `false` に設定可能。

## 例

```
/commercial-legal-ja:review vendor-msa.pdf
```

```
/commercial-legal-ja:review https://drive.google.com/file/d/ABC123
```

```
/commercial-legal-ja:review
[契約テキストをペースト]
```

## 出力

スキルの形式に従った完全なレビューメモ。ルーティング判断は冒頭にログ。乖離ごと、具体的なレッドライン文言、指名された承認者付き。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` → House style が示す場所に成果物を保存。
