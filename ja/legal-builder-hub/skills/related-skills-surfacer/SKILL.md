---
name: related-skills-surfacer
description: >
  他プラグインでの最近の活動に基づきコミュニティスキルを提案します。
  コミュニティがタスクに関連するものを作っているかをチェックし、一度だけ、
  邪魔にならない形で言及します。ユーザーが「これのコミュニティスキルある?」
  「他に何がある?」と言ったり、スキル推奨を求めたときに使用; 他プラグインの
  ワークフローの一部として受動的にも実行されます。
  (Use when the user says "is there a community skill for this", "what else is
   out there", or asks for skill recommendations; also runs passively as part
   of other plugins' workflows.
   日本語トリガー: 関連スキル、コミュニティスキル提案、他のスキル、スキル推奨)
---

# /related-skills-surfacer

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` をロード → プラクティスプロファイル。
2. 下記のワークフローを使用。
3. 他プラグインが何をしてきたかをチェック。レジストリと照合。
4. 提案:「X をしてきましたね — コミュニティに関連する Y スキルがあります。」

---

## 目的

コミュニティがあなたがこれから作ろうとしているものを既に作っているかもしれません。このスキルはそれに気づき、一度だけ、簡潔に、邪魔にならない形で言及します。

## どのように動くか

このスキルはタスク後に関連するコミュニティスキルを表面化します。ユーザーが直接起動(「X のために他に何がある?」)するか、Stop hook 経由で他のプラグインに接続できます — hook ベースのパターンは各兄弟プラグインがこのスキルを呼ぶ Stop hook を宣言する必要があり、デフォルトでは接続されていません。hook 接続がない場合は、直接起動してください。

他のプラグインはタスクの最後に軽いチェックを含められます:
> 「legal-builder-hub-ja がこの種類の助けになるコミュニティスキルを見つけました: [name] — [one-line]。見てみますか?」

## コンテキストのロード

`~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` → プラクティスプロファイル、インストール済みスキル(既にインストールされているものは提案しない)。
registry-browser のレジストリキャッシュ。

## マッチ

タスク記述(ユーザーが直前に行っていたこと)が与えられたら、マッチするレジストリスキルを見つける:

- タスクとスキル説明のキーワード重複
- プラクティスプロファイル適合(取引弁護士に訴訟スキルを提案しない)
- まだインストールされていない

**閾値:** マッチが強い場合のみ表面化。弱いマッチはノイズ。煩わせるより何も表面化しない方がよい。

## 出力

強いマッチの場合:
> 💡 コミュニティにこれのスキルがあります: **[name]**(from [registry])— 「[description]」。試すには `/legal-builder-hub-ja:skill-installer [name]`。

強いマッチがない: サイレント。出力なし。「何も見つかりませんでした」と言わない。

## 頻度制限

同じスキルを 2 回表面化しない。最初にインストールしなかったなら、見て「いいえ」と決めたのです。dismissals を `references/surfaced.json` で追跡。

## ユーザー制御

`~/.claude/plugins/config/claude-for-legal-ja/legal-builder-hub-ja/CLAUDE.md` → 新規スキル通知:
- **All:** すべてのマッチを表面化
- **Matching practice profile:** プロファイルでフィルタ(デフォルト)
- **None:** このスキルはオフ

## Next-steps デシジョンツリーで締めくくる

CLAUDE.md の `## Outputs` に従って next-steps デシジョンツリーで終わる。このスキルが直前に生成したものに選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X、escalate、get more facts、watch and wait、something else)は出発点であってロックインではない。ツリー IS the output、弁護士が選ぶ。

## このスキルが NOT 行うこと

- 何かをインストール。
- 進行中のタスクを中断。表面化はタスクの *終わり* に、途中ではない。
- 何度も促す。スキルにつき 1 回のみ、ずっと。
