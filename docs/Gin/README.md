# Gin 中文文档
Gin 中文文档，本文档基于 [Gin](https://github.com/gin-gonic/gin) 官方文档。不定期更新。

<img align="right" width="159px" src="https://raw.githubusercontent.com/gin-gonic/logo/master/color.png">

[![Build Status](https://travis-ci.org/gin-gonic/gin.svg)](https://travis-ci.org/gin-gonic/gin)
[![codecov](https://codecov.io/gh/gin-gonic/gin/branch/master/graph/badge.svg)](https://codecov.io/gh/gin-gonic/gin)
[![Go Report Card](https://goreportcard.com/badge/github.com/gin-gonic/gin)](https://goreportcard.com/report/github.com/gin-gonic/gin)
[![GoDoc](https://godoc.org/github.com/gin-gonic/gin?status.svg)](https://godoc.org/github.com/gin-gonic/gin)
[![Join the chat at https://gitter.im/gin-gonic/gin](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/gin-gonic/gin?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Sourcegraph](https://sourcegraph.com/github.com/gin-gonic/gin/-/badge.svg)](https://sourcegraph.com/github.com/gin-gonic/gin?badge)
[![Open Source Helpers](https://www.codetriage.com/gin-gonic/gin/badges/users.svg)](https://www.codetriage.com/gin-gonic/gin)
[![Release](https://img.shields.io/github/release/gin-gonic/gin.svg?style=flat-square)](https://github.com/gin-gonic/gin/releases)

Gin 是一个使用 Go 语言写的 web 框架.它拥有与 Martini 相似的 API,但它比 Martini 快 40 多倍。Gin 内部使用 Golang 最快的
HTTP 路由器 [httprouter](https://github.com/julienschmidt/httprouter)。如果你需要更高的性能,更快的开发效率,你会喜欢上 Gin。

## Contents
- [安装](#installation)
- [快速开始](#quick-start)
- [基准](#benchmarks)
- [Gin v1.stable](#gin-v1-stable)
- [使用 jsoniter 构建](#build-with-jsoniter)
- [API 示例](#api-examples)
    - [使用 GET,POST,PUT,PATCH,DELETE 和 OPTIONS 方法](#using-get-post-put-patch-delete-and-options)
    - [路径参数](#parameters-in-path)
    - [Querystring parameters](#querystring-parameters)
    - [Multipart/Urlencoded Form](#multiparturlencoded-form)
    - [Another example: query + post form](#another-example-query--post-form)
    - [Map as querystring or postform parameters](#map-as-querystring-or-postform-parameters)
    - [Upload files](#upload-files)
    - [Grouping routes](#grouping-routes)
    - [Blank Gin without middleware by default](#blank-gin-without-middleware-by-default)
    - [Using middleware](#using-middleware)
    - [How to write log file](#how-to-write-log-file)
    - [Custom log format](#custom-log-format)
    - [Controlling Log output coloring](#controlling-log-output-coloring)
    - [Model binding and validation](#model-binding-and-validation)
    - [Custom Validators](#custom-validators)
    - [Only Bind Query String](#only-bind-query-string)
    - [Bind Query String or Post Data](#bind-query-string-or-post-data)
    - [Bind Uri](#bind-uri)
    - [Bind Header](#bind-header)
    - [Bind HTML checkboxes](#bind-html-checkboxes)
    - [Multipart/Urlencoded binding](#multiparturlencoded-binding)
    - [XML, JSON, YAML and ProtoBuf rendering](#xml-json-yaml-and-protobuf-rendering)
    - [JSONP rendering](#jsonp)
    - [Serving static files](#serving-static-files)
    - [Serving data from reader](#serving-data-from-reader)
    - [HTML rendering](#html-rendering)
    - [Multitemplate](#multitemplate)
    - [Redirects](#redirects)
    - [Custom Middleware](#custom-middleware)
    - [Using BasicAuth() middleware](#using-basicauth-middleware)
    - [Goroutines inside a middleware](#goroutines-inside-a-middleware)
    - [Custom HTTP configuration](#custom-http-configuration)
    - [Support Let's Encrypt](#support-lets-encrypt)
    - [Run multiple service using Gin](#run-multiple-service-using-gin)
    - [Graceful restart or stop](#graceful-restart-or-stop)
    - [Build a single binary with templates](#build-a-single-binary-with-templates)
    - [Bind form-data request with custom struct](#bind-form-data-request-with-custom-struct)
    - [Try to bind body into different structs](#try-to-bind-body-into-different-structs)
    - [http2 server push](#http2-server-push)
    - [Define format for the log of routes](#define-format-for-the-log-of-routes)
    - [Set and get a cookie](#set-and-get-a-cookie)
- [Testing](#testing)
- [Users](#users)

## 安装
## Installation

安装 gin 包之前，需要先下载安装 Go 以及设置好 Go 工作空间。

1. 下载安装（Go 版本 1.10+）:

```sh
$ go get -u github.com/gin-gonic/gin
```

2. 导入包:

```go
import "github.com/gin-gonic/gin"
```

3. (可选) 导入 `net/http`。 如果需要 http 包中的一些参量如 `http.StatusOK`。

```go
import "net/http"
```

### 使用包管理工具 [Govendor](https://github.com/kardianos/govendor)

1. `go get` govendor

```sh
$ go get github.com/kardianos/govendor
```
2. 创建项目文件夹并进入

```sh
$ mkdir -p $GOPATH/src/github.com/myusername/project && cd "$_"
```

3. 初始化你的项目并添加 gin

```sh
$ govendor init
$ govendor fetch github.com/gin-gonic/gin@v1.3
```

4. 复制一个模板到项目

```sh
$ curl https://raw.githubusercontent.com/gin-gonic/gin/master/examples/basic/main.go > main.go
```

5. 启动项目

```sh
$ go run main.go
```

## 快速开始
## Quick start

```sh
# 假定下面的代码在 example.go 文件中
$ cat example.go
```

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080
}
```

```
# 云运行 example.go 然后在浏览器访问 0.0.0.0:8080/ping
$ go run example.go
```

## 基准
## Benchmarks

Gin 使用 [HttpRouter](https://github.com/julienschmidt/httprouter) 的一个定制版本

[查看所有基准](https://github.com/gin-gonic/gin/blob/master/BENCHMARKS.md)

Benchmark name                              | (1)        | (2)         | (3) 		    | (4)
--------------------------------------------|-----------:|------------:|-----------:|---------:
**BenchmarkGin_GithubAll**                  | **30000**  |  **48375**  |     **0**  |   **0**
BenchmarkAce_GithubAll                      |   10000    |   134059    |   13792    |   167
BenchmarkBear_GithubAll                     |    5000    |   534445    |   86448    |   943
BenchmarkBeego_GithubAll                    |    3000    |   592444    |   74705    |   812
BenchmarkBone_GithubAll                     |     200    |  6957308    |  698784    |  8453
BenchmarkDenco_GithubAll                    |   10000    |   158819    |   20224    |   167
BenchmarkEcho_GithubAll                     |   10000    |   154700    |    6496    |   203
BenchmarkGocraftWeb_GithubAll               |    3000    |   570806    |  131656    |  1686
BenchmarkGoji_GithubAll                     |    2000    |   818034    |   56112    |   334
BenchmarkGojiv2_GithubAll                   |    2000    |  1213973    |  274768    |  3712
BenchmarkGoJsonRest_GithubAll               |    2000    |   785796    |  134371    |  2737
BenchmarkGoRestful_GithubAll                |     300    |  5238188    |  689672    |  4519
BenchmarkGorillaMux_GithubAll               |     100    | 10257726    |  211840    |  2272
BenchmarkHttpRouter_GithubAll               |   20000    |   105414    |   13792    |   167
BenchmarkHttpTreeMux_GithubAll              |   10000    |   319934    |   65856    |   671
BenchmarkKocha_GithubAll                    |   10000    |   209442    |   23304    |   843
BenchmarkLARS_GithubAll                     |   20000    |    62565    |       0    |     0
BenchmarkMacaron_GithubAll                  |    2000    |  1161270    |  204194    |  2000
BenchmarkMartini_GithubAll                  |     200    |  9991713    |  226549    |  2325
BenchmarkPat_GithubAll                      |     200    |  5590793    | 1499568    | 27435
BenchmarkPossum_GithubAll                   |   10000    |   319768    |   84448    |   609
BenchmarkR2router_GithubAll                 |   10000    |   305134    |   77328    |   979
BenchmarkRivet_GithubAll                    |   10000    |   132134    |   16272    |   167
BenchmarkTango_GithubAll                    |    3000    |   552754    |   63826    |  1618
BenchmarkTigerTonic_GithubAll               |    1000    |  1439483    |  239104    |  5374
BenchmarkTraffic_GithubAll                  |     100    | 11383067    | 2659329    | 21848
BenchmarkVulcan_GithubAll                   |    5000    |   394253    |   19894    |   609

- (1): 持续时间达到的总重复次数越多，意味着结果越好
- (2): 单次重复持续时间（ns / op）越低越好
- (3): 堆内存（B / op）越低越好
- (4): 平均每次重复分配 (allocs/op) 越低越好

## Gin v1. stable

- [x] Zero allocation router.
- [x] Still the fastest http router and framework. From routing to writing.
- [x] Complete suite of unit tests
- [x] Battle tested
- [x] API frozen, new releases will not break your code.

## 使用 [jsoniter](https://github.com/json-iterator/go) 构建
## Build with [jsoniter](https://github.com/json-iterator/go)

Gin 使用 `encoding/json` 作为默认的 json 解析器但你可以构建时指定成 [jsoniter](https://github.com/json-iterator/go)

```sh
$ go build -tags=jsoniter .
```

## API示例
## Api examples

### 使用 GET, POST, PUT, PATCH, DELETE 和 OPTIONS 路由方法
### Using GET, POST, PUT, PATCH, DELETE and OPTIONS

```go
func main() {
	// 禁用控制台颜色
	// gin.DisableConsoleColor()

	// 用默认的中间件创建一个gin路由器:
	// logger and recovery (crash-free) middleware
	// 包含日志和恢复(不崩溃)中间件
	router := gin.Default()

	router.GET("/someGet", getting)
	router.POST("/somePost", posting)
	router.PUT("/somePut", putting)
	router.DELETE("/someDelete", deleting)
	router.PATCH("/somePatch", patching)
	router.HEAD("/someHead", head)
	router.OPTIONS("/someOptions", options)

	// By default it serves on :8080 unless a
	// PORT environment variable was defined.
	// 默认监听8080端口，除非指定了PORT环境变量
	router.Run()
	// router.Run(":3000") 指定端口号为 :3000

}
```

### 路径参数
### Parameters in path

```go
func main() {
	router := gin.Default()

	// 路由1 匹配 /user/john,但是不匹配 /user/ 或 /user
	router.GET("/user/:name", func(c *gin.Context) {
		name := c.Param("name")
		c.String(http.StatusOK, "Hello %s", name)
	})


	// 路由2 这个会匹配 /user/john/ 和 /user/john/send
	router.GET("/user/:name/*action", func(c *gin.Context) {
		name := c.Param("name")
		action := c.Param("action")
		message := name + " is " + action
		c.String(http.StatusOK, message)
	})

	// 注意 /user/:name 和 /user/:name/是俩个完全不同的路由

	router.Run(":8080")
}
```

### 查询字符串参数
### Querystring parameters

```go
func main() {
	router := gin.Default()

	// 查询字符串参数通过解析基础请求对象获得
	// 请求响应匹配url:  /welcome?firstname=Jane&lastname=Doe
	router.GET("/welcome", func(c *gin.Context) {
	    // 取firstname的值,不存在则设为Guest
		firstname := c.DefaultQuery("firstname", "Guest")
		lastname := c.Query("lastname") // shortcut for c.Request.URL.Query().Get("lastname")

		c.String(http.StatusOK, "Hello %s %s", firstname, lastname)
	})
	router.Run(":8080")
}
```

### Multipart/Urlencoded 表单
### Multipart/Urlencoded Form

```go
func main() {
	router := gin.Default()

	router.POST("/form_post", func(c *gin.Context) {
		message := c.PostForm("message")
		nick := c.DefaultPostForm("nick", "anonymous")

		c.JSON(200, gin.H{
			"status":  "posted",
			"message": message,
			"nick":    nick,
		})
	})
	router.Run(":8080")
}
```

### 其他示例: 查询 + 表单
### Another example: query + post form

```sh
POST /post?id=1234&page=1 HTTP/1.1
Content-Type: application/x-www-form-urlencoded

name=manu&message=this_is_great
```

```go
func main() {
	router := gin.Default()

	router.POST("/post", func(c *gin.Context) {
		// url 中查询数据
		id := c.Query("id")
		page := c.DefaultQuery("page", "0")

		// post 表单中数据
		name := c.PostForm("name")
		message := c.PostForm("message")

		fmt.Printf("id: %s; page: %s; name: %s; message: %s", id, page, name, message)
	})
	router.Run(":8080")
}
```

```sh
id: 1234; page: 1; name: manu; message: this_is_great
```

### 以映射表示的查询字符串或者表单参数
### Map as querystring or postform parameters

```sh
POST /post?ids[a]=1234&ids[b]=hello HTTP/1.1
Content-Type: application/x-www-form-urlencoded

names[first]=thinkerou&names[second]=tianou
```

```go
func main() {
	router := gin.Default()

	router.POST("/post", func(c *gin.Context) {

		ids := c.QueryMap("ids")
		names := c.PostFormMap("names")

		fmt.Printf("ids: %v; names: %v", ids, names)
	})
	router.Run(":8080")
}
```

```sh
ids: map[b:hello a:1234], names: map[second:tianou first:thinkerou]
```

### 上传文件
### Upload files

#### 单个文件

参考问题 [#774](https://github.com/gin-gonic/gin/issues/774) 和细节 [example code](examples/upload-file/single).

`file.Filename` **是不可信的**。
查看 [MDN `Content-Disposition`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition#Directives)
和 [#1693](https://github.com/gin-gonic/gin/issues/1693)

> filename 总是可选的，应用程序不能盲目地使用它：应该删除路径信息，并执行到服务器文件系统规则的转换。

```go
func main() {
	router := gin.Default()
	// 设置可以上传 multipart 表单的最大体积（默认为 32MiB）
	// router.MaxMultipartMemory = 8 << 20  // 8 MiB
	router.POST("/upload", func(c *gin.Context) {
		// single file
		file, _ := c.FormFile("file")
		log.Println(file.Filename)

		// 复制文件到指定目标
		// c.SaveUploadedFile(file, dst)

		c.String(http.StatusOK, fmt.Sprintf("'%s' uploaded!", file.Filename))
	})
	router.Run(":8080")
}
```

使用 `curl`:

```bash
curl -X POST http://localhost:8080/upload \
  -F "file=@/Users/appleboy/test.zip" \
  -H "Content-Type: multipart/form-data"
```

#### 多个文件

查看细节 [example code](https://github.com/gin-gonic/examples/tree/master/upload-file/multiple).

```go
func main() {
	router := gin.Default()
	// 设置可以上传 multipart 表单的最大体积（默认为 32MiB）
	// router.MaxMultipartMemory = 8 << 20  // 8 MiB
	router.POST("/upload", func(c *gin.Context) {
		// Multipart form
		form, _ := c.MultipartForm()
		files := form.File["upload[]"]

		for _, file := range files {
			log.Println(file.Filename)

			// Upload the file to specific dst.
			// c.SaveUploadedFile(file, dst)
		}
		c.String(http.StatusOK, fmt.Sprintf("%d files uploaded!", len(files)))
	})
	router.Run(":8080")
}
```

使用 `curl`:

```bash
curl -X POST http://localhost:8080/upload \
  -F "upload[]=@/Users/appleboy/test1.zip" \
  -F "upload[]=@/Users/appleboy/test2.zip" \
  -H "Content-Type: multipart/form-data"
```

### 分组路由
### Grouping routes

```go
func main() {
	router := gin.Default()

	// Simple group: v1
	v1 := router.Group("/v1")
	{
		v1.POST("/login", loginEndpoint)
		v1.POST("/submit", submitEndpoint)
		v1.POST("/read", readEndpoint)
	}

	// Simple group: v2
	v2 := router.Group("/v2")
	{
		v2.POST("/login", loginEndpoint)
		v2.POST("/submit", submitEndpoint)
		v2.POST("/read", readEndpoint)
	}

	router.Run(":8080")
}
```

### 无中间件路由
### Blank Gin without middleware by default

使用

```go
r := gin.New()
```

而不是

```go
// 默认情况已启用了 log 和恢复中间件
r := gin.Default()
```


### 使用中间件
### Using middleware

```go
func main() {
	// 创建不含默认中间件的路由
	r := gin.New()

	// 全局中间件
	// Logger 中间件会将日志写到 gin.DefaultWriter 即使指定 GIN_MODE=release
	// 默认情况下 gin.DefaultWriter = os.Stdout
	r.Use(gin.Logger())
    
	// Recovery 中间件从任何 panic 恢复 并且写入一个 500 状态码
	r.Use(gin.Recovery())
    
	// 可以随心添加中间件到任何你想要添加的路由上
	r.GET("/benchmark", MyBenchLogger(), benchEndpoint)

	// 认证分组
	// authorized := r.Group("/", AuthRequired())
	// exactly the same as:
	authorized := r.Group("/")
	// per group middleware! in this case we use the custom created
	// AuthRequired() middleware just in the "authorized" group.
	authorized.Use(AuthRequired())
	{
		authorized.POST("/login", loginEndpoint)
		authorized.POST("/submit", submitEndpoint)
		authorized.POST("/read", readEndpoint)

		// 嵌套分组
		testing := authorized.Group("testing")
		testing.GET("/analytics", analyticsEndpoint)
	}

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")
}
```

### 如何写日志
### How to write log file

```go
func main() {
    // 当你不需要写入日志时颜色提示时, 关闭控制台颜色.
    gin.DisableConsoleColor()

    // 将日志写入指定文件
    f, _ := os.Create("gin.log")
    gin.DefaultWriter = io.MultiWriter(f)

    // 同时使用文件与控制台记录日志
    // gin.DefaultWriter = io.MultiWriter(f, os.Stdout)

    router := gin.Default()
    router.GET("/ping", func(c *gin.Context) {
        c.String(200, "pong")
    })

    router.Run(":8080")
}
```

### 自定义 log format
### Custom log format
```go
func main() {
	router := gin.New()

	// LoggerWithFormatter 中间件将 log 写入 gin.DefaultWriter
	// 默认情况下 gin.DefaultWriter = os.Stdout
	router.Use(gin.LoggerWithFormatter(func(param gin.LogFormatterParams) string {

		// 自定义格式
		return fmt.Sprintf("%s - [%s] \"%s %s %s %d %s \"%s\" %s\"\n",
				param.ClientIP,
				param.TimeStamp.Format(time.RFC1123),
				param.Method,
				param.Path,
				param.Request.Proto,
				param.StatusCode,
				param.Latency,
				param.Request.UserAgent(),
				param.ErrorMessage,
		)
	}))
	router.Use(gin.Recovery())

	router.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong")
	})

	router.Run(":8080")
}
```

输出：
```sh
::1 - [Fri, 07 Dec 2018 17:04:38 JST] "GET /ping HTTP/1.1 200 122.767µs "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.80 Safari/537.36" "
```

### Controlling Log output coloring
默认情况下，控制台输出的日志颜色依赖于检测到的 TTY。

永远不为日志着色:
```go
func main() {
    // 禁用日志颜色
    gin.DisableConsoleColor()

    // 创建一个包含默认中间件的 gin 路由器:
    // logger and recovery (crash-free) middleware
    router := gin.Default()

    router.GET("/ping", func(c *gin.Context) {
        c.String(200, "pong")
    })

    router.Run(":8080")
}
```

总是为日志着色:
```go
func main() {
    // 强制使用日志颜色
    gin.DisableConsoleColor()

    // 创建一个包含默认中间件的 gin 路由器:
    // logger and recovery (crash-free) middleware
    router := gin.Default()

    router.GET("/ping", func(c *gin.Context) {
        c.String(200, "pong")
    })

    router.Run(":8080")
}
```
### 模型绑定与验证
### Model binding and validation

使用 model binding 绑定请求主体到类型，Gin 支持绑定 JSON, XML, 标准表单。(foo=bar&boo=baz)

Gin 使用 [**go-playground/validator.v8**](https://github.com/go-playground/validator) 验证。
在 [此处](http://godoc.org/gopkg.in/go-playground/validator.v8#hdr-Baked_In_Validators_and_Tags) 查看有关标签用法的完整文档。

注意你需要在所需要绑定的字段设置对应的绑定标签。例如，当从 JSON 绑定时，设置 `json:"fieldname"`

Gin 提供两种方法集绑定

- **Type** - Must bind
  - **Methods** - `Bind`, `BindJSON`, `BindXML`, `BindQuery`
  - **Behavior** - 这些方法底层使用 `MustBindWith` 。如果绑定失败，该请求会使用 `c.AbortWithError(400, err).SetType(ErrorTypeBind)` 中止舍弃。
  返回 400 状态码，设置 `Content-Type` 首部为 `text/plain; charset=utf-8`。注意如果你在此后想设置状态状态码，会导致一个
  警告 `[GIN-debug] [WARNING] Headers were already written. Wanted to override status code 400 with 422`。
  如果你想更好地掌控该行为，考虑使用 `ShouldBind` 方法。
- **Type** - Should bind
  - **Methods** - `ShouldBind`, `ShouldBindJSON`, `ShouldBindXML`, `ShouldBindQuery`
  - **Behavior** - 这些方法底层使用 `ShouldBindWith`。如果绑定失败，由开发者掌控处理请求与错误。

Gin 会从首部 `Content-Type`字段推断绑定内容的类型，如果你确定需要绑定内容的类型，可以使用 `MustBindWith` 或者 `ShouldBindWith`。

你同样可以指定绑定必须提供的字段。如果一个带有 `binding:"required"` 标签的类型绑定时无对应值，将返回一个错误。

```go
// Binding from JSON
type Login struct {
	User     string `form:"user" json:"user" xml:"user"  binding:"required"`
	Password string `form:"password" json:"password" xml:"password" binding:"required"`
}

func main() {
	router := gin.Default()

	// Example for binding JSON ({"user": "manu", "password": "123"})
	router.POST("/loginJSON", func(c *gin.Context) {
		var json Login
		if err := c.ShouldBindJSON(&json); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}
		
		if json.User != "manu" || json.Password != "123" {
			c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
			return
		} 
		
		c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
	})

	// Example for binding XML (
	//	<?xml version="1.0" encoding="UTF-8"?>
	//	<root>
	//		<user>user</user>
	//		<password>123</user>
	//	</root>)
	router.POST("/loginXML", func(c *gin.Context) {
		var xml Login
		if err := c.ShouldBindXML(&xml); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}
		
		if xml.User != "manu" || xml.Password != "123" {
			c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
			return
		} 
		
		c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
	})

	// Example for binding a HTML form (user=manu&password=123)
	router.POST("/loginForm", func(c *gin.Context) {
		var form Login
		// This will infer what binder to use depending on the content-type header.
		if err := c.ShouldBind(&form); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}
		
		if form.User != "manu" || form.Password != "123" {
			c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
			return
		} 
		
		c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
	})

	// Listen and serve on 0.0.0.0:8080
	router.Run(":8080")
}
```

**Sample request**
```shell
$ curl -v -X POST \
  http://localhost:8080/loginJSON \
  -H 'content-type: application/json' \
  -d '{ "user": "manu" }'
> POST /loginJSON HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.51.0
> Accept: */*
> content-type: application/json
> Content-Length: 18
>
* upload completely sent off: 18 out of 18 bytes
< HTTP/1.1 400 Bad Request
< Content-Type: application/json; charset=utf-8
< Date: Fri, 04 Aug 2017 03:51:31 GMT
< Content-Length: 100
<
{"error":"Key: 'Login.Password' Error:Field validation for 'Password' failed on the 'required' tag"}
```

**跳过验证**

使用上面的 `curl` 命令，将返回错误。因为 `Password` 字段使用了 `binding:"required"`。 如果使用 `binding:"-"`，则不会返回错误。

### 自定义验证器
### Custom Validators

可以注册自定义验证器，查看 [example code](https://github.com/gin-gonic/examples/blob/master/custom-validation/server.go)。

```go
package main

import (
	"net/http"
	"reflect"
	"time"

	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
	"gopkg.in/go-playground/validator.v8"
)

// Booking contains binded and validated data.
type Booking struct {
	CheckIn  time.Time `form:"check_in" binding:"required,bookabledate" time_format:"2006-01-02"`
	CheckOut time.Time `form:"check_out" binding:"required,gtfield=CheckIn" time_format:"2006-01-02"`
}

func bookableDate(
	v *validator.Validate, topStruct reflect.Value, currentStructOrField reflect.Value,
	field reflect.Value, fieldType reflect.Type, fieldKind reflect.Kind, param string,
) bool {
	if date, ok := field.Interface().(time.Time); ok {
		today := time.Now()
		if today.Year() > date.Year() || today.YearDay() > date.YearDay() {
			return false
		}
	}
	return true
}

func main() {
	route := gin.Default()

	if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
		v.RegisterValidation("bookabledate", bookableDate)
	}

	route.GET("/bookable", getBookable)
	route.Run(":8085")
}

func getBookable(c *gin.Context) {
	var b Booking
	if err := c.ShouldBindWith(&b, binding.Query); err == nil {
		c.JSON(http.StatusOK, gin.H{"message": "Booking dates are valid!"})
	} else {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
	}
}
```

```sh
$ curl "localhost:8085/bookable?check_in=2018-04-16&check_out=2018-04-17"
{"message":"Booking dates are valid!"}

$ curl "localhost:8085/bookable?check_in=2018-03-08&check_out=2018-03-09"
{"error":"Key: 'Booking.CheckIn' Error:Field validation for 'CheckIn' failed on the 'bookabledate' tag"}
```

[Struct level validations](https://github.com/go-playground/validator/releases/tag/v8.7) 可以同样以此注册。
查看 [struct-lvl-validation example](https://github.com/gin-gonic/examples/tree/master/struct-lvl-validations) 学习更多。

### 只绑定查询字符串
### Only Bind Query String

`ShouldBindQuery` 方法只从查询字符串绑定，而不包括 POST 方法提供的数据。
查看 [detail information](https://github.com/gin-gonic/gin/issues/742#issuecomment-315953017)。
```go
package main

import (
	"log"

	"github.com/gin-gonic/gin"
)

type Person struct {
	Name    string `form:"name"`
	Address string `form:"address"`
}

func main() {
	route := gin.Default()
	route.Any("/testing", startPage)
	route.Run(":8085")
}

func startPage(c *gin.Context) {
	var person Person
	if c.ShouldBindQuery(&person) == nil {
		log.Println("====== Only Bind By Query String ======")
		log.Println(person.Name)
		log.Println(person.Address)
	}
	c.String(200, "Success")
}

```

### 绑定查询字符串或者 Post Data
### Bind Query String or Post Data

查看 [detail information](https://github.com/gin-gonic/gin/issues/742#issuecomment-264681292)。

```go
package main

import (
	"log"
	"time"

	"github.com/gin-gonic/gin"
)

type Person struct {
        Name       string    `form:"name"`
        Address    string    `form:"address"`
        Birthday   time.Time `form:"birthday" time_format:"2006-01-02" time_utc:"1"`
        CreateTime time.Time `form:"createTime" time_format:"unixNano"`
        UnixTime   time.Time `form:"unixTime" time_format:"unix"`
}

func main() {
	route := gin.Default()
	route.GET("/testing", startPage)
	route.Run(":8085")
}

func startPage(c *gin.Context) {
	var person Person
	// If `GET`, only `Form` binding engine (`query`) used.
	// If `POST`, first checks the `content-type` for `JSON` or `XML`, then uses `Form` (`form-data`).
	// See more at https://github.com/gin-gonic/gin/blob/master/binding/binding.go#L48
        if c.ShouldBind(&person) == nil {
                log.Println(person.Name)
                log.Println(person.Address)
                log.Println(person.Birthday)
                log.Println(person.CreateTime)
                log.Println(person.UnixTime)
        }

	c.String(200, "Success")
}
```

测试:
```sh
$ curl -X GET "localhost:8085/testing?name=appleboy&address=xyz&birthday=1992-03-15&createTime=1562400033000000123&unixTime=1562400033"
```

### 绑定 Uri
### Bind Uri
查看 [detail information](https://github.com/gin-gonic/gin/issues/846)。

```go
package main

import "github.com/gin-gonic/gin"

type Person struct {
	ID string `uri:"id" binding:"required,uuid"`
	Name string `uri:"name" binding:"required"`
}

func main() {
	route := gin.Default()
	route.GET("/:name/:id", func(c *gin.Context) {
		var person Person
		if err := c.ShouldBindUri(&person); err != nil {
			c.JSON(400, gin.H{"msg": err})
			return
		}
		c.JSON(200, gin.H{"name": person.Name, "uuid": person.ID})
	})
	route.Run(":8088")
}
```

测试:
```sh
$ curl -v localhost:8088/thinkerou/987fbc97-4bed-5078-9f07-9141ba07c9f3
$ curl -v localhost:8088/thinkerou/not-uuid
```

### 绑定 Header
### Bind Header

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

type testHeader struct {
	Rate   int    `header:"Rate"`
	Domain string `header:"Domain"`
}

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		h := testHeader{}

		if err := c.ShouldBindHeader(&h); err != nil {
			c.JSON(200, err)
		}

		fmt.Printf("%#v\n", h)
		c.JSON(200, gin.H{"Rate": h.Rate, "Domain": h.Domain})
	})

	r.Run()

// client
// curl -H "rate:300" -H "domain:music" 127.0.0.1:8080/
// output
// {"Domain":"music","Rate":300}
}
```

### 绑定 HTML 复选框
### Bind HTML checkboxes

查看 [detail information](https://github.com/gin-gonic/gin/issues/129#issuecomment-124260092)。

main.go

```go
...

type myForm struct {
    Colors []string `form:"colors[]"`
}

...

func formHandler(c *gin.Context) {
    var fakeForm myForm
    c.ShouldBind(&fakeForm)
    c.JSON(200, gin.H{"color": fakeForm.Colors})
}

...

```

form.html

```html
<form action="/" method="POST">
    <p>Check some colors</p>
    <label for="red">Red</label>
    <input type="checkbox" name="colors[]" value="red" id="red" />
    <label for="green">Green</label>
    <input type="checkbox" name="colors[]" value="green" id="green" />
    <label for="blue">Blue</label>
    <input type="checkbox" name="colors[]" value="blue" id="blue" />
    <input type="submit" />
</form>
```

结果:

```
{"color":["red","green","blue"]}
```

### Multipart/Urlencoded 表单绑定
### Multipart/Urlencoded binding

```go
type ProfileForm struct {
	Name   string                `form:"name" binding:"required"`
	Avatar *multipart.FileHeader `form:"avatar" binding:"required"`

	// or for multiple files
	// Avatars []*multipart.FileHeader `form:"avatar" binding:"required"`
}


func main() {
	router := gin.Default()
	router.POST("/profile", func(c *gin.Context) {
		// 你可以绑定 multipart form 通过明确的绑定声明：
		// c.ShouldBindWith(&form, binding.Form)
		// 或者简单地使用 ShouldBind method 自动绑定：
		var form ProfileForm
		// 该情况下 gin 会自动选择合适的绑定类型
		if err := c.ShouldBind(&form); err != nil {
			c.String(http.StatusBadRequest, "bad request")
			return
		}

		err := c.SaveUploadedFile(form.Avatar, form.Avatar.Filename)
		if err != nil {
			c.String(http.StatusInternalServerError, "unknown error")
			return
		}

        // db.Save(&form)

        c.String(http.StatusOK, "ok")
	})
	router.Run(":8080")
}
```

测试:
```sh
$ curl -X POST -v --form name=user --form "avatar=@./avatar.png" http://localhost:8080/profile
```

### XML, JSON, YAML and ProtoBuf 渲染
### XML, JSON, YAML and ProtoBuf rendering

```go
func main() {
	r := gin.Default()

	// gin.H 是 map[string]interface{} 的快捷方法
	r.GET("/someJSON", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
	})

	r.GET("/moreJSON", func(c *gin.Context) {
		// 你同样可以使用结构体
		var msg struct {
			Name    string `json:"user"`
			Message string
			Number  int
		}
		msg.Name = "Lena"
		msg.Message = "hey"
		msg.Number = 123
		// 注意 msg.Name 在 JSON 中变成了用 "user" 表示
		// 输出： {"user": "Lena", "Message": "hey", "Number": 123}
		c.JSON(http.StatusOK, msg)
	})

	r.GET("/someXML", func(c *gin.Context) {
		c.XML(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
	})

	r.GET("/someYAML", func(c *gin.Context) {
		c.YAML(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
	})

	r.GET("/someProtoBuf", func(c *gin.Context) {
		reps := []int64{int64(1), int64(2)}
		label := "test"
		// 该 protobuf 的定义在 testdata/protoexample 文件中
		data := &protoexample.Test{
			Label: &label,
			Reps:  reps,
		}
		// 数据在响应中变成二进制格式
		// 输出protoexample.Test protobuf序列化数据
		c.ProtoBuf(http.StatusOK, data)
	})

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")
}
```

#### SecureJSON

使用 SecureJSON 防止 JSON 劫持. Default prepends `"while(1),"` to response body if the given struct is array values.

```go
func main() {
	r := gin.Default()
    
	// 可以使用自定义的安全 json 前缀
	// r.SecureJsonPrefix(")]}',\n")

	r.GET("/someJSON", func(c *gin.Context) {
		names := []string{"lena", "austin", "foo"}

		// 输出：   while(1);["lena","austin","foo"]
		c.SecureJSON(http.StatusOK, names)
	})

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")
}
```
#### JSONP
#### JSONP

使用 JSONP 请求来自不同域名中的服务器的数据。如果存在查询参数回调，则将回调添加到响应体


```go
func main() {
	r := gin.Default()

	r.GET("/JSONP", func(c *gin.Context) {
		data := gin.H{
			"foo": "bar",
		}

		//callback is x
		// Will output  :   x({\"foo\":\"bar\"})
		c.JSONP(http.StatusOK, data)
	})

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")

        // client
        // curl http://127.0.0.1:8080/JSONP?callback=x
}
```

#### AsciiJSON

使用 AsciiJSON 生成只由 ASCII 编码的 JSON 字符。

```go
func main() {
	r := gin.Default()

	r.GET("/someJSON", func(c *gin.Context) {
		data := map[string]interface{}{
			"lang": "GO语言",
			"tag":  "<br>",
		}

		// will output : {"lang":"GO\u8bed\u8a00","tag":"\u003cbr\u003e"}
		c.AsciiJSON(http.StatusOK, data)
	})

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")
}
```

#### PureJSON

Normally, JSON replaces special HTML characters with their unicode entities, e.g. `<` becomes  `\u003c`. If you want to encode such characters literally, you can use PureJSON instead.
This feature is unavailable in Go 1.6 and lower.

```go
func main() {
	r := gin.Default()
	
	// Serves unicode entities
	r.GET("/json", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"html": "<b>Hello, world!</b>",
		})
	})
	
	// Serves literal characters
	r.GET("/purejson", func(c *gin.Context) {
		c.PureJSON(200, gin.H{
			"html": "<b>Hello, world!</b>",
		})
	})
	
	// listen and serve on 0.0.0.0:8080
	r.Run(":8080)
}
```

### 提供静态文件
### Serving static files

```go
func main() {
	router := gin.Default()
	router.Static("/assets", "./assets")
	router.StaticFS("/more_static", http.Dir("my_file_system"))
	router.StaticFile("/favicon.ico", "./resources/favicon.ico")

	// Listen and serve on 0.0.0.0:8080
	router.Run(":8080")
}
```

### Serving data from reader

```go
func main() {
	router := gin.Default()
	router.GET("/someDataFromReader", func(c *gin.Context) {
		response, err := http.Get("https://raw.githubusercontent.com/gin-gonic/logo/master/color.png")
		if err != nil || response.StatusCode != http.StatusOK {
			c.Status(http.StatusServiceUnavailable)
			return
		}

		reader := response.Body
		contentLength := response.ContentLength
		contentType := response.Header.Get("Content-Type")

		extraHeaders := map[string]string{
			"Content-Disposition": `attachment; filename="gopher.png"`,
		}

		c.DataFromReader(http.StatusOK, contentLength, contentType, reader, extraHeaders)
	})
	router.Run(":8080")
}
```

### HTML 渲染
### HTML rendering

使用 `LoadHTMLGlob()` 或者 `LoadHTMLFiles()`

```go
func main() {
	router := gin.Default()
	
	// 加载所有的模板文件
	router.LoadHTMLGlob("templates/*")

	// 加载某个模板文件
	// router.LoadHTMLFiles("templates/template1.html", "templates/template2.html")
	
	router.GET("/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.tmpl", gin.H{
			"title": "Main website",
		})
	})
	router.Run(":8080")
}
```

`templates/index.tmpl`

```html
<html>
	<h1>
		{{ .title }}
	</h1>
</html>
```

#### 渲染原生 HTML
```go
g.LoagHTMLFiles(filepath.Join(staticPath, "index.html"))

router.GET("/", func(c *gin.Context) {
    c.HTML(http.StatusOK, "index.html", nil)
})

// 或者
router.GET("/", func(c *gin.Context) {
   c.File(filepath.Join(staticPath, "index.html"))
})
```

#### 使用不同文件夹相同名称的 HTML 模板


```go
func main() {
	router := gin.Default()
	router.LoadHTMLGlob("templates/**/*")
	router.GET("/posts/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "posts/index.tmpl", gin.H{
			"title": "Posts",
		})
	})
	router.GET("/users/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "users/index.tmpl", gin.H{
			"title": "Users",
		})
	})
	router.Run(":8080")
}
```

`templates/posts/index.tmpl`

```html
{{ define "posts/index.tmpl" }}
<html><h1>
	{{ .title }}
</h1>
<p>Using posts/index.tmpl</p>
</html>
{{ end }}
```

`templates/users/index.tmpl`

```html
{{ define "users/index.tmpl" }}
<html><h1>
	{{ .title }}
</h1>
<p>Using users/index.tmpl</p>
</html>
{{ end }}
```

#### 自定义模板渲染器

可以使用自定义 HTML 模板渲染器

```go
import "html/template"

func main() {
	router := gin.Default()
	html := template.Must(template.ParseFiles("file1", "file2"))
	router.SetHTMLTemplate(html)
	router.Run(":8080")
}
```

#### 自定义分隔符

可以自定义分隔符

```go
	r := gin.Default()
	r.Delims("{[{", "}]}")
	r.LoadHTMLGlob("/path/to/templates"))
```

#### 自定义模板函数

查看细节 [example code](https://github.com/gin-gonic/examples/tree/master/template).

`main.go`

```go
import (
    "fmt"
    "html/template"
    "net/http"
    "time"

    "github.com/gin-gonic/gin"
)

func formatAsDate(t time.Time) string {
    year, month, day := t.Date()
    return fmt.Sprintf("%d%02d/%02d", year, month, day)
}

func main() {
    router := gin.Default()
    router.Delims("{[{", "}]}")
    router.SetFuncMap(template.FuncMap{
        "formatAsDate": formatAsDate,
    })
    router.LoadHTMLFiles("./testdata/template/raw.tmpl")

    router.GET("/raw", func(c *gin.Context) {
        c.HTML(http.StatusOK, "raw.tmpl", map[string]interface{}{
            "now": time.Date(2017, 07, 01, 0, 0, 0, 0, time.UTC),
        })
    })

    router.Run(":8080")
}

```

`raw.tmpl`

```html
Date: {[{.now | formatAsDate}]}
```

显示：
```
Date: 2017/07/01
```

### 多模板
### Multitemplate

Gin 默认情况下只允许使用一个模板文件. 点击 [这里](https://github.com/gin-contrib/multitemplate) 看如何使用如 go 1.6 `block template` 来实现多模板渲染.

### 重定向
### Redirects

发起一个 HTTP 重定向很容易，支持内部与外部链接。

```go
r.GET("/test", func(c *gin.Context) {
	c.Redirect(http.StatusMovedPermanently, "http://www.google.com/")
})
```

发起一个路由重定向，使用 `HandleContext`。

``` go
r.GET("/test", func(c *gin.Context) {
    c.Request.URL.Path = "/test2"
    r.HandleContext(c)
})
r.GET("/test2", func(c *gin.Context) {
    c.JSON(200, gin.H{"hello": "world"})
})
```


### 自定义中间件
### Custom Middleware

```go
func Logger() gin.HandlerFunc {
	return func(c *gin.Context) {
		t := time.Now()

		// 设置变量example
		c.Set("example", "12345")

		// 请求之前

		c.Next()

		// 请求之后
		latency := time.Since(t)
		log.Print(latency)

		// 访问我们发送的状态
		status := c.Writer.Status()
		log.Println(status)
	}
}

func main() {
	r := gin.New()
	r.Use(Logger())

	r.GET("/test", func(c *gin.Context) {
		example := c.MustGet("example").(string)

		// it would print: "12345"
		log.Println(example)
	})

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")
}
```

### 使用 BasicAuth() 中间件
### Using BasicAuth middleware

```go
// 模拟隐私数据
var secrets = gin.H{
	"foo":    gin.H{"email": "foo@bar.com", "phone": "123433"},
	"austin": gin.H{"email": "austin@example.com", "phone": "666"},
	"lena":   gin.H{"email": "lena@guapa.com", "phone": "523443"},
}

func main() {
	r := gin.Default()

	// 下面分组使用 gin.BasicAuth() 中间件
	// gin.Accounts是map[string]string的快捷方法
	authorized := r.Group("/admin", gin.BasicAuth(gin.Accounts{
		"foo":    "bar",
		"austin": "1234",
		"lena":   "hello2",
		"manu":   "4321",
	}))

	// /admin/secrets 端点
	// 点击访问 "localhost:8080/admin/secrets
	authorized.GET("/secrets", func(c *gin.Context) {
		// 获取用户,由BasicAuth middleware代理设置
		user := c.MustGet(gin.AuthUserKey).(string)
		if secret, ok := secrets[user]; ok {
			c.JSON(http.StatusOK, gin.H{"user": user, "secret": secret})
		} else {
			c.JSON(http.StatusOK, gin.H{"user": user, "secret": "NO SECRET :("})
		}
	})

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")
}
```

### 中间件中的协程
### Goroutines inside a middleware

在中间件或者处理器中开启协程时，**不应**使用原来的 context 上下文，只可以使用一个只读的副本。

```go
func main() {
	r := gin.Default()

	r.GET("/long_async", func(c *gin.Context) {
		// 创建副本用于协程之中
		cCp := c.Copy()
		go func() {
			// 假设一个运行5秒的任务
			time.Sleep(5 * time.Second)

			// 注意你在使用context的副本
			log.Println("Done! in path " + cCp.Request.URL.Path)
		}()
	})

	r.GET("/long_sync", func(c *gin.Context) {
		// 假设一个运行5秒的任务
		time.Sleep(5 * time.Second)

		// 不在协程中，可直接使用context
		log.Println("Done! in path " + c.Request.URL.Path)
	})

	// Listen and serve on 0.0.0.0:8080
	r.Run(":8080")
}
```

### 自定义 HTTP 配置
### Custom HTTP configuration

直接使用 `http.ListenAndServe()` 如下：

```go
func main() {
	router := gin.Default()
	http.ListenAndServe(":8080", router)
}
```
或者

```go
func main() {
	router := gin.Default()

	s := &http.Server{
		Addr:           ":8080",
		Handler:        router,
		ReadTimeout:    10 * time.Second,
		WriteTimeout:   10 * time.Second,
		MaxHeaderBytes: 1 << 20,
	}
	s.ListenAndServe()
}
```

### Support Let's Encrypt

example for 1-line LetsEncrypt HTTPS servers.

```go
package main

import (
	"log"

	"github.com/gin-gonic/autotls"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	// Ping handler
	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong")
	})

	log.Fatal(autotls.Run(r, "example1.com", "example2.com"))
}
```

自定义 autocert 管理器的示例.

[embedmd]:# (examples/auto-tls/example2/main.go go)
```go
package main

import (
	"log"

	"github.com/gin-gonic/autotls"
	"github.com/gin-gonic/gin"
	"golang.org/x/crypto/acme/autocert"
)

func main() {
	r := gin.Default()

	// Ping handler
	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong")
	})

	m := autocert.Manager{
		Prompt:     autocert.AcceptTOS,
		HostPolicy: autocert.HostWhitelist("example1.com", "example2.com"),
		Cache:      autocert.DirCache("/var/www/.cache"),
	}

	log.Fatal(autotls.RunWithManager(r, &m))
}
```

### 使用 Gin 运行多服务
### Run multiple service using Gin

请参阅 [问题](https://github.com/gin-gonic/gin/issues/346) 并尝试以下示例：

```go
package main

import (
	"log"
	"net/http"
	"time"

	"github.com/gin-gonic/gin"
	"golang.org/x/sync/errgroup"
)

var (
	g errgroup.Group
)

func router01() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 01",
			},
		)
	})

	return e
}

func router02() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 02",
			},
		)
	})

	return e
}

func main() {
	server01 := &http.Server{
		Addr:         ":8080",
		Handler:      router01(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}

	server02 := &http.Server{
		Addr:         ":8081",
		Handler:      router02(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}

	g.Go(func() error {
		return server01.ListenAndServe()
	})

	g.Go(func() error {
		return server02.ListenAndServe()
	})

	if err := g.Wait(); err != nil {
		log.Fatal(err)
	}
}
```

### 优雅重启或停止
### Graceful restart or stop

以下方式可以让你优雅的重启或停止你的 web 服务器。

我们可以用 [fvbock/endless](https://github.com/fvbock/endless) 取代默认的 `ListenAndServe`。
请参阅 [问题 #296](https://github.com/gin-gonic/gin/issues/296) 获得更多细节.

```go
router := gin.Default()
router.GET("/", handler)
// [...]
endless.ListenAndServe(":4242", router)
```

endless 的替代品:

* [manners](https://github.com/braintree/manners): A polite Go HTTP server that shuts down gracefully.
* [graceful](https://github.com/tylerb/graceful): Graceful is a Go package enabling graceful shutdown of an http.Handler server.
* [grace](https://github.com/facebookgo/grace): Graceful restart & zero downtime deploy for Go servers.

如果你在使用 Go 1.8，你可能并不需要这个库。考虑使用 `http.Server` 的内建 [Shutdown()](https://golang.org/pkg/net/http/#Server.Shutdown) 方法优雅关闭。
查看示例 [graceful-shutdown](./examples/graceful-shutdown).

```go
// +build go1.8

package main

import (
	"context"
	"log"
	"net/http"
	"os"
	"os/signal"
	"syscall"
	"time"

	"github.com/gin-gonic/gin"
)

func main() {
	router := gin.Default()
	router.GET("/", func(c *gin.Context) {
		time.Sleep(5 * time.Second)
		c.String(http.StatusOK, "Welcome Gin Server")
	})

	srv := &http.Server{
		Addr:    ":8080",
		Handler: router,
	}

	go func() {
		// service connections
		if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
			log.Fatalf("listen: %s\n", err)
		}
	}()

	// Wait for interrupt signal to gracefully shutdown the server with
	// a timeout of 5 seconds.
	quit := make(chan os.Signal)
	// kill (no param) default send syscall.SIGTERM
	// kill -2 is syscall.SIGINT
	// kill -9 is syscall.SIGKILL but can't be catch, so don't need add it
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
	<-quit
	log.Println("Shutdown Server ...")

	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()
	if err := srv.Shutdown(ctx); err != nil {
		log.Fatal("Server Shutdown:", err)
	}
	// catching ctx.Done(). timeout of 5 seconds.
	select {
	case <-ctx.Done():
		log.Println("timeout of 5 seconds.")
	}
	log.Println("Server exiting")
}
```

### 将服务器构建为一个包含模板文件的二进制文件
### Build a single binary with templates

可以通过使用 [go-assets](https://github.com/jessevdk/go-assets)，将服务器构建为包含模板的单个二进制文件

```go
func main() {
	r := gin.New()

	t, err := loadTemplate()
	if err != nil {
		panic(err)
	}
	r.SetHTMLTemplate(t)

	r.GET("/", func(c *gin.Context) {
		c.HTML(http.StatusOK, "/html/index.tmpl",nil)
	})
	r.Run(":8080")
}

// loadTemplate loads templates embedded by go-assets-builder
func loadTemplate() (*template.Template, error) {
	t := template.New("")
	for name, file := range Assets.Files {
		if file.IsDir() || !strings.HasSuffix(name, ".tmpl") {
			continue
		}
		h, err := ioutil.ReadAll(file)
		if err != nil {
			return nil, err
		}
		t, err = t.New(name).Parse(string(h))
		if err != nil {
			return nil, err
		}
	}
	return t, nil
}
```

See a complete example in the `https://github.com/gin-gonic/examples/tree/master/assets-in-binary` directory.

### 使用自定义结构绑定表单数据
### Bind form-data request with custom struct

以下使用自定义结构的示例:

```go
type StructA struct {
    FieldA string `form:"field_a"`
}

type StructB struct {
    NestedStruct StructA
    FieldB string `form:"field_b"`
}

type StructC struct {
    NestedStructPointer *StructA
    FieldC string `form:"field_c"`
}

type StructD struct {
    NestedAnonyStruct struct {
        FieldX string `form:"field_x"`
    }
    FieldD string `form:"field_d"`
}

func GetDataB(c *gin.Context) {
    var b StructB
    c.Bind(&b)
    c.JSON(200, gin.H{
        "a": b.NestedStruct,
        "b": b.FieldB,
    })
}

func GetDataC(c *gin.Context) {
    var b StructC
    c.Bind(&b)
    c.JSON(200, gin.H{
        "a": b.NestedStructPointer,
        "c": b.FieldC,
    })
}

func GetDataD(c *gin.Context) {
    var b StructD
    c.Bind(&b)
    c.JSON(200, gin.H{
        "x": b.NestedAnonyStruct,
        "d": b.FieldD,
    })
}

func main() {
    r := gin.Default()
    r.GET("/getb", GetDataB)
    r.GET("/getc", GetDataC)
    r.GET("/getd", GetDataD)

    r.Run()
}
```

Using the command `curl` command result:

```
$ curl "http://localhost:8080/getb?field_a=hello&field_b=world"
{"a":{"FieldA":"hello"},"b":"world"}
$ curl "http://localhost:8080/getc?field_a=hello&field_c=world"
{"a":{"FieldA":"hello"},"c":"world"}
$ curl "http://localhost:8080/getd?field_x=hello&field_d=world"
{"d":"world","x":{"FieldX":"hello"}}
```

### 尝试将 body 绑定到不同的结构中
### Try to bind body into different structs

The normal methods for binding request body consumes `c.Request.Body` and they
cannot be called multiple times.

```go
type formA struct {
  Foo string `json:"foo" xml:"foo" binding:"required"`
}

type formB struct {
  Bar string `json:"bar" xml:"bar" binding:"required"`
}

func SomeHandler(c *gin.Context) {
  objA := formA{}
  objB := formB{}
  // This c.ShouldBind consumes c.Request.Body and it cannot be reused.
  if errA := c.ShouldBind(&objA); errA == nil {
    c.String(http.StatusOK, `the body should be formA`)
  // Always an error is occurred by this because c.Request.Body is EOF now.
  } else if errB := c.ShouldBind(&objB); errB == nil {
    c.String(http.StatusOK, `the body should be formB`)
  } else {
    ...
  }
}
```

For this, you can use `c.ShouldBindBodyWith`.

```go
func SomeHandler(c *gin.Context) {
  objA := formA{}
  objB := formB{}
  // This reads c.Request.Body and stores the result into the context.
  if errA := c.ShouldBindBodyWith(&objA, binding.JSON); errA == nil {
    c.String(http.StatusOK, `the body should be formA`)
  // At this time, it reuses body stored in the context.
  } else if errB := c.ShouldBindBodyWith(&objB, binding.JSON); errB == nil {
    c.String(http.StatusOK, `the body should be formB JSON`)
  // And it can accepts other formats
  } else if errB2 := c.ShouldBindBodyWith(&objB, binding.XML); errB2 == nil {
    c.String(http.StatusOK, `the body should be formB XML`)
  } else {
    ...
  }
}
```

* `c.ShouldBindBodyWith` stores body into the context before binding. This has
a slight impact to performance, so you should not use this method if you are
enough to call binding at once.
* This feature is only needed for some formats -- `JSON`, `XML`, `MsgPack`,
`ProtoBuf`. For other formats, `Query`, `Form`, `FormPost`, `FormMultipart`,
can be called by `c.ShouldBind()` multiple times without any damage to
performance (See [#1341](https://github.com/gin-gonic/gin/pull/1341)).

### http2 server push
### http2 server push

http.Pusher is supported only **go1.8+**. See the [golang blog](https://blog.golang.org/h2push) for detail information.

[embedmd]:# (examples/http-pusher/main.go go)
```go
package main

import (
	"html/template"
	"log"

	"github.com/gin-gonic/gin"
)

var html = template.Must(template.New("https").Parse(`
<html>
<head>
  <title>Https Test</title>
  <script src="/assets/app.js"></script>
</head>
<body>
  <h1 style="color:red;">Welcome, Ginner!</h1>
</body>
</html>
`))

func main() {
	r := gin.Default()
	r.Static("/assets", "./assets")
	r.SetHTMLTemplate(html)

	r.GET("/", func(c *gin.Context) {
		if pusher := c.Writer.Pusher(); pusher != nil {
			// use pusher.Push() to do server push
			if err := pusher.Push("/assets/app.js", nil); err != nil {
				log.Printf("Failed to push: %v", err)
			}
		}
		c.HTML(200, "https", gin.H{
			"status": "success",
		})
	})

	// Listen and Server in https://127.0.0.1:8080
	r.RunTLS(":8080", "./testdata/server.pem", "./testdata/server.key")
}
```

### 定义路由日志记录格式
### Define format for the log of routes

默认路由日志格式如下：
```
[GIN-debug] POST   /foo                      --> main.main.func1 (3 handlers)
[GIN-debug] GET    /bar                      --> main.main.func2 (3 handlers)
[GIN-debug] GET    /status                   --> main.main.func3 (3 handlers)
```

如果你想以给定的格式记录日志（例如 JSON 的键值或其他数据），可以使用 `gin.DebugPrintRputeFunc` 定义格式。下面的例子，我们使用标准日志包记录所有路由，
但你也可以使用符合你需求的其他日志包。

```go
import (
	"log"
	"net/http"

	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	gin.DebugPrintRouteFunc = func(httpMethod, absolutePath, handlerName string, nuHandlers int) {
		log.Printf("endpoint %v %v %v %v\n", httpMethod, absolutePath, handlerName, nuHandlers)
	}

	r.POST("/foo", func(c *gin.Context) {
		c.JSON(http.StatusOK, "foo")
	})

	r.GET("/bar", func(c *gin.Context) {
		c.JSON(http.StatusOK, "bar")
	})

	r.GET("/status", func(c *gin.Context) {
		c.JSON(http.StatusOK, "ok")
	})

	// Listen and Server in http://0.0.0.0:8080
	r.Run()
}
```

### Set and get a cookie
```go
import (
    "fmt"

    "github.com/gin-gonic/gin"
)

func main() {

    router := gin.Default()

    router.GET("/cookie", func(c *gin.Context) {

        cookie, err := c.Cookie("gin_cookie")

        if err != nil {
            cookie = "NotSet"
            c.SetCookie("gin_cookie", "test", 3600, "/", "localhost", false, true)
        }

        fmt.Printf("Cookie value: %s \n", cookie)
    })

    router.Run()
}

```

## Testing

`net/http/httptest`包很适合用于HTTP测试。

```go
package main

func setupRouter() *gin.Engine {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong")
	})
	return r
}

func main() {
	r := setupRouter()
	r.Run(":8080")
}
```

测试代码示例如下：

```go
package main

import (
	"net/http"
	"net/http/httptest"
	"testing"

	"github.com/stretchr/testify/assert"
)

func TestPingRoute(t *testing.T) {
	router := setupRouter()

	w := httptest.NewRecorder()
	req, _ := http.NewRequest("GET", "/ping", nil)
	router.ServeHTTP(w, req)

	assert.Equal(t, 200, w.Code)
	assert.Equal(t, "pong", w.Body.String())
}
```

## Users

Awesome project lists using [Gin](https://github.com/gin-gonic/gin) web framework.

* [drone](https://github.com/drone/drone): Drone is a Continuous Delivery platform built on Docker, written in Go.
* [gorush](https://github.com/appleboy/gorush): A push notification server written in Go.
* [fnproject](https://github.com/fnproject/fn): The container native, cloud agnostic serverless platform.

