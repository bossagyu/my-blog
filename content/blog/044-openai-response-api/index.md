+++
title = 'OpenAI Response APIの使い方まとめ'
description = 'OpenAI Response APIの基本的な使い方を解説。APIキー取得からPythonでのテキスト生成、ツール呼び出しまで、最小限のコードで始められます。'
date = 2025-12-24T10:00:00+09:00
lastmod = 2025-12-24T10:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['OpenAI', 'ResponseAPI', 'Python', 'API']
+++

## 概要
OpenAIのResponse APIを使ってテキスト生成を行う手順をまとめました。
公式ドキュメントの場所、APIキーの取得方法、Pythonからの最小サンプルまでを確認できます。

## Response APIとは
Response APIは、テキスト生成やツール呼び出しなどを統一的に扱えるOpenAIのAPIです。
単発の問い合わせから、会話形式のやり取りまで幅広く利用できます。

## 公式ドキュメント
最新の仕様は公式ドキュメントを参照するのが確実です。

- Response API リファレンス: <https://platform.openai.com/docs/api-reference/responses>

## APIキーの取得方法
Response APIを利用するにはOpenAIのAPIキーが必要です。
以下の手順で取得できます。

1. OpenAIのダッシュボードにサインインする
2. API Keysページ（<https://platform.openai.com/api-keys>）を開く
3. 「Create new secret key」から新しいキーを発行する

取得したキーは環境変数に設定しておくと安全です。

```bash
export OPENAI_API_KEY="<YOUR_API_KEY>"
```

## Pythonのサンプルコード
Python SDKを使う場合は、まずuvでパッケージをインストールします。

```bash
uv pip install openai
```

以下はResponse APIでテキストを生成する最小サンプルです。

```python
from openai import OpenAI

client = OpenAI()

response = client.responses.create(
    model="gpt-4.1-mini",
    input="Response APIの特徴を3行で教えてください。",
)

print(response.output_text)
```

### 実行イメージ

```text
Response APIは単発の生成にも対話にも使える統一APIです。
シンプルなリクエストでモデル出力を取得できます。
公式ドキュメントを確認しながら用途に合わせて拡張できます。
```

## まとめ
Response APIはシンプルな入力でテキスト生成を行えるOpenAIのAPIです。
公式ドキュメントを確認し、APIキーを準備してからPythonで試すとスムーズに導入できます。

## 関連記事

- [ChatGPT 4oの紹介](/blog/027-chatgpt-4o/)（ChatGPTの最新機能紹介）
- [GCSとGemini File Search APIを自動同期するイベント駆動バックエンドの設計](/blog/048-lawve-backend/)（API統合パターンの実践例）
