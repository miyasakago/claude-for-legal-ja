# コミュニティスキル著者向け Freshness フィールド

スキルが `references/` 配下に参考コンテンツ — 規制、制定法、手続き、書式、現行法に紐づくチェックリスト — をバンドルする場合、その freshness を `SKILL.md` フロントマターで宣言してください:

```yaml
---
name: my-legal-skill
description: ...
last_verified: 2026-04-15       # バンドル参照が現行であることを最後に確認した日
freshness_window: 6 months      # 検証が有効である期間(デフォルト: regulatory/statutory コンテンツは
                                # 6 か月、procedural/stylistic は 12 か月)
freshness_category: regulatory  # regulatory | procedural | stylistic | stable
verified_against:               # どこで検証したか — ユーザーが自身でチェック可能な URL
  - https://www.ecfr.gov/current/title-16/part-312
  - https://www.federalregister.gov/...
---
```

## なぜ重要か

2 年前に最後に触れられたスキルが、廃止された規制を出荷し続けることがあります。バイト同一のファイルはコミットベースのアップデーターには永遠に現行に見えます。害は、ユーザーがスキルを起動して古いコンテンツに依拠したときに発生します — インストール時に警告を読んで忘れたときではなく。

## これらのフィールドで何が起きるか

- builder-hub の **skill-installer** は、実行前に `last_verified` を `today + freshness_window` に対してチェック。ウィンドウを超過していれば、実行前に警告を表面化。
- **skills-qa** レビューは、バンドル `references/` を持つが `last_verified` がないスキルを Some Concern としてフラグ。
- **auto-updater** は、git SHA が変わっていなくても、古い `last_verified` を再検証トリガーとして扱う。
- ユーザーの freshness 閾値(コールドスタートで設定)は著者のウィンドウより **厳しく** することができる — 2 つのうち厳しい方が勝つ。

これらのフィールドがなければ、ハブはスキルを「freshness unknown」としてフラグし、インストール時と起動時にユーザーに警告します。

## 受け入れられる値(厳格)

ハブはフロントマター・フィールドを外部公開元が書いたデータとして扱い、指示としては扱いません。下記の形に合う値のみが honored されます。それ以外は無視され(ハブは `unknown` に置換)、インストール時に所見として表面化されます。

| フィールド | 受け入れ形 |
|---|---|
| `last_verified` | ISO 8601 日付: `YYYY-MM-DD`(例: `2026-04-15`)。未来日は `unknown` として扱う。 |
| `freshness_window` | `N days`、`N months`、または `N years`、`N` は正の整数 ≤ 120。 |
| `freshness_category` | 次のいずれか: `regulatory`、`procedural`、`stylistic`、`stable`。 |
| `verified_against` | URL のリスト。各々 `https://`(または `http://`)、ホスト名と任意のパス。クエリ文字列とフラグメントは表示前に除去。最大 10 エントリ、各 2,048 文字以下。 |

これらのフィールドのいずれかにフリーフォーム散文、複数行文字列、指令、役割変更言語、異常な Unicode、またはエンコードされたコンテンツがある場合、インストール時に拒否されます。インストーラーは raw 値をインストールログに記録し(切り詰め、引用符付き、解釈しない)、フィールドを missing として扱います。

## カテゴリ

- **regulatory** — 規則、制定法、機関ガイダンス。動きが速い。
- **procedural** — 裁判所規則、提出手続き、手続きに紐づく書式。
- **stylistic** — ハウススタイル、フォーマットテンプレート、条項ライブラリ。
- **stable** — 歴史的参照、バー試験アウトライン、月単位ではなく年単位で動く教義的入門。

迷ったら、狭い(速く動く)カテゴリを選ぶ。ユーザーの閾値がより厳しくしたければ閉めます; 著者の値は floor ではなく ceiling です。

## 「last verified」の本当の意味

「last edited」ではない。「last commit」でもない。**著者が、`verified_against` の URL を最後に開き、バンドル参照がそれらのソースが言うものを依然として反映していると確認した時点。** バンドル PDF が 16 CFR 312 の古いバージョンだが、現在の eCFR が異なるテキストを示しているなら、検証は失敗しました — 参照を更新して新しいコミットをプッシュするか、参照が再びソースと一致したら `last_verified` のみを更新してください。

実際の再検証なしに `last_verified` をバンプし続けるスキルは、日付を古くなるに任せるよりも悪いです。古い日付は、著者が何をしたかについて正直です。バンプされた日付は、ユーザーが依拠する主張です。

## `freshness_category: stable` を設定する時

めったにありません。教義のテキスト(例: promissory estoppel の要素)やフレームワークの構造(例: FRCP discovery タイムラインの形)をバンドルするスキルは stable です。具体的な規則テキスト、具体的な閾値、具体的な書式、または具体的な手続き的期限をバンドルするスキルは、たとえ基礎となる教義が stable でも、stable では NOT — バンドルされたアーティファクトが古くなるものです。

迷ったら: stable ではない。
