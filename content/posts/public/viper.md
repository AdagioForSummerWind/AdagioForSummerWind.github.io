---
title: "Viper"
date: 2022-11-19T19:02:19+08:00
lastmod: 2022-11-19
tags: [tools]
categories: [tools]
slug: viper
draft: false
---


### 配置解析神器：Viper 使用介绍
几乎所有的后端服务，都需要一些配置项来配置我们的服务，一些小型的项目，配置不是很多，可以选择只通过命令行参数来传递配置。但是大型项目配置很多，通过命令行参数传递就变得很麻烦，不好维护。标准的解决方案是将这些配置信息保存在配置文件中，由程序启动时加载和解析。Go 生态中有很多包可以加载并解析配置文件，目前最受欢迎的是 Viper 包。

Viper 是 Go 应用程序现代化的、完整的解决方案，能够处理不同格式的配置文件，让我们在构建现代应用程序时，不必担心配置文件格式。Viper 也能够满足我们对应用配置的各种需求。


Viper 可以从不同的位置读取配置，不同位置的配置具有不同的优先级，高优先级的配置会覆盖低优先级相同的配置，按优先级从高到低排列如下：

1. 通过 viper.Set 函数显示设置的配置
2. 命令行参数
3. 环境变量
4. 配置文件
5. Key/Value 存储
6. 默认值

这里需要**注意**，Viper 配置键不区分大小写。Viper 有很多功能，最重要的两类功能是读入配置和读取配置，Viper 提供不同的方式来实现这两类功能。接下来，我们就来详细介绍下 Viper 如何读入配置和读取配置。


#### 读入配置
读入配置，就是将配置读入到 Viper 中，有如下读入方式：

- 设置默认的配置文件名。
- 读取配置文件。
- 监听和重新读取配置文件。
- 从 io.Reader 读取配置。
- 从环境变量读取。
- 从命令行标志读取。
- 从远程 Key/Value 存储读取。


这几个方法的具体读入方式，你可以看下面的展示。



设置默认值。一个好的配置系统应该支持默认值。Viper 支持对 key 设置默认值，当没有通过配置文件、环境变量、远程配置或命令行标志设置 key 时，设置默认值通常是很有用的，可以让程序在没有明确指定配置时也能够正常运行。例如：


```go
viper.SetDefault("ContentDir", "content")
viper.SetDefault("LayoutDir", "layouts")
viper.SetDefault("Taxonomies", map[string]string{"tag": "tags", "category": "categories"})
```
读取配置文件。Viper 可以读取配置文件来解析配置，支持 JSON、TOML、YAML、YML、Properties、Props、Prop、HCL、Dotenv、Env 格式的配置文件。Viper 支持搜索多个路径，并且默认不配置任何搜索路径，将默认决策留给应用程序。


以下是如何使用 Viper 搜索和读取配置文件的示例：


```go
package main

import (
  "fmt"

  "github.com/spf13/pflag"
  "github.com/spf13/viper"
)

var (
  cfg  = pflag.StringP("config", "c", "", "Configuration file.")
  help = pflag.BoolP("help", "h", false, "Show this help message.")
)

func main() {
  pflag.Parse()
  if *help {
    pflag.Usage()
    return
  }

  // 从配置文件中读取配置
  if *cfg != "" {
    viper.SetConfigFile(*cfg)   // 指定配置文件名
    viper.SetConfigType("yaml") // 如果配置文件名中没有文件扩展名，则需要指定配置文件的格式，告诉viper以何种格式解析文件
  } else {
    viper.AddConfigPath(".")          // 把当前目录加入到配置文件的搜索路径中
    viper.AddConfigPath("$DYTT_ROOT/config") // 配置文件搜索路径，可以设置多个配置文件搜索路径
    viper.SetConfigName("config")     // 配置文件名称（没有文件扩展名）
  }

  if err := viper.ReadInConfig(); err != nil { // 读取配置文件。如果指定了配置文件名，则使用指定的配置文件，否则在注册的搜索路径中搜索
    panic(fmt.Errorf("Fatal error config file: %s \n", err))
  }

  fmt.Printf("Used configuration file is: %s\n", viper.ConfigFileUsed())
}
```
Viper 支持设置多个配置文件搜索路径，需要**注意**添加搜索路径的顺序，Viper 会根据添加的路径顺序搜索配置文件，如果找到则停止搜索。如果调用 SetConfigFile 直接指定了配置文件名，并且配置文件名没有文件扩展名时，需要显式指定配置文件的格式，以使 Viper 能够正确解析配置文件。


如果通过搜索的方式查找配置文件，则需要**注意**，SetConfigName 设置的配置文件名是不带扩展名的，在搜索时 Viper 会在文件名之后追加文件扩展名，并尝试搜索所有支持的扩展类型。


监听和重新读取配置文件。Viper 支持在运行时让应用程序实时读取配置文件，也就是热加载配置。可以通过 WatchConfig 函数热加载配置。在调用 WatchConfig 函数之前，需要确保已经添加了配置文件的搜索路径。另外，还可以为 Viper 提供一个回调函数，以便在每次发生更改时运行。这里是个示例：


```go
viper.WatchConfig()
viper.OnConfigChange(func(e fsnotify.Event) {
   // 配置文件发生变更之后会调用的回调函数
  fmt.Println("Config file changed:", e.Name)
})
```
在实际开发中并不建议使用热加载功能，因为即使配置热加载了，程序中的代码也不一定会热加载。例如：修改了服务监听端口，但是服务没有重启，这时候服务还是监听在老的端口上，会造成不一致。


设置配置值。我们可以通过 viper.Set() 函数来显式设置配置：


```
viper.Set("user.username", "colin")
```
使用环境变量。Viper 还支持环境变量，通过如下 5 个函数来支持环境变量：


- AutomaticEnv()
- BindEnv(input …string) error
- SetEnvPrefix(in string)
- SetEnvKeyReplacer(r *strings.Replacer)
- AllowEmptyEnv(allowEmptyEnv bool)


这里要**注意**：Viper 读取环境变量是区分大小写的。Viper 提供了一种机制来确保 Env 变量是唯一的。通过使用 SetEnvPrefix，可以告诉 Viper 在读取环境变量时使用前缀。BindEnv 和 AutomaticEnv 都将使用此前缀。比如，我们设置了 viper.SetEnvPrefix(“VIPER”)，当使用 viper.Get(“apiversion”) 时，实际读取的环境变量是VIPER_APIVERSION。


BindEnv 需要一个或两个参数。第一个参数是键名，第二个是环境变量的名称，环境变量的名称区分大小写。如果未提供 Env 变量名，则 Viper 将假定 Env 变量名为：环境变量前缀_键名全大写。例如：前缀为 VIPER，key 为 username，则 Env 变量名为VIPER_USERNAME。当显示提供 Env 变量名（第二个参数）时，它不会自动添加前缀。例如，如果第二个参数是 ID，Viper 将查找环境变量 ID。


在使用 Env 变量时，需要**注意**的一件重要事情是：每次访问该值时都将读取它。Viper 在调用 BindEnv 时不固定该值。


还有一个魔法函数 SetEnvKeyReplacer，SetEnvKeyReplacer 允许你使用 strings.Replacer 对象来重写 Env 键。如果你想在 Get() 调用中使用-或者.，但希望你的环境变量使用_分隔符，可以通过 SetEnvKeyReplacer 来实现。比如，我们设置了环境变量USER_SECRET_KEY=bVix2WBv0VPfrDrvlLWrhEdzjLpPCNYb，但我们想用viper.Get("user.secret-key")，那我们就调用函数：


```go
viper.SetEnvKeyReplacer(strings.NewReplacer(".", "_", "-", "_"))
```
上面的代码，在调用 viper.Get() 函数时，会用 _ 替换.和-。默认情况下，空环境变量被认为是未设置的，并将返回到下一个配置源。若要将空环境变量视为已设置，可以使用 AllowEmptyEnv 方法。使用环境变量示例如下：



```go
// 使用环境变量
os.Setenv("VIPER_USER_SECRET_ID", "QLdywI2MrmDVjSSv6e95weNRvmteRjfKAuNV")
os.Setenv("VIPER_USER_SECRET_KEY", "bVix2WBv0VPfrDrvlLWrhEdzjLpPCNYb")

viper.AutomaticEnv()                                             // 读取环境变量
viper.SetEnvPrefix("VIPER")                                      // 设置环境变量前缀：VIPER_，如果是viper，将自动转变为大写。
viper.SetEnvKeyReplacer(strings.NewReplacer(".", "_", "-", "_")) // 将viper.Get(key) key字符串中'.'和'-'替换为'_'
viper.BindEnv("user.secret-key")
viper.BindEnv("user.secret-id", "USER_SECRET_ID") // 绑定环境变量名到key
```
使用标志。Viper 支持 Pflag 包，能够绑定 key 到 Flag。与 BindEnv 类似，在调用绑定方法时，不会设置该值，但在访问它时会设置。对于单个标志，可以调用 BindPFlag() 进行绑定：


```go
viper.BindPFlag("token", pflag.Lookup("token")) // 绑定单个标志
```
还可以绑定一组现有的 pflags（pflag.FlagSet）：


```go
viper.BindPFlags(pflag.CommandLine)             //绑定标志集
```
#### 读取配置
Viper 提供了如下方法来读取配置：
- Get(key string) interface{}
- Get(key string) 
- AllSettings() map[string]interface{}
- IsSet(key string) : bool


每一个 Get 方法在找不到值的时候都会返回零值。为了检查给定的键是否存在，可以使用 IsSet() 方法。可以是 Viper 支持的类型，首字母大写：Bool、Float64、Int、IntSlice、String、StringMap、StringMapString、StringSlice、Time、Duration。例如：GetInt()。


常见的读取配置方法有以下几种。


访问嵌套的键。例如，加载下面的 JSON 文件：


```
json
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
Viper 可以通过传入.分隔的路径来访问嵌套字段：


```go
viper.GetString("datastore.metric.host") // (返回 "127.0.0.1")
```
如果datastore.metric被直接赋值覆盖（被 Flag、环境变量、set() 方法等等），那么datastore.metric的所有子键都将变为未定义状态，它们被高优先级配置级别覆盖了。如果存在与分隔的键路径匹配的键，则直接返回其值。例如：


```
json
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
```
通过 viper.GetString 获取值：


```go
viper.GetString("datastore.metric.host") // 返回 "0.0.0.0"
```
反序列化。Viper 可以支持将所有或特定的值解析到结构体、map 等。可以通过两个函数来实现：

- Unmarshal(rawVal interface{}) error
- UnmarshalKey(key string, rawVal interface{}) error

一个示例：


```go
type config struct {
  Port int
  Name string
  PathMap string `mapstructure:"path_map"`
}

var C config

err := viper.Unmarshal(&C)
if err != nil {
  t.Fatalf("unable to decode into struct, %v", err)
}
```
如果想要解析那些键本身就包含.(默认的键分隔符）的配置，则需要修改分隔符：


```go
v := viper.NewWithOptions(viper.KeyDelimiter("::"))

v.SetDefault("chart::values", map[string]interface{}{
    "ingress": map[string]interface{}{
        "annotations": map[string]interface{}{
            "traefik.frontend.rule.type":                 "PathPrefix",
            "traefik.ingress.kubernetes.io/ssl-redirect": "true",
        },
    },
})

type config struct {
  Chart struct{
        Values map[string]interface{}
    }
}

var C config

v.Unmarshal(&C)
```
Viper 在后台使用github.com/mitchellh/mapstructure来解析值，其默认情况下使用mapstructure tags。当我们需要将 Viper 读取的配置反序列到我们定义的结构体变量中时，一定要使用 mapstructure tags。


序列化成字符串。有时候我们需要将 Viper 中保存的所有设置序列化到一个字符串中，而不是将它们写入到一个文件中，示例如下：


```go
import (
    yaml "gopkg.in/yaml.v2"
    // ...
)

func yamlStringSettings() string {
    c := viper.AllSettings()
    bs, err := yaml.Marshal(c)
    if err != nil {
        log.Fatalf("unable to marshal config to YAML: %v", err)
    }
    return string(bs)
}
```