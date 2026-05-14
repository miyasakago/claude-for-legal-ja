---
name: termination-review
description: >
  解雇レビュー — 高リスクフラグ検知、退職金 + 権利放棄合意、法域別最終給与
  タイミング。法域固有のルールと権利放棄検討期間は保存せず、レビューごとに
  リサーチします。ユーザーが「解雇をレビュー」「この人を解雇できるか」
  「解雇レビュー」と言うとき、または解雇シナリオを説明するときに使用。
  (Termination review — high-risk flag detection, severance + release, and
  final pay timing by jurisdiction. Use when the user says "reviewing a
  termination", "can we fire this person", "term review", or describes a
  termination scenario. 日本語トリガー: 「解雇をレビュー」「解雇できるか」
  「解雇レビュー」「termination review」「クビにできるか」)
argument-hint: "[describe the termination, or attach documentation]"
---

# /termination-review

1. `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` をロード → 解雇レビュートリガー、高リスクフラグ、退職金実務、法域ルール。
2. 下記のワークフローを使用。
3. チェックリストを通す。すべての高リスクフラグをチェック。
4. 従業員の法域別最終給与タイミング。該当する場合は退職金 + 権利放棄合意。
5. 高リスクフラグが点灯した場合: 表に従ってエスカレーション、サインオフなしに進まない。

---

## 案件コンテキスト

**案件コンテキスト。** プラクティスレベル CLAUDE.md の `## 案件ワークスペース` を確認。`Enabled` が `✗` の場合(インハウスユーザーのデフォルト)、このパラグラフの残りはスキップ — スキルはプラクティスレベルのコンテキストを使用し、案件機構は見えません。有効でアクティブな案件がない場合、尋ねる: 「これはどの案件のものですか? `/employment-legal-ja:matter-workspace switch <slug>` を実行、または `practice-level` と言ってください。」案件固有のコンテキストと上書きについてアクティブ案件の `matter.md` をロード。出力は案件フォルダ `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/matters/<matter-slug>/` に書き込み。`Cross-matter context` が `on` でない限り、他の案件のファイルを決して読まない。

---

## 目的

ほとんどの解雇は問題なし。少数は訴訟が待っている。このスキルは判断が最終決定される前に 2 種類目を捕まえるチェックリストを実行。スキルは法律を述べない — すべての法域固有ルールと権利放棄期間要件はレビュー時にリサーチ・引用されます。

## コンテキストをロード

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → 解雇レビュートリガー、高リスクフラグ、標準退職金、法域表。

## 出力ヘッダー

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → `## 出力` の work-product ヘッダーを前置(ユーザー役割により異なる — `## 利用者` 参照)。その設定で参照されたシード解雇メモからメモフォーマットを合わせる(あれば)。work-product ヘッダーは常に最初。

## ワークフロー

### Step 1: 基本事実

- 従業員名(または抽象的に留める場合は役割)
- 法域(働く場所)
- 解雇理由(能力不足、職務上不正、RIF、ポジション廃止)
- 勤続期間
- 年齢(高齢労働者保護に対する権利放棄要件に関連)
- 同じ意思決定単位またはプログラムの一部として他の従業員が解雇されているか(集団解雇の権利放棄ルールに関連)
- 計画された解雇日

### Step 2: 高リスクフラグ・スキャン

これが最も重要なステップ。`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` の各フラグをチェック。デフォルトセット:

| フラグ | なぜ高リスクか | チェック |
|---|---|---|
| **最近の苦情** | 報復クレーム | この従業員は最近苦情(HR、倫理ホットライン、規制機関)を提出したか? |
| **保護対象休職** | 休職法干渉/報復 | 保護対象休職(FMLA(米国家族・医療休暇法)/州レベル相当法、障害、育児、軍人)中か最近復帰したか? |
| **保護階層 + タイミング** | 差別クレーム | 保護階層 AND 最近開示/可視(妊娠発表、宗教的配慮要請、障害開示)か? |
| **内部告発者** | 連邦・州の内部告発者制定法 | この従業員は違法性、安全、不正に関する懸念を提起したか? |
| **薄い文書** | 「なぜ今」問題 | 能力不足解雇: PIP、書面警告、文書化されたフィードバックはあるか? それともこれは突然か? |
| **比較対象問題** | 不均等な扱い | 他の誰かが同じことをしていて解雇されていないか? |
| **契約/ハンドブックの約束** | 違反 | オファーレター、ハンドブック、書面が、従われていないプロセスを約束しているか? |
| **除外対象誤分類** | 清算損害金付き FLSA(米国公正労働基準法) + 州賃金クレーム | 下記の分類チェック参照。州 + 区分 + 肩書で発火。 |

**除外/非除外区分フラグ。** 以下のすべてが真のとき、このフラグを発火:

1. 従業員が高い除外給与閾値のある州 — **CA、NY、WA、CO、AK**(および `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → `## 賃金・労働時間` → 既知の分類リスク領域に高閾値州としてリストされた他の州) — で働く **AND**
2. 従業員が **除外対象**(給与制、残業なし)に分類されている **AND**
3. 従業員の肩書に **「supervisor」「lead」「coordinator」「analyst」「administrator」「specialist」**(大文字小文字区別せず、プラクティスプロファイルがリスクとしてフラグする同等スコープの肩書)が含まれる。

3 つすべて発火したとき、発出:

> 🔴 **潜在的な除外対象誤分類** — [state] で $[X] を稼ぐ [title]。[state] の除外給与閾値は約 $[Y] `[model knowledge — verify]`。解雇前に分類チェック用に `/employment-legal-ja:wage-hour-qa` にルーティング — 誤分類されて解雇された従業員は、清算損害金、弁護士費用、(CA では)PAGA 露出付きで、FLSA と州賃金クレームの即席材料を持つ。退職合意書はそれをクリーンに放棄できないかもしれない。未払残業露出のある解雇された原告は、これらの州で最も訴訟されている賃金・労働時間ファクトパターン。

肩書が「マネージャーに見える」からといってこのフラグを抑制しない — 誤分類クレームの全前提は肩書が嘘をつくこと。実際の職務と給与テストのために `/employment-legal-ja:wage-hour-qa` にルーティング。

**このレビューの一部としてバックペイ数(退職金モデリング、和解スタンス、露出見積もり)が計算されているなら、このスキルでは計算しない。** `wage-hour-qa` → Step 2a にルーティングし、その通常レート足場を使用: 通常レート中の §207(e) 包含(裁量なしのボーナス、コミッション、シフト差)、OT 時間にストレートタイムがすでに支払われた場合 0.5× 割増(そうでなければ 1.5×)、§216(b) 下の清算損害金、§255(a) 下の 2 年 / 3 年故意 SOL。すべてのバックペイ数は `[verify — 主張または支払い前に賃金・労働時間カウンセルに相談]` を運ぶ。きれいに見える誤った数こそ、この足場が防ぐ特定の失敗モード。

**いずれかのフラグが発火 → 解雇が進む前に `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` に従ってエスカレーション。** 後ではなく。前に。

### Step 3: 法域固有の要件

> **計画を確定する前に従業員の法域の適用ルールをリサーチ。** 具体的に:
>
> - 最終給与タイミング — これは州により広く異なり、しばしば従業員が解雇されたか辞職したかに依存。現在運用中のルールを、待機時間または遅延ペイのペナルティを含めてリサーチ。
> - 累積 PTO の支払 — 法域が支払を要求するか、および累積上限または use-it-or-lose-it ポリシーとの相互作用をリサーチ。
> - 必要通知 — 解雇時に必要な法域固有の通知(例: 州失業、連邦 COBRA 以外の継続カバレッジ通知、給付継続)をリサーチ。
> - 大量レイオフ / 工場閉鎖通知 — 連邦 WARN Act と、より大きな削減の一部の場合に適用される可能性のある州「mini-WARN」または地方条例をリサーチ。カバレッジ閾値と通知期間は異なる。
>
> 原典を引用。最新性を検証。
>
> **サイレント補完なし。** 設定済み法務リサーチツールへの研究クエリが、法域の最終給与、PTO、通知、または WARN ルールについて結果をほとんどまたはまったく返さない場合、見つかったものを報告し停止。**ウェブ検索やモデル知識でギャップを埋めないで尋ねる。** 言う: 「検索は [tool] から [N] 件の結果を返しました。[jurisdiction / rule] のカバレッジは薄いようです。オプション: (1) 検索クエリを広げる、(2) 別のリサーチツールを試す、(3) ウェブを検索 — 結果は `[web search — verify]` でタグ付けされ、依拠前に原典に対してチェックすべき、(4) ここで停止して弁護士検証用にフラグ。どれにしますか?」弁護士が低信頼度ソースを受け入れるかどうかを決定。
>
> **出典帰属。** 計画の各引用 — 最終給与ルール、PTO ルール、通知、WARN / mini-WARN、OWBPA(米国高齢労働者給付保護法)検討期間、州権利放棄制限 — に出処をタグ: 法務リサーチコネクターから取得した引用は `[Westlaw]`、`[CourtListener]`、または MCP ツール名; ウェブ検索引用は `[web search — verify]`; 訓練データから想起した引用は `[model knowledge — verify]`; ユーザーが提供した引用は `[user provided]`。`verify` タグ付き引用は捏造リスクが高く、最初にチェックされるべき。タグを剥がしたり集約したりしない。

### Step 4: 退職金と権利放棄合意

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → 標準退職金に従う:

- 退職金が提供されているか? 算定式または裁量?
- 権利放棄合意が必要? (通常、退職金を支払うならイエス — それが対価。)

> **適用される権利放棄検討ルールをリサーチ。** 従業員が 40 歳以上の場合、連邦法(OWBPA(米国高齢労働者給付保護法))は検討期間、撤回期間、必要助言、— 集団解雇の場合 — 必要な意思決定単位開示に影響する特定要件を課す。具体的な検討期間は個別解雇、集団 RIF、集団退職インセンティブで異なる; ルールも従業員の年齢と影響を受ける従業員数に依存。記憶から日数を述べないこと — 特定状況の現在運用中のルールをリサーチし、原典を引用。州法相当法または並行権利放棄要件もリサーチ。最新性を検証。

別途、以下が権利放棄に適用されるか検討:
- 州固有の権利放棄制限(一部の州は何を放棄できるか制限、または特定の文言を要求)。
- セクシャルハラスメント、差別、または他の保護カテゴリーに関連する非開示または非中傷条項の連邦または州制限。
- NLRA 保護活動に関する分離合意ルール。

### Step 5: 文書チェック

特に能力不足解雇:

- 紙の証跡はあるか? 書面警告、PIP、フィードバック文書?
- 紙の証跡は一貫したストーリーを語るか?
- 理由と矛盾するもの(最近の肯定的レビュー、ボーナス、昇進)が書面にあるか?

「なぜ今」の質問: この人が 1 年間業績不振だったなら、何が変わったか? 答えは文書化されているべき。

## 出力

> **研究コネクター事前チェック。** メモを発出する前に、このセッションで法務リサーチコネクター — Westlaw、CourtListener、または事務所設定の研究 MCP — が到達可能かをチェック。CLAUDE.md `## 出力` に従ってこれを reviewer note に集約: Step 3 でコネクターが結果を返さない(または実行時に何も設定されていない)場合、reviewer note の **Sources:** 行に記録 — 例: `not connected — cites from training knowledge; the highest-fabrication topics in termination-law memos are final-pay timing, OWBPA group/individual distinctions, state-specific NDA / non-disparagement rules (e.g., CA SB 331), and NLRB positions (e.g., McLaren Macomb) — spot-check those first`。引用ごとの `[model knowledge — verify]` タグはインラインに残る。メモの上に独立したバナーを発出しない。

> **法域前提。** このレビューは Step 1 で記述された従業員の法域と `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` → 法域フットプリントからの任意のデフォルトを想定。雇用ルール、最終給与タイミング、権利放棄要件、通知義務は法域により実質的に異なる。従業員が異なる州・国で働く、または準拠法が争われる場合、この分析は記述通り適用されないかもしれない。

`~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` で参照されたシード解雇メモからメモフォーマットを合わせる。なければ:

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

## Termination Review: [Role/Name] — [Date]

**Jurisdiction:** [State]
**Reason:** [Performance / Misconduct / RIF / Elimination]
**Planned date:** [Date]

---

### Bottom line

[Can you proceed / Need to fix X first / Stop — one-sentence why]

---

### High-risk flags

[Every flag from Step 2. ✅ Clear or 🔴 FLAG with detail.]

**Escalation:** [None needed | Escalate to [name] before proceeding — [which flag]]

---

### Jurisdiction requirements ([State])

- Final pay: [researched rule and cite; state whether PTO is included per the
  researched rule and any team policy]
- Required notices: [list, each researched and cited]
- Mass-layoff notice (if applicable): [researched rule and cite]

---

### Severance and release

- Severance: [amount per formula / none]
- Release: [required / not — if required, research and apply the
  consideration-period, revocation-period, advisement, and (for groups)
  decisional-unit-disclosure requirements that govern this specific
  situation; cite primary sources and verify currency]
- [Any state-law release rules or non-disclosure/non-disparagement
  restrictions that apply]

---

### Documentation

[Assessment of paper trail. Gaps flagged.]

---

### Go / No-go

[Clear to proceed | Proceed with changes below | Hold — escalation pending]

### Checklist for term day

- [ ] Final paycheck ready, correct amount, delivered per researched rule
- [ ] Continuation-coverage notices (COBRA / state analogs) prepared
- [ ] [State] unemployment notice prepared
- [ ] Severance agreement (if applicable) with the consideration period
      required for this specific situation
- [ ] Return of property / access cutoff coordinated
- [ ] [etc.]
```

## 結果を伴う行動のゲート(従業員を解雇)

**「Go」推奨または準備完了とマークされた解雇日チェックリストを生成する前に:** `~/.claude/plugins/config/claude-for-legal-ja/employment-legal-ja/CLAUDE.md` の `## 利用者` を読む。役割が **Non-lawyer** の場合:

> 従業員を解雇することは法的結果を持つ — 不当解雇、差別、報復、賃金法クレームはすべて、この判断がどう構造化されたかにさかのぼる。この解雇を弁護士とレビューしましたか? はいなら進める。いいえなら、彼らに持っていくブリーフ:
>
> - 従業員、法域、理由、計画日
> - レビューが浮上させたすべての高リスクフラグ(最近の苦情、保護対象休職、保護階層 + タイミング、内部告発者、薄い文書、比較対象、契約/ハンドブックの約束) — 詳細付き
> - 法域固有の発見(最終給与、PTO、必要通知、大量レイオフルール)と、どこから引用されたか
> - 退職金/権利放棄分析、OWBPA(米国高齢労働者給付保護法)/高齢労働者保護の角度を含む
> - 未解決の質問
> - 何が間違いうるか(このファクトパターンが支持するクレーム理論)
> - 弁護士に尋ねること(これはクリーンな解雇か、まず追加の文書が必要か、権利放棄に特定の文言が必要か、意思決定単位を交互配置する必要があるか)
>
> 弁護士、ソリシター、バリスター、または他の認定法務専門家を見つける必要がある場合: 専門規制機関(米国の州弁護士会、英国 & ウェールズの SRA / Bar Standards Board、スコットランド / NI / アイルランド / カナダ / オーストラリアの Law Society、またはあなたの法域の同等機関)に連絡し、紹介サービスへ。雇用は、解雇会議前の短い相談が解雇後のクレーム防御を一貫して上回る価値を持つ実務分野の 1 つ。

このゲートを通過した「Clear to proceed」出力を明示的な yes なしに生成しない。弁護士レビュー用 DRAFT マークは OK。

---

## 次のステップ・デシジョンツリーで締めくくる

CLAUDE.md `## 出力` の次のステップ・デシジョンツリーで終わる。このスキルが生成したものにオプションを調整 — 5 つのデフォルト分岐(X をドラフト、エスカレーション、追加情報を取得、様子見、その他)は出発点で、固定ではない。ツリー**こそ**が出力; 弁護士が選ぶ。

## このスキルが行わないこと

- 解雇判断を下す。判断をチェックする。
- 会話を行う。マネージャーがそれを行う。
- 記憶から権利放棄または法域ルールを述べる — すべてのルールはレビュー時にリサーチ・引用される。
- 訴訟なしを保証。明白な問題を捕まえることでリスクを減らす。
