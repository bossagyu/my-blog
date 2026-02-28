+++
title = 'Voltaを利用してTypeScriptの開発環境を簡単にセットアップする方法'
description = 'VoltaでNode.jsとTypeScriptの開発環境をMacにセットアップする方法を解説。yarnのインストールからプロジェクト作成、Hello World実行までの手順を紹介します。'
date = 2024-03-10T13:11:13+09:00
lastmod = 2024-03-10T13:11:13+09:00
draft = false
categories = ['Engineering']
tags = ['TypeScript', 'Node.js']
+++

## 概要
この記事では、TypeScriptの開発環境を簡単にセットアップする方法について説明します。
本記事ではMacOSを対象にしています。

## Voltaとは

VoltaはNode.jsのバージョン管理ツールです。  
[Voltaの公式サイト](https://volta.sh/) で紹介されている通り以下の特徴を備えています。

* 高速
  * Rustで構築されており、Node.jsのバージョン切り替えが高速です。
* 信頼できる
  * プロジェクトの全員が同じツールを利用可能
* ユニバーサル
  * パッケージマネージャー、ノードランタイム、OSに依存なく利用可能。

今まではnodebrewなどを利用することが、一般的でしたが、現在はVoltaを利用するケースが増えている印象です。

## VoltaとNode.jsのインストール

voltaのインストールは以下のコマンドだけで完了です。

```shell
curl https://get.volta.sh | bash
```

パスが通っていないことがあるのでzshを利用している方は以下のコマンドでパスを通してください。

```shell
echo 'VOLTA_HOME=$HOME/.volta' >> ~/.zshrc
echo 'export PATH=$VOLTA_HOME/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
```

動作を確認。バージョンが表示されれば問題なくインストールができています。

```shell
volta -v
```

voltaを利用してNode.jsをインストールします。  
バージョンの指定をしない場合最新のLTSがインストールされます。

```shell
volta install node
```

## yarnをインストールしてTypeScriptのプロジェクトを作成

### npmとyarnの違い

npm, yarnともにNode.jsのパッケージマネージャーとなります。  
それぞれの特徴は以下の通りです。

npm

* Node.jsがリリースされた翌年（2010年）リリース
* Node Package Managerの略
* package-lock.jsonファイルを自動的に生成する
* Node.jsをインストールすれば自動的にインストールされる

yarn

* 2016年リリース
* Facebook、Google、Exponent、Tildeによって開発された新しいJavaScriptパッケージマネージャー
* npｍと互換性がある
    * 同じpackage.jsonが使える
* npmより厳密にモジュールのバージョンを固定できる
* npmよりインストールが速い

yarnの方が優れているように見えますが、最近ではnpmがアップデートされて機能の差はあまりないようです。  
今回はyarnを利用してTypeScriptのプロジェクトを作成します。

### yarnのインストール

voltaを利用してyarnをインストールします。

```shell
volta install yarn
```

インストールされているかを確認します。  
listの結果にyarnが表示されれば問題なくインストールされています。

```shell
volta list
```

### TypeScriptのプロジェクトを作成

yarnの初期化

```shell
yarn init -y
```

Node.jsのインストール

```shell
volta pin node@20.0.0
```

TypeScriptのインストール

```shell
yarn add typescript
```

node-tsのインストール

```shell 
yarn add --dev ts-node
```

tsconfig.jsonを作成します。  
tsconfig.jsonはTypeScriptの設定ファイルで、コンパイル時の設定を記述します。  
今回は、console.logを利用するため、今回はtargetを `es2016` に設定します。特にデフォルトで生成されるものから変更する必要はありません。


```shell
yarn tsc --init
```

サンプルプログラムを実行してみる。

```shell
echo "console.log('Hello, TypeScript!');" > hello.ts
yarn ts-node hello.ts

# 以下のように表示されれば成功
Hello, TypeScript!
```

無事にテストスクリプトが動きました。  
以上でTypeScriptの開発環境のセットアップが完了です。

## まとめ
本記事では、Voltaを利用してTypeScriptの開発環境を簡単にセットアップする方法について説明しました。
Voltaを利用することで、Node.jsのバージョン管理が簡単になり、開発環境のセットアップがスムーズに行えます。
また、VoltaでNode.jsのバージョンを指定すると、package.jsonにバージョンが記述され、他の開発者とのバージョンの差異を解消することができる点も魅力的ですね。

## 関連記事

- [TyeScriptにおけるEnumの使い方](/blog/022-typescript-enum/)（TypeScript Enumの使い方）

