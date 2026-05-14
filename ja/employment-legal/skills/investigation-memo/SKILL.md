---
name: investigation-memo
description: >
  調査ログから秘匿特権下の調査メモをドラフトまたは更新。調査が初回メモを
  書くのに十分進んでいるとき、または新しいデータが追加されて既存ドラフトの
  更新が必要なときに使用。
  (Draft or update the privileged investigation memo from the investigation
  log. Use when an investigation is far enough along to write the first memo
  cut, or when new data has been added and the existing draft needs updating.
  日本語トリガー: 「調査メモをドラフト」「メモを書く」「メモを更新」
  「調査メモを起草」)
argument-hint: "[matter name]"
---

# /investigation-memo

ログから秘匿特権下の調査メモの初回カットをドラフト、または新しいデータが追加されたときに既存ドラフトを更新。

## 指示

1. `internal-investigation` 参照スキルをロードし、モード 4(メモをドラフトまたは更新)を実行。
2. 初回ドラフトの場合、高優先度ソースがチェックリストでまだ未解決なら警告。
3. 更新の場合、書き直し前に何が変わったかを表示。
4. すべての出力は PRIVILEGED AND CONFIDENTIAL — ATTORNEY WORK PRODUCT でマーク。

## 例

```
/employment-legal-ja:investigation-memo [matter name]
```

```
/employment-legal-ja:investigation-memo [matter name]
(メモが存在する場合は既存メモを更新)
```

> 詳細なメモ構造、信用性評価フレームワーク、更新ルールは `internal-investigation` 参照スキルにあります — 実質的な作業前にロードしてください。
