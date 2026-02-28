+++
title = 'RedisをDockerで起動する方法'
description = 'DockerでRedisコンテナを起動する方法を解説。インメモリデータベースRedisのセットアップから動作確認、コンテナの停止・削除まで実践的に紹介します。'
date = 2025-10-03T07:29:31+09:00
lastmod = 2025-10-03T07:29:31+09:00
draft = false
categories = ['Engineering']
tags = ['Redis', 'Database', 'NoSQL']
+++

## 概要
この記事では、RedisをDockerで起動する方法について説明します。

## Redisとは
Redisは、オープンソースのインメモリデータ構造ストアであり、データベース、キャッシュ、メッセージブローカーとして使用されます。高速な読み書き性能を持ち、様々なデータ構造（文字列、リスト、セット、ハッシュなど）をサポートしています。

## DockerでのRedis起動

### 1. Dockerのインストール
まず、Dockerがインストールされていることを確認します。インストールされ
ていない場合は、[Dockerの公式サイト](https://www.docker.com/get-started)からインストールしてください。 

### 2. Redisコンテナの起動
以下のコマンドを実行して、Redisコンテナを起動します。


```bash
docker run --name redis -d  -p 6379:6379 redis:7.4.5-alpine
```

コンテナにログインする
```bash
docker exec -it redis sh
```

### 3. Redisの動作確認
Redisコンテナ内で以下のコマンドを実行して、Redisクライアントを起動します。  
```bash
redis-cli
```

データの設定と取得を試してみます。

```bash
set name "hoge"
get name
"hoge"
```

これで、Redisが正常に動作していることが確認できました。
### 4. Redisコンテナの停止と削除
Redisコンテナを停止するには、以下のコマンドを実行します。
```bash
docker stop redis
``` 

Redisコンテナを削除するには、以下のコマンドを実行します。
```bash
docker rm redis
```

## まとめ
この記事では、Dockerを使用してRedisを起動する方法について説明しました。
Redisは高速なデータベースとして広く利用されており、Dockerを使用することで簡単にセットアップできます。  
ぜひ、Redisを活用してみてください。
