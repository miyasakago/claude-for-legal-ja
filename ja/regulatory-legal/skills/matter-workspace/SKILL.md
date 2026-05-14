---
name: matter-workspace
description: 案件ワークスペースを管理 — アクティブな案件を新規作成、リスト、切替、close、または detach(プラクティスレベル)。複数のクライアントや案件をまたいで作業し、1 つの engagement のコンテキストを別のものから分離したいとき、または実質スキルが動作中の案件を知る必要があるときに使用。Use when working across multiple clients or matters and you need to keep one engagement's context separate from another, or when a substantive skill needs to know which matter it's working in. 日本語トリガー: 案件ワークスペース、案件管理、案件切替、新規案件。
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件をまたいで作業します。案件ワークスペースは、1 つのクライアントまたは engagement のコンテキストを他のあらゆるものから分離します。このスキルはそれらのワークスペースを管理します。

## サブコマンド

- `/regulatory-legal-ja:matter-workspace new <slug>` — 新しい案件ワークスペースを作成、短いインテイクを実行、`matter.md` を書く
- `/regulatory-legal-ja:matter-workspace list` — status とアクティブフラグ付きで案件をリスト
- `/regulatory-legal-ja:matter-workspace switch <slug>` — アクティブな案件をセット
- `/regulatory-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/_archived/` に移動、決して削除しない)
- `/regulatory-legal-ja:matter-workspace none` — 任意のアクティブな案件から detach、プラクティスレベルのみで作業

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` を読む — `## Matter workspaces` セクションが populated されていることを確認。`Enabled` が `✗` の場合、ユーザーに伝える: "Matter workspaces are off — you're configured as an in-house practice with one client, so the plugin works from practice-level context automatically. If you actually work across multiple clients, re-run `/regulatory-legal-ja:cold-start-interview --redo` and select a private-practice setting. Otherwise, you don't need `/matter-workspace` at all." エラーにしない — disabled 状態は in-house ユーザーで期待されるもの。
2. 下記のファイル管理ロジックを使う。
3. `$ARGUMENTS` の最初のトークンでディスパッチ:
   - `new` → インテイクインタビューを実行、`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/<slug>/matter.md` を書く、`history.md` と `notes.md` を seed。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/*/matter.md` を列挙、テーブルを表示、アクティブな案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/_archived/<slug>/` に移動、`history.md` に close 日をログ。
   - `none` → `Active matter:` を `none — practice-level context only` にセット。
4. 何が変わったかをユーザーに表示し、書き込み前に確認。

## 注記

- プラクティスレベル CLAUDE.md で `Cross-matter context` が `on` でない限り、このスキルは決して案件をまたいで読まない。
- アーカイブは削除ではない — closed 案件は retention/conflicts 目的で読み取り可能のまま。
- slug は小文字とハイフン。slug がアーカイブとアクティブで再利用される場合、アーカイブされたものは `_archived/<slug>/` の下に保存される。

---

マルチクライアントの実務家(私的実務 — solo、小規模事務所、大規模事務所)は多くの案件をまたいで作業する。1 つのコンテキストは別のものに漏れてはならない。このスキルは、それを実現する thin なファイル管理層です。

**デフォルト状態は off。** in-house ユーザーは見ない — プラクティスレベルのみで動作。案件ワークスペースは私的実務ユーザーのコールドスタートで turn on するか、プラクティスレベル CLAUDE.md の `## Matter workspaces` を編集してオン。`Enabled` が `✗` ならこのスキルは動作しない;disabled 状態を説明し、実際に案件分離が必要なユーザーには `/regulatory-legal-ja:cold-start-interview --redo` を提案する。

## ストレージレイアウト

すべての案件データは以下の下に置かれる:

```
~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/
├── CLAUDE.md                       # プラクティスレベルのプラクティスプロファイル
└── matters/
    ├── <slug>/
    │   ├── matter.md               # クライアント、相手方、案件タイプ、key facts、オーバーライド
    │   ├── history.md              # イベント、決定、ドラフト、レビューの日付付きログ
    │   ├── notes.md                # 自由形式の作業メモ
    │   └── outputs/                # この案件のスキル出力(オプションのサブフォルダ)
    └── _archived/
        └── <slug>/                 # closed 案件 — 読み取り可能、ただしアクティブではない
```

slug は小文字とハイフン。例: `acme-msa-2026`、`zenith-renewal`、`vendor-xyz-nda`。

## アクティブな案件はプラクティス CLAUDE.md にある

プラクティスレベル CLAUDE.md の `## Matter workspaces` の下の `Active matter:` 行が single source of truth。案件の switching はその行を編集する。別の state ファイルなし。

## サブコマンドのロジック

### `new <slug>`

1. slug が `matters/<slug>/` または `matters/_archived/<slug>/` に既に存在しないことを確認。再利用なら、別の slug を選ぶようユーザーに求める。
2. インテイクインタビューを実行:
   - **Client**(我々が代理する party、または in-house なら社内ビジネスユニット)
   - **Counterparty**(相手方 — 複数の場合あり)
   - **Matter type**(典型カテゴリーはプラグインのプラクティスプロファイルから読む;regulatory-legal の場合: rulemaking | comment period | gap remediation | agency inquiry | enforcement response | standing topic | other)
   - **Confidentiality level**(standard | heightened | clean-team — heightened は cross-matter 設定で特別な注意を促す)
   - **Key facts**(2-5 文: この案件は何について、ステークホルダーは誰か、何が at stake か)
   - **プラクティスプレイブックへの案件固有オーバーライド**(例: "client requires 24-month LoL cap not 12"、"counterparty is a strategic partner — relationship-preserving tone")
   - **Related matters**(関連する案件の slug)
3. 下記のテンプレートを使い `matters/<slug>/matter.md` を書く。
4. `matters/<slug>/history.md` を単一の "Opened" エントリで seed。
5. 空の `matters/<slug>/notes.md` を作成。
6. 新案件に **auto-switch しない**。聞く: "Want to switch to `<slug>` now? (`/regulatory-legal-ja:matter-workspace switch <slug>`)"

### `list`

`matters/*/matter.md` を列挙。各ファイルのフロントマターまたは最初の数行を読んで status を抽出。テーブルを表示:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現在のアクティブな案件を `*` でマーク。`_archived/*` が存在すれば別の "Archived" 見出しの下に含める。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認。なければ `/regulatory-legal-ja:matter-workspace new <slug>` を提案。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. ユーザーに matter.md サマリを表示し、正しい案件にいることを確認できるようにする。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認。
2. `matters/<slug>/history.md` に今日の日付で "Closed" エントリを追加。
3. `matters/<slug>/` → `matters/_archived/<slug>/` に移動。
4. close された案件がアクティブな案件だったなら、`Active matter:` を `none — practice-level context only` にセット。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` にセット。ユーザーに確認。

## `matter.md` テンプレート

```markdown
[WORK-PRODUCT HEADER — プラグイン config ## Outputs に従う — role により異なる;プラクティスレベル CLAUDE.md の `## Who's using this` 参照]

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

[vendor MSA | customer agreement | NDA | SaaS subscription | amendment | renewal | other — 1 行の rationale 付き]

## Key facts

[2-5 文。この案件は何について。ステークホルダーは誰か。何が at stake か。デフォルトプレイブックとどう違うか。]

## Matter-specific overrides

*この案件にのみ適用される、プラクティスレベルプレイブックからの逸脱。*

- [例: "LoL cap: client requires 24 months, not house standard 12."]
- [例: "Tone: relationship-preserving — counterparty is a strategic partner."]
- [例: "Governing law: must be English law, not Delaware."]

## Related matters

- [slug — 関連理由を一行]

## Confidentiality に関する注記

[heightened または clean-team の場合、理由を記述。誰が案件ファイルを見られるか。globally on でも cross-matter context が許容されるか。]
```

## `history.md` の seed

```markdown
# History: [Client] — [short description]

追記専用のイベントログ。最新が上。

---

## [YYYY-MM-DD] — 案件オープン

インテイク完了。Slug: `[slug]`。Status: active。
[matter.md を超えて保存価値のある初期コンテキスト — 例: "[counterparty] からの inbound MSA draft に対応して開始。"]
```

## Cross-matter context

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがある。`off`(デフォルト)のとき、案件 A で作業するスキルは、任意の他の `B` について `matters/B/` のファイルを **決して読まない**。完全に。これは、この設定が提供する confidentiality guarantee です。

`on` のとき、スキルはユーザーが明示的に求めた場合に限り、案件フォルダをまたいで読む(例: "compare our position on liability caps across the last five vendor matters")。`on` でも、ユーザーが cross-matter ビューを求めない限り、デフォルトはアクティブな案件のみをロードすること。

## このスキルがやらないこと

- **Conflicts check を実行。** Conflicts は実務家/事務所の仕事;インテイクはユーザーが宣言したものをキャプチャ。
- **Retention の強制。** クロージングは案件をアーカイブする;削除しない。Retention ポリシーは scope 外。
- **出力を auto-route。** 実質スキルがどこに書くかを決める;このスキルは *どのフォルダ* がアクティブかを伝えるが、何を入れるかは言わない。
- **Cross-matter が適切か判断する。** フラグを読み、従う。
