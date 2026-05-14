---
name: matter-workspace
description: >
  案件ワークスペースを管理 — new、list、switch、close、または detach
  (プラクティスレベル)。あるクライアント案件のコンテキストが他に決して漏れ
  ないよう、アクティブ案件を作成、リスト、切り替え、クローズ、デタッチします。
  複数クライアントの実務家が「新規案件」「案件切り替え」「案件をリスト」
  「この案件をクローズ」と言うとき、または別のスキルがどの案件がアクティブ
  かを知る必要があるときに使用。
  (Manage matter workspaces — new, list, switch, close, or detach (practice-
  level). Use when a multi-client practitioner says "new matter", "switch
  matter", "list my matters", "close this matter", or needs to manage which
  matter is active. 日本語トリガー: 「新規案件」「案件切り替え」「案件リスト」
  「案件をクローズ」「案件ワークスペース」)
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件を横断して働きます。案件ワークスペースは、あるクライアントまたは案件のコンテキストを他のすべてから分離します。このスキルはそれらのワークスペースを管理します。

## サブコマンド

- `/employment-legal-ja:matter-workspace new <slug>` — 新規案件ワークスペースを作成、短いインテイクを実行、`matter.md` を書き込み
- `/employment-legal-ja:matter-workspace list` — ステータスとアクティブフラグ付きで案件をリスト
- `/employment-legal-ja:matter-workspace switch <slug>` — アクティブ案件を設定
- `/employment-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/_archived/` に移動、決して削除しない)
- `/employment-legal-ja:matter-workspace none` — アクティブ案件からデタッチ、プラクティスレベルのみで作業

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` を読む — `## 案件ワークスペース` セクションが記入済みであることを確認。`Enabled` が `✗` の場合、ユーザーに伝える: 「案件ワークスペースはオフです — あなたは 1 クライアントのインハウス・プラクティスとして設定されているため、プラグインはプラクティスレベルのコンテキストから自動で動作します。実際に複数のクライアントを横断して働いているなら、`/employment-legal-ja:cold-start-interview --redo` を再実行してプライベートプラクティス設定を選択してください。それ以外の場合、`/matter-workspace` はまったく必要ありません。」エラーを出さない — 無効化状態はインハウスユーザーの期待される状態。
2. 下記のサブコマンドロジックを使用。
3. `$ARGUMENTS` の最初のトークンでディスパッチ:
   - `new` → インテイクインタビューを実行、`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/<slug>/matter.md` を書き込み、`history.md` と `notes.md` を seed。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/*/matter.md` を列挙、表を印刷、アクティブ案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/_archived/<slug>/` に移動、`history.md` にクローズ日をログ。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. 何が変わったかユーザーに表示し、書き込み前に確認。

## 注意点

- プラクティスレベル CLAUDE.md で `Cross-matter context` が `on` でない限り、スキルは案件を横断して決して読まない。
- アーカイブは削除ではない — クローズされた案件は保持/利益相反目的で読み取り可能なまま。
- slug は小文字とハイフン。slug がアーカイブとアクティブで再利用される場合、アーカイブされたものは `_archived/<slug>/` 下に保持される。

---

## 参照

複数クライアントの実務家(プライベートプラクティス — 個人、小規模事務所、大規模事務所)は多数の案件を横断して働きます。あるもののコンテキストは他に漏れてはなりません。このスキルはそれを実現する薄いファイル管理層です。

**デフォルト状態はオフ。** インハウスユーザーには見えない — プラクティスレベルのみで実行。案件ワークスペースはプライベートプラクティスユーザーのコールドスタートで、またはプラクティスレベル CLAUDE.md の `## 案件ワークスペース` を編集することでオンになります。`Enabled` が `✗` の場合、このスキルは実行しない; 代わりに無効状態を説明し、実際に案件分離が必要なユーザーに `/employment-legal-ja:cold-start-interview --redo` を提案します。

## ストレージレイアウト

すべての案件データは以下に存在:

```
~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/
├── CLAUDE.md                       # プラクティスレベルのプラクティスプロファイル
└── matters/
    ├── <slug>/
    │   ├── matter.md               # クライアント、相手方、案件タイプ、主要事実、上書き
    │   ├── history.md              # 日付付きイベント、判断、ドラフト、レビューログ
    │   ├── notes.md                # 自由形式の作業ノート
    │   └── outputs/                # この案件のスキル出力(オプションのサブフォルダ)
    └── _archived/
        └── <slug>/                 # クローズされた案件 — 読み取り可能だが非アクティブ
```

slug は小文字とハイフン。例: `acme-msa-2026`、`zenith-renewal`、`vendor-xyz-nda`。

## アクティブ案件はプラクティス CLAUDE.md にある

プラクティスレベル CLAUDE.md の `## 案件ワークスペース` 下の `Active matter:` 行が真実の単一情報源。案件の切り替えはその行を編集する。別途の状態ファイルなし。

## サブコマンドロジック

### `new <slug>`

1. slug が `matters/<slug>/` または `matters/_archived/<slug>/` に既に存在しないか確認。再利用されている場合、別の slug を選ぶようユーザーに頼む。
2. インテイクインタビューを実行:
   - **クライアント**(代理する当事者、またはインハウスなら社内ビジネスユニット)
   - **相手方**(他方 — 複数あり得る)
   - **案件タイプ**(プラグインのプラクティスプロファイルで一般的なカテゴリーを読む; employment-legal-ja の場合: hire | termination | investigation | leave | accommodation | classification | country expansion | policy project | other)
   - **秘密性レベル**(standard | heightened | clean-team — heightened は案件横断設定で追加の注意を促す)
   - **主要事実**(2-5 文: この案件が何についてか、ステークホルダーは誰か、何が掛かっているか)
   - **プラクティスプレイブックへの案件固有の上書き**(例: 「クライアントは 12 ヶ月ではなく 24 ヶ月の LoL キャップを要求」、「相手方は戦略パートナー — 関係保全的なトーン」)
   - **関連案件**(任意の関連案件の slug)
3. 下記のテンプレートを使って `matters/<slug>/matter.md` を書き込み。
4. `matters/<slug>/history.md` を単一の「Opened」エントリで seed。
5. 空の `matters/<slug>/notes.md` を作成。
6. **自動切り替えはしない。** 尋ねる: 「今 `<slug>` に切り替えますか? (`/employment-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙。各ファイルのフロントマターまたは最初の数行を読んでステータスを抽出。表を印刷:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現在のアクティブ案件を `*` でマーク。`_archived/*` がある場合、別の「Archived」見出し下に含める。

### `switch <slug>`

1. `matters/<slug>/matter.md` の存在を確認。なければ `/employment-legal-ja:matter-workspace new <slug>` を提案。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. ユーザーが正しい案件にいることを確認できるように matter.md サマリーを表示。

### `close <slug>`

1. `matters/<slug>/` の存在を確認。
2. `matters/<slug>/history.md` に今日の日付で「Closed」エントリを追記。
3. `matters/<slug>/` → `matters/_archived/<slug>/` へ移動。
4. クローズされた案件がアクティブ案件だった場合、`Active matter:` を `none — practice-level context only` に設定。

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

*この案件およびこの案件のみに適用される、プラクティスレベルプレイブックからの偏差。*

- [例: "LoL cap: client requires 24 months, not house standard 12."]
- [例: "Tone: relationship-preserving — counterparty is a strategic partner."]
- [例: "Governing law: must be English law, not Delaware."]

## Related matters

- [slug — 関連する理由を 1 行で]

## Notes on confidentiality

[heightened または clean-team の場合、なぜか説明。誰が案件ファイルを見られるか。グローバルに on の場合でも案件横断コンテキストが許容されるか。]
```

## `history.md` シード

```markdown
# History: [Client] — [short description]

追記専用イベントログ。最新が最上部。

---

## [YYYY-MM-DD] — Matter opened

インテイク完了。Slug: `[slug]`。Status: active。
[matter.md を超えて保存すべき初期コンテキスト — 例: "[counterparty] からの インバウンド MSA ドラフトに応じて開設。"]
```

## クロス案件コンテキスト

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがあります。`off`(デフォルト)のとき、案件 A で動作中のスキルは他の任意の `B` に対する `matters/B/` のファイルを**決して読まない**。期間。これは設定が提供するために存在する秘密性保証。

`on` のとき、ユーザーが明示的に頼んだ場合のみ(例: 「過去 5 ベンダー案件での責任上限に関する立場を比較」)、スキルは案件フォルダを横断して読む。`on` でも、ユーザーが案件横断ビューを求めない限り、デフォルトはアクティブ案件のみをロード。

## このスキルが行わないこと

- **利益相反チェックを実行。** 利益相反は実務家/事務所の仕事; インテイクはユーザーが宣言したものをキャプチャ。
- **保持を強制。** クローズは案件をアーカイブする; 削除はしない。保持ポリシーはスコープ外。
- **出力を自動ルーティング。** 実質的なスキルがどこに書くかを決定; このスキルはどの*フォルダ*がアクティブかを伝え、何を入れるかは伝えない。
- **案件横断が適切かを決定。** フラグを読んで従う。
