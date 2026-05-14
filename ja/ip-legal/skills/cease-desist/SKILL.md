---
name: cease-desist
description: >
  Draft a cease-and-desist letter (send mode) or triage one you received
  (receive mode). Use when asserting your rights against an infringer with a
  demand letter calibrated to your enforcement posture, or when an incoming
  C&D needs triage into a structured options memo with a recommendation.
  警告状(Cease & Desist)を作成(send モード)、または受領した警告状をトリアージ
  (receive モード)。エンフォースメント・スタンスにキャリブレーションされた要求状で
  侵害者に権利を主張するとき、または受領した C&D を構造化された選択肢メモと推奨に
  トリアージする必要があるときに使用。
  日本語トリガー: 「警告状作成」「C&D ドラフト」「警告状トリアージ」「警告状の対応」「セース&デシスト」。
argument-hint: "<--send | --receive> [コンテキスト、相手方、または受領レターのパス]"
---

# /cease-desist

2 つのモード。一つ選んでください:

- `/ip-legal-ja:cease-desist --send` — エンフォースメント・スタンスにキャリブレーションされた警告状を作成。送付前にラウドゲートが動作。
- `/ip-legal-ja:cease-desist --receive` — 受領した C&D をトリアージ。推奨付きの選択肢メモを生成。

## 指示

1. **プラクティスプロファイルを読む.** `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` をロード。`[PLACEHOLDER]` マーカーがあるか存在しない場合は停止して: 「このプラグインを有用に使うにはセットアップが必要です。`/ip-legal-ja:cold-start-interview` を実行してください — C&D スキルはエンフォースメント・スタンス、承認マトリックス、プラクティスエリア構成に依存し、いずれもまだ設定されていません」。

2. **案件ワークスペースをチェック.** `## Matter workspaces` のとおり: `Enabled` が `✗` ならスキップ — スキルはプラクティスレベル・コンテキストを使う。有効でアクティブ案件がなければ問います: 「これはどの案件向け? `/ip-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください」。

3. **`$ARGUMENTS` でディスパッチ:**
   - `--send` が含まれる場合: send モードを実行(下記)。identify-the-right、identify-the-conduct、identify-the-relationship、identify-the-demand、calibrate-to-posture、draft、pre-delivery gate を歩く。
   - `--receive` が含まれる場合: receive モードを実行(下記)。受領レター(パスまたは貼り付けテキスト)を求め、評価、エクスポージャー特定、選択肢提示、トリアージ・メモを書く。
   - フラグなしの場合: 一度尋ねる — 「警告状を送りますか(主張する側)、それとも受領したものをトリアージしますか(防御する側)?」 — その後ディスパッチ。

4. **ゲートを尊重.** send モードでは、最終ドラフトがディスクに書かれる前にラウドゲートが動作。スキップしないこと。

5. **承認マトリックスを尊重.** `## Enforcement posture → Approval matrix` の C&D 行から承認者を取得。自動エスカレーションを取得。両方をゲートで表面化; 隠さないこと。

6. **適切な場所でハンドオフ.** receive モードで推奨が断固として返答することなら、レスポンス・コンテキストを事前投入した `/ip-legal-ja:cease-desist --send` に連鎖を提案。推奨が DJ アクションや TTAB 取消で先手を打つことなら、プラクティスプロファイルの IP 訴訟行に従って外部弁護士へエスカレート — ドラフトしないこと。

## 例

```
/ip-legal-ja:cease-desist --send
/ip-legal-ja:cease-desist --receive ~/Downloads/incoming-cd-acme.pdf
/ip-legal-ja:cease-desist
```

## 注記

- 送付される C&D は work-product ヘッダーを運びません。内部ドラフト、pre-send ブリーフ、トリアージ・メモは運びます。
- 商標権は地域的; ドラフトはプラクティスプロファイルの `Registered in:` フットプリントで宣言された法域を仮定。conduct または相手方がそれ以外にあれば、ドラフト前にフラグ。
- 各 `[CITE:___]` は citator 実行まで未検証。ソース帰属タグはドラフト上に残る。
- 非弁護士ユーザーは、ゲートがクリアする前に弁護士との会話のための 1 ページ・ブリーフを取得。

---

## 目的

警告状は法的権利を主張し、誰かに何かをやめるよう要求します。IP プラクティスが送る・受領する最も帰結のあるレターの 1 つです。送付は訴訟への第一歩 — 受領者は自分の選ぶフォーラムで確認判決訴訟を提起でき、過剰または悪意ある主張は送付者に対して使われうる。受領は時計を始め、決断を強います。本スキルは判断にふさわしいガードレールで両側を扱います。

2 つのモード:

- `--send` — あなたは主張中。スタンスにキャリブレートされた C&D を作成、配信前にゲート。
- `--receive` — あなたは防御中。受領レターをトリアージ、選択肢メモを作成、正当なら案件作成にルート。

ユーザーがフラグを渡さない場合、一度尋ねる: 「警告状を送りますか(主張する側)、それとも受領したものをトリアージしますか(防御する側)?」

> **External deliverable (send mode):** 作成された C&D は相手方に送付されます。送付されるレターには `PRIVILEGED & CONFIDENTIAL — ATTORNEY WORK PRODUCT` ヘッダーを含めないこと。内部ドラフト、pre-send ブリーフ、トリアージ・メモはプラグイン設定 `## Outputs` のとおりヘッダーを保持します。

## 法域前提

商標権は地域的 — 米国登録は travel しない。著作権は Berne 多国間だが、執行は法域固有で、法定救済(米国 §504 法定損害賠償を含む)はローカル法に依存。本スキルは案件またはプラクティスプロファイルの `Registered in:` フットプリントで宣言された法域を仮定。侵害行為、相手方、またはフォーラムがそれ以外にあれば、フラグ — ドラフトは書かれたとおりには適用されないかもしれない。

## コンテキストをロード

- `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` → `## Enforcement posture`(スタンス、C&D トリガー、ソフトレター基準、承認マトリックス、自動エスカレーション)、`## IP practice profile`(プラクティスエリア構成、登録法域、外部弁護士ロスター)、`## Outputs`(work-product ヘッダー、Role)、`## Who's using this`(Role — 弁護士 vs 非弁護士)
- プラクティスプロファイルのシード文書で参照される C&D テンプレートまたはエンフォースメント・プレイブック — それを読み、構造に合わせる
- **Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(インハウス・ユーザーのデフォルト)なら、案件機構をスキップ — スキルはプラクティスレベル・コンテキストを使う。有効でアクティブ案件がなければ問う: 「これはどの案件向け? `/ip-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください」。アクティブ案件の `matter.md` を案件固有オーバーライド(例: スタンス・オーバーライド、承認者オーバーライド)用に読む。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/` に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まないこと。

## Send モード — C&D の作成

### Step 1: 権利を特定

1 バッチで尋ねる:

> どの IP 権利を主張しますか?
>
> - **Trademark** — 登録済みか? どこで(USPTO、EUIPO、UKIPO、national)? Reg 番号とクラス? それともコモンローのみ(first-use 日、地理的範囲)?
> - **Copyright** — 登録済みか? タイトル、登録番号、日付? 未登録か(注記: 米国訴訟は提出クレームに登録を要求; 法定損害賠償と弁護士費用は侵害前登録を要求)?
> - **両方** — それぞれ特定。

各権利を記録。登録済み権利は番号で引用。コモンロー権利は first-use 証拠段落を取得。未登録著作権はフラグ: 「未登録米国著作権について先に登録せず提訴できないかも — レターが訴訟を脅す前に `[SME VERIFY]`」。

### Step 2: 行為を特定

> 侵害行為を形容詞ではなく具体的に記述:
>
> - **誰** がやっているか — 法人名、個人、プラットフォーム・ハンドル?
> - **何を** — 被告マーク、被告コピー、被告製品? サンプルを添付または記述。
> - **どこで** — ウェブサイト URL、マーケットプレイス・リスティング、物理小売、ソーシャルメディア?
> - **いつから** — 最初に観察された日、文書化できる最も古い使用日?
> - **証拠** — スクリーンショット、領収書、ウォッチサービス・ヒット、顧客混同レポート?

事実は具体的に。「You sold product X on [URL] bearing the mark [Y] on [date]」 は 「You have been infringing our rights」 を上回る。形容詞は薄い記録を告白する。

### Step 3: 関係を特定

> 私たちと受領者の関係は?
>
> - **Competitor**(直接または隣接) — 標準スタンス適用
> - **Reseller / channel partner** — トーンを調整; ソフトレター・パスを検討
> - **Former licensee / ex-employee / former partner** — 契約条項が適用; 引用
> - **Stranger / random infringer** — 標準
> - **Current customer / partner** — プラクティスプロファイル別自動エスカレーション; ドラフト前にフラグ

これはトーン、承認者、エスカレートせずにドラフトするかどうかを変える。

### Step 4: 要求を特定

> クライアントは実際に何を望むか?
>
> - **Stop** — 侵害的使用を停止
> - **Account** — 売上、利益、数量を報告(損害賠償ベースライン用)
> - **Destroy** — 侵害的在庫を破棄またはリコール
> - **Damages** — 金銭和解
> - **Transfer / assign** — ドメインを移転、アカウントを引き渡し、被告マーク or 著作権を譲渡
> - **Public correction** — 攻撃的コンテンツのテイクダウン、公的声明
> - **Confirm in writing** — 日付による遵守の誓約

実際の救済を選ぶ。要求は害に比例的でなければならない — 過剰な要求は、案件が訴訟になれば悪意の証拠。

**Channel-takedown 並列パス(マーケットプレイス侵害).** 被告行為がマーケットプレイス(Amazon、Etsy、eBay、Alibaba、TikTok Shop、AliExpress、Walmart Marketplace、Shopify-hosted storefront)上なら、プラットフォームのブランド保護 / IP 侵害報告パスを、C&D や訴訟を要しないより速く安い並列トラックとしてフラグ:

- **Amazon Brand Registry**(商標と著作権テイクダウン、模倣品除去)
- **Etsy IP Infringement reporting**(商標 / 著作権 / 特許フォーム)
- **eBay VeRO**(Verified Rights Owner プログラム)
- **Alibaba IPP**(IP Protection Platform)
- **TikTok Shop IP Protection**
- **Shopify DMCA / trademark reporting**

マーケットプレイス・テイクダウンはしばしば数日で解決; C&D は侵害者に交渉中に在庫を売り切る時間を与える。2 つのパスは相互排他ではない — 行為がマーケットプレイスベースなら両方の提出を推奨、C&D は off-platform 行為(DTC サイト、卸売、ソーシャル、物理小売)をカバー(プラットフォーム報告は届かない)。並列パスが提出済み、キュー、または却下(理由付き)を pre-send ブリーフで注記。

### Step 5: スタンスにキャリブレート

`## Enforcement posture` → `Default posture:` を読み、適用:

- **Aggressive** — ファームレター、短い期日(しばしば 7〜14 日)、明示的な帰結文言(訴訟、法定損害賠償、費用、差止)、和解的軟化なし
- **Measured** — ファームだがプロフェッショナル、標準期日(14〜30 日)、theatrics なしで帰結を注記、応答があれば議論にオープン
- **Conservative** — ソフトレター・フレーミング、長い期日または期日なし、「議論したい」オープニング、帰結文言は控えめまたは不在

`When we send a C&D`、`When we send a soft letter first`、`When we just file` も読む。事実がプラクティスプロファイル別にソフトレターまたは直接提訴を示唆するなら、ドラフト前にフラグ: 「あなたのエンフォースメント・スタンスでは、このパターンは [ソフトレター / 提訴] にマッチします。それでも C&D が欲しいか、[代替] を希望しますか?」

`matter.md` の matter-level オーバーライドはプラクティスデフォルトを上回る。

### Step 5.5: 相手方デューデリ — 必須前提条件

**ドラフト前に、相手方デューデリを実行し、結果をユーザーに提示します.** これは「相手方が大きく見えるなら」の条件付きではない。すべての C&D 主張は、受領者が*誰*かにキャリブレートされた DJ / 費用転嫁 / 悪意エクスポージャーを運ぶ。本スキルはユーザーがデューデリを見て、依然この戦いを選びたいと確認するまで、C&D をドラフトしない。

ユーザー・サインオフのために 1 ブロックで以下を集めて提示:

- **法人** — 正確な企業名、設立州/国、登録代理人、`d/b/a` の別名。USPTO / EUIPO オーナーシップ・レコード; 州 Secretary of State ビジネス検索; 公的会社ファイリング(あれば)。ソースが未確認なら `[SME VERIFY]` でフラグ。
- **規模とリソース** — 公開なら大まかな従業員数、収益帯、スタートアップなら資金調達、子会社なら親会社。公的ソース(LinkedIn 従業員数、プレス、Crunchbase、SEC ファイリング)。規模が判定できないなら honest にフラグ。
- **IP ポートフォリオ** — 隣接クラスで登録マーク、特許、著作権を保有しているか? 自身の IP ポートフォリオを持つ相手方は、(a) スタンスを理解する、(b) カウンター主張する、(c) DJ を提起する、可能性が高い。被告法人と関連会社に対する USPTO TESS / TSDR クイック検索。
- **訴訟履歴** — PACER / Court Listener のクイックパスで、原告または被告としての過去の IP 訴訟。繰り返し提訴者または DJ 好きの相手方は計算を変える。業界での過去の C&D キャンペーンをフラグ。
- **代理人** — 既知の外部 IP 弁護士はいるか? 事務所、過去のファイリングから識別可能なら lead パートナー。「ファイル上の代理人なし」自体がデータポイント。
- **DJ 原告リスク・スタンス** — 規模、IP ポートフォリオ、訴訟履歴、代理人、フォーラムを踏まえ: この相手方は C&D を自身の選ぶフォーラムで DJ を提起する招待として歓迎しそうか? 1 文の理由で高 / 中 / 低でフラグ。
- **関係リスク** — 彼らのカスタマーか、投資家を共有するか、潜在的買収者またはパートナーか? 「Not a customer」の確認はプラクティスプロファイルから取得; それ以外はフラグ。

これをドラフトの BEFORE にチャット内の短いメモとして提示:

```
## 相手方デューデリ — [Entity Name]

- **Entity:** [名前、設立州、ある場合の親会社]
- **Size:** [従業員数帯、収益帯、資金調達ステージ] — [ソース、該当時 `[SME VERIFY]`]
- **IP portfolio:** [隣接クラスの登録マーク / 特許 / 著作権 — または「見つからず」]
- **Litigation history:** [原告または被告としての過去の IP 案件 — または「クイックパスで見つからず」]
- **Counsel:** [既知の外部 IP 弁護士 — または「特定されず」]
- **DJ-plaintiff risk:** [高 / 中 / 低 — 理由]
- **Relationship risk:** [カスタマー / 投資家 / パートナー / 買収者のオーバーラップ — または「なし」]

**これがトリガーする自動エスカレーション**(プラクティスプロファイル `## Enforcement posture` → Automatic escalations より):
- [このデューデリが表面化する各トリガーをリスト]

**ドラフト前に確認:**
- 上記デューデリを踏まえ、この相手方に C&D を進めたいですか?
- 自動エスカレーションのいずれかが該当しますか? Yes なら、プロファイルで名指された承認者がドラフトの後ではなく前にサインオフします。
```

**ユーザーがデューデリ・ブロックに関与するまで、Step 6(ドラフト)に進まないこと.** 空の「ok」は確認なしより悪い — 押し返します: 「ドラフト前に — 計算を変えるものはデューデリにありますか? 規模、過去訴訟、彼らの代理人、関係?」

デューデリがプラクティスプロファイルの自動エスカレーション・リスト(カスタマー、より大きな相手方、特許案件、プレス注目、など)のいずれかを表面化したら、プロファイルで名指された承認者にルート — レビューアの代わりにドラフトせず、承認者が進むことにサインオフするまで待つ。

クリティカルなデューデリ項目に答えられない場合(例: 法人が確認できない、規模が不明で相手方がいかなる公開登録上にもない)、そう言ってフラグ: 「利用可能なソースから [法人 / 規模 / 代理人] を確認できません。これをお持ちですか、それともパラリーガルまたは OC が確認を実行するまで一時停止しますか?」

### Step 6: ドラフト

ドラフトの構造:

1. **送付者 / レターヘッドと日付**
2. **受領者ブロック**
3. **Re: 行** — 簡潔、特権戦略を明かさない。`Re: Unauthorized use of [MARK] (US Reg. No. [•])`
4. **オープニング** — 送付者、権利、登録(あれば)、レターの事実を特定
5. **権利** — 商標: reg 番号、クラス、first-use 日、登録ステータス; 著作権: 登録番号、タイトル、年、著作物の記述; コモンロー: first-use 日、地理的範囲、acquired distinctiveness の証拠
6. **侵害行為** — 具体的に: 誰、何、どこ、いつ、証拠
7. **法的根拠** — 該当時 `[CITE: Lanham Act §32 / §43(a) / 17 U.S.C. §501 / state UCL / contract §]`
8. **要求** — 番号付き、具体的、比例的
9. **期日** — カレンダー日付、確認方法
10. **不遵守の帰結** — スタンスにキャリブレート
11. **保全要求** — 被告行為に関連する文書、コミュニケーション、メタデータ
12. **権利の留保** — 「without waiver of any claims or remedies, whether at law or in equity」
13. **署名ブロック** — プラクティスプロファイル別の承認者

**ドラフト・ルール:**

- **形容詞より具体性.** 日付、URL、reg 番号、サンプル。形容詞はドラフト者が事実が薄いことを伝える信号。
- **過剰主張なし.** マークが 1 クラスで登録され、被告使用が別のクラスならそう言う — 登録が両方をカバーするふりはしない。過剰な C&D は悪意の証拠で §43(a)(1)(B) または Rule 11 エクスポージャーを支持しうる。
- **検証されていない限り引用はプレースホルダー.** `[CITE: Lanham Act §32, 15 U.S.C. §1114]` は、ユーザーが引用を提供するか調査ツールが返さない限りプレースホルダーのまま。各引用にソースをタグ — `[Westlaw]`、`[user provided]`、`[model knowledge — verify]`、`[web search — verify]`。タグを決して剥がさないこと。
- **帰結文言はスタンスに合致.** Aggressive → 具体的な救済を脅す(差止、15 U.S.C. §1117 / 17 U.S.C. §504 下の法定損害賠償、弁護士費用)。Measured → 「we reserve all rights」。Conservative → 「we'd like to discuss before considering further steps」。
- **法域固有のフック** — 米国なら、ドメイン案件で Anti-Cybersquatting(15 U.S.C. §1125(d))、未登録マークで §43(a)、登録前タイミングで §504(c) に注意。非米国: フォーラムをフラグし、ドラフトに外国アソシエイト・レビューが必要かもしれないと注記。

### Step 7: 配信前のラウドゲート

ドラフトをチャット内で提示するか .docx を書く前に、このゲートを文字どおり表示します。**ユーザーは関与しなければならない** — 空の承認はゲートなしより悪い。

```
┌─────────────────────────────────────────────────────────────┐
│  BEFORE THIS DRAFT GOES ANYWHERE                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  This is a draft for attorney review — not a letter to      │
│  send. Sending a cease-and-desist letter is an assertion    │
│  of legal rights with real consequences:                    │
│                                                             │
│  • It can trigger a declaratory judgment action in a        │
│    jurisdiction of the recipient's choosing. A well-funded  │
│    recipient can use a C&D as an invitation to pick a       │
│    hostile forum.                                           │
│                                                             │
│  • Overbroad or bad-faith assertions can be used against    │
│    the sender — §43(a)(1)(B) claims, Rule 11 sanctions,     │
│    attorneys' fees under the Lanham Act / Copyright Act.    │
│                                                             │
│  • It starts a dispute that may not settle cheaply.         │
│                                                             │
│  Confirm before the letter leaves:                          │
│                                                             │
│    1. The rights asserted are valid — registered (pulled    │
│       from the register, not assumed) or solidly common     │
│       law with evidence of acquired distinctiveness.        │
│    2. The claim is colorable — a reasonable practitioner    │
│       would make it on these facts.                         │
│    3. The demand is proportionate — we are asking for       │
│       relief the conduct warrants, not everything.          │
│    4. Whoever has authority to start a fight has approved.  │
│    5. Counterparty diligence (Step 5.5) was presented       │
│       and confirmed — entity, size, IP portfolio, prior     │
│       litigation, counsel, DJ-plaintiff risk, and           │
│       relationship risk. Not conditional. Required.         │
│                                                             │
│  Approver per your practice profile: [approver name/role    │
│  from Enforcement posture → Approval matrix → C&D row]      │
│                                                             │
│  Automatic escalations that apply here: [list any from the  │
│  practice profile that this matter triggers — customer,     │
│  bigger counterparty, patent, press-attracting, etc. —      │
│  surfaced in Step 5.5 diligence]                            │
│                                                             │
│  Parallel-path status (marketplace conduct): [filed /       │
│  queued / declined — from Step 4. "Not applicable" if       │
│  conduct is not on a marketplace.]                          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

ユーザーが非弁護士(`## Who's using this` 別)なら、追加:

> C&D の送付は、受領者の応答を超える法的帰結を持ちます — 権利の積極的主張であり、あなたに対して使われうる。弁護士とこれをレビューしましたか? いいえなら、彼らに持参するブリーフ: [1 ページ・サマリーを生成: 当事者、主張する権利、侵害行為、要求、スタンス、上記のフラグされたリスク、何が悪い方向に進みうるか、弁護士への具体的な質問]。
>
> あなたの法域で認可された弁護士、ソリシター、バリスター、他の認可された法専門家を見つけるなら: あなたの専門職規制当局のリファラル・サービスが最速の出発点(米国の州弁護士会、英国イングランド&ウェールズの SRA/Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの法曹会、またはあなたの法域の同等機関)。ABA IP セクションと州 IP 協会(米国)、CIPA/ITMA(UK)、他地域の同等機関は、商標および著作権実務家のリファラル・ロスターを維持。

ゲートへの明示的関与なしに .docx を書いたり、ドラフトを ready とマークしたりしないこと。

### Step 8: 出力

**主要:** `<matter-folder>/cease-desist/<slug>/draft-v<N>.docx`(またはプラクティスレベルで `cease-desist/<slug>/draft-v<N>.docx`)。`docx` スキルを使用。上記のドラフト構造に基づくレター形式。送付されるレターから work-product ヘッダーを剥がす。

**チャット内:** ディスクに書く前にドラフトをプレーンテキストで表示してレビュー。ディスクへのコミット前に反復。

**レビューアー向けクロージング・ノート**(チャット内プレビューにのみ追加、.docx から剥がす):

> これは弁護士レビュー用の警告状ドラフトであり、送付準備の整ったレターではありません。送付は配信前ゲートで記述されたとおりの帰結を伴う法的権利の主張です。認可された弁護士がレビュー、編集し、送付前に専門職責任を取ります。このドラフトをレビューなしで送付しないこと。

**引用検証.** 各 `[CITE:___]` および提供された典拠から運ばれる各引用は、citator を通すまで未検証。送付前に、各引用が法的調査プラットフォームで good law であることを検証する。送付された主張レターでの捏造または誤引用された引用は、専門職責任エクスポージャー。ソース帰属タグを保存 — `[Westlaw]`、`[CourtListener]`、`[Descrybe]`、`[user provided]`、`[model knowledge — verify]`、`[web search — verify]` — `verify` フラグのタグが最初にチェックされる。

**No silent supplement.** 設定された調査ツールが、ドラフトに必要な典拠について少数のまたはゼロの結果を返す場合、見つかったものを報告して停止。尋ねずに Web 検索やモデル知識から backfill しないこと。選択肢を提示 — クエリーを広げる、別ツールを試す、タグ付きで Web 検索を受け入れる、プレースホルダーを残す — ユーザーが決める。

**Post-send チェックリスト.** ドラフトが承認された後、`<matter-folder>/cease-desist/<slug>/checklist.md` を書く: 承認者による最終読み、すべての `[VERIFY]` 解決、すべての `[CITE]` 記入と検証、送付レターから特権マーキング除去、承認者のサイン、配信方法の実行、配信証明保持、遵守期日のカレンダー登録、応答なしの場合のエスカレーション・プラン、まだなければ `matters/` に案件作成。

## Receive モード — 受領した C&D をトリアージ

### Step 1: レターを読む

抽出:

- **送付者** — 法人、署名者、ある場合の外部弁護士
- **受領者** — 当方のどの法人/人
- **配信方法と日付**
- **主張権利** — 商標(reg 番号? 法域?)、著作権(登録? タイトル?)、両方、その他
- **被告行為** — 我々が何をしていると彼らが言うか
- **法的根拠** — 引用された制定法、契約条項、理論
- **要求** — 何を望むか; 期日は記述されているか?
- **脅し** — 何をすると言うか
- **トーン** — ファーム / ソフト / 焦土; カウンセル署名は通常 seriousness のシグナル

### Step 2: 主張を評価

法的意見ではない — 構造化された読み取り:

- **権利の有効性.** 主張された登録は本物でアクティブか?(USPTO TSDR、EUIPO eSearch、Copyright Office レコードをチェック — ドーマントまたは効力なしに見えるものをフラグ。)コモンロー・クレームについては、彼らが実際に何の証拠を引用するか?
- **混同 / 類似性 / 侵害の妥当性.** 主張された事実上、これは colorable クレームか、それとも引き伸ばしか? 商標について: 混同のおそれは多要因テスト(Polaroid / AMF / Sleekcraft、巡回区により — `[SME VERIFY]` フォーラムのテスト)に依存。著作権について: アクセス + 実質的類似性。クレームが最も弱いところをフラグ。
- **過剰広がり.** 行為が warrant する以上を要求しているか?(再ラベルだけをカバーするはずの登録でマークの移転を要求? 1 チャネルだけが権利に触れたのに全売上を要求?)過剰な要求はレバレッジを弱め、§43(a)(1)(B) / unclean-hands カウンターを強化。
- **タイミング.** Laches、出訴期限、登録タイミング(米国著作権の法定損害賠償用) — レターの表面の日付問題をフラグ。
- **フォーラム.** 彼らはどこで訴えるか? フォーラムは契約で固定されているか(stranger IP 紛争では最も unlikely)? 我々に DJ の機会はあるか?

### Step 3: 我々のエクスポージャーを評価

- **我々は実際に侵害しているか?** Honest な見方。レコードは何を示すか?
- **簡単に止められるか?** 遵守コスト vs 戦闘コスト。
- **送付者はトロールか実物の主張者か?** 繰り返し原告? 戦う意思で知られている? 比較使用での最近の C&D キャンペーン? 時間が許せば公的ドケットをチェック。
- **この紛争を超えて何が懸かっているか?** ブランドエクイティ、カスタマー関係、類似のインバウンド C&D への先例。

### Step 4: 選択肢

トレードオフ付きで 4〜5 つの選択肢を提示:

**A — 早急に遵守**
- いつ: クレームが colorable、遵守が安く、戦いの価値がない
- トレードオフ: 後で彼らが指し示しうる譲歩を確立; 将来の主張を勇気付けるかもしれない
- 次のステップ: 遵守を書面で確認(狭く)、広い理論に譲歩しない

**B — 交渉**
- いつ: それを解決する middle-ground のビジネスディール(ライセンス、共存、リブランディング・タイムライン)がある
- トレードオフ: 時間を要する; 和解コミュニケーション・スタンスに注意(FRE 408 または州の同等; 保護はラベル単独ではなく実質とコンテキストから付着)
- 次のステップ: ホールディング・レター + 交渉トラックを開く

**C — 断固として返答(却下)**
- いつ: 彼らのクレームが弱い、過剰、または事実的に誤り; 訴訟せずにこれを閉じたい
- トレードオフ: 立場をロックイン; もしクレームが実際 colorable なら、応答は exhibit に
- 次のステップ: 応答レターをドラフト — 応答として再フレームした `/ip-legal-ja:cease-desist --send` を通すことを検討

**D — 無視(と保全)**
- いつ: クレームが軽率、送付者に提訴能力がないように見える、期日に法的帰結なし
- トレードオフ: 沈黙は一部の文脈で non-denial として使われうる; とにかくリーガルホールドは必要; 提訴が続くリスク
- 次のステップ: matter-level プロセス経由でリーガルホールド発行; 要求をログ; 進む

**E — DJ アクションまたは取消で先手**
- いつ: 真のビジネス不確実性に直面、クレームが弱く、我々のフォーラムから利益を得る
- トレードオフ: 我々は攻撃に出る; 予算とリーダーシップ・サインオフが必要; 今や訴訟あり
- 次のステップ: プラクティスプロファイル別に外部弁護士にエスカレート、ドラフトしない

**F — 彼らのマーク(TTAB)または著作権登録の無効化を提起**
- いつ: 彼らの権利自体が脆弱で、ボードから道具を外したい
- トレードオフ: 遅い、高価、公的; 紛争自体とは別
- 次のステップ: 外部弁護士にエスカレート

理由 2 文で 1 つを推奨。なぜか具体的に。

### Step 5: 期日トリアージ

- 彼らの記述された期日 — 注記するが、法的に我々を拘束しない(特定の制定法が歯を与えない限り)。
- 我々の内部判断期日 — 通常、記述された期日からドラフト・レビュー・承認に十分な時間を引いたもの。カレンダー上でフラグ。
- 法的期日 — 任意の underlying クレームの出訴期限、契約上のキュア期間、フォーラム固有のタイムライン。

記述された期日を完全に無視するのは選択であり、デフォルトではない。提訴は通常、期日ではなく沈黙の後に続く。

### Step 6: トリアージ・メモを書く

出力: `<matter-folder>/cease-desist/inbound/<slug>/triage.md`(案件ワークスペースがオフならプラクティスレベル)。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — Role により異なる; `## Who's using this` 参照]

[PRIVILEGE INHERITANCE BLOCK — Role と案件タイプ別に選択; テンプレ下のガイダンス参照]

# C&D Received — Triage

> **READ FOR TRIAGE, NOT OPINION.** これは intake スキャンと選択肢分析であり、法的メリット意見ではありません — 下記評価はカウンセルのルーティングと応答の判断を支援する構造化された読み取り。引用された各制定法、ルール、判例は SME 検証のためフラグ; メリット判断はカウンセルのもので、本スキルのものではない。

**Slug:** [slug]
**Received:** [YYYY-MM-DD]
**Received by:** [法人 / 人]
**Incoming file:** [path]

## 主張

**送付者:** [法人、署名者、代理人]
**主張権利:** [trademark / copyright / both — 具体的事項、reg 番号、法域付き]
**被告行為:** [彼らの版、1 段落]
**要求:** [list — 具体的な依頼]
**彼らの記述された期日:** [date]
**トーン:** [firm / soft / scorched-earth]

## 権利の有効性

[主張された登録 — 登記所に対し `[SME VERIFY]`; コモンロー・クレームは引用された証拠に照らして評価]

## 引用された法的根拠

[各引用にインライン・タグ `[SME VERIFY: applicability / currency / jurisdiction]` とソース `[Westlaw / user provided / model knowledge — verify / web search — verify]`。独立チェックなしでここの引用に依拠しないこと。]

## 妥当性評価

- **事実上の混同 / 類似性 / 侵害:** [読み取り]
- **過剰広がり:** [読み取り]
- **タイミング問題(laches、SoL、登録タイミング):** [読み取り]
- **フォーラム:** [彼らの likely フォーラム; DJ 機会]

## 我々のエクスポージャー

- **実際に侵害?** [honest な読み取り]
- **遵守コスト vs 戦闘コスト:** [読み取り]
- **送付者の信頼性:** [troll / 実物の主張者 / 繰り返し原告 — 公的ドケット証拠付き]
- **付随的に懸かるもの:** [ブランド、カスタマー、先例]

**トリアージ評価:** [substantial / debatable / weak / frivolous] — *ルーティングのための構造化された読み取り、メリット意見ではない; `[SME VERIFY]`*

## 選択肢

### A. 早急に遵守
[理由、トレードオフ、次のステップ]

### B. 交渉
[理由、トレードオフ、次のステップ]

### C. 断固として返答
[理由、トレードオフ、次のステップ]

### D. 無視 + 保全
[理由、トレードオフ、次のステップ]

### E. 先手(DJ)
[理由、トレードオフ、次のステップ]

### F. 取消 / 無効化を提起
[理由、トレードオフ、次のステップ]

**推奨:** [A/B/C/D/E/F] — [2 文でなぜ] — `[SME VERIFY: counsel to confirm before executing]`

## 期日

- **彼らの記述された期日:** [date]
- **我々の内部判断期日:** [date]
- **任意の underlying クレームの法的期日:** [SoL、cure、procedural — 日付付き]

## 即時アクション

- [ ] リーガルホールド発行 — [yes/no]
- [ ] ログに案件作成 — [yes/no/TBD]
- [ ] カウンセル割当 — [誰]
- [ ] 保険テンダー — [yes/no/N-A]
- [ ] 内部エスカレーション — [誰/いつ]
```

**特権継承ブロック — Role と案件タイプ別に選択.** プラグイン設定の `## Who's using this`(Role)と案件タイプ(trademark / copyright / patent / OSS / その他)を読む。本トリアージは敵対的主張に対する first-pass メリット読み取りを記録; 実際に特権かどうかは、誰が準備し、何についてかに依存。どちらの方向で誤っても有害 — 誤った "privileged" マーキングは、譲歩として読まれる開示可能な自白を作る; 真に特権付きメモを under-mark するのは保護を放棄しうる。以下のうち正確に 1 つを挿入:

- **Role = 弁護士 / 法務担当者:**
  > **Privilege inheritance.** This triage records our first-pass merit read and response posture on an adverse assertion. It is attorney-client and/or work-product material. Do not forward, attach to an insurance tender without scrubbing, or share with counterparty. Store with privileged matter material and mark per house privilege conventions.

- **Role = 登録特許代理人、案件は USPTO 向け特許案件:**
  > **Privilege (patent agent-client).** This triage is privileged under the federal patent agent-client privilege recognized in *In re Queen's University at Kingston*, 820 F.3d 1287 (Fed. Cir. 2016), because it relates to a matter reasonably necessary and incident to the prosecution of patents before the USPTO. That privilege is narrow: it does not extend to matters outside USPTO practice. Do not forward, attach to an insurance tender without scrubbing, or share with counterparty. Bring to supervising counsel for matter-specific privilege decisions.

- **Role = 登録特許代理人、案件は特許案件ではない**(商標、著作権、OSS、営業秘密、契約、または USPTO 実務外のもの):
  > **CONFIDENTIAL — NOT PRIVILEGED.** This triage is not privileged because a registered patent agent's privilege is limited to patent prosecution before the USPTO (*In re Queen's University at Kingston*, 820 F.3d 1287 (Fed. Cir. 2016)). A trademark, copyright, OSS, or other non-patent matter falls outside that privilege. Treat this document as confidential, store it with care, bring it to counsel, and let counsel mark it. Do not forward it as a privileged document.

- **Role = 非弁護士で、登録特許代理人ではない:**
  > **CONFIDENTIAL — NOT PRIVILEGED.** This document is not privileged unless and until reviewed by a licensed attorney. Treat it as confidential; do not forward to anyone outside the legal review chain; bring it to counsel and let counsel mark it. Forwarding this document as "privileged" before an attorney reviews it does not make it so and can harm you if the matter becomes contested.

チャット内のプレゼンテーションを以下のガードレールで literal に締めくくる:

> これはトリアージ・メモで、助言ではありません。上記の強み評価はレター単独に基づく first read — あなたが教えていない事実、私が検証できない登録、または法域問題を考慮していません。返答、無視、パスへのコミット前に弁護士が評価します。

ユーザーが非弁護士なら、send モードからの「弁護士を見つける」ルーティング段落を追加。

### Step 7: ハンドオフ

推奨とユーザー確認に基づき:

- 断固として返答 → コンテキストを応答レターとして事前投入した `/ip-legal-ja:cease-desist --send` にハンドオフ(これは send モードのゲートを再びトリガー)。
- 交渉 → 案件でホールディング・レター / 交渉トラックを開始。
- 先手または取消 → プラクティスプロファイルの IP 訴訟行に従って外部弁護士にエスカレート; ドラフトしない。
- 案件作成 → なくて案件が重要なら、事前投入の `/ip-legal-ja:matter-workspace new <slug>` を提案。
- 遵守 / 無視 → 案件履歴に判断をログ; リーガルホールドを発行または確認; トリアージ・レコードをクローズ。

## 決断スタンス

プラクティスプロファイルの `## Decision posture on subjective legal calls` の通り: 侵害があるか、マークが混同的に類似か、著作物が実質的に類似か、クレームが colorable か、または送付が安全か不確実なときは、サイレントに大丈夫と決定しないこと。弁護士レビューのためフラグ、両方向に切れる要因を表面化、不確実性を注記。前提で C&D を送るのは一方通行のドア; 疑念を表面化するのは両方向のドア。

## このスキルが行わないこと

- **レターを送付する.** ドラフトのみ。承認後にユーザーが送る。
- **引用をリサーチする.** ユーザーが典拠を提供するか接続された調査ツールが返さない限り、プレースホルダーはプレースホルダーのまま。引用の発明は専門職責任エクスポージャー。
- **ゲートをバイパス.** Send モードのゲートは毎回実行。`--skip-gate` フラグでも(提供なし)、スキルはドラフトファイルにスキップをログする。
- **受領側でメリットを確定的に決定.** 評価はルーティング用の構造化された読み取り; 正式なメリット意見はカウンセルにある。
- **送付者の引用された法律を検証.** ユーザー向けにフラグ; 自律的にクレームを valid または invalid と呼ばないこと。
- **案件作成の判断をする.** 推奨を表面化; ユーザーが決める。
