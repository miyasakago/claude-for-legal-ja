# Claude for Legal(日本語翻訳版 / Unofficial Japanese Translation)

> [!WARNING]
> **これは非公式の日本語翻訳版です。** Anthropic PBC が公認または保証するものではありません。
> - **元著作物(公式 / 英語):** https://github.com/anthropics/claude-for-legal
> - **本翻訳版:** https://github.com/miyasakago/claude-for-legal-ja
> - **ライセンス:** [Apache License 2.0](LICENSE)(元と同一)
> - **派生物としての告知・変更内容:** [NOTICE.md](NOTICE.md)
> - 翻訳の正確性は保証しません。法的判断にあたっては必ず英語原文をご参照ください。
> - 本派生物は米国法務実務を題材としており、**日本法へのローカライズは行っていません**。
> - **Claude**, **Anthropic** ほか本書中の各社製品名は各社の商標です(詳細は [NOTICE.md](NOTICE.md))。

---

最もよく見られる法務ワークフロー — 社内商事、プライバシー、プロダクト、コーポレート、雇用、訴訟、規制、AI ガバナンス、IP、そして法務実務の学習側面(ロースクールクリニックと学生) — のリファレンス・エージェント、スキル、データコネクター集です。

> **初めての方へ:** まずは [QUICKSTART.md](QUICKSTART.md) をご覧ください — 60 秒でインストールできます。本 README は完全なリファレンスです。

ここにあるすべては、**1 つのソースから 2 通りの方法**で利用できます: [Claude Cowork](https://claude.com/product/cowork) または [Claude Code](https://claude.com/product/claude-code) のプラグインとしてインストールするか、[Claude Managed Agents API](https://docs.claude.com/en/api/managed-agents) を介して独自のワークフローエンジンの背後にデプロイするか。同じシステムプロンプト、同じスキル — 動作場所は自由に選べます。

## Cowork で始める
- [Claude Desktop をインストール](https://claude.com/download)
- Claude Cowork へのアクセスを取得
- 下記の動画の手順に従ってください:

https://github.com/user-attachments/assets/51394f0a-5277-4fe2-b81c-5c5e9ac876b5

> [!IMPORTANT]
> **これらのプラグインからのすべての出力は弁護士レビューを前提とする下書きであり、法的助言ではなく、法的結論でもなく、弁護士の代替でもありません。** これを反映したガードレールが組み込まれています: すべての引用に出典属性、秘匿特権と主観的法的判断に関する保守的なデフォルト、法域の前提の明示、書類提出・送付・依拠の前の明示的ゲート。弁護士がレビューし、検証し、外部に出るすべてに専門的責任を負います。これらのプラグインはそのレビューを高速化するものであり、置き換えるものではありません。
>
> **これらのプラグインは Anthropic の法的見解を表すものではありません。** これらは弁護士が論点を分析する助けとなるツールです。スキルにチェックリスト項目、推奨フレームワーク、リスクフラグ、判例または規制ガイダンスの特徴付けが含まれる場合、それはレビュー弁護士自身の分析の助けであり、Anthropic の法解釈の表明ではありません。本領域の多くは未確定で発展途上です。プラグインを使用する弁護士 — プラグインや Anthropic ではなく — が、業務成果物において取った法的立場に責任を負います。

リポジトリの内容:

- **プラクティスエリア・プラグイン**: 社内、法律事務所、学術領域の法務をカバー — それぞれがコールドスタート・インタビューを中心に構築され、貴社のプレイブックを学習し、すべてのスキルが読み込む `CLAUDE.md` プラクティスプロファイルを書き出します。
- **Managed Agent クックブック**: スケジュール実行・フィード監視型のワークフロー(更新ウォッチャー、ドケットウォッチャー、規制フィード監視、デューデリジェンス・グリッド、ローンチレーダー)向け。
- **MCP コネクター**: 汎用プロダクティビティ(Slack, Google Drive, Box)と法務固有システム(Ironclad, DocuSign, iManage, Everlaw, CourtListener など)にわたる。
- **[名付きエージェント](#エージェント)**: エンドツーエンドのワークフローエージェント(Vendor Agreement Reviewer, DSAR Responder, Termination Reviewer, Claim Chart Builder 等)— 職務スタイルの名前と、1 つのコマンドで実行できます。

## エージェント

各エージェントは、それが実行するワークフローにちなんで名付けられています。これらは最も一般的な表面です — 自分の業務に合うものから始め、そこからベースとなるスキル、プラクティスプロファイル、コネクターをチームの実務に合わせて調整してください。

| エージェント | 何をするか | プラグイン | コマンド |
|---|---|---|---|
| **Vendor Agreement Reviewer** | ベンダー MSA をプレイブックに照らしてレビューし、レッドラインメモを作成 | `commercial-legal-ja` | `/commercial-legal-ja:review` |
| **NDA Triager** | 受信 NDA を GREEN/YELLOW/RED でトリアージ — 難しいものだけ弁護士机へ | `commercial-legal-ja` | `/commercial-legal-ja:review` |
| **Amendment Tracer** | 基本契約と全変更契約にわたる契約変更履歴をトレース | `commercial-legal-ja` | `/commercial-legal-ja:amendment-history` |
| **Renewal Watcher** | 契約台帳の解約・更新期限をスキャン | `commercial-legal-ja` | スケジュールエージェント |
| **Deal Debrief** | 締結済み合意のプレイブック逸脱を週次でスイープ — 記憶が新しいうちに弁護士へ文脈の記録を促す | `commercial-legal-ja` | スケジュールエージェント |
| **Playbook Monitor** | 逸脱ログを監視し、条項がドリフトしたときにプレイブック更新を提案 | `commercial-legal-ja` | スケジュールエージェント |
| **Escalation Router** | 契約論点を適切な承認者にルーティングし、依頼文を起案 | `commercial-legal-ja` | `/commercial-legal-ja:escalation-flagger` |
| **Tabular Diligence Review** | データルームに対する表形式レビュー — 文書 1 つにつき 1 行、すべてのセルに引用 | `corporate-legal-ja` | `/corporate-legal-ja:tabular-review` |
| **Issue Extractor** | VDR 文書を読み、自社カテゴリと重要性基準に従って論点を抽出 | `corporate-legal-ja` | `/corporate-legal-ja:diligence-issue-extraction` |
| **Board Consent Drafter** | 全会一致書面決議を自社書式と先例検索で起案 | `corporate-legal-ja` | `/corporate-legal-ja:written-consent` |
| **Material Contracts Schedule Builder** | 株式譲渡契約の重要性基準に対してデューデリジェンス所見から開示別紙を構築 | `corporate-legal-ja` | `/corporate-legal-ja:material-contract-schedule` |
| **Entity Compliance Tracker** | 法域・法人種別にわたる提出期限を計算し、ヘルスオーディットを実施 | `corporate-legal-ja` | `/corporate-legal-ja:entity-compliance` |
| **Closing Checklist Driver** | クロージングを阻害するすべての条件、同意、文書、提出物を追跡 | `corporate-legal-ja` | `/corporate-legal-ja:closing-checklist` |
| **Integration Runbook** | 同意取得追跡と週次ステータスを伴う段階的なクロージング後統合計画 | `corporate-legal-ja` | `/corporate-legal-ja:integration-management` |
| **Data Room Watcher** | VDR の新規アップロードを監視し、クロージング・チェックリスト状況をスケジュール投稿 | `corporate-legal-ja` | スケジュールエージェント |
| **Termination Reviewer** | 提案された解雇を法域固有のリスクフラグに対して実行 | `employment-legal-ja` | `/employment-legal-ja:termination-review` |
| **Hire Reviewer** | オファーレターと制限的誓約を法域チェック付きでレビュー | `employment-legal-ja` | `/employment-legal-ja:hiring-review` |
| **Worker Classification Screener** | 提案された就業形態を支配的な州別テストに対してテスト | `employment-legal-ja` | `/employment-legal-ja:worker-classification` |
| **Leave Tracker** | FMLA / CFRA / PFL / ADA の期限と判断時点アラート付きで休職を監視 | `employment-legal-ja` | スケジュールエージェント |
| **Investigation Lead** | 内部調査案件を開始、追跡、追記、要約 | `employment-legal-ja` | `/employment-legal-ja:investigation-open` |
| **Policy Drafter** | 法律が異なる州別補足付きで雇用規程を起案 | `employment-legal-ja` | `/employment-legal-ja:policy-drafting` |
| **International Expansion Planner** | 新規国への EOR-vs-法人設立計画と外部弁護士ブリーフィングを開始 | `employment-legal-ja` | `/employment-legal-ja:expansion-kickoff` |
| **Wage & Hour Q&A** | 「サクッと質問」チャネル向けの法域対応雇用 Q&A | `employment-legal-ja` | `/employment-legal-ja:wage-hour-qa` |
| **DSAR Responder** | 法定期限内に DSAR 受領通知と実質回答を起案 | `privacy-legal-ja` | `/privacy-legal-ja:dsar-response` |
| **DPA Reviewer** | 管理者・処理者いずれの立場でもプレイブックに照らして DPA をレビュー | `privacy-legal-ja` | `/privacy-legal-ja:dpa-review` |
| **PIA Generator** | 新機能・新活動向けにプライバシー影響評価を自社書式で生成 | `privacy-legal-ja` | `/privacy-legal-ja:pia-generation` |
| **Privacy Triager** | 処理活動に PIA が必要か、強制 GDPR DPIA が必要か、進めてよいかを判定 | `privacy-legal-ja` | `/privacy-legal-ja:use-case-triage` |
| **Privacy Reg Gap Checker** | 新規または変更規制を現行のプライバシーポリシー・実務に対して差分比較 | `privacy-legal-ja` | `/privacy-legal-ja:reg-gap-analysis` |
| **Privacy Policy Monitor** | 保存済み PIA、DPA レビュー、トリアージ結果をスイープしポリシー乖離を検出 | `privacy-legal-ja` | `/privacy-legal-ja:policy-monitor` |
| **Launch Reviewer** | 自社リスク基準に照らして製品ローンチをレビュー | `product-legal-ja` | `/product-legal-ja:launch-review` |
| **Marketing Claims Checker** | 実証根拠、言い換え、削除が必要なコピーをフラグ付け | `product-legal-ja` | `/product-legal-ja:marketing-claims-review` |
| **「これは問題ですか?」トリアージ** | 短い Slack 質問への高速回答 — 自社基準にパターンマッチ | `product-legal-ja` | `/product-legal-ja:is-this-a-problem` |
| **Launch Watcher** | ローンチトラッカーを監視し、法務レビューが必要なローンチを察知 | `product-legal-ja` | スケジュールエージェント |
| **Reg Feed Watcher** | 規制フィードをポーリングし、月曜朝のダイジェストを作成 | `regulatory-legal-ja` | スケジュールエージェント |
| **オンデマンド Reg チェック** | 規制フィードを今すぐ確認し、前回チェック以降の新着を報告 | `regulatory-legal-ja` | `/regulatory-legal-ja:reg-feed-watcher` |
| **Policy Diff** | 特定の規制変更をインデックス化されたポリシーライブラリと差分比較 | `regulatory-legal-ja` | `/regulatory-legal-ja:policy-diff` |
| **Gap Tracker** | 未対応ギャップトラッカー — フラグ済みで未クローズの項目 | `regulatory-legal-ja` | `/regulatory-legal-ja:gaps` |
| **Policy Redrafter** | ギャップを閉じるマークアップ済みポリシー再ドラフト — ポリシーオーナーのレビュー向け提案、ソース文書への直接編集ではない | `regulatory-legal-ja` | `/regulatory-legal-ja:policy-redraft` |
| **NPRM Comment Tracker** | 未対応 NPRM パブコメ期間のレビュー、決定の記録、期限追跡 | `regulatory-legal-ja` | `/regulatory-legal-ja:comments` |
| **AI Use Case Triager** | 提案された AI ユースケースをレジストリに照らして分類 | `ai-governance-legal-ja` | `/ai-governance-legal-ja:use-case-triage` |
| **AI Impact Assessor** | 適用範囲のレジームにわたる AIA を実施 | `ai-governance-legal-ja` | `/ai-governance-legal-ja:aia-generation` |
| **Vendor AI Reviewer** | ベンダー AI 規約をデータ学習利用、責任、モデル変更、ポリシーギャップ観点でレビュー | `ai-governance-legal-ja` | `/ai-governance-legal-ja:vendor-ai-review` |
| **AI Reg Gap Checker** | 新規 AI 規制を現行ガバナンススタンスに対して差分比較 | `ai-governance-legal-ja` | `/ai-governance-legal-ja:reg-gap-analysis` |
| **AI Policy Monitor** | 保存済み AIA、トリアージ結果、ベンダーレビューをスイープし AI ポリシー乖離を検出 | `ai-governance-legal-ja` | `/ai-governance-legal-ja:policy-monitor` |
| **Trademark Clearance Screener** | ノックアウト・チェックと混同惹起ヒューリスティクスによる初回クリアランス | `ip-legal-ja` | `/ip-legal-ja:clearance` |
| **Cease & Desist Drafter** | 自社の執行スタンスに合わせて C&D を起案またはトリアージ | `ip-legal-ja` | `/ip-legal-ja:cease-desist` |
| **DMCA Takedown** | テイクダウンを起案、受領分をトリアージ、§512(g) 異議通知の起案 | `ip-legal-ja` | `/ip-legal-ja:takedown` |
| **OSS Compliance Checker** | オープンソースライセンスをデプロイメントモデルに照らして分類 | `ip-legal-ja` | `/ip-legal-ja:oss-review` |
| **FTO Triager** | ブロッキング可能性のある特許の構造化された初回チェック — トリアージであって意見書ではない | `ip-legal-ja` | `/ip-legal-ja:fto-triage` |
| **Infringement Triager** | TM / 著作権 / 特許 / 営業秘密にわたる侵害トリアージ — ファクターであって認定ではない | `ip-legal-ja` | `/ip-legal-ja:infringement-triage` |
| **IP Clause Reviewer** | 譲渡、所有権、ライセンス許諾、保証、補償をレビュー | `ip-legal-ja` | `/ip-legal-ja:ip-clause-review` |
| **IP Portfolio Tracker** | 登録、更新、維持年金、使用宣言 | `ip-legal-ja` | `/ip-legal-ja:portfolio` |
| **IP Renewal Watcher** | IP ポートフォリオ台帳からのスケジュール期限レポート | `ip-legal-ja` | スケジュールエージェント |
| **Claim Chart Builder** | 要素別クレームチャート — 特許または民事訴訟原因 | `litigation-legal-ja` | `/litigation-legal-ja:claim-chart` |
| **Docket Watcher** | 訴訟ドケットの提出と期限を監視 | `litigation-legal-ja` | スケジュールエージェント |
| **Demand Letter Drafter** | FRE 408 認識と送付ゲート付きで要求書を起案 | `litigation-legal-ja` | `/litigation-legal-ja:demand-draft` |
| **Demand Intake** | 起案前の文脈収集 — 当事者、事実、根拠、レバレッジ、特権 | `litigation-legal-ja` | `/litigation-legal-ja:demand-intake` |
| **Demand Received Triage** | 受領要求書のトリアージ — 選択肢、ポートフォリオ照会、引き継ぎ | `litigation-legal-ja` | `/litigation-legal-ja:demand-received` |
| **Subpoena Triage** | 新規召喚状の分類、範囲設定、対応計画 | `litigation-legal-ja` | `/litigation-legal-ja:subpoena-triage` |
| **Chronology Builder** | 宣言されたソースとアップロードから時系列を構築・更新 | `litigation-legal-ja` | `/litigation-legal-ja:chronology` |
| **Deposition Prep** | 事案理論と紐づけた、文書とインピーチメント付きデポジション・アウトラインを構築 | `litigation-legal-ja` | `/litigation-legal-ja:deposition-prep` |
| **Brief Section Drafter** | 事案理論に整合的な自社スタイルでブリーフのセクションを起案 | `litigation-legal-ja` | `/litigation-legal-ja:brief-section-drafter` |
| **Privilege Log Reviewer** | 一次的特権ログレビュー — 明らかな判断 + 弁護士レビュー用フラグ | `litigation-legal-ja` | `/litigation-legal-ja:privilege-log-review` |
| **Legal Hold** | リーガルホールドの発出、リフレッシュ、解除、報告 | `litigation-legal-ja` | `/litigation-legal-ja:legal-hold` |
| **Matter Intake** | 新規案件の統一インテーク — matter.md と history.md を作成、ログに追記 | `litigation-legal-ja` | `/litigation-legal-ja:matter-intake` |
| **Matter Briefing** | 1 案件のディープブリーフィング — GC または外部弁護士との通話に対応 | `litigation-legal-ja` | `/litigation-legal-ja:matter-briefing` |
| **Portfolio Status** | リスク分布、今後の期限、滞留案件 | `litigation-legal-ja` | `/litigation-legal-ja:portfolio-status` |
| **Outside Counsel Status** | アクティブポートフォリオ全体の週次ステータス依頼ドラフトを生成 | `litigation-legal-ja` | `/litigation-legal-ja:oc-status` |
| **Clinic Intake** | 領域横断的な論点抽出と利害関係フラグ付きの構造化された依頼者インテーク | `legal-clinic-ja` | `/legal-clinic-ja:client-intake` |
| **Case Memo Scaffold** | リサーチギャップにフラグを立てた IRAC スキャフォールド型ケース分析メモ | `legal-clinic-ja` | `/legal-clinic-ja:memo` |
| **Research Roadmap** | チェックすべき法令、判例分野、Westlaw 検索語 — リードであり引用ではない | `legal-clinic-ja` | `/legal-clinic-ja:research-start` |
| **Clinic Deadline Tracker** | マルプラクティス警戒型警告付きで案件期限を追加、報告、更新、クローズ | `legal-clinic-ja` | `/legal-clinic-ja:deadlines` |
| **Case Status Summarizer** | 対象別の案件ステータス — 依頼者、教授、または裁判所向け | `legal-clinic-ja` | `/legal-clinic-ja:status` |
| **Client Letter Drafter** | 定型依頼者コミュニケーション — アポ確認、文書依頼、更新 | `legal-clinic-ja` | `/legal-clinic-ja:client-letter` |
| **Student Ramp** | 学期オンボーディング — クリニック手順、ツールウォークスルー、実技演習 | `legal-clinic-ja` | `/legal-clinic-ja:ramp` |
| **Semester Handoff** | 学期末ケース引き継ぎメモ — ramp の鏡 | `legal-clinic-ja` | `/legal-clinic-ja:semester-handoff` |
| **Supervisor Review Queue** | 教授のレビューキュー(正式レビュー監督が設定されている場合) | `legal-clinic-ja` | `/legal-clinic-ja:supervisor-review-queue` |
| **Bar Prep Coach** | 弱点科目を狙った法域対応 MBE・エッセイ演習 | `law-student-ja` | `/law-student-ja:bar-prep-questions` |
| **Socratic Drill Sergeant** | 問い、答え、押し返す — 答えは教えない | `law-student-ja` | `/law-student-ja:socratic-drill` |
| **IRAC Grader** | IRAC エッセイを構造、論点抽出、ルール、分析の観点で採点 | `law-student-ja` | `/law-student-ja:irac-practice` |
| **Case Briefer** | 好みの書式で判例をブリーフ | `law-student-ja` | `/law-student-ja:case-brief` |
| **Outline Builder** | 講義ノートと教科書から好みの書式でアウトラインを構築・拡張 | `law-student-ja` | `/law-student-ja:outline-builder` |
| **Cold Call Prep** | 教授の質問を予測し、授業前にドリル | `law-student-ja` | `/law-student-ja:cold-call-prep` |
| **Exam Forecaster** | 同じ教授の過去問を分析し、注力点を予測 | `law-student-ja` | `/law-student-ja:exam-forecast` |
| **Legal Writing Critic** | ドラフトへの構造フィードバック — 書き直しは決してしない | `law-student-ja` | `/law-student-ja:legal-writing` |
| **Flashcard Drillmaster** | フラッシュカードを生成・ドリル — ライトナー式バケット | `law-student-ja` | `/law-student-ja:flashcards` |
| **Study Planner** | スケジュールセッション付きの長期学習計画、セッション履歴に適応 | `law-student-ja` | `/law-student-ja:study-plan` |
| **Skill Registry Browser** | コミュニティ法務スキル向けにウォッチ済みレジストリを検索 | `legal-builder-hub-ja` | `/legal-builder-hub-ja:registry-browser` |
| **Skill Installer** | トラストチェックとスキル QA 付きでコミュニティスキルをインストール | `legal-builder-hub-ja` | `/legal-builder-hub-ja:skill-installer` |
| **Skill QA** | Legal Skill Design Framework に対してスキルを評価 | `legal-builder-hub-ja` | `/legal-builder-hub-ja:skills-qa` |
| **Community Skill Recommender** | 他のプラグインでの最近の活動に基づきコミュニティスキルを提案 | `legal-builder-hub-ja` | `/legal-builder-hub-ja:related-skills-surfacer` |
| **Community Skill Updater** | インストール済みコミュニティスキルの更新を確認 | `legal-builder-hub-ja` | `/legal-builder-hub-ja:auto-updater` |
| **Registry Sync** | ウォッチ済みレジストリでの新規・更新スキルの定期チェック | `legal-builder-hub-ja` | スケジュールエージェント |

Managed Agent デプロイメント向け — `agent.yaml`、リーフワーカー・サブエージェント、ステアリングイベント例、エージェント別セキュリティノート — については **[managed-agent-cookbooks/](./managed-agent-cookbooks)** を参照してください。

## リポジトリのレイアウト

```
commercial-legal/         # 社内商事 — ベンダー / NDA / SaaS レビュー、更新、エスカレーション
corporate-legal/          # M&A デューデリジェンス、クロージング・チェックリスト、取締役会同意、法人コンプライアンス
employment-legal/         # 採用 / 解雇レビュー、労働者区分、休職、内部調査
privacy-legal/            # DPA、DSAR、PIA、プライバシートリアージ、ポリシー監視
product-legal/            # ローンチレビュー、マーケティング表現、「これは問題?」トリアージ
regulatory-legal/         # 規制フィード監視、ポリシー差分、ギャップトラッカー、NPRM コメント
ai-governance-legal/      # AI ユースケース・トリアージ、AIA、ベンダー AI レビュー、AI 規制ギャップ
ip-legal/                 # 商標クリアランス、FTO、C&D、DMCA、OSS、IP 条項、ポートフォリオ
litigation-legal/         # ポートフォリオ、案件、保全命令、要求書、デポ準備、クレームチャート
legal-clinic/             # クリニック設置、学生ランプ、インテーク、期限、メモ、引き継ぎ
law-student/              # ソクラテス式ドリル、アウトライン、IRAC、バー対策、フラッシュカード
legal-builder-hub/        # トラストゲート付きコミュニティスキル探索・インストール
external_plugins/         # ベンダーが構築・運用するパートナープラグイン
  cocounsel-legal/        # Thomson Reuters — CoCounsel Legal MCP 経由の Westlaw Deep Research
managed-agent-cookbooks/  # Claude Managed Agent クックブック — スケジュールエージェント 1 個につき 1 ディレクトリ
  diligence-grid/
  docket-watcher/
  launch-radar/
  reg-monitor/
  renewal-watcher/
scripts/                  # deploy-managed-agent.sh · validate.py · orchestrate.py · lint-tool-scope.py · test-cookbooks.sh
.claude-plugin/
  marketplace.json        # プラグイン登録
```

各プラグインディレクトリは同じ形をしています:

```
<plugin>/
  .claude-plugin/plugin.json
  CLAUDE.md               # テンプレート・プラクティスプロファイル — /<plugin>:cold-start-interview で記入される
  README.md
  skills/                 # スキル — それぞれが /<plugin>:<skill> のスラッシュコマンド
  agents/                 # スケジュールエージェント(あれば)
  hooks/                  # ツール実行前後のフック(あれば)
```

## はじめかた

### Claude Cowork

Cowork で:

1. **Cowork** タブを開きます。
2. 左サイドバーの **Customize** をクリックします。
3. **Browse plugins** をクリックして欲しいものをインストール、**または** カスタムプラグインファイル(プラグインディレクトリを zip 化したもの)をアップロードします。

インストール後、スキルは関連時に自動発火し、スラッシュコマンドは `/` から利用可能、スケジュールエージェントはフロントマターで設定されたケイデンスで実行されます。

### Claude Code

```bash
# マーケットプレイスを追加(このリポジトリの絶対パスまたは GitHub URL を指定)
# 日本語版マーケットプレイスを使う場合は ja サブディレクトリを指定
/plugin marketplace add <path-to-this-repo>/ja

# プラグインをインストール — 自分の業務に合うものを選ぶ
/plugin install commercial-legal-ja@claude-for-legal-ja
/plugin install privacy-legal-ja@claude-for-legal-ja
/plugin install corporate-legal-ja@claude-for-legal-ja

# Claude Code を再起動し、インストールした各プラグインのセットアップを実行。
# これによりプラクティスプロファイルが ~/.claude/plugins/config/claude-for-legal-ja/<plugin>-ja/CLAUDE.md に書き込まれます
/commercial-legal-ja:cold-start-interview
/privacy-legal-ja:cold-start-interview
/corporate-legal-ja:cold-start-interview
```

**まずコールドスタート・インタビューを実行してください。** プラグイン内の他のすべてのスキルは、コールドスタートが書き出すプラクティスプロファイルから読み込みます。セットアップを飛ばすことが、スキルが汎用的な出力を出す最も一般的な理由です。インタビューはプラグインあたり 10〜20 分かかり、シード文書(締結済み MSA、プレイブック、過去のレビューメモ — プラグインに合うもの)を指し示すよう求められます。シード材料は多いほどよく、2 分で生産的になり後で洗練したい場合は **クイックスタート** オプションがあります。

**リサーチツールを接続するところから始めてください。** リサーチツールがあると他のすべてが良くなり、ない場合は引用が未検証になります。完全リストは下記の [MCP コネクター](#mcp-コネクター) を参照 — CourtListener、Trellis、Descrybe、Solve Intelligence は、引用ガードレールが探すリサーチツールです。

更新: `/plugin update`。

### Claude Managed Agents

スケジュールエージェント — 規制フィード監視、更新ウォッチャー、ドケットウォッチャー、デューデリジェンス・グリッド、ローンチレーダー — は、独自のオーケストレータの背後にデプロイします:

```bash
export ANTHROPIC_API_KEY=sk-ant-...
scripts/deploy-managed-agent.sh reg-monitor
scripts/deploy-managed-agent.sh renewal-watcher
scripts/deploy-managed-agent.sh docket-watcher
scripts/deploy-managed-agent.sh diligence-grid
scripts/deploy-managed-agent.sh launch-radar
```

[`managed-agent-cookbooks/`](./managed-agent-cookbooks) 配下の各テンプレートは、対応するプラグインと同じシステムプロンプトとスキルを参照しています。デプロイスクリプトはファイル参照を解決し、スキルをアップロードし、リーフワーカー・サブエージェントを作成し、オーケストレータを `/v1/agents` に POST します。エージェント間で独自のオーケストレーション層を介して `handoff_request` イベントをルーティングするリファレンスのイベントループは [`scripts/orchestrate.py`](./scripts/orchestrate.py) を参照してください。

> **リサーチプレビュー:** サブエージェント委譲(`callable_agents`)はプレビュー機能で、単一の委譲レベルをサポートします。セキュリティ階層と引き継ぎガイダンスは各エージェントの README を参照してください。

## どう組み合わさるか

| | 何か | どこにあるか |
|---|---|---|
| **プラグイン** | 自己完結型のプラクティスエリア・バンドル — スキル、エージェント、フック、テンプレート・プラクティスプロファイル。必要なものをインストール。 | `<plugin>/` |
| **スキル** | ドメイン専門知識、慣行、ステップバイステップの手法 — Claude が関連時に自動的に引き出し、明示的にトリガーするスラッシュアクション: `/commercial-legal-ja:review`, `/privacy-legal-ja:dsar-response`, `/litigation-legal-ja:claim-chart`。 | `<plugin>/skills/<skill>/SKILL.md` |
| **エージェント** | スケジュールまたはイベント駆動のワークフロー(更新ウォッチャー、ドケットウォッチャー、規制変更モニター)。バックグラウンドで実行され、チャネルに投稿またはファイルに書き込み。 | `<plugin>/agents/` |
| **プラクティスプロファイル** | プレーン英語(または日本語)の `CLAUDE.md`。プレイブック、エスカレーションルール、ハウススタイルを記述。すべてのスキルが読み込み。 | `~/.claude/plugins/config/claude-for-legal-ja/<plugin>-ja/CLAUDE.md` |
| **コネクター** | データに Claude を配線する [MCP サーバー](https://modelcontextprotocol.io/) — CLM、DMS、e-discovery、リサーチプラットフォーム、プロダクティビティ。 | `.mcp.json`(プラグイン別) |
| **Managed Agent クックブック** | ヘッドレスデプロイメント向けの `agent.yaml` + depth-1 サブエージェント + ステアリング例。 | `managed-agent-cookbooks/<slug>/` |

すべて Markdown と JSON です。ビルドステップなし。

## バーティカル・プラグイン

業務の場所別にグループ化しています。各プラグインのコールドスタート・インタビューが、チームに合わせて調整する仕組みです — そこから始めてください。

### トランザクション & アドバイザリー

| プラグイン | 何を加えるか |
|---|---|
| **[commercial-legal-ja](./commercial-legal)** | ベンダー契約、NDA、SaaS サブスクリプションのプレイブック対応レビュー。変更履歴トレース。解約期限アラート付き更新台帳。エスカレーション・ルーティング。ステークホルダー向けサマリー。 |
| **[corporate-legal-ja](./corporate-legal)** | セル別引用付き表形式レビューによる M&A デューデリジェンス。開示別紙、クロージング・チェックリスト、書面決議、取締役会議事録。法人コンプライアンス・トラッカー。クロージング後統合。 |
| **[privacy-legal-ja](./privacy-legal)** | プライバシートリアージ(PIA vs DPIA vs 進行)、PIA 生成、管理者または処理者としての DPA レビュー、DSAR 対応。ポリシーモニターはポリシーと実務の乖離を監視。 |
| **[product-legal-ja](./product-legal)** | 自社リスク基準に対するローンチレビュー。マーケティング表現チェック。Slack 質問用「これは問題?」トリアージ。機能リスクアセスメント。 |
| **[employment-legal-ja](./employment-legal)** | 法域固有フラグ付きの採用・解雇レビュー。労働者区分。休職トラッカー(FMLA/CFRA/PFL/ADA)。内部調査。州別補足付き規程起案。 |
| **[ai-governance-legal-ja](./ai-governance-legal)** | レジストリに対する AI ユースケース・トリアージ。スコープ内のレジームにわたる影響評価。ベンダー AI レビュー。規制とポリシーのギャップ分析。 |
| **[regulatory-legal-ja](./regulatory-legal)** | 規制フィードウォッチャー、ポリシー差分、ギャップトラッカー、NPRM パブコメ期間トラッカー。チームが実際に読む月曜朝のダイジェスト。 |
| **[ip-legal-ja](./ip-legal)** | 商標クリアランス、FTO トリアージ、C&D 起案・トリアージ、DMCA テイクダウン・異議通知、OSS コンプライアンス、IP 条項レビュー、ポートフォリオ追跡。 |

### 訴訟

| プラグイン | 何を加えるか |
|---|---|
| **[litigation-legal-ja](./litigation-legal)** | 2 つの表面で動作。**社内 / ポートフォリオ:** 案件インテーク、ポートフォリオ・ステータス、リーガルホールド、外部弁護士ステータス、要求書。**事務所 / 個人事務所:** 時系列構築、クレームチャート(特許・民事)、デポジション準備、特権ログレビュー、ブリーフ起案。 |

### 学習 & 実務

| プラグイン | 何を加えるか |
|---|---|
| **[law-student-ja](./law-student)** | ソクラテス式ドリル、判例ブリーフ、アウトライン構築、IRAC 採点、コールドコール準備、フラッシュカード、バー試験対策、試験予測、学習計画。**学習モードであって回答モードではない** — 答えを代わりに書くことは決してしない。 |
| **[legal-clinic-ja](./legal-clinic)** | 教授セットアップと学生学期ランプ。教育法的スタンス(支援 / 指導 / 教授)別のプラクティスエリア別スーパーバイザーガイド。領域横断的論点抽出付きの構造化インテーク。マルプラクティス警戒型期限追跡。メモスキャフォールド、依頼者レター(定型 + 平易な表現)、学期引き継ぎ。ABA 公式意見 512 の枠組み内で構築。 |

### エコシステム

| プラグイン | 何を加えるか |
|---|---|
| **[legal-builder-hub-ja](./legal-builder-hub)** | 真のトラスト層付きコミュニティスキル探索とインストール — ウォッチ済みレジストリ、QA フレームワーク(`/legal-builder-hub-ja:skills-qa`)、SHA 固定更新、環境に取り込む前の必須トラストチェック。 |

### 外部 / パートナー製

[`external_plugins/`](./external_plugins) 配下のプラグインは、ベンダーが構築・運用しています。他のプラグインと同様に本マーケットプレイスからインストールできますが、ベンダーがコード、コネクター、サポートチャネルを所有します。

| プラグイン | 構築元 | 何を加えるか |
|---|---|---|
| **[cocounsel-legal-ja](./external_plugins/cocounsel-legal)** | Thomson Reuters | 完全引用付きレポート付きの Westlaw Deep Research — 判例法、制定法、規制、Practical Law、二次資料を、実行ごとに最大 3 つの米国法域にわたって。MCP コネクターが有効化された CoCounsel Legal サブスクリプションが必要。サポート: cocounselsupport@tr.com。 |

## コミュニティ法務スキルのトラスト層

コミュニティは法務スキルを高速に構築しています — LegalOps Consulting の `lpm-skills` や Lawvable のようなレジストリにはすでに数十のスキルが掲載されています。しかし、コミュニティスキルを認証する機関はなく、GitHub からランダムにスキルをインストールする弁護士は、自分の案件ファイル、プラクティスプロファイル、リサーチコネクターへのアクセス権を持つコードをインストールしていることになります。

`legal-builder-hub-ja` がエコシステムに足りないトラスト層を提供します:

- **セキュリティレビュー** — 隠しコンテンツ・スキャン、インジェクション検出、すべてのインストールに対する構造的トラストチェック
- **アローリスト** — デフォルトで制限的なソースゲート(レジストリ、発行元、コネクター、ライセンス)
- **ライセンス・ゲート** — デプロイメント文脈対応のライセンスポリシー(個人 / 社内 / 製品組み込み)
- **鮮度ゲート** — バンドルされた参照コンテンツ(規制、制定法、手続)が検証期間を過ぎているかを追跡し、呼び出し時に警告
- **更新時の再スキャン** — v1.0 でクリーン、v1.1 で汚染されたスキルを検出
- **インストールログ** — 何が、どこから、どのライセンスで、どのレビュー判定でインストールされたかの監査可能な記録

アローリストはデフォルトで制限的です。寛容モードは明示的選択です。非弁護士は「とにかくインストール」ボタンではなく、弁護士コンタクトにルーティングされます。

コミュニティスキルは、ファーストパーティ・プラグインと同じ設計レビュー(`/legal-builder-hub-ja:skills-qa`)を通過します。弁護士向けに構築するなら、公開前に自分のスキルに対して QA を実行してください。コードを読める弁護士が行うであろうレビューです。

## MCP コネクター

> [!IMPORTANT]
> **まずリサーチツールを接続してください。** 各プラグインには法務リサーチコネクターが既に設定されています — CourtListener、Trellis、Descrybe、Solve Intelligence、その他プラクティスエリアに応じたもの。一度認可すれば、それ以降 Claude は権威ある情報源から取得し、トレーニング知識ではなく現行データベースに対して引用を検証します。リサーチコネクター経由の引用はソースタグ付きになります。モデル知識のみからの引用は `[verify]` フラグ付きになり、リサーチツールが一切接続されていない場合は、成果物の上のレビューアー注記にソースが未検証であることが記録されるため、確認が必要であることが分かります。コネクターが引用を信頼できるものにします — 他のセットアップ前に設定してください。

これらのプラグインは、法務チームが日常的に使うシステム向けのコネクターを同梱しています。コネクターは Claude にあなたのデータからの読み込み(およびスコープ内なら書き込み)能力を与え、スキルとコマンドがそれを使用します。

| コネクター | Claude に何を与えるか | プラグイン | 備考 |
|---|---|---|---|
| **Slack** | チャネル読み込み、検索、メッセージとキャンバスの送信 | 全プラグイン | あなたのワークスペース |
| **Google Drive** | ドキュメント、シート、スライドの読み込み;リンク取得 | 全プラグイン | あなたのアカウント |
| **CoCounsel Legal (Thomson Reuters)** | Westlaw Deep Research — 判例法、制定法、規制、Practical Law にわたる引用付きレポート | `cocounsel-legal-ja` | 顧客サブスクリプション;OAuth |
| **Box** | VDR と案件ルーム内のファイル・フォルダ読み込み | `corporate-legal-ja` | あなたのテナント |
| **Ironclad** | 契約台帳、更新日、条項の読み込み | `commercial-legal-ja` | 顧客サブスクリプション |
| **DocuSign / DocuSign CLM** | 封筒ステータス、締結済み契約、CLM メタデータ | `commercial-legal-ja` | 顧客サブスクリプション |
| **iManage** | DMS からの読み込み — 案件ワークスペース、文書バージョン | `commercial-legal-ja`, `corporate-legal-ja` | 顧客サブスクリプション |
| **Everlaw** | e-discovery プロダクション、タグ付きセット、時系列 | `litigation-legal-ja` | 顧客サブスクリプション |
| **CourtListener** | 連邦ドケットと判例 | `legal-clinic-ja`, `ip-legal-ja`, `litigation-legal-ja`, `law-student-ja` | 公開;オプションの API キー |
| **Trellis** | 州裁判所のドケットと申立書 | `litigation-legal-ja` | 顧客サブスクリプション |
| **Aurora** | クリニックスタイルの案件管理とカレンダリング | `litigation-legal-ja` | 顧客サブスクリプション |
| **Definely** | 文書内起案と定義語チェック | `commercial-legal-ja`, `corporate-legal-ja` | 顧客サブスクリプション |
| **Lawve AI** | 契約レビュー支援と条項ライブラリ | `legal-builder-hub-ja` | 顧客サブスクリプション |
| **Courtroom5** | 本人訴訟当事者向けワークフロー | `legal-clinic-ja` | 顧客サブスクリプション |
| **Descrybe** | 判例リサーチとサマリゼーション | `legal-clinic-ja`, `ip-legal-ja`, `law-student-ja` | 顧客サブスクリプション |
| **Solve Intelligence** | 特許起案と権利化手続 | `corporate-legal-ja`, `ip-legal-ja` | 顧客サブスクリプション |
| **TopCounsel** | 案件ルーティングと外部弁護士パネル | `commercial-legal-ja`, `corporate-legal-ja`, `litigation-legal-ja` | 顧客サブスクリプション |
| **Linear** | ローンチトラッカー、イシュー追跡 | `product-legal-ja` | 顧客ワークスペース |
| **Atlassian (Jira)** | ローンチトラッカー、イシュー追跡 | `product-legal-ja` | 顧客ワークスペース |
| **Asana** | ローンチトラッカー、プロジェクト追跡 | `product-legal-ja` | 顧客ワークスペース |

> 「顧客サブスクリプション」と記されたコネクターには、顧客自身のアカウントと API キーが必要です。各プラグインの `.mcp.json` で、または Claude Code セットアップで `claude mcp` 経由で設定してください。

> **コネクターを構築中?** [CONNECTORS.md](./CONNECTORS.md) を参照して、優れた法務 MCP サーバーの条件と、自身のものを掲載するための提出方法をご覧ください。

## Claude for Microsoft 365

弁護士は Word と Excel で生きています。**本リポジトリのすべての契約タッチ系スキルは、Claude for Word サイドバーで、修正履歴を出力モードとして動作するよう書かれています。** これは `commercial-legal:review`(ベンダー契約、NDA、SaaS サブスクリプション)、`commercial-legal:amendment-history`、`ip-legal:ip-clause-review`、`ai-governance-legal:vendor-ai-review`、`privacy-legal:dpa-review`、および `corporate-legal-ja` のデューデリジェンス抽出に該当します。レビュー担当者は、人間のマークアップとまったく同じように各変更を承認・拒否でき、ナンバリング、定義語、相互参照、スタイルが保持されます。

Excel 向けスキルは、きれいに開けるワークブックを生成します: `corporate-legal:tabular-review` はソースシート付きのマルチシート `.xlsx` を、`litigation-legal:claim-chart` は引用列付きの要素別クレームチャートを、`corporate-legal:entity-compliance` は期限列付きコンプライアンス台帳を、`commercial-legal:renewal-tracker` は解約日順に並べた更新台帳をエクスポートします。

Claude for Microsoft 365 は **[Microsoft AppSource](https://marketplace.microsoft.com/en-us/product/office/wa200010453)** からインストールしてください。インストール後、有効化したプラグインのスキルがサイドバーから `/` で利用可能になり、コネクターは同じ表面から到達可能です。1 つのスレッドが Word、Excel、PowerPoint、Outlook にまたがることができます。

Anthropic の API ではなく独自クラウド(Vertex AI、Bedrock、または内部ゲートウェイ)に対してアドインをデプロイする IT 管理者は、別途 [`claude-for-msft-365-install`](https://github.com/anthropics/financial-services/tree/main/claude-for-msft-365-install) ツーリングを参照してください。

## 自分のものにする

これらはリファレンス・テンプレートです。チームの実務にチューニングすることで良くなります — そしてカスタマイズの仕組みは、リポジトリの奥深くに埋もれた設定ファイルではなく、プラグインそのものです。

- **コールドスタート・インタビューを実行する。** これがカスタマイズの仕組みです。実務がどう動くかを聞き、シード文書を読み、プラクティスプロファイルを書き込みます。他のすべてのスキルがそのプロファイルから読み込みます。5 つの締結済み MSA、プレイブック、エスカレーション・マトリックス付きの `/commercial-legal-ja:cold-start-interview` は、レビュースキルを目に見えて鋭くします。
- **プラクティスプロファイルを編集する。** プロファイルは `~/.claude/plugins/config/claude-for-legal-ja/<plugin>-ja/CLAUDE.md` にあります。エスカレーション閾値の間違い、新しい統合、ポリシー更新など、軽微な修正は直接編集してください。プラグイン更新後も残ります。
- **セットアップを再実行する。** 実務が大きく変わったとき(新規法域、新規 CLM、新規ポリシー)は、再度 `/<plugin>:cold-start-interview` で完全に再インタビュー。
- **コネクターを差し替える。** `.mcp.json` を自社の CLM、DMS、e-discovery プラットフォーム、ローンチトラッカー、HRIS に向けます。コネクター未設定時はスキルがグレースフルにフォールバックします — サイレント no-op はしません。
- **プレイブックとテンプレートを持ち込む。** 自社用語、ハウススタイル、ブランドテンプレートをプラグインの `CLAUDE.md` と `references/` に投入します。スキルがそれを拾い上げます。
- **ハウススタイル向けにスキルをフォークする。** すべてのスキルは `skills/` 配下の Markdown ファイルです。ステップ、ゲート、出力形式を編集してください。
- **スケジュールエージェントを追加する。** `<plugin>/agents/` 配下のエージェントは cron スタイルのスケジュール付き Markdown です。チームに必要なウォッチャーを追加してください。

ビルドステップなし。すべて Markdown と JSON。

## スキル & コマンドリファレンス

全プラグインを横断する完全マップ。どのプラグインでも、最初に実行するのはコールドスタート・インタビューです。

> 注:以下の表のコマンドは英語版プラグインのスラッシュコマンドです。日本語版を使う場合は、プラグイン名に `-ja` 接尾辞が付きます(例: `/commercial-legal-ja:review`)。スキルの slug 部分(コマンドのコロン後)は同じです。

### ai-governance-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/ai-governance-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — AI ガバナンス実務を学習 |
| `/ai-governance-legal-ja:ai-inventory` | ai-inventory | EU AI Act のシステム別インベントリ — 各システムのロールとリスク階層を追跡 |
| `/ai-governance-legal-ja:use-case-triage` | use-case-triage | AI ユースケースを分類 — 承認、条件付き、否 |
| `/ai-governance-legal-ja:aia-generation` | aia-generation | 自社書式で AI 影響評価を実施 |
| `/ai-governance-legal-ja:vendor-ai-review` | vendor-ai-review | ベンダー AI 規約をガバナンススタンスに照らしてレビュー |
| `/ai-governance-legal-ja:reg-gap-analysis` | reg-gap-analysis | 新規 AI 規制をガバナンススタンスと差分比較 |
| `/ai-governance-legal-ja:policy-monitor` | policy-monitor | AI ポリシーを実務に追随させる |
| `/ai-governance-legal-ja:policy-starter` | policy-starter | 公開モデルポリシーから、プラクティスプロファイルに合わせた事務所 AI 利用規程を起案 |
| `/ai-governance-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |

### legal-builder-hub

| コマンド | スキル | 何をするか |
|---|---|---|
| `/legal-builder-hub-ja:cold-start-interview` | cold-start-interview | プラクティスプロファイル・インタビューとスターターパック推奨 |
| `/legal-builder-hub-ja:registry-browser` | registry-browser | コミュニティ法務スキル向けにウォッチ済みレジストリを検索 |
| `/legal-builder-hub-ja:skill-installer` | skill-installer | トラストチェック付きでコミュニティスキルをインストール |
| `/legal-builder-hub-ja:skills-qa` | skills-qa | デザインフレームワークに対してスキルを評価 |
| `/legal-builder-hub-ja:related-skills-surfacer` | related-skills-surfacer | 他プラグインの活動からコミュニティスキルを提案 |
| `/legal-builder-hub-ja:auto-updater` | auto-updater | インストール済みコミュニティスキルの更新を確認 |
| `/legal-builder-hub-ja:disable` | skill-manager | ファイル削除なしでコミュニティスキルを無効化 |
| `/legal-builder-hub-ja:uninstall` | skill-manager | ハブ経由でインストールしたコミュニティスキルをアンインストール |
| スケジュール | registry-sync (agent) | ウォッチ済みレジストリの更新を定期チェック |

### legal-clinic

| コマンド | スキル | 何をするか |
|---|---|---|
| `/legal-clinic-ja:cold-start-interview` | cold-start-interview | 教授セットアップ — 領域、法域、監督スタイル |
| `/legal-clinic-ja:build-guide` | build-guide | 教授プラクティスエリアガイド — インテーク、教育法、レビューゲート |
| `/legal-clinic-ja:ramp` | ramp | 実技演習付きの学生学期オンボーディング |
| `/legal-clinic-ja:client-intake` | client-intake | 領域横断的論点抽出付きの構造化インテーク |
| `/legal-clinic-ja:client-comms-log` | client-comms-log | 依頼者コミュニケーションを記録 — 案件ごとの追記専用記録 |
| `/legal-clinic-ja:research-start` | research-start | リサーチロードマップ — 法令、判例法、検索語 |
| `/legal-clinic-ja:memo` | memo | リサーチギャップのフラグ付き IRAC スキャフォールド分析メモ |
| `/legal-clinic-ja:draft` | draft | 一般的なクリニック文書の初回ドラフト |
| `/legal-clinic-ja:client-letter` | client-letter · plain-language-letters | テンプレートからの定型依頼者通信 |
| `/legal-clinic-ja:status` | status | 対象別の案件ステータス — 依頼者、教授、裁判所向け |
| `/legal-clinic-ja:deadlines` | deadlines | マルプラクティス警戒型警告付きで案件期限を追跡 |
| `/legal-clinic-ja:supervisor-review-queue` | supervisor-review-queue | 教授のレビューキュー(正式監督時) |
| `/legal-clinic-ja:semester-handoff` | semester-handoff | 学期末ケース引き継ぎメモ |

### commercial-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/commercial-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — 商事契約実務を学習 |
| `/commercial-legal-ja:review` | vendor-agreement-review · nda-review · saas-msa-review | ベンダー契約、NDA、または SaaS サブスクリプションのレビュー |
| `/commercial-legal-ja:amendment-history` | amendment-history | 基本契約と変更契約にわたる契約変更履歴をトレース |
| `/commercial-legal-ja:renewal-tracker` | renewal-tracker | 90 日以内に解約期限が来る契約を表示 |
| `/commercial-legal-ja:escalation-flagger` | escalation-flagger | 契約論点をルーティングし依頼文を起案 |
| `/commercial-legal-ja:review-proposals` | (内部) | 保留中のプレイブック更新提案をレビューし承認 |
| `/commercial-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |
| — | stakeholder-summary | レビューをビジネス・ステークホルダー向けサマリーに変換 |
| スケジュール | renewal-watcher (agent) | 週次の更新台帳スイープ |
| スケジュール | deal-debrief (agent) | 逸脱を伴う締結済み合意の週次サーフェス |
| スケジュール | playbook-monitor (agent) | 条項がドリフトしたときにプレイブック更新を提案 |

### corporate-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/corporate-legal-ja:cold-start-interview` | cold-start-interview | 自社向けコールドスタート、オプションの `--new-deal` キックオフ付き |
| `/corporate-legal-ja:tabular-review` | tabular-review | 表形式レビュー — 文書 1 つにつき 1 行、すべてのセルに引用 |
| `/corporate-legal-ja:diligence-issue-extraction` | diligence-issue-extraction | 自社基準に従って VDR 文書から論点を抽出 |
| `/corporate-legal-ja:material-contract-schedule` | material-contract-schedule | 重要契約の開示別紙を構築 |
| `/corporate-legal-ja:closing-checklist` | closing-checklist | クリティカルパス付きでクロージングを阻害している事項 |
| `/corporate-legal-ja:written-consent` | written-consent | 自社書式で取締役会または委員会の同意書を起案 |
| `/corporate-legal-ja:entity-compliance` | entity-compliance | 法域横断の法人コンプライアンス・トラッカー |
| `/corporate-legal-ja:integration-management` | integration-management | 同意取得追跡付きのクロージング後統合トラッカー |
| `/corporate-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |
| — | board-minutes | 自社書式で取締役会または委員会の議事録を起案 |
| — | deal-team-summary | デューデリジェンス所見を案件ブリーフィングに集約 |
| — | ai-tool-handoff | Luminance/Kira を検出し、一括ツール出力を QA |
| スケジュール | dataroom-watcher (agent) | VDR アップロードを監視しチェックリスト状況を投稿 |

### employment-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/employment-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — 法域とエスカレーションルールを学習 |
| `/employment-legal-ja:wage-hour-qa` | wage-hour-qa | 法域対応の賃金 / 時間および雇用 Q&A |
| `/employment-legal-ja:hiring-review` | hiring-review | オファーレターと制限的誓約をレビュー |
| `/employment-legal-ja:termination-review` | termination-review | 高リスクフラグ検出付き解雇レビュー |
| `/employment-legal-ja:worker-classification` | worker-classification | 提案された就業形態を州別テストに対して分類 |
| `/employment-legal-ja:policy-drafting` | policy-drafting | 州別補足付きで雇用規程を起案 |
| `/employment-legal-ja:leave-tracker` | leave-tracker | 期限アラートのため未終了休職をチェック |
| `/employment-legal-ja:log-leave` | log-leave | 新しい休職を休職台帳に追加 |
| `/employment-legal-ja:investigation-open` | internal-investigation | 新規内部調査案件を開始 |
| `/employment-legal-ja:investigation-add` | internal-investigation | 開いた調査にデータを追加 — 文書、メモ |
| `/employment-legal-ja:investigation-memo` | internal-investigation | 秘匿特権対象調査メモを起案または更新 |
| `/employment-legal-ja:investigation-query` | internal-investigation | 開いた調査ログに対して質問 |
| `/employment-legal-ja:investigation-summary` | internal-investigation | 調査メモから対象別サマリーを起案 |
| `/employment-legal-ja:expansion-kickoff` | international-expansion | 新規国向けの展開計画を開始 |
| `/employment-legal-ja:expansion-update` | international-expansion | 進行中展開プロジェクトのステータス更新 |
| `/employment-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |
| — | handbook-updates | ハンドブック変更を差分比較し州別補足影響をフラグ付け |
| スケジュール | leave-tracker (agent) | 厳格期限付き未終了休職の週次監視 |

### ip-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/ip-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — IP 実務とスタンスを学習 |
| `/ip-legal-ja:clearance` | clearance | 商標クリアランス初回 — ノックアウト + 類似商標 |
| `/ip-legal-ja:fto-triage` | fto-triage | FTO トリアージ、FTO 意見書ではない |
| `/ip-legal-ja:invention-intake` | invention-intake | 発明届出の初回スクリーニング — 新規性、非自明性、§101、バー期限 |
| `/ip-legal-ja:cease-desist` | cease-desist | C&D を起案または受領分をトリアージ |
| `/ip-legal-ja:takedown` | takedown | DMCA 通知、回答トリアージ、または §512(g) 異議通知 |
| `/ip-legal-ja:infringement-triage` | infringement-triage | 4 種類すべての IP 権利にわたる侵害トリアージ |
| `/ip-legal-ja:ip-clause-review` | ip-clause-review | IP 条項レビュー — 譲渡、ライセンス、保証 |
| `/ip-legal-ja:oss-review` | oss-review | オープンソースライセンス・コンプライアンスチェック |
| `/ip-legal-ja:portfolio` | portfolio | IP ポートフォリオの期限と更新を追跡 |
| `/ip-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |
| スケジュール | ip-renewal-watcher (agent) | IP ポートフォリオ期限の週次レポート |

### litigation-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/litigation-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — リスク、ランドスケープ、自社ブリーフスタイル |
| `/litigation-legal-ja:matter-intake` | matter-intake | 新規案件のインテーク — matter.md と履歴を作成 |
| `/litigation-legal-ja:matter-briefing` | matter-briefing | 通話用の 1 案件ディープブリーフィング |
| `/litigation-legal-ja:matter-update` | matter-update | 日付付きイベントを案件履歴に追記 |
| `/litigation-legal-ja:portfolio-status` | portfolio-status | ポートフォリオ集約 — リスク、期限、滞留案件 |
| `/litigation-legal-ja:matter-close` | matter-close | 案件をクローズ — アーカイブ、記録保持 |
| `/litigation-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |
| `/litigation-legal-ja:demand-intake` | demand-intake | 起案前文脈 — 当事者、事実、レバレッジ |
| `/litigation-legal-ja:demand-draft` | demand-draft | FRE 408 ゲートと .docx 出力付きで要求書を起案 |
| `/litigation-legal-ja:demand-received` | demand-received | 受領要求書のトリアージ — 選択肢、ポートフォリオ照会 |
| `/litigation-legal-ja:subpoena-triage` | subpoena-triage | 召喚状トリアージ — 範囲、負担、特権、計画 |
| `/litigation-legal-ja:legal-hold` | legal-hold | リーガルホールドの発出、リフレッシュ、解除、報告 |
| `/litigation-legal-ja:oc-status` | oc-status | 外部弁護士への週次ステータス依頼メール |
| `/litigation-legal-ja:claim-chart` | claim-chart | 要素チャート — 特許または民事訴訟原因 |
| `/litigation-legal-ja:chronology` | chronology | ソースとアップロードから時系列を構築・更新 |
| `/litigation-legal-ja:deposition-prep` | deposition-prep | 事案理論と紐づけたデポジション・アウトライン |
| `/litigation-legal-ja:privilege-log-review` | privilege-log-review | 初回特権ログレビュー、フラグ付き |
| `/litigation-legal-ja:brief-section-drafter` | brief-section-drafter | 自社スタイルでブリーフのセクションを起案 |
| スケジュール | docket-watcher (agent) | 訴訟ドケットの提出と期限を監視 |

### privacy-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/privacy-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — プライバシー実務を学習 |
| `/privacy-legal-ja:use-case-triage` | use-case-triage | PIA か GDPR DPIA か進行可かを判定 |
| `/privacy-legal-ja:pia-generation` | pia-generation | 自社書式でプライバシー影響評価を生成 |
| `/privacy-legal-ja:dpa-review` | dpa-review | DPA をレビュー — 管理者 vs 処理者を自動判定 |
| `/privacy-legal-ja:dsar-response` | dsar-response | DSAR を進めて回答を起案 — 検証、所在確認、評価 |
| `/privacy-legal-ja:reg-gap-analysis` | reg-gap-analysis | 規制を現行ポリシー・実務と差分比較 |
| `/privacy-legal-ja:policy-monitor` | policy-monitor | プライバシーポリシーを実務に追随させる |
| `/privacy-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |

### product-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/product-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — ローンチトラッカーを接続、基準を学習 |
| `/product-legal-ja:is-this-a-problem` | is-this-a-problem | クイック質問への高速「これは問題?」回答 |
| `/product-legal-ja:launch-review` | launch-review | フレームワークと基準に対する完全なローンチレビュー |
| `/product-legal-ja:marketing-claims-review` | marketing-claims-review | マーケコピーを、検討が必要な表現観点でレビュー |
| `/product-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |
| — | feature-risk-assessment | ローンチレビューでフラグが立った単一機能のディープリスク分析 |
| スケジュール | launch-watcher (agent) | 今後のレビューに向けてローンチトラッカーを監視 |

### regulatory-legal

| コマンド | スキル | 何をするか |
|---|---|---|
| `/regulatory-legal-ja:cold-start-interview` | cold-start-interview | コールドスタート — ウォッチリスト、ポリシーインデックス、重要性 |
| `/regulatory-legal-ja:reg-feed-watcher` | reg-feed-watcher | 規制フィードを今すぐ確認し、新着を報告 |
| `/regulatory-legal-ja:policy-diff` | policy-diff | 規制変更をポリシーライブラリと差分比較 |
| `/regulatory-legal-ja:gaps` | gap-surfacer | 未対応ギャップトラッカー — フラグ済み未クローズ項目 |
| `/regulatory-legal-ja:policy-redraft` | policy-redraft | ギャップを閉じるマークアップ済みポリシー再ドラフト — ポリシーオーナーのレビュー向け提案 |
| `/regulatory-legal-ja:comments` | (tracker) | 未対応 NPRM パブコメ期間と期限をレビュー |
| `/regulatory-legal-ja:matter-workspace` | matter-workspace | 案件ワークスペースを管理(プラクティスレベル) |
| スケジュール | reg-change-monitor (agent) | 重要性フィルター付きスケジュール規制フィードスイープ |

### law-student

| コマンド | スキル | 何をするか |
|---|---|---|
| `/law-student-ja:cold-start-interview` | cold-start-interview | あなたについてのインタビュー — 授業、バー、学習スタイル |
| `/law-student-ja:socratic-drill` | socratic-drill | ソクラテス式ドリル — 問い、答え、押し返す |
| `/law-student-ja:case-brief` | case-brief | 好みの書式で判例をブリーフ |
| `/law-student-ja:outline-builder` | outline-builder | 好みの書式でアウトラインを構築・拡張 |
| `/law-student-ja:irac-practice` | irac-practice | IRAC エッセイ採点 — 構造、論点、ルール、分析 |
| `/law-student-ja:legal-writing` | legal-writing | 書く文への構造フィードバック — 書き直しは決してしない |
| `/law-student-ja:cold-call-prep` | cold-call-prep | 教授の質問を予測し、ドリル |
| `/law-student-ja:bar-prep-questions` | bar-prep-questions | 弱点科目を狙った MBE またはエッセイ問題 |
| `/law-student-ja:flashcards` | flashcards | フラッシュカードを生成・ドリル — ライトナー式 |
| `/law-student-ja:exam-forecast` | exam-forecast | 過去問を分析し注力点を予測 |
| `/law-student-ja:study-plan` | study-plan | 長期学習計画を構築・更新 |
| `/law-student-ja:session` | study-plan | 集中型 N 問セッションを実行し、計画を更新 |

### cocounsel-legal (Thomson Reuters)

| コマンド | スキル | 何をするか |
|---|---|---|
| `/cocounsel-legal-ja:deep-research` | deep-research | Westlaw Deep Research を実行 — 開始、ポーリング、完全引用付きレポートの提示 |

## 貢献

すべて Markdown と JSON です。Fork、編集、PR。

- **新スキル** → `<plugin>/skills/<skill-name>/SKILL.md` の下に、既存スキルが使うフロントマター(`name`, `description`, `argument-hint`)付きで追加してください。description は 1024 文字以下に — それがトリガーシグナルです。スキルは `/<plugin>:<skill-name>` として呼び出し可能。純粋なリファレンス・スキルは `user-invocable: false` でマークしてください。
- **新エージェント** → `<plugin>/agents/<name>.md` をスケジュール用フロントマターとシステムプロンプト付きで追加してください。ヘッドレスデプロイメントが必要なら、対応する `managed-agent-cookbooks/<name>/` も追加してください。
- **コミュニティスキル** → `/legal-builder-hub-ja:skill-installer` を使って環境内でコミュニティスキルをテストしてください。Hub はインストール前にすべてのスキルに対して `/legal-builder-hub-ja:skills-qa` を実行します — Legal Skill Design Framework(9 つの設計パラメータ、3 つの法的失敗モード、トラスト表面チェック)に対してスキルをスコアし、不合格のものは却下します。
- **プッシュ前にクックブックを検証** → `bash scripts/test-cookbooks.sh` がすべての managed-agent クックブックをドライランし、オーケストレーター・ツールスコープを lint します。

## ライセンス

[Apache License, Version 2.0](LICENSE) の下でライセンスされています。

Copyright 2026 Anthropic PBC.
