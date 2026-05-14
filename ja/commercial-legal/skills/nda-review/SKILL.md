---
name: nda-review
description: >
  Reference: インバウンド NDA を GREEN / YELLOW / RED に高速トリアージして、
  チームが本当にレビューが必要な NDA にのみ弁護士時間を費やせるようにします。
  セールスと BD が法務に投げる前にセルフサービスで使えるように作られています。
  NDA が検出されたときに /commercial-legal-ja:review からロードされます。
  日本語トリガー例: NDA トリアージ、NDA レビュー、秘密保持契約チェック。
user-invocable: false
---

# NDA Review

## 案件コンテキスト

**案件コンテキスト。** プラクティスレベル CLAUDE.md の `## Matter workspaces` を確認。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、この段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は不可視。有効でアクティブな案件がなければ尋ねる:「これはどの案件のもの? `/commercial-legal-ja:matter-workspace switch <slug>` を実行するか `practice-level` と言ってください。」アクティブな案件の `matter.md` を案件固有のコンテキストとオーバーライドのためにロード。出力は `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/matters/<matter-slug>/` の案件フォルダに書き込む。`Cross-matter context` が `on` でない限り、他の案件のファイルを読まない。

---

## 宛先チェック

出力前に、どこに行くかを確認。ユーザーが宛先を指定した場合(チャンネル、配信リスト、相手方、「全員」)、それが秘匿特権サークル内かを尋ねる。公開チャンネル、全社配信リスト、相手方/相手方代理人、ベンダー、クライアント(work product については)は保護を放棄させる。宛先がサークル外に見える場合、フラグして提供:(a) 法務のみの秘匿特権版、(b) 広範なチャンネル用のサニタイズ版、(c) 両方 — 秘匿特権ヘッダーを黙って適用してから、それが保護しない場所への貼り付けを助けない。このプラグインの CLAUDE.md の正本 `## Shared guardrails → Destination check` を参照。

## 目的

インバウンド NDA のほとんどは問題ない。一部に地雷がある。このスキルは 1 分以内にそれらを並び替え、法務が本当に重要な NDA だけを読むようにする。

**目標:**GREEN な NDA は署名以上のものは必要ない。YELLOW は弁護士の目が 1〜2 項目に必要。RED は誰かが時間を無駄にする前に止める。

## まずプレイブックをロード

**どちらのサイドか?** プレイブックを適用する前に、この NDA について自社がどちらのサイドかを判定。通常は文脈から明らか:相手方が自社の製品を評価するベンダーまたはパートナーなら、セールスサイド。あなたが相手のものを評価するなら、パーチェシングサイド。相互 NDA でもサイドはある — 誰の書式か、評価の方向はどちらか。明らかでなければ尋ねる。設定の該当プレイブック節(`### Sales-side playbook` または `### Purchasing-side playbook`)を読む。出力にどちらのサイドが適用されたかを記載し、レビューアーがどのプレイブックが適用されたかを分かるようにする。該当サイドが `[Not configured]` なら、停止してユーザーにこのトリアージ前に `/commercial-legal-ja:cold-start-interview --side <side>` を実行するよう伝える。

**何かをトリアージする前に `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` → `## Playbook` → 該当サイド → `NDA triage positions` を読む。** その節が、*このチーム*の*このサイド*において NDA を GREEN、YELLOW、RED にする判断基準の真実の源。このスキルは NDA 条項に関するデフォルト立場と共に出荷されない — 法律、市場、各チームのリスク許容度はあまりにも変動するため、ハードコードされたデフォルトは安全ではない。

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` にまだ `NDA triage positions` 節がない、またはレビュー中の NDA で出てきた条項について沈黙している場合、ユーザーに尋ねる:

> プレイブックは [条項 — 例:"residuals clauses"、"survival period"、"one-way NDAs where you're the receiver"] をカバーしていません。デフォルト立場は? — いつ GREEN、いつ YELLOW、いつ RED にすべきですか? 次のレビューで一貫するよう `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` に追加します。

回答を `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` に記録し、新しい立場を使ってトリアージを進める。

## スコープチェック

**NDA 固有の条項をレビューする前に、文書が名前以上のことをしていないかを確認。** 相互商業 NDA は次のものを隠せる:standstill、ライセンス許諾、独占、勧誘禁止、競業禁止、IP 譲渡、優先交渉権、最恵国条項、機密性紛争をはるかに超えるものを支配する仲裁/管轄条項。

NDA が機密保持を超える義務を含む場合:**NDA 条項分析にかかわらず自動的に YELLOW。** NDA でない条項をフラグ:

> この文書は NDA とラベル付けされていますが [standstill / ライセンス許諾 / 勧誘禁止 / 独占 / IP 譲渡 / ROFR / MFN / 広範な仲裁] を含みます。NDA 以上のものです。弁護士レビューに振り分けてください。

実質的義務が業務委託契約、タームシート、または NDA の衣をまとった誓約パッケージである場合、「NDA」とラベル付けされた文書を NDA トリアージに静かに通さない。

## トリアージ

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の立場を適用して NDA を 3 つのバケットの 1 つに分類。バケット定義は安定だが、各バケットを満たす*基準*はプレイブックから来る。

### GREEN — 署名へ振り分け

NDA はチームのプレイブックの全立場を満たし、プレイブックに従って RED フラグを引き起こす条項はない。プレイブックが通常カバーするチェックの例:相互性、期間、サバイバル期間、carveouts、準拠法、制限的誓約、費用転嫁。GREEN を呼ぶ前に `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` に対して各項目を確認。

**GREEN は弁護士レビュー済みプレイブック立場を要する。** GREEN は弁護士レビューなしに署名に至る唯一の経路。デフォルトまたは不在の立場に対しては発行できない。GREEN を発行する前にチェック:プラクティスプロファイルに弁護士レビュー済みの `## NDA triage positions` 節があるか? なければ:

> あなたのプラクティスプロファイルに弁護士レビュー済みの NDA 立場がないので、GREEN を発行できません。商業カウンセルと一緒に `/commercial-legal-ja:cold-start-interview --full` を実行して立場を設定するか、この NDA を弁護士レビューに振り分けてください。デフォルトに対して GREEN を発行することは、非弁護士が次の非弁護士が依拠する立場を設定することを意味します。

デフォルトに対して署名へ振り分けない。立場が欠落しているとき YELLOW が正しい判断 — 判断できる人間に NDA を浮かび上がらせる。

**出力:**

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の `## Outputs` から作成物ヘッダーを先頭に付ける(ユーザー役割によって異なる — `## Who's using this` 参照)。

```markdown
[WORK-PRODUCT HEADER — プラグイン設定の ## Outputs に従って]

## NDA Triage: [相手方]

GREEN — 署名へ振り分け

### Executive Summary

プレイブック下で red flag は識別されませんでした。標準プロセスに従って署名へ振り分け。

| Check | Status | Playbook reference |
|---|---|---|
| [各プレイブックチェック] | [pass/fail] | [`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` 節] |

**Next step:** [[CLM] 標準 NDA ワークフローへ送信 | `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` からの承認者へ署名のため送付]
```

**GREEN から署名へ進む前に:** `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の `## Who's using this` を読む。Role が非弁護士の場合:

> このステップには法的影響があります(NDA への副署は会社を拘束します)。弁護士とレビューしましたか? Yes であれば進行。No であれば、弁護士に持ち込むためのブリーフは以下のとおり:
>
> [1 ページのサマリーを生成:相手方、NDA 方向(相互 / 一方向)、実行したプレイブックチェック、プレイブックがカバーしていなかった事項、このまま署名した場合に何が起こり得るか、弁護士に尋ねる 3 つのこと。]
>
> 弁護士、ソリシター、バリスター、または他の認定法律専門家を見つける必要があれば:あなたの専門規制機関(米国の州弁護士会、英国 SRA/Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアの Law Society、または管轄相当の機関)に連絡 — 紹介サービスがあります。

明示的な「yes」なしにこのゲートを越えて進まない。

### YELLOW — 特定項目に弁護士の目が必要

1 つ以上の条項がプレイブックから乖離するが、絶対的なディールブレーカーではない、またはプレイブックが扱わない条項が出てくる。各項目を個別に浮かび上がらせ、承認者が判断できるようにする。

**出力:**

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の `## Outputs` から作成物ヘッダーを先頭に付ける(ユーザー役割によって異なる — `## Who's using this` 参照)。

```markdown
[WORK-PRODUCT HEADER — プラグイン設定の ## Outputs に従って]

## NDA Triage: [相手方]

YELLOW — [`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の承認者名] にフラグ

### Executive Summary

- [1 行のアクション可能な編集、例:「Section 6 の勧誘禁止条項を削除」]
- [1 行のアクション可能な編集]

### フラグされた項目

**1. [論点]** — Section [X]
   What: [1 行]
   Why flagged: [1 行 — これが当たるプレイブック立場、または「プレイブックはこれについて沈黙」]
   **Legal risk:** [🔴/🟠/🟡/🟢] | **Business friction:** [🔴 Blocks deals / 🟠 Slows deals / 🟡 Confuses customers / 🟢 Invisible]
   Likely resolution: [accept / X を push back / 取引文脈による]

[各フラグについて繰り返し]

### その他

| Check | Status | Playbook reference |
|---|---|---|
| [pass したプレイブックチェック] | pass | [`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` 節] |

**Next step:** [承認者] にフラグされた項目について尋ね、彼/彼女が OK ならば署名へ振り分け。
```

### RED — 停止、まず法務に相談

NDA がプレイブックの「絶対拒否」リストの立場に当たる、または契約構造がチームの標準姿勢と互換性がない(例:プレイブックが相互を要求するのに一方向 NDA、プレイブックが有限期間にキャップするのに永続期間、「絶対拒否」リスト上の準拠法)。

**出力:**

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の `## Outputs` から作成物ヘッダーを先頭に付ける(ユーザー役割によって異なる — `## Who's using this` 参照)。

```markdown
[WORK-PRODUCT HEADER — プラグイン設定の ## Outputs に従って]

## NDA Triage: [相手方]

RED — 送信しない、まず法務に相談

### Executive Summary

- [1 行のアクション可能な編集、例:「Section 4 — 法務レビューに振り分け」]
- [1 行のアクション可能な編集]

### 重大な論点

**1. [論点]** — Section [X]
   > "[厳密な引用]"
   なぜ問題か: [特定のリスク。それが違反するプレイブック立場を引用]
   **Legal risk:** [🔴/🟠/🟡/🟢] | **Business friction:** [🔴 Blocks deals / 🟠 Slows deals / 🟡 Confuses customers / 🟢 Invisible]
   推奨応答: [自社書式を使う | 具体的な文言で push back | 撤退]

**Next step:** このトリアージを [`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の GC または指名されたエスカレーション担当者] に送付。[CLM または承認ワークフロー] に送らない。署名する旨を相手方に伝えない。
```

## レッドラインの粒度

**可能な限り小さな粒度で編集。** レッドラインは交渉アーティファクトであり、書き直しではない。条項の全体置換は「あなたのドラフティングを捨てた」というシグナル — 攻撃的で、相手方に条項全体を再読させ、彼らのドラフティングのうち問題なかった部分も捨てる。外科的なレッドライン — 単語を削除、フレーズを挿入、サブクローズを再構築 — は「具体的な要望がある」というシグナルで、読み、理解し、受け入れるのが速い。

プレイブック立場を達成する最小の編集をデフォルトとする:
- フレーズの前に**単語**を置換。("twelve (12)" → "twenty-four (24)")
- 文の前に**フレーズ**を置換。("paid by the Buyer" → "paid and payable by the Buyer")
- 文を置換する前に**サブクローズ**を再構築。(複合条件を分割するために "(a)" と "(b)" を追加。)
- 条項を置換する前に**文**を置換。
- 相手方のバージョンが立場から遠すぎて外科的編集よりも新しいドラフトの方が読みやすい場合にのみ**条項全体**を置換 — そうする場合は送付メールに記載:「§8.2 はマークアップではなく置換しました。変更が広範だったためです。差分について喜んでウォークスルーします。」

迷ったらより小さく。外科的レッドラインを受け取ったクライアントは、あなたが注意深く読んだと信頼する。全体置換を受け取ったクライアントは、あなたが読んだのかどうかを疑う。

## 法域の前提

このトリアージは `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` に記録された準拠法と制限的誓約の立場を適用します。法的ルール(競業禁止、勧誘禁止、費用転嫁、法選択の執行可能性)は法域によって実質的に異なる。NDA がチームの設定姿勢の外の法域を含む場合、出力でそれをフラグし、トリアージは書かれたままでは通用しないかもしれないと注記する。

## 出力ルール

**複雑性フィルタ:** 論点に対処するために新しい文言の起草、条項の再構築、実質的な新条項の挿入が必要な場合 — 試みない。代わりに書く:
"Section [X] — 法務レビューに振り分け。"
Executive Summary には単純で機械的なアクション(削除、置換、単語またはフレーズの置換)のみを含める。

**クリーン NDA ルール:** NDA がすべてのチェックを通過し、フラグがない場合、Executive Summary は次のように述べるだけ:「red flag は識別されませんでした。標準プロセスに従って署名へ振り分けてください。」

クリーンな NDA に対して長いレポートを生成しない。

## 詳細チェックリファレンス

下記の各チェックについて、バケット(GREEN/YELLOW/RED)は `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` で決まる。このスキルはチェックする*カテゴリ*を列挙する。閾値をハードコードしない。

### 相互性

NDA は相互か一方向か? `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` のチームの立場を適用。プレイブックがこの文脈で一方向 NDA を扱わない場合、下記の一方向質問票を実行し、結果を人間のために浮かび上がらせる。

**一方向 NDA 質問票**

NDA が一方向(一方が開示、他方は受領のみ)の場合、即座に RED フラグまたは終了しない。尋ねる:

> 一方向 NDA はいくつかの状況では適切です。これをフラグする前に、
> いくつか簡単な質問をさせてください:
>
> 1. この関係で、機密情報を開示するのはあなた側だけですか?
>    (つまり、相手方は何も返さない)
> 2. これは限定された特定の開示用ですか — 例えば、
>    技術をベンダーに共有して作業させるが、ベンダーの技術は
>    共有してもらわない、など?
> 3. これは M&A、雇用、または投資に関連しますか? (Yes なら停止 —
>    このスキルは商業 MNDA のみ。法務に振り分け。)

回答と `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の立場を使って GREEN/YELLOW/RED を判断。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` がこの事実パターンに立場を取らない場合、YELLOW フラグして承認者のために質問票の回答を浮かび上がらせる。

### 機密情報の定義

範囲(マーク済みのみ対 開示されたすべて)、マーク要件、口頭開示の確認ウィンドウをチェック。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` のチームの立場を適用。プレイブックがいずれかについて沈黙していれば尋ねる。

### Carveouts

NDA に通常存在する 5 つの carveout:

1. 公知のもの、または公知となった情報(違反による場合を除く)
2. 受領当事者が既に保有していた情報
3. CI を参照せずに独立して開発された情報
4. 第三者から制限なく受領した情報
5. 法律または裁判所命令により開示を要求された情報(法的に許される場合は開示者への通知付き)

チームがどの carveout を要求し、どれくらい厳密にかは、プレイブック問題。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` でチームの必要 carveout、許容可能な文言の差異、欠落時の対応の立場を確認。

### Residuals

residuals 条項は受領当事者が補助なしの記憶に保持された情報を使うことを許す。これが受け入れ可能かどうか、どの条件下で(例:狭い「補助なしの記憶」文言 対 メモまたはコピーをカバーする広い範囲)は、プレイブック問題。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` を適用。プレイブックが residuals を扱わなければ尋ねる。

### 期間とサバイバル

初期期間長、機密性義務の期間後サバイバル期間、営業秘密がより長い保護で carveout されているかをチェック。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` のチームの立場を適用。プレイブックがいずれかをカバーしなければ尋ねる。

### 制限的誓約

勧誘禁止(従業員、顧客)、競業禁止、独占、受領当事者が他に誰と契約できるかに対する制限をチェック。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` を適用。プレイブックが沈黙していれば尋ねる — 制限的誓約は法域に敏感で、チームの姿勢が重要。

### 弁護士費用

費用転嫁条項、それが相互か、一方的か、prevailing-party かをチェック。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` を適用。

### バックアップとアーカイブの carveout

破棄/返却条項に標準のバックアップとアーカイブ保持システムに対する例外が含まれるかチェック。`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` のチームの立場を適用 — この carveout を要求し追加するよう push するチームもあれば、それなしで NDA を受け入れるチームもある。プレイブックが扱わなければ尋ねる。

### 準拠法

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` → `## Playbook` → `Governing law and venue` に従う。

## 相手方コンテキスト

**BigCo NDA:** Fortune 500 の相手方は通常 NDA を交渉しない。キャリブレート:RED フラグは本当にディールブレーカーか、それとも「我々のフォームと違う」だけか? ビジネス関係が重要なら、彼らの書式を受け入れるかどうかが判断 — その判断をエスカレーションし、自分で決めない。

**スタートアップ NDA:** 通常自社書式を受け入れる。彼らの NDA に問題があれば、最速の道はしばしば「我々のを使おう」で、彼らのをレッドラインするのではない。

## インテグレーション:CLM

接続されていれば:
- GREEN → 標準 NDA ワークフローで CLM レコード作成を提示
- YELLOW → フラグされた項目を列挙した注記付きで作成を提示
- RED → レコードを作成しない。弁護士が次を決める

## このスキルが行わないこと

- 交渉しない。並び替えるだけ。
- NDA を起草しない。答えが「自社書式を使う」なら、ユーザーが [CLM またはドキュメントシステム] から自社フォームを引き出す。
- YELLOW 項目の判断をしない。人間のために浮かび上がらせる。
- いかなる NDA 条項についても立場を述べない。立場は `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` に存在する。

## 締めのアクション

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` → `## NDA triage preferences` → `closing_action` を読む。

設定されていれば、各出力の末尾に締めのアクションをそのまま追記。設定例:

```
closing_action: "署名前に最終確認のため、この分析の全文と NDA のコピーを
Legal at legal@[yourcompany].com に送付してください。"

closing_action: "標準 NDA ワークフローで [CLM] へ送信。
Legal が署名へ振り分ける前に確認します。"

closing_action: "この出力と NDA をあなたの契約マネージャーへ転送してください。"
```

`closing_action` が `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` に設定されていなければ、次を追記:
「最終 NDA を標準承認プロセスに通してください。」

コールドスタート・インタビューは尋ねる:「誰かが NDA トリアージを終えたら、出力を何に使ってほしいですか? 各レビューの末尾に常設指示として追加します。」

## 次のステップのデシジョンツリーで締める

CLAUDE.md `## Outputs` の next-steps decision tree でクローズ。このスキルが今生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X、escalate、get more facts、watch and wait、something else)は出発点でロックインではない。ツリーが出力であり、弁護士が選ぶ。
