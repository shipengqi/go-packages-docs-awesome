<p align="center">
<h1 align="center">Resty 中文文档</h1>
<p align="center">Go 的简单的 HTTP 和 REST 客户端库 (灵感来源于 Ruby 的 rest-client)</p>
<p align="center"><a href="#features">Features</a> 详细描述了 Resty 的功能</p>
</p>
<p align="center">
<p align="center">
<a href="https://travis-ci.org/go-resty/resty">
<img src="https://travis-ci.org/go-resty/resty.svg?branch=master" alt="Build Status">
</a> 
<a href="https://codecov.io/gh/go-resty/resty/branch/master">
<img src="https://codecov.io/gh/go-resty/resty/branch/master/graph/badge.svg" alt="Code Coverage">
</a> 
<a href="https://goreportcard.com/report/go-resty/resty">
<img src="https://goreportcard.com/badge/go-resty/resty" alt="Go Report Card">
</a> 
<a href="https://github.com/go-resty/resty/releases/latest">
<img src="https://img.shields.io/badge/version-2.0.0-blue.svg" alt="Release Version">
</a> 
<a href="https://godoc.org/github.com/go-resty/resty">
<img src="https://godoc.org/github.com/go-resty/resty?status.svg" alt="GoDoc">
</a> 
<a href="LICENSE"><img src="https://img.shields.io/github/license/go-resty/resty.svg" alt="License">
</a>
</p>
</p>
<p align="center">
<h4 align="center">Resty Communication Channels</h4>
<p align="center">
<a href="https://gitter.im/go_resty/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge">
<img src="https://badges.gitter.im/go_resty/community.svg" alt="Chat on Gitter - Resty Community"
></a> 
<a href="https://twitter.com/go_resty">
<img src="https://img.shields.io/badge/twitter-@go__resty-55acee.svg" alt="Twitter @go_resty">
</a>
</p>
</p>

## News

  * v2.0.0 [released](https://github.com/go-resty/resty/releases/tag/v2.0.0) and tagged on Jul 16, 2019.
  * v1.12.0 [released](https://github.com/go-resty/resty/releases/tag/v1.12.0) and tagged on Feb 27, 2019.  
  * v1.0 released and tagged on Sep 25, 2017. - Resty's first version was released on Sep 15, 2015 then it grew 
  gradually as a very handy and helpful library. Its been a two years since first release. I'm very thankful to Resty 
  users and its [contributors](https://github.com/go-resty/resty/graphs/contributors).

## Features

  * GET, POST, PUT, DELETE, HEAD, PATCH, OPTIONS, etc.
  * Simple and chainable methods for settings and request
  * Request Body can be `string`, `[]byte`, `struct`, `map`, `slice` and `io.Reader` too
    * Auto detects `Content-Type`
    * Buffer less processing for `io.Reader`
  * [Response](https://godoc.org/github.com/go-resty/resty#Response) object gives you more possibility
    * Access as `[]byte` array - `response.Body()` OR Access as `string` - `response.String()`
    * Know your `response.Time()` and when we `response.ReceivedAt()`
  * Automatic marshal and unmarshal for `JSON` and `XML` content type
    * Default is `JSON`, if you supply `struct/map` without header `Content-Type`
    * For auto-unmarshal, refer to -
        - Success scenario [Request.SetResult()](https://godoc.org/github.com/go-resty/resty#Request.SetResult) 
        and [Response.Result()](https://godoc.org/github.com/go-resty/resty#Response.Result).
        - Error scenario [Request.SetError()](https://godoc.org/github.com/go-resty/resty#Request.SetError) 
        and [Response.Error()](https://godoc.org/github.com/go-resty/resty#Response.Error).
        - Supports [RFC7807](https://tools.ietf.org/html/rfc7807) - `application/problem+json` & `application/problem+xml`
  * Easy to upload one or more file(s) via `multipart/form-data`
    * Auto detects file content type
  * Request URL [Path Params (aka URI Params)](https://godoc.org/github.com/go-resty/resty#Request.SetPathParams)
  * Backoff Retry Mechanism with retry condition function [reference](retry_test.go)
  * Resty client HTTP & REST [Request](https://godoc.org/github.com/go-resty/resty#Client.OnBeforeRequest) 
  and [Response](https://godoc.org/github.com/go-resty/resty#Client.OnAfterResponse) middlewares
  * `Request.SetContext` supported
  * Authorization option of `BasicAuth` and `Bearer` token
  * Set request `ContentLength` value for all request or particular request
  * Custom [Root Certificates](https://godoc.org/github.com/go-resty/resty#Client.SetRootCertificate) and 
  Client [Certificates](https://godoc.org/github.com/go-resty/resty#Client.SetCertificates)
  * Download/Save HTTP response directly into File, like `curl -o` flag. 
  See [SetOutputDirectory](https://godoc.org/github.com/go-resty/resty#Client.SetOutputDirectory) & 
  [SetOutput](https://godoc.org/github.com/go-resty/resty#Request.SetOutput).
  * Cookies for your request and CookieJar support
  * SRV Record based request instead of Host URL
  * Client settings like `Timeout`, `RedirectPolicy`, `Proxy`, `TLSClientConfig`, `Transport`, etc.
  * Optionally allows GET request with payload, 
  see [SetAllowGetMethodPayload](https://godoc.org/github.com/go-resty/resty#Client.SetAllowGetMethodPayload)
  * Supports registering external JSON library into resty, 
  see [how to use](https://github.com/go-resty/resty/issues/76#issuecomment-314015250)
  * Exposes Response reader without reading response (no auto-unmarshaling) if need be, 
  see [how to use](https://github.com/go-resty/resty/issues/87#issuecomment-322100604)
  * Option to specify expected `Content-Type` when response `Content-Type` header missing. 
  Refer to [#92](https://github.com/go-resty/resty/issues/92)
  * Resty design
    * Have client level settings & options and also override at Request level if you want to
    * Request and Response middlewares
    * Create Multiple clients if you want to `resty.New()`
    * Supports `http.RoundTripper` implementation, 
    see [SetTransport](https://godoc.org/github.com/go-resty/resty#Client.SetTransport)
    * goroutine concurrent safe
    * Resty Client trace, see [Client.EnableTrace](https://godoc.org/github.com/go-resty/resty#Client.EnableTrace) 
    and [Request.EnableTrace](https://godoc.org/github.com/go-resty/resty#Request.EnableTrace)
    * Debug mode - clean and informative logging presentation
    * Gzip - Go does it automatically also resty has fallback handling too
    * Works fine with `HTTP/2` and `HTTP/1.1`
  * [Bazel support](#bazel-support)
  * Easily mock Resty for testing, [for e.g.](#mocking-http-requests-using-httpmock-library)
  * Well tested client library

### Included Batteries

  * 重定向策略 - 如何使用查看 [这里](#redirect-policy)
    * NoRedirectPolicy
    * FlexibleRedirectPolicy
    * DomainCheckRedirectPolicy
    * 等等。 [更多信息](redirect.go)
  * 重试机制，如何使用查看 [这里](#retries)
    * Backoff Retry
    * Conditional Retry
  * SRV Record based request instead of Host URL [how to use](resty_test.go#L1412)
  * etc (upcoming - throw your idea's [here](https://github.com/go-resty/resty/issues)).


#### Supported Go Versions

最初，Resty 从 `v1.10.0` 版本开始支持`go modules`。 

使用 Resty v2 和更高的版本, 它完全包含 [go modules](https://github.com/golang/go/wiki/Modules) 
包版本.。

它需要一个能够理解 `/vN` 后缀导入的 Go 版本：
- 1.9.7+
- 1.10.3+
- 1.11+


## It might be beneficial for your project :smile:

Resty 的作者还为 Go 发布了以下项目。

  * [aah framework](https://aahframework.org) - 一个安全、灵活、快速的 Go web 框架。
  * [THUMBAI](https://thumbai.app) - Go Mod 仓库, Go Vanity 服务和简单的代理服务器。
  * [go-model](https://github.com/jeevatkm/go-model) - 健壮的，易于使用的 Go `struct` 模型映射器和工具方法。 


## Installation

```bash
# Go Modules
require github.com/go-resty/resty/v2 v2.0.0
```

## Usage

下面的示例会帮助你熟悉 resty 库。
```go
// 导入 resty
import "github.com/go-resty/resty/v2"
```

#### Simple GET

```go
// 创建一个 Resty Client
client := resty.New()

resp, err := client.R().
		EnableTrace().
		Get("https://httpbin.org/get")

// Explore response object
fmt.Println("Response Info:")
fmt.Println("Error      :", err)
fmt.Println("Status Code:", resp.StatusCode())
fmt.Println("Status     :", resp.Status())
fmt.Println("Time       :", resp.Time())
fmt.Println("Received At:", resp.ReceivedAt())
fmt.Println("Body       :\n", resp)
fmt.Println()

// Explore trace info
fmt.Println("Request Trace Info:")
ti := resp.Request.TraceInfo()
fmt.Println("DNSLookup    :", ti.DNSLookup)
fmt.Println("ConnTime     :", ti.ConnTime)
fmt.Println("TLSHandshake :", ti.TLSHandshake)
fmt.Println("ServerTime   :", ti.ServerTime)
fmt.Println("ResponseTime :", ti.ResponseTime)
fmt.Println("TotalTime    :", ti.TotalTime)
fmt.Println("IsConnReused :", ti.IsConnReused)
fmt.Println("IsConnWasIdle:", ti.IsConnWasIdle)
fmt.Println("ConnIdleTime :", ti.ConnIdleTime)

/* Output
Response Info:
Error      : <nil>
Status Code: 200
Status     : 200 OK
Time       : 465.301137ms
Received At: 2019-03-10 01:52:33.772456 -0800 PST m=+0.466672260
Body       :
 {
  "args": {},
  "headers": {
    "Accept-Encoding": "gzip",
    "Host": "httpbin.org",
    "User-Agent": "go-resty/2.0.0-rc.1 (https://github.com/go-resty/resty)"
  },
  "origin": "0.0.0.0",
  "url": "https://httpbin.org/get"
}

Request Trace Info:
DNSLookup    : 2.21124ms
ConnTime     : 393.875795ms
TLSHandshake : 319.313546ms
ServerTime   : 71.109256ms
ResponseTime : 94.466µs
TotalTime    : 465.301137ms
IsConnReused : false
IsConnWasIdle: false
ConnIdleTime : 0s
*/
```

#### Enhanced GET

```go
// 创建一个 Resty Client
client := resty.New()

resp, err := client.R().
      SetQueryParams(map[string]string{
          "page_no": "1",
          "limit": "20",
          "sort":"name",
          "order": "asc",
          "random":strconv.FormatInt(time.Now().Unix(), 10),
      }).
      SetHeader("Accept", "application/json").
      SetAuthToken("BC594900518B4F7EAC75BD37F019E08FBC594900518B4F7EAC75BD37F019E08F").
      Get("/search_result")


// 使用 Request.SetQueryString 方法示例
resp, err := client.R().
      SetQueryString("productId=232&template=fresh-sample&cat=resty&source=google&kw=buy a lot more").
      SetHeader("Accept", "application/json").
      SetAuthToken("BC594900518B4F7EAC75BD37F019E08FBC594900518B4F7EAC75BD37F019E08F").
      Get("/show_product")
```

#### Various POST method combinations

```go
// 创建一个 Resty Client
client := resty.New()

// POST JSON string
// 不需要设置 content type, 如果你有 client level 的设置
resp, err := client.R().
      SetHeader("Content-Type", "application/json").
      SetBody(`{"username":"testuser", "password":"testpass"}`).
      SetResult(&AuthSuccess{}).    // or SetResult(AuthSuccess{}).
      Post("https://myapp.com/login")

// POST []byte array
// 不需要设置 content type, 如果你有 client level 的设置
resp, err := client.R().
      SetHeader("Content-Type", "application/json").
      SetBody([]byte(`{"username":"testuser", "password":"testpass"}`)).
      SetResult(&AuthSuccess{}).    // or SetResult(AuthSuccess{}).
      Post("https://myapp.com/login")

// POST Struct, 默认为 JSON content type。不需要设置
resp, err := client.R().
      SetBody(User{Username: "testuser", Password: "testpass"}).
      SetResult(&AuthSuccess{}).    // or SetResult(AuthSuccess{}).
      SetError(&AuthError{}).       // or SetError(AuthError{}).
      Post("https://myapp.com/login")

// POST Map, 默认为 JSON content type。不需要设置
resp, err := client.R().
      SetBody(map[string]interface{}{"username": "testuser", "password": "testpass"}).
      SetResult(&AuthSuccess{}).    // or SetResult(AuthSuccess{}).
      SetError(&AuthError{}).       // or SetError(AuthError{}).
      Post("https://myapp.com/login")

// POST of raw bytes for file upload. For example: upload file to Dropbox
fileBytes, _ := ioutil.ReadFile("/Users/jeeva/mydocument.pdf")

// 这里我们没有设置 ，因为 go-resty 会自动为你检测 content-type
resp, err := client.R().
      SetBody(fileBytes).
      SetContentLength(true).          // Dropbox expects this value
      SetAuthToken("<your-auth-token>").
      SetError(&DropboxError{}).       // or SetError(DropboxError{}).
      Post("https://content.dropboxapi.com/1/files_put/auto/resty/mydocument.pdf") // for upload Dropbox supports PUT too

// 注意: 如果 content-type 的 header, resty 检测请求 body/payload 的 Content-Type.
//   * 对于 struct 和 map 数据类型默认为 'application/json'
//   * Fallback is plain text content type
```

#### Sample PUT

你可以像使用 `POST` 一样，使用各种组合调用 `PUT` 方法。
```go
// 注意: 这是 PUT 方法使用的一个示例，更多的组合请参考 POST

// 创建一个 Resty Client
client := resty.New()

// Request goes as JSON content type
// 不需要设置 auth token, error, 如果你有 client level 的设置
resp, err := client.R().
      SetBody(Article{
        Title: "go-resty",
        Content: "This is my article content, oh ya!",
        Author: "Jeevanandam M",
        Tags: []string{"article", "sample", "resty"},
      }).
      SetAuthToken("C6A79608-782F-4ED0-A11D-BD82FAD829CD").
      SetError(&Error{}).       // or SetError(Error{}).
      Put("https://myapp.com/article/1234")
```

#### Sample PATCH

你可以像使用 `POST` 一样，使用各种组合调用 `PATCH` 方法。
```go
// 注意: 这是 PATCH 方法使用的一个示例，更多的组合请参考 POST

// 创建一个 Resty Client
client := resty.New()

// Request goes as JSON content type
// 不需要设置 auth token, error, 如果你有 client level 的设置
resp, err := client.R().
      SetBody(Article{
        Tags: []string{"new tag1", "new tag2"},
      }).
      SetAuthToken("C6A79608-782F-4ED0-A11D-BD82FAD829CD").
      SetError(&Error{}).       // or SetError(Error{}).
      Patch("https://myapp.com/articles/1234")
```

#### Sample DELETE, HEAD, OPTIONS

```go
// 创建一个 Resty Client
client := resty.New()

// DELETE a article
// 不需要设置 auth token, error, 如果你有 client level 的设置
resp, err := client.R().
      SetAuthToken("C6A79608-782F-4ED0-A11D-BD82FAD829CD").
      SetError(&Error{}).       // or SetError(Error{}).
      Delete("https://myapp.com/articles/1234")

// DELETE a articles with payload/body as a JSON string
// 不需要设置 auth token, error, 如果你有 client level 的设置
resp, err := client.R().
      SetAuthToken("C6A79608-782F-4ED0-A11D-BD82FAD829CD").
      SetError(&Error{}).       // or SetError(Error{}).
      SetHeader("Content-Type", "application/json").
      SetBody(`{article_ids: [1002, 1006, 1007, 87683, 45432] }`).
      Delete("https://myapp.com/articles")

// HEAD of resource
// 不需要设置 auth token, error, 如果你有 client level 的设置
resp, err := client.R().
      SetAuthToken("C6A79608-782F-4ED0-A11D-BD82FAD829CD").
      Head("https://myapp.com/videos/hi-res-video")

// OPTIONS of resource
// 不需要设置 auth token, error, 如果你有 client level 的设置
resp, err := client.R().
      SetAuthToken("C6A79608-782F-4ED0-A11D-BD82FAD829CD").
      Options("https://myapp.com/servers/nyc-dc-01")
```

### Multipart File(s) upload

#### Using io.Reader

```go
profileImgBytes, _ := ioutil.ReadFile("/Users/jeeva/test-img.png")
notesBytes, _ := ioutil.ReadFile("/Users/jeeva/text-file.txt")

// 创建一个 Resty Client
client := resty.New()

resp, err := client.R().
      SetFileReader("profile_img", "test-img.png", bytes.NewReader(profileImgBytes)).
      SetFileReader("notes", "text-file.txt", bytes.NewReader(notesBytes)).
      SetFormData(map[string]string{
          "first_name": "Jeevanandam",
          "last_name": "M",
      }).
      Post("http://myapp.com/upload")
```

#### Using File directly from Path

```go
// 创建一个 Resty Client
client := resty.New()

// 上传一个文件
resp, err := client.R().
      SetFile("profile_img", "/Users/jeeva/test-img.png").
      Post("http://myapp.com/upload")

// 上传多个文件
resp, err := client.R().
      SetFiles(map[string]string{
        "profile_img": "/Users/jeeva/test-img.png",
        "notes": "/Users/jeeva/text-file.txt",
      }).
      Post("http://myapp.com/upload")

// Multipart of form fields and files
resp, err := client.R().
      SetFiles(map[string]string{
        "profile_img": "/Users/jeeva/test-img.png",
        "notes": "/Users/jeeva/text-file.txt",
      }).
      SetFormData(map[string]string{
        "first_name": "Jeevanandam",
        "last_name": "M",
        "zip_code": "00001",
        "city": "my city",
        "access_token": "C6A79608-782F-4ED0-A11D-BD82FAD829CD",
      }).
      Post("http://myapp.com/profile")
```

#### Sample Form submission

```go
// 创建一个 Resty Client
client := resty.New()

// 以 POST 作为一个简单的例子
// User Login
resp, err := client.R().
      SetFormData(map[string]string{
        "username": "jeeva",
        "password": "mypass",
      }).
      Post("http://myapp.com/login")

// Followed by profile update
resp, err := client.R().
      SetFormData(map[string]string{
        "first_name": "Jeevanandam",
        "last_name": "M",
        "zip_code": "00001",
        "city": "new city update",
      }).
      Post("http://myapp.com/profile")

// Multi value form data
criteria := url.Values{
  "search_criteria": []string{"book", "glass", "pencil"},
}
resp, err := client.R().
      SetFormDataFromValues(criteria).
      Post("http://myapp.com/search")
```

#### Save HTTP Response into File

```go
// 创建一个 Resty Client
client := resty.New()

// 设置要输出的目录路径, 如果目录不存在，go-resty 会创建
// 这是一个可选项, 如果你计划在 `Request.SetOutput` 中使用绝对路径，可以一起使用
client.SetOutputDirectory("/Users/jeeva/Downloads")

// HTTP 响应保存到文件中, 类似于 curl 的 -o 标志
_, err := client.R().
          SetOutput("plugin/ReplyWithHeader-v5.1-beta.zip").
          Get("http://bit.ly/1LouEKr")

// 或者使用绝对路径
// 注意: 输出目录路径不能用于绝对路径
_, err := client.R().
          SetOutput("/MyDownloads/plugin/ReplyWithHeader-v5.1-beta.zip").
          Get("http://bit.ly/1LouEKr")
```

#### Request URL Path Params

Resty 提供了易于使用的动态请求 URL 路径参数。可以在 client 和 request level 设置参数。
request level 设置的参数会覆盖 client level 的设置的相同参数的值。

```go
// 创建一个 Resty Client
client := resty.New()

client.R().SetPathParams(map[string]string{
   "userId": "sample@sample.com",
   "subAccountId": "100002",
}).
Get("/v1/users/{userId}/{subAccountId}/details")

// Result:
//   组成的 URL - /v1/users/sample@sample.com/100002/details
```

#### Request and Response Middleware

Resty 提供了处理请求和响应的中间件功能。它比回调函数更灵活。

```go
// 创建一个 Resty Client
client := resty.New()

// 注册 Request 中间件
client.OnBeforeRequest(func(c *resty.Client, req *resty.Request) error {
    // 现在你可以访问当前 Client 和 Request 对象
    // 根据你的需要操作它们 

    return nil  // 成功返回 nil，否则返回 error
  })

// 注册 Response 中间件
client.OnAfterResponse(func(c *resty.Client, resp *resty.Response) error {
    // Now you have access to Client and current Response object
    // manipulate it as per your need

    return nil  // if its success otherwise return error
  })
```

#### Redirect Policy

Resty 提供了一些可以使用重定向策略的选项，而且它支持同时使用多个策略。

```go
// 创建一个 Resty Client
client := resty.New()

// 设置 Client 的 Redirect Policy. 根据需要创建一个
client.SetRedirectPolicy(resty.FlexibleRedirectPolicy(15))

// 想要多个策略，如重定向计数，域名检查等
client.SetRedirectPolicy(resty.FlexibleRedirectPolicy(20),
                        resty.DomainCheckRedirectPolicy("host1.com", "host2.org", "host3.net"))
```

##### Custom Redirect Policy

实现 [RedirectPolicy](redirect.go#L20) 接口并且使用 resty client 注册它. 更多内容查看 [redirect.go](redirect.go)。

```go
// 创建一个 Resty Client
client := resty.New()

// 在 resty.SetRedirectPolicy 使用原始函数
client.SetRedirectPolicy(resty.RedirectPolicyFunc(func(req *http.Request, via []*http.Request) error {
  // 这里实现你的逻辑

  // 继续重定向返回 nil ，否则返回 error 以停止/阻止重定向
  return nil
}))

//---------------------------------------------------

// 使用 struct 创建更多的灵活的 redirect policy
type CustomRedirectPolicy struct {
  // variables goes here
}

func (c *CustomRedirectPolicy) Apply(req *http.Request, via []*http.Request) error {
  // 这里实现你的逻辑

  // 继续重定向返回 nil ，否则返回 error 以停止/阻止重定向
  return nil
}

// 在 resty 里注册
client.SetRedirectPolicy(CustomRedirectPolicy{/* initialize variables */})
```

#### Custom Root Certificates and Client Certificates

```go
// 创建一个 Resty Client
client := resty.New()

// 自定义的 Root 证书, 只需要提供 .pem 文件
// 你可以添加一个或多个 root 证书
client.SetRootCertificate("/path/to/root/pemFile1.pem")
client.SetRootCertificate("/path/to/root/pemFile2.pem")
// ... and so on!

// 添加 Client 证书, 你可以添加一个或多个证书
// 创建 certificate 对象的示例
// 从一对文件中解析 public/private key. 这些文件必须包含P EM 编码的数据。
cert1, err := tls.LoadX509KeyPair("certs/client.pem", "certs/client.key")
if err != nil {
  log.Fatalf("ERROR client certificate: %s", err)
}
// ...

// 添加一个或多个证书
client.SetCertificates(cert1, cert2, cert3)
```

#### Proxy Settings - Client as well as at Request Level

默认情况下 `Go` 支持从环境变量 `HTTP_PROXY` 获取代理。Resty 提供了 `SetProxy` & `RemoveProxy`，
根据你的需要来选择代理。

**Client Level Proxy** 应用于所有请求的设置

```go
// 创建一个 Resty Client
client := resty.New()

// 设置一个 Proxy URL 和 Port
client.SetProxy("http://proxyserver:8888")

// 移除 proxy 设置
client.RemoveProxy()
```

#### Retries

Resty 使用 [backoff](http://www.awsarchitectureblog.com/2015/03/backoff.html) 来增加每次尝试后的重试间隔。

使用示例:

```go
// 创建一个 Resty Client
client := resty.New()

// 每个 client 配置重试
client.
    // 将重试计数设置为 非零 以启用重试
    SetRetryCount(3).
    // 你可以覆盖初始的重试等待时间。
    // 默认 100 毫秒.
    SetRetryWaitTime(5 * time.Second).
    // MaxWaitTime 也可以被覆盖.
    // 默认是 2 秒.
    SetRetryMaxWaitTime(20 * time.Second).
    // SetRetryAfter 设置回调函数来计算重试之间的等待时间。
    // Default (nil) implies exponential backoff with jitter
    SetRetryAfter(func(client *Client, resp *Response) (time.Duration, error) {
        return 0, errors.New("quota exceeded")
    })
```

上面的设置，当请求返回非 nil 的 error 时，resty 会重试请求 3 次。

可以选择为 client 提供自定义的重试条件：
```go
// 创建一个 Resty Client
client := resty.New()

client.AddRetryCondition(
    // RetryConditionFunc type is for retry condition function
	  // input: non-nil Response OR request execution error
    func(r *resty.Response, err error) bool {
        return r.StatusCode() == http.StatusTooManyRequests
    },
)
```

上面的例子，如果请求以 `429 Too Many requests` 状态码结束，resty 会重试请求。

可以添加多个重试条件。

还可以使用 `resty.Backoff(...)` 获得任意重试场景的实现。[Reference](retry_test.go)。

#### Allow GET request with Payload

```go
// 创建一个 Resty Client
client := resty.New()

// 允许带有 Payload 的 GET 请求。默认是 disabled 的。
client.SetAllowGetMethodPayload(true)
```

#### Wanna Multiple Clients

```go
// Here you go!
// Client 1
client1 := resty.New()
client1.R().Get("http://httpbin.org")
// ...

// Client 2
client2 := resty.New()
client2.R().Head("http://httpbin.org")
// ...

// Bend it as per your need!!!
```

#### Remaining Client Settings & its Options

```go
// 创建一个 Resty Client
client := resty.New()

// Client level 的唯一设置
//--------------------------------
// 启用 debug 模式
client.SetDebug(true)

// 设置 Client TLSClientConfig
// 设置自定义的 root-certificate. 参考: http://golang.org/pkg/crypto/tls/#example_Dial
client.SetTLSClientConfig(&tls.Config{ RootCAs: roots })

// 或者禁用安全检查 (https)
client.SetTLSClientConfig(&tls.Config{ InsecureSkipVerify: true })

// 根据需要设置 client 超时
client.SetTimeout(1 * time.Minute)


// 如果你想，你可以在 request level 覆盖下面的所有的设置和选项
//--------------------------------------------------------------------------------
// 设置所有请求的 Host URL。就可以在所有请求中使用相对路径
client.SetHostURL("http://httpbin.org")

// 设置所有请求的 Headers
client.SetHeader("Accept", "application/json")
client.SetHeaders(map[string]string{
        "Content-Type": "application/json",
        "User-Agent": "My custom User Agent String",
      })

// 设置所有请求的 Cookies
client.SetCookie(&http.Cookie{
      Name:"go-resty",
      Value:"This is cookie value",
      Path: "/",
      Domain: "sample.com",
      MaxAge: 36000,
      HttpOnly: true,
      Secure: false,
    })
client.SetCookies(cookies)

// 设置所有请求的 URL query parameters
client.SetQueryParam("user_id", "00001")
client.SetQueryParams(map[string]string{ // sample of those who use this manner
      "api_key": "api-key-here",
      "api_secert": "api-secert",
    })
client.R().SetQueryString("productId=232&template=fresh-sample&cat=resty&source=google&kw=buy a lot more")

// 设置所有请求的 Form data. 通常和 POST and PUT 一起
client.SetFormData(map[string]string{
    "access_token": "BC594900-518B-4F7E-AC75-BD37F019E08F",
  })

// 设置所有请求的 Basic Auth
client.SetBasicAuth("myuser", "mypass")

// 设置所有请求的 Bearer Auth Token
client.SetAuthToken("BC594900518B4F7EAC75BD37F019E08FBC594900518B4F7EAC75BD37F019E08F")

// 启用所有请求的 Content length value
client.SetContentLength(true)

// 为 JSON/XML 请求注册全局的 Error 对象结构体
client.SetError(&Error{})    // or resty.SetError(Error{})
```

#### Unix Socket

```go
unixSocket := "/var/run/my_socket.sock"

// 创建一个 Go 原生的 http.Transport ，这样可以在 resty 中设置它.
transport := http.Transport{
	Dial: func(_, _ string) (net.Conn, error) {
		return net.Dial("unix", unixSocket)
	},
}

// 创建一个 Resty Client
client := resty.New()

// 设置之前创建的 transport, 将通信 scheme 设置为套接字，并将 unixSocket 设置为 HostURL。
client.SetTransport(&transport).SetScheme("http").SetHostURL(unixSocket)

// 不需要在请求上写主机的 URL，只需要路径。
client.R().Get("/index.html")
```

#### Bazel support

Resty 可以通过 [Bazel](https://bazel.build) 构建、测试和依赖 .
例如，运行所有 test:

```shell
bazel test :go_default_test
```

#### Mocking http requests using [httpmock](https://github.com/jarcoal/httpmock) library

为了在测试应用程序时模拟 http 请求，可以使用 `httpmock` 库。
当使用默认 resty client, 要把 client 传递给库，像下面这样:

```go
// 创建一个 Resty Client
client := resty.New()

// 获取底层的 HTTP Client，并设置为 Mock
httpmock.ActivateNonDefault(client.GetClient())
```

使用 ginko 模拟 resty http 请求的更详细示例在 [这里](https://github.com/jarcoal/httpmock#ginkgo--resty-example)。

## Versioning

Resty releases versions according to [Semantic Versioning](http://semver.org)

  * Resty v2 does not use `gopkg.in` service for library versioning.
  * Resty fully adapted to `go mod` capabilities since `v1.10.0` release. 
  * Resty v1 series was using `gopkg.in` to provide versioning. `gopkg.in/resty.vX` points to appropriate tagged 
  versions; `X` denotes version series number and it's a stable release for production use. For e.g. `gopkg.in/resty.v0`.
  * Development takes place at the master branch. Although the code in master should always compile and test 
  successfully, it might break API's. I aim to maintain backwards compatibility, but sometimes API's and behavior 
  might be changed to fix a bug.

## Contribution

I would welcome your contribution! If you find any improvement or issue you want to fix, feel free to send a pull 
request, I like pull requests that include test cases for fix/enhancement. I have done my best to bring pretty good 
code coverage. Feel free to write tests.

BTW, I'd like to know what you think about `Resty`. Kindly open an issue or send me an email; it'd mean a lot to me.

## Creator

[Jeevanandam M.](https://github.com/jeevatkm) (jeeva@myjeeva.com)

## Contributors

Have a look on [Contributors](https://github.com/go-resty/resty/graphs/contributors) page.

## License

Resty released under MIT license, refer [LICENSE](LICENSE) file.
