# CLAUDE.md

このファイルは、Claude Code（claude.ai/code）がこのリポジトリで作業する際のガイダンスを提供します。

## プロジェクト概要

Hugo静的サイトジェネレーターによるブログ（日本語/英語バイリンガル対応）。テーマ: `hugo-theme-stack`（`themes/`内のgit submodule）。サイト: https://bossagyu.com

## コマンド

```bash
# ローカル開発サーバー
hugo server

# 本番用ビルド
hugo --minify

# 下書きを含めてビルド
hugo server -D

# 新規記事の作成
hugo new blog/NNN-slug-name/index.md
```

npm/yarn/package.jsonはなし — 純粋なHugoプロジェクトです。

## コンテンツ構造

記事は `content/blog/NNN-topic-name/` に連番（001〜046+）で配置されます。各ディレクトリには以下を含みます:
- `index.md` — 日本語記事（メイン）
- `index.en.md` — 英語翻訳（すべての記事に必須）

## 記事のフロントマター

TOML形式で `+++` 区切りを使用:

```toml
+++
title = "日本語タイトル"
date = 2026-02-07T00:00:00+09:00
draft = false
categories = ["Engineering"]  # Engineering | Management | Blog
tags = ["tool-name", "topic"]  # 英語で記載、既存のタグを参照
+++
```

## 執筆ガイドライン（AGENTS.mdより）

- 日本語で執筆、です・ます調を使用
- 本文は `## 概要` から始め、`##`/`###` の見出しを使用
- コードブロックには言語指定子を付ける
- 日本語記事と対応する英語記事（`index.en.md`）を必ず作成する
- コンテンツの焦点: 実践的なエンジニアリングチュートリアル、ツールセットアップガイド、マネジメント/スクラム関連トピック

## 設定

Hugo設定は `config/_default/` に分割:
- `config.toml` — ベースURL、言語設定（日本語がデフォルト、en-US）、テーマ、著者
- `params.toml` — サイドバー、ウィジェット、画像処理、コメント（無効）
- `markup.toml` — コードハイライト、目次設定
- `menu.toml` — ソーシャルリンク

## デプロイ

- **GitHub Pages**（メイン）: `main`へのプッシュでGitHub Actionsが実行、Hugo 0.119.0 extended、`gh-pages`ブランチにデプロイ
- **Netlify**（サブ）: Hugo 0.120.3、`public/`を公開

## Gitワークフロー

- `main`に直接コミットしない — 必ずフィーチャーブランチを作成する
- 変更後、コミットしてPRを作成する
- テーマはgit submodule — `git clone --recurse-submodules`または`git submodule update --init`を使用
