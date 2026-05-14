---
name: legal-hold
description: リーガルホールドを発行・更新・解除・報告 — ホールド通知を .docx として起案し、_log.yaml の legal_hold フィールドを更新、次回更新をカレンダー化。Use when the user says "issue a hold", "refresh hold", "release hold", or asks for a portfolio-wide hold status report. 日本語トリガー: リーガルホールド発行、ホールド更新、ホールド解除、ホールドステータス
argument-hint: "[slug] [--issue | --refresh | --release | --status]"
---

# /legal-hold

1. `--status`(slug なし)なら: `_log.yaml` を読み、ポートフォリオ全体のホールドレポートを生成。
2. それ以外: `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` + ログ行をロード。
3. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → 特権マーキング、ホールドテンプレートポインタ、エスカレーション規範。
4. 以下のワークフローと参照に従う。
5. フラグでルート:
   - `--issue`: スコープ、custodian、日付範囲、システムをキャプチャ。`legal-hold-v1.docx` を起案。`legal_hold` フィールドを更新。履歴エントリを追記。`next_refresh` を設定(デフォルト +6 ヶ月)。
   - `--refresh`: スコープ/custodian 変更をキャプチャ。次バージョンを起案。`last_refresh` + `next_refresh` を更新。離職した custodian をフラグ。
   - `--release`: 解除日、保持指示をキャプチャ。解除通知を起案。`released:` フィールドを設定。
6. 書き込み前に確認。ユーザーにドラフト通知とログ diff を表示。

---

# Legal Hold

## 目的

リーガルホールド(訴訟ホールド / 証拠保全命令)は、社内弁護士が書く最も機械的で高ステークスの文書です。通知自体はテンプレート化されています。失敗モードは運用的: 発行が遅すぎる、スコープが狭すぎる、決して更新されない、決して解除されない。このスキルは 4 つのフェーズすべてを所有: **発行 → 更新 → (解除) → 追跡**。

ポートフォリオは既に欠落ホールドをフラグします;このスキルがそれらを書きます。

## 法域の前提

保全義務はフォーラムで大きく異なります。連邦コモンロー(Zubulake / Residential Funding / Rule 37(e) 経由)は州プラクティスと異なる;州は互いに、トリガータイミング、スコープ、制裁、証拠隠滅救済について異なる;規制保全義務は一部案件で民事規則の上にレイヤー(SEC Rule 17a-4、HIPAA など)。ドラフトで引用されるトリガー、スコープ、制裁エクスポージャーは、案件で指名されたフォーラムのスタートポイントの読み — 発行、更新、解除前に弁護士と確認。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — ログ行(legal_hold フィールド + ステータス)
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/matter.md` — 案件コンテキスト(相手方、事実、internal_owners からのキー custodian)
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` — 訴訟ホールドテンプレートポインタ、特権マーキング、エスカレーション規範のためのハウススタイル

**コンフリクトゲート — バイパス不可。** ホールドを発行、更新、解除する前に、`_log.yaml` で案件 slug をチェック。`_log.yaml` に案件がない場合、拒否してルート:

> "I don't see [matter slug] in the matter log. Run `/litigation-legal-ja:matter-intake` first so the conflicts check runs and the matter workspace is set up. I won't issue, refresh, or release a legal hold on a matter that hasn't been intaken — the conflicts check is the gate, and a hold issued against an unmanaged matter has no `_log.yaml` row to track `last_refresh` / `next_refresh` / `released` against."

## モード

コマンドはフラグを取る: `--issue | --refresh | --release | --status`。デフォルト(フラグなし)→ プロンプト。

### `--issue` — 初回発行

`legal_hold.issued == false` で案件がアクティブまたは合理的に予見されているときに必須。

**custodian にホールドを発行する前(重大な行為):** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Issuing a legal hold has legal consequences — the scope, custodian list, and timing create the preservation record the company will be judged on if spoliation is argued later. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: 案件とトリガー、提案されたスコープと custodian、調査されたフォーラム固有の保全規則、既知の証拠隠滅エクスポージャー、何が悪くなり得るか(広すぎ/狭すぎ)、弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしに通知を送付しないこと。起案とスコープ決めにはゲート不要 — 発行には必要。

**発行前に該当する保全規則を調査。** 法域と保全義務の出典(コモンロー、民事訴訟規則、規制保全義務、契約)を特定。現在有効なトリガー基準(義務がアタッチするとき)、スコープ基準(何が保全されるべきか)、制裁エクスポージャー(フォーラムの証拠隠滅法理)を確認。プライマリソースを引用。連邦法と州法はトリガータイミング、スコープ、救済について大きく異なる可能性 — 依拠するフォーラムをフラグ。不確実なら、そう述べて、発行前に外部弁護士のサインオフを得る。

> **外部成果物:** 下記の通知は custodian に送付されます。アウトゴーイング通知に `PRIVILEGED & CONFIDENTIAL — ATTORNEY WORK PRODUCT — PREPARED AT THE DIRECTION OF COUNSEL` ヘッダーを含めないこと;テンプレートの attorney-client マーキングを使用。法域と案件に正しいマーキングを確認。

**入力:**
1. **スコープ** — 文書、データ、通信のカテゴリー。具体的に始める: 相手方との契約、[プロジェクト/対象] に言及するすべての通信、関連する財務記録、カレンダーエントリ。`[SME VERIFY — スコープ広すぎ = 運用負担; 狭すぎ = 証拠隠滅リスク]`
2. **Custodian** — 関連物を保有する可能性が高い指名された個人。matter.md internal_owners と一般的な役割(ビジネスリード、雇用の場合 HR パートナー、データの場合 CISO)から提案を引き出す。`[SME VERIFY — custodian リストは防御可能な保全とギャップ議論の差]`
3. **日付範囲** — いつから保全を開始するか(通常: トリガーイベントまたはそれ以前)、現在 + 進行中まで。
4. **システム** — メール、Slack/Teams、ファイル共有、デバイス(該当する場合 BYOD を含む)、Jira/Asana、CRM、レガシーシステム。
5. **緊急性** — 訴訟が既に送達されたかまたは訴訟脅威付きで要求書が受領された場合、これは今日出る。
6. **発効日** — ホールドの日付。

**通知を起案** 各 custodian に対し、`~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` で設定されているならハウステンプレートを使用;それ以外は下のデフォルトテンプレート。

**デフォルトホールド通知テンプレート:**

```
[PRIVILEGED & CONFIDENTIAL — ATTORNEY-CLIENT COMMUNICATION]

DATE: [発効日]
TO: [custodian 名]
FROM: [署名者 — `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` デフォルトに従って]
RE: LITIGATION HOLD NOTICE — [案件略称]

You are receiving this notice because [会社] has determined that [1 文の
紛争 / 調査の説明、prejudicial な詳細を避ける]. The law requires
preservation of documents and communications potentially relevant to this
matter.

EFFECTIVE IMMEDIATELY, you must preserve:

1. All documents, emails, text messages, Slack/Teams messages, and other
   communications relating to [スコープ項目 1].
2. [スコープ項目 2]
3. [スコープ項目 3]
...

This preservation obligation applies to:
- Email (including sent, archived, deleted folders)
- Slack/Teams/messaging platforms
- Shared drives and cloud storage
- Personal devices used for company business (BYOD)
- Paper documents
- Voicemails
- Calendar entries and meeting notes

DO NOT:
- Delete, modify, destroy, or dispose of any potentially responsive material
- Auto-delete or "Inbox Zero" any email or messaging

Coordinate with [リーガル連絡先] before sharing this notice with direct reports
or IT.

Direct questions about this notice or your preservation obligations to [リーガル
連絡先]. You may continue to discuss the underlying business subject matter
with colleagues as needed for your work, but do not discuss this legal notice,
the litigation, or legal strategy.

IF YOU ARE UNSURE whether something is covered, ERR ON THE SIDE OF PRESERVING.

Please acknowledge receipt of this notice by [reply / link / form] within
three business days. If you have questions, contact [署名者 email].

This notice remains in effect until you receive written notice of its
release. You may be asked to reaffirm compliance at periodic intervals.

[署名者 signature block]
```

**送付ゲート(ドラフトの締めくくり注記):** 通知のインチャットプレビューに追記 — 通知が custodian に行く前に剥がす:

> This is a draft legal hold notice for attorney review, not a notice ready to issue. Issuing a hold triggers preservation obligations the company will be judged on in any later spoliation argument, and the notice itself may be discoverable. A licensed attorney reviews, approves, and issues. Do not distribute this draft unreviewed.

**書き込み:**
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/legal-hold-v1.docx` を `docx` スキル経由で
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/history.md` に追記:
  ```
  ## [YYYY-MM-DD] — Legal hold issued

  Hold issued to [N] custodians: [リスト].
  Scope: [1 行サマリー].
  Next refresh: [YYYY-MM-DD (デフォルト発行 + 6 ヶ月)].
  ```
- `_log.yaml` 行を更新:
  ```yaml
  legal_hold:
    issued: true
    issued_date: [YYYY-MM-DD]
    scope: "[1 行サマリー]"
    custodians: [リスト]
    last_refresh: [YYYY-MM-DD]   # 初回発行時は issued_date と同じ
    next_refresh: [YYYY-MM-DD]   # デフォルト: issued_date + 6 ヶ月
    released: null
  ```

### `--refresh` — 定期再確認

更新ケイデンス: デフォルト 6 ヶ月;案件ごとに調整可能。`next_refresh < today` の場合(またはユーザーが手動呼び出し)、スキルは更新通知を起案。

**入力:**
1. 前回更新以降の**スコープ変更**(ディスカバリーで浮上した新トピック、新 custodian、新システム)。
2. **追加または削除する custodian**(離職には特別な扱いが必要 — 下記参照)。
3. 再確認の文言。

**更新通知テンプレート:** 発行に類似;「This is a reaffirmation of the legal hold originally issued [date].」 で始まる。現在のスコープをリスト(必要なら修正)。再承認をリクエスト。

**離職した custodian:** 前回更新以降に custodian が会社を離れた場合、スキルは保全アクション項目としてフラグ — 離職する従業員のファイルとメールアーカイブは個人への通知だけでなく IT レベルで保全する必要があります。これをアクションが必要な別エントリとして history.md に記録。

**書き込み:**
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/legal-hold-v[N].docx`(次バージョン番号)
- `history.md` エントリ
- `_log.yaml`: `last_refresh` と `next_refresh` フィールドを更新;変更があれば `custodians` リストを修正

### `--release` — ホールドをクローズ

通常は案件クローズ時。案件が本当に終わっていることを確認(上訴中でない、再オープン可能性低い、関連請求の出訴期限経過)。

**ホールドを解除する前(重大な行為 — 保全義務は通常保持に戻る):** `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` の `## このプラグインの利用者` を読む。Role が Non-lawyer の場合:

> Releasing a legal hold has legal consequences — once released, custodians may begin deleting material. Release at the wrong time creates spoliation exposure. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [1 ページのサマリーを生成: 案件ステータス、なぜ今解除が提案されているか、関連請求 / 上訴 / SOL エクスポージャー、custodian インパクト、何が悪くなり得るか、弁護士に尋ねるべきこと。]
>
> If you need to find a licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

明示的な yes なしに解除通知を送付しない。

**入力:**
1. 解除権限の確認(通常は署名者または GC)。
2. 解除日。
3. 保持指示 — ホールド下にあった素材はどうなる?(通常保持に戻る? 定義期間の継続保全? アーカイブに転送?)

**解除通知テンプレート:** 1 段落、フォーマル。「The litigation hold issued [date] regarding [matter] is released effective [date]. Normal retention resumes.」

**書き込み:**
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/[slug]/legal-hold-release.docx`
- `history.md` エントリ
- `_log.yaml`: `released: [YYYY-MM-DD]` を設定

### `--status` — ポートフォリオ全体のレポート

`_log.yaml` を読む。レポートを生成:

```markdown
# Legal Hold Status — [today]

## アクティブホールド

| Matter | Issued | Last refresh | Next refresh | Custodians | Status |
|---|---|---|---|---|---|
| [slug] | [date] | [date] | [date] | [N] | [ok / ⚠️ refresh due / ❌ overdue] |

## ⚠️ 注意

- **更新期限超過:** [next_refresh < today の slugs]
- **30 日以内に更新期限:** [list]
- **アクティブ案件でホールド未発行:** [list — high/critical リスクが最初]
- **クローズ案件でホールドが依然アクティブ:** [list — 解除を検討]

## 最近解除

[直近 5 つの解除済ホールドと日付]
```

これは別のコマンド呼び出し(slug なしの `/legal-hold --status`)、OR `/portfolio-status` がポートフォリオロールアップのセクションとして呼び出します。

## portfolio-status との統合

`portfolio-status` スキルは既に「Hold not issued on active litigation」をフラグします。このスキルがそれらのフラグを解決するものです。案件オープン時のブリーフィングでクロスリファレンスする価値あり: `legal_hold.issued == false` なら、`/matter-intake` は `/legal-hold --issue` の実行を提案して締めくくります。

## このスキルがしないこと

- **保全を強制する。** 通知を発行;IT/custodian が保全。スキルは custodian が離れたときにフラグ(IT がシステムレベルで保全できるように)しますが、システムには手を伸ばしません。
- **スコープを単独で決定する。** スキルは案件コンテキストからスコープを提案;ユーザーが確認。スコープ広すぎ = 運用負担。スコープ狭すぎ = 証拠隠滅リスク。ユーザーの判断。
- **レビューなしで自動更新。** `next_refresh` が来ても、ユーザーは更新通知が出る前にスコープ変更をレビューします。
- **通知を送付する。** `.docx` を起案;ユーザーがハウス慣例に従ってメール送付。(将来の統合: Gmail/O365 MCP がユーザーレビュー後に直接送付可能。)
