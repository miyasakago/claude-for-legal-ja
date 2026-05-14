---
name: gap-surfacer
description: >
  リファレンス: /regulatory-legal-ja:gaps と /regulatory-legal-ja:comments を支える、共有のギャップ・コメント
  トラッカーフレームワーク。remediation status 付きの未解決ポリシーギャップを追跡、policy-diff からの
  ギャップを取り込み、何が未解決でエイジングしているかを表面化、オーナーへルーティング、per-send
  確認付きで Slack 経由でギャップオーナーに通知。実質作業前に gaps と comments スキルにロードされる。
  日本語トリガー: ギャップトラッカー、ポリシーギャップ、ギャップ管理。
user-invocable: false
---

# Gap Surfacer

> オーナー通知: デフォルトでオン。オーナーをオプトアウトするには、`owner_slack` を空のままにする。

## Per-send 確認 — 例外なし

任意の Slack メッセージ(割当通知、overdue リマインダー、bulk 通知、ステータスレポート)を送信する前に:

1. 何を誰に送ろうとしているかを正確にユーザーに表示: "I'm about to send this to [N] people: [preview]."
2. 明示的な yes を待つ。
3. メッセージに引用、deadline、コンプライアンス結論を含む場合、追加: "⚠️ The citations in this message are unverified — I'm not confirming they're current before sending. Do you want me to add a 'verify before acting' line?"
4. 確認なしに決して送信しない。cadence では。バッチでは。昨日送ったからでは。

確認なしの自動送信は、このプラグインで最も irreversible なアクションです — このプラグイン自身のフッターが「間違っているかもしれない」と言うコンテンツを、確認の術を持たない人々に送る。その組み合わせはレビューをスキップできません。

## Matter context

**Matter context.** プラクティスレベル CLAUDE.md の `## Matter workspaces` をチェック。`Enabled` が `✗`(in-house ユーザーのデフォルト)の場合、この段落の残りをスキップ — スキルはプラクティスレベルコンテキストを使い、matter machinery は不可視。enabled でアクティブな案件がない場合、聞く: "Which matter is this for? Run `/regulatory-legal-ja:matter-workspace switch <slug>` or say `practice-level`." アクティブな案件の `matter.md` を、案件固有コンテキストとオーバーライドのためにロード。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/matters/<matter-slug>/` に書く。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まない。

---

## 目的

ギャップは発見されて、忘れられる。このスキルは閉じられるまで追跡し、閉じる責任のある人に通知する。

## トラッカー

`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/gap-tracker.yaml` にある:

> **comment-tracker.yaml に関する注記:** `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/comment-tracker.yaml` は comments スキルが所有する sibling ファイル。reg-feed-watcher(NPRM を自動的にログ)と comments スキル(ユーザー起動のコメント決定を追跡)が書き込む。このスキルはそれを読まないし cross-reference もしない。comment-tracker スキーマを変更する場合、実際の consumer 両方を更新する。

```yaml
gaps:
  - id: GAP-001
    requirement: "[regが要求するもの]"
    regulation: "[name + cite]"
    policy_affected: "[name or 'new policy needed']"
    gap_type: "partial"  # none | partial | full | new-policy | watch | comment-decision
    owner: "[policy index からの名前]"
    owner_slack: "[Slack user ID または handle(分かれば)]"
    opened: 2026-03-01
    due: 2026-06-01  # reg 施行日、内部 deadline、またはコメント deadline
    status_verified: true  # upstream policy-diff がルールが in force と確認できなかった場合 false;未検証項目は決して 🔴 Overdue にならない
    status: "open"  # open | in-progress | closed | risk-accepted
    notified: false  # 割当通知送信後 true にセット
    resolution: ""  # close 時に埋める
```

**未検証ルール上のギャップを Overdue として分類しない。** 🔴 Overdue 分類は「我々は拘束力ある deadline を逃した」を意味する。ルールの status が未検証(policy-diff が `status_verified: false` をセット、または rule が 12 ヶ月以上前 / 適用日を過ぎて currency 確認なし)の場合、deadline は拘束力がないかもしれない。🟡 "Review needed" を使い、注記: "If this rule is in force as published, this would be overdue by [N] days. Verify rule status before escalating." 未検証ルール項目を `watch` にルーティング、active overdue/due-soon バケットではなく;`watch` revisit cadence は項目がコンプライアンスギャップとして再浮上する前に rule-status チェックを強制する。

**`gap_type` セマンティクス:**

| Value | 意味 | 典型的なリマインダー cadence |
|---|---|---|
| `none` | ポリシーは既に要件をカバー。監査証跡のためだけにログ。稀であるべき — エントリの大半が `none` なら、diff はおそらく間違ったポリシーに対して動いている。 | 自動リマインダーなし。 |
| `partial` | ポリシーはトピックに触れるが、新しい要件を完全にカバーしない。修正が必要。 | due 30 日前。 |
| `full` | ポリシーは新しい要件と矛盾するか silently omit(暗黙的に省略)。書き直しか新セクションが必要。 | due 30 日前。 |
| `new-policy` | これをカバーする既存ポリシーなし。ポリシーを起草する必要がある。 | due 30 日前。 |
| `watch` | forward-looking 項目 — ANPR(規則案前告示)、RFI(情報提供要請)、まだ最終化されていない proposed rule。今日のコンプライアンス義務なし;ポリシー作業は final rule を待つ。`due:` は revisit 日(通常は NPRM 予想日または 1 年先地平)であり、コンプライアンス deadline ではない。 | 自動リマインダーなし;NPRM が出るか revisit 日に再評価。 |
| `comment-decision` | 規則制定前のコメント決定が保留中 — ANPR または NPRM で、チームがコメント提出するか判断中。`due:` はコメント deadline。 | due 21 日前(コメント起草ウィンドウが短いので、コンプライアンスギャップより厳しい)。 |

`watch` または `comment-decision` エントリはコンプライアンスギャップではない — watch スキルと comments スキルが produce する pre-rule 項目の tracking artifact。ステータスレポートで独自のバケットに表面化、午前 7 時に読む弁護士が「規制機関に気づかれる前に修正」vs.「これに目を光らせておく」を一目で区別できるように。

## モード

### Mode 1: policy-diff から取り込み

policy-diff がギャップを見つけたとき、gap-tracker.yaml に追加。重複排除 — 同じ requirement + 同じポリシー = 同じギャップ、二重カウントしない。

**取り込んだ後、オーナーに通知:**

Slack MCP が利用可能で `owner_slack` がセットされている場合:

ギャップオーナーに Slack DM を送信 — ただしこのファイルのトップにある per-send 確認の後にのみ。ユーザーにメッセージをプレビューし、明示的な yes を待ち、それから送信:

```
📋 新しいコンプライアンスギャップが割り当てられました

Gap: [GAP-ID] — [requirement, one sentence]
Regulation: [name + link]
Policy affected: [policy name or "new policy needed"]
Due: [reg effective date]

ギャップトラッカー全体を見る: /regulatory-legal-ja:gaps
```

送信後、トラッカーエントリに `notified: true` をセット。

Slack MCP が利用できない場合: オーナー通知が送信されなかったとステータスレポートで注記し、手動フォローアップ用にフラグ。

### Mode 2: ステータスレポート

```markdown
[WORK-PRODUCT HEADER — プラグイン config ## Outputs に従う — role により異なる;`## Who's using this` 参照]

## 未解決ギャップ — [date]

### ボトムライン

[N gaps need action by [date] — top 3: X, Y, Z]

### 🔴 Overdue

| ID | Requirement | Policy | Owner | Due | 経過日数 |
|---|---|---|---|---|---|

### 🟠 30 日以内に Due

[同じ]

### 🟡 Open

[同じ]

### 👀 Watch items(forward-looking — pre-rule)

[Pre-rule トラッキング — `watch` と `comment-decision` エントリ。これらはコンプライアンスギャップではない。overdue / due-soon バンドが実際のコンプライアンス deadline のみを含むよう、別途表面化。]

| ID | Item | Type (ANPR/NPRM/RFI) | Comment deadline | Owner |
|---|---|---|---|---|

### 進行中

[同じ]

### 最近 close されたもの

[直近 5 件、resolution 付き]

---

**最古の未解決ギャップ:** [ID]、[N] 日
**オーナー別ギャップ:** [breakdown]
**オーナー通知済み:** [N] / [N total gaps]

---

**各未解決ギャップの次のステップ:** `/regulatory-legal-ja:policy-redraft` は `[verify]` タグ付きのマークアップ済みポリシー修正案と変更サマリを produce する。ポリシーオーナーのレビュー用 proposal — ソースドキュメントへの直接編集ではない。

---

**Verify citations before relying on them.** このトラッカーの規制 citations は upstream(reg-feed-watcher と policy-diff によって)で AI 生成され、primary source と照合チェックされていない。ギャップを close するか risk-accept する前 — または attestation、取締役会報告、規制機関への回答で cite する前 — Westlaw、所属事務所の調査プラットフォーム、または発行機関のウェブサイトに対して underlying rule を確認する。AI 生成の規制 citations は時に捏造、誤引用、または stale。upstream から carry forward された source tags(例: `[Federal Register]`、`[web search — verify]`)は各 citation の origin を示す;`verify` タグは捏造リスクが高く、先にチェックすべき。ギャップを表面化するときにタグを strip しない。
```

## Config-dependent fallbacks

このスキルは gap-response オーナーとエスカレーションパスを `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` から読む。必要な値が空または `[PLACEHOLDER]` のまま:

- **Gap-response triager 不在:** 割当を open のままにし、出力に追加: "No triager is set in `## Gap response process`. Assign one with `/regulatory-legal-ja:cold-start-interview --redo` or by editing `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` so new gaps get routed."
- **新規取り込みギャップにオーナー不明(ポリシーライブラリが指名なし):** `owner: [unassigned]` でギャップをログし追加: "[N] gaps were ingested without an owner because the policy library doesn't name one for the affected policy. Fill in the Owner column in the policy library to route them."
- **material overdue gap のエスカレーションパス不在:** overdue として報告し、追加: "No escalation path is set for material overdue gaps. Configure it with `/regulatory-legal-ja:cold-start-interview --redo` or by editing `~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md`."

値が populated されている場合、config について何も言わない。

**Due-date リマインダーロジック(ステータスレポートとスケジュールドエージェント実行時):**

リマインダー cadence は `gap_type` の関数 — コンプライアンスギャップは 30 日前の heads-up、comment-decision 項目は 21 日(起草ウィンドウが短いので tighter)、watch 項目は自動リマインダーなし(NPRM が出たときに再評価)。

status "open" または "in-progress" の各ギャップについて:
- `partial`, `full`, `new-policy`, `none`: due 日が 30 日以内で、過去 7 日にリマインダーが送られていなければ、Slack DM を PREVIEW(subject "⏰ Reminder: compliance gap due in [N] days")し、送信前に per-send 確認を待つ。
- `comment-decision`: コメント deadline が 21 日以内で、過去 7 日にリマインダーが送られていなければ、Slack DM を PREVIEW(subject "💬 Comment-decision deadline in [N] days")し、送信前に per-send 確認を待つ。
- `watch`: 自動リマインダーなし。トラッカーがレビューされるか、同じ規制の NPRM がログされたとき再訪。
- コンプライアンスギャップの due 日が過ぎた場合: レポートで overdue としてフラグし、Slack DM を PREVIEW — per-send 確認を待ってから送信。
- `comment-decision` 項目のコメント deadline が過ぎ、コメントが filed されなかった場合: overdue としてフラグ、Slack DM を PREVIEW(per-send 確認を待つ)、オーナーに `risk-accepted`(意図的な no-comment)または `closed`(コメント filed)に注記付きで更新するよう求める。
- 繰り返し nag を避けるため、リマインダーのタイムスタンプをトラッカーに記録。
- バッチリマインダーも per-send 確認を要する — 「12 件の DM を送ろうとしている」をプレビューして yes を待つのは OK;バッチを silently 撃つのは NG。

### Consequential-action gate(コンプライアンス認証)

**ギャップを resolved として close する前、または規制要件に対するコンプライアンスを認証する任意の出力(内部 attestation、取締役会報告、監査回答、規制機関への回答)を生成する前:** ~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md の `## Who's using this` を読む。Role が **Non-lawyer** の場合:

> コンプライアンス認証 — またはギャップを resolved として close すること — には法的影響があります。認証は後で誤りと示されたら会社に対して使われる可能性があり、premature closure(早すぎる close)は未対処の exposure を残します。弁護士とレビュー済みか?yes なら進めてください。no なら、持参するブリーフは以下:
>
> - ギャップ(requirement、source、policy diff が見つけたもの)
> - 提案された resolution が何をカバーしカバーしないか
> - 残存ギャップまたは曖昧さ
> - 未解決の質問と未確定事項
> - 何が起こりうるか(overbroad な認証、未解決の residual obligation、prior position との不整合)
> - 弁護士に聞くこと(本当に close か;rationale 付きで risk-accept すべきか;外部弁護士の concurrence が必要か)
>
> もし弁護士を見つける必要があるなら: あなたのプロフェッショナル規制機関の referral service が最速の出発点(米国は state bar、英国(イングランド&ウェールズ)は SRA / Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアは Law Society、または法域の同等機関)。

明示的な yes なしに、このゲートを越えてギャップを closed としてマークしたり、コンプライアンス認証を生成しない。ステータスレポートとトラッキングビューはゲート不要。

### Mode 3: ギャップを close

```
/regulatory-legal-ja:gaps --close GAP-001
Resolution: "Policy updated v2.3, approved [date]"
```

status を closed に更新、resolution と close 日を記録。

### Mode 4: ギャップを risk-accept

時に答えは「これは fix しない」です。これは有効な決定ですが、文書化されるべきです。

```
/regulatory-legal-ja:gaps --accept GAP-002
Rationale: "Requirement applies only to [我々が満たさない condition]. Revisit if [trigger]."
Accepted by: [authority を持つ名前]
```

Status → risk-accepted。トラッカーに残る(削除されない)が、未解決ギャップレポートから外れる。

## 統合: reg-change-monitor エージェント

エージェントのダイジェストはギャップ数と最古の未解決ギャップの age を含む。何かが overdue になったら、それがダイジェストのトップに行く。エージェントはまた due-date リマインダーチェックを実行し、未送信の Slack 通知を送信する。

## next-steps デシジョンツリーで締める

CLAUDE.md `## Outputs` に従い next-steps デシジョンツリーで終わる。このスキルが生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルト分岐(draft the X, escalate, get more facts, watch and wait, something else)は出発点であって lock-in ではない。ツリーが出力本体;弁護士が選ぶ。

トラッカーが ~10 を超える未解決ギャップを表面化した場合、またはユーザーが求めたら: ダッシュボードを提案(CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs` 参照)。この出力に合わせて提案を整形 — severity 別カウント、due 日でのギャップタイムライン、owner、status、last-touched 日付付きの sortable グリッド。

## このスキルがやらないこと

- 自分でギャップを close。close には resolution note と note が記述する human action が必要。
- Slack MCP が設定されていない場合に Slack 通知を送る。ステータスレポートでフラグするフォールバック。
- ギャップ 1 件につき 7 日に 1 件を超えるリマインダーを送る。Nag は一度、常時ではない。
