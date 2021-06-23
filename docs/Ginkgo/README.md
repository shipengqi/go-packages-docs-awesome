# Ginkgo 中文文档

Ginkgo 中文文档，本文档基于 [Ginkgo](https://onsi.github.io/ginkgo/) 官方文档。不定期更新。

Ginkgo 是一个 Go 测试框架，旨在帮助你有效地编写富有表现力的全方位的 [Behavior-Driven Development](https://en.wikipedia.org/wiki/Behavior-driven_development) (BDD) 风格的测试。
最好与 [Gomega](https://github.com/onsi/gomega) 匹配器库搭配使用，但它的设计是与匹配器无关的。

本文档你假设你同时使用了 Ginkgo 和 Gomega。同时也假设你对 Go 有一定的了解，并且对 Go 如何在 $GOPATH 下组织软件包有一个好的思维模型。

## 获取 Ginkgo
使用 go get 获取:
```sh
$ go get github.com/onsi/ginkgo/ginkgo
$ go get github.com/onsi/gomega/...
```

上面的命令会将获取到的 `ginkgo` 可执行文件安装到 `$GOPATH/bin` 下面 - 你需要在 `$PATH` 上配置。

Ginkgo 要求 Go 版本在 v1.6 或以上。 要安装 Go ，参考 [安装手册](https://golang.org/doc/install) 。

上面的命令同时安装了全部 gomega 库。如果你只时想安装测试需要的包，使用 `go get -t` 来导入你需要的包。

例如，在你的测试代码中导入 gomega：

```go
import "github.com/onsi/gomega"
```

使用 `go get -t` 来检索你的测试代码中引用的软件包：
```sh
$ cd /path/to/my/app
$ go get -t ./...
```

## 入门: 第一个测试
Ginkgo 与 Go 现有的测试基础设施挂钩，这允许你使用 `go test` 去运行一个 Ginkgo 套件。

```
这也意味着 Ginkgo 测试可以和传统 Go testing 测试一起使用。go test 和 ginkgo 都会运行你套件内的所有测试。`go test` 和 `ginkgo` 都会运行你套件内的所有测试。
```

### 引导套件

要为一个包写 Ginkgo 测试的话你必须首先初始化一个 Ginkgo 测试套件。比如说你有一个名为 books 的包：

```sh
$ cd path/to/books
$ ginkgo bootstrap
```
这会生成一个名为 `books_suite_test.go` 的文件，包含：

```go
package books_test

import (
    . "github.com/onsi/ginkgo"
    . "github.com/onsi/gomega"
    "testing"
)

func TestBooks(t *testing.T) {
    RegisterFailHandler(Fail)
    RunSpecs(t, "Books Suite")
}
```

我们来分析上面的代码：
- Go 允许我们在 books 包中声明 books_test 包。使用 books_test 替代 books 允许我们遵守 books 包的封装性：你的测试将要导入 books 并且从外部使用它，就像其他包一样。当让，如果你想要进入包内部来测试它内部组件并进行跟多行为测试的话，你可以选择将 package books_test 换成 package books 。
- 我们使用 dot-import 将 ginkgo 和 gomega 包导入到了顶级命名空间。如果你不想这样做的话，查看下面的 避免 Dot Imports 。
- TestBooks 是一个 testing 测试.你运行 go test 或 ginkgo 的时候 Go 测试执行器会执行这个函数。
- RegisterFailHandler(Fail) ： 一个 Ginkgo 测试调用 Ginkgo 的 Fail(description string) 函数发出失败信号。我们使用RegisterFailHandler 将这个函数传给 Gomega 。这是 Ginkgo 和 Gomega 唯一的连接点。
- RunSpecs(t *testing.T, suiteDescription string) 告诉 Ginkgo 开始这个测试套件。如果任意 specs（说明）失败了，Ginkgo 会自动使 testing.T 失败。
