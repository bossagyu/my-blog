+++
title = 'VSCodeでGithub Copilotを設定して使う方法'
date = 2024-02-04T22:34:51+09:00
draft = false
categories = ['Engineering']
tags = ['VSCode', 'Copilot']
+++

## 概要
この記事では、VSCodeでGithub Copilotを設定して使う方法を説明します。
前提としてGithub Copilotのアカウントが必要です。

## VSCodeでGithub Copilotを使えるようにするまで
### 拡張機能をインストール
まずは、VSCodeに拡張機能をインストールします。
VSCode を開き、左メニューの四角形が4つあるアイコンをクリックし、検索用テキスト入力に「copilot」と入力します。
「install」をクリックし、インストールを開始してください。

![拡張機能](img-017-001.png)

### GitHubとの連携
installをクリックし、installが完了すれば以下のような画面が表示されるので、「Sign in to GitHub」をクリックします。

![Sing in to GitHubの画面](img-017-002.png)

GitHubのアカウントへのアクセスを要求されるので「Allow」で許可します。

![アクセス許可確認画面](img-017-003.png)

「Authorize Visual Studio Code」をクリックし、許可します。

![確認画面](img-017-004.png)

これでGithub CopilotとVSCodeの連携が完了し、使えるようになりました。

## 使い方
基本的には、コードを書いていくだけで自動的に保管されるようになります。
保管内容が提案されるので以下のコマンドを使いながらコードを書いていくと良いでしょう。

### チートシート
| 機能                            | キー                  |
|---------------------------------|----------------------|
| 提案を受け入れる                 | Tab                  |
| 提案を拒否する                   | Esc                  |
| Copilotを開く                    | Ctrl + Enter         |
| 次の提案                         | Alt/Option + ]       |
| 前の提案                         | Alt/Option + [       |
| インラインCopilotをトリガーする   | Alt/Option + \\      |

## まとめ
VSCodeでGithub Copilotを設定して使う方法について説明しました。  
また、Github Copilotはソースコードだけではなく文章にも保管を行ってくれます。

このブログもGithub Copilotで保管を行いながら書いており、かなり効率化できています。  
ぜひ使ってみてください。


