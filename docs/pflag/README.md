# pflag 中文文档
pflag 中文文档，本文档基于 [pflag](https://github.com/spf13/pflag) 官方文档。不定期更新。

[![Build Status](https://travis-ci.org/spf13/pflag.svg?branch=master)](https://travis-ci.org/spf13/pflag)
[![Go Report Card](https://goreportcard.com/badge/github.com/spf13/pflag)](https://goreportcard.com/report/github.com/spf13/pflag)
[![GoDoc](https://godoc.org/github.com/spf13/pflag?status.svg)](https://godoc.org/github.com/spf13/pflag)

## Description

pflag 是 Go flag 包的一个替代品，实现了 `POSIX/GNU-style` `--flags`。

pflag 兼容 [GNU extensions to the POSIX recommendations for command-line options][1].
有关更精确的说明，请参阅下面的 “Command-line flag syntax” 部分。

[1]: http://www.gnu.org/software/libc/manual/html_node/Argument-Syntax.html

pflag 在与 Go 语言相同的 BSD 许可证样式下可用，可以在 LICENSE 文件中找到。

## Installation

pflag 可以通过标准的 `go get` 命令使用。

安装:
```bash
go get github.com/spf13/pflag
```
测试:
```bash
go test github.com/spf13/pflag
```
## Usage

pflag 是 Go 原生 flag 包的一个替代品。如果你以 `flag` 的名称导入 pflag，那么所有代码都应该继续运行，不需要任何更改。

``` go
import flag "github.com/spf13/pflag"
```

这有一个例外：如果直接实例化 Flag struct，则还需要设置一个 `Shorthand` 字段。
大多数代码从不直接实例化此结构，而是使用 `String()`，`BoolVar()` 和 `Var()` 等函数，因此不受影响。

使用 `flag.String()`，`Bool()`，`Int()` 等定义标志。

这里声明了一个整数标志 `-flagname`，存储在指针 `ip` 中，类型为 `*int`。

``` go
var ip *int = flag.Int("flagname", 1234, "help message for flagname")
```

如果愿意，可以使用 `Var()` 函数将标志绑定到变量。

``` go
var flagvar int
func init() {
    flag.IntVar(&flagvar, "flagname", 1234, "help message for flagname")
}
```

或者，你可以创建满足 Value 接口（使用指针接收器）的自定义标志，将它们结合起来标记解析

``` go
flag.Var(&flagVal, "name", "help message for flagname")
```

对于此类标志，默认值是变量的初始值。

定义完所有标志后，调用

``` go
flag.Parse()
```

将命令行解析为定义的标志。

然后可以直接使用标志。如果你直接使用 flag 本身，它们都是指针; 如果你绑定到变量，它们就是值。

``` go
fmt.Println("ip has value ", *ip)
fmt.Println("flagvar has value ", flagvar)
```

如果你有一个 `FlagSet`，但是发现很难跟踪代码中的所有指针，可以使用一些辅助函数来获取存储在标志中的值。
如果你有一个 `pflag.FlagSet`，带有一个名为 `flagname` 的 `int` 类型的标志，你可以使用 `GetInt()` 来获取 `int` 值。 但请注意 `flagname`
必须存在且必须是 `int`。
`GetString("flagname")` 会失败。

``` go
i, err := flagset.GetInt("flagname")
```

解析后，标志后面的参数 `flag.Args()` 可作为 slice 使用，`flag.Arg(i)`可单独使用。
参数下标 `0` 到 `flag.NArg() - 1`。

pflag 包还定义了一些不在 flag 中的新函数，这些函数为 flag 提供了一个字母缩写。你可以通过将`P`附加到定义标志的任意函数名来调用这些函数，
比如 `intVar()` 加上 `P` 就是 `intVarP()`。

``` go
var ip = flag.IntP("flagname", "f", 1234, "help message")
var flagvar bool
func init() {
	flag.BoolVarP(&flagvar, "boolname", "b", true, "help message")
}
flag.VarP(&flagVal, "varname", "v", "help message")
```

简写字母可以在命令行上使用单个 `-`。
布尔简写标志可以与其他简写标志组合使用。

默认的命令行标志集由顶级函数控制。`FlagSet` 类型允许定义独立的标志集，例如在命令行接口中实现子命令。
`FlagSet` 的方法类似于命令行标志集的顶级函数。

## 不为标志设置选项默认值

创建一个 flag 后，可以为这个 flag 设置 `pflag.NoOptDefVal`。这样做会稍微改变标志的含义。
如果一个 flag 具有 `NoOptDefVal`，并且该 flag 在命令行上没有设置这个 flag 的值，则该标志将设置为 `NoOptDefVal` 指定的值。举个例子：

``` go
var ip = flag.IntP("flagname", "f", 1234, "help message")
flag.Lookup("flagname").NoOptDefVal = "4321"
```

会产生下面类似的结果

| Parsed Arguments | Resulting Value |
| -------------    | -------------   |
| --flagname=1357  | ip=1357         |
| --flagname       | ip=4321         |
| [nothing]        | ip=1234         |

## Command line flag syntax

```
--flag    // boolean flags, or flags with no option default values
--flag x  // only on flags without a default value
--flag=x
```

与 flag 包不同，选项前的一个 `-` 与两个 `--` 的意思是不同的。一个 `-` 表示 flag 的简写字母。
除了最后一个简写字母之外，其他所有字母都必须是布尔标志或具有默认值的标志。

```
// boolean or flags where the 'no option default value' is set
-f
-f=true
-abc
but
-b true is INVALID

// non-boolean and flags without a 'no option default value'
-n 1234
-n=1234
-n1234

// mixed
-abcs "hello"
-absd="hello"
-abcs1234
```

结束符 `--` 之后停止标志解析。与 flag 包不同，标志可以在此终结符之前在命令行的任何位置穿插参数。

整数标志接受 `1234`,`0664`,`0x1234` 并且可能是负数。
布尔标志（长格式）接受 `1`，`0`，`t`，`f`，`true`，`false`，`TRUE`，`FALSE`，`True`，`False`。
Duration 标志接受对 `time.ParseDuration` 有效的任何输入。

## Mutating or "Normalizing" Flag names

可以设置一个自定义标志名的 “normalization function”。它允许在代码中创建时以及在命令行上使用某些需要 “normalized” 的形式时，标记名都会发生 normalized。
“normalized” 形式一般用于比较。下面是使用自定义 “normalized” 函数的两个示例。

**例 #1**: 你想要 `-`，`_` 和 `.` 在标志中进行比较是是一样的。比如 `--my-flag == --my_flag == --my.flag`

```go
func wordSepNormalizeFunc(f *pflag.FlagSet, name string) pflag.NormalizedName {
	from := []string{"-", "_"}
	to := "."
	for _, sep := range from {
		name = strings.Replace(name, sep, to, -1)
	}
	return pflag.NormalizedName(name)
}

myFlagSet.SetNormalizeFunc(wordSepNormalizeFunc)
```

**例 #2**: 你想要给两个标志设置别名。例如`--old-flag-name == --new-flag-name`

```go
func aliasNormalizeFunc(f *pflag.FlagSet, name string) pflag.NormalizedName {
	switch name {
	case "old-flag-name":
		name = "new-flag-name"
		break
	}
	return pflag.NormalizedName(name)
}

myFlagSet.SetNormalizeFunc(aliasNormalizeFunc)
```

## Deprecating a flag or its shorthand
可以弃用标志或者标志的简写。弃用的 标志/简写 在帮助文本中会被隐藏，并在使用不推荐的 标志/简写 时打印正确的用法提示。

**例 #1**: 你想要弃用名为 “badflag” 的标志，并告知用户应该使用哪个标志代替。
```go
// deprecate a flag by specifying its name and a usage message
flags.MarkDeprecated("badflag", "please use --good-flag instead")
```

这样隐藏了帮助文本中的 `badflag`，并且当使用 `badflag` 时打印了 `Flag --badflag has been deprecated, please use --good-flag instead`。

**例 #2**: 你希望保留名为 `noshorthandflag` 的标志，但是弃用它的简写 `n`。
```go
// deprecate a flag shorthand by specifying its flag name and a usage message
flags.MarkShorthandDeprecated("noshorthandflag", "please use --noshorthandflag only")
```
这样隐藏了帮助文本中的简写 `n`，并且当使用简写 `n` 时打印了 `Flag shorthand -n has been deprecated, please use --noshorthandflag only`。

**注意，usage message 在此处必不可少，并且不应为空**。

## Hidden flags
可以将 flag 标记为隐藏的，这意味着它仍将正常运行，但不会显示在 usage/help 文本中。


**例**: 你有一个名为 `secretFlag` 的标志，你只需要内部使用，并且不希望它显示在帮助文本中，或者它的使用文本显示。
```go
// hide a flag by specifying its name
flags.MarkHidden("secretFlag")
```

## Disable sorting of flags
`pflag`允许你禁用 flag 的 help 和 usage 的排序。

**例**:
```go
flags.BoolP("verbose", "v", false, "verbose output")
flags.String("coolflag", "yeaah", "it's really cool flag")
flags.Int("usefulflag", 777, "sometimes it's very useful")
flags.SortFlags = false
flags.PrintDefaults()
```
**Output**:
```
  -v, --verbose           verbose output
      --coolflag string   it's really cool flag (default "yeaah")
      --usefulflag int    sometimes it's very useful (default 777)
```


## Supporting Go flags when using pflag
为了支持使用 Go 的 `flag` 包定义的 flag ，必须将它们添加到 `pflag` 标志集中。这通常是支持第三方依赖项定义的标志所必需的(例如 `golang/glog`)。

**例**: 你想要将 Go flags 添加到 `CommandLine` 标志集
```go
import (
	goflag "flag"
	flag "github.com/spf13/pflag"
)

var ip *int = flag.Int("flagname", 1234, "help message for flagname")

func main() {
	flag.CommandLine.AddGoFlagSet(goflag.CommandLine)
	flag.Parse()
}
```

## More info

你可以在 [godoc.org][3] 查看 pflag 包的完整参考文档，或在安装后通过 go 的标准文档系统运行 `godoc -http =:6060` 并浏览器打开
[http//localhost6060/pkg/github.com/spf13/pflag][2]。

[2]: http://localhost:6060/pkg/github.com/spf13/pflag
[3]: http://godoc.org/github.com/spf13/pflag
