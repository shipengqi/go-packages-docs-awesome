# Cobra 中文文档
Cobra 中文文档，本文档基于 [cobra](https://github.com/spf13/cobra) 官方文档。不定期更新。

![cobra logo](https://cloud.githubusercontent.com/assets/173412/10886352/ad566232-814f-11e5-9cd0-aa101788c117.png)

Cobra 既是一个创建强大的现代 CLI 应用程序的库，也是一个生成应用和命令文件的程序。

许多最广泛使用的 Go 项目都是使用 Cobra 构建的，包括:

* [Kubernetes](http://kubernetes.io/)
* [Hugo](http://gohugo.io)
* [rkt](https://github.com/coreos/rkt)
* [etcd](https://github.com/coreos/etcd)
* [Moby (former Docker)](https://github.com/moby/moby)
* [Docker (distribution)](https://github.com/docker/distribution)
* [OpenShift](https://www.openshift.com/)
* [Delve](https://github.com/derekparker/delve)
* [GopherJS](http://www.gopherjs.org/)
* [CockroachDB](http://www.cockroachlabs.com/)
* [Bleve](http://www.blevesearch.com/)
* [ProjectAtomic (enterprise)](http://www.projectatomic.io/)
* [Giant Swarm's gsctl](https://github.com/giantswarm/gsctl)
* [Nanobox](https://github.com/nanobox-io/nanobox)/[Nanopack](https://github.com/nanopack)
* [rclone](http://rclone.org/)
* [nehm](https://github.com/bogem/nehm)
* [Pouch](https://github.com/alibaba/pouch)

[![Build Status](https://travis-ci.org/spf13/cobra.svg "Travis CI status")](https://travis-ci.org/spf13/cobra)
[![CircleCI status](https://circleci.com/gh/spf13/cobra.png?circle-token=:circle-token "CircleCI status")](https://circleci.com/gh/spf13/cobra)
[![GoDoc](https://godoc.org/github.com/spf13/cobra?status.svg)](https://godoc.org/github.com/spf13/cobra)

# Table of Contents

- [Overview](#overview)
- [Concepts](#concepts)
  * [Commands](#commands)
  * [Flags](#flags)
- [Installing](#installing)
- [Getting Started](#getting-started)
  * [Using the Cobra Generator](#using-the-cobra-generator)
  * [Using the Cobra Library](#using-the-cobra-library)
  * [Working with Flags](#working-with-flags)
  * [Positional and Custom Arguments](#positional-and-custom-arguments)
  * [Example](#example)
  * [Help Command](#help-command)
  * [Usage Message](#usage-message)
  * [PreRun and PostRun Hooks](#prerun-and-postrun-hooks)
  * [Suggestions when "unknown command" happens](#suggestions-when-unknown-command-happens)
  * [Generating documentation for your command](#generating-documentation-for-your-command)
  * [Generating bash completions](#generating-bash-completions)
- [Contributing](#contributing)
- [License](#license)

# Overview

Cobra 是一个提供了简单的接口，可以创建类似 `git` 和 `go tools` 的强大的现代 CLI 接口的库。

Cobra 也是一个生成应用脚手架的应用程序，快速开发基于 Cobra 的应用程序。

Cobra 支持:
* 基于子命令的简易 CLI: `app server`, `app fetch` 等。
* 完全符合 POSIX 标准
* 嵌套的子命令
* 全局，本地和级联标志
* `cobra init appname` 和 `cobra add cmdname` 命令轻松生成应用程序和命令
* 智能化建议 (`app srver`... did you mean `app server`?)
* 自动生成命令和标志的 `help`
* 自动帮助标志识别 `-h`，`--help` 等。
* 为你的应用程序自动生成 bash autocomplete
* 为你的应用程序自动生成 man pages
* 命令别名，以便可以在不破坏它们的情况下进行更改
* 灵活的定义你自己的帮助，用法等。
* 可选择与 [viper](http://github.com/spf13/viper)紧密集成，用于 12-factor 应用

# Concepts

Cobra 建立在 **Commands**，**Args** 和 **Flags** 的结构之上。

**Commands** 代表动作，**Args** 代表事物，**Flags** 是这些动作的修饰符。

最好的应用程序在使用时会像句子一样读取。 用户会知道如何使用该应用程序，因为他们将原生地了解如何使用它。

要遵循的模式是 `APPNAME VERB NOUN --ADJECTIVE`。 或 `APPNAME COMMAND ARG --FLAG`

一些好的现实的例子可以更好地说明这一点。

下面的例子，`server` 是一个命令，`port` 是一个标志:
```bash
hugo server --port=1313
```
在这个命令中，我们告诉 Git 只克隆这个 url 地址。
```bash
git clone URL --bare
```
## Commands

命令是应用程序的中心点。应用程序支持的每个交互都将包含在命令中。命令可以有子命令并可选择运行一个动作。

在上面的例子中，`server` 就是一个命令。

[更多关于 cobra.Command](https://godoc.org/github.com/spf13/cobra#Command)

## Flags

标志是一种修改命令行为的方法。Cobra 完全符合 POSIX 标准以及 Go [flag package](https://golang.org/pkg/flag/)。
Cobra 命令可以定义持久保存到子命令和标志的标志，这些命令和标志仅对该命令可用。

在上面的例子中，`port` 是一个标志。

标志功能由 [pflag library](https://github.com/spf13/pflag) 提供，是 flag 库的一个分支，它在添加 POSIX 合规性时保持相同的接口。

# Installing
Cobra 使用很简单，首先，使用 `go get` 安装最新的版本。此命令将安装 `cobra` 生成器可执行文件以及库及其依赖项：
```bash
go get -u github.com/spf13/cobra/cobra
```
接下来, 在你的应用引入 Cobra:

```go
import "github.com/spf13/cobra"
```

# Getting Started

虽然欢迎提供自己的组织, 但通常基于 Cobra 的应用程序将遵循以下组织结构:

```
  ▾ appName/
    ▾ cmd/
        add.go
        your.go
        commands.go
        here.go
      main.go
```

在一个 Cobra 应用里, 通常这个 `main.go` 文件仅仅只有一个目的：初始化 Cobra。

```go
package main

import (
  "{pathToYourApp}/cmd"
)

func main() {
  cmd.Execute()
}
```

## Using the Cobra Generator

Cobra 提供了自己的程序，可以创建你的应用程序并添加想要的任何命令。 这是将 Cobra 整合到你的应用程序中的最简单方法。

[这里](https://github.com/spf13/cobra/blob/master/cobra/README.md) 你可以找到更多相关信息。

## Using the Cobra Library

要手动实现 Cobra，需要创建一个空的 `main.go` 文件和一个 `rootCmd` 文件。你可以选择根据需要提供其他命令。

### Create rootCmd

Cobra 不需要任何特殊的构造函数。只需创建命令即可。

理想情况下，你将它放在 `app/cmd/root.go`:

```go
var rootCmd = &cobra.Command{
  Use:   "hugo",
  Short: "Hugo is a very fast static site generator",
  Long: `A Fast and Flexible Static Site Generator built with
                love by spf13 and friends in Go.
                Complete documentation is available at http://hugo.spf13.com`,
  Run: func(cmd *cobra.Command, args []string) {
    // Do Stuff Here
  },
}

func Execute() {
  if err := rootCmd.Execute(); err != nil {
    fmt.Println(err)
    os.Exit(1)
  }
}
```

还可以在 `init()` 函数中定义标志和处理配置。

例如`cmd/root.go`:

```go
import (
  "fmt"
  "os"

  homedir "github.com/mitchellh/go-homedir"
  "github.com/spf13/cobra"
  "github.com/spf13/viper"
)

func init() {
  cobra.OnInitialize(initConfig)
  rootCmd.PersistentFlags().StringVar(&cfgFile, "config", "", "config file (default is $HOME/.cobra.yaml)")
  rootCmd.PersistentFlags().StringVarP(&projectBase, "projectbase", "b", "", "base project directory eg. github.com/spf13/")
  rootCmd.PersistentFlags().StringP("author", "a", "YOUR NAME", "Author name for copyright attribution")
  rootCmd.PersistentFlags().StringVarP(&userLicense, "license", "l", "", "Name of license for the project (can provide `licensetext` in config)")
  rootCmd.PersistentFlags().Bool("viper", true, "Use Viper for configuration")
  viper.BindPFlag("author", rootCmd.PersistentFlags().Lookup("author"))
  viper.BindPFlag("projectbase", rootCmd.PersistentFlags().Lookup("projectbase"))
  viper.BindPFlag("useViper", rootCmd.PersistentFlags().Lookup("viper"))
  viper.SetDefault("author", "NAME HERE <EMAIL ADDRESS>")
  viper.SetDefault("license", "apache")
}

func initConfig() {
  // Don't forget to read config either from cfgFile or from home directory!
  if cfgFile != "" {
    // Use config file from the flag.
    viper.SetConfigFile(cfgFile)
  } else {
    // Find home directory.
    home, err := homedir.Dir()
    if err != nil {
      fmt.Println(err)
      os.Exit(1)
    }

    // Search config in home directory with name ".cobra" (without extension).
    viper.AddConfigPath(home)
    viper.SetConfigName(".cobra")
  }

  if err := viper.ReadInConfig(); err != nil {
    fmt.Println("Can't read config:", err)
    os.Exit(1)
  }
}
```

### Create your main.go

使用 `root` 命令，需要让 `main` 函数执行它。为了清楚起见，应该在 `root` 上运行 `Execute`，尽管可以在任何命令上调用它。

在一个 Cobra 应用里, 通常这个`main.go`文件仅仅只有一个目的：初始化 Cobra。

```go
package main

import (
  "{pathToYourApp}/cmd"
)

func main() {
  cmd.Execute()
}
```

### Create additional commands

可以定义其他命令，通常每个命令都在 `cmd/` 目录中给出自己的文件。

如果要创建版本命令，可以创建 `cmd/version.go` 并使用以下内容填充它

```go
package cmd

import (
  "fmt"

  "github.com/spf13/cobra"
)

func init() {
  rootCmd.AddCommand(versionCmd)
}

var versionCmd = &cobra.Command{
  Use:   "version",
  Short: "Print the version number of Hugo",
  Long:  `All software has versions. This is Hugo's`,
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hugo Static Site Generator v0.9 -- HEAD")
  },
}
```

## Working with Flags

标志提供修饰符来控制动作命令的操作方式。

### Assign flags to a command

由于标志是在不同的位置定义和使用的，因此我们需要在外部定义一个具有正确范围的变量来分配要使用的标志。

```go
var Verbose bool
var Source string
```

分配标志有两种不同的方法。

### Persistent Flags

标志可以是“持久的”，这意味着该标志可用于它所分配的命令以及该命令下的每个命令。对于全局标志，在 `root` 上分配标志作为持久标志。

```go
rootCmd.PersistentFlags().BoolVarP(&Verbose, "verbose", "v", false, "verbose output")
```

### Local Flags

也可以在本地分配一个标志，该标志仅适用于该特定命令。

```go
rootCmd.Flags().StringVarP(&Source, "source", "s", "", "Source directory to read from")
```

### Local Flag on Parent Commands

默认情况下，Cobra 仅解析目标命令上的本地标志，并且忽略父命令上的任何本地标志。通过启用 `Command.TraverseChildren`，
Cobra 将在执行目标命令之前解析每个命令上的本地标志。

```go
command := cobra.Command{
  Use: "print [OPTIONS] [COMMANDS]",
  TraverseChildren: true,
}
```

### Bind Flags with Config

你也可以用 [viper](https://github.com/spf13/viper) 绑定标志:
```go
var author string

func init() {
  rootCmd.PersistentFlags().StringVar(&author, "author", "YOUR NAME", "Author name for copyright attribution")
  viper.BindPFlag("author", rootCmd.PersistentFlags().Lookup("author"))
}
```

在这个例子中持久标志 `author` 被 `viper` 绑定了。
**注意**, 当用户没有提供 `--author` 标志时，变量 `author` 不会被设置为 config 的值。

更多参考 [viper documentation](https://github.com/spf13/viper#working-with-flags).

### Required flags

标志默认是可选的。如果希望命令在未设置标志时报告错误，将其设置为 `required`:
```go
rootCmd.Flags().StringVarP(&Region, "region", "r", "", "AWS region (required)")
rootCmd.MarkFlagRequired("region")
```

## Positional and Custom Arguments

可以使用 `Command` 的 `Args` 字段指定位置参数的验证。

内置以下验证器:

- `NoArgs` - 如果存在任何位置参数，该命令将报告错误。
- `ArbitraryArgs` - 该命令将接受任何参数。
- `OnlyValidArgs` - 如果有任何位置参数不在 `Command` 的 `ValidArgs` 字段中，该命令将报告错误。
- `MinimumNArgs(int)` - 如果没有至少 N 个位置参数，该命令将报告错误。
- `MaximumNArgs(int)` - 如果有多于 N 个位置参数，该命令将报告错误。
- `ExactArgs(int)` - 如果没有确切的 N 位置参数，该命令将报告错误。
- `ExactValidArgs(int)` - 如果没有正确的 N 位置参数，或者如果有任何位置参数不在 `Command` 的 `ValidArgs` 字段中，命令将报告并报错
- `RangeArgs(min, max)` - 如果参数的数量不在预期参数的最小和最大数量之间，则该命令将报告错误。

一个设置自定义验证器的例子:

```go
var cmd = &cobra.Command{
  Short: "hello",
  Args: func(cmd *cobra.Command, args []string) error {
    if len(args) < 1 {
      return errors.New("requires at least one arg")
    }
    if myapp.IsValidColor(args[0]) {
      return nil
    }
    return fmt.Errorf("invalid color specified: %s", args[0])
  },
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hello, World!")
  },
}
```

## Example

下面的例子，我们定义了三个命令. 两个位于顶层，一个（cmdTimes）是顶级命令之一的子级。在这种情况下，`root` 不可执行，这意味着需要子命令。
这是通过不为 `rootCmd` 提供 `Run` 来实现的。

我们只为一个命令定义了一个标志。

有关标志的更多文档，访问 https://github.com/spf13/pflag

```go
package main

import (
  "fmt"
  "strings"

  "github.com/spf13/cobra"
)

func main() {
  var echoTimes int

  var cmdPrint = &cobra.Command{
    Use:   "print [string to print]",
    Short: "Print anything to the screen",
    Long: `print is for printing anything back to the screen.For many years people have printed back to the screen.`,
    Args: cobra.MinimumNArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
      fmt.Println("Print: " + strings.Join(args, " "))
    },
  }

  var cmdEcho = &cobra.Command{
    Use:   "echo [string to echo]",
    Short: "Echo anything to the screen",
    Long: `echo is for echoing anything back.Echo works a lot like print, except it has a child command.`,
    Args: cobra.MinimumNArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
      fmt.Println("Print: " + strings.Join(args, " "))
    },
  }

  var cmdTimes = &cobra.Command{
    Use:   "times [# times] [string to echo]",
    Short: "Echo anything to the screen more times",
    Long: `echo things multiple times back to the user by providinga count and a string.`,
    Args: cobra.MinimumNArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
      for i := 0; i < echoTimes; i++ {
        fmt.Println("Echo: " + strings.Join(args, " "))
      }
    },
  }

  cmdTimes.Flags().IntVarP(&echoTimes, "times", "t", 1, "times to echo the input")

  var rootCmd = &cobra.Command{Use: "app"}
  rootCmd.AddCommand(cmdPrint, cmdEcho)
  cmdEcho.AddCommand(cmdTimes)
  rootCmd.Execute()
}
```

更多大型应用程序的更完整示例，请查看 [Hugo](http://gohugo.io/).

## Help Command

当你有子命令时，Cobra 会自动为应用程序添加一个帮助命令。当用户运行 `app help` 时会调用此方法。此外，帮助还将支持所有其他命令作为输入。
比方说，你有一个叫做 `create` 的命令没有任何额外配置;当 `app help create` 被调用时，Cobra 将工作。每个命令都会自动添加 `--help` 标志。

### Example

以下输出由Cobra自动生成。 除了定义命令和标志之外，不需要任何其他内容。
```bash
$ cobra help

Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.

Usage:
  cobra [command]

Available Commands:
  add         Add a command to a Cobra Application
  help        Help about any command
  init        Initialize a Cobra Application

Flags:
  -a, --author string    author name for copyright attribution (default "YOUR NAME")
      --config string    config file (default is $HOME/.cobra.yaml)
  -h, --help             help for cobra
  -l, --license string   name of license for the project
      --viper            use Viper for configuration (default true)

Use "cobra [command] --help" for more information about a command.
```

帮助像任何其他命令一样。没有特殊的逻辑或行为。事实上，如果你愿意，你可以提供自己的。

### Defining your own help

可以为默认命令提供自己的帮助命令或自己的模板，使用以下函数:

```go
cmd.SetHelpCommand(cmd *Command)
cmd.SetHelpFunc(f func(*Command, []string))
cmd.SetHelpTemplate(s string)
```

后两者也适用于任何子命令。

## Usage Message

当用户提供无效标志或无效命令时，Cobra 会通过向用户显示 `usage` 来做出响应。

### Example
可以从上面的帮助中认识到这一点。那是因为默认帮助将用法嵌入其输出的一部分。
```bash
$ cobra --invalid
Error: unknown flag: --invalid
Usage:
  cobra [command]

Available Commands:
  add         Add a command to a Cobra Application
  help        Help about any command
  init        Initialize a Cobra Application

Flags:
  -a, --author string    author name for copyright attribution (default "YOUR NAME")
      --config string    config file (default is $HOME/.cobra.yaml)
  -h, --help             help for cobra
  -l, --license string   name of license for the project
      --viper            use Viper for configuration (default true)

Use "cobra [command] --help" for more information about a command.
```
### Defining your own usage
可以提供自己的 `usage` 函数或模板供 Cobra 使用。与帮助一样，函数和模板可以通过公共方法覆盖：

```go
cmd.SetUsageFunc(f func(*Command) error)
cmd.SetUsageTemplate(s string)
```

## Version Flag

如果在 `root` 命令上设置了 `Version` 字段，Cobra 会添加顶级 `--version` 标志。
使用 `--version` 标志运行应用程序会使用 `Version` 模板将版本打印到 stdout。可以使用 `cmd.SetVersionTemplate(s string)` 函数自定义模板。

## PreRun and PostRun Hooks

可以在命令的主 `Run` 函数之前或之后运行函数。`PersistentPreRun` 和 `PreRun` 函数将在 `Run` 之前执行。`PersistentPostRun`
和 `PostRun` 将在 `Run` 之后执行。如果他们没有声明自己的 `Persistent*Run` 函数将由子项继承。这些函数按以下顺序运行:

- `PersistentPreRun`
- `PreRun`
- `Run`
- `PostRun`
- `PersistentPostRun`

**注意：父级的 `PreRun` 只会在父级命令运行时调用，子命令时不会调用的。**
运行 `hectl detect` 时，子命令 `detect` 运行并不会调用父级 `hectl` 的 `PreRun` 函数。

下面是使用所有这些功能的两个命令的示例。执行子命令时，它将运行 root 命令的 `PersistentPreRun`，但不运行 root 命令的 `PersistentPostRun`:

```go
package main

import (
  "fmt"

  "github.com/spf13/cobra"
)

func main() {

  var rootCmd = &cobra.Command{
    Use:   "root [sub]",
    Short: "My root command",
    PersistentPreRun: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside rootCmd PersistentPreRun with args: %v\n", args)
    },
    PreRun: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside rootCmd PreRun with args: %v\n", args)
    },
    Run: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside rootCmd Run with args: %v\n", args)
    },
    PostRun: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside rootCmd PostRun with args: %v\n", args)
    },
    PersistentPostRun: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside rootCmd PersistentPostRun with args: %v\n", args)
    },
  }

  var subCmd = &cobra.Command{
    Use:   "sub [no options!]",
    Short: "My subcommand",
    PreRun: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside subCmd PreRun with args: %v\n", args)
    },
    Run: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside subCmd Run with args: %v\n", args)
    },
    PostRun: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside subCmd PostRun with args: %v\n", args)
    },
    PersistentPostRun: func(cmd *cobra.Command, args []string) {
      fmt.Printf("Inside subCmd PersistentPostRun with args: %v\n", args)
    },
  }

  rootCmd.AddCommand(subCmd)

  rootCmd.SetArgs([]string{""})
  rootCmd.Execute()
  fmt.Println()
  rootCmd.SetArgs([]string{"sub", "arg1", "arg2"})
  rootCmd.Execute()
}
```

Output:
```
Inside rootCmd PersistentPreRun with args: []
Inside rootCmd PreRun with args: []
Inside rootCmd Run with args: []
Inside rootCmd PostRun with args: []
Inside rootCmd PersistentPostRun with args: []

Inside rootCmd PersistentPreRun with args: [arg1 arg2]
Inside subCmd PreRun with args: [arg1 arg2]
Inside subCmd Run with args: [arg1 arg2]
Inside subCmd PostRun with args: [arg1 arg2]
Inside subCmd PersistentPostRun with args: [arg1 arg2]
```

## Suggestions when "unknown command" happens

当 "unknown command" 错误发生时，Cobra 将自动打印建议。当错字发生时，这使得 Cobra 的行为与 `git` 命令类似。例如:

```bash
$ hugo srever
Error: unknown command "srever" for "hugo"

Did you mean this?
        server

Run 'hugo --help' for usage.
```

根据注册的每个子命令自动建议并使用 [Levenshtein distance](http://en.wikipedia.org/wiki/Levenshtein_distance) 实现。
每个匹配最小距离为 2（忽略大小写）的注册命令将显示为建议。

如果需要在命令中禁用建议或调整字符串距离，使用:

```go
command.DisableSuggestions = true
```

or

```go
command.SuggestionsMinimumDistance = 1
```

你还可以使用 `SuggestFor` 属性显式设置要为其指定命令的名称。这允许建议在字符串距离方面不接近的字符串，但在你的命令集和某些你不想要别名的字符串中有意义。例如:

```
$ kubectl remove
Error: unknown command "remove" for "kubectl"

Did you mean this?
        delete

Run 'kubectl help' for usage.
```

## Generating documentation for your command

Cobra 可以按以下格式生成基于子命令，标志等的文档：

- [Markdown](docs/md_docs.md)
- [ReStructured Text](docs/rest_docs.md)
- [Man Page](docs/man_docs.md)

## Generating bash completions

Cobra 可以生成 `bash-completion` 文件。如果向命令添加更多信息，这些 Completions 功能可以非常强大且灵活。
阅读更多关于 [Bash Completions](docs/bash_completions.md).

## Generating zsh completions

Cobra 可以生成 `zsh-completion`。阅读更多关于 [Zsh Completions](docs/zsh_completions.md).

# Contributing

1. Fork it
2. Download your fork to your PC (`git clone https://github.com/your_username/cobra && cd cobra`)
3. Create your feature branch (`git checkout -b my-new-feature`)
4. Make changes and add them (`git add .`)
5. Commit your changes (`git commit -m 'Add some feature'`)
6. Push to the branch (`git push origin my-new-feature`)
7. Create new pull request

# License

Cobra is released under the Apache 2.0 license. See [LICENSE.txt](https://github.com/spf13/cobra/blob/master/LICENSE.txt)