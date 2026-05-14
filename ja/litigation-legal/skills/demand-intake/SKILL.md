---
name: demand-intake
description: 要求書の起案前コンテキスト収集 — 当事者、事実、根拠、レバレッジ、BATNA、特権フィルタを、demand-draft スキルが読む構造化された intake.md に書き出す。Use when the user wants to prep a demand letter, run intake before drafting, or capture context for a payment demand, breach/cure notice, cease-and-desist, employment separation, or preservation demand. 日本語トリガー: 要求書 intake、要求書準備、支払い要求、警告状準備
argument-hint: "[title] [--full]"
---

# /demand-intake

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → 要求書プラクティス、ランドスケープ、リスクキャリブレーション。
2. 以下のワークフローと参照に従う。
3. 適応的 intake を実行(コア 8 は常に;実質的の場合または `--full` なら戦略ブロック)。
4. タイトル + 相手方 + 年-月から slug を生成。
5. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/intake.md` を書き出し。
6. ユーザーと確認: 「Intake saved. Run `/litigation-legal-ja:demand-draft [slug]` when ready.」

---

# Demand Intake

## 目的

起案は下流。価値は事前作業 — 不注意なレターがスキップする質問を強制すること — にあります。レバレッジ、BATNA(交渉が決裂した場合の最善の代替案)、ダウンサイド許容度、特権フィルタ、実際のオーディエンス。それらを考えずに送付される要求書はレターなしより悪いです。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` → 要求書プラクティス(保険テンダーのタイミング、案件作成のための重要性閾値、任意のシードドキュメントテンプレート)、ランドスケープ(相手方種別、常連敵対者パターン)、リスクキャリブレーション(重要性を事前推定)、ハウススタイル。**トーン、応答期間、マーキング、署名者はプラクティスレベルデフォルトではない — それらは以下の `## この案件のポスチャ` ステップで案件ごとに設定。**

## フラグ

- `--full` → 重要性ヒューリスティックに関係なく完全な intake を実行(常に徹底したい弁護士向け)

## intake

### この案件のポスチャ(コアの前に最初に尋ねる)

> **この案件のポスチャ。** 要求書のトーンと条件はケースバイケースであり、プラクティスデフォルトではありません。尋ねる:
> - **トーン:** 抑制的 / 主張的 / 攻撃的?(関係性、金額、訴訟の可能性に依存)
> - **応答ウィンドウ:** 請求に対して何が合理的か?(支払い要求では 14 日が一般的;治癒に 30 日;cease-and-desist に 7 日 — ただし契約またはプロトコルが設定するかも)
> - **マーキング:** 「without prejudice」または「without prejudice save as to costs」マーキングが必要か?(和解通信は必要;クレームの主張はしばしば不要;法域が重要 — 不確実なら尋ねる)
> - **署名者:** あなた、クライアント、GC、指示された弁護士/カウンセル?
> 仮定しないこと。案件ファイルに先行する要求通信があれば読む — それがレジスターを確立。

intake で `## ポスチャ` セクションに `## 当事者` の前に答えを記録。これらの回答が intake の残りと下流のドラフトを支配 — ユーザーがいずれかを空白のままにしてもプラクティスレベルデフォルトに頼らず、再度尋ねる。

### コア — 常に尋ねる(8 質問)

**1. 要求タイプ**
`payment | breach-cure | cease-desist | employment-separation | preservation | other`

**2. 当事者**
- **送信者:** 自社(マルチエンティティなら特定のエンティティ)
- **受信者:** 相手方 — 名前、エンティティ、住所
- **受信者オーディエンス:** 実際に読むのは誰か(GC? CEO? 個人? 社内法務?)
- **関係性:** `customer | vendor | ex-employee | competitor | third-party | other`

**3. トリガーイベント**
- 何が起きたか、いつ(日付が重要 — 出訴期限、通知期間)
- 利用可能な証拠(契約、メール、記録、証人)

*シードドキュメント機会: 「If you can share the underlying contract, correspondence, or evidence, the draft will be materially sharper. Paths work.」*

**4. 法的 / 契約的根拠**
- どの条項 — 該当する特定の契約条項
- 準拠法(法域、準拠法条項)
- 依拠する制定法または規則(プレースホルダ OK — ドラフトはとにかく `[CITE:___]` でフラグ)

**5. 望む結果**
- 具体的なリクエスト。「解決」ではない — 日付 Y までに X ドル支払い、特定の活動 Z の停止、N 日以内の治癒、特定の財産の返還。
- 複数のリクエストがあれば、順序付け(プライマリ vs フォールバック)

**6. 期限**
- これを駆動する外部期限(SoL、進行中の損害ウィンドウ、ビジネスイベント)
- 要求コンプライアンス期限 — 受信者にどれくらいの時間を与えるか。上の `## この案件のポスチャ` でキャプチャされた応答ウィンドウを使用;プラクティスレベルデフォルトに頼らない。

**7. 事前接触**
- これは非公式に提起されたか? いつ、誰によって、どんな形で?
- これまでに応答はあったか?
- なぜ要求書へのエスカレーションが今起きているか?

**8. 配信**
- 配信方法(尋ねる;プラクティスレベルデフォルトなし)
- 署名者 — 上の `## この案件のポスチャ` でキャプチャ
- コピー — 社内ステークホルダー、保険キャリア(プラクティスレベルテンダータイミングルールに従って事前テンダー中の場合)、弁護士

### 戦略 — 実質的または `--full` の場合に尋ねる

重要性ヒューリスティック: 以下のいずれかが真なら戦略ブロックを尋ねる。

- 要求タイプが `cease-desist`、`breach-cure`、`employment-separation`、または `preservation`
- 望む結果のドル価値が `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` リスクキャリブレーションの medium 重大度バンド以上
- 相手方が `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` ランドスケープに従ってカスタマー、競合、または頻出敵対者
- ユーザーが `--full` で実行

**明示的スキップオプション。** 戦略ブロックがトリガーされたとき、ユーザーは回答を拒否できます。率直に尋ねる:

> This is a material demand by the heuristic. The strategic block (leverage, BATNA, tone, privilege filters) is where most of the pre-writing value lives. Skipping it produces a thinner draft.
> - **Answer now** — walk the strategic block (5-7 min)
> - **Answer partial** — walk the subset you feel prepared for
> - **Skip** — proceed to draft with only the core block; I'll flag `strategic_block: skipped` in the intake

ユーザーが Skip を選んだ場合、intake ファイルは記録:

```yaml
strategic_block: skipped        # answered | partial | skipped
skipped_reason: string | null   # ユーザーが提供したらキャプチャ
```

ドラフトスキルはスキップを尊重 — pre-draft ゲートは関係なく実行されるが、戦略ブロック回答に依存するセクションは `[SME VERIFY: leverage/tone/privilege not captured in intake]` マーカーを取得。`/demand-draft` コマンドも 2 度目のプロンプトで、ユーザーが起案前に戦略ブロックを完了したいか尋ねます。

**9. レバレッジと BATNA**
- 何が交渉力を与えるか(契約権、事実レバレッジ、レピュテーション、商業)
- 拒否されたらどうするか — 訴訟する準備があるか? 公表するか? より小さな結果を受け入れるか?
- 彼らの可能性のある BATNA — 彼らの最善の代替案は?(我々が訴えないと思っていれば、要求は弱い。)

**10. ダウンサイド許容度**
- 公表された場合のレピュテーションエクスポージャー
- 先例リスク — このレターは他の案件に影響するパターンを設定するか?
- 規制 / 開示の含意(これは 10-Q 項目になる種類の紛争か?)
- 保険の含意 — テンダーなしで送付するとカバレッジを放棄するか?

**11. トーンポスチャ**
- 上の `## この案件のポスチャ` で既にキャプチャ。ここでは、ユーザーが事実が正当化するように見えるより強いトーンを選んだ場合、または事実が正当化するように見えるより弱いトーンを選んだ場合のトレードオフを探る。
- 明示的に名指すに値する: 攻撃的なトーンは関係を焼きます。ビジネス関係を維持しつつ法的立場を保護したいなら、`measured` が通常正解。

**12. 和解通信ポスチャ**
- フォーラムで適用可能な和解通信保護(連邦では FRE 408 / 米国連邦証拠規則 408 / 和解協議の証拠排除、それ以外は州相当)を調査。このレターは保護されるべき和解通信か? それとも保護されない権利の主張か?
- 保護される場合: ドラフトは和解通信マーカーを含み、ラベルだけでなく実質(妥協の議論)がポスチャを支えるよう構造化。
- 保護は行為とコンテキストからアタッチし、ラベリングだけからではない。マーカーはベルトとサスペンダーの選択。

**13. 特権フィルタ**
- レターに現れてはならない我々の内部分析にあるものは?(検証していない事実、ケースに対する疑念、戦略的推論、先行する和解議論)
- 不適切に表現された 1 文が関連分析の特権を放棄する可能性。何が残るかを明示。

**14. 自認とアコード&サティスファクションリスク**
- 相手方が後で事実または責任の自認と性格付けできるレターの中の何か?
- この要求は別の請求を不本意に満たす(または受諾するふりをする)リスクがあるか?(Accord-and-satisfaction: 「全額支払い」マークの小切手を換金することは、紛争中の債務を終わらせる可能性。)

## intake の書き込み

### Slug

`[type]-[counterparty-short]-[yyyy-mm]`。`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/` で一意性を確認。

### `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/demand-letters/[slug]/intake.md`

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

# Demand Intake: [title]

**Slug:** [slug]
**Demand type:** [type]
**Drafted by:** [counsel]
**Opened:** [YYYY-MM-DD]
**Status:** intake | ready-to-draft | drafted | sent | closed
**Strategic block:** answered | partial | skipped
**Skipped reason:** [該当する場合]

---

## ポスチャ

- **Tone:** [抑制的 / 主張的 / 攻撃的 — 関係性と金額に紐付けた 1 行の根拠付き]
- **Response window:** [N 日 — 請求 / 契約 / プロトコルに紐付け]
- **Marking:** [なし / without prejudice / without prejudice save as to costs / その他 — 根拠付き]
- **Signer:** [名前 / ロール — あなた / クライアント / GC / 指示された弁護士]

*これは intake でキャプチャされた案件ごとのポスチャです。ドラフトスキルはここから読みます。*

---

## 当事者

- **Sender:** [自社エンティティ]
- **Recipient:** [相手方、エンティティ、住所]
- **Recipient audience:** [読むのは誰]
- **Relationship:** [タイプ]

## トリガーイベント

[何が起きたか、いつ、証拠]

## 法的 / 契約的根拠

[条項、準拠法、制定法]

## 望む結果

[優先順位順の具体的リクエスト]

## 期限

- **External:** [SoL、進行中の損害ウィンドウ]
- **Compliance:** [どれくらいの時間を与えるか]

## 事前接触

[履歴、最新を上に]

## 配信

- **Delivery:** [方法]
- **Signer:** [名前/ロール]
- **Copies:** [リスト]

---

## 戦略(該当する場合)

### レバレッジ & BATNA

[我々の力、彼らの可能性のある応答]

### ダウンサイド許容度

[レピュテーション、先例、規制、保険]

### トーンポスチャ

[relationship-preserving / measured / scorched-earth — 根拠付き]

### 和解通信ポスチャ

[フォーラムで保護されるか否か — 推論付き。該当する規則(FRE 408 または州相当)のプライマリソースを引用。]

### 特権フィルタ

[ドラフトに現れてはならないもの]

### 自認 / アコード&サティスファクションリスク

[フラグされた具体的リスク]

---

## シードドキュメント

| Doc | Path |
|---|---|
| [基礎となる契約] | [パスまたは "not shared"] |
| [先行する通信] | [パスまたは "not shared"] |
| [証拠] | [パスまたは "not shared"] |

---

## 重要性評価

**Auto-heuristic says:** [material / immaterial — 推論付き]
**User call:** [material / immaterial / 送付後 TBD]
```

## 書き込み前に確認

ユーザーにドラフト intake を表示。薄いものをフラグ:

> Here's the intake. I notice [thin spots]. Before I save, anything to add?

## 起案へのハンドオフ

締めくくり:
> Intake saved. When ready: `/litigation-legal-ja:demand-draft [slug]`

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` に従って、次ステップの決定ツリーで締めくくります。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではありません。ツリーが出力です;弁護士が選びます。

## このスキルがしないこと

- レターを起案する。それは `demand-draft` — 2 つのステップは意図的に分離されているので、起案前にビジネス入力、外部弁護士相談、または保険テンダーのために弁護士が一時停止できる。
- レターを送付するかを決定する。一部の intake セッションは「実際は送らない — 直接交渉しよう」で終わる。それは有効な結果;intake 記録は依然価値あり。
- コンフリクトチェックを実行。相手方がカスタマーまたは既知のエンティティの場合、これは送付前にコンフリクトをクリアすべきとフラグ(`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` に従って) — チェック自体は matter-intake ワークフローまたはこのスキル外に住む。
