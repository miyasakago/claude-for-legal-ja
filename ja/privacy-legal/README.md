# プライバシー法務プラグイン (Privacy Counsel Plugin)

社内プライバシーカウンセル向けのワークフロー集: DPA(データ処理契約)レビュー、DSAR(データ主体アクセス請求)対応ドラフト作成、PIA(プライバシー影響評価)作成、規制とポリシーのギャップ分析。実際のプライバシーポリシー、DPA テンプレート、参考 PIA から学習したチームの**プラクティスプロファイル**を中核に構築されています。

**すべての出力は、弁護士レビューを前提とした下書きです — 出典付き・フラグ付き・ゲート付き、最終的な法的判断ではありません。** プラグインが実務を肩代わりします: 文書を読み、プレイブックを適用し、論点を見つけ、メモを起草します。弁護士がそれをレビューし、検証し、判断します。引用は出典別にタグ付けされ、どの引用が調査ツール経由でどれが要検証なのか分かります。秘匿特権マーカーは保守的に適用され、誤って権利放棄が生じないようにします。重要な行為(申立、送付、署名)は明示的な確認を経たうえで実行されるゲート構造です。

## 対象ユーザー

| 役割 | 主なワークフロー |
|---|---|
| **プライバシーカウンセル** | DPA レビュー、PIA サインオフ、規制ギャップ分析 |
| **プライバシープログラムマネージャー** | DSAR 対応、PIA インテーク、ベンダープライバシーレビュー |
| **プロダクトカウンセル** | ローンチ時の PIA 作成 |
| **サポート / CS** | DSAR の一次対応(エスカレーション付き) |

## 初回起動: コールドスタート・インタビュー

プラグインはあなたにインタビューを行い、以下を学習します: あなたが管理者か処理者か、実際にどの規制が適用されるか、DPA で何を受け入れ何を受け入れないか。次に 3 つのシード文書 — あなたのプライバシーポリシー、DPA テンプレート、満足のいく PIA 1 件 — を読み、実際のポジションとハウススタイルを学習します。

設定は `~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/CLAUDE.md` に保存され、プラグイン更新でも保持されます。

```
/privacy-legal-ja:cold-start-interview
```

## コマンド

| コマンド | 内容 |
|---|---|
| `/privacy-legal-ja:cold-start-interview` | コールドスタート・インタビュー |
| `/privacy-legal-ja:use-case-triage [activity]` | PIA は必要? 迅速な分類と条件提示 |
| `/privacy-legal-ja:dpa-review [file]` | DPA をプレイブックに照らしてレビュー(方向は自動検出) |
| `/privacy-legal-ja:dsar-response` | DSAR を一通り進めて応答をドラフト |
| `/privacy-legal-ja:pia-generation [feature]` | ハウススタイルで PIA を作成 |
| `/privacy-legal-ja:reg-gap-analysis [regulation]` | 新規制と現行ポリシー/実務の差分 |
| `/privacy-legal-ja:policy-monitor` | ポリシー乖離の週次スイープ、または新規実務案の直接照会 |
| `/privacy-legal-ja:matter-workspace` | 案件ワークスペースの管理(複数クライアントの私的実務のみ)— new, list, switch, close, none |

## スキル

| スキル | 目的 |
|---|---|
| **cold-start-interview** | インタビュー + シード文書から CLAUDE.md を書き出し |
| **use-case-triage** | PIA / DPIA が必要か / 進めて良いか?ポリシー抵触チェック + ハンドオフ |
| **dpa-review** | 双方向(処理者 / 管理者)対応の DPA 条項別レビュー |
| **dsar-response** | 本人確認 → システム巡回 → 例外検討 → 応答ドラフト |
| **pia-generation** | ハウスフォーマットの PIA、ポリシー整合性チェック付き |
| **reg-gap-analysis** | 新規制 vs 現状、修復計画 |
| **policy-monitor** | 出力物を巡回し実務乖離を検知、ポリシー文言の更新案を提示 |
| **matter-workspace** | 複数クライアント実務向けに案件ワークスペースを作成・一覧・切替・終了。各クライアント/案件を分離し、コンテキストの混在を防止 |

## クイックスタート

### 1. セットアップ

```
/privacy-legal-ja:cold-start-interview
```

準備するもの: 公開プライバシーポリシーの URL、標準 DPA、参考 PIA 1 件。

### 2. 新機能や処理活動のトリアージ

```
/privacy-legal-ja:use-case-triage "マーケティングが行動データを広告パーソナライゼーションに使いたい"
```

出力: PROCEED / PIA REQUIRED / DPIA MANDATORY / STOP — 条件表、法的根拠の質問、その場で PIA を開始するオプション付き。

### 3. 顧客 DPA のレビュー

```
/privacy-legal-ja:dpa-review customer-dpa.pdf
```

出力: 方向の自動検出、条項別のプレイブック対比、修正案、ポリシー整合性チェック。

### 4. DSAR の処理

```
/privacy-legal-ja:dsar-response
```

分類 → 本人確認 → データ探索 → 例外 → ドラフトの順に進めます。設定 CLAUDE.md のシステムリストを参照します。

### 5. 新機能の PIA 作成

```
/privacy-legal-ja:pia-generation "位置情報共有機能"
```

インテーク質問 → ハウスフォーマットの PIA → ポリシー差分 → 条件リスト。

## 学習の仕組み

`~/.claude/plugins/config/claude-for-legal-ja/privacy-legal-ja/CLAUDE.md` のプラクティスプロファイルは静的ではなく、利用に応じて改善されます。スキルは出力がデフォルト値を使っている場合に通知します。`policy-monitor` スキルはポリシーと実務の乖離を監視し、更新案を提示します。セットアップを再実行する、ファイルを直接編集する、スキルに新ポジションを記録させる、いずれも可能です。

## ファイル構造

```
privacy-legal-ja/
├── .claude-plugin/plugin.json
├── .mcp.json
├── CLAUDE.md
├── README.md
├── skills/
│   ├── cold-start-interview/
│   ├── use-case-triage/
│   ├── dpa-review/
│   ├── dsar-response/
│   ├── pia-generation/
│   ├── reg-gap-analysis/
│   ├── policy-monitor/
│   └── matter-workspace/
└── hooks/hooks.json
```

## 注意事項

- DPA レビューは双方向対応: 同じスキルが顧客 DPA(自社のオペレーション柔軟性を守る)とベンダー DPA(自社のデータを守る)の両方を扱います。方向は自動検出、または質問します。
- PIA フォーマットはシード PIA から学習します。セットアップで提供しなかった場合は汎用構造になります — 参考 PIA を用意して再セットアップしてください。
- ギャップ分析(`reg-gap-analysis`)は外部から入ってくる規制を扱います。ポリシーモニターは内部の実務乖離を扱います。変化の向きが異なる別ツールです。
- ポリシーモニターはスイープ実行のために出力フォルダの設定(セットアップ時)が必要です。直接照会モードはなくても動作します。
