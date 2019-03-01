# Gopkg.toml

这个`Gopkg.toml`文件最初是由`dep init`主要是手工编辑的.它包含管理DEP行为的几种规则声明:

-   *依赖规则:* [`constraints`](#constraint)和[`overrides`](#override)允许用户指定依赖关系的哪些版本是可接受的，以及它们应该从哪里检索.
-   *包装图规则:* [`required`](#required)和[`ignored`](#ignored)允许用户通过导入或排除导入路径来操作导入图.
-   [`metadata`](#metadata)是DEP将忽略的键值对的用户定义映射.他们提供了一个数据集的工具建设的顶部.
-   [`prune`](#prune)设置确定什么文件和目录可以被视为不必要的，从而自动从`vendor/`.
-   [`noverify`](#noverify)是项目根目录[供应商验证](glossary.md#vendor-verification)跳过.

请注意，因为ToML不遵守树结构，所以`required`和`ignored`字段必须在任何之前声明`[[constraint]]`或`[[override]]`.

有一个完整的[例子](#example) `Gopkg.toml`文件的底部文件.`dep init`默认情况下，也会生成`Gopkg.toml`包含一些示例值，用于指导.

## 依赖规则:`[[constraint]]`和`[[override]]`

A中的大多数规则声明`Gopkg.toml`要么是`[[constraint]]`或`[[override]]`诗节.这两种类型的Stasas允许完全相同类型的值，但DEP解释它们不同.每个值允许以下值:

-   `name`-对应于[源根](glossary.md#source-root)依赖关系(一般情况下:VCS根是在哪里)
-   至多[版本规则](#version-rules)
-   可选的[`source`规则](#source)
-   [`metadata`](#metadata)这是特定于`name`D项目

这些节中的任何一个的完整示例(实际上无效，因为它有多个版本规则，出于说明的目的)如下所示:

```toml
[[constraint]]
  # Required: the root import path of the project being constrained.
  name = "github.com/user/project"
  # Recommended: the version constraint to enforce for the project.
  # Note that only one of "branch"， "version" or "revision" can be specified.
  version = "1.0.0"
  branch = "master"
  revision = "abc123"

  # Optional: an alternate location (URL or import path) for the project's source.
  source = "https://github.com/myfork/package.git"

  # Optional: metadata about the constraint or override that could be used by other independent systems
  [metadata]
  key1 = "value that convey data to other systems"
  system1-data = "value that is used by a system"
  system2-data = "value that is used by another system"
```

### `[[constraint]]`

一`[[constraint]]`StAZA定义了一个规则的规则[直接依赖](glossary.md#direct-dependency)必须合并到依赖图中.DEP尊重来自当前项目的这些声明.`Gopkg.toml`以及`Gopkg.toml`在任何依赖项中找到的文件.

**使用:**有一个[直接依赖](FAQ.md#what-is-a-direct-or-transitive-dependency)使用特定的分支、版本范围、修订或备用源(如叉子).

### `[[override]]`

安`[[override]]`节与A不同`[[constraint]]`因为它适用于所有的依赖关系，[直接的](glossary.md#direct-dependency)和[传递的](glossary.md#transitive-dependency)并取代所有其他`[[constraint]]`该项目的声明.但是，仅覆盖当前项目的`Gopkg.toml`并入.

**使用:**重写主要目的是消除多种不可调和的分歧.`[[constraint]]`对单个依赖项的声明.然而，如果你需要的话，它们也将是你的主要来源.[约束传递依赖的版本?](FAQ.md#how-do-i-constrain-a-transitive-dependencys-version)

在可能的情况下，应谨慎使用并临时使用.

### `source`

一`source`规则可以指定另一个位置，从该位置`name`应该检索D项目.它主要用于临时指定存储库的叉.

`source`规则通常是脆弱的，只有在没有其他追索权时才可以使用.使用它们来规避网络可达性问题通常是一种反模式.

### 版本规则

可以使用版本规则`[[constraint]]`或`[[override]]`诗节.版本规则有三种类型:`version`，`branch`和`revision`. 最多可以指定三种类型中的一种.

#### `version`

`version`是一种属性`constraint`S和`override`它用于指定特定依赖项的版本约束.它可以用于目标任意的VCS标签，或语义版本，或语义版本的范围.

使用以下运算符可以指定语义版本范围:

-   `=`平等的
-   `!=`不平等
-   `>`大于
-   `<`:小于
-   `>=`大于或等于
-   `<=`小于或等于
-   `-`文字范围.例如，1.2 -1.4.5相当于>=1.2，\<1.4.5
-   `~`小范围.例如，~1.2.3相当于>=1.2.3，\<1.3.0.
-   `^`主要范围.例如，^ 1.2.3相当于>=1.2.3，\<2.0.0.
-   `[xX*]`通配符.例如，1.2.x相当于>=1.2.0，\<1.3.0.

例如，你可以包括指定的规则.`version = "=2.0.0"`将依赖项绑定到版本2.0.0，或约束为次要版本:`version = "~2.1.0"`. 参考[档案图书馆](https://github.com/Masterminds/semver)更多信息的文档.

**注释**当你指定版本时*没有操作员*，`dep`自动使用`^`默认情况下的操作符.`dep ensure`将解释给定的版本作为范围的最小边界，例如:

-   `1.2.3`成为靶场`>=1.2.3， <2.0.0`
-   `0.2.3`成为靶场`>=0.2.3， <0.3.0`
-   `0.0.3`成为靶场`>=0.0.3， <0.1.0`

`~`和`=`运算符可以与版本一起使用.在没有任何操作符的情况下指定版本时，`dep`自动添加插入符号运算符，`^`. 插入符号操作符将版本中最左边的非零数字插入.例如:

```
^1.2.3 means 1.2.3 <= X < 2.0.0
^0.2.3 means 0.2.3 <= X < 0.3.0
^0.0.3 means 0.0.3 <= X < 0.1.0
```

要在清单中插入直接依赖的版本，请用`=`. 例如:

```toml
[[constraint]]
  name = "github.com/pkg/errors"
  version = "=0.8.0"
```

#### `branch`

使用A`branch`约束将导致DEP使用指定的分支(例如，`branch = "master"`)用于特定的依赖关系.在树枝顶端的修改将被记录到`Gopkg.lock`并且几乎总是保持不变，直到请求更改为止.`dep ensure -update`.

通常，当项目使其可用时，你应该更喜欢语义版本到分支.

#### `revision`

一`revision`是基本不变的标识符，就像Git提交的Sa1.虽然它被允许限制为`revision`这样做几乎总是一种反模式.

通常情况下，人们倾向于修改，因为他们觉得它会在某种程度上提高他们的项目的可重复性.这不是一个很好的理由.`Gopkg.lock`提供再现性.只使用`revision`如果你有充分的理由相信*不*该依赖的其他版本*能够*工作.

## 包装图规则:`required`和`ignored`

作为正常操作的一部分，DEP分析GO代码中的导入语句.这些导入语句将包连接在一起，最终形成图表.这个`required`和`ignored`规则以大致相互对偶的方式操纵该图:`required`向图中添加导入路径，以及`ignored`移除它们.

### `required`

`required`列出必须包含在Gopkg.lock中的一组包(不是项目).此列表与当前项目导入的包集合并.

```toml
required = ["github.com/user/thing/cmd/thing"]
```

**使用:**LTENS、发电机和其他开发工具

-   你的项目需要
-   不是`import`通过你的项目，[直接或传递](FAQ.md#what-is-a-direct-or-transitive-dependency)
-   你不想把它们放进你的手中`GOPATH`和/或你想锁定版本

请注意，这只会牵扯这些依赖项的来源.它不安装或编译它们.因此，如果你需要安装该工具，你还应该运行以下操作(手动或从`Makefile`)之后`dep ensure`:

```bash
cd vendor/pkg/to/install
go install .
```

如果这是唯一的安装这些可执行文件的项目，这只会可靠地工作.如果你希望根据正在工作的项目运行同一可执行文件的不同版本，那么这还不够.在那种情况下，你必须使用不同的方法.`GOBIN`对于每个项目，在运行上面的命令之前，执行这样的操作:

```bash
export GOBIN=$PWD/bin
export PATH=$GOBIN:$PATH
```

你也可以试试[虚拟现实](https://github.com/GetStream/vg)在其中安装依赖项`required`在特定项目中自动列出`GOBIN`.

### `ignored`

`ignored`列出DEP静态分析源代码时忽略的一组包(非项目).忽略的包可以在这个项目中，或者在依赖项中.

```toml
ignored = ["github.com/user/project/badpkg"]
```

使用`*`定义要忽略的包前缀.这将导致任何词汇通配符匹配被忽略，包括在`*`.

```toml
ignored = ["github.com/user/project/badpkg*"]
```

**使用:**防止包和任何包的唯一依赖性被纳入`Gopkg.lock`.

## `metadata`

`metadata`可以存在于根部，也可以存在于`constraint`和`override`声明.

`metadata`声明被DEP忽略，并用于其他独立系统的使用.

根`metadata`声明定义项目本身的信息，而`metadata`A下的声明`[[constraint]]`或`[[override]]`定义有关该规则的元数据，用于`name`D项目.

```toml
[metadata]
key1 = "value that convey data to other systems"
system1-data = "value that is used by a system"
system2-data = "value that is used by another system"
```

## `prune`

`prune`定义依赖项的全局和每个项目修剪选项.选项在写入时确定哪些文件被丢弃.`vendor/`树.

以下是当前可用的选项:

-   `unused-packages`指示从包导入图中不出现的目录中的文件应该被修剪.
-   `non-go`GUE文件不被GO使用.
-   `go-tests`修剪去测试文件.

出于足够的谨慎，DEP无需保留可能具有法律意义的文件.

默认情况下，修剪选项被禁用.然而，生成一个`Gopkg.toml`通过`dep init`将添加行使能`go-tests`和`unused-packages`在根级修剪选项.

```toml
[prune]
  go-tests = true
  unused-packages = true
```

每个项目可以定义相同的修剪选项.额外的`name`字段是必需的，如`[[constraint]]`和`[[override]]`应该是[源根](glossary.md#source-root)，不只是任何导入路径.

```toml
[prune]
  non-go = true

  [[prune.project]]
    name = "github.com/project/name"
    go-tests = true
    non-go = false
```

几乎所有的项目都会很好，没有设置任何项目特定的规则，并且可以在全球范围内启用以下修剪规则:

```toml
[prune]
  unused-packages = true
  go-tests = true
```

通常安全设置.`non-go = true`也一样.然而，由于dep仅对Go文件所扮演的角色有一个清晰的模型，并且非Go文件必然落在该模型之外，因此不可能有可比较的一般安全定义.

## `noverify`

这个`noverify`字段是路径的列表，通常是[项目根](glossary.md#project-root)排除[供应商验证](glossary.md#vendor-verification).

DEP使用每个项目散列摘要，修剪后计算并记录在[格洛克船闸](Gopkg.lock.md#digest)，确定内容是否`vendor/`果不其然.如果记录的摘要和相应树的哈希`vendor/`不同的是，该项目被认为是不同步的:

-   `dep ensure`会再生它
-   `dep check`将抱怨散列失配并退出1

强烈建议你离开.`vendor/`未修改的，无论DEP把它放在什么状态.然而，这并不总是可行的.如果你除了修改别无选择`vendor/`对于特定项目，然后将该项目的项目根添加到`noverify`. 这将产生以下影响:

-   `dep ensure`将忽略项目的哈希错配，并仅在其中重新生成`vendor/`如果绝对必要(修剪选项更改，包列表更改，版本更改)
-   `dep check`将继续报告散列错配(尽管有注释)`noverify`对于该项目，将不再退出1.

`noverify`也可用于保存某些多余的路径，否则会被删除;例如，添加`WORKSPACE`到`noverify`列表将允许你保存`vendor/WORKSPACE`这可以帮助一些基于BZEL的工作流.

## 范围

`dep`评价

-   `[[override]]`
-   `required`
-   `ignored`

仅在根项目中，即项目在哪里`dep`跑.例如，如果你有一个项目:`github.com/urname/goproject`和`github.com/foo/bar`是项目的依赖项，那么DEP将评估`Gopkg.toml`这些项目的文件如下:

| github.com/urname/goproject | github.com/foo/bar |
| --------------------------- | ------------------ |
| \[[constraint]] ✔ | \[[constraint]] ✔ |
| \[[override]] ✔ | \[[override]] ✖ |
| required ✔ | required ✖ |
| ignored ✔ | ignored ✖ |

评价:未评估

# 例子

样本`Gopkg.toml`大多数元素存在:

```toml
required = ["github.com/user/thing/cmd/thing"]

ignored = [
  "github.com/user/project/pkgX"，
  "bitbucket.org/user/project/pkgA/pkgY"
]

noverify = ["github.com/something/odd"]

[metadata]
codename = "foo"

[prune]
  non-go = true

  [[prune.project]]
    name = "github.com/project/name"
    go-tests = true
    non-go = false

[[constraint]]
  name = "github.com/user/project"
  version = "1.0.0"

  [metadata]
  property1 = "value1"
  property2 = 10

[[constraint]]
  name = "github.com/user/project2"
  branch = "dev"
  source = "github.com/myfork/project2"

[[override]]
  name = "github.com/x/y"
  version = "2.4.0"

  [metadata]
  propertyX = "valueX"
```
