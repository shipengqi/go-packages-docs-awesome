# Dep 中文文档
Dep 中文文档，本文档基于 [dep](https://golang.github.io/dep/) 官方文档。不定期更新。


<p align="center"><img src="docs/images/DigbyShadows.png" width="360"></p>
<p align="center">
  <a href="https://travis-ci.org/golang/dep"><img src="https://travis-ci.org/golang/dep.svg?branch=master" alt="Build Status"></img></a>
  <a href="https://ci.appveyor.com/project/golang/dep"><img src="https://ci.appveyor.com/api/projects/status/github/golang/dep?svg=true&branch=master&passingText=Windows%20-%20OK&failingText=Windows%20-%20failed&pendingText=Windows%20-%20pending" alt="Windows Build Status"></a>
  <a href="https://goreportcard.com/report/github.com/golang/dep"><img src="https://goreportcard.com/badge/github.com/golang/dep" /></a>
</p>

## 目录
- 指南
  - [介绍](./docs/guides/introduction.md)
  - [安装](./docs/guides/installation.md)
  - [创建一个新项目](./docs/guides/new_project.md)
  - [迁移到 Dep](./docs/guides/migrating.md)
  - [Dep 日常](./docs/guides/daily_dep.md)
  - [卸载](./docs/guides/uninstalling.md)
- 参考
  - [模型和机制](docs/references/ensure_mechanics.md)
  - [Import Path Deduction](./docs/references/deduction.md)

## Dep

`dep` 是 Go 的依赖管理工具。需要 Go 1.9 或更新版本才能编译。

`dep` 是"官方实验"。Go 工具链，从 1.11 开始，（通过实验）采用了一种与 `dep` 明显不同的方法。结果是我们会继续开发 `dep`，但是，考察与开发 dep 的工作主要作为之后
开发工具链中，版本化行为的替代原型。

`dep` 相关指南和参考资料，查看 [文档](https://golang.github.io/dep)。

## 安装

强烈建议使用一个已经发布的版本。可用的二进制文件在 [releases](https://github.com/golang/dep/releases) 页面。

在 MacOS 上，可以使用 Homebrew 安装或升级到最新发布的版本:

```sh
$ brew install dep
$ brew upgrade dep
```

在其他平台上，可以使用 `install.sh` 脚本:

```sh
$ curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
```

默认情况下，它会安装到你的 `$GOPATH/bin` 目录，或你使用 `INSTALL_DIRECTORY` 环境变量指定的任何的其他目录。

如果你的平台不受支持，需要手动构建它或让我们的团队知道，我们会考虑将你的平台添加到发布版本中。

如果你对 hacking `dep` 感兴趣，你可以通过 `go get` 来安装:

```sh
go get -u github.com/golang/dep/cmd/dep
```