# Renewal Watcher — Managed Agent テンプレート

## 概要

契約リポジトリで今後の更新と解約期限をスキャンし、チームのプレイブックに照らしてクロスリファレンスし、今後の期限・プレイブック逸脱・エスカレーショントリガーを持つ契約をフラグし、アラートレポートを書き出します。Claude Code エージェント [`renewal-watcher`](../../commercial-legal/agents/renewal-watcher.md) およびスキル [`renewal-tracker`](../../commercial-legal/skills/renewal-tracker) と同一ソース ― このディレクトリは `POST /v1/agents` 向けの Managed Agent クックブックです。

これは**クックブックであって製品ではありません。** ペアになるプラグインが Ironclad を CLM として前提とするため、ここでもそれを前提とします。Agiloft、Ironclad 代替、iManage、または署名済み PDF の Google Drive を使うチームは、MCP エンドポイントを適宜スワップしてください。

## ⚠️ デプロイ前に

- **契約メタデータからプルされた解約期限と更新条件は誤っている可能性があります。** CLM メタデータは執行済み文書からドリフトします ― 修正契約が署名されても再取り込みされない、効力発生日が署名日と異なる、自動更新メカニクスが誤タグ付けされる、などです。計算された期限を解約または更新判断に依拠する前に、資格を有する弁護士が署名済み合意書と修正契約に照らして検証します。
- **エスカレーションルーティングは設定されたマトリクスに従いますが、エスカレーション判断自体は行いません。** フラグされたプレイブック逸脱が文脈上受け入れ可能であることもあり、フラグされていない条件でも注意が必要であることもあります。マトリクスはルーターであって、レビューアーではありません。
- **静かな週は綺麗な週ではありません。** 浮かび上がってこなかった契約が、CLM から欠落しているか、誤タグ付けされているか、メタデータがそれを反映しないまま通知期間を過ぎている可能性があります。All-clear フッターはエージェントが実行されたことを意味し、何もすべきことがないという意味ではありません。

## デプロイ

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export IRONCLAD_MCP_URL=...
export GDRIVE_MCP_URL=...
# 任意 — 署名済み合意書がここにある場合はマニフェストで有効化
export IMANAGE_MCP_URL=...
export DOCUSIGN_MCP_URL=...
../../scripts/deploy-managed-agent.sh renewal-watcher
```

## ステアリングイベント

[`steering-examples.json`](./steering-examples.json) を参照してください。デフォルトの月曜朝のスイープは 1 つ目の例を使用します。他の 2 つは、アドホックな相手方範囲のラン、および署名後の逸脱チェックをカバーします。

## セキュリティとハンドオフ

契約テキスト、相手方メッセージ、CLM コメントは**信頼できない入力**です。3 層の分離:

| ティア | 信頼できない文書に触れる? | ツール | コネクタ |
|---|---|---|---|
| **`repo-reader`** | **Yes** | `Read`, `Grep` のみ | ironclad、gdrive(読み取り専用); imanage はデフォルト off |
| `deadline-calculator` / Orchestrator | No | `Read`, `Grep`, `Glob`, `Agent` | なし |
| **`alert-writer`**(Write 保有者) | No | `Read`, `Write`, `Edit` | なし |

`repo-reader` は長さ制限・スキーマ検証済みの JSON を返します。`deadline-calculator` はその JSON とディスク上のプレイブック設定に対する純粋な計算です ― MCP なし、Web なし。`alert-writer` は `./out/renewal-alerts-<YYYY-MM-DD>.md` を生成し、Slack 配信のための `handoff_request` を発出します。

**ハンドオフ:** Orchestrator は、デプロイチームの House style 設定からのチャンネルを使って、`alert-writer` の `handoff_request` を Slack 送信ワーカーにルーティングします。エージェント自身は Slack メッセージを送信しません。

**関連エージェント:** `handoff_request` は、署名後の逸脱チェックが必要な場合は [`deal-debrief`](../../commercial-legal/agents/deal-debrief.md) に、更新時の逸脱がパターンに累積した場合は [`playbook-monitor`](../../commercial-legal/agents/playbook-monitor.md) にもルーティングできます。名前付きエージェントは互いに直接呼び出すことはなく、ルーティングは Orchestrator の仕事です。

**保証されないこと:** このエージェントはアクションを推奨します。解約するか、再交渉するか、更新を走らせるかは弁護士が決定します。

## 適応ノート

ワークフローで出力を信頼する前に:

- **CLM に向ける。** デフォルトは `IRONCLAD_MCP_URL` です。署名済み合意書が iManage にある場合、`agent.yaml` と `subagents/repo-reader.yaml` で `imanage` を `default_config: { enabled: true }` に反転し、`IMANAGE_MCP_URL` を設定してください。Google Drive フォルダにある場合は、`gdrive` と repo-reader のフォールバック検索パスに依拠してください。公開 MCP のない CLM(Agiloft、Conga)にある場合は、カスタムコネクタを結線し、MCP サーバブロックを更新してください。
- **Slack チャンネルを設定する。** alert-writer は Slack チャンネルを指名する `handoff_request` を発出します。Orchestrator は、お客様のプレイブック設定の **House style → Renewal alerts** フィールドからそのチャンネルを読みます。初回スケジュールド実行の前に設定してください。さもないとハンドオフはデッドレターになります。
- **先読みウィンドウをチューニングする。** deadline-calculator のデフォルトティアは overdue / 30 / 60 / 90 / 180 日です。更新サイクルがより短い(1 年未満の SaaS オーダーフォーム)、またはより長い(12 か月の通知ウィンドウを持つ複数年エンタープライズ MSA)場合、deadline-calculator プロンプトと `alert-writer.yaml` の対応セクションでティア閾値を調整してください。
- **エスカレーションマトリクスを調整する。** deadline-calculator はプレイブックのエスカレーションマトリクスを読み、`escalation_needed: true` を設定するかどうか、誰にルーティングするかを決定します。スケジュールド実行を有効化する前に、マトリクスが現在の承認権限(自動更新を流すサインオフは誰、ドル閾値超の再交渉のサインオフは誰)を反映していることを確認してください。フォーマット用に [`escalation-flagger`](../../commercial-legal/skills/escalation-flagger) スキルが `alert-writer` にロードされます。
- **成果物ヘッダーを確認する。** `agent.yaml` の headless append はあなたのプレイブックの成果物ヘッダーを先頭付加するようエージェントに指示します。起動前に GC でヘッダー文言を確認してください。
- **頻度。** デフォルトは週次。大量契約のチームは日次、小規模チームは月次でよいでしょう。頻度はお客様自身のワークフローエンジンに存在します ― クックブックは自身をスケジュールしません。
