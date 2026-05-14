---
name: diligence-issue-extraction
description: >
  VDR 文書を読み、ハウスのカテゴリと重要性基準に従って論点を抽出し、ハウスのメモフォーマットで所見を生成します。
  Use when user says "review the data room", "extract issues from [folder]",
  "diligence review", "what's in the VDR", or points at VDR documents.
  日本語トリガー: 「データルームをレビュー」「[フォルダ] から論点を抽出」「DD レビュー」「VDR の中身」、または VDR 文書を指した場合。
argument-hint: "[VDR folder path or category name]"
---

# /diligence-issue-extraction

1. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` + `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/deal-context.md` をロード。
2. 以下のワークフローを使用。
3. `ai-tool-handoff` を確認 — カテゴリがバルクでツール構成済みなら先にハンドオフ。
4. 文書を読み、重要性フィルタを適用、カテゴリ別に抽出。
5. 所見をハウスメモフォーマットで。Consents をクロージング・チェックリストへハンドオフ。

---

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

VDR には 2,000 件の文書があります。その中のどこかに、ディールにとって重要な 30 件があります。本スキルは `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の DD カテゴリと重要性基準に照らして文書を読み、論点を抽出し、ハウスのメモフォーマットで書き出します。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → DD 構造(カテゴリ、重要性基準)
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → 論点メモフォーマット(所見の記述方法)
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/deal-context.md` → ディール固有の閾値、VDR の場所

deal-context.md がなければ、どのディールか尋ねる。

## ワークフロー

### Step 1: VDR をインベントリ

VDR MCP(Box/Intralinks/Datasite)が接続済みなら、インデックスを引く。VDR フォルダを DD リクエストリストのカテゴリにマップ。ギャップを記録 — VDR にコンテンツがないリクエストリストカテゴリ。

```markdown
## VDR Inventory: [Deal code]

| Request category | VDR folder | Docs | Status |
|---|---|---|---|
| Corporate & Organizational | /01-Corporate | 45 | Reviewed |
| Material Contracts | /02-Contracts | 312 | In progress |
| IP | /03-IP | 89 | Not started |
| [etc.] | | | |

**Gaps:** [VDR コンテンツのないリクエストカテゴリ — フォローアップ依頼が必要]
```

### Step 2: 重要性フィルタを適用

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` / deal-context の閾値に従う。閾値が「契約 >$X」と言うならすべてはレビューしない。

契約については特に:記載金額(ファイル名/メタデータにあれば)または相手方の重要性でソート。閾値に達するか、カテゴリが尽きるまで top-down でレビュー。

### Step 3: 論点抽出

読んだ各文書について、そのカテゴリの標準 DD 懸念に対してチェック:

**Material contracts(重要契約) — 標準抽出セット:**
- Change of control 条項(本ディールで発動?consent 必要?)
- Assignment 制限(契約を買い手に移せるか?)
- Exclusivity / non-compete(買い手のビジネスを制限?)
- MFN(最恵国条項 — 価格制約)
- Termination rights(相手方が本ディールにより離脱可能?)
- 異常な補償または責任エクスポージャー

**Corporate(コーポレート) — 標準抽出セット:**
- Cap table の正確性、未消化ストックオプション/ワラント
- 取引に対する取締役会承認の要件
- 株主間契約の制限(drags、tags、ROFR)
- 子会社構造と社内アレンジメント

**IP — 標準抽出セット:**
- 所有権チェーン(創業者・従業員からの assignment が整っているか?)
- 製品内のオープンソース(コピーレフトリスク)
- 重要 IP がライセンスか自社所有か
- 係属または威嚇 IP 訴訟

**Employment(雇用) — 標準抽出セット:**
- 支配権変動による退職金トリガー(parachute コスト)
- 重要従業員のリテンションリスク
- 係属中の雇用関連訴訟
- 区分リスク(従業員に見える契約者)

**Litigation(訴訟) — 標準抽出セット:**
- 係属案件と引当金
- 威嚇クレーム
- 規制当局照会
- パターン訴訟(消費者集団訴訟など)

### Step 4: 各所見を記述

> **ソース帰属。** 所見が条文、規則、判例、規制者アクションを参照する場合 — 例:適用法下で分析された change-of-control 条項、特定ドクトリンに対して引用された IP 所有権ギャップ、判例引用付きの係属中訴訟 — 引用を出自タグ付け:法務リサーチコネクターから取得した引用には `[Westlaw]`、`[CourtListener]`、または MCP ツール名;Web 検索引用には `[web search — verify]`;トレーニングデータからの想起には `[model knowledge — verify]`;VDR、ディールチームメモ、外部弁護士フィードバックからの引用には `[user provided]`。文書出所の引用(VDR パス、Bates、ファイル名)はそのネイティブ参照を保持。`verify` タグ付き引用は fabrication リスクが高く、最初にチェックすべき。タグを絶対に剥がさない・畳まないこと。
>
> **ユーザー引用の条文に反論する場合、テキストを引用するか、性質付けを差し控える。** ユーザー(またはディールチームのメモ、売り手側の開示)があなたが正しくないと考える主張のために条文を引用し、接続済みリサーチツールまたは VDR から条文テキストを取得できない場合、条文が何を言っているかについて記述を捏造しないでください。代わりにこう述べる:「その条はバルクセール通知 / 後継者責任 / その他の要件として私が想定するものと合致しません — 実際に何を含むか述べるには本文を引く必要があります。 `[statute unretrieved — verify]`」。その後 (a) 設定されたリサーチツールでテキストを取得して引用するか、(b) ユーザーにテキストを貼ってもらうか、(c) 外部弁護士に振る。実在条文の自信ある誤った記述は「知らない」より悪い — 架空のサブチャプターを引用したディールチームメモは、ギャップより信じてもらえなくする難易度が高い。条文を性質付ける各スキルに適用、issue 抽出だけでなく。
>
> **No silent supplement.** 構成された法務リサーチツールへの検索が、所見が必要な法的根拠(例:支配権変動 consent 要件のルール、IP 譲渡ドクトリン、雇用区分テスト)について少数または無結果を返した場合、見つかったものを報告して停止。Web 検索またはモデル知識から尋ねずにギャップを埋め**ない**こと。言う:「検索は [tool] から [N] 件返した。[rule / doctrine] のカバレッジが薄いようです。選択肢:(1) クエリを広げる、(2) 別のリサーチツールを試す、(3) Web 検索 — 結果は `[web search — verify]` タグが付き、依拠前に一次資料で確認すべき、(4) 未検証としてフラグして停止。どれにしますか?」弁護士が低信頼度ソースを受け入れるかを決めます。

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の所見テンプレートに従う。シードメモがこれを使っていれば:

```
Issue #N: [Title]
Category: [request list category]
Severity: [level per house scheme]
Documents: [VDR path + doc name]
Finding: [文書が何を言うか、なぜ重要か]
Recommendation: [price adjustment / indemnity / consent required / rep & warranty / walk]
```

...ならそれを正確に使う。シードメモが箇条書きだったら箇条書きを書く。

**重大度キャリブレーション**(ハウススキームが R/Y/G の場合):
- 🔴 **Red:** ディールバリューまたはストラクチャーに影響。主要顧客 consent を要する change of control。未開示の重要訴訟。IP 所有権ギャップ。
- 🟡 **Yellow:** 注意必要、解決可能。Consent 必要だが取得可能。修正を要するオープンソース。雇用区分リスク。
- 🟢 **Green:** ファイル用に記録。reps と一致。rep を超えるアクション不要。

### Step 5: カテゴリ別にアセンブル

所見をリクエストリストカテゴリでグループ化。カテゴリ内では重大度でソート。

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

> 本出力は特権・秘密(またはその両方)の VDR 資料から派生。ソースの特権・秘密ステータスを継承 — 特権の輪を超えた配布は特権放棄を引き起こす可能性。案件の特権ファイルと共に保管し、配布判断は意図的に行うこと。

# Diligence Issues: [Deal code] — [Category]

**Documents reviewed:** [N] of [M] in category
**Coverage:** [All | >$X threshold | Top N]
**Findings:** [N]🔴 [N]🟡 [N]🟢

---

### Bottom line

[🔴 N blocking · 🟠 N high · 🟡 N medium] — [ディールチームが知るべき 1 つのこと]

---

[各所見をハウスフォーマットで]

---

## Gaps

- [対応文書のないリクエストリスト項目]
- [参照されたが VDR にない文書]
```

## ハンドオフ

- **To ai-tool-handoff:** `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` に従って Luminance/Kira が使われていれば、バルク契約レビューをそちらにハンドオフ。本スキルはニュアンスのある文書(サイドレター、修正、AI ツールが苦手なもの)を処理。
- **To deal-team-summary:** 集約された所見はディールチームブリーフに供給。
- **To material-contract-schedule:** 契約レベルの抽出は開示別紙に供給。
- **To closing-checklist:** 個別のクロージング前アクションを意味する所見はチェックリスト項目になる。ハンドオフは第三者 consent に限らない — 以下もカバー:
  - **Shareholder vote / other closing action** — §280G cleansing votes、要求される株主 consent、要求される取締役会決議、appraisal-rights 通知期間、conversion メカニクス、その他クローズに必要な corporate approval。アクション、承認閾値、条文または定款のソース、タイミング制約を性質付ける。
  - **Regulatory filings and approvals** — 抽出中にフラグされた HSR、CFIUS、外資審査、業界承認。
  - **Counterparties からの consent** — change-of-control、anti-assignment、MFN トリガー consent。
  - **Releases、terminations、pay-offs** — 支配権変動に紐づく雇用 release、payoff letter、リーエン release。
  - **エスクロー / holdback メカニクス** — 抽出が特定論点に紐づくインデムニティ・エスクロー、R&W insurance 引渡物、holdback を表面化した場合。
  クロージング前アクションタグ付き所見はすべて、「consent」とラベル付けされたものだけでなく、closing-checklist に届くべき。所見がグレーゾーン(クロージングアクションが必要かも、クロージング後 covenant かも)にある場合、フラグ付きでハンドオフ — closing-checklist は株式譲渡契約がそうでないと言えばドロップできる。アンダーハンドオフは一方通行のドア;オーバーハンドオフはレビューで是正される。


**Successor liability(後継者責任)。** フラグ:係属または威嚇された tort / 製造物責任クレーム、環境問題と浄化義務、bulk-sale / fraudulent-transfer エクスポージャー(売り手は残債権者への支払いに足る資産を保持しているか?)、売り手のクロージング後解散計画(売り手が解散すれば原告は買い手を追う)、株式譲渡契約に既知エクスポージャーを実際にカバーする assumed/excluded-liabilities 別紙があるか。資産ディールでも、"de facto merger"、"mere continuation"、"product line" ドクトリンは責任を移転可能 — 資産をクリーンに買うと考える買い手側クライアントを驚かせる分析。

## バッチ処理

大規模カテゴリ(300 契約)では、バッチで処理。各バッチ後、running の論点リストを更新し、🔴 を即フラグ — ディールを左右する論点を表面化するためにカテゴリ全体を待たない。

## next-steps デシジョンツリーで締めくくる

CLAUDE.md の `## Outputs` に従い、next-steps デシジョンツリーで終わる。選択肢は本スキルが今生成したものに合わせてカスタマイズ — デフォルト 5 分岐(draft the X、escalate、get more facts、watch and wait、something else)は出発点で固定ではない。ツリーが出力;弁護士が選ぶ。

抽出が約 10 件超の論点を表面化した場合、またはユーザーが求めた時:ダッシュボードを提案(CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs` 参照)。本出力向けに提案を形成する — 重大度別カウント(🔴 / 🟠 / 🟡 / 🟢)、ハウスカテゴリ別カウント、重要性・カテゴリ・VDR ソース付きの論点のソート可能グリッド。

## 本スキルがしないこと

- close なケースの重要性判断はしない。閾値を適用 — borderline は人間が判断。
- reps and warranties を交渉しない。それを情報提供する所見を生成。
- バルク AI レビューを置き換えない。大量条項抽出には `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` に従って Luminance/Kira にハンドオフ。本スキルは判断レイヤー用。
