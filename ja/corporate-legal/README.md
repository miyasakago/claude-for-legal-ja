# Corporate Counsel プラグイン

社内コーポレート法務向けのワークフローを 4 つのプラクティスエリアにわたって提供します:M&A、Board & Secretary(取締役会・コーポレートセクレタリー)、Public Company(公開会社ガバナンス)、Entity Management(法人管理)。役割に該当するモジュールのみを有効化してください。コールドスタート・インタビューはモジュール構成で、有効なプラクティスエリアごとに的を絞った質問を行い、関連セクションのみをプラクティスプロファイルに書き込みます。

**すべての出力は、引用付き・フラグ付き・ゲート付きで生成された、弁護士レビューを前提とした下書きであり、法的結論ではありません。** プラグインが作業を実行します:文書を読み、プレイブックを適用し、論点を洗い出し、メモを起案します。弁護士はレビューし、検証し、判断します。引用はソース別にタグ付けされるため、どれがリサーチツール由来でどれが要確認かが分かります。秘匿特権マーカーは保守的に付与されるため、誤って権利放棄することはありません。重要なアクション(提出・送信・実行)は明示的な確認のゲート背後に置かれます。

## 対象ユーザー

| 役割 | 有効モジュール |
|---|---|
| **インハウス M&A 弁護士** | M&A |
| **コーポレートセクレタリー / アシスタント・セクレタリー** | Board & Secretary |
| **公開会社の GC** | M&A + Public Company + Board & Secretary |
| **非公開会社の GC** | M&A + Board & Secretary + Entity Management |
| **リーガルオペレーション / ソロ GC** | 該当するモジュールを自由に組み合わせ |

## 初回実行

```
/corporate-legal-ja:cold-start-interview
```

モジュール選択 → 有効化されたエリアごとの短い的を絞ったインタビューを順に進めます。モジュール構成の `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` に、関連セクションのみが書き込まれます。設定はこのパスに保存され、プラグイン更新後も維持されます。

ディール単位のセットアップ(M&A モジュールのみ):

```
/corporate-legal-ja:cold-start-interview --new-deal
```

## コマンド

| コマンド | 動作 |
|---|---|
| `/corporate-legal-ja:cold-start-interview` | モジュール構成のコールドスタート。`--new-deal` / `--module [m&a \| board \| public \| entities]` |
| `/corporate-legal-ja:diligence-issue-extraction [folder]` | VDR 文書を読み、ハウスフォーマットで論点抽出 |
| `/corporate-legal-ja:tabular-review` | タブラーレビュー — 1 文書 1 行、1 データポイント 1 列、すべてのセルをソース引用付き、Excel 出力 |
| `/corporate-legal-ja:material-contract-schedule` | DD 所見から重要契約の開示別紙(Material Contracts Disclosure Schedule)を構築 |
| `/corporate-legal-ja:closing-checklist` | クロージング・チェックリスト — ブロッカーとクリティカルパス |
| `/corporate-legal-ja:written-consent` | 全会一致書面決議(UWC) — 先例マッチした下書き + 署名者トラッカー |
| `/corporate-legal-ja:entity-compliance` | 法人コンプライアンス・トラッカー — 初期化、レポート、更新、監査、エクスポート |
| `/corporate-legal-ja:integration-management` | クロージング後の統合(インテグレーション)ワークプラン、Consents トラッカー、契約譲渡、ステータスレポート |
| `/corporate-legal-ja:matter-workspace` | 案件ワークスペースの管理(マルチクライアントの私的実務専用) — new, list, switch, close, none |

## 前提条件

いくつかの機能は Slack、Google Drive、SharePoint、Box、Intralinks、Datasite との連携を参照します。これらは環境で構成された MCP サーバーを必要とします — **プラグインには同梱されていません**。これらがない場合、プラグインはファイル出力にフォールバックします(チャンネル投稿の代わりにローカルにドラフトを書き出し、接続済みリポジトリの読込の代わりにトラッカーファイルをディスクに書き出します)。

MCP サーバーは、リポジトリレベルまたはユーザーレベルの `.mcp.json` で構成してください。スキルとエージェントは実行時に利用可能なものを検出し、挙動を調整します。

## スキル

| スキル | モジュール | 目的 |
|---|---|---|
| **cold-start-interview** | 全モジュール | モジュラーなインタビュー — 関連セクションのみを有効化 |
| **diligence-issue-extraction** | M&A | VDR 文書 → ハウスフォーマットでカテゴリ別に論点抽出 |
| **tabular-review** | M&A | 型付きカラムスキーマに対する文書セットのレビュー。セル単位の引用付き。`.xlsx` / `.csv` / Markdown 出力。material-contract-schedule に供給 |
| **deal-team-summary** | M&A | 階層化ブリーフ:経営層 / ディールリード / 作業チーム |
| **material-contract-schedule** | M&A | 株式譲渡契約の Material Contract 定義に基づく開示別紙 |
| **closing-checklist** | M&A | 自己更新型:DD と別紙構築から取り込み |
| **ai-tool-handoff** | M&A | Luminance / Kira 連携 — 一括抽出 + QA レイヤー |
| **board-minutes** | Board & Secretary | カレンダー検出した会議 → ハウスフォーマットで議事録ドラフト |
| **written-consent** | Board & Secretary | コンセント・リポジトリの先例検索付きの全会一致書面決議。重大な単発アクションに対するスコープ警告 |
| **entity-compliance** | Entity Management | コンプライアンスカレンダー・トラッカー(YAML)、法人別・州別の申請期限、ヘルス監査、CT Corp レポート取り込み、CSV エクスポート |
| **integration-management** | M&A | クロージング後の統合トラッカー。段階別ワークプラン(Day 1/30/90/180)、株式譲渡契約の期限付き Required Consents トラッカー、契約譲渡(リポジトリまたは手動リスト)、週次ステータスレポート |
| **matter-workspace** | — | マルチクライアント実務のための案件ワークスペースの作成・一覧・切替・クローズ。各クライアント / 案件を分離しコンテキスト漏洩を防止 |

*Public Company 関連スキルは次回リリースで提供予定。*

## インタラクティブコマンド vs. スケジュール実行エージェント

上記のコマンドは呼び出した時に実行されます — 自分が案件に取り組んでいる時用です。下記のエージェントはスケジュールに沿って実行されます — 目を離している間に動くもの用です:

| エージェント | モジュール | 監視対象 | デフォルトのケイデンス |
|---|---|---|---|
| **dataroom-watcher** | M&A | VDR の新規アップロード。高優先度カテゴリに該当するアップロードのフラグ。クロージング・チェックリストの進捗実行 | 週次 |

## 連携(Integrations)

**最初にリサーチツールを接続してください — 引用関連のガードレールはこれに依存します。** リサーチツールがない場合、すべての引用に `[verify]` タグが付き、各成果物の上の Reviewer note にもソースが検証されていない旨が記録されます。スキルはどちらの状態でも動作しますが、リサーチツール(CourtListener など)は検証作業を肩代わりしてくれます。

同梱:

- **Slack** — メッセージ検索、チャンネル読込、ディスカッション発見(汎用バケット)
- **Google Drive** — ドキュメントの検索・読込・取得(汎用バケット)
- **Box** — データルームおよびドキュメント管理

Intralinks、Datasite、その他の VDR コネクターは、パートナー URL が提供され次第 `.mcp.json` に追加できます。

## 学習の仕組み

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` のプラクティスプロファイルは静的ではありません — プラグインを使うほど改善されます。スキルは、調整したほうがよいデフォルトを使った時にそれを通知します。セットアップを再実行する、ファイルを直接編集する、新しいポジションを記録するようスキルに指示する — いずれでも可能です。

## M&A 関連の注記

- 論点抽出は重要性基準を適用します — 「金額上位 N 件」基準であればすべての文書を読むわけではありません。
- 買い手側(Buy-side)・売り手側(Sell-side)の双方をサポートします。プラクティスプロファイルが当該ディールでどちらが該当するかを記録し、スキルはそれに応じてスタンスを調整します。
- AI ツール連携(Luminance / Kira)はオプションです。`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` でツールなしと設定されていれば、抽出はすべて直接スキル経由で実行されます。
- クロージング・チェックリストは株式譲渡契約から初期化され、DD で同意取得(Consents)が必要と判明するごとに自己更新します。
