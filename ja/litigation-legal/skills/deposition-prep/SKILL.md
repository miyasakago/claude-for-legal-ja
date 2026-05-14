---
name: deposition-prep
description: 証人のためのデポジションアウトラインを構築 — eDiscovery プラットフォームから彼らの文書を取得、ケースセオリーを中心にトピックを整理、impeachment 素材を表面化。Use when the user says "depo prep for [witness]", "build a depo outline", or "prepare for [name]'s deposition". 日本語トリガー: デポジション準備、depo 準備、証人尋問準備、デポ準備
argument-hint: "[witness name]"
---

# /deposition-prep

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → ケースセオリー、キーファクト。
2. 以下のワークフローと参照に従う。
3. eDiscovery プラットフォームから証人が著した/証人に言及する文書を取得。
4. アウトラインを構築: 背景、キー文書、セオリーに紐付くトピック、impeachment 素材。

---

# Deposition Prep

## イングランド&ウェールズの証人陳述書 — PD 57AC

ユーザーの法域がイングランド&ウェールズを含み、Business & Property Courts(または任意の CPR 規律手続)のためのトライアル証人陳述書を求めている場合、PD 57AC が適用されます。陳述書は証人自身の言葉で、議論を含まず、証人が記憶をリフレッシュするのに使った文書を特定し、要求されるコンプライアンス確認と法定代理人の証明書を運ぶ必要があります。

**時系列表、文書セット、またはケースの説明から「証人として」物語を起案することは、PD 57AC が防ぐために設計されたことそのものです。** 裁判所は AI 支援証人陳述書起案を積極的に制裁しています。それを依頼されたら、私は実行しません。

私が実行すること: 証人の実際の記憶を引き出すための質問プロンプトを準備;証人が言うこと(彼らの言葉、私のではない)をキャプチャして整理;見せられた文書のリストを生成;証人が起案した陳述書に対して PD 57AC コンプライアンスチェックリストを実行;solicitor のコンプライアンス証明書を起案。証人の証拠を陳述書に入れるのを助けます。証拠を書きません。

米国デポジション、宣言、宣誓供述書: 異なる規則ですが、同じ規律が適用。証人が書いていない証人の声での宣言は、せいぜい信用問題。

## 宛先チェック

出力を生成する前に、行き先を確認。ユーザーが宛先(チャネル、配信リスト、相手方、「全員」)を指名した場合、特権の輪の内側か尋ねる。公開チャネル、全社リスト、相手方/相手方代理人、ベンダー、クライアント(成果物の場合)は保護を放棄。宛先が輪の外に見えるとき、フラグして提案 (a) リーガル専用の特権版、(b) より広いチャネル向けのサニタイズ版、(c) 両方 — 特権ヘッダーを黙って適用してヘッダーが保護しない場所への貼り付けを助けないこと。このプラグインの CLAUDE.md の標準的な `## 共有ガードレール → Destination check` 参照。

## 目的

depo アウトラインはマップ: 背景 → 良い事実をロックイン → 悪いもので対決 → セオリーで箱詰め。このスキルは文書とケースセオリーからマップを構築。

## レコード忠実性 — 引用とピンポイント

レコードからこのアウトラインに引き出されるすべての引用とすべての quotation を支配する 2 つのルール。標準的なステートメントはプラグインの `CLAUDE.md` 共有ガードレールに住む;ここに繰り返し、なぜなら誤引用された prior 陳述または misgrounded transcript 引用に基づく impeachment 対決は impeachment を崩壊させる。

**レコードからの逐語引用は逐語的でなければならない。** 相手方弁護士、証人、別の deponent、裁判所、または任意のレコード文書に帰属する言葉に、正確な箇所が目の前にあり引用先を示せる場合を除き、引用符を付けないこと。誰かが言ったことを性格付けたいが正確な言葉を見つけられないとき:

- **引用符なしで言い換え**、明確に帰属: 「Witness previously testified that X `[verify against record — Tr. p. __]`.」
- **プレースホルダをマーク:** `[verify exact quote — record cite pending]`
- **絶対に穴を埋めない。** 作り出した prior 陳述は、証人が disavow し transcript があなたを支えない瞬間に impeachment を破壊。すべての `[verify exact quote]` がレビュアー注記でフラグされる必要あり。

**ピンポイント引用は命題全体を支持しなければならない。** impeachment ポイントが「証人は [date] に X、Y、Z と言った」なら、ピンポイント引用が X **かつ** Y **かつ** Z を支持することを検証。Z だけしか支持しないなら、引用を分割 — 「said X (Tr. p. 10), Y (Tr. p. 12), Z (Tr. p. 15)」 — または命題を狭める。impeachment の一部を支持する引用は、相手方弁護士が証人に周囲の transcript をもっと読むよう求め、対決が崩れる失敗モード。

## 口頭キャリブレーション

depo アウトラインはリアルタイムで読み上げられる。それは口頭弁論、書面ではない。それは意味:

- 実際に重要な 3-4 トピックを選ぶ。すべてをカバーしようとしないこと — 4 時間 depo の 200 質問アウトラインは弁護士に skim させ、skim は質問のラインがシーケンス中に失われる方法。
- 最強の対決で先導。証人は冒頭が freshest で、transcript のオープニングページは裁判官または陪審員が最も見る可能性が高い。
- 敵対証人: 最もタイトな質問は最もタイトなシーケンスに行く。それ以外はすべて scaffolding。
- depo 後の反駁結審を準備しているなら、キャリブレーションはさらに厳格 — 法廷は最初の 2 分と最後の 2 分を覚える。

口頭作業の「徹底的すぎる」は unfocused に読まれる。アウトラインが長いのはレコードが deep だからなら、そう言い、弁護士が折りたたむべきところをフラグ。

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → ケースセオリー(セオリー、pivot fact、我々のため/反対するキーファクト)、eDiscovery プラットフォーム。

**コンフリクトゲート — バイパス不可。** アウトラインを構築する前に、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace is set up. I won't build a deposition outline on a matter that hasn't been intaken — the conflicts check is the gate."

intake されていない案件で進まないこと。intake はコンフリクトを実行し、このスキルが読み取る `_log.yaml` 行を書きます。

## ワークフロー

### Step 1: この証人は誰?

- 名前、ロール、ケースへの関係
- なぜ我々は彼らを deposeing するか — この証人から何を必要とするか?

「why」はセオリーに接続。証人が pivot fact を確立できるなら、それがアウトラインのセンターピース。

### Step 1a: 証人ポスチャ — 質問起案前に分岐

準備構造はポスチャで異なる。質問を書く前に証人ポスチャを特定:

- **敵対 / 敵意的** — 反対尋問スタイル: closed、leading、一度に 1 事実。箱を構築。
- **友好 / 我々のもの** — 直接尋問スタイル: 証人に物語を語らせる open 質問。自分の証人への closed leading 質問は通常不適切で、factfinder との信用を undercut。
- **中立第三者** — 混在;しばしば物語を取得するために open、詳細を pin するために closed。
- **法人代表(30(b)(6) または州相当)** — トピック指名、エンティティを縛るルール、証人の個人知識 vs 法人知識の区別すべてに distinct ルール。フォーラムの該当 deposition 規則と 30(b)(6) / 州相当手続をリサーチ。確認: どのトピックが指名されたか、誰が produced されたか、binding testimony のスコープ。

**フォーラムと証人タイプの該当 deposition 規則をリサーチ**(FRCP 30 / 連邦民事訴訟規則 30 / 州相当、ローカルルール、デポジションに関する裁判官のスタンディングオーダー)。プライマリソースを引用。one-size 準備構造を適用しないこと — 質問形式、文書へのアプローチ、impeachment 素材の使用すべてがポスチャに依存。

**No silent supplement.** 設定された法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)へのリサーチクエリが、フォーラムの deposition 規則または impeachment に必要な引用について少数または結果なしを返す場合、見つかったものを報告して停止。尋ねずに web 検索やモデル知識からギャップを埋めないこと。こう言う: 「The search returned [N] results from [tool]. Coverage appears thin for [rule / authority]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) leave the `[UNCERTAIN]` marker and stop here. Which would you like?」 弁護士が低い自信の出典を受け入れるかを決定;スキルは決定しない。

**Source attribution.** アウトラインのすべての規則参照、判例引用、権威に出処をタグ付け: 法律調査コネクター経由で取得された引用には `[Westlaw]`、`[CourtListener]`、`[Trellis]`、`[Descrybe]`、または MCP ツール名;web 検索引用には `[web search — verify]`;トレーニングデータから思い出された引用には `[model knowledge — verify]`;パートナーまたはシニアアソシエイトが供給した引用には `[user provided]`。文書引用(Bates、生成番号)はネイティブ出典を保持。`verify` タグの引用は高い捏造リスクを運び、デポジション前にチェックすべき。タグを絶対に剥がしたり折りたたんだりしないこと。

### Step 2: 彼らの文書を取得

eDiscovery プラットフォーム(接続されていれば Everlaw/Relativity/DISCO)から:

- 証人が著した文書
- 証人へ/からの文書
- 名前で証人に言及する文書
- 証人が存在するカレンダーエントリとミーティングノート

日付で整理。ホットドックスをフラグ — セオリーに最も重要なもの。

### Step 3: トピックを構築

各トピックは確立または探求したいもの。セオリーを中心に整理:

**背景(常に最初 — 証人が defensive になる前に争いのない事実をロックイン):**
- ロール、tenure、責任
- レポート構造
- キープレーヤーとどう interact したか

**良い事実(対決前にロックイン):**
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → 我々のためのキーファクトから、この証人が確立できる
- セオリーを支える文書、この証人が著者または受信者

**悪い事実(文書で対決):**
- とにかく証人が尋ねられる我々に反する事実 — 我々のバージョンを先に取得
- 痛い文書 — 証人がどう説明するか知る

**Impeachment(敵対または矛盾する場合):**
- 先行する一貫しない陳述(docs、prior testimony、宣言から)
- 期待することと矛盾する文書

**Pivot fact:**
- ケースが turn する事実を確立(または undermine)する質問のシーケンス
- これが最も注意深く構築されたセクション。質問形式は Step 1a の証人ポスチャに従う: 敵対には tight closed leading、友好には controlled open、中立には混在。1 パターンにデフォルトしないこと。

### Step 4: アウトラインを書く

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

# Deposition Outline: [Witness Name]

**Date:** [depo 日付]
**Witness role:** [タイトル、ケースへの関係]
**Witness posture:** [adverse / friendly / neutral / 30(b)(6) or state equivalent] — 質問形式を駆動
**Applicable deposition rules:** [FRCP 30 / 州規則 / ローカルルール / スタンディングオーダー — ピンポイント引用付き] `[UNCERTAIN — verify currency]`
**Why we're taking this depo:** [1 文 — 目標]
**Theory connection:** [この証人がケースセオリーにどうフィット]

---

## I. 背景

[質問 — closed、1 質問 1 事実。争いのないものをロックイン。]

## II. [良い事実トピック]

**Goal:** summary judgment / トライアルでの使用のために [事実] を確立。

**Documents:**
- [Bates] — [説明] — [なぜ重要]

**Questions:**
[シーケンス。各質問 closed。承認に向けて構築。]

## III. [悪い事実トピック]

**Goal:** トライアル準備される前に [悪い事実] について我々の条件で証人の説明を取得。

[同じ構造]

## IV. Impeachment 素材(必要なら使用)

[先行する陳述 / 文書、証人が矛盾したら対決するためのもの]

## V. [pivot fact シーケンス]

**Goal:** [ケースが turn するもの]

[これは最もタイトなセクション。各質問が yes/no。各質問が 1 事実を確立。箱を構築。]

---

## Exhibit リスト

| # | Bates | 説明 | 使用するセクション |
|---|---|---|---|

## マーカー規律

構築とレビュー中インラインで使用:
- `[VERIFY: factual assertion]` — レコードに対して未確認の事実
- `[UNCERTAIN: legal proposition]` — 現行権威に対して未確認の法的ポイント(規則、期限、尋問範囲制限)
- `[CITE NEEDED: specific cite]` — レコードまたは権威引用が保留

## 弁護士のための注

- [アウトラインがキャプチャしないもの — 証人 demeanor 注記、その場で下す戦略的判断]

---

**特権 / 成果物素材。** このアウトラインはケース素材と成果物から構築され、保護ステータスを継承。特権素材フォルダに保持、適切にマーキング、任意の流通決定(共同弁護士、クライアント、エキスパート)を deliberately に — 特権サークル外への流通は保護を放棄する可能性。

**依拠した権威について引用チェック。** アウトラインに引き出された規則引用(FRCP 30、州相当、ローカルルール、スタンディングオーダー)と任意の判例法は AI モデルによって生成。Westlaw、CourtListener、またはあなたのリサーチプラットフォームに対して各々を検証 — デポジションで使用前に通貨とスコープを確認。各引用の出典タグ(例: `[Westlaw]`、`[web search — verify]`)が引用がどこから来たかを示す;`verify` タグは高い捏造リスクを運び、最初にチェックすべき。
```

## このスキルがしないこと

- デポジションを行う。アウトラインはマップ;弁護士が運転。
- 証人が何を言うか予測。可能性のある回答に備えるが、証人は驚かせる。
- その場で何を尋ねるかを決定。フォローアップは部屋での弁護士の判断。
