# コネクターの追加

各プラグインは、信頼できる情報源に接続されたときに最大限の力を発揮します。法務データソース、リサーチツール、CLM(契約ライフサイクル管理)、DMS(文書管理システム)、eDiscovery プラットフォーム、または案件管理システムを構築・運用している方は、ぜひ皆様の MCP コネクターを本スイートに加えてください。

## 優れた法務 MCP コネクターの条件

- **HTTPS 経由のリモート MCP サーバー** で OAuth または API キー認証(streamable HTTP または SSE トランスポート)
- **読み取り重視のツール** — search、fetch、list。書き込みツール(create, send, file)はクライアント側で明示的な確認プロンプトが必要 — ツールの description にその旨を記載してください。
- **結果にプロベナンス(出所情報)を付与** — ソース、取得日、引用に使える識別子を返す。各プラグインはすべての引用にソース別タグを付けるため、コネクターはこれを可能にすべきです。
- **結果に指示風の内容を含めない** — プラグインは取得コンテンツをデータとして扱い、命令としては扱いません。ツール結果にメタデータやシステムノートを含める場合は、埋め込み指示に見えないよう明確に印を付けてください。
- **レート制限とエラーをグレースフルに劣化させる** — コネクターが応答しない場合のフォールバックがプラグインに備わっています。タイムアウトより、きれいなエラーが好ましいです。

## 提出方法

1. MCP サーバーを公開し、ツール、認証フロー、データカバレッジをドキュメント化してください。
2. 該当プラグインの `.mcp.json` に、サーバーの URL、認証方法、Claude に提供する内容の 1 行説明を加える PR を開いてください。
3. どのプラクティスエリア / プラグインに最も有用かのメモを添えてください。
4. プラグインのワークフローに対してテストし、マージします。取得品質とインジェクション耐性チェックをパスしたコネクターはデフォルトの `.mcp.json` に入ります。それ以外はプラグインの README で文書化し、ユーザーが自身で追加できるようにします。

## 現在のコネクター

各プラグインのデフォルト `.mcp.json` に含まれるコネクター:

| コネクター | プラグイン |
|---|---|
| **Slack** | 全 12 個 |
| **Google Drive** (`gdrive`) | 全 12 個 |
| **CourtListener** | legal-clinic, ip-legal, litigation-legal, law-student |
| **Descrybe** | legal-clinic, ip-legal, law-student |
| **Definely** | commercial-legal, corporate-legal |
| **iManage** | commercial-legal, corporate-legal |
| **Solve Intelligence** | corporate-legal, ip-legal |
| **TopCounsel** | commercial-legal, corporate-legal, litigation-legal |
| **Box** | corporate-legal |
| **Ironclad** | commercial-legal |
| **DocuSign / DocuSign CLM** | commercial-legal |
| **Everlaw** | litigation-legal |
| **Trellis** | litigation-legal |
| **Aurora** | litigation-legal |
| **Courtroom5** | legal-clinic |
| **Lawve AI** | legal-builder-hub |
| **Linear** | product-legal |
| **Atlassian (Jira)** | product-legal |
| **Asana** | product-legal |

正確なリストは各プラグインディレクトリの `.mcp.json` を参照してください。

## 募集中のコネクター

以下は、特定プラグインを大幅に有用にするコネクターです。構築・運用されている方は上記「提出方法」をご参照ください。

- **IP 管理システム**(Anaqua, Clarivate IPfolio, AppColl, Patrix, Alt Legal, FoundationIP) — `ip-legal` ポートフォリオ追跡のためのフル ドケット同期
- **USPTO カスタマー番号経由** — 出願単位ではなくフルポートフォリオのステータス・期限
- **USPTO TSDR / Trademark Status** — `ip-legal` のブランド管理向け商標ステータス・期限
- **OSS リクエスト用 Jira / Linear / Asana** — `ip-legal` の OSS クリアランスが受信チケットを監視・対応可能に
- **Thomson Reuters**(CoCounsel, Practical Law, Westlaw) — 全プラグイン向けのリサーチ・起案
- **SS&C Intralinks / Datasite** — `corporate-legal` のデューデリジェンス用 VDR アクセス
- **Relativity / Everlaw の読み取り以上** — `litigation-legal` の eDiscovery ワークフロー
- **米国州バー CLE トラッカー** — `law-student` バー試験対策
- **裁判所電子提出システム**(PACER 書き込み、州 e-filing) — 当然ながら厳格な不可逆性ゲート付きで
- **Global AI Regulation Tracker**(techieray.com/GlobalAIRegulationTracker) — 法域タグ付き AI 規制トラッキングと構造化 API。キュレート・検証済みのマルチ法域。`ai-governance-legal` と `regulatory-legal` の準一次情報源フィードとして有用。
- **規制の一次情報源** — 公式レジスター(eCFR, Federal Register, EUR-Lex, legislation.gov.uk, Federal Register of Legislation AU, Singapore Statutes Online)へのコネクター — 多くの立法サイトが用いるエージェント遮断機構を回避できるもの。キュレートされた規制ナレッジベースは価値の高い追加になります。

## 質問

本リポジトリで Issue を開いてください。パートナーシップ・統合に関する質問は、各プラグインの README に記載の連絡先をご参照ください。
