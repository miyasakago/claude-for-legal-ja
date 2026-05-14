# demand-letters/ — 訴訟前の要求書ワーク

このフォルダは、弁護士が送付するすべての要求書(支払い要求、契約違反/治癒通知、警告状(Cease & Desist)、雇用終了要求、保全要求)の成果物を保持します。

`matters/` と分けている理由:

- すべての要求書がトラッキング対象の案件に発展するとは限りません。少額の支払い要求や定型的な回収には台帳の行は不要です。
- 要求書はすべて同じワークフローの形(intake → ドラフト → 送付 → チェックリスト)を持ち、案件化するかどうかに関わらず共通です。
- 要求書が案件化した場合、案件の `matter.md` がここへクロスリンクし、起案履歴は要求書側に残ります。

## レイアウト

```
demand-letters/
├── _README.md                     # このファイル
└── [slug]/
    ├── intake.md                  # コンテキスト収集、戦略、レバレッジ、特権フィルタ
    ├── draft-v1.docx              # レター(イテレーションに応じて v2, v3)
    └── checklist.md               # 送付後チェックリスト — 配達、コピー、カレンダー化された期限、フォローアップ
```

## Slug の規約

`[type]-[counterparty]-[yyyy-mm]`。例:

- `payment-acme-2026-04`
- `ceasedesist-competitor-x-2026-04`
- `breach-supplier-2026-04`
- `separation-smith-2026-04`
- `preservation-vendor-2026-04`

## ワークフロー

1. `/litigation-legal-ja:demand-intake [title]` → 適応的 intake を実行、`intake.md` を書き込み
2. `/litigation-legal-ja:demand-draft [slug]` → FRE 408(米国連邦証拠規則 408 / 和解協議の証拠排除)/ 特権 / 放棄チェックリストを実行、`.docx` を起案、`checklist.md` を書き込み、案件作成を提案

## 案件との関係

要求書を起案した後、`demand-draft` は重要性(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` のヒューリスティック)を評価し、案件作成を提案します。Yes の場合、`matters/_log.yaml` に `source: demand-letter` で案件行が追加され、`matters/[matter-slug]/matter.md` がこの要求書のフォルダへリンクバックされます。

重要性が低い要求書はここに残ります。それでも成果物の記録ではあり、ただポートフォリオでトラッキングされないだけです。

## 訂正とバージョン管理

送付済みのドラフトは絶対に上書きしないでください。送付したレターに修正が必要な場合(例: 追加要求書)は `draft-v2.docx` から始めます。バージョンの履歴それ自体が有用な記録です。
