---
name: takedown
description: >
  Draft a DMCA takedown notice, triage one you received, or draft a §512(g)
  counter-notice. Use when asserting copyright through a §512(c)(3) takedown
  with the fair-use and perjury gates, when an incoming takedown needs triage
  into comply / counter / engage / ignore options, or when drafting a
  §512(g)(3) counter-notice with the consent-to-federal-jurisdiction gate.
  DMCA テイクダウン通知を作成、受領したものをトリアージ、または §512(g) カウンター
  通知を作成。フェアユースと偽証ゲート付き §512(c)(3) テイクダウンで著作権を主張
  するとき、受領したテイクダウンを comply / counter / engage / ignore 選択肢に
  トリアージする必要があるとき、または連邦管轄への consent ゲート付きで §512(g)(3)
  カウンター通知をドラフトするときに使用。
  日本語トリガー: 「DMCA テイクダウン」「DMCA 通知」「カウンター通知」「テイクダウン作成」「DMCA 対応」。
argument-hint: "<--send | --respond | --counter> [コンテキストまたは受領通知のパス]"
---

# /takedown

3 つのモード。1 つを選ぶ:

- `/ip-legal-ja:takedown --send` — §512(c)(3) テイクダウン通知を作成。フェアユース・ゲート(*Lenz*) + 配信前のラウドな偽証 / §512(f) ゲート。
- `/ip-legal-ja:takedown --respond` — 誰かから受領したテイクダウンをトリアージ。選択肢: comply / counter / engage / ignore。
- `/ip-legal-ja:takedown --counter` — §512(g)(3) カウンター通知を作成。連邦管轄の admission と偽証声明にラウドゲート。

## 指示

1. **プラクティスプロファイルを読む.** `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` をロード。`[PLACEHOLDER]` マーカーがあるか存在しないなら停止して: 「このプラグインを有用に使うにはセットアップが必要です。`/ip-legal-ja:cold-start-interview` を実行してください — テイクダウン・スキルは承認マトリックスとプラクティスプロファイルに依存します」。

2. **案件ワークスペースをチェック.** `## Matter workspaces` のとおり: `Enabled` が `✗` ならスキップ。有効でアクティブ案件がなければ問う: 「これはどの案件向け? `/ip-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください」。

3. **`$ARGUMENTS` でディスパッチ:**
   - `--send` → send モードを実行(下記)。identify-the-work、identify-the-infringing-material、fair-use gate(*Lenz*)、good-faith belief、accuracy/authority、§512(c)(3) 通知をドラフト、ラウドゲートを実行、出力を書く、を歩く。
   - `--respond` → respond モードを実行(下記)。受領通知を読み、評価(ライセンス、フェアユース、欠陥、ホスト §512(g) 遵守、送付者の信頼性)、4 つの選択肢を提示、推奨、トリアージ・メモを書く。
   - `--counter` → counter モードを実行(下記)。前提条件(§512 通知への応答でテイクダウン、ミステイクまたは誤識別という good-faith 信念、連邦管轄 admission の準備、弁護士がループ内)を確認、§512(g)(3) カウンター通知をドラフト、ラウドゲートを実行、出力を書く。
   - フラグなし → 一度尋ねる: 「DMCA テイクダウンを送りますか、受領したものをトリアージしますか、それともカウンター通知をドラフトしますか?」

4. **ゲートを尊重.** `--send` と `--counter` で、最終出力が書かれる前にラウドゲートが動作。`--send` のフェアユース・ゲートは別で、早めに動作; 「debatable」または「likely」フェアユースはドラフトを停止し弁護士レビューにルート。

5. **法域注記.** DMCA §512 は米国連邦法。サービスプロバイダー、コンテンツ、または侵害者が米国管轄外にあるなら、ドラフト前にフラグ — EU DSA 通知、UK OSA 通知、または DMCA 通知の代わりに(または追加に)ローカル・レジーム・インストルメントが必要かもしれない。

6. **適切な場所でハンドオフ.** カウンター通知推奨を伴う `--respond` は `/ip-legal-ja:takedown --counter` に連鎖 — ただし、トリアージ・メモがレビューされ、カウンターする判断が意図的になされた後でのみ。

## 例

```
/ip-legal-ja:takedown --send
/ip-legal-ja:takedown --respond ~/Downloads/youtube-takedown-notice.pdf
/ip-legal-ja:takedown --counter
/ip-legal-ja:takedown
```

## 注記

- 送付される通知とカウンター通知は work-product ヘッダーを運びません。内部ドラフト、フェアユース分析、トリアージ・メモは運びます。
- §512(c)(3) と §512(g)(3) は要素単位の制定法 — 各要素が必須、さもなくば通知は欠陥。
- カウンター通知は、claimant の地区(または非米国 subscriber 用の指定地区)で連邦裁判所管轄に consent。これは形式ではない。
- 非弁護士ユーザーは、ゲートがクリアする前に弁護士との会話のための 1 ページ・ブリーフを取得 — カウンター通知に特に重要で、訴訟の前のステップ。

---

## 目的

DMCA §512 通知・テイクダウン・システムは fast、cheap、equal な意味で帰結的。テイクダウンは偽証の罰則下の宣誓声明で、司法レビューなしにコンテンツを pull する。カウンター通知は連邦管轄に consent しコンテンツを戻すもう 1 つの宣誓声明。両方とも訴訟になりうる。本スキルは各々が warrant するガードレールで 3 つの動きすべてを扱います。

3 つのモード:

- `--send` — §512(c)(3) テイクダウン通知をドラフト
- `--respond` — 誰かから送られたテイクダウンをトリアージ; 選択肢を生成
- `--counter` — §512(g)(3) カウンター通知をドラフト

ユーザーがフラグを渡さない場合、一度尋ねる: 「DMCA テイクダウンを送りますか、受領したものをトリアージしますか、それともカウンター通知をドラフトしますか?」

> **External deliverables (send と counter モード):** 送付される通知/カウンター通知はサービスプロバイダーの指定エージェントに行きます。送付される文書には `PRIVILEGED & CONFIDENTIAL — ATTORNEY WORK PRODUCT` ヘッダーを含めないこと。通知自体は特権付きではない — 制定法プロセス内でなされる声明。内部ドラフト、pre-send ブリーフ、フェアユース分析、トリアージ・メモはプラグイン設定 `## Outputs` のとおりヘッダーを保持します。

## 法域前提

DMCA §512 は **米国連邦法**。米国管轄に従うサービスプロバイダーに対して run。他の法域には独自の通知・アクション・レジーム — EU Digital Services Act Art. 16、UK Online Safety Act、India IT Rules 2021 など — があり、必要要素、カウンター通知メカニズム、誤用に対する責任で物質的に異なります。サービスプロバイダー、コンテンツ、または侵害者が米国管轄外にあるなら、フラグ — 米国 DMCA 通知は wrong instrument かもしれない、またはローカル・レジームの通知とペアにする必要があるかもしれない。著作権の存続自体は Berne 多国間だが、エンフォースメント・メカニズムは法域固有。

## コンテキストをロード

- `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/CLAUDE.md` → `## IP practice profile`(任意の著作権登録)、`## Enforcement posture` → `Approval matrix → DMCA takedown (ordinary)` 行、`## Outputs`(work-product ヘッダー、Role)、`## Who's using this`(Role — 弁護士 vs 非弁護士)
- **Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(インハウスのデフォルト)なら、案件機構をスキップ。有効でアクティブ案件がなければ問う: 「どの案件? `/ip-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と言ってください」。出力はアクティブ案件のフォルダ `~/.claude/plugins/config/claude-for-legal-ja/ip-legal-ja/matters/<matter-slug>/takedown/<slug>/`(またはプラクティスレベルで `takedown/<slug>/`)に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まないこと。

## Send モード — §512(c)(3) テイクダウン通知をドラフト

### Step 1: 著作権付き作品を特定

> 著作権付き作品は何ですか?
>
> - **タイトル / 記述** — 作品は何(ソフトウェア、画像、テキスト、ビデオ、オーディオ)?
> - **登録ステータス** — 米国著作権局登録番号と日付(あれば)。登録はテイクダウン送付には NOT 必要だが、米国作品で提訴するには必要で、その侵害前タイミングは法定損害賠償と費用を統制。
> - **所有権** — 我々は完全に所有、またはテイクダウン権限を持つ独占ライセンスを保有?(非排他ライセンシーは通常、ライセンサーの作品に対するテイクダウンを送れない。)
> - **過去のライセンス** — この使用、またはそれをカバーしうるより広い使用を、これまでにライセンスしたか?

所有権と権限は §512(f) 案件が最初に見るもの。ドラフト前にレコードでクリアに取得。

### Step 2: 侵害素材とその場所を特定

> 侵害素材はどこ?
>
> - **プラットフォーム / サービスプロバイダー** — YouTube、Twitter/X、GitHub、Reddit、Amazon、ウェブホストなど。
> - **URL** — 侵害素材への specific permalinks。1 つの通知は同じサービスからの複数 URL をカバーできる。
> - **記述** — 侵害素材は何で、どう侵害するか(verbatim copy、実質的に類似、派生)?
> - **スクリーンショット / 証拠** — タイムスタンプと URL が visible で保存。

§512(c)(3) は「サービスプロバイダーが素材を locate するのに reasonably sufficient な情報」を要求。URL のみでも通常十分; precise であること。

### Step 3: フェアユース・ゲート

*Lenz v. Universal Music Corp.*, 801 F.3d 1126 (9th Cir. 2015) 下、著作権者はテイクダウン送付前にフェアユースを考慮しなければならない。これはフェアユースに関する判断ではない — 送付者が取らなければならず、取ったことを証明できる考慮ステップ。

尋ねる:

> 通知をドラフトする前に、フェアユースを歩く。*Lenz* 下、結論が「フェアユースでない」でも、送付前に考慮しなければならない。4 要因:
>
> 1. **目的と性質** — 商業? transformative? 批評、コメント、ニュース報道、教育、scholarship、研究?
> 2. **著作権付き作品の性質** — 事実または創造的? 出版済みか否か?
> 3. **量と実質性** — 作品のどれだけが使用? 作品の心臓か?
> 4. **市場への影響** — 使用がオリジナルを substitute するか派生市場を harm するか?
>
> 各々のあなたの読みは? そして結論 — フェアユース unlikely、debatable、likely?

回答を通知ファイルに記録。「debatable」または「likely」なら、ドラフトしない。停止して弁護士レビューにルート: 「これらの事実上フェアユースが debatable/likely。フェアユースで保護される使用にテイクダウンを送るのは、制定法が作る正確な §512(f) エクスポージャー。通知が出る前にカウンセルにルート」。

### Step 4: Good-faith 信念

§512(c)(3)(A)(v) は「complaining party が、苦情の対象の方法での素材の使用が、著作権者、その代理人、または法によって authorized されていないという good faith 信念を持つ声明」を要求。

送付者はレコードで信念を形成。彼らは:

- 作品が自分のものであることを確認(または独占ライセンス経由でテイクダウン権限がある)?
- 使用がライセンスされていないことを確認(過去の deal なし、暗黙のライセンスなし、それをカバーする Creative Commons grant なし)?
- フェアユースを考慮(Step 3)?
- レポートではなく、被告コンテンツを直接レビュー?

4 つすべてに yes なら、good-faith 信念は colorable。任意の no なら一時停止。

### Step 5: 精度とエージェント権限

§512(c)(3)(A)(vi) は「通知内の情報が正確であり、偽証の罰則下で、complaining party が allegedly に侵害された独占権の所有者の代理として行動する authorized であるという声明」を要求。

これは偽証声明。識別と権限の精度に適用 — フェアユース判定自体ではないが、§512(f) 責任は両方に及ぶ。

署名者を確認: これを誰の代理で誰が送っているか、彼らはそうする権限を持つか?

### Step 6: 通知をドラフト

§512(c)(3)(A) 要素 — 各々が必須:

1. 権利保有者または authorized agent の **署名**(物理または電子)
2. **著作権付き作品の識別** — 「Copyrighted work: [title, description, registration no. if any]」
3. 場所情報付きの **侵害素材の識別** — 「Infringing material: [URL(s), description, how it infringes]」
4. **連絡情報** — complaining party またはエージェントの住所、電話、メール
5. **Good-faith 信念声明** — verbatim、適応: 「I have a good faith belief that use of the copyrighted material described above is not authorized by the copyright owner, its agent, or the law.」
6. **偽証の罰則下の精度と権限声明** — verbatim、適応: 「I swear, under penalty of perjury, that the information in this notification is accurate and that I am the copyright owner, or am authorized to act on behalf of the owner, of an exclusive right that is allegedly infringed.」

構造:

- 送付者住所ブロック / 日付
- 受領者: [service provider] の指定 DMCA エージェント(著作権局の DMCA Designated Agent Directory 経由で見つける — `https://www.copyright.gov/dmca-directory/`)
- Re: 17 U.S.C. §512(c) に従う著作権侵害通知
- 番号付きまたは明確に分離された上記の 6 要素
- 署名行

ほとんどのサービスプロバイダーは preferred form または web intake を公開(YouTube Content ID / Copyright webform、Twitter / X 著作権レポート、GitHub DMCA repo など)。スキルは通知コンテンツを生成; ユーザーはプロバイダーのパス経由で submit。名指されたサービスプロバイダー向けにどの intake パスが期待されるかを出力で注記。

### Step 7: 配信前のラウドゲート

```
┌─────────────────────────────────────────────────────────────┐
│  BEFORE THIS TAKEDOWN GOES ANYWHERE                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  A DMCA takedown is a statement under penalty of perjury.   │
│  Signing and sending it is not a routine administrative     │
│  step — it is a sworn declaration with specific legal       │
│  consequences.                                              │
│                                                             │
│  • 17 U.S.C. §512(f) creates LIABILITY for knowing          │
│    material misrepresentations. People have been sued,      │
│    and have lost, for bad-faith takedowns — *Lenz v.        │
│    Universal*, 801 F.3d 1126 (9th Cir. 2015); *Online       │
│    Policy Group v. Diebold*, 337 F. Supp. 2d 1195 (N.D.     │
│    Cal. 2004); *Stephens v. Clash*, 796 F.3d 281 (3d        │
│    Cir. 2015).                                              │
│                                                             │
│  • The accuracy and authority statement is sworn under      │
│    penalty of perjury. That is a real statement, not a      │
│    formality.                                               │
│                                                             │
│  • Sending a takedown on material that is in fact           │
│    licensed, owned by someone else, or fair use is the      │
│    fact pattern §512(f) was written for.                    │
│                                                             │
│  Confirm before the notice leaves:                          │
│                                                             │
│    1. You own the copyright, or you hold an exclusive       │
│       license with takedown authority.                      │
│    2. The accused use is not authorized — you have          │
│       checked licenses, grants, and any prior consents.     │
│    3. You considered fair use per *Lenz* (see Step 3 of     │
│       this draft); your conclusion is on the record.        │
│    4. Whoever has authority to sign approves sending.       │
│                                                             │
│  Approver per your practice profile: [approver from         │
│  Enforcement posture → Approval matrix → DMCA takedown      │
│  (ordinary) row]                                            │
│                                                             │
│  Automatic escalations that apply here: [list any from      │
│  the practice profile that this matter triggers]            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

ユーザーが非弁護士(`## Who's using this` 別)なら、追加:

> DMCA テイクダウンは偽証の罰則下で宣誓され、悪意または過剰な使用に対する §512(f) エクスポージャーを生成。弁護士とこれをレビューしましたか? いいえなら、彼らに持参するブリーフ: [短いサマリーを生成: 作品、所有権、被告使用、ライセンスチェック、フェアユース分析、署名者、サービスプロバイダー]。今の数千ドルの弁護士時間は、§512(f) 訴訟より物質的に cheaper です。
>
> あなたの法域で認可された弁護士、ソリシター、バリスター、他の認可された法専門家を見つけるなら: あなたの専門職規制当局のリファラル・サービス(米国の州弁護士会、英国イングランド&ウェールズの SRA/Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの法曹会、またはあなたの法域の同等機関); ABA IP セクション・リファラル・ロスター(米国); 個人クリエーターと小規模事業向けロースクール IP クリニック。

ゲートへの明示的関与なしに最終出力を書かないこと。

### Step 8: 出力

**主要:** `<matter-folder>/takedown/<slug>/notice-v<N>.md`(またはサービスプロバイダーが accept するなら .docx — ほとんどは貼り付けテキストまたは web フォーム submission を accept)。通知コンテンツ、サービスプロバイダーの DMCA intake フォームへ paste または指定エージェントへ送付の準備が整ったもの。

**チャット内:** 書く前にレビュー用にプレーンテキストで通知を表示。コミット前に反復。

**レビューアー向けクロージング・ノート**(チャット内プレビューのみ):

> これは弁護士レビュー用の DMCA 通知ドラフトであり、送付準備の整った通知ではありません。送付は §512(f) エクスポージャー付きの宣誓声明。認可された弁護士がレビュー、編集し、submission 前に専門職責任を取ります。これをレビューなしで送付しないこと。

**引用検証.** 含まれる任意の判例または制定法引用(例: 通知周辺の内部メモ内)は、法律調査ツールで検証されなければならない。各々をソースタグ — `[Westlaw]`、`[CourtListener]`、`[user provided]`、`[model knowledge — verify]`、`[web search — verify]`。`verify` でタグされた引用が最初にチェックされる。設定された調査ツールが thin だった場合、Web またはモデル知識からサイレントに supplement しないこと — ユーザーに選択肢を提示。

**Post-send 記録.** Submission 後、`<matter-folder>/takedown/<slug>/submission.md` を書く: サービスプロバイダー、使用された指定エージェント(住所または web フォーム URL)、submit 日付、返された確認 ID、ターゲット URL、カウンター通知監視日付(通常 10〜14 営業日)、リーガルホールドを refresh。

## Respond モード — 受領したテイクダウンをトリアージ

あなたのコンテンツが取り下げられた。サービスプロバイダーが §512(c)(3) 通知をあなたに通知。選択肢があります。

### Step 1: 受領通知を読む

抽出:

- **送付者** — 法人、署名者、住所、メール
- **サービスプロバイダー** — 誰が通知(プラットフォーム)
- **主張作品** — 彼らが自分のものと言うもの
- **侵害が alleged されたあなたのコンテンツ** — 彼らが名指した URL または識別子
- **テイクダウン / 通知の日付**
- **通知が表面上 §512(c)(3) を満たすか** — 欠落要素をフラグ; 欠陥通知は proper 通知ではない

### Step 2: 評価

- **ライセンスを持っているか?** 交渉、暗黙、Creative Commons、過去の和解、譲渡 — 使用を authorize する何か。
- **フェアユースか?** *Lenz* 4 要因を歩く。Honest であること; これは我々のためで、応答のためではない。
- **通知は欠陥か?** §512(c)(3)(A) 要素のいずれかが欠落、偽証声明欠如、明らかな権限のない人が署名? 欠陥通知は properly compliant でない; ホストは依然それに作用するかもしれないが、送付者の §512(f) エクスポージャーが上昇し、我々のレバレッジが上昇。
- **ホストは §512(g) を proper に遵守?** 我々は通知とカウンターする機会を与えられたか? ホストが我々にチャンスを与えずに行動したなら、それは送付者ではなくホストとの別問題。
- **送付者はトロールか?** このプラットフォームでの overbroad テイクダウンの繰り返しパターン?

### Step 3: 選択肢

トレードオフ付きで 4 つの選択肢を提示:

**A — 遵守(テイクダウンを stand させる)**
- いつ: 彼らが正しい、または戦いの価値がない
- トレードオフ: コンテンツがダウンのまま; SEO、strikes ポリシーのアカウント、クリエーターの livelihood に影響しうる
- 次のステップ: イベントをログ、カウンター通知期日問題なしを確認、進む

**B — カウンター通知を送付**(§512(g)(3))
- いつ: 素材が誤識別または mistakly に削除されたという good-faith 信念があり — しばしば使用がライセンス、フェアユース、または送付者が作品を所有していないところに適用
- トレードオフ: 偽証の罰則下で宣誓、送付者の地区(または外側で designate するなら自分の地区)で連邦裁判所管轄に consent、10〜14 営業日間、送付者に判断を put — 訴えるならコンテンツはダウン、訴えないならコンテンツは復元
- 次のステップ: `/ip-legal-ja:takedown --counter`

**C — 送付者と直接 engage**
- いつ: ビジネス解決(ライセンス、credit、より狭い部分のテイクダウン)の余地がある
- トレードオフ: 会話中コンテンツがダウンのまま; 和解コミュニケーション・ハイジーンが重要(FRE 408 または等価; 実質とコンテキストからの保護、ラベルではない)
- 次のステップ: 送付者へのアウトリーチ・レター; ディスカッションが live の間カウンター通知を送らない

**D — 無視して stand させる; 別場所で raise**
- いつ: 害が小さい、連邦管轄 admission を望まない、送付者を別々に扱いたい
- トレードオフ: コンテンツがダウンのまま; テイクダウン自体が悪意なら、自分のスケジュールで §512(f) を主張できるかもしれない — それは独自の戦い

理由 2 文で 1 つを推奨。

### Step 4: トリアージ・メモを書く

出力: `<matter-folder>/takedown/inbound/<slug>/triage.md`。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs]

> **Privilege inheritance.** This triage records our first-pass assessment of an adverse takedown. It is attorney-client and/or work-product material. Do not forward outside the privilege circle or attach to counter-notice submissions without scrubbing.

# DMCA Takedown Received — Triage

> **READ FOR TRIAGE, NOT OPINION.** 構造化された intake スキャンで、法的メリット意見ではありません。すべての典拠は SME 検証のためフラグ; すべてのメリット判断はカウンセルのもの。

**Slug:** [slug]
**Received:** [YYYY-MM-DD]
**Service provider:** [プラットフォーム]
**Incoming file:** [path]

## 通知

**送付者:** [法人、署名者、ある場合のカウンセル]
**主張作品:** [タイトル、記述、提供されたなら reg no.]
**ターゲットされた我々のコンテンツ:** [URLs / 識別子]
**テイクダウン日付:** [YYYY-MM-DD]
**通知は表面上 §512(c)(3) を満たす:** [yes / no — 欠落要素をリスト]

## 評価

**ライセンス / authorization チェック:** [読み取り]
**フェアユース歩行(Lenz 要因):** [読み取り — 各要因 + 結論; `[SME VERIFY]`]
**通知の欠陥:** [list またはなし]
**ホストの §512(g) 遵守:** [我々は通知と機会を与えられた]
**送付者の信頼性:** [トロール / 実物の主張者 / 繰り返しテイクダウン・パターン]

## 選択肢

### A. 遵守
### B. カウンター通知(§512(g)(3))
### C. 送付者と engage
### D. 無視

**推奨:** [A/B/C/D] — [2 文でなぜ] — `[SME VERIFY: counsel to confirm before executing]`

## 期日

- **カウンター通知監視ウィンドウ:** カウンター通知 submit 後 10〜14 営業日 — そのウィンドウに送付者が提訴したらコンテンツがダウンのまま
- **送付者の提訴タイミング:** 通常、カウンターするなら我々のカウンター通知クロック上
- **ホストとの任意の契約上の期日:** [チェック]

## 即時アクション

- [ ] 被告作品と関連コンテンツに対しリーガルホールド発行 — [yes/no]
- [ ] ビジネス影響評価(収益、アカウント strikes、SEO) — [yes/no]
- [ ] ログに案件作成 — [yes/no/TBD]
- [ ] カウンセル割当 — [誰]
```

チャット内のプレゼンテーションを以下で締めくくる:

> これはトリアージ・メモで、助言ではありません。上記評価は通知の 4 隅からの first read。カウンター通知(連邦管轄に consent)または応答しない判断の前に弁護士が評価します。

## Counter モード — §512(g)(3) カウンター通知をドラフト

カウンター通知は、オリジナル送付者が 10〜14 営業日内に訴えない限りコンテンツを戻す。訴訟の前のステップ。

### Step 1: 前提条件を確認

- コンテンツが §512 通知への応答で取り下げられた(ホストの利用規約アクションではない)。
- 素材が ミステイクまたは誤識別で削除されたという good-faith 信念がある — 制定法テスト。
- オリジナル送付者の地区(または米国外なら designate)で連邦裁判所管轄に consent する準備がある。
- 判断が意図的になされた — 反応ではなく、弁護士インプットなしではない。

### Step 2: §512(g)(3) ごとにドラフト

§512(g)(3) 要素 — 各々が必須:

1. subscriber の **署名**(物理または電子)
2. **削除された素材の識別** と削除前のその場所(コンテンツがあった URL)
3. **subscriber が、素材がミステイクまたは誤識別の結果として削除または無効化されたという good faith 信念を持つという偽証の罰則下の声明** — verbatim、適応
4. **Subscriber の名前、住所、電話番号** — そして、critically、**subscriber の住所が位置する地区の連邦地区裁判所の管轄への consent**(または米国外なら、サービスプロバイダーが見つかりうる任意の地区)、および通知を提供した人またはその人のエージェントからのプロセスのサービスの受領

構造:

- subscriber 住所ブロック / 日付
- 受領者: サービスプロバイダーの指定 DMCA エージェント(オリジナル・テイクダウンを受け取った同じエージェント)
- Re: 17 U.S.C. §512(g) に従うカウンター通知
- 番号付きまたは明確に分離された上記の 4 要素
- 署名行

### Step 3: 配信前のラウドゲート

```
┌─────────────────────────────────────────────────────────────┐
│  BEFORE THIS COUNTER-NOTICE GOES ANYWHERE                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  A DMCA counter-notice is a statement under penalty of      │
│  perjury AND consents to federal court jurisdiction. It     │
│  is the step before litigation.                             │
│                                                             │
│  • If the original claimant files suit within 10–14         │
│    business days after your counter-notice, the content     │
│    stays down pending the suit. 17 U.S.C. §512(g)(2)(C).    │
│                                                             │
│  • If they do not sue within the window, the host must      │
│    restore the content within 14 business days of your      │
│    counter-notice.                                          │
│                                                             │
│  • You are consenting to be sued in federal court in the    │
│    claimant's judicial district (or, if you are outside     │
│    the US, designating a district). This is a jurisdiction  │
│    admission you make by signing.                           │
│                                                             │
│  • The perjury statement is real. §512(f) liability runs    │
│    in both directions — senders and counter-senders.        │
│                                                             │
│  Confirm before the counter-notice leaves:                  │
│                                                             │
│    1. The material was removed in response to a §512        │
│       notice (not a TOS action).                            │
│    2. You have a good-faith belief the removal was a        │
│       mistake or misidentification — because the use is     │
│       licensed, fair use, not actually infringing, or the   │
│       sender doesn't own the work.                          │
│    3. You are prepared to be sued in federal court in the   │
│       claimant's district. Budget, counsel, and risk        │
│       tolerance are all set.                                │
│    4. An attorney has reviewed this before it is sent.      │
│                                                             │
│  Approver per your practice profile: [approver from         │
│  Enforcement posture → Approval matrix — counter-notices    │
│  generally route above the DMCA takedown (ordinary)         │
│  approver because of the federal-jurisdiction admission]    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

ユーザーが非弁護士なら:

> カウンター通知は連邦裁判所管轄に consent し、偽証の罰則下で宣誓されます。あなたの法域で認可された弁護士、ソリシター、バリスター、他の認可された法専門家でレビューしましたか? これは Claude レビュー層ではない; これは認可された専門職判断が必要なステップ。会話用ブリーフ: [1 ページ・サマリーを生成]。リファラル・リソース: あなたの専門職規制当局のリファラル・サービス(米国の州弁護士会、英国イングランド&ウェールズの SRA/Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの法曹会、またはあなたの法域の同等機関); ロースクール IP クリニック; ABA IP セクション(米国)。

明示的関与なしに最終出力を書かないこと。

### Step 4: 出力

**主要:** `<matter-folder>/takedown/<slug>/counter-notice-v<N>.md` — カウンター通知コンテンツ、サービスプロバイダーのカウンター通知 intake 経由で submit 準備の整ったもの。

**チャット内:** コミット前にレビュー用にプレーンテキストで提示。

**レビューアー向けクロージング・ノート**(チャット内のみ):

> これは弁護士レビュー用のカウンター通知ドラフトであり、送付準備の整ったカウンターではありません。送付は宣誓声明で、claimant の地区で連邦裁判所管轄に consent。認可された弁護士が submission 前にレビュー。これをレビューなしで送付しないこと。

**Post-submission 記録.** Submission 後、`<matter-folder>/takedown/<slug>/counter-submission.md` を書く: サービスプロバイダー、submit 日付、確認 ID、10〜14 営業日監視ウィンドウ end 日付をカレンダー登録、claimant の地区での提訴監視、コンテンツが復元された場合のプラン、提訴された場合のプラン。

## 決断スタンス

プラクティスプロファイルの `## Decision posture on subjective legal calls` の通り: 使用がフェアか、権利保有者が我々か、作品が実際に我々のものか、フェアユースが受領側でクレームを破るかが不確実な場合、サイレントに決定しないこと。フェアユースは paradigmatic な不確実な判断。弁護士レビューのためフラグ; 要因を表面化。前提でテイクダウンまたはカウンター通知を送るのは一方通行のドア。

## このスキルが行わないこと

- **通知を submit する.** ドラフトのみ。ユーザーがサービスプロバイダーの指定チャネル経由で submit。
- **ユーザーのためにサービスプロバイダーの intake フォームを選ぶ.** どのパスが期待されるかを注記; 自動 submit しない。
- **フェアユースを決定する.** 4 要因を歩く; フラグ。弁護士が進むかを決定。
- **受領側で送付者のクレームを検証する.** 構造化された読み取り; すべての典拠を SME 検証のためフラグ。
- **ゲートをバイパス.** `--send` と `--counter` モードでゲートは毎回動作。
- **引用を発明する.** 含まれる任意の cite はソースタグ付きで検証のためフラグ; サイレントな supplement なし。
- **非米国レジームを扱う.** DMCA は米国固有。EU DSA、UK OSA、India IT Rules、その他レジーム向け — フラグしてルート。
