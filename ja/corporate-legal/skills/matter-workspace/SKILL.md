---
name: matter-workspace
description: >
  案件ワークスペースの管理 — アクティブな案件の作成、一覧、切替、クローズ、デタッチ。
  マルチクライアントの実務家が、あるクライアントのコンテキストを他のクライアントから分離するために使用します。
  どの案件で作業中かを知る必要のある実質的スキルが本ワークスペースを参照します。
  Use when user says "new matter", "switch matter", "list matters", "close matter",
  or wants to work at practice-level only.
  日本語トリガー: 「新規案件」「案件切替」「案件一覧」「案件クローズ」、プラクティスレベルのみで作業したい場合。
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件を横断して作業します。案件ワークスペースは、1 つのクライアントまたはエンゲージメントのコンテキストを他のものから分離します。本スキルはそれらのワークスペースを管理します。

## サブコマンド

- `/corporate-legal-ja:matter-workspace new <slug>` — 新規案件ワークスペースを作成、簡単な intake を実行、`matter.md` を書き出す
- `/corporate-legal-ja:matter-workspace list` — ステータスとアクティブフラグ付きで案件をリスト
- `/corporate-legal-ja:matter-workspace switch <slug>` — アクティブ案件を設定
- `/corporate-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/_archived/` に移動、削除はしない)
- `/corporate-legal-ja:matter-workspace none` — 任意のアクティブ案件からデタッチ、プラクティスレベルのみで作業

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` を読む — `## Matter workspaces` セクションが populated されていることを確認。`Enabled` が `✗` の場合、ユーザーに伝える:「案件ワークスペースはオフ — あなたは 1 クライアントのインハウス実務として構成されており、プラグインは自動的にプラクティスレベルコンテキストから動作します。実際に複数クライアントで作業している場合、`/corporate-legal-ja:cold-start-interview --redo` を再実行して private-practice 設定を選んでください。そうでなければ `/matter-workspace` は全く必要ありません。」エラーにしない — 無効状態はインハウスユーザーにとって想定状態。
2. 以下のワークフローを使用。
3. `$ARGUMENTS` の最初のトークンで dispatch:
   - `new` → intake インタビュー実行、`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<slug>/matter.md` 書き出し、`history.md` と `notes.md` を seed。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/*/matter.md` を列挙、テーブルを表示、アクティブ案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/_archived/<slug>/` に移動、`history.md` にクローズ日をログ。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. ユーザーに変更内容を表示し、書き込み前に確認。

## ノート

- スキルは、プラクティスレベル CLAUDE.md で `Cross-matter context` が `on` でない限り、案件を横断して読まない。
- アーカイブは削除ではない — クローズした案件は retention / コンフリクト目的で引き続き読める。
- Slug は小文字とハイフン。アーカイブとアクティブで slug が再利用された場合、アーカイブされたものは `_archived/<slug>/` 配下で保持される。

---

マルチクライアント実務家(私的実務 — ソロ、小規模事務所、大規模事務所)は多くの案件を横断して作業。1 案件のコンテキストが別案件に漏れてはいけない。本スキルはそれを真にする薄いファイル管理レイヤー。

**デフォルト状態は off。** インハウスユーザーには表示されない — プラクティスレベルのみで動作。案件ワークスペースは私的実務ユーザーのコールドスタート時、またはプラクティスレベル CLAUDE.md の `## Matter workspaces` を編集することで有効化。`Enabled` が `✗` なら、本スキルは実行されない;`/corporate-legal-ja:matter-workspace` は無効状態を説明し、実際に案件分離が必要なユーザーに `/corporate-legal-ja:cold-start-interview --redo` を提案。

## ストレージレイアウト

すべての案件データは以下に存在:

```
~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/
├── CLAUDE.md                       # practice-level practice profile
└── matters/
    ├── <slug>/
    │   ├── matter.md               # client, counterparty, matter type, key facts, overrides
    │   ├── history.md              # dated log of events, decisions, drafts, reviews
    │   ├── notes.md                # free-form working notes
    │   └── outputs/                # skill outputs for this matter (optional subfolder)
    └── _archived/
        └── <slug>/                 # closed matters — readable but not active
```

Slug は小文字とハイフン。例:`acme-msa-2026`、`zenith-renewal`、`vendor-xyz-nda`。

## アクティブ案件はプラクティス CLAUDE.md に

プラクティスレベル CLAUDE.md の `## Matter workspaces` 配下の `Active matter:` 行が真実の単一ソース。案件切替は当該行を編集。別途の状態ファイルはない。

## サブコマンドのロジック

### `new <slug>`

1. slug がすでに `matters/<slug>/` または `matters/_archived/<slug>/` に存在しないことを確認。再利用ならユーザーに別 slug を選ぶよう依頼。
2. intake インタビューを実行:
   - **Client**(代理する当事者、またはインハウスなら内部ビジネスユニット)
   - **Counterparty**(相手方 — 複数の可能性あり)
   - **Matter type**(プラグインのプラクティスプロファイルから典型カテゴリを読む;corporate-legal の場合:M&A buy-side | M&A sell-side | financing | board matter | entity reorg | integration project | other)
   - **Confidentiality level**(standard | heightened | clean-team — heightened はクロスマター設定で余計な注意を促す)
   - **Key facts**(2〜5 文:この案件が何について、ステークホルダーは誰、何が懸かっているか)
   - **Matter-specific overrides to the practice playbook**(例:「クライアントは LoL cap を 12 か月ではなく 24 か月要求」「相手方は戦略パートナー — 関係保持トーン」)
   - **Related matters**(接続案件の slug)
3. テンプレートを使って `matters/<slug>/matter.md` を書く。
4. `matters/<slug>/history.md` を "Opened" エントリ 1 件で seed。
5. 空の `matters/<slug>/notes.md` を作成。
6. 新案件に **自動切替しない**。聞く:「Want to switch to `<slug>` now? (`/corporate-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙。各ファイルのフロントマターまたは最初の数行を読み、ステータスを抽出。テーブルを表示:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現アクティブ案件を `*` でマーク。`_archived/*` があれば別途 "Archived" 見出し配下に含める。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認。なければ `/corporate-legal-ja:matter-workspace new <slug>` を提案。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. ユーザーに matter.md サマリーを表示し、正しい案件かを確認できるようにする。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認。
2. `matters/<slug>/history.md` に今日の日付で "Closed" エントリを append。
3. `matters/<slug>/` を `matters/_archived/<slug>/` に移動。
4. クローズした案件がアクティブだった場合、`Active matter:` を `none — practice-level context only` に設定。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` に設定。ユーザーに確認。

## `matter.md` テンプレート

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this` in the practice-level CLAUDE.md]

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

[vendor MSA | customer agreement | NDA | SaaS subscription | amendment | renewal | other — with one-line rationale]

## Key facts

[2–5 sentences. What this matter is about. Who the stakeholders are. What's at stake. What makes it different from the default playbook.]

## Matter-specific overrides

*Any deviation from the practice-level playbook that applies to this matter and only this matter.*

- [e.g., "LoL cap: client requires 24 months, not house standard 12."]
- [e.g., "Tone: relationship-preserving — counterparty is a strategic partner."]
- [e.g., "Governing law: must be English law, not Delaware."]

## Related matters

- [slug — one line why related]

## Notes on confidentiality

[If heightened or clean-team, describe why. Who may see matter files. Whether cross-matter context is permissible even if globally on.]
```

## `history.md` seed

```markdown
# History: [Client] — [short description]

Append-only event log. Most recent at top.

---

## [YYYY-MM-DD] — Matter opened

Intake completed. Slug: `[slug]`. Status: active.
[Any initial context worth preserving beyond matter.md — e.g., "Opened in response to inbound MSA draft from [counterparty]."]
```

## Cross-matter context

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがある。`off`(デフォルト)時、案件 A で作業中のスキルは他の `B` の `matters/B/` のファイルを**絶対に読まない**。終わり。これは本設定が提供する秘密性保証。

`on` 時、スキルはユーザーが明示的に依頼した場合のみ案件フォルダ横断でファイルを読める(例:「過去 5 件のベンダー案件について責任上限額についての我々のポジションを比較」)。`on` でもデフォルトはアクティブ案件のみロードし、ユーザーがクロスマタービューを求めた場合のみそれ以外を読む。

## 本スキルがしないこと

- **コンフリクトチェックの実行。** コンフリクトは実務家 / 事務所の仕事;intake はユーザーが申告するものを取得。
- **保管期間の強制。** クローズは案件をアーカイブ;削除しない。保管ポリシーはスコープ外。
- **出力の自動ルーティング。** 実質的スキルが書き込み先を決定;本スキルは*どのフォルダ*がアクティブかを伝えるが、何を入れるかは決めない。
- **クロスマターが適切かを判断。** フラグを読み、それに従う。
