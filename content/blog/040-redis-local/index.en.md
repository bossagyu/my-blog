+++
title = 'How to Run Redis with Docker'
description = 'How to run Redis with Docker. Covers container setup for the in-memory database, basic operations, connection testing, and container cleanup.'
date = 2025-10-03T07:29:31+09:00
lastmod = 2025-10-03T07:29:31+09:00
draft = false
categories = ['Engineering']
tags = ['Redis', 'Database', 'NoSQL']
+++

## Overview

This article explains how to run Redis with Docker.

## What is Redis?

Redis is an open-source in-memory data structure store that can be used as a database, cache, and message broker. It provides high-speed read/write performance and supports various data structures such as strings, lists, sets, and hashes.

## Running Redis with Docker

### 1. Install Docker

First, make sure Docker is installed.
If it is not installed, please download and install it from the [official Docker website](https://www.docker.com/get-started).

### 2. Start a Redis Container

Run the following command to start a Redis container:

```bash
docker run --name redis -d  -p 6379:6379 redis:7.4.5-alpine
```

Log in to the container:

```bash
docker exec -it redis sh
```

### 3. Verify Redis Operation

Inside the Redis container, run the following command to start the Redis client:

```bash
redis-cli
```

Try setting and getting data:

```bash
set name "hoge"
get name
"hoge"
```

At this point, you can confirm that Redis is working correctly.

### 4. Stop and Remove the Redis Container

To stop the Redis container, run:

```bash
docker stop redis
```

To remove the Redis container, run:

```bash
docker rm redis
```

## Summary

In this article, we explained how to run Redis using Docker.
Redis is widely used as a high-performance database, and with Docker, it can be easily set up.
Give Redis a try and see how it can help you.

## Related Articles

- [How to Use Redis with Go](/en/blog/041-redis-go/) (practical example of using Redis in Go)
