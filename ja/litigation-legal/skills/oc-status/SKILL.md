---
name: oc-status
description: アクティブポートフォリオ全体で外部弁護士向けの週次ステータス依頼メールのドラフトを生成 — 案件ごとのマークダウン、MCP 利用可能時は Gmail 下書きも。Use when the user asks for OC status requests, weekly outside counsel check-ins, or wants per-matter status emails drafted from the portfolio log. 日本語トリガー: OC ステータス、外部弁護士チェックイン、週次ステータス依頼
argument-hint: "[--all | --slug=foo | --no-gmail]"
---

# /oc-status

週次で実行するには、`/litigation-legal-ja:oc-status` を呼び出す定期リマインダーを設定してください。自動スケジューリングには scheduled-tasks 連携が必要で、同梱されていません。

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` をロード、デフォルトルール(またはフラグ)に従ってフィルタ。
2. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → 外部弁護士指示スタイル、署名者デフォルト、予算ポスチャ。
3. 以下のワークフローと参照に従う。
4. スコープ内の各案件について: `matter.md` + `history.md` を読み、案件ごとのメールを起案。
5. マークダウンを `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/oc-status/[YYYY-MM-DD]/[slug].md` に書き込み。
6. Gmail MCP 認証済みなら: Gmail 下書きを作成。それ以外: マークダウンのみ、サマリーに注記。
7. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/oc-status/[YYYY-MM-DD]/_summary.md` を書き出し — 実行内容、スキップ内容と理由。

---

# OC Status

## 目的

毎週 5-15 案件にわたって外部弁護士に同じステータス依頼メールを書くことは、機械的な認知税です。コンテンツは案件ごとに一貫(ステータス、保留中の決定、予算チェック)。オーディエンスも一貫(OC リードパートナー)。トーンも一貫(ハウスの外部弁護士指示スタイルに従って)。スケジュールタスクがそれらすべてを起案し、弁護士がレビューして送付。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — フィルタリングとフィールドの出典
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` — 案件コンテキスト(現在のポスチャ、未解決の質問)
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` — 何を尋ねるか情報を与える最近のイベント
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → 外部弁護士指示スタイル、署名者名/メール、予算ポスチャ

## フィルタリング — どの案件?

デフォルトフィルタ:

- `status != closed`
- `outside_counsel.firm != null` かつ `outside_counsel.lead != null`
- いずれか: 最終更新が 10 日超前(何かが起きるのに時間あり) または `next_deadline` が 21 日以内

過去 10 日以内にステータス更新があった案件はスキップ(再 ping 不要)、`outside_counsel.email` が null の案件はスキップ(Gmail 下書きにはメールアドレスが必要;マークダウンは引き続き生成)。

フラグ:
- `--all` → 鮮度に関係なくすべてのアクティブ案件のドラフト
- `--slug=[slug]` → 1 案件のみ起案(アドホックリクエスト)
- `--no-gmail` → MCP 利用可能でも Gmail 下書き作成をスキップ

## 案件ごとのメールドラフト

各メールは同じスケルトンを持ち;内容は案件固有。

**Subject:** ハウス慣例に従って(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 外部弁護士指示スタイルから;フォールバック: `[Matter: [matter name]] — Weekly status update`)

**Body スケルトン:**

```
[リードパートナー名(下の名前)],

[1 文のオープナー — 自然に、ハウストーンにマッチ。]

Checking in on [matter name]. A few items:

1. **Status since [history.md でキャプチャされた最終更新の日付]** — what's moved, what's pending? Any filings, hearings, correspondence, or calls since we last touched base?

2. **Upcoming deadlines** — I show [ログの next_deadline + matter.md の任意の期限]. Confirm coverage plan and any dates we should add.

3. **Decisions pending** — [OC 入力を要する matter.md からの未解決質問を引き出す; なければ、この番号付き項目を省略して再番号付け]

4. **Budget** — [`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 予算ポスチャに従って monthly / quarterly / on-request]. Where are we against [matter.md からの予算授権]? Any variance to flag?

[実質的かつ関連する場合: 5. Specific ask — 例: 「Please send me the latest draft of the motion to dismiss before [date]」 — matter.md 未解決質問から引き出す。]

[署名 — 名前、ロール、連絡先。OC 指示の `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 署名者デフォルトから。]
```

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 外部弁護士指示スタイルに従ってトーンを適応 — 「dear counsel」フォーマルなショップもあれば、ファーストネームと箇条書きのショップも。マッチ。

## 出力

### マークダウンドラフト

書き込み先: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/oc-status/[YYYY-MM-DD]/[slug].md`

各ファイルは 1 通のメール、フォーマット:

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

# [Matter name] — OC status request — [YYYY-MM-DD]

**To:** [ログの outside_counsel.email] ([outside_counsel.lead], [outside_counsel.firm])
**From:** [`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` からの署名者名 / メール]
**Subject:** [件名]

> 上の work-product header はこの内部記録に適用。下のアウトゴーイングメール本文は retainer 案件の外部弁護士に行き、それ自体が特権通信です — 送付されるメールの上部にハウスの特権マーキング(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 特権慣行)を適用、典型的には `Privileged & Confidential — Attorney-Client Communication / Attorney Work Product`、この内部 work-product header ではなく。

---

[スケルトンに従った本文]
```

### 送付ゲート(各ドラフトの締めくくり注記)

各マークダウンドラフトに以下を追記、本文の直下で実行メタデータの上 — 送付前に剥がす:

> This is a draft status email for attorney review before sending to outside counsel. Check for privileged content you did not intend to share outside the engagement circle, factual accuracy, tone, and budget posture. Do not send unreviewed — even routine weekly check-ins can surface theory, strategy, or concessions the sender didn't mean to put in writing.

### Gmail 下書き(MCP 利用可能の場合)

Gmail 下書き作成 MCP が認証済みなら:

- ユーザーの Gmail に `to`、`from`、`subject`、`body` を入れた案件ごとの下書きを作成
- 下書きは Drafts フォルダにあり;ユーザーが月曜朝にレビューして送付
- Gmail MCP が NOT 利用可能または失敗: マークダウンのみにフォールバックし、ユーザーに伝える

### 実行サマリー

すべての案件を処理後、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/oc-status/[YYYY-MM-DD]/_summary.md` を書き出し:

```markdown
# OC Status Run — [YYYY-MM-DD]

**Matters processed:** [N]
**Drafts created:** [N]
**Gmail drafts:** [created / skipped — 理由]

## ドラフト作成対象

| Matter | OC lead | Last updated | 含める理由 |
|---|---|---|---|
| [slug] | [lead] | [date] | [停滞 / 近接期限 / --all / --slug] |

## スキップ

| Matter | 理由 |
|---|---|
| [slug] | 最近の更新([date] に最終タッチ) |
| [slug] | ログに OC メールなし — `/matter-update [slug]` で更新 |

## 異常

- 外部弁護士未割当の案件: [リスト — high/critical リスクのものはフラグ]
- 外部弁護士ありだがログにメールなしの案件: [リスト]
```

## スケジューリング

このスキルは週次実行向けに設計されています。自動スケジューリングにはプラグインに同梱されていない scheduled-tasks 連携が必要です。週次で実行するには、`/litigation-legal-ja:oc-status` を呼び出す定期リマインダーを設定 — 例: 月曜朝のカレンダー。

アドホック: いつでも `/oc-status`。単一案件には `/oc-status --slug=foo`。

## このスキルがしないこと

- **メールを送付する。** ドラフトのみ。弁護士がレビューして送付。
- **持っていないコンテンツを生成。** `matter.md` が薄ければ、メールは短く、広範なステータス質問。スキルはゼロから具体的な質問を捏造しません。
- **失敗を再試行。** Gmail 下書き作成が実行中に失敗したら、スキルは失敗をログしてマークダウンで継続。ユーザーは認証修正後に再試行可能。
- **history.md を再書きする。** コンテキストのために読みますが、変更しません。(OC の応答が新規イベントを表面化したら、`/matter-update [slug]` でログを。)
- **最小テンプレートを強制する。** ハウストーンが「1 行、ファーストネーム、完」なら、ドラフトはそれに従い箇条書き構造をスキップ。`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` にマッチ。
