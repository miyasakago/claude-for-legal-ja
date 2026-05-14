---
name: matter-close
description: 案件をクローズ — 結果、最終エクスポージャー、学びをキャプチャし、記録を削除せずにアクティブポートフォリオからアーカイブします。Use when the user wants to close a matter, says "[matter] is done", or needs to record a settlement, dismissal, judgment, withdrawal, or consolidation outcome. 日本語トリガー: 案件クローズ、案件終了、和解記録、判決記録、案件アーカイブ
argument-hint: "[slug]"
---

# /matter-close

1. 以下のワークフローと参照に従う。
2. slug と現在のステータスを確認。
3. 結果をキャプチャ: 解決種別(和解、却下、勝訴判決、敗訴判決、取下、統合)、日付、最終エクスポージャー/コスト、学び。
4. `_log.yaml` を更新: `status: closed`、`closed: YYYY-MM-DD`、`outcome:` フィールドを追加。
5. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` に最終エントリを追記。
6. 案件は `_log.yaml` と `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/` に残ります — 削除されません。`/portfolio-status` はアクティブロールアップからフィルタアウトします。

---

# Matter Close

## 目的

案件は終わります。結果はポートフォリオが生成する単一で最も価値のあるデータポイントです — 将来の案件のリスクフレームワークをキャリブレートします。案件をクローズするとは、結果を構造化してキャプチャすること、その記録が有用で、単にアーカイブされるだけでない状態にすることです。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — 該当行を探す
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` — 参照(intake コンテキスト)
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` — 追記先

**コンフリクトゲート — バイパス不可。** クローズする前に、`_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Nothing to close — either the slug is wrong or the matter was never intaken through `/litigation-legal-ja:matter-intake`. Check the slug first; if it genuinely was never intaken, there's no row to update and no file structure to close."

## 入力

slug(必須)。

## クローズ

### 1. 解決種別

- `settled` — 相手方と、ドル額、構造的条件
- `dismissed` — 偏見を伴う/伴わない、どのメカニズムで
- `judgment-for-us` — どの段階で、上訴エクスポージャー
- `judgment-against-us` — どの段階で、上訴ステータス、エクスポージャー結晶化
- `withdrawn` — 相手方によって、状況
- `consolidated` — 別案件にマージ(親の slug 提供)
- `other` — 説明付き

### 2. 解決日

案件が実際に終わった日(和解実行、命令発令、却下提出)。

### 3. 最終エクスポージャー

- 会社への実費(和解額 + 手数料 + 差止/構造的コスト)
- intake 時点の初期エクスポージャー範囲との比較(コールできたか?)
- 引当精度(引当した場合): 計上額 vs 実額

### 4. 学び

2-3 文。何が正しかったか? 何を誤判断したか? intake が早期にフラグすべきだったものは?

これが将来の弁護士が再読する部分です。正直に。「Misjudged likelihood — plaintiff firm was more aggressive than expected」は「resolved favorably」より価値があります。

### 5. シードドキュメントプロンプト

和解契約、最終命令、却下 — 利用可能ならパス。必須ではありません。

## 書き込み

**案件をクローズする前(重大な行為 — 案件はアーカイブされ、アクティブトラッキングは終了):** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Closing a matter has legal consequences — it ends active tracking, may affect any associated legal hold (run `/legal-hold --release` separately if appropriate), and establishes the final record the company relies on. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: 案件、解決種別と条件、最終エクスポージャー vs 初期、引当精度、まだ生きている関連案件または上訴、早すぎるクローズで何が悪くなり得るか、クローズ前に弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしには、クローズフィールドの書き込みもクローズエントリの追記も行わない。

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` を更新

```yaml
status: closed
closed: [YYYY-MM-DD]
outcome: [resolution-type]
final_cost: [dollar amount]
last_updated: [today]   # クローズが最終タッチ; 記録
```

既存フィールドはすべて保持。行を削除しないこと。

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` に最終エントリを追記

```markdown
## [YYYY-MM-DD] — Matter closed: [resolution-type]

**Resolution:** [物語 — 何が起きたか、どんな条件で]
**Final cost:** [金額 + 構造的条件があれば]
**vs. initial exposure:** [matter.md intake 範囲と比較]
**Reserve accuracy:** [該当する場合]

**Lessons:**
[2-3 文 — 正直な振り返り]

**Related doc:** [提供されていれば和解契約 / 最終命令 / 等]
```

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` にタッチ

クロージングブロックを末尾に追加(以前のセクションを変更しない — それらは履歴的な intake):

```markdown
---

## Closed [YYYY-MM-DD]

[1 段落の解決サマリー。詳細は最終履歴エントリへのポインタ。]
```

## 確認

書き込み前に、完全なクローズエントリと yaml 変更をユーザーに表示。

## このスキルがしないこと

- 案件を削除する。クローズ案件は `_log.yaml` とディスクに残ります — それらはポートフォリオの判断のトレーニングセットです。
- 再オープン。クローズ案件が戻ってきた場合(上訴、関連訴訟)、クローズ案件を `matter.md` で参照する新規案件をオープンしてください。
- ユーザーが言わなかった学びを要約する。ユーザーが学びセクションをスキップした場合、空のままにし、捏造しないでください。
