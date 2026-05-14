---
name: use-case-triage
description: >
  Classify a proposed AI use case against your registry — approved, conditional,
  or not approved — and produce required conditions and next steps. Flags
  cross-plugin handoffs to privacy or product counsel. Use when user says "triage
  this use case", "can we use AI for X", "is this approved", "what do we need to
  do to use AI for X".
  提案された AI ユースケースを台帳に照らして分類 — approved、conditional、または
  not approved — し、必要な条件と次のステップを生成します。プライバシーまたは
  プロダクトカウンセルへのクロスプラグインハンドオフをフラグします。
  日本語トリガー: このユースケースをトリアージ、X に AI を使えるか、これは承認されているか、
  X に AI を使うために何をする必要があるか。
argument-hint: "[describe the use case, or 'batch' to triage a list]"
---

# /use-case-triage

1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます。台帳がポピュレートされていることを確認 — そうでない場合は停止してセットアップに誘導します。
2. 下記のフレームワークを使用。ユースケースが曖昧な場合は明確化します。
3. 台帳ルックアップ → レッドラインチェック → 分類。
4. 出力: 分類、推論、条件表(conditional の場合)、ガバナンス階層、クロスプラグインハンドオフ。
5. ユースケースがまだ台帳になかった場合、台帳更新を提案。

```
/ai-governance-legal-ja:use-case-triage "営業チームがリードを AI で自動スコアリングしたい"
```

---

## Matter context (案件コンテキスト)

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` を確認します。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、この段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は不可視です。有効でアクティブな案件がない場合、尋ねます: 「どの案件? `/ai-governance-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください。」アクティブ案件の `matter.md` を案件固有のコンテキストとオーバーライドのためにロードします。出力は `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/<matter-slug>/` の案件フォルダーに書き込みます。`Cross-matter context` が `on` でない限り、別の案件のファイルを決して読まないこと。

---

## 目的

「ちょっとこれに AI を使えますか?」と廊下で始まる会話を止めます。台帳から速く、キャリブレートされた答えを与えます — そして答えが conditional の場合、条件を具体的にし、次のステップを明らかにします。

トリアージスキルはゲートウェイであり、目的地ではありません。その仕事は分類し、何が必要かをフラグし、ルーティングすることです。aia-generation スキルが深い作業を行います。

## 最初に `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読む

トリアージ前に、常に `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます。そこのユースケース台帳とレッドラインが authoritative です。汎用 AI ethics 推論は、この会社が実際に決定したものの代替ではありません。

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` が `[PLACEHOLDER]` を含む場合、このバウンスを表面化:

> プラクティスプロファイルをまだ設定していないことに気づきました — それがユースケース台帳、レッドライン、ガバナンス階層をあなたのプラクティスに合わせて調整する方法です。
>
> **2 つの選択肢:**
> - `/ai-governance-legal-ja:cold-start-interview`(2 分)を実行してプロファイルを設定し、その後あなたのプラクティスに合わせてトリアージします。
> - **"provisional"** と言うと、汎用デフォルト — US jurisdiction、中程度のリスク許容度、lawyer role、playbook なし — に対してトリアージし、すべての出力に `[PROVISIONAL — configure your profile for tailored output]` をタグ付けして、コミットする前に何をするかを確認できるようにします。

### Provisional モード

ユーザーが "provisional" と言った場合、これらの汎用デフォルトを使用してトリアージを通常通り実行します: 中程度のリスク許容度、lawyer role、US jurisdiction、台帳なし(登録されたエントリへのマッチではなく、一般的な AI ガバナンス原則で分類)。レビューアー注記とすべての finding ブロックに `[PROVISIONAL]` をタグ付け。出力の最後に追加:

> 「これはデフォルト仮定に対する汎用実行でした。`/ai-governance-legal-ja:cold-start-interview` を実行してあなたのプラクティス — 台帳、jurisdiction、リスク許容度 — に合わせた出力を得てください。2 分。」

**Jurisdictional scope。** トリアージは、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の規制フットプリント用に設定された台帳、レッドライン、ガバナンス階層を適用します。AI ルールは法域によって実質的に異なります — 1 つのフットプリントで APPROVED の分類は、別のものでは CONDITIONAL または禁止されている可能性があります。デプロイがフットプリントにない法域に触れる場合、それを表面化し、類推で拡張する代わりに再トリアージします。

---

## トリアージプロセス

### Step 1: ユースケースを理解

分類前に、実際に何が提案されているかを理解していることを確認します。記述が曖昧な場合は尋ねます:

- 「AI は正確に何をしているか — コンテンツ生成、決定、推奨の表面化、タスクの自動化?」
- 「AI は誰または何に作用しているか — 従業員、顧客、サードパーティ、内部データのみ?」
- 「何かが起きる前に人間が AI 出力をレビューしているか、または自動化されているか?」
- 「どのベンダーまたはツールが提案されているか?」
- 「これは内部のみか、または顧客または他の外部関係者に触れるか?」

「[曖昧なもの] に AI を使いたい」を未トリアージのまま通さないこと。正確に分類できる程度に具体的になります。

---

### Step 2: 台帳ルックアップ

`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` のユースケース台帳で直接マッチまたは近いマッチを確認します。

**Direct match:** 台帳に直接一致するエントリがある場合、それを適用します。

**Near match:** ユースケースが台帳エントリに似ているが同一でない場合、これをフラグ: 「これは [registered use case] のように見えます — その分類を適用しますが、スコープが実質的に異なる場合は独自の評価が必要かもしれません。」

**No match:** ユースケースが台帳にない場合、AI 影響評価が完了するまでデフォルトで CONDITIONAL に。リスクの予備的読み取りを表面化し、AIA にルーティング。

> 「このユースケースはまだ台帳にありません。AI 影響評価が完了するまでデフォルトで CONDITIONAL にしました。リスクの予備的読み取りはこちら: [preliminary read]。次のステップ: 影響評価を実行、分類が確定したら台帳にユースケースを追加します。」

---

### Source attribution (トリアージが規制を引用するときに適用)

トリアージは通常ハイレベルですが、分類が規制、法令、規則、ディレクティブ、標準、またはガイダンスの引用に依存する場合 — 引用をタグ付け。トリアージの推論、レッドライン説明、または条件リストにタグ無しの規制引用を出力しないこと。「Art. 22(1)」とタグ無しで述べるトリアージは、捏造された pinpoint が読者を通り過ぎる場所です。

**Source attribution tiering。** モデル知識引用については、3 つの階層のいずれかを使用:

- `[settled]` — 安定し、変わっていない可能性が高い、よく知られた法令・規制参照(例: 概念としての GDPR Art. 22、EU AI Act としての Regulation (EU) 2024/1689 の存在)。証明前に検証するが、優先度は低い。
- `[verify]` — 実在するが検証すべきモデル知識引用: 特定の delegated / implementing acts、規制者ガイダンス、標準、施行日、閾値、2023 年以降の改正。
- `[verify-pinpoint]` — pinpoint 引用(特定の条項番号、annex 参照、サブセクションレター、パラグラフ番号)は最高の捏造リスクを抱え、常に一次ソースに対して検証すべきです。特に EU AI Act の条項番号は consolidation 中にシフトしました。Act への pinpoint 引用は、Official Journal テキストに対してすべて検証されるべきです。

他のソースは独自のタグを保持: プラクティスプロファイルのユースケース台帳から引かれた場合は `[registry]`; 接続された法務リサーチツールから取得された場合は `[Westlaw]`, `[EUR-Lex]`, `[regulator site]`, または MCP ツール名; web 検索引用には `[web search — verify]`; ユーザー提供引用には `[user provided]`。ティアリングは実際の検証作業を表面化します — すべてを検証する読者は何も検証しません。タグをストリップしたり折り畳んだりしないこと。

**非弁護士ユーザーの場合、不確実な日付と閾値はインラインではなく confirm-list に。** 「effective February 1, 2026」に `[verify]` タグが付いていると、タグの意味を知らない人には「effective February 1, 2026」と読まれます。`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Who's using this` を読みます。Role が **Non-lawyer** で、施行日、段階的施行、閾値、または期限が不確実(インラインなら `[verify]` または `[verify-pinpoint]` を伴うもの)である場合、インラインアサーションを「effective date: confirm with counsel」(または「threshold: confirm with counsel」)に置き換え、すべての不確実なアサーションを最後のトリアージセクションに集めます: 「**Things I'm not certain about — ask your attorney to confirm before relying on this:**」、各項目(私が言ったこと、何が不確実か、なぜ重要か)をリスト。弁護士ロールユーザーはインライン `[verify]` の扱いを保持します。

---

### Step 3: レッドラインチェック

さらに進む前に、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` のレッドラインをチェックします。

ユースケースがレッドラインをトリガーする場合 — 部分的にでも、慈善的な読み方でも — 直ちにそう述べます。

> 「このユースケースは [red line] に触れます。あなたのレッドラインはこれを自動的に no として扱います。この状況について何か違うことがある場合、それはリーガルサインオフのための会話であり、トリアージコールではありません。」

レッドラインの結果を柔らかくしないこと。no なら no です。

---

**Jurisdictional scope。** 尋ねます: 「誰が影響を受け、彼らはどこにいるか?(従業員 / 顧客 / 一般公衆 / 特定のグループ。) どの jurisdiction?(あなたの会社の場所だけでなく — 影響を受ける人々がいる場所。)」

その後、プラクティスプロファイルの `## Regulatory footprint` の **すべての** レジームに対してユースケースをチェックします — 主要なものだけではありません。衝突をフラグ:
- 「US 法の下では APPROVED ですが、EU 居住者が影響を受ける場合は EU AI Act Article 27 FRIA をトリガー — 影響を受ける個人が EU にいるかを確認。」
- 「ガバナンスフレームワーク下では標準階層ですが、NYC 居住者に影響する採用決定に使用される場合 NYC LL144 はバイアス監査を要求。」
- 「Australian AI Ethics Framework の下では低リスクですが、Colorado 居住者が影響を受ける場合は Colorado AI Act の下で高リスクの可能性。」

法域をまたぐユースケースは、最も便利な処理ではなく、最も厳しい適用可能な処理を受けます。

---

### Step 4: 分類と出力

APPROVED / CONDITIONAL / NOT APPROVED バケット、red-line 定義、CONDITIONAL の必要なコントロールリストはすべて、`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → `## AI use case triage criteria` と `## Use case registry` から来ます。プレイブックがユースケースが依存する基準を定義していない場合、ユーザーに尋ねます: 「あなたのプレイブックは [specific question] をカバーしていません。デフォルトポジションは何ですか? `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` に追加して、次のトリアージが一貫するようにします。」

**APPROVED 分類を発行する前に(AI ユースケースをデプロイのために承認):** `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Who's using this` を読みます。Role が Non-lawyer の場合:

> このユースケースをデプロイのために承認することは法的結果を持ちます。弁護士とレビューしましたか? Yes であれば続行。No であれば、彼らに持っていくブリーフはこちらです:
>
> [1 ページの要約を生成: ユースケースとそのスコープ、台帳にどのようにマップするか、どのポリシーまたはレッドラインに触れるか、デプロイで何が悪くなる可能性があるか、green-light の前に弁護士に尋ねるべきこと。]
>
> 弁護士、ソリシター、バリスター、またはあなたの法域のその他の認可された法律専門家を見つける必要がある場合: あなたの専門規制者の照会サービスが最速の出発点です(米国では state bar、英国では SRA/Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアでは Law Society、またはあなたの法域の相当物)。

このゲートを明示的な yes なしに通過しないこと。CONDITIONAL 出力はゲートを必要としません。

**提案されたユースケースを打ち切る NOT APPROVED 分類を発行する前に:** `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` の `## Who's using this` を読みます。Role が Non-lawyer の場合、対称的なゲートが適用されます — ユースケースを誤って拒否することも重大なエラーで、トリアージコールに関係なくビジネスが押し返します:

> これはビジネスの依頼に対するフルストップです。弁護士とレビューしましたか? Yes であれば続行。No であれば、彼らに持っていくブリーフはこちらです:
>
> [1 ページの要約を生成: ユースケースとそのスコープ、それをブロックする特定のレッドラインまたは台帳エントリ、(あれば)elevated tier をクリアできる狭いバージョンはどのようなものか、ビジネスが弁護士に求める可能性が高いもの、no を受け入れる前に弁護士に尋ねる 3 つの質問。]
>
> 弁護士、ソリシター、バリスター、またはあなたの法域のその他の認可された法律専門家を見つける必要がある場合: あなたの専門規制者の照会サービスが最速の出発点です(米国では state bar、英国では SRA/Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアでは Law Society、またはあなたの法域の相当物)。

このゲートを明示的な yes なしに通過しないこと。非弁護士が弁護士なしに AI プラグインを代表して hard no を発行することは、非弁護士が hard yes を発行することのミラー失敗です。

**各トリアージ出力のフォーマット:**

---

[WORK-PRODUCT HEADER — plugin config ## Outputs ごと — ロールにより異なる; `## Who's using this` を参照]

**USE CASE:** [理解したとおりにユースケースを述べる]

**CLASSIFICATION:** [APPROVED / CONDITIONAL / NOT APPROVED]

**Registry match:** [Direct match / Near match — [name] / No match]

**Reasoning:**
[なぜこの分類かについて 1〜3 文。承認の場合、何が安全にするか。conditional の場合、条件が管理するリスクを作るものは何か。承認されない場合、どのレッドラインまたはポリシーポジションが適用されるか。]

**Red lines triggered:** [None / 適用されるものをリスト]

---

*CONDITIONAL の場合 — 進行前に必須:*

| Requirement | Owner | Done? |
|---|---|---|
| [e.g., AI impact assessment] | [AI governance counsel] | ☐ |
| [e.g., Privacy review / PIA] | [Privacy counsel] | ☐ |
| [e.g., Human-in-the-loop requirement — no automated decisions] | [Product] | ☐ |
| [e.g., Disclosure to affected parties] | [Product / Legal] | ☐ |
| [e.g., Specific vendor only — [approved vendor name]] | [Procurement] | ☐ |
| [e.g., Legal sign-off] | [GC] | ☐ |

**Governance tier:** [Standard / Elevated / High — `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` ごと]

**Approval path:** [階層ごとに誰がサインオフする必要があるか]

**Next step — 続行を提供:**

CONDITIONAL 結果を提示した後、常に終わります:

> 「今影響評価を始めますか? インテーク質問を実行し、別のコマンドを実行する必要なく評価ドキュメントを生成できます。」

yes と言った場合、`aia-generation` スキルをロードし、同じ会話で続行 — 再開する必要なし。既に決定されたユースケース記述とガバナンス階層を渡します。

no と言った場合(または応答しない場合)、トリアージ結果はスタンドアロン出力として立ちます。
AIA はいつでも次で実行できます:
`/ai-governance-legal-ja:aia-generation [use case]`

---

*NOT APPROVED の場合:*

**Reason:** [特定のレッドライン、ポリシー禁止、または台帳エントリ]

**もし動くバージョンがあれば:** [オプション — 「すべての adverse 決定に人間をループ内に保つ狭いバージョンは elevated tier をクリアできる可能性。それは次を必要とする…」] 本当に真実な場合にのみ含める。すべての no に回避策を提供しないこと。

---

### Step 5: クロスプラグインハンドオフ

**プライバシーハンドオフ:** ユースケースが個人データを含む場合 — 従業員データ、顧客データ、行動データ — フラグ:

> 「このユースケースは個人データを含みます。AI 影響評価に加えて PIA がおそらく必要です。プラグインがインストールされていれば `/privacy-legal:pia-generation [use case]` を使用して並行して実行してください。」

**プロダクトカウンセルハンドオフ:** AI を含む新製品機能の場合:

> 「このユースケースが製品ローンチの一部である場合、プロダクトカウンセルをループインしてください。プラグインがインストールされていれば `/product-legal:launch-review` を使用 — AI コンポーネントを検出してこのプラグインにルーティングします。」

実際に関連するハンドオフのみをフラグします。すべてのトリアージに両方を boilerplate として追加しないこと。

---

### Step 6: 台帳更新の提案

このトリアージが、台帳にまだない分類をもたらした場合 — no-match または near-match が gap を明らかにした:

> 「これをユースケース台帳に追加することをお勧めします。提案されたエントリ:」

```
| [Use case description] | [Approved/Conditional/Never] | [Conditions if any] | [Reason if Never] |
```

> 「`~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` → ユースケース台帳に追加。これは次回同じリクエストが出たとき、答えが文書化されて一貫していることを意味します。」

---

## バッチトリアージ

ユーザーが複数のユースケースを一度に提示する場合 — リスト、バックログ、製品ロードマップ — それぞれを実行し、最初にサマリー表を出力し、その後 conditional または not-approved エントリを展開します:

| # | Use case | Classification | Key condition / blocker |
|---|---|---|---|
| 1 | [use case] | 🟢 Approved | — |
| 2 | [use case] | 🟡 Conditional | 影響評価必須 |
| 3 | [use case] | 🔴 Not approved | 自動的な adverse 決定 — レッドライン |

その後、クリーンな承認ではない各行を展開します。

---

## エッジケースと失敗モード

**「我々は既にこれを行っている」トリアージ:**
誰かが retroactive トリアージを求めている場合 — ユースケースが既にデプロイされている — 平易にそう述べ、ゼロから分類する前に、デプロイされたバージョンをカバーする既存のエントリを台帳で検索します。Retroactive トリアージはしばしば、現在の実務から条件がドリフトした古い台帳エントリを表面化させます。新しい行を追加するのではなく、そのエントリを更新するのが通常正しいフォローアップです。
> 「これは retroactive トリアージのようです。これが評価なしで既に実行されている場合、それは waive するのではなく文書化すべきギャップです。新たにトリアージを実行する前に、このデプロイをカバーする既存のエントリを台帳で検索しています。分類はこちら: [トリアージを通常通り実行]。Conditional の場合、それらの条件は仮定するのではなく、今 inplace されていることを確認すべきです。台帳に既存のエントリがあり、デプロイされたバージョンがドリフトしている場合、正しいフォローアップは新しいエントリを追加するのではなく、そのエントリを更新することです。」

**「内部だけ」は分析を変えない:**
従業員(スクリーニング、モニタリング、評価)に影響する内部 AI 利用は、しばしば顧客向け AI よりも高リスクです。ユーザーが内部スコープがリスクを減らすと示唆する場合、これをフラグします。

**「ベンダーが安全と言っている」:**
ベンダーの表明は独自の影響評価の代替にはなりません。フラグ:
> 「ベンダーのポジションは独自の評価の代替にはなりません — 特に elevated または high tier のものに対して。」

**「我々はパイロットしているだけ」:**
実際の従業員または顧客データに触れるパイロットは、トリアージまたは影響評価から免除されません。同じ分類を適用; 条件が影響評価を含む場合、パイロットもそれを持つべきです。

## Close with the next-steps decision tree

CLAUDE.md `## Outputs` のデシジョンツリーで終わります。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X, escalate, get more facts, watch and wait, something else)は出発点であり、ロックインではありません。ツリーが出力です; 弁護士が選びます。
