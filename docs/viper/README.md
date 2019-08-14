# viper 中文文档
viper 中文文档，本文档基于 [viper](https://github.com/spf13/viper) 官方文档。不定期更新。


![viper logo](https://cloud.githubusercontent.com/assets/173412/10886745/998df88a-8151-11e5-9448-4736db51020d.png)

 Go 使用 fangs 配置！

许多 Go 项目使用 Viper 构建，包括：

* [Hugo](http://gohugo.io)
* [EMC RexRay](http://rexray.readthedocs.org/en/stable/)
* [Imgur’s Incus](https://github.com/Imgur/incus)
* [Nanobox](https://github.com/nanobox-io/nanobox)/[Nanopack](https://github.com/nanopack)
* [Docker Notary](https://github.com/docker/Notary)
* [BloomApi](https://www.bloomapi.com/)
* [doctl](https://github.com/digitalocean/doctl)
* [Clairctl](https://github.com/jgsqware/clairctl)

[![Build Status](https://travis-ci.org/spf13/viper.svg)](https://travis-ci.org/spf13/viper) [![Join the chat at https://gitter.im/spf13/viper](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/spf13/viper?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [![GoDoc](https://godoc.org/github.com/spf13/viper?status.svg)](https://godoc.org/github.com/spf13/viper)


## Viper是什么？

Viper 是 Go 应用程序的完整配置解决方案，包括 12-Factor 应用程序。它旨在在应用程序中工作，并可以处理所有类型的配置需求和格式。它支持：

* 默认配置
* 从 JSON, TOML, YAML, HCL 和 Java 属性配置文件读取数据
* 实时查看和重新读取配置文件（可选）
* 从环境变量中读取
* 从远程配置系统(etcd 或 Consul)读取数据并监听变化
* 从命令行参数读取
* 从 buffer 中读取
* 设置显式值

Viper 可以被认为是所有应用程序配置需求的注册表。

## 为什么选择 Viper?

当你构建现代应用程序时，不必担心配置文件格式; 你想专注于构建超赞的软件。Viper 就是为此提供帮助的。

Viper 为你做了以下事情:

1. 以 JSON，TOML，YAML，HCL 或 Java 属性格式查找，加载和解组配置文件。
2. 提供一种机制来为不同的配置选项设置默认值。
3. 提供一种机制可以通过命令行标志指定的选项设置来覆盖值。
4. 提供别名系统，轻松重命名参数，而不会破坏现有代码。
5. 当用户提供命令行或配置文件与默认值相同时，可以轻松区分。

Viper 使用以下优先级顺序。每个项目优先于其下方的项目

 * explicit call to Set
 * flag
 * env
 * config
 * key/value store
 * default

**Viper配置键不区分大小写**。

## 将值放入 Viper

### 建立默认值

一个好的配置系统是支持默认值的。key 不需要默认值，但是当没有通过配置文件，环境变量，远程配置或标志设置 key 时，它是非常有用的。

例:

```go
viper.SetDefault("ContentDir", "content")
viper.SetDefault("LayoutDir", "layouts")
viper.SetDefault("Taxonomies", map[string]string{"tag": "tags", "category": "categories"})
```

### 读取配置文件

Viper 需要极少的配置让它知道去哪里查找配置文件。Viper 支持 JSON, TOML, YAML, HCL 和 Java 属性配置文件。Viper 可以搜索多个路径，但目前单个 Viper 实例仅
支持单个配置文件。Viper 不默认任何配置搜索路径，将默认决策留给应用程序。

以下是如何使用 Viper 搜索和读取配置文件的示例。不需要任何特定的路径，但是至少应该在需要配置文件的地方提供一个路径。

```go
viper.SetConfigName("config") // name of config file (without extension)
viper.AddConfigPath("/etc/appname/")   // path to look for the config file in
viper.AddConfigPath("$HOME/.appname")  // call multiple times to add many search paths
viper.AddConfigPath(".")               // optionally look for config in the working directory
err := viper.ReadInConfig() // Find and read the config file
if err != nil { // Handle errors reading the config file
	panic(fmt.Errorf("Fatal error config file: %s \n", err))
}
```

### 监听和重新读取配置文件

Viper 支持在运行时让应用程序实时读取配置文件。

需要重新启动服务器才能使配置生效的日子已经一去不复返了，viper 支持的应用程序可以在运行时读取配置文件的更新而不会错过任何一个节拍。

只需告诉 viper 实例 `watchConfig` 即可。你可以选择为 Viper 提供当每次发生更改时运行的函数。

**确保在调用 `WatchConfig()` 之前添加所有的配置路径 `ConfigPath`**

```go
viper.WatchConfig()
viper.OnConfigChange(func(e fsnotify.Event) {
	fmt.Println("Config file changed:", e.Name)
})
```

### 从 `io.Reader` 读取配置

Viper 预定义了许多配置源，例如文件，环境变量，标志和远程 K/V 存储，但你不受它们的约束。你还可以实现自己的需要的配置源并将其提供给 viper。

```go
viper.SetConfigType("yaml") // or viper.SetConfigType("YAML")

// any approach to require this configuration into your program.
var yamlExample = []byte(`
Hacker: true
name: steve
hobbies:
- skateboarding
- snowboarding
- go
clothing:
  jacket: leather
  trousers: denim
age: 35
eyes : brown
beard: true
`)

viper.ReadConfig(bytes.NewBuffer(yamlExample))

viper.Get("name") // this would be "steve"
```

### 覆盖设置

这些可以来自命令行标志，也可以来自你自己的应用程序逻辑。

```go
viper.Set("Verbose", true)
viper.Set("LogFile", LogFile)
```

### 注册和使用别名

别名允许多个键引用单个值

```go
viper.RegisterAlias("loud", "Verbose")

viper.Set("verbose", true) // same result as next line
viper.Set("loud", true)   // same result as prior line

viper.GetBool("loud") // true
viper.GetBool("verbose") // true
```

### 使用环境变量

Viper 完全支持环境变量。这使得 12 factor 应用程序可以开箱即用。 五种方法可以帮助使用 ENV:

 * `AutomaticEnv()`
 * `BindEnv(string...) : error`
 * `SetEnvPrefix(string)`
 * `SetEnvKeyReplacer(string...) *strings.Replacer`
 * `AllowEmptyEnvVar(bool)`

_在处理环境变量时，重要的是要认识到 Viper 将环境变量视为区分大小写的变量。_

Viper 提供了一种机制来确保 ENV 变量是唯一的。通过使用 `SetEnvPrefix`，可以告诉 Viper 在读取环境变量时使用前缀。`BindEnv` 和 `AutomaticEnv` 都将使用此前缀。

`BindEnv` 需要一个或两个参数。第一个参数是键名，第二个是环境变量的名称。环境变量的名称区分大小写。如果未提供 ENV 变量名，则 Viper 将自动假设键名与 ENV 变量名称匹配，
但 ENV 变量为 IN ALL CAPS。 当明确提供ENV变量名称时，它**不会自动添加前缀**。

使用 ENV 变量时要认识到的一件重要事情是每次访问时都会读取该值。当调用 `BindEnv` 时，Viper不会修复该值。

`AutomaticEnv` 是一个强大的帮手，特别是与 `SetEnvPrefix` 结合使用时。每当发出 `viper.Get` 请求时，Viper 都会检查环境变量。它将适用以下规则。
它将检查一个环境变量，其名称与大写的键匹配，如果设置了 `EnvPrefix`，则以它为前缀。

`SetEnvKeyReplacer` 允许你使用 `strings.Replacer` 对象来重写 Env 键。如果你想在 `Get()` 调用中使用 `-` 或者某些东西，但希望你的环境变量使用 `_` 分隔符，
这是很有用的。使用它的一个例子可以在 `viper_test.go` 中找到。

默认情况下，空环境变量被视为未设置，并将回退到下一个配置源。要设置空环境变量，请使用 `AllowEmptyEnv` 方法。

#### 环境变量例子

```go
SetEnvPrefix("spf") // will be uppercased automatically
BindEnv("id")

os.Setenv("SPF_ID", "13") // typically done outside of the app

id := Get("id") // 13
```

### 使用标志

Viper 能够绑定到标志。具体来说，Viper 支持 [Cobra](https://github.com/spf13/cobra) 库中使用的 “Pflags”。

与 `BindEnv` 类似，在调用绑定方法时，不会设置该值。但在访问它时，会设置。这意味着你可以尽早绑定，即使在 `init()` 函数中也是如此。

对于单个标志，`BindPFlag()` 方法提供此功能。

例:

```go
serverCmd.Flags().Int("port", 1138, "Port to run Application server on")
viper.BindPFlag("port", serverCmd.Flags().Lookup("port"))
```

You can also bind an existing set of pflags (pflag.FlagSet):

例:

```go
pflag.Int("flagname", 1234, "help message for flagname")

pflag.Parse()
viper.BindPFlags(pflag.CommandLine)

i := viper.GetInt("flagname") // retrieve values from viper instead of pflag
```

在 Viper 中使用 [pflag](https://github.com/spf13/pflag/) 并不排除使用标准库中使用 [flag](https://golang.org/pkg/flag/) 包的其他包。
pflag 包可以通过导入这些标志来处理为 flag 包定义的标志。这是通过调用由的 pflag 包提供的名为 `AddGoFlagSet` 便利函数来实现的。

Example:

```go
package main

import (
	"flag"
	"github.com/spf13/pflag"
)

func main() {

	// using standard library "flag" package
	flag.Int("flagname", 1234, "help message for flagname")

	pflag.CommandLine.AddGoFlagSet(flag.CommandLine)
	pflag.Parse()
	viper.BindPFlags(pflag.CommandLine)

	i := viper.GetInt("flagname") // retrieve value from viper

	...
}
```

#### Flag 接口

如果你不使用 `Pflags`，Viper 提供两个 Go 接口来绑定其他标志系统。

`FlagValue` 代表一个标志。 这是一个关于如何实现此接口的非常简单的示例:

```go
type myFlag struct {}
func (f myFlag) HasChanged() bool { return false }
func (f myFlag) Name() string { return "my-flag-name" }
func (f myFlag) ValueString() string { return "my-flag-value" }
func (f myFlag) ValueType() string { return "string" }
```

一旦你的标志实现了这个接口，你就可以告诉 Viper 绑定它:

```go
viper.BindFlagValue("my-flag-name", myFlag{})
```

`FlagValueSet` represents a group of flags. This is a very simple example on how to implement this interface:

```go
type myFlagSet struct {
	flags []myFlag
}

func (f myFlagSet) VisitAll(fn func(FlagValue)) {
	for _, flag := range flags {
		fn(flag)
	}
}
```

Once your flag set implements this interface, you can simply tell Viper to bind it:

```go
fSet := myFlagSet{
	flags: []myFlag{myFlag{}, myFlag{}},
}
viper.BindFlagValues("my-flags", fSet)
```

### 远程 Key/Value 存储支持

要在 Viper 中启用远程支持，空白导入 `viper/remote` 软件包:

`import _ "github.com/spf13/viper/remote"`

Viper 将读取从  Key/Value 存储（如etcd或Consul）中的路径检索的配置字符串（如 JSON，TOML，YAML 或 HCL）。这些值优先于默认值，
但会被从磁盘，标志或环境变量检索的配置值覆盖。

Viper 使用 [crypt](https://github.com/xordataexchange/crypt) 从 K/V 存储中检索配置，这意味着你可以存储加密的配置值，
并在拥有正确的 gpg 密钥环时自动解密它们。加密是可选的。

你可以将远程配置与本地配置结合使用，也可以独立使用。

你可以使用 `crypt` 命令行助手将你的配置放到到 K/V 存储。`crypt` 默认存储为 etcd，url 是 `http://127.0.0.1:4001`。

```bash
$ go get github.com/xordataexchange/crypt/bin/crypt
$ crypt set -plaintext /config/hugo.json /Users/hugo/settings/config.json
```

确认你的值已设置：

```bash
$ crypt get -plaintext /config/hugo.json
```

有关如何设置加密值或如何使用 Consul 的示例，请参阅 `crypt` 文档。

### Remote Key/Value Store Example - Unencrypted

#### etcd
```go
viper.AddRemoteProvider("etcd", "http://127.0.0.1:4001","/config/hugo.json")
viper.SetConfigType("json") // because there is no file extension in a stream of bytes, supported extensions are "json", "toml", "yaml", "yml", "properties", "props", "prop"
err := viper.ReadRemoteConfig()
```

#### Consul
你需要使用包含所需配置的 JSON 值为 Consul K/V 存储 设置密钥。例如，使用下面的值创建一个 Consul K/V 存储 key 为 `MY_CONSUL_KEY`：

```json
{
    "port": 8080,
    "hostname": "myhostname.com"
}
```

```go
viper.AddRemoteProvider("consul", "localhost:8500", "MY_CONSUL_KEY")
viper.SetConfigType("json") // Need to explicitly set this to json
err := viper.ReadRemoteConfig()

fmt.Println(viper.Get("port")) // 8080
fmt.Println(viper.Get("hostname")) // myhostname.com
```

### Remote Key/Value Store Example - Encrypted

```go
viper.AddSecureRemoteProvider("etcd","http://127.0.0.1:4001","/config/hugo.json","/etc/secrets/mykeyring.gpg")
viper.SetConfigType("json") // because there is no file extension in a stream of bytes,  supported extensions are "json", "toml", "yaml", "yml", "properties", "props", "prop"
err := viper.ReadRemoteConfig()
```

### Watching Changes in etcd - Unencrypted

```go
// alternatively, you can create a new viper instance.
var runtime_viper = viper.New()

runtime_viper.AddRemoteProvider("etcd", "http://127.0.0.1:4001", "/config/hugo.yml")
runtime_viper.SetConfigType("yaml") // because there is no file extension in a stream of bytes, supported extensions are "json", "toml", "yaml", "yml", "properties", "props", "prop"

// read from remote config the first time.
err := runtime_viper.ReadRemoteConfig()

// unmarshal config
runtime_viper.Unmarshal(&runtime_conf)

// open a goroutine to watch remote changes forever
go func(){
	for {
	    time.Sleep(time.Second * 5) // delay after each request

	    // currently, only tested with etcd support
	    err := runtime_viper.WatchRemoteConfig()
	    if err != nil {
	        log.Errorf("unable to read remote config: %v", err)
	        continue
	    }

	    // unmarshal new config into our runtime config struct. you can also use channel
	    // to implement a signal to notify the system of the changes
	    runtime_viper.Unmarshal(&runtime_conf)
	}
}()
```

## Getting Values From Viper

在 Viper 中，有几种方法可以根据值的类型获取值。存在以下函数和方法：

 * `Get(key string) : interface{}`
 * `GetBool(key string) : bool`
 * `GetFloat64(key string) : float64`
 * `GetInt(key string) : int`
 * `GetString(key string) : string`
 * `GetStringMap(key string) : map[string]interface{}`
 * `GetStringMapString(key string) : map[string]string`
 * `GetStringSlice(key string) : []string`
 * `GetTime(key string) : time.Time`
 * `GetDuration(key string) : time.Duration`
 * `IsSet(key string) : bool`
 * `AllSettings() : map[string]interface{}`

要认识到的一件重要事情是，**每个 Get 函数如果找不到相应的配置，都将返回零值**。要检查给定的 key 是否存在，使用 `IsSet()` 方法。

例:
```go
viper.GetString("logfile") // case-insensitive Setting & Getting
if viper.GetBool("verbose") {
    fmt.Println("verbose enabled")
}
```
### Accessing nested keys

可以访问深层嵌套的 key。例如，如果加载了以下 JSON 文件：

```json
{
    "host": {
        "address": "localhost",
        "port": 5799
    },
    "datastore": {
        "metric": {
            "host": "127.0.0.1",
            "port": 3099
        },
        "warehouse": {
            "host": "198.0.0.1",
            "port": 2112
        }
    }
}

```

Viper 可以通过传递一个 `.` 分隔的键路径来访问嵌套字段：

```go
GetString("datastore.metric.host") // (returns "127.0.0.1")
```

这会遵守上面建立的优先规则;对路径的搜索将通过其余配置注册表级联，直到找到为止。

例如，给定的配置文件中，`datastore.metric.host` 和 `datastore.metric.port`都 已经定义（并且可以被覆盖）。
如果在默认情况下定义了 `datastore.metric.protocol`，Viper 也会找到它。

但是，如果 `datastore.metric` 被重写（通过标志，环境变量，`Set()` 方法，...），那么 `datastore.metric` 的所有子键都变为未定义，它们是 被更高优先级的配置“遮蔽”。

最后，如果存在与分隔的键路径匹配的键，则将返回其值。例。

```json
{
    "datastore.metric.host": "0.0.0.0",
    "host": {
        "address": "localhost",
        "port": 5799
    },
    "datastore": {
        "metric": {
            "host": "127.0.0.1",
            "port": 3099
        },
        "warehouse": {
            "host": "198.0.0.1",
            "port": 2112
        }
    }
}

GetString("datastore.metric.host") // returns "0.0.0.0"
```

### Extract sub-tree

从 Viper 中提取 `sub-tree`。

例如，`viper` 表示:

```json
app:
  cache1:
    max-items: 100
    item-size: 64
  cache2:
    max-items: 200
    item-size: 80
```

执行后:

```go
subv := viper.Sub("app.cache1")
```

`subv`表示:

```json
max-items: 100
item-size: 64
```

假设我们有:

```go
func NewCache(cfg *Viper) *Cache {...}
```

它根据格式为 `subv` 的配置信息创建缓存。
现在很容易分别创建这两个缓存:

```go
cfg1 := viper.Sub("app.cache1")
cache1 := NewCache(cfg1)

cfg2 := viper.Sub("app.cache2")
cache2 := NewCache(cfg2)
```

### Unmarshaling

你还可以选择 Unmarshaling 所有的值或特定得值到 struct，map 等。

有两种方法可以做到这一点:

 * `Unmarshal(rawVal interface{}) : error`
 * `UnmarshalKey(key string, rawVal interface{}) : error`

例:

```go
type config struct {
	Port int
	Name string
	PathMap string `mapstructure:"path_map"`
}

var C config

err := Unmarshal(&C)
if err != nil {
	t.Fatalf("unable to decode into struct, %v", err)
}
```

### Marshalling to string

你可能需要将 viper 中保存的所有设置编组为字符串，而不是将其写入文件。使用 `AllSettings()` 返回的配置，使用你喜欢的 marshaller 格式。

```go
import (
    yaml "gopkg.in/yaml.v2"
    // ...
)

func yamlStringSettings() string {
    c := viper.AllSettings()
	bs, err := yaml.Marshal(c)
	if err != nil {
        t.Fatalf("unable to marshal config to YAML: %v", err)
    }
	return string(bs)
}
```

## Viper 还是 Vipers?

Viper 是开箱你用的。使用 Viper 不需要配置或初始化。由于大多数应用程序都希望使用单个中央存储库进行配置，因此 viper 软件包提供了此功能。它类似于单例。

在上面的所有示例中，他们演示了使用 viper 的单例式方法。

### Working with multiple vipers

你还可以创建多个不同的 viper 实例，以便在你的应用程序中使用。每个都有自己独特的配置和值集。每个都可以从不同的配置文件、键值存储区等读取。
viper 包支持的所有函数都在 viper 实例上有相应的镜像方法。

例:

```go
x := viper.New()
y := viper.New()

x.SetDefault("ContentDir", "content")
y.SetDefault("ContentDir", "foobar")

//...
```

当使用多个 viper 实例时，由用户来跟踪不同的 viper 实例。

## Q & A

Q: 为什么不使用 INI 文件?

A: Ini 文件非常糟糕。 没有标准格式，很难验证。Viper 旨在使用 JSON，TOML 或 YAML 文件。如果有人真的想要添加此功能，我很乐意将其合并。
你可以轻松指定应用程序允许的格式。

Q: 为什么称它为 “Viper”?

A: Viper 被设计成 [Cobra](https://github.com/spf13/cobra) 的 [companion](http://en.wikipedia.org/wiki/Viper_(G.I._Joe))。虽然两者可以完全独立地运行，
   但它们共同构成了一个强大的组合，以满足大部分应用程序的基础需求。

Q: 为什么称它为 “Cobra”?

A: 还有比 [commander](http://en.wikipedia.org/wiki/Cobra_Commander) 更好的名字么？


