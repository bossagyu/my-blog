+++
title = 'Hugo + Netlify + Githubでブログを公開する'
description = 'HugoとNetlify、Githubを使ってブログを公開する手順を解説。静的サイト生成からGitHub連携、Netlifyデプロイ、Favicon設定まで初心者向けに説明します。'
date = 2023-12-02T00:59:37+09:00
draft = false
categories = ['Engineering']
tags = ['Hugo', 'Netlify', 'Github', 'Favicon']
aliases = ['/blog/010-favicon/']
+++
## 概要
Hugoで作ったサイトをGithubで管理、Netlifyでビルドした手順を0から作れるよう記載します。
この方式にすると手元でMarkDownで書いたブログをGithubにPushするだけで簡単に公開できるようになります。

Faviconの設定方法も合わせて解説します。

## 流れ
1. Hugoでサイトを生成
2. Githubにプッシュ
3. Netlifyでデプロイ
4. Faviconを設定

## Hugoで静的サイトを生成
まずはHugoをインストールします。
```shell
brew install hugo
```

blogの雛形を作成します。
```shell
hugo new site my-blog
```

ブログに適応するテーマをsubmoduleとして追加します。
```shell
cd my-blog
git init

# テーマをgithubのsubmoduleとして追加
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

hugo.tomlに記載することでテーマを適応する。
```shell
echo "theme = 'ananke'" >> config.toml
```

サーバを起動させます。
```shell
hugo server
```

起動ログの `Web Server is available at http://localhost:51517/ (bind address 127.0.0.1)` のような記述の `http://localhost:51517/` にアクセスすればローカルに起動した静的サイトが閲覧できます。

###  Tips
* Hugoのテーマを変えたい場合は、[Hugo Themas](https://themes.gohugo.io/) から好きなものを選んで変えてください。
  * これは後からでも変えられるのでとりあえずNetlifyでビルドするところまで走り切るのがおすすめ。
* Tomlファイルの書き方は [Configure Hugo](https://gohugo.io/getting-started/configuration/) に記載されています。 

<br>

## Githubにpush

[Github](https://github.com/) にリポジトリを作成。<br>
作成後以下のコマンドを実行し、サイトをpushします。
```shell
cd my-blog
echo .hugo_build.lock >> .gitignore
git add .
git commit -m "first commit"
git branch -M main

# <user name>は自分のユーザー名に置き換えてください。
# 今回はmy-blogというリポジトリを作成している例です。
git remote add origin git@github.com:<user name>/my-blog
git push -u origin main
```

pushが完了するとGithubのUI上でソースコードが閲覧できる状態になっています。


## Netlifyでデプロイ
[netlify](https://www.netlify.com/) へアクセスし、デプロイを行う。  
[Hugoの公式で案内](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/) があるのでこちらを参考に連携を行う。

指示に従いデプロイを完了すると以下のようにDeployの結果が `published` になる。

![デプロイの実行結果画面](img-001-001.png)


サイト上に表示されたURLをクリックするとデプロイされたサイトにアクセスできる。
![リンク表示画面](img-001-002.png)


これでデプロイまではおしまい。
以降は変更を加えてmainにpushするだけで自動デプロイが走り、サイトの内容が更新されるようになる。

## Faviconを設定する

Faviconとは、ウェブサイトのブックマークやタブ、ホーム画面などに表示されるアイコンのことです。
Googleより検索結果に表示されるための[faviconのガイドライン](https://developers.google.com/search/docs/appearance/favicon-in-search?hl=ja#guidelines)が公開されており、
こちらに従うことで検索結果にも表示されるようになります。

### Faviconの作成

Faviconを作成するには、以下のサイトを利用します。

* [Favicon.ico & App Icon Generator](https://www.favicon-generator.org/)

サイトにアクセスし、faviconにしたい画像をアップロードして「Generate Favicon」をクリックします。
その後表示される画面で、「Download the generated favicon」のリンクをクリックすると、faviconがダウンロードできます。

### HugoでFaviconを表示する

HugoでFaviconを表示するには、themeによっても違いますが、多くのテーマでは以下のようにtomlに設定するだけでfaviconが表示できます。

```toml
[params]
  favicon = "images/favicon.ico"
```

ダウンロードしたfavicon.icoを`static/images/`ディレクトリに配置して、上記の設定を追加してください。

## まとめ

Hugo + Netlify + Githubでブログを公開する方法を解説しました。

1. **Hugo**: 静的サイトを生成
2. **Github**: ソースコードを管理
3. **Netlify**: 自動デプロイ
4. **Favicon**: ブランディングのための設定

この構成なら、Markdownで記事を書いてGithubにpushするだけで自動的にサイトが更新されます。