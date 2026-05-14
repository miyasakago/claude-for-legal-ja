---
name: portfolio-status
description: _log.yaml からポートフォリオをロールアップ — リスク分布、近接期限、停滞案件、重要性合計、ステージ分布、フラグされた異常。Use when the user asks "where do we stand", "how many open matters", or wants a portfolio rollup or status across all active matters. 日本語トリガー: ポートフォリオ状況、案件状況、アクティブ案件のロールアップ、現状把握
argument-hint: "[--all | --risk=high | --stale]"
---

# /portfolio-status

1. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` をロード → リスクキャリブレーション(`risk:` フィールドの読み方を定義)。
2. 以下のワークフローと参照に従う。
3. `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` をパース。デフォルトでクローズ案件をフィルタ(`--all` で含む)。
4. ロールアップを生成: リスク分布、14/30/60 日内の期限、30 日超未更新の案件、重要性合計、ステージ分布。
5. 異常をフラグ — クリティカルマーク全て、`next_deadline` 超過、リスクが medium 以上で外部弁護士未割当の案件。

---

# Portfolio Status

## 目的

何を今所有しているか、何が注意を要するか、何が滑り落ちているかを答える 1 つの読み取り。出力はスキャンしやすい — 次の通話まで 3 分の弁護士向けに設計。

## コンテキストのロード

- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/matters/_log.yaml` — 真の情報源
- `~/.claude/plugins/config/claude-for-legal-ja/litigation-legal-ja/CLAUDE.md` — リスクキャリブレーション(risk/materiality フィールドを正しく解釈するため)

## フラグとフィルタ

デフォルト: アクティブ案件のみ(`status: closed` を除外)。

フラグ:
- `--all` — クローズを含む
- `--risk=high`(または `critical` / `medium` / `low`)— リスクバンドでフィルタ
- `--stale` — `last_updated` 30 日超の案件のみ
- `--type=employment` — 案件種別でフィルタ
- `--owner=[name]` — ビジネス/HR/コミュニケーションオーナーでフィルタ

## ロールアップ

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## このプラグインの利用者`]

# Portfolio Status — [today]

**Active matters:** [N]
**Closed (ytd):** [N] *(--all のみ表示)*

---

## リスク別

| Risk | Count | Matters |
|---|---|---|
| Critical | [N] | [slugs] |
| High | [N] | [slugs] |
| Medium | [N] | [カウントのみ — `--risk=medium` で展開] |
| Low | [N] | [カウントのみ] |

## 近接期限

| Within | Matters |
|---|---|
| 14 days | [slug — deadline — brief] |
| 15–30 days | [...] |
| 31–60 days | [...] |

*超過した `next_deadline` は別途下にフラグ。*

## 重要性

| Category | Count | Total exposure (中点) |
|---|---|---|
| Reserved | [N] | [$X] |
| Disclosed | [N] | [$X] |
| Monitored | [N] | — |
| None | [N] | — |

## ステージ別

[テーブル: pleadings / discovery / dispositive motions / trial prep / settlement / appeal]

---

## ⚠️ 異常とフラグ

- **期限超過:** [next_deadline が過ぎた slugs]
- **停滞(30 日超未更新):** [list]
- **コンフリクト未解決:** [`conflicts.status in [pending, not-run]` の slugs]
- **コンフリクトバイパス(オーバーライドアクティブ):** [`conflicts.override.by` が入力された slugs — 手動クリアまで永続フラグ]
- **High/critical リスクで外部弁護士なし:** [list]
- **引当済みで last_updated 60 日超:** [list] — 引当再キャリブレーションが遅れている可能性
- **アクティブ訴訟でホールド未発行:** [list]
- **欠落フィールド:** [slug → field]

---

## 締めくくりのアドバイス

[最初に見るべきものについての 1-2 文。boilerplate ではない — 何かが本当に目立つ場合のみ。]
```

## 異常ルール

これらが装飾的でなくスキルを有用にするチェックです:

1. **期限超過:** `next_deadline < today` かつ `status != closed`
2. **停滞:** `last_updated < today - 30d` かつ `status != closed`
3. **コンフリクト未解決:** `conflicts.status in [pending, not-run]` かつ `status != closed`
3b. **コンフリクトオーバーライドアクティブ:** `conflicts.override.by != null`(自動クリアなし)
4. **ハイリスク未カバー:** `risk in [high, critical]` かつ `outside_counsel.firm == null`
5. **停滞引当:** `materiality == reserved` かつ `last_updated < today - 60d`
6. **ホールドギャップ:** `status in [threatened, active, discovery, trial, appeal]` かつ `legal_hold.issued == false` — 保全義務は合理的予見時にアタッチするため、`threatened` 案件もスコープ内。
7. **欠落フィールド:** 必須フィールド null — `risk`、`materiality`、`status`、`opened`、`conflicts.status`

## 次のステップの決定ツリーで締めくくる

CLAUDE.md の `## Outputs` に従って、次ステップの決定ツリーで締めくくります。このスキルが生成したものに合わせて選択肢をカスタマイズ — デフォルトの 5 つの分岐(draft the X、エスカレーション、追加情報の取得、様子見、その他)は出発点であり、固定ではありません。ツリーが出力です;弁護士が選びます。

ポートフォリオが約 10 案件超、またはユーザーが尋ねたときはいつでも: ダッシュボードを提案(CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs` 参照)。この出力に合わせて提案を形作る — リスク階層別のカウント、近接期限のタイムライン、ステータス、コンフリクトチェック、最終タッチ日付を伴うソート可能な案件台帳。

## このスキルがしないこと

- 決定を下す。注意が必要なものを表面化;ユーザーが優先度を決定。
- 持っていない精度をふりまかない。エクスポージャー中点はラフであり、そうラベル付けすべきです。
- 実際の MMS を置き換える。これは作業メモリーロールアップであり、システム・オブ・レコードではありません。
