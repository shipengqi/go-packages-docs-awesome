# 求解器

dep的核心是约束求解引擎 -  a[CDCL](https://en.wikipedia.org/wiki/Conflict-Driven_Clause_Learning)风格的求解器(虽然在"CL"部分点亮)，专门针对Go包管理领域量身定制.它生活在`github.com/golang/dep/gps`包，并且是确定有效的，可传递完整的依赖图(也就是说，的内容)的工作`Gopkg.lock`)执行.

这个页面最终将详细介绍求解器的机制，但与此同时，还有[旧版解算器的文档](https://github.com/sdboyer/gps/wiki/gps-for-Contributors)仍然足够准确，以提供其行为的粗略图片.

## 解决不变量

求解器在它返回的每个完整解决方案中保证某些不变量.稍后将详细探讨每个不变量，但它们可归纳如下:

-   激活中指定的所有规则`[[constraint]]`除非被a取代，否则当前项目和依赖项目中的节将得到满足`[[override]]`当前项目中的节.
-   对于指向给定项目的所有导入路径，所选项目的版本将在相应目录中包含"有效"Go包.
-   如果[导入评论](https://golang.org/cmd/go/#hdr-Import_path_checking)由包指定，任何寻址该包的导入路径都将是注释中指定的形式.
-   对于任何给定的导入路径，该导入路径的所有实例将使用完全相同的外壳.

求解器是一种迭代算法，通过可能的依赖图逐个项目地工作.为了选择一个项目，它必须首先证明，就其目前的知识而言，所有上述条件都得到满足.当解算器找不到解决方案时，根据项目版本无法满足上述标准之一来定义失败.

### `[[constraint]]`规则

如中描述的那样`Gopkg.toml`docs，每个[`[[constraint]]`](Gopkg.toml.md#constraint)节与一个项目相关联，每个节都可以包含这两个节[版本规则](Gopkg.toml.md#version-rules)和a[来源规则](Gopkg.toml.md#source).对于任何给定的项目`P`，所有依赖项`P`其约束规则被"激活"必须表达相互兼容的规则.这意味着:

-   对于版本规则，所有激活的约束`P`必须[相交](https://en.wikipedia.org/wiki/Intersection_(set_theory))，并且必须至少有一个已发布的版本必须存在于相交空间中.交叉点因版本规则类型而异:
    -   对于`revision`和`branch`，它必须是字符串 - 文字匹配.
    -   对于`version`，如果字符串不是有效的语义版本，那么它必须是字符串 - 文字匹配.
    -   对于`version`作为有效的语义版本范围，交集是每个范围范围内的可能值的标准集合理论交集.没有范围的语义版本被视为单个元素集(例如，`version = "=v1.0.0"`)用于交叉目的.
-   对于`source`规则，具有特定依赖关系的所有项目必须表达字符串相等`source`价值，或没有`source`价值.这允许一个依赖项指定备用`source`，如果他们没有意见，还有其他依赖关系.(注意:在将来的版本中可能会删除此播放行为.)

如果当前项目的`Gopkg.toml`有一个[`[[override]]`](Gopkg.toml.md#override)上`P`那么所有`[[constraint]]`声明(包括当前项目中的任何声明)被忽略，从而避免了冲突的可能性.

#### 激活约束

只因为一个`[[constraint]]`上`P`出现在`D`的`Gopkg.toml`并不一定意味着约束`P`被认为是活跃的.一个包裹`P`必须由包中导入`D`- 而如果`D`不是当前项目，然后是其中一个包导入`P`也必须进口.

给出以下依赖图，其中`C`是目前的项目:

```
C -> D
C -> P
D/subpkg -> P
```

即使`C`进口`D`因为`D/subpkg`无法通过`C`进口，任何`[[constraint]]`声明`D`的`Gopkg.toml`' 上`P`不会活跃.

进一步解释了这种行为背后的原因[在这个要点](https://gist.github.com/sdboyer/b0813bf2b9dba58a335a85092085472f).

### 包装有效期

dep只对包中的代码进行表面验证，但它确实做了一些.要使包认为有效，必须满足以下三个条件:

-   必须至少有一个`.go`文件.
-   没有报告错误[`parser.ParseFile()`](https://golang.org/pkg/go/parser/#ParseFile)当被召唤时[`parser.ImportsOnly|parser.ParseComments`](https://golang.org/pkg/go/parser/#Mode)在包目录中的任何文件上.


-   包裹不得包含任何物品[当地进口](https://golang.org/pkg/go/build/#IsLocalImport).注意:这不允许标准工具链编译器允许的东西，这通常意味着dep必须支持它.但是，在工具链中已经强烈建议不要使用本地导入，并且跳过它们可以避免dep[点点地狱](https://9p.io/sys/doc/lexnames.html).

如果上述任何内容不真实，则包中的代码将被视为格式错误，并且无法在解决方案中使用.

如果项目仅包含一些无效的包裹，则不会立即取消其资格;必须导入它们才能使不变量被破坏.因此，如果`P/invalid`是一个包含无效代码的子包，如果是，则仍然可以接受`C -> P`.但是，内部进口`P`也考虑了，所以这个进口链:

```
C -> P
P -> invalid
```

会导致错误，如`C`导入必然导致导入无效包的包.

### 导入评论

去1.4介绍[导入评论](https://golang.org/cmd/go/#hdr-Import_path_checking)，允许包指定在寻址时必须使用的导入路径.例如，`import "github.com/golang/net/dict"`会指向一个有效的包，但因为[它使用导入注释](https://github.com/golang/net/blob/42fe2e1c20de1054d3d30f82cc9fb5b41e2e3767/dict/dict.go#L7)强制它必须导入为`golang.org/x/net/dict`，dep将拒绝任何试图通过其github地址直接导入它的项目.

由于大多数项目的导入注释使用时间一致，因此通常仅在添加新依赖项或尝试恢复旧项目时才会出现此问题.

> 注意:dep目前不强制执行此规则，但是[它需要](https://github.com/golang/dep/issues/902).

**修复:**通过修复违规导入路径来更改代码.如果违规导入路径不在当前项目中并且你没有直接控制依赖项，则必须自行分叉并修复它，然后使用`source`指向你的前叉.

### 导入路径套管

标准的Go工具链编译器[才不是](https://github.com/golang/go/issues/4773) [允许](https://github.com/golang/go/issues/20264)导入路径只有在同一版本中存在的情况下才会有所不同.例如，任何一个`github.com/sirupsen/logrus`要么`github.com/Sirupsen/logrus`很好(GitHub将用户名视为不区分大小写)，但它们不能存在于同一个项目中.

解算器会跟踪已处理的每个导入路径的已接受案例变体.它看到的任何后续项目都会拒绝为已知导入路径引入仅包含案例的变体.

**修复:**选择一个套管变体(全部小写通常是正确的答案)，并在整个depgraph中强制执行它.因为它必须在所有依赖项中受到尊重，如果你不直接控制它们，这可能需要拉取请求并可能需要分析依赖项.
