# Diligence Grid — Managed Agent テンプレート

## 概要

バーチャルデータルーム(VDR)に対するバッチ文書レビュー。2つのモードがあります。

- **watch** — カットオフ以降の VDR の新規アップロードを監視し、デプロイチームのデューデリジェンス・リクエストリストのカテゴリに照らして分類し、高優先度カテゴリ(Material Contracts / Litigation / IP)のアップロードをフラグします。
- **grid** — 文書フォルダに対して列スキーマに基づく表形式レビューを実行します。1文書1行、1データポイント1列、すべてのセルに逐語(verbatim)ソース引用を伴います。M&A デューデリジェンスの主力ツール。

[`corporate-legal`](../../corporate-legal) プラグインと同一ソース ― このディレクトリは `POST /v1/agents` 向けの Managed Agent クックブックです。Grid モードは `tabular-review` スキルそのものを、複数の extractor ワーカーにわたるヘッドレスファンアウトで実行します。

## ⚠️ デプロイ前に

- **すべてのセルはリード(手がかり)であって、知見(finding)ではありません。** デューデリジェンスグリッドは、弁護士が原文を読んで初めて、表明保証・開示別紙・デューデリジェンスメモになります。各セルの逐語引用は、レビューアーが素早く検証できるようにするためのものです ― 活用してください。
- **重要性フィルタと列分類はヒューリスティクスを適用するもので、法的判断ではありません。** スキーマが「重要でない」と分類した契約が、案件を破談させる契約かもしれません。「answered」のセルでも、extractor が条項を読み違えていれば誤りです。レビューアーの作業量は `unclear` + `needs_review` + `answered` の合計とスケールします ― フラグされたものだけではありません。
- **Watch モードはメタデータとプレビューを分類するもので、文書全体ではありません。** 分類器が「低優先度」とタグ付けした新規アップロードが、案件を変えるサイドレターであるかもしれません。Watch レポートはキューとして扱い、フィルタとして扱わないでください。
- **相手方がアップロードした文書は、ツールチェーンにとっても信頼できない入力です。** Grid-writer の CSV 数式インジェクション対策は必須であり、オプションではありません ― 下記セキュリティセクション参照。

## デプロイ

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export BOX_MCP_URL=...
export GDRIVE_MCP_URL=...
export IMANAGE_MCP_URL=...          # 任意。使用する場合はツールセットのデフォルトを enabled に
export DEFINELY_MCP_URL=...         # 任意。normalizer パスの条項構造 QA 用
../../scripts/deploy-managed-agent.sh diligence-grid
```

## ステアリングイベント

[`steering-examples.json`](./steering-examples.json) を参照してください。

## セキュリティとハンドオフ

VDR 文書 ― 契約、取締役会議事録、サイドレター、相手方アップロード ― は**信頼できない入力**です。相手方がアップロードした契約には、レビューアーや下流ツールチェーンを操作することを意図した文字列が含まれることがあります。4階層の分離により、Write 権限と MCP 権限を文書から遠ざけます。

| ティア | 信頼できない文書に触れる? | ツール | コネクタ |
|---|---|---|---|
| **`doc-reader`** | **Yes**(読み取り専用) | `Read`, `Grep` | Box、Google Drive、iManage(読み取り) |
| **`extractor`** | **Yes**(読み取り専用) | `Read`, `Grep` | なし |
| `normalizer` / Orchestrator | No | `Read`, `Grep`, `Glob`, `Agent` | なし(definely は任意、読み取り専用) |
| **`grid-writer`**(Write 保有者) | No | `Read`, `Write` | なし |

`doc-reader` と `extractor` は長さ制限付き・スキーマ検証済みの JSON を返します。オーケストレーターと `normalizer` は構造化データのみを見ます。`grid-writer` は `./out/diligence-grid-<date>.csv`、`./out/diligence-grid-<date>_sources.csv`、`./out/diligence-grid-<date>-summary.md` を生成します。

**CSV 数式インジェクション。** `grid-writer` が書き込む全セル ― 値、逐語引用、位置、文書名、列ラベル ― は、最初の1文字が `=`、`+`、`-`、`@`、タブ、復帰文字でないか検査されます。該当するセルは、CSV に書き込む前に先頭にシングルクォート(`'`)を付加します。相手方アップロードの契約には、デプロイチームがファイルを開いた瞬間に Excel や Sheets が数式として実行してしまう文字列(`=HYPERLINK(...)` による外部送信、旧 Excel の `=cmd|...` DDE)が日常的に含まれます。ソース CSV は、逐語引用が攻撃者の制御下にある表面であるため、より大きな露出となります。

**Xlsx はデプロイ側の責務です。** クックブックは CSV のみを出力します。デプロイチームは [`corporate-legal/skills/tabular-review/references/excel-output.md`](../../corporate-legal/skills/tabular-review/references/excel-output.md) のワークブック構造に従って `.xlsx` に変換します ― 隠し `_source` 列、ホバー時の引用を持つセルコメント、状態ベースの塗りつぶし、列ごとの `Verified` ドロップダウン、`_schema` および `_summary` シート。その変換は、デプロイチームの Excel 面(Claude in Excel、openpyxl、Google Sheets API 経由)で行われます。ヘッドレスエージェントから xlsx を出すには、信頼できるランタイムとマクロ面が必要であり、本クックブックはあえてそれを前提としません。

**保証されないこと:** このエージェントが生成する全セルは**検証が必要なリード**であり、知見(finding)ではありません。レビューアーがソースを読み、引用を確認し、`Verified` 列にマークします。弁護士が、表明・別紙・メモに何を入れるかを決定します。

## 適応ノート

- **VDR URL。** `BOX_MCP_URL` / `GDRIVE_MCP_URL` / `IMANAGE_MCP_URL` をお客様のデータルームに合わせて設定してください。デフォルトは Box と Google Drive を有効にしています。iManage や Datasite を主に使う場合は [`agent.yaml`](./agent.yaml) の `default_config` を反転してください。VDR が Intralinks や Datasite の場合、`mcp_servers` と `tools` に対応する MCP URL のエントリを追加してください。
- **列スキーマ。** [`corporate-legal/skills/tabular-review/references/ma-diligence-columns.md`](../../corporate-legal/skills/tabular-review/references/ma-diligence-columns.md) の M&A デューデリジェンス標準がデフォルトです。テック/IP、ヘルスケア、不動産、政府契約、規制金融など、案件タイプに合わせて、そのリファレンスの追加項目を使ってカスタマイズしてください。
- **出力先。** 出力は `./out/` に書き出されます。お客様のデプロイパイプラインを通じて、案件フォルダ、Google Drive、iManage ワークスペース、Box フォルダに結線してください。`grid-writer` にアップロード用 MCP を渡さないでください ― お客様のアップロードステップにハンドオフする方がクリーンで、Write ティアの分離が保たれます。
- **デフォルトモード。** Watch と Grid はステアリングイベントごとに選択されます。ワークフローがほぼ片方に偏っている場合は、オーケストレーターのステアリングイベントテンプレートをそれに合わせてシードしてください。
- **リクエストリストカテゴリ。** Watch モードは、デプロイチームの corporate-legal `CLAUDE.md` 設定にあるカテゴリに照らして分類します。Watch モードを稼働案件に結線する前に、`/corporate-legal-ja:cold-start-interview` を再実行してください。
- **成果物ヘッダー。** `grid-writer` は、デプロイチームの `## Outputs` 設定からヘッダーを先頭に付加します。デプロイ前に、法務チームでヘッダーを確認してください ― レビューアーの役割(弁護士か非弁護士か)によって異なります。
- **Slack ルーティング。** このエージェントは直接投稿しません。レポートはファイルであり、`handoff_request` がオーケストレーターにルーティング先チャンネルを伝えます。デプロイチームの `CLAUDE.md` の House style セクションで案件チャンネルを設定してください。
