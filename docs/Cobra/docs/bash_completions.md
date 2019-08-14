# Generating Bash Completions For Your Own cobra.Command

如果你正在使用 generator，可以通过运行下面的命令来创建补全命名：
```sh
cobra add completion
```

更新帮助文本，显示 linux 如何安装 bash completion，
查看这里 [Kubectl docs show mac options](https://kubernetes.io/docs/tasks/tools/install-kubectl/#enabling-shell-autocompletion)。

```go
// completionCmd represents the completion command
var completionCmd = &cobra.Command{
	Use:   "completion",
	Short: "Generates bash completion scripts",
	Long: `To load completion run

. <(bitbucket completion)

To configure your bash shell to load completions for each session add to your bashrc

# ~/.bashrc or ~/.profile
. <(bitbucket completion)
`,
	Run: func(cmd *cobra.Command, args []string) {
		rootCmd.GenBashCompletion(os.Stdout);
	},
}
```

注意: cobra generator 可能包含打印到 stdout 的消息，例如，如果加载了配置文件，这将破坏自动补全脚本

## Example from kubectl
从一个 cobra 命令生成 bash completion 非常简单。kubernetes kubectl 二进制文件的实际程序如下:
```go
package main

import (
	"io/ioutil"
	"os"

	"k8s.io/kubernetes/pkg/kubectl/cmd"
	"k8s.io/kubernetes/pkg/kubectl/cmd/util"
)

func main() {
	kubectl := cmd.NewKubectlCommand(util.NewFactory(nil), os.Stdin, ioutil.Discard, ioutil.Discard)
	kubectl.GenBashCompletionFile("out.sh")
}
```

`out.sh` 会补全子命令和标志。按[此处](https://debian-administration.org/article/316/An_introduction_to_bash_completion_part_1)描述，
将它复制到 `/etc/bash_completion.d/`，并重置你的 terminal。如果在代码中添加额外的注释，可以获得更加智能和灵活的行为。

## Creating your own custom functions
一些在 kubernetes 中的实际代码：
```go
const (
        bash_completion_func = `__kubectl_parse_get()
{
    local kubectl_output out
    if kubectl_output=$(kubectl get --no-headers "$1" 2>/dev/null); then
        out=($(echo "${kubectl_output}" | awk '{print $1}'))
        COMPREPLY=( $( compgen -W "${out[*]}" -- "$cur" ) )
    fi
}

__kubectl_get_resource()
{
    if [[ ${#nouns[@]} -eq 0 ]]; then
        return 1
    fi
    __kubectl_parse_get ${nouns[${#nouns[@]} -1]}
    if [[ $? -eq 0 ]]; then
        return 0
    fi
}

__kubectl_custom_func() {
    case ${last_command} in
        kubectl_get | kubectl_describe | kubectl_delete | kubectl_stop)
            __kubectl_get_resource
            return
            ;;
        *)
            ;;
    esac
}
`)
```
然后我在命令定义中设置:
```go
cmds := &cobra.Command{
	Use:   "kubectl",
	Short: "kubectl controls the Kubernetes cluster manager",
	Long: `kubectl controls the Kubernetes cluster manager.

Find more information at https://github.com/GoogleCloudPlatform/kubernetes.`,
	Run: runHelp,
	BashCompletionFunction: bash_completion_func,
}
```

`BashCompletionFunction` 选项实际上只在根命令上有效/有用。执行上述操作将导致在内置处理器无法找到解决方案
时调用 `__kubectl_custom_func()` (`__<command-use>_custom_func()`)。在 kubernetes 中，一个有效的命令可能
类似于 `kubectl get pod [mypod]`。如果你键入 `kubectl get pod [tab][tab]`，`__kubectl_custom_func()` 将运行，
因为 `cobra.Command` 只理解 `kubectl` 和 `get`。`__kubectl_custom_func()` 会看到 `cobra.Command` 是 `kubectl get`，
因此将调用另一个 helper `__kubectl_get_resource()`。`__kubectl_get_resource` 会看到所收集的 "nouns"。
在我们的例子中，唯一的名词是 `pod`。所以它会调用 `__kubectl_parse_get pod`。`__kubectl_parse_get` 会 call kubernetes 并
得到 `pod`。然后它将设置 `COMPREPLY` 为有效的 pods!

### Have the completions code complete your 'nouns'
在上面的示例中，假定 "pod" 已经被键入。但是，如果你希望 `kubectl get [tab][tab]` 显示有效的 "nouns" 列表，则必须设置它们。
`kubectl get` 的简化代码如下:
```go
validArgs []string = { "pod", "node", "service", "replicationcontroller" }

cmd := &cobra.Command{
	Use:     "get [(-o|--output=)json|yaml|template|...] (RESOURCE [NAME] | RESOURCE/NAME ...)",
	Short:   "Display one or many resources",
	Long:    get_long,
	Example: get_example,
	Run: func(cmd *cobra.Command, args []string) {
		err := RunGet(f, out, cmd, args)
		util.CheckErr(err)
	},
	ValidArgs: validArgs,
}
```

注意，我们将 `ValidArgs` 放在 `get` 子命令上。这样做会得到如下结果：
```sh
# kubectl get [tab][tab]
node                 pod                    replicationcontroller  service
```

### Plural form and shortcuts for nouns
如果你的名词有很多别名，你可以在 `ValidArgs` 旁边使用 `ArgAliases` 来定义它们:
```go
argAliases []string = { "pods", "nodes", "services", "svc", "replicationcontrollers", "rc" }

cmd := &cobra.Command{
    ...
	ValidArgs:  validArgs,
	ArgAliases: argAliases
}
```

这些别名在 tab 补全中不会显示给用户，但是如果手工输入，补全算法会将它们作为有效名词接受，例如：
```sh
# kubectl get rc [tab][tab]
backend        frontend       database
``

### Mark flags as required
大多数情况下，补全只显示子命令。但是，如果要使一个子命令工作，有一个 flag 是必须的，你可能希望它在用户键入 `[tab][tab]` 时显示出来。
将 flag 标记为 `Required`。
```go
cmd.MarkFlagRequired("pod")
cmd.MarkFlagRequired("container")
```
这样做会得到如下结果：
```sh
# kubectl exec [tab][tab][tab]
-c            --container=  -p            --pod=
```

## Specify valid filename extensions for flags that take a filename
在本例中，我们使用 `--filename=` 并期望得到一个 json 或 yaml 文件作为参数。
为了使这更容易，我们使用有效的文件名扩展名来注释 `--filename` 标志。

```go
annotations := []string{"json", "yaml", "yml"}
annotation := make(map[string][]string)
annotation[cobra.BashCompFilenameExt] = annotations

flag := &pflag.Flag{
    Name:        "filename",
    Shorthand:   "f",
    Usage:       usage,
    Value:       value,
    DefValue:    value.String(),
    Annotations: annotation,
}
cmd.Flags().AddFlag(flag)
```

现在，当运行带有 `--filename` 或 `-f` 的命令时，将得到像下面结果：
```sh
# kubectl create -f
test/                         example/                      rpmbuild/
hello.yml                     test.json
```

因此，虽然 CWD 中还有许多其他文件，但它只显示子目录和具有有效扩展名的文件。

## Specify custom flag completion
类似于使用 `cobra.BashCompFilenameExt` 来补全和过滤文件名。你可以使用 `cobra.BashCompCustom` 指定一个自定义标志补全函数：
```go
annotation := make(map[string][]string)
annotation[cobra.BashCompCustom] = []string{"__kubectl_get_namespaces"}

flag := &pflag.Flag{
    Name:        "namespace",
    Usage:       usage,
    Annotations: annotation,
}
cmd.Flags().AddFlag(flag)
```

此外，在 `BashCompletionFunction` 的值中添加 `__handle_namespace_flag` 实现，例如:
```go
__kubectl_get_namespaces()
{
    local template
    template="{{ range .items  }}{{ .metadata.name }} {{ end }}"
    local kubectl_out
    if kubectl_out=$(kubectl get -o template --template="${template}" namespace 2>/dev/null); then
        COMPREPLY=( $( compgen -W "${kubectl_out}[*]" -- "$cur" ) )
    fi
}
```

## Using bash aliases for commands

还可以为命令配置 `bash aliases`，并仍会支持补全。
```sh
alias aliasname=origcommand
complete -o default -F __start_origcommand aliasname

# and now when you run `aliasname` completion will make
# suggestions as it did for `origcommand`.

$) aliasname <tab><tab>
completion     firstcommand   secondcommand
```