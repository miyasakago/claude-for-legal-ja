# Legal Builder Hub プラグイン

コミュニティ製法務スキルの発見とインストール。GitHub レジストリ(lpm-skills、[追加レジストリ — /legal-builder-hub-ja:registry-browser から追加]、その他)を閲覧し、インストール・自動更新を行い、他の法務プラグイン内で関連するコミュニティスキルを表示します。コールドスタート・インタビューがスターターパック・レコメンダーそのもの — プラクティスタイプを尋ね、最初に何をインストールすべきかを推奨します。

**すべてのコミュニティスキルはインストール前に raw のまま表示され、プロンプトインジェクション・パターンがスキャンされ、Legal Skill Design Framework に照らして評価されます。プラグインは検索と評価を支援します。何を信頼するかはあなたが決めます。**

## このプラグインを使うのは誰か

他のすべての法務プラグインを使う人。これはアプリストアです。

## 初回起動: コールドスタート

プラクティスタイプ、業界、チーム規模、ツーリングの慣れを尋ねます。マッチするコミュニティスキルのスターターパックを推奨します。選んだものをインストールします。

```
/legal-builder-hub-ja:cold-start-interview
```

設定は `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` に保存され、プラグインの更新後も保持されます。

## セキュリティ姿勢

インストールされたコミュニティスキルは、あなたがクライアントデータ・案件ファイル・チームのプレイブックにアクセスするのと同じ権限で実行されます。ハブはあらゆるインストールと更新を信頼の決定として扱います。4 層の防御があり、いずれも単独では十分ではありません:

- **許可リスト(管理者管理):** `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/allowlist.yaml` が、コミュニティスキルが利用してよいレジストリ、公開元、MCP コネクターを宣言します。`permissive` モード(デフォルト)はリストにないものを警告し、`restrictive` モード(事務所 / エンタープライズ展開に推奨)は拒否します。許可リストはインストーラーが第三者コンテンツを読む前にチェックされます。スキーマは `skills/skill-installer/references/allowlist.md` を参照。
- **要約ではなく raw のソース:** インストーラーは何かが書き込まれる前に、AI 要約ではなく、完全な raw `SKILL.md` を表示します。要約は便利ですが、怪しいことをするスキルは raw 表示が表すテキストでそれを行う必要があります。
- **ヒューリスティック・スキャン:** インストーラーと `skills-qa` の両方が、プロンプトインジェクション・パターン(上書き / 権威主張、スコープ外の読み書き、外部 URL、隠し Unicode、シェル実行、認証情報要求)をスキャンします。これらは AI ヒューリスティックなスキャンであり、明示的にそうラベル付けされます — クリーンなスキャンはセキュリティ監査ではなく、自分でテキストを読むよう促すものです。
- **毎回のヒューマン承認:** 新しく入力された `yes` なしには何もディスクに書き込まれません。承認は過去のメッセージから推測されません。多層防御のため、インストーラーは取得 / 分析を読み取り専用サブエージェントで実行することを推奨します — 書き込み機能は承認後にしか利用できなくなります。

更新も同じ姿勢を採ります: auto-updater はコミット SHA(可変タグではない)にピン留めし、hooks や MCP の変更を含む完全な diff を表示し、更新ごとに明示的な承認を要求します。自動適用モードはありません。

インストール後にスキルが問題を起こした場合: `/legal-builder-hub-ja:disable [skill]` はファイルを削除せずに無効化し、`/legal-builder-hub-ja:uninstall [skill]` は完全に削除します。両方ともこのハブからインストールされたコミュニティスキルに限定され、ファーストパーティのプラグイン・スキルには触れません。

## 前提条件

- registry-sync エージェントからの Slack 通知には、環境内に Slack MCP サーバーが設定されている必要があります。なければ、エージェントはダイジェストをファイルに書き込みます。
- `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` のデフォルトレジストリリストには、`lpm-skills` を除いて空のまま出荷されます。`/legal-builder-hub-ja:registry-browser` または `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` を直接編集して、信頼するレジストリを追加してください。

## コマンド

| コマンド | 機能 |
|---|---|
| `/legal-builder-hub-ja:cold-start-interview` | プラクティスプロファイル + スターターパック推奨 |
| `/legal-builder-hub-ja:registry-browser [query]` | 監視対象レジストリでスキルを検索 |
| `/legal-builder-hub-ja:skill-installer [skill]` | コミュニティスキルをインストール |
| `/legal-builder-hub-ja:auto-updater` | インストール済みスキルの更新をチェック |
| `/legal-builder-hub-ja:related-skills-surfacer` | 最近の活動に基づいてスキルを提案 |
| `/legal-builder-hub-ja:skills-qa [skill]` | インストール前に Legal Skill Design Framework に照らしてスキルを評価 |
| `/legal-builder-hub-ja:disable [skill]` | インストール済みコミュニティスキルをファイル削除せずに無効化 |
| `/legal-builder-hub-ja:uninstall [skill]` | ハブ経由でインストールされたコミュニティスキルをアンインストール |

## スキル

| スキル | 目的 |
|---|---|
| **cold-start-interview** | プラクティスプロファイル → スターターパック |
| **registry-browser** | 監視対象レジストリ横断検索 |
| **skill-installer** | 許可リストゲート、取得、raw SKILL.md 表示、トラストチェック、QA、コミュニティスキルのインストール |
| **uninstall** | ハブ経由でインストールされたコミュニティスキルをアンインストール(ファーストパーティのプラグイン・スキルは対象外) |
| **disable** | ファイル削除せずにコミュニティスキルを無効化、後で再有効化可 |
| **skill-manager** | リファレンス: `uninstall` と `disable` スキルが使う詳細なアンインストール / 無効化 / 再有効化ワークフロー |
| **skills-qa** | Legal Skill Design Framework に照らしてスキルを評価 — 設計、失敗モード、トラスト表面、プロンプトインジェクション・ヒューリスティック・スキャン |
| **auto-updater** | 更新をチェック; diff とトラストレビューを表示; 明示的な承認時のみ適用 |
| **related-skills-surfacer** | タスク後に関連するコミュニティスキルを表面化(直接またはフック経由) |

## インタラクティブコマンド vs. スケジュール済みエージェント

上記のコマンドは、案件作業中に呼び出されたときに実行されます。下記のエージェントはスケジュールに従って実行されます — あなたが見ていない間に動くものを把握するため:

| エージェント | 監視対象 | デフォルト頻度 |
|---|---|---|
| **registry-sync** | 監視対象レジストリの新規・更新スキル; 更新設定に従って通知を投稿 | 週次 |

## 監視対象レジストリ(デフォルト)

デフォルトの許可リストには、私たちがレビュー済みのコミュニティ・レジストリがあらかじめ設定されています。リポジトリの `references/allowlist-default.yaml`、または個別インストールの許可リスト `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/allowlist.yaml` を編集して、追加・削除や restrictive と permissive モードの切替を行ってください。

- **lpm-skills** — Legal project management(Scott Margetts / LegalOps Consulting)— `github.com/legalopsconsulting/lpm-skills`
- **Lawvable / awesome-legal-skills** — 法務向け AI エージェントスキルのキュレーション済みリスト — `github.com/lawvable/awesome-legal-skills`
- **Lawvable / agent-skills** — 法務向けエージェントスキルのキュレーション済みコレクション — `github.com/lawvable/agent-skills`
- 自身のレジストリを追加するには `/legal-builder-hub-ja:registry-browser` または許可リストを編集

## 学習のしくみ

`~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` のプラクティスプロファイルは静的ではありません — プラグインを使うにつれて改善されます。ハブは毎回の `/legal-builder-hub-ja:registry-browser` と `/legal-builder-hub-ja:related-skills-surfacer` でこれを再読込するので、プラクティスタイプ、業界、監視対象レジストリを調整すれば今後の推奨が鋭くなります。ファイルを直接編集するか、仕事の方向性が変わったら `/legal-builder-hub-ja:cold-start-interview --redo` を再実行してください。

## 注記

- コミュニティスキルはインストール前に読まれます。承認前に **raw の** SKILL.md(要約ではない)が表示されます。
- 自動更新はデフォルトでオフです。ソースを信頼するならスキルごとにオンにできます。
- related-skills-surfacer は他のプラグイン内で動作します: タスクを実行しているときに、コミュニティに関連スキルがあるかをチェックします。
- エンタープライズ / 事務所展開: `allowlist.yaml` で `mode: restrictive` を設定し、`registries`、`publishers`、`connectors` リストに値を入れてください。restrictive モードでは、リストにないソースからの取得・分析・インストールは拒否されます。
