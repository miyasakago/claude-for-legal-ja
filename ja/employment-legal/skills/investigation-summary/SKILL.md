---
name: investigation-summary
description: >
  秘匿特権下の調査メモから対象者別サマリーをドラフト — HR、リーダーシップ、
  または外部弁護士バージョン。完全な秘匿特権付き成果物を見るべきでない対象者
  に調査メモを伝える必要があるときに使用。
  (Draft an audience-specific summary from the privileged investigation memo —
  HR, leadership, or outside counsel versions. Use when an investigation memo
  needs to be communicated to an audience that should not see the full
  privileged work product. 日本語トリガー: 「調査サマリーをドラフト」
  「HR 向けサマリー」「リーダーシップ向け要約」「外部弁護士向けブリーフィング」)
argument-hint: "[matter name] [audience: hr / leadership / outside-counsel]"
---

# /investigation-summary

秘匿特権下の調査メモから、対象者に合わせた絞り込まれたサマリーをドラフト。HR サマリーは特権分析を含まない。リーダーシップサマリーは高レベル。外部弁護士ブリーフィングは完全なコンテキストを含む。

## 指示

1. `internal-investigation` 参照スキルをロードし、モード 5(対象者別サマリー)を実行。
2. メモがまだ存在しない場合、先にメモをドラフトすることを提案。
3. HR サマリーは弁護士の心理的印象、信用性方法論、または法的露出分析を含まないこと。

## 例

```
/employment-legal-ja:investigation-summary [matter name] hr
```

```
/employment-legal-ja:investigation-summary [matter name] leadership
```

```
/employment-legal-ja:investigation-summary [matter name] outside-counsel
```

> 詳細な対象者別絞り込みルールとサマリーテンプレートは `internal-investigation` 参照スキルにあります — 実質的な作業前にロードしてください。
