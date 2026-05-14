---
name: chronology
description: 宣言された文書出典とアップロードから時系列表を構築または更新 — 日付付きイベントを抽出、重複排除、案件セオリーに従って重要度タグ付け。Use when the user asks to build a chronology or timeline from a production or matter file, says "chron from the production" or "what happened when", or needs a working, statement-of-facts, or witness-specific timeline. 日本語トリガー: 時系列表、クロノロジー、タイムライン構築、案件の時系列
argument-hint: "[slug] [--format=working|sof|witness-[name]]"
---

# /chronology

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` をロード → セオリー、pivot fact、キーファクト。
2. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → 文書ストレージ出典、デフォルト案件フォルダパターン。
3. 以下のワークフローと参照に従う。
4. 順序で出典を特定: このセッションでユーザー提供のパス、デフォルト案件フォルダ、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` で宣言された出典。
5. 読み取り可能な出典: 日付付きイベントを抽出。到達不能出典: ギャップに注記。
6. 重複排除、イベントごとに出典リストとマージ。
7. 案件セオリーに従って重要度(🔴/🟡/⚪)をタグ付け。
8. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/chronology.md`(またはフラグごとのフォーマットバリアント)を書き出し。
9. 先行バージョンがあれば: バージョン番号インクリメント、diff サマリーをユーザーに提示。
10. 最終化前に確認: 「Here's what I built. Scan the 🔴 entries — anything I miscalled?」

---

# Chronology

## ディスクローズ文書使用制限

訴訟文書セットで作業を開始する前に尋ねる: 「これらの文書のいずれかが法的手続きでのディスクローズまたはディスカバリーを通じて取得されましたか?」 Yes なら:

- **イングランド&ウェールズ(CPR 31.22):** ディスクローズを通じて取得した文書には、暗黙の約束が適用される — 裁判所が許可を与える、ディスクローズする当事者が同意する、または文書が公開法廷で読まれた場合を除き、ディスクローズされた手続きの目的のためにのみ使用できる。許可なしに別の案件、別の請求、または商業目的のために使うことは contempt です。
- **米国:** 保護命令と Rule 26(c) が類似の制限を課す可能性。命令を確認。
- **その他の法域:** 類似の制限が一般的に適用。ローカルルールを確認。

確認: 「この使用はディスクローズされた手続き内、または許可 / 同意あり、または文書は現在公開」。確認できなければフラグ: 「⚠️ ディスクローズ文書には使用制限がある可能性。進む前にこの使用が許可されていることを確認。」

## 目的

事実は順序で起きる。時系列表はすべての物語が hang するスパインです — ブリーフの事実陳述、引当メモ、和解メモ、depo 準備、証人準備。手で chron を構築するのは遅い;AI は構造化抽出が得意。catch: garbage-in、garbage-out。このスキルは設定が宣言する出典とユーザーがアップロードするものから引き出します。

## モード

このスキルは 2 つのプラクティス設定にサービス。プラグインの設定 CLAUDE.md のユーザーの `## Role` からデフォルトを選択;ユーザーは実行ごとにフラグでオーバーライド可能。

- **`--matter` モード(社内訴訟弁護士のデフォルト)。** 案件履歴フォーカス。`matter.md` から案件のケースセオリーとキーファクトを読み、宣言された文書ストレージ出典(Google Drive、SharePoint、Gmail、iManage、CLM — CLAUDE.md の `## ランドスケープ` セクションが宣言するもの)から引き出し、`history.md` を running internal log(決定、ホールド、引当メモ — 意図的に時系列表に含まれない)として扱う。出力は案件中心: 紛争を跨いで何が起きたか、アドボカシー使用のためにタグ付け。
- **`--documents` モード(事務所アソシエイト / パラリーガルのデフォルト)。** 生成文書フォーカス。設定からケースセオリーを読み、eDiscovery 輸出、custodial ファイルセット、または Bates 番号付き生成から抽出。出力は生成中心: 文書が示すもの、Bates 引用付き、ケースセオリーに従ってタグ付け。

両モードは同じ出力構造(タイムライン、🔴/🟡/⚪ 重要度タグ、ギャップ、SoF バリアント)に収束。違いは出典プロファイルと重要度フレーム。

`## Role` が `solo` または `other` なら、`--matter` にデフォルト、ただし最初の実行で両モードを言及してユーザーに選ばせる。

## サイドフレーミング(重要度タグ)

同じイベントは、実務家がクレームを立証するか反証するかによって異なる方法で重要。プラクティスプロファイルの `## サイド`(およびマターがデフォルトをオーバーライドするなら案件ごとのポスチャ)を読む:

- **Plaintiff(攻撃的フレーミング)** — 🔴 はクレームの要素(責任、因果関係、損害、通知)を*確立*する、防御が開こうとする隙間を*閉じる*、または plaintiff の有利に出訴期限を*開始*するイベント。🟡 はクレームを支えるが impeachment 対象のイベント。⚪ は背景コンテキスト。
- **Defense(防御的フレーミング)** — 🔴 はクレームの要素(因果関係、通知、依拠の失敗)を*破る*、出訴期限または管轄権防御を*開く*、または積極的防御(免責、放棄、リスク引受、比較過失)を*支える*イベント。🟡 は plaintiff の物語を undermine するイベント。⚪ は背景。
- **Both / varies** — 時系列表ごとにどのサイドのフレーミングを重要度タグに適用するかをユーザーに尋ねる。基礎となるタイムラインはサイド中立;重要度の読みだけが変わる。

出力の上部に適用フレーミングを注記: `Significance tags applied from [plaintiff / defense] perspective.` 事実陳述バリアントを生成するとき、ユーザーが指定しない限りサイドデフォルトを使用。

## コンテキストのロード

共通:
- プラグイン設定 CLAUDE.md → ケースセオリーコンテキスト(社内: 文書出典のための `## ランドスケープ`;事務所アソシエイト: プラットフォーム + custodian のための `## ケースセオリー` と `## 文書レビュー`)、成果物ヘッダーのための `## Outputs`、特権フラグルールのための `## 意思決定ポスチャ`。
- 案件の先行する `chronology.md`、存在すれば。
- セッション内でユーザーがアップロードする任意のファイルまたは提供するパス。

`--matter` モードはまた読む:
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` → ケースセオリー、キーファクト、pivot fact(重要度タグ付け用)、キー日付。
- CLAUDE.md からのデフォルト案件フォルダパターン → この slug の docs がどこに住むか。

`--documents` モードはまた読む:
- コネクター(Everlaw、Relativity、DISCO、Aurora)が利用可能なら eDiscovery プラットフォームメタデータ — custodian + 日付範囲で。
- ユーザーが指し示すなら Bates 範囲マニフェストまたは生成インデックス。

**コンフリクトゲート — バイパス不可(`--matter` モード)。** 時系列表構築前に、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace is set up. I won't build a chronology on a matter that hasn't been intaken — the conflicts check is the gate."

intake されていない案件で進まないこと。intake はコンフリクトを実行し、このスキルが読み取る `_log.yaml` 行を書きます。`--documents` モード(案件 slug なしのアドホック文書セットに対して実行)はゲートを免除されますが、出力は pre-matter リサーチとして扱われるべきで、提出された案件成果物としてではない。

## ワークフロー

### Step 0: 特権ゲート(最初に毎回実行)

時系列表作業は文書から引き出します。文書はしばしば特権(弁護士・依頼者、成果物、共通利益、joint defense) — 社内案件ファイルはしばしばデフォルトで;eDiscovery 生成、特にローリング生成または共通利益生成は、しばしば特権または未レビュー素材を含みます。特権文書から後で共有される時系列表へのコンテンツ抽出は、誰が受信するか、どの法理下かに依存して、放棄を*リスク*する可能性があります(共通利益、joint-defense、Kovel、成果物保護が適用される可能性)。放棄分析は事実固有 — 配信前に弁護士サインオフを得る。

スキルはユーザーが特権ポスチャを選ぶまで抽出しません:

> Before I extract: how have the sources been privilege-screened?
>
> - **A. All sources cleared** — you've already screened these. I extract without privilege flags. Output is discovery-ready posture; still marked work product.
>
> - **B. Mixed or not yet screened** — I extract and tag every entry with a `priv` flag: `ok` (sourced from clearly non-privileged material), `flag` (sourced from potentially privileged material — A/C, WP, common interest), or `review` (source unclear). Flagged entries are visually marked in the output, and the Statement-of-Facts variant filters them out by default.
>
> - **C. Abort — screen first** — pause the skill. Screen the sources. Return and re-run.

選択を時系列表ヘッダーに `privilege_posture: A-cleared | B-mixed | C-aborted` として記録。B または C なら、根拠を簡潔に記録。

**なぜゲートで単なる警告ではないか:** 警告は一度読まれて忘れられる。ゲートはポスチャ決定をレコードに強制、つまりすべての時系列表ファイルが自身の出処を運ぶ — 後で読む誰でも、エントリが特権スクリーン素材から派生したかを知ります。

### Step 1: 文書出典を特定

**`--matter` モード:**

1. **ユーザー提供パス** — このセッションでドロップされたもの(ファイルパス、ドライブリンク、メール輸出)。
2. **デフォルト案件フォルダ** — CLAUDE.md の文書ストレージパターンから、この slug 用に展開(例: `G:/Legal/Matters/acme-v-us-2026`)。
3. **宣言された出典** — CLAUDE.md の `Document storage` テーブル、この案件が触れる可能性のあるものにフィルタ(例: 送信者サイドの通信用 Gmail アーカイブ、SharePoint リーガルフォルダ)。
4. **尋ねる** — 出典が薄く見えるなら、プロンプト: 「I can build from what I have, but the chronology will be incomplete. Anything else to point me at? Key emails, contracts, internal memos, production letters?」

**`--documents` モード:**

1. **生成輸出 / Bates セット** — ユーザーが生成ディレクトリまたはマニフェストを指し示す;スキルが Bates 範囲 + 日付で読む。
2. **eDiscovery コネクター** — MCP コネクターが利用可能なら(Everlaw、Relativity、DISCO、Aurora)、custodian + 日付範囲で引き出す。
3. **Custodial ファイル** — ユーザーが raw custodial メールボックスまたはドライブ輸出を提供したら、それらも読む。
4. **尋ねる** — キー custodian または日付範囲のカバレッジが薄く見えるなら、プロンプト。

### Step 2: 取得 + 読み

読み取り可能ファイルのある各出典について:

- **PDF、メール(.eml)、.docx、.txt** — 直接読む。
- **メールアーカイブ(Gmail、Outlook)** — MCP コネクターが認証済みなら、日付範囲 + 相手方 / キー用語でクエリ;それ以外はユーザーが関連スレッドをフォルダに輸出。
- **eDiscovery プラットフォーム(Everlaw、Relativity、DISCO、Aurora)** — コネクターが利用可能なら、custodian + 日付範囲で引き出す;それ以外はユーザーが輸出を提供。

スキルが宣言された出典にアクセスできないなら、黙って進む代わりに出力の Gaps セクションで明示的に名前を出す。

**No silent supplement.** 案件の時代の出典カバレッジが薄い — 主張された時間窓に期待されるより少ない文書、メールボックスがアクセスできない custodian、まだ着いていない生成 — 場合、見つかったものを報告して停止。尋ねずに web 検索、公開記録検索、または案件についてのモデル知識からギャップを埋めないこと。こう言う: 「Sources returned [N] events for [period / custodian]. Coverage appears thin. Options: (1) point me at additional sources (Bates, folder, mailbox), (2) try a different MCP connector if configured, (3) search the web for public-record events in this window — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) stop here and note the gap. Which would you like?」 弁護士が低い自信の出典を受け入れるかを決定;スキルは決定しない。

**Source attribution.** 取得文書から抽出されたイベントについて、すべての時系列表エントリにイベントが来た場所をタグ付け: ファイルパス、Bates 番号、MCP コネクター、または宣言された文書ストレージ出典(Sources 列に既にキャプチャ)。取得文書に遡れない任意のイベントまたは日付について — 例: モデルトレーニングデータから思い出された事実、web 検索で見つかった公開記録イベント — インラインでタグ: `[web search — verify]`、`[model knowledge — verify]`、またはユーザーがセッション中に事実を述べた場合 `[user provided]`。`verify` タグのエントリは文書出典エントリより高い捏造リスクを運び、最初にチェックすべき。タグを絶対に剥がしたり折りたたんだりしないこと — それらは、ブリーフや SoF に引き込む前にどのエントリを検証するかについての弁護士への最速のシグナル。

**タグ付けは法的結論、期限、または計算された日付を述べるすべてのセクションに到達する — タイムラインエントリだけではない。** タイムラインは文書から sourced される。Gaps セクション、Key events セクション、Theory tie 行、および任意の制限ステートメント、tolling イベント、提出期限、ディスカバリーカットオフ、または特権決定は、sourced されない限りモデル知識からスキルが書く法的分析。そのような各ステートメントは出処タグを運ぶ: `[computed from: <rule cited with tag>]`、`[model knowledge — verify]`、`[user provided]`、またはこのセッションで取得されたならリサーチコネクタータグ。タグなしの出訴期限ウィンドウはデフォルトで `[model knowledge — verify]`。事実の法的重要性を性格付ける「key event」行は分析でタグが必要。ルールは単純: 文書が言うことについての主張ではなく、法についての主張なら、タイムラインエントリと同じ出処タグを運ばなければならない。リサーチコネクターが到達不能でスキルが期限を計算したり規則を引用したりしているとき、レビュアー注記の **Sources:** 行に記録(プラグイン CLAUDE.md `## Outputs` 参照) — スタンドアロンバナーを出力しないこと。

### Step 3: イベントを抽出

各文書について、日付付きイベントを特定:

- **Email:** `[date] [sender] told [recipient] [subject/content]`
- **Meeting:** `[date] [attendees] met about [topic]`(カレンダーエントリまたはノートに従って)
- **Decision:** `[date] [decision-maker] decided [what]`(記憶化文書に従って)
- **Filing / pleading:** `[date] [party] filed [motion/complaint/response]`
- **External event:** `[date] [thing happened]`(契約署名、製品ローンチ、規制当局アクション、イベントが閾値を越える)

文書ごとに通常 1 イベント。たまにゼロ(undated またはイベントが確立されない)。時に複数(複数の決定をカバーするミーティングサマリー)。

**エントリごとの特権フラグ(privilege_posture == B-mixed のときのみ)。3 状態ルール — 主観的特権テストが満たされないと黙って決定しない:**

- `priv: ok` — 出典が**自信を持って**非特権(提出物、規制通信、公開 docs、我々の弁護士なしの相手方通信)。妥当な特権理論がない場合のみ使用。
- `priv: flag` — 出典が自信を持って、または likely 特権(弁護士との通信、成果物メモ、特権ドラフト、joint-defense 素材)。**不確実なもののデフォルト** — 支配的目的判断が際どい、または訴訟検討が境界、またはコンテンツが混在なら、`ok` ではなくここに入る。
- `priv: review` — 出典が表面上不明確だが、スキルが判断をまったく下せなかった(送信者/受信者メタデータなし、読めない、など)。

`priv: flag` または `priv: review` のとき、レビュー中に弁護士が見るようインラインで `[SME VERIFY: privilege status]` を追加。Under-flagging は特権を放棄(一方通行ドア);over-flagging はレビューで弁護士が修正(両開きドア)。回復可能なエラーを優先。

### Step 4: 重複排除

同じイベントが複数の文書に現れる: ミーティングが 3 つのカレンダーにあり、サマリーメールを生成 — それは**1 イベントと 4 出典**で、4 イベントではない。マージ。マージされたエントリはすべての出典を引用。

### Step 5: 重要度をタグ付け — ケースセオリーごと

`matter.md`(`--matter` モード)または設定の `## ケースセオリー` セクション(`--documents` モード)から pivot fact とキーファクトを読む。各イベントをタグ付け:

- 🔴 **Key** — イベントが pivot fact または我々のため/反対するキーファクトの一部
- 🟡 **Relevant** — コンテキスト、パターン証拠、二次議論を支える
- ⚪ **Background** — 完全性のために有用、ブリーフには入らない

**規律:** 300 エントリの時系列表で 300 🔴 タグはタグなし。🔴 を本当に factfinder を動かすイベントに留保。疑わしいなら 🟡。

**境界タグ付け:** エントリが 🔴 と 🟡 の間(または 🟡 と ⚪)に座る場合、低い重要度でタグ付けし、インラインで `[SME VERIFY — borderline significance call]` を追加。弁護士の判断がスキルの判断をオーバーライド。自信を持って over-tag する時系列表は、不確実性を表面化するものより役立たない。

### Step 6: 書き込み

デフォルト出力は working chronology。リクエストでバリアント。

## 出力フォーマット

### Working chronology(デフォルト)

場所: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/chronology.md`。完全、タグ付き、注釈付き。弁護士が作業するリファレンス doc。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

> **Privilege inheritance.** This chronology is derived from matter documents that may be attorney-client-privileged, work-product-protected, common-interest / joint-defense material, or a mix. It inherits the sources' protection status. Distributing it beyond the privilege circle — to business stakeholders outside the engagement, to opposing counsel, to a regulator — can waive protection over both the chronology and the underlying sources. Store with privileged matter material, mark consistently with house privilege conventions, and make distribution decisions deliberately. The privilege-posture choice captured below is the provenance stamp for any later distribution call.

# Chronology — [Matter Name]

> 重要度タグ(🔴/🟡/⚪)と特権フラグ(🔒)は最初のパスの読みで、任意の外部成果物(ブリーフ、SoF、取締役会メモ、外部弁護士成果物)で使用前に `[SME VERIFY]` を要する。

**Matter:** [slug]
**Mode:** matter | documents
**Built:** [YYYY-MM-DD]
**Sources:** [N] documents across [出典タイプ]
**Entries:** [N]([N] 🔴 / [N] 🟡 / [N] ⚪)
**Pivot fact:** [1 文]
**Privilege posture:** A-cleared | B-mixed | C-aborted
**Flagged entries:** [N] 🔒 *(posture == B-mixed のときのみ存在)*

---

## タイムライン

| Date | Event | Tag | 🔒 | Sources |
|---|---|---|---|---|
| [YYYY-MM-DD] | [何が起きたか、1 文] | 🔴/🟡/⚪ | [空白 / 🔒-flag / 🔒-review] | [ファイルパスまたは Bates] |

---

## キーイベント(🔴 のみ)

[引き出され、各々がセオリーに重要な理由の行付き。]

### [date] — [イベントタイトル]
- What: [1 行]
- Theory tie: [なぜこれが重要]
- Sources: [リスト]

---

## ギャップ

**イベントのない日付範囲:**
[範囲 — この期間の文書はどこ?]

**期待されるが欠落:**
[文書化されると期待されるが文書化されないイベント — 例: 「2024-06 から 2025-03 の間の契約修正 — 生成されず」]

**読めない出典:**
[CLAUDE.md で宣言されたがこの実行でアクセスできない出典 — 例: 「Everlaw 生成 — MCP コネクターなし;輸出が必要」]

---

## マーカー規律

- `[VERIFY: factual assertion — date, attendees, content]` — 基礎となる doc に対して未確認
- `[UNCERTAIN: legal characterization — 例: イベントが規制トリガーを確立するかどうか]`
- `[CITE NEEDED: Bates / exhibit / depo page:line]`
- `[SME VERIFY: 特権ステータス | 境界重要度判断]` — 弁護士判断必要

---

## バージョン
- v[N] [date] に [出典サマリー] から構築
- v[N-1] [date] に構築(先行、superseded)
```

### 事実陳述時系列表(リクエストで)

🔴 と関連 🟡 のみにフィルタ。時系列物語順の散文として提示 — ブリーフの事実セクションのスケルトン。各段落は 1 イベントまたは密接にリンクしたクラスタで、レコード引用付き。

**特権フィルタデフォルト:** `privilege_posture == B-mixed` のとき、🔒-flagged と 🔒-review エントリはデフォルトで**除外**。SoF バリアントは最終的な外部使用(ブリーフ、ディスクロージャ、相手方との交渉)向け — 弁護士が特権ステータスを確認するまで 🔒 エントリはそこに属さない。それでも 🔒 エントリを含めたい場合、明示的な `--include-flagged` 承認を要求;承認を出力ヘッダーに永久記録としてキャプチャ。

### 証人固有時系列表(リクエストで)

指名された証人が送信者、受信者、出席者、または対象であるイベントにフィルタ。証人準備にフィードし、証人がいつ何を知っていたかを再構築するのを助ける。

## インクリメンタル構築

`chronology.md` が存在する場合:

- 先行バージョンを読む
- 現在の出典から新規時系列表を構築
- Diff: 新規イベント(前回ビルド以降)、修正エントリ(既存イベントに追加された新規出典)、削除エントリ(まれ;理由を注記)
- 先行バージョン番号を保持;`v[N+1]` で新規バージョンを書く
- 変化のサマリーを出力

## matter.md / history.md との統合

**意図的に別個**(社内 `--matter` モード)。`history.md` は弁護士の running log — 決定、更新、手続的マイルストーン、内部戦略ノート。`chronology.md` は事実のアドボカシー向けタイムライン。重複するがマージしない:

- ホールド発行 → history.md に行く(内部アクション)。通常時系列表に入らない(紛争の事実ではない)。
- 相手方が 3 月 14 日に違反通知を送った → chronology.md に行く(🟡 — 彼らの知識を確立)。intake が言及したら history.md にも。
- 我々の引当推奨メモが起案された → history.md のみ。

弁護士が history イベントを時系列表に入れたい場合、貼り付けられる。デフォルトは別々のまま。

## このスキルがしないこと

- **矛盾を解決。** 2 つの文書がイベントの起きた時について異なることを言うとき、両方のエントリがフラグ付きで入る。解決は弁護士の判断;証人インタビューまたはさらなるディスカバリーを要する可能性。
- **出典にないイベントを捏造。** 文書になければ(matter.md または設定にキャプチャされた事実としてなければ)、時系列表に入らない — ただし「ギャップ」が欠落として呼び出すかも。
- **完全性を保証。** 時系列表は出典と同じだけ良い。eDiscovery 生成が進行中で 20% しか着地していなければ、時系列表はそれを反映。制限を名指す。
- **ユーザーのために特権ステータスを決定。** Step 0 ゲートがポスチャ選択を強制;エントリごとの `priv` フラグが最初のパス分類をキャプチャ。実際の特権決定は `[SME VERIFY]` フラグごとに弁護士の判断。
