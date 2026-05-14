---
name: comments
description: 開いている NPRM(規則案告示)コメント期間をレビューし、意思決定を記録し、deadline を追跡します。NPRM のコメントウィンドウが開いていて、deadline を表面化したい、提出するか判断したい、または filing / not-filing / waived の決定を記録したい(--decide CMT-ID)ときに使用。Use when an NPRM has a comment window open and you need to surface deadlines, decide whether to file, or record a filing / not-filing / waived decision. 日本語トリガー: コメント期間、NPRM、規則案、パブコメ。
argument-hint: "[optional: --decide CMT-ID]"
---

# /comments

## 目的

NPRM(規則案告示)には deadline があります。コメントを提出するかどうかは弁護士判断ですが、リスクは「決定がログされないまま deadline が消える」ことです。このスキルは、開いているコメント期間を表面化し、決定を記録します。

## コンテキストロード

`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/comment-tracker.yaml` → 追跡中のすべての NPRM とその status。
`~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md` → デフォルトのコメント決定オーナー。

## デフォルトビュー — 開いているコメント期間

```markdown
## コメント期間トラッカー — [date]

### ⏰ 14 日以内に deadline

| ID | 規制 | Deadline | 残日数 | Decision | Owner |
|---|---|---|---|---|---|
| CMT-001 | [name] | [date] | [N] | Undecided | [owner] |

### 🟡 開いている (>14 日)

[同じテーブル]

### 最近の決定

| ID | 規制 | Decision | 理由 |
|---|---|---|---|
| CMT-002 | [name] | Not filing | [reason] |

---

**未決定合計:** [N]  **deadline <30 日で undecided:** [N]
```

## 決定をログ

```
/regulatory-legal-ja:comments --decide CMT-001
Decision: [filing / not-filing / waived]
Rationale: "[簡潔に — 例: 'Rule doesn't apply to our model' or 'Filing comment on Section 3']"
```

トラッカーを更新。決定が "filing" の場合: filing deadline reminder(コメント deadline マイナス 5 営業日、内部レビュー用)を促す。

## 通知

NPRM の初回検出時(reg-feed-watcher により populated): Slack MCP が設定され `owner_slack` がセットされていれば、コメント決定オーナーに Slack DM。

decision がまだ "undecided" の場合、deadline の 14 日前にリマインダー。
未解決の場合、deadline の 3 日前にリマインダー — 緊急度を引き上げ。

## Consequential-action gate(規制機関にコメント提出 / 回答)

**決定を "filing" としてログする前 — そして、コメントレターや規制機関への回答ドラフトを submission 用に生成する前は常に:** ~/.claude/plugins/config/claude-for-legal-ja/regulatory-legal-ja/CLAUDE.md の `## Who's using this` を読む。Role が **Non-lawyer** の場合:

> 規制機関へのコメントや回答の提出には法的影響があります。これは会社のポジションを公的に表明するもので、規則制定または執行案件で記録に残り、ここで取ったポジションは会社を拘束し、後続手続で使われる可能性があります。弁護士とレビュー済みか?yes なら進めてください。no なら、持参するブリーフは以下:
>
> - 規則制定または調査(規制機関、docket、deadline)
> - 提案されたコメント/回答が何を、どのセクションで言うか
> - 未解決の質問と未確定事項
> - 何が起こりうるか(adverse admissions(不利な認定)、prior position との不整合、業界団体との coordination-of-comment 懸念)
> - 弁護士に聞くこと(提出すべきか;業界団体経由で joint で提出すべきか;取るべきでないポジションがあるか)
>
> もし弁護士を見つける必要があるなら: あなたのプロフェッショナル規制機関の referral service が最速の出発点(米国は state bar、英国(イングランド&ウェールズ)は SRA / Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアは Law Society、または法域の同等機関)。

明示的な yes なしに、このゲートを越えて "filing" decision をログしたり、submission-ready ドラフトを生成しない。トラッキングビュー、deadline リマインダー、"not-filing / waived" 決定はゲート不要。

---

## このスキルがやらないこと

- コメントレターを起草。これは別の弁護士タスクです。
- 提出決定を行う。決定を追跡;弁護士が決定する。
- post-comment 活動を監視。提出されたら、このトラッカーの仕事は終わり — 規則制定の続きは `/regulatory-legal-ja:reg-feed-watcher` でフォロー。

> `comment-decision` の `gap_type` セマンティクス、per-send Slack 確認ルール、comment-tracker.yaml スキーマは **gap-surfacer** リファレンススキルにある — 実質作業前にロード。
