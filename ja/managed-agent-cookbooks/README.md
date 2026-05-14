# 法務向け Managed Agent テンプレート

本リポジトリの各エージェントは**2通りの提供形態**を持ちます。1つは今すぐインストールできる Claude Code プラグイン(リポジトリルートにある縦割りディレクトリを参照)、もう1つは自社のワークフローエンジンの背後にプラットフォームチームがデプロイする**Claude Managed Agent**テンプレートです。**同じエージェント、同じスキル ― 提供面を選んでください。** 以下の各ディレクトリは、対応するプラグインの正規 system prompt とスキルを参照するデプロイマニフェストであり、Single Source of Truth(信頼できる単一の情報源)となります。

これらは**クックブックであって製品ではありません。** あくまで出発点です。お客様の文書管理システム、契約リポジトリ、Slack ワークスペース、通知ルーティング、レビュー頻度に合わせて適応させてください。そのままでは動かず、また動くことを意図していません。

`../scripts/deploy-managed-agent.sh <slug>` を実行すると、スキルがアップロードされ、リーフワーカーが作成され、解決済みの設定で `POST /v1/agents` が呼び出されます。各テンプレートには [`steering-examples.json`](./reg-monitor/steering-examples.json) と、セキュリティティアおよびハンドオフを記載したエージェント別 README が同梱されています。

| Agent | 縦割りプラグイン | 監視対象 | CMA ステアリングイベント | リーフワーカー |
|---|---|---|---|---|
| [`reg-monitor`](./reg-monitor/) | regulatory-legal | 規制フィード(Federal Register、各機関 RSS、TR) | `Check feeds as-of <date>, materiality: <threshold>` | feed-reader · materiality-filter · **digest-writer** |
| [`renewal-watcher`](./renewal-watcher/) | commercial-legal | 契約リポジトリ(Ironclad)の更新・解約期限 | `Scan renewals <X>–<Y> days out, flag playbook deviations` | repo-reader · deadline-calculator · **alert-writer** |
| [`diligence-grid`](./diligence-grid/) | corporate-legal | バーチャルデータルーム(Box、Datasite、Intralinks、iManage)への新規アップロード+バッチレビュー | `Review folder <path> against schema <schema-id>` | doc-reader · extractor · normalizer · **grid-writer** |
| [`launch-radar`](./launch-radar/) | product-legal | プロダクトロードマップ / ローンチトラッカー(Jira、Linear、Asana)で法務レビューが必要なローンチ | `Scan tracker for launches in next <N> weeks` | tracker-reader · risk-classifier · **memo-writer** |
| [`docket-watcher`](./docket-watcher/) | litigation-legal | 裁判所のドケット(Trellis、CourtListener)で新規提出書面・期限・成果物を監視 | `Watch docket <case-id> in <court>, matter <matter-id>` | docket-reader · deadline-mapper · **tracker-writer** |

**太字**のリーフ = `Write` を持つ唯一のワーカー。

## マニフェスト vs API

`agent.yaml` ファイルは実際の `POST /v1/agents` のフィールド名を使用しており、デプロイスクリプトが解決するいくつかの便宜的記法を含みます。

| マニフェスト記法 | 解決後 |
|---|---|
| `system: {file: ../../<plugin>/agents/<agent>.md, append: "..."}` | `system: "<inlined contents + append>"` |
| `system: {text: "..."}` | `system: "<text>"` |
| `skills: [{from_plugin: ../../<plugin>}]` | そのディレクトリ配下の `skills/*` をすべてアップロード → `[{type: custom, skill_id: ...}, ...]` |
| `skills: [{path: ../../...}]` | `skills: [{type: custom, skill_id: <uploaded-id>}]` |
| `callable_agents: [{manifest: ./subagents/x.yaml}]` | `callable_agents: [{type: agent, id: <created-id>, version: latest}]` |

> **リサーチプレビュー:** `callable_agents`(マルチエージェント委任)は**1階層**の委任のみサポートします。オーケストレーターはワーカーを呼び出せますが、ワーカーがさらにサブエージェントを呼び出すことはできません。

## エージェント間のハンドオフ

名前付きエージェントは互いに直接呼び出すことはありません。あるエージェントが別のエージェントを必要とする場合(例: `launch-radar` が完全なレビューメモを要するローンチを発見した場合)、その出力に `handoff_request` を発します。[`../scripts/orchestrate.py`](../scripts/orchestrate.py)(またはお客様のイベントバス)が、それを新たなステアリングイベントとして対象セッションにルーティングします。リファレンススクリプトはターゲットをハードコード許可リストで縛り、ペイロードをスキーマ検証します。

## セキュリティモデル

法務文書と裁判所への提出書面は**信頼できない入力**です。すべてのクックブックは3層のワーカー分離を採用しています。

1. **Reader** は信頼できない文書に触れ、`Read`/`Grep` のみを持ちます ― MCP なし、Write なし、ネットワークなし。長さ制限付きの構造化 JSON を返します。文書に埋め込まれた指示はデータであってコマンドではありません。
2. **Analyzer** は Reader から構造化 JSON を受け取り、ユーザー設定のルールを適用し、検証のための MCP 読み取りアクセスを持ちます。Write なし。
3. **Writer** は最終出力を生成し、`Write` を持つ唯一のティアです。Writer は生文書を一切見ません。

オーケストレーターは Write を持たず、生文書も読みません。ルーティングするだけで、ハンドリングはしません。

## 成果物と秘匿特権

通常のデプロイにおいて、これらのエージェントが生成するものはすべて**Attorney Work Product(弁護士成果物の保護法理の対象)**です。各マニフェストの headless append は、ユーザーのプラグイン設定にある成果物ヘッダーをエージェントに先頭付加するよう指示します。デプロイ前に、お客様の法務チームでヘッダーを確認してください。お客様のデプロイで保持すべきでない素材を処理する場合は、起動前に Anthropic のデータ保持設定と自社のストレージ保持を確認してください。

## 得られるもの、得られないもの

- **得られるもの:** 動作するマニフェスト構造、合理的なセキュリティティアを備えたリファレンスアーキテクチャ、Claude Code プラグインで実証済みのスキル、ステアリングイベントの例。
- **得られないもの:** 本番運用可能なエージェント。お客様のシステムへの MCP コネクタの結線、頻度の設定、通知ルーティングの設定、実務に合わせたプロンプトのチューニング、そして出力を信頼する前のお客様自身の評価が必要です。
- **特に得られないもの:** 弁護士の代替。これらのエージェントは監視・抽出・ドラフトを行います。レビュー・検証・判断は弁護士の役割です。
