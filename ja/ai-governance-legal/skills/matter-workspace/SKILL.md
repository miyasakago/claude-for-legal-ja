---
name: matter-workspace
description: >
  Manage matter workspaces — new, list, switch, close, or detach (practice-level).
  File-management logic for keeping one client or engagement's context separate
  from every other. Use when working across multiple clients or matters, when the
  user says "new matter", "switch matter", "list matters", "close matter", or when
  any substantive skill needs to know which matter it's working in.
  案件ワークスペースの管理 — new, list, switch, close, または detach(プラクティスレベル)。
  あるクライアントまたはエンゲージメントのコンテキストを他から分離するための
  ファイル管理ロジック。複数のクライアントまたは案件にまたがって作業しているとき、
  または実質的なスキルがどの案件で作業しているかを知る必要があるときに使用します。
  日本語トリガー: 新規案件、案件切り替え、案件一覧、案件クローズ、ワークスペース管理。
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件にまたがって作業します。案件ワークスペースは、1 つのクライアントまたはエンゲージメントのコンテキストを他から分離します。このスキルはそれらのワークスペースを管理します。

## サブコマンド

- `/ai-governance-legal-ja:matter-workspace new <slug>` — 新しい案件ワークスペースを作成、短いインテークを実行、`matter.md` を書き込み
- `/ai-governance-legal-ja:matter-workspace list` — ステータスとアクティブフラグ付きで案件をリスト
- `/ai-governance-legal-ja:matter-workspace switch <slug>` — アクティブ案件を設定
- `/ai-governance-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/_archived/` に移動、決して削除しない)
- `/ai-governance-legal-ja:matter-workspace none` — 任意のアクティブ案件から切り離し、プラクティスレベルでのみ作業

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます — `## Matter workspaces` セクションがポピュレートされていることを確認します。`Enabled` が `✗` の場合、ユーザーに伝えます: 「案件ワークスペースはオフ — 1 クライアントのインハウスプラクティスとして設定されているため、プラグインはプラクティスレベルのコンテキストから自動的に動作します。実際に複数のクライアントにまたがって作業する場合は、`/ai-governance-legal-ja:cold-start-interview --redo` を再実行し、private-practice 設定を選択してください。そうでなければ、`/matter-workspace` は必要ありません。」エラーにしないこと — 無効状態はインハウスユーザーにとって期待される状態です。
2. 下記のワークフローを使用。
3. `$ARGUMENTS` の最初のトークンで分岐:
   - `new` → インテークインタビューを実行、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/<slug>/matter.md` を書く、`history.md` と `notes.md` をシード。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/*/matter.md` を列挙、表を印刷、アクティブ案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/_archived/<slug>/` に移動、`history.md` にクローズ日付をログ。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. 書き込み前にユーザーに変更内容を表示し、確認します。

## 注記

- `Cross-matter context` がプラクティスレベル CLAUDE.md で `on` でない限り、スキルは決して案件をまたいで読みません。
- アーカイブは削除ではありません — クローズされた案件は保持/コンフリクト目的で読み取り可能のままです。
- Slug はハイフン付きの小文字です。slug がアーカイブとアクティブで再使用された場合、アーカイブされたものは `_archived/<slug>/` の下に保存されます。

---

マルチクライアント実務家(私的執務 — solo, small firm, large firm)は多くの案件にまたがって作業します。1 つのコンテキストが別のものに漏れてはいけません。このスキルは、それを真実にする薄いファイル管理層です。

**デフォルト状態はオフ。** インハウスユーザーはこれを見ません — プラクティスレベルでのみ実行します。案件ワークスペースは、private-practice ユーザーの場合 cold-start でオンになる、またはプラクティスレベル CLAUDE.md の `## Matter workspaces` を編集することでオンになります。`Enabled` が `✗` の場合、このスキルは実行されません。上のワークフローが無効状態を説明し、実際に案件分離を必要とするユーザーのために `/ai-governance-legal-ja:cold-start-interview --redo` を提案します。

## ストレージレイアウト

すべての案件データは以下に置かれます:

```
~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/
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

Slug はハイフン付きの小文字です。例: `acme-msa-2026`、`zenith-renewal`、`vendor-xyz-nda`。

## アクティブ案件はプラクティス CLAUDE.md にある

プラクティスレベル CLAUDE.md の `## Matter workspaces` の下の `Active matter:` 行が唯一の真実のソースです。案件の切り替えはその行を編集します。別の状態ファイルはありません。

## サブコマンドロジック

### `new <slug>`

1. Slug が `matters/<slug>/` または `matters/_archived/<slug>/` に既に存在しないことを確認します。再使用された場合、別の slug を選ぶようユーザーに尋ねます。
2. インテークインタビューを実行:
   - **Client**(我々が代理する当事者、またはインハウスの場合は内部事業ユニット)
   - **Counterparty**(相手方 — 複数の場合あり)
   - **Matter type**(プラグインのプラクティスプロファイルで典型的なカテゴリを読む; ai-governance-legal-ja の場合: ユースケース(内部) | ベンダー AI レビュー | AIA | 規制変更 | ポリシープロジェクト | その他)
   - **Confidentiality level**(standard | heightened | clean-team — heightened はクロス案件設定で追加のケアを促します)
   - **Key facts**(2〜5 文: この案件は何についてか、ステークホルダーは誰か、何が問題か)
   - **Matter-specific overrides to the practice playbook**(例: 「クライアントは 12 か月ではなく 24 か月の LoL キャップを要求」、「相手方は戦略的パートナー — 関係保全的トーン」)
   - **Related matters**(関連する案件の slug)
3. 下記のテンプレートを使用して `matters/<slug>/matter.md` を書きます。
4. `matters/<slug>/history.md` を単一の「Opened」エントリでシードします。
5. 空の `matters/<slug>/notes.md` を作成します。
6. 新しい案件に自動切り替え **しない** こと。尋ねます: 「今 `<slug>` に切り替えますか?(`/ai-governance-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙します。各ファイルのフロントマターまたは最初の数行を読んでステータスを抽出します。表を印刷:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現在アクティブな案件を `*` でマークします。存在する場合は別の「Archived」見出しの下に `_archived/*` を含めます。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認します。存在しない場合は `/ai-governance-legal-ja:matter-workspace new <slug>` を提供します。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集します。
3. ユーザーに matter.md のサマリーを表示し、正しい案件にいることを確認できるようにします。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認します。
2. 今日の日付で `matters/<slug>/history.md` に「Closed」エントリを追加します。
3. `matters/<slug>/` → `matters/_archived/<slug>/` に移動します。
4. クローズされた案件がアクティブ案件だった場合、`Active matter:` を `none — practice-level context only` に設定します。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` に設定します。ユーザーに確認します。

## `matter.md` テンプレート

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — ロールにより異なる; プラクティスレベル CLAUDE.md の `## Who's using this` を参照]

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

[2〜5 文。この案件は何についてか。ステークホルダーは誰か。何が問題か。デフォルトプレイブックと何が異なるか。]

## Matter-specific overrides

*この案件にのみ適用され、プラクティスレベルプレイブックからの逸脱。*

- [例: 「LoL キャップ: クライアントは 12 か月のハウス標準ではなく 24 か月を要求。」]
- [例: 「トーン: 関係保全的 — 相手方は戦略的パートナー。」]
- [例: 「準拠法: デラウェアではなく英国法でなければならない。」]

## Related matters

- [slug — 関連の理由を 1 行]

## Notes on confidentiality

[heightened または clean-team の場合、理由を記述。誰が案件ファイルを見ることができるか。グローバルにオンの場合でも、クロス案件コンテキストが許容されるかどうか。]
```

## `history.md` シード

```markdown
# History: [Client] — [short description]

追記専用イベントログ。最新が上。

---

## [YYYY-MM-DD] — Matter opened

インテーク完了。Slug: `[slug]`。Status: active。
[matter.md を超えて保存する価値のある初期コンテキスト — 例: 「[counterparty] からの inbound MSA ドラフトに応えて開設」]
```

## Cross-matter context (案件横断コンテキスト)

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがあります。`off`(デフォルト)の場合、案件 A で作業するスキルは他の任意の `B` について `matters/B/` のファイルを **決して読みません**。期間。これは、設定が提供するために存在する機密性保証です。

`on` の場合、スキルは、ユーザーが明示的に依頼したときに限り、案件フォルダーをまたいでファイルを読むことができます(例: 「過去 5 件のベンダー案件にわたって責任キャップに関する我々のポジションを比較してください」)。`on` の場合でも、ユーザーがクロス案件ビューを依頼しない限り、デフォルトはアクティブ案件のみのロードです。

## このスキルがしないこと

- **コンフリクトチェックを実行。** コンフリクトは実務家/事務所の仕事; インテークはユーザーが宣言したものをキャプチャします。
- **保持を強制。** クローズすると案件はアーカイブされる; 削除されません。保持ポリシーは範囲外です。
- **出力を自動ルーティング。** 実質的なスキルがどこに書くかを決める; このスキルは *どのフォルダー* がアクティブかを伝えますが、何をそこに入れるかは伝えません。
- **クロス案件が適切かどうかを判断。** フラグを読み、従います。
