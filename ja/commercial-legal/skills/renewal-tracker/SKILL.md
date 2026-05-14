---
name: renewal-tracker
description: >
  維持されている更新台帳をもとに、解約期限が近づいている契約を表示し、通知ウィンドウが
  閉じる前に警告します。ユーザーが "what's renewing soon"、"what renewals are due"、
  "did we miss a cancellation window"、"add this to the renewal tracker" と尋ねたとき、
  または定期的な実行時に使用。saas-msa-review からハンドオフを受け取ります。
  日本語トリガー例: 何が更新される、近々の更新、解約期限を逃したか、更新トラッカーに追加。
argument-hint: "[--days N でウィンドウ変更 | --missed で期限超過の確認]"
---

# /renewal-tracker

何が更新されるか、いつまでに解約しなければならないかを浮かび上がらせます。

## 指示

1. **`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/renewal-register.yaml` を読む**(config ディレクトリ — プラグイン更新後も残る)。

2. **デフォルトモード:**モード 2 — 次の 90 日に控えているものを、半開区間で緊急度別にグループ化:🔴 0〜13 日、🟠 14〜44 日、🟡 45〜89 日。14、45、90 日は境界 — 各日はちょうど 1 つのバンドに属し、2 つには属さない。

3. **`--days N`:** ウィンドウを変更。

4. **`--missed`:** モード 4 — キャンセル記録なしに経過した解約期限。

5. **台帳が空で [CLM] が接続されていれば:** モード 3 を提案 — [CLM] をスキャンして更新日のあるアクティブな契約を一括ロード。

6. **出力には推奨アクションを含める:** 誰に連絡するか(各台帳エントリのビジネスオーナー)、どれが価格無上限か(ウィンドウが閉じる前にレバレッジを得る)。

## 例

```
/commercial-legal-ja:renewal-tracker
```

```
/commercial-legal-ja:renewal-tracker --days 180
```

```
/commercial-legal-ja:renewal-tracker --missed
```

---

## 目的

誰も契約を 2 度読みません。更新日はレビュー時に一度抽出され、その後どこかに住みます — 理想的には、解約期限の 45 日後ではなく 45 日前に大声で知らせてくれる場所に。

このスキルは更新台帳を保守し、次に控えているものを浮かび上がらせます。

## 台帳

`~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/renewal-register.yaml`(config ディレクトリ — プラグイン更新後も残る)に存在します。各エントリ:

```yaml
- counterparty: "Acme SaaS Inc."
  agreement: "Acme Platform Subscription Agreement"
  signed_date: 2025-06-15
  initial_term_end: 2026-06-15
  current_term_end: 2026-06-15     # 各自動更新後にロールフォワード。cancel_by_* はこれから計算
  renewal_mechanism: "auto-renew annual"
  notice_period_days: 60
  notice_method: "email"           # email / portal / certified mail / registered post / courier / per contract §X
  transit_buffer_days: 0           # 電子なら 0、国内 certified mail なら 5、国際 registered post なら 10 — または契約に指定があればそれに従う
  cancel_by_calendar: 2026-04-16    # current_term_end から notice_period_days を引いた値
  cancel_by_effective: 2026-04-16   # 必要なら最後の営業日にロールバック
  send_by_effective: 2026-04-16    # cancel_by_effective から transit_buffer_days を引いた値 — 通知を SEND しなければならない日付
  cancel_by_roll_note: ""           # 例:"rolled back from Sunday 2026-11-01; verify against contract's business-day definition"
  cancel_by_provenance: "[model calculation — verify against the notice clause]"
  price_on_renewal: "then-current list (uncapped)"
  annual_value: 48000
  business_owner: "jane@company.com"
  clm_id:        "IC-12345"        # 接続されていれば
  docusign_envelope: "abc-123"   # 接続されていれば
  status: "active"               # active | cancelled | renewed | lapsed
  notes: "価格無上限 — 更新前に再検討。代替ベンダー: X, Y。"
```

**通知の輸送時間 — `cancel_by_effective` ではなく `send_by_effective` でアラート発火。** certified mail 要件付き 60 日ウィンドウは実質 ~55 日。受領日でアラートするトラッカーは期限を逃すトラッカー。`send_by_effective = cancel_by_effective - transit_buffer_days` を計算し、アラート(モード 2 の 🔴 / 🟠 / 🟡 緊急バンド)を `send_by_effective` で発火させる。モード 2 の緊急度カラムは `send_by_effective` を表示し、詳細カラムは `cancel_by_effective`、`notice_method`、`transit_buffer_days` を出し、読み手が差分を見てバッファを問えるようにする。

**ローリング更新 — ロールフォワードしない台帳は一度だけ正しい台帳。** 記録のために `initial_term_end` を保存するが、`cancel_by_*` は `current_term_end` から計算する。更新が発火した(解約ウィンドウが過ぎて通知が出されなかった)とき、プロンプト:

> この契約は [日付] に自動更新されました。台帳を更新:新しい `current_term_end` は [日付 + 更新期間]、新しい `cancel_by_effective` は [計算値]、新しい `send_by_effective` は [計算値]。確認しますか?

1 年目の後、`initial_term_end` は不正確で、`current_term_end` のみが正しい cancel-by 日付を生む。

## すべての cancel-by 日付で営業日チェック

**台帳の cancel-by 日付は通知が有効な最後の営業日でなければならず、暦日ではない。** 暦日が週末に当たることは、更新期限を逃す最も一般的な方法。台帳がそれを捕捉する。

cancel-by 日付を計算(または取り込み)するとき:

1. **暦日を計算。** `cancel_by_calendar = initial_term_end − notice_period_days`(または条項が指定する内容)。これが生の算術。
2. **準拠法に応じた営業日のロールバック。** 契約の準拠法がどの祝日をカウントするかを決める。米国:連邦祝日 + 準拠法が州なら州の祝日。England & Wales:bank holidays。ドイツ:Feiertage(Bundesland により異なる — 尋ねる)。カナダ:連邦 + 州。シンガポール:public holidays。土曜なら金曜にロールバック。日曜なら金曜にロールバック。準拠法管轄の祝日なら前営業日にロールバック。前にロールバックし、決して後ろに進めない — 後ろに進めると通知はウィンドウが閉じた後に到着する。準拠法が米国外で祝日カレンダーを判定できない場合、フラグ:「準拠法は [X] — 営業日ロールバックは米国連邦祝日をプレースホルダーとして使用。[管轄] 祝日カレンダーに照らして有効日を確認してから依拠してください。」
3. **契約自身の日数カウントルールをチェック。** 「business day」「received by」「deemed received」「5:00 p.m. [現地時間]」または通知方法条項を探す。契約が「business day」を定義し、または受領メカニクス(certified mail、read receipt 付きメール)を指定していれば、その定義が支配。デフォルトのロールバックと契約自身のルールに不一致があればフラグ。
4. **両方の日付を台帳に記録。** `cancel_by_calendar` は生の算術、`cancel_by_effective` は通知が有効な最後の営業日、`cancel_by_roll_note` はそれらが異なる理由を記録(例:「rolled back from Sunday 2026-11-01; verify against contract's business-day definition」)。計算された `cancel_by_effective` には `cancel_by_provenance` タグ `[model calculation — verify against the notice clause]` を付け、verify フラグが周辺の散文ではなく日付と共に移動するようにする。
5. **暦日ではなく EFFECTIVE 日付でアラート発火。** モード 2 の緊急バンド(🔴 / 🟠 / 🟡)は `cancel_by_effective` を使用。モード 2 出力は緊急度カラムで `cancel_by_effective` を表示し、ロールバックが起きた詳細カラムで `cancel_by_calendar` と `cancel_by_roll_note` を出し、読み手が見て問えるようにする。

`cancel_by: 2026-11-01`(日曜)を曜日も警告もなく出力するモード 2 レポートは、静かに間違った有効期限。台帳はそれを捕捉する場所 — 後でウィンドウが既に動いた後ではなく、取り込み時に一度。

## モード

### モード 1: 更新を取り込む(レビューからのハンドオフ)

saas-msa-review または vendor-agreement-review が更新条項を見つけたとき、レコードを引き渡します。台帳に追記。相手方に既存エントリがあれば、これが置換(更新された契約)か追加契約かを尋ねる。

### モード 2: 何が控えているか

**デフォルトのルックバックウィンドウ:**次の 90 日。

**緊急バンドは半開区間 — 期限はちょうど 1 つのバンドに属す。** 「cancel-by までの日数」(`cancel_by_effective - today`)を使用。14、45、90 日はちょうど 1 つのバンドに属し、2 つには属さない。ここで off-by-one すると、最も緊急なアイテムを緊急度の低いバケットに入れてしまう。

- 🔴 **0〜13 日**(cancel-by が 14 日未満 — 今日を含む)
- 🟠 **14〜44 日**
- 🟡 **45〜89 日**
- (90 日以上はデフォルトのルックバックウィンドウ外 — ユーザーが `--horizon` で 90 を超えたときのみ含める)

```markdown
## 更新案件 — 次の 90 日

### 🔴 解約期限 0〜13 日

| Counterparty | Cancel by | Renewal date | Annual $ | Owner | Notes |
|---|---|---|---|---|---|
| [名称] | **[日付]** | [日付] | $[n] | [email] | [注記] |

### 🟠 解約期限 14〜44 日

[同じ表]

### 🟡 解約期限 45〜89 日

[同じ表]

---

**推奨アクション:**
- [ ] [相手方] — [ビジネスオーナー] に確認:継続したいか?
- [ ] [相手方] — 価格無上限。レバレッジを失う前に代替の見積もりを取得
```

ウィンドウ内で台帳に ~10 件超の更新がある場合、またはユーザーが求めたときはいつでも、ダッシュボードを提案(CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs` 参照)。この出力向けに提案を整える — 緊急度別(🔴 / 🟠 / 🟡)のカウント、cancel-by タイムライン、相手方/更新日/年額 $/オーナーを含むソート可能な台帳。

### モード 3: [CLM] / 電子署名ツールをスキャンして台帳を埋める

MCP が接続されていて台帳が空または古い場合:

1. ステータス「Active」と更新日フィールドのあるすべての契約を [CLM] にクエリ
2. 過去 24 か月で完了した DocuSign エンベロープのうち、メタデータに「subscription」「renewal」「auto-renew」を含むものをクエリ
3. 各ヒットについて更新メカニクスを抽出し、台帳に追加
4. メタデータから更新日が判定できないものはフラグ — 人間が契約を読む必要がある

これは一度きりの一括ロード。その後は取り込みはレビュー時に発生。

### モード 4: 期限超過(悪い知らせのレポート)

```markdown
## 解約ウィンドウを逃した

以下の契約は解約期限が過ぎ、キャンセルが記録されていません:

| Counterparty | Cancel-by was | Renewal date | Status |
|---|---|---|---|
| [名称] | [日付] | [日付] | [日付] に自動更新予定 |

**選択肢:**
- 遅延キャンセルの交渉(成功は稀だが試す価値あり)
- 更新を受け入れ、来年の cancel-by を今マーク
- 契約に他の解除権(任意解除、解除事由による解除)があるかチェック
```

## ゲート:更新の承諾または辞退

更新日を追跡するのはリサーチ。それに*基づいて行動する* — 非更新通知の送付、自動更新の発火許容、または更新フォームへの副署 — は結果を伴う法的ステップです。

**更新の承諾または辞退に進む前に(非更新通知の送付、または cancel-by 日を過ぎての自動更新の進行を含む):** `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` の `## Who's using this` を読む。Role が非弁護士の場合:

> このステップには法的影響があります(別の期間にコミットするか、関係を終了するか)。弁護士とレビューしましたか? Yes であれば進行。No であれば、弁護士に持ち込むためのブリーフは以下のとおり:
>
> [1 ページのサマリーを生成:相手方、現在の期間終了と cancel-by 日、更新価格メカニズム、何もしなければどうなるか、買い替えを検討する場合の代替ベンダー、ウィンドウが閉じる前に弁護士に尋ねる 3 つの質問。]
>
> 弁護士、ソリシター、バリスター、または他の認定法律専門家を見つける必要があれば:あなたの専門規制機関(米国の州弁護士会、英国 SRA/Bar Standards Board、スコットランド/NI/アイルランド/カナダ/オーストラリアの Law Society、または管轄相当の機関)に連絡 — ほとんどが弁護士紹介サービスを提供。

明示的な「yes」なしにこのゲートを越えて進まない。

## インテグレーション:renewal-watcher エージェント

このプラグインの renewal-watcher エージェントは、このスキルをスケジュール(デフォルト週次)で実行し、「今後到来」レポートを `~/.claude/plugins/config/claude-for-legal-ja/commercial-legal-ja/CLAUDE.md` → `## House style` → 「成果物がどこに行くか」で指定されたチャンネルに投稿します。モード 2 がエージェントの主な出力。

## このスキルが行わないこと

- 契約のキャンセル。判断のタイミングを伝える。
- 更新するかの判断。期限とビジネスオーナーを浮かび上がらせる。
- 更新日を見つけるための契約の読み取り — それはレビュー時に起こる。更新日のない契約が台帳にあれば、それは手動追加で、誰かが空白を埋める必要がある。
