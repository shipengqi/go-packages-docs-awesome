# Generating Markdown Docs For Your Own cobra.Command

从 cobra 命令生成 Markdown 文档非常容易。下面一个例子:

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
	err := doc.GenMarkdownTree(cmd, "/tmp")
	if err != nil {
		log.Fatal(err)
	}
}
```

这将为你提供一个 Markdown 文档`/tmp/test.md`

## Generate markdown docs for the entire command tree

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
	err := doc.GenMarkdownTree(kubectl, "./")
	if err != nil {
		log.Fatal(err)
	}
}
```

这将生成一系列文件，一个用于树中的每个命令，在指定的目录中（在本例中为`./`）

## Generate markdown docs for a single command

你可能希望更多地控制输出，或仅生成单个命令，而不是整个命令树。如果是这种情况，你可能更喜欢`GenMarkdown`而不是`GenMarkdownTree`

```go
	out := new(bytes.Buffer)
	err := doc.GenMarkdown(cmd, out)
	if err != nil {
		log.Fatal(err)
	}
```

这会将只有`cmd`的 markdown文档写入输出缓冲区。

## Customize the output

`GenMarkdown`和`GenMarkdownTree`都有备用版本，带有回调以控制输出:

```go
func GenMarkdownTreeCustom(cmd *Command, dir string, filePrepender, linkHandler func(string) string) error {
	//...
}
```

```go
func GenMarkdownCustom(cmd *Command, out *bytes.Buffer, linkHandler func(string) string) error {
	//...
}
```

`filePrepender`将在给定完整文件路径的情况下将返回值添加到呈现的Markdown文件中。一个常见的用例是添加前端内容，使用[Hugo](http://gohugo.io/)生成的文档:

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

给定文件名，`linkHandler`可用于自定义呈现的命令内部链接:

```go
linkHandler := func(name string) string {
	base := strings.TrimSuffix(name, path.Ext(name))
	return "/commands/" + strings.ToLower(base) + "/"
}
```
