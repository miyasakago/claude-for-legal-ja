---
name: ramp
description: >
  学生の学期オンボーディング — クリニック手順、ツールウォークスルー、実案件前の
  練習エクササイズ。教授がセットアップでアップロードしたハンドブックを読み、
  インタラクティブに教えます。新規クリニック学生が「オンボーディングしてほしい」
  「クリニックは初めて」「はじめに」と言ったとき、または学期開始時に使用。
  1 ページのリファレンスには --card を渡してください。
  (Use when a new clinic student says
  "onboard me", "I'm new to the clinic", "getting started", or at the start of
  each semester; pass --card for the one-page reference.
   日本語トリガー: ランプアップ、ramp、オンボーディング、新学期スタート、新規学生)
argument-hint: "[--card で 1 ページリファレンス]"
---

# /ramp

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` がセットアップされているか確認。プレースホルダーがあれば: 「Ask [professor] to run `/legal-clinic-ja:cold-start-interview` first.」
2. 下記のウォークスルーを使用。
3. ウォークスルー: クリニックコンテキスト(ハンドブックから) → コマンド → 練習エクササイズ(模擬インテイク、練習ドラフト、リサーチロードマップ) → 検証習慣。
4. `--card`: 1 ページリファレンスカードを生成。

```
/legal-clinic-ja:ramp
```

```
/legal-clinic-ja:ramp --card
```

---

# Ramp: 学期オンボーディング

## 目的

学期ごとに、クリニックはワークフォース全体を失い、ゼロから再構築する。新しい学生は手順、ケース管理、提出規約、プラクティスエリアの基礎を学ぶ必要があり、それから役に立つようになる。伝統的には PDF を読み、教授に同じ質問を毎学期する数週間かかる。

このスキルはガイド付きウォークスルー。コールドスタートで教授がアップロードしたもの — ハンドブック、提出ガイド、ローカルルール — を読み、インタラクティブに教える。学生が実際のクライアントの前に出る前に、低リスクの設定でツールを試せる練習エクササイズ付き。

**対象: 学生。** 教授はこれを実行しない(彼らは `/cold-start-interview` を実行)。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → クリニックプロファイル、プラクティスエリア、法域、ハンドブックパス、監督スタイル、プラクティスエリアテンプレート。

そのファイルが欠落しているか、まだプレースホルダーがある場合: 「The clinic hasn't been set up yet. Ask [supervising professor] to run `/cold-start-interview` first.」

## ウォークスルー

### オープニング

> Welcome to [clinic name]. I'm going to walk you through how this clinic works and how to use these tools — about twenty minutes, and you can pause anytime. By the end you'll have run a practice intake, drafted a practice document, and you'll know what to do when you get your first real case.
>
> One thing up front: everything I generate is a starting point, not a final answer. You do the analysis. [Professor] reviews your work [per supervision style]. I handle the formatting and the first draft so you spend your time on the lawyering, not on writing "Dear Judge" for the twentieth time.

### Part 1: このクリニック(5 分)

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` と取り込まれたハンドブックから読む。インタラクティブにカバー:

- **プラクティスエリア** — クリニックが扱うもの、扱わないもの(範囲外の問題で来談する人をどこに紹介するか)
- **クライアント** — 彼らは誰か、何に直面しているか、言語
- **法域** — どの裁判所、どの裁判官、ローカルの癖は何か
- **ケース管理** — 案件がどう追跡されるか、ファイルがどこに住むか、よく文書化された案件はどう見えるか
- **監督** — このクリニックでレビューがどう機能するか(CLAUDE.md の監督スタイルに従って)。具体的に: 「クライアントまたは裁判所に何かが行く前に、[レビューキューに入る / Professor X と確認 / etc.]」

講義しない — 理解を確認。「クライアントが立退き通知を持って来談し、無書類であると言及した場合、何をするか?」(答え: 両方の論点をインテイクで注記; 在留資格問題はクリニックの範囲によって紹介または教授へのフラグが必要かもしれない。)

### Part 2: コマンド(5 分)

学生が実際に使う各コマンドをウォークスルー:

| コマンド | いつ使うか | 何を得るか |
|---|---|---|
| `/client-intake` | クライアントインタビュー | 論点発見、利益相反フラグ、トリアージ付きのフォーマット済み案件サマリー |
| `/draft [doc type]` | 一般文書の初稿が必要 | 案件ノートから埋められたプラクティスエリアテンプレート — *出発点であって完成ではない* |
| `/memo` | 案件を内部分析したい | リサーチギャップをフラグした IRAC フォーマットメモ |
| `/research-start [issue]` | 法的リサーチを始める | ロードマップ: チェックする法令、判例領域、検索語 — *典拠ではなくリード* |
| `/status [audience]` | 誰かに案件を更新 | クライアント / 教授 / 裁判所に合わせたサマリー |
| `/client-letter [type]` | 定型コレスポンデンス | テンプレートからのアポイント確認、書類請求、ステータス更新 |

各について: 何をするか、明示的に何をしないか、依拠する前に学生が何を検証するか。

### Part 3: 練習エクササイズ(8 ~ 10 分)

**低リスク。模擬クライアント。実際のツール。**

**Exercise 1 — 練習インテイク:**
> 模擬クライアントシナリオ: [プラクティスエリア適合の仮想 — 例: 住居クリニックなら「Maria が先週火曜に 3 日 quit 通知を受けた。仕事を失った後、家賃が 2 ヶ月遅れ。アパートは 11 月からヒーターが壊れている。子ども 2 人。」]
>
> `/client-intake` を実行し、私が Maria であるかのようにインタビューしてください。Maria のように答えます。終わったら、生成された案件サマリーを見てください — どんな論点を発見したか? habitability 抗弁を捕捉したか?

ディブリーフ: インテイクが捕捉したもの、*学生* がもっと深く探るべきだったもの、教授にフラグされるもの。

**Exercise 2 — 練習ドラフト:**
> Maria のインテイクを使って、`/draft eviction-answer` を実行。初稿を得る。
>
> 読む。何が正しいか? 何が間違っているか? [Professor] に見せる前に何を変えるか?

ポイント: ドラフトは有能だが最終ではない。学生は批判的に読むことを学ぶ、受け入れるのではなく。

**Exercise 3 — リサーチロードマップ:**
> `/research-start "habitability defense to eviction in [state]"` を実行。ロードマップを得る — 法令、判例領域、検索語。
>
> それらの引用は検証されていない。意図的に。ロードマップから 1 つの法令を選び、それが現行で、ここに適用されるかをどう検証するかを教えて。

ポイント: `/research-start` は出発地点であって引用ではない。学生はリサーチを依然行う。

### Part 4: 検証習慣(2 分)

重要な習慣:

- **すべての出力は出発点。** 君が批判的に読まずにクライアントや裁判所に行ったなら、何かが間違っている。
- **すべての引用を検証** してから何かに入れる。`/research-start` はリードを与え、典拠ではない。
- **法域固有の詳細をチェック。** プラグインはセットアップから君の州を知っているが、ローカル裁判所の癖は変わる — 現行ローカルルールに照らして再確認。
- **不確かなときは、そう言う。** 出力に `[UNCERTAIN: ...]` フラグがあれば、それはリサーチするか教授に尋ねるプロンプトであって、フラグを消して進むためではない。
- **[CLAUDE.md スタイル下の監督リマインダー]** — 何が外に出る前にレビューされるか、どう。

### クロージング

> That's it. インテイクを実行し、文書をドラフトし、リサーチロードマップを構築した。最初の実際の案件は同じように感じるだろう、ただしクライアントは本物で、教授が君の作業を読んでいる。
>
> 1 ページのリファレンスカード: `/ramp --card`

## `/ramp --card`

1 ページ student reference card を 1 ページ card spec に従って生成。内容:

- コマンド(Part 2 のテーブルを圧縮)
- Claude が支援できること / できないこと(出発点は yes、完成成果物は no、典拠引用は no)
- 検証習慣(Part 4 の bullets)
- 困ったときに尋ねる人(CLAUDE.md の教授名)

印刷可能。1 ページ。初日に配布。

## このスキルが行わないこと

- 教授のオリエンテーションを置き換える。手順とツールをカバーする; 教授は判断、戦略、良い人がやるのを見て初めて学ぶことをカバーする。
- 実体法を教える。プラクティスエリアの *オリエンテーション* であって、ドクトリンコースではない。
- 学生を ready と認定する。教授が学生が実際の案件を取る時を決める。
