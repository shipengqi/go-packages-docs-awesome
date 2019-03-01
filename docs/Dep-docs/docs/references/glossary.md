# 术语

Dep使用一些专门术语.在这里学习!

<!-- START doctoc -->

* [Atom](#atom)
* [Cache lock](#cache-lock)
* [Constraint](#constraint)
* [Current Project](#current-project)
* [Deducible](#deducible)
* [Deduction](#deduction)
* [Direct Dependency](#direct-dependency)
* [External Import](#external-import)
* [GPS](#gps)
* [Local cache](#local-cache)
* [Lock](#lock)
* [Manifest](#manifest)
* [Metadata Service](#metadata-service)
* [Override](#override)
* [Project](#project)
* [Project Root](#project-root)
* [Solver](#solver)
* [Source](#source)
* [Source Root](#source-root)
* [Sync](#sync)
* [Transitive Dependency](#transitive-dependency)
* [Vendor Verification](#vendor-verification)

<!-- END doctoc -->

* * *

### Atom

> 原子

原子是特定版本的源.在实践中，这意味着一个[项目根](#project-root)的二元组和版本，例如`github.com/foo/bar@master`. 原子主要是内部的. [求解器](#solver)这个词在别处很少使用.

### Cache lock

> 缓存锁定

也就是"缓存锁定文件".一个名为`sm.lock`的文件， 用于确保同时只有一个Dep过程运行在[本地缓存](#local-cache)， Dep当前对多个进程进行访问本地缓存的设计是不安全的.

### Constraint

> 约束

约束既有狭义又有较松散的含义.狭义指的是`Gopkg.toml`中[`[[constraint]]`](Gopkg.toml.zh.md#constraint)字段. 然而，在某些上下文中，这个词可以更宽泛地用来指应用规则，和要求依赖管理的想法.

### Current Project

> 当前项目

Dep正在运作的项目 - 写进项目的`Gopkg.lock`， 并填充它的`vendor`目录.

也称为"根项目".

### Deducible

> 可推断的

简单说明， 对于给定的导入路径，导入路径的[推导](#deduction)方式，是否成功返回. 也经常使用"Undeducible-不可推断"，来引用推导失败的导入路径.

### Deduction

> 推导

推导是查实， 与源主目录的对应导入路径的子集的过程. 某些模式是先验已知的(静态的); 其他模式必须通过网络请求(动态)来发现. 参见参考文献[导入路径推导](deduction.zh.md)具体情况.

### Direct Dependency

> 直接依赖

一个项目的直接依赖是*imports*其一个或多个包，或包含在`Gopkg.toml`的[`required`](Gopkg.toml.zh.md#required)中.

如果`A -> B -> C -> D`每个字母都在表示仅包含单个包的不同项目，以及`->`是导入语句，那`B`是`A`是直接依赖，而`C`和`D`是`A`的[传递依赖](#transitive-dependency).

[当前项目](#current-project) 的 `Gopkg.toml`，Dep只包含了`required`规则. 因此，`=>`就代表`required`， 而不是一个标准的进口， `A -> B => C`，说`C`是`B`的直接依赖 *只在*`B`是当前项目的情况下. 因为`A`是当前项目时， `B`-到-`C`的链接不存在， 那`C`实际上不会出现在图表中.

### External Import

> 外部进口

一个`import`语句，指向项目中的包，而不是它的源包.例如，包`github.com/foo/bar`的一个`import`如果它指向任何*其他*东西，将被认为是外部输入. 除非指向stdlib或`github.com/foo/bar/*`.

### GPS

> Go packaging solver

"Go包装解决方案"的缩写，它是[Dep中的库风格包的子树](https://godoc.org/github.com/golang/dep/gps)， 是Dep建立的发动机. 最常被称为"GPS".

### Local cache

> 本地缓存

Dep维护自己的、原始的上游的源集合(一般是，Git存储库克隆).这是与`$GOPATH/src`分开的.这样就没有维护`$GOPATH`磁盘状态的义务.因为Dep经常需要改变磁盘状态才能完成它的工作.

默认情况下，本地缓存位于`$GOPATH/pkg/dep`. 如果你有多个`$GOPATH`路径，Dep将使用进程的工作目录的逻辑父对象.或者，可以通过[`DEPCACHEDIR`环境变量](env-vars.zh.md#depcachedir)设置.

### Lock

> 锁

一个通用术语，用于许多语言包管理器，Dep保持的`Gopkg.lock`文件的信息类型.

### Manifest

> 主内容

一个通用术语，用于许多语言包管理器，Dep保持的`Gopkg.toml`文件的信息类型.

### Metadata Service

> 元数据服务

在HTTP服务，当它接收到包含`go-get=1`查询字符串的HTTP请求时，将请求的路径部分解释为导入路径，并通过在HTML中嵌入数据来进行响应.`<meta>`指示底层源根的类型和URL的标记.这是动态的服务器端组件.[推导](#deduction).

元数据服务的行为定义在[远程导入路径：Go命令官方文档](https://golang.org/cmd/go/#hdr-Remote_import_paths).

各种表示为"HTTP元数据服务"，"`go-get`HTTP元数据服务"，"`go-get`服务等.

### Override

> 重写

重写是`Gopkg.toml`的[`[[override]]`](Gopkg.toml.md#override).

### Project

> 项目

一个项目是一个Go包的树.不能嵌套项目.见[项目根](#project-root)有关如何确定树的根的更多信息.

### Project Root

> 项目根

项目的根导入路径.项目根定义为:

-   对于当前项目，`Gopkg.toml`文件定义项目根目录的位置
-   对于依赖关系，网络[来源](#source)(VCS存储库)的根被视为项目根.

这些通常是同一个，但并不总是如此.当使用Dep内部的monorepo，个重`Gopkg.toml`文件可能存在于离散项目的子路径中， 将每个导入路径指定为项目根目录. 当直接在这些项目上工作时，这很好.但是，如果存储库中没有哪个项目试图导入monorepo， dep会将monorepo视为一个大型项目，那这根目录就成为了**Project Root**;它将忽略任何和所有子目录中的`Gopkg.toml`文件.

这也可以称为"导入根"或"根导入路径".

### Solver

> 求解器

"求解器"是对特定于域的SAT求解器的借鉴. [gps](#gps). 更多细节可看其[参考页](the-solver.zh.md).

### Source

> 来源

保存版本代码的远程实体. 来源是包含代码的实体，而不是代码本身的任何特定版本.

"来源"被用来代替"VCS"，因为Go包管理工具将很快学会使用，除VCS系统之外的系统.

### Source Root

> 源根

导入路径的一部分，对应于来源的网络位置.这类似于[项目根](#project-root)，但严格地引用第二个，面向网络的定义.

### Sync

> 同步

Dep是被设计成， 围绕四个状态，定义良好的关系来的:

1.  `.go`文件的`import`语句
2.  `Gopkg.toml`
3.  `Gopkg.lock`
4.  `vendor`目录

如果该关系的任何方面未实现(例如，有一个`import`没有反映在`Gopkg.lock`，或者`vendor`一个缺少的项目， 那Dep就认为该项目"不同步".

这一概念在[保障机制](ensure-mechanics.zh.md#staying-in-sync).

### Transitive Dependency

> 传递依赖

项目的传递依赖项是指它不导入自身，而是通过其依赖项之一，导入的依赖项.

如果`A -> B -> C -> D`每个字母都表示仅包含单个包的不同项目，以及`->`是导入语句，那`C`和`D`是`A`的传递依赖关系，而`B`是`A`的一个[直接依赖](#transitive-dependency).

### Vendor Verification

> 供应商验证

Dep保证，`vendor/`是准确地的包含期待的代码， 它通过对每个项目的内容进行哈希，并存储[Gopkg.lock 摘要](Gopkg.lock.zh.md#digest)结果. 这个摘要是在应用修剪规则*之后*计算出来的.

摘要用于确定， `vendor/`的内容是否需要在`dep ensure`运行期间重生成，还有`dep check`也使用它来确定`Gopkg.lock`和`vendor/`是否在[同步](#sync). 这个[`noverify`](Gopkg.toml.zh.md#noverify)列在`Gopkg.toml`，可以用来绕过大多数这些验证行为.