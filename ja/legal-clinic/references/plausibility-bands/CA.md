# Plausibility bands(妥当性レンジ) — California(およびフェデラル、常時ロード)

これらはおおまかな妥当性レンジであって、計算ではありません。学生が入力した期日がレンジ外に出る場合、`/legal-clinic-ja:deadlines --add` フローが再確認のためのフラグを立てます。本スキルは **計算しません** — 学生自身の計算における大きな算術ミスを捕捉します。本ファイルの引用はすべて、指導弁護士がコネクター取得済みまたはユーザー提供のソースに差し替えていない限り、`[model knowledge — verify]` の扱いです。

## 使い方

- クリニックが定常的に扱う期限種別ごとに 1 行。
- 典型的レンジは妥当性ウィンドウであり、裁定(holding)ではありません。
- 学生が再計算できるよう、根拠規則を Notes 欄に引用すること。
- 期間計算ルール(例: California では CCP § 12、§ 12a / フェデラルでは FRCP 6)はすべての項目に適用される。該当する場合は Notes に再掲する。

> **米国法引用について:** 以下に登場する CCP(California Code of Civil Procedure / カリフォルニア民事訴訟法)、CRC(California Rules of Court / カリフォルニア州裁判所規則)、Gov. Code(California Government Code / 同州政府法典)、FRCP(Federal Rules of Civil Procedure / 連邦民事訴訟規則)、FRAP(Federal Rules of Appellate Procedure / 連邦上訴規則)、USC(United States Code / 合衆国法典)、CFR(Code of Federal Regulations / 連邦規則集)、EOIR(Executive Office for Immigration Review / 移民審査局)、CRD(Civil Rights Department / 公民権局)、FEHA(Fair Employment and Housing Act / カリフォルニア州公正雇用住宅法)、NTA(Notice to Appear / 出頭通知書)はいずれも米国法令・規則・機関の固有名称で、原文表記を維持しています。

## California

| 期限種別 | 起算事由からの典型的レンジ | Notes |
|---|---|---|
| CA UD response (post-AB 2347) | 送達後 約 10 ~ 14 暦日 | CCP § 1167 + § 12a に基づき court days(開庁日)で計算; 現行規則と照合せよ |
| CA answer to complaint (non-UD) | 送達後 約 30 日 | CCP § 412.20 / § 430.40; 要確認 |
| CA demurrer / MTD | 送達後 約 30 日 | 答弁書に代えて提出; CCP § 430.40 |
| Notice of appeal (CA civil) | 判決登録通知後 約 60 日 | CRC 8.104; 起算事由(送達か郵送か)を要確認 |
| CA statute of limitations — personal injury | 受傷から 約 2 年 | CCP § 335.1; discovery rule(発見主義)関連の複雑性あり |
| CA statute of limitations — written contract | 違反から 約 4 年 | CCP § 337 |
| CA statute of limitations — oral contract | 違反から 約 2 年 | CCP § 339 |
| CA statute of limitations — fraud | 発見から 約 3 年 | CCP § 338(d) |
| CA small claims appeal | 書記官が判決登録通知を郵送後 約 30 日 | CCP § 116.710; de novo(覆審)の範囲は限定的 |
| CA FEHA right-to-sue lawsuit | CRD の RTS 通知から 約 1 年 | Gov. Code § 12965; 改正前は古い起算ルールが適用される可能性 |
| CA unlawful-detainer post-judgment — motion to stay | 約 5 暦日 | CCP § 918、ローカルルール; 緊急対応のタイムライン |

## Federal(どの州と並列でも常時ロード)

| 期限種別 | 起算事由からの典型的レンジ | Notes |
|---|---|---|
| Federal civil answer (Rule 12(a)) | 送達後 約 21 日(放棄時は 60/90 日) | FRCP 12(a); 送達方法ごとに要確認 |
| Federal MTD / Rule 12 motion | answer ウィンドウと同じ | 答弁書に代えて提出; FRCP 12(b) |
| Notice of appeal (federal civil) | 判決登録後 約 30 日 | FRAP 4(a)(1)(A); 米国が当事者なら 60 日(FRAP 4(a)(1)(B)) |
| Rule 4 service of process | 訴状提出後 90 日 | FRCP 4(m); 裁判所が延長可 |
| Rule 26(f) conference | スケジューリングオーダー前、典型的に Rule 16 の約 21 日前 | FRCP 26(f); ローカルルールによる差異あり |
| Asylum one-year filing rule | 直近の入国から 約 1 年 | 8 USC § 1158(a)(2)(B); 例外あり |
| EOIR / 移民裁判所 — 典型的な応答・申立 | NTA または命令に従う — 普遍的デフォルトなし | 命令を読むこと; 想定しないこと |
| Motion for reconsideration (EOIR) | 最終命令後 約 30 日 | 8 CFR § 1003.23(b)(1); 要確認 |
| Habeas petition — § 2254 1-year SOL | 確定州判決または新事実・新法令から 約 1 年 | 28 USC § 2244(d); tolling(時効停止)ルールあり |

## 期間計算に関するリマインダー

- **California 州裁判所:** CCP § 12(初日不算入、末日算入)、§ 12a(期日が週末・祝日に当たる場合は翌開庁日に延長)、§ 1010.6 / § 1013(郵送・FAX・電子の送達方法に応じた期間延長)。
- **Federal 裁判所:** FRCP 6(a)(暦日計算、週末・祝日に伴う延長)、FRCP 6(d)(該当する場合の郵送 3 日延長)。
- **ローカルルール:** 必ず確認すること。本バンドファイルは妥当性チェックであって、裁判所自身の規則の代替ではありません。
