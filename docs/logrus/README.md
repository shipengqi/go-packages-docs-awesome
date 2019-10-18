# Logrus 中文文档
Logrus <img src="http://i.imgur.com/hTeVwmJ.png" width="40" height="40" alt=":walrus:" class="emoji" title=":walrus:"/>&nbsp;[![Build Status](https://travis-ci.org/sirupsen/logrus.svg?branch=master)](https://travis-ci.org/sirupsen/logrus)&nbsp;[![GoDoc](https://godoc.org/github.com/sirupsen/logrus?status.svg)](https://godoc.org/github.com/sirupsen/logrus)

Logrus 是一个用于 Go (golang) 的结构化日志模块，完全兼容标准的日志库的 API

**看到奇怪的大小写敏感问题？** 在过去，可能同时导入大写和小写的 Logrus 。由于 Go 包环境的原因，这在社区中引起了一些问题，我们需要一个标准。
一些环境遇到了大写变体的问题，因此决定使用小写变体。
所有使用 `logrus` 的项目都需要使用小写: `github.com/sirupsen/logrus`。任何不是这样的包都应该被更改。

要修复 Glide, 查看 [these comments](https://github.com/sirupsen/logrus/issues/553#issuecomment-306591437).
关于这个问题的深入解释，查看 [this comment](https://github.com/sirupsen/logrus/issues/570#issuecomment-313933276).

在开发中使用漂亮的颜色代码(当附加 TTY 时，否则只是纯文本):

![Colored](http://i.imgur.com/PY7qMwd.png)

使用 `log.SetFormatter(&log.JSONFormatter{})`, 可以使用 `logstash` 或 `Splunk` 轻松解析：

```json
{"animal":"walrus","level":"info","msg":"A group of walrus emerges from the
ocean","size":10,"time":"2014-03-10 19:57:38.562264131 -0400 EDT"}

{"level":"warning","msg":"The group's number increased tremendously!",
"number":122,"omg":true,"time":"2014-03-10 19:57:38.562471297 -0400 EDT"}

{"animal":"walrus","level":"info","msg":"A giant walrus appears!",
"size":10,"time":"2014-03-10 19:57:38.562500591 -0400 EDT"}

{"animal":"walrus","level":"info","msg":"Tremendously sized cow enters the ocean.",
"size":9,"time":"2014-03-10 19:57:38.562527896 -0400 EDT"}

{"level":"fatal","msg":"The ice breaks!","number":100,"omg":true,
"time":"2014-03-10 19:57:38.562543128 -0400 EDT"}
```

使用默认的 `log.SetFormatter(&log.TextFormatter{})` 当没有附加 TTY 时, 输出与 [logfmt](http://godoc.org/github.com/kr/logfmt) 格式兼容:

```text
time="2015-03-26T01:27:38-04:00" level=debug msg="Started observing beach" animal=walrus number=8
time="2015-03-26T01:27:38-04:00" level=info msg="A group of walrus emerges from the ocean" animal=walrus size=10
time="2015-03-26T01:27:38-04:00" level=warning msg="The group's number increased tremendously!" number=122 omg=true
time="2015-03-26T01:27:38-04:00" level=debug msg="Temperature changes" temperature=-4
time="2015-03-26T01:27:38-04:00" level=panic msg="It's over 9000!" animal=orca size=9009
time="2015-03-26T01:27:38-04:00" level=fatal msg="The ice breaks!" err=&{0x2082280c0 map[animal:orca size:9009] 2015-03-26 01:27:38.441574009 -0400 EDT panic It's over 9000!} number=100 omg=true
```
要确保即使在一个 TTY 中，也使用这种 Formatter ，设置 Formatter 如下:

```go
	log.SetFormatter(&log.TextFormatter{
		DisableColors: true,
		FullTimestamp: true,
	})
```

#### Logging Method Name

如果希望添加一个 `method` 字段**记录被调用时的方法**，通过以下方式：
```go
log.SetReportCaller(true)
```
这将添加 `method` 字段，如下所示:

```json
{"animal":"penguin","level":"fatal","method":"github.com/sirupsen/arcticcreatures.migrate","msg":"a penguin swims by",
"time":"2014-03-10 19:57:38.562543129 -0400 EDT"}
```

```text
time="2015-03-26T01:27:38-04:00" level=fatal method=github.com/sirupsen/arcticcreatures.migrate msg="a penguin swims by" animal=penguin
```
请注意，这会增加开销，成本取决于 Go 的版本，在最近的 1.6 和 1.7 版本的测试中，成本在 20% 和 40% 之间。你可以在你的环境中使用 benchmarks 校验:
```
go test -bench=.*CallerTracing
```


#### Case-sensitivity

组织的名称已更改为小写 —— 并且不会再更改回来。如果由于大小写敏感而导致导入冲突，请使用小写导入：`github.com/sirupsen/logrus`。

#### Example

使用 Logrus 最简单的方法是简单的包级导出 log：

```go
package main

import (
  log "github.com/sirupsen/logrus"
)

func main() {
  log.WithFields(log.Fields{
    "animal": "walrus",
  }).Info("A walrus appears")
}
```

注意，它与 stdlib log API 完全兼容，所以你可以用 `log github.com/sirupsen/logrus` 替换所有的 `log` 导入。
你可以定制你想要的一切：

```go
package main

import (
  "os"
  log "github.com/sirupsen/logrus"
)

func init() {
  // 使用 json 格式替代 ASCII.
  log.SetFormatter(&log.JSONFormatter{})

  // 输出到 stdout 而不是默认的 stderr
  // 可以使任意的 io.Writer, 参考下面的 文件 示例
  log.SetOutput(os.Stdout)

  // 仅记录 warning 及以上级别的日志
  log.SetLevel(log.WarnLevel)
}

func main() {
  log.WithFields(log.Fields{
    "animal": "walrus",
    "size":   10,
  }).Info("A group of walrus emerges from the ocean")

  log.WithFields(log.Fields{
    "omg":    true,
    "number": 122,
  }).Warn("The group's number increased tremendously!")

  log.WithFields(log.Fields{
    "omg":    true,
    "number": 100,
  }).Fatal("The ice breaks!")

  // A common pattern is to re-use fields between logging statements by re-using
  // the logrus.Entry returned from WithFields()
  contextLogger := log.WithFields(log.Fields{
    "common": "this is a common field",
    "other": "I also should be logged always",
  })

  contextLogger.Info("I'll be logged with common and other field")
  contextLogger.Info("Me too")
}
```

对于更高级的用法，例如从同一个应用程序记录日志到多个位置，可以创建 `logrus` 的实例:

```go
package main

import (
  "os"
  "github.com/sirupsen/logrus"
)

// New() 函数来创建一个 logrus 的实例
// 你可以有多个示例
var log = logrus.New()

func main() {
  // 为当前 logrus 实例设置消息的输出
  log.Out = os.Stdout

  // 可以设置 logrus 实例的输出到任意 io.writer
  // file, err := os.OpenFile("logrus.log", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0666)
  // if err == nil {
  //  log.Out = file
  // } else {
  //  log.Info("Failed to log to file, using default stderr")
  // }

  log.WithFields(logrus.Fields{
    "animal": "walrus",
    "size":   10,
  }).Info("A group of walrus emerges from the ocean")
}
```

#### Fields

Logrus 不推荐使用冗长的消息来记录运行信息。 推荐使用 Fields 来进行精细化的、结构化的信息记录。例如: 替换 `log.Fatalf("Failed
to send event %s to topic %s with key %d")`, 应该记录更容易发现的：

```go
log.WithFields(log.Fields{
  "event": event,
  "topic": topic,
  "key": key,
}).Fatal("Failed to send event")
```

我们发现，这个 API 迫使你考虑如何产生更有用的日志消息。我们已经经历无数的情况，在日志声明中添加一个字段可以节省很多时间。
`WithFields` 调用不是必须的。

一般来说，使用 Logrus 中的任何 `printf`- family 函数都该被视为应该添加字段的提示，但是，仍然可以使用 Logrus 中的 `printf` - family 函数。

#### Default Fields

通常，将字段*总是*附加到应用程序的日志声明中或应用程序的一部分是有帮助的。例如，你可能希望始终在请求上下文中记录 `request_id` 和 `user_ip`。
而不是在每行都写 `log.WithFields(log.Fields{"request_id": request_id, "user_ip": user_ip})`， 你可以创建一个 `logrus.Entry`:

```go
requestLogger := log.WithFields(log.Fields{"request_id": request_id, "user_ip": user_ip})
requestLogger.Info("something happened on that request") # will log request_id and user_ip
requestLogger.Warn("something not great happened")
```

#### Hooks

你可以添加日志级别的 hooks。例如，向异常跟踪服务发送 `Error`，`Fatal` 和 `Panic`、`info` 到 StatsD 或同时将日志发送到多个位置，例如 syslog。

Logrus [内建的 hooks](https://github.com/sirupsen/logrus/tree/master/hooks)。在 `init` 中添加这些或自定义 hook：

```go
import (
  log "github.com/sirupsen/logrus"
  "gopkg.in/gemnasium/logrus-airbrake-hook.v2" // the package is named "airbrake"
  logrus_syslog "github.com/sirupsen/logrus/hooks/syslog"
  "log/syslog"
)

func init() {

  // Use the Airbrake hook to report errors that have Error severity or above to
  // an exception tracker. You can create custom hooks, see the Hooks section.
  log.AddHook(airbrake.NewHook(123, "xyz", "production"))

  hook, err := logrus_syslog.NewSyslogHook("udp", "localhost:514", syslog.LOG_INFO, "")
  if err != nil {
    log.Error("Unable to connect to local syslog daemon")
  } else {
    log.AddHook(hook)
  }
}
```
注意: Syslog 钩子还支持连接到本地 Syslog(例如。`/dev/log` 或 `/var/run/syslog` 或 `/var/run/log`)。
更多详情查看 [syslog hook README](docs/syslog/README.md).

目前已知的服务 hooks 列表可以在这个 [wiki page](https://github.com/sirupsen/logrus/wiki/Hooks) 中找到。


#### Level logging

Logrus 有七个日志等级: Trace, Debug, Info, Warning, Error, Fatal 和 Panic。

```go
log.Trace("Something very low level.")
log.Debug("Useful debugging information.")
log.Info("Something noteworthy happened!")
log.Warn("You should probably take a look at this.")
log.Error("Something failed but I'm not quitting.")
// 输出日志之后，调用 os.Exit(1)
log.Fatal("Bye.")
// 输出日志之后，调用 panic()
log.Panic("I'm bailing.")
```

你可以为一个 `Logger` 设置一个日志等级，然后它只会记录对应等级或以上的条目：

```go
// 默认情况下会记录 info 及以上等级 (warn, error, fatal, panic) 的日志
log.SetLevel(log.InfoLevel)
```

设置 `log.Level = logrus.DebugLevel` 在 debug 或 verbose 下是非常有用的。

#### Entries

除了使用 `WithField` 或 `WithFields` 添加字段外，有些字段还会自动添加到所有日志事件中：

1. `time`。创建条目时的时间戳。
2. `msg`。在 `AddFields` 调用之后，日志消息传递给 `Info,Warn,Error,Fatal,Panic}`。
   例如 `Failed to send event.`
3. `level`。日志等级。例如 `info`。

#### Environments

Logrus 没有环境的概念。

如果你希望 hooks 和 formatters 只在特定的环境中使用，你需要自己处理它。
例如，如果你的应有有一个全局变量 `Environment`，这是一个表示环境的字符串，你可以:

```go
import (
  log "github.com/sirupsen/logrus"
)

init() {
  // do something here to set environment depending on an environment variable
  // or command-line flag
  if Environment == "production" {
    log.SetFormatter(&log.JSONFormatter{})
  } else {
    // The TextFormatter is default, you don't actually have to do this.
    log.SetFormatter(&log.TextFormatter{})
  }
}
```

这个配置就是如何使用 `logrus`，但是，JSON 在生产中通常只在使用诸如 Splunk或Logstash 此类的工具进行日
志聚合时才有用。

#### Formatters

内建的 formatters:

* `logrus.TextFormatter`。如果 stdout 是一个 TTY，则使用颜色记录事件，否则没有颜色。
  * *Note:* 在没有 TTY 是强制使用带颜色的输出，设置 `ForceColors` 字段为 `true`。
    在 TTY 中强制不使用带颜色的输出，设置 `DisableColors` 字段为 `true`。
    对于 windows，查看 [github.com/mattn/go-colorable](https://github.com/mattn/go-colorable)。
  * 启用 colors 后，默认情况下 levels 被截断为 4 个字符。 禁用截断设置 `DisableLevelTruncation`
    字段为 `true`。在所有层的宽度都相同的情况下，直观地扫描列通常是有帮助的。
  * 当输出到 TTY 时, 所有 levels 的宽度都相同对于直观地扫描列通常是有帮助的。设置 `PadLevelText` 字段
    为 `true`, 通过向水平文本添加填充，来启用该行为。
  * [generated docs](https://godoc.org/github.com/sirupsen/logrus#TextFormatter) 列出了所有选项。
* `logrus.JSONFormatter`. 以 JSON 格式记录。
  * [generated docs](https://godoc.org/github.com/sirupsen/logrus#JSONFormatter) 列出了所有选项。

第三方的 formatters:

* [`FluentdFormatter`](https://github.com/joonix/log). 格式化条目以便 Kubernetes 和 Google Container 引擎解析。
* [`GELF`](https://github.com/fabienm/go-logrus-formatters). 格式化条目，使它们符合 Graylog 的格式 [GELF 1.1 specification](http://docs.graylog.org/en/2.4/pages/gelf.html)。
* [`logstash`](https://github.com/bshuster-repo/logrus-logstash-hook). 将字段记录为 [Logstash](http://logstash.net) Envents.
* [`prefixed`](https://github.com/x-cray/logrus-prefixed-formatter). 显示日志条目源。
* [`zalgo`](https://github.com/aybabtme/logzalgo)
* [`nested-logrus-formatter`](https://github.com/antonfisher/nested-logrus-formatter). 将 logrus 字段转换为嵌套结构。

你可以通过实现 `Formatter` interface 来定义自己的 Formatter，需要提供 `Format` 方法。
`Format`接受一个 `*Entry`。`entry.Data` 是一个 `Fields` 类型 (`map[string]interface{}`) 包含所有字段和默认字段(参见上面的 Entries 部分):

```go
type MyJSONFormatter struct {
}

log.SetFormatter(new(MyJSONFormatter))

func (f *MyJSONFormatter) Format(entry *Entry) ([]byte, error) {
  // Note this doesn't include Time, Level and Message which are available on
  // the Entry. Consult `godoc` on information about those fields or read the
  // source of the official loggers.
  serialized, err := json.Marshal(entry.Data)
    if err != nil {
      return nil, fmt.Errorf("Failed to marshal fields to JSON, %v", err)
    }
  return append(serialized, '\n'), nil
}
```

#### Logger as an `io.Writer`

Logrus 可以转换成一个 `io.Writer`。 writer 是一个 `io.Pipe` 的结束，并且你需要关闭它。

```go
w := logger.Writer()
defer w.Close()

srv := http.Server{
    // create a stdlib log.Logger that writes to
    // logrus.Logger.
    ErrorLog: log.New(w, "", 0),
}
```

被写入 writer 的每一行会以一般的方式打印出来，使用 formatters 和 hooks。这些条目的级别是 `info`。

这意味着我们可以轻易的覆盖标准库的 log:

```go
logger := logrus.New()
logger.Formatter = &logrus.JSONFormatter{}

// Use logrus for standard log output
// Note that `log` here references stdlib's log
// Not logrus imported under the name `log`.
log.SetOutput(logger.Writer())
```

#### Rotation

Logrus 并没有提供 Log rotation。Log rotation 需要一个外部的程序完成 (比如 `logrotate(8)`) 可以压缩和删除旧日志条目。
它不应该是应用程序级 logger 的特性。

#### Tools

| Tool | Description |
| ---- | ----------- |
|[Logrus Mate](https://github.com/gogap/logrus_mate)|Logrus mate is a tool for Logrus to manage loggers, you can initial logger's level, hook and formatter by config file, the logger will generated with different config at different environment.|
|[Logrus Viper Helper](https://github.com/heirko/go-contrib/tree/master/logrusHelper)|An Helper around Logrus to wrap with spf13/Viper to load configuration with fangs! And to simplify Logrus configuration use some behavior of [Logrus Mate](https://github.com/gogap/logrus_mate). [sample](https://github.com/heirko/iris-contrib/blob/master/middleware/logrus-logger/example) |

#### Testing

Logrus 有一个内置的工具，用于断言日志消息的存在。这是通过 `test` hook 实现的，并提供:

* 现有的 logger 的装饰器 (`test.NewLocal` 和 `test.NewGlobal`) 只是添加 `test` hook
* 一个测试的 logger (`test.NewNullLogger`) 只记录日志消息 (并且不做任何输出):

```go
import(
  "github.com/sirupsen/logrus"
  "github.com/sirupsen/logrus/hooks/test"
  "github.com/stretchr/testify/assert"
  "testing"
)

func TestSomething(t*testing.T){
  logger, hook := test.NewNullLogger()
  logger.Error("Helloerror")

  assert.Equal(t, 1, len(hook.Entries))
  assert.Equal(t, logrus.ErrorLevel, hook.LastEntry().Level)
  assert.Equal(t, "Helloerror", hook.LastEntry().Message)

  hook.Reset()
  assert.Nil(t, hook.LastEntry())
}
```

#### Fatal handlers

Logrus 可以注册一个或多个函数，这些函数将在 `fatal` 级别的日志记录时时被调用。
注册的函数会在 logrus 执行  `os.Exit(1)` 之前调用。调用者需要优雅的 shutdown 时很有用。
与调用一个 `panic("Something went wrong...")` 不同， `panic("Something went wrong...")` 可以通过延迟的 `recover` 拦截，对 `os.Exit(1)` 的调用，不能拦截。
```go
...
handler := func() {
  // gracefully shutdown something...
}
logrus.RegisterExitHandler(handler)
...
```

#### Thread safety

默认情况下，Logger 受以个 mutex 的保护，用于并发写操作。这个 mutex 在调用 hook 和写入日志时被持有。
如果确定不需要这样的锁，可以调用 `log .SetNoLock()` 来禁用锁。

不需要锁的情况:

* 你没有注册 hooks，或者调用 hook 已经是线程安全的。

* 写入 `logger.Out` 已经是线程安全的, 例如:

  1) `logger.Out` 已经别锁保护。

  2) `logger.Out` 是一个通过标志 `O_APPEND` 打开的 `os.File` handler, 并且 每次写入都小于 4k. (这允许 多线程/多进程 写)

     (参考 http://www.notthewizard.com/2014/06/17/are-files-appends-really-atomic/)
