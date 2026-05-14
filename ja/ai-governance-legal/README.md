# AI ガバナンスプラグイン (AI Governance Plugin)

インハウスの AI ガバナンスカウンセル向けワークフロー: ユースケースのトリアージ、AI 影響評価(AIA)、ベンダー AI レビュー、規制とポリシーのギャップ分析。AI ポリシー、リファレンス影響評価、主要なベンダー AI 契約から学習したチームのプラクティスプロファイルを中心に構築されています。

**すべての出力は、弁護士レビューを前提とするドラフトです — 引用元のタグ付け・フラグ付け・ゲート付きで提供され、法的結論ではありません。** プラグインが作業を行います: ドキュメントを読み、プレイブックを適用し、論点を発見し、メモを起草します。弁護士がレビューし、検証し、判断します。引用元はソース別にタグ付けされるため、どの引用がリサーチツールから取得され、どれを確認すべきかが分かります。秘匿特権マーカーは保守的に適用され、誤って権利放棄しないようになっています。重大なアクション(申請、送信、実行)は明示的な確認を経るゲートになっています。

## 対象ユーザー

| ロール | 主要ワークフロー |
|---|---|
| **プライバシーカウンセル / AI ガバナンスカウンセル** | 影響評価、ベンダー AI レビュー、規制ギャップ分析 |
| **プロダクトカウンセル** | ユースケースのトリアージ、AI コンポーネントを含むローンチレビュー |
| **GC / リーガルオペレーション** | AI ポリシーガバナンス、エスカレーション、取締役会レベルの論点 |
| **調達 / 法務** | ベンダー AI 契約のレビュー |

## 初回実行: コールドスタート・インタビュー

プラグインはインタビューで以下を学習します: あなたは構築者(builder)か、利用者(deployer)か、両方か — 実際に適用される規制は何か — ユースケースのレッドラインは何か — ここでの良い影響評価とはどのようなものか。その後、シード文書を読み込み、あなたの実際のポジションとハウススタイルを学習します。

```
/ai-governance-legal-ja:cold-start-interview
```

## コマンド

| コマンド | 機能 |
|---|---|
| `/ai-governance-legal-ja:cold-start-interview` | コールドスタート・インタビュー — プラクティスプロファイルを書き出す |
| `/ai-governance-legal-ja:use-case-triage [use case]` | ユースケースを台帳に照らして分類(approved / conditional / never) |
| `/ai-governance-legal-ja:aia-generation [use case]` | AI 影響評価(AIA)をハウススタイルで実施 |
| `/ai-governance-legal-ja:vendor-ai-review [vendor/file]` | ベンダー AI 契約をあなたのポジションに照らしてレビュー |
| `/ai-governance-legal-ja:reg-gap-analysis [regulation]` | 新規制やガイダンスを現行ポリシー/実務との差分として分析 |
| `/ai-governance-legal-ja:policy-monitor` | AI ポリシードリフトの週次スイープ、または提案された新規プラクティスのダイレクトクエリ |
| `/ai-governance-legal-ja:policy-starter` | 公開モデルポリシーからファームの AI 利用ポリシーをプラクティスプロファイルに合わせて起草(弁護士レビュー向けドラフト) |
| `/ai-governance-legal-ja:matter-workspace` | 案件ワークスペースの管理(マルチクライアントの私的執務専用) — new, list, switch, close, none |

## スキル

| スキル | 目的 |
|---|---|
| **cold-start-interview** | インタビュー + シード文書から `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を作成 |
| **use-case-triage** | ユースケースを台帳に照らして分類し、未実施の評価をフラグ |
| **aia-generation** | AI 影響評価(AIA)をハウスフォーマットで作成 |
| **vendor-ai-review** | ガバナンスポジションに照らした AI 固有のベンダー契約レビュー |
| **reg-gap-analysis** | 新規制/ガイダンス vs. 現状、改善計画 |
| **policy-monitor** | 出力をクロールしてプラクティスドリフトを検出、AI ポリシー文言の更新案を起草 |
| **policy-starter** | 公開モデルポリシー(ABA, 州弁護士会, ILTA, CLOC, NIST, EU AI Act, 同業他社ポリシー)から初回ドラフトの AI 利用ポリシーを作成し、プラクティスプロファイルに合わせて適応 — 弁護士レビュー向けドラフト、完成版ポリシーではない |
| **matter-workspace** | マルチクライアント執務向けに案件ワークスペースを作成・一覧・切替・クローズ。各クライアント/案件を分離し、コンテキストが漏れないようにする |

## クイックスタート

### 1. セットアップ

```
/ai-governance-legal-ja:cold-start-interview
```

以下のものがあれば用意してください: AI または許容利用ポリシー、過去の影響評価 1 件、主要なベンダー AI 契約、モデルインベントリまたは承認済みツールリスト。

設定は `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` に保存され、プラグインの更新後も保持されます。

### 2. 新しいユースケースをトリアージ

```
/ai-governance-legal-ja:use-case-triage "営業チームが AI でリードを自動スコアリングしたい"
```

出力: リスク階層、台帳マッチまたはギャップ、必要な条件、影響評価の要否。

### 3. 影響評価を実施

```
/ai-governance-legal-ja:aia-generation "人事の AI 履歴書スクリーニング"
```

インテーク質問 → ハウスフォーマットでの影響評価 → ポリシー整合性チェック → 緩和条件。

### 4. ベンダー AI 契約をレビュー

```
/ai-governance-legal-ja:vendor-ai-review openai-terms.pdf
```

出力: 各条項 vs. ポジション、提案レッドライン、エスカレーションすべきギャップ。

## プラグインのトライアングル: AI ガバナンス ↔ プロダクトカウンセル ↔ プライバシー

これら 3 つのプラグインは連携して動作するよう設計されています。AI ガバナンスは 3 本目の脚です。

- **プロダクトカウンセル**は AI コンポーネントを含むローンチを検出すると、
  `/ai-governance-legal-ja:use-case-triage` と `/ai-governance-legal-ja:aia-generation` にハンドオフ
- **プライバシー**は AI ユースケースが個人データを含むことを検出すると、
  プラグインがインストールされていれば `/privacy-legal:pia-generation` にハンドオフ
- **AI ガバナンス**は影響評価がデータ保護論点を含むことを検出すると、
  プラグインがインストールされていれば `/privacy-legal:pia-generation` にハンドオフ

ハンドオフは明示的です: 各プラグインは他のプラグインが必要なときにフラグを立て、そこで答えるべき質問を明示します。

## ファイル構造

```
ai-governance-legal-ja/
├── CLAUDE.md
├── README.md
└── skills/
    ├── cold-start-interview/
    ├── use-case-triage/
    ├── aia-generation/
    ├── vendor-ai-review/
    ├── reg-gap-analysis/
    ├── policy-monitor/
    ├── policy-starter/
    └── matter-workspace/
```

## 学習の仕組み

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` にあるプラクティスプロファイルは静的なものではありません — プラグインを使うにつれて改善されていきます。各スキルは、調整すべきデフォルトが使われた場合に教えてくれます。`policy-monitor` エージェントは AI ガバナンスポリシーと実務との間のドリフトを監視し、更新を提案します。セットアップを再実行する、ファイルを直接編集する、スキルに新しいポジションを記録するよう指示することができます。

## 注意事項

- ギャップチェック(`reg-gap-analysis`)は新たに到来した規制を扱います。Policy monitor は内部のプラクティスドリフトを扱います。変化の方向が異なるため、別のツールです。
- Policy monitor がスイープを行うには、セットアップ時にアウトプットフォルダが設定されている必要があります。ダイレクトクエリモードはそれなしでも動作します。
- ユースケースのトリアージは台帳の品質に依存します。セットアップ・インタビューでレッドラインを正確に把握することに時間をかけてください — すべてはそこから動きます。
- 影響評価のフォーマットはシード評価から取得されます。セットアップ時にシードを提供しなかった場合はベースライン構造が使われます — リファレンスを指定してセットアップを再実行すると改善されます。
- 構築者(builder)と利用者(deployer)の義務は分けて扱われます。両方の場合、各タスクでどちらの帽子をかぶっているかをスキルが尋ねます。
- ギャップ分析は手動です(規制やガイダンスドキュメントを指し示します)。自動監視には、プラグインがインストールされていれば `regulatory-legal` プラグインと組み合わせて使用してください。
- `## Company profile` セクションは、慣習上 `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の最初のブロックです。他の `-counsel` プラグインを実行している場合は、同じコンテキストを再入力する代わりに、それをコピーすることができます。
