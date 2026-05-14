---
name: demand-draft
description: 完了した intake から要求書を起案 — 特権 / FRE 408 / 放棄 / 自認のチェックリストにゲート、.docx 出力、送付後チェックリスト、案件作成の提案付き。Use when the user says "draft the demand", "write the [type] letter", or has a finished demand intake ready to turn into a sendable draft. 日本語トリガー: 要求書起案、要求書ドラフト、警告状起案
argument-hint: "[slug] [--skip-gate] [--version=N]"
---

# /demand-draft

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/intake.md` をロード。欠落または戦略ブロックが空(実質的要求の場合)なら拒否。
2. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → 要求書プラクティス、ハウススタイル、シードドキュメントテーブル。
3. 以下のワークフローと参照に従う。
4. pre-draft ゲートを実行: 特権フィルタ、自認リスク、accord-and-satisfaction、FRE 408 ポスチャ、放棄スキャン、トーン、事実精度。各項目に従事するまで進まない。
5. テンプレート選択: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` でシードドキュメント提供されていればそれ;それ以外は要求タイプのソフトテンプレート。
6. インチャットでレビュー用に起案。ユーザーが承認するまで反復。
7. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/draft-v[N].docx` を `docx` スキル経由で書き込み。
8. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/checklist.md`(送付後チェックリスト)を書き込み。
9. ヒューリスティックに従って重要性を評価;案件作成を提案。Yes なら: 事前入力されたフィールドで `matter-intake` にハンドオフ。

---

# Demand Draft

## 目的

完了した intake から送付可能なドラフトを生成。価値の大半は、特権、放棄、自認、和解通信のポスチャが意識的に対処されるまで起案を拒否することにあります — 失敗モードは、誰もポーズしてチェックしなかったために特権を放棄するか自認を構成するレターです。

## レコード忠実性 — 引用とピンポイント

要求書はアドボカシーで、契約、メール、または先行する通信から引用された各行は相手方がテストする主張になります。標準的なステートメントはプラグインの `CLAUDE.md` 共有ガードレールに住む;ここに繰り返し。

**逐語引用は逐語的でなければならない。** 相手方、その弁護士、証人、または任意の文書に帰属する言葉に、正確な箇所が目の前にない限り、引用符を付けないこと。正確な言葉なしに性格付けたいとき:

- **引用符なしで言い換え**、プレースホルダ付き: 「Your [date] email stated X `[verify exact quote — email cite pending]`.」
- **絶対に穴を埋めない。** 要求書での誤引用された契約条項は、初回ラウンドで相手方弁護士の信用を失う最速の方法です。
- レターが出る前にすべての `[verify exact quote]` がレビュアー注記でフラグされる必要あり。

**ピンポイント引用は命題全体を支持しなければならない。** 要求が「Section 4.2 requires payment within 30 days upon invoice receipt」と主張する場合、引用されたセクションは義務 AND トリガー AND ウィンドウをカバーする必要あり。1 つしかカバーしない場合、引用を分割(例: 「Section 4.2 (payment obligation); Section 4.3 (30-day window)」)または命題を狭める。要求の一部を支持する契約引用は、相手方が全文で返信してポスチャを反転する経路。

## 弱い議論に対する率直さ

法または記録がポイントに対して反する場合、しっかりしているように装わない。要求の議論が弱いとき — 契約言語が曖昧、権威が反対側を切る、損害賠償理論がストレッチ — 送信者にフラグ:

> "The [claim / theory] here is weak because [authority / fact]. Options: (a) press it and frame as `[alternative framing]`, (b) drop it and rely on [stronger claim], (c) keep it as a hook but hedge the language. `[review — strategic call]`."

過剰主張する要求書は、すべての行き過ぎをカタログ化し、レバレッジをシフトし、次のラウンドを焼く返信を得ます。最強の要求書は、相手方ができないように弱いものを譲歩するものです。

## エコー vs 繰り返し

案件に先行する通信があれば、キー用語をエコー — 違反の同じ性格付け、コア義務の同じフレーミング、取引の同じ名前。文全体を持ち上げない。先行のコピーペーストのように読める要求書は、何も変わっていないことを示します;新しいレターはポスチャを進める(新事実、新期限、新結果)べきで、再述すべきではありません。

> **外部成果物:** 起案された要求書は相手方に送付されます。送付されるレターには `PRIVILEGED & CONFIDENTIAL — ATTORNEY WORK PRODUCT — PREPARED AT THE DIRECTION OF COUNSEL` ヘッダーを含めないこと。送付後チェックリストと intake ファイルは内部成果物でヘッダーを運びます。

## サイドコンテキスト

要求書の起案は本質的にアサーション — 送信者は請求を行っています。プラクティスプロファイルの `## サイド` を読む:

- **Plaintiff / claimant**(このスキルのデフォルト): demand-draft はポスチャと整合。レターは請求。トーン、結果言語、求める救済はすべて plaintiff サイドのプレイブックから流れる。
- **Defense / respondent**: defense からの要求起案は一般的ではないが起こります — defense 実務家がカウンターデマンド、拠出要求、または無関係案件で要求書を送ることがあります。起案前に確認: 「You said defense is your default. Is this matter plaintiff-posture for you (you're asserting a claim), or is this a different posture?」
- **Both / varies**: ドラフトごとにどのポスチャが適用されるか尋ねる。ドラフトのトーンとデフォルト署名者が異なる可能性。

社内の defense 実務家で要求書を送るより受領する方が多い場合、`demand-received` にルート — そのスキルがインバウンドトリアージケースを処理。

## この案件のポスチャ

pre-draft ゲートの前に、案件レベルポスチャを確認。要求書のトーンと条件はケースバイケースで、プラクティスデフォルトではありません。ユーザーと確認(intake の `## ポスチャ` セクションがあれば読む;なければ尋ねる):

> **この案件のポスチャ。** 要求書のトーンと条件はケースバイケースであり、プラクティスデフォルトではありません。尋ねる:
> - **トーン:** 抑制的 / 主張的 / 攻撃的?(関係性、金額、訴訟の可能性に依存)
> - **応答ウィンドウ:** 請求に対して何が合理的か?(支払い要求では 14 日が一般的;治癒に 30 日;cease-and-desist に 7 日 — ただし契約またはプロトコルが設定するかも)
> - **マーキング:** 「without prejudice」または「without prejudice save as to costs」マーキングが必要か?(和解通信は必要;クレームの主張はしばしば不要;法域が重要 — 不確実なら尋ねる)
> - **署名者:** あなた、クライアント、GC、指示された弁護士/カウンセル?
> 仮定しないこと。案件ファイルに先行する要求通信があれば読む — それがレジスターを確立。

回答が動詞選択のトーン、結果言語、`Without prejudice` ヘッダー(またはその不在)、署名ブロック、コンプライアンス期限を駆動。intake でキャプチャされなかったポスチャはここでキャプチャされる — プラクティスレベルデフォルトに頼らないこと。

## 法域の前提

このドラフトは intake で特定された法域とフォーラムに適用可能な和解通信規則(連邦では FRE 408、それ以外は州相当)を前提とします。法的規則、期限、手数料シフト、制定法フックは法域ごとに大きく異なります。基礎となる事実が異なるフォーラム、異なる相手方のホームステート、または準拠法問題に触れる場合、ドラフトは記述通りには適用されない可能性 — 送付前に確認。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/intake.md` — 必須;欠落なら進めるのを拒否
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → 要求書プラクティス(シードドキュメントパス、保険テンダーのタイミング、案件作成のための重要性閾値)、ハウススタイル(特権マーキング、トーン参照用の外部弁護士指示フォーマット)。**トーン、コンプライアンス期間、マーキング、署名者は `## この案件のポスチャ` から来る — それらは案件レベルであり、プラクティスレベルではない。**
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — 既存の関連案件(同じ相手方)をチェックし、クロスリンクを提案

### 戦略ブロックスキップの取り扱い

intake に `strategic_block: skipped` または `partial` があれば、pre-draft ゲート実行前にユーザーをプロンプト:

> The intake skipped [all / some] of the strategic block (leverage, BATNA, tone, privilege filters). Drafting now will produce a usable letter but the strategic sections will be generic and flagged with `[SME VERIFY]`.
>
> - **Complete strategic block now** — pause, return to `/demand-intake [slug] --resume-strategic`
> - **Proceed anyway** — continue to pre-draft gate; downstream sections flagged

「proceed anyway」なら、スキップされた戦略質問に依存するドラフトの各セクションがインラインで `[SME VERIFY: [specific question]]` を取得。

## フラグ

- `--skip-gate` → pre-draft チェックリストをバイパス。利用可能だがログされる;チェックリストが別途実行され文書化された場合のみ使用。
- `--version=N` → `draft-vN.docx` として起案(デフォルト: 次のバージョン番号)

## pre-draft ゲート

**これは起案前に実行。ユーザーが従事しないなら停止。**

```
PRE-DRAFT CHECKLIST — [slug]

1. 特権フィルタ
   intake 特権フィルタごとに: [リスト]
   確認: これらのいずれもドラフトに現れないか?  [y/n]

2. 自認リスク
   intake 自認リスクごとに: [リスト]
   各々について、表現は制御または削除されているか?  [項目ごとに y/n]

3. アコード&サティスファクション
   intake ごと: [フラグされたリスク、あれば]
   要求は別の請求を不本意に満たすか受諾するか?  [y/n]

4. 和解通信ポスチャ
   フォーラム(連邦では FRE 408、それ以外は州相当)に適用可能な
   和解通信保護を調査。保護は行為とコンテキストからアタッチし、単に
   通信にラベル付けすることからではないことに注意。
   intake says: [protected / not protected / case-by-case]
   ドラフトは和解通信マーカーを[含む / 省略]し、ラベルだけでなく実質が
   ポスチャを支えるよう構造化される。確認。

5. 特権放棄スキャン
   ドラフトのいずれかの文が、結論だけでなく我々の内部法的分析の実質を
   明かすか?  [y/n]
   Yes なら、起案前に表現変更。

6. トーンポスチャ
   intake says: [relationship-preserving / measured / scorched-earth]
   これが動詞選択、フレーミング、結果言語を駆動。確認。

7. 事実精度
   ドラフトのすべての事実は検証されている必要あり。「probably true」ではなく
   検証された。まだ検証されていない事実をリストアップ、それらは
   インラインで [VERIFY: ___] フラグされる。
```

ユーザーが各項目に従事したときのみ進む。空白で承認されたチェックリストはチェックリストなしより悪い。

## テンプレート選択

### Step 1: シードドキュメント

`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → 要求書プラクティス → intake の要求タイプのシードドキュメントテーブルをチェック。

- **シードドキュメントあり:** 読む。構造、トーン、署名ブロック、特権マーキング、典型的なセクション順序をマッチ。シードドキュメントがテンプレート。
- **シードドキュメントなし:** 要求タイプの下のソフトテンプレートを使用。

### Step 2: ソフトテンプレート(シードドキュメントがない場合のみ使用)

各々はスケルトン — 見出しと期待されるコンテンツ。事実が要求するなら逸脱。

**支払い要求スケルトン:**
1. 当事者と関係性のコンテキスト(1 段落)
2. 事実 — 義務とその出典(契約 § / 請求書 / 注文)、日付
3. 不履行 — 支払額、期日、何が起きた(または起きなかった)
4. 要求 — 具体的金額、期限、支払い方法
5. 結果 — 弁護士への紹介、利息、手数料、回収、訴訟
6. 保全通知(関連する場合)
7. 署名ブロック

**違反 / 治癒通知スケルトン:**
1. 当事者と契約(契約を特定 — 発効日、当事者)
2. 違反が主張される義務 — 契約セクション、平易な言葉
3. 違反 — 具体的事実、日付、利用可能な証拠
4. 治癒 — 何が具体的に治癒するか;治癒期間(契約からまたは合理的)
5. 治癒失敗の結果 — 終了、損害賠償、契約の特定救済
6. 権利の保全
7. 署名ブロック

**Cease & Desist スケルトン:**
1. 当事者と我々の権利(商標/著作権/契約/コモンロー — 権利を特定)
2. 侵害 / 違反 — 具体的行為、日付、証拠
3. 要求 — 即時停止、削除、過去の使用の説明、書面でコンプライアンス確認
4. コンプライアンス期限
5. 非コンプライアンスの結果 — 訴訟、差止救済、該当する場合制定法的損害賠償、手数料
6. 保全要求(主張された行為に関連する文書、メタデータ、システム)
7. 署名ブロック

**雇用終了要求スケルトン:**
1. 当事者と関係性のコンテキスト(元従業員、雇用日付)
2. 義務 — 違反された雇用後義務(機密性、勧誘禁止、競業禁止、IP 譲渡);契約を引用
3. 主張された具体的行為
4. 要求 — 停止、財産/IP の返還、コンプライアンス確認、該当する場合非誹謗の強化
5. 結果 — 訴訟、差止救済、契約にあれば手数料シフト
6. 非公式解決のオファー(戦略的に適切なら)
7. 保全要求
8. 署名ブロック

**保全要求スケルトン:**
1. 当事者とコンテキスト — どの紛争が予見されているか
2. スコープ — 文書、データ、システム、通信のカテゴリー
3. Custodian — 関連物を持つと予想される指名された個人
4. 日付範囲
5. 積極的保全義務 — 自動削除を停止、メタデータを保全、デバイスを保全
6. 証拠隠滅の結果 — 不利な推論、制裁、手数料シフト
7. 承認リクエスト
8. 署名ブロック

## 起案ルール

0. **マルチロット商品紛争のための分割払い契約デフォルト。** U.C.C.(統一商事法典)下の複数配達商品契約(時間にわたる複数の出荷、ロット、または配達)を伴う契約違反要求では、§ 2-601 の完全な提供ルールや § 2-711 の単一配達買い手救済フレームワークではなく、**U.C.C. § 2-612** の分割払い契約フレームワーク — 「substantial impairment of the value of the installment」 — にデフォルト。

§ 2-601 下の完全な提供は、単一配達商品契約にきれいに適用されます。分割払い契約には clean に転送されません;§ 2-612 はルールを修正: 買い手は、非適合が当該分割払いの価値を substantial に損ない治癒できない場合にのみ、非適合分割払いを拒否できる;契約全体が違反されたと扱えるのは、非適合が契約全体の価値を substantial に損なう場合のみ。

マルチロット商品違反のための要求書を起案するとき:

- 主要フレームワークとして `[CITE: U.C.C. § 2-612 — installment contracts; substantial impairment of the installment]` を引用、§ 2-601 ではなく。
- § 2-711 と § 2-712(カバー)を違反から流れる救済として引用、ただし違反基準は § 2-612 の用語で述べる。
- ドラフトの上の `[SIGNER NOTE:]` ブロックで署名者にフラグ: 「This letter is drafted under U.C.C. § 2-612 (installment contracts), not § 2-601 (perfect tender). The two have materially different breach standards. Confirm the contract's delivery structure supports installment-contract characterization before sending.」
- intake から契約の配達構造が不明確な場合(例: intake は「three lots delivered」と言うが契約が別個のロット配達を要求したのか出荷の利便性のために単一出荷を分割したのかを確認しない)、`[VERIFY: is this an installment contract under § 2-612, or a single-delivery contract split into lots by shipping convenience?]` でフラグ — § 2-612 が適用されると黙って主張しないこと。

単一配達違反: § 2-601 完全な提供フレーミングを使用。分割払い: § 2-612 を使用。混同しないこと。

1. **形容詞より具体性。** 「On March 14, 2026, you sent X」は「You repeatedly and improperly sent X」に勝つ。形容詞は起案者の事実が薄い tell です。

2. **事実は出典に辿れる。** すべての事実主張は文書、日付、または証人にマップ。まだ検証可能でなければ: `[VERIFY: specific claim]`。

3. **プレースホルダとしての引用。** 法的権威が行くところに `[CITE: statute/section/case]`。引用を捏造しないこと。intake で権威が提供されていれば、忠実に使用。

4. **結果言語はトーンポスチャにマッチ。**
   - `relationship-preserving`: 「We hope to resolve this without further action.」
   - `measured`: 「If not cured within [N] days, we will consider our options, including litigation.」
   - `scorched-earth`: 「Failure to cure within [N] days will result in immediate legal action, including [specific relief].」

5. **インラインの代替表現。** トーンがシフトする可能性のあるところで、ドラフトはコンパクトな代替を含む。フォーマット:
   > *The attached invoice of $X remains unpaid.* [or more assertive: *You have failed to pay the attached invoice of $X, due [date].*]

6. **意図しない限り記録上の和解議論なし。** intake が通信をフォーラムで和解通信保護を運ばないとフラグした場合、ドラフトは妥協のオファー、「without prejudice」フレーミング、または和解通信として性格付けされ得る言語を一切含まない。保護は行為とコンテキストからアタッチすることを思い出す;ラベリングだけでは治癒ではない。

7. **ハウススタイルに従った特権マーキング。** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` 特権慣行を正確に適用。

## 出力

### 主要: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/draft-v[N].docx`

`docx` スキルを使用してレターフォーマットの .docx を生成:
- レターヘッド / 送信者アドレスブロック
- 日付
- 受信者アドレスブロック
- Re: 行(簡潔;特権戦略を明かさない)
- 挨拶
- 本文(テンプレート + 起案ルールに従って)
- 結び
- intake に従った署名ブロック

### インチャットレビュー

ユーザーがレビューして編集をリクエストできるよう、読みやすいプレーンテキストとしてドラフトを表示。最終 .docx を書く前に反復。承認されたらディスクに書き込み。

### 送付ゲート(ドラフトの締めくくり注記)

以下を、本文と区別して、インチャットプレゼンテーションと任意の内部プレビューに追記 — レビュアー向けの注記で、レター本文ではなく、レターが出る前に剥がされる:

> This is a draft demand letter for attorney review, not a letter ready to send. Sending it may constitute an attorney communication, create FRE 408 (or state-equivalent) implications, and start the clock on disputes, counterclaims, and statutes. A licensed attorney reviews, edits, and takes professional responsibility before sending. Do not send this draft unreviewed.

### 引用検証

すべての `[CITE:___]` プレースホルダ — および intake またはシードドキュメントから引き出されたすべての引用 — は、人間が citator(引用検証ツール)を通すまで未検証。送付前に、検証パスを実行: 各ケース、制定法、規則を法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)に対して、正確性、good law ステータス、後続履歴をチェック。送付された要求書と提出された文書での捏造または誤引用された引用は制裁になっています。

**Source attribution.** ドラフトの各引用に出処をタグ付け: 法律調査コネクター経由で取得された引用には `[Westlaw]`、`[CourtListener]`、`[Trellis]`、`[Descrybe]`、または特定の MCP ツール名;web 検索で浮上した引用には `[web search — verify]`;トレーニングデータからモデルが思い出した引用には `[model knowledge — verify]`;intake またはシードドキュメントで供給された引用には `[user provided]`。`verify` タグの引用は、ツール取得引用より高い捏造リスクを運び、最初にチェックすべき。タグを絶対に剥がしたり折りたたんだりしないでください — それらは、レターが出る前にどの引用を検証するかについての署名者への最速のシグナル。

**No silent supplement.** 設定された法律調査ツール(Westlaw、CourtListener、Trellis、Descrybe、または事務所プラットフォーム)へのリサーチクエリが、ドラフトが必要とする権威について少数または結果なしを返す場合、見つかったものを報告して停止。尋ねずに web 検索やモデル知識からギャップを埋めないこと。こう言う: 「The search returned [N] results from [tool]. Coverage appears thin for [issue]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) leave the `[CITE:___]` placeholder and stop here. Which would you like?」 弁護士が低い自信の出典を受け入れるかを決定;スキルは決定しません。

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/checklist.md` — 送付後チェックリスト

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`。このヘッダーは内部チェックリストファイルに適用;アウトゴーイングレターはそれを運びません。]

# Post-Send Checklist — [slug]

**Draft version sent:** [v1 / v2 / etc.]
**Sent date:** [YYYY-MM-DD — 送付後に記入]
**Signer:** [名前]

## 送付前(レターが出る前)

- [ ] 署名者による最終読み通し
- [ ] 事実精度: すべての [VERIFY] フラグが解決
- [ ] 引用: すべての [CITE] プレースホルダが埋められ citator を通過(good law を検証)d (live law が引用されている場合)
- [ ] ハウススタイルに従った特権マーキング適用 — 注記: これは外部成果物;相手方に送付されるバージョンには `PRIVILEGED & CONFIDENTIAL — ATTORNEY WORK PRODUCT` ヘッダーを含めない
- [ ] 和解通信マーカー [present / absent] が intake 指定通り、実質がポスチャと整合
- [ ] 内部コピーがクリア(intake 配信リストごと)
- [ ] 保険テンダー送付(ハウスプラクティスごとに必要なら)
- [ ] コンフリクト確認(まだクリアされていない場合)

**レターが送付される前(重大な行為):** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Sending this demand letter has legal consequences — it creates a record, can trigger statutes and counterclaims, and may waive privileges or constitute admissions. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: 相手方と紛争、要求と期限、トーンポスチャ、FRE 408 / 和解通信ステータス、pre-draft ゲートでフラグされた特権と自認リスク、何が悪くなり得るか、送付前に弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしに sent としてマークしない — 下の Send mechanics を実行しない。

## 送付メカニクス

- [ ] 配信方法実行: [certified / email / both]
- [ ] 配信証明を保持(certified 受領、メール既読受領、宅配確認)
- [ ] 配信リストごとにコピー送付

## 送付後

- [ ] コンプライアンス期限カレンダー化: [YYYY-MM-DD]
- [ ] 応答がない場合のエスカレーション計画: [次ステップ + 日付]
- [ ] フォローアップチェックインカレンダー化: [日付 — 通常期限 + 営業日 2 日]
- [ ] `_log.yaml` で案件作成: [yes / no — 下記重要性参照]

## 重要性の判断

**Heuristic says:** [material / immaterial]
**Reason:** [要求タイプ / エクスポージャー / 相手方タイプ]
**Your call:** [material → 案件作成] [immaterial → demand-letters 記録のみ]

If material: `source: demand-letter` 事前入力でこの intake から `/litigation-legal-ja:matter-intake`。
```

### 案件自動作成提案

起案とチェックリスト書き込み後、ヒューリスティックに従って重要性を評価:

- **以下のいずれかなら yes デフォルト:**
  - 要求タイプが `cease-desist`、`breach-cure`、`employment-separation`、または `preservation`
  - 望む結果 $$ ≥ `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` medium 重大度バンド
  - 相手方がランドスケープごとにカスタマー、競合、または頻出敵対者
- **それ以外は no デフォルト**

判断を提示:
> Materiality heuristic: [結果]. [1 文の理由.]
> `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` で追跡される案件を作成?(デフォルト: [yes/no])

ユーザーが受諾: intake からのフィールド事前入力(相手方、タイプ、法域、`source: demand-letter`、初期セオリー、社内ステークホルダー)で `matter-intake` をトリガー。ユーザーが事前入力フィールドをレビューして確認。

ユーザーが拒否: intake `status: drafted` を更新(後でユーザーが確認すると `sent`)。記録は `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/` のみに残る。

## バージョン管理

送付されたドラフトを絶対に上書きしないでください。送付後に修正なら、`draft-v2.docx`。送付バージョン履歴それ自体が、相手方が受信したものの記録です。

## このスキルがしないこと

- **レターを送付する。** 起案のみ。ユーザーが送付。
- **引用をリサーチ。** `[CITE:___]` プレースホルダはプレースホルダのまま。intake でユーザーが権威を提供した場合、使用;それ以外は空白。引用を捏造することは過誤責任エクスポージャー。
- **pre-draft ゲートをバイパス。** `--skip-gate` でも、スキルはドラフトファイルでゲートがスキップされた理由を注記。
- **intake を再書きする。** intake が薄ければ、ユーザーを `demand-intake` に戻す。ドラフトは読み取り元と同じだけ良い。
- **重要性を決定する。** ヒューリスティックがデフォルトを提供;ユーザーの判断が記録。
