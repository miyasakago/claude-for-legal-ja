# Excel 出力仕様

Excel ファイルはほとんどのディールチームが実際に開く成果物。正しく作る。

## Claude in Excel / Office agent が利用可能な場合

Office agent 経由で直接 Excel でワークブックを構築。これが推奨パス、フォーマットを保持し、レビューアーはネイティブツールで作業でき、セルコメントパターンをネイティブサポートする。

## そうでなければ openpyxl を使用

`python3 -c "import openpyxl"` で確認。未インストールなら、インストール(`pip3 install openpyxl`)を提案または CSV にフォールバック。

## ワークブック構造

**Sheet 1: `Review`**(メイングリッド)
- Row 1: Work-product ヘッダー(マージセル、プラグイン設定 `## Outputs` のヘッダー)
- Row 2: 列ラベル
- Row 3+: 文書あたり 1 行
- Column A: 文書名 / パス
- Column B 以降:スキーマ列ごとに 1 列、スキーマ順
- 各データ列の後に `[quote] | [location]` を含む隠し `_source` 列
- データ列セルのセルコメント = 引用と位置(`_source` 隠しでもホバーで表示される)
- 状態でセル塗りつぶし:塗りなし = `answered`、`#FFF2CC`(薄黄)= `unclear` または `needs_review`、`#EFEFEF`(薄灰)= `not_present`
- [data + _source] の各グループ後に `Verified` 列:デフォルトは空。レビューアーが記入。ドロップダウン検証:`✓`、`✗`、`?`。

**Sheet 2: `Flags`**
- `unclear` または `needs_review` としてフラグされたセルあたり 1 行
- カラム:Document、Column、State、Value(あれば)、Quote、Location、Note
- これは検証ワークキュー。列でソートして、レビューアーが類似判断をバッチ化できる。

**Sheet 3: `_schema`**
- `.review-schema.yaml` の列定義、列あたり 1 行:id, label, type, options, prompt
- ファイルを自己文書化。6 か月後に開いたパートナーが何を尋ねたかを正確に見られる。

**Sheet 4: `_summary`**
- 文書数、列数、実行日
- 列ごとの answered / not_present / unclear / needs_review カウント
- 正規化パスがフラグした列のリスト
- 検証リマインダーテキスト

## してはいけないこと

- 信頼度パーセンテージ列を書かない。情報ではない。state + quote がシグナル。
- セルに収めるために引用を切り詰めない。テキストを wrap するか、コメントに完全引用を入れる。
- データ領域でセルをマージしない。弁護士はソート・フィルタする。
- `_schema` と `_summary` シートなしでテーブルを書かない。自己文書化がファイルを信頼可能にするもの。


## Formula injection 対策

Excel、Sheets、CSV 出力でセルを書き込む前に、formula injection を無害化する。相手方由来テキスト(契約引用、当事者名、登録代理人データ、CLM エクスポート)は攻撃者制御。`=`、`+`、`-`、`@`、`	`、``、または `
` で始まるセルは数式として解釈されるか、行構造を壊す。

- **シングルクオートで prefix:** `'=SUM(A1:A10)` → `=SUM(A1:A10)`(実行されずテキストとして表示)
- **文書、ツール結果、ユーザー貼り付けからのテキストを含むすべてのセルに適用。** あなたが制御するカラムヘッダーと生成する計算値は安全。
- **CSV: 埋め込みコンマ、ダブルクォート、改行もエスケープ**(RFC 4180 quoting)。
- これはオプションではない。マクロをトリガーするか DDE 経由でデータを exfiltrate するスプレッドシートをユーザーが Excel で開く事態は、ユーザーへのサプライチェーン攻撃。
