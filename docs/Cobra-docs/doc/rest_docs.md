# Generating ReStructured Text Docs For Your Own cobra.Command

从 cobra 命令生成 rest 手册非常容易。下面一个例子: 

```go
package main

import (
	"log"

	"github.com/spf13/cobra"
	"github.com/spf13/cobra/doc"
)

func main() {
	cmd := &cobra.Command{
		Use:   "test",
		Short: "my test program",
	}
	err := doc.GenReSTTree(cmd, "/tmp")
	if err != nil {
		log.Fatal(err)
	}
}
```

 这将为你提供一个 rest 手册 `/tmp/test.rst`

## Generate ReST docs for the entire command tree

该程序实际上可以为kubernetes项目中的kubectl命令生成文档

```go
package main

import (
	"log"
	"io/ioutil"
	"os"

	"k8s.io/kubernetes/pkg/kubectl/cmd"
	cmdutil "k8s.io/kubernetes/pkg/kubectl/cmd/util"

	"github.com/spf13/cobra/doc"
)

func main() {
	kubectl := cmd.NewKubectlCommand(cmdutil.NewFactory(nil), os.Stdin, ioutil.Discard, ioutil.Discard)
	err := doc.GenReSTTree(kubectl, "./")
	if err != nil {
		log.Fatal(err)
	}
}
```

这将生成一系列文件，一个用于树中的每个命令，在指定的目录中（在本例中为`./`）

## Generate ReST docs for a single command

你可能希望更多地控制输出，或仅生成单个命令，而不是整个命令树。如果是这种情况，你可能更喜欢`GenReST`而不是`GenReSTTree`

```go
	out := new(bytes.Buffer)
	err := doc.GenReST(cmd, out)
	if err != nil {
		log.Fatal(err)
	}
```

这会将只有`cmd`的ReST文档写入输出缓冲区。

## Customize the output

`GenReST`和`GenReSTTree`都有备用版本，带有回调以控制输出:

```go
func GenReSTTreeCustom(cmd *Command, dir string, filePrepender func(string) string, linkHandler func(string, string) string) error {
	//...
}
```

```go
func GenReSTCustom(cmd *Command, out *bytes.Buffer, linkHandler func(string, string) string) error {
	//...
}
```

`filePrepender`将在给定完整文件路径的情况下将返回值添加到呈现的 ReST 文件中。一个常见的用例是添加前端内容，使用[Hugo](http://gohugo.io/)生成的文档:

```go
const fmTemplate = `---
date: %s
title: "%s"
slug: %s
url: %s
---
`
filePrepender := func(filename string) string {
	now := time.Now().Format(time.RFC3339)
	name := filepath.Base(filename)
	base := strings.TrimSuffix(name, path.Ext(name))
	url := "/commands/" + strings.ToLower(base) + "/"
	return fmt.Sprintf(fmTemplate, now, strings.Replace(base, "_", " ", -1), base, url)
}
```

在给定命令名和参考的情况下，`linkHandler`可用于自定义命令的呈现链接。这在将rst转换为html或使用Sphinx等工具生成文档时非常有用，其中使用了`:ref:`:

```go
// Sphinx cross-referencing format
linkHandler := func(name, ref string) string {
    return fmt.Sprintf(":ref:`%s <%s>`", name, ref)
}
```
