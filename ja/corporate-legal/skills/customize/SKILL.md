---
name: customize
description: >
  コーポレートプラクティスプロファイルのガイド付きカスタマイズ — コールドスタート・インタビュー全体を再実行せずに 1 件を変更します。
  リスクスタンス、エスカレーション連絡先、アクティブモジュール(M&A / Board / Public Company / Entity Management)、
  重要性基準、開示別紙フォーマット、書面決議の先例、案件ワークスペースのパスを調整できます。
  Use when the user says "change my [thing]", "update my profile", "edit my config", or "customize".
  日本語トリガー: 「[項目] を変更」「プロファイル更新」「設定編集」「カスタマイズ」。
argument-hint: "[section name, or describe what you want to change]"
---

# /customize

## いつ実行されるか

ユーザーが `/corporate-legal-ja:customize` をタイプした時。プラクティスプロファイルの何かを変更したい — リスクスタンス、エスカレーション連絡先、モジュールのオン/オフ、出力フォーマット — コールドスタート・インタビュー全体を再実行せず、YAML を手編集せずに行いたい。

## 何をするか

1. **設定を読む。** `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md`(および 1 階層上の `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md`)を読む。プラグイン設定が存在しない、または `[PLACEHOLDER]` 値を含む場合、言う:

   > まだセットアップが実行されていません。まず `/corporate-legal-ja:cold-start-interview` を実行してください — customize は既存プロファイルの調整用です。

2. **カスタマイズ可能マップを表示。** プロファイルにあるものをグループ化してリスト、現在値の 1 行サマリー:

   - **会社 / 誰か** — 名前、業種、法域、ステージ、公開 vs 非公開、実務環境 *(全 12 プラグインで共有 — 変更は `company-profile.md` 経由で流れる)*
   - **アクティブモジュール** — M&A、Board & Secretary、Public Company、Entity Management のうちオンのもの。モジュールをオン/オフするとどのスキルがセットアップを促すかが変わる。
   - **リスクスタンス** — conservative / middle / aggressive、各々が DD 重要性と開示別紙スコープに何を意味するか
   - **人物** — ディールチーム、board secretary、entity management オーナー、エスカレーションチェーン
   - **M&A モジュール** — 重要性基準(契約金額、人員、収益)、信頼するデータルームプラットフォーム、AI 一括レビューの信頼レベル(Luminance / Kira)、ディールチーム・ブリーフィングのケイデンス
   - **Board & Secretary モジュール** — ハウス書面決議フォーマット、署名者の選好、委員会構造
   - **Public Company モジュール** — 報告カレンダー、開示統制、10-K/10-Q レビュータイミング
   - **Entity Management モジュール** — 法人テーブル、登録代理人、申請法域、年次報告書カレンダー
   - **ワークフロー** — 案件ワークスペース(ディールルーム)、クロージング・チェックリストの場所、VDR watcher のケイデンス
   - **連携** — Box / Intralinks / Datasite / CT Corp / Slack ステータス、フォールバック

3. **何を変更したいか尋ねる。**

   > 何を調整しますか? セクションを選ぶか、ご自身の言葉で変更内容を説明してください。

4. **変更を実行。** 現在値を表示、新しい値を尋ね、下流で何が変わるかを説明、確認、設定に書き込む。

   例:
   - *重要性基準 $250K → $500K:* 「`/diligence-issue-extraction` と `/material-contract-schedule` は $500K をカットオフとして扱います。既存の所見はログのまま;新閾値を過去にも適用したい場合は再実行。」
   - *Public Company モジュールをオン:* 「次回そのエリアで何か実行する時、報告カレンダーと開示統制を尋ねます。」
   - *AI 一括レビュー信頼 "check every row" → "spot-check 10%":* 「`/ai-tool-handoff` は全抽出ではなく 10% サンプルを QA します。」

5. **共有プロファイル変更**(会社名、業種、法域、実務環境、ステージ)の場合: `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` に書き出し、注記:

   > この変更は全 12 プラグインに影響します — 法域フットプリントを読むプラグインは [new value] を見るようになりました。

6. **クローズ。**

   > 完了。次回出力に変更が反映されます。他に何か? `/corporate-legal-ja:customize` はいつでも実行可能。

## ガードレール

- **セクションを削除しない。** ユーザーが何かを「削除」したい場合、`[Not configured]` に設定し、それがプラグインの挙動に何を意味するか説明。
- **内部不整合をフラグ。** 変更がプロファイルを矛盾させる場合(例:Public Company モジュール off + "SEC counsel" がエスカレーションに残る;または aggressive risk posture + $25K 重要性基準)、緊張をフラグ。
- **ガードレール劣化をフラグ。** `[review]` フラグ、取得文書のソース帰属タグ、引用された典拠の `[verify]` タグは load-bearing — 削除前にトレードオフを説明。
- **一度に 1 変更。** インタビュー全体を再質問しない。
