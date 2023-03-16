---
title: My go journey 1
date: 2022-11-04 16:48:40
categories:
  - go
tags:
  - go
  - software engineering
---

### 安装 Go

1. 下载 [Go](https://go.dev/dl/) 安装包，不同的系统下载对应的安装包。
2. 直接运行对应的安装包

<!--more-->

### Go 项目创建

> go mod 模式

1. 初始化项目

```bash
mkdir go_test_project
cd go_test_project
go mod init go_test_project
```

> 设置 GOROOT 环境变量为安装的 Go 的路径。

2. 引入依赖

> 以 gin 为例

```bash
go get -u github.com/gin-gonic/gin
```

完成上面两步就实现了 go mod 项目的创建，这个时候就可以在 go_test_project 目录下直接写代码了。

3. 创建第一个程序

```go
pacakge main

import fmt

func main() {
    fmt.Println("Hello World!")
}
```

4. 编译 main.go

```bash
go build main.go
```

如果需要指定编译的目标平台，以 centos 为例：

```bash
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o ./bin/main
```

5. 运行程序

```bash
./main
```
