---
name: material-contract-schedule
description: >
  DD 所見から重要契約の開示別紙(Material Contracts Disclosure Schedule)を構築します。
  株式譲渡契約の Material Contract 定義を適用し、契約に規定された別紙形式でフォーマットします。
  Use when user says "build the contracts schedule", "disclosure schedule",
  "schedule 3.X", "material contracts list", or when drafting disclosure schedules.
  日本語トリガー: 「契約別紙を構築」「開示別紙」「Schedule 3.X」「重要契約リスト」、開示別紙のドラフト時。
argument-hint: "[purchase agreement path, or paste the Material Contract definition]"
---

# /material-contract-schedule

1. 株式譲渡契約をロード → Material Contract 定義 + 別紙形式。
2. 以下のワークフローを使用。
3. 定義を DD 所見に適用。エッジケースをフラグ。
4. 契約のフォーマットに合わせる。Consent オーバーレイはクロージング・チェックリストに供給。

---

## Matter context(案件コンテキスト)

**案件コンテキスト。** プラクティスレベルの CLAUDE.md の `## Matter workspaces` を確認してください。`Enabled` が `✗`(インハウスユーザーのデフォルト)であれば、本段落の残りはスキップ — スキルはプラクティスレベルのコンテキストを使い、案件機構は見えません。有効でアクティブな案件がない場合、こう尋ねてください:「Which matter is this for? `/corporate-legal-ja:matter-workspace switch <slug>` を実行するか、`practice-level` と返答してください。」アクティブな案件の `matter.md` を読み、案件固有のコンテキストとオーバーライドを取得します。出力は `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/matters/<matter-slug>/` の案件フォルダに書き出します。`Cross-matter context` が `on` でない限り、他の案件のファイルは絶対に読まないでください。

---

## 目的

株式譲渡契約には「Schedule 3.X はすべての Material Contract を列挙」という rep があります。本スキルはその別紙を DD 所見から構築 — 契約の定義に従ってどの契約が Material か、契約が要求するフォーマットで。

## コンテキストのロード

- 株式譲渡契約ドラフト — "Material Contract" の定義と別紙形式
- `~/.claude/plugins/config/claude-for-legal-ja/corporate-legal-ja/CLAUDE.md` → 重要性基準(PA 定義と異なる可能性 — PA のものを使う)
- diligence-issue-extraction からの DD 所見 — 契約レベルデータ

## ワークフロー

### Step 1: 定義を取得

株式譲渡契約から "Material Contract" の定義を引く — PA 定義が controlling。ディール構造の差(株式 vs 資産 vs 合併)は prong の解釈を変える可能性があり、規制業界オーバーレイ(医療、防衛、金融サービス、通信、政府契約)は PA の外にある consent 要件を追加する可能性。ディールがそれらのオーバーレイを含む場合、適用される anti-assignment または novation ルール(例:連邦契約、政府契約 novation、業界別 consent 法令)を調査し、controlling rule を引用。

PA 定義で探す一般的な prong カテゴリ — これらは PA を読むことの代替ではなく、PA が使うリストが controlling:

- ドル金額閾値(年額または累計)
- 契約期間
- Change-of-control または anti-assignment 条項
- 独占または競業避止
- 上位 N 顧客または供給業者契約
- 不動産リース
- IP ライセンス(in-bound および out-bound)
- 関連当事者契約
- 政府契約
- 通常業務外の契約

PA 定義がテスト。機械的に適用 — PA 定義のいずれかの prong を満たす契約はすべて別紙に。

### Step 2: 定義を所見に適用

DD でレビューされた各契約について:

| Contract | Meets prong(s) | Include |
|---|---|---|
| [name] | [$X+ annual value; CoC provision] | Yes |
| [name] | [none] | No |

**人間の判断のためにフラグするエッジケース:**
- 契約が $X-1(閾値直下)だがビジネスに重要
- 契約は prong を満たすがどのみち解除予定
- 該当するか否かが不明確な口頭合意やサイドレター

### Step 3: 別紙データを収集

含まれる各契約について、別紙は通常以下が必要:

| Field | Source |
|---|---|
| Counterparty name | Contract |
| Contract title/type | Contract |
| Date | Contract |
| Term / expiration | Contract |
| Annual/total value | Contract or management data |
| Which materiality prong it meets | Step 2 analysis |
| Consent required for the deal | Diligence finding |
| VDR reference | Diligence inventory |

既存 DD 抽出から引く。フィールドが欠落していればフラグ — 推測しない。

### Step 4: 契約に合わせてフォーマット

開示別紙にはフォーマットがある — 通常は番号付きリストまたはテーブル、時に契約タイプ別の sub-parts。ドラフト契約の他の別紙のフォーマットに合わせる。

```markdown
## Schedule 3.[X] — Material Contracts

The following are the Material Contracts as of the date hereof:

### (a) Customer Contracts

1. [Agreement Title], dated [date], between [Target] and [Counterparty].
   [Brief description if the format calls for it.]
   [VDR: path]

2. [...]

### (b) Supplier Contracts

[...]

### (c) Real Property

[...]

[etc. — sub-parts per the agreement's definition structure]
```

### Step 5: Consent 追跡オーバーレイ

別途(別紙自体ではなく — これは内部用)、別紙に載った契約のうち consent が必要なものを追跡。

> Consent オーバーレイと別紙の引渡前作業ドラフトは、特権ある DD 資料から派生し、その特権・秘密ステータスを継承 — 特権の輪を超えた配布は特権放棄を引き起こす可能性。締結済み PA に別紙として引渡された別紙自体はディール文書で特権ではない;引渡前に内部注釈をすべて剥がすこと。


| Schedule # | Counterparty | Consent required | Status | Owner | Due |
|---|---|---|---|---|---|
| 3.X(a)(1) | [name] | Yes — CoC §12.2 | Requested | [name] | [date] |

これは closing-checklist に供給。

## クロスチェック

引渡前に:

- prong を満たすすべての契約が別紙に載っている(完全性)
- prong を満たさない契約が別紙にない(過剰開示なし — これは rep であって、データダンプではない)
- 別紙が他の reps と一貫(Schedule 3.X で liens を生成する契約は liens 別紙にも載るべき)
- 各エントリに VDR 引用があり、buyer's counsel が underlying doc を見つけられる

## ハンドオフ

- **From diligence-issue-extraction:** 契約レベル所見が入力。
- **To closing-checklist:** Consent 項目はチェックリストに載る。

## 本スキルがしないこと

- 重要性定義を決定しない — それは株式譲渡契約に記載。
- consent を取得しない — どれが必要かを追跡。
- rep をドラフトしない — rep が参照する別紙を populate する。
