# 安装

强烈建议使用 dep 的已发布版本。虽然 tip 从未故意破坏，但其稳定性无法保证。

## 二进制安装

可用的预编译的二进制文件在 [releases](https://github.com/golang/dep/releases) 页。你可以使用 `install.sh` 脚本自动为本地平台安装 `dep`:

```sh
$ curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
```

## MacOS

使用 Homebrew 安装，或升级到最新发布的版本:

```sh
$ brew install dep
$ brew upgrade dep
```

## Arch Linux

安装`dep`包:

```sh
pacman -S dep
```

## 从源码安装

下面的代码片段从源代码安装最新版本的 dep，并将版本设置为二进制文件，让 `dep version` 可以按预期工作。

注意，建议一般情况下不要使用此方法。我们不试图打破 tip，但也不保证其稳定性。同时，我们很乐意使用者愿意接受实验，并快速向我们提供反馈！

```sh
go get -d -u github.com/golang/dep
cd $(go env GOPATH)/src/github.com/golang/dep
DEP_LATEST=$(git describe --abbrev=0 --tags)
git checkout $DEP_LATEST
go install -ldflags="-X main.version=$DEP_LATEST" ./cmd/dep
git checkout master
```

## 开发

如果你想 hacking dep，可以通过 `go get` 安装:

```sh
go get -u github.com/golang/dep/cmd/dep
```

**注意，dep 需要一个正常运行的 Go 工作区和 `GOPATH`**。如果你对 Go 工作区和 `GOPATH` 不熟悉，
请查看 [Go 语言文档](https://golang.org/doc/code.html#Organization)
并设置你的本地工作区。Dep 的模型可以在没有 `GOPATH` 的情况下工作，但目前还没有。

## 卸载

正在寻找一种卸载方式 `dep` ? 有一个单独的 [页面](uninstalling.md) 说明这个问题！
