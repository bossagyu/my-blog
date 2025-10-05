+++
title = 'How to Use Redis with Go'
date = 2025-10-05T20:25:02+09:00
draft = false
categories = ['Engineering']
tags = ['Redis', 'Go', 'Database', 'NoSQL']
+++

## Overview

This article explains how to use Redis with Go.

## Starting Redis

For an explanation of Redis and how to start it using Docker, refer to [this article](https://bossagyu.com/blog/040-redis-local/).

Start Redis:

```bash
docker run -d --name my-redis \
  -p 6379:6379 \
  redis:7.4.5-alpine
```

Check the connection using `redis-cli`:

```
docker exec -it my-redis redis-cli ping
```

If it returns `PONG`, the connection is successful.

## Create a Go Project

Create a new Go project:

```bash
mkdir redis-go
cd redis-go
go mod init redis-go
```

## Using Redis in Go

Here is a sample code that connects to Redis, writes, and reads data:

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
	// Connect to local Redis on port 6379 without a password
	rdb := redis.NewClient(&redis.Options{
		Addr:         "127.0.0.1:6379",
		Password:     "", // no password
		DB:           0,
		DialTimeout:  3 * time.Second,
		ReadTimeout:  1 * time.Second,
		WriteTimeout: 1 * time.Second,
	})
	defer rdb.Close()

	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	// Ping to test connection
	if err := rdb.Ping(ctx).Err(); err != nil {
		log.Fatalf("redis ping failed: %v", err)
	}
	log.Println("connected to redis ✅")

	// Basic operations (SET/GET)
	if err := rdb.Set(ctx, "hello", "world", 10*time.Minute).Err(); err != nil {
		log.Fatalf("SET error: %v", err)
	}
	val, err := rdb.Get(ctx, "hello").Result()
	if err != nil {
		log.Fatalf("GET error: %v", err)
	}
	fmt.Println("hello =", val)

	// Example using Set type
	key := "team:dev"
	if err := rdb.SAdd(ctx, key, "alice", "bob", "alice").Err(); err != nil {
		log.Fatalf("SADD error: %v", err)
	}
	members, _ := rdb.SMembers(ctx, key).Result()
	fmt.Println("members =", members)
}
```

Install the dependencies:

```bash
go mod tidy
```

Run the program:

```bash
go run main.go
```

If you see the following output, everything is working correctly:

```
2025/10/05 20:30:00 connected to redis ✅
hello = world
members = [alice bob]
```

## Summary

This article explained how to use Redis with Go.
When working with Redis in Go, use the [go-redis](https://github.com/redis/go-redis) package.
