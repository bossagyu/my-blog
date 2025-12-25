+++
title = 'Codex CLIの使い方まとめ'
date = 2025-12-12T10:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['Codex', 'CLI', 'AI', 'Tools']
+++

## 概要
Codex CLIを使ってローカル環境からコマンドライン経由でCodexにアクセスする手順をまとめました。
インストールからサインイン、代表的なコマンドの使い方まで一通り確認できます。

## Codex CLIのインストール方法
Node.jsが入っている環境ならnpm経由で簡単に導入できます。

```bash
npm install -g codex-cli
```

Homebrewを利用している場合は以下でもOKです。

```bash
brew tap codexcli/tap
brew install codex
```

インストール後、バージョンが表示されれば準備完了です。

```bash
codex --version
```

## サインインの方法
Codex CLIは初回実行時に認証が必要です。用途に応じてAPIキーかChatGPTアカウントでサインインできます。

### APIキーでサインインする
```bash
codex login --token <YOUR_API_KEY>
```

環境変数に設定しておくと毎回入力する手間を省けます。

```bash
export CODEX_API_KEY=<YOUR_API_KEY>
```

### ChatGPTのアカウントでサインインする
ブラウザでの認証フローを利用したい場合は、トークン指定を省いて `codex login` を実行します。

```bash
codex login
```

上記を実行するとブラウザが開き、ChatGPTのサインイン画面が表示されます。メールアドレスやGoogle/Appleアカウントでサインインし、許可を与えるとCLIに戻ってトークンが保存されます。

サインイン後は共通で以下を実行し、アカウント情報が表示されれば認証成功です。

```bash
codex whoami
```

## Codexの実行方法とサンプル
### 単発でコード生成を行う

```bash
codex run "PythonでFizzBuzzを書いて"
```

### ファイルをコンテキストに含めて要約する

```bash
codex run "このファイルを要約して" --file README.md
```

### 対話モードで試す

```bash
codex chat
```

チャットモードでは、プロンプトを入力すると逐次回答が返ってきます。`Ctrl + D` で終了できます。

#### チャット内のスラッシュコマンド
対話モードでは先頭に `/` を付けて補助操作を行えます。

| コマンド | 役割 |
| --- | --- |
| `/help` | 利用可能なコマンド一覧を表示する |
| `/new` | 会話履歴をリセットして新しいスレッドを開始する |
| `/file <path>` | 指定したファイルをコンテキストに添付する |
| `/exit` | チャットを終了する |

## コマンドの使い方
主なコマンドは以下の通りです。

| コマンド | 説明 |
| --- | --- |
| `codex login --token <API_KEY>` | APIキーでサインインする |
| `codex login` | ChatGPTのアカウントでブラウザ認証を開始する |
| `codex whoami` | 現在サインインしているアカウントを確認する |
| `codex run "<prompt>"` | 単発のプロンプトを投げて結果を取得する |
| `codex run "<prompt>" --file <path>` | ファイルをコンテキストに含めて実行する |
| `codex chat` | 対話モードで利用する |
| `codex history` | 直近の実行履歴を確認する |

## まとめ
Codex CLIはインストール後にサインインを済ませるだけで、コマンドラインからすぐにCodexを活用できます。
よく使うコマンドを覚えておくと、スクリプト生成やファイル要約が素早く行えるようになります。
