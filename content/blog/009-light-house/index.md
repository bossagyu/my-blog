+++
title = 'Lighthouseの使い方の紹介'
date = 2023-12-22T23:08:00+09:00
draft = false
+++

## 概要
Lighthouseを用いて、ブログのパフォーマンスを計測する方法を解説します。

## Lighthouseとは
LighthouseはGoogleが提供している、Webサイトのパフォーマンスを計測するツールです。
Google Chromeの拡張機能として提供されており、プラグインをインストールすることで利用することができます。

## Lighthouseのインストール
Lighthouseをchromeウェブストアからインストールします。  
![Lighthouse chromeストア](/blog/img-009-001.png)

分析したいサイトを開き、Lighthouseのアイコンをクリックします。  
![Lighthouse アイコン](/blog/img-009-002.png)

Generate reportをクリックすると、分析が始まります。  
今回は私の[ブログのページ](https://bossagyu.com/blog/001-hugo-netlify-build/)で実行しました。  
![Lighthouse 実行画面](/blog/img-009-003.png)

実行すると、以下のような結果が表示されます。  
実行の完了までに約1分くらい時間がかかります。  
![Lighthouse 結果](/blog/img-009-004.png)

## 結果の見方
### Performance
ページの読み込み速度や画像の表示速度など、webサイトのパフォーマンスが評価される。
See calculatorのリンクをクリックすると詳細に飛べる。  
![Lighthouse Performance](/blog/img-009-005.png)

### Accessibility
すべてのユーザーがコンテンツにアクセス、サイト内を効率的に移動できるかどうかを確認する。
スクロールすると、Accessibilityで指摘されている箇所が表示される。  
![Lighthouse Accessibility](/blog/img-009-006.png)

コードスニペットを貼っているところの色のコントラストが弱いことと、リンクに説明がないことを指摘されています。

ただ、指摘されている内容は自分の記述ではなく、テンプレートに依存している部分なので、これを直そうと思うとHugoのテンプレートをオーバーライドする必要がありますね。。

### Best Practices
ウェブページの健全性についてテストを行います。
検証項目については結果から閲覧できます。
![Lighthouse Best Practices](/blog/img-009-007.png)

### SEO
ページが検索エンジンの結果ランキング向けに最適化されているかを確認できます。
![Lighthouse SEO](/blog/img-009-008.png)

### Progressive Web App
スマートフォン上のウェブページの読み込み速度を高速化できているか、PWAに最適化できているかを確認できます。
今回はチェックしてません。

## まとめ
Lighthouseを用いて、ブログのパフォーマンスを計測する方法を解説しました。
特にSEOについては、Googleの検索結果に表示されるかどうかに影響するので、しっかりと対応しておきましょう。


