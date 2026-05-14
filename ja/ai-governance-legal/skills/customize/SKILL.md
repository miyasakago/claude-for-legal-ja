---
name: customize
description: >
  Guided customization of your AI governance practice profile — change one thing
  without re-running the whole cold-start interview. Adjust risk posture,
  escalation contacts, use-case registry entries, vendor AI positions,
  AI policy commitments, impact-assessment house style, or matter workspace
  paths. Use when the user says "change my [thing]", "update my profile",
  "edit my config", "tune my playbook", or "customize".
  AI ガバナンス・プラクティスプロファイルのガイド付きカスタマイズ — コールドスタート・
  インタビュー全体を再実行することなく、1 つのことを変更します。リスクスタンス、
  エスカレーション連絡先、ユースケース台帳エントリ、ベンダー AI ポジション、
  AI ポリシーコミットメント、影響評価のハウススタイル、案件ワークスペースパスを調整します。
  日本語トリガー: 設定変更、プロファイル更新、config を編集、プレイブックを調整、
  カスタマイズ。
argument-hint: "[section name, or describe what you want to change]"
---

# /customize

## このスキルが実行される条件

ユーザーが `/ai-governance-legal-ja:customize` をタイプしました。彼らはプラクティスプロファイル内の何か — リスクスタンス、エスカレーション連絡先、プレイブックポジション、法域、出力フォーマット — を、コールドスタート・インタビュー全体を再実行することも YAML を手動編集することもなく変更したいと考えています。

## やるべきこと

1. **設定を読む。**
   `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md`
   (および 1 階層上の `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md`)
   を読みます。プラグイン設定が存在しないか、まだ `[PLACEHOLDER]` 値を含んでいる
   場合、次のように述べます:

   > まだセットアップを実行していません。まず `/ai-governance-legal-ja:cold-start-interview`
   > を実行してください — customize は既に持っているプロファイルを調整するためのものです。

2. **カスタマイズ可能なマップを表示。** プロファイル内のものをグループ化してリストし、現在値の 1 行サマリーを付けます:

   - **Company / who you are** — 名前、業界、法域、ステージ、プラクティス設定 *(すべての 12 プラグインで共有 — 変更は `company-profile.md` を通じて流れる)*
   - **Regulatory footprint** — 範囲内の EU AI Act、州 AI 法、セクター規制者
   - **Risk posture** — conservative / middle / aggressive、それぞれがトリアージと AIA 出力に何を意味するか
   - **People** — ガバナンスチーム、AI リスクオーナー、エスカレーションチェーン、承認者
   - **Use case registry** — approved / conditional / never のエントリ、各々に付随する条件
   - **AI system inventory** — EU AI Act 下でのシステムごとのロール(provider / deployer など)と階層。専用エディタには `/ai-governance-legal-ja:ai-inventory` を実行。
   - **Vendor AI governance** — データの学習利用、責任、モデル変更通知、その他のベンダー AI プレイブックポジション
   - **AI policy commitments** — 公的または内部の AI ポリシーが行ったコミットメント、プラグインがクロスチェックするもの
   - **Impact assessment house style** — AIA セクション順、リスクスコアリングフォーマット、ステークホルダーフレーミング
   - **Workflow** — インテークパス、出力フォーマット、案件ワークスペースパス、ポリシーモニターのレビューケイデンス
   - **Integrations** — 何が接続されているか(Slack、ドキュメントストレージ、scheduled-tasks)、何がフォールバックするか

3. **何を変更したいかを尋ねる。**

   > 何を調整しますか? セクションを選ぶか、あなたの言葉で変更を記述してください。

4. **変更を行う。** 現在値を表示し、新しい値を尋ね、何が下流に変わるかを説明し、確認し、設定に書き込みます。

   下流説明の例:
   - *Risk posture middle → conservative:* 「より多くのユースケースを approved ではなく conditional としてフラグし、より多くの AIA フォローアップを表面化し、より保守的なベンダー AI レッドラインを推奨します。」
   - *エスカレーション連絡先の追加:* 「エスカレーションをルーティングするすべてのスキル(`/triage`、`/review-vendor-ai`、`/gap-check`)は、関連するリスクバンドでこの連絡先を含めるようになります。」
   - *新しいユースケース台帳エントリ:* 「`/triage` は次回の実行でこのエントリに対してマッチします。既存の AIA は書き直されません — 新しいスタンスを反映したい場合は再実行してください。」

5. **共有プロファイル変更**(会社名、業界、法域、プラクティス設定、ステージ)の場合: `~/.claude/plugins/config/claude-for-legal-ja/company-profile.md` に書き、注記:

   > この変更はすべての 12 プラグインに影響します — 法域フットプリントを読むプラグインは [new value] を見るようになります。

6. **閉じる。**

   > 完了。次の出力は変更を反映します。他に何かありますか? `/ai-governance-legal-ja:customize` はいつでも実行できます。

## ガードレール

- **セクションを決して削除しないこと。** ユーザーが何かを「削除」したい場合、`[Not configured]` に設定し、それがプラグインの動作に何を意味するかを説明します。(「エスカレーションチェーンを削除すると、`/triage` はエスカレーションに値する項目をフラグしますが、特定の人物にルーティングしません。」)
- **内部矛盾をフラグ。** 変更がプロファイルを矛盾させる場合(例: risk posture aggressive + エスカレーション「すべてが GC に行く」; または「EU AI Act 範囲内」 + 「EU 用にフラグされたシステムなし」)、緊張をフラグし、どちらを望むかを尋ねます。
- **ガードレール劣化をフラグ。** ユーザーがガードレールをオフにしたい場合(「`[review]` フラグの追加を止める」「引用警告を落とす」「特権ヘッダーをスキップ」)、ガードレールが何から保護するかを説明し、トレードオフを理解していることを確認します。ほとんどのガードレールは調整可能 — いくつかは構造的:
  - `[review]` フラグメカニズム(自信ある誤った答えではなく、法的判断が必要なときをユーザーに伝える) — ロードベアリング、削除しないこと。
  - 取得されたコンテンツのソース属性タグ — ロードベアリング、削除しないこと。
  - 引用された法令/規制の `[verify]` タグ — ロードベアリング、削除しないこと。
- **一度に 1 つの変更。** インタビュー全体を再質問しないこと。ユーザーが複数の変更を望む場合、順次処理し、移動する前に各々を確認します。
