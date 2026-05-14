---
name: closing-checklist
description: >
  クロージングを止めているものは何か — ステータス、クリティカルパス、クロージングまでの日数を含むクロージング・チェックリストを維持します。
  DD 所見と別紙構築から新規アイテムを自動取り込みし、ステータスを追跡し、ブロッカーを表面化します。
  Use when user says "closing checklist", "what's left to close", "checklist status", "add to the checklist",
  or on a scheduled status pull.
  日本語トリガー: 「クロージング・チェックリスト」「クロージングに残っているもの」「チェックリストの状況」「チェックリストに追加」、
  または定期的なステータス取得時。
argument-hint: "[optional: item ID + status update]"
---

# /closing-checklist

1. `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/closing-checklist.yaml` を読み、以下のモードを使用します。
2. ステータス更新が提供された場合: Mode 3(アイテム更新)。
3. それ以外は Mode 4: ブロッカー項目、クリティカルパス、クロージングまでの日数。

---

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

ディールはチェックリストが完了したらクローズします。リストの全項目を完了。欠落なし。本スキルはリストを維持し、DD から表面化した新規項目を取り込み、何がブロックしているかをチームに伝えます。

## チェックリスト

`~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/deals/[code]/closing-checklist.yaml` に保管。構造:

```yaml
deal_code: "Project Falcon"
target_close: [DATE]
signing_date: [DATE]
last_updated: [DATE]

conditions_precedent:
  - id: CP-001
    item: "HSR waiting period expiration"
    category: "Regulatory"
    responsible: "Buyer counsel"
    due: 2026-04-15
    status: "Filed 2026-03-01, waiting period runs"
    blocking: true
    source: "Purchase Agreement §7.1(a)"

  - id: CP-002
    item: "Acme Corp consent to assignment"
    category: "Third-party consents"
    responsible: "Target — Jane Doe"
    due: 2026-04-20
    status: "Request sent 2026-03-10, no response"
    blocking: true
    source: "Schedule 3.12(a)(4); Acme MSA §14.2"

closing_deliverables:
  - id: CD-001
    item: "Certificate of good standing — Target (DE)"
    category: "Corporate"
    responsible: "Target counsel"
    due: 2026-04-28
    status: "Not started"
    blocking: true
    source: "Purchase Agreement §2.3(b)(iv)"

  # ... etc
```

## モード

### Mode 1: 株式譲渡契約から初期化

署名済み(または最終に近い)株式譲渡契約を読む。抽出:

- 全 condition precedent(クロージング前提条件)(場所は契約により異なる — 実際のセクション見出しを読む)
- 全 closing deliverable(クロージング引渡物)(closing deliverables 別紙または対応セクション)
- クロージング前期限を持つ全 covenant(誓約)

それぞれは契約セクションへの source cite 付きのチェックリストアイテムになる。

**規制 / 承認項目を populate する前に obligation を調査。** 競争法、外資規制、業界規制(例:HSR 型申請、CFIUS、業界監督機関)は法域固有のメカニクス、閾値、タイミングウィンドウを持ち、変動します。PA(株式譲渡契約)から各規制条件の名称を抽出し、その後、現在運用されているメカニクス(誰が申請、いつ、何が second request をトリガー、待機期間は)を調査。一次資料を引用し、最新性を検証。記憶からタイミングを populate しない。

**Material-adverse-effect / material-adverse-change クロージング条件。** PA から定義された語を引く — MAC/MAE のフレーミングは交渉対象で標準ではない。特定の使用文言の準拠法解釈(Delaware、New York、その他の法域はカーブアウトや定量テストを異なって扱う)を調査してから、事象を MAC トリガー候補としてフラグする。

**重要契約からの consent-requirement 抽出**は、準拠法のデフォルトルールと各契約の anti-assignment 文言に依存。デフォルトを前提せず、契約毎に適用ルールを調査。

### Mode 2: DD からの取り込み(「自己更新」部分)

上流スキルがクロージング前アクション付きの所見を生成した時に Mode 2 がトリガー。本モードが取り込む上流スキルと出力タイプ:

- **`diligence-issue-extraction` 所見** — クロージングアクション(consent、株主決議、取締役会決議、規制申請、release、エスクローメカニクス、pay-off letter)用にフラグされた任意の所見。「consents」だけでなく — 全リストは抽出スキルの Handoffs セクションを参照。
- **`material-contract-schedule` の CoC / assignment 項目** — 別紙構築中に表面化した change-of-control 条項、anti-assignment 条項、MFN トリガー。
- **`deal-team-summary` 出力** — エグゼクティブ層のブリーフは抽出所見を集約し、個別抽出メモの機械的読込では見落とすクロージングアクション項目を表面化することがある(例:複数雇用契約にまたがる §280G cleansing vote、または合成 consent パッケージ)。Mode 2 はディールフォルダの最新 deal-team-summary を読み、チェックリストに対してそのクロージングアクション項目を照合。deal-team-summary がクロージング前アクション必要としてフラグした未掲載項目は追加。

引き渡しスキーマは consent だけでなく、クロージング前アクションの全範囲をカバーする:

```yaml
handoff:
  # Required fields
  item: "[Counterparty or action, one line]"
  category: "[Third-party consents | Shareholder / board action | Regulatory filing | Release / termination | Escrow / holdback | Closing deliverable]"
  source: "[Contract name / statutory section / VDR path + Bates]"
  blocking: true  # unless the agreement has a materiality qualifier
  severity: "[🔴 / 🟠 / 🟡 / 🟢 — carried from upstream, see severity-floor rule in CLAUDE.md]"

  # Consent / third-party action fields
  counterparty: "[e.g., Dunmore Holdings LLC]"
  guarantor: "[e.g., Buyer parent guaranty required, or N/A]"
  conditions: "[any substantive condition the counterparty attached — e.g., 'replacement guaranty from buyer parent required before consent effective']"
  notice_deadline: "[e.g., 30 days prior to closing, or specific date]"

  # Corporate action fields
  approval_body: "[Shareholders | Board | Committee | Regulator]"
  approval_threshold: "[e.g., 75% disinterested stockholder vote for §280G cleansing]"
  statutory_or_charter_source: "[e.g., IRC §280G(b)(5)(B); Charter Art. IV §2]"

  # Timing
  estimated_time_to_complete: "[e.g., 30 days]"
  must_occur_before: "[e.g., closing | signing | end of hiatus period]"
```

上流スキルが populate した全フィールドを保持する。「Dunmore consent required, with replacement guaranty condition and 30-day notice」はチェックリスト上で 3 要素(consent, guarantor, notice)すべてが表面化すべきで、「Dunmore consent to change of control」に圧縮しない。上流スキルが重大度を提供する場合、それを運ぶ — `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の cross-skill severity floor ルール参照。

チェックリストに追加。De-dupe(重複排除)は freeform のアイテム名ではなく(counterparty + action type)で行う — Dunmore consent と Dunmore release は両方 Dunmore を名指していても別アイテム。De-dupe する際は上書きせずフィールドをマージする:一つのハンドオフが `guarantor` を populate し、後のハンドオフが `notice_deadline` を populate した場合、チェックリスト行は両方を運ぶ。

### Mode 3: ステータス更新

ユーザー(または dataroom-watcher エージェント)がステータス更新を提供。該当アイテムを見つけ、ステータスと last-updated を更新。

```
/corporate-legal-ja:closing-checklist
CP-002: Acme responded, consent form attached, needs countersignature
```

### Mode 4: 何がブロックしているか

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

> 本ステータスレポートは株式譲渡契約、DD 所見、内部ディール記録から派生しています。これらの特権・秘密ステータスを継承します — 特権の輪(相手方、より広い業務チーム)を超えた配布は特権放棄を引き起こす可能性があります。送信前に配布リストを確認してください。

## Closing Checklist Status — [Deal code] — [date]

**Target close:** [date] ([N] days out)
**Items:** [N] total — [N] done, [N] in progress, [N] not started

### 🔴 Blocking and at risk

| ID | Item | Due | Status | Days to due |
|---|---|---|---|---|
| [CP-XXX] | [item] | [date] | [status] | **[N]** |

### 🟡 Blocking, on track

[same table]

### ✅ Complete

[N] items — [collapsed list]

### Not blocking (post-closing, informational)

[N] items

---

**Critical path:** [The item(s) that, if they slip, push the close date]
```

## クリティカルパス分析

すべてのブロッカー項目が同価値ではない。30 日かかる consent はクリティカルパス。2 日で取れる good-standing certificate はクリティカルパスではない、両方ともブロッカーであっても。

各ブロッカー項目について time-to-complete を見積もる。`(due date - today) < estimated time` のものは at risk。それらは各ステータスレポートの最上位に置く。

チェックリストが約 10 件超、またはユーザーが求めた時:ダッシュボードを提案(CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs` 参照)。本出力向けに提案を形成する — ステータス別カウント(done / in progress / not started / at risk)、ワークストリーム別のクリティカルパスビュー、アイテム・オーナー・期限・期限までの日数のソート可能グリッド。

## 統合:dataroom-watcher エージェント

エージェントはチェックリストを毎日確認し、接続されていればメール / Slack からステータス更新を取得し、「何がブロックしているか」レポートをディールチームチャンネルに投稿する。Mode 4 がエージェントの出力。

## Consequential-action gate(クロージングの認証)

**「ready to close / all CPs satisfied」認証またはクロージングメモを生成する前に:** `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` の `## Who's using this` を読む。Role が **Non-lawyer** の場合:

> クロージング条件が満たされた旨を認証する(またはそれを主張するクロージングメモを生成する)ことは法的帰結を持ちます — それは資金フローとクロージング後義務を駆動するシグナルです。弁護士とレビュー済みですか? Yes なら進める。No なら、彼らに持って行くブリーフ:
>
> - 全 CP リストとステータス(完了、進行中、未着手)
> - 完了の証跡が弱いまたは欠落している項目
> - 期限内にクローズしない項目に必要な waiver や side letter
> - オープンな質問(まだ係属中の相手方 consent、MAC / bring-down リスク)
> - 弁護士に聞くこと(これは closed と呼べる準備ができているか、誤って通過させるべきでない条件はないか、例外リストに載せるべきものは何か)
>
> 弁護士、ソリシター、バリスター、その他認可された法律専門家を見つける必要があれば:あなたの専門規制者(米国の州弁護士会、英国(イングランド・ウェールズ)の SRA / Bar Standards Board、スコットランド/北アイルランド/アイルランド/カナダ/オーストラリアの Law Society、または当該法域の同等機関)に紹介サービスを問い合わせてください。

このゲートを明示的な Yes なしに通過した最終「ready to close」認証は生成しないこと。ステータス追跡や「何がブロックしているか」レポートはゲート不要。

---

## 本スキルがしないこと

- consent の取得、フォームの提出、文書の起案はしない。それらが必要な旨を追跡するのみ。
- 何がブロッカーかを決定しない — 株式譲渡契約が決める。本スキルは契約を読む。
- ディールをクローズしない。クローズできるタイミングを伝える。
