---
name: policy-starter
description: >
  Draft a firm AI usage policy from published model policies, adapted to your
  practice profile — a research-and-synthesis tool whose output is a draft for
  attorney review and adoption, not a finished policy. Use when user says "draft
  an AI policy", "we need an AI policy", "build an AI usage policy", "our firm
  needs a GenAI policy", or similar requests to generate a first-cut internal
  AI policy.
  公開モデルポリシーから、プラクティスプロファイルに合わせて適応されたファームの AI
  利用ポリシーを起草します — 弁護士のレビューと採用のためのドラフトであり、完成版ポリシー
  ではないリサーチ・シンセシスツール。
  日本語トリガー: AI ポリシーを起草、AI ポリシーが必要、AI 利用ポリシーを構築、
  ファームに GenAI ポリシーが必要、社内 AI ポリシーの初版ドラフト作成。
argument-hint: "[optional — scope hint, e.g. 'firm-wide', 'legal team only', 'update existing']"
---

# /policy-starter

1. `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読みます。プラクティスプロファイルが未ポピュレートの場合、停止して `/ai-governance-legal-ja:cold-start-interview` に誘導します。
2. 下記のフレームワークを使用。
3. スコープインタビューを実行 — ポリシーがカバーする必要があるセクション、対象オーディエンス、デプロイコンテキスト。ドラフトに飛ばないこと。
4. デプロイコンテキストに関連する現在の公開モデルポリシーとガイダンスを web 検索(ABA、州弁護士会、ILTA、CLOC、NIST、同業他社ポリシー、現行州 AI 法、EU AI Act、適用されるセクター規制者)。
5. 選択されたセクションを、モデルポリシーから出典を取り、各選択ポイントに `[review]` フラグを付け、各セクションの下部に `[review]` のオープン質問を付けてドラフトします。
6. ドラフトヘッダー(「DRAFT FOR INTERNAL LEGAL REVIEW — NOT FOR DISTRIBUTION」)、ソースブロック、レビューアー注記、採用チェックリスト付きで出力。
7. Next-steps デシジョンツリーで閉じる。

```
/ai-governance-legal-ja:policy-starter
/ai-governance-legal-ja:policy-starter "30 人の弁護士事務所のための AI ポリシーが必要"
/ai-governance-legal-ja:policy-starter "2026 年州 AI 法のために既存ポリシーを更新"
```

---

## Matter context (案件コンテキスト)

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` を確認します。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、この段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は不可視です。有効でアクティブな案件がない場合、尋ねます: 「どの案件? `/ai-governance-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください。」アクティブ案件の `matter.md` を案件固有のコンテキストとオーバーライドのためにロードします。出力は `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/matters/<matter-slug>/` の案件フォルダーに書き込みます。`Cross-matter context` が `on` でない限り、別の案件のファイルを決して読まないこと。

---

## 目的

多くのファームとインハウスチームには、まだ書かれた AI 利用ポリシーがないか、州 AI 法、EU AI Act 実施法令、2025 年 COPPA 改正、または実際に Copilot や Claude for Work で行うことになったものに言及しない 2024 年ヴィンテージのもので動いています。このスキルは、意思決定者(GC、マネージング・パートナー、エグゼクティブ・コミッティ、取締役会、IT 責任者、HR 責任者)に持っていく **ドラフト** ポリシーを生成 — 配布する完成版ポリシーではありません。

このスキルの規律:

1. **発明ではなく、公開モデルポリシーから出典を取る。** ABA AI Toolkit、州弁護士会ガイダンス、ILTA のモデルポリシー、CLOC のテンプレート、および公開されている同業他社ポリシーを検索して読みます。各ソースが何を述べるかを引用し、それを適応させます — 何もないところからポリシー言語を生成しないこと。
2. **ドラフト前にスコープをデシジョンツリー化。** すべてをカバーしようとするポリシーは何もカバーしません。ポリシーが必要なセクションをユーザーに尋ねます。彼らに選ばせます。その後、各選択されたセクションを各選択ポイントに `[review]` フラグ付きで構築します。
3. **すべての判断コールをフラグ。** 出力は弁護士がレビューして採用するドラフト; すべての閾値、すべての名前付きツール、すべての開示トリガー、すべての施行結果は `[review]` 行。
4. **ヘッダーがオーディエンスのスコープをシグナル。** この出力はリーガルを超えて読まれる可能性があります — HR、IT、全スタッフによって。ヘッダーはそれに応じて適応されます。

このスキルは、ハードコールに対する特定のポジションを完成させたり、配布したり、公開したり、推奨することはしません。ドラフトを生成し、選択肢を表面化します。

## 最初に `~/.claude/plugins/config/claude-for-legal-ja/ai-governance-legal-ja/CLAUDE.md` を読む

ドラフト前に、常にプラクティスプロファイルを読みます。ドラフトを駆動するセクション:

- `## Company profile` — AI ロール(Builder / Deployer / Both)、規制フットプリント、外部コミットメント、プラクティス設定
- `## Use case registry` — 既に承認、conditional、またはレッドラインのもの
- `## AI policy commitments` — 過去または現行ポリシーが既に言うこと
- `## Vendor AI governance` — チームが既にベンダーに要求するもの
- `## Governance team and escalation` — 誰が承認、誰がエスカレート
- `## Who's using this` — ロール(lawyer / non-lawyer)がヘッダーと「これを採用」のフレーミングを支配

`## AI policy commitments` がポピュレートされている場合、これは UPDATE であり新ドラフトではありません — 既存ポリシーをベースとして扱い、変更を提案します。空の場合、これは first-cut ドラフトです。

## スコープインタビュー(ドラフト前に行う)

ユーザーにポリシーがカバーすべきセクションを尋ねます。チェックリストとして提示 — ユーザーが選び、あなたが構築。事前決定しないこと。

> **AI ポリシーは何をカバーすべきか? ドラフトに含めたいセクションを選んでください:**
> 1. **Scope(範囲)** — ポリシーが誰に適用されるか(全スタッフ、特定のロール、契約者)、どのツールをカバーするか(GenAI のみ、すべての AI、特定のベンダー)、どのデータが範囲内/外か。
> 2. **Permitted and prohibited uses(許可された使用と禁止された使用)** — 承認されたカテゴリ、レッドライン、「最初に尋ねる」ケース。
> 3. **Approval and review(承認とレビュー)** — 誰が新しいツールを承認、誰が新しいユースケースを承認、レビューリクエストはどのように提出、SLA は何か。
> 4. **Disclosure(開示)** — クライアントへ(ファーム)、裁判所へ、相手方へ、従業員へ、AI 機能のエンドユーザーへ。
> 5. **Data handling(データ取り扱い)** — 何の機密/クライアント/特権データをどこに行けるか、データレジデンシー、ベンダー保持条項、データの学習利用スタンス。
> 6. **Training and certification(訓練と認証)** — 誰がトレーニングを受けなければならないか、どのケイデンスで、未完了の結果は何か。
> 7. **Incidents and reporting(インシデントと報告)** — AI インシデントとは何か、どう報告、誰が処理。
> 8. **Enforcement(施行)** — ポリシーが違反されたとき何が起きるか、懲戒フレームワークへのリンク。
> 9. **Review cadence and ownership(レビューケイデンスと所有権)** — ポリシーがどのくらいの頻度で更新、誰が更新を所有、変更はどのように伝達。
> 10. **Glossary(用語集)** — 定義された用語(GenAI、承認済みツール、高リスク使用、重大な決定、機密データなど)。
>
> ポリシーを持ったことがないファーム / インハウス・リーガルチームのためのデフォルト・スターターパック: 1, 2, 3, 4, 5, 9。v1 では残りはスキップ。

ユーザーが選択した後、2 番目の質問を尋ねます:

> **ドラフト前にあと 2 つの入力:**
> - **Audience** — これは誰が読むか?(全スタッフ / リーガルチームのみ / 弁護士 + スタッフ / クライアント向けバージョンも必要)これはトーンと用語集を駆動します。
> - **Deployment context** — (a) 法律事務所、(b) 会社のインハウス・リーガル(ポリシーはリーガルまたは会社全体をカバー?)、(c) リーガルエイド / クリニック、(d) 政府。これは私が検索するモデルポリシーを駆動します。

## モデルポリシーを出典として取る

ドラフト前に、最新の公開された AI モデルポリシーとガイダンスを web 検索します。

**モデルポリシーソースをプラクティスプロファイルの `## Regulatory footprint` から導出します。** グローバルユーザーに対して US ソースをハードコードしないこと。

| Jurisdiction | Model policy sources |
|---|---|
| US | ABA Formal Opinion 512、州弁護士会ガイダンス(CA、FL、NY、TX はすべて公開された AI ガイダンスを持つ)、ILTA モデルポリシー、CLOC テンプレート、公開された同業他社ファーム AI ポリシー |
| UK | Solicitors Regulation Authority リスクアウトルック、Law Society AI 原則、ICO AI ガイダンス、Bar Council ガイダンス |
| EU | EU AI Act コンプライアンスフレームワーク(Article 4 AI リテラシー、Article 17 品質管理)、各国 DPA AI ガイダンス(CNIL, DSB, Garante, AEPD)、EDPB ガイドライン、EU 機関の AI ポリシー |
| Australia | Law Council of Australia AI ガイドライン、OAIC AI ガイダンス、州 law society ガイダンス、Australian AI Ethics Framework |
| Singapore | PDPC Model AI Governance Framework、MinLaw ガイダンス、MAS AI fairness principles(金融サービス向け) |
| Canada | Law Society of Ontario/BC/Alberta AI ガイダンス、OPC AI ガイダンス、TBS Directive on Automated Decision-Making |
| Multi-jurisdiction | 適用されるすべてを使用し、どこで分岐するかを注記する(例: EU は US が必要としない人間監督文書化を要求する; Australia は voluntary ethics frameworks に焦点を当てる; Singapore はセクター規制に焦点を当てる) |

プラクティスプロファイルのフットプリントが空または `[PLACEHOLDER]` の場合、尋ねます: 「あなたの組織はどの jurisdiction で運営していますか? US 中心のテンプレートではなく、あなたの規制環境と職業責任フレームワークに一致するモデルポリシーからドラフトします。」

ドラフトが使用する各ソースについて、出力の上部の「Sources」ブロックに次を **記録**: 名前、URL、アクセス日、ドラフトが何を取り入れたか。

web 検索が実行できない場合、レビューアー注記に注記: 「Web 検索を実行できませんでした — ドラフトは訓練知識のみから出典を取りました。採用前に引用されたソースの現行バージョンに対して検証してください。」検証ログが適用されます。

## ドラフト

出力は一貫した構造に従います。**すべての選択ポイントは `[review]` フラグを得ます。** ユーザーが決定しなければなりません; スキルが選択肢を提示します。

### ヘッダー

```
DRAFT FOR INTERNAL LEGAL REVIEW — NOT FOR DISTRIBUTION
Prepared for: [プラクティスプロファイルからのファーム / 会社名]
Date: [today's date]
Prepared by: ai-governance-legal-ja policy-starter skill, 公開モデルポリシーから適応
プラクティスプロファイルのガバナンスチームセクションごとに [attorney / GC / managing partner / executive committee] がレビュー、適応、承認するまで、採用、配布、投稿、または依拠不可。
```

`## Who's using this` のロールが Non-lawyer の場合: ヘッダーの下に 2 行目を追加 — 「あなたの jurisdiction で認可された弁護士、ソリシター、バリスター、またはその他の認可された法律専門家でない場合、これのいずれかを使用する前に、このドラフトを弁護士コンタクト([プラクティスプロファイルからの名前])に持って行ってください。これは彼らのレビューのための出発点ドラフトであり、採用できるポリシーではありません。」

### Sources ブロック(ヘッダー下、上部)

ドラフトが引き出したモデルポリシー / ガイダンス / 規制の表:

| Source | URL | Accessed | What the draft took from it |
|---|---|---|---|
| ABA Formal Op. 512 | [url] | [date] | 開示と能力のフレーミング |
| ILTA Model AI Policy v.[X] | [url] | [date] | 承認ワークフロー、データ取り扱い |
| [State] Bar Op. [X] | [url] | [date] | クライアントへの開示 |
| [peer firm] published AI policy | [url] | [date] | スコープ言語 |
| Colorado SB 24-205 | [url] | [date] | 高リスク AI 定義 |
| EU AI Act, Art. [X] | [url] | [date] | ベンダー flow-down |

### Executive summary

最大 3 段落。ポリシーが何をするか、誰を拘束するか、ポリシーが効力を持つ前に読者が何をしなければならないか。

### セクション

ユーザーが選んだセクションのみ、上記の順序で。各々について:

- **見出しと範囲** 文。
- 引用されたモデルポリシーから適応された **実質的なルール**。すべての特定の閾値、数字、名前付きツール、名前付きベンダー、エスカレーション連絡先は `[review]`。例: 「機密クライアントデータは [汎用消費者 AI ツール] `[review — ツールをリスト、または approved-tools リストを参照]` に入力してはいけません。そのようなデータを [承認済みファーム・ライセンス・ツール] `[review — ツールをリスト]` で使用することは、データ取り扱いセクションに従って許可されます。」
- 特定のソースから適応されたルールがある場所にインラインで **ソース属性**。例: 「弁護士は、クライアントの代理で使用する前に、すべての AI 生成作業成果物の精度を検証しなければなりません `[ABA Formal Op. 512]`。」
- 各セクションの下部に **オープン質問** — セクションが準備完了になる前に弁護士が行う必要がある 2〜3 の決定。これらはインライン `[review]` フラグとは異なります — これらは「ここにはまだポジションがない」項目で、「詳細を埋める」項目ではありません。

### 採用チェックリスト

ドラフトの最後に、ポリシーが採用される前に起こる必要があることのチェックリスト。これらを発明しないこと — プラクティスプロファイルのガバナンスチームとエスカレーションセクションから引き出します。典型的な項目:

- [ ] GC / マネージング・パートナーによるレビュー `[review — 名前]`
- [ ] IT / セキュリティによるレビュー `[review — 名前]`
- [ ] HR によるレビュー(施行 / 訓練セクション用) `[review — 名前]`
- [ ] 取締役会 / エグゼクティブ・コミッティの承認(必要な場合) `[review — 必要かどうかを確認]`
- [ ] 訓練資料のドラフト
- [ ] 発表のドラフト
- [ ] 施行日の設定 `[review]`
- [ ] レビューケイデンスのカレンダー化 `[review — 年次が典型]`
- [ ] 採用されたら、プラクティスプロファイルの `## AI policy commitments` セクションにポリシーを追加

### Reviewer note (レビューアー注記)

プラクティスプロファイルの `## Outputs` セクションごとに、ヘッダー上の標準的なレビューアー注記。ブロックフォーマットを使用:

> **⚠️ Reviewer note**
> - **Sources:** web search ✓ / not connected — cites from training knowledge
> - **Read:** practice profile · [N] published model policies
> - **Flagged for your judgment:** [N] `[review]` items inline · [N] open questions per section
> - **Currency:** searched for developments since [date]
> - **Before relying:** this is a DRAFT — bring to [プラクティスプロファイルからの承認者], don't distribute until adopted

## してはいけないこと

- **ポリシー言語を発明しないこと。** ドラフトのすべての実質的ルールは、引用されたソースに traceable であるか、`[review — adapted, no direct source]` でフラグされる必要があります。
- **弁護士のためにハードコールを選ばないこと。** 「パラリーガルが first-draft 作業に AI を使用することを許可すべきか?」は `[review]` であり、推奨ポジションではありません。
- **完成版に見えるポリシーを生成しないこと。** ヘッダー、レビューアー注記、ドラフト全体の `[review]` フラグが、これがドラフトであることのシグナル。それらを柔らかくしないこと。
- **スコープインタビューをスキップしないこと。** ユーザーが「ただフルポリシーをドラフトして」と言う場合、押し返します: 「すべてをカバーしようとするポリシーは何もカバーしません。どのセクションを希望しますか? チェックリストはこちら。」交渉 1 ラウンドは OK — 2 ラウンドも OK。スコープなしのドラフトが失敗モード。
- **ユーザーが依頼しなかったセクション・コンテンツを生成しないこと。** 1, 2, 3, 4, 5, 9 を選んだ場合、それらを実行します。「実際のポリシーには訓練が必要」だからセクション 6 を追加しないこと。
- **特定のベンダー、ツール、または結果を推奨しないこと。** それらを、ユーザーのポジションがどうあるべきかではなく、典型的な決定がどのようなものかのコンテキスト付きで `[review]` フラグします。
- **法的十分性を約束しないこと。** ドラフトは弁護士レビューのための出発点であり、テスト済みポリシーではありません。

## ハンドオフ

ドラフトが生成された後、プラクティスプロファイルからのデシジョンツリーで閉じます。最も一般的な次のステップ:

1. **ドラフトを調整** — ユーザーが `[review]` フラグをウォークスルーし、弁護士と解決します; スキルは決定が組み込まれて再実行します。
2. **ステークホルダーサマリー** — ポリシーが何をするかしないかを説明する取締役会またはエグゼクティブ・コミッティ向けの 1 ページバージョンを生成。
3. **訓練資料** — ポリシーが採用されたら、`/ai-governance-legal-ja:aia-generation` を使用してユースケースごとの訓練ノートを生成できます。
4. **ベンダー sweep** — ポリシーが採用されたら、ポリシーが参照するベンダーに対して `/ai-governance-legal-ja:vendor-ai-review` を実行して適合性を確認すべきです。
5. **新規制に対するギャップチェック** — 採用前に特定の規制またはガイダンスに対してドラフトをテストするために `/ai-governance-legal-ja:reg-gap-analysis` とペアにします。

## 出力スコープのリマインダー

このスキルが生成する文書は、リーガルだけでなく HR、IT、より広いビジネスに届きます。非弁護士がフォローできる程度に平易な言語を保ちます。法的精度はジャーゴンではなく、`[review]` フラグとソースにあります。
