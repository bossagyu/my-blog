+++
title = 'RedisをGoで使う方法'
date = 2025-10-05T20:25:02+09:00
draft = false
categories = ['Engineering']
tags = ['Redis', 'Go', 'Database', 'NoSQL']
+++

## 概要
この記事では、GoでRedisを使う方法について説明します。

## Redisの起動方法
Redisの説明やDockerでの起動方法については、[こちらの記事](https://bossagyu.com/blog/040-redis-local/)を参照してください。

Redisを起動する。

```bash
docker run -d --name my-redis \
  -p 6379:6379 \
  redis:7.4.5-alpine
```

redis-cliで接続確認。
```
docker exec -it my-redis redis-cli ping
```

PONGと返ってくればOK。

## Goのプロジェクトの作成
Goのプロジェクトを作成する。

```bash
mkdir redis-go
cd redis-go
go mod init redis-go
```

## GoでRedisを使う

Redisに接続して、書き込み・読み込みを行うサンプルコード。

```go
package main

import (
	"context"
	"fmt"
	"log"
	"time"

	"github.com/redis/go-redis/v9"
)

func main() {
	// パスワードなし、ローカルの6379に接続
	rdb := redis.NewClient(&redis.Options{
		Addr:         "127.0.0.1:6379",
		Password:     "", // なし
		DB:           0,
		DialTimeout:  3 * time.Second,
		ReadTimeout:  1 * time.Second,
		WriteTimeout: 1 * time.Second,
	})
	defer rdb.Close()

	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	// 接続確認
	if err := rdb.Ping(ctx).Err(); err != nil {
		log.Fatalf("redis ping failed: %v", err)
	}
	log.Println("connected to redis ✅")

	// 基本操作（SET/GET）
	if err := rdb.Set(ctx, "hello", "world", 10*time.Minute).Err(); err != nil {
		log.Fatalf("SET error: %v", err)
	}
	val, err := rdb.Get(ctx, "hello").Result()
	if err != nil {
		log.Fatalf("GET error: %v", err)
	}
	fmt.Println("hello =", val)

	// Set型の例
	key := "team:dev"
	if err := rdb.SAdd(ctx, key, "alice", "bob", "alice").Err(); err != nil {
		log.Fatalf("SADD error: %v", err)
	}
	members, _ := rdb.SMembers(ctx, key).Result()
	fmt.Println("members =", members)
}
```

パッケージのインストールを行う。

```bash
go mod tidy
```

実行する。

```bash
go run main.go
```

以下のように表示されればOK。

```2025/10/05 20:30:00 connected to redis ✅
hello = world
members = [alice bob]
```

## まとめ
GoでRedisを使う方法について説明しました。
GoでRedisを使う場合は、[go-redis](https://github.com/redis/go-redis)を使用します。