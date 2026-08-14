---
name: customize
description: >
  訴訟プラクティスプロファイルのガイド付きカスタマイズ — コールドスタート・
  インタビュー全体を再実行せずに、1 つの項目を変更します。プラクティスロール、
  サイド(原告 / 被告 / 混合)、リスクキャリブレーション、ランドスケープ、
  ハウススタイル、エスカレーション連絡先、重大度語彙、案件ワークスペース
  パスを調整します。Use when the user says "change my [thing]",
  "update my profile", "edit my config", or "customize".
  日本語トリガー: 設定変更、プロファイル更新、設定編集、カスタマイズ、
  プレイブック調整
argument-hint: "[section name, or describe what you want to change]"
---

# /customize

## このスキルが動くとき

ユーザーが `/litigation-legal-ja:customize` をタイプ。コールドスタート・インタビュー全体を再実行することなく、YAML を手編集することなく、訴訟プロファイル内の何か — リスクキャリブレーション、ハウススタイルのルール、エスカレーション連絡先、ランドスケープ注記 — を変更したい。

## 何をするか

1. **設定を読む。**
   `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md`
   (および 1 階層上の `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md`)
   を読みます。プラグイン設定が存在しないか、`[PLACEHOLDER]` 値が残っている場合:

   > You haven't run setup yet. Run `/litigation-legal-ja:cold-start-interview`
   > first — customize is for adjusting a profile you already have.

2. **カスタマイズ可能なマップを表示。** プロファイルにあるものをグループ化してリストアップし、現在値の 1 行サマリーを付ける:

   - **会社 / あなたは誰か** — 名前、業界、法域、ステージ、プラクティス設定
     *(全 12 プラグインで共有 — `company-profile.md` を介して変更が流れる)*
   - **プラクティスロール** — 社内弁護士 / 外部弁護士 / ソロ / クリニック
   - **サイド** — 原告 / 被告 / 混合、およびポスチャのニュアンス(集団訴訟被告、規制執行被告、商事原告、など)
   - **リスクキャリブレーション** — 受領した要求書、サブポエナ、新規案件の高 / 中 / 低リスクの定義;エスカレーショントリガー
   - **ランドスケープ** — 常連の敵対者、友好的・非友好的なヴェニュー、知っておくべき裁判官、常設の OC 関係
   - **ハウススタイル** — ブリーフスタイル、宣誓供述書フォーマット、要求書テンプレート、デポジションアウトラインの構造、リーガルホールドテンプレート
   - **重大度語彙マップ** — クライアント / 内部 / 裁判所向け出力で重大度ラベルをどう翻訳するか
   - **人々** — 案件リード、社内チーム、案件種別ごとの外部弁護士、エスカレーションチェーン
   - **ワークフロー** — 案件ワークスペース、ポートフォリオログ、OC ステータスケイデンス、リーガルホールド更新ケイデンス
   - **連携** — ドキュメントストレージ / e-filing / カレンダー / Slack ステータス、フォールバック

3. **何を変えたいか尋ねる。**

   > What would you like to adjust? Pick a section, or describe the change in
   > your own words.

4. **変更を加える。** 現在値を表示、新しい値を尋ね、下流で何が変わるか説明、確認、設定に書き込み。

   例:
   - *Side 混合 → 被告専用:* 「`/matter-intake` は原告サイドの質問をしなくなります。`/demand-draft` は被告サイドの訴訟前要求でも動作しますが、出発のフレームが変わります。」
   - *リスクキャリブレーションで高リスク閾値を厳しく:* 「より多くの受領要求とサブポエナが `/matter-briefing` と `/oc-status` にルートされます。」
   - *IP 案件用の新規スタンディング OC:* 「`/oc-status` は IP タグの案件の週次スイープにこの事務所を含めます。」

5. **共有プロファイルの変更**(会社名、業界、法域、プラクティス設定、ステージ)については
   `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` に書き込み、注記:

   > This change affects all 12 plugins — any plugin that reads your
   > jurisdiction footprint now sees [new value].

6. **締めくくり。**

   > Done. Your next output will reflect the change. Anything else? You can
   > run `/litigation-legal-ja:customize` anytime.

## ガードレール

- **セクションを削除しない。** ユーザーがスコープから案件種別を「削除」したい場合、`[Not currently handled]` とマークすることを提案し、intake のルーティングがどう変わるかを説明します。
- **内部の不整合をフラグ。** 変更がプロファイルを矛盾させる場合(例: 原告のみのサイド + 被告のみの OC ロスター;または「ハイボリューム」ポートフォリオ + 案件ワークスペース未設定)、緊張をフラグします。
- **ガードレール低下をフラグ。** `/demand-draft` の FRE 408 / 特権ゲート、案件出力の特権ヘッダー、出典帰属タグ、引用された権威の `[verify]` タグは load-bearing です — 削除しないでください。`[review]` フラグと「弁護士レビューなしで提出しない」のフレーミングは load-bearing です。
- **一度に 1 つの変更。** インタビュー全体を再質問しないでください。
