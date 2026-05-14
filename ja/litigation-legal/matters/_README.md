# matters/ — ポートフォリオデータ

このフォルダはポートフォリオを保持します。2層構造:

- **`_log.yaml`** — 台帳。案件ごとに1行。スキルがパース可能。ロールアップの真の情報源。
- **`[slug]/`** — 案件ごとの詳細。物語と履歴。人間が読み・編集する場所。

## レイアウト

```
matters/
├── _log.yaml                  # 台帳(クローズ済を含む全案件)
├── _README.md                 # このファイル
└── [matter-slug]/
    ├── matter.md              # 物語形式の intake + セオリー + ポスチャ
    └── history.md             # 追記専用のイベントログ
```

## Slug の規約

小文字、ハイフン区切り、末尾に年。例:
- `acme-v-us-2026`
- `ftc-inquiry-2026`
- `employment-smith-2026`

年を末尾に付けることで、類似案件が後から発生しても slug が安定します。フォルダ名は slug と完全一致させます。

## 誰が何を書くか

| ファイル | 書き込み元 | 直接編集可? |
|---|---|---|
| `_log.yaml` | `/matter-intake`、`/matter-update`、`/matter-close` | 可。ただし案件の `history.md` に変更内容を反映 |
| `matter.md` | intake 時に `/matter-intake` が作成; `/matter-close` が追記 | 可。セオリー/ポスチャの進化メモなど |
| `history.md` | `/matter-intake` が初期化; `/matter-update` と `/matter-close` が追記 | 実務上は追記専用 — 過去エントリは記録として扱う |

## クローズした案件

このフォルダに残します。削除しないでください。`/portfolio-status` はデフォルトでアクティブロールアップから除外しますが、`/portfolio-status --all` で含めます。クローズ案件はポートフォリオ判断のトレーニングセットです。

## 修正

過去の history エントリが誤っていた場合、編集しないでください。それを参照して訂正する新規エントリを追記してください。訂正の記録自体が、訂正と同じくらい重要です。
