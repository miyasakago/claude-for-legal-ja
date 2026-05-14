---
name: internal-investigation
description: >
  参照: インテイクから最終メモまでの内部調査運営の共有フレームワーク —
  特権下の調査ログ、針探し付きの文書処理、ソースカバレッジ追跡、ログに対
  するQ&A、メモ起草、対象者別サマリー。/investigation-open、/investigation-
  add、/investigation-query、/investigation-memo、/investigation-summary が
  ロードします; 直接呼び出されません。
  (Reference: shared framework for managing internal investigations from
  intake through final memo. Loaded by /investigation-open, /investigation-add,
  /investigation-query, /investigation-memo, and /investigation-summary; not
  invoked directly.)
user-invocable: false
---

# 内部調査スキル (Internal Investigation Skill)

## 案件コンテキスト

**案件コンテキスト。** プラクティスレベル CLAUDE.md の `## 案件ワークスペース` を確認。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、このパラグラフの残りはスキップ — スキルはプラクティスレベルのコンテキストを使用し、案件機構は見えません。有効でアクティブな案件がない場合、尋ねる: 「これはどの案件のものですか? `/employment-legal-ja:matter-workspace switch <slug>` を実行、または `practice-level` と言ってください。」案件固有のコンテキストと上書きについてアクティブ案件の `matter.md` をロード。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/<matter-slug>/` に書き込み。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まない。

---

## 出力ヘッダー

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → `## 出力` の work-product ヘッダーを前置(ユーザー役割により異なる — `## 利用者` 参照)。このスキルが生成するすべてのファイル、ログ、メモ、サマリーはそのヘッダーで始まる。

> **配布規律。** このスキルが作成するすべてのファイル — ログエントリ、メモドラフト、対象者別サマリー、文書ノート — は、基礎となる調査の特権と秘密性ステータスを継承する。特権サークルを超えた配布(調査チーム外の非弁護士への転送、スコープなしの HR への cc、ビジネス側への引き渡し)は調査全体にわたって特権を放棄させ得る。これらのファイルを特権素材が存在する場所に保存、work-product ヘッダーに従ってラベル付け、すべての配布判断を意図的に行う。

## ⚠️ 特権に関する注意 — 進む前に読む

**マーキングは特権を作らない。** 上記ヘッダーは意図された保護を反映し、含めることが重要 — しかしそれ自体は特権を確立しない。任意の出力が実際に特権の対象かどうかは、調査が弁護士指示下か、文書が作成された目的、その後どう使用または開示されたかに依存する。

**案件を開設する前に確認:** この調査は弁護士指示下か? そうでないなら — HR が運営し法務は助言役、または法的助言を得る目的で弁護士の指示で開始されたのでないなら — 特権分析が実質的に変わり、このスキルのデフォルトラベリングは誤導的かもしれない。ログまたはファイルを作成する前に弁護士にその質問をフラグ。

特権適用可能性について疑念があるなら、調査ファイルを作成する前に弁護士が解決すべき。不適切にラベル付けされた素材は、後に特権が争われた場合、ディスカバリーで問題を生み出し得る。

---

## 目的

内部調査は 2 つの方法で失敗する: カバレッジギャップ(収集されなかったソース)とシンセシスギャップ(収集されたが結びつけられなかった証拠)。このスキルは両方を扱う — 何が収集され何が収集されていないかを追跡、文書ダンプを処理して重要なものを浮上させ弁護士を埋もれさせず、任意の時点で特権下のメモに変換できる構造化ログを維持する。

## 特権注記

このスキルが作成するすべてのファイルは上記の特権マーキングを運ぶ。そのマーキングが何をして何をしないかについての完全な留保はこのスキル上部の注意を参照。

## コンテキストをロード

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` を読む → エスカレーション表、メモされた調査プロトコル。

---

## モード 1: 新規案件を開設

`/employment-legal-ja:investigation-open`、または「調査を開設」「[件名] への調査を開始」でトリガー。

### Step 1 — インテイク

以下を単一ブロックで尋ねる:

> 調査ログを開設するためにいくつか必要です:
>
> **案件**
> - プレーンな言葉での申立てまたは懸念は?
> - 申立人は誰か(または何がトリガーしたか — 苦情、タレコミ、監査、マネージャー観察)?
> - 被申立人または対象者は?
> - 申立行為が発生したおおよその時間枠は?
> - これは弁護士指示下か? (はいなら: work product 保護が適用。いいえなら: 進む前に特権リスクをフラグ。)
>
> **調査タイプ**(正しいソースチェックリストを提案するのに役立つ)
> - HR: ハラスメント / 差別 / 報復
> - 財務不正: 経費不正 / 調達違反 / 横領
> - 役員不正: COI / 未開示関係 / ガバナンス失敗
> - 内部告発者: 保護活動への報復
> - その他: 簡潔に説明
>
> **代表と雇用主ステータス**(インタビュー手続を変える並行法的フレームワークを表面化)
> - 被申立人、申立人、または予想される証人は労働組合または団体交渉契約に組合の代表または対象か? (はいなら、Weingarten リサーチ用にフラグ — 調査インタビューでの代表権が適用される可能性があり、インタビュープロトコルを変える。)
> - 会社は公的雇用主(政府機関、州立大学、州または市の機関)か、その他州法の色の下に行動しているか? (はいなら、Garrity リサーチ用にフラグ — 公的部門調査での強制陳述は特別な使用免責結果があり、インタビューがどう実施され文書化されなければならないかを変える。)

いずれかのフラグが点灯したら、適用ルール(Weingarten については NLRA(米国全国労働関係法) / 州レベル公的部門労働制定法; Garrity ラインの判例と州の類似法、加えて第 5 修正)をインタビュー実施前にリサーチ。原典を引用。最新性を検証。プロトコルが調整されるまでインタビューしない。

### Step 2 — 案件ディレクトリとファイルを作成

以下のファイルを作成:

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/investigation-[matter-slug]/log.yaml`:

```yaml
# [WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]
matter: "[matter name]"
matter_slug: "[slug]"
opened: "[ISO date]"
attorney_directed: [true/false]
allegation: "[plain-language summary]"
complainant: "[name/role or anonymous]"
respondent: "[name/role]"
conduct_timeframe: "[approximate dates]"
investigation_type: "[HR/financial/executive/whistleblower/other]"
status: open
last_updated: "[ISO date]"

issues:
  - "[Issue 1 — derived from allegation, e.g. 'alleged hostile work environment']"
  - "[Issue 2 if applicable]"

entries: []

evidentiary_gaps: []
```

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/investigation-[matter-slug]/sources-checklist.yaml`:

調査タイプから生成。下記のソースチェックリスト・テンプレート参照。

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/investigation-[matter-slug]/documents-reviewed.yaml`:

```yaml
# [WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]
matter: "[matter name]"
total_reviewed: 0
total_surfaced: 0
last_updated: "[ISO date]"
documents: []
```

### Step 3 — ソースチェックリスト

調査タイプに基づく適切なチェックリストを生成。弁護士に提示して尋ねる: 「これはあなたの案件に合いますか? 適用しない項目があれば(N/A としてマーク)、またはこの状況に固有の追加ソースがあれば教えてください。」

**HR 調査ソース(ハラスメント/差別/報復):**
```yaml
sources:
  - id: 1
    source: "Complainant interview"
    status: open
    notes: ""
  - id: 2
    source: "Respondent interview"
    status: open
    notes: ""
  - id: 3
    source: "Witness interviews — identify from complainant and respondent accounts"
    status: open
    notes: ""
  - id: 4
    source: "Email/messaging review — parties, relevant date range"
    status: open
    notes: ""
  - id: 5
    source: "HR records — respondent's performance history, prior complaints,
             prior discipline"
    status: open
    notes: ""
  - id: 6
    source: "Prior complaints — any prior complaints against respondent in
             HR system"
    status: open
    notes: ""
  - id: 7
    source: "Comparator data — how were similar situations handled"
    status: open
    notes: ""
  - id: 8
    source: "Relevant policies — harassment, code of conduct, reporting
             procedures (version in effect at time of alleged conduct)"
    status: open
    notes: ""
  - id: 9
    source: "Org chart and reporting relationships at time of alleged conduct"
    status: open
    notes: ""
  - id: 10
    source: "Calendar records — any meetings or events mentioned in accounts"
    status: open
    notes: ""
  - id: 11
    source: "Upjohn warning documentation — confirm interviews were preceded
             by Upjohn warnings and documented"
    status: open
    notes: ""
```

**財務不正ソース:**
```yaml
sources:
  - id: 1
    source: "Expense reports — subject, relevant period"
    status: open
    notes: ""
  - id: 2
    source: "Approval records — who approved the expenses or transactions"
    status: open
    notes: ""
  - id: 3
    source: "Vendor/contractor records — contracts, invoices, payment records"
    status: open
    notes: ""
  - id: 4
    source: "Financial system records — AP, GL entries for relevant accounts"
    status: open
    notes: ""
  - id: 5
    source: "Email/messaging review — subject, approvers, counterparties"
    status: open
    notes: ""
  - id: 6
    source: "Subject interview"
    status: open
    notes: ""
  - id: 7
    source: "Approver interviews"
    status: open
    notes: ""
  - id: 8
    source: "Counterparty/vendor interviews (if accessible)"
    status: open
    notes: ""
  - id: 9
    source: "Audit logs — system access logs for relevant accounts/systems"
    status: open
    notes: ""
  - id: 10
    source: "Prior audits or reviews covering the relevant period"
    status: open
    notes: ""
  - id: 11
    source: "Upjohn warning documentation"
    status: open
    notes: ""
```

**役員不正ソース:**
```yaml
sources:
  - id: 1
    source: "Subject interview"
    status: open
    notes: ""
  - id: 2
    source: "Board/compensation committee records — relevant resolutions,
             minutes, approvals"
    status: open
    notes: ""
  - id: 3
    source: "Employment agreement and any amendments"
    status: open
    notes: ""
  - id: 4
    source: "Equity records — grants, exercises, vesting"
    status: open
    notes: ""
  - id: 5
    source: "Expense reports and approval records"
    status: open
    notes: ""
  - id: 6
    source: "Email/messaging review — subject, relevant counterparties"
    status: open
    notes: ""
  - id: 7
    source: "Conflict of interest disclosures (or absence thereof)"
    status: open
    notes: ""
  - id: 8
    source: "Outside business activity records"
    status: open
    notes: ""
  - id: 9
    source: "Witness interviews — direct reports, peers, board members"
    status: open
    notes: ""
  - id: 10
    source: "Prior complaints or concerns raised about subject"
    status: open
    notes: ""
  - id: 11
    source: "Upjohn warning documentation"
    status: open
    notes: ""
```

**内部告発者ソース:**
```yaml
sources:
  - id: 1
    source: "Complainant interview"
    status: open
    notes: ""
  - id: 2
    source: "Original complaint or tip — written form if exists"
    status: open
    notes: ""
  - id: 3
    source: "Records related to the underlying allegation (the thing
             complainant blew the whistle on)"
    status: open
    notes: ""
  - id: 4
    source: "Records related to any adverse action taken against complainant
             after the protected activity"
    status: open
    notes: ""
  - id: 5
    source: "Decision-maker interviews — who made the adverse action decision"
    status: open
    notes: ""
  - id: 6
    source: "Comparator data — treatment of similarly situated employees
             who did not engage in protected activity"
    status: open
    notes: ""
  - id: 7
    source: "Email/messaging review — decision-makers, relevant timeframe"
    status: open
    notes: ""
  - id: 8
    source: "Timing analysis — proximity of protected activity to adverse
             action"
    status: open
    notes: ""
  - id: 9
    source: "Respondent/decision-maker interviews"
    status: open
    notes: ""
  - id: 10
    source: "Upjohn warning documentation"
    status: open
    notes: ""
```

チェックリストを提示した後、`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/investigation-[slug]/sources-checklist.yaml` に書き込み。

---

## モード 2: データを追加

`/employment-legal-ja:investigation-add`、または「[案件] 調査に追加」、または弁護士が文書またはインタビューノートを貼り付けたときにトリガー。

### Step 1 — 案件を特定

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/` に複数の調査フォルダが存在する場合、このデータがどの案件に属するか尋ねる。1 つのみなら進む。

### Step 2 — データタイプを特定

尋ねる(コンテキストから明確でない場合):
- インタビューノート(誰のインタビュー?)
- 文書バッチ(メール、記録、ファイル)
- 弁護士ノートまたは観察
- Upjohn 警告確認

### Step 3 — 文書プル基準

任意の文書バッチについて、以下のプル基準を適用。文書は次のいずれかを満たすと浮上。基準は意図的にやや積極的にプルするよう設定 — 重要な項目を見逃すよりは偽陽性を浮上させるほうが良い。

**プル基準:**
1. 調査の任意の当事者の名前(申立人、被申立人、先のログエントリで名指された証人)を含む
2. 主要行為時間枠中に当事者により作成または受領
3. 申立タイプに関連するキーワード(インテイクと先のログエントリから特定 — 説明から新たな用語が現れたらキーワードリストを更新)を含む
4. 明示的または暗黙の自認(「すべきでなかった」「これがどう見えるか分かっている」「これを書面にしないで」「これを削除して」)を含む
5. ログに既にある説明と矛盾する文言を含む — 特定の矛盾と衝突するログエントリをフラグ
6. 訴訟で敏感な文言を含む: 差別的用語、脅威、保護特性または活動の議論、申立パターンに合致する財務違反
7. 先の説明で言及されたが文書セットにまだ現れていない文書タイプ(例: インタビューで会議が言及されたがカレンダー招待がレビューされていない)→ 浮上文書ではなく、証拠ギャップとしてログ

**レビューされたすべての文書の処分:**
- `surfaced`: 1 つ以上のプル基準を満たす — ログエントリとしてログに追加
- `reviewed-nothing-significant`: レビュー済み、プル基準を満たさない — 1 行説明のみで documents-reviewed.yaml にログ

**文書バッチ処理後、報告:**

```
Document review complete.
Reviewed: [N] documents
Surfaced: [N] as potentially significant
Logged as reviewed / nothing significant: [N]
New evidentiary gaps identified: [N]

Surfaced items:
[list with one-line description and which pull criterion triggered]
```

このレポートが「見逃した針について」への答え。プル基準は文書化、浮上比率は可視、弁護士はいつでも完全な文書ログをレビュー可能。Q&A モードでは「[topic] について [N] 件のレビュー済み文書に文書を見ていない」が意味ある陳述になるのは、レビューされたすべての文書がログされているから。

### Step 4 — ログエントリを書く

各浮上項目について、`log.yaml` に追記:

```yaml
- entry_id: [auto-increment]
  entry_type: [interview / document / attorney-note / gap]
  date_of_event: "[date the event occurred — not when logged]"
  date_logged: "[ISO datetime]"
  source: "[witness name/role, or document filename/description]"
  source_type: [complainant / respondent / witness / document / attorney-note]
  issues: ["[which investigation issue(s) this entry relates to]"]
  significance: [high / medium / background]
  summary: "[what this entry adds to the record — 2-5 sentences]"
  quote: "[verbatim quote if significant — otherwise empty]"
  contradicts_entry: [entry_id or null]
  corroborates_entry: [entry_id or null]
  credibility_note: ""
  pull_criterion: "[which criterion triggered — for documents]"
  privilege: attorney-work-product
```

証拠ギャップについて:

```yaml
- gap_id: [auto-increment]
  description: "[what document/source should exist but hasn't been found]"
  identified_from: "[which log entry or account raised this]"
  source_to_obtain: "[where to get it]"
  priority: [high / medium / low]
  status: open
```

### Step 5 — ソースチェックリストを更新

追加されたデータがチェックリスト項目に対応するなら、完了または進行中とマークすべきか弁護士に尋ねる。自動で完了マークしない — 弁護士がソースが十分カバーされているかを決定。

---

## モード 3: ログにクエリ

`/employment-legal-ja:investigation-query`、または調査に対する任意の質問(例: 「[witness] は [Y] について何を言ったか」「どの文書が裏付けるか」「まだ何が必要か」「各サイドで最強の証拠は何か」)でトリガー。

回答前にフルログを読む。回答タイプ:

**事実クエリ**(「X は Y について何を言ったか」):
ログエントリから、エントリ ID を引用して回答。ログにトピックに関する何もなければ: 「この調査ログには [topic] に関する情報を見ていません(レビュー済み [N] エントリ)。ギャップとしてフラグする価値があるかもしれません。」

**矛盾クエリ**(「説明はどこで矛盾するか」):
すべての contradicts_entry リンクを浮上。各矛盾について: 矛盾が何か、緊張下のエントリ、矛盾に関連する文書証拠(あれば)を述べる。

**カバレッジクエリ**(「まだ何が必要か」/「ギャップは何か」):
sources-checklist.yaml と log.yaml の evidentiary_gaps を読む。報告:
- チェックリスト項目で未解決のもの
- ログされた証拠ギャップ
- 未収集のソースを参照する説明

**強度クエリ**(「各論点で最強の証拠は何か」):
ログの各論点について特定: 最高重要度のログエントリ、文書裏付け、未解決の矛盾。論点別に提示。

**Upjohn クエリ**(「Upjohn 警告を文書化したか」):
チェックリスト項目と Upjohn 文書化としてタグされたログエントリをチェック。未完了ならフラグ。

---

## モード 4: メモをドラフトまたは更新

`/employment-legal-ja:investigation-memo`、または「メモをドラフト」「メモを更新」でトリガー。

### メモがまだ存在しない — 初回ドラフト

フルログを読む。以下が完了するまでドラフトしない(完了していなければ警告):
- 各オープン論点について少なくとも 1 エントリ
- 申立人と被申立人エントリが存在
- ソースチェックリストをレビュー(高優先度の未解決項目をフラグ)

標準的な内部調査メモランダム実務に従い、以下の構造でメモをドラフト:

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

---

**MEMORANDUM**

To: [Attorney to fill in]
From: [Attorney to fill in]
Date: [Date]
Re: Internal Investigation — [Matter name]
Status: PRELIMINARY DRAFT

---

## Executive Summary

[2-3 paragraphs: allegation in plain terms, investigation scope and
methodology summary, key findings in bullet form (Sustained / Not
Sustained / Inconclusive), recommended actions. Written last but
appears first.]

---

## Background and Scope

**Triggering event:** [What initiated the investigation]

**Allegations investigated:**
[Each issue from the log as a numbered allegation]

**Out of scope:** [Anything explicitly not investigated and why]

**Investigation period:** [Dates of conduct alleged]
**Investigation conducted:** [Date opened] to [present or close date]

---

## Methodology

**Interviews conducted:**
| Witness | Role | Date | Notes |
|---|---|---|---|
[Populated from log entries with source_type = interview]

**Documents reviewed:**
[Summary of document categories reviewed, volume, date range.
Full document log is maintained separately.]

**Other sources:**
[Any other sources from checklist — policies, HR records, etc.]

**Limitations:** [Any sources requested but not obtained, any constraints]

---

## Factual Findings

*[Organized by issue — one section per allegation. Not by witness,
not purely chronological.]*

### Issue 1: [Allegation]

[Narrative of what the evidence shows on this issue. Cite log entry IDs
inline in brackets. Where accounts conflict, present the conflict directly
— do not smooth it over. Documentary evidence presented with quotes where
significant.]

### Issue 2: [Allegation]

[Same structure]

[Continue for each issue]

---

## Credibility Assessment

*[Standalone section. Address only witnesses whose credibility is
determinative — i.e., where the finding on an issue depends on which
account is credited.]*

### [Witness name/role]

**Internal consistency:** [Consistent / Inconsistent — note specifics]
**Corroboration:** [What documentary or other evidence corroborates
or undermines the account]
**Motive:** [Any reason to credit or discount the account]
**Demeanor:** [Attorney's observations if interviews were in person —
leave blank if not applicable or not observed]
**Assessment:** [Credit / Do not credit / Partially credit — with basis]

---

## Relevant Policies

[Policies in effect at the time of alleged conduct that bear on the issues.
Cite the version. Do not cite policies that were adopted after the conduct.]

---

## Conclusions

| Issue | Finding | Basis |
|---|---|---|
| [Issue 1] | Sustained / Not Sustained / Inconclusive | [One sentence] |
| [Issue 2] | ... | ... |

*Findings are based on a preponderance of the evidence standard.*

---

## Recommendations

[Organized by action type:]

**Disciplinary action:** [If any — state the basis, not just the outcome]
**Policy or process changes:** [If any gap in policies contributed]
**Training:** [If indicated]
**Further investigation:** [Any threads not fully resolved]
**Monitoring:** [Any follow-up needed]

---

## Appendix A: Chronology of Events

[Auto-generated from log entries sorted by date_of_event, not date_logged.
Format: Date | Summary | Source (Entry ID)]

## Appendix B: Documents Reviewed

[Summary table from documents-reviewed.yaml]
```

ドラフトを `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/investigation-[slug]/memo.md` に書き込み。

### メモが既に存在する — 更新

メモとログを読む。メモが最終ドラフトされて以降に追加されたログエントリを特定(date_logged をメモの last-updated 日付と比較)。

何が変わったか報告:

```
Since the last memo draft ([date]), the following has been added to the log:

[N] new entries
New issues: [any]
New conflicts: [any]
Resolved gaps: [any]

Sections that need updating:
  Factual findings: [which issues are affected]
  Credibility: [any new credibility-relevant entries]
  Conclusions: [any findings that should be revisited]
  Appendix A: [N] new chronology entries
```

尋ねる: 「フルメモを更新しますか、それとも影響を受けるセクションのみですか?」

更新を適用。先のドラフトを保持。変更されたセクションを `[UPDATED: date]` でマーク、弁護士がレビューするまで。

---

## モード 5: 対象者別サマリーをドラフト

`/employment-legal-ja:investigation-summary`、または「[audience] 向けのサマリーをドラフト」でトリガー。

尋ねる: 対象者は誰か、このサマリーがサポートする判断または行動は何か?

**HR サマリー**(懲戒措置に関する HR 判断のため):
- 何が起きたか(事実サマリー、法的分析なし)
- 各申立に対する発見(Sustained/Not Sustained/Inconclusive)
- 推奨措置
- このサマリーに**ない**もの: 特権分析、信用性方法論、法的露出評価、弁護士の心理的印象
- ヘッダー: "Confidential — HR Use Only — Do Not Distribute"
- エントリ ID または文書引用を含めない — それらはメモに留まる

**リーダーシップ/取締役会サマリー**(ガバナンス判断のため):
- 申立とスコープを 1 段落で
- 主要発見
- ビジネス影響 / 露出(高レベル — 特定の法的分析なし)
- 会社が何をしているか
- ヘッダー: "[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]"

**外部弁護士ブリーフィング**(訴訟または深いレビューへの引き渡し):
- 法的露出分析を含む完全なコンテキスト
- 未解決の証拠スレッド
- 争われたままの信用性問題
- 訴訟で最も重要な文書
- ヘッダー: "[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]"

---

## 結果を伴う行動のゲート(要求または苦情への応答)

**外部応答(EEOC(米国雇用機会均等委員会)/DFEH(カリフォルニア州雇用住宅省)/州機関訴状応答、原告弁護士要求書応答、規制機関応答、または任意の正式な苦情応答)向けのサマリー、メモ、コンテンツを生成する前に:** `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` の `## 利用者` を読む。役割が **Non-lawyer** の場合:

> 要求、訴状、または苦情への応答は法的結果を持つ — ここで取られた立場は後の手続で自認、抗弁の放棄は不注意になり得る、基礎となる調査への特権は失われ得る。この応答を弁護士とレビューしましたか? はいなら進める。いいえなら、彼らに持っていくブリーフ:
>
> - 申立、フォーラム、期日
> - 調査が浮上させたもの(申立別の発見; レビュー済み文書; インタビュー済み証人; Upjohn 警告与えられたかどうか)
> - 未解決の証拠スレッドまたは信用性争い
> - 提案応答が何を述べ、何を暗黙に認めるか
> - 未解決の質問
> - 何が間違いうるか(特権放棄、矛盾する事実陳述、抗弁見落とし)
> - 弁護士に尋ねること(これは正しい理論か、抗弁を保全しているか、外部事務所がこれを引き継ぐべきか、編集または特権ログが必要か)
>
> 弁護士、ソリシター、バリスター、または他の認定法務専門家を見つける必要がある場合: 専門規制機関(米国の州弁護士会、英国 & ウェールズの SRA / Bar Standards Board、スコットランド / NI / アイルランド / カナダ / オーストラリアの Law Society、またはあなたの法域の同等機関)に連絡し、紹介サービスへ。機関と要求書応答は、訓練されていない応答が基礎となる申立より定常的に多くの露出を作る場所。

このゲートを通過した外部応答ドラフトを明示的な yes なしに生成しない。組織内のみで使用される内部メモ、HR サマリー、リーダーシップブリーフィングはこのゲートをトリップしない(ただしこのスキル上部の特権形成留保は適用)。

---

## このスキルが**行わない**こと

- 懲戒判断を下す — 弁護士の発見をサポートし、HR の措置ではない
- 特権を保証 — 特権は調査がどう構造化されたかに依存し、メモがどうラベル付けされたかではない
- 読めない文書を処理 — パースできないフォーマットなら手動レビュー用にフラグ
- インタビューを実施 — インタビューノートをログするが、証人にインタビューしない
- Upjohn 警告を代替 — 与えられたかを追跡するが、自身では与えない

## 次のステップ・デシジョンツリーで締めくくる

CLAUDE.md `## 出力` の次のステップ・デシジョンツリーで終わる。このスキルが生成したものにオプションを調整 — 5 つのデフォルト分岐(X をドラフト、エスカレーション、追加情報を取得、様子見、その他)は出発点で、固定ではない。ツリー**こそ**が出力; 弁護士が選ぶ。
