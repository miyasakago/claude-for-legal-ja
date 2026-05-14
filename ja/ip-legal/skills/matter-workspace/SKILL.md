---
name: matter-workspace
description: >
  Manage matter workspaces — create, list, switch, close, or detach the
  active matter. Use in multi-client private practice to keep one client's
  context separate from another, or when a substantive skill needs to know
  which matter it's working in.
  案件ワークスペースを管理 — アクティブ案件の作成、リスト、切替、クローズ、デタッチ。
  マルチクライアントのプライベートプラクティスで、あるクライアントのコンテキストを
  別のクライアントから分離するため、または実質的なスキルがどの案件で作業しているかを
  知る必要があるときに使用。
  日本語トリガー: 「案件作成」「案件切替」「案件リスト」「案件クローズ」「マターワークスペース」。
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

実務家は複数のクライアントと案件を横断して仕事をします。案件ワークスペースは、1 つのクライアントまたは契約のコンテキストを他のすべてから分離します。本スキルはそのワークスペースを管理します。

## サブコマンド

- `/ip-legal-ja:matter-workspace new <slug>` — 新しい案件ワークスペースを作成し、短いインテークを実行し、`matter.md` を書き込みます
- `/ip-legal-ja:matter-workspace list` — 案件をステータスとアクティブ・フラグ付きでリスト
- `/ip-legal-ja:matter-workspace switch <slug>` — アクティブ案件を設定
- `/ip-legal-ja:matter-workspace close <slug>` — 案件をアーカイブ(`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/_archived/` に移動、削除はしない)
- `/ip-legal-ja:matter-workspace none` — アクティブ案件からデタッチし、プラクティスレベルのみで作業

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` を読み — `## Matter workspaces` セクションが記入されていることを確認。`Enabled` が `✗` なら、ユーザーに伝えます: 「案件ワークスペースはオフです — 1 つのクライアントのインハウスとして設定されているため、プラグインはプラクティスレベルのコンテキストから自動的に動作します。実際に複数のクライアントを横断して仕事をするなら、`/ip-legal-ja:cold-start-interview --redo` を再実行してプライベートプラクティスのセッティングを選択してください。そうでなければ `/ip-legal-ja:matter-workspace` は不要です」。エラーにしないこと — 無効化状態はインハウス・ユーザーにとって期待される状態です。
2. 下記のサブコマンドロジックに従います。
3. `$ARGUMENTS` の最初のトークンでディスパッチ:
   - `new` → インテーク・インタビューを実行し、`~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<slug>/matter.md` を書き込み、`history.md` と `notes.md` をシードします。
   - `list` → `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/*/matter.md` を列挙し、テーブルを印刷し、アクティブ案件をマーク。
   - `switch` → プラクティスレベル CLAUDE.md の `Active matter:` 行を更新。
   - `close` → `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<slug>/` を `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/_archived/<slug>/` に移動し、`history.md` にクローズ日をログ。
   - `none` → `Active matter:` を `none — practice-level context only` に設定。
4. 何が変わったかをユーザーに見せ、書き込み前に確認します。

## 注記

- 本スキルは、プラクティスレベル CLAUDE.md の `Cross-matter context` が `on` でない限り、案件をまたいで読まないこと。
- アーカイブは削除ではない — クローズ済み案件は保存期間/利益相反チェック目的で読み取り可能。
- スラグは小文字とハイフン。アーカイブとアクティブでスラグが再利用された場合、アーカイブされたものは `_archived/<slug>/` の下に保存されます。

---

マルチクライアントの実務家(プライベートプラクティス — 個人、小規模事務所、大規模事務所)は多くの案件を横断して仕事をします。あるコンテキストが別のものに漏れてはなりません。本スキルは、そう実現するための薄いファイル管理レイヤーです。

**デフォルト状態はオフ.** インハウスユーザーはこれを見ません — プラクティスレベルのみで実行します。案件ワークスペースは、プライベートプラクティスのユーザー向けにコールドスタート時にオンになるか、プラクティスレベル CLAUDE.md の `## Matter workspaces` を編集することでオンになります。`Enabled` が `✗` なら、本スキルは実行されず、代わりに無効化状態を説明し、本当に案件分離が必要なユーザーに `/ip-legal-ja:cold-start-interview --redo` を提案します。

## ストレージレイアウト

案件データはすべて以下に存在します:

```
~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/
├── CLAUDE.md                       # プラクティスレベルのプラクティスプロファイル
└── matters/
    ├── <slug>/
    │   ├── matter.md               # クライアント、相手方、案件タイプ、主要事実、オーバーライド
    │   ├── history.md              # 日付付きのイベント、判断、ドラフト、レビューのログ
    │   ├── notes.md                # 自由形式のワーキングノート
    │   └── outputs/                # この案件のスキル出力(オプションのサブフォルダ)
    └── _archived/
        └── <slug>/                 # クローズ済み案件 — 読み取り可能だがアクティブではない
```

スラグは小文字とハイフン。例: `acme-trademark-2026`, `zenith-dmca`, `novacorp-fto`。

## アクティブ案件はプラクティス CLAUDE.md にある

プラクティスレベル CLAUDE.md の `## Matter workspaces` セクションの `Active matter:` 行が唯一のソース・オブ・トゥルース。案件の切替はこの行を編集します。別のステートファイルはありません。

## サブコマンドのロジック

### `new <slug>`

1. スラグが `matters/<slug>/` または `matters/_archived/<slug>/` にまだ存在しないことを確認します。再利用なら別のスラグを選ぶようユーザーに依頼します。
2. インテーク・インタビューを実行:
   - **Client**(代表する当事者、またはインハウスの場合は内部ビジネスユニット)
   - **Counterparty**(相手方 — 複数の場合あり。ウォッチトリガーの案件では「unknown third-party infringer」になりうる)
   - **Matter type**(プラグインのプラクティスプロファイルから典型的なカテゴリを読み込み。ip-legal の場合: 商標クリアランス | 商標エンフォースメント | DMCA | 特許 FTO | 特許侵害 | IP 条項レビュー | OSS コンプライアンス | ポートフォリオ維持 | その他)
   - **Confidentiality level**(standard | heightened | clean-team — heightened はクロスマター設定で余分な注意を促す、clean-team は特許 FTO 作業でよくある)
   - **Key facts**(2〜5 文: この案件は何か、誰がステークホルダーか、何が懸かっているか)
   - **Matter-specific overrides to the practice posture**(例: 「このマークに限ってクライアントは aggressive を望む」、「相手方は戦略的パートナー — measured トーンのみ」、「発明者が利用不可 — インタビューで表面化しない」)
   - **Related matters**(関連案件のスラグ)
3. 下記のテンプレートを使って `matters/<slug>/matter.md` を書き込みます。
4. `matters/<slug>/history.md` を「Opened」エントリーでシードします。
5. 空の `matters/<slug>/notes.md` を作成します。
6. 新しい案件に **auto-switch しないこと**。問います: 「今 `<slug>` に切り替えますか?(`/ip-legal-ja:matter-workspace switch <slug>`)」

### `list`

`matters/*/matter.md` を列挙します。各ファイルのフロントマターまたは最初の数行を読み、ステータスを抽出します。テーブルを印刷:

| Slug | Client | Matter type | Status | Opened | Active |
|---|---|---|---|---|---|

現在アクティブな案件を `*` でマークします。`_archived/*` があれば別の「Archived」見出しの下に含めます。

### `switch <slug>`

1. `matters/<slug>/matter.md` が存在することを確認。なければ `/ip-legal-ja:matter-workspace new <slug>` を提案。
2. プラクティスレベル CLAUDE.md の `Active matter:` 行を `Active matter: <slug>` に編集。
3. ユーザーに matter.md サマリーを見せ、正しい案件にいることを確認できるようにします。

### `close <slug>`

1. `matters/<slug>/` が存在することを確認。
2. `matters/<slug>/history.md` に今日の日付で「Closed」エントリーを追加。
3. `matters/<slug>/` → `matters/_archived/<slug>/` に移動。
4. クローズした案件がアクティブだった場合、`Active matter:` を `none — practice-level context only` に設定。

### `none`

プラクティスレベル CLAUDE.md の `Active matter:` を `none — practice-level context only` に設定。ユーザーに確認します。

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

[商標クリアランス | 商標エンフォースメント | DMCA | 特許 FTO | 特許侵害 | IP 条項レビュー | OSS コンプライアンス | ポートフォリオ維持 | その他 — 1 行の理由付き]

## Key facts

[2〜5 文。この案件は何か。ステークホルダーは誰か。何が懸かっているか。デフォルトスタンスと何が違うか。]

## Matter-specific overrides

*この案件のみに適用される、プラクティスレベルのスタンスからの逸脱。*

- [例: 「Enforcement posture: house default は aggressive だが、ここでは measured — 相手方はキーチャネル・パートナー」]
- [例: 「Approval for assertion: いかなるレターも出る前にマーケティングからの追加サインオフが必要」]
- [例: 「Clean-team: cross-matter context がグローバルにオンでも案件ファイルを読み取り不可」]

## Related matters

- [slug — 関連する理由 1 行]

## Notes on confidentiality

[heightened または clean-team の場合、理由を記述。誰が案件ファイルを見られるか。グローバルにオンでも cross-matter context が許されるか。]
```

## `history.md` シード

```markdown
# History: [Client] — [short description]

追記型イベントログ。最新が上。

---

## [YYYY-MM-DD] — Matter opened

インテーク完了。Slug: `[slug]`。Status: active。
[matter.md を超えて保存する価値のある初期コンテキスト — 例: 「クラス 25 の `APEXLEAF` のウォッチサービス・ヒットを受けてオープン」]
```

## Cross-matter context

プラクティスレベル CLAUDE.md には `Cross-matter context:` フラグがあります。これが `off`(デフォルト)の場合、案件 A で動作するスキルは、他のどの `B` についても `matters/B/` のファイルを**決して読みません**。これがこの設定が提供すべき秘匿性の保証です。

`on` の場合、スキルは、ユーザーが明示的に求めたとき(例: 「このマークについて案件全体で送付したすべてのエンフォースメント・レターを見せて」)に限り、案件フォルダを横断して読み取れます。`on` の場合でもデフォルトはアクティブ案件のみをロードし、ユーザーが cross-matter ビューを求めた場合のみ広げます。

## このスキルが行わないこと

- **利益相反チェックを実行する.** 利益相反は実務家/事務所の仕事です。インテークはユーザーが宣言したものを取得します。
- **保存期間を強制する.** クローズはアーカイブで、削除ではありません。保存ポリシーはスコープ外です。
- **出力を自動ルーティング.** 実質的なスキルがどこに書くかを決めます。本スキルはどのフォルダがアクティブかを伝えるのであって、何を入れるかではありません。
- **cross-matter が適切かを決める.** フラグを読み、従います。
