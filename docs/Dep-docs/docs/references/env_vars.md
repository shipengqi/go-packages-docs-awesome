# 环境变量

dep的行为可以通过一些环境变量修改:

-   [`DEPCACHEAGE`](#depcacheage)
-   [`DEPCACHEDIR`](#depcachedir)
-   [`DEPPROJECTROOT`](#depprojectroot)
-   [`DEPNOLOCK`](#depnolock)

环境变量传递给子命令，因此可用于影响vcs(例如`git`)行为.

* * *

### `DEPCACHEAGE`

如果设置为了一个[持续时间](https://golang.org/pkg/time/#ParseDuration)(例如.`24h`)，它将启用从源存储库缓存元数据:

-   已发布版本的列表
-   项目的`Gopkg.toml`文件内容，特定版本
-   特定版本的项目包和导入树

必须设置持续时间以启用缓存.(在dep的未来版本中，它将默认启用).持续时间用作TTL，但仅用于可变信息，如版本列表.与不可变VCS修订版相关的信息(packages和imports; `Gopkg.toml`声明) 无限期地缓存.

缓存存在于`$DEPCACHEDIR/bolt-v1.db`，其中版本号是dep特定数据模式使用的内部相关联编号.

该文件可以安全删除; 数据库将根据需要自动重建.

### `DEPCACHEDIR`

允许用户指定自定义目录， 为dep原始VCS源代码库的[本地缓存](glossary。zh.md#local-cache).默认为`$GOPATH/pkg/dep`.

### `DEPPROJECTROOT`

如果设置，则此变量的值将被视为[当前的项目](glossary。zh.md#current-project)的[项目根](glossary。zh.md#project-root) ，取代基于GOPATH的推理.

主要用于， 如果你不使用标准`go`工具链作为编译器(例如，使用Bazel)， ， 而在GOPATH之外的操作就没有多大用处.

### `DEPNOLOCK`

默认情况下，dep创建一个`sm.lock`存档于`$DEPCACHEDIR/sm.lock`，为了防止多个dep进程同时与[本地缓存](glossary。zh.md#local-cache)之交互. 设置此变量将绕过该保护;不会创建任何文件.这对某些文件系统很有用; 特别是VirtualBox共享行为不好的情况.
