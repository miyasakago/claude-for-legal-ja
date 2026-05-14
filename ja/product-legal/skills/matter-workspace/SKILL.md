---
name: matter-workspace
description: >
  案件ワークスペースの管理 — 新規、一覧、切替、終了、または解除
  (プラクティスレベル)。
  (Use when working across multiple clients or matters in private practice and
   you need to create, list, switch, close, or detach the active matter so
   context from one engagement doesn't leak into another.
   日本語トリガー: 案件ワークスペース、案件管理、新規案件、案件切替、案件終了、
   案件分離)
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件にわたって作業します。案件ワークスペースは、1 つのクライアントまたはエンゲージメントのコンテキストを他のすべてから分離して保ちます。このスキルはそれらのワークスペースを管理します。

## サブコマンド

- `/product-legal-ja:matter-workspace new <slug>` — 新しい案件ワークスペースを作成、短いインテークを実行、`matter.md` を書く
- `/product-legal-ja:matter-workspace list` — ステータスとアクティブフラグ付きで案件をリスト
- `/product-legal-ja:matter-workspace switch <slug>` — アクティブな案件を設定
- `/product-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/product-legal-ja/matters/_archived/` に移動、決して削除しない)
- `/product-legal-ja:matter-workspace none` — アクティブな案件から切り離し、プラクティスレベルのみで作業

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/product-legal-ja/CLAUDE.md` を読む — `## Matter workspaces` セクションが記入されていることを確認。`Enabled` が `✗` の場合、ユーザーに伝える: 「案件ワークスペースはオフです — 1 つのクライアントを持つインハウスプラクティスとして構成されているため、プラグインはプラクティスレベルのコンテキストから自動的に動作します。複数のクライアントにわたって実際に作業するなら、`/product-legal-ja:cold-start-interview --redo` を再実行し、私的実務設定を選んでください。それ以外の場合、`/matter-workspace` は全く必要ありません。」エラーにしないこと — 無効状態はインハウスユーザーの期待される状態です。
2. 下記のストレージレイアウトとサブコマンドロジックを適用。
3. `$ARGUMENTS` の最初のトークンでディスパッチ:
   - `new` → インテークインタビューを実行、`~/.claude/plugins/config/claude-for-legal-ja/product-legal-ja/matters/<slug>/matter.md` を書き、`history.md` と `notes.md` をシード。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/product-legal-ja/matters/*/matter.md` を列挙し、表を印刷、アクティブな案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/product-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/product-legal-ja/matters/_archived/<slug>/` に移動し、`history.md` に終了日を記録。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. 何が変わったかをユーザーに表示し、書き込み前に確認。

## 注記

- このスキルは、プラクティスレベル CLAUDE.md で `Cross-matter context` が `on` でない限り、決して案件をまたいで読みません。
- アーカイブは削除ではありません — 終了した案件は保持/利害関係目的で読み取り可能のまま残ります。
- Slug は小文字とハイフン。slug がアーカイブ済みとアクティブで再利用される場合、アーカイブ済みのものは `_archived/<slug>/` の下に保存されます。

---

# Matter Workspace(案件ワークスペース)

マルチクライアント実務家(私的実務 — ソロ、小規模事務所、大規模事務所)は多くの案件にわたって作業します。1 つからのコンテキストは別のものに漏れてはなりません。このスキルは、それを真にする薄いファイル管理レイヤーです。

**デフォルト状態はオフ。** インハウスユーザーはこれを見ません — プラクティスレベルのみで動作します。案件ワークスペースは、私的実務ユーザー向けにコールドスタートで、またはプラクティスレベル CLAUDE.md の `## Matter workspaces` を編集することで有効になります。`Enabled` が `✗` の場合、このスキルは実行されません; `/matter-workspace` コマンドは無効状態を説明し、案件分離が実際に必要なユーザーに `/cold-start-interview --redo` を提案します。

## ストレージレイアウト

すべての案件データは以下の下に存在:

```
~/.claude/plugins/config/claude-for-legal-ja/product-legal-ja/
├── CLAUDE.md                       # プラクティスレベル プラクティスプロファイル
└── matters/
    ├── <slug>/
    │   ├── matter.md               # クライアント、相手方、案件タイプ、主要事実、オーバーライド
    │   ├── history.md              # 日付付きのイベント、決定、ドラフト、レビューのログ
    │   ├── notes.md                # 自由形式の作業ノート
    │   └── outputs/                # この案件のスキル出力(オプションサブフォルダ)
    └── _archived/
        └── <slug>/                 # 終了した案件 — 読み取り可能だがアクティブでない
```

Slug は小文字とハイフン。例: `acme-msa-2026`、`zenith-renewal`、`vendor-xyz-nda`。

## アクティブな案件はプラクティス CLAUDE.md にある

プラクティスレベル CLAUDE.md の `## Matter workspaces` の下の `Active matter:` 行が、唯一の真実の源です。案件の切替はその行を編集します。別の状態ファイルはありません。

## サブコマンドロジック

### `new <slug>`

1. slug が `matters/<slug>/` または `matters/_archived/<slug>/` にまだ存在しないことを確認。再利用なら、ユーザーに別の slug を選ぶよう尋ねます。
2. インテークインタビューを実行:
   - **クライアント**(代理する当事者、またはインハウスの場合は内部ビジネスユニット)
   - **相手方**(他方 — 複数の場合あり)
   - **案件タイプ**(典型的カテゴリについてプラグインのプラクティスプロファイルを読む。product-legal の場合: launch | feature review | marketing claim review | risk deep dive | product area (standing) | other)
   - **機密性レベル**(standard | heightened | clean-team — heightened は案件横断設定での余分な注意を促します)
   - **主要事実**(2〜5 文: この案件が何についてか、ステークホルダーは誰か、何が懸かっているか)
   - **プラクティスプレイブックへの案件固有のオーバーライド**(例: 「クライアントは 12 か月ではなく 24 か月の LoL キャップを要求」、「相手方は戦略パートナー — 関係維持的トーン」)
   - **関連案件**(接続された案件の slug)
3. 下記のテンプレートを使って `matters/<slug>/matter.md` を書く。
4. `matters/<slug>/history.md` に「Opened」エントリ 1 つでシード。
5. 空の `matters/<slug>/notes.md` を作成。
6. 新しい案件に自動切替**しない**。尋ねる: 「今 `<slug>` に切り替えますか?(`/product-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙。各ファイルのフロントマターまたは最初の数行を読み、ステータスを抽出。表を印刷:

| Slug | クライアント | 案件タイプ | ステータス | 開始日 | アクティブ |
|---|---|---|---|---|---|

現在アクティブな案件を `*` でマーク。アーカイブが存在する場合、`_archived/*` を別の「Archived」見出しの下に含める。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認。なければ、`/product-legal-ja:matter-workspace new <slug>` を提供。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. ユーザーに matter.md サマリーを表示し、正しい案件にいることを確認できるようにする。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認。
2. `matters/<slug>/history.md` に今日の日付付きで「Closed」エントリを追記。
3. `matters/<slug>/` → `matters/_archived/<slug>/` に移動。
4. 終了した案件がアクティブな案件だった場合、`Active matter:` を `none — practice-level context only` に設定。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` に設定。ユーザーに確認。

## `matter.md` テンプレート

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — 役割により異なる; プラクティスレベル CLAUDE.md の `## Who's using this` を参照]

# Matter: [クライアント] — [短い説明]

**Slug:** [slug]
**Opened:** [YYYY-MM-DD]
**Status:** active
**Confidentiality:** [standard / heightened / clean-team]

---

## 当事者

**クライアント:** [名前]
**相手方:** [名前]

## 案件タイプ

[vendor MSA | customer agreement | NDA | SaaS subscription | amendment | renewal | other — 1 行の根拠付き]

## 主要事実

[2〜5 文。この案件が何についてか。ステークホルダーは誰か。何が懸かっているか。デフォルトプレイブックと何が違うか。]

## 案件固有のオーバーライド

*この案件にのみ、そして他に何も適用されない、プラクティスレベルプレイブックからの逸脱。*

- [例: 「LoL キャップ: クライアントは 24 か月を要求、ハウス標準 12 ではない。」]
- [例: 「トーン: 関係維持的 — 相手方は戦略パートナー。」]
- [例: 「準拠法: デラウェアではなく英国法でなければならない。」]

## 関連案件

- [slug — 関連の理由を 1 行]

## 機密性に関する注記

[heightened または clean-team の場合、なぜかを説明。誰が案件ファイルを見られるか。グローバルにオンでも案件横断コンテキストが許容されるか。]
```

## `history.md` シード

```markdown
# History: [クライアント] — [短い説明]

追加のみのイベントログ。最新が上。

---

## [YYYY-MM-DD] — 案件開始

インテーク完了。Slug: `[slug]`。ステータス: アクティブ。
[matter.md を超えて保存する価値のある初期コンテキスト — 例: 「[相手方] からの inbound MSA ドラフトに対応して開始」。]
```

## 案件横断コンテキスト

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがあります。オフ(デフォルト)のとき、案件 A で作業するスキルは他の任意の `B` について `matters/B/` 内のファイルを**決して読みません**。期間限定。これはこの設定が存在するために提供する機密性の保証です。

オンのとき、スキルはユーザーが明示的に要求した場合(例: 「過去 5 件のベンダー案件にわたる責任上限のポジションを比較」)にのみ案件フォルダにわたって読むことができます。オンでも、ユーザーが案件横断ビューを要求しない限り、デフォルトはアクティブな案件のみをロードすることです。

## このスキルが行わないこと

- **利害関係チェックの実行。** 利害関係は実務家/事務所の仕事です; インテークはユーザーが宣言したことをキャプチャします。
- **保持の強制。** 終了は案件をアーカイブします; 削除しません。保持ポリシーはスコープ外。
- **出力の自動ルーティング。** 実質的なスキルがどこに書くかを決定します; このスキルはそれに*どのフォルダ*がアクティブかを伝え、その中に何を入れるかは伝えません。
- **案件横断が適切かを決定。** フラグを読み、従います。
