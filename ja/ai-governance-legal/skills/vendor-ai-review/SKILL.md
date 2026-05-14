---
name: vendor-ai-review
description: >
  Review vendor AI terms — agreement, addendum, or ToS AI provisions — against your
  governance positions; flag training-on-data, liability, model changes, and AI policy
  consistency. Use when user says "review this AI agreement", "check OpenAI terms",
  "what did we agree to with [vendor]", "vendor sent an AI addendum", "is this AI
  contract okay", or attaches vendor AI terms.
  ベンダーの AI 利用規約(契約書、addendum、または ToS の AI 条項)をガバナンスポジションに
  照らしてレビューします。データの学習利用、責任、モデル変更、AI ポリシー整合性をフラグします。
  日本語トリガー: この AI 契約をレビュー、OpenAI 規約をチェック、[ベンダー] と何を合意したか、
  ベンダーが AI addendum を送ってきた、この AI 契約は OK か、ベンダー AI 利用規約添付。
argument-hint: "[vendor name, or attach the contract]"
---

# /vendor-ai-review

1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます。ベンダーガバナンスポジションがポピュレートされていることを確認 — そうでない場合は停止してセットアップに誘導します。
2. 下記のフレームワークを使用。
3. 文書タイプ(AI addendum / メイン契約の AI 条項 / ToS)を確認。AUP のみが提供された場合、フル規約を依頼。
4. 条項ごとのレビュー: データの学習利用、入力の機密性、モデル変更、出力 IP、責任、インシデント通知、人間レビュー権、利用制限、監査権。
5. DPA が存在するが AI addendum がない場合の AI addendum ギャップチェック。
6. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` に対する AI ポリシー整合性差分。
7. 出力: ボトムライン、条項ごと、推奨レッドライン、彼らが動かない場合のルーティング。

```
/ai-governance-legal-ja:vendor-ai-review openai-enterprise-agreement.pdf
```

---

## Matter context (案件コンテキスト)

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` を確認します。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、この段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は不可視です。有効でアクティブな案件がない場合、尋ねます: 「どの案件? `/ai-governance-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください。」アクティブ案件の `matter.md` を案件固有のコンテキストとオーバーライドのためにロードします。出力は `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/<matter-slug>/` の案件フォルダーに書き込みます。`Cross-matter context` が `on` でない限り、別の案件のファイルを決して読まないこと。

---

## 目的

ベンダー AI 規約は、ガバナンスポジションが実際にテストされる場所です。コールドスタート・インタビューは、何を *望む* かをキャプチャします。このスキルは、何に *同意した* かを確認 — そして 2 つの間のギャップをフラグします。

ここでの方向は常に同じです: 私たちは deployer または buyer としてベンダーの規約をレビューします。これは DPA レビューの controller/processor の質問とは反対のスタンスです — flip はありません。

変わるのは *入力* です:
- スタンドアロンの AI 契約または AI addendum(最も構造化されている)
- AI 条項が埋め込まれたベンダーの汎用 ToS(しばしば埋もれている)
- 許容利用ポリシー(何をできないかを伝える; ベンダーがデータまたは出力で何をできるかについては何も言わない)
- 組み合わせ — マスター契約 + DPA + AI addendum(本格的なエンタープライズ AI ベンダーで一般的)

DPA が既に inplace されている場合、このレビューはそれを補完します — 代替ではありません。DPA はデータ保護義務を支配しますが、AI 規約はモデル固有の権利とリスクを支配します。両方をレビューする必要があります。

---

## プレイブックをロード

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → `## Vendor AI governance` を読みます。また `## AI policy commitments` を読みます — ベンダー規約は、我々自身のポリシーが課す利用制限に一致できません、もし我々が異なるものに同意した場合。

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` が `[PLACEHOLDER]` を含む場合、このバウンスを表面化:

> プラクティスプロファイルをまだ設定していないことに気づきました — それがベンダーガバナンスポジションをあなたのプラクティスに合わせて調整する方法です。
>
> **2 つの選択肢:**
> - `/ai-governance-legal-ja:cold-start-interview`(2 分)を実行してプロファイルを設定し、その後あなたのポジションに合わせてレビューします。
> - **"provisional"** と言うと、汎用デフォルト — US jurisdiction、中程度のリスク許容度、lawyer role、playbook なし — に対してレビューし、すべての出力に `[PROVISIONAL — configure your profile for tailored output]` をタグ付けして、コミットする前に何をするかを確認できるようにします。

### Provisional モード

ユーザーが "provisional" と言った場合、これらの汎用デフォルトを使用してベンダー AI レビューを通常通り実行します: 中程度のリスク許容度、lawyer role、US jurisdiction、playbook なし(設定されたポジションへのマッチではなく、第一原理からすべての一般的なベンダー AI リスクをフラグ)。レビューアー注記とすべての finding ブロックに `[PROVISIONAL]` をタグ付け。出力の最後に追加:

> 「これはデフォルト仮定に対する汎用実行でした。`/ai-governance-legal-ja:cold-start-interview` を実行してあなたのプラクティス — ベンダーガバナンスポジション、jurisdiction、リスク許容度 — に合わせた出力を得てください。2 分。」

---

## 文書を読む前に

ユーザーが実際のベンダー規約を共有していない場合、尋ねます:

> 「ベンダーの AI 規約を共有できますか? 最も役立つのは実際の契約言語 — あれば AI addendum、または AI 条項がハイライトされたメイン契約。許容利用ポリシーだけでは、ベンダーが私たちの入力で何をできるかは分かりません; 私たちが何をすることが許可されているかしか伝えません。」

許容利用ポリシーのみを共有した場合:
> 「これは許容利用ポリシー — ベンダーの AI で私たちが何をできないかを伝えます。それは役立つコンテキストですが、商業条項に対処しません: ベンダーが私たちのデータで訓練できるか、AI エラーに対する責任は何か、モデルが変わったときに通知するか。サービス契約または AI addendum はありますか?」

---

## 条項ごとのレビュー

### コアな AI 固有条項(すべてのベンダー AI 契約で確認)

下記の各条項をレビューします。各条項について、ベンダーの契約が実際に何を述べるかを抽出し、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → `## Vendor AI governance` のポジション(standard / acceptable fallback / automatic no)と比較します。デフォルトポジションはこのスキルからではなく、チームのプレイブックから来ます。

| Term | What to look for |
|---|---|
| **Training on our data(データの学習利用)** | ベンダーは私たちの入力を訓練、ファインチューニング、またはモデル改善に使用するか? 明示的なオプトアウトまたは禁止はあるか? 訓練はデフォルトでオプトインかオプトアウトか? |
| **Confidentiality of inputs(入力の機密性)** | 我々のプロンプト、文書、データは機密か? ベンダースタッフが入力を読むことを許す「品質レビュー」または人間レビュー carveouts はあるか? |
| **Model changes(モデル変更)** | モデルへの重要な変更について通知義務はあるか? Version pinning は利用可能か? |
| **Output ownership / IP(出力の所有権 / IP)** | AI 生成コンテンツを誰が所有するか? 出力でベンダーへの license-back はあるか? IP 補償はあるか? |
| **Liability for outputs(出力に対する責任)** | AI が有害、不正確、または侵害する出力を生成した場合、ベンダーは責任を受諾するか? キャップ構造? Carve-outs? |
| **Incident notification(インシデント通知)** | AI システムが失敗、侵害、または systematic エラーを私たちに影響する形で生成したとき、どのように、いつ通知されるか? |
| **Human review rights(人間レビュー権)** | 特定のケースで出力の人間レビューを要求できるか? AI 決定を appeal または dispute できるか? |
| **Use restrictions(利用制限)** | 我々は何を禁じられているか? 実際にツールを使いたい目的と一致するか? 我々の意図する使用を sweep する定義用語(例: "automated decision-making")はあるか? |
| **Audit / auditability(監査 / 監査可能性)** | SOC 2、サードパーティ監査、バイアステスト結果 — 監査権はあるか? |
| **Subprocessors / model providers(副処理者 / モデル提供者)** | ベンダーはモデルに sub-vendor を使用するか? 開示されているか? 誰の規約が支配するか? |
| **Data residency(データレジデンシー)** | 我々のデータはどこで処理されるか? 推論のためにどこに行くか? |
| **Term and termination(期間と終了)** | 終了時に我々のデータはどうなるか? 削除タイムライン? |
| **Stacked-vendor accountability(スタックされたベンダーの説明責任)** | このベンダーはモデルプロバイダー(例: Anthropic, OpenAI, Google, Meta)か、または他者のモデルの deployer(例: Claude, ChatGPT, Gemini の SaaS ラッパー)か、インフラホスト型ファウンデーションモデルのリセラー(Anthropic-on-Bedrock, Claude-on-Vertex, OpenAI-on-Azure)か? 後者の場合: プレイ中の 2 つのベンダーの規約があります — レビューしているもの、加えて上流のモデルプロバイダーの規約。識別: (a) 入力の学習利用、保持、安全性を支配するのは誰の規約か、(b) モデル動作に対して契約的に責任を負うのは誰か、(c) 各上流コミットメント(例: 「入力で訓練しない」)があなたに flow-down されているか、ベンダーと上流プロバイダー間のみのままか。一方が他方の責任を否認する条項(例: 「Anthropic は Bedrock または AWS から受け取る他のサービスに対して責任を負わない」; 「Azure は OpenAI モデル出力に対する責任を否認」)、および相手方の契約がギャップを閉じるかをフラグ。2 つの契約を分離してレビューしないこと。 |

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` がこのリストの条項のポジションを定義していない場合、尋ねます: 「あなたのプレイブックは [term] をカバーしていません。あなたのデフォルトポジション、acceptable fallback、automatic no は何ですか? `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` に追加して、次のレビューが一貫するようにします。」

---

## プレイブック比較

上記の各条項について、見つけたものを `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` のポジションと比較します。

**各条項の出力フォーマット:**

> **[Term name]**
> 🟢 / 🟡 / 🟠 / 🔴
> **Vendor says:** [契約が実際に述べることの要約]
> **Our position:** [`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` から]
> **Gap:** [具体的なデルタ — または「Aligned」]
> **Proposed fix:** [具体的なレッドライン言語、または「escalate — outside fallback」]

重要度評価を一貫して使用(`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` ポジションに対してキャリブレート):

- 🟢 **Aligned** — プレイブックの standard ポジション以上。
- 🟡 **Note** — fallback 内だが standard より悪い; awareness のためにフラグ、ブロッカーではない。
- 🟠 **Significant** — standard ポジション外だが fallback 内; 署名前にレッドライン必要。
- 🔴 **Critical** — fallback 外; 解決なしにデプロイを進めるべきでない。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` ごとにエスカレート。

---

## AI addendum ギャップチェック

**ベンダーが DPA を持つが AI addendum がない場合:**

> 「DPA は inplace ですが、AI 固有 addendum はありません。DPA はデータ保護義務をカバーしますが、以下には対応しません: 我々のデータでの訓練、モデル変更通知、AI 出力に対する責任、AI システム障害のインシデント通知。
>
> [Standard / Elevated / High] tier ユースケースの場合、このギャップは [Standard tier では acceptable / Elevated または High tier ではブロッカー]。AI addendum を要求するか、少なくとも次の更新で AI 固有条項を交渉することを推奨します。」

**AI 条項が全くない場合:**

> 「この契約には AI 固有条項がありません。ベンダーは一般的なサービス規約の下で AI 駆動サービスを提供しています — つまり、最高リスクの AI ガバナンス項目(訓練、責任、モデル変更)に対して契約上の保護がありません。これは Elevated または High tier ユースケースに対して 🔴 です。」

---

## AI ポリシー整合性チェック

ベンダーの規約を `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の AI ポリシーコミットメントに対してクロスチェック。

一般的な衝突:
- 我々のポリシーがベンダーによる我々のデータの訓練利用を禁止 — ベンダーの規約がデフォルトでそれを許可。(契約に明示的な禁止またはオプトアウト確認が必要。)
- 我々のポリシーが特定のユースケースで人間レビューを要求 — ベンダーの規約が AI 出力を最終としている。(ベンダー規約ではなく、ワークフローが人間ステップを課す必要がある。)
- 我々の承認済みベンダーリストにこのベンダーが含まれていない — または blocklist に含まれている。
- 我々のポリシーが影響を受ける関係者への開示を要求 — ベンダーの規約が AI システム能力に守秘義務を課して開示を防ぐ。

すべての不一致をフラグします。どちらかが変わる必要があります。

---

## レッドラインの粒度

**可能な限り最小の粒度で編集する。** レッドラインは交渉のアーティファクトであり、書き直しではありません。Wholesale clause replacement は「我々はあなたの起草を捨てた」とシグナル — 攻撃的で、相手方に節全体を再読することを強制し、良かった部分を捨てます。Surgical レッドライン — 単語を削除、フレーズを挿入、サブ条項を再構造化 — は「我々には特定の依頼がある」とシグナルし、読み、理解し、受け入れるのが速くなります。

プレイブックポジションを達成する最小編集にデフォルト:
- フレーズの前に **単語** を置き換える。(「twelve (12)」 → 「twenty-four (24)」)
- 文の前に **フレーズ** を置き換える。(「paid by the Buyer」 → 「paid and payable by the Buyer」)
- 文を置き換える前に **サブ条項** を再構造化する。(複合条件を分割するために「(a)」と「(b)」を追加。)
- 節を置き換える前に **文** を置き換える。
- 相手方のバージョンがポジションから遠く、surgical 編集が新しいドラフトより読みにくい場合のみ **節全体** を置き換える — そしてそうするときは、送り状で述べる: 「変更が広範だったので §8.2 をマークアップではなく置き換えました。デルタをウォークスルーする用意があります。」

迷ったら、小さく。Surgical レッドラインを受け取るクライアントは、慎重に読んだことを信頼します。Wholesale な置き換えを受け取るクライアントは、本当に読んだのか疑問に思います。

## 出力

**ベンダー AI 契約の署名を推奨する前に(会社が執行するバージョン):** `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Who's using this` を読みます。Role が Non-lawyer の場合:

> このベンダー AI 契約に署名することは法的結果を持ちます。弁護士とレビューしましたか? Yes であれば続行。No であれば、彼らに持っていくブリーフはこちらです:
>
> [1 ページの要約を生成: ベンダーとユースケース、レビューされた主要条項(データ使用、責任、監査可能性、モデル変更、人間レビュー)、ベンダーポジションがポリシーから分岐する場所、何が受諾されているか、何が悪くなる可能性があるか、弁護士に尋ねるべきこと。]
>
> 弁護士、ソリシター、バリスター、またはあなたの法域のその他の認可された法律専門家を見つける必要がある場合: あなたの専門規制者の照会サービスが最速の出発点です(米国では state bar、英国では SRA/Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアでは Law Society、またはあなたの法域の相当物)。

このゲートを明示的な yes なしに通過しないこと。弁護士検討用のレビュー/レッドラインドラフトはゲートを必要としません — 署名はします。

```markdown
[WORK-PRODUCT HEADER — plugin config ## Outputs ごと — ロールにより異なる; `## Who's using this` を参照]

*このレビューは、典型的に NDA の下で機密であるベンダー契約規約から派生し、それ自体が privileged である可能性があります。ソースの機密性と privilege ステータスを継承します。privilege サークルを超えて配布すること(例: ベンダーに転送、オープンチャンネルで共有)は、privilege を waive し、NDA に違反する可能性があります。それに応じてマーク、保管、ルーティングしてください。*

# Vendor AI Review: [Vendor Name]

**Document reviewed:** [AI addendum / main agreement AI provisions / ToS]
**Reviewed:** [date]
**Use case(s):** [このベンダーの AI をデプロイする対象]
**Governance tier:** [Standard / Elevated / High]

---

## Bottom line

[2 文。これらの規約の下でデプロイできるか? 何が最初に変わる必要があるか?]

**Issues:** [N]🔴 [N]🟠 [N]🟡 [N]🟢

---

## Term-by-term

[上記の各条項について — ベンダーポジション、我々のポジション、ギャップ、重要度、提案された修正]

---

## AI addendum status

[Present / Absent — このデプロイメントに対して何を意味するか]

---

## AI policy consistency

[🟢 Consistent | 🟡 Flags: list]

---

## Recommended redlines

[統合されたドラフトレッドライン。外部送信前にカウンセルとレビュー。Fallback が存在しない critical な問題については、言語を提案するのではなくエスカレーションのためにフラグします。]

---

## If they won't move

[各 🔴 と 🟠 について: `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` からの fallback、または「escalate — outside fallback」とエスカレーション表ごとのルーティング]
```

---

## 実用的注記

**データの学習利用条項はほとんどの人が見逃すもの。**
ベンダー AI 規約は、API 入力がモデルの訓練または改善に使用できるかについて歴史的に大きく異なっています — 一部のベンダーはデフォルトで許可、他は禁止し、多くは時間とともにポジションを変えました。あなたの目の前にある特定の契約を読まずに、任意の特定ベンダーの現在のスタンスを仮定しないこと。これはほぼ常に、機密または敏感なデータを持つ任意の会社にとって最も重要な条項であり、評判または過去の経験から仮定するのではなく、書面で確認されなければなりません。

**AI スタックをマップする。** 現代の AI デプロイメントは層状です。規約をレビューする前に、層をマップ:
1. **エンドユーザー SaaS アプリケーション**(例: legal tech ツール、AI スコアリング付き CRM、ドキュメントアシスタント) — あなたの組織がサインアップするツール
2. **API ゲートウェイ / オーケストレーション層**(例: Azure OpenAI Service, AWS Bedrock, Google Vertex, LangChain-hosted) — しばしば不可視、常に独自の規約を持つ
3. **モデルプロバイダー**(例: Anthropic, OpenAI, Google, Meta) — LLM
4. **ホスト型ナレッジベース / RAG ソース**(例: ベクターデータベース、サードパーティデータコーパス、取得サービス) — Claude が読むデータ
5. **追加の副処理者** — 分析、ログ、ファインチューニングパートナー

尋ねます: 「スタックをウォークスルーしてください — [SaaS ツール] は内部で何を使っていますか? クラウド AI サービスの上に構築されていますか? モデルプロバイダーを直接、またはゲートウェイ経由で呼び出しますか? ホスト型ナレッジベースを使用しますか?」その後、上部だけでなく **各層** で規約をレビュー。

各層間のハンドオフは flow-down リスクです。層 3 の規約が他のことを言い、層 1 がコミットメントを flow down しなかった場合、層 1 のコミットメント(「我々はあなたのデータで訓練しない」)は何の意味もありません。

**Flow-down テスト。** 各フラグされたスタックされたベンダー条項 — 特にデータの学習利用、データ保持、副処理者変更、責任 — について、「上流規約を確認」を単にフラグするだけではなく、チェックを実行します:

1. **契約で flow-down 言語を検索。** 探します: 「subprocessor obligations no less protective than」、「flow-down of data commitments」、「back-to-back terms」、「Provider shall ensure that its subprocessors are bound by」、「equivalent obligations」。
2. **存在する場合:** 引用し、特定のフラグされた条項をカバーすることを検証し、強制可能か(誰が強制できるか — あなたか、中間ベンダーのみか?)をフラグ。
3. **存在しない場合:** それを要求する特定のレッドラインを生成:
   > "Add to §[X]: Provider shall ensure that any third-party model providers, infrastructure providers, or subprocessors used in delivering the Services are bound by obligations with respect to [Customer Data / AI training / data retention / confidentiality] no less protective than those set forth in this Agreement, and shall be responsible for any breach of this Agreement caused by such third parties."
4. **ギャップを重要度でフラグ:** 条項がデータの学習利用または責任で flow-down がない場合は 🔴; 条項がより敏感でないか、部分的な flow-down がある場合は 🟡。

「上流をエスカレートしてチェック」はコンプライアンスが死ぬ場所。テストとレッドラインを生成してください。

**許容利用ポリシーはフレームを反転させる。**
AUP はあなたが何をできないかを伝えます; ベンダーが何をできるかは伝えません。
クリーンな AUP レビューをデータ使用と責任条項の読み取りの代替にしないこと。

**更新はレバレッジポイント。**
現在の契約が不利でベンダーが mid-term で再交渉しない場合、今ギャップを文書化し、更新のためにフラグします。調達にフラグ:
「この更新は AI addendum で [list] に対処することなしにクローズすべきではない。」

**Builder コンテキストは層を追加。**
会社がベンダーのモデルをファウンデーションとして使用する builder の場合、ベンダーの規約は、会社が自身の顧客に提供できるものも支配します。一部の規約は特定の下流使用を禁止します。現在の内部ワークフローだけでなく、製品ロードマップに対して利用制限をチェックします。

---

## Close with the next-steps decision tree

CLAUDE.md `## Outputs` のデシジョンツリーで終わります。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X, escalate, get more facts, watch and wait, something else)は出発点であり、ロックインではありません。ツリーが出力です; 弁護士が選びます。

## このスキルがしないこと

- 同じ契約の DPA 条項をレビューしないこと — プラグインがインストールされていれば `/privacy-legal:dpa-review` を実行。
- Fallback 外の規約を受諾するかを決定しないこと。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` のエスカレーション表ごとにそれらをルーティングします。
- 契約にあるもの以上にベンダーセキュリティスタンスを評価しないこと — それはセキュリティチームの機能です。
