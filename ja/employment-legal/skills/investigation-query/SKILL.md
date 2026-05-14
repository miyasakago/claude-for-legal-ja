---
name: investigation-query
description: >
  開設中の調査ログに対して質問 — 証人が何を言ったか、説明がどこで矛盾するか、
  どんなギャップが存在するか、各論点で最強の証拠は何か。弁護士がすべての
  エントリを再読することなく調査記録にクエリする必要があるときに使用。
  (Ask questions against an open investigation log. Use when the attorney
  needs to query the investigation record without re-reading every entry.
  日本語トリガー: 「調査ログにクエリ」「証人が何を言ったか」「説明の矛盾」
  「調査ギャップ」「強い証拠は何か」)
argument-hint: "[matter name] [question]"
---

# /investigation-query

調査ログに対する質問に答える — 証人が何を言ったか、説明がどこで矛盾するか、どんなギャップが存在するか、各論点で最強の証拠は何か。

## 指示

1. `internal-investigation` 参照スキルをロードし、モード 3(クエリ)を実行。
2. 回答で常にログエントリ ID を引用。
3. ログに質問に関連するものがなければ、明示的に述べる — 「この調査ログには [topic] に関する情報を見ていません(レビュー済み [N] エントリ)」 — ギャップとしてフラグすることを提案。

## 例

```
/employment-legal-ja:investigation-query [matter name]
被申立人は 12 月のチームディナーについて何を言いましたか?
```

```
/employment-legal-ja:investigation-query [matter name]
申立人と被申立人の説明はどこで矛盾していますか?
```

```
/employment-legal-ja:investigation-query [matter name]
まだ何が必要ですか?
```

> 詳細なログクエリプロセス、引用ルール、ギャップフラグテンプレートは `internal-investigation` 参照スキルにあります — 実質的な作業前にロードしてください。
