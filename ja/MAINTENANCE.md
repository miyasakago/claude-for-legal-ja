# メンテナンス手順 — Upstream 追従

本リポジトリは [anthropics/claude-for-legal](https://github.com/anthropics/claude-for-legal) の派生物(日本語翻訳版)です。元著作物は活発に更新されるため、翻訳版を最新に保つ運用手順を本書に記します。

---

## 前提

### Git のリモート設定

本リポジトリには 2 つのリモートが設定されている必要があります:

```bash
git remote -v
# origin    https://github.com/miyasakago/claude-for-legal-ja.git (fetch)
# origin    https://github.com/miyasakago/claude-for-legal-ja.git (push)
# upstream  https://github.com/anthropics/claude-for-legal.git    (fetch)
# upstream  https://github.com/anthropics/claude-for-legal.git    (push)
```

未設定の場合:

```bash
git remote add upstream https://github.com/anthropics/claude-for-legal.git
```

---

## 定期同期の手順

### Step 1. Upstream の差分を取得

```bash
git fetch upstream
git log HEAD..upstream/main --oneline
```

新規 commit があれば内容を確認します。

### Step 2. 英語原文側に upstream をマージ

英語原文(ルート直下のファイル群)は upstream と同期し続けます。

```bash
# 通常のマージ(推奨)
git merge upstream/main

# あるいは衝突回避のため rebase でもよい
# git rebase upstream/main
```

衝突が起こる可能性は低いです(`ja/` 配下のみ追加しており、英語原文には基本的に触れていないため)。万一衝突した場合は、**英語原文側を upstream に合わせる**のが原則です。

### Step 3. 差分の特定

upstream で変更された英語ファイルを列挙します。

```bash
# 直近の同期からの差分(例: 1 週間前との比較)
git diff --name-only HEAD@{1.week.ago} HEAD -- \
  ':!ja/' ':!TRANSLATION_POLICY.md' ':!GLOSSARY.md'
```

または、リリースタグ間の差分:

```bash
git diff --name-only v1.0.0 v1.1.0 -- ':!ja/'
```

### Step 4. 翻訳の更新

差分のある英語ファイルそれぞれについて、対応する `ja/` 配下のファイルを更新します。

#### 翻訳更新時のチェックリスト

- [ ] [TRANSLATION_POLICY.md](../TRANSLATION_POLICY.md) の方針に従っているか
- [ ] [GLOSSARY.md](../GLOSSARY.md) の用語を統一しているか(新規用語があれば追加してから着手)
- [ ] インラインタグ(`[verify]`, `[review]`, `[PLACEHOLDER]` 等)を英語のまま保持しているか
- [ ] コマンドパス `/plugin:skill` を `/plugin-ja:skill` に変換しているか
- [ ] 設定パス `claude-for-legal/<plugin>` を `claude-for-legal-ja/<plugin>-ja` に変換しているか
- [ ] `plugin.json` の `name` が `<plugin>-ja` になっているか
- [ ] 米国法・EU 法の固有引用は原文維持・日本語注釈付与しているか
- [ ] 重大度絵文字(🔴🟠🟡🟢)を保持しているか

### Step 5. 新規プラグイン・スキルが追加された場合

upstream に新規プラグインや新規スキルが追加された場合は、以下を実施します:

1. 新規プラグインの場合: `ja/<plugin>/` ディレクトリを作成し、全ファイル翻訳
2. 新規プラグインの場合: `ja/.claude-plugin/marketplace.json` にエントリ追加(`<plugin>-ja` 名で)
3. 新規スキルの場合: `ja/<plugin>/skills/<skill>/SKILL.md` を翻訳
4. `ja/README.md` のスキル & コマンドリファレンス表を更新
5. `ja/NOTICE.md` の変更点リストを必要に応じて更新

### Step 6. 構造的バリデーション

翻訳更新後、以下のチェックを実行します:

```bash
# JSON 構文チェック
find ja -name "*.json" -exec python3 -c "import json,sys; json.load(open(sys.argv[1]))" {} \;

# YAML 構文チェック
find ja \( -name "*.yaml" -o -name "*.yml" \) -exec python3 -c "import yaml,sys; yaml.safe_load(open(sys.argv[1]))" {} \;

# marketplace.json と各 plugin.json の整合性
python3 -c "
import json, os
m = json.load(open('ja/.claude-plugin/marketplace.json'))
for p in m['plugins']:
    pj_path = os.path.join('ja', p['source'].lstrip('./'), '.claude-plugin/plugin.json')
    pj = json.load(open(pj_path))
    assert pj['name'] == p['name'], f'{p[\"name\"]} != {pj[\"name\"]}'
print('OK')
"

# 旧コマンドパス・旧設定パスが残っていないか
grep -rE '/(commercial|privacy|product|corporate|employment|regulatory|ai-governance|litigation|law-student|legal-clinic|legal-builder-hub|ip-legal|cocounsel-legal):' ja/ --include='*.md' | grep -v '\-ja:' || echo "OK: no leaked English commands"

grep -r 'plugins/config/claude-for-legal/' ja/ 2>/dev/null | grep -v 'claude-for-legal-ja' || echo "OK: no leaked old config paths"
```

### Step 7. コミット & プッシュ

```bash
git add ja/
git commit -m "Upstream 同期: <要約>(upstream commits <SHA1>..<SHA2>)"
git push origin main
```

---

## 同期のタイミング

- **強く推奨:** upstream の release tag が更新されたら同期
- **推奨:** 週次〜月次で `git fetch upstream && git log HEAD..upstream/main --oneline` をチェック
- **緊急:** セキュリティ修正・破壊的変更があった場合は即座に同期

---

## 翻訳の保留・スキップ判断

upstream で以下のような変更があった場合は、翻訳更新を**保留**してもよい:

- 軽微なタイポ修正
- 英語固有の表現の微調整(日本語訳が既に適切なら)
- 廃止予定スキルの最終調整

逆に**必ず追従**すべき変更:

- 新規プラグイン・新規スキルの追加
- ガードレールの追加・変更(CLAUDE.md の Shared guardrails セクション)
- セキュリティ関連の変更
- ライセンス・商標関連の表記変更
- インストール手順・コマンドパスの変更

---

## トラブルシューティング

### Q. upstream の英語ファイルが大きく変わって、翻訳の整合性が崩れた

A. 該当ファイルを再翻訳します。元の翻訳に意味的な誤りがなく、差分が軽微なら部分的に更新。大幅変更なら全文書き直しの方が早い場合もあります。

### Q. upstream にマージ衝突が起きた

A. 原則として **英語原文側を upstream に合わせます**。`ja/` 配下のファイルは独立に管理されているため、衝突が `ja/` で起こることは稀です。万一起きた場合は、自分の翻訳を優先します。

### Q. upstream で構造変更(ディレクトリ移動など)があった

A. `ja/` 配下にも同じ構造変更を適用します。`git mv ja/old/path ja/new/path` を実行。

### Q. upstream の Git ヒストリーが書き換えられた(force push)

A. 通常のオープンソースリポジトリでは稀ですが、もし発生した場合は `git reset upstream/main` で同期し直し、`ja/` 配下を手動で復元します。

---

## メンテナの責務(Apache 2.0 / NOTICE 観点)

- [NOTICE.md](NOTICE.md) の「加えた変更点」セクションを、構造的変更があれば更新
- [TRANSLATION_POLICY.md](../TRANSLATION_POLICY.md) と [GLOSSARY.md](../GLOSSARY.md) の改訂は別 commit で
- 翻訳の commit メッセージには **upstream の commit SHA** または **release tag** を含める
  (例: `commercial-legal: termination clauses を更新(upstream 1a2b3c4 同期)`)
- 商標・ライセンスに関する upstream の変更は最優先で追従

---

*本ドキュメント自体も翻訳作業の運用変更に応じて更新してください。*
