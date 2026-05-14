---
name: matter-update
description: 案件の履歴ファイルに日付付きイベントを追記し、ログ行を更新 — 新規進展、ステータス変更、リスク再評価、期限シフト、和解権限変更をキャプチャ。Use when the user wants to log an update on a matter, note a development, or record a status change against the portfolio. 日本語トリガー: 案件更新、進展記録、ステータス変更、案件アップデート
argument-hint: "[slug] [brief event description]"
---

# /matter-update

1. 以下のワークフローと参照に従う。
2. slug が `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/` と `_log.yaml` に存在することを確認。
3. イベントタイプ、日付(デフォルト今日)、サマリー、ログフィールド更新(リスク変更、ステータス変更、次期限シフト、重要性再分類)をプロンプト。
4. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` に日付付きエントリを追記。
5. `_log.yaml` を更新 — `last_updated` を今日に設定、フィールド更新を適用。
6. 確認。

---

# Matter Update

## 目的

ポートフォリオは最新に保たれてこそ有用です。このスキルはアップデートのログを安価に — 構造化キャプチャに 2 分、自由形式のドリフトなし — します。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — 該当行を探す
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` — 追記先
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` — 参照(再書きしない)
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` — リスクキャリブレーション(リスク再評価する場合)

**コンフリクトゲート — バイパス不可。** アップデートをログする前に、`_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace exists. I won't append history to an unmanaged matter — the conflicts check is the gate, and there's no `history.md` to append to until the matter is intaken."

## 入力

slug(必須)。提供されない場合、最近更新された案件の短いリストから選ばせて尋ねる。

## アップデート

### 1. イベントタイプ

カテゴリを提示:

- **手続的** — 申立て提出/受領、命令発令、審理開催、期限設定
- **ディスカバリー** — プロダクション実施/受領、デポジション、サブポエナ送達
- **実質的** — 新事実、キー文書浮上、メリットに関する判決
- **戦略** — ポスチャシフト、和解オファー実施/受領、権限更新
- **リスク再評価** — 重大度または確度が変更
- **ステークホルダー** — 新人ループイン、外部弁護士変更
- **管理的** — エンゲージメントレター実行、予算調整、ホールド更新

または合致しなければ自由形式で。

### 2. 日付

デフォルト今日。オーバーライドを受け入れる(例: 先週のイベントをキャプチャ)。

### 3. サマリー

1 段落の物語。何が起きたか、何を意味するか、即時の含意。

### 4. ログフィールド変更

潜在的に影響を受けるフィールドを順に確認:

- `status:` — 段階がシフトしたか(例: 訴答 → 事実ディスカバリー)?
- `stage:` — サブステージ更新
- `risk:` — 再評価が必要か?
- `materiality:` — 何か変更(新事実が引当または開示をトリガーする可能性)?
- `exposure_range:` — 新情報があれば修正
- `next_deadline:` — 新しい今後の日付、あれば
- `outside_counsel:` — 変更?
- `internal_owners:` — 新人または削除?
- `legal_hold:` — 更新、拡大、解除?

イベントタイプに影響を受ける可能性が高いフィールドのみプロンプト。手続的更新は通常 `stage` と `next_deadline` のみに触れる;和解オファーは `materiality`、`exposure_range`、`status` に触れるかも。

### 4pre. 和解受諾ゲート

戦略アップデートが**和解受諾**(会社が和解オファーを受諾、和解契約を執行、または原則的に受諾を授権する — オファー実施/受領のログだけではない)の場合: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Accepting a settlement has legal consequences — it resolves claims, typically requires a release, and can affect insurance, tax, and related matters. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: 案件、提案和解条件(ドル、構造、免責スコープ、機密性、誹謗禁止)、懸かっているエクスポージャー、権限ラダーステータス(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 和解権限参照)、何が悪くなり得るか、受諾前に弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしに、受諾をログしたり受諾を基準に重要性を反転したりしない。オファーまたはカウンターのログにはゲート不要 — 受諾には必要。

### 4a. 重要性トリガー — 明示プロンプト

特定のイベントタイプは重要性の再チェックを強制します。イベントタイプが下のリストにあるとき、**常にプロンプト** — ユーザーに明示的回答なしに進ませないでください:

| Event type | Materiality trigger prompt |
|---|---|
| 実質的(新事実、キー文書、メリット判決) | 「This event is substantive. Does it push `materiality`? Current: `[current]`. Options: `reserved / disclosed / monitored / none`. Change?」 |
| 戦略(ポスチャシフト、和解オファー実施/受領) | 「Settlement activity often triggers materiality reclassification. Current: `[current]`. If the offer, counter, or acceptance moves exposure or shifts from contested to probable-and-estimable, reclassify.」 |
| リスク再評価(重大度または確度変更) | 「Risk moved. Materiality should track. Current: `[current]`. Reclassify?」 |
| 規制 / 執行進展 | 「Regulator action (subpoena, CID, enforcement notice) usually triggers disclosure analysis. Current: `[current]`. Change?」 |

受容可能な回答には `no change` を含みます — ただし `no change` は明示的でなければならず、沈黙によって示唆されません。履歴エントリにキャプチャ:

```markdown
**Materiality check:** [no change / changed from X to Y]
**Reasoning:** [1 文]
```

重要性が `reserved` または `disclosed` に動き、案件が以前引当または開示を運んでいなかった場合、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の重要性閾値に従って財務 / 監査委員会通知が必要としてイベントをフラグ。

### 5. シードドキュメントプロンプト(任意)

アップデートが文書(命令、提出物、通信)を参照する場合、リンクするパスがあるか尋ねる。押し付けがましくなく。

## 書き込み

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` に追記

最新を上に、ヘッダー後の `---` の直下に。

```markdown
## [YYYY-MM-DD] — [Event type]: [short title]

[段落サマリー。]

**Fields changed:**
- [field]: [old → new]
- [field]: [old → new]

**Related doc:** [パス、提供されていれば]
```

フィールド変更がなければ、「Fields changed」ブロックを省略。

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` を更新

- 任意のフィールド変更を適用。
- `last_updated: [today]` を設定(またはユーザーがオーバーライドしたらイベント日 — ログは記録が最後にタッチされた時を追跡)。

## 確認

書き込み前に履歴エントリと yaml diff をユーザーに表示:

> Here's what I'll append and update. Good to commit?

## このスキルがしないこと

- 過去の履歴エントリを編集する。訂正は、過去のエントリを参照して訂正する新規エントリです。
- 黙ってログを変更する。すべてのフィールド変更は書き込み前にユーザーに表示されます。
- 新規進展が引当/開示に値するかを決定する。質問を表面化(「これは重要性を押し進める可能性 — 再分類しますか?」)、ユーザーが答えます。
