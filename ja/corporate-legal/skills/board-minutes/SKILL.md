---
name: board-minutes
description: >
  取締役会または委員会の議事録をハウスフォーマットで起案します。カレンダーから今後の取締役会・委員会を自動検出し、
  アジェンダおよびスライド・事前資料を求め、シード議事録から学んだフォーマットで完全なドラフトを生成します。
  会議に代わる書面決議(written consent)にも対応します。
  Trigger: "board minutes", "draft minutes", "upcoming board meeting", "committee minutes", "written consent",
  or calendar detection of an upcoming board or committee event.
  日本語トリガー: 「取締役会議事録」「議事録を起案」「次の取締役会」「委員会議事録」「書面決議」、
  または今後の取締役会 / 委員会イベントのカレンダー検出。
---

# Board Minutes(取締役会議事録)

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

取締役会議事録は法的記録です。ファイナンスの DD レビュー、規制当局の照会、M&A データルームのいずれの精査にも耐えるよう、正確で、完全で、フォーマットが整っている必要があります。本スキルは自社のハウスフォーマットで起案し、フォーマット整形や再入力ではなく、レビューと修正に時間を使えるようにします。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → `## Board & Secretary` セクション:
  - 議事録フォーマット(long-form narrative / action minutes / hybrid)
  - シード文書から抽出した議事録テンプレート(構造、決議文言、ヘッダーフォーマット)
  - 取締役会構成と委員会
  - 書面決議 — 用途と制限
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` に議事録フォーマットがなければ、まずコールドスタートを実行。汎用フォーマットで進めないこと。

---

## Step 1: 会議を特定

### カレンダー検出

カレンダーコネクターが承認済みなら、取締役会・委員会キーワードに合致する今後のイベントを検索:

**検索語:** "Board of Directors", "Board Meeting", "Audit Committee", "Compensation Committee", "Comp Committee", "Nominating", "Nom/Gov", "Governance Committee", "Special Committee", "Board of Directors — [Company]"

**時間ウィンドウ:** 30 日先を見る。今後の会議が見つからない場合、過去 14 日を見る(議事録は事後に起案されることが多い)。

見つかったものを提示:

> あなたのカレンダーに次の取締役会または委員会会議が見つかりました:
>
> 1. **[Meeting name]** — [Date], [Time], [Location/Virtual]
> 2. **[Meeting name]** — [Date], [Time], [Location/Virtual]
>
> どれの議事録ですか? もしくはここにない別の会議ですか?

カレンダーコネクターが未承認または何も返さない場合:直接尋ねる — どの会議か、いつか、種類(全取締役会 / どの委員会)か。

### 確認すべき会議メタデータ

会議が特定されたら、以下を確認または記入:

- **Meeting type(会議種別):** Full Board of Directors / [Committee name]
- **Date and time(日時)**
- **Location or platform(場所・プラットフォーム)**(対面アドレス / Zoom / Teams / 電話)
- **Called by / Notice(招集 / 通知):** 適切な通知がなされたか?(Yes / 放棄 — waiver of notice は別紙として典型)

---

## Step 2: 出席

出席者リストを尋ねるか、コネクター承認済みならカレンダー招待から引くと申し出る。

**Directors present(出席取締役):**
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の取締役会構成を出発点とする
- 実際の出席者・欠席者、欠席取締役が事前通知を受けていたかを確認

**Management present(出席経営陣):**
- どのマネジメントが出席したか?(CFO, CAO, CTO 等)
- 注:経営陣の出席は通常、取締役とは別に記載

**Guests(ゲスト):**
- 外部弁護士の出席?(氏名と事務所名)
- 投資銀行家、監査人、その他アドバイザー?
- 特定議題のみ出席したゲスト(出席をその議題に限定する旨を記録)

**Chair(議長):**
- 誰が議長を務めたか?
- 誰がセクレタリーを務めたか?

**Quorum(定足数):**

- 定款・付属定款の定足数要件を確認。定款が沈黙していれば、当該法人類型のデフォルトルールについて適用される州コーポレート法を調査。確認内容(出典とピンポイント)を起案ノートに記録。
- 定足数の充足を確認。不足の場合:停止して起案前にフラグする。有効な会議が開催されたと示唆する議事録を生成しないこと。質問を外部弁護士に振る — 是正経路(ratification(追認)、再開催、written consent、その他)は incorporation の州とアクションの性質次第。

---

## Step 3: 資料

会議資料を求める。これらはアジェンダ項目と決議のソース。

> この会議のアジェンダおよび事前資料を共有してもらえますか? ラフなアジェンダだけでも議事録の構造化には十分です。取締役会向けスライドやマネジメント・プレゼンがあれば、それもアップロードしてください — アジェンダ項目の要約に使います。
>
> 資料が事前配布されなかった場合、アジェンダ項目を教えてください、各項目にプレースホルダーを起案します。

**アジェンダとスライドから抽出:**
- 順序付きのアジェンダ項目
- 提案された決議(取締役会承認文言を探す:"approve", "authorize", "ratify", "adopt", "elect")
- 参照される別紙(マネジメント・プレゼン、財務報告、法務メモ、評価)
- 予定された投票

**資料がない場合:** アジェンダ項目を口頭で聞き、議論内容のプレースホルダーで進める。

---

## Step 4: 議事録を起案

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` のハウスフォーマットを使う。汎用フォーマットにデフォルトしないこと。シード議事録がテンプレート — 構造、ヘッダー、決議文言、議論の詳細レベルを複製する。

### 標準構造(ハウスフォーマットに合わせて調整)

**Header block:**
```
MINUTES OF [MEETING TYPE] OF THE BOARD OF DIRECTORS
[OR: MINUTES OF THE [COMMITTEE NAME] OF THE BOARD OF DIRECTORS]
OF [COMPANY NAME]

[Date]
[Location / Telephonic / Video Conference]
```

**Opening:**
- Meeting called to order by [Chair name] at [time]
- Notice: [proper notice given / notice waived — attach waiver as exhibit if applicable]
- Quorum confirmed: [N of M directors present]
- Secretary: [name]

**Attendees:**
- Directors present: [list]
- Directors absent: [list, if any]
- Also present: [management, outside counsel, guests — with roles]

**Previous minutes:**
標準文言:前回会議の議事録の承認。前回会議の日付は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の取締役会カレンダーから引く、なければ [DATE OF PRIOR MEETING] のまま。

**Agenda items — 各項目につき 1 セクション:**

```
[AGENDA ITEM TITLE]

[Chair/presenter name] [presented / reported on / led a discussion of] [topic].

[Discussion summary — 下記起案ノート参照]

[決議が続く場合:]
Upon motion duly made and seconded, the following resolution was adopted [by unanimous vote / by a vote of N for, N against, N abstaining]:

RESOLVED, that [resolution text in house language from `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md`].
```

**Adjournment:**
標準文言:meeting adjourned at [time], there being no further business.

**Signature block:**
セクレタリーの署名欄。フォーマットによっては議長の副署も含む。

---

### 起案ノート

**Discussion summaries(議論サマリー):** 議事録で最も難しいのは、議論をどれだけ記録するか決めること。シード文書のハウスフォーマットに厳密に従う:

- *Long-form narrative:* 議論の実質を要約 — どんな質問が出たか、どんな情報が提示されたか、取締役会がどんな要素を考慮したか。法的に特定の帰属が重要でない限り、個人を引用しない。
- *Action minutes:* 提示されたものと取られたアクションのみを記録。「the board discussed the matter」を超える議論内容はなし。
- *Hybrid:* 重要項目(買収、財務、重要な承認)はフルナラティブ、ルーチン項目はアクションのみ。

資料が提供された場合:スライドとマネジメント・プレゼンからサマリーコンテンツを引く。取締役会はプレゼンを「受領しレビューした」 — プレゼンがカバーした内容を要約。

資料がない場合:`[PLACEHOLDER — summarize discussion here]` を挿入し、明確にフラグ。議論内容を捏造しないこと。

**Resolutions(決議):** シード議事録の正確な決議文言を使う — "RESOLVED, THAT" vs "BE IT RESOLVED" vs "RESOLVED" 単独。文言はハウススタイルで、互換性はない。

**Exhibit references(別紙参照):** 別紙は登場順に番号付け(Exhibit A, B, C)。一般的な別紙:マネジメント・プレゼン、財務諸表、評価レポート、法務意見書、waivers of notice、consents。

---

## Step 4.5: Consequential-action gate(議事録の採択)

**議事録を最終として採択する前に:** `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の `## Who's using this` を読む。Role が **Non-lawyer(非弁護士)** の場合:

> 議事録の採択はそれを取締役会の決定の公式記録にします — 会議で取られたアクションの授権の主要証拠となります。弁護士とレビュー済みですか? Yes なら進める。No なら、彼らに持って行くブリーフ:
>
> - 何が決定されたか(決議、投票、出席者)
> - ドラフトが捕捉した内容とまだプレースホルダーの内容
> - オープンな質問(フラグされた出席、定足数、利益相反のノート)
> - 何が悪い結果になり得るか(誤記された決議、欠落した開示、定足数の瑕疵、議論サマリーへの特権漏洩)
> - 弁護士に聞くこと(この取締役会の実務に対する議論深度は適切か、エグゼクティブセッションのノートは適切に分離されているか、より文書化が必要な項目はあるか)
>
> 弁護士、ソリシター、バリスター、その他認可された法律専門家を見つける必要があれば:あなたの専門規制者(米国の州弁護士会、英国(イングランド・ウェールズ)の SRA / Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの Law Society、または当該法域の同等機関)に紹介サービスを問い合わせてください。

このゲートを明示的な Yes なしに通過した最終採択準備版は出力しないこと。弁護士レビュー用に DRAFT 表示されたものは可。

---

## Step 5: 出力とレビュー用プロンプト

完全ドラフトを生成する。議事録自体はコーポレート記録であって特権ではない。回覧される議事録には work-product ヘッダーを適用しない。起案ノート、プレースホルダーフラグ、下記レビューチェックリストは work product — `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の `## Outputs` から work-product ヘッダーを冒頭に付与(ユーザー役割により異なる — `## Who's using this` 参照):

```
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]
```

ドラフトの後、レビューチェックリストを追加:

```
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

REVIEW CHECKLIST — please verify before circulating:

□ All directors confirmed present/absent (check against actual attendance)
□ Quorum confirmed correct
□ Resolution language matches what was actually approved (check wording carefully)
□ Votes recorded correctly — any abstentions or dissents to note?
□ Exhibits numbered and referenced correctly
□ Any executive sessions held? (Add separate executive session note if so)
□ Any conflicts of interest disclosed? (Director recusal to note if applicable)
□ Time of adjournment to fill in
□ Outside counsel reviewed? (If required by your process)
```

コンテンツがプレースホルダーで、議事録が正確になる前に弁護士の入力が必要なセクションをフラグする。

採択前ノートとしてドラフトに付け、採択前に剥がす:

> これは弁護士レビュー用のドラフトであって、採択済みの議事録ではありません。採択済み議事録は取締役会のアクションの公式記録で、法的帰結を持ちます — 認可された弁護士が採択前にレビュー、編集、専門的責任を負います。レビューなしで採択しないでください。

---

## 書面決議(written consents)

会議に代わる書面決議の起案には、`/corporate-legal-ja:written-consent` を使用してください。同スキルが先例検索、州法確認、および重大な単発アクションのスコープ警告を処理します。

---

## 本スキルがしないこと

- 会議に出席せず、リアルタイムの議論を捕捉しません — 資料と弁護士の入力から起案します。
- 決議が法的に有効か十分かを判断しません — ハウスフォーマットで起案します。十分性の法的判断は弁護士の仕事です。
- 議事録を最終化しません — 回覧前にドラフトは弁護士レビューを要します。
- 議事録を配布しません — 出力は弁護士がレビュー、編集、自身のプロセスで回覧するためのものです。
