+++
title = 'Setting Up a Go Development Environment on Mac and Running Hello World'
description = 'How to set up a Go development environment on Mac. Covers Homebrew installation, Hello World program creation, execution, and binary build steps.'
date = 2024-09-15T16:52:04+09:00
draft = false
categories = ['Engineering']
tags = ['ChatGPT']
+++

## Overview

This guide introduces how to quickly set up a Go development environment on Mac and run a Hello World program.

## Installing Go

Install Go using Homebrew.

```shell
> brew install go
```

Check the installed version.

```shell
> go version
go version go1.21.3 darwin/arm64
```

## Running Hello World

Save the following code as `main.go`.

```go
package main

import "fmt"

func main() {
  fmt.Printf("Hello World\n")
}
```

Run the code.

```shell
> go run hello.go
Hello World
```

Build the binary and execute it.

```shell
> go build hello.go

> ls
hello*    hello.go

> ./hello
Hello World
```

## Conclusion

That's how you can set up a Go development environment on Mac and quickly run a Hello World program.