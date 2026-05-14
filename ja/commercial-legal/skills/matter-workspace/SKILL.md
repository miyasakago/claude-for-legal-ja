---
name: matter-workspace
description: >
  案件ワークスペースの管理 — new、list、switch、close、または detach(プラクティスレベル)。
  マルチクライアントの実務家が案件を作成、アクティブな案件を切り替え、案件を一覧表示、
  案件をアーカイブ、プラクティスレベルのコンテキストへデタッチする必要があるとき、
  または別のスキルが現在どの案件で作業しているかを知る必要があるときに使用。
  日本語トリガー例: 案件作成、新規案件、案件切替、案件一覧、案件をクローズ。
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件をまたいで作業します。案件ワークスペースは、あるクライアントまたはエンゲージメントのコンテキストを他のすべてから分離して保ちます。このコマンドはそれらのワークスペースを管理します。

## サブコマンド

- `/commercial-legal-ja:matter-workspace new <slug>` — 新しい案件ワークスペースを作成、短いインテイクを実行、`matter.md` を書き込み
- `/commercial-legal-ja:matter-workspace list` — ステータスとアクティブフラグ付きで案件を一覧表示
- `/commercial-legal-ja:matter-workspace switch <slug>` — アクティブな案件を設定
- `/commercial-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/matters/_archived/` に移動。削除はしない)
- `/commercial-legal-ja:matter-workspace none` — アクティブな案件から切り離し、プラクティスレベルのみで作業

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` を読む — `## Matter workspaces` 節が入力されていることを確認。`Enabled` が `✗` であれば、ユーザーに伝える:「案件ワークスペースはオフです — 1 クライアントのインハウスとして設定されているので、プラグインは自動的にプラクティスレベルのコンテキストから動きます。実際に複数クライアントをまたいで作業しているなら、`/commercial-legal-ja:cold-start-interview --redo` を実行して私的実務の設定を選択してください。それ以外の場合、`/matter-workspace` は必要ありません。」エラーにしない — インハウスユーザーにとっては無効状態が期待される状態。
2. 以下のサブコマンドロジックを使用。
3. `$ARGUMENTS` の最初のトークンで分岐:
   - `new` → インテイクインタビューを実行し、`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/matters/<slug>/matter.md` を書き込み、`history.md` と `notes.md` をシード。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/matters/*/matter.md` を列挙、表を出力、アクティブな案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/matters/_archived/<slug>/` に移動、クローズ日を `history.md` にログ。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. 何が変わったかをユーザーに表示し、書き込み前に確認する。

## 注意

- `Cross-matter context` がプラクティスレベル CLAUDE.md で `on` でない限り、スキルは案件をまたいで読むことはない。
- アーカイブは削除ではない — クローズした案件は保存期間・利益相反目的のため読み取り可能のまま。
- スラグは小文字とハイフン。アーカイブとアクティブの両方でスラグが再利用された場合、アーカイブされたものは `_archived/<slug>/` に保存される。

---

マルチクライアントの実務家(私的実務 — ソロ、小規模事務所、大規模事務所)は多くの案件にまたがって作業します。あるクライアントのコンテキストが別のクライアントに漏れてはなりません。このスキルは、それを実現する薄いファイル管理層です。

**デフォルト状態はオフ。** インハウスユーザーはこれを見ません — 彼らはプラクティスレベルのみで動きます。案件ワークスペースは私的実務ユーザー向けにコールドスタート時にオンになるか、プラクティスレベル CLAUDE.md の `## Matter workspaces` を編集することでオンになります。`Enabled` が `✗` であれば、このスキルは実行されません。上記ワークフローは無効状態を説明し、実際に案件分離が必要なユーザーには `/commercial-legal-ja:cold-start-interview --redo` を提案します。

## ストレージレイアウト

すべての案件データは以下の下に存在します:

```
~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/
├── CLAUDE.md                       # プラクティスレベルのプラクティスプロファイル
└── matters/
    ├── <slug>/
    │   ├── matter.md               # クライアント、相手方、案件タイプ、主要事実、オーバーライド
    │   ├── history.md              # 日付付きのイベント、判断、ドラフト、レビューのログ
    │   ├── notes.md                # 自由形式の作業メモ
    │   └── outputs/                # この案件のスキル出力(任意のサブフォルダ)
    └── _archived/
        └── <slug>/                 # クローズした案件 — 読み取り可能だがアクティブではない
```

スラグは小文字とハイフン。例:`acme-msa-2026`、`zenith-renewal`、`vendor-xyz-nda`。

## アクティブな案件はプラクティス CLAUDE.md にある

プラクティスレベル CLAUDE.md の `## Matter workspaces` 下にある `Active matter:` 行が唯一の真実の源です。案件を切り替えるとはその行を編集すること。別状態ファイルはありません。

## サブコマンドロジック

### `new <slug>`

1. スラグが `matters/<slug>/` または `matters/_archived/<slug>/` に既に存在しないことを確認。再利用なら別のスラグを選ぶようユーザーに依頼。
2. インテイクインタビューを実行:
   - **クライアント**(自社が代理する当事者、またはインハウスなら社内事業ユニット)
   - **相手方**(もう一方の当事者 — 複数の場合あり)
   - **案件タイプ**(プラグインのプラクティスプロファイルで典型的なカテゴリを読む。commercial-legal の場合: vendor MSA | customer agreement | NDA | SaaS subscription | amendment | renewal | other)
   - **機密性レベル**(standard | heightened | clean-team — heightened はクロス案件設定で追加の注意を促す)
   - **主要事実**(2〜5 文:この案件は何についてか、誰がステークホルダーか、何がかかっているか)
   - **プラクティスプレイブックに対する案件固有のオーバーライド**(例:「クライアントは LoL 上限を 12 か月ではなく 24 か月に要求」、「相手方は戦略的パートナー — 関係維持のトーン」)
   - **関連案件**(関連する案件のスラグ)
3. 下のテンプレートを使って `matters/<slug>/matter.md` を書く。
4. `matters/<slug>/history.md` を 1 つの「Opened」エントリでシード。
5. 空の `matters/<slug>/notes.md` を作成。
6. **自動的に**新しい案件に切り替えない。尋ねる:「今 `<slug>` に切り替えますか?(`/commercial-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙。各ファイルのフロントマターまたは最初の数行を読んでステータスを抽出。表を出力:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現在アクティブな案件を `*` でマーク。`_archived/*` があれば別の「Archived」見出しの下に含める。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認。存在しなければ `/commercial-legal-ja:matter-workspace new <slug>` を提案。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. matter.md のサマリーを表示し、正しい案件にいることをユーザーが確認できるようにする。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認。
2. `matters/<slug>/history.md` に今日の日付付きで「Closed」エントリを追記。
3. `matters/<slug>/` を `matters/_archived/<slug>/` に移動。
4. クローズした案件がアクティブな案件だった場合、`Active matter:` を `none — practice-level context only` に設定。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` に設定。ユーザーと確認。

## `matter.md` テンプレート

```markdown
[WORK-PRODUCT HEADER — プラグイン設定の ## Outputs に従って — 役割によって異なる。プラクティスレベル CLAUDE.md の `## Who's using this` を参照]

# Matter: [Client] — [短い説明]

**Slug:** [slug]
**Opened:** [YYYY-MM-DD]
**Status:** active
**Confidentiality:** [standard / heightened / clean-team]

---

## Parties

**Client:** [名称]
**Counterparty:** [名称]

## Matter type

[vendor MSA | customer agreement | NDA | SaaS subscription | amendment | renewal | other — 1 行の根拠付き]

## Key facts

[2〜5 文。この案件は何についてか。誰がステークホルダーか。何がかかっているか。デフォルトプレイブックから何が違うか。]

## Matter-specific overrides

*この案件にのみ適用される、プラクティスレベルプレイブックからの逸脱。*

- [例:「LoL 上限:クライアント要求 24 か月。ハウス標準 12 か月ではない。」]
- [例:「トーン:関係維持 — 相手方は戦略的パートナー。」]
- [例:「準拠法:Delaware ではなく英国法でなければならない。」]

## Related matters

- [slug — 関連する理由を 1 行]

## Notes on confidentiality

[heightened または clean-team なら、その理由を記述。誰が案件ファイルを見られるか。グローバルに有効でもクロス案件コンテキストが許されるか。]
```

## `history.md` シード

```markdown
# History: [Client] — [短い説明]

追記専用のイベントログ。最新を上に。

---

## [YYYY-MM-DD] — Matter opened

インテイク完了。スラグ:`[slug]`。ステータス:active。
[matter.md を超えて保存に値する初期コンテキスト — 例:「[相手方] からのインバウンド MSA ドラフトに応じて開設」]
```

## クロス案件コンテキスト

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがあります。それが `off`(デフォルト)のとき、案件 A で作業しているスキルは、他のどの `B` についても `matters/B/` のファイルを**読みません**。期間。これがこの設定が提供する機密性の保証です。

`on` のとき、スキルはユーザーが明示的に求めたときのみ、案件フォルダにまたがってファイルを読みます(例:「過去 5 件のベンダー案件にわたる責任上限の立場を比較」)。`on` であってもデフォルトは、ユーザーがクロス案件ビューを求めない限りアクティブな案件のみをロードします。

## このスキルが行わないこと

- **利益相反チェックの実施。** 利益相反は実務家/事務所の仕事。インテイクはユーザーが宣言した内容を捕捉するだけ。
- **保存期間の強制。** クローズは案件をアーカイブするだけ。削除はしない。保存期間ポリシーは範囲外。
- **出力の自動振り分け。** 実質的なスキルがどこに書くか決める。このスキルはどのフォルダがアクティブかを伝えるだけで、何を入れるかは伝えない。
- **クロス案件が適切かの判断。** フラグを読み、従う。
