---
name: matter-workspace
description: >
  案件ワークスペースを管理 — 作成、一覧、切替、終了、または切り離し
  (プラクティスレベル)。複数クライアント実務者向けに、各クライアントや
  エンゲージメントのコンテキストを互いに分離します。新規案件のオープン、
  案件切替、案件一覧、案件のクローズ/アーカイブ、またはプラクティスレベル
  のみでの作業を希望するときに使用。
  (Use when the user wants to open a new matter, switch matters, list matters,
   close/archive a matter, or work at practice-level only.
   日本語トリガー: 案件作成、案件切替、案件一覧、案件クローズ、ワークスペース管理)
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務者は複数のクライアントと案件を横断して働きます。案件ワークスペースは、あるクライアントまたはエンゲージメントのコンテキストを他のすべてから分離します。このスキルがそれらのワークスペースを管理します。

## サブコマンド

- `/privacy-legal-ja:matter-workspace new <slug>` — 新規案件ワークスペース作成、短いインテーク実行、`matter.md` 書き込み
- `/privacy-legal-ja:matter-workspace list` — 状態とアクティブフラグ付きで案件を列挙
- `/privacy-legal-ja:matter-workspace switch <slug>` — アクティブ案件を設定
- `/privacy-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/matters/_archived/` に移動、決して削除しない)
- `/privacy-legal-ja:matter-workspace none` — アクティブ案件から切り離し、プラクティスレベルのみで作業

## 手順

1. `~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/CLAUDE.md` を読み — `## 案件ワークスペース` セクションが populate されているか確認。`Enabled` が `✗` ならユーザーに伝える: 「案件ワークスペースはオフ — 1 クライアントのインハウス実務として設定されているため、プラグインはプラクティスレベルコンテキストで自動的に動作。実際に複数クライアントを扱うなら `/privacy-legal-ja:cold-start-interview --redo` を再実行し、プライベートプラクティス設定を選択。そうでなければ `/matter-workspace` は不要。」エラーにしない — 無効状態はインハウスユーザーにとって期待される状態。
2. 下のサブコマンドロジックを使用。
3. `$ARGUMENTS` の最初のトークンでディスパッチ:
   - `new` → インテークインタビュー実行、`~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/matters/<slug>/matter.md` を書き、`history.md` と `notes.md` を seed。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/matters/*/matter.md` を列挙、テーブル表示、アクティブ案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/matters/_archived/<slug>/` に移動、`history.md` にクローズ日をログ。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. 変更内容をユーザーに表示し、書き込み前に確認。

## 注意

- スキルは、プラクティスレベル CLAUDE.md で `Cross-matter context` が `on` でない限り、案件横断で決して読まない。
- アーカイブは削除ではない — クローズした案件は保持/利益相反のため読み取り可能のまま。
- スラグは小文字+ハイフン。スラグがアーカイブ済みとアクティブで重複する場合、アーカイブ済みは `_archived/<slug>/` 配下に保持。

---

# 案件ワークスペース

複数クライアント実務者(プライベートプラクティス — 個人、小規模事務所、大規模事務所)は多くの案件を横断して働きます。あるもののコンテキストが別のものに漏れてはいけません。このスキルはそれを真にする薄いファイル管理層です。

**デフォルト状態はオフ。** インハウスユーザーには見えない — プラクティスレベルのみで動作。案件ワークスペースは、プライベートプラクティスユーザーのコールドスタートでオンになる、またはプラクティスレベル CLAUDE.md の `## 案件ワークスペース` を編集することで。`Enabled` が `✗` なら、このスキルは実行しない; 上のワークフローは無効状態を説明し、実際に案件分離が必要なユーザーには `/privacy-legal-ja:cold-start-interview --redo` を提案。

## ストレージレイアウト

すべての案件データは以下の配下:

```
~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/
├── CLAUDE.md                       # プラクティスレベル実務プロファイル
└── matters/
    ├── <slug>/
    │   ├── matter.md               # クライアント、相手方、案件タイプ、主要事実、上書き
    │   ├── history.md              # 日付付きイベント・判断・ドラフト・レビューのログ
    │   ├── notes.md                # 自由形式の作業メモ
    │   └── outputs/                # この案件のスキル出力(オプションのサブフォルダ)
    └── _archived/
        └── <slug>/                 # クローズ済み案件 — 読み取り可だがアクティブではない
```

スラグは小文字+ハイフン。例: `acme-msa-2026`, `zenith-renewal`, `vendor-xyz-nda`。

## アクティブ案件はプラクティス CLAUDE.md に

プラクティスレベル CLAUDE.md の `## 案件ワークスペース` 配下の `Active matter:` 行が単一の信頼源。案件切替はその行を編集。別の状態ファイルは無し。

## サブコマンドロジック

### `new <slug>`

1. スラグが `matters/<slug>/` または `matters/_archived/<slug>/` に既に存在しないか確認。再利用ならユーザーに別スラグを依頼。
2. インテークインタビュー実行:
   - **クライアント**(代理する当事者、またはインハウスなら内部事業部門)
   - **相手方**(他方 — 複数可)
   - **案件タイプ**(プラグインのプラクティスプロファイルから典型カテゴリを読む; privacy-legal-ja では: PIA(処理活動) | DPA レビュー | DSAR | 規制機関照会 | 移転メカニズムレビュー | インシデント | その他)
   - **秘密性レベル**(standard | heightened | clean-team — heightened は案件横断設定で追加配慮を促す)
   - **主要事実**(2-5 文: この案件は何について、ステークホルダーは誰、何が懸かっているか)
   - **プラクティスプレイブックへの案件固有上書き**(例: 「クライアントは LoL キャップ 24 ヶ月を要求、12 でない」「相手方は戦略的パートナー — 関係維持のトーン」)
   - **関連案件**(関連する案件のスラグ)
3. 下のテンプレートで `matters/<slug>/matter.md` を書く。
4. `matters/<slug>/history.md` を単一の「オープン」エントリで seed。
5. 空の `matters/<slug>/notes.md` を作成。
6. 新案件に**自動切替しない**。問う: 「`<slug>` に今切り替えますか?(`/privacy-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙。各ファイルの front-matter または最初数行を読み状態を抽出。テーブル表示:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現在アクティブな案件を `*` でマーク。`_archived/*` があれば別の「Archived」見出しで含める。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認。なければ `/privacy-legal-ja:matter-workspace new <slug>` を提案。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. matter.md サマリーをユーザーに表示、正しい案件か確認。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認。
2. `matters/<slug>/history.md` に今日の日付で「Closed」エントリを追記。
3. `matters/<slug>/` を `matters/_archived/<slug>/` に移動。
4. クローズした案件がアクティブだったなら、`Active matter:` を `none — practice-level context only` に設定。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` に設定。ユーザーと確認。

## `matter.md` テンプレート

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## 利用者` in the practice-level CLAUDE.md]

# Matter: [Client] — [short description]

**Slug:** [slug]
**Opened:** [YYYY-MM-DD]
**Status:** active
**Confidentiality:** [standard / heightened / clean-team]

---

## 当事者

**クライアント:** [name]
**相手方:** [name(s)]

## 案件タイプ

[vendor MSA | customer agreement | NDA | SaaS subscription | amendment | renewal | other — 1 行の根拠]

## 主要事実

[2-5 文。この案件は何について。ステークホルダーは誰。何が懸かっているか。デフォルトプレイブックと何が違うか。]

## 案件固有の上書き

*この案件にのみ適用される、プラクティスレベルプレイブックからの逸脱。*

- [例: 「LoL キャップ: クライアント要求 24 ヶ月、ハウス標準 12 ではない。」]
- [例: 「トーン: 関係維持 — 相手方は戦略的パートナー。」]
- [例: 「準拠法: 英国法、Delaware ではない。」]

## 関連案件

- [slug — 関連理由 1 行]

## 秘密性の注意

[heightened or clean-team なら、理由。案件ファイルを誰が見てよいか。グローバルにオンでも案件横断コンテキストが許容されるか。]
```

## `history.md` シード

```markdown
# History: [Client] — [short description]

追記専用イベントログ。最新が上。

---

## [YYYY-MM-DD] — 案件オープン

インテーク完了。スラグ: `[slug]`。状態: active。
[matter.md 以外で保持する価値ある初期コンテキスト — 例: 「[相手方] からの MSA ドラフト到来を受けてオープン」]
```

## クロス案件コンテキスト

プラクティスレベル CLAUDE.md に `Cross-matter context:` フラグあり。`off`(デフォルト)のとき、案件 A で動作中のスキルは他の任意の `B` の `matters/B/` 配下のファイルを**決して読まない**。期間。これは設定が提供する秘密性保証。

`on` のとき、ユーザーが明示的に依頼したときのみスキルが案件フォルダ横断で読める(例: 「過去 5 件のベンダー案件で責任キャップに関する立場を比較」)。`on` でも、ユーザーが案件横断ビューを依頼しない限り、アクティブ案件のみをデフォルトで読み込み。

## このスキルがしないこと

- **利益相反チェックを実行。** 利益相反は実務者/事務所の仕事; インテークはユーザーが宣言したものをキャプチャ。
- **保持を執行。** クローズはアーカイブ; 削除しない。保持ポリシーはスコープ外。
- **出力を自動ルーティング。** 実質スキルが書き込み先を決定; このスキルはどのフォルダがアクティブかを伝え、何を入れるかは伝えない。
- **案件横断が適切か判断。** フラグを読み従う。
