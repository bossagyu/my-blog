+++
title = 'macでGoの開発環境を構築して最速でHello Worldする'
date = 2024-09-15T16:52:04+09:00
draft = false
categories = ['Engineering']
tags = ['go']
+++

## 概要

MacでGo言語の開発環境を構築して、最速で Hello World する方法を紹介します。

## Go言語のインストール

brewを使ってGo言語をインストールします。

```shell
> brew install go
```

バージョンを確認

```shell
> go version
go version go1.21.3 darwin/arm64
```

## Hello World の実行

以下のコードを `main.go` として保存します。

```go
package main

import "fmt"

func main() {
  fmt.Printf("Hello World\n")
}
```

実行する

```shell
> go run hello.go
Hello World
```

バイナリをビルドして実行する

```shell
> go build hello.go

> ls
hello*    hello.go

> ./hello
Hello World
```

## まとめ

以上で、MacでGo言語の開発環境を構築して、最速で Hello World する方法を紹介しました。
