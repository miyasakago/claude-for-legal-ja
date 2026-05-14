# Reg Monitor — Managed Agent テンプレート

## 概要

スケジュールに従って規制フィードをチェックし、デプロイチームの重要性閾値でフィルタし、常時重要な項目についてポリシーライブラリに対するクイックギャップチェックを実行し、ダイジェストを書き出します。Claude Code エージェント [`reg-change-monitor`](../../regulatory-legal/agents/reg-change-monitor.md) およびスキル [`reg-feed-watcher`](../../regulatory-legal/skills/reg-feed-watcher) / [`policy-diff`](../../regulatory-legal/skills/policy-diff) と同一ソース ― このディレクトリは `POST /v1/agents` 向けの Managed Agent クックブックです。

## ⚠️ デプロイ前に

- **ダイジェストアイテムはスクリーニングされたリードであって、法的結論ではありません。** 重要性フィルタは設定可能な閾値を適用するもので、法的判断ではありません。エージェントが「informational」と分類した規制変更が、ビジネスに重要であることはあり得ます。「material」とフラグした変更が、結局適用されないことも判明し得ます。すべてのダイジェストをレビューしてください。資格を有する弁護士が、項目がアクション、開示、ポリシー変更、エスカレーションを要するかを決定します。
- **ポリシーギャップチェックは初回パスであり、適用性の法的評価ではありません。** ギャップ表面は、新規制テキストをポリシーライブラリにヒューリスティクスで比較します。「gap」は弁護士が評価すべきリードであり、「aligned」結果がコンプライアンスを証明するわけではありません。
- **重要性閾値はあなたのキャリブレーションであって、法律ではありません。** `## Materiality threshold` セクションが古い、または異なるリスクポスチャ向けにチューニングされている場合、トリアージも古いです。スケジュールド実行を有効化する前に再確認してください。
- **ウォッチリストはあなたが行ったカバレッジ主張です。** ウォッチリストにない規制当局も、重要な何かを公表する可能性があります。規制当局の欠落は設定バグであり、フィードバグではありません。

## デプロイ

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export GDRIVE_MCP_URL=...
../../scripts/deploy-managed-agent.sh reg-monitor
```

## ステアリングイベント

[`steering-examples.json`](./steering-examples.json) を参照してください。デフォルトの週次スイープは 1 つ目の例を使用します。他の 2 つは、特定の動向への対象限定ディープチェックと、フラグされた項目のギャップ分析をカバーします。

## セキュリティとハンドオフ

規制フィードのコンテンツ(Federal Register エントリー、機関 RSS の投稿、TR alerts 通知)は**信頼できない入力**です。3 層の分離:

| ティア | 信頼できない文書に触れる? | ツール | コネクタ |
|---|---|---|---|
| **`feed-reader`** | **Yes** | `Read`, `Grep`, `WebFetch` のみ | なし |
| `materiality-filter` / Orchestrator | No | `Read`, `Grep`, `Glob`, `Agent` | gdrive(Orchestrator のみ) |
| **`digest-writer`**(Write 保有者) | No | `Read`, `Write`, `Edit` | なし |

`feed-reader` は長さ制限・スキーマ検証済みの JSON を返します。`materiality-filter` はその JSON とディスク上の regulatory-legal 設定に対する純粋な計算です ― MCP なし、Web なし。`digest-writer` は `./out/reg-digest-<YYYY-MM-DD>.md` を生成し、Slack 配信のための `handoff_request` を発出します。

**ハンドオフ:** Orchestrator は、デプロイチームの House style 設定からのチャンネルを使って、`digest-writer` の `handoff_request` を Slack 送信ワーカーにルーティングします。エージェント自身は Slack メッセージを送信しません。

**保証されないこと:** このエージェントは変更を浮かび上がらせ、潜在的なポリシーギャップをフラグします。規制変更がアクションを要するかどうか、誰がレスポンスをオーナーするかは弁護士が決定します。

## 適応ノート

ワークフローで出力を信頼する前に:

- **`feed-reader` をあなたのソースに向ける。** デフォルトターゲットは Federal Register(無料の公開 API、MCP 不要)です。あなたの法律事務所が Thomson Reuters Regulatory Intelligence、Bloomberg Law、または直接の機関 RSS に加入している場合、feed-reader の web_fetch 許可リストにエンドポイントを追加し、Orchestrator のスキャンプランを調整してください。無料ソースしかない場合でも、Federal Register API 単体で実用可能です。
- **(任意で)Thomson Reuters MCP URL を設定する。** マニフェストでは TR はコメントアウトされています。チームが課金している場合、結線して `enabled: true` を反転してください。
- **ダイジェスト配信チャンネルを設定する。** digest-writer は Slack チャンネルを指名する `handoff_request` を発出します。Orchestrator は、お客様の regulatory-legal 設定の **House style → Reg digest** フィールドからそのチャンネルを読みます。初回スケジュールド実行の前に設定してください。さもないとハンドオフはデッドレターになります。ダイジェストをメールや Confluence ページで受け取りたいチームは、Orchestrator の許可リストでハンドオフターゲットをスワップしてください。
- **重要性閾値をチューニングする。** materiality-filter はあなたの設定の `## Materiality threshold` セクション ― always material / review-worthy / FYI ― を読みます。スケジュールド実行を有効化する前に、ティアが現在のリスクポスチャを反映していることを確認してください。閾値が低すぎるとダイジェストが溢れ、高すぎると締切のある義務を見逃します。
- **ウォッチリストを更新する。** materiality-filter は `## Regulators we watch` テーブルも読みます。あなたのフットプリントが変わったら規制当局を追加または削除してください。
- **成果物ヘッダーを確認する。** `agent.yaml` の headless append はあなたの設定の成果物ヘッダーを先頭付加するようエージェントに指示します。起動前に GC でヘッダー文言を確認してください。
- **頻度。** デフォルトは週次。活発な規制環境(金融サービスのルールメイキングサイクル、クロスボーダー AI 規制)は日次が適切な場合があります。頻度はお客様自身のワークフローエンジンに存在します ― クックブックは自身をスケジュールしません。
