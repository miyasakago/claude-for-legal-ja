---
name: supervisor-review-queue
description: >
  指導教授のレビューキュー — 学生の成果物がクライアントや裁判所に行く前に
  教授承認のためここで待機します。「formal review queue」監督スタイルが
  セットアップで選ばれた場合のみアクティブ; そうでなければ休眠。教授が
  レビュー待ちのものを確認、承認、編集して承認、または項目を差し戻したい
  ときに使用。
  (Use when the
  professor wants to see what's waiting for review, approve, edit-then-approve,
  or return an item.
   日本語トリガー: レビューキュー、教授レビュー、承認、差し戻し)
argument-hint: "[--approve ID | --return ID 'note' | --edit ID]"
---

# /supervisor-review-queue

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を確認 → 監督スタイル。「formal review queue」でない場合: クリニックが [flags/lighter-touch] 用にセットアップされ、正式キューは存在せず、切り替え方法を説明。
2. 下記のワークフローを使用。
3. デフォルト: 待機中のものを緊急性別・学生別に表示。
4. アクション: 承認 / 編集して承認 / 注記付き差戻し。すべて記録。

```
/legal-clinic-ja:supervisor-review-queue
```

```
/legal-clinic-ja:supervisor-review-queue --approve Q-003
```

```
/legal-clinic-ja:supervisor-review-queue --return Q-004 "Check the service requirement — local rules changed"
```

---

# Supervisor Review Queue(指導教授レビューキュー、オプショナル)

## 目的

一部のクリニックは正式ゲートを望む: 学生ドラフト、教授レビュー、出力リリース。他は too prescriptive と感じる — 彼らはキューを通じてではなく case rounds と 1on1 を通じて監督する。

**本スキルは `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → Supervision style が「formal review queue」の場合のみアクティブ。** そうでなければ休眠 — コールドスタート・インタビューが教授にどのモデルを望むか尋ね、これは 3 つの選択肢の 1 つ。

正式レビューワークフローを使うかどうかはクリニック採用にとって真のオープン質問。学生経験レベル、案件量、教授が既に監督を運営する方法に依存する。教授がセットアップで決定し、後で変更可能。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → 監督スタイル。「formal review queue」でない場合: 「The clinic is set up for [flags/lighter-touch] supervision — there's no formal queue. [Professor] reviews through [the clinic's existing structure]. To switch to a formal queue, edit CLAUDE.md → Supervision style.」 で応答。

正式キューが有効 → フラグトリガーを読み、進める。

## キュー

`references/review-queue.yaml` に住む。各エントリ:

```yaml
- id: Q-001
  type: "draft"  # intake | draft | memo | status | client-letter
  client: "[name or ID]"
  student: "[name]"
  submitted: [timestamp]
  flags:
    - rule: "Court filing"
      detail: "Eviction answer — always queued"
  content_path: "[path to the document]"
  status: "pending"  # pending | approved | edited-approved | returned
```

## モード

### 何が待機中

```markdown
## Review Queue — [date]

**Pending:** [N] | **Oldest:** [N] hours

### 🔴 Deadline-sensitive
| ID | Type | Client | Student | Why flagged | Waiting |
|---|---|---|---|---|---|

### Standard
[same table]

### By student
[内訳 — パターンを発見: 誰が多くキューしているか、誰がチェックインを必要としそうか]
```

### 項目をレビュー

完全な内容 + フラグされた理由 + 学生注記を表示。

### 承認 / 編集して承認 / 差戻し

- **Approve:** Status → approved、学生通知、ログ。
- **Edit then approve:** 教授がインライン編集、承認バージョンは編集されたもの、オリジナルはログに保存され学生は diff を見る(teaching moment)。
- **Return:** 注記とともに。学生が改訂・再提出。

## ログ

すべてのアクションを記録。承認ログはクリニック記録 — ライセンスを持つ弁護士、ソリシター、バリスター、その他のクリニック法域における正規法律専門家が、学生の作業がクライアントまたは裁判所に行く前にレビューしたことを文書化する。これはクリニック自身のコンプライアンスと学生評価にとって重要。

## 教育シグナル

キューはまたデータ。返却のパターン(「Student X が service 要件をミスし続ける」)はコーチング会話。編集のパターン(「全員の demand letters が長すぎる」)は次学期の `/ramp` 更新。

## このスキルが行わないこと

- **教授が選ばない限り実行。** 3 つの監督モデルの 1 つであり、唯一ではない。
- **自動承認。** 教授が承認。
- **クリニックの既存監督構造を置き換え。** 成果物のゲートであって、case rounds、1on1、または学生の実際の作業を観察することの代替ではない。
