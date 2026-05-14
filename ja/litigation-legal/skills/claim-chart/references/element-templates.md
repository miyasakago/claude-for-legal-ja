# 請求原因 要素テンプレート

一般的な民事請求原因と積極的防御の baseline 要素リスト。**これらは baseline であり、支配的な法ではない。** ユーザーの法域の要素 — パターン陪審指示(カリフォルニアの CACI、ニューヨークの NYPJI、連邦サーキットのパターンチャージ、または州固有パターン)または支配的制定法に記述されたもの — が支配。マッピング前に常に確認。

ここの各テンプレートは baseline がどの出典から来たかを述べる。チャートの `_elements` シートは、使用されたテンプレートとユーザーが確認した法域固有出典を記録すべき。

---

## 使い方

1. 訴答されたカウントにマッチするテンプレートを選ぶ。
2. ユーザーと確認: 「Does your jurisdiction's pattern instruction add, drop, or reword any of these?」
3. Yes ならマッピング前にリストを編集。
4. `_elements` シートに記録: 使用されたテンプレート、参照されたパターン指示または制定法、任意の法域固有修正。

このライブラリにないカウント — 陪審指示、制定法、または訴状主張から直接マップ。このライブラリは網羅的ではない;繰り返されるものをカバーする。

---

## 契約

### 契約違反

**要素(baseline — Restatement (Second) of Contracts;CACI 303):**
1. 契約の存在
2. 原告の履行または不履行の言い訳
3. 被告の違反
4. 因果関係(違反が害を引き起こした)
5. 損害賠償

*法域 caveat: 一部の法域は契約形成をサブ要素(オファー、受諾、約因、相互合意)に分け別々に訴答することを要求。詐欺防止法は特定の契約タイプに書面要件を追加する可能性 — prima facie ケースの要素ではなく防御。*

### 誠実取引と公正取引の黙示的契約違反

**要素(baseline — Restatement (Second) of Contracts § 205;CACI 325):**
1. 契約の存在
2. 原告が契約が要求したことのすべてまたは substantial にすべてを行った
3. 被告の履行に必要なすべての条件が発生した
4. 被告が原告の契約利益受領権を unfairly に妨害した
5. 原告が被告の行為で害を受けた

*法域 caveat: ほとんどの州で認められるが、ニューヨークでは独立した不法行為ではない(保険コンテキストに限定);カリフォルニアでは契約を要求し、契約違反自体とは distinct な別個の請求原因。*

### Promissory Estoppel

**要素(baseline — Restatement (Second) of Contracts § 90):**
1. 明確で曖昧でない約束
2. 受諾者による合理的で予見可能な reliance
3. 受諾者の不利益に actual reliance
4. 約束の執行によってのみ avoidable な不公正

### 不当利得 / Quantum Meruit

**要素(baseline — Restatement (Third) of Restitution and Unjust Enrichment):**
1. 原告から被告への利益付与
2. 被告の利益の知識(一部法域)
3. 支払いなしに保持することを不公正にする状況下での被告の利益の受諾と保持

*法域 caveat: 要素と利用可能性は significantly に変わる — 一部法域は adequate な法的救済の不在を要求;一部は同じ subject matter を支配する有効な契約があるとき不当利得をスタンドアロンクレームとして認めない。*

---

## 不法行為 — 過失と関連

### 過失

**要素(baseline — Restatement (Second) of Torts §§ 281, 328A;CACI 400):**
1. 注意義務
2. 義務違反
3. Actual cause(cause in fact)
4. Proximate cause(legal cause)
5. 損害賠償

*法域 caveat: 寄与過失 vs 比較過失体制は防御としてチャートがどう動作するかに影響。一部法域は感情的苦痛賠償の回復に身体的傷害を要求。*

### Negligence per se

**要素(baseline):**
1. 被告が制定法、条例、または規制に違反した
2. 違反が原告の傷害を proximately に引き起こした
3. 原告が制定法が保護するよう設計された人のクラスにいる
4. 害が制定法が防ぐよう設計されたタイプ

### Gross Negligence / Recklessness

**要素(baseline):**
1. 注意義務
2. 注意基準からの極端な逸脱
3. Actual と proximate cause
4. 損害賠償

*法域 caveat: しばしば契約上の責任制限を defeat し、punitive damages を支持するのに関連。定義は法域で材料的に変わる。*

---

## 不法行為 — 意図的

### 詐欺 / 故意誤表示

**要素(baseline — Restatement (Second) of Torts § 525;CACI 1900):**
1. 重要事実の誤表示(または actionable な省略)
2. 虚偽性の知識(scienter)
3. Reliance を誘導する意図
4. Justifiable(または合理的)reliance
5. Reliance によって proximately に引き起こされた損害賠償

*法域 caveat: Fed. R. Civ. P. 9(b) とほとんどの州相当下で特殊性を持って訴答される必要。一部法域は積極的誤表示を省略(開示義務でのみ actionable)から区別する。*

### 過失誤表示

**要素(baseline — Restatement (Second) of Torts § 552):**
1. 重要事実の誤表示
2. それが真実と信じる合理的根拠なし
3. Reliance を誘導する意図(または他者の指導のために事業の過程でなされた)
4. Justifiable reliance
5. Proximately に引き起こされた損害賠償

*法域 caveat: 一部法域は信認関係または特別関係を要求;一部はそうでない。経済損失ルールは損失が純粋に経済的な場合の回復を妨げる可能性。*

### 詐欺的隠匿

**要素(baseline):**
1. 重要事実の隠匿または抑圧
2. 被告が開示義務を持っていた
3. 詐欺の意図(reliance を誘導する目的で隠匿)
4. 原告が知らず、知っていればそうしなかった
5. 損害賠償

### 契約への不法行為的干渉

**要素(baseline — Restatement (Second) of Torts § 766;CACI 2201):**
1. 原告と第三者間の有効な契約の存在
2. 被告の契約の知識
3. 違反または disruption を誘導するよう設計された被告の意図的行為
4. Actual な違反または disruption
5. 損害賠償

### 見込み経済利益への不法行為的干渉

**要素(baseline — Restatement (Second) of Torts § 766B):**
1. 将来の経済利益の確率を持つ経済関係の存在
2. 被告の関係の知識
3. Disrupt するよう設計された意図的、wrongful(独立して不法行為的または違法)な行為
4. Actual な disruption
5. 損害賠償

*法域 caveat: カリフォルニアといくつかの他州は干渉行為が「独立して wrongful」であること — 干渉自体を超える別個の wrongful 行為 — を要求。*

### 名誉毀損(libel / slander)

**要素(baseline — Restatement (Second) of Torts § 558;CACI 1700 シリーズ):**
1. 事実の虚偽陳述(意見ではない)
2. 第三者への公表
3. Fault — 過失(私的原告、公的関心事項)または actual malice(公人 / 公的職員 — *New York Times Co. v. Sullivan*, 376 U.S. 254 (1964))
4. 損害賠償(per se カテゴリーは special damages を obviate する可能性)

*法域 caveat: Per se / per quod の区別は変わる。一部州は前提条件として retraction 要求を要する。多くの州の anti-SLAPP 制定法は早期段階で負担を変える。*

### Conversion(動産横領)

**要素(baseline — Restatement (Second) of Torts § 222A):**
1. Conversion 時の財産に対する原告の所有または占有権
2. 被告の wrongful 行為(原告の権利と矛盾する dominion の行使)
3. 損害賠償

### Trespass to Chattels(動産侵害)

**要素(baseline — Restatement (Second) of Torts §§ 217, 218):**
1. 動産に対する原告の占有的利益
2. 原告の使用または占有への被告の意図的干渉
3. Actual な損害(占有喪失、状態の impairment、使用の deprivation)

### 故意の感情的苦痛賦課

**要素(baseline — Restatement (Second) of Torts § 46):**
1. 極端で outrageous な行為
2. 重大な感情的苦痛を引き起こす意図、または引き起こす確率の reckless disregard
3. 重大な感情的苦痛
4. Actual と proximate な因果関係

---

## 信認 / 法人

### 信認義務違反

**要素(baseline):**
1. 信認関係の存在
2. 信認義務の違反(注意義務、忠誠義務、または誠実義務)
3. 因果関係
4. 損害賠償(または equity では、不当利得 / 剥奪)

*法域 caveat: デラウェアのフレームワークは注意義務、忠誠義務(誠実を含む)を区別し、business judgment rule を推定として適用。Entire fairness レビューは利益相反取引に適用。Demand futility / 派生 standing ルールは派生クレームの significant な手続要素を追加。*

### 信認義務違反の幇助

**要素(baseline):**
1. 信認義務の存在
2. 信認者によるその義務の違反
3. 被告による違反への knowing 参加
4. Proximately に引き起こされた損害賠償

---

## 証券

### §10(b) / Rule 10b-5 証券詐欺

**要素(baseline — *Dura Pharmaceuticals, Inc. v. Broudo*, 544 U.S. 336 (2005);*Stoneridge Inv. Partners v. Scientific-Atlanta*, 552 U.S. 148 (2008)):**
1. 重要な誤表示または省略(開示義務があるとき省略が actionable)
2. Scienter(欺く、操作する、または defraud する意図 — または最低でも recklessness)
3. 証券の購入または売却との接続
4. Reliance(*Basic Inc. v. Levinson*, 485 U.S. 224 (1988) ごとに fraud-on-the-market 下で推定)
5. 経済的損失
6. 損失因果関係(誤表示が損失を引き起こした)

*法域 caveat: PSLRA heightened 訴答基準が連邦裁判所で適用 — scienter は強い推論を生む特殊性を持って訴答される必要。クラス認証は追加の *Halliburton* / *Amgen* 分析を要求。*

### §11 証券法

**要素(baseline):**
1. 登録ステートメントに従って発行された証券の取得
2. 登録ステートメントの重要な誤表示または省略
3. Tracing(原告の株が defective とされる登録ステートメントに traceable)

*法域 caveat: 発行者には厳格責任;引受人と取締役には due diligence 防御。損害賠償は statutorily に定義される。*

---

## 反トラスト

### Sherman Act § 1(取引制限の合意)

**要素(baseline):**
1. 契約、結合、または陰謀の存在(2 つ以上の独立した経済的アクター間の協調行為)
2. 不合理な取引制限(per se 違法カテゴリーまたは rule-of-reason 分析)
3. 州際商業への影響
4. 反トラスト傷害
5. 損害賠償

### Sherman Act § 2(独占化)

**要素(baseline):**
1. 関連市場での独占力の所有
2. その力の willful な取得または維持(優れた製品、ビジネス acumen、または歴史的偶然の結果としての成長または発展と区別される)
3. 反トラスト傷害
4. 損害賠償

---

## 雇用

### Title VII 不利益取扱い(McDonnell Douglas burden-shifting)

**Prima facie 要素(baseline — *McDonnell Douglas Corp. v. Green*, 411 U.S. 792 (1973)):**
1. 保護されたクラスのメンバーシップ
2. その地位の資格
3. 不利な雇用行為
4. 差別の推論を生じる状況(しばしば: 保護クラス外の類似状況の従業員がより favorably に扱われた、または保護クラス外の人によってその地位が埋められた)

*その後: 負担は被告に正当な非差別理由を articulate するためにシフト、その後原告に pretext を示すために戻る。チャートは prima facie 証拠を pretext 証拠から分離すべき。*

### Title VII 敵対的職場環境

**要素(baseline — *Harris v. Forklift Systems, Inc.*, 510 U.S. 17 (1993)):**
1. 保護されたクラスのメンバーシップ
2. 歓迎されないハラスメント
3. 保護された特徴に基づくハラスメント
4. 雇用条件を変え abusive な労働環境を作るのに十分 severe または pervasive なハラスメント
5. ハラスメント実行者によって、雇用主の責任(*Faragher/Ellerth* 下のスーパーバイザー;*Vance v. Ball State University*, 570 U.S. 421 (2013) 下の同僚)

### Title VII 報復

**Prima facie 要素(baseline — *Burlington N. & S. F. R. Co. v. White*, 548 U.S. 53 (2006);*University of Texas Southwestern Med. Ctr. v. Nassar*, 570 U.S. 338 (2013)):**
1. 保護された活動(差別への反対または手続への参加)
2. 重大な不利益な行為(合理的従業員が保護された活動への従事を dissuade するであろうもの)
3. 保護された活動と不利な行為間の but-for 因果接続

### ADEA 不利益取扱い

**要素(baseline — *Gross v. FBL Fin. Servs., Inc.*, 557 U.S. 167 (2009)):**
1. 原告が 40 歳以上
2. その地位の資格
3. 不利な雇用行為
4. But-for 因果関係 — 年齢が不利な行為の but-for cause(単に motivating factor ではない)

### FLSA 残業クレーム

**要素(baseline):**
1. FLSA でカバーされる雇用主・従業員関係(enterprise または個人カバレッジ)
2. 従業員が workweek で 40 時間超働いた
3. 雇用主が残業時間に time-and-a-half を支払わなかった
4. 従業員が non-exempt(exemptions は積極的防御)

### 公共政策違反による不当解雇

**要素(baseline — 州で変わる;カリフォルニア *Tameny* 定式が代表的):**
1. 雇用主・従業員関係
2. 解雇(または constructive discharge)
3. 制定法または憲法規定に tether された基本的公共政策の違反
4. 損害賠償

---

## 営業秘密 / IP(民事)

### 営業秘密 misappropriation(DTSA / UTSA)

**要素(baseline — 18 U.S.C. § 1836;UTSA § 1):**
1. 情報が営業秘密として qualify(一般的に知られていない;一般的に知られていないことから経済的価値を派生)
2. 所有者が秘密性を維持する合理的な措置を取った
3. Misappropriation — 不適切な手段による取得、または秘密性を維持する義務の違反での開示 / 使用

*法域 caveat: DTSA は州際 nexus を要求。UTSA はほとんどの州で採用されるがニューヨーク(Restatement of Torts § 757 アプローチに従う)またはマサチューセッツ(MUTSA)ではない。関連コモンロー不法行為クレームの preemption は変わる。*

### 著作権侵害

**要素(baseline — 17 U.S.C. § 501):**
1. 有効な著作権の所有
2. オリジナルの作品の constituent な要素のコピー

*法域 caveat: 侵害訴訟提出前に登録(または前登録)が必要 — *Fourth Estate Public Benefit Corp. v. Wall-Street.com, LLC*, 586 U.S. 296 (2019)。Substantial similarity 分析はサーキットで変わる。*

### 商標侵害(Lanham Act § 32 / § 43(a))

**要素(baseline — 15 U.S.C. §§ 1114, 1125(a)):**
1. 原告が有効で保護可能なマークを所有(登録は aid;§ 43(a) では不要)
2. 被告の類似マークの商業での使用
3. 関連消費者間の混同の likelihood

*法域 caveat: マルチファクター likelihood-of-confusion テストはサーキットで変わる(Sleekcraft、Polaroid、du Pont、など)。特許侵害は、このスキルの特許モードにルート。*

---

## 財産

### Trespass to Land(土地侵害)

**要素(baseline — Restatement (Second) of Torts § 158):**
1. 土地に対する原告の占有
2. 被告の意図的入場(または物の入場の引き起こし)
3. 同意または特権なし

### Nuisance(私的)

**要素(baseline — Restatement (Second) of Torts § 821D):**
1. 土地の使用と享受における原告の利益
2. その使用と享受への substantial で unreasonable な干渉
3. 被告の行為(意図的または過失)によって引き起こされた
4. 損害賠償

---

## 積極的防御(選択)

防御は防御を上げる当事者が一般的に立証しなければならない独自の要素を持つ。請求原因と同じようにマップ — 要素、証拠、ギャップリスト。

### 出訴期限

**要素(baseline):**
1. クレームに適用可能な制限期間
2. クレームが特定の日に accrued(任意の discovery-rule または tolling 分析付き)
3. 訴状が期間切れ後に提出された

### Laches(衡平防御)

**要素(baseline):**
1. クレーム主張における原告の unreasonable な遅延
2. 遅延によって引き起こされた被告への prejudice

### 衡平 Estoppel

**要素(baseline):**
1. 被告の行為または表明
2. それへの原告の reliance
3. 原告の地位の detrimental 変化
4. Estoppel が適用されない場合の不公正

### 放棄

**要素(baseline):**
1. 既知の権利の存在
2. その権利の自発的放棄(意図的かつ知識を持って)

### Unclean Hands(衡平防御)

**要素(baseline):**
1. 原告による inequitable または wrongful 行為
2. クレームの対象に直接関連する行為
3. 被告への prejudice

### Release(免責)

**要素(baseline):**
1. 有効な免責契約
2. 該当するクレームをカバー
3. 約因によって支えられている(一般的に)
4. 権限を持つ当事者によって執行

### Accord and Satisfaction(和解と履行)

**要素(baseline — negotiable instruments のための UCC § 3-311;それ以外はコモンロー):**
1. 未確定または争われるクレームに関する bona fide な紛争
2. 紛争を解決する合意
3. 完全履行での tender
4. Tender の受諾

### 損害賠償緩和の失敗

**要素(baseline):**
1. 原告が合理的努力で損害賠償を減らせた
2. 原告がその努力をしなかった
3. 損害賠償が減らせた額

### 比較過失 / 寄与過失

**要素(baseline — 法域依存):**
1. 原告自身の過失行為
2. 原告自身の傷害の proximate cause
3. (比較体制)過失の分配

*法域 caveat: 純粋な比較 vs 修正比較(50% または 51% bar) vs 純粋な寄与(アラバマ、メリーランド、ノースカロライナ、バージニア、D.C. — 完全 bar) — 法域の体制が effect を決定。*

### リスク引受

**要素(baseline):**
1. 原告のリスクの actual な知識
2. 原告のリスクの自発的受諾
3. そのリスクから生じた傷害

### 行政救済の exhaust 失敗

**要素(baseline):**
1. 制定法または規制が行政 exhaustion を要求
2. 原告が必要な行政プロセスを完了しなかった
3. 認識された例外(futility、irreparable harm、など)が適用しない

---

## テンプレートの追加

このライブラリは網羅的ではない。新規請求原因または防御が出てきたとき:
1. 支配的パターン指示、制定法、または Restatement から要素をマップ。
2. テンプレートが案件を跨いで繰り返される可能性が高ければ、引用付きでここに追加。
3. 法域 caveat を注記 — 要素が変わるなら、そう言い、1 つの代表的な代替定式を与える。

テンプレートは baseline であり、権威ではない。支配的パターン指示または制定法が常に支配。
