+++
title = 'Github CopilotをChat toolを使って便利に使う方法'
date = 2025-10-07T08:39:41+09:00
draft = true
categories = ['Engineering']
tags = ['Github Copilot', 'VScode', 'Chat tool']
+++

## 概要
この記事では、Github Copilot Chatをより便利に使うためのChat toolを使う方法について説明します。
Chat toolを利用することで、Copilotとのチャットの中で様々なタスクを実行できるようになります。

## Chat toolとは
Chat toolとはGithub Copilotのチャット内で特殊なタスクを実行できる機能です。
例えば、ターミナルからの出力を取得したり、URLから情報を取得したりすることができます。

## Chat toolの実行方法
今回はVSCodeでChat toolを利用する方法を説明します。

基本的には `#<command> <args>` の形式でコマンドを入力します。

![Chat toolの実行例](image.png)

これだけです。

## 代表的なChat toolのコマンド
以下に代表的なChat toolのコマンドを紹介します。

| コマンド | 機能 |
|---------|------|
| `#codebase` | 現在のワークスペースを全部検索する |
| `#selection` | 現在のエディタで選択しているコードをコンテキストとしてプロンプトに追加する |
| `#terminal_selection` | 現在のターミナルで選択している部分をコンテキストとしてプロンプトに追加する。<br>エラーが出たときにターミナルの出力を参照するのに便利です。 |
| `#fetch_webpage` | URLを指定してwebページからコンテンツを取得してコンテキストとしてプロンプトに追加する |

他にもたくさん便利な使い方があるので、公式ドキュメントを参照してください。
- [Github Copilot Chat tools](https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features#_chat-tools)

## まとめ
この記事では、Github Copilot ChatでChat toolを使う方法について説明しました。
Chat toolを使うことで、より便利にCopilotを利用できるようになるので、ぜひ活用してみてください。