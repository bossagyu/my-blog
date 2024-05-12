+++
title = 'グローバルなgitignoreを設定してプロジェクト全体に適応する方法'
date = 2024-04-16T23:16:25+09:00
draft = false
categories = ['Development']
tags = ['git', 'gitignore']
+++

## 概要
`.gitignore` ファイルをプロジェクトに追加することでプロジェクト事にgitのトラッキング対象から外すことができます。  
しかしながら`.idea` などIDEがデフォルトで生成するディレクトリを毎回プロジェクト毎にgitignoreに追加するのが面倒です。  
本記事では `gitignore` に設定した内容をすべてのプロジェクトに 適応する方法をまとめます。

## gitignoreを全体に適応する方法

gitはデフォルトで `~/.config/git/ignore` へignore設定を見に行きます。  
このため、`~/.config/git/ignore` にignore設定を記述することですべてのプロジェクトにgitignoreの内容を適応できます。

よく `.gitignore_global` を作成して、`core.excludesfile` に登録する方法が案内されていますが、この方法だと `.gitconfig` に無駄な設定をいれる必要があるため、こちらの方法をおすすめします。

## プロジェクト全体にgitignoreを適応する手順

ignoreファイルを格納するためのディレクトリを作成します。

```shell
mkdir -p ~/.config/git/
```

ignoreファイルを作成し、全プロジェクトで無視したい内容を記述してください。
```
vim ~/.config/git/ignore
```

記載例

```shell
.idea/
*.log
node_modules/
```

この設定を行うことで、全プロジェクトで同じignore設定を適応できます。  
すでにトラッキングしているファイルを含む場合は、一度 `git rm --cached` でトラッキングを解除してください。

## 参考
* [Git - gitignore](https://git-scm.com/docs/gitignore)
