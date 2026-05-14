---
name: matter-briefing
description: 1 案件の深掘りブリーフィング — 現在のポスチャ、変化したこと、次の期限、未解決の質問、リスク再評価チェック。GC アップデートや外部弁護士との通話前に読める状態。Use when the user says "brief me on [matter]", "where are we on [matter]", or needs a read on a specific matter. 日本語トリガー: 案件ブリーフィング、案件状況、案件のまとめ
argument-hint: "[slug]"
---

# /matter-briefing

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → リスクキャリブレーション + 関連ステークホルダー。
2. 以下のワークフローと参照に従う。
3. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` + `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` + `_log.yaml` のログ行を読む。
4. ブリーフィングを生成: 現在ポスチャ、前回更新以降の変化、次期限、未解決の質問、リスク再評価チェック(「`risk:` フィールドは依然現実を反映しているか?」)。
5. 停滞をフラグ: `last_updated` が 30 日超なら、伝える。

---

# Matter Briefing

## 目的

会議室に歩いていく時間で、1 案件についてクリーンな読み取りを弁護士に提供します。現在ポスチャ、変化、次に来るもの、再考に値するもの。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — 構造化された行
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` — 物語形式の intake
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` — イベントログ
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` — リスクキャリブレーション(「risk: high」が一般的ではなく具体的な意味を持つように)

**コンフリクトゲート — バイパス不可。** ブリーフィング前に、`_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace is set up. I won't build a briefing on a matter that hasn't been intaken — the conflicts check is the gate."

## 入力

slug(必須)。曖昧または欠落している場合、アクティブ案件のリストから選ぶようユーザーに依頼。

## ブリーフィング

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

# [Matter Name] — Briefing as of [today]

**Status:** [status / stage]
**Risk:** [rating] ([severity] × [likelihood])
**Materiality:** [category]
**Outside counsel:** [firm — lead]
**Last updated:** [date] [30 日超なら ⚠️ STALE をフラグ]
**Conflicts:** [status — `pending` または `not-run` なら ⚠️ をフラグ]

---

## 1 段落サマリー

[現在ポスチャ。何をしているか、なぜか。キャプチャされていれば pivot fact を述べる。]

## 最近の変化

[history.md の直近 3-5 エントリ、最新を上に。履歴が薄ければ、そう伝える。]

## 次に来るもの

- **直近期限:** [next_deadline + 何のための]
- **今後のマイルストーン:** [matter.md または最近の履歴で日付があるもの]
- **保留中の決定:** [matter.md でフラグされたオープン質問]

## エクスポージャー

[範囲 + intake からの変化。引当している場合、現在の引当 + 再キャリブレーションが遅れていないか。]

## 内部オーナー

[ループインされているのは誰か;ループインされるべきだがされていないのは誰か]

## リスク再評価チェック

*答えではなくプロンプト。*

- `risk: [rating]` は依然正しく感じるか、それともケースは動いたか?
- `materiality: [category]` は依然マッチするか?(新事実が引当または開示に押し進めるかも。)
- 案件が必要とする新しいステークホルダーは?(例: ディスカバリーの進展後 CISO が関連に。)

## 未解決の質問

[matter.md と、履歴で未解決のもの]

## 会話のために

[ユーザーが目的を指定した場合 — 「外部弁護士との通話前にブリーフして」 — 最終セクションをテーラリング: 尋ねるべき質問、得るべき決定、抽出すべきアップデート。目的がなければ、このセクションは省略。]
```

## 停滞

`last_updated` が 30 日以上前の場合: 上部でフラグし、ミーティング後に話し合った内容をキャプチャするために `/litigation-legal-ja:matter-update [slug]` を実行することを提案。

## トーン

これはマーケティングではありません。分かっていることを言い、分からないことをフラグします。案件の履歴が薄く、たった今オープンされた場合、ブリーフィングは短く — それが正しい。水増ししないでください。

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` に従って、次ステップの決定ツリーで締めくくります。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではありません。ツリーが出力です;弁護士が選びます。

## このスキルがしないこと

- 結果を予測する。リスク評価はキャプチャされた判断であり、予測ではありません。
- 戦略を推奨する。質問を表面化;弁護士が答えます。
- 再トリアージ。ユーザーが再トリアージしたい場合、それはフィールド変更を伴う `/matter-update` です — このスキルは読み、書きません。
