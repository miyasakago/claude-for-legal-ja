---
name: log-leave
description: >
  期日追跡を開始するために必要な最小情報で、新規休職を休職台帳に追加します。
  従業員が休職に入り、初日から指定、証明、枯渇のクロックをトラッカーに監視
  させたいときに使用。
  (Add a new leave to the leave register with the minimum information needed
  to start tracking deadlines. Use when an employee goes on leave and you
  want the tracker to watch designation, certification, and exhaustion clocks
  from day one. 日本語トリガー: 「休職を記録」「休職を追加」「FMLA を開始」
  「休職台帳に追加」)
argument-hint: "[describe the leave — employee/role, type, jurisdiction, start date]"
---

# /log-leave

期日追跡を開始するために必要な最小情報で、`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/leave-register.yaml` に新規休職エントリーを追加。従業員が休職に入り、初日からクロックを監視させたいときに使用。

## 指示

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` を読む → 法域表とシステムセクション。

2. 以下をすべて単一のプロンプトで尋ねる — 1 つずつ滴下しない:

   > 休職追跡をセットアップするための簡単な質問:
   >
   > - 従業員名または役割(匿名化で OK)
   > - どこで働くか? (州 — どのルールが適用されるかを決定)
   > - 休職タイプ: FMLA / 州休職(どの州) / USERRA / ADA 配慮
   > - 休職開始日
   > - 断続的休職か?
   > - 復帰予定日(分かっている場合 — 不明なら空白)
   > - 指定通知は送付済みか? 送付済みならいつ?
   > - 医療証明は要請済みか? 要請済みならいつ?

3. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` の法域表を使用し、この法域でこの休職タイプに適用される休職権利(時間/週)を調べる。

4. 提供された情報に基づいて最初の今後の期日を計算:
   - 指定未送付 → 期日は休職開始から営業日 5 日
   - 医療証明要請済み未受領 → 期日は要請日から 15 日
   - 両方送付・受領済み → 次の期日は 75% 枯渇時

5. leave-tracker エージェントの休職台帳フォーマットを使い、新規エントリーを `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/leave-register.yaml` に書き込み。ファイルが存在しなければ作成。

6. 1 行で確認:
   > 「ログ済み。[Employee/Role] — [Leave type] — [Jurisdiction] — 開始 [date]。最初の期日: [何でいつ]。休職トラッカーが自動でアラート。」

## 例

```
/employment-legal-ja:log-leave
```

```
/employment-legal-ja:log-leave
Sarah(Sr. Engineer、カリフォルニア勤務)が今日重大な健康状態のため FMLA を
開始しました。断続的。指定はまだ未送付。
```
