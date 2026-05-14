---
name: matter-workspace
description: マルチクライアントプラクティスの案件ワークスペースを管理 — アクティブ案件の作成、一覧、切り替え、クローズ、デタッチを行います。Use when the user wants to create a new matter workspace, switch the active matter, list matters, archive a matter, or work at practice-level only without an active matter. 日本語トリガー: 案件ワークスペース、案件作成、案件切り替え、案件一覧、案件クローズ
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件を跨いで作業します。案件ワークスペースは、1 つのクライアントまたはエンゲージメントのコンテキストを他のすべてと分けて保ちます。このコマンドはそのワークスペースを管理します。

## サブコマンド

- `/litigation-legal-ja:matter-workspace new <slug>` — 新規案件ワークスペースを作成、短い intake を実行、`matter.md` を書き出し
- `/litigation-legal-ja:matter-workspace list` — ステータスとアクティブフラグ付きで案件をリストアップ
- `/litigation-legal-ja:matter-workspace switch <slug>` — アクティブ案件を設定
- `/litigation-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_archived/` に移動、削除しない)
- `/litigation-legal-ja:matter-workspace none` — アクティブ案件からデタッチ、プラクティスレベルのみで作業

注: `/litigation-legal-ja:matter-briefing [slug]`(サブコマンドなし)は別のコマンドで、特定の案件についてのブリーフィングを生成します — 社内ポートフォリオレビュー向け。案件ワークスペース管理はここに住みます。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` を読み、`## 案件ワークスペース` セクションが入力されていることを確認。`Enabled` が `✗` なら、ユーザーに伝える: 「Matter workspaces are off — you're configured as an in-house practice with one client, so the plugin works from practice-level context automatically. If you actually work across multiple clients, re-run `/litigation-legal-ja:cold-start-interview --redo` and select a private-practice setting. Otherwise, you don't need `/matter-workspace` at all.」 エラーにしない — 社内ユーザーには disabled が期待状態です。
2. 以下のワークフローと参照に従う。
3. `$ARGUMENTS` の最初のトークンでディスパッチ:
   - `new` → intake インタビューを実行、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/<slug>/matter.md` を書き出し、`history.md` と `notes.md` を初期化。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/*/matter.md` を列挙、テーブル印刷、アクティブ案件にマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_archived/<slug>/` に移動、`history.md` にクローズ日を記録。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. 何が変わるかをユーザーに表示し、書き込む前に確認。

## 注

- スキルはプラクティスレベル CLAUDE.md で `Cross-matter context` が `on` でない限り、案件を跨いで読みません。
- アーカイブは削除ではありません — クローズした案件は保持/コンフリクトのために読み取り可能のまま残ります。
- Slug は小文字とハイフン。アーカイブとアクティブで slug が再利用される場合、アーカイブされたものは `_archived/<slug>/` の下に保持されます。

---

# 案件ワークスペース

マルチクライアント実務家(プライベートプラクティス — ソロ、小規模事務所、大規模事務所)は多数の案件を跨いで作業します。あるもののコンテキストは別のものに漏れてはいけません。このスキルは、それを真にするための薄いファイル管理レイヤーです。

**デフォルト状態は off。** 社内ユーザーはこれを目にしません — プラクティスレベルのみで実行します。案件ワークスペースは、プライベートプラクティスユーザーのコールドスタートで、またはプラクティスレベル CLAUDE.md の `## 案件ワークスペース` を編集することで on になります。`Enabled` が `✗` なら、このスキルは実行しません;`/matter-workspace` スキルは disabled 状態を説明し、実際に案件分離が必要なユーザーには `/cold-start-interview --redo` を提案します。

## ストレージレイアウト

すべての案件データは以下の下に住みます:

```
~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/
├── CLAUDE.md                       # プラクティスレベルのプラクティスプロファイル
└── matters/
    ├── <slug>/
    │   ├── matter.md               # クライアント、相手方、案件種別、キーファクト、オーバーライド
    │   ├── history.md              # イベント、決定、ドラフト、レビューの日付付きログ
    │   ├── notes.md                # 自由形式の作業ノート
    │   └── outputs/                # この案件のためのスキル出力(任意のサブフォルダ)
    └── _archived/
        └── <slug>/                 # クローズした案件 — 読めるがアクティブではない
```

Slug は小文字とハイフン。例: `acme-msa-2026`、`zenith-renewal`、`vendor-xyz-nda`。

## アクティブ案件はプラクティス CLAUDE.md にある

プラクティスレベル CLAUDE.md の `## 案件ワークスペース` 下の `Active matter:` 行が唯一の真実の情報源です。案件を切り替えるとはその行を編集すること。別の状態ファイルはありません。

## サブコマンドのロジック

### `new <slug>`

1. Slug が `matters/<slug>/` または `matters/_archived/<slug>/` に既に存在しないか確認。再利用されているなら、別の slug を選ぶようユーザーに依頼。
2. intake インタビューを実行:
   - **Client**(代理する当事者、または社内ならビジネスユニット)
   - **Counterparty**(相手 — 複数可)
   - **Matter type**(プラグインのプラクティスプロファイルを読んで典型カテゴリを取得; litigation-legal-ja の場合: 契約紛争 | 雇用 | IP | 規制 / 調査 | 製造物責任 | 集団訴訟 | その他)
   - **Confidentiality level**(standard | heightened | clean-team — heightened はクロス案件設定で追加の注意を促します)
   - **Key facts**(2-5 文: この案件が何についてか、誰がステークホルダーか、何が懸かっているか)
   - **Matter-specific overrides to the practice playbook**(例: 「client requires 24-month LoL cap not 12」、「counterparty is a strategic partner — relationship-preserving tone」)
   - **Related matters**(関連する任意の案件の slug)
3. 下記テンプレートを使って `matters/<slug>/matter.md` を書き出し。
4. `matters/<slug>/history.md` を単一の "Opened" エントリで初期化。
5. 空の `matters/<slug>/notes.md` を作成。
6. 新規案件に**自動切り替えしない**。尋ねる: 「Want to switch to `<slug>` now? (`/litigation-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙。各ファイルのフロントマターまたは最初の数行を読んでステータスを抽出。テーブル印刷:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現在アクティブな案件に `*` でマーク。`_archived/*` が存在する場合は別の「Archived」見出しの下に含める。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認。なければ `/litigation-legal-ja:matter-workspace new <slug>` を提案。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. ユーザーに matter.md サマリーを表示して、正しい案件にいるか確認。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認。
2. 今日の日付で「Closed」エントリを `matters/<slug>/history.md` に追記。
3. `matters/<slug>/` → `matters/_archived/<slug>/` に移動。
4. クローズした案件がアクティブ案件だった場合、`Active matter:` を `none — practice-level context only` に設定。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` に設定。ユーザーと確認。

## `matter.md` テンプレート

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者` in the practice-level CLAUDE.md]

# Matter: [Client] — [short description]

**Slug:** [slug]
**Opened:** [YYYY-MM-DD]
**Status:** active
**Confidentiality:** [standard / heightened / clean-team]

---

## Parties

**Client:** [name]
**Counterparty:** [name(s)]

## Matter type

[vendor MSA | customer agreement | NDA | SaaS subscription | amendment | renewal | other — 1 行の根拠付き]

## Key facts

[2-5 文。この案件が何についてか。誰がステークホルダーか。何が懸かっているか。デフォルトプレイブックと何が違うか。]

## Matter-specific overrides

*この案件にのみ適用される、プラクティスレベルプレイブックからの逸脱。*

- [例: "LoL cap: client requires 24 months, not house standard 12."]
- [例: "Tone: relationship-preserving — counterparty is a strategic partner."]
- [例: "Governing law: must be English law, not Delaware."]

## Related matters

- [slug — 1 行関連理由]

## Notes on confidentiality

[heightened または clean-team なら、理由を記述。誰が案件ファイルを見られるか。グローバルに on でもクロス案件コンテキストが許容されるか。]
```

## `history.md` シード

```markdown
# History: [Client] — [short description]

追記専用イベントログ。最新を上に。

---

## [YYYY-MM-DD] — Matter opened

Intake completed. Slug: `[slug]`. Status: active.
[matter.md を超えて保持に値する初期コンテキスト — 例: "Opened in response to inbound MSA draft from [counterparty]."]
```

## クロス案件コンテキスト

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがあります。`off`(デフォルト)のとき、案件 A で動作するスキルは、他の `B` について `matters/B/` のファイルを**決して読まない**。終わり。これは、設定が提供するために存在する機密性保証です。

`on` のとき、スキルはユーザーが明示的に依頼したとき(例: 「過去 5 件のベンダー案件で責任上限に関する立場を比較」)に限り、案件フォルダを跨いでファイルを読めます。`on` でも、デフォルトはアクティブ案件のみをロードし、ユーザーがクロス案件ビューを依頼しない限りそうします。

## このスキルがしないこと

- **コンフリクトチェックを実行する。** コンフリクトは実務家/事務所の仕事;intake はユーザーが宣言したものをキャプチャします。
- **保持を強制する。** クローズは案件をアーカイブする;削除しません。保持ポリシーはスコープ外。
- **出力を自動ルーティング。** 実質的なスキルがどこに書くか決定;このスキルは*どのフォルダ*がアクティブかを伝え、何を入れるかは伝えません。
- **クロス案件が適切かを決定。** フラグを読み、従います。
