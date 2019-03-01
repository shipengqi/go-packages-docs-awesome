# Gopkg.lock

这个`Gopkg.lock`文件是由`dep ensure`和`dep init`. 它的输出是[解函数](ensure-mechanics.md#functional-flow)一个项目的依赖图的传递完成的快照，表示为一系列`[[project]]`诗节.这意味着:

-   一个项目需要编译的每个包
-   加任何[`required`](Gopkg.toml.md#required)包装
-   少任何[`ignored`](Gopkg.toml.md#ignored)包装

`Gopkg.lock`还包含用于到达最终图的算法和输入的一些元数据.`[solve-meta]`.

`Gopkg.lock`总是包含一个`revision`对于所有列出的依赖项，作为语义`revision`保证他们是不可改变的.因此，`Gopkg.lock`作为一个可重复的构建列表-只要上游仍然可用，所有依赖关系都可以被精确地再现.

`Gopkg.lock`是自动生成的;手动编辑它通常是反模式.如果有一个目标你只能通过手工编辑来实现`Gopkg.lock`它至少是一个特征请求，很可能是一个bug.

## `[[projects]]`

依赖关系图被表示为一系列`[[projects]]`Stasas，每个表示单个依赖项目.一个给定的项目只能在列表中出现一次，并且关于它们表达的版本信息包括所有包含的包——不可能有多个来自不同版本的单个项目的包.

这些是可以在A中出现的所有属性.`[[projects]]`节，以及它们是否保证存在/必须存在于一节有效.

| **Property** | **Always present?** |
| ------------ | ------------------- |
| `name` | Y |
| `packages` | Y |
| `source` | N |
| `revision` | Y |
| `version` | N |
| `branch` | N |
| `pruneopts` | Y |
| `digest` | Y |

### `name`

节所应用的项目，如其所标识的[项目根](glossary.md#project-root).

### `source`

如果存在，则指示要从中检索项目的上游源.它具有相同的特性[`source`在里面`Gopkg.toml`](Gopkg.toml.md#source).

### `packages`

DEP中的一个完整目录列表，DEP被确定为构建所必需的.

通常，这组包是通过至少一个但与下列所有机制一样多的、发现是包导入图中的参与者的:

-   在当前项目中[`required`](Gopkg.toml.md#required)列表
-   由当前项目或不同依赖项的包导入
-   由来自该项目内的包导入，该包直接或传递地由来自不同项目的包导入

### `pruneopts`

一种紧凑编码的形式[指定的修剪选项`Gopkg.toml`](Gopkg.toml.md#prune). 每个字符代表三种可能的规则之一:

| Character | Pruning Rule in `Gopkg.toml` |
| --------- | ---------------------------- |
| `N` | `non-go` |
| `U` | `unused-packages` |
| `T` | `go-tests` |

如果字符存在于`pruneopts`为该项目启用修剪规则.因此，`NUT`指示所有三个修剪规则都是活动的.

### `digest`

内容的哈希摘要`vendor/`对于这个项目，*之后*修剪规则已被应用.摘要通过冒号分隔的前缀来版本化;字符串是格式的.`<version>:<hex-encoded digest>`. 对应于版本1的散列算法是Sh256，如在STDLIB包中实现的那样.`crypto/sha256`.

有一些调整将哈希区别于一个天真的文件系统树哈希实现:

-   忽略了SysLink.
-   行结束归一化到LF(使用类似于GIT的算法)，以确保在平台上的摘要不变化.

### 版本信息:`revision`，`version`和`branch`

为了提供可重复的构建，绝对要求每个项目节包含一个`revision`，无论遇到什么样的约束`Gopkg.toml`文件夹.也有可能正好是其中之一`version`或`branch`将*另外*出席.

当另一个存在的时候，`revision`被理解为与此相对应的基础的、不可变的标识符.`version`或`branch` *当时的时候`Gopkg.lock`是书面的*.

## `[solve-meta]`

本节中包含的元数据告诉我们用于生成`Gopkg.lock`文件.这些是非常粗略的指示符，主要用于在锁可能已经无效时触发对锁的重新评估，以及当其成员正在使用具有潜在微妙不同效果的算法时警告团队.

关于"分析器"和"求解器"的更多细节如下，但是版本控制原理是相同的:导致至少一个输入集可接受解决方案集减少的算法更改通常需要版本凸起，而增加该集大小的更改则不需要.然而，这不是一个正式的定义;对于小的更改和bug修复，我们留有判断余地，每次发布最多会遇到一次.

通过只在解决方案集收缩(而不是扩展)上碰撞版本，它允许我们避免不断碰撞(这会使跨团队使用dep变得笨拙)，同时仍然使得当求解器和版本号匹配时`Gopkg.lock`一个运行版本的DEP，文件中记录的内容可以通过运行版本的规则来接受.

### `input-imports`

当时出现的所有导入输入的排序列表.`Gopkg.lock`计算.此列表包括两个实际`import`来自项目的声明，以及任何`required`列出的导入路径`Gopkg.toml`，不包括任何`ignored`.

### `analyzer-name`和`analyzer-version`

分析器是负责解释内容的内部DEP组件.`Gopkg.toml`文件，以及元数据工具从任何工具DEP知道:`glide.yaml`，`vendor.json`等.

分析器之所以命名，是因为DEP需要识别它自己的引擎，GPS.`github.com/golang/dep/gps`gps对dep一无所知.当分析器逻辑中的某些东西开始以显著不同的方式处理它已经接受的数据，或者停止接受特定类型的数据时，分析器版本就会发生颠簸.它是*不*当添加对全新类型数据的支持时发生更改.

例如，如果dep的分析器停止支持滑动的自动转换，那么就不需要颠簸分析器版本，因为这样做会使*更多*可能的解决方案.添加支持从新工具转换，或更改解释`version`字段在`Gopkg.toml`因此，它只允许指定最小版本，将导致版本颠簸.

### `solver-name`和`solver-version`

解算器是后面的算法.[解函数](ensure-mechanics.md#functional-flow). 它选择最终出现的所有版本.`Gopkg.lock`通过找到满足所有规则的组合，包括来自`Gopkg.toml`(由分析器供给解算器).

之所以命名这个求解器，是因为它和分析器一样，是可插拔的;可以编写一个替代算法，应用不同的规则来实现相同的目标.一个DEP使用"GPS CDCL"，命名为[最相似的SAT求解算法的一般类](https://en.wikipedia.org/wiki/Conflict-Driven_Clause_Learning)，虽然该算法实际上是专用的、领域特定的.[SMT求解器](https://en.wikipedia.org/wiki/Satisfiability_modulo_theories).

版本颠簸的一般原理适用于求解器版本:如果求解器开始强制执行[转到1.4导入路径注释](https://golang.org/cmd/go/#hdr-Import_path_checking)这会导致一个颠簸，因为它只能缩小解集.如果以后要放松这个要求，它就不需要颠簸，因为它只能扩展解决方案集.
