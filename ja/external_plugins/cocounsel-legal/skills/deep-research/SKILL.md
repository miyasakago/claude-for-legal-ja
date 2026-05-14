---
name: cocounsel-legal:deep-research
version: 0.1.0
description: >
  ユーザーが明示的に法的リサーチまたは Westlaw Deep Research を要求した場合、
  CoCounsel Legal / cocounsel legal のサポートを求めた場合、または米国法の
  説明・分析・統合を必要とする質問をした場合に、本スキルを使用する。
  (Use this skill whenever a user specifically requests legal research or
   Westlaw Deep Research, asks for CoCounsel Legal or cocounsel legal support,
   or asks a question that requires explaining, analyzing, or synthesizing U.S. law.
   日本語トリガー: 法的リサーチ、Westlaw、判例調査、米国法の調査、ディープリサーチ)
allowed-tools:
  - mcp
  - Bash
---

# Westlaw Deep Research

Westlaw Deep Research は、判例法、制定法、行政決定の Westlaw データベースを検索し、関連する権威を説明・分析・統合する書面リサーチレポートを返します。

Deep Research は人間のリサーチャーの方法論を模倣したエージェント・プロセスを採用し、Westlaw の独自ツールを活用して Westlaw および Practical Law 上の信頼できるコンテンツを体系的に分析します。

本スキルは、リサーチサイクル全体(start、poll、report)を自律的に実行します。

## 前提条件

`cocounsel-legal` MCP サーバーが接続されている必要があります。リサーチ開始前に利用可能であることを確認してください。サーバーが接続されていない場合は、ユーザーにその旨を伝えて停止してください。

## 使用するとき

- 判例法、制定法、規制、行政資料、二次資料、Practical Law 文書、最新情報(JD Supra を含む)から回答可能な質問に使用します。例:
- ある論点について裁判所がどう判断したか、ある立場を支持/否定する根拠は何か
- 請求原因の構成要件・抗弁、または特定法域における支配的基準
- 制定法、規制、または法理がどう解釈・適用されているか
- 未決着の論点について双方の立場の論拠

## 使用しないとき

- リクエストが下記カテゴリに該当する場合、本スキルは適合しないことを簡潔に説明し、推奨代替手段をユーザーに案内してください。
  - **特定文書の全文取得**
    - _代わりに:_ Westlaw の従来の検索ボックスを推奨。
  - **特定の制定法、規制、または論文の内容を単独で要約**(例:「カリフォルニア州証拠法は伝聞についてどう定めているか」)
    - _代わりに:_ Westlaw の従来の検索ボックスを推奨。
  - **分析的リクエスト**(「Scalia 判事は何回支持判決を出したか」など)
    - _代わりに:_ Westlaw の Litigation Analytics を推奨。
  - **計算**(「最終提出可能日はいつか」など)
    - _代わりに:_ このリクエストは Westlaw Deep Research の対象外。
  - **結果予測**
    - _代わりに:_ このリクエストは Westlaw Deep Research の対象外。
  - **法的文書、書式、テンプレートの起案**
    - _代わりに:_ CoCounsel を推奨。
  - **特定の裁判官、弁護士、当事者に関する情報**
    - _代わりに:_ Westlaw の Litigation Analytics を推奨。
  - **外国法(米国以外の法律)**
  - _代わりに:_ 国別 Westlaw(Westlaw UK、Westlaw Canada など)または Westlaw International を推奨。
  - **3 つを超える法域にわたる比較**
    - _代わりに:_ Westlaw の AI Jurisdictional Surveys を推奨。
  - **Terms and Connectors(ブール検索)クエリ**
    - _代わりに:_ Westlaw の従来の検索ボックスを推奨。
  - **タスク実行命令**
    - _代わりに:_ CoCounsel を推奨。
  - **網羅的な結果リストの取得**
    - _代わりに:_ Westlaw のブール検索または Precision Research を推奨。
  - **潜在的な請求原因の特定**
    - _代わりに:_ Westlaw の Claims Explorer を推奨。
  - **事実関係の網羅的レビュー**(例:「〜について論じたすべての判例を見つけて」)
    - _代わりに:_ Westlaw の Precision Research を推奨。
- 代替を提案した場合は、そのタスクに対して以後 Deep Research スキルの使用を試みないでください。

## コミュニケーション・ルール

- ツール呼び出し、ツール呼び出し予算、ポーリング、ステータスチェック、内部制限、conversation ID、percent_complete、その他いかなる実装詳細についても**決してユーザーに言及しない**。リサーチそのものについて話し、追跡の仕組みについては話さないでください。
- リサーチ完了前に(内部理由で)一時停止が必要な場合、その理由を説明しない。
- ユーザーには「リサーチが進行中であり、まもなくレポートが完成する」とのみ伝える。

## リサーチワークフロー

### 1. クエリのフレーミング

- ユーザーのクエリから、明確な自然言語で法的リサーチ質問を抽出する。
- ユーザーが名指しした場合、最大 3 つの法域を使用する。
- 法域が記載されていない場合、ユーザーにどの法域を使うか質問する。

### 2. リサーチ開始

- MCP ツールを呼び出してリサーチを開始: `legal_research_start_deep_research(query, jurisdictions)`
- パラメータ:
  - `query`(string、必須): 法的リサーチ質問
  - `jurisdictions`(string のリスト、任意): 最大 3 法域(例: `["California", "New York"]`)
- `conversation_id` と初期の `status` が返る。後続呼び出しのために `conversation_id` を保存する。
- 次のステップ: `conversation_id` を使って `check_deep_research_status` を呼び出す。
- 初回ステータスチェック前に、約 10 秒待機する(サーバーがまだセットアップ中)。

#### レンダリング

- ディープリサーチが進行中であることをユーザーに伝え、法的質問を自然でプロフェッショナルな言葉で簡潔に再述する。
- `conversation_id` をユーザーに表示しない。

### 3. 完了までポーリング

- `legal_research_check_deep_research_status(conversation_id)` をポーリングする。
- ポーリングの間には必ず Bash の `sleep` を挟む。`check_deep_research_status` を連続して呼び出さない。
- `is_terminal` が true になるまで継続する。
- レスポンスに基づいて次のアクションを決定する:
  - (1) `is_terminal` が true で `status` が `'complete'` の場合、`get_deep_research_report` を `conversation_id` で呼び出す。
  - (2) `status` が `'failed'` の場合、停止し、フィールド名を露出せずに `error_type` と `failure_reason` を平易な言葉でユーザーに報告する。
  - (3) それ以外の場合、レスポンスの `next_action_poll_backoff_ms` フィールド(ミリ秒)の時間 sleep し、再度ポーリングする。
- `percent_complete` が連続 2 回のチェックで変化しない場合、sleep に 5 秒を追加する。

#### レンダリング

- リサーチプロセスをナレーションするように、平易な言葉でコミュニケーションする。
- `research_plan` を Markdown の順序なしリスト(1 行 1 項目、各行を `- ` で始める)としてレンダリングし、ステップが視覚的に明瞭に分離されるようにする。
- リストの前後に空行を入れて、きれいに表示されるようにする。
- 新しく伝えるべきことがあるとき(ステップ完了、新ステップ開始)のみユーザーに更新を伝える。同じステータスを繰り返さない。

### 4. レポートを取得し、**逐語的に**提示

- ステータスが `"complete"` になったら、最終レポートを取得: `legal_research_get_deep_research_report(conversation_id)`
- レポートは `answer_text` フィールドにある。
- これがリサーチライフサイクルの最終出力。以後のツール呼び出しは不要。
- ユーザーが同じトピックでフォローアップ質問をする場合、新規リサーチセッションを開始するのではなく、同じ `conversation_id` で `follow_up_deep_research` を使う。

#### レンダリング

- `answer_text` の内容を**編集、追加、削除、再構築なしに**回答にそのまま貼り付ける。ペイロードには Markdown、HTML アンカー、インラインアンカー引用、引用ブロック化されたソース抜粋、水平線が含まれる — すべての要素は意図的であり、保持しなければならない。

## 役に立つ情報

システム障害や、アクセスに関するユーザーからの質問があった場合は、以下を共有してください:

- サポートメール: cocounselsupport@tr.com
- サブスクリプション必要: ユーザーのアカウントで MCP コネクターが有効化された CoCounsel Legal サブスクリプション。エンタイトルメントやアクセスに関する質問は cocounselsupport@tr.com まで。
- プロバイダー: Thomson Reuters
- 関連ポリシー:
  - プライバシー: https://www.thomsonreuters.com/en/privacy-statement.html
  - 利用規約: https://www.thomsonreuters.com/en/terms-of-use.html
  - アクセシビリティ: https://www.thomsonreuters.com/en/policies/accessibility.html
