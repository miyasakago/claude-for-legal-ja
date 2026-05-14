---
name: customize
description: >
  Guided customization of your IP practice profile — change one thing without
  re-running the whole cold-start interview. Adjust risk posture, escalation
  contacts, portfolio scope, brand protection strategy, enforcement posture,
  clearance thresholds, OSS review rules, or matter workspace paths. Use when
  the user says "change my [thing]", "update my profile", "edit my config",
  or "customize".
  IP プラクティスプロファイルのガイド付きカスタマイズ — コールドスタート・インタビュー
  全体を再実行せずに一つの項目を変更します。リスクスタンス、エスカレーション連絡先、
  ポートフォリオの範囲、ブランド保護戦略、エンフォースメント・スタンス、クリアランス閾値、
  OSS レビュー規則、または案件ワークスペース・パスを調整。
  日本語トリガー: 「[項目] を変更したい」「プロファイルを更新」「設定を編集」「カスタマイズ」。
argument-hint: "[セクション名、または変更したい内容を記述]"
---

# /customize

## いつ実行するか

ユーザーが `/ip-legal-ja:customize` を入力したとき。プラクティスプロファイルの何か — リスクスタンス、エスカレーション連絡先、ポートフォリオの立場、エンフォースメント戦術 — をコールドスタート・インタビュー全体を再実行せず、かつ YAML を手編集せずに変更したい場合です。

## 何をするか

1. **設定を読みます.** `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md`(および 1 つ上の階層にある `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md`)を読みます。プラグイン設定が存在しないか、まだ `[PLACEHOLDER]` 値を含む場合は、次のように言います:

   > まだセットアップを実行していません。先に `/ip-legal-ja:cold-start-interview` を実行してください — customize は既存プロファイルの調整用です。

2. **カスタマイズ可能なマップを表示します.** プロファイル内の項目をグルーピングしてリスト化し、現在の値の 1 行サマリーを付けます:

   - **Company / who you are** — 名前、業種、法域、ステージ、プラクティスセッティング *(12 個のプラグイン全体で共有 — 変更は `company-profile.md` 経由で全プラグインに流れます)*
   - **IP practice profile** — どの IP タイプがスコープにあるか(特許、商標、著作権、営業秘密、意匠)、プラクティスの方向性(出願 / 取引 / エンフォースメント / インハウス・ポートフォリオ)
   - **Risk posture** — conservative / middle / aggressive、各々がクリアランス閾値、FTO 意見、C&D エスカレーションに何を意味するか
   - **People** — IP カウンセル、IP タイプ別の外部事務所、エンフォースメント・エスカレーション・チェーン、発明委員会
   - **Portfolio** — 特許ファミリー、商標クラス、主要マーク、登録国、ウォッチサービス
   - **Brand protection** — マーケットプレイス・テイクダウン、ドメイン・スクワッター、パロディ / フェアユース判断に関するエンフォースメント・スタンス
   - **Enforcement posture** — C&D vs キュアレター vs 訴訟をいつ送るか、侵害タイプ別のエスカレーション・トリガー
   - **Clearance and FTO** — 調査ベンダー、クリアランス信頼度の閾値、FTO 意見書フォーマット
   - **OSS review** — ライセンス階層ポリシー、出荷ブロッカー・ライセンス、新規依存関係のレビュー実行頻度
   - **Workflow** — 案件ワークスペース(案件 ID、ファミリー ID)、ドケットフィード、発明インテーク・フォーム
   - **Integrations** — 特許ドケットシステム / 商標庁コネクター / Slack / 文書ストレージのステータス、フォールバック

3. **何を変更したいか尋ねます.**

   > どこを調整しますか? セクションを選ぶか、ご自身の言葉で変更内容を記述してください。

4. **変更を実行します.** 現在の値を見せ、新しい値を尋ね、下流で何が変わるかを説明し、確認し、設定に書き込みます。

   例:
   - *新しい商標監視クラスの追加:* 「`/portfolio` はクラス XX を監視レポートに含み、`/infringement-triage` はクラス XX 関連の発見を適切にルーティングします」
   - *エンフォースメント・スタンスを aggressive → middle へ:* 「`/cease-desist` は曖昧なケースで C&D 直行ではなく、最初のオプションとしてキュアレターのドラフトを提示します」
   - *新しい出荷ブロッカー OSS ライセンス:* 「`/oss-review` はこのライセンスを含むレビューを警告ではなく失敗にします」

5. **共有プロファイルの変更**(社名、業種、法域、プラクティスセッティング、ステージ): `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` に書き込み、次を注記します:

   > この変更は 12 個のプラグインすべてに影響します — あなたの法域フットプリントを読む任意のプラグインが新しい値を見ます。

6. **クローズ.**

   > 完了です。次の出力に変更が反映されます。他にありますか? `/ip-legal-ja:customize` はいつでも実行できます。

## ガードレール

- **セクションを削除しないこと.** ユーザーが IP タイプをスコープから「除去」したいなら、`[Not currently in scope]` に設定し、何が落ちるかを説明します。
- **内部矛盾をフラグ.** 変更がプロファイルを矛盾させる場合(例: 商標がスコープ外 + 商標ウォッチサービス設定済み、または aggressive エンフォースメント・スタンス + 「すべての C&D は外部弁護士に」)、テンションをフラグします。
- **ガードレール劣化をフラグ.** `[review]` フラグ、ソース帰属タグ、引用された典拠の `[verify]` タグは load-bearing です — 削除しないこと。クリアランス信頼度は `/clearance` 出力で load-bearing です — 抑制しないこと。
- **一度に一つの変更.** インタビュー全体を再質問しないこと。
