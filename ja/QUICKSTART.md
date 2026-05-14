# クイックスタート

**60 秒。** ここまでで、プラグインが使えるようになります。

## Claude Cowork でインストール

1. [Claude Desktop をインストール](https://claude.com/download)
2. Claude Cowork へのアクセスを取得
3. 下記の動画の手順に従ってください:

https://github.com/user-attachments/assets/51394f0a-5277-4fe2-b81c-5c5e9ac876b5

## Claude Code でインストール

1. **Claude Code を開く**(ターミナルで)または **Claude Cowork**(デスクトップアプリ)を開く。どちらか分からない? ターミナルウィンドウで Claude が動いているなら、それは Claude Code です。

2. **マーケットプレイスを追加。** Claude Code で `/plugin marketplace add ` と入力(末尾に半角スペース)し、**展開した `claude-for-legal` フォルダをターミナルウィンドウにドラッグ** — パスが自動入力されます。Enter キーを押します。

   日本語版を使いたい場合は、`claude-for-legal/ja` フォルダ(`ja` サブディレクトリ)をドラッグしてください。

   (または完全パスを入力: `/plugin marketplace add /Users/you/Desktop/claude-for-legal/ja`)

3. **プラグインをインストール。** 下表から自分の業務に合うものを選び、次のように実行:
   ```
   /plugin install privacy-legal-ja@claude-for-legal-ja
   ```

4. **⚠️ Claude Code を再起動。** 閉じて再度開いてください。このステップは省略不可 — 再起動するまでプラグインは有効になりません。

5. **セットアップを実行。** 所要時間 2 分(クイックスタート)または 10〜15 分(フル)。
   ```
   /privacy-legal-ja:cold-start-interview
   ```

6. **リサーチツールを接続。** リサーチツールを接続しないと引用が未検証フラグ付きになります。Cowork: 設定 → コネクター → CourtListener を追加。Claude Code: プラグインの設定ですでにリサーチ MCP が登録されています。スキル初回利用時に認可が求められます。

## ユーザースコープでインストール — プロジェクトスコープではなく

`/plugin install` を実行すると、このプロジェクトのみか全プロジェクト(ユーザースコープ)のどちらにインストールするか聞かれる場合があります。**ユーザースコープを選んでください。**

直感に反します: プロジェクトスコープの方が安全に感じます。しかしプロジェクトスコープでは、プラグインがプロジェクトフォルダ外のファイルを読めなくなります — Downloads にあるアウトライン、Documents にある契約書、Dropbox にあるクライアントファイル等。ほとんどのスキルはあなたのファイルを読む必要があります。ユーザースコープでも、プラグインに追加の権限が与えられるわけではありません — プラグインは、あなたが明示的に指し示したファイルまたは現在のディレクトリ内のファイルしか読めません。ただ、フォルダを問わず動くようになるだけです。

すでにプロジェクトスコープでインストール済みで切り替えたい場合: `/plugin uninstall <plugin>` のあと、ホームディレクトリで `/plugin install <plugin>@claude-for-legal-ja`。

## どのプラグインを選べばいい?

| あなたは… | インストールするのは… | 最初のコマンド |
|---|---|---|
| プライバシー法務 / DPO | `privacy-legal-ja` | `/privacy-legal-ja:use-case-triage` |
| 商事 / 契約法務 | `commercial-legal-ja` | `/commercial-legal-ja:review` |
| コーポレート / M&A 法務 | `corporate-legal-ja` | `/corporate-legal-ja:diligence-issue-extraction` |
| 雇用法務 / HR カウンセル | `employment-legal-ja` | `/employment-legal-ja:wage-hour-qa` |
| プロダクト カウンセル | `product-legal-ja` | `/product-legal-ja:is-this-a-problem` |
| IP 法務 / 弁理士 | `ip-legal-ja` | `/ip-legal-ja:clearance` |
| 訴訟担当(社内・事務所いずれも) | `litigation-legal-ja` | `/litigation-legal-ja:matter-intake` |
| 規制 / コンプライアンス カウンセル | `regulatory-legal-ja` | `/regulatory-legal-ja:reg-feed-watcher` |
| AI ガバナンス責任者 | `ai-governance-legal-ja` | `/ai-governance-legal-ja:use-case-triage` |
| クリニック スーパーバイザー(ロースクール) | `legal-clinic-ja` | `/legal-clinic-ja:cold-start-interview` |
| ロースクール生 | `law-student-ja` | `/law-student-ja:cold-start-interview` |
| Legal Ops / スキルを探している方 | `legal-builder-hub-ja` | `/legal-builder-hub-ja:registry-browser` |

## インストールされる内容

各プラグインは、セットアップ・インタビューを通じてあなたのプレイブックを学習し、プラクティスプロファイルファイル(`~/.claude/plugins/config/claude-for-legal-ja/<plugin>-ja/CLAUDE.md`)に書き込み、すべてのスキルがそれを読み込みます。プロファイルはあなたのものです — 編集する、セットアップを再実行する、またはスキルに更新するよう指示することもできます。

**すべての出力は弁護士レビューを前提とする下書きです。** プラグインは不確実な部分にフラグを立て、引用をソース別に印付けし、不可逆的なものはゲート前で止めます。弁護士がレビューし、検証し、責任を引き受けます。プラグインはそのレビューを高速化しますが、置き換えません。

## 同梱されるもの

12 のプラクティスエリア・プラグイン、5 つの Managed Agent クックブック、16+ のコネクター。フルリファレンスは [README.md](README.md) を参照してください。

## 困ったときは

- **インストール後に「コマンドが見つかりません」** → ステップ 4 を忘れています。Claude Code を再起動してください。
- **「セットアップを最初に実行」** → 他のコマンドの前に `/<plugin>:cold-start-interview` を実行してください。
- **引用が `[verify]` フラグ付き** → リサーチツールを接続(ステップ 6)。接続しないと、すべての引用は現行データベースではなくトレーニングデータからのものになります。
- **「ファイル [X] を読めません」** → 最も多い原因は、プラグインがプロジェクトスコープでインストールされていて、ファイルがプロジェクトフォルダ外にあること。上記の「ユーザースコープでインストール」を参照し、ユーザースコープで再インストールするか、ファイルをプロジェクトフォルダに移動してください。
- **プラグインが X を実行できない** → `/legal-builder-hub-ja:related-skills-surfacer` を実行してより良いマッチを探すか、プラグインの README の「このプラグインがしないこと」を確認してください。
