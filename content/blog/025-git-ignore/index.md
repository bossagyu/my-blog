+++
title = 'gitignoreを全体に適応する方法'
date = 2024-04-16T23:16:25+09:00
draft = false
categories = ['Development']
tags = ['git', 'gitignore']
+++

## 概要
`.idea` などIDEがデフォルトで生成するディレクトリを毎回プロジェクト毎にgitignoreに追加するのが面倒なので、全体に適応する方法をまとめます。

## gitignoreを全体に適応する方法

`.gitignore_global` を作成して、`core.excludesfile` に登録する方法がよく案内されていますが、gitはデフォルトで `~/.config/git/ignore` へignore設定を見に行きます。  
このため、`~/.config/git/ignore` にignore設定を記述することで全体に適応できます。

ディレクトリを作成

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

これだけでOKです。  
`.gitignore_global` を作成する方法だと `.gitconfig` に無駄な設定をいれる必要があるので、こちらのほうがおすすめです。

## 参考
* [Git - gitignore](https://git-scm.com/docs/gitignore)
