# コンテンツ戦略 Phase 1: 基盤整備 実装計画

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 既存49記事の内部リンク整備、lastmod追加、記事テンプレート標準化により、サイト内回遊とSEO基盤を強化する

**Architecture:** 既存記事のフロントマターとコンテンツを更新。Hugo archetypeテンプレートを標準化。内部リンクはクラスター単位で追加。

**Tech Stack:** Hugo, Markdown, TOML frontmatter

---

### Task 1: 記事アーキタイプの標準化

**Files:**
- Modify: `archetypes/default.md`

**Step 1: アーキタイプを更新**

`archetypes/default.md` を以下に置き換える:

```toml
+++
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
description = ''
date = {{ .Date }}
draft = true
categories = ['Engineering']
tags = []
+++

## 概要

## 前提条件

## 手順

## 動作確認

## まとめ
```

**Step 2: hugo new で確認**

Run: `hugo new blog/999-test-archetype/index.md`
Expected: 新しいテンプレートで記事が作成される

確認後、テスト記事を削除:
Run: `rm -rf content/blog/999-test-archetype`

**Step 3: コミット**

```bash
git add archetypes/default.md
git commit -m "chore: 記事アーキタイプを標準テンプレートに更新"
```

---

### Task 2: 全記事に lastmod フロントマターを追加

**Files:**
- Modify: 全 `content/blog/*/index.md`（日本語）
- Modify: 全 `content/blog/*/index.en.md`（英語）

各記事の `date` と同じ値で `lastmod` を追加する。リライト時に `lastmod` を更新日に書き換える運用にする。

**Step 1: 日本語記事に lastmod を追加するスクリプトを実行**

以下のスクリプトで、`date = ` の次の行に `lastmod = ` を挿入する（dateと同じ値）:

```bash
for f in content/blog/*/index.md; do
  if ! grep -q "^lastmod" "$f"; then
    date_val=$(grep "^date = " "$f" | head -1 | sed 's/^date = //')
    if [ -n "$date_val" ]; then
      sed -i '' "s/^date = ${date_val}/date = ${date_val}\nlastmod = ${date_val}/" "$f"
    fi
  fi
done
```

**Step 2: 英語記事にも同様に追加**

```bash
for f in content/blog/*/index.en.md; do
  if [ -f "$f" ] && ! grep -q "^lastmod" "$f"; then
    date_val=$(grep "^date = " "$f" | head -1 | sed 's/^date = //')
    if [ -n "$date_val" ]; then
      sed -i '' "s/^date = ${date_val}/date = ${date_val}\nlastmod = ${date_val}/" "$f"
    fi
  fi
done
```

**Step 3: 確認**

Run: `grep -l "^lastmod" content/blog/*/index.md | wc -l`
Expected: 49前後（全記事数と一致）

Run: `grep -l "^lastmod" content/blog/*/index.en.md | wc -l`
Expected: 全英語記事数と一致

**Step 4: コミット**

```bash
git add content/blog/*/index.md content/blog/*/index.en.md
git commit -m "chore: 全記事にlastmodフロントマターを追加"
```

---

### Task 3: Hugo Blog基盤クラスターの内部リンク追加

**対象記事:** 001, 003, 007, 009, 011, 012

001をハブ記事とし、各記事の「まとめ」セクション末尾に関連記事リンクを追加する。

**Step 1: 001-hugo-netlify-build/index.md のまとめに追加**

記事末尾（まとめセクション）に以下を追加:

```markdown
## 関連記事

- [HugoでGoogle Analyticsの設定をする方法](/blog/003-google-analytics/) - アクセス解析の導入
- [Google Search Consoleでブログを検索対象にする方法](/blog/007-google-search-console/) - SEO対策
- [Lighthouseの使い方の紹介](/blog/009-light-house/) - パフォーマンス計測
- [ChatGPTを利用してHugoで作ったブログを多言語対応する方法](/blog/011-hugo-multilingul-support/) - 多言語対応
- [Twitter Social Cardの設定方法](/blog/012-social-card/) - SNS共有の最適化
```

**Step 2: 003, 007, 009, 011, 012 の各記事に追加**

各記事のまとめセクション末尾に以下パターンで追加:

003 → 001, 007 への参照
007 → 001, 003, 009 への参照
009 → 001, 003 への参照
011 → 001 への参照
012 → 001, 007 への参照

**Step 3: 英語版にも対応する内部リンクを追加**

各記事の `index.en.md` にも同様のリンクを英語で追加。

**Step 4: ビルド確認**

Run: `hugo --minify 2>&1 | tail -5`
Expected: エラーなし

**Step 5: コミット**

```bash
git add content/blog/001-*/index.md content/blog/001-*/index.en.md
git add content/blog/003-*/index.md content/blog/003-*/index.en.md
git add content/blog/007-*/index.md content/blog/007-*/index.en.md
git add content/blog/009-*/index.md content/blog/009-*/index.en.md
git add content/blog/011-*/index.md content/blog/011-*/index.en.md
git add content/blog/012-*/index.md content/blog/012-*/index.en.md
git commit -m "feat: Hugo基盤クラスターの記事間に内部リンクを追加"
```

---

### Task 4: AWSサーバーレスクラスターの内部リンク追加

**対象記事:** 006, 008, 014, 015, 046

046をキャップストーン記事として、各記事から相互リンクする。

**Step 1: 各記事のまとめセクション末尾に関連記事を追加**

006 → 014, 046
008 → 014, 046
014 → 006, 008, 046
015 → 046
046 → 002, 008, 014, 015（既存リンクと重複しないよう確認）

**Step 2: 英語版にも追加**

**Step 3: ビルド確認**

Run: `hugo --minify 2>&1 | tail -5`

**Step 4: コミット**

```bash
git add content/blog/006-*/index.md content/blog/006-*/index.en.md
git add content/blog/008-*/index.md content/blog/008-*/index.en.md
git add content/blog/014-*/index.md content/blog/014-*/index.en.md
git add content/blog/015-*/index.md content/blog/015-*/index.en.md
git add content/blog/046-*/index.md content/blog/046-*/index.en.md
git commit -m "feat: AWSサーバーレスクラスターの記事間に内部リンクを追加"
```

---

### Task 5: GitHub Copilot/AIツールクラスターの内部リンク追加

**対象記事:** 005, 017, 035, 042, 043

017をハブ記事とし、IDE別・機能別にリンクする。

**Step 1: 各記事にリンク追加**

005 → 017, 042
017 → 005, 035, 042, 043
035 → 017, 042
042 → 017, 035, 043
043 → 017, 042, 044

**Step 2: 英語版にも追加**

**Step 3: ビルド確認・コミット**

```bash
git add content/blog/005-*/index.md content/blog/005-*/index.en.md
git add content/blog/017-*/index.md content/blog/017-*/index.en.md
git add content/blog/035-*/index.md content/blog/035-*/index.en.md
git add content/blog/042-*/index.md content/blog/042-*/index.en.md
git add content/blog/043-*/index.md content/blog/043-*/index.en.md
git commit -m "feat: Copilot/AIツールクラスターの記事間に内部リンクを追加"
```

---

### Task 6: Python開発クラスターの内部リンク追加

**対象記事:** 004, 015, 024, 031, 032, 033, 044, 046, 048

004と032を環境構築のハブとし、各プロジェクト記事からリンクする。

**Step 1: 各記事にリンク追加**

004 → 032（モダンな代替手段として紹介）
032 → 004（従来手法として比較参照）
015, 024, 031, 033, 044, 048 → 004 or 032（前提条件として）
031 → 033（Feast関連）
044 → 048（API統合パターン）

**Step 2: 英語版にも追加**

**Step 3: ビルド確認・コミット**

```bash
git add content/blog/004-*/index.md content/blog/004-*/index.en.md
git add content/blog/015-*/index.md content/blog/015-*/index.en.md
git add content/blog/024-*/index.md content/blog/024-*/index.en.md
git add content/blog/031-*/index.md content/blog/031-*/index.en.md
git add content/blog/032-*/index.md content/blog/032-*/index.en.md
git add content/blog/033-*/index.md content/blog/033-*/index.en.md
git add content/blog/044-*/index.md content/blog/044-*/index.en.md
git add content/blog/048-*/index.md content/blog/048-*/index.en.md
git commit -m "feat: Python開発クラスターの記事間に内部リンクを追加"
```

---

### Task 7: 残りのクラスター（Redis, Go, TypeScript, ITIL, LINE BOT, ChatGPT/AI）の内部リンク追加

**Step 1: Redis (040 ↔ 041)**

040 → 041, 041 → 040 + 030

**Step 2: Go (029, 030, 041)**

030 → 029, 041
029 → 030

**Step 3: TypeScript (021, 022)**

021 → 022, 022 → 021

**Step 4: ITIL (016, 018, 020)**

各記事に「関連ITIL v4記事」セクションで相互リンク

**Step 5: LINE BOT (002, 045, 046, 047)**

002 → 045（実践例として）
045, 047 → 046（既存リンクと重複確認）

**Step 6: ChatGPT/AI (023, 027, 037, 044)**

027 → 023, 044
023 → 027
037 → 027, 044
044 → 027, 037

**Step 7: 全英語版にも追加**

**Step 8: ビルド確認・コミット**

```bash
git add content/blog/*/index.md content/blog/*/index.en.md
git commit -m "feat: 残りクラスター（Redis, Go, TS, ITIL, LINE, AI）の内部リンクを追加"
```

---

### Task 8: 最終確認とビルド

**Step 1: 全体ビルド**

Run: `hugo --minify`
Expected: エラーなし

**Step 2: ローカルサーバーで確認**

Run: `hugo server`
Expected: リンク切れなし、各記事の関連記事セクションが表示される

**Step 3: 内部リンクの総数を確認**

Run: `grep -r "](/blog/" content/blog/*/index.md | wc -l`
Expected: 大幅に増加（現在7件 → 100件以上）
