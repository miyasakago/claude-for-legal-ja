# 規制ソースカタログ

reg-feed-watcher のためのスターターカタログ。コールドスタート・インタビューが
どのソースを watch するかを設定する;このカタログは選択肢を提供する。URL は
**2026 年 5 月** 時点で検証済み — フィード URL は変わるので、ソースが結果を返さなくなったら検証してください。

**このカタログの読み方:**
- **Format** — フィードが返すもの: JSON API(構造化、最良)、RSS/Atom(半構造化、良)、HTML page(スクレイピングまたは変更検出が必要)、Email のみ(Gmail/Outlook MCP が必要)。
- **Tier** — *Primary* は規制機関自体;*Secondary* は primary source を要約するコメンテーター、aggregator、または法律事務所。authoritative として扱う前に、必ず secondary source を primary に traced back する。
- **Auth** — None は open;Key は無料だが登録が必要な API key;Paid はサブスクリプション。
- **Notes** — 注意点(レート制限、フィード退役、発見ステップ)。

⚠️ がついたソースは、ユーザーまたは規制機関から信頼できないか中止されたと
報告されている — 設定前に検証してください。

---

## US 連邦 — Primary

| Source | Feed URL | Format | カバー | Auth | Notes |
|---|---|---|---|---|---|
| Federal Register(米国連邦官報) | `https://www.federalregister.gov/api/v1/documents.json` | JSON API | すべての連邦規則、規則案、通知、大統領文書 | None | `conditions[agencies][]=<slug>`、`conditions[publication_date][gte]=<YYYY-MM-DD>`、`conditions[type][]=RULE\|PRORULE\|NOTICE\|PRESDOCU` でフィルタ。documented: federalregister.gov/developers/documentation/api/v1。要旨、施行日、コメント締切、citation を返す。**これを最初に使う** — ほとんどの連邦機関ドキュメントはここを通る。 |
| Regulations.gov(米国規則案コメント窓口) | `https://api.regulations.gov/v4/documents` | JSON API | 規則制定 dockets、パブリックコメント、サポーティング文書 | Key(無料) | Key は open.gsa.gov/api/regulationsgov/。docket レベルトラッキングとコメント pull に使う。 |
| Congress.gov(米国議会公式サイト) | `https://api.congress.gov/v3/bill` | JSON API | 連邦法案、法、委員会レポート | Key(無料) | Key は api.congress.gov/sign-up。事前構築済みの RSS も congress.gov/rss にあり(narrower: 大統領提出法案、最多閲覧、floor)。 |
| SEC(米国証券取引委員会)Press Releases | `https://www.sec.gov/news/pressreleases.rss` | RSS | 規則、執行、スピーチ(press release のみ) | None | SEC RSS hub: sec.gov/about/rss-feeds。EDGAR の構造化 filing フィードも sec.gov/structureddata/rss-feeds(営業時間中 10 分ごとに更新)。Rules-adopted ニュースは通常 Federal Register にも投稿 — 重複排除する。 |
| FTC(米国連邦取引委員会)Press Releases | `https://www.ftc.gov/feeds/press-release.xml` | RSS | 執行、規則、ブログ投稿、settlement | None | トピック別サブフィード: `ftc.gov/feeds/press-release-consumer-protection.xml`、`press-release-competition.xml`、blog feed `ftc.gov/feeds/business-blog.xml`。フィードハブ: ftc.gov/news-events/stay-connected/ftc-rss-feeds。 |
| CFPB(米国消費者金融保護局)Newsroom | `https://www.consumerfinance.gov/about-us/newsroom/` | HTML + ページ上の RSS オプション | 規則、執行、回覧、ブログ | None | ページが RSS サブスクリプションを提供;`consumerfinance.gov/activity-log/` のアクティビティログが最広範な単一 URL。主要規則は Federal Register にも。 |
| DOJ(米国司法省)Antitrust Division | `https://www.justice.gov/atr/news-feeds` | RSS(複数フィード) | プレスリリース、スピーチ、statements of interest | None | ページはコンテンツタイプ別に複数の Atom/RSS URL をリスト。DOJ メインのプレスリリースフィードは justice.gov の sibling(`justice.gov/news` からナビゲート)。 |
| DOJ Main | `https://www.justice.gov/news/rss` | RSS | division 全体のすべての DOJ プレスリリース | None | クライアントサイドでトピックでフィルタ。Civil Division、ATR、Civil Rights Division すべてが流入。 |
| FCC(米国連邦通信委員会)Daily Digest | `fcc.gov/news-events/rss-feeds-and-email-updates-fcc` 経由でサブスク | RSS + email | 命令、通知、public notices | None | ECFS docket 別フィードも持つ — 「Hot Dockets」から docket を選び、RSS アイコンを右クリック。 |
| HHS(米国保健福祉省)OCR | `https://www.hhs.gov/ocr/newsroom/index.html` | HTML | HIPAA 執行、settlement、guidance | None | 直接 RSS は見つからず;HHS 全体のプレスリリースフィードが `hhs.gov/rss` にあり OCR をカバー。X で `@HHSOCR` をフォローしてプッシュアラートを受ける。 |
| OFAC(米国外国資産管理局)Recent Actions | `https://ofac.treasury.gov/recent-actions` | HTML + email | sanctions designations、一般 license、FAQ | None | ⚠️ 2025 年 1 月 31 日に RSS 退役。Email がサポートされる push チャネル — service.govdelivery.com/service/multi_subscribe.html?code=USTREAS でサブスクライブ。ページには browsable リスト。 |
| BIS(米国産業安全保障局/商務省) | `https://www.bis.gov/news-updates` | HTML | 輸出規制更新、Entity List、final rules | None | `bis.gov/regulations/federal-register-notices` の Federal Register 通知 index が最もクリーンなリスト。公開 RSS は見つからず。 |
| DOL(米国労働省)News Releases | `https://www.dol.gov/rss/releases.xml` | RSS | 賃金/時間、OSHA、OFCCP、EBSA プレスリリース | None | 他のフィードは `dol.gov/rss` にインデックス。 |
| NIST(米国国立標準技術研究所)Cybersecurity | `https://www.nist.gov/news-events/cybersecurity/rss.xml` | RSS | トピック別 cybersecurity ニュース | None | AI/blog フィード: `nist.gov/blogs/cybersecurity-insights/rss.xml`。 |
| CISA(米国サイバーセキュリティ・インフラセキュリティ庁)Alerts/Advisories | `https://www.cisa.gov/news-events/cybersecurity-advisories` | HTML + RSS オプション | ICS advisories、alerts | None | ページでフィード URL を検証;コンテンツタイプ別に複数のサブフィード。 |

---

## US 州 — Primary

カバレッジは uneven。active なプライバシー/消費者保護執行を持つ州を優先。
多くの州規制機関は HTML のみのページを公開 — RSS がない場合、
「manual」として設定するか web-page change detection を設定する。

| Source | Feed URL | Format | カバー | Auth | Notes |
|---|---|---|---|---|---|
| California AG(カリフォルニア州司法長官) | `https://oag.ca.gov/news/feed/729/oag.ca.gov` | RSS | プレスリリース、CCPA(カリフォルニア州プライバシー法)執行、複数州アクション | None | メインプレスページ: `oag.ca.gov/media/news`。 |
| California Privacy Protection Agency (CPPA / カリフォルニア州プライバシー保護機関) | `https://cppa.ca.gov/announcements/` | HTML | CCPA 規制、執行、advisories | None | ⚠️ 直接 RSS URL は見つからず — primary チャネルはメーリングリスト(ページでサインアップ)。ページの変更を監視するか manual entry を使う。 |
| New York AG(ニューヨーク州司法長官) | `https://ag.ny.gov/press-releases` | HTML | プレスリリース、複数州 AG アクション | None | ⚠️ 公開 RSS は見つからず。`ag.ny.gov/press-releases-for-month` の月次アーカイブは scrape 可能なほど構造化。 |
| Texas AG(テキサス州司法長官) — News Releases | `https://www2.texasattorneygeneral.gov/feeds/feeds.php?feed=pr` | RSS | プレスリリース | None | 追加のフィードは `www2.texasattorneygeneral.gov/agency/feeds`。 |
| Illinois AG(イリノイ州司法長官) | `https://illinoisattorneygeneral.gov/news-room/` | HTML | プレスリリース | None | ⚠️ 公開 RSS は見つからず。 |
| Washington AG(ワシントン州司法長官) | `https://www.atg.wa.gov/news` | ページ上の RSS オプション | 最新ニュース、AGO opinions、consumer alerts | None | ニュース、opinions、consumer alerts 用に別フィード — ページからサブスク。 |
| Colorado AG(コロラド州司法長官) | `https://coag.gov/press-releases/` | HTML | プレスリリース、CPA(コロラド州プライバシー法)規則制定 | None | ⚠️ 公開 RSS は見つからず。Colorado Privacy Act 規則制定は SOS 経由でも公開。 |
| Connecticut AG(コネチカット州司法長官) | `https://portal.ct.gov/ag/press-releases/press-releases` | HTML | プレスリリース | None | ⚠️ 公開 RSS は見つからず。 |
| Virginia AG(バージニア州司法長官) | `https://www.oag.state.va.us/media-center/news-releases` | HTML | プレスリリース、VCDPA(バージニア州消費者データ保護法)監督 | None | ⚠️ 公開 RSS は見つからず。 |
| Massachusetts AG(マサチューセッツ州司法長官) | `https://www.mass.gov/orgs/office-of-attorney-general-maura-healey/news` | HTML | プレスリリース | None | ⚠️ 公開 RSS は見つからず。Mass.gov は組織別 newsroom ページを持つ。 |
| NYDFS(ニューヨーク州金融サービス局) | `https://www.dfs.ny.gov/reports_and_publications/press_releases` | HTML | 執行、規制、cybersecurity(Part 500) | None | ⚠️ 公開 RSS は見つからず。 |

---

## EU / UK — Primary

| Source | Feed URL | Format | カバー | Auth | Notes |
|---|---|---|---|---|---|
| EDPB(欧州データ保護会議)News | `https://www.edpb.europa.eu/news/news_en` | RSS(2 フィード提供) | ガイドライン、opinions、執行サマリ、拘束力ある決定 | None | フィードは `edpb.europa.eu/sme-data-protection-guide/faq-frequently-asked-questions/answer/how-can-i-keep-edpbs-work_en` で広告。 |
| European Commission(欧州委員会)Press Corner | `https://ec.europa.eu/commission/presscorner/` | RSS + email | プレスリリース、スピーチ、Q&A — DSA、DMA、AI Act 実施 acts | None | `ec.europa.eu/commission/presscorner/login/en` でサブスク。トピック別の narrower サブフィード。 |
| EUR-Lex (OJ / EU 法令データベース) | `https://eur-lex.europa.eu/` | Webservice + サーチ別 RSS | Official Journal 公開 | Key(無料、webservice) | 最終形式の規則と指令の追跡に使う。 |
| ICO(英国情報コミッショナー) | `https://ico.org.uk/global/rss-feeds/` | RSS(複数フィード) | 執行、ガイダンス、ニュース、コンサルテーション | None | ニュース、執行措置、blog 用に別フィード。執行リストは `ico.org.uk/action-weve-taken/enforcement/` にも。 |
| CNIL(フランスデータ保護当局) | `https://www.cnil.fr/en/rss.xml`(検証 — feeder.co がこれをインデックス) | RSS | フランス DPA 決定、ガイダンス、sanctions | None | 英語ニュースは `cnil.fr/en/news`。サードパーティインデックスはフィードの存在を示唆;依拠する前に検証。 |
| DPC(アイルランドデータ保護委員会) | `https://www.dataprotection.ie/en/news-media/latest-news` | HTML | 調査、決定、ガイダンス — 大半の US tech 企業の lead DPA | None | ⚠️ 公開 RSS は見つからず。US 企業に対する GDPR 執行で critical なソース;change-detection またはメールサブスクの価値あり。 |
| BfDI(ドイツ連邦データ保護コミッショナー) | `https://www.bfdi.bund.de/EN/Home/home_node.html` | HTML | 連邦ドイツ DPA | None | ⚠️ 公開 RSS は見つからず。 |
| ENISA(EU サイバーセキュリティ機関) | — | Email | サイバーセキュリティ、NIS2 ガイダンス | None | ⚠️ **新ウェブサイトで RSS フィード中止**。新サブスクメカニズムが launch するまでメールアラートのみ(`enisa.europa.eu/rss-feeds-discontinued-new-subscription-mechanism-coming-soon`)。 |
| FCA(英国金融行動監視機構) | `https://www.fca.org.uk/news/rss.xml`(検証) | RSS + email | 英国金融サービスのルール、執行、warnings | None | `fca.org.uk/newsletters-emails-sign-up` のメールアラートがサポートされるチャネル;RSS は歴史的に提供。 |
| EDPS(欧州データ保護監督官) | `https://www.edps.europa.eu/press-publications/press-news_en` | HTML + RSS オプション | EU-institutional DPA | None | |

---

## 国際

| Source | Feed URL | Format | カバー | Auth | Notes |
|---|---|---|---|---|---|
| OECD(経済協力開発機構)AI Policy Observatory | `https://oecd.ai/en/` | HTML + newsletter | 各国 AI ポリシー、OECD ガイダンス | None | 非 EU、非 US の AI 規則制定を追跡するのに最良。 |
| Council of Europe(欧州評議会) | `https://www.coe.int/en/web/portal/news` | RSS + HTML | AI Framework Convention を含む CoE 条約 | None | |
| UK Parliament Bills(英国議会法案) | `https://bills.parliament.uk/rss/publicbills.rss`(検証) | RSS | 英国法案 | None | |

---

## Secondary / Aggregators

**これらのソースのコンテンツを leads として扱い、authority としてではなく。**
secondary source が「FTC が X を発出した」と言うのは: ftc.gov で X を見つけ、それに依拠する、という意味。
これらのフィードから pull した項目はダイジェストで `[secondary source]` とタグ付け。

| Source | Feed URL | Format | カバー | Auth | Notes |
|---|---|---|---|---|---|
| IAPP(国際プライバシー専門家協会)Daily Dashboard | `https://iapp.org/rss/daily-dashboard/` | RSS | グローバルプライバシー + AI ガバナンスニュース、curated | None(一部 paywall) | プライバシーチーム向けに最高の signal-to-noise。 |
| Future of Privacy Forum(FPF) | `https://fpf.org/feed/` | RSS(WordPress) | プライバシーコメント、州法トラッカー、レポート | None | |
| Hogan Lovells(法律事務所) | `https://www.hoganlovells.com/en/rss` | RSS(プラクティス別複数) | クライアントアラート、engagements | None | プラクティス別サブフィードを提供。 |
| Covington & Burling(法律事務所) | `https://www.cov.com/`(ブログごとに検証) | ブログ別 RSS | InsidePrivacy、Global Policy Watch、Inside Global Tech、Inside Tech Media | None | 各トピックブログは標準的な `/feed` エンドポイントを持つ WordPress スタイルサイト。 |
| WilmerHale(法律事務所) | `https://www.wilmerhale.com/` | Email / HTML | クライアントアラート | None | ⚠️ consolidated 公開 RSS は見つからず;メールサブスクが primary。 |
| Wilson Sonsini(法律事務所) | `https://www.wsgr.com/` | Email / HTML | クライアントアラート | None | ⚠️ consolidated 公開 RSS は見つからず。 |
| Lexology | `https://www.lexology.com/account/rss` | RSS(トピック/法域でカスタマイズ可能) | aggregated ファームアラート | アカウント(無料) | 強力: トピック+法域フィードを構築。LBR が所有。 |
| JD Supra | `https://www.jdsupra.com/legal-news/rss-law-feeds.aspx` | RSS(トピック別複数) | aggregated ファームアラート | None | Lexology より広いがノイジー。 |
| Artificial Lawyer | `https://www.artificiallawyer.com/feed/` | RSS | 法律テック / AI 規制ニュース | None | |
| LawSites (Bob Ambrogi) | `https://www.lawsitesblog.com/feed` | RSS | 法律テック、legal AI の規制もカバー | None | |

---

## フィードなしのソース(web monitoring または email が必要)

一部の重要なソースはフィードを公開していないか、RSS が退役している。
監視には以下のいずれかが必要:
- Web-page change detection(現在は組み込みではない)
- メールニュースレター転送(Gmail/Outlook MCP 統合が必要)
- reg-feed-watcher "manual entry" パス経由の手動チェック

| Source | URL | Notes |
|---|---|---|
| OFAC Recent Actions | `https://ofac.treasury.gov/recent-actions` | 2025 年 1 月に RSS 退役;email がサポートチャネル |
| ENISA | `https://www.enisa.europa.eu/news` | RSS 中止;新サブスクメカニズム保留中 |
| DPC Ireland | `https://www.dataprotection.ie/en/news-media/latest-news` | RSS なし;GDPR 執行に critical |
| CPPA | `https://cppa.ca.gov/announcements/` | メーリングリストのみ;RSS は見つからず |
| ほとんどの州 AG(NY、IL、CO、CT、VA、MA) | 上記の州テーブル参照 | プレスリリース HTML ページ;RSS なし |
| NYDFS | `https://www.dfs.ny.gov/reports_and_publications/press_releases` | HTML のみ |
| BIS(商務省) | `https://www.bis.gov/news-updates` | HTML のみ;ルールレベルイベントには Federal Register API を使う |
| HHS OCR スタンドアロン | `https://www.hhs.gov/ocr/newsroom/` | HHS 全体 RSS には含まれるが OCR 専用フィードなし |
| BfDI(ドイツ) | `https://www.bfdi.bund.de/EN/` | HTML のみ |
| WilmerHale、Wilson Sonsini | ファームサイト | メールサブスクが primary チャネル |

---

## 提案されるスターターパック

**プライバシー重視の in-house チーム(US + EU):**
Federal Register(FTC、HHS/OCR の agency フィルタ)、FTC RSS、CFPB、CA AG、CPPA(email)、
NY AG(page watch)、EDPB、ICO、CNIL、DPC Ireland(page watch)、IAPP、FPF。

**Commercial / regulatory in-house チーム(broad):**
Federal Register(関心のあるすべての agencies)、SEC RSS、CFPB、DOJ Antitrust、DOJ
Main、FCC、DOL、BIS page watch、OFAC email、European Commission Press Corner、
FCA。aggregator カバレッジに IAPP + Lexology を追加。

**AI ガバナンスチーム:**
Federal Register(filter: FTC、HHS、NIST、Commerce)、NIST Cybersecurity RSS、EU
Commission Press Corner、EDPB、OECD AI Observatory、Council of Europe、IAPP、FPF、
Artificial Lawyer、CA AG(ADMT)、CPPA。

---

## ソースを追加する

このカタログにないソースを追加する場合:
1. フィード URL を見つける(`/rss`、`/feed`、`/news.rss` を試す、またはページソースで `<link rel="alternate" type="application/rss+xml">` を見る)。
2. ブラウザまたは `curl` で XML/JSON が返ることを検証。
3. ユーザーの regulatory-legal CLAUDE.md の **Feed configuration → Direct regulator feeds** の下に追加(ソース名、URL、format、何をカバーするか)。
4. フィードが存在しない場合、**Sources without feeds** の下に追加し決める: manual、email、または change detection。
