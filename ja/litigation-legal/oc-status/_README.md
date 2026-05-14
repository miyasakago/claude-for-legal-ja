# oc-status/ — 週次の OC ステータス依頼ドラフト

`/litigation-legal-ja:oc-status` の出力先です。実行日ごとのフォルダが日次で作成され、各フォルダには案件ごとのマークダウンファイルと `_summary.md` が格納されます。

## レイアウト

```
oc-status/
├── _README.md                       # このファイル
└── [YYYY-MM-DD]/
    ├── _summary.md                  # 実行内容、スキップ内容と理由
    ├── [slug-1].md                  # 案件ごとに1メール下書き
    ├── [slug-2].md
    └── ...
```

Gmail MCP が認証済みの場合、ユーザーの受信トレイにも Gmail 下書きが作成されます。マークダウンファイルが永続的な記録で、Gmail 下書きが実行レイヤです。

## 実行頻度

スケジュール設定時は週次(月曜日 AM)。`/litigation-legal-ja:oc-status --setup-schedule` でスケジュールを登録できます。

随時実行は `/litigation-legal-ja:oc-status`(デフォルトフィルタ)または `/litigation-legal-ja:oc-status --slug=[slug]`(単一案件)。

## メンテナンス

古い日付フォルダは溜まります。OC からの回答が来て案件履歴が更新された後は不要です。30日以上経過したものは自由に削除して問題ありません。
