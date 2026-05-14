---
name: written-consent
description: >
  取締役会または委員会の全会一致書面決議(UWC)をハウスフォーマットでドラフトします。
  書面決議リポジトリからの precedent 検索付き。複数決議、取締役利益相反フラグ、州法通知要件、
  署名者追跡を扱い、重要な単発アクションには組込みのスコープ警告を備えます。
  Use when user says "written consent", "unanimous consent", "board consent",
  "consent in lieu", "UWC", or describes an action needing board approval without a meeting.
  日本語トリガー: 「書面決議」「全会一致決議」「取締役会の書面決議」「UWC」、会議なしで取締役会承認が必要なアクション。
argument-hint: "[describe the action needing board approval]"
---

# /written-consent

1. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → Board & Secretary(書面決議リポジトリ、決議文言、incorporation 州、取締役会構成)をロード。
2. 以下のワークフローを使用。
3. アクションを特定し分類(routine / review-flag)。
4. review-flag なら:外部弁護士警告を表示し、進む前に確認。
5. 書面決議リポジトリで最も近い precedent を検索。リポジトリなければ:`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` のシード書面決議を使用。
6. precedent をベースにハウスフォーマットで書面決議をドラフト。
7. 出力:書面決議ドラフト + 署名者チェックリスト + レビュー用プロンプト。

---

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

ほとんどのルーチンな取締役会承認は会議を要しません。役員任命、株式付与、銀行授権、役員閾値超の契約承認、社内アレンジメント — これらは全会一致書面決議で行われます。本スキルはあなたのハウスフォーマットで素早くドラフトし、必要なものに最も近い過去書面決議を見つけ、誰かが署名する前に外部弁護士の目を入れるべきアクションをフラグします。

## スコープ警告 — ドラフト前に読む

> **本スキルは、あなたのリポジトリまたはシード文書に直接 precedent のある日常的な書面決議向けに設計されています。** ルーチンアクション — 役員任命、株式付与、年次授権、標準契約承認 — が正しいユースケース。スキルは厳密にマッチする過去書面決議を見つけ、現アクションに適応させ、クリーンなドラフトを生成。
>
> **重要な単発アクションには、本スキルが生成するものに関わらず外部弁護士レビューが賢明。** これには:M&A 取引(資産譲渡、株式譲渡、合併、投資)、ファイナンスラウンド、新規投資家への株式発行、change-of-control 規定、解散・終了、重要な不動産取引、その後の DD プロセスで精査されるあらゆるアクションが含まれる。
>
> アクションが重要な単発に見えた時、スキルは自動的にフラグする。そのフラグはブロックではない — 進める。クリーンな precedent 適応ドラフトがこの特定アクションに十分かを考えるプロンプト。

---

## 重要アクション + 緊急性 = 停止

重要な単発アクション(M&A、ファイナンス、解散、資本構造変更、ファイナンスまたは M&A に紐づく取締役選任)の取締役会書面決議で、ユーザーが**今日**署名を求めている場合 — 「今日午後 DocuSign で送付」「1 時間後の会議」「今夜署名」「市場開場前に必要」 — 外部弁護士レビューを通す。プラグインがドラフトできないからではなく — 重要アクションの誤った書面決議は一方通行のドアで、緊急性のプレッシャーはまさにミスが起きる時。

トリガー(両方が真):

1. アクションが下記の **Review flag — major one-off** カテゴリ(M&A、ファイナンス、解散、資本構造変更、change-of-control 規定、ファイナンスまたは M&A に紐づく取締役選任、重要な不動産取引、将来のファイナンスや M&A データルームに登場するあらゆるアクション)。
2. ユーザーの依頼に不可逆性シグナルが含まれる — "send for DocuSign"、"sign today"、"board is signing this afternoon/tonight"、"need this before [market open / closing / the meeting at X]"、同じターンで署名にコミットするフレーズ。

両方真なら、以下を出力して停止:

> ⛔ **重要アクション + 同日署名 — これを ready to sign としてマークしません。**
>
> これは [action type] で、一方通行のドアです。今日署名するよう求められました。その組合せはまさに取締役会書面決議のミスを取り戻すのが最も難しくなる時です。
>
> ドラフトはします — 喜んで — しかし外部弁護士の確認なしに ready to sign としてマークしません。外部弁護士がすでに本ディールに関与していれば、このドラフトを渡してください。そうでなければ、これが外部弁護士の出番です。あなたの専門規制者(米国の州弁護士会、英国の SRA / Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの Law Society、または当該法域の同等機関)は、必要なら同日対応可能な弁護士紹介サービスを示せます。
>
> 2 つの進路:
>
> 1. **私がドラフト、外部弁護士がレビュー、その後署名** — 重要なコーポレートアクションの通常パス。ドラフトと言われたらします。
> 2. **外部弁護士がすでに本ディールにいてドラフトパスをクリアした** — 誰がいつレビューしたか教えてください。進めて、外部弁護士がドラフトを保有している旨のノートを含めます。
>
> 同日プレッシャー下で「ready-to-send」形式でドラフトしません。これらいずれかなしには。これは遅延ではなく — 誰かがファイルを後で見た時、同日重要アクション書面決議が弁護可能になる唯一の方法。

このゲート下でパス 1 または 2 を選ぶ明示的な応答なしに Step 1 やドラフトに進まないこと。重要アクションのトリガーなしのルーチン書面決議、または同日署名依頼なしの重要アクション書面決議は、下記の通常フローに従う — 重要単発カテゴリの「Outside counsel review recommended」フラグは引き続き適用されるがハードストップしない。

---

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → `## Board & Secretary`:
  - 書面決議リポジトリの場所
  - ハウス決議文言
  - incorporation 州(通知要件用)
  - 取締役会構成(署名者リスト用)
  - 書面決議 — スコープと制限

### Precedent なしのハードストップ

(a) `## Board & Secretary` → Consents repository に書面決議リポジトリが構成されていない、**かつ** (b) 本セッションでアップロードまたは `## Board & Secretary` → Consent format セクションで特定シードからの抽出された resolution/recital/authorisation 文言で参照されたシード書面決議文書が本スキルに提供されていない場合、**ドラフト前に停止**。Step 1 intake に進まない、汎用テンプレートからドラフトしない、フィラーフォーマットで「始めない」こと。

正確に次のブロックを出力し、応答を待つ:

> **Precedent 利用不可 — ドラフト前停止。**
>
> マッチする precedent がありません。あなたのハウスフォーマットなしでドラフトされた取締役会書面決議は、節約より修正が多くなります — 決議文言、recital 深度、authorisation ボイラープレート、署名ブロック規約はすべてハウス固有の選択を含み、汎用テンプレートから始めると、レビューアーがゼロから書き直すことになります。
>
> 詰まりを解消する 2 つの方法:
>
> 1. **過去書面決議を貼り付けまたはアップロード**(本会社の任意カテゴリの任意の最近の UWC — フォーマットを抽出、実質は抽出しない)、または
> 2. **「汎用テンプレートから anyway ドラフト — 形式は自分で調整」と教えて** — 配布前に決議文言、recital スタイル、authorisation ブロックを手で再加工することを知っている場合のみこれを選ぶ。明示的に言う;推論しない。
>
> どちらにしますか?

これらいずれかを明示的に選ぶ応答なしに進まないこと。precedent なしのドラフト試行は、本スキルが生成できる最も再加工対価値比の悪い出力 — ハードストップは意図的。

---

## Step 1: アクションを特定

ユーザーに、取締役会が承認する必要のあるアクションを尋ねる。収集:

- **何が承認されるか?**(1 文。)
- **支持する詳細?** 例:任命される役員の名前、株式付与の付与額と価格、契約承認の相手方と契約金額。
- **発効日:** 今日、または特定日?
- **署名者:** フルボード、または特定委員会? `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の written-consent スコープが、特定アクションは書面決議ではなく会議を要すると言うなら、今フラグ。
- **取締役利益相反はあるか?** 承認されるアクションに重要な利害を持つ取締役はいるか? Yes ならフラグ。利益相反のある取締役は州法と利益相反の性質次第で署名可能だが、書面決議でそれを開示し、ユーザーが確認すべき。

### アクション分類

precedent を検索する前にアクションを分類:

**Routine — 直接 precedent が存在する可能性が高い:**
- 役員任命または解任
- 既存プラン参加者への株式付与(オプション、RSU、制限付き株式)
- 銀行口座授権または signatory 更新
- 重要性閾値以下の契約承認
- 年次授権決議(税務、ベネフィットプラン等)
- 独立当事者間条件での社内ローンまたはサービス契約
- 登録代理人または登録オフィス変更

**Review flag — 重要単発、外部弁護士が賢明:**
- M&A 取引(買収、合併、資産譲渡、投資)
- 新規ファイナンスラウンドまたはデットファシリティ
- 新規投資家への株式発行
- Change-of-control 規定またはトリガー
- 会社の定款または株主間契約により取締役会承認を要する契約の承認
- 解散、終了、または破産申立
- 重要な不動産取引
- 将来のファイナンスまたは M&A データルームに取締役会承認別紙として登場するアクション

アクションが review-flag カテゴリにある場合、ドラフト前に表示:

> ⚠️ **外部弁護士レビュー推奨。** これは [action type] のようです — precedent 適応ドラフトでは不十分な可能性のある重要コーポレートアクション。配布前に外部弁護士レビューを検討。anyway ドラフトを進めますか?

---

## Step 2: precedent を検索

### 書面決議リポジトリが接続済みなら

リポジトリで最も近い過去書面決議を検索。検索戦略:

1. アクションタイプキーワード(例:"officer appointment"、"equity grant"、"bank authorization")で検索
2. 最も最近のマッチ書面決議を返す、または複数の近いマッチが存在すればユーザーに選ばせる:

> このように見える過去書面決議が [N] 件見つかりました:
>
> 1. [Consent title / description] — [Date]
> 2. [Consent title / description] — [Date]
>
> 必要なものに最も近いのはどれですか? それとも最も最近のを使うべき?

3. 選択された書面決議を読む。抽出:決議文言、recital 構造、authorization 文言、特定の条件やカーブアウト。
4. 過去アクションと現アクションの差で、ドラフトで更新が必要なものを記録。

### リポジトリなし(シード文書のみ)

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` のシード書面決議からフォーマットを抽出。precedent 検索は利用不可と記録 — ドラフトはハウスフォーマットに従うが実質的 precedent マッチなし。ユーザーにフラグ:

> 書面決議リポジトリが接続されていないので、フォーマットはシード文書から作業しています。このアクションタイプ専用に、実質的出発点として使える過去書面決議があるかチェックしたいかもしれません。

---

## Step 3: 書面決議をドラフト

ハウスフォーマットを使用。下記構造は標準 — precedent またはシードフォーマットに正確に合わせる。

```
UNANIMOUS WRITTEN CONSENT
[OF THE BOARD OF DIRECTORS / OF THE [COMMITTEE NAME]]
OF [COMPANY NAME]

[Date]

The undersigned, constituting all of the members of the
[Board of Directors / [Committee]] of [Company Name], a [State] [corporation /
limited liability company] (the "Company"), hereby adopt the following
resolutions by written consent pursuant to [Section X of the [State] General
Corporation Law / applicable operating agreement], in lieu of a meeting:

[AGENDA ITEM / ACTION HEADING — if multiple resolutions]

WHEREAS, [background recital — one or two sentences stating the relevant facts
and why the board is being asked to act]; and

WHEREAS, [additional recital if needed]; and

NOW, THEREFORE, BE IT RESOLVED, that [the specific action being approved,
in precise language — name names, state amounts, reference the specific
agreement or instrument where applicable];

RESOLVED FURTHER, that [any related or implementing resolution — e.g., the
specific officers authorized to sign documents, the authority granted];

RESOLVED FURTHER, that the officers of the Company are, and each of them
hereby is, authorized and directed, in the name and on behalf of the Company,
to take all actions and to execute and deliver all documents, instruments,
certificates and agreements as such officers may deem necessary or appropriate
to carry out the intent and purposes of the foregoing resolutions; and

RESOLVED FURTHER, that any actions previously taken by any officer of the
Company in connection with the foregoing are hereby ratified, confirmed and
approved in all respects.

[Repeat WHEREAS / RESOLVED block for each additional action if multi-resolution consent]

This Written Consent may be executed in one or more counterparts, each of
which shall be deemed an original and all of which together shall constitute
one and the same instrument. Electronic signatures shall be deemed original
signatures for all purposes.

[SIGNATURE BLOCKS — one per required signatory]

_______________________________
[Director Name]
[Title, if applicable]
Date: _______________

[Repeat for each director / committee member]
```

### 決議起案ノート

- **正確に。** 曖昧な決議は DD で問題を生む。「取引を承認」は有用でない。「[Buyer] と [Company] 間の [date] 付け Asset Purchase Agreement を、本書面決議に Exhibit A として添付された形式で実質的に承認」が有用。
- **授権された署名者を名指す。** 特定役員が特定事項の権限を必要とする場合、「officers」と言わない。名指す。
- **別紙を参照。** 文書が承認される場合、それを別紙として添付し、決議で参照。書面決議は特定性のみが有用性を生む。
- **ハウス言語に正確にマッチ。** "RESOLVED, THAT" vs "BE IT RESOLVED" vs "RESOLVED" — precedent またはシード文書にあるものを使う。書面決議内でフォーマットを切り替えないこと。

---

## Step 4: incorporation 州の書面決議ルールを確認

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` で incorporation 州を確認。ドラフト前にその州の written-consent 要件を調査:

- 取締役会書面決議に全会一致が必要、またはより低い閾値が許容されるか?
- 非署名取締役への通知が必要? どのタイミングで?
- 非署名株主への通知が必要(株主書面決議の場合)? どのタイミングで?
- 有効な署名形式(wet ink、電子、counterparts)?
- 定款または bylaws がデフォルトルールを上書きするか — 例:より高い署名閾値、異なる通知ウィンドウ、書面決議で取れるアクションの制限。

controlling 条文セクションと依拠する charter / bylaw 規定を引用。最新性検証 — 州コーポレートコードは定期改正。確認していないルールを述べるのではなく、不確実性を弁護士検証用にフラグする。

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` がこれらの質問にハウスポジションを記録していれば適用し、依拠する legal backstop を記録。確認(またはフラグ)した内容を要約する短い「State-law notice」ブロックを出力に追加、ユーザーが疑問のまま残らないように。

---

## Step 4.5: Consequential-action gate(書面決議の execute)

**出力に進む前に:** `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の `## Who's using this` を読む。Role が **Non-lawyer** の場合:

> 書面決議の execute は法的帰結を持つ — エンティティを bind し、コーポレート記録になる。弁護士とレビュー済みですか? Yes なら進める。No なら、彼らに持って行くブリーフ:
>
> - アクションが何か(決議)
> - 分析が見つけたもの(州法通知、署名閾値、フラグされた利益相反)
> - オープンな質問(上記で弁護士検証用にフラグされたもの)
> - 何が悪い結果になり得るか(無効な書面決議、信認義務違反、署名瑕疵、利益相反が適切に処理されない)
> - 弁護士に聞くこと(これが正しい vehicle か、欠けている recitals はあるか、定款 / bylaws はこのアクションの書面決議を許容するか)
>
> 弁護士、ソリシター、バリスター、その他認可された法律専門家を見つける必要があれば:あなたの専門規制者(米国の州弁護士会、英国(イングランド・ウェールズ)の SRA / Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの Law Society、または当該法域の同等機関)に紹介サービスを問い合わせてください。

このゲートを明示的な Yes なしに通過した最終署名準備ドラフトを生成しないこと。リサーチ、フォーマット抽出、弁護士レビュー用 DRAFT マーク付きは可。

---

## Step 5: 出力

生成:

1. **書面決議ドラフト** — 完全、レビュー・配布可能。execute された書面決議自体はコーポレート記録で特権ではない;配布される書面決議には work-product ヘッダーを適用しない。下記の起案ノート、署名者トラッカー、分析は work product — `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` `## Outputs` から work-product ヘッダーを冒頭に付与(ユーザー役割により異なる — `## Who's using this` 参照):

   ```
   [WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]
   ```

2. **署名者チェックリスト:**
```
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

SIGNATORY CHECKLIST — [Action] — [Date]

Required signatories (unanimous consent required):
□ [Director Name 1]
□ [Director Name 2]
□ [Director Name 3]
[etc. — pulled from board composition in `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md`]

Conflict disclosures:
[None / [Director Name] has a disclosed interest — confirm whether recusal or disclosure is appropriate]

State law notice: [confirmed-rule-for-state-of-incorporation / confirm]
```

3. **レビュー用プロンプト:**
```
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

BEFORE CIRCULATING — check:
□ Resolution language precisely describes the action (no vague approvals)
□ Correct effective date
□ All required exhibits attached and referenced
□ Authorised signatories named correctly
□ Any director conflicts disclosed or resolved
□ For major actions: outside counsel has reviewed
```

4. **ドラフトの最終ノート — 配布前に追加。** 書面決議ドラフトに別途 pre-execution note として prepend、書面決議が署名される前に剥がす:

> これは弁護士レビュー用のドラフトであって、execute された書面決議ではありません。execute はエンティティを bind し、コーポレート記録になります — 認可された弁護士が必要に応じてレビュー・編集し、出る前に専門責任を負います。レビューなしで署名のため配布しないでください。

---

## 本スキルがしないこと

- アクションが法的に取締役会承認を要するかを判断しない — その判断は弁護士に属する。
- 取締役信認義務や利益相反の解決についてアドバイスしない — 利益相反をフラグ、弁護士が処理。
- 重要取引の外部弁護士レビューを置き換えない — スコープ警告は本物でボイラープレートではない。
- 書面決議を配布しない — 出力は弁護士がレビューし自身のプロセスで送るためのもの。
- 返却された署名を追跡しない — 署名者チェックリストは出発点;署名追跡は手動またはあなたのドキュメント管理プロセスで処理。
