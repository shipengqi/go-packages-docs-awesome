# FAQ

FAQ之前的其余部分的介绍.如果这里的东西与其他指南或参考文件冲突，可能在这里是错误的-请提交PR!

## Concepts

-   [做`dep`代替`go get`?](#does-dep-replace-go-get)
-   [为什么是`dep ensure`而不是`dep install`?](#why-is-it-dep-ensure-instead-of-dep-install)
-   [什么是直接的或传递的依赖关系?](#what-is-a-direct-or-transitive-dependency)

## Configuration

-   [Gopkg.toml("清单")和Gopkg.lock("锁")的区别是什么?](#what-is-the-difference-between-gopkgtoml-the-manifest-and-gopkglock-the-lock)
-   [如何约束传递依赖的版本?](#how-do-i-constrain-a-transitive-dependency-s-version)
-   [如何更改依赖项的版本?](#how-do-i-change-the-version-of-a-dependency)
-   [我能把清单和锁放在供应商目录中吗?](#can-i-put-the-manifest-and-lock-in-the-vendor-directory)
-   [我如何得到`dep`对…进行认证`git`回购协议?](#how-do-i-get-dep-to-authenticate-to-a-git-repo)
-   [我如何得到`dep`私下消费`git`使用GITHUB令牌吗?](#how-do-i-get-dep-to-consume-private-git-repos-using-a-github-token)

## Behavior

-   [如何`dep`决定使用什么版本的依赖项?](#how-does-dep-decide-what-version-of-a-dependency-to-use)
-   [默认值是多少`dep ensure -update`依赖于导入的但不包含为一个依赖项的行为`[[Constraint]]`在里面`Gopkg.toml`?](#what-is-the-default-dep-ensure--update-behavior-for-dependencies-that-are-imported-but-not-included-as-a-constraint-in-gopkgtoml)
-   [支持哪些外部工具?](#what-external-tools-are-supported)
-   [为什么是`dep`忽略清单中的版本约束?](#why-is-dep-ignoring-a-version-constraint-in-the-manifest)
-   [为什么`dep`对包X使用不同的修改，而不是锁定文件中的修订?](#why-did-dep-use-a-different-revision-for-package-x-instead-of-the-revision-in-the-lock-file)
-   [为什么是`dep`慢?](#why-is-dep-slow)
-   [如何`dep`处理符号链接?](#how-does-dep-handle-symbolic-links)
-   [做`dep`支持相对进口?](#does-dep-support-relative-imports)
-   [我该怎么做`dep`解决依赖关系`GOPATH`?](#how-do-i-make-dep-resolve-dependencies-from-my-gopath)
-   [威尔`dep`让我使用Git子模块来存储依赖项`vendor`?](#will-dep-let-me-use-git-submodules-to-store-dependencies-in-vendor)
-   [如何`dep`不改变我的套餐进口吗?](#how-does-dep-work-without-changing-my-packages-imports)

## Best Practices

-   [我应该提交我的供应商目录吗?](#should-i-commit-my-vendor-directory)
-   [如何滚动发布`dep`能用吗?](#how-do-i-roll-releases-that-dep-will-be-able-to-use)
-   [我应该使用什么样的SEMVER版本?](#what-semver-version-should-i-use)
-   [现在做出前后不兼容的变化是否可以?](#is-it-ok-to-make-backwards-incompatible-changes-now)
-   [我的家属不使用`dep`然而.我该怎么办?](#my-dependers-don-t-use-dep-yet-what-should-i-do)
-   [如何配置不标记其发布的依赖项](#how-do-i-configure-a-dependency-that-doesn-t-tag-its-releases)
-   [如何使用`dep`和Docker在一起?](#how-do-i-use-dep-with-docker)
-   [如何使用`dep`在CI?](#how-do-i-use-dep-in-ci)

## Concepts

### Does `dep` replace `go get`?

不.`dep`和`go get`服务主要是不同的目的.

以下是一些你可以使用的建议`dep`或`go get`:

> 我认为DEP并不能代替Go GET，但它们都可以做类似的事情.以下是我如何使用它们:
>
> `go get`我想下载一个GO项目的源代码，这样我就可以自己动手，或者安装一个工具.这克隆了GopaTM下的RPO，供所有人使用.
>
> `dep ensure`我在代码中导入了一个新的依赖项，希望下载依赖关系，这样我就可以开始使用它了.我的工作流程是"将导入添加到代码中，然后运行DEP确保，以便更新清单/锁/供应商".这将复制项目供应商目录下的回购，并记住所使用的修订版本，以便保证每个从事项目工作的人都使用相同版本的依赖项.
>
> [公元376年的卡罗来纳](https://github.com/golang/dep/issues/376#issuecomment-293964655)
>
> 长远的眼光是一个明智的，整体一致的GO工具.我的将军是这样的`go get`是用于消耗GO代码的人，DEP家族命令是为开发它的人使用的.
>
> ["376"中的SDBy耶](https://github.com/golang/dep/issues/376#issuecomment-294045873)

### Why is it `dep ensure` instead of `dep install`?

> 是的，我们到处找名字.[很多](https://gist.github.com/jessfraz/315db91b272441f510e81e449f675a8b).
>
> "确保"的概念大致是"确保我所有的本地状态——代码树、清单、锁和供应商——彼此同步."当参数被传递时，它变成"确保这个参数被满足，以及所有本地状态之间的同步."
>
> 我们之所以选择这种方法，是因为我们得出的结论是，允许工具在中间状态执行部分工作/退出最终会创建一个具有更多命令的工具，具有更多可能的有效退出和输入状态，并且通常充满了步枪.在这种方法中，用户拥有大多数相同的最终控制，但是行使的方式不同(通过修改代码/清单和重新运行dep来确保).
>
> ["371"中的SDBy耶](https://github.com/golang/dep/issues/371#issuecomment-293246832)

### What is a direct or transitive dependency?

-   直接依赖项是由项目直接导入的依赖项:它们出现在来自项目的至少一个导入语句中.
-   传递依赖关系是依赖关系的依赖关系.编译所需的，但不直接由代码使用.

## Configuration

### What is the difference between `Gopkg.toml` (the "manifest") and `Gopkg.lock` (the "lock")?

> 清单描述用户意图，并且锁描述计算输出.在锁中不存在的清单中具有灵活性，因为"分支":"主"约束将匹配当前版本主HAPPENS的任何版本，而锁被固定到特定的版本.
>
> 这种灵活性很重要，因为它允许我们提供简单的命令(例如).`dep ensure -update`它可以在你指定的约束内为你管理更新过程，因为它允许你的项目(当由其他人导入时)协作地指定你自己的依赖项的约束.
>
> ["281"中的SDBy耶](https://github.com/golang/dep/issues/281#issuecomment-284118314)

## <a id="how-do-i-constrain-a-transitive-dependency-s-version"></a>How do I constrain a transitive dependency's version?

首先，如果你对此感到疑惑，因为你正试图防止传递依赖的版本发生更改，那么你将处理以下问题`dep`的设计.锁定文件，`Gopkg.lock`它将使所选版本的传递依赖关系保持稳定，除非你显式地请求升级，或者无法在不更改该版本的情况下找到解决方案.

如果这不是你的用例，你仍然需要限制传递依赖性，那么你有几个选项:

1.  使传递依赖性成为直接的依赖关系，既可以使用虚拟导入，也可以使用`required`列入`Gopkg.toml`.
2.  使用重写.

超越是一把大锤，只能作为最后的手段.同时以相同的方式声明约束和重写.`Gopkg.toml`他们的行为不同:

-   制约因素:
    1.  可以通过任何项目清单来声明，你的清单或依赖项
    2.  仅应用于声明约束的项目的直接依赖关系
    3.  不得与之冲突`constraint`在任何其他项目清单中声明的条目
-   超越:
    1.  仅从当前/项目清单中使用
    2.  全局应用，以直接和传递依赖关系
    3.  取代所有清单中声明的约束，你的约束或依赖项的约束

还讨论了在一些可视化中的重写.[GPS文档](https://github.com/sdboyer/gps/wiki/gps-for-Implementors#overrides).

## How do I change the version of a dependency

如果你想:

-   更改允许`version`/`branch`/`revision`
-   切换到使用叉子

对于一个或多个依赖项，请执行以下操作:

1.  手动编辑你的`Gopkg.toml`.
2.  跑

    ```sh
    $ dep ensure
    ```

## Can I put the manifest and lock in the vendor directory?

不.

> 把这些文件放在里面`vendor/`会把我们具体地束缚在一起`vendor/`从长远来看.我们更喜欢对待`vendor/`作为实现细节.
>
> [打包管理列表](https://groups.google.com/d/msg/go-package-management/et1qFUjrkP4/LQFCHP4WBQAJ)

## How do I get dep to authenticate to a git repo?

`dep`目前使用`git`在引擎盖下的命令，因此为你希望认证的每个存储库配置凭据以允许`dep`使用已验证的存储库.

首先，配置`git`使用特定存储库的凭据选项.

例如，如果你使用GITLAB，并且你希望访问`https://gitlab.example.com/example/package.git`然后，你将希望使用以下配置:

```
$ git config --global credential.https://gitlab.example.com.example yourusername
```

在该示例中，主机名`gitlab.example.com.example`字符串似乎不正确，但实际上是主机名加上你正在访问的回购协议的名称.`username`. 尾随"你的用户名"是你将用于实际身份验证的用户名.

你还需要配置`git`使用你希望使用的身份验证提供者.你可以通过以下命令获得提供者列表:

```
$ git help -a | grep credential-
  credential-cache          remote-fd
  credential-cache--daemon  remote-ftp
  credential-osxkeychain    remote-ftps
  credential-store          remote-http
```

然后选择合适的提供者.例如，要使用OXKEY链，你将使用以下内容:

```
git config --global credential.helper osxkeychain
```

如果你需要为CI系统这样做，那么你可能希望使用"存储"提供程序.请参阅有关如何配置该文档的文档:[HTTPS://GIT-SCM.COM/DOCS/GIT-Cuto存储](https://git-scm.com/docs/git-credential-store)

配置后`git`你可能需要使用`git`手动一次让它存储凭据.一旦你手动检查了回购协议，它将使用存储的凭据.这至少是OXKEY链提供程序的行为.

### How do I get dep to consume private git repos using a GitHub Token?

另一种选择`dep`与私人回购公司合作使用A[个人GITHUB令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)并将其配置在[`.netrc`文件](https://www.gnu.org/software/inetutils/manual/html_node/The-_002enetrc-file.html)如下面的例子:

```
machine github.com
    login [YOUR_GITHUB_USERNAME]
    password [YOUR_GITHUB_TOKEN]
```

一旦设置好，DEP将自动使用该令牌对存储库进行身份验证.

## How do I get dep to authenticate via SSH to a git repo?

你可以重写RePo URL，并使用GIT+SSH SeMMA，以下示例:

```
git config --global url."git@github.yourEnterprise.com:".insteadOf "https://github.yourEnterprise.com/"
```

## Behavior

### How does `dep` decide what version of a dependency to use?

完整的算法是复杂的，但最重要的是要理解的是`dep`在A中尝试版本[一定顺序](https://godoc.org/github.com/golang/dep/gps#SortForUpgrade)根据指定的约束检查是否可以接受一个版本.

-   所有的SSEVER版本都是第一种，主要根据Sever 2规范进行排序，有一个例外:
    -   Sever版本的预存排序后*全部的*非预处理的SEMVER.在这个子集中，它们首先由它们的数值成分排序，然后按它们的预版本进行词典编纂.
-   接下来是缺省分支;"缺省分支"的语义是特定于底层源类型的，但是这通常是从`go get`.
-   所有其他分支都是下一个，按词典分类.
-   接下来，所有非半版本版本(标签)按字典排序.
-   如果有的话，修订是最后的，词典编纂.修订通常不会出现在版本列表中，所以我们保持的唯一不变性就是确定性——更深层的语义，比如时间或拓扑，并不重要.

因此，给出了以下的一个版本:

-   Branch:`master` `devel`
-   Simver标签:`v1.0.0` `v1.1.0` `v1.1.0-alpha1`
-   非域名标签:`footag`
-   修订:`f6e74e8d`

排序升级将导致以下切片:

`[v1.1.0 v1.0.0 v1.1.0-alpha1 master devel footag f6e74e8d]`

有许多因素可以消除对版本的考虑，其中最简单的是它与约束不匹配.但是如果你想弄明白为什么`dep`就是做它做的事情，理解它的基本作用是尝试按照这种顺序进行版本，这应该可以帮助你对正在发生的事情进行推理.

## What is the default `dep ensure -update` behavior for dependencies that are imported but not included as a `[[Constraint]]` in `Gopkg.toml`?

`dep`更新对最新的Sever标签的依赖关系.如果没有SAMVER标签，`dep`使用大师的提示.

## What external tools are supported?

期间`dep init`从其他依赖管理器的配置被检测并导入，除非`-skip-tools`指定.

支持以下工具:`glide`，`godep`，`vndr`，`govend`，`gb`，`gvt`，`govendor`和`glock`.

见[α186](https://github.com/golang/dep/issues/186#issuecomment-306363441)如何添加对另一个工具的支持.

## Why is `dep` ignoring a version constraint in the manifest?

只有项目的直接导入依赖项受`constraint`清单中的条目.传递依赖性不受影响.见[如何约束传递依赖的版本](#how-do-i-constrain-a-transitive-dependency-s-version)?

## Why did `dep` use a different revision for package X instead of the revision in the lock file?

有时在锁定文件中指定的修订不再有效.这可能会发生以下几种情况:

-   生成锁文件时，在你的包X存储库的本地副本中有一个未提交的提交.`GOPATH`. (这个案子很快就要结束了)
-   在生成锁文件之后，新的提交被强制推送到包X的存储库，导致锁文件中的提交修订不再存在.

若要排除故障，可以将DEP的更改还原为锁，然后运行.`dep ensure -v -n`. 此命令在启用了冗长日志的干运行模式下重试命令.检查输出，如下面的警告，指示锁中的提交不再有效.

```
Unable to update checked out version: fatal: reference is not a tree: 4dfc6a8a7e15229398c0a018b6d7a078cccae9c8
```

> 锁定文件表示一组精确的、通常是不可变的版本，用于项目的整个依赖项的传递闭包.但是，"项目"可以分解为一组算法的参数.当这些输入改变时，锁也可能需要改变.
>
> 在大多数情况下，如果这些参数不变，那么锁仍然是正确的.你碰到了少数情况下保证不适用的情况之一.你运行dep.，并且它DID了一个解决方案，这个事实是某些参数变化的产物;这个解决方案失败了，因为这个特定的提交已经过时了，这是一个单独的问题.
>
> ["405"中的SDBy耶](https://github.com/golang/dep/issues/405#issuecomment-295998489)

## Why is `dep` slow?

有两件事真的很慢`dep`下来.一个是不可避免的，另一个是我们有一个计划.

不可避免的部分是最初的克隆.`dep`依赖于本地存储库的缓存(存储在`$GOPATH/pkg/dep`)，这是按需填充的.不幸的是，第一`dep`运行，特别是对于大型项目，可能需要一段时间，因为所有依赖项都被克隆到缓存中.

幸运的是，这只是一个*最初的*克隆-支付一次，然后你就完成了.当你跑步的时候，这个问题会有点重复.`dep`这是第一次，有新的变更集来获取，但即使如此，这些成本只在变更集支付一次.

另一部分是检索依赖关系信息的工作.这里有三个部分:

1.  从上游源获得最新版本的列表
2.  阅读`Gopkg.toml`对于本地缓存之外的特定版本
3.  在特定版本中解析导入语句的包树

第一个需要一个或多个网络调用;第二个通常意味着类似于`git checkout`第三个是文件系统行走，加上加载和解析.`.go`文件夹.所有这些都是昂贵的操作.

幸运的是，我们可以缓存第二个和第三个.而且，当对一个不可变的标识符键入一个Git提交的Sh1散列时，该缓存可以是永久的.第一个有点棘手，但我们可以考虑避免网络完全的合理的权衡.有个问题要解决[实现持久缓存](https://github.com/golang/dep/issues/431)这是所有这些改进的大门.

还有一个主要的性能问题要困难得多——选择版本本身的过程是一个NP完全问题.`dep`目前的设计.这是一个棘手的问题.

## How does `dep` handle symbolic links?

> 因为我们不是疯狂的人，他们喜欢把混乱带入我们的生活，我们需要在一个内部工作.`GOPATH`一次.-["247"中的SDBy耶](https://github.com/golang/dep/pull/247#issuecomment-284181879)

为了方便起见，人们可能会在它们的目录中创建一个链接.`GOPATH/src`，例如`ln -s ~/go/src/github.com/user/awesome-project ~/Code/awesome-project`.

什么时候?`dep`用一个SyLink的项目根来调用，它将按照以下规则来解决:

-   如果SyLink在外部`GOPATH`和一个目录内的链接`GOPATH`然后，反之亦然`dep`将选择内部路径`GOPATH`.
-   如果SyLink在一个`GOPATH`解决的路径在一个*不同的* `GOPATH`然后引发错误.
-   如果SyLink和解析路径都是相同的`GOPATH`然后引发错误.
-   如果SyLink和解析路径都不在`GOPATH`然后引发错误.

这是唯一的符号链接支持`dep`真的打算提供.与一般惯例相一致`go`工具，`dep`倾向于忽略Salink(当步行)或复制Simulink本身，这取决于正在执行的文件系统操作.

## Does `dep` support relative imports?

不.

> DEP根本不允许相对进口.这是少数几个限制工具链本身允许的情况之一.我们不允许他们仅仅是因为:
>
> -   工具链已经对他们产生了严重的不满.<br>
> -   这对我们的情况来说更糟，因为我们开始冒险.[点地狱](http://doc.cat-v.org/plan_9/4th_edition/papers/lexnames)领土当试图证明进口不逃脱项目树
>
> ["899"中的SDBy耶](https://github.com/golang/dep/issues/899#issuecomment-317904001)

有关GO的推荐工作区组织的更新程序，请参见["如何编写GO代码"](https://golang.org/doc/code.html)在GO文档中的文章.以这种方式组织代码，可以为每个包提供唯一的导入路径.

## How do I make `dep` resolve dependencies from my `GOPATH`?

`dep init`提供扫描的选项`GOPATH`通过依赖来做依赖`dep init -gopath`当没有找到包时，它返回到网络模式.`GOPATH`. `dep ensure`不与项目一起工作`GOPATH`.

## Will `dep` let me use git submodules to store dependencies in `vendor`?

不，只有一个小小的例外:`dep`蜜饯`/vendor/.git`，如果它存在.这是在[蟑螂](https://github.com/cockroachdb/cockroach)的请求，谁依靠它来保持`vendor`从他们的主要储存库膨胀.

Git子模块不是DEP的一部分的原因最好表示为Pro/CON列表:

**赞成的意见**

-   Git子模块提供了一种结构良好的嵌套存储库的方法.

**欺骗**

-   g it子模块执行的嵌套并不比dep已经执行的功能更强大，也更富表现力，但是dep更一般(对于bzr和hg)并且更具体地进行域嵌套(例如，消除嵌套的供应商目录).
-   以任何方式合并git子模块都会在逻辑中产生新的路径来处理子模块情况，这意味着非平凡的复杂性增加.
-   DEP目前不知道或关心它运行的项目是否在版本控制之下.依赖子模块将意味着DEP开始关注这一点.它只会有条件地不能使它更好——逻辑上更复杂的路径，更复杂.
-   将子模块以一种对用户可见的方式进行整合(为什么你会这样做?)使DEP的工作流程更加复杂和不可预测:*有时*预期与子模块相关的行动;*有时*子模块派生的工作流是足够的.
-   在另一个库中嵌套一个存储库意味着，可以直接在该子库中进行更改.这与DEP的基本原理是相反的.`vendor`是死代码，直接修改任何东西都有错误.

## How does `dep` work without changing my packages imports?

`dep`不需要进口(或`$GOPATH`要更新，因为[GO从版本1.5开始对供应商目录进行本地支持](https://golang.org/cmd/go/#hdr-Vendor_Directories). 你不需要更新导入路径是相对的.例如，`import github.com/user/awesome-project`将在项目中找到`/vendor/github.com/user/awesome-project`在寻找之前`$GOPATH/src/github.com/user/awesome-project`.

## Best Practices

### Should I commit my vendor directory?

这取决于你:

**赞成的意见**

-   这是获得真正可复制的构建的唯一方法，因为它防止上游重命名、删除和提交历史重写.
-   你不需要额外的`dep ensure`同步步`vendor/`具有`Gopkg.lock`在大多数操作之后，例如`go get`克隆、获得最新、合并等.

**欺骗**

-   你的回购将更大，潜在的更大，虽然[`prune`](Gopkg.toml.md#prune)可以帮助最小化这个问题.
-   PR差异将包括文件下的更改`vendor/`什么时候?`Gopkg.lock`被修改，但是文件在`vendor/`是[默认隐藏](https://github.com/github/linguist/blob/v5.2.0/lib/linguist/generated.rb#L328)在吉瑟布上.

## How do I roll releases that `dep` will be able to use?

简而言之:确保你已经承诺了自己的承诺.`Gopkg.toml`和`Gopkg.lock`然后在你的版本控制系统中创建一个标签并将其推到标准位置.`dep`被设计为自动地从这种元数据中工作.`git`，`bzr`和`hg`.

你非常喜欢使用它.[塞弗](http://semver.org)兼容的标签名.我们希望尽快开发出更精确的描述文档，但与此同时，[npm](https://docs.npmjs.com/misc/semver)文档匹配我们的模式很好.

## What semver version should I use?

这可能是一个微妙的问题，并且社区将不得不制定一些公认的标准，以便如何将semver应用于Go项目.然而，在最高层，这些规则是:

-   下面`v1.0.0`什么都行.使用这些版本来确定你希望API是什么.
-   上面`v1.0.0`一般的Go最佳实践仍然适用——不要进行向后不兼容的更改——导出的标识符可以添加到，但不能更改或删除.
-   如果必须进行向后不兼容的更改，则点击主版本.

重要的是要注意有一个`v1.0.0`不排除你拥有Alpha/beta / ETC版本.Sever规范允许[预版本](http://semver.org/#spec-item-9)和`dep`小心*不*允许这样的版本，除非`Gopkg.toml`包含显式包含预删除的范围约束:如果存在版本`v1.0.1-alpha4`然后约束`>=1.0.0`不会匹配它，但是`>=1.0.1-alpha1`威尔.

已经做了一些工作.[一个工具](https://github.com/bradleyfalzon/apicompat)这将分析和比较你的代码和最后一个版本，并建议你应该使用的下一个版本.

## Is it OK to make backwards-incompatible changes now?

对.但是.

`dep`将使GO生态系统能够更优雅地处理落后的不兼容变化.然而，`dep`不是某种神奇的灵丹妙药.版本和依赖性管理是硬的，依赖性地狱是真实的.长期以来避免破坏变化的社区智慧仍然很重要.任何`v1.0.0`发布应该伴随着如何避免将来打破API更改的计划.

一个好的策略可能是添加到API中，而不是改变它，在旧进程中删去旧版本.然后，当时间是正确的，你可以滚动一个新的主要版本，并清理出一堆废弃的符号一次.

请注意，在突破性变化(即，垫片)上提供增量迁移路径是棘手的，而且是我们的一些事情.[还没有好的答案](https://groups.google.com/forum/#!topic/go-package-management/fp2uBMf6kq4).

## <a id="my-dependers-don-t-use-dep-yet-what-should-i-do"></a>My dependers don't use `dep` yet. What should I do?

在大多数情况下，你不需要做任何不同的事情.

唯一可能的问题是，你的项目是否曾经作为一个图书馆被消耗掉.如果是这样的话，那么你可能会对你的承诺提心吊胆.`vendor/`目录，因为它可以[原因问题](https://groups.google.com/d/msg/golang-nuts/AnMr9NL6dtc/UnyUUKcMCAAJ). 如果你的家属正在使用`dep`这不是一个问题，因为`dep`注意剥离嵌套`vendor`目录.

## <a id="how-do-i-configure-a-dependency-that-doesn-t-tag-its-releases"></a>How do I configure a dependency that doesn't tag its releases?

添加约束`Gopkg.toml`指定的`branch: "master"`(或者你需要的分支)`[[constraint]]`对于这种依赖性.`dep ensure`将确定当前对依赖关系的主分支的修订，并将其放置在`Gopkg.lock`为你.参见:[GopkG.ToML和Gopk.God的区别是什么?](#what-is-the-difference-between-gopkgtoml-the-manifest-and-gopkglock-the-lock)

## How do I use `dep` with Docker?

`dep ensure -vendor-only`从有效的文件夹中创建供应商文件夹`Gopkg.toml`和`Gopkg.lock`不检查GO代码.这对于使用缓存层在DOCKER内部构建尤其有用.

Sample Dockerfile:

```Dockerfile
FROM golang:1.9 AS builder

RUN curl -fsSL -o /usr/local/bin/dep https://github.com/golang/dep/releases/download/vX.X.X/dep-linux-amd64 && chmod +x /usr/local/bin/dep

RUN mkdir -p /go/src/github.com/***
WORKDIR /go/src/github.com/***

COPY Gopkg.toml Gopkg.lock ./
# copies the Gopkg.toml and Gopkg.lock to WORKDIR

RUN dep ensure -vendor-only
# install the dependencies without checking for go code

...
```

## How do I use `dep` in CI?

自从`dep`有望改变，直到`v1.0.0`是发布的，建议依赖发布版本.你可以从中找到最新的二进制文件.[发行版](https://github.com/golang/dep/releases)页.

Travis CI的示例配置:

```yml
# ...

env:
  - DEP_VERSION="X.X.X"

before_install:
  # Download the binary to bin folder in $GOPATH
  - curl -L -s https://github.com/golang/dep/releases/download/v${DEP_VERSION}/dep-linux-amd64 -o $GOPATH/bin/dep
  # Make the binary executable
  - chmod +x $GOPATH/bin/dep

install:
  - dep ensure
```

缓存也可以启用，但有两个注意事项你应该注意:

> 直到最近，我们已经断断续续的高速缓存损坏，这将是非常恼人的，如果它也打破了特拉维斯建设.
>
> 还根据[http://dcs.特拉维斯Cy.com/用户/缓存/不缓存的事物](https://docs.travis-ci.com/user/caching/#Things-not-to-cache)他们不推荐使用更大的缓存.
>
> [http://DOCS.C.C.com/用户/缓存/μi缓存工作如何%3F](https://docs.travis-ci.com/user/caching/#How-does-the-caching-work%3F)
>
> > 注意，这使得我们的缓存不是网络本地的，它仍然绑定到S3的网络带宽和DNS解析.这会影响你可以在缓存中存储的内容.如果你在缓存中存储大于几百兆字节的档案，那么你不太可能看到一个大的速度提升.
>
> [公元1293年的卡罗来纳](https://github.com/golang/dep/pull/1293#issuecomment-342969292)

如果你确信要在特拉维斯上启用缓存，可以通过添加`$GOPATH/pkg/dep`默认位置为`dep`缓存到缓存目录:

```yml
# ...

cache:
  directories:
    - $GOPATH/pkg/dep
```
