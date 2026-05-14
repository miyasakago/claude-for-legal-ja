---
name: client-intake
description: >
  構造化されたインテイク — プラクティスエリア別テンプレート、エリア横断の論点発見、
  利益相反フラグ、トリアージ分類。学生が分析し、教授がレビューするフォーマット済み
  案件サマリーを生成。案件受任の判断は行いません。新規クライアントの
  インテイク開始時、インテイク面談実行時、新規クライアントの状況の
  書き起こし時に使用します。
  (Use when starting a new client intake, running an intake
  interview, or writing up a new client's situation.
   日本語トリガー: クライアントインテイク、インテイク面談、新規クライアント、案件受付)
argument-hint: "[任意: プラクティスエリアのヒント]"
---

# /client-intake

1. `~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` を読み込む → プラクティスエリア、インテイクテンプレート、監督スタイル、フラグトリガー。
2. 下記のワークフローを使用。
3. プラクティスエリア別テンプレートにルーティング。全体を通じてエリア横断の論点を聞く。
4. 利益相反チェックのフラグ。トリアージ分類。
5. フォーマット済み案件サマリーを出力(AI 支援ラベル、検証プロンプト、監督ルーティング付き)。

```
/legal-clinic-ja:client-intake
```

---

# Client Intake(クライアント・インテイク)

## 目的

インテイクはクリニックで最大のボトルネックの 1 つ。学生は 45 分の面談と、もう 1 時間の書き起こし、論点発見にさらに時間を費やすかもしれない。一方で待機リストは伸びる。

このスキルは会話を構造化し、書き起こしを生成し、プラクティスエリアを横断して論点を発見し、利益相反をフラグする — そうすることで学生の時間が分析(transcription ではなく)に向かう。

**このスキルが行わないこと:** 案件を受任するかどうかの判断。それは学生の分析と教授の判断。Claude は情報収集と構造化を加速するが、ロイヤリングを加速するわけではない。

## コンテキストの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` → プラクティスエリア、インテイクテンプレート(複数ある場合はプラクティスエリア別)、監督スタイル、法域、フラグトリガー。

## 指導弁護士ガイドの読み込み

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/guides/<practice-area>.md` でプラクティスエリア別ガイドを確認。存在する場合、下記の汎用デフォルトの代わりにそのインテイク質問、レッドフラグ、good-fit 基準を使用。存在しない場合、汎用インテイクを使い、インテイクサマリーの末尾に注記: 「This was a generic intake — your supervisor can tailor the questions for your clinic type with `/legal-clinic-ja:build-guide`.(これは汎用インテイクでした — 指導弁護士は `/legal-clinic-ja:build-guide` でクリニック種別用に質問を調整できます。)」

プラクティスエリアがルーティングされる前(下記ワークフローの Step 1)にインテイクが始まる場合、ルーティング後にガイドを再確認 — ガイドのパスはインテイクがランディングしたプラクティスエリアに依存する。

## ワークフロー

### Step 1: プラクティスエリアのルーティング

このインテイクはどのプラクティスエリアから始まるか? クライアントは知らないかもしれない — 彼らは自分の問題を知っており、法的カテゴリを知っているわけではない。

> "Tell me what's going on — what brought you to the clinic today?"
> (「何が起きているか教えてください — 今日クリニックに来た理由は?」)

回答から、適切なインテイクテンプレートにルーティング。クリニックが複数エリアを扱い、問題がそれらにまたがる場合(住居クライアントが在留資格に言及、家族クライアントが DV に言及)、関連するすべてのエリアを注記 — エリア横断の論点発見は機能であってバグではない。

### Step 2: プラクティスエリア固有インテイク

各プラクティスエリアは異なる質問をする。`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` のこのエリア用テンプレートを使用。提供されない場合のデフォルト:

**Immigration(在留資格):**
- 現在のステータスと入国経緯
- 過去の申請、退去、ICE / CBP(米国移民・税関執行局/税関国境警備局)との遭遇
- 庇護(asylum)/送還停止(withholding)の主張に関係する出身国情勢
- 家族とそのステータス
- 犯罪歴(センシティブ — 尋ねる理由を説明)
- タイムラインの緊急性: 係属中の聴聞、期限、NTA(Notice to Appear / 出頭通知書)

**Housing(住居):**
- 住居種別(私的、補助、公的)
- 何が起きたか: 受領した通知、ロックアウト、状態の問題、敷金紛争
- 賃貸条件と支払い履歴
- 居住適合性問題(修理要請、家主の対応、書類化)
- タイムラインの緊急性: 通知日、ある場合は法廷日

**Family(家族):**
- 関係と争点(親権、扶養、離婚、保護)
- 関与する子ども — 年齢、現在の状況
- 安全性: 暴力、脅迫、恐怖(慎重に扱う — エリア横断フラグ参照)
- 既存の裁判所命令
- タイムラインの緊急性: 予定された聴聞

**Consumer(消費者):**
- 債務または紛争の種別
- 誰がどう連絡してきているか(FDCPA / Fair Debt Collection Practices Act / 米国公正債権回収業務法 の関連性)
- 書類: 契約、明細、回収通知
- 何か申立てが行われたか
- タイムラインの緊急性: 答弁期限、賃金差押え、判決

### Step 3: プラクティスエリア横断の論点発見

プラクティスエリアテンプレートを実行中、そのエリア外の論点を聞き取る:

| クライアントが言うこと | 同時にフラグするもの |
|---|---|
| 「在留資格が心配」 | 住居インテイクでも在留資格の論点 |
| 「パートナーが [脅迫行動]」 | 消費者インテイクでも DV / 家族法 / 保護命令 |
| 「ケガで働けない」 | 給付/障害の請求の可能性 |
| 「給料から差し引かれている」 | 賃金差押え — 消費者/雇用のオーバーラップ |
| 「家主が ICE を呼ぶと言った」 | 住居 + 在留資格 + 報復請求の可能性 |

エリア横断の論点はすべてサマリーに記録。クリニックが扱うか、紹介するか、両方か — それは教授の判断。学生はそれを見るべき。

### Step 4: 利益相反チェックのフラグ

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` が記述する利益相反チェックプロセスに従う。最低限:

- 相手方の名前 — クリニックが代理しているか、代理したことがあるか?
- 関連当事者 — 学生またはクリニックに利益相反があり得る他の誰か?
- ポジショナル利益相反 — この案件は他のクリニッククライアントを害するものを求めているか?

教授レビュー用にフラグ。利益相反を解決しない — 浮上させる。

### Step 5: トリアージ分類

案件受任の決定ではなく、トリアージ入力:

| 分類 | 意味 |
|---|---|
| **Urgent** | 数日内の期限、安全性の問題、回復不能な害が間近 |
| **Time-sensitive** | 数週内の期限、害が継続しているが直ちには回復不能ではない |
| **Standard** | 直近の期限なし、通常通り順番待ち可 |
| **May be out of scope** | クリニックのプラクティスエリア外 — 紹介評価用にフラグ |

### Step 6: 監督フラグチェック

`~/.claude/plugins/config/claude-for-legal-ja/legal-clinic-ja/CLAUDE.md` の監督スタイルとフラグトリガーに従う。正式キューまたは構成可能フラグが有効で、トリガー(期限への言及、DV 指標、在留資格が論点など)が存在する場合、フラグを注記。

### Step 7: 期限引継ぎ — 必須デリバラブル

インテイクで何らかのタイムライン期限(答弁期限、聴聞、消滅時効カットオフ、治癒期間、提出ウィンドウ、通知ウィンドウ、ICE チェックイン、退去聴聞、立退き裁判日、保護命令更新)が浮上した場合、**インテイク出力の一部として、コピー&ペースト可能な `/legal-clinic-ja:deadlines --add ...` ブロックを発出する**。これは必須デリバラブルであって提案ではない — インテイクが期限を特定し、学生がそれを期限スキルに再転記すべきではない。

各期限を、インテイクから事前入力された全フィールド付きで、学生がコピーできる fenced code block としてフォーマット:

```
/legal-clinic-ja:deadlines --add
  case=[case slug or client-last-name-keyword]
  type=[response|hearing|statute-of-limitations|discovery|cure-period|filing-window|notice|other]
  description="[期日となるものの一行説明]"
  due=[VERIFY — 学生 + 指導弁護士が起算事由から計算]
  source="[起算事由 + 法令/規則引用、例: 'UD complaint served 2026-05-04, CCP § 1167']"
  owner=[学生名]
  warnings=[14,7,3,1]
```

ルール:
- 浮上した期限ごとに 1 ブロック。結合しない。各ブロックは期限スキルの事前追加重複チェックを通過する。
- 期限が法域的(応答期限、消滅時効、特定規則下の通知ウィンドウ)である場合、`due=` の値を `[VERIFY — student + supervisor compute]` のままにする。期限スキルは計算しない; 学生 + 指導弁護士が計算してエントリを更新。
- 期限のトリガー文書に日付がある場合(召喚状の聴聞日、ICE チェックイン日、保護命令の更新期限)、その日付を `due=` に入れる。日付が計算される(起算事由から N 日後)場合は `[VERIFY]` マーカーを残す。
- インテイクで期限が浮上しなければ、このセクションを省略する — 作り出さない。

## 出力

```markdown
# Intake Summary: [Client name or ID]

---
[AI-ASSISTED DRAFT — requires student analysis and attorney review]

**Privilege and confidentiality.** This summary is derived from client communications that may be privileged, confidential, or both. It inherits the source's privilege status. Distributing it beyond the privilege circle (including outside the clinic) can waive privilege. Keep it in the clinic's privileged file store, mark it appropriately, and make distribution decisions with your supervisor.
(本サマリーは、秘匿特権・秘密のいずれか、または双方の対象であり得るクライアント・コミュニケーションから得られたものです。ソースの秘匿特権ステータスを引き継ぎます。秘匿特権の輪を越えて(クリニック外を含む)配布すると、秘匿特権を放棄する可能性があります。クリニックの特権ファイル保管庫に保管し、適切にマーキングし、配布判断は指導弁護士と行ってください。)
---

**Date:** [date] | **Intake by:** [student] | **Practice area:** [primary + any cross-area]

## Bottom line

[Take the case / Decline because X / Need more info on Y — next step is Z]

## Client's situation (in their words)

[クライアントが法的カテゴリ化前に語った叙述。これは人間の物語。]

## Legal issues identified

*このセクションの法令、条例、規則、または判例引用にはすべて出所タグが付きます(タグ語彙はプラグイン CLAUDE.md `## Shared guardrails` を参照)。指導弁護士が本文をアップロードした場合は `[user provided]`、本セッションで公式ソースから取得した場合は `[statute / regulator site]`、本会話でのツール結果から来た場合はリサーチコネクタータグ(`[CourtListener]` 等)、それ以外は `[model knowledge — verify]`。デフォルトは `[model knowledge — verify]`。コネクターに対して引用を検証できない指導弁護士は、何を最初にチェックすべきか知るためにタグを見る必要があります。*

### Primary ([practice area])
- [Issue 1]: [タグ付きで引用を含む 1 行、例: 「RLTO §5-12-080 `[model knowledge — verify]`」]
- [Issue 2]: [1 行]

### Cross-practice-area flags
- [Other area]: [これを発生させたクライアントの発言]
  [UNCERTAIN: クリニックが扱うか紹介するか — 教授判断]

## Key facts

| Fact | Source | Documentation |
|---|---|---|
| [fact] | [client statement / document provided] | [have it / need it] |

## Conflict check

**Opposing party:** [name(s)]
**Related parties:** [any]
**Flag:** [clear / needs conflict check against clinic database]

## Triage

**Classification:** [Urgent / Time-sensitive / Standard / May be out of scope]
**Driving deadline:** [if any — date and what it is]

## Deadlines to log

[Step 7 に従い、浮上した期限ごとに 1 つの `/legal-clinic-ja:deadlines --add ...` ブロック。
ない場合はこのセクションを省略。]

## Jurisdictional notes

*このセクションの法令、条例、規則、または判例引用にはすべて出所タグが付きます — `## Legal issues identified` と同じ語彙。デフォルトは `[model knowledge — verify]`。本セッションでリサーチコネクターに到達できない場合は reviewer note の **Sources:** 行に記録(プラグイン CLAUDE.md `## Outputs` 参照) — 独立したバナーは出さない。*

[CLAUDE.md の法域に従う、本案件種別に関連する州固有またはローカルルール固有の問題、引用ごとにタグ付き]

## Supervision flags

[監督スタイルがフラグを含む場合: どれが発動したか、なぜ。正式キューの場合:
「QUEUED for [professor]」。]

---

## Verification prompts for the student

分析の前に、検証:
- [ ] [インテイクが依拠する具体的事実 — クライアントまたは書類で確認]
- [ ] [期限日 — クライアントの記憶ではなく実際の通知/裁判文書から確認]
- [ ] [上記の法的結論はすべて出発仮説 — 依拠する前にリサーチ]

## What this summary does NOT do

このサマリーは、クリニックがこの案件を受任するかどうかの判断はしません。それはあなたの分析と [Professor] の判断です。クライアントが伝えたことを構造化し、分析に時間を使えるようにします。
```

## プラクティスエリア別インテイクテンプレート参照

プラクティスエリア固有の質問セットを `references/intake-templates/[area].md` に保管。コールドスタートが教授のインテイクフォームから埋める; 提供されない場合は上記デフォルトを使用。

## このスキルが行わないこと

- **案件受任の判断。** 学生が分析、教授が決定。
- **利益相反の解決。** 教授用にフラグする。
- **インテイク中の助言。** インテイクは収集; 助言は分析と教授レビューの後。
- **完成文書の生成。** サマリーは出発点 — 学生が読み、誤って性格付けされた箇所を修正し、そこから分析を構築する。

## 次のステップのデシジョンツリーで締めくくる

CLAUDE.md `## Outputs` に従って次のステップのデシジョンツリーで終わる。このスキルが今生成したものに合わせて選択肢をカスタマイズ — 5 つのデフォルトブランチ(draft the X、escalate、get more facts、watch and wait、something else)は出発点であって固定ではない。ツリーが出力であり、弁護士が選ぶ。
