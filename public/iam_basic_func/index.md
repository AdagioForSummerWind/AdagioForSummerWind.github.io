# iam_basic_func

## 12 | API 风格（上）：如何设计RESTful API？

绝大部分的 Go 后端服务需要编写 API 接口，对外提供服务。所以在开发之前，我们需要确定一种 API 风格。API 风格也可以理解为 API 类型，目前业界常用的 API 风格有三种：REST、RPC 和 GraphQL。我们需要根据项目需求，并结合 API 风格的特点，确定使用哪种 API 风格，这对以后的编码实现、通信方式和通信效率都有很大的影响。

在 Go 项目开发中，用得最多的是 REST 和 RPC，我们在 IAM 实战项目中也使用了 REST 和 RPC 来构建示例项目。接下来的两讲，我会详细介绍下 REST 和 RPC 这两种风格，如果你对 GraphQL 感兴趣，GraphQL 中文官网有很多文档和代码示例，你可以自行学习。


### RESTful API 介绍


在回答“RESTful API 是什么”之前，我们先来看下 REST 是什么意思：REST 代表的是表现层状态转移（REpresentational State Transfer），由 Roy Fielding 在他的论文《Architectural Styles and the Design of Network-based Software Architectures》里提出。REST 本身并没有创造新的技术、组件或服务，它只是一种软件架构风格，是一组架构约束条件和原则，而不是技术框架。

REST 有一系列规范，满足这些规范的 API 均可称为 RESTful API。**REST 规范把所有内容都视为资源，也就是说网络上一切皆资源**。REST 架构对资源的操作包括获取、创建、修改和删除，这些操作正好对应 HTTP 协议提供的 GET、POST、PUT 和 DELETE 方法。HTTP 动词与 REST 风格 CRUD 的对应关系见下表：![img](https://static001.geekbang.org/resource/image/40/92/409164157ce4cde3131f0236d660e092.png?wh=1754x582)

REST 风格虽然适用于很多传输协议，但在实际开发中，由于 REST 天生和 HTTP 协议相辅相成，因此 HTTP 协议已经成了实现 RESTful API 事实上的标准。所以，REST 具有以下核心特点：
- 以资源 (resource) 为中心，所有的东西都抽象成资源，所有的行为都应该是在资源上的 CRUD 操作。
    - 资源对应着面向对象范式里的对象，面向对象范式以对象为中心。
    - 资源使用 URI 标识，每个资源实例都有一个唯一的 URI 标识。例如，如果我们有一个用户，用户名是 admin，那么它的 URI 标识就可以是 /users/admin。
- 资源是有状态的，使用 JSON/XML 等在 HTTP Body 里表征资源的状态。
- 客户端通过四个 HTTP 动词，对服务器端资源进行操作，实现“表现层状态转化”。
- 无状态，这里的无状态是指每个 RESTful API 请求都包含了所有足够完成本次操作的信息，服务器端无须保持 session。无状态对于服务端的弹性扩容是很重要的。

因为怕你弄混概念，这里强调下 REST 和 RESTful API 的区别：REST 是一种规范，而 RESTful API 则是满足这种规范的 API 接口。

### RESTful API 设计原则
上面我们说了，RESTful API 就是满足 REST 规范的 API，由此看来，RESTful API 的核心是规范，那么具体有哪些规范呢？

接下来，我就从 URI 设计、API 版本管理等七个方面，给你详细介绍下 RESTful API 的设计原则，然后再通过一个示例来帮助你快速启动一个 RESTful API 服务。希望你学完这一讲之后，对如何设计 RESTful API 有一个清楚的认知。

#### URI 设计
资源都是使用 URI 标识的，我们应该按照一定的规范来设计 URI，通过规范化可以使我们的 API 接口更加易读、易用。以下是 URI 设计时，应该遵循的一些规范：
- **资源名使用名词而不是动词，并且用名词复数表示。资源分为 Collection 和 Member 两种。**
    - Collection：一堆资源的集合。例如我们系统里有很多用户（User）, 这些用户的集合就是 Collection。Collection 的 URI 标识应该是 域名/资源名复数, 例如https:// iam.api.marmotedu.com/users。
    - Member：单个特定资源。例如系统中特定名字的用户，就是 Collection 里的一个 Member。Member 的 URI 标识应该是 域名/资源名复数/资源名称, 例如https:// iam.api.marmotedu/users/admin。
- URI 结尾不应包含/。
- URI 中不能出现下划线 _，必须用中杠线 -代替（有些人**推荐**用 _，有些人**推荐**用 -，统一使用一种格式即可，我比较**推荐**用 -）。
- URI 路径用小写，不要用大写。
- **避免层级过深的 URI。超过 2 层的资源嵌套会很乱，建议将其他资源转化为?参数，比如：**

```
/schools/tsinghua/classes/rooma/students/zhang # 不**推荐**
/students?school=qinghua&class=rooma # **推荐**
```

这里有个地方需要**注意**：在实际的 API 开发中，可能你会发现有些操作不能很好地映射为一个 REST 资源，这时候，你可以参考下面的做法。
- 将一个操作变成资源的一个属性，比如想在系统中暂时禁用某个用户，可以这么设计 URI：/users/zhangsan?active=false。
- 将操作当作是一个资源的嵌套资源，比如一个 GitHub 的加星操作：
```
PUT /gists/:id/star # github star action
DELETE /gists/:id/star # github unstar action
```
- 如果以上都不能解决问题，有时可以打破这类规范。比如登录操作，登录不属于任何一个资源，URI 可以设计为：/login。

在设计 URI 时，如果你遇到一些不确定的地方，**推荐**你参考 GitHub 标准 RESTful API。

#### REST 资源操作映射为 HTTP 方法
基本上 RESTful API 都是使用 HTTP 协议原生的 GET、PUT、POST、DELETE 来标识对资源的 CRUD 操作的，形成的规范如下表所示：![img](https://static001.geekbang.org/resource/image/d9/2d/d970bcd53d2827b7f2096e639d5fa82d.png?wh=1524x698)

对资源的操作应该满足安全性和幂等性：
- 安全性：不会改变资源状态，可以理解为只读的。
- 幂等性：执行 1 次和执行 N 次，对资源状态改变的效果是等价的。

使用不同 HTTP 方法时，资源操作的安全性和幂等性对照见下表：![img](https://static001.geekbang.org/resource/image/b7/e1/b746421291654e4d2e51509b885c4ee1.png?wh=1434x448)

在使用 HTTP 方法的时候，有以下两点需要你**注意**：
- GET 返回的结果，要尽量可用于 PUT、POST 操作中。例如，用 GET 方法获得了一个 user 的信息，调用者修改 user 的邮件，然后将此结果再用 PUT 方法更新。这要求 GET、PUT、POST 操作的资源属性是一致的。
- 如果对资源进行状态 / 属性变更，要用 PUT 方法，POST 方法仅用来创建或者批量删除这两种场景。

**在设计 API 时，经常会有批量删除的需求，需要在请求中携带多个需要删除的资源名，但是 HTTP 的 DELETE 方法不能携带多个资源名，这时候可以通过下面三种方式来解决：**

- 发起多个 DELETE 请求。
- 操作路径中带多个 id，id 之间用分隔符分隔, 例如：DELETE /users?ids=1,2,3 。
- 直接使用 POST 方式来批量删除，body 中传入需要删除的资源列表。

其中，**第二种是我最推荐的方式**，因为使用了匹配的 DELETE 动词，并且不需要发送多次 DELETE 请求。

你需要**注意**的是，这三种方式都有各自的使用场景，你可以根据需要自行选择。如果选择了某一种方式，那么整个项目都需要统一用这种方式。
#### 统一的返回格式
一般来说，一个系统的 RESTful API 会向外界开放多个资源的接口，每个接口的返回格式要保持一致。另外，每个接口都会返回成功和失败两种消息，这两种消息的格式也要保持一致。不然，客户端代码要适配不同接口的返回格式，每个返回格式又要适配成功和失败两种消息格式，会大大增加用户的学习和使用成本。

返回的格式没有强制的标准，你可以根据实际的业务需要返回不同的格式。本专栏 第 19 讲 中会**推荐**一种返回格式，它也是业界最常用和**推荐**的返回格式。

#### API 版本管理
随着时间的推移、需求的变更，一个 API 往往满足不了现有的需求，这时候就需要对 API 进行修改。对 API 进行修改时，不能影响其他调用系统的正常使用，这就要求 API 变更做到向下兼容，也就是新老版本共存。

**但在实际场景中，很可能会出现同一个 API 无法向下兼容的情况。这时候最好的解决办法是从一开始就引入 API 版本机制，当不能向下兼容时，就引入一个新的版本，老的版本则保留原样。**这样既能保证服务的可用性和安全性，同时也能满足新需求。

API 版本有不同的标识方法，在 RESTful API 开发中，通常将版本标识放在如下 3 个位置：
- URL 中，比如/v1/users。
- HTTP Header 中，比如Accept: vnd.example-com.foo+json; version=1.0。
- Form 参数中，比如/users?version=v1。

**我们这门课中的版本标识是放在 URL 中的，比如/v1/users，这样做的好处是很直观，GitHub、Kubernetes、Etcd 等很多优秀的 API 均采用这种方式。**

这里要**注意**，有些开发人员不**建议**将版本放在 URL 中，因为他们觉得不同的版本可以理解成同一种资源的不同表现形式，所以应该采用同一个 URI。对于这一点，没有严格的标准，根据项目实际需要选择一种方式即可。

#### API 命名
API 通常的命名方式有三种，分别是驼峰命名法 (serverAddress)、蛇形命名法 (server_address) 和脊柱命名法 (server-address)。

驼峰命名法和蛇形命名法都需要切换输入法，会增加操作的复杂性，也容易出错，所以这里**建议**用脊柱命名法。GitHub API 用的就是脊柱命名法，例如 selected-actions。

#### 统一分页 / 过滤 / 排序 / 搜索功能

**REST 资源的查询接口，通常情况下都需要实现分页、过滤、排序、搜索功能**，因为这些功能是每个 REST 资源都能用到的，所以可以实现为一个公共的 API 组件。下面来介绍下这些功能。

- 分页：在列出一个 Collection 下所有的 Member 时，应该提供分页功能，**例如/users?offset=0&limit=20（limit，指定返回记录的数量；offset，指定返回记录的开始位置**）。引入分页功能可以减少 API 响应的延时，同时可以避免返回太多条目，导致服务器 / 客户端响应特别慢，甚至导致服务器 / 客户端 crash 的情况。
- 过滤：如果用户不需要一个资源的全部状态属性，可以在 URI 参数里指定返回哪些属性，例如/users?fields=email,username,address。
- 排序：用户很多时候会根据创建时间或者其他因素，列出一个 Collection 中前 100 个 Member，这时可以在 URI 参数中指明排序参数，例如/users?sort=age,desc。
- 搜索：当一个资源的 Member 太多时，用户可能想通过搜索，快速找到所需要的 Member，或着想搜下有没有名字为 xxx 的某类资源，这时候就需要提供搜索功能。搜索**建议**按**模糊匹配**来搜索。



#### 域名
API 的域名设置主要有两种方式：
- https://marmotedu.com/api，这种方式适合 API 将来不会有进一步扩展的情况，比如刚开始 marmotedu.com 域名下只有一套 API 系统，未来也只有这一套 API 系统。
- https://iam.api.marmotedu.com，如果 marmotedu.com 域名下未来会新增另一个系统 API，这时候最好的方式是每个系统的 API 拥有专有的 API 域名，**比如：storage.api.marmotedu.com，network.api.marmotedu.com。腾讯云的域名就是采用这种方式**。

到这里，我们就将 REST 设计原则中的核心原则讲完了，这里有个需要**注意**的点：不同公司、不同团队、不同项目可能采取不同的 REST 设计原则，以上所列的基本上都是大家公认的原则。

REST 设计原则中，还有一些原则因为内容比较多，并且可以独立成模块，所以放在后面来讲。比如 RESTful API 安全性、状态返回码和认证等。
#### REST 示例
上面介绍了一些概念和原则，这里我们通过一个“Hello World”程序，来教你用 Go 快速启动一个 RESTful API 服务，示例代码存放在gopractise-demo/apistyle/ping/main.go。
```go
package main
import ( "log" "net/http")
func main() { 
    http.HandleFunc("/ping", pong) 
    log.Println("Starting http server ...") 
    log.Fatal(http.ListenAndServe(":50052", nil))
}
func pong(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("pong"))
}

package main
import (
	"log"
    "net/http"
)
func main(){
    http.HandleFunc("/ping",pong)
    log.Println("Starting http server ...")
    log.Fatal(http.ListenAndServe(":50052",nil))
}
func pong(w http.ResponseWriter,r *http.Request){
    w.write([]byte("pong"))
}



```
在上面的代码中，我们通过 http.HandleFunc，向 HTTP 服务注册了一个 pong handler，在 pong handler 中，我们编写了真实的业务代码：返回 pong 字符串。

创建完 main.go 文件后，在当前目录下执行 go run main.go 启动 HTTP 服务，在一个新的 Linux 终端下发送 HTTP 请求，进行使用 curl 命令测试：
```
$ curl http://127.0.0.1:50052/ping
pong
```
### 总结
这一讲，我介绍了两种常用 API 风格中的一种，RESTful API。REST 是一种 API 规范，而 RESTful API 则是满足这种规范的 API 接口，RESTful API 的核心是规范。

在 REST 规范中，资源通过 URI 来标识，资源名使用名词而不是动词，并且用名词复数表示，资源都是分为 Collection 和 Member 两种。RESTful API 中，分别使用 POST、DELETE、PUT、GET 来表示 REST 资源的增删改查，HTTP 方法、Collection、Member 不同组合会产生不同的操作，具体的映射你可以看下 REST 资源操作映射为 HTTP 方法 部分的表格。

为了方便用户使用和理解，每个 RESTful API 的返回格式、错误和正确消息的返回格式，都应该保持一致。RESTful API 需要支持 API 版本，并且版本应该能够向前兼容，我们可以将版本号放在 URL 中、HTTP Header 中、Form 参数中，但这里我**建议**将版本号放在 URL 中，例如 /v1/users，这种形式比较直观。

另外，我们可以通过脊柱命名法来命名 API 接口名。对于一个 REST 资源，其查询接口还应该支持分页 / 过滤 / 排序 / 搜索功能，这些功能可以用同一套机制来实现。 API 的域名可以采用 https://marmotedu.com/api 和 https://iam.api.marmotedu.com 两种格式。

最后，在 Go 中我们可以使用 net/http 包来快速启动一个 RESTful API 服务。


## 13 | API 风格（下）：RPC API介绍

在 Go 项目开发中，如果业务对性能要求比较高，并且需要提供给多种编程语言调用，这时候就可以考虑使用 RPC API 接口。RPC 在 Go 项目开发中用得也非常多，需要我们认真掌握。

为什么要用rpc框架：
在采用微服务架构之前，我们需要思考为什么采用微服务架构，并不是所有的开发团队和发展阶段都适合采用微服务架构。通常，采用微服务架构可以解决以下问题：首先，开发团队具有一定的规模，所有成员共同开发一个单体应用的内耗太高，如果采用微服务架构，每个服务可以由单个或者少数成员独立负责。第二，业务系统的功能模块很多，耦合在一起会增加测试和部署的成本，任何一个模块故障也会导致整个系统故障。第三，功能模块之间的负载无法隔离，容易互相影响，没有办法针对热点模块的计算层或者存储层进行扩容。

如果我们采用微服务架构，单个服务是⾮常简单的，但是，分布式服务之间的功能调用远⽐单体应用内部更加复杂。在单体应用中，⼀个函数可以调⽤其他任何一个公共函数。在微服务架构中，一个函数只可以调⽤同⼀个微服务的函数。如何实现分布式服务之间的通信是微服务架构的首要问题，构建高性能、高可用的远程调用能力并不容易。值得庆幸的是，已经有 grpc、thrift、tars、go-zero、GoFrame、[cloudwego/kitex](https://github.com/cloudwego/kitex) 和 spring cloud 等大量开源的分布式服务开发框架，这些框架可以帮助终端用户快速地构建微服务。不幸的是，仅仅把服务开发出来并且跑通是不够的，保障大规模服务的稳定运营还需要考虑诸多问题，例如：在分布式架构中如何处理基础设施以及应用层的各种异常、如何实现大规模服务的无损发布和流量调度，如何定位和分析复杂调用链路中出现的问题等。对于中大型企业来说，还存在异构的开发技术栈和运行时环境，存在跨地域和混合云的架构要求，如何在更加复杂的应用场景中解决上述问题，面临更多的挑战。


### RPC 介绍
根据维基百科的定义，RPC（Remote Procedure Call），即远程过程调用，是一个计算机通信协议。该协议允许运行于一台计算机的程序调用另一台计算机的子程序，而程序员不用额外地为这个交互作用编程。

**通俗来讲，就是服务端实现了一个函数，客户端使用 RPC 框架提供的接口，像调用本地函数一样调用这个函数，并获取返回值。**RPC 屏蔽了底层的网络通信细节，使得开发人员无需关注网络编程的细节，可以将更多的时间和精力放在业务逻辑本身的实现上，从而提高开发效率。

RPC 的调用过程如下图所示：![img](https://static001.geekbang.org/resource/image/98/1d/984yy094616b9b24193b22a1f2f2271d.png?wh=2521x1671)


RPC 调用具体流程如下：
1. Client 通过本地调用，调用 Client Stub。
2. Client Stub 将参数打包（也叫 Marshalling）成一个消息，然后发送这个消息。 
3. Client 所在的 OS 将消息发送给 Server。
4. Server 端接收到消息后，将消息传递给 Server Stub。
5. Server Stub 将消息解包（也叫 Unmarshalling）得到参数。
6. Server Stub 调用服务端的子程序（函数），处理完后，将最终结果按照相反的步骤返回给 Client。

这里需要**注意**，Stub 负责调用参数和返回值的流化（serialization）、参数的打包和解包，以及网络层的通信。Client 端一般叫 Stub，Server 端一般叫 Skeleton。

目前，业界有很多优秀的 RPC 协议，例如腾讯的 Tars、阿里的 Dubbo、微博的 Motan、Facebook 的 Thrift、RPCX，等等。但使用最多的还是gRPC，这也是本专栏所采用的 RPC 框架，所以接下来我会重点介绍 gRPC 框架。

### gRPC 介绍
gRPC 是由 Google 开发的高性能、开源、跨多种编程语言的通用 RPC 框架，基于 HTTP 2.0 协议开发，默认采用 Protocol Buffers 数据序列化协议。gRPC 具有如下特性：
- 支持多种语言，例如 Go、Java、C、C++、C#、Node.js、PHP、Python、Ruby 等。
- 基于 IDL（Interface Definition Language）文件定义服务，通过 proto3 工具生成指定语言的数据结构、服务端接口以及客户端 Stub。通过这种方式，也可以将服务端和客户端解耦，使客户端和服务端可以并行开发。
- 通信协议基于标准的 HTTP/2 设计，支持双向流、消息头压缩、单 TCP 的多路复用、服务端推送等特性。
- 支持 Protobuf 和 JSON 序列化数据格式。Protobuf 是一种语言无关的高性能序列化框架，可以减少网络传输流量，提高通信效率。

这里要**注意**的是，gRPC 的全称不是 golang Remote Procedure Call，而是 google Remote Procedure Call。

gRPC 的调用如下图所示：![img](https://static001.geekbang.org/resource/image/01/09/01ac424c7c1d64f678e1218827bc0109.png?wh=2079x1025)

在 gRPC 中，客户端可以直接调用部署在不同机器上的 gRPC 服务所提供的方法，调用远端的 gRPC 方法就像调用本地的方法一样，非常简单方便，通过 gRPC 调用，我们可以非常容易地构建出一个分布式应用。

像很多其他的 RPC 服务一样，gRPC 也是通过 IDL 语言，预先定义好接口（接口的名字、传入参数和返回参数等）。在服务端，gRPC 服务实现我们所定义的接口。在客户端，gRPC 存根提供了跟服务端相同的方法。

gRPC 支持多种语言，比如我们可以用 Go 语言实现 gRPC 服务，并通过 Java 语言客户端调用 gRPC 服务所提供的方法。通过多语言支持，我们编写的 gRPC 服务能满足客户端多语言的需求。

gRPC API 接口通常使用的数据传输格式是 Protocol Buffers。接下来，我们就一起了解下 Protocol Buffers。


### Protocol Buffers 介绍
Protocol Buffers（ProtocolBuffer/ protobuf）是 Google 开发的一套对数据结构进行序列化的方法，可用作（数据）通信协议、数据存储格式等，也是一种更加灵活、高效的数据格式，与 XML、JSON 类似。它的传输性能非常好，所以常被用在一些对数据传输性能要求比较高的系统中，作为数据传输格式。Protocol Buffers 的主要特性有下面这几个。
- 更快的数据传输速度：protobuf 在传输时，会将数据序列化为二进制数据，和 XML、JSON 的文本传输格式相比，这可以节省大量的 IO 操作，从而提高数据传输速度。
- 跨平台多语言：protobuf 自带的编译工具 protoc 可以基于 protobuf 定义文件，编译出不同语言的客户端或者服务端，供程序直接调用，因此可以满足多语言需求的场景。
- 具有非常好的扩展性和兼容性，可以更新已有的数据结构，而不破坏和影响原有的程序。
- 基于 IDL 文件定义服务，通过 proto3 工具生成指定语言的数据结构、服务端和客户端接口。

在 gRPC 的框架中，Protocol Buffers 主要有三个作用。

第一，可以用来**定义数据结构**。举个例子，下面的代码定义了一个 SecretInfo 数据结构：
```go
// SecretInfo contains secret details.
message SecretInfo {
    string name = 1;
    string secret_id  = 2;
    string username   = 3;
    string secret_key = 4;
    int64 expires = 5;
    string description = 6;
    string created_at = 7;
    string updated_at = 8;
}
```
第二，可以用来**定义服务接口**。下面的代码定义了一个 Cache 服务，服务包含了 ListSecrets 和 ListPolicies 两个 API 接口。


```go
// Cache implements a cache rpc service.
service Cache{
  rpc ListSecrets(ListSecretsRequest) returns (ListSecretsResponse) {}
  rpc ListPolicies(ListPoliciesRequest) returns (ListPoliciesResponse) {}
}
```
第三，可以通过 **protobuf 序列化和反序列化**，提升传输效率。


### gRPC 示例
我们已经对 gRPC 这一通用 RPC 框架有了一定的了解，但是你可能还不清楚怎么使用 gRPC 编写 API 接口。接下来，我就通过 gRPC 官方的一个示例来快速给大家展示下。运行本示例需要在 Linux 服务器上安装 Go 编译器、Protocol buffer 编译器（protoc，v3）和 protoc 的 Go 语言插件，在 02 讲 中我们已经安装过，这里不再讲具体的安装方法。

这个示例分为下面几个步骤：定义 gRPC 服务。生成客户端和服务器代码。实现 gRPC 服务。实现 gRPC 客户端。

示例代码存放在gopractise-demo/apistyle/greeter目录下。代码结构如下：
```
$ tree
├── client
│   └── main.go
├── helloworld
│   ├── helloworld.pb.go
│   └── helloworld.proto
└── server
    └── main.go
```

client 目录存放 Client 端的代码，helloworld 目录用来存放服务的 IDL 定义，server 目录用来存放 Server 端的代码。

下面我具体介绍下这个示例的四个步骤。
1. 定义 gRPC 服务。

首先，需要定义我们的服务。进入 helloworld 目录，新建文件 helloworld.proto：
```go
$ cd helloworld
$ vi helloworld.proto
```

内容如下：
```go
syntax = "proto3";

option go_package = "github.com/marmotedu/gopractise-demo/apistyle/greeter/helloworld";

package helloworld;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1; 
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```
在 helloworld.proto 定义文件中，option 关键字用来对.proto 文件进行一些设置，其中 go_package 是必需的设置，而且 go_package 的值必须是包导入的路径。package 关键字指定生成的.pb.go 文件所在的包名。我们通过 service 关键字定义服务，然后再指定该服务拥有的 RPC 方法，并定义方法的请求和返回的结构体类型：

```go
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}
```

gRPC 支持定义 **4 种类型的服务方法**，分别是**简单模式、服务端数据流模式、客户端数据流模式和双向数据流模式。**
- 简单模式（Simple RPC）：是最简单的 gRPC 模式。客户端发起一次请求，服务端响应一个数据。定义格式为 rpc SayHello (HelloRequest) returns (HelloReply) {}。
- 服务端数据流模式（Server-side streaming RPC）：客户端发送一个请求，服务器返回数据流响应，客户端从流中读取数据直到为空。定义格式为 rpc SayHello (HelloRequest) returns (stream HelloReply) {}。
- 客户端数据流模式（Client-side streaming RPC）：客户端将消息以流的方式发送给服务器，服务器全部处理完成之后返回一次响应。定义格式为 rpc SayHello (stream HelloRequest) returns (HelloReply) {}。
- 双向数据流模式（Bidirectional streaming RPC）：客户端和服务端都可以向对方发送数据流，这个时候双方的数据可以同时互相发送，也就是可以实现实时交互 RPC 框架原理。定义格式为 rpc SayHello (stream HelloRequest) returns (stream HelloReply) {}。

本示例使用了简单模式。.proto 文件也包含了 Protocol Buffers 消息的定义，包括请求消息和返回消息。例如请求消息：
```go
// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}
```

2. 生成客户端和服务器代码。

接下来，我们需要根据.proto 服务定义生成 gRPC 客户端和服务器接口。我们可以使用 protoc 编译工具，并指定使用其 Go 语言插件来生成：

```
$ protoc -I. --go_out=plugins=grpc:$GOPATH/src helloworld.proto
$ ls
helloworld.pb.go  helloworld.proto
```

你可以看到，新增了一个 helloworld.pb.go 文件。

3. 实现 gRPC 服务。
接着，我们就可以实现 gRPC 服务了。进入 server 目录，新建 main.go 文件：
```
$ cd ../server
$ vi main.go
```
main.go 内容如下：
```go
// Package main implements a server for Greeter service.
package main

import (
  "context"
  "log"
  "net"

  pb "github.com/marmotedu/gopractise-demo/apistyle/greeter/helloworld"
  "google.golang.org/grpc"
)

const (
  port = ":50051"
)

// server is used to implement helloworld.GreeterServer.
type server struct {
  pb.UnimplementedGreeterServer
}

// SayHello implements helloworld.GreeterServer
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
  log.Printf("Received: %v", in.GetName())
  return &pb.HelloReply{Message: "Hello " + in.GetName()}, nil
}

func main() {
  lis, err := net.Listen("tcp", port)
  if err != nil {
    log.Fatalf("failed to listen: %v", err)
  }
  s := grpc.NewServer()
  pb.RegisterGreeterServer(s, &server{})
  if err := s.Serve(lis); err != nil {
    log.Fatalf("failed to serve: %v", err)
  }
}
```


上面的代码实现了我们上一步根据服务定义生成的 Go 接口。

我们先定义了一个 Go 结构体 server，并为 server 结构体添加SayHello(context.Context, pb.HelloRequest) (pb.HelloReply, error)方法，也就是说 server 是 GreeterServer 接口（位于 helloworld.pb.go 文件中）的一个实现。

在我们实现了 gRPC 服务所定义的方法之后，就可以通过 net.Listen(...) 指定监听客户端请求的端口；接着，通过 grpc.NewServer() 创建一个 gRPC Server 实例，并通过 pb.RegisterGreeterServer(s, &server{}) 将该服务注册到 gRPC 框架中；最后，通过 s.Serve(lis) 启动 gRPC 服务。

创建完 main.go 文件后，在当前目录下执行 go run main.go ，启动 gRPC 服务。

4. 实现 gRPC 客户端。

打开一个新的 Linux 终端，进入 client 目录，新建 main.go 文件：
```
$ cd ../client
$ vi main.go
```
main.go 内容如下：
```go
// Package main implements a client for Greeter service.
package main

import (
  "context"
  "log"
  "os"
  "time"

  pb "github.com/marmotedu/gopractise-demo/apistyle/greeter/helloworld"
  "google.golang.org/grpc"
)

const (
  address     = "localhost:50051"
  defaultName = "world"
)

func main() {
  // Set up a connection to the server.
  conn, err := grpc.Dial(address, grpc.WithInsecure(), grpc.WithBlock())
  if err != nil {
    log.Fatalf("did not connect: %v", err)
  }
  defer conn.Close()
  c := pb.NewGreeterClient(conn)

  // Contact the server and print out its response.
  name := defaultName
  if len(os.Args) > 1 {
    name = os.Args[1]
  }
  ctx, cancel := context.WithTimeout(context.Background(), time.Second)
  defer cancel()
  r, err := c.SayHello(ctx, &pb.HelloRequest{Name: name})
  if err != nil {
    log.Fatalf("could not greet: %v", err)
  }
  log.Printf("Greeting: %s", r.Message)
}
```

在上面的代码中，我们通过如下代码创建了一个 gRPC 连接，用来跟服务端进行通信：
```go
// Set up a connection to the server.
conn, err := grpc.Dial(address, grpc.WithInsecure(), grpc.WithBlock())
if err != nil {
    log.Fatalf("did not connect: %v", err)
}
defer conn.Close()
```

在创建连接时，我们可以指定不同的选项，用来控制创建连接的方式，例如 grpc.WithInsecure()、grpc.WithBlock() 等。gRPC 支持很多选项，更多的选项可以参考 grpc 仓库下dialoptions.go文件中以 With 开头的函数。

连接建立起来之后，我们需要创建一个客户端 stub，用来执行 RPC 请求c := pb.NewGreeterClient(conn)。创建完成之后，我们就可以像调用本地函数一样，调用远程的方法了。例如，下面一段代码通过 c.SayHello 这种本地式调用方式调用了远端的 SayHello 接口：
```go
r, err := c.SayHello(ctx, &pb.HelloRequest{Name: name})
if err != nil {
    log.Fatalf("could not greet: %v", err)
}
log.Printf("Greeting: %s", r.Message)
```

从上面的调用格式中，我们可以看到 RPC 调用具有下面两个特点。
- 调用方便：RPC 屏蔽了底层的网络通信细节，使得调用 RPC 就像调用本地方法一样方便，调用方式跟大家所熟知的调用类的方法一致：ClassName.ClassFuc(params)。
- 不需要打包和解包：RPC 调用的入参和返回的结果都是 Go 的结构体，不需要对传入参数进行打包操作，也不需要对返回参数进行解包操作，简化了调用步骤。

最后，创建完 main.go 文件后，在当前目录下，执行 go run main.go 发起 RPC 调用：
```
$ go run main.go
2020/10/17 07:55:00 Greeting: Hello world
```

至此，我们用四个步骤，创建并调用了一个 gRPC 服务。接下来我再给大家讲解一个在具体场景中的**注意**事项。

在做服务开发时，我们经常会遇到一种场景：定义一个接口，接口会通过判断是否传入某个参数，决定接口行为。例如，我们想提供一个 GetUser 接口，期望 GetUser 接口在传入 username 参数时，根据 username 查询用户的信息，如果没有传入 username，则默认根据 userId 查询用户信息。

这时候，我们需要判断客户端有没有传入 username 参数。我们不能根据 username 是否为空值来判断，因为我们不能区分客户端传的是空值，还是没有传 username 参数。这是由 Go 语言的语法特性决定的：如果客户端没有传入 username 参数，Go 会默认赋值为所在类型的零值，而字符串类型的零值就是空字符串。

那我们怎么判断客户端有没有传入 username 参数呢？最好的方法是通过指针来判断，如果是 nil 指针就说明没有传入，非 nil 指针就说明传入，具体实现步骤如下：

1. 编写 protobuf 定义文件。新建 user.proto 文件，内容如下:
```proto
syntax = "proto3";

package proto;
option go_package = "github.com/marmotedu/gopractise-demo/protobuf/user";

//go:generate protoc -I. --experimental_allow_proto3_optional --go_out=plugins=grpc:.

service User {
  rpc GetUser(GetUserRequest) returns (GetUserResponse) {}
}

message GetUserRequest {
  string class = 1;
  optional string username = 2;
  optional string user_id = 3;
}

message GetUserResponse {
  string class = 1;
  string user_id = 2;
  string username = 3;
  string address = 4;
  string sex = 5;
  string phone = 6;
}
```
你需要**注意**，这里我们在需要设置为可选字段的前面添加了 optional 标识。

2. 使用 protoc 工具编译 protobuf 文件。

在执行 protoc 命令时，需要传入--experimental_allow_proto3_optional参数以打开 optional 选项，编译命令如下：
```
$ protoc --experimental_allow_proto3_optional --go_out=plugins=grpc:. user.proto
```
上述编译命令会生成 user.pb.go 文件，其中的 GetUserRequest 结构体定义如下：
```go
type GetUserRequest struct {
    state         protoimpl.MessageState
    sizeCache     protoimpl.SizeCache
    unknownFields protoimpl.UnknownFields

    Class    string  `protobuf:"bytes,1,opt,name=class,proto3" json:"class,omitempty"`
    Username *string `protobuf:"bytes,2,opt,name=username,proto3,oneof" json:"username,omitempty"`
    UserId   *string `protobuf:"bytes,3,opt,name=user_id,json=userId,proto3,oneof" json:"user_id,omitempty"`
}
```
通过 optional + --experimental_allow_proto3_optional 组合，我们可以将一个字段编译为指针类型。
3. 编写 gRPC 接口实现。
新建一个 user.go 文件，内容如下：
```go
package user

import (
    "context"

    pb "github.com/marmotedu/api/proto/apiserver/v1"

    "github.com/marmotedu/iam/internal/apiserver/store"
)

type User struct {
}

func (c *User) GetUser(ctx context.Context, r *pb.GetUserRequest) (*pb.GetUserResponse, error) {
    if r.Username != nil {
        return store.Client().Users().GetUserByName(r.Class, r.Username)
    }

    return store.Client().Users().GetUserByID(r.Class, r.UserId)
}
```
总之，在 GetUser 方法中，我们可以通过判断 r.Username 是否为 nil，来判断客户端是否传入了 Username 参数。
### RESTful VS gRPC
到这里，今天我们已经介绍完了 gRPC API。回想一下我们昨天学习的 RESTful API，你可能想问：这两种 API 风格分别有什么优缺点，适用于什么场景呢？我把这个问题的答案放在了下面这张表中，你可以对照着它，根据自己的需求在实际应用时进行选择。![img](https://static001.geekbang.org/resource/image/e6/ab/e6ae61fc4b0fc821f94d257239f332ab.png?wh=1483x1026)

当然，更多的时候，RESTful API 和 gRPC API 是一种合作的关系，对内业务使用 gRPC API，对外业务使用 RESTful API，如下图所示：![img](https://static001.geekbang.org/resource/image/47/18/471ac923d2eaeca8fe13cb74731c1318.png?wh=1606x1144)


在 Go 项目开发中，我们可以选择使用 RESTful API 风格和 RPC API 风格，这两种服务都用得很多。其中，RESTful API 风格因为规范、易理解、易用，所以适合用在需要对外提供 API 接口的场景中。而 RPC API 因为性能比较高、调用方便，更适合用在内部业务中。

RESTful API 使用的是 HTTP 协议，而 RPC API 使用的是 RPC 协议。目前，有很多 RPC 协议可供你选择，而我**推荐**你使用 gRPC，因为它很轻量，同时性能很高、很稳定，是一个优秀的 RPC 框架。所以目前业界用的最多的还是 gRPC 协议，腾讯、阿里等大厂内部很多核心的线上服务用的就是 gRPC。

除了使用 gRPC 协议，在进行 Go 项目开发前，你也可以了解业界一些其他的优秀 Go RPC 框架，比如腾讯的 tars-go、阿里的 dubbo-go、Facebook 的 thrift、rpcx 等，你可以在项目开发之前一并调研，根据实际情况进行选择。

## 14 | 项目管理：如何编写高质量的Makefile？

要写出一个优雅的 Go 项目，不仅仅是要开发一个优秀的 Go 应用，而且还要能够高效地管理项目。有效手段之一，就是通过 Makefile 来管理我们的项目，这就要求我们要为项目编写 Makefile 文件。


在和其他开发同学交流时，我发现大家都认可 Makefile 强大的项目管理能力，也会自己编写 Makefile。但是其中的一些人项目管理做得并不好，我和他们进一步交流后发现，这些同学在用 Makefile 简单的语法重复编写一些低质量 Makefile 文件，根本没有把 Makefile 的功能充分发挥出来。

下面给你举个例子，你就会理解低质量的 Makefile 文件是什么样的了。


```makefile
build: clean vet
  @mkdir -p ./Role
  @export GOOS=linux && go build -v .

vet:
  go vet ./...

fmt:
  go fmt ./...

clean:
  rm -rf dashboard
```
上面这个 Makefile 存在不少问题。例如：功能简单，只能完成最基本的编译、格式化等操作，像构建镜像、自动生成代码等一些高阶的功能都没有；扩展性差，没法编译出可在 Mac 下运行的二进制文件；没有 Help 功能，使用难度高；单 Makefile 文件，结构单一，不适合添加一些复杂的管理功能。所以，我们不光要编写 Makefile，还要编写高质量的 Makefile。那么如何编写一个高质量的 Makefile 呢？我觉得，可以通过以下 4 个方法来实现：


打好基础，也就是熟练掌握 Makefile 的语法。做好准备工作，也就是提前规划 Makefile 要实现的功能。进行规划，设计一个合理的 Makefile 结构。掌握方法，用好 Makefile 的编写技巧。


### 熟练掌握 Makefile 语法
工欲善其事，必先利其器。编写高质量 Makefile 的第一步，便是熟练掌握 Makefile 的核心语法。

因为 Makefile 的语法比较多，我把一些**建议**你重点掌握的语法放在了近期会更新的特别放送中，包括 Makefile 规则语法、伪目标、变量赋值、条件语句和 Makefile 常用函数等等。如果你想更深入、全面地学习 Makefile 的语法，我**推荐**你学习陈皓老师编写的[《跟我一起写 Makefile》 (PDF 重制版)](https://github.com/seisman/how-to-write-makefile)。


### 规划 Makefile 要实现的功能
接着，我们需要规划 Makefile 要实现的功能。提前规划好功能，有利于你设计 Makefile 的整体结构和实现方法。


不同项目拥有不同的 Makefile 功能，这些功能中一小部分是通过目标文件来实现的，但更多的功能是通过伪目标来实现的。对于 Go 项目来说，虽然不同项目集成的功能不一样，但绝大部分项目都需要实现一些通用的功能。接下来，我们就来看看，在一个大型 Go 项目中 Makefile 通常可以实现的功能。下面是 IAM 项目的 Makefile 所集成的功能，希望会对你日后设计 Makefile 有一些帮助。


```makefile
$ make help

Usage: make <TARGETS> <OPTIONS> ...

Targets:
  # 代码生成类命令
  gen                Generate all necessary files, such as error code files.

  # 格式化类命令
  format             Gofmt (reformat) package sources (exclude vendor dir if existed).

  # 静态代码检查
  lint               Check syntax and styling of go sources.

  # 测试类命令
  test               Run unit test.
  cover              Run unit test and get test coverage.

  # 构建类命令
  build              Build source code for host platform.
  build.multiarch    Build source code for multiple platforms. See option PLATFORMS.

  # Docker镜像打包类命令
  image              Build docker images for host arch.
  image.multiarch    Build docker images for multiple platforms. See option PLATFORMS.
  push               Build docker images for host arch and push images to registry.
  push.multiarch     Build docker images for multiple platforms and push images to registry.

  # 部署类命令
  deploy             Deploy updated components to development env.

  # 清理类命令
  clean              Remove all files that are created by building.

  # 其他命令，不同项目会有区别
  release            Release iam
  verify-copyright   Verify the boilerplate headers for all files.
  ca                 Generate CA files for all iam components.
  install            Install iam system with all its components.
  swagger            Generate swagger document.
  tools              install dependent tools.

  # 帮助命令
  help               Show this help info.

# 选项
Options:
  DEBUG        Whether to generate debug symbols. Default is 0.
  BINS         The binaries to build. Default is all of cmd.
               This option is available when using: make build/build.multiarch
               Example: make build BINS="iam-apiserver iam-authz-server"
  ...
```

更详细的命令，你可以在 IAM 项目仓库根目录下执行make help查看。通常而言，Go 项目的 Makefile 应该实现以下功能：格式化代码、静态代码检查、单元测试、代码构建、文件清理、帮助等等。如果通过 docker 部署，还需要有 docker 镜像打包功能。因为 Go 是跨平台的语言，所以构建和 docker 打包命令，还要能够支持不同的 CPU 架构和平台。为了能够更好地控制 Makefile 命令的行为，还需要支持 Options。


为了方便查看 Makefile 集成了哪些功能，我们需要支持 help 命令。help 命令最好通过解析 Makefile 文件来输出集成的功能，例如：


```makefile
## help: Show this help info.
.PHONY: help
help: Makefile
  @echo -e "\nUsage: make <TARGETS> <OPTIONS> ...\n\nTargets:"
  @sed -n 's/^##//p' $< | column -t -s ':' | sed -e 's/^/ /'
  @echo "$$USAGE_OPTIONS"
```
上面的 help 命令，通过解析 Makefile 文件中的##注释，获取支持的命令。通过这种方式，我们以后新加命令时，就不用再对 help 命令进行修改了。你可以参考上面的 Makefile 管理功能，结合自己项目的需求，整理出一个 Makefile 要实现的功能列表，并初步确定实现思路和方法。做完这些，你的编写前准备工作就基本完成了。


### 设计合理的 Makefile 结构
设计完 Makefile 需要实现的功能，接下来我们就进入 Makefile 编写阶段。编写阶段的第一步，就是设计一个合理的 Makefile 结构。


对于大型项目来说，需要管理的内容很多，所有管理功能都集成在一个 Makefile 中，可能会导致 Makefile 很大，难以阅读和维护，所以****建议**采用分层的设计方法，根目录下的 Makefile 聚合所有的 Makefile 命令，具体实现则按功能分类，放在另外的 Makefile 中。**


我们经常会在 Makefile 命令中集成 shell 脚本，但如果 shell 脚本过于复杂，也会导致 Makefile 内容过多，难以阅读和维护。并且在 Makefile 中集成复杂的 shell 脚本，编写体验也很差。对于这种情况，**可以将复杂的 shell 命令封装在 shell 脚本中，供 Makefile 直接调用，而一些简单的命令则可以直接集成在 Makefile 中**。所以，最终我**推荐**的 Makefile 结构如下：


![img](https://static001.geekbang.org/resource/image/5c/f7/5c524e0297b6d6e4e151643d2e1bbbf7.png?wh=2575x1017)

在上面的 Makefile 组织方式中，根目录下的 Makefile 聚合了项目所有的管理功能，这些管理功能通过 Makefile 伪目标的方式实现。同时，还将这些伪目标进行分类，把相同类别的伪目标放在同一个 Makefile 中，这样可以使得 Makefile 更容易维护。对于复杂的命令，则编写成独立的 shell 脚本，并在 Makefile 命令中调用这些 shell 脚本。举个例子，下面是 IAM 项目的 Makefile 组织结构：


```
├── Makefile
├── scripts
│   ├── gendoc.sh
│   ├── make-rules
│   │   ├── gen.mk
│   │   ├── golang.mk
│   │   ├── image.mk
│   │   └── ...
    └── ...
```
我们将相同类别的操作统一放在 scripts/make-rules 目录下的 Makefile 文件中。Makefile 的文件名参考分类命名，例如 golang.mk。最后，在 /Makefile 中 include 这些 Makefile。为了跟 Makefile 的层级相匹配，golang.mk 中的所有目标都按go.xxx这种方式命名。通过这种命名方式，我们可以很容易分辨出某个目标完成什么功能，放在什么文件里，这在复杂的 Makefile 中尤其有用。以下是 IAM 项目根目录下，Makefile 的内容摘录，你可以看一看，作为参考：


```makefile
include scripts/make-rules/golang.mk
include scripts/make-rules/image.mk
include scripts/make-rules/gen.mk
include scripts/make-rules/...

## build: Build source code for host platform.
.PHONY: build
build:
  @$(MAKE) go.build

## build.multiarch: Build source code for multiple platforms. See option PLATFORMS.
.PHONY: build.multiarch
build.multiarch:
  @$(MAKE) go.build.multiarch

## image: Build docker images for host arch.
.PHONY: image
image:
  @$(MAKE) image.build

## push: Build docker images for host arch and push images to registry.
.PHONY: push
push:
  @$(MAKE) image.push

## ca: Generate CA files for all iam components.
.PHONY: ca
ca:
  @$(MAKE) gen.ca
```
另外，一个合理的 Makefile 结构应该具有前瞻性。也就是说，要在不改变现有结构的情况下，接纳后面的新功能。这就需要你整理好 Makefile 当前要实现的功能、即将要实现的功能和未来可能会实现的功能，然后基于这些功能，利用 Makefile 编程技巧，编写可扩展的 Makefile。


这里需要你**注意**：上面的 Makefile 通过 .PHONY 标识定义了大量的伪目标，定义伪目标一定要加 .PHONY 标识，否则当有同名的文件时，伪目标可能不会被执行。



### 掌握 Makefile 编写技巧
最后，在编写过程中，你还需要掌握一些 Makefile 的编写技巧，这些技巧可以使你编写的 Makefile 扩展性更强，功能更强大。接下来，我会把自己长期开发过程中积累的一些 Makefile 编写经验分享给你。这些技巧，你需要在实际编写中多加练习，并形成编写习惯。



#### 技巧 1：善用通配符和自动变量
Makefile 允许对目标进行类似正则运算的匹配，主要用到的通配符是%。通过使用通配符，可以使不同的目标使用相同的规则，从而使 Makefile 扩展性更强，也更简洁。我们的 IAM 实战项目中，就大量使用了通配符%，例如：go.build.%、ca.gen.%、deploy.run.%、tools.verify.%、tools.install.%等。这里，我们来看一个具体的例子，tools.verify.%（位于scripts/make-rules/tools.mk文件中）定义如下：

```makefile
tools.verify.%:
  @if ! which $* &>/dev/null; then $(MAKE) tools.install.$*; fi
```
make tools.verify.swagger, make tools.verify.mockgen等均可以使用上面定义的规则，%分别代表了swagger和mockgen。如果不使用%，则我们需要分别为tools.verify.swagger和tools.verify.mockgen定义规则，很麻烦，后面修改也困难。


另外，这里也能看出tools.verify.%这种命名方式的好处：tools 说明依赖的定义位于scripts/make-rules/tools.mk Makefile 中；verify说明tools.verify.%伪目标属于 verify 分类，主要用来验证工具是否安装。通过这种命名方式，你可以很容易地知道目标位于哪个 Makefile 文件中，以及想要完成的功能。另外，上面的定义中还用到了自动变量$*，用来指代被匹配的值swagger、mockgen。


#### 技巧 2：善用函数
Makefile 自带的函数能够帮助我们实现很多强大的功能。所以，在我们编写 Makefile 的过程中，如果有功能需求，可以优先使用这些函数。我把常用的函数以及它们实现的功能整理在了 Makefile 常用函数列表 中，你可以参考下。


IAM 的 Makefile 文件中大量使用了上述函数，如果你想查看这些函数的具体使用方法和场景，可以参考 IAM 项目的 Makefile 文件 make-rules。



#### 技巧 3：依赖需要用到的工具
如果 Makefile 某个目标的命令中用到了某个工具，可以将该工具放在目标的依赖中。这样，当执行该目标时，就可以指定检查系统是否安装该工具，如果没有安装则自动安装，从而实现更高程度的自动化。例如，/Makefile 文件中，format 伪目标，定义如下：


```makefile
.PHONY: format
format: tools.verify.golines tools.verify.goimports
  @echo "===========> Formating codes"
  @$(FIND) -type f -name '*.go' | $(XARGS) gofmt -s -w
  @$(FIND) -type f -name '*.go' | $(XARGS) goimports -w -local $(ROOT_PACKAGE)
  @$(FIND) -type f -name '*.go' | $(XARGS) golines -w --max-len=120 --reformat-tags --shorten-comments --ignore-generated .
```
你可以看到，format 依赖tools.verify.golines tools.verify.goimports。我们再来看下tools.verify.golines的定义：


```makefile
tools.verify.%:
  @if ! which $* &>/dev/null; then $(MAKE) tools.install.$*; fi
```

再来看下tools.install.$*规则：



```makefile
.PHONY: install.golines
install.golines:
  @$(GO) get -u github.com/segmentio/golines
```
通过tools.verify.%规则定义，我们可以知道，tools.verify.%会先检查工具是否安装，如果没有安装，就会执行tools.install.$*来安装。如此一来，当我们执行tools.verify.%目标时，如果系统没有安装 golines 命令，就会自动调用go get安装，提高了 Makefile 的自动化程度。



#### 技巧 4：把常用功能放在 /Makefile 中，不常用的放在分类 Makefile 中
一个项目，尤其是大型项目，有很多需要管理的地方，其中大部分都可以通过 Makefile 实现自动化操作。不过，为了保持 /Makefile 文件的整洁性，我们不能把所有的命令都添加在 /Makefile 文件中。



一个比较好的**建议**是，将常用功能放在 /Makefile 中，不常用的放在分类 Makefile 中，并在 /Makefile 中 include 这些分类 Makefile。例如，IAM 项目的 /Makefile 集成了format、lint、test、build等常用命令，而将gen.errcode.code、gen.errcode.doc这类不常用的功能放在 scripts/make-rules/gen.mk 文件中。当然，我们也可以直接执行 make gen.errcode.code来执行gen.errcode.code伪目标。通过这种方式，既可以保证 /Makefile 的简洁、易维护，又可以通过make命令来运行伪目标，更加灵活。



#### 技巧 5：编写可扩展的 Makefile
什么叫可扩展的 Makefile 呢？在我看来，可扩展的 Makefile 包含两层含义：


可以在不改变 Makefile 结构的情况下添加新功能。扩展项目时，新功能可以自动纳入到 Makefile 现有逻辑中。


其中的第一点，我们可以通过设计合理的 Makefile 结构来实现。要实现第二点，就需要我们在编写 Makefile 时采用一定的技巧，例如多用通配符、自动变量、函数等。这里我们来看一个例子，可以让你更好地理解。


在我们 IAM 实战项目的golang.mk中，执行 make go.build 时能够构建 cmd/ 目录下的所有组件，也就是说，当有新组件添加时， make go.build 仍然能够构建新增的组件，这就实现了上面说的第二点。具体实现方法如下：



```makefile
COMMANDS ?= $(filter-out %.md, $(wildcard ${ROOT_DIR}/cmd/*))
BINS ?= $(foreach cmd,${COMMANDS},$(notdir ${cmd}))

.PHONY: go.build
go.build: go.build.verify $(addprefix go.build., $(addprefix $(PLATFORM)., $(BINS)))
.PHONY: go.build.%               

go.build.%:             
  $(eval COMMAND := $(word 2,$(subst ., ,$*)))
  $(eval PLATFORM := $(word 1,$(subst ., ,$*)))
  $(eval OS := $(word 1,$(subst _, ,$(PLATFORM))))           
  $(eval ARCH := $(word 2,$(subst _, ,$(PLATFORM))))                         
  @echo "===========> Building binary $(COMMAND) $(VERSION) for $(OS) $(ARCH)"
  @mkdir -p $(OUTPUT_DIR)/platforms/$(OS)/$(ARCH)
  @CGO_ENABLED=0 GOOS=$(OS) GOARCH=$(ARCH) $(GO) build $(GO_BUILD_FLAGS) -o $(OUTPUT_DIR)/platforms/$(OS)/$(ARCH)/$(COMMAND)$(GO_OUT_EXT) $(ROOT_PACKAGE)/cmd/$(COMMAND)
```
当执行make go.build 时，会执行 go.build 的依赖 $(addprefix go.build., $(addprefix $(PLATFORM)., $(BINS))) ,addprefix函数最终返回字符串 go.build.linux_amd64.iamctl go.build.linux_amd64.iam-authz-server go.build.linux_amd64.iam-apiserver ... ，这时候就会执行 go.build.% 伪目标。

在 go.build.% 伪目标中，通过 eval、word、subst 函数组合，算出了 COMMAND 的值 iamctl/iam-apiserver/iam-authz-server/...，最终通过 $(ROOT_PACKAGE)/cmd/$(COMMAND) 定位到需要构建的组件的 main 函数所在目录。


上述实现中有两个技巧，你可以**注意**下。首先，通过


```makefile
COMMANDS ?= $(filter-out %.md, $(wildcard ${ROOT_DIR}/cmd/*))
BINS ?= $(foreach cmd,${COMMANDS},$(notdir ${cmd}))
```

获取到了 cmd/ 目录下的所有组件名。接着，通过使用通配符和自动变量，自动匹配到go.build.linux_amd64.iam-authz-server 这类伪目标并构建。可以看到，想要编写一个可扩展的 Makefile，熟练掌握 Makefile 的用法是基础，更多的是需要我们动脑思考如何去编写 Makefile。


#### 技巧 6：将所有输出存放在一个目录下，方便清理和查找
在执行 Makefile 的过程中，会输出各种各样的文件，例如 Go 编译后的二进制文件、测试覆盖率数据等，我**建议**你把这些文件统一放在一个目录下，方便后期的清理和查找。通常我们可以把它们放在_output这类目录下，这样清理时就很方便，只需要清理_output文件夹就可以，例如：


```makefile
.PHONY: go.clean
go.clean:
  @echo "===========> Cleaning all build output"
  @-rm -vrf $(OUTPUT_DIR)
```
这里要**注意**，要用-rm，而不是rm，防止在没有_output目录时，执行make go.clean报错。


#### 技巧 7：使用带层级的命名方式
通过使用带层级的命名方式，例如tools.verify.swagger ，我们可以实现目标分组管理。这样做的好处有很多。首先，当 Makefile 有大量目标时，通过分组，我们可以更好地管理这些目标。其次，分组也能方便理解，可以通过组名一眼识别出该目标的功能类别。最后，这样做还可以大大减小目标重名的概率。



例如，IAM 项目的 Makefile 就大量采用了下面这种命名方式。


```makefile
.PHONY: gen.run
gen.run: gen.clean gen.errcode gen.docgo

.PHONY: gen.errcode
gen.errcode: gen.errcode.code gen.errcode.doc

.PHONY: gen.errcode.code
gen.errcode.code: tools.verify.codegen
    ...
.PHONY: gen.errcode.doc
gen.errcode.doc: tools.verify.codegen
    ...
```

#### 技巧 8：做好目标拆分
还有一个比较实用的技巧：我们要合理地拆分目标。比如，我们可以将安装工具拆分成两个目标：验证工具是否已安装和安装工具。通过这种方式，可以给我们的 Makefile 带来更大的灵活性。例如：我们可以根据需要选择性地执行其中一个操作，也可以两个操作一起执行。

这里来看一个例子：


```makefile
gen.errcode.code: tools.verify.codegen

tools.verify.%:    
  @if ! which $* &>/dev/null; then $(MAKE) tools.install.$*; fi  

.PHONY: install.codegen
install.codegen:              
  @$(GO) install ${ROOT_DIR}/tools/codegen/codegen.go
```
上面的 Makefile 中，gen.errcode.code 依赖了 tools.verify.codegen，tools.verify.codegen 会先检查 codegen 命令是否存在，如果不存在，再调用 install.codegen 来安装 codegen 工具。如果我们的 Makefile 设计是：

```makefile
gen.errcode.code: install.codegen
```

那每次执行 gen.errcode.code 都要重新安装 codegen 命令，这种操作是不必要的，还会导致 make gen.errcode.code 执行很慢。



#### 技巧 9：设置 OPTIONS
编写 Makefile 时，我们还需要把一些可变的功能通过 OPTIONS 来控制。为了帮助你理解，这里还是拿 IAM 项目的 Makefile 来举例。



假设我们需要通过一个选项 V ，来控制是否需要在执行 Makefile 时打印详细的信息。这可以通过下面的步骤来实现。

首先，在 /Makefile 中定义 USAGE_OPTIONS 。定义 USAGE_OPTIONS 可以使开发者在执行 make help 后感知到此 OPTION，并根据需要进行设置。



```makefile
define USAGE_OPTIONS    
                         
Options:
  ...
  BINS         The binaries to build. Default is all of cmd.
               ...
  ...
  V            Set to 1 enable verbose build. Default is 0.    
endef    
export USAGE_OPTIONS    
```

接着，在scripts/make-rules/common.mk文件中，我们通过判断有没有设置 V 选项，来选择不同的行为：


```makefile
ifndef V    
MAKEFLAGS += --no-print-directory    
endif
```

当然，我们还可以通过下面的方法来使用 V ：


```makefile
ifeq ($(origin V), undefined)                                
MAKEFLAGS += --no-print-directory              
endif
```
上面，我介绍了 V OPTION，我们在 Makefile 中通过判断有没有定义 V ，来执行不同的操作。其实还有一种 OPTION，这种 OPTION 的值我们在 Makefile 中是直接使用的，例如BINS。针对这种 OPTION，我们可以通过以下方式来使用：


```makefile
BINS ?= $(foreach cmd,${COMMANDS},$(notdir ${cmd}))
...
go.build: go.build.verify $(addprefix go.build., $(addprefix $(PLATFORM)., $(BINS)))
```
也就是说，通过 ?= 来判断 BINS 变量有没有被赋值，如果没有，则赋予等号后的值。接下来，就可以在 Makefile 规则中使用它。


#### 技巧 10：定义环境变量
我们可以在 Makefile 中定义一些环境变量，例如：



```makefile
GO := go                                          
GO_SUPPORTED_VERSIONS ?= 1.13|1.14|1.15|1.16|1.17    
GO_LDFLAGS += -X $(VERSION_PACKAGE).GitVersion=$(VERSION) \    
  -X $(VERSION_PACKAGE).GitCommit=$(GIT_COMMIT) \       
  -X $(VERSION_PACKAGE).GitTreeState=$(GIT_TREE_STATE) \                          
  -X $(VERSION_PACKAGE).BuildDate=$(shell date -u +'%Y-%m-%dT%H:%M:%SZ')    
ifneq ($(DLV),)                                                                                                                              
  GO_BUILD_FLAGS += -gcflags "all=-N -l"    
  LDFLAGS = ""      
endif                                                                                   
GO_BUILD_FLAGS += -tags=jsoniter -ldflags "$(GO_LDFLAGS)" 
...
FIND := find . ! -path './third_party/*' ! -path './vendor/*'    
XARGS := xargs --no-run-if-empty 
```
这些环境变量和编程中使用宏定义的作用是一样的：只要修改一处，就可以使很多地方同时生效，避免了重复的工作。通常，我们可以将 GO、GO_BUILD_FLAGS、FIND 这类变量定义为环境变量。


#### 技巧 11：自己调用自己
在编写 Makefile 的过程中，你可能会遇到这样一种情况：A-Target 目标命令中，需要完成操作 B-Action，而操作 B-Action 我们已经通过伪目标 B-Target 实现过。为了达到最大的代码复用度，这时候最好的方式是在 A-Target 的命令中执行 B-Target。方法如下：


```makefile
tools.verify.%:
  @if ! which $* &>/dev/null; then $(MAKE) tools.install.$*; fi
```
这里，我们通过 $(MAKE) 调用了伪目标 tools.install.$* 。要**注意**的是，默认情况下，Makefile 在切换目录时会输出以下信息：


```makefile
$ make tools.install.codegen
===========> Installing codegen
make[1]: Entering directory `/home/colin/workspace/golang/src/github.com/marmotedu/iam'
make[1]: Leaving directory `/home/colin/workspace/golang/src/github.com/marmotedu/iam'
```
如果觉得 Entering directory 这类信息很烦人，可以通过设置 MAKEFLAGS += --no-print-directory 来禁止 Makefile 打印这些信息。



### 总结
如果你想要高效管理项目，使用 Makefile 来管理是目前的最佳实践。我们可以通过下面的几个方法，来编写一个高质量的 Makefile。首先，你需要熟练掌握 Makefile 的语法。我**建议**你重点掌握以下语法：Makefile 规则语法、伪目标、变量赋值、特殊变量、自动化变量。接着，我们需要提前规划 Makefile 要实现的功能。一个大型 Go 项目通常需要实现以下功能：代码生成类命令、格式化类命令、静态代码检查、 测试类命令、构建类命令、Docker 镜像打包类命令、部署类命令、清理类命令，等等。然后，我们还需要通过 Makefile 功能分类、文件分层、复杂命令脚本化等方式，来设计一个合理的 Makefile 结构。最后，我们还需要掌握一些 Makefile 编写技巧，例如：善用通配符、自动变量和函数；编写可扩展的 Makefile；使用带层级的命名方式，等等。通过这些技巧，可以进一步保证我们编写出一个高质量的 Makefile。

## 15 | 研发流程实战：IAM项目是如何进行研发流程管理的？

在这一讲中，我会重点介绍这两个阶段中的 Makefile 项目管理功能，并且穿插一些我的 Makefile 的设计思路。

为了向你演示流程，这里先假设一个场景。我们有一个需求：给 IAM 客户端工具 iamctl 增加一个 helloworld 命令，该命令向终端打印 hello world。接下来，我们就来看下如何具体去执行研发流程中的每一步。首先，我们进入开发阶段。

### 开发阶段
开发阶段是开发者的主战场，完全由开发者来主导，它又可分为代码开发和代码提交两个子阶段。我们先来看下代码开发阶段。



#### 代码开发
拿到需求之后，首先需要开发代码。这时，我们就需要选择一个适合团队和项目的 Git 工作流。因为 Git Flow 工作流比较适合大型的非开源项目，所以这里我们选择 Git Flow 工作流。代码开发的具体步骤如下：


第一步，基于 develop 分支，新建一个功能分支 feature/helloworld。


```
$ git checkout -b feature/helloworld develop
```

这里需要**注意**：新建的 branch 名要符合 Git Flow 工作流中的分支命名规则。否则，在 git commit 阶段，会因为 branch 不规范导致 commit 失败。IAM 项目的分支命令规则具体如下图所示：

![img](https://static001.geekbang.org/resource/image/15/79/15bb43219269273baf70a27ea94e1279.png?wh=2775x1250)

IAM 项目通过 pre-commit githooks 来确保分支名是符合规范的。在 IAM 项目根目录下执行 git commit 命令，git 会自动执行pre-commit脚本，该脚本会检查当前 branch 的名字是否符合规范。


这里还有一个地方需要你**注意**：git 不会提交 .git/hooks 目录下的 githooks 脚本，所以我们需要通过以下手段，确保开发者 clone 仓库之后，仍然能安装我们指定的 githooks 脚本到 .git/hooks 目录：


```
# Copy githook scripts when execute makefile    
COPY_GITHOOK:=$(shell cp -f githooks/* .git/hooks/) 
```
上述代码放在scripts/make-rules/common.mk文件中，每次执行 make 命令时都会执行，可以确保 githooks 都安装到 .git/hooks 目录下。

第二步，在 feature/helloworld 分支中，完成 helloworld 命令的添加。首先，通过 iamctl new helloworld 命令创建 helloworld 命令模板：


```
$ iamctl new helloworld -d internal/iamctl/cmd/helloworld
Command file generated: internal/iamctl/cmd/helloworld/helloworld.go
```
接着，编辑internal/iamctl/cmd/cmd.go文件，在源码文件中添加helloworld.NewCmdHelloworld(f, ioStreams),，加载 helloworld 命令。这里将 helloworld 命令设置为Troubleshooting and Debugging Commands命令分组：



```
import (
    "github.com/marmotedu/iam/internal/iamctl/cmd/helloworld"
)
        ...
        {
            Message: "Troubleshooting and Debugging Commands:",
            Commands: []*cobra.Command{
                validate.NewCmdValidate(f, ioStreams),
                helloworld.NewCmdHelloworld(f, ioStreams),
            },
        },
```
这些操作中包含了 low code 的思想。在第 10 讲 中我就强调过，要尽可能使用代码自动生成这一技术。这样做有两个好处：一方面能够提高我们的代码开发效率；另一方面也能够保证规范，减少手动操作可能带来的错误。所以这里，我将 iamctl 的命令也模板化，并通过 iamctl new 自动生成。

第三步，生成代码。

```
$ make gen
```

如果改动不涉及代码生成，可以不执行make gen操作。 make gen 执行的其实是 gen.run 伪目标：



```
gen.run: gen.clean gen.errcode gen.docgo.doc
```

可以看到，当执行 make gen.run 时，其实会先清理之前生成的文件，再分别自动生成 error code 和 doc.go 文件。这里需要**注意**，通过make gen 生成的存量代码要具有幂等性。只有这样，才能确保每次生成的代码是一样的，避免不一致带来的问题。


我们可以将更多的与自动生成代码相关的功能放在 gen.mk Makefile 中。例如：gen.docgo.doc，代表自动生成 doc.go 文件。gen.ca.%，代表自动生成 iamctl、iam-apiserver、iam-authz-server 证书文件。


第四步，版权检查。如果有新文件添加，我们还需要执行 make verify-copyright ，来检查新文件有没有添加版权头信息。


```
$ make verify-copyright
```

如果版权检查失败，可以执行make add-copyright自动添加版权头。添加版权信息只针对开源软件，如果你的软件不需要添加，就可以略过这一步。这里还有个 Makefile 编写技巧：**如果 Makefile 的 command 需要某个命令，就可以使该目标依赖类似 tools.verify.addlicense 这种目标，tools.verify.addlicense 会检查该工具是否已安装，如果没有就先安装。**




```
.PHONY: copyright.verify    
copyright.verify: tools.verify.addlicense 
  ...
tools.verify.%:          
  @if ! which $* &>/dev/null; then $(MAKE) tools.install.$*; fi
.PHONY: install.addlicense                              
install.addlicense:        
  @$(GO) get -u github.com/marmotedu/addlicense

```
通过这种方式，可以使 make copyright.verify 尽可能自动化，减少手动介入的概率。第五步，代码格式化。


```
$ make format
```

执行make format会依次执行以下格式化操作：调用 gofmt 格式化你的代码。调用 goimports 工具，自动增删依赖的包，并将依赖包按字母序排序并分类。调用 golines 工具，把超过 120 行的代码按 golines 规则，格式化成 <120 行的代码。调用 go mod edit -fmt 格式化 go.mod 文件。



第六步，静态代码检查。

```
$ make lint
```
关于静态代码检查，在这里你可以先了解代码开发阶段有这个步骤，至于如何操作，我会在下一讲给你详细介绍。

第七步，单元测试。


```
$ make test
```
这里要**注意**，并不是所有的包都需要执行单元测试。你可以通过如下命令，排除掉不需要单元测试的包：


```
go test `go list ./...|egrep -v $(subst $(SPACE),'|',$(sort $(EXCLUDE_TESTS)))`
```

在 go.test 的 command 中，我们还运行了以下命令：



```
sed -i '/mock_.*.go/d' $(OUTPUT_DIR)/coverage.out
```
运行该命令的目的，是把 mock_.* .go 文件中的函数单元测试信息从 coverage.out 中删除。mock_.*.go 文件中的函数是不需要单元测试的，如果不删除，就会影响后面的单元测试覆盖率的计算。



如果想检查单元测试覆盖率，请执行：


```
$ make cover
```

默认测试覆盖率至少为 60%，也可以在命令行指定覆盖率阈值为其他值，例如：


```
$ make cover COVERAGE=90
```

如果测试覆盖率不满足要求，就会返回以下错误信息：


```
test coverage is 62.1%
test coverage does not meet expectations: 90%, please add test cases!
make[1]: *** [go.test.cover] Error 1
make: *** [cover] Error 2
```

这里 make 命令的退出码为1。如果单元测试覆盖率达不到设置的阈值，就需要补充测试用例，否则禁止合并到 develop 和 master 分支。IAM 项目配置了 GitHub Actions CI 自动化流水线，CI 流水线会自动运行，检查单元测试覆盖率是否达到要求。


第八步，构建。最后，我们执行make build命令，构建出cmd/目录下所有的二进制安装文件。


```
$ make build
```
make build 会自动构建 cmd/ 目录下的所有组件，如果只想构建其中的一个或多个组件，可以传入 BINS选项，组件之间用空格隔开，并用双引号引起来：


```
$ make build BINS="iam-apiserver iamctl"
```
到这里，我们就完成了代码开发阶段的全部操作。如果你觉得手动执行的 make 命令比较多，可以直接执行 make 命令：


```
$ make
===========> Generating iam error code go source files
===========> Generating error code markdown documentation
===========> Generating missing doc.go for go packages
===========> Verifying the boilerplate headers for all files
===========> Formating codes
===========> Run golangci to lint source codes
===========> Run unit test
...
===========> Building binary iam-pump v0.7.2-24-g5814e7b for linux amd64
===========> Building binary iamctl v0.7.2-24-g5814e7b for linux amd64
...
```
直接执行make会执行伪目标all所依赖的伪目标 all: tidy gen add-copyright format lint cover build，也即执行以下操作：依赖包添加 / 删除、生成代码、自动添加版权头、代码格式化、静态代码检查、覆盖率测试、构建。


这里你需要**注意**一点：all 中依赖 cover，cover 实际执行的是 go.test.cover ，而 go.test.cover 又依赖 go.test ，所以 cover 实际上是先执行单元测试，再检查单元测试覆盖率是否满足预设的阈值。

最后补充一点，在开发阶段我们可以根据需要随时执行 make gen 、 make format 、 make lint 、 make cover 等操作，为的是能够提前发现问题并改正。

#### 代码提交
代码开发完成之后，我们就需要将代码提交到远程仓库，整个流程分为以下几个步骤。

第一步，开发完后，将代码提交到 feature/helloworld 分支，并 push 到远端仓库。



```
$ git add internal/iamctl/cmd/helloworld internal/iamctl/cmd/cmd.go
$ git commit -m "feat: add new iamctl command 'helloworld'"
$ git push origin feature/helloworld
```

这里我**建议**你只添加跟feature/helloworld相关的改动，这样就知道一个 commit 做了哪些变更，方便以后追溯。所以，我不**建议**直接执行git add .这类方式提交改动。


在提交 commit 时，commit-msg githooks 会检查 commit message 是否符合 Angular Commit Message 规范，如果不符合会报错。commit-msage 调用了go-gitlint来检查 commit message。go-gitlint 会读取 .gitlint 中配置的 commit message 格式：



```
--subject-regex=^((Merge branch.*of.*)|((revert: )?(feat|fix|perf|style|refactor|test|ci|docs|chore)(\(.+\))?: [^A-Z].*[^.]$))
--subject-maxlen=72
--body-regex=^([^\r\n]{0,72}(\r?\n|$))*$
```
IAM 项目配置了 GitHub Actions，当有代码被 push 后，会触发 CI 流水线，流水线会执行make all目标。GitHub Actions CI 流程执行记录如下图：![img](https://static001.geekbang.org/resource/image/68/22/6819f96bda8dcb214c3b7eeba2f37022.png?wh=2061x435)


如果 CI 不通过，就需要修改代码，直到 CI 流水线通过为止。这里，我们来看下 GitHub Actions 的配置：



```
name: IamCI

on: 
  push:
    branchs:
    - '*'
  pull_request:
    types: [opened, reopened]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2

    - name: Set up Go
      uses: actions/setup-go@v2
      with:
        go-version: 1.16

    - name: all
      run: make
```
可以看到，GitHub Actions 实际上执行了 3 步：拉取代码、设置 Go 编译环境、执行 make 命令（也就是执行 make all 目标）。


GitHub Actions 也执行了 make all 目标，和手动操作执行的 make all 目标保持一致，这样做是为了让线上的 CI 流程和本地的 CI 流程完全保持一致。这样，当我们在本地执行 make 命令通过后，在线上也会通过。保持一个一致的执行流程和执行结果很重要。否则，本地执行 make 通过，但是线上却不通过，岂不很让人头疼？


第二步，提交 pull request。登陆 GitHub，基于 feature/helloworld 创建 pull request，并指定 Reviewers 进行 code review。具体操作如下图：


![img](https://static001.geekbang.org/resource/image/53/ab/53f4103f5c8cabb76ef2fddaec3a54ab.png?wh=1694x733)


当有新的 pull request 被创建后，也会触发 CI 流水线。


第三步，创建完 pull request 后，就可以通知 reviewers 来 review 代码，GitHub 也会发站内信。第四步，Reviewers 对代码进行 review。


Reviewer 通过 review github diff 后的内容，并结合 CI 流程是否通过添加评论，并选择 Comment（仅评论）、Approve（通过）、Request Changes（不通过，需要修改），如下图所示：


![img](https://static001.geekbang.org/resource/image/39/ce/39d992c7bdb35848706bce792877e8ce.png?wh=2473x1001)


如果 review 不通过，feature 开发者可以直接在 feature/helloworld 分支修正代码，并 push 到远端的 feature/helloworld 分支，然后通知 reviewers 再次 review。因为有 push 事件发生，所以会触发 GitHub Actions CI 流水线。



第五步，code review 通过后，maintainer 就可以将新的代码合并到 develop 分支。使用 Create a merge commit 的方式，将 pull request 合并到 develop 分支，如下图所示：


![img](https://static001.geekbang.org/resource/image/30/7d/30de6bb6c8ff431ec56debbc0f5b667d.png?wh=1247x366)

Create a merge commit 的实际操作是 git merge --no-ff，feature/helloworld 分支上所有的 commit 都会加到 develop 分支上，并且会生成一个 merge commit。使用这种方式，可以清晰地知道是谁做了哪些提交，回溯历史的时候也会更加方便。


第六步，合并到 develop 分支后，触发 CI 流程。到这里，开发阶段的操作就全部完成了，整体流程如下：


![img](https://static001.geekbang.org/resource/image/44/73/444b0701f8866b50a49bd0138488c873.png?wh=1697x1028)

合并到 develop 分支之后，我们就可以进入开发阶段的下一阶段，也就是测试阶段了。


### 测试阶段
在测试阶段，开发人员主要负责提供测试包和修复测试期间发现的 bug，这个过程中也可能会发现一些新的需求或变动点，所以需要合理评估这些新的需求或变动点是否要放在当前迭代修改。

测试阶段的操作流程如下。第一步，基于 develop 分支，创建 release 分支，测试代码。


```
$ git checkout -b release/1.0.0 develop
$ make
```
第二步，提交测试。将 release/1.0.0 分支的代码提交给测试同学进行测试。这里假设一个测试失败的场景：我们要求打印“hello world”，但打印的是“Hello World”，需要修复。那具体应该怎么操作呢？


你可以直接在 release/1.0.0 分支修改代码，修改完成后，本地构建并提交代码：


```
$ make
$ git add internal/iamctl/cmd/helloworld/
$ git commit -m "fix: fix helloworld print bug"
$ git push origin release/1.0.0
```
push 到 release/1.0.0 后，GitHub Actions 会执行 CI 流水线。如果流水线执行成功，就将代码提供给测试；如果测试不成功，再重新修改，直到流水线执行成功。


测试同学会对 release/1.0.0 分支的代码进行充分的测试，例如功能测试、性能测试、集成测试、系统测试等。


第三步，测试通过后，将功能分支合并到 master 分支和 develop 分支。



```
$ git checkout develop
$ git merge --no-ff release/1.0.0
$ git checkout master
$ git merge --no-ff release/1.0.0
$ git tag -a v1.0.0 -m "add print hello world" # master分支打tag
```
到这里，测试阶段的操作就基本完成了。测试阶段的产物是 master/develop 分支的代码。



第四步，删除 feature/helloworld 分支，也可以选择性删除 release/1.0.0 分支。我们的代码都合并入 master/develop 分支后，feature 开发者可以选择是否要保留 feature。不过，如果没有特别的原因，我**建议**删掉，因为 feature 分支太多的话，不仅看起来很乱，还会影响性能，删除操作如下：


```
$ git branch -d feature/helloworld
```
### IAM 项目的 Makefile 项目管理技巧
在上面的内容中，我们以研发流程为主线，亲身体验了 IAM 项目的 Makefile 项目管理功能。这些是你最应该掌握的核心功能，但 IAM 项目的 Makefile 还有很多功能和设计技巧。接下来，我会给你分享一些很有价值的 Makefile 项目管理技巧。


#### help 自动解析
因为随着项目的扩展，Makefile 大概率会不断加入新的管理功能，这些管理功能也需要加入到 make help 输出中。但如果每添加一个目标，都要修改 make help 命令，就比较麻烦，还容易出错。所以这里，我通过自动解析的方式，来生成make help输出：



```
## help: Show this help info.    
.PHONY: help           
help: Makefile               
  @echo -e "\nUsage: make <TARGETS> <OPTIONS> ...\n\nTargets:"                         
  @sed -n 's/^##//p' $< | column -t -s ':' | sed -e 's/^/ /'    
  @echo "$$USAGE_OPTIONS"    
```
目标 help 的命令中，通过 sed -n 's/^##//p' $< | column -t -s ':' | sed -e 's/^/ /' 命令，自动解析 Makefile 中 ## 开头的注释行，从而自动生成 make help 输出。


#### Options 中指定变量值
通过以下赋值方式，变量可以在 Makefile options 中被指定：


```
ifeq ($(origin COVERAGE),undefined)    
COVERAGE := 60    
endif   
```

例如，如果我们执行make ，则 COVERAGE 设置为默认值 60；如果我们执行make COVERAGE=90 ，则 COVERAGE 值为 90。通过这种方式，我们可以更灵活地控制 Makefile 的行为。


#### 自动生成 CHANGELOG
一个项目最好有 CHANGELOG 用来展示每个版本之间的变更内容，作为 Release Note 的一部分。但是，如果每次都要手动编写 CHANGELOG，会很麻烦，也不容易坚持，所以这里我们可以借助git-chglog工具来自动生成。

IAM 项目的 git-chglog 工具的配置文件放在.chglog目录下，在学习 git-chglog 工具时，你可以参考下。


#### 自动生成版本号
一个项目也需要有一个版本号，当前用得比较多的是语义化版本号规范。但如果靠开发者手动打版本号，工作效率低不说，经常还会出现漏打、打的版本号不规范等问题。所以最好的办法是，版本号也通过工具自动生成。在 IAM 项目中，采用了gsemver工具来自动生成版本号。


整个 IAM 项目的版本号，都是通过scripts/ensure_tag.sh脚本来生成的：


```
version=v`gsemver bump`
if [ -z "`git tag -l $version`" ];then
  git tag -a -m "release version $version" $version
fi
```
在 scripts/ensure_tag.sh 脚本中，通过 gsemver bump 命令来自动化生成语义化的版本号，并执行 git tag -a 给仓库打上版本号标签，gsemver 命令会根据 Commit Message 自动生成版本号。

之后，Makefile 和 Shell 脚本用到的所有版本号均统一使用scripts/make-rules/common.mk文件中的 VERSION 变量：



```
VERSION := $(shell git describe --tags --always --match='v*')
```
上述的 Shell 命令通过 git describe 来获取离当前提交最近的 tag（版本号）。


在执行 git describe 时，如果符合条件的 tag 指向最新提交，则只显示 tag 的名字，否则会有相关的后缀，来描述该 tag 之后有多少次提交，以及最新的提交 commit id。例如：


```
$ git describe --tags --always --match='v*'
v1.0.0-3-g1909e47
```
这里解释下版本号中各字符的含义：3：表示自打 tag v1.0.0 以来有 3 次提交。g1909e47：g 为 git 的缩写，在多种管理工具并存的环境中很有用处。1909e47：7 位字符表示为最新提交的 commit id 前 7 位。


最后解释下参数：–tags，使用所有的标签，而不是只使用带注释的标签（annotated tag）。git tag 生成一个 unannotated tag，git tag -a -m '' 生成一个 annotated tag。–always，如果仓库没有可用的标签，那么使用 commit 缩写来替代标签。–match，只考虑与给定模式相匹配的标签。



#### 保持行为一致
上面我们介绍了一些管理功能，例如检查 Commit Message 是否符合规范、自动生成 CHANGELOG、自动生成版本号。这些可以通过 Makefile 来操作，我们也可以手动执行。例如，通过以下命令，检查 IAM 的所有 Commit 是否符合 Angular Commit Message 规范：



```
$ go-gitlint
b62db1f: subject does not match regex [^(revert: )?(feat|fix|perf|style|refactor|test|ci|docs|chore)(\(.+\))?: [^A-Z].*[^.]$]
```
也可以通过以下命令，手动来生成 CHANGELOG：


```
$ git-chglog v1.0.0 CHANGELOG/CHANGELOG-1.0.0.md
```
还可以执行 gsemver 来生成版本号：


```
$ gsemver bump
1.0.1
```
这里要强调的是，我们要保证不管使用手动操作，还是通过 Makefile 操作，都要确保 git commit message 规范检查结果、生成的 CHANGELOG、生成的版本号是一致的。这需要我们采用同一种操作方式。


### 总结
在整个研发流程中，需要开发人员深度参与的阶段有两个，分别是开发阶段和测试阶段。在开发阶段，开发者完成代码开发之后，通常需要执行生成代码、版权检查、代码格式化、静态代码检查、单元测试、构建等操作。我们可以将这些操作集成在 Makefile 中，来提高效率，并借此统一操作。


另外，IAM 项目在编写 Makefile 时也采用了一些技巧，例如make help 命令中，help 信息是通过解析 Makefile 文件的注释来完成的；可以通过 git-chglog 自动生成 CHANGELOG；通过 gsemver 自动生成语义化的版本号等。

## 16 | 代码检查：如何进行静态代码检查？
在做 Go 项目开发的过程中，我们肯定需要对 Go 代码做静态代码检查。虽然 Go 命令提供了 go vet 和 go tool vet，但是它们检查的内容还不够全面，我们需要一种更加强大的静态代码检查工具。


其实，Go 生态中有很多这样的工具，也不乏一些比较优秀的。今天我想给你介绍的 golangci-lint，是目前使用最多，也最受欢迎的静态代码检查工具，我们的 IAM 实战项目也用到了它。


接下来，我就从 golangci-lint 的优点、golangci-lint 提供的命令和选项、golangci-lint 的配置这三个方面来向你介绍下它。在你了解这些基础知识后，我会带着你使用 golangci-lint 进行静态代码检查，让你熟悉操作，在这个基础上，再把我使用 golangci-lint 时总结的一些经验技巧分享给你。


### 为什么选择 golangci-lint 做静态代码检查？
选择 golangci-lint，是因为它具有其他静态代码检查工具不具备的一些优点。在我看来，它的核心优点至少有这些：


- 速度非常快：golangci-lint 是基于 gometalinter 开发的，但是平均速度要比 gometalinter 快 5 倍。golangci-lint 速度快的原因有三个：可以并行检查代码；可以复用 go build 缓存；会缓存分析结果。
- 可配置：支持 YAML 格式的配置文件，让检查更灵活，更可控。
- IDE 集成：可以集成进多个主流的 IDE，例如 VS Code、GNU Emacs、Sublime Text、Goland 等。
- linter 聚合器：1.41.1 版本的 golangci-lint 集成了 76 个 linter，不需要再单独安装这 76 个 linter。并且 golangci-lint 还支持自定义 linter。
- 最小的误报数：golangci-lint 调整了所集成 linter 的默认设置，大幅度减少了误报。
- 良好的输出：输出的结果带有颜色、代码行号和 linter 标识，易于查看和定位。


下图是一个 golangci-lint 的检查结果：


![img](https://static001.geekbang.org/resource/image/ed/d4/ed9c1d775e31a8a5b60a5a6882d0bed4.png?wh=2527x563)

你可以看到，输出的检查结果中包括如下信息：检查出问题的源码文件、行号和错误行内容。出问题的原因，也就是打印出不符合检查规则的原因。报错的 linter。


通过查看 golangci-lint 的输出结果，可以准确地定位到报错的位置，快速弄明白报错的原因，方便开发者修复。


除了上述优点之外，在我看来 golangci-lint 还有一个非常大的优点：当前更新迭代速度很快，不断有新的 linter 被集成到 golangci-lint 中。有这么全的 linter 为你的代码保驾护航，你在交付代码时肯定会更有自信。


目前，有很多公司 / 项目使用了 golangci-lint 工具作为静态代码检查工具，例如 Google、Facebook、Istio、Red Hat OpenShift 等。


### golangci-lint 提供了哪些命令和选项？
在使用之前，首先需要安装 golangci-lint。golangci-lint 的安装方法也很简单，你只需要执行以下命令，就可以安装了。



```
$ go get github.com/golangci/golangci-lint/cmd/golangci-lint@v1.41.1
$ golangci-lint version # 输出 golangci-lint 版本号，说明安装成功
golangci-lint has version v1.39.0 built from (unknown, mod sum: "h1:aAUjdBxARwkGLd5PU0vKuym281f2rFOyqh3GB4nXcq8=") on (unknown)
```
这里**注意**，为了避免安装失败，强烈**建议**你安装 golangci-lint releases page 中的指定版本，例如 v1.41.1。另外，还**建议**你定期更新 golangci-lint 的版本，因为该项目正在被积极开发并不断改进。安装之后，就可以使用了。我们可以通过执行 golangci-lint -h 查看其用法，golangci-lint 支持的子命令见下表：


![img](https://static001.geekbang.org/resource/image/34/42/34617a68604b7b5613948f89230c7a42.png?wh=1376x1015)

此外，golangci-lint 还支持一些全局选项。全局选项是指适用于所有子命令的选项，golangci-lint 支持的全局选项如下：


![img](https://static001.geekbang.org/resource/image/79/88/79e517e6a7fd3882ce30320f28aa7088.png?wh=1339x1063)

接下来，我就详细介绍下 golangci-lint 支持的核心子命令：run、cache、completion、config、linters。


#### run 命令
run 命令执行 golangci-lint，对代码进行检查，是 golangci-lint 最为核心的一个命令。run 没有子命令，但有很多选项。run 命令的具体使用方法，我会在讲解如何执行静态代码检查的时候详细介绍。


#### cache 命令
cache 命令用来进行缓存控制，并打印缓存的信息。它包含两个子命令：clean 用来清除 cache，当我们觉得 cache 的内容异常，或者 cache 占用空间过大时，可以通过golangci-lint cache clean清除 cache。status 用来打印 cache 的状态，比如 cache 的存放目录和 cache 的大小，例如：


```
$ golangci-lint cache status
Dir: /home/colin/.cache/golangci-lint
Size: 773.4KiB
```

#### completion 命令
completion 命令包含 4 个子命令 bash、fish、powershell 和 zsh，分别用来输出 bash、fish、powershell 和 zsh 的自动补全脚本。


下面是一个配置 bash 自动补全的示例：



```
$ golangci-lint completion bash > ~/.golangci-lint.bash
$ echo "source '$HOME/.golangci-lint.bash'" >> ~/.bashrc
$ source ~/.bashrc
```
执行完上面的命令，键入如下命令，即可自动补全子命令：


```
$ golangci-lint comp<TAB>
```
上面的命令行会自动补全为golangci-lint completion 。


#### config 命令
config 命令可以打印 golangci-lint 当前使用的配置文件路径，例如：


```
$ golangci-lint config path
.golangci.yaml
```
#### linters 命令
linters 命令可以打印出 golangci-lint 所支持的 linter，并将这些 linter 分成两类，分别是配置为启用的 linter 和配置为禁用的 linter，例如：


```
$ golangci-lint linters
Enabled by your configuration linters:
...
deadcode: Finds unused code [fast: true, auto-fix: false]
...
Disabled by your configuration linters:
exportloopref: checks for pointers to enclosing loop variables [fast: true, auto-fix: false]
...
```
上面我介绍了 golangci-lint 提供的命令，接下来，我们再来看下 golangci-lint 的配置。


#### golangci-lint 配置
和其他 linter 相比，golangci-lint 一个非常大的优点是使用起来非常灵活，这要得益于它对自定义配置的支持。


golangci-lint 支持两种配置方式，分别是命令行选项和配置文件。如果 bool/string/int 的选项同时在命令行选项和配置文件中被指定，命令行的选项就会覆盖配置文件中的选项。如果是 slice 类型的选项，则命令行和配置中的配置会进行合并。golangci-lint run 支持很多命令行选项，可通过golangci-lint run -h查看，这里选择一些比较重要的选项进行介绍，见下表：


![img](https://static001.geekbang.org/resource/image/ac/fa/ac6098cf64cde7b8326cfd3508b04dfa.jpg?wh=2284x3988)

此外，我们还可以通过 golangci-lint配置文件进行配置，默认的配置文件名为.golangci.yaml、.golangci.toml、.golangci.json，可以通过-c选项指定配置文件名。通过配置文件，可以实现下面几类功能：


- golangci-lint 本身的一些选项，比如超时、并发，是否检查*_test.go文件等。
- 配置需要忽略的文件和文件夹。
- 配置启用哪些 linter，禁用哪些 linter。
- 配置输出格式。
- golangci-lint 支持很多 linter，其中有些 linter 支持一些配置项，这些配置项可以在配置文件中配置。
- 配置符合指定正则规则的文件可以忽略的 linter。
- 设置错误严重级别，像日志一样，检查错误也是有严重级别的。


更详细的配置内容，你可以参考Configuration。另外，你也可以参考 IAM 项目的 golangci-lint 配置.golangci.yaml。.golangci.yaml 里面的一些配置，我**建议**你一定要设置，具体如下：


```yaml
run:
  skip-dirs: # 设置要忽略的目录
    - util
    - .*~
    - api/swagger/docs
  skip-files: # 设置不需要检查的go源码文件，支持正则匹配，这里**建议**包括：_test.go
    - ".*\\.my\\.go$"
    - _test.go
linters-settings:
  errcheck:
    check-type-assertions: true # 这里**建议**设置为true，如果确实不需要检查，可以写成`num, _ := strconv.Atoi(numStr)`
    check-blank: false
  gci:
    # 将以`github.com/marmotedu/iam`开头的包放在第三方包后面
    local-prefixes: github.com/marmotedu/iam
  godox:
    keywords: # **建议**设置为BUG、FIXME、OPTIMIZE、HACK
      - BUG
      - FIXME
      - OPTIMIZE
      - HACK
  goimports:
    # 设置哪些包放在第三方包后面，可以设置多个包，逗号隔开
    local-prefixes: github.com/marmotedu/iam
  gomoddirectives: # 设置允许在go.mod中replace的包
    replace-local: true
    replace-allow-list:
      - github.com/coreos/etcd
      - google.golang.org/grpc
      - github.com/marmotedu/api
      - github.com/marmotedu/component-base
      - github.com/marmotedu/marmotedu-sdk-go
  gomodguard: # 下面是根据需要选择可以使用的包和版本，**建议**设置
    allowed:
      modules:
        - gorm.io/gorm
        - gorm.io/driver/mysql
        - k8s.io/klog
      domains: # List of allowed module domains
        - google.golang.org
        - gopkg.in
        - golang.org
        - github.com
        - go.uber.org
    blocked:
      modules:
        - github.com/pkg/errors:
            recommendations:
              - github.com/marmotedu/errors
            reason: "`github.com/marmotedu/errors` is the log package used by marmotedu projects."
      versions:
        - github.com/MakeNowJust/heredoc:
            version: "> 2.0.9"
            reason: "use the latest version"
      local_replace_directives: false
  lll:
    line-length: 240 # 这里可以设置为240，240一般是够用的
  importas: # 设置包的alias，根据需要设置
    jwt: github.com/appleboy/gin-jwt/v2         
    metav1: github.com/marmotedu/component-base/pkg/meta/v1
```

需要**注意**的是，golangci-lint 不**建议**使用 enable-all: true 选项，为了尽可能使用最全的 linters，我们可以使用以下配置：



```yaml
linters: 
  disable-all: true  
  enable: # enable下列出 <期望的所有linters>
    - typecheck
    - ... 

```

<期望的所有linters> = - <不期望执行的linters>，我们可以通过执行以下命令来获取：



```
$ ./scripts/print_enable_linters.sh
    - asciicheck
    - bodyclose
    - cyclop
    - deadcode
    - ...
```
将以上输出结果替换掉.golangci.yaml 配置文件中的 linters.enable 部分即可。


上面我们介绍了与 golangci-lint 相关的一些基础知识，接下来我就给你详细展示下，如何使用 golangci-lint 进行静态代码检查。


### 如何使用 golangci-lint 进行静态代码检查？
要对代码进行静态检查，只需要执行 golangci-lint run 命令即可。接下来，我会先给你介绍 5 种常见的 golangci-lint 使用方法。


对当前目录及子目录下的所有 Go 文件进行静态代码检查：


```
$ golangci-lint run
```
命令等效于golangci-lint run ./...。


对指定的 Go 文件或者指定目录下的 Go 文件进行静态代码检查：

```
$ golangci-lint run dir1 dir2/... dir3/file1.go
```
这里需要你**注意**：上述命令不会检查 dir1 下子目录的 Go 文件，如果想递归地检查一个目录，需要在目录后面追加/...，例如：dir2/...。

根据指定配置文件，进行静态代码检查：


```
$ golangci-lint run -c .golangci.yaml ./...
```
运行指定的 linter：
golangci-lint 可以在不指定任何配置文件的情况下运行，这会运行默认启用的 linter，你可以通过golangci-lint help linters查看它。


你可以传入参数-E/--enable来使某个 linter 可用，也可以使用-D/--disable参数来使某个 linter 不可用。下面的示例仅仅启用了 errcheck linter：


```
$ golangci-lint run --no-config --disable-all -E errcheck ./...
```
这里你需要**注意**，默认情况下，golangci-lint 会从当前目录一层层往上寻找配置文件名.golangci.yaml、.golangci.toml、.golangci.json直到根（/）目录。如果找到，就以找到的配置文件作为本次运行的配置文件，所以为了防止读取到未知的配置文件，可以用 --no-config 参数使 golangci-lint 不读取任何配置文件。


禁止运行指定的 liner：如果我们想禁用某些 linter，可以使用-D选项。


```
$ golangci-lint run --no-config -D godot,errcheck
```
在使用 golangci-lint 进行代码检查时，可能会有很多误报。所谓的误报，其实是我们希望 golangci-lint 的一些 linter 能够容忍某些 issue。那么如何尽可能减少误报呢？golangci-lint 也提供了一些途径，我**建议**你使用下面这三种：

在命令行中添加-e参数，或者在配置文件的issues.exclude部分设置要排除的检查错误。你也可以使用issues.exclude-rules来配置哪些文件忽略哪些 linter。通过run.skip-dirs、run.skip-files或者issues.exclude-rules配置项，来忽略指定目录下的所有 Go 文件，或者指定的 Go 文件。通过在 Go 源码文件中添加//nolint注释，来忽略指定的代码行。

因为 golangci-lint 设置了很多 linters，对于一个大型项目，启用所有的 linter 会检查出很多问题，并且每个项目对 linter 检查的粒度要求也不一样，所以 glangci-lint使用 nolint 标记来开关某些检查项，不同位置的 nolint 标记效果也会不一样。接下来我想向你介绍 nolint 的几种用法。

忽略某一行所有 linter 的检查


```
var bad_name int //nolint
```
忽略某一行指定 linter 的检查，可以指定多个 linter，用逗号 , 隔开。


```
var bad_name int //nolint:golint,unused
```

忽略某个代码块的检查。


```
//nolint
func allIssuesInThisFunctionAreExcluded() *string {
  // ...
}

//nolint:govet
var (
  a int
  b int
)
```
忽略某个文件的指定 linter 检查。在 package xx 上面一行添加//nolint注释。


```
//nolint:unparam
package pkg
...
```
在使用 nolint 的过程中，有 3 个地方需要你**注意**。首先，如果启用了 nolintlint，你就需要在//nolint后面添加 nolint 的原因// xxxx。其次，你使用的应该是//nolint而不是// nolint。因为根据 Go 的规范，需要程序读取的注释 // 后面不应该有空格。最后，如果要忽略所有 linter，可以用//nolint；如果要忽略某个指定的 linter，可以用//nolint:,。



#### golangci-lint 使用技巧
我在使用 golangci-lint 时，总结了一些经验技巧，放在这里供你参考，希望能够帮助你更好地使用 golangci-lint。


技巧 1：第一次修改，可以按目录修改。如果你第一次使用 golangci-lint 检查你的代码，一定会有很多错误。为了减轻修改的压力，可以按目录检查代码并修改。这样可以有效减少失败条数，减轻修改压力。当然，如果错误太多，一时半会儿改不完，想以后慢慢修改或者干脆不修复存量的 issues，那么你可以使用 golangci-lint 的 --new-from-rev 选项，只检查新增的 code，例如：


```
$ golangci-lint run --new-from-rev=HEAD~1
```
技巧 2：按文件修改，减少文件切换次数，提高修改效率。如果有很多检查错误，涉及很多文件，**建议**先修改一个文件，这样就不用来回切换文件。可以通过 grep 过滤出某个文件的检查失败项，例如：


```
$ golangci-lint run ./...|grep pkg/storage/redis_cluster.go
pkg/storage/redis_cluster.go:16:2: "github.com/go-redis/redis/v7" imported but not used (typecheck)
pkg/storage/redis_cluster.go:82:28: undeclared name: `redis` (typecheck)
pkg/storage/redis_cluster.go:86:14: undeclared name: `redis` (typecheck)
...
```

技巧 3：把 linters-setting.lll.line-length 设置得大一些。在 Go 项目开发中，为了易于阅读代码，通常会将变量名 / 函数 / 常量等命名得有意义，这样很可能导致每行的代码长度过长，很容易超过lll linter 设置的默认最大长度 80。这里**建议**将linters-setting.lll.line-length设置为 120/240。



技巧 4：尽可能多地使用 golangci-lint 提供的 linter。golangci-lint 集成了很多 linters，可以通过如下命令查看：


```
$ golangci-lint linters
Enabled by your configuration linters:
deadcode: Finds unused code [fast: true, auto-fix: false]
...
varcheck: Finds unused global variables and constants [fast: true, auto-fix: false]

Disabled by your configuration linters:
asciicheck: Simple linter to check that your code does not contain non-ASCII identifiers [fast: true, auto-fix: false]
...
wsl: Whitespace Linter - Forces you to use empty lines! [fast: true, auto-fix: false]
```
这些 linter 分为两类，一类是默认启用的，另一类是默认禁用的。每个 linter 都有两个属性：
- fast：true/false，如果为 true，说明该 linter 可以缓存类型信息，支持快速检查。因为第一次缓存了这些信息，所以后续的运行会非常快。
- auto-fix：true/false，如果为 true 说明该 linter 支持自动修复发现的错误；如果为 false 说明不支持自动修复。


如果配置了 golangci-lint 配置文件，则可以通过命令golangci-lint help linters查看在当前配置下启用和禁用了哪些 linter。golangci-lint 也支持自定义 linter 插件，具体你可以参考：New linters。


在使用 golangci-lint 的时候，我们要尽可能多的使用 linter。使用的 linter 越多，说明检查越严格，意味着代码越规范，质量越高。如果时间和精力允许，**建议**打开 golangci-lint 提供的所有 linter。


技巧 5：每次修改代码后，都要执行 golangci-lint。每次修改完代码后都要执行 golangci-lint，一方面可以及时修改不规范的地方，另一方面可以减少错误堆积，减轻后面的修改压力。

技巧 6：**建议**在根目录下放一个通用的 golangci-lint 配置文件。在/目录下存放通用的 golangci-lint 配置文件，可以让你不用为每一个项目都配置 golangci-lint。当你需要为某个项目单独配置 golangci-lint 时，只需在该项目根目录下增加一个项目级别的 golangci-lint 配置文件即可。



### 总结
Go 项目开发中，对代码进行静态代码检查是必要的操作。当前有很多优秀的静态代码检查工具，但 golangci-lint 因为具有检查速度快、可配置、少误报、内置了大量 linter 等优点，成为了目前最受欢迎的静态代码检查工具。


golangci-lint 功能非常强大，支持诸如 run、cache、completion、linters 等命令。其中最常用的是 run 命令，run 命令可以通过以下方式来进行静态代码检查：


```
$ golangci-lint run #  对当前目录及子目录下的所有Go文件进行静态代码检查
$ golangci-lint run dir1 dir2/... dir3/file1.go # 对指定的Go文件或者指定目录下的Go文件进行静态代码检查
$ golangci-lint run -c .golangci.yaml ./... # 根据指定配置文件，进行静态代码检查
$ golangci-lint run --no-config --disable-all -E errcheck ./... # 运行指定的 errcheck linter
$ golangci-lint run --no-config -D godot,errcheck # 禁止运行指定的godot,errcheck liner
```
此外，golangci-lint 还支持 //nolint 、//nolint:golint,unused 等方式来减少误报。最后，我分享了一些自己使用 golangci-lint 时总结的经验。例如：第一次修改，可以按目录修改；按文件修改，减少文件切换次数，提高修改效率；尽可能多地使用 golangci-lint 提供的 linter。希望这些**建议**对你使用 golangci-lint 有一定帮助。

## 17 | API 文档：如何生成 Swagger API 文档 ？
作为一名开发者，我们通常讨厌编写文档，因为这是一件重复和缺乏乐趣的事情。但是在开发过程中，又有一些文档是我们必须要编写的，比如 API 文档。

一个企业级的 Go 后端项目，通常也会有个配套的前端。为了加快研发进度，通常是后端和前端并行开发，这就需要后端开发者在开发后端代码之前，先设计好 API 接口，提供给前端。所以在设计阶段，我们就需要生成 API 接口文档。



一个好的 API 文档，可以减少用户上手的复杂度，也意味着更容易留住用户。好的 API 文档也可以减少沟通成本，帮助开发者更好地理解 API 的调用方式，从而节省时间，提高开发效率。这时候，我们一定希望有一个工具能够帮我们自动生成 API 文档，解放我们的双手。Swagger 就是这么一个工具，可以帮助我们生成易于共享且具有足够描述性的 API 文档。


接下来，我们就来看下，如何使用 Swagger 生成 API 文档。


### Swagger 介绍
Swagger 是一套围绕 OpenAPI 规范构建的开源工具，可以设计、构建、编写和使用 REST API。Swagger 包含很多工具，其中主要的 Swagger 工具包括：

- Swagger 编辑器：基于浏览器的编辑器，可以在其中编写 OpenAPI 规范，并实时预览 API 文档。https://editor.swagger.io 就是一个 Swagger 编辑器，你可以尝试在其中编辑和预览 API 文档。
- Swagger UI：将 OpenAPI 规范呈现为交互式 API 文档，并可以在浏览器中尝试 API 调用。
- Swagger Codegen：根据 OpenAPI 规范，生成服务器存根和客户端代码库，目前已涵盖了 40 多种语言。


### Swagger 和 OpenAPI 的区别
我们在谈到 Swagger 时，也经常会谈到 OpenAPI。那么二者有什么区别呢？


OpenAPI 是一个 API 规范，它的前身叫 Swagger 规范，通过定义一种用来描述 API 格式或 API 定义的语言，来规范 RESTful 服务开发过程，目前最新的 OpenAPI 规范是OpenAPI 3.0（也就是 Swagger 2.0 规范）。

OpenAPI 规范规定了一个 API 必须包含的基本信息，这些信息包括：对 API 的描述，介绍 API 可以实现的功能。每个 API 上可用的路径（/users）和操作（GET /users，POST /users）。每个 API 的输入 / 返回的参数。验证方法。联系信息、许可证、使用条款和其他信息。


所以，你可以简单地这么理解：OpenAPI 是一个 API 规范，Swagger 则是实现规范的工具。另外，要编写 Swagger 文档，首先要会使用 Swagger 文档编写语法，因为语法比较多，这里就不多介绍了，你可以参考 Swagger 官方提供的[OpenAPI Specification](https://swagger.io/specification/)来学习。


### 用 go-swagger 来生成 Swagger API 文档
在 Go 项目开发中，我们可以通过下面两种方法来生成 Swagger API 文档：


第一，如果你熟悉 Swagger 语法的话，可以直接编写 JSON/YAML 格式的 Swagger 文档。**建议**选择 YAML 格式，因为它比 JSON 格式更简洁直观。第二，通过工具生成 Swagger 文档，目前可以通过swag和go-swagger两个工具来生成。


对比这两种方法，直接编写 Swagger 文档，不比编写 Markdown 格式的 API 文档工作量小，我觉得不符合程序员“偷懒”的习惯。所以，本专栏我们就使用 go-swagger 工具，基于代码注释来自动生成 Swagger 文档。为什么选 go-swagger 呢？有这么几个原因：


go-swagger 比 swag 功能更强大：go-swagger 提供了更灵活、更多的功能来描述我们的 API。使我们的代码更易读：如果使用 swag，我们每一个 API 都需要有一个冗长的注释，有时候代码注释比代码还要长，但是通过 go-swagger 我们可以将代码和注释分开编写，一方面可以使我们的代码保持简洁，清晰易读，另一方面我们可以在另外一个包中，统一管理这些 Swagger API 文档定义。更好的社区支持：go-swagger 目前有非常多的 Github star 数，出现 Bug 的概率很小，并且处在一个频繁更新的活跃状态。


你已经知道了，go-swagger 是一个功能强大的、高性能的、可以根据代码注释生成 Swagger API 文档的工具。除此之外，go-swagger 还有很多其他特性：


根据 Swagger 定义文件生成服务端代码。根据 Swagger 定义文件生成客户端代码。校验 Swagger 定义文件是否正确。启动一个 HTTP 服务器，使我们可以通过浏览器访问 API 文档。根据 Swagger 文档定义的参数生成 Go model 结构体定义。

可以看到，使用 go-swagger 生成 Swagger 文档，可以帮助我们减少编写文档的时间，提高开发效率，并能保证文档的及时性和准确性。这里需要**注意**，如果我们要对外提供 API 的 Go SDK，可以考虑使用 go-swagger 来生成客户端代码。但是我觉得 go-swagger 生成的服务端代码不够优雅，所以**建议**你自行编写服务端代码。

目前，有很多知名公司和组织的项目都使用了 go-swagger，例如 Moby、CoreOS、Kubernetes、Cilium 等。


#### 安装 Swagger 工具
go-swagger 通过 swagger 命令行工具来完成其功能，swagger 安装方法如下：


```
$ go get -u github.com/go-swagger/go-swagger/cmd/swagger
$ swagger version
dev
```

#### swagger 命令行工具介绍
swagger 命令格式为swagger [OPTIONS] 。可以通过swagger -h查看 swagger 使用帮助。swagger 提供的子命令及功能见下表：


![img](https://static001.geekbang.org/resource/image/yy/78/yy3428aa968c7029cb4f6b11f2596678.png?wh=1176x1180)

#### 如何使用 swagger 命令生成 Swagger 文档？
go-swagger 通过解析源码中的注释来生成 Swagger 文档，go-swagger 的详细注释语法可参考官方文档。常用的有如下几类注释语法：


![img](https://static001.geekbang.org/resource/image/94/b3/947262c5175f6f518ff677063af293b3.png?wh=1087x1134)


#### 解析注释生成 Swagger 文档
swagger generate 命令会找到 main 函数，然后遍历所有源码文件，解析源码中与 Swagger 相关的注释，然后自动生成 swagger.json/swagger.yaml 文件。这一过程的示例代码为gopractise-demo/swagger。目录下有一个 main.go 文件，定义了如下 API 接口：


```go
package main

import (
    "fmt"
    "log"
    "net/http"

    "github.com/gin-gonic/gin"

    "github.com/marmotedu/gopractise-demo/swagger/api"
    // This line is necessary for go-swagger to find your docs!
    _ "github.com/marmotedu/gopractise-demo/swagger/docs"
)

var users []*api.User

func main() {
    r := gin.Default()
    r.POST("/users", Create)
    r.GET("/users/:name", Get)

    log.Fatal(r.Run(":5555"))
}

// Create create a user in memory.
func Create(c *gin.Context) {
    var user api.User
    if err := c.ShouldBindJSON(&user); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"message": err.Error(), "code": 10001})
        return
    }

    for _, u := range users {
        if u.Name == user.Name {
            c.JSON(http.StatusBadRequest, gin.H{"message": fmt.Sprintf("user %s already exist", user.Name), "code": 10001})
            return
        }
    }

    users = append(users, &user)
    c.JSON(http.StatusOK, user)
}

// Get return the detail information for a user.
func Get(c *gin.Context) {
    username := c.Param("name")
    for _, u := range users {
        if u.Name == username {
            c.JSON(http.StatusOK, u)
            return
        }
    }

    c.JSON(http.StatusBadRequest, gin.H{"message": fmt.Sprintf("user %s not exist", username), "code": 10002})
}
```

main 包中引入的 User struct 位于 gopractise-demo/swagger/api 目录下的user.go文件：


```go
// Package api defines the user model.
package api

// User represents body of User request and response.
type User struct {
    // User's name.
    // Required: true
    Name string `json:"name"`

    // User's nickname.
    // Required: true
    Nickname string `json:"nickname"`

    // User's address.
    Address string `json:"address"`

    // User's email.
    Email string `json:"email"`
}
```
// Required: true说明字段是必须的，生成 Swagger 文档时，也会在文档中声明该字段是必须字段。为了使代码保持简洁，我们在另外一个 Go 包中编写带 go-swagger 注释的 API 文档。假设该 Go 包名字为 docs，在开始编写 Go API 注释之前，需要在 main.go 文件中导入 docs 包：


```
_ "github.com/marmotedu/gopractise-demo/swagger/docs"

```
通过导入 docs 包，可以使 go-swagger 在递归解析 main 包的依赖包时，找到 docs 包，并解析包中的注释。在 gopractise-demo/swagger 目录下，创建 docs 文件夹：


```
$ mkdir docs
$ cd docs
```
在 docs 目录下，创建一个 doc.go 文件，在该文件中提供 API 接口的基本信息：


```go
// Package docs awesome.
//
// Documentation of our awesome API.
//
//     Schemes: http, https
//     BasePath: /
//     Version: 0.1.0
//     Host: some-url.com
//
//     Consumes:
//     - application/json
//
//     Produces:
//     - application/json
//
//     Security:
//     - basic
//
//    SecurityDefinitions:
//    basic:
//      type: basic
//
// swagger:meta
package docs
```

Package docs 后面的字符串 awesome 代表我们的 HTTP 服务名。Documentation of our awesome API是我们 API 的描述。其他都是 go-swagger 可识别的注释，代表一定的意义。最后以swagger:meta注释结束。


编写完 doc.go 文件后，进入 gopractise-demo/swagger 目录，执行如下命令，生成 Swagger API 文档，并启动 HTTP 服务，在浏览器查看 Swagger：



```
$ swagger generate spec -o swagger.yaml
$ swagger serve --no-open -F=swagger --port 36666 swagger.yaml

2020/10/20 23:16:47 serving docs at http://localhost:36666/docs
```
- -o：指定要输出的文件名。swagger 会根据文件名后缀.yaml 或者.json，决定生成的文件格式为 YAML 或 JSON。
- –no-open：因为是在 Linux 服务器下执行命令，没有安装浏览器，所以使–no-open 禁止调用浏览器打开 URL。
- -F：指定文档的风格，可选 swagger 和 redoc。我选用了 redoc，因为觉得 redoc 格式更加易读和清晰。
- –port：指定启动的 HTTP 服务监听端口。


打开浏览器，访问http://localhost:36666/docs ，如下图所示：


![img](https://static001.geekbang.org/resource/image/9a/2c/9a9fb7a31d418d8e4dc13b19cefa832c.png?wh=2524x699)

如果我们想要 JSON 格式的 Swagger 文档，可执行如下命令，将生成的 swagger.yaml 转换为 swagger.json：

```
$ swagger generate spec -i ./swagger.yaml -o ./swagger.json
```

接下来，我们就可以编写 API 接口的定义文件（位于[gopractise-demo/swagger/docs/user.go](https://github.com/marmotedu/gopractise-demo/blob/main/swagger/docs/user.go)文件中）：


```go
package docs

import (
    "github.com/marmotedu/gopractise-demo/swagger/api"
)

// swagger:route POST /users user createUserRequest
// Create a user in memory.
// responses:
//   200: createUserResponse
//   default: errResponse

// swagger:route GET /users/{name} user getUserRequest
// Get a user from memory.
// responses:
//   200: getUserResponse
//   default: errResponse

// swagger:parameters createUserRequest
type userParamsWrapper struct {
    // This text will appear as description of your request body.
    // in:body
    Body api.User
}

// This text will appear as description of your request url path.
// swagger:parameters getUserRequest
type getUserParamsWrapper struct {
    // in:path
    Name string `json:"name"`
}

// This text will appear as description of your response body.
// swagger:response createUserResponse
type createUserResponseWrapper struct {
    // in:body
    Body api.User
}

// This text will appear as description of your response body.
// swagger:response getUserResponse
type getUserResponseWrapper struct {
    // in:body
    Body api.User
}

// This text will appear as description of your error response body.
// swagger:response errResponse
type errResponseWrapper struct {
    // Error code.
    Code int `json:"code"`

    // Error message.
    Message string `json:"message"`
}
```
user.go 文件说明：

- swagger:route：swagger:route代表 API 接口描述的开始，后面的字符串格式为HTTP方法 URL Tag ID。可以填写多个 tag，相同 tag 的 API 接口在 Swagger 文档中会被分为一组。ID 是一个标识符，swagger:parameters是具有相同 ID 的swagger:route的请求参数。swagger:route下面的一行是该 API 接口的描述，需要以英文点号为结尾。responses:定义了 API 接口的返回参数，例如当 HTTP 状态码是 200 时，返回 createUserResponse，createUserResponse 会跟swagger:response进行匹配，匹配成功的swagger:response就是该 API 接口返回 200 状态码时的返回。
- swagger:response：swagger:response定义了 API 接口的返回，例如 getUserResponseWrapper，关于名字，我们可以根据需要自由命名，并不会带来任何不同。getUserResponseWrapper 中有一个 Body 字段，其注释为// in:body，说明该参数是在 HTTP Body 中返回。swagger:response之上的注释会被解析为返回参数的描述。api.User 自动被 go-swagger 解析为Example Value和Model。我们不用再去编写重复的返回字段，只需要引用已有的 Go 结构体即可，这也是通过工具生成 Swagger 文档的魅力所在。
- swagger:parameters：swagger:parameters定义了 API 接口的请求参数，例如 userParamsWrapper。userParamsWrapper 之上的注释会被解析为请求参数的描述，// in:body代表该参数是位于 HTTP Body 中。同样，userParamsWrapper 结构体名我们也可以随意命名，不会带来任何不同。swagger:parameters之后的 createUserRequest 会跟swagger:route的 ID 进行匹配，匹配成功则说明是该 ID 所在 API 接口的请求参数。


进入 gopractise-demo/swagger 目录，执行如下命令，生成 Swagger API 文档，并启动 HTTP 服务，在浏览器查看 Swagger：


```
$ swagger generate spec -o swagger.yaml
$ swagger serve --no-open -F=swagger --port 36666 swagger.yaml
2020/10/20 23:28:30 serving docs at http://localhost:36666/docs

```
打开浏览器，访问 http://localhost:36666/docs ，如下图所示：


![img](https://static001.geekbang.org/resource/image/e6/e0/e6d6d138fb890ef219d71671d146d5e0.png?wh=2450x1213)
上面我们生成了 swagger 风格的 UI 界面，我们也可以使用 redoc 风格的 UI 界面，如下图所示：


![img](https://static001.geekbang.org/resource/image/dd/48/dd568a44290283861ba5c37f28307d48.png?wh=2527x1426)

#### go-swagger 其他常用功能介绍
上面，我介绍了 swagger 最常用的 generate、serve 命令，关于 swagger 其他有用的命令，这里也简单介绍一下。


对比 Swagger 文档


```
$ swagger diff -d change.log swagger.new.yaml swagger.old.yaml
$ cat change.log

BREAKING CHANGES:
=================
/users:post Request - Body.Body.nickname.address.email.name.Body : User - Deleted property
compatibility test FAILED: 1 breaking changes detected
```

生成服务端代码
我们也可以先定义 Swagger 接口文档，再用 swagger 命令，基于 Swagger 接口文档生成服务端代码。假设我们的应用名为 go-user，进入 gopractise-demo/swagger 目录，创建 go-user 目录，并生成服务端代码：


```
$ mkdir go-user
$ cd go-user
$ swagger generate server -f ../swagger.yaml -A go-user
```
上述命令会在当前目录生成 cmd、restapi、models 文件夹，可执行如下命令查看 server 组件启动方式：

```
$ go run cmd/go-user-server/main.go -h
```
生成客户端代码
在 go-user 目录下执行如下命令：


```
$ swagger generate client -f ../swagger.yaml -A go-user
```
上述命令会在当前目录生成 client，包含了 API 接口的调用函数，也就是 API 接口的 Go SDK。

验证 Swagger 文档是否合法


```
$ swagger validate swagger.yaml
2020/10/21 09:53:18
The swagger spec at "swagger.yaml" is valid against swagger specification 2.0
```
合并 Swagger 文档


```
$ swagger mixin swagger_part1.yaml swagger_part2.yaml
```
### IAM Swagger 文档
IAM 的 Swagger 文档定义在iam/api/swagger/docs目录下，遵循 go-swagger 规范进行定义。

iam/api/swagger/docs/doc.go文件定义了更多 Swagger 文档的基本信息，比如开源协议、联系方式、安全认证等。



更详细的定义，你可以直接查看 iam/api/swagger/docs 目录下的 Go 源码文件。为了便于生成文档和启动 HTTP 服务查看 Swagger 文档，该操作被放在 Makefile 中执行（位于iam/scripts/make-rules/swagger.mk文件中）：


```makefile
.PHONY: swagger.run    
swagger.run: tools.verify.swagger    
  @echo "===========> Generating swagger API docs"    
  @swagger generate spec --scan-models -w $(ROOT_DIR)/cmd/genswaggertypedocs -o $(ROOT_DIR)/api/swagger/swagger.yaml    
    
.PHONY: swagger.serve    
swagger.serve: tools.verify.swagger    
  @swagger serve -F=redoc --no-open --port 36666 $(ROOT_DIR)/api/swagger/swagger.yaml  
```

Makefile 文件说明：
- tools.verify.swagger：检查 Linux 系统是否安装了 go-swagger 的命令行工具 swagger，如果没有安装则运行 go get 安装。
- swagger.run：运行 swagger generate spec 命令生成 Swagger 文档 swagger.yaml，运行前会检查 swagger 是否安装。 --scan-models 指定生成的文档中包含带有 swagger:model 注释的 Go Models。-w 指定 swagger 命令运行的目录。
- swagger.serve：运行 swagger serve 命令打开 Swagger 文档 swagger.yaml，运行前会检查 swagger 是否安装。


在 iam 源码根目录下执行如下命令，即可生成并启动 HTTP 服务查看 Swagger 文档：



```
$ make swagger
$ make serve-swagger
2020/10/21 06:45:03 serving docs at http://localhost:36666/docs
```
打开浏览器，打开http://x.x.x.x:36666/docs查看 Swagger 文档，x.x.x.x 是服务器的 IP 地址，如下图所示：


![img](https://static001.geekbang.org/resource/image/6a/3b/6ac3529ed98aa94573862da99434683b.png?wh=2524x1440)

IAM 的 Swagger 文档，还可以通过在 iam 源码根目录下执行go generate ./...命令生成，为此，我们需要在 iam/cmd/genswaggertypedocs/swagger_type_docs.go 文件中，添加//go:generate注释。如下图所示：


![img](https://static001.geekbang.org/resource/image/cc/d7/cc03b896e5403cc55d7e11fe2078d9d7.png?wh=1657x397)


### 总结
在做 Go 服务开发时，我们要向前端或用户提供 API 文档，手动编写 API 文档工作量大，也难以维护。所以，现在很多项目都是自动生成 Swagger 格式的 API 文档。提到 Swagger，很多开发者不清楚其和 OpenAPI 的区别，所以我也给你总结了：OpenAPI 是一个 API 规范，Swagger 则是实现规范的工具。


在 Go 中，用得最多的是利用 go-swagger 来生成 Swagger 格式的 API 文档。go-swagger 包含了很多语法，我们可以访问Swagger 2.0进行学习。学习完 Swagger 2.0 的语法之后，就可以编写 swagger 注释了，之后可以通过


```
$ swagger generate spec -o swagger.yaml
```
来生成 swagger 文档 swagger.yaml。通过


```
$ swagger serve --no-open -F=swagger --port 36666 swagger.yaml
```
来提供一个前端界面，供我们访问 swagger 文档。为了方便管理，我们可以将 swagger generate spec 和 swagger serve 命令加入到 Makefile 文件中，通过 Makefile 来生成 Swagger 文档，并提供给前端界面。

## 18 | 错误处理（上）：如何设计一套科学的错误码？

现代的软件架构，很多都是对外暴露 RESTful API 接口，内部系统通信采用 RPC 协议。因为 RESTful API 接口有一些天生的优势，比如规范、调试友好、易懂，所以通常作为直接面向用户的通信规范。


既然是直接面向用户，那么首先就要求消息返回格式是规范的；其次，如果接口报错，还要能给用户提供一些有用的报错信息，通常需要包含 Code 码（用来唯一定位一次错误）和 Message（用来展示出错的信息）。这就需要我们设计一套规范的、科学的错误码。


这一讲，我就来详细介绍下，如何设计一套规范的、科学的错误码。下一讲，我还会介绍如何提供一个 errors 包来支持我们设计的错误码。


### 期望错误码实现的功能
要想设计一套错误码，首先就得弄清我们的需求。

RESTful API 是基于 HTTP 协议的一系列 API 开发规范，HTTP 请求结束后，无论 API 请求成功或失败，都需要让客户端感知到，以便客户端决定下一步该如何处理。为了让用户拥有最好的体验，需要有一个比较好的错误码实现方式。这里我介绍下在设计错误码时，期望能够实现的功能。


第一个功能是有业务 Code 码标识。因为 HTTP Code 码有限，并且都是跟 HTTP Transport 层相关的 Code 码，所以我们希望能有自己的错误 Code 码。一方面，可以根据需要自行扩展，另一方面也能够精准地定位到具体是哪个错误。同时，因为 Code 码通常是对计算机友好的 10 进制整数，基于 Code 码，计算机也可以很方便地进行一些分支处理。当然了，业务码也要有一定规则，可以通过业务码迅速定位出是哪类错误。


第二个功能，考虑到安全，希望能够对外对内分别展示不同的错误信息。当开发一个对外的系统，业务出错时，需要一些机制告诉用户出了什么错误，如果能够提供一些帮助文档会更好。但是，我们不可能把所有的错误都暴露给外部用户，这不仅没必要，也不安全。所以也需要能让我们获取到更详细的内部错误信息的机制，这些内部错误信息可能包含一些敏感的数据，不宜对外展示，但可以协助我们进行问题定位。


所以，我们需要设计的错误码应该是规范的，能方便客户端感知到 HTTP 是否请求成功，并带有业务码和出错信息。



### 常见的错误码设计方式
在业务中，大致有三种错误码实现方式。我用一次因为用户账号没有找到而请求失败的例子，分别给你解释一下：

第一种方式，不论请求成功或失败，始终返回200 http status code，在 HTTP Body 中包含用户账号没有找到的错误信息。例如 Facebook API 的错误 Code 设计，始终返回 200 http status code：


```
{
  "error": {
    "message": "Syntax error \"Field picture specified more than once. This is only possible before version 2.1\" at character 23: id,name,picture,picture",
    "type": "OAuthException",
    "code": 2500,
    "fbtrace_id": "xxxxxxxxxxx"
  }
}
```
采用固定返回200 http status code的方式，有其合理性。比如，HTTP Code 通常代表 HTTP Transport 层的状态信息。当我们收到 HTTP 请求，并返回时，HTTP Transport 层是成功的，所以从这个层面上来看，HTTP Status 固定为 200 也是合理的。


但是这个方式的缺点也很明显：对于每一次请求，我们都要去解析 HTTP Body，从中解析出错误码和错误信息。实际上，大部分情况下，我们对于成功的请求，要么直接转发，要么直接解析到某个结构体中；对于失败的请求，我们也希望能够更直接地感知到请求失败。这种方式对性能会有一定的影响，对客户端不友好。所以我不**建议**你使用这种方式。

第二种方式，返回http 404 Not Found错误码，并在 Body 中返回简单的错误信息。例如：Twitter API 的错误设计，会根据错误类型，返回合适的 HTTP Code，并在 Body 中返回错误信息和自定义业务 Code。


```
HTTP/1.1 400 Bad Request
x-connection-hash: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
set-cookie: guest_id=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Date: Thu, 01 Jun 2017 03:04:23 GMT
Content-Length: 62
x-response-time: 5
strict-transport-security: max-age=631138519
Connection: keep-alive
Content-Type: application/json; charset=utf-8
Server: tsa_b

{"errors":[{"code":215,"message":"Bad Authentication data."}]}
```
这种方式比第一种要好一些，通过http status code可以使客户端非常直接地感知到请求失败，并且提供给客户端一些错误信息供参考。但是仅仅靠这些信息，还不能准确地定位和解决问题。


第三种方式，返回http 404 Not Found错误码，并在 Body 中返回详细的错误信息。例如：微软 Bing API 的错误设计，会根据错误类型，返回合适的 HTTP Code，并在 Body 中返回详尽的错误信息。


```
HTTP/1.1 400
Date: Thu, 01 Jun 2017 03:40:55 GMT
Content-Length: 276
Connection: keep-alive
Content-Type: application/json; charset=utf-8
Server: Microsoft-IIS/10.0
X-Content-Type-Options: nosniff

{"SearchResponse":{"Version":"2.2","Query":{"SearchTerms":"api error codes"},"Errors":[{"Code":1001,"Message":"Required parameter is missing.","Parameter":"SearchRequest.AppId","HelpUrl":"http\u003a\u002f\u002fmsdn.microsoft.com\u002fen-us\u002flibrary\u002fdd251042.aspx"}]}}
```

这是我比较**推荐**的一种方式，既能通过http status code使客户端方便地知道请求出错，又可以使用户根据返回的信息知道哪里出错，以及如何解决问题。同时，返回了机器友好的业务 Code 码，可以在有需要时让程序进一步判断处理。


### 错误码设计**建议**
综合刚才讲到的，我们可以总结出一套优秀的错误码设计思路：


- 有区别于http status code的业务码，业务码需要有一定规则，可以通过业务码判断出是哪类错误。
- 请求出错时，可以通过http status code直接感知到请求出错。
- 需要在请求出错时，返回详细的信息，通常包括 3 类信息：业务 Code 码、错误信息和参考文档（可选）。
- 返回的错误信息，需要是可以直接展示给用户的安全信息，也就是说不能包含敏感信息；同时也要有内部更详细的错误信息，方便 debug。
- 返回的数据格式应该是固定的、规范的。
- 错误信息要保持简洁，并且提供有用的信息。


这里其实还有两个功能点需要我们实现：业务 Code 码设计，以及请求出错时，如何设置http status code。接下来，我会详细介绍下如何实现这两个功能点。


### 业务 Code 码设计
要解决业务 Code 码如何设计这个问题，我们先来看下为什么要引入业务 Code 码。


在实际开发中，引入业务 Code 码有下面几个好处：
- 可以非常方便地定位问题和定位代码行（看到错误码知道什么意思、grep 错误码可以定位到错误码所在行、某个错误类型的唯一标识）。
- 错误码包含一定的信息，通过错误码可以判断出错误级别、错误模块和具体错误信息。
- Go 中的 HTTP 服务器开发都是引用 net/http 包，该包中只有 60 个错误码，基本都是跟 HTTP 请求相关的错误码，在一个大型系统中，这些错误码完全不够用，而且这些错误码跟业务没有任何关联，满足不了业务的需求。引入业务的 Code 码，则可以解决这些问题。
- 业务开发过程中，可能需要判断错误是哪种类型，以便做相应的逻辑处理，通过定制的错误可以很容易做到这点，例如：



```go
if err == code.ErrBind {
    ...
}
```
这里要**注意**，业务 Code 码可以是一个整数，也可以是一个整型字符串，还可以是一个字符型字符串，它是错误的唯一标识。



**通过研究腾讯云、阿里云、新浪的开放 API，我发现新浪的 API Code 码设计更合理些。所以，我参考新浪的 Code 码设计，总结出了我**推荐**的 Code 码设计规范：纯数字表示，不同部位代表不同的服务，不同的模块。**


错误代码说明：10010110: 服务。01: 某个服务下的某个模块。01: 模块下的错误码序号，每个模块可以注册 100 个错误。


通过100101可以知道这个错误是服务 A，数据库模块下的记录没有找到错误。


你可能会问：按这种设计，每个模块下最多能注册 100 个错误，是不是有点少？其实在我看来，如果每个模块的错误码超过 100 个，要么说明这个模块太大了，**建议**拆分；要么说明错误码设计得不合理，共享性差，需要重新设计。


### 如何设置 HTTP Status Code
Go net/http 包提供了 60 个错误码，大致分为如下 5 类：


- 1XX - （指示信息）表示请求已接收，继续处理。
- 2XX - （请求成功）表示成功处理了请求的状态代码。
- 3XX - （请求被重定向）表示要完成请求，需要进一步操作。通常，这些状态代码用来重定向。
- 4XX - （请求错误）这些状态代码表示请求可能出错，妨碍了服务器的处理，通常是客户端出错，需要客户端做进一步的处理。
- 5XX - （服务器错误）这些状态代码表示服务器在尝试处理请求时发生内部错误。这些错误可能是服务器本身的错误，而不是客户端的问题。


可以看到 HTTP Code 有很多种，如果每个 Code 都做错误映射，会面临很多问题。比如，研发同学不太好判断错误属于哪种http status code，到最后很可能会导致错误或者http status code不匹配，变成一种形式。而且，客户端也难以应对这么多的 HTTP 错误码。

所以，这里**建议**http status code不要太多，基本上只需要这 3 个 HTTP Code:200 - 表示请求成功执行。400 - 表示客户端出问题。500 - 表示服务端出问题。


如果觉得这 3 个错误码不够用，最多可以加如下 3 个错误码：401 - 表示认证失败。403 - 表示授权失败。404 - 表示资源找不到，这里的资源可以是 URL 或者 RESTful 资源。


将错误码控制在适当的数目内，客户端比较容易处理和判断，开发也比较容易进行错误码映射。



### IAM 项目错误码设计规范
接下来，我们来看下 IAM 项目的错误码是如何设计的。


#### Code 设计规范
先来看下 IAM 项目业务的 Code 码设计规范，具体实现可参考internal/pkg/code 目录。IAM 项目的错误码设计规范符合上面介绍的错误码设计思路和规范，具体规范见下。

Code 代码从 100001 开始，1000 以下为 github.com/marmotedu/errors 保留 code。
错误代码说明：100001


![img](https://static001.geekbang.org/resource/image/9c/6e/9c088dcb4c7b2509c2eaa81ed3be3b6e.jpg?wh=1385x699)

服务和模块说明


![img](https://static001.geekbang.org/resource/image/91/f5/91296aab54da035580e80b6637dd4df5.png?wh=1457x1188)

通用：说明所有服务都适用的错误，提高复用性，避免重复造轮子。


错误信息规范说明
对外暴露的错误，统一大写开头，结尾不要加.。对外暴露的错误要简洁，并能准确说明问题。对外暴露的错误说明，应该是 该怎么做 而不是 哪里错了。


这里你需要**注意**，错误信息是直接暴露给用户的，不能包含敏感信息。


#### IAM API 接口返回值说明
如果返回结果中存在 code 字段，则表示调用 API 接口失败。例如：


```
{
  "code": 100101,
  "message": "Database error",
  "reference": "https://github.com/marmotedu/iam/tree/master/docs/guide/zh-CN/faq/iam-apiserver"
}
```
上述返回中 code 表示错误码，message 表示该错误的具体信息。每个错误同时也对应一个 HTTP 状态码。比如上述错误码对应了 HTTP 状态码 500(Internal Server Error)。另外，在出错时，也返回了reference字段，该字段包含了可以解决这个错误的文档链接地址。关于 IAM 系统支持的错误码，我给你列了一个表格，你可以看看：


![img](https://static001.geekbang.org/resource/image/b5/95/b58ff5b9455d13fc397fdf5228ea7195.png?wh=1441x1518)

![img](https://static001.geekbang.org/resource/image/c6/d2/c6d356a3f5f2bfc3d6001yy3c05a90d2.png?wh=1321x1570)

### 总结
对外暴露的 API 接口需要有一套规范的、科学的错误码。目前业界的错误码大概有 3 种设计方式，我用一次因为用户账号没有找到而请求失败的例子，给你做了解释：


不论请求成功失败，始终返回200 http status code，在 HTTP Body 中包含用户账号没有找到的错误信息。返回http 404 Not Found错误码，并在 Body 中返回简单的错误信息。返回http 404 Not Found错误码，并在 Body 中返回详细的错误信息。


这一讲，我参考这 3 个错误码设计，给出了自己的错误码设计**建议**：错误码包含 HTTP Code 和业务 Code，并且业务 Code 会映射为一个 HTTP Code。错误码也会对外暴露两种错误信息，一种是直接暴露给用户的，不包含敏感信息的信息；另一种是供内部开发查看，定位问题的错误信息。该错误码还支持返回参考文档，用于在出错时展示给用户，供用户查看解决问题。

****建议**你重点关注我总结的 Code 码设计规范：纯数字表示，不同部位代表不同的服务，不同的模块。**

比如错误代码100101，其中 10 代表服务；中间的 01 代表某个服务下的某个模块；最后的 01 代表模块下的错误码序号，每个模块可以注册 100 个错误。

## 19 | 错误处理（下）：如何设计错误包？

在 Go 项目开发中，错误是我们必须要处理的一个事项。除了我们上一讲学习过的错误码，处理错误也离不开错误包。业界有很多优秀的、开源的错误包可供选择，例如 Go 标准库自带的errors包、github.com/pkg/errors包。但是这些包目前还不支持业务错误码，很难满足生产级应用的需求。所以，在实际开发中，我们有必要开发出适合自己错误码设计的错误包。当然，我们也没必要自己从 0 开发，可以基于一些优秀的包来进行二次封装。这一讲里，我们就来一起看看，如何设计一个错误包来适配上一讲我们设计的错误码，以及一个错误码的具体实现。


### 错误包需要具有哪些功能？
要想设计一个优秀的错误包，我们首先得知道一个优秀的错误包需要具备哪些功能。在我看来，至少需要有下面这六个功能：


首先，应该能支持错误堆栈。我们来看下面一段代码，假设保存在bad.go文件中：


```go
package main

import (
  "fmt"
  "log"
)

func main() {
  if err := funcA(); err != nil {
    log.Fatalf("call func got failed: %v", err)
    return
  }

  log.Println("call func success")
}

func funcA() error {
  if err := funcB(); err != nil {
    return err
  }

  return fmt.Errorf("func called error")
}

func funcB() error {
  return fmt.Errorf("func called error")
}
```
执行上面的代码：


```
$ go run bad.go
2021/07/02 08:06:55 call func got failed: func called error
exit status 1
```
这时我们想定位问题，但不知道具体是哪行代码报的错误，只能靠猜，还不一定能猜到。为了解决这个问题，我们可以加一些 Debug 信息，来协助我们定位问题。这样做在测试环境是没问题的，但是在线上环境，一方面修改、发布都比较麻烦，另一方面问题可能比较难重现。这时候我们会想，要是能打印错误的堆栈就好了。例如：


```
2021/07/02 14:17:03 call func got failed: func called error
main.funcB
  /home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/errors/good.go:27
main.funcA
  /home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/errors/good.go:19
main.main
  /home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/errors/good.go:10
runtime.main
  /home/colin/go/go1.16.2/src/runtime/proc.go:225
runtime.goexit
  /home/colin/go/go1.16.2/src/runtime/asm_amd64.s:1371
exit status 1
```
通过上面的错误输出，我们可以很容易地知道是哪行代码报的错，从而极大提高问题定位的效率，降低定位的难度。所以，在我看来，一个优秀的 errors 包，首先需要支持错误堆栈。

其次，能够支持不同的打印格式。例如%+v、%v、%s等格式，可以根据需要打印不同丰富度的错误信息。


再次，能支持 Wrap/Unwrap 功能，也就是在已有的错误上，追加一些新的信息。例如errors.Wrap(err, "open file failed") 。Wrap 通常用在调用函数中，调用函数可以基于被调函数报错时的错误 Wrap 一些自己的信息，丰富报错信息，方便后期的错误定位，例如：



```go
func funcA() error {
    if err := funcB(); err != nil {
        return errors.Wrap(err, "call funcB failed")
    }

    return errors.New("func called error")
}

func funcB() error {
    return errors.New("func called error")
}
```
这里要**注意**，不同的错误类型，Wrap 函数的逻辑也可以不同。另外，在调用 Wrap 时，也会生成一个错误堆栈节点。我们既然能够嵌套 error，那有时候还可能需要获取被嵌套的 error，这时就需要错误包提供Unwrap函数。


还有，错误包应该有Is方法。在实际开发中，我们经常需要判断某个 error 是否是指定的 error。在 Go 1.13 之前，也就是没有 wrapping error 的时候，我们要判断 error 是不是同一个，可以使用如下方法：


```go
if err == os.ErrNotExist {
  // normal code
}
```
但是现在，因为有了 wrapping error，这样判断就会有问题。因为你根本不知道返回的 err 是不是一个嵌套的 error，嵌套了几层。这种情况下，我们的错误包就需要提供Is函数：


```go
func Is(err, target error) bool
```
当 err 和 target 是同一个，或者 err 是一个 wrapping error 的时候，如果 target 也包含在这个嵌套 error 链中，返回 true，否则返回 fasle。

**另外，错误包应该支持 As 函数。**在 Go 1.13 之前，没有 wrapping error 的时候，我们要把 error 转为另外一个 error，一般都是使用 type assertion 或者 type switch，也就是类型断言。例如：


```go
if perr, ok := err.(*os.PathError); ok {
  fmt.Println(perr.Path)
}
```

但是现在，返回的 err 可能是嵌套的 error，甚至好几层嵌套，这种方式就不能用了。所以，我们可以通过实现 As 函数来完成这种功能。现在我们把上面的例子，用 As 函数实现一下：



```go
var perr *os.PathError
if errors.As(err, &perr) {
  fmt.Println(perr.Path)
}
```

这样就可以完全实现类型断言的功能，而且还更强大，因为它可以处理 wrapping error。最后，能够支持两种错误创建方式：非格式化创建和格式化创建。例如：


```go
errors.New("file not found")
errors.Errorf("file %s not found", "iam-apiserver")
```
上面，我们介绍了一个优秀的错误包应该具备的功能。一个好消息是，Github 上有不少实现了这些功能的错误包，其中github.com/pkg/errors包最受欢迎。所以，我基于github.com/pkg/errors包进行了二次封装，用来支持上一讲所介绍的错误码。

### 错误包实现
明确优秀的错误包应该具备的功能后，我们来看下错误包的实现。实现的源码存放在github.com/marmotedu/errors。


我通过在文件github.com/pkg/errors/errors.go中增加新的withCode结构体，来引入一种新的错误类型，该错误类型可以记录错误码、stack、cause 和具体的错误信息。


```go
type withCode struct {
    err   error // error 错误
    code  int // 业务错误码
    cause error // cause error
    *stack // 错误堆栈
}
```

下面，我们通过一个示例，来了解下github.com/marmotedu/errors所提供的功能。假设下述代码保存在errors.go文件中：


```go
package main

import (
  "fmt"

  "github.com/marmotedu/errors"
  code "github.com/marmotedu/sample-code"
)

func main() {
  if err := bindUser(); err != nil {
    // %s: Returns the user-safe error string mapped to the error code or the error message if none is specified.
    fmt.Println("====================> %s <====================")
    fmt.Printf("%s\n\n", err)

    // %v: Alias for %s.
    fmt.Println("====================> %v <====================")
    fmt.Printf("%v\n\n", err)

    // %-v: Output caller details, useful for troubleshooting.
    fmt.Println("====================> %-v <====================")
    fmt.Printf("%-v\n\n", err)

    // %+v: Output full error stack details, useful for debugging.
    fmt.Println("====================> %+v <====================")
    fmt.Printf("%+v\n\n", err)

    // %#-v: Output caller details, useful for troubleshooting with JSON formatted output.
    fmt.Println("====================> %#-v <====================")
    fmt.Printf("%#-v\n\n", err)

    // %#+v: Output full error stack details, useful for debugging with JSON formatted output.
    fmt.Println("====================> %#+v <====================")
    fmt.Printf("%#+v\n\n", err)

    // do some business process based on the error type
    if errors.IsCode(err, code.ErrEncodingFailed) {
      fmt.Println("this is a ErrEncodingFailed error")
    }

    if errors.IsCode(err, code.ErrDatabase) {
      fmt.Println("this is a ErrDatabase error")
    }

    // we can also find the cause error
    fmt.Println(errors.Cause(err))
  }
}

func bindUser() error {
  if err := getUser(); err != nil {
    // Step3: Wrap the error with a new error message and a new error code if needed.
    return errors.WrapC(err, code.ErrEncodingFailed, "encoding user 'Lingfei Kong' failed.")
  }

  return nil
}

func getUser() error {
  if err := queryDatabase(); err != nil {
    // Step2: Wrap the error with a new error message.
    return errors.Wrap(err, "get user failed.")
  }

  return nil
}

func queryDatabase() error {
  // Step1. Create error with specified error code.
  return errors.WithCode(code.ErrDatabase, "user 'Lingfei Kong' not found.")
}
```
上述代码中，通过WithCode函数来创建新的 withCode 类型的错误；通过WrapC来将一个 error 封装成一个 withCode 类型的错误；通过IsCode来判断一个 error 链中是否包含指定的 code。

withCode 错误实现了一个func (w *withCode) Format(state fmt.State, verb rune)方法，该方法用来打印不同格式的错误信息，见下表：


![img](https://static001.geekbang.org/resource/image/18/5c/18a93313e017d4f3b21370099d011c5c.png?wh=1755x1198)
例如，%+v会打印以下错误信息：


```
get user failed. - #1 [/home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/errors/errortrack_errors.go:19 (main.getUser)] (100101) Database error; user 'Lingfei Kong' not found. - #0 [/home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/errors/errortrack_errors.go:26 (main.queryDatabase)] (100101) Database error
```
那么你可能会问，这些错误信息中的100101错误码，还有Database error这种对外展示的报错信息等等，是从哪里获取的？这里我简单解释一下。


首先， withCode 中包含了 int 类型的错误码，例如100101。其次，当使用github.com/marmotedu/errors包的时候，需要调用Register或者MustRegister，将一个 Coder 注册到github.com/marmotedu/errors开辟的内存中，数据结构为：


```go
var codes = map[int]Coder{}
```
Coder 是一个接口，定义为：


```go
type Coder interface {
    // HTTP status that should be used for the associated error code.
    HTTPStatus() int

    // External (user) facing error text.
    String() string

    // Reference returns the detail documents for user.
    Reference() string

    // Code returns the code of the coder
    Code() int
}
```

这样 withCode 的Format方法，就能够通过 withCode 中的 code 字段获取到对应的 Coder，并通过 Coder 提供的 HTTPStatus、String、Reference、Code 函数，来获取 withCode 中 code 的详细信息，最后格式化打印。


这里要**注意**，我们实现了两个注册函数：Register和MustRegister，二者唯一区别是：当重复定义同一个错误 Code 时，MustRegister会 panic，这样可以防止后面注册的错误覆盖掉之前注册的错误。在实际开发中，**建议**使用MustRegister。


XXX()和MustXXX()的函数命名方式，是一种 Go 代码设计技巧，在 Go 代码中经常使用，例如 Go 标准库中regexp包提供的Compile和MustCompile函数。和XXX相比，MustXXX 会在某种情况不满足时 panic。因此使用MustXXX的开发者看到函数名就会有一个心理预期：使用不当，会造成程序 panic。



最后，我还有一个**建议**：在实际的生产环境中，我们可以使用 JSON 格式打印日志，JSON 格式的日志可以非常方便的供日志系统解析。我们可以根据需要，选择%#-v或%#+v两种格式。



错误包在代码中，经常被调用，所以我们要保证错误包一定要是高性能的，否则很可能会影响接口的性能。这里，我们再来看下github.com/marmotedu/errors包的性能。


在这里，我们把这个错误包跟 go 标准库的 errors 包，以及 github.com/pkg/errors 包进行对比，来看看它们的性能：


```
$  go test -test.bench=BenchmarkErrors -benchtime="3s"
goos: linux
goarch: amd64
pkg: github.com/marmotedu/errors
BenchmarkErrors/errors-stack-10-8           57658672          61.8 ns/op        16 B/op         1 allocs/op
BenchmarkErrors/pkg/errors-stack-10-8        2265558        1547 ns/op       320 B/op         3 allocs/op
BenchmarkErrors/marmot/errors-stack-10-8     1903532        1772 ns/op       360 B/op         5 allocs/op
BenchmarkErrors/errors-stack-100-8           4883659         734 ns/op        16 B/op         1 allocs/op
BenchmarkErrors/pkg/errors-stack-100-8       1202797        2881 ns/op       320 B/op         3 allocs/op
BenchmarkErrors/marmot/errors-stack-100-8    1000000        3116 ns/op       360 B/op         5 allocs/op
BenchmarkErrors/errors-stack-1000-8           505636        7159 ns/op        16 B/op         1 allocs/op
BenchmarkErrors/pkg/errors-stack-1000-8       327681       10646 ns/op       320 B/op         3 allocs/op
BenchmarkErrors/marmot/errors-stack-1000-8             304160       11896 ns/op       360 B/op         5 allocs/op
PASS
ok    github.com/marmotedu/errors  39.200s
```
可以看到github.com/marmotedu/errors和github.com/pkg/errors包的性能基本持平。在对比性能时，重点关注 ns/op，也即每次 error 操作耗费的纳秒数。另外，我们还需要测试不同 error 嵌套深度下的 error 操作性能，嵌套越深，性能越差。例如：在嵌套深度为 10 的时候， github.com/pkg/errors 包 ns/op 值为 1547， github.com/marmotedu/errors 包 ns/op 值为 1772。可以看到，二者性能基本保持一致。


具体性能数据对比见下表：


![img](https://static001.geekbang.org/resource/image/a6/5e/a6a794d7523bc1edfa459d3a49f9685e.png?wh=1737x1145)
我们是通过BenchmarkErrors测试函数来测试 error 包性能的，你感兴趣可以打开链接看看。


### 如何记录错误？
上面，我们一起看了怎么设计一个优秀的错误包，那如何用我们设计的错误包来记录错误呢？根据我的开发经验，我**推荐**两种记录错误的方式，可以帮你快速定位问题。

方式一：通过github.com/marmotedu/errors包提供的错误堆栈能力，来跟踪错误。具体你可以看看下面的代码示例。以下代码保存在errortrack_errors.go中。



```go
package main

import (
  "fmt"

  "github.com/marmotedu/errors"

  code "github.com/marmotedu/sample-code"
)

func main() {
  if err := getUser(); err != nil {
    fmt.Printf("%+v\n", err)
  }
}

func getUser() error {
  if err := queryDatabase(); err != nil {
    return errors.Wrap(err, "get user failed.")
  }

  return nil
}

func queryDatabase() error {
  return errors.WithCode(code.ErrDatabase, "user 'Lingfei Kong' not found.")
}
```
执行上述的代码：


```
$ go run errortrack_errors.go
get user failed. - #1 [/home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/errors/errortrack_errors.go:19 (main.getUser)] (100101) Database error; user 'Lingfei Kong' not found. - #0 [/home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/errors/errortrack_errors.go:26 (main.queryDatabase)] (100101) Database error
```
可以看到，打印的日志中打印出了详细的错误堆栈，包括错误发生的函数、文件名、行号和错误信息，通过这些错误堆栈，我们可以很方便地定位问题。

你使用这种方法时，我**推荐**的用法是，在错误最开始处使用 errors.WithCode() 创建一个 withCode 类型的错误。上层在处理底层返回的错误时，可以根据需要，使用 Wrap 函数基于该错误封装新的错误信息。如果要包装的 error 不是用github.com/marmotedu/errors包创建的，**建议**用 errors.WithCode() 新建一个 error。


方式二：在错误产生的最原始位置调用日志包记录函数，打印错误信息，其他位置直接返回（当然，也可以选择性的追加一些错误信息，方便故障定位）。示例代码（保存在errortrack_log.go）如下：



```go
package main

import (
  "fmt"

  "github.com/marmotedu/errors"
  "github.com/marmotedu/log"

  code "github.com/marmotedu/sample-code"
)

func main() {
  if err := getUser(); err != nil {
    fmt.Printf("%v\n", err)
  }
}

func getUser() error {
  if err := queryDatabase(); err != nil {
    return err
  }

  return nil
}

func queryDatabase() error {
  opts := &log.Options{
    Level:            "info",
    Format:           "console",
    EnableColor:      true,
    EnableCaller:     true,
    OutputPaths:      []string{"test.log", "stdout"},
    ErrorOutputPaths: []string{},
  }

  log.Init(opts)
  defer log.Flush()

  err := errors.WithCode(code.ErrDatabase, "user 'Lingfei Kong' not found.")
  if err != nil {
    log.Errorf("%v", err)
  }
  return err
}
```
执行以上代码：


```
$ go run errortrack_log.go
2021-07-03 14:37:31.597  ERROR  errors/errortrack_log.go:41  Database error
Database error

```
当错误发生时，调用 log 包打印错误。通过 log 包的 caller 功能，可以定位到 log 语句的位置，也就是定位到错误发生的位置。你使用这种方式来打印日志时，我有两个**建议**。


只在错误产生的最初位置打印日志，其他地方直接返回错误，一般不需要再对错误进行封装。当代码调用第三方包的函数时，第三方包函数出错时打印错误信息。比如：


```go
if err := os.Chdir("/root"); err != nil {
    log.Errorf("change dir failed: %v", err)
}
```
### 一个错误码的具体实现
接下来，我们看一个依据上一讲介绍的错误码规范的具体错误码实现github.com/marmotedu/sample-code。


sample-code实现了两类错误码，分别是通用错误码（sample-code/base.go）和业务模块相关的错误码（sample-code/apiserver.go）。首先，我们来看通用错误码的定义：



```go
// 通用: 基本错误
// Code must start with 1xxxxx
const (
    // ErrSuccess - 200: OK.
    ErrSuccess int = iota + 100001

    // ErrUnknown - 500: Internal server error.
    ErrUnknown

    // ErrBind - 400: Error occurred while binding the request body to the struct.
    ErrBind

    // ErrValidation - 400: Validation failed.
    ErrValidation

    // ErrTokenInvalid - 401: Token invalid.
    ErrTokenInvalid
)
```
在代码中，我们通常使用整型常量（ErrSuccess）来代替整型错误码（100001），因为使用 ErrSuccess 时，一看就知道它代表的错误类型，可以方便开发者使用。

错误码用来指代一个错误类型，该错误类型需要包含一些有用的信息，例如对应的 HTTP Status Code、对外展示的 Message，以及跟该错误匹配的帮助文档。所以，我们还需要实现一个 Coder 来承载这些信息。这里，我们定义了一个实现了github.com/marmotedu/errors.Coder接口的ErrCode结构体：


```go
// ErrCode implements `github.com/marmotedu/errors`.Coder interface.
type ErrCode struct {
    // C refers to the code of the ErrCode.
    C int

    // HTTP status that should be used for the associated error code.
    HTTP int

    // External (user) facing error text.
    Ext string

    // Ref specify the reference document.
    Ref string
}
```
可以看到ErrCode结构体包含了以下信息：int 类型的业务码。对应的 HTTP Status Code。暴露给外部用户的消息。错误的参考文档。


下面是一个具体的 Coder 示例：


```go
coder := &ErrCode{
    C:    100001,
    HTTP: 200,
    Ext:  "OK",
    Ref:  "https://github.com/marmotedu/sample-code/blob/master/README.md",
}
```
接下来，我们就可以调用github.com/marmotedu/errors包提供的Register或者MustRegister函数，将 Coder 注册到github.com/marmotedu/errors包维护的内存中。一个项目有很多个错误码，如果每个错误码都手动调用MustRegister函数会很麻烦，这里我们通过代码自动生成的方法，来生成 register 函数调用：



```go
//go:generate codegen -type=int
//go:generate codegen -type=int -doc -output ./error_code_generated.md
```
//go:generate codegen -type=int 会调用codegen工具，生成sample_code_generated.go源码文件：


```go
func init() {
  register(ErrSuccess, 200, "OK")
  register(ErrUnknown, 500, "Internal server error")
  register(ErrBind, 400, "Error occurred while binding the request body to the struct")
  register(ErrValidation, 400, "Validation failed")
    // other register function call
}
```
这些register调用放在 init 函数中，在加载程序的时候被初始化。这里要**注意**，在注册的时候，我们会检查 HTTP Status Code，只允许定义 200、400、401、403、404、500 这 6 个 HTTP 错误码。这里通过程序保证了错误码是符合 HTTP Status Code 使用要求的。


//go:generate codegen -type=int -doc -output ./error_code_generated.md会生成错误码描述文档 error_code_generated.md。当我们提供 API 文档时，也需要记着提供一份错误码描述文档，这样客户端才可以根据错误码，知道请求是否成功，以及具体发生哪类错误，好针对性地做一些逻辑处理。


codegen工具会根据错误码注释生成sample_code_generated.go和error_code_generated.md文件：


```go
// ErrSuccess - 200: OK.
 ErrSuccess int = iota + 100001
```
codegen 工具之所以能够生成sample_code_generated.go和error_code_generated.md，是因为我们的错误码注释是有规定格式的：// <错误码整型常量> - <对应的HTTP Status Code>: .。codegen 工具可以在 IAM 项目根目录下，执行以下命令来安装：


```
$ make tools.install.codegen
```
安装完 codegen 工具后，可以在 github.com/marmotedu/sample-code 包根目录下执行 go generate 命令，来生成sample_code_generated.go和error_code_generated.md。这里有个技巧需要你**注意**：生成的文件**建议**统一用 xxxx_generated.xx 来命名，这样通过 generated ，我们就知道这个文件是代码自动生成的，有助于我们理解和使用。

在实际的开发中，我们可以将错误码独立成一个包，放在 internal/pkg/code/目录下，这样可以方便整个应用调用。例如 IAM 的错误码就放在 IAM 项目根目录下的internal/pkg/code/目录下。我们的错误码是分服务和模块的，所以这里**建议**你把相同的服务放在同一个 Go 源文件中，例如 IAM 的错误码存放文件：


```
$ ls base.go apiserver.go authzserver.go 
apiserver.go  authzserver.go  base.go
```
一个应用中会有多个服务，例如 IAM 应用中，就包含了 iam-apiserver、iam-authz-server、iam-pump 三个服务。这些服务有一些通用的错误码，为了便于维护，可以将这些通用的错误码统一放在 base.go 源码文件中。其他的错误码，我们可以按服务分别放在不同的文件中：iam-apiserver 服务的错误码统一放在 apiserver.go 文件中；iam-authz-server 的错误码统一存放在 authzserver.go 文件中。其他服务以此类推。

另外，同一个服务中不同模块的错误码，可以按以下格式来组织：相同模块的错误码放在同一个 const 代码块中，不同模块的错误码放在不同的 const 代码块中。每个 const 代码块的开头注释就是该模块的错误码定义。例如：


```go
// iam-apiserver: user errors.
const (
    // ErrUserNotFound - 404: User not found.
    ErrUserNotFound int = iota + 110001

    // ErrUserAlreadyExist - 400: User already exist.
    ErrUserAlreadyExist
)

// iam-apiserver: secret errors.
const (
    // ErrEncrypt - 400: Secret reach the max count.
    ErrReachMaxCount int = iota + 110101

    //  ErrSecretNotFound - 404: Secret not found.
    ErrSecretNotFound
)
```
最后，我们还需要将错误码定义记录在项目的文件中，供开发者查阅、遵守和使用，例如 IAM 项目的错误码定义记录文档为code_specification.md。这个文档中记录了错误码说明、错误描述规范和错误记录规范等。

### 错误码实际使用方法示例
上面，我讲解了错误包和错误码的实现方式，那你一定想知道在实际开发中我们是如何使用的。这里，我就举一个在 gin web 框架中使用该错误码的例子：


```go
// Response defines project response format which in marmotedu organization.
type Response struct {
    Code      errors.Code `json:"code,omitempty"`
    Message   string      `json:"message,omitempty"`
    Reference string      `json:"reference,omitempty"`
    Data      interface{} `json:"data,omitempty"`
}

// WriteResponse used to write an error and JSON data into response.
func WriteResponse(c *gin.Context, err error, data interface{}) {
    if err != nil {
        coder := errors.ParseCoder(err)

        c.JSON(coder.HTTPStatus(), Response{
            Code:      coder.Code(),
            Message:   coder.String(),
            Reference: coder.Reference(),
            Data:      data,
        })
    }

    c.JSON(http.StatusOK, Response{Data: data})
}

func GetUser(c *gin.Context) {
    log.Info("get user function called.", "X-Request-Id", requestid.Get(c))
    // Get the user by the `username` from the database.
    user, err := store.Client().Users().Get(c.Param("username"), metav1.GetOptions{})
    if err != nil {
        core.WriteResponse(c, errors.WithCode(code.ErrUserNotFound, err.Error()), nil)
        return
    }

    core.WriteResponse(c, nil, user)
}
```
上述代码中，通过WriteResponse统一处理错误。在 WriteResponse 函数中，如果err != nil，则从 error 中解析出 Coder，并调用 Coder 提供的方法，获取错误相关的 Http Status Code、int 类型的业务码、暴露给用户的信息、错误的参考文档链接，并返回 JSON 格式的信息。如果 err == nil 则返回 200 和数据。


### 总结
记录错误是应用程序必须要做的一件事情，在实际开发中，我们通常会封装自己的错误包。一个优秀的错误包，应该能够支持错误堆栈、不同的打印格式、Wrap/Unwrap/Is/As 等函数，并能够支持格式化创建 error。


根据这些错误包设计要点，我基于 github.com/pkg/errors 包设计了 IAM 项目的错误包 github.com/marmotedu/errors ，该包符合我们上一讲设计的错误码规范。另外，本讲也给出了一个具体的错误码实现 sample-code ， sample-code 支持业务 Code 码、HTTP Status Code、错误参考文档、可以对内对外展示不同的错误信息。


最后，因为错误码注释是有固定格式的，所以我们可以通过 codegen 工具解析错误码的注释，生成 register 函数调用和错误码文档。这种做法也体现了我一直强调的 low code 思想，可以提高开发效率，减少人为失误。


## 20 | 日志处理（上）：如何设计日志包并记录日志？
在做 Go 项目开发时，除了处理错误之外，我们必须要做的另外一件事是记录日志。通过记录日志，可以完成一些基本功能，比如开发、测试期间的 Debug，故障排除，数据分析，监控告警，以及记录发生的事件等。

要实现这些功能，首先我们需要一个优秀的日志包。另外，我还发现不少 Go 项目开发者记录日志很随意，输出的日志并不能有效定位到问题。所以，我们还需要知道怎么更好地记录日志，这就需要一个日志记录规范。

有了优秀的日志包和日志记录规范，我们就能很快地定位到问题，获取足够的信息，并完成后期的数据分析和监控告警，也可以很方便地进行调试了。这一讲，我就来详细介绍下，如何设计日志包和日志记录规范。首先，我们来看下如何设计日志包。


### 如何设计日志包
目前，虽然有很多优秀的开源日志包可供我们选择，但在一个大型系统中，这些开源日志包很可能无法满足我们定制化的需求，这时候我们就需要自己开发日志包。


这些日志包可能是基于某个，或某几个开源的日志包改造而来，也可能是全新开发的日志包。那么在开发日志包时，我们需要实现哪些功能，又如何实现呢？接下来，我们就来详细聊聊。先来看下日志包需要具备哪些功能。根据功能的重要性，我将日志包需要实现的功能分为**基础功能、高级功能和可选功能**。基础功能是一个日志包必须要具备的功能；高级功能、可选功能都是在特定场景下可增加的功能。我们先来说基础功能。



### 基础功能
基础功能，是优秀日志包必备的功能，能够满足绝大部分的使用场景，适合一些中小型的项目。一个日志包应该具备以下 4 个基础功能。


支持基本的日志信息
日志包需要支持基本的日志信息，包括时间戳、文件名、行号、日志级别和日志信息。时间戳可以记录日志发生的时间。在定位问题时，我们往往需要根据时间戳，来复原请求过程，核对相同时间戳下的上下文，从而定位出问题。


文件名和行号，可以使我们更快速定位到打印日志的位置，找到问题代码。一个日志库如果不支持文件名和行号，排查故障就会变得非常困难，基本只能靠 grep 和记忆来定位代码。对于企业级的服务，需要保证服务在故障后能够快速恢复，恢复的时间越久，造成的损失就越大，影响就越大。这就要求研发人员能够快速定位并解决问题。通过文件名和行号，我们可以精准定位到问题代码，尽快地修复问题并恢复服务。


通过日志级别，可以知道日志的错误类型，最通常的用法是：直接过滤出 Error 级别的日志，这样就可以直接定位出问题出错点，然后再结合其他日志定位出出错的原因。如果不支持日志级别，在定位问题时，可能要查看一大堆无用的日志。在大型系统中，一次请求的日志量很多，这会大大延长我们定位问题的时间。而通过日志信息，我们可以知道错误发生的具体原因。


支持不同的日志级别
不同的日志级别代表不同的日志类型，例如：Error 级别的日志，说明日志是错误类型，在排障时，会首先查看错误级别的日志。Warn 级别日志说明出现异常，但还不至于影响程序运行，如果程序执行的结果不符合预期，则可以参考 Warn 级别的日志，定位出异常所在。Info 级别的日志，可以协助我们 Debug，并记录一些有用的信息，供后期进行分析。


通常一个日志包至少要实现 6 个级别，我给你提供了一张表格，按优先级从低到高排列如下：


![img](https://static001.geekbang.org/resource/image/bb/2b/bb1356bd3cf332ddeb30d3aef8fc8d2b.png?wh=1794x1120)

有些日志包，例如 logrus，还支持 Trace 日志级别。Trace 级别比 Debug 级别还低，能够打印更细粒度的日志信息。在我看来，Trace 级别不是必须的，你可以根据需要自行选择。


打印日志时，一个日志调用其实具有两个属性：
- 输出级别：打印日志时，我们期望日志的输出级别。例如，我们调用 glog.Info("This is info message") 打印一条日志，则输出日志级别为 Info。
- 开关级别：启动应用程序时，期望哪些输出级别的日志被打印。例如，使用 glog 时 -v=4 ，说明了只有日志级别高于 4 的日志才会被打印。


如果开关级别设置为 L ，只有输出级别 >=L 时，日志才会被打印。例如，开关级别为 Warn，则只会记录 Warn、Error 、Panic 和 Fatal 级别的日志。具体的输出关系如下图所示：


![img](https://static001.geekbang.org/resource/image/dd/ed/dd4e9c3ed26b254cb5b65ff1fddd40ed.png?wh=1654x526)


支持自定义配置
不同的运行环境，需要不同的日志输出配置，例如：开发测试环境为了能够方便地 Debug，需要设置日志级别为 Debug 级别；现网环境为了提高应用程序的性能，则需要设置日志级别为 Info 级别。又比如，现网环境为了方便日志采集，通常会输出 JSON 格式的日志；开发测试环境为了方便查看日志，会输出 TEXT 格式的日志。

所以，我们的日志包需要能够被配置，还要不同环境采用不同的配置。通过配置，可以在不重新编译代码的情况下，改变记录日志的行为。


支持输出到标准输出和文件
日志总是要被读的，要么输出到标准输出，供开发者实时读取，要么保存到文件，供开发者日后查看。输出到标准输出和保存到文件是一个日志包最基本的功能。


### 高级功能
除了上面提到的这些基本功能外，在一些大型系统中，通常还会要求日志包具备一些高级功能。这些高级功能可以帮我们更好地记录日志，并实现更丰富的功能，例如日志告警。那么一个日志包可以具备哪些高级功能呢？


支持多种日志格式
日志格式也是我们要考虑的一个点，一个好的日志格式，不仅方便查看日志，还能方便一些日志采集组件采集日志，并对接类似 Elasticsearch 这样的日志搜索引擎。


一个日志包至少需要提供以下两种格式：

TEXT 格式：TEXT 格式的日志具有良好的可读性，可以方便我们在开发联调阶段查看日志，例如：


```
2020-12-02T01:16:18+08:00 INFO example.go:11 std log
2020-12-02T01:16:18+08:00 DEBUG example.go:13 change std log to debug level
```

JSON 格式：JSON 格式的日志可以记录更详细的信息，日志中包含一些通用的或自定义的字段，可供日后的查询、分析使用，而且可以很方便地供 filebeat、logstash 这类日志采集工具采集并上报。下面是 JSON 格式的日志：



```
{"level":"DEBUG","time":"2020-12-02T01:16:18+08:00","file":"example.go:15","func":"main.main","message":"log in json format"}
{"level":"INFO","time":"2020-12-02T01:16:18+08:00","file":"example.go:16","func":"main.main","message":"another log in json format"}
```
我**建议**在开发联调阶段使用 TEXT 格式的日志，在现网环境使用 JSON 格式的日志。一个优秀的日志库，例如 logrus，除了提供基本的输出格式外，还应该允许开发者自定义日志输出格式。


能够按级别分类输出
为了能够快速定位到需要的日志，一个比较好的做法是将日志按级别分类输出，至少错误级别的日志可以输出到独立的文件中。这样，出现问题时，可以直接查找错误文件定位问题。例如，glog 就支持分类输出，如下图所示：


![img](https://static001.geekbang.org/resource/image/10/4b/100af3121e8d4e84428979f9d0yydf4b.png?wh=2091x326)

支持结构化日志
结构化日志（Structured Logging），就是使用 JSON 或者其他编码方式使日志结构化，这样可以方便后续使用 Filebeat、Logstash Shipper 等各种工具，对日志进行采集、过滤、分析和查找。就像下面这个案例，使用 zap 进行日志打印：


```go
package main

import (
    "time"

    "go.uber.org/zap"
)

func main() {
    logger, _ := zap.NewProduction()
    defer logger.Sync() // flushes buffer, if any
    url := "http://marmotedu.com"
    // 结构化日志打印
    logger.Sugar().Infow("failed to fetch URL", "url", url, "attempt", 3, "backoff", time.Second)

    // 非结构化日志打印
    logger.Sugar().Infof("failed to fetch URL: %s", url)
}
```
上述代码输出为：


```
{"level":"info","ts":1607303966.9903321,"caller":"zap/structured_log.go:14","msg":"failed to fetch URL","url":"http://marmotedu.com","attempt":3,"backoff":1}
{"level":"info","ts":1607303966.9904354,"caller":"zap/structured_log.go:17","msg":"failed to fetch URL: http://marmotedu.com"}
```
支持日志轮转
在一个大型项目中，一天可能会产生几十个 G 的日志。为了防止日志把磁盘空间占满，导致服务器或者程序异常，就需要确保日志大小达到一定量级时，对日志进行切割、压缩，并转存。

如何切割呢？你可以按照日志大小进行切割，也可以按日期切割。日志的切割、压缩和转存功能可以基于 GitHub 上一些优秀的开源包来封装，例如：lumberjack可以支持按大小和日期归档日志，file-rotatelogs支持按小时数进行日志切割。

对于日志轮转功能，其实我不**建议**在日志包中添加，因为这会增加日志包的复杂度，我更**建议**的做法是借助其他的工具来实现日志轮转。例如，在 Linux 系统中可以使用 Logrotate 来轮转日志。Logrotate 功能强大，是一个专业的日志轮转工具。


具备 Hook 能力
Hook 能力可以使我们对日志内容进行自定义处理。例如，当某个级别的日志产生时，发送邮件或者调用告警接口进行告警。很多优秀的开源日志包提供了 Hook 能力，例如 logrus 和 zap。


在一个大型系统中，日志告警是非常重要的功能，但更好的实现方式是将告警能力做成旁路功能。通过旁路功能，可以保证日志包功能聚焦、简洁。例如：可以将日志收集到 Elasticsearch，并通过 ElastAlert 进行日志告警。


### 可选功能
除了基础功能和高级功能外，还有一些功能。这些功能不会影响到日志包的核心功能，但是如果具有这些功能，会使日志包更加易用。比如下面的这三个功能。


支持颜色输出
在开发、测试时开启颜色输出，不同级别的日志会被不同颜色标识，这样我们可以很轻松地发现一些 Error、Warn 级别的日志，方便开发调试。发布到生产环境时，可以关闭颜色输出，以提高性能。


兼容标准库 log 包
一些早期的 Go 项目大量使用了标准库 log 包，如果我们的日志库能够兼容标准库 log 包，我们就可以很容易地替换掉标准库 log 包。例如，logrus 就兼容标准库 log 包。这里，我们来看一个使用了标准库 log 包的代码：


```go
package main

import (
    "log"
)

func main() {
    log.Print("call Print: line1")
    log.Println("call Println: line2")
}
```
只需要使用log "github.com/sirupsen/logrus"替换"log"就可以完成标准库 log 包的切换：


```go
package main

import (
    log "github.com/sirupsen/logrus"
)

func main() {
    log.Print("call Print: line1")
    log.Println("call Println: line2")
}
```

支持输出到不同的位置
在分布式系统中，一个服务会被部署在多台机器上，这时候如果我们要查看日志，就需要分别登录不同的机器查看，非常麻烦。我们更希望将日志统一投递到 Elasticsearch 上，在 Elasticsearch 上查看日志。

我们还可能需要从日志中分析某个接口的调用次数、某个用户的请求次数等信息，这就需要我们能够对日志进行处理。一般的做法是将日志投递到 Kafka，数据处理服务消费 Kafka 中保存的日志，从而分析出调用次数等信息。


以上两种场景，分别需要把日志投递到 Elasticsearch、Kafka 等组件，如果我们的日志包支持将日志投递到不同的目的端，那会是一项非常让人期待的功能：


![img](https://static001.geekbang.org/resource/image/bd/4b/bda7177a7a627b0117bfffdbd129914b.png?wh=1904x842)
如果日志不支持投递到不同的下游组件，例如 Elasticsearch、Kafka、Fluentd、Logstash 等位置，也可以通过 Filebeat 采集磁盘上的日志文件，进而投递到下游组件。



### 设计日志包时需要关注的点
上面，我们介绍了日志包具备的功能，这些功能可以指导我们完成日志包设计。这里，我们再来看下设计日志包时，我们还需要关注的几个层面：

- 高性能：因为我们要在代码中频繁调用日志包，记录日志，所以日志包的性能是首先要考虑的点，一个性能很差的日志包必然会导致整个应用性能很差。
- 并发安全：Go 应用程序会大量使用 Go 语言的并发特性，也就意味着需要并发地记录日志，这就需要日志包是并发安全的。
- 插件化能力：日志包应该能提供一些插件化的能力，比如允许开发者自定义输出格式，自定义存储位置，自定义错误发生时的行为（例如 告警、发邮件等）。插件化的能力不是必需的，因为日志自身的特性就能满足绝大部分的使用需求，例如：输出格式支持 JSON 和 TEXT，存储位置支持标准输出和文件，日志监控可以通过一些旁路系统来实现。
- 日志参数控制：日志包应该能够灵活地进行配置，初始化时配置或者程序运行时配置。例如：初始化配置可以通过 Init 函数完成，运行时配置可以通过 SetOptions / SetLevel 等函数来完成。


### 如何记录日志？
前面我们介绍了在设计日志包时，要包含的一些功能、实现方法和**注意**事项。但在这个过程中，还有一项重要工作需要**注意**，那就是日志记录问题。



日志并不是越多越好，在实际开发中，经常会遇到一大堆无用的日志，却没有我们需要的日志；或者有效的日志被大量无用的日志淹没，查找起来非常困难。一个优秀的日志包可以协助我们更好地记录、查看和分析日志，但是如何记录日志决定了我们能否获取到有用的信息。日志包是工具，日志记录才是灵魂。这里，我就来详细讲讲如何记录日志。

想要更好地记录日志，我们需要解决以下几个问题：**在何处打印日志？在哪个日志级别打印日志？如何记录日志内容？**


#### 在何处打印日志？
日志主要是用来定位问题的，所以整体来说，我们要在有需要的地方打印日志。那么具体是哪些地方呢？我给你几个**建议**。


- 在分支语句处打印日志。在分支语句处打印日志，可以判断出代码走了哪个分支，有助于判断请求的下一跳，继而继续排查问题。
- 写操作必须打印日志。写操作最可能会引起比较严重的业务故障，写操作打印日志，可以在出问题时找到关键信息。
- 在循环中打印日志要慎重。如果循环次数过多，会导致打印大量的日志，严重拖累代码的性能，**建议**的办法是在循环中记录要点，在循环外面总结打印出来。
- 在错误产生的最原始位置打印日志。对于嵌套的 Error，可在 Error 产生的最初位置打印 Error 日志，上层如果不需要添加必要的信息，可以直接返回下层的 Error。我给你举个例子：


```go
package main

import (
    "flag"
    "fmt"

    "github.com/golang/glog"
)

func main() {
    flag.Parse()
    defer glog.Flush()

    if err := loadConfig(); err != nil {
        glog.Error(err)
    }
}

func loadConfig() error {
    return decodeConfig() // 直接返回
}

func decodeConfig() error {
    if err := readConfig(); err != nil {
        return fmt.Errorf("could not decode configuration data for user %s: %v", "colin", err) // 添加必要的信息，用户名称
    }

    return nil
}

func readConfig() error {
    glog.Errorf("read: end of input.")
    return fmt.Errorf("read: end of input")
}
```
通过在最初产生错误的位置打印日志，我们可以很方便地追踪到日志的根源，进而在上层追加一些必要的信息。这可以让我们了解到该错误产生的影响，有助于排障。另外，直接返回下层日志，还可以减少重复的日志打印。当代码调用第三方包的函数，且第三方包函数出错时，会打印错误信息。比如：


```go
if err := os.Chdir("/root"); err != nil {
    log.Errorf("change dir failed: %v", err)
}
```
#### 在哪个日志级别打印日志？
不同级别的日志，具有不同的意义，能实现不同的功能，在开发中，我们应该根据目的，在合适的级别记录日志，这里我同样给你一些**建议**。


Debug 级别
为了获取足够的信息进行 Debug，通常会在 Debug 级别打印很多日志。例如，可以打印整个 HTTP 请求的请求 Body 或者响应 Body。


Debug 级别需要打印大量的日志，这会严重拖累程序的性能。并且，Debug 级别的日志，主要是为了能在开发测试阶段更好地 Debug，多是一些不影响现网业务的日志信息。所以，对于 Debug 级别的日志，在服务上线时我们一定要禁止掉。否则，就可能会因为大量的日志导致硬盘空间快速用完，从而造成服务宕机，也可能会影响服务的性能和产品体验。

Debug 这个级别的日志可以随意输出，任何你觉得有助于开发、测试阶段调试的日志，都可以在这个级别打印。


Info 级别
Info 级别的日志可以记录一些有用的信息，供以后的运营分析，所以 Info 级别的日志不是越多越好，也不是越少越好，应以满足需求为主要目标。一些关键日志，可以在 Info 级别记录，但如果日志量大、输出频度过高，则要考虑在 Debug 级别记录。



现网的日志级别一般是 Info 级别，为了不使日志文件占满整个磁盘空间，在记录日志时，要**注意**避免产生过多的 Info 级别的日志。例如，在 for 循环中，就要慎用 Info 级别的日志。


Warn 级别
一些警告类的日志可以记录在 Warn 级别，Warn 级别的日志往往说明程序运行异常，不符合预期，但又不影响程序的继续运行，或者是暂时影响，但后续会恢复。像这些日志，就需要你关注起来。Warn 更多的是业务级别的警告日志。


Error 级别
Error 级别的日志告诉我们程序执行出错，这些错误肯定会影响到程序的执行结果，例如请求失败、创建资源失败等。要记录每一个发生错误的日志，避免日后排障过程中这些错误被忽略掉。大部分的错误可以归在 Error 级别。


Panic 级别
Panic 级别的日志在实际开发中很少用，通常只在需要错误堆栈，或者不想因为发生严重错误导致程序退出，而采用 defer 处理错误时使用。


Fatal 级别
Fatal 是最高级别的日志，这个级别的日志说明问题已经相当严重，严重到程序无法继续运行，通常是系统级的错误。在开发中也很少使用，除非我们觉得某个错误发生时，整个程序无法继续运行。


这里用一张图来总结下，如何选择 Debug、Info、Warn、Error、Panic、Fatal 这几种日志级别。


![img](https://static001.geekbang.org/resource/image/75/35/75e8c71a791f279a68c35734f2451035.png?wh=3646x1542)


#### 如何记录日志内容？
关于如何记录日志内容，我有几条**建议**：


- 在记录日志时，不要输出一些敏感信息，例如密码、密钥等。
- 为了方便调试，通常会在 Debug 级别记录一些临时日志，这些日志内容可以用一些特殊的字符开头，例如 log.Debugf("XXXXXXXXXXXX-1:Input key was: %s", setKeyName) 。这样，在完成调试后，可以通过查找 XXXXXXXXXXXX 字符串，找到这些临时日志，在 commit 前删除。
- 日志内容应该小写字母开头，以英文点号 . 结尾，例如 log.Info("update user function called.") 。
- 为了提高性能，尽可能使用明确的类型，例如使用 log.Warnf("init datastore: %s", err.Error()) 而非 log.Warnf("init datastore: %v", err) 。
- 根据需要，日志最好包含两个信息。一个是请求 ID（RequestID），是每次请求的唯一 ID，便于从海量日志中过滤出某次请求的日志，可以将请求 ID 放在请求的通用日志字段中。另一个是用户和行为，用于标识谁做了什么。
- 不要将日志记录在错误的日志级别上。例如，我在项目开发中，经常会发现有同事将正常的日志信息打印在 Error 级别，将错误的日志信息打印在 Info 级别。



### 记录日志的“最佳”实践总结
关于日志记录问题，我从以上三个层面给你讲解了。综合来说，对于日志记录的最佳实践，你在平时都可以**注意**或进行尝试，我把这些重点放在这里，方便你后续查阅。


- 开发调试、现网故障排障时，不要遗忘一件事情：根据排障的过程优化日志打印。好的日志，可能不是一次就可以写好的，可以在实际开发测试，还有现网定位问题时，不断优化。但这需要你重视日志，而不是把日志仅仅当成记录信息的一种方式，甚至不知道为什么打印一条 Info 日志。
- 打印日志要“不多不少”，避免打印没有作用的日志，也不要遗漏关键的日志信息。最好的信息是，仅凭借这些关键的日志就能定位到问题。
- 支持动态日志输出，方便线上问题定位。
- 总是将日志记录在本地文件：通过将日志记录在本地文件，可以和日志中心化平台进行解耦，这样当网络不可用，或者日志中心化平台故障时，仍然能够正常的记录日志。
- 集中化日志存储处理：因为应用可能包含多个服务，一个服务包含多个实例，为了查看日志方便，最好将这些日志统一存储在同一个日志平台上，例如 Elasticsearch，方便集中管理和查看日志。
- 结构化日志记录：添加一些默认通用的字段到每行日志，方便日志查询和分析。
- 支持 RequestID：使用 RequestID 串联一次请求的所有日志，这些日志可能分布在不同的组件，不同的机器上。支持 RequestID 可以大大提高排障的效率，降低排障难度。在一些大型分布式系统中，没有 RequestID 排障简直就是灾难。
- 支持动态开关 Debug 日志：对于定位一些隐藏得比较深的问题，可能需要更多的信息，这时候可能需要打印 Debug 日志。但现网的日志级别会设置为 Info 级别，为了获取 Debug 日志，我们可能会修改日志级别为 Debug 级别并重启服务，定位完问题后，再修改日志级别为 Info 级别，然后再重启服务，这种方式不仅麻烦而且还可能会对现网业务造成影响，最好的办法是能够在请求中通过 debug=true 这类参数动态控制某次请求是否开启 Debug 日志。



### 拓展内容：分布式日志解决方案（EFK/ELK）
前面我们介绍了设计日志包和记录日志的规范，除此之外，还有一个问题你应该了解，那就是：我们记录的日志如何收集、处理和展示。

在实际 Go 项目开发中，为了实现高可用，同一个服务至少需要部署两个实例，通过轮询的负载均衡策略转发请求。另外，一个应用又可能包含多个服务。假设我们的应用包含两个服务，每个服务部署两个实例，如果应用出故障，我们可能需要登陆 4（2 x 2）台服务器查看本地的日志文件，定位问题，非常麻烦，会增加故障恢复时间。所以在真实的企业场景中，我们会将这些日志统一收集并展示。


在业界，日志的收集、处理和展示，早已经有了一套十分流行的日志解决方案：EFK（Elasticsearch + Filebeat + Kibana）或者 ELK（Elasticsearch + Logstash + Kibana），EFK 可以理解为 ELK 的演进版，把日志收集组件从 Logstash 替换成了 Filebeat。用 Filebeat 替换 Logstash，主要原因是 Filebeat 更轻量级，占用的资源更少。关于日志处理架构，你可以参考这张图。


![img](https://static001.geekbang.org/resource/image/5d/c8/5daabdfea213c05fc0387aa735e54ec8.png?wh=4194x778)

通过 log 包将日志记录在本地文件中（*.log 文件），再通过 Shipper 收集到 Kafka 中。Shipper 可以根据需要灵活选择，常见的 Shipper 有 Logstash Shipper、Flume、Fluentd、Filebeat。其中 Filebeat 和 Logstash Shipper 用得最多。Shipper 没有直接将日志投递到 Logstash indexer，或者 Elasticsearch，是因为 Kafka 能够支持更大的吞吐量，起到削峰填谷的作用。

Kafka 中的日志消息会被 Logstash indexer 消费，处理后投递到 Elasticsearch 中存储起来。Elasticsearch 是实时全文搜索和分析引擎，提供搜集、分析、存储数据三大功能。Elasticsearch 中存储的日志，可以通过 Kibana 提供的图形界面来展示。Kibana 是一个基于 Web 的图形界面，用于搜索、分析和可视化存储在 Elasticsearch 中的日志数据。

Logstash 负责采集、转换和过滤日志。它支持几乎任何类型的日志，包括系统日志、错误日志和自定义应用程序日志。Logstash 又分为 Logstash Shipper 和 Logstash indexer。其中，Logstash Shipper 监控并收集日志，并将日志内容发送到 Logstash indexer，然后 Logstash indexer 过滤日志，并将日志提交给 Elasticsearch。


### 总结
记录日志，是应用程序必备的功能。记录日志最大的作用是排障，如果想更好地排障，我们需要一个优秀的工具，日志包。那么如何设计日志包呢？首先我们需要知道日志包的功能，在我看来日志包需要具备以下功能：


- 基础功能：支持基本的日志信息、支持不同的日志级别、支持自定义配置、支持输出到标准输出和文件。
- 高级功能：支持多种日志格式、能够按级别分类输出、支持结构化日志、支持日志轮转、具备 Hook 能力。
- 可选功能：支持颜色输出、兼容标准库 log 包、支持输出到不同的位置。


另外，一个日志包还需要支持不同级别的日志，按日志级别优先级从低到高分别是：Trace < Debug < Info < Warn/Warning < Error < Panic < Fatal。其中 Debug、Info、Warn、Error、Fatal 是比较基础的级别，**建议**在开发一个日志包时包含这些级别。Trace、Panic 是可选的级别。


在我们掌握了日志包的功能之后，就可以设计、开发日志包了。但我们在开发过程中，还需要确保我们的日志包具有比较高的性能、并发安全、支持插件化的能力，并支持日志参数控制。有了日志包，我们还需要知道如何更好地使用日志包，也就是如何记录日志。在文中，我给出了一些记录**建议**，内容比较多，你可以返回文中查看。

最后，我还给出了分布式日志解决方案：EFK/ELK。EFK 是 ELK 的升级版，在实际项目开发中，我们可以直接选择 EFK。在 EFK 方案中，通过 Filebeat 将日志上传到 Kafka，Logstash indexer 消费 Kafka 中的日志，并投递到 Elasticsearch 中存储起来，最后通过 Kibana 图形界面来查看日志。

## 21 | 日志处理（下）：手把手教你从 0 编写一个日志包

在实际开发中，我们可以选择一些优秀的开源日志包，不加修改直接拿来使用。但更多时候，是基于一个或某几个优秀的开源日志包进行二次开发。想要开发或者二次开发一个日志包，就要掌握日志包的实现方式。那么这一讲中，我来带你从 0 到 1，实现一个具备基本功能的日志包，让你从中一窥日志包的实现原理和实现方法。在开始实战之前，我们先来看下目前业界有哪些优秀的开源日志包。


### 有哪些优秀的开源日志包？
在 Go 项目开发中，我们可以通过修改一些优秀的开源日志包，来实现项目的日志包。Go 生态中有很多优秀的开源日志包，例如标准库 log 包、glog、logrus、zap、seelog、zerolog、log15、apex/log、go-logging 等。其中，用得比较多的是标准库 log 包、glog、logrus 和 zap。


为了使你了解开源日志包的现状，接下来我会简单介绍下这几个常用的日志包。至于它们的具体使用方法，你可以参考我整理的一篇文章：[优秀开源日志包使用教程](https://github.com/marmotedu/geekbang-go/blob/master/%E4%BC%98%E7%A7%80%E5%BC%80%E6%BA%90%E6%97%A5%E5%BF%97%E5%8C%85%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B.md)。


#### 标准库 log 包
标准库 log 包的功能非常简单，只提供了 Print、Panic 和 Fatal 三类函数用于日志输出。因为是标准库自带的，所以不需要我们下载安装，使用起来非常方便。标准库 log 包只有不到 400 行的代码量，如果你想研究如何实现一个日志包，阅读标准库 log 包是一个不错的开始。Go 的标准库大量使用了 log 包，例如net/http 、 net/rpc 等。


#### glog
glog是 Google 推出的日志包，跟标准库 log 包一样，它是一个轻量级的日志包，使用起来简单方便。但 glog 比标准库 log 包提供了更多的功能，它具有如下特性：

- 支持 4 种日志级别：Info、Warning、Error、Fatal。
- 支持命令行选项，例如-alsologtostderr、-log_backtrace_at、-log_dir、-logtostderr、-v等，每个参数实现某种功能。
- 支持根据文件大小切割日志文件。
- 支持日志按级别分类输出。
- 支持 V level。V level 特性可以使开发者自定义日志级别。
- 支持 vmodule。vmodule 可以使开发者对不同的文件使用不同的日志级别。
- 支持 traceLocation。traceLocation 可以打印出指定位置的栈信息。

Kubernetes 项目就使用了基于 glog 封装的 klog，作为其日志库。


#### logrus
logrus是目前 GitHub 上 star 数量最多的日志包，它的优点是功能强大、性能高效、高度灵活，还提供了自定义插件的功能。很多优秀的开源项目，例如 Docker、Prometheus 等，都使用了 logrus。除了具有日志的基本功能外，logrus 还具有如下特性：


- 支持常用的日志级别。logrus 支持 Debug、Info、Warn、Error、Fatal 和 Panic 这些日志级别。
- 可扩展。logrus 的 Hook 机制允许使用者通过 Hook 的方式，将日志分发到任意地方，例如本地文件、标准输出、Elasticsearch、Logstash、Kafka 等。
- 支持自定义日志格式。logrus 内置了 JSONFormatter 和 TextFormatter 两种格式。除此之外，logrus 还允许使用者通过实现 Formatter 接口，来自定义日志格式。
- 结构化日志记录。logrus 的 Field 机制允许使用者自定义日志字段，而不是通过冗长的消息来记录日志。
- 预设日志字段。logrus 的 Default Fields 机制，可以给一部分或者全部日志统一添加共同的日志字段，例如给某次 HTTP 请求的所有日志添加 X-Request-ID 字段。
- Fatal handlers。logrus 允许注册一个或多个 handler，当产生 Fatal 级别的日志时调用。当我们的程序需要优雅关闭时，这个特性会非常有用。


#### zap
zap是 uber 开源的日志包，以高性能著称，很多公司的日志包都是基于 zap 改造而来。除了具有日志基本的功能之外，zap 还具有很多强大的特性：


- 支持常用的日志级别，例如：Debug、Info、Warn、Error、DPanic、Panic、Fatal。
- 性能非常高。zap 具有非常高的性能，适合对性能要求比较高的场景。
- 支持针对特定的日志级别，输出调用堆栈。
- 像 logrus 一样，zap 也支持结构化的目录日志、预设日志字段，也因为支持 Hook 而具有可扩展性。


### 开源日志包选择
上面我介绍了很多日志包，每种日志包使用的场景不同，你可以根据自己的需求，结合日志包的特性进行选择：


- 标准库 log 包： 标准库 log 包不支持日志级别、日志分割、日志格式等功能，所以在大型项目中很少直接使用，通常用于一些短小的程序，比如用于生成 JWT Token 的 main.go 文件中。标准库日志包也很适合一些简短的代码，用于快速调试和验证。
- glog： glog 实现了日志包的基本功能，非常适合一些对日志功能要求不多的小型项目。
- logrus： logrus 功能强大，不仅实现了日志包的基本功能，还有很多高级特性，适合一些大型项目，尤其是需要结构化日志记录的项目。
- zap： zap 提供了很强大的日志功能，性能高，内存分配次数少，适合对日志性能要求很高的项目。另外，zap 包中的子包 zapcore，提供了很多底层的日志接口，适合用来做二次封装。


举个我自己选择日志包来进行二次开发的例子：我在做容器云平台开发时，发现 Kubernetes 源码中大量使用了 glog，这时就需要日志包能够兼容 glog。于是，我基于 zap 和 zapcore 封装了github.com/marmotedu/iam/pkg/log日志包，这个日志包可以很好地兼容 glog。


在实际项目开发中，你可以根据项目需要，从上面几个日志包中进行选择，直接使用，但更多时候，你还需要基于这些包来进行定制开发。为了使你更深入地掌握日志包的设计和开发，接下来，我会从 0 到 1 带你开发一个日志包。


### 从 0 编写一个日志包
接下来，我会向你展示如何快速编写一个具备基本功能的日志包，让你通过这个简短的日志包实现掌握日志包的核心设计思路。该日志包主要实现以下几个功能：


- 支持自定义配置。
- 支持文件名和行号。
- 支持日志级别 Debug、Info、Warn、Error、Panic、Fatal。
- 支持输出到本地文件和标准输出。
- 支持 JSON 和 TEXT 格式的日志输出，支持自定义日志格式。
- 支持选项模式。


日志包名称为cuslog，示例项目完整代码存放在 cuslog。具体实现分为以下四个步骤：

- 定义：定义日志级别和日志选项。
- 创建：创建 Logger 及各级别日志打印方法。
- 写入：将日志输出到支持的输出中。
- 自定义：自定义日志输出格式。


#### 定义日志级别和日志选项
一个基本的日志包，首先需要定义好日志级别和日志选项。本示例将定义代码保存在options.go文件中。


```go
type Level uint8

const (
    DebugLevel Level = iota
    InfoLevel
    WarnLevel
    ErrorLevel
    PanicLevel
    FatalLevel
)

var LevelNameMapping = map[Level]string{
    DebugLevel: "DEBUG",
    InfoLevel:  "INFO",
    WarnLevel:  "WARN",
    ErrorLevel: "ERROR",
    PanicLevel: "PANIC",
    FatalLevel: "FATAL",
}
```

在日志输出时，要通过对比开关级别和输出级别的大小，来决定是否输出，所以日志级别 Level 要定义成方便比较的数值类型。几乎所有的日志包都是用常量计数器 iota 来定义日志级别。另外，因为要在日志输出中，输出可读的日志级别（例如输出 INFO 而不是 1），所以需要有 Level 到 Level Name 的映射 LevelNameMapping，LevelNameMapping 会在格式化时用到。



接下来看定义日志选项。日志需要是可配置的，方便开发者根据不同的环境设置不同的日志行为，比较常见的配置选项为：日志级别。输出位置，例如标准输出或者文件。输出格式，例如 JSON 或者 Text。是否开启文件名和行号。


本示例的日志选项定义如下：


```go
type options struct {
    output        io.Writer
    level         Level
    stdLevel      Level
    formatter     Formatter
    disableCaller bool
}
```
为了灵活地设置日志的选项，你可以通过选项模式，来对日志选项进行设置：


```go
type Option func(*options)

func initOptions(opts ...Option) (o *options) {
    o = &options{}
    for _, opt := range opts {
        opt(o)
    }

    if o.output == nil {
        o.output = os.Stderr
    }

    if o.formatter == nil {
        o.formatter = &TextFormatter{}
    }

    return
}

func WithLevel(level Level) Option {
    return func(o *options) {
        o.level = level
    }
}
...
func SetOptions(opts ...Option) {
    std.SetOptions(opts...)
}

func (l *logger) SetOptions(opts ...Option) {
    l.mu.Lock()
    defer l.mu.Unlock()

    for _, opt := range opts {
        opt(l.opt)
    }
}
```

具有选项模式的日志包，可通过以下方式，来动态地修改日志的选项：


```
cuslog.SetOptions(cuslog.WithLevel(cuslog.DebugLevel))
```
你可以根据需要，对每一个日志选项创建设置函数 WithXXXX 。这个示例日志包支持如下选项设置函数：
- WithOutput（output io.Writer）：设置输出位置。
- WithLevel（level Level）：设置输出级别。
- WithFormatter（formatter Formatter）：设置输出格式。
- WithDisableCaller（caller bool）：设置是否打印文件名和行号。


#### 创建 Logger 及各级别日志打印方法
为了打印日志，我们需要根据日志配置，创建一个 Logger，然后通过调用 Logger 的日志打印方法，完成各级别日志的输出。本示例将创建代码保存在logger.go文件中。


可以通过如下方式创建 Logger：



```go
var std = New()

type logger struct {
    opt       *options
    mu        sync.Mutex
    entryPool *sync.Pool
}

func New(opts ...Option) *logger {
    logger := &logger{opt: initOptions(opts...)}
    logger.entryPool = &sync.Pool{New: func() interface{} { return entry(logger) }}
    return logger
}
```
上述代码中，定义了一个 Logger，并实现了创建 Logger 的 New 函数。日志包都会有一个默认的全局 Logger，本示例通过 var std = New() 创建了一个全局的默认 Logger。cuslog.Debug、cuslog.Info 和 cuslog.Warnf 等函数，则是通过调用 std Logger 所提供的方法来打印日志的。


定义了一个 Logger 之后，还需要给该 Logger 添加最核心的日志打印方法，要提供所有支持级别的日志打印方法。如果日志级别是 Xyz，则通常需要提供两类方法，分别是非格式化方法Xyz(args ...interface{})和格式化方法Xyzf(format string, args ...interface{})，例如：



```go
func (l *logger) Debug(args ...interface{}) {
    l.entry().write(DebugLevel, FmtEmptySeparate, args...)
}
func (l *logger) Debugf(format string, args ...interface{}) {
    l.entry().write(DebugLevel, format, args...)
}

```

本示例实现了如下方法：Debug、Debugf、Info、Infof、Warn、Warnf、Error、Errorf、Panic、Panicf、Fatal、Fatalf。更详细的实现，你可以参考 cuslog/logger.go。这里要**注意**，Panic、Panicf 要调用 panic() 函数，Fatal、Fatalf 函数要调用 os.Exit(1) 函数。


#### 将日志输出到支持的输出中
调用日志打印函数之后，还需要将这些日志输出到支持的输出中，所以需要实现 write 函数，它的写入逻辑保存在entry.go文件中。实现方式如下：


```go
type Entry struct {
    logger *logger
    Buffer *bytes.Buffer
    Map    map[string]interface{}
    Level  Level
    Time   time.Time
    File   string
    Line   int
    Func   string
    Format string
    Args   []interface{}
}

func (e *Entry) write(level Level, format string, args ...interface{}) {
    if e.logger.opt.level > level {
        return
    }
    e.Time = time.Now()
    e.Level = level
    e.Format = format
    e.Args = args
    if !e.logger.opt.disableCaller {
        if pc, file, line, ok := runtime.Caller(2); !ok {
            e.File = "???"
            e.Func = "???"
        } else {
            e.File, e.Line, e.Func = file, line, runtime.FuncForPC(pc).Name()
            e.Func = e.Func[strings.LastIndex(e.Func, "/")+1:]
        }
    }
    e.format()
    e.writer()
    e.release()
}

func (e *Entry) format() {
    _ = e.logger.opt.formatter.Format(e)
}

func (e *Entry) writer() {
    e.logger.mu.Lock()
    _, _ = e.logger.opt.output.Write(e.Buffer.Bytes())
    e.logger.mu.Unlock()
}

func (e *Entry) release() {
    e.Args, e.Line, e.File, e.Format, e.Func = nil, 0, "", "", ""
    e.Buffer.Reset()
    e.logger.entryPool.Put(e)
}
```
上述代码，首先定义了一个 Entry 结构体类型，该类型用来保存所有的日志信息，即日志配置和日志内容。写入逻辑都是围绕 Entry 类型的实例来完成的。用 Entry 的 write 方法来完成日志的写入，在 write 方法中，会首先判断日志的输出级别和开关级别，如果输出级别小于开关级别，则直接返回，不做任何记录。

在 write 中，还会判断是否需要记录文件名和行号，如果需要则调用 runtime.Caller() 来获取文件名和行号，调用 runtime.Caller() 时，要**注意**传入正确的栈深度。write 函数中调用 e.format 来格式化日志，调用 e.writer 来写入日志，在创建 Logger 传入的日志配置中，指定了输出位置 output io.Writer ，output 类型为 io.Writer ，示例如下：


```go
type Writer interface {
    Write(p []byte) (n int, err error)
}
```
io.Writer 实现了 Write 方法可供写入，所以只需要调用e.logger.opt.output.Write(e.Buffer.Bytes())即可将日志写入到指定的位置中。最后，会调用 release() 方法来清空缓存和对象池。至此，我们就完成了日志的记录和写入。


#### 自定义日志输出格式
cuslog 包支持自定义输出格式，并且内置了 JSON 和 Text 格式的 Formatter。Formatter 接口定义为：


```go
type Formatter interface {
    Format(entry *Entry) error
}
```
cuslog 内置的 Formatter 有两个：JSON和TEXT。


#### 测试日志包
cuslog 日志包开发完成之后，可以编写测试代码，调用 cuslog 包来测试 cuslog 包，代码如下：


```go
package main

import (
    "log"
    "os"

    "github.com/marmotedu/gopractise-demo/log/cuslog"
)

func main() {
    cuslog.Info("std log")
    cuslog.SetOptions(cuslog.WithLevel(cuslog.DebugLevel))
    cuslog.Debug("change std log to debug level")
    cuslog.SetOptions(cuslog.WithFormatter(&cuslog.JsonFormatter{IgnoreBasicFields: false}))
    cuslog.Debug("log in json format")
    cuslog.Info("another log in json format")

    // 输出到文件
    fd, err := os.OpenFile("test.log", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
    if err != nil {
        log.Fatalln("create file test.log failed")
    }
    defer fd.Close()

    l := cuslog.New(cuslog.WithLevel(cuslog.InfoLevel),
        cuslog.WithOutput(fd),
        cuslog.WithFormatter(&cuslog.JsonFormatter{IgnoreBasicFields: false}),
    )
    l.Info("custom log with json formatter")
}
```
将上述代码保存在 main.go 文件中，运行：


```
$ go run example.go
2020-12-04T10:32:12+08:00 INFO example.go:11 std log
2020-12-04T10:32:12+08:00 DEBUG example.go:13 change std log to debug level
{"file":"/home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/log/cuslog/example/example.go:15","func":"main.main","message":"log in json format","level":"DEBUG","time":"2020-12-04T10:32:12+08:00"}
{"level":"INFO","time":"2020-12-04T10:32:12+08:00","file":"/home/colin/workspace/golang/src/github.com/marmotedu/gopractise-demo/log/cuslog/example/example.go:16","func":"main.main","message":"another log in json format"}
```
到这里日志包就开发完成了，完整包见 log/cuslog。


### IAM 项目日志包设计
这一讲的最后，我们再来看下我们的 IAM 项目中，日志包是怎么设计的。

先来看一下 IAM 项目 log 包的存放位置：pkg/log。放在这个位置，主要有两个原因：第一个，log 包属于 IAM 项目，有定制开发的内容；第二个，log 包功能完备、成熟，外部项目也可以使用。


该 log 包是基于 go.uber.org/zap 包封装而来的，根据需要添加了更丰富的功能。接下来，我们通过 log 包的Options，来看下 log 包所实现的功能：



```go
type Options struct {
    OutputPaths       []string `json:"output-paths"       mapstructure:"output-paths"`
    ErrorOutputPaths  []string `json:"error-output-paths" mapstructure:"error-output-paths"`
    Level             string   `json:"level"              mapstructure:"level"`
    Format            string   `json:"format"             mapstructure:"format"`
    DisableCaller     bool     `json:"disable-caller"     mapstructure:"disable-caller"`
    DisableStacktrace bool     `json:"disable-stacktrace" mapstructure:"disable-stacktrace"`
    EnableColor       bool     `json:"enable-color"       mapstructure:"enable-color"`
    Development       bool     `json:"development"        mapstructure:"development"`
    Name              string   `json:"name"               mapstructure:"name"`
}
```
Options 各配置项含义如下：
- development：是否是开发模式。如果是开发模式，会对 DPanicLevel 进行堆栈跟踪。
- name：Logger 的名字。
- disable-caller：是否开启 caller，如果开启会在日志中显示调用日志所在的文件、函数和行号。
- disable-stacktrace：是否在 Panic 及以上级别禁止打印堆栈信息。
- enable-color：是否开启颜色输出，true，是；false，否。
- level：日志级别，优先级从低到高依次为：Debug, Info, Warn, Error, Dpanic, Panic, Fatal。
- format：支持的日志输出格式，目前支持 Console 和 JSON 两种。Console 其实就是 Text 格式。
- output-paths：支持输出到多个输出，用逗号分开。支持输出到标准输出（stdout）和文件。
- error-output-paths：zap 内部 (非业务) 错误日志输出路径，多个输出，用逗号分开。


log 包的 Options 结构体支持以下 3 个方法：
- Build 方法。Build 方法可以根据 Options 构建一个全局的 Logger。AddFlags 方法。
- AddFlags 方法可以将 Options 的各个字段追加到传入的 pflag.FlagSet 变量中。
- String 方法。String 方法可以将 Options 的值以 JSON 格式字符串返回。


log 包实现了以下 3 种日志记录方法：


```
log.Info("This is a info message", log.Int32("int_key", 10))
log.Infof("This is a formatted %s message", "info")
log.Infow("Message printed with Infow", "X-Request-ID", "fbf54504-64da-4088-9b86-67824a7fb508")
```
Info 使用指定的 key/value 记录日志。Infof 格式化记录日志。 Infow 也是使用指定的 key/value 记录日志，跟 Info 的区别是：使用 Info 需要指定值的类型，通过指定值的日志类型，日志库底层不需要进行反射操作，所以使用 Info 记录日志性能最高。


log 包支持非常丰富的类型，具体你可以参考 types.go。上述日志输出为：


```
2021-07-06 14:02:07.070 INFO This is a info message {"int_key": 10}
2021-07-06 14:02:07.071 INFO This is a formatted info message
2021-07-06 14:02:07.071 INFO Message printed with Infow {"X-Request-ID": "fbf54504-64da-4088-9b86-67824a7fb508"}
```
log 包为每种级别的日志都提供了 3 种日志记录方式，举个例子：假设日志格式为 Xyz ，则分别提供了 Xyz(msg string, fields ...Field) ，Xyzf(format string, v ...interface{}) ，Xyzw(msg string, keysAndValues ...interface{}) 3 种日志记录方法。


另外，log 包相较于一般的日志包，还提供了众多记录日志的方法。

第一个方法， log 包支持 V Level，可以通过整型数值来灵活指定日志级别，数值越大，优先级越低。例如：


```go
// V level使用
log.V(1).Info("This is a V level message")
log.V(1).Infof("This is a %s V level message", "formatted")
log.V(1).Infow("This is a V level message with fields", "X-Request-ID", "7a7b9f24-4cae-4b2a-9464-69088b45b904")
```
这里要**注意**，Log.V 只支持 Info 、Infof 、Infow三种日志记录方法。
第二个方法， log 包支持 WithValues 函数，例如：


```go
// WithValues使用
lv := log.WithValues("X-Request-ID", "7a7b9f24-4cae-4b2a-9464-69088b45b904")
lv.Infow("Info message printed with [WithValues] logger")
lv.Infow("Debug message printed with [WithValues] logger")
```

上述日志输出如下：


```
2021-07-06 14:15:28.555 INFO Info message printed with [WithValues] logger {"X-Request-ID": "7a7b9f24-4cae-4b2a-9464-69088b45b904"}
2021-07-06 14:15:28.556 INFO Debug message printed with [WithValues] logger {"X-Request-ID": "7a7b9f24-4cae-4b2a-9464-69088b45b904"}
```
WithValues 可以返回一个携带指定 key-value 的 Logger，供后面使用。

第三个方法， log 包提供 WithContext 和 FromContext 用来将指定的 Logger 添加到某个 Context 中，以及从某个 Context 中获取 Logger，例如：


```go
// Context使用
ctx := lv.WithContext(context.Background())
lc := log.FromContext(ctx)
lc.Info("Message printed with [WithContext] logger")
```

WithContext和FromContext非常适合用在以context.Context传递的函数中，例如：


```go
func main() {
 
    ...
 
    // WithValues使用
    lv := log.WithValues("X-Request-ID", "7a7b9f24-4cae-4b2a-9464-69088b45b904")
     
    // Context使用
    lv.Infof("Start to call pirntString")
    ctx := lv.WithContext(context.Background())
    pirntString(ctx, "World")  
}
 
func pirntString(ctx context.Context, str string) {
    lc := log.FromContext(ctx)
    lc.Infof("Hello %s", str)
}
```

上述代码输出如下：


```
2021-07-06 14:38:02.050 INFO Start to call pirntString {"X-Request-ID": "7a7b9f24-4cae-4b2a-9464-69088b45b904"}
2021-07-06 14:38:02.050 INFO Hello World {"X-Request-ID": "7a7b9f24-4cae-4b2a-9464-69088b45b904"}
```
将 Logger 添加到 Context 中，并通过 Context 在不同函数间传递，可以使 key-value 在不同函数间传递。例如上述代码中， X-Request-ID 在 main 函数、printString 函数中的日志输出中均有记录，从而实现了一种调用链的效果。



第四个方法， 可以很方便地从 Context 中提取出指定的 key-value，作为上下文添加到日志输出中，例如 internal/apiserver/api/v1/user/create.go文件中的日志调用：




```go
log.L(c).Info("user create function called.")
```
通过调用 Log.L() 函数，实现如下：


```go
// L method output with specified context value.
func L(ctx context.Context) *zapLogger {
    return std.L(ctx)
}
 
func (l *zapLogger) L(ctx context.Context) *zapLogger {
    lg := l.clone()
 
    requestID, _ := ctx.Value(KeyRequestID).(string)
    username, _ := ctx.Value(KeyUsername).(string)
    lg.zapLogger = lg.zapLogger.With(zap.String(KeyRequestID, requestID), zap.String(KeyUsername, username))
 
    return lg
}
```
L() 方法会从传入的 Context 中提取出 requestID 和 username ，追加到 Logger 中，并返回 Logger。这时候调用该 Logger 的 Info、Infof、Infow 等方法记录日志，输出的日志中均包含 requestID 和 username 字段，例如：


```
2021-07-06 14:46:00.743 INFO    apiserver       secret/create.go:23     create secret function called.  {"requestID": "73144bed-534d-4f68-8e8d-dc8a8ed48507", "username": "admin"}
```
通过将 Context 在函数间传递，很容易就能实现调用链效果，例如：


```go
// Create add new secret key pairs to the storage.
func (s *SecretHandler) Create(c *gin.Context) {
    log.L(c).Info("create secret function called.")
     
    ...
     
    secrets, err := s.srv.Secrets().List(c, username, metav1.ListOptions{    
        Offset: pointer.ToInt64(0),
        Limit:  pointer.ToInt64(-1),
    })
     
    ...
     
     if err := s.srv.Secrets().Create(c, &r, metav1.CreateOptions{}); err != nil {
        core.WriteResponse(c, err, nil)

        return
    }
 
    core.WriteResponse(c, nil, r)
}
```
上述代码输出为：


```
2021-07-06 14:46:00.743 INFO    apiserver       secret/create.go:23     create secret function called.  {"requestID": "73144bed-534d-4f68-8e8d-dc8a8ed48507", "username": "admin"}
2021-07-06 14:46:00.744 INFO    apiserver       secret/create.go:23     list secret from storage.  {"requestID": "73144bed-534d-4f68-8e8d-dc8a8ed48507", "username": "admin"}
2021-07-06 14:46:00.745 INFO    apiserver       secret/create.go:23     insert secret to storage.  {"requestID": "73144bed-534d-4f68-8e8d-dc8a8ed48507", "username": "admin"}
```

这里要**注意**， log.L 函数默认会从 Context 中取 requestID 和 username 键，这跟 IAM 项目有耦合度，但这不影响 log 包供第三方项目使用。这也是我**建议**你自己封装日志包的原因。


### 总结
开发一个日志包，我们很多时候需要基于一些业界优秀的开源日志包进行二次开发。当前很多项目的日志包都是基于 zap 日志包来封装的，如果你有封装的需要，我**建议**你优先选择 zap 日志包。


这一讲中，我先给你介绍了标准库 log 包、glog、logrus 和 zap 这四种常用的日志包，然后向你展现了开发一个日志包的四个步骤，步骤如下：定义日志级别和日志选项。创建 Logger 及各级别日志打印方法。将日志输出到支持的输出中。自定义日志输出格式。


最后，我介绍了 IAM 项目封装的 log 包的设计和使用方式。log 包基于 go.uber.org/zap封装，并提供了以下强大特性：
- log 包支持 V Level，可以灵活的通过整型数值来指定日志级别。
- log 包支持 WithValues 函数， WithValues 可以返回一个携带指定 key-value 对的 Logger，供后面使用。
- log 包提供 WithContext 和 FromContext 用来将指定的 Logger 添加到某个 Context 中和从某个 Context 中获取 Logger。
- log 包提供了 Log.L() 函数，可以很方便的从 Context 中提取出指定的 key-value 对，作为上下文添加到日志输出中。

## 22 | 应用构建三剑客：Pflag、Viper、Cobra 核心功能介绍

因为 IAM 项目使用了 Pflag、Viper 和 Cobra 包来构建 IAM 的应用框架，为了让你后面学习更加容易，这里简单介绍下这 3 个包的核心功能和使用方式。其实如果单独讲每个包的话，还是有很多功能可讲的，但我们这一讲的目的是减小你后面学习 IAM 源码的难度，所以我会主要介绍跟 IAM 相关的功能。在正式介绍这三个包之前，我们先来看下如何构建应用的框架。


### 如何构建应用框架
想知道如何构建应用框架，首先你要明白，一个应用框架包含哪些部分。在我看来，一个应用框架需要包含以下 3 个部分：

- 命令行参数解析：主要用来解析命令行参数，这些命令行参数可以影响命令的运行效果。
- 配置文件解析：一个大型应用，通常具有很多参数，为了便于管理和配置这些参数，通常会将这些参数放在一个配置文件中，供程序读取并解析。
- 应用的命令行框架：应用最终是通过命令来启动的。这里有 3 个需求点，一是命令需要具备 Help 功能，这样才能告诉使用者如何去使用；二是命令需要能够解析命令行参数和配置文件；三是命令需要能够初始化业务代码，并最终启动业务进程。也就是说，我们的命令需要具备框架的能力，来纳管这 3 个部分。

这 3 个部分的功能，你可以自己开发，也可以借助业界已有的成熟实现。跟之前的想法一样，我不**建议**你自己开发，更**建议**你采用业界已有的成熟实现。命令行参数可以通过Pflag来解析，配置文件可以通过Viper来解析，应用的命令行框架则可以通过Cobra来实现。这 3 个包目前也是最受欢迎的包，并且这 3 个包不是割裂的，而是有联系的，我们可以有机地组合这 3 个包，从而实现一个非常强大、优秀的应用命令行框架。


接下来，我们就来详细看下，这 3 个包在 Go 项目开发中是如何使用的。


### 命令行参数解析工具：Pflag 使用介绍
Go 服务开发中，经常需要给开发的组件加上各种启动参数来配置服务进程，影响服务的行为。像 kube-apiserver 就有多达 200 多个启动参数，而且这些参数的类型各不相同（例如：string、int、ip 类型等），使用方式也不相同（例如：需要支持--长选项，-短选项等），所以我们需要一个强大的命令行参数解析工具。

虽然 Go 源码中提供了一个标准库 Flag 包，用来对命令行参数进行解析，但在大型项目中应用更广泛的是另外一个包：Pflag。Pflag 提供了很多强大的特性，非常适合用来构建大型项目，一些耳熟能详的开源项目都是用 Pflag 来进行命令行参数解析的，例如：Kubernetes、Istio、Helm、Docker、Etcd 等。

接下来，我们就来介绍下如何使用 Pflag。Pflag 主要是通过创建 Flag 和 FlagSet 来使用的。我们先来看下 Flag。


#### Pflag 包 Flag 定义
Pflag 可以对命令行参数进行处理，一个命令行参数在 Pflag 包中会解析为一个 Flag 类型的变量。Flag 是一个结构体，定义如下：



```go
type Flag struct {
    Name                string // flag长选项的名称
    Shorthand           string // flag短选项的名称，一个缩写的字符
    Usage               string // flag的使用文本
    Value               Value  // flag的值
    DefValue            string // flag的默认值
    Changed             bool // 记录flag的值是否有被设置过
    NoOptDefVal         string // 当flag出现在命令行，但是没有指定选项值时的默认值
    Deprecated          string // 记录该flag是否被放弃
    Hidden              bool // 如果值为true，则从help/usage输出信息中隐藏该flag
    ShorthandDeprecated string // 如果flag的短选项被废弃，当使用flag的短选项时打印该信息
    Annotations         map[string][]string // 给flag设置注解
}
```

Flag 的值是一个 Value 类型的接口，Value 定义如下：


```go
type Value interface {
    String() string // 将flag类型的值转换为string类型的值，并返回string的内容
    Set(string) error // 将string类型的值转换为flag类型的值，转换失败报错
    Type() string // 返回flag的类型，例如：string、int、ip等
}

```
通过将 Flag 的值抽象成一个 interface 接口，我们就可以自定义 Flag 的类型了。只要实现了 Value 接口的结构体，就是一个新类型。


#### Pflag 包 FlagSet 定义
Pflag 除了支持单个的 Flag 之外，还支持 FlagSet。FlagSet 是一些预先定义好的 Flag 的集合，几乎所有的 Pflag 操作，都需要借助 FlagSet 提供的方法来完成。在实际开发中，我们可以使用两种方法来获取并使用 FlagSet：


方法一，调用 NewFlagSet 创建一个 FlagSet。方法二，使用 Pflag 包定义的全局 FlagSet：CommandLine。实际上 CommandLine 也是由 NewFlagSet 函数创建的。


先来看下第一种方法，自定义 FlagSet。下面是一个自定义 FlagSet 的示例：


```go
var version bool
flagSet := pflag.NewFlagSet("test", pflag.ContinueOnError)
flagSet.BoolVar(&version, "version", true, "Print version information and quit.")
```
我们可以通过定义一个新的 FlagSet 来定义命令及其子命令的 Flag。再来看下第二种方法，使用全局 FlagSet。下面是一个使用全局 FlagSet 的示例：


```go
import (
    "github.com/spf13/pflag"
)

pflag.BoolVarP(&version, "version", "v", true, "Print version information and quit.")

```
这其中，pflag.BoolVarP 函数定义如下：



```go
func BoolVarP(p *bool, name, shorthand string, value bool, usage string) {
    flag := CommandLine.VarPF(newBoolValue(value, p), name, shorthand, usage)
    flag.NoOptDefVal = "true"
}
```
可以看到 pflag.BoolVarP 最终调用了 CommandLine，CommandLine 是一个包级别的变量，定义为：


```go
// CommandLine is the default set of command-line flags, parsed from os.Args.
var CommandLine = NewFlagSet(os.Args[0], ExitOnError)

```
在一些不需要定义子命令的命令行工具中，我们可以直接使用全局的 FlagSet，更加简单方便。


#### Pflag 使用方法
上面，我们介绍了使用 Pflag 包的两个核心结构体。接下来，我来详细介绍下 Pflag 的常见使用方法。Pflag 有很多强大的功能，我这里介绍 7 个常见的使用方法。


支持多种命令行参数定义方式。
Pflag 支持以下 4 种命令行参数定义方式：

支持长选项、默认值和使用文本，并将标志的值存储在指针中。


```go
var name = pflag.String("name", "colin", "Input Your Name")
```
支持长选项、短选项、默认值和使用文本，并将标志的值存储在指针中。


```go
var name = pflag.StringP("name", "n", "colin", "Input Your Name")
```
支持长选项、默认值和使用文本，并将标志的值绑定到变量。


```go
var name string
pflag.StringVar(&name, "name", "colin", "Input Your Name")
```

支持长选项、短选项、默认值和使用文本，并将标志的值绑定到变量。



```go
var name string
pflag.StringVarP(&name, "name", "n","colin", "Input Your Name")
```

上面的函数命名是有规则的：函数名带Var说明是将标志的值绑定到变量，否则是将标志的值存储在指针中。函数名带P说明支持短选项，否则不支持短选项。


使用Get获取参数的值。
可以使用Get来获取标志的值，代表 Pflag 所支持的类型。例如：有一个 pflag.FlagSet，带有一个名为 flagname 的 int 类型的标志，可以使用GetInt()来获取 int 值。需要**注意** flagname 必须存在且必须是 int，例如：



```go
i, err := flagset.GetInt("flagname")
```
获取非选项参数。
代码示例如下：


```go
package main

import (
    "fmt"

    "github.com/spf13/pflag"
)

var (
    flagvar = pflag.Int("flagname", 1234, "help message for flagname")
)

func main() {
    pflag.Parse()

    fmt.Printf("argument number is: %v\n", pflag.NArg())
    fmt.Printf("argument list is: %v\n", pflag.Args())
    fmt.Printf("the first argument is: %v\n", pflag.Arg(0))
}

```

执行上述代码，输出如下：


```
$ go run example1.go arg1 arg2
argument number is: 2
argument list is: [arg1 arg2]
the first argument is: arg1
```
在定义完标志之后，可以调用pflag.Parse()来解析定义的标志。解析后，可通过pflag.Args()返回所有的非选项参数，通过pflag.Arg(i)返回第 i 个非选项参数。参数下标 0 到 pflag.NArg() - 1。


指定了选项但是没有指定选项值时的默认值。
创建一个 Flag 后，可以为这个 Flag 设置pflag.NoOptDefVal。如果一个 Flag 具有 NoOptDefVal，并且该 Flag 在命令行上没有设置这个 Flag 的值，则该标志将设置为 NoOptDefVal 指定的值。例如：


```go
var ip = pflag.IntP("flagname", "f", 1234, "help message")
pflag.Lookup("flagname").NoOptDefVal = "4321"
```

上面的代码会产生结果，具体你可以参照下表：


![img](https://static001.geekbang.org/resource/image/fe/3f/fe76a52906c35b49b890225d1f5fc93f.png?wh=1428x336)


弃用标志或者标志的简写。
Pflag 可以弃用标志或者标志的简写。弃用的标志或标志简写在帮助文本中会被隐藏，并在使用不**推荐**的标志或简写时打印正确的用法提示。例如，弃用名为 logmode 的标志，并告知用户应该使用哪个标志代替：


```go
// deprecate a flag by specifying its name and a usage message
pflag.CommandLine.MarkDeprecated("logmode", "please use --log-mode instead")
```
这样隐藏了帮助文本中的 logmode，并且当使用 logmode 时，打印了Flag --logmode has been deprecated, please use --log-mode instead。


保留名为 port 的标志，但是弃用它的简写形式。


```go
pflag.IntVarP(&port, "port", "P", 3306, "MySQL service host port.")

// deprecate a flag shorthand by specifying its flag name and a usage message
pflag.CommandLine.MarkShorthandDeprecated("port", "please use --port only")
```
这样隐藏了帮助文本中的简写 P，并且当使用简写 P 时，打印了Flag shorthand -P has been deprecated, please use --port only。usage message 在此处必不可少，并且不应为空。

隐藏标志。
可以将 Flag 标记为隐藏的，这意味着它仍将正常运行，但不会显示在 usage/help 文本中。例如：隐藏名为 secretFlag 的标志，只在内部使用，并且不希望它显示在帮助文本或者使用文本中。代码如下：


```go
// hide a flag by specifying its name
pflag.CommandLine.MarkHidden("secretFlag")
```
至此，我们介绍了 Pflag 包的重要用法。接下来，我们再来看下如何解析配置文件。


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
    viper.AddConfigPath("$HOME/.iam") // 配置文件搜索路径，可以设置多个配置文件搜索路径
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


监听和重新读取配置文件。Viper 支持在运行时让应用程序实时读取配置文件，也就是热加载配置。可以通过 WatchConfig 函数热加载配置。在调用 WatchConfig 函数之前，需要确保已经添加了配置文件的搜索路径。另外，还可以为 Viper 提供一个回调函数，以便在每次发生更改时运行。这里我也给你个示例：


```go
viper.WatchConfig()
viper.OnConfigChange(func(e fsnotify.Event) {
   // 配置文件发生变更之后会调用的回调函数
  fmt.Println("Config file changed:", e.Name)
})
```
我不**建议**在实际开发中使用热加载功能，因为即使配置热加载了，程序中的代码也不一定会热加载。例如：修改了服务监听端口，但是服务没有重启，这时候服务还是监听在老的端口上，会造成不一致。


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
Viper 可以通过传入.分隔的路径来访问嵌套字段：


```go
viper.GetString("datastore.metric.host") // (返回 "127.0.0.1")
```
如果datastore.metric被直接赋值覆盖（被 Flag、环境变量、set() 方法等等），那么datastore.metric的所有子键都将变为未定义状态，它们被高优先级配置级别覆盖了。如果存在与分隔的键路径匹配的键，则直接返回其值。例如：


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
### 现代化的命令行框架：Cobra 全解
Cobra 既是一个可以创建强大的现代 CLI 应用程序的库，也是一个可以生成应用和命令文件的程序。有许多大型项目都是用 Cobra 来构建应用程序的，例如 Kubernetes、Docker、etcd、Rkt、Hugo 等。


Cobra 建立在 commands、arguments 和 flags 结构之上。commands 代表命令，arguments 代表非选项参数，flags 代表选项参数（也叫标志）。一个好的应用程序应该是易懂的，用户可以清晰地知道如何去使用这个应用程序。应用程序通常遵循如下模式：APPNAME VERB NOUN --ADJECTIVE或者APPNAME COMMAND ARG --FLAG，例如：


```
git clone URL --bare # clone 是一个命令，URL是一个非选项参数，bare是一个选项参数
```
这里，VERB 代表动词，NOUN 代表名词，ADJECTIVE 代表形容词。


Cobra 提供了两种方式来创建命令：Cobra 命令和 Cobra 库。Cobra 命令可以生成一个 Cobra 命令模板，而命令模板也是通过引用 Cobra 库来构建命令的。所以，这里我直接介绍如何使用 Cobra 库来创建命令。



#### 使用 Cobra 库创建命令
如果要用 Cobra 库编码实现一个应用程序，需要首先创建一个空的 main.go 文件和一个 rootCmd 文件，之后可以根据需要添加其他命令。具体步骤如下：


创建 rootCmd。


```
$ mkdir -p newApp2 && cd newApp2
```
通常情况下，我们会将 rootCmd 放在文件 cmd/root.go 中。



```go
var rootCmd = &cobra.Command{
  Use:   "hugo",
  Short: "Hugo is a very fast static site generator",
  Long: `A Fast and Flexible Static Site Generator built with
                love by spf13 and friends in Go.
                Complete documentation is available at http://hugo.spf13.com`,
  Run: func(cmd *cobra.Command, args []string) {
    // Do Stuff Here
  },
}

func Execute() {
  if err := rootCmd.Execute(); err != nil {
    fmt.Println(err)
    os.Exit(1)
  }
}
```

还可以在 init() 函数中定义标志和处理配置，例如 cmd/root.go。


```go
import (
  "fmt"
  "os"

  homedir "github.com/mitchellh/go-homedir"
  "github.com/spf13/cobra"
  "github.com/spf13/viper"
)

var (
    cfgFile     string
    projectBase string
    userLicense string
)

func init() {
  cobra.OnInitialize(initConfig)
  rootCmd.PersistentFlags().StringVar(&cfgFile, "config", "", "config file (default is $HOME/.cobra.yaml)")
  rootCmd.PersistentFlags().StringVarP(&projectBase, "projectbase", "b", "", "base project directory eg. github.com/spf13/")
  rootCmd.PersistentFlags().StringP("author", "a", "YOUR NAME", "Author name for copyright attribution")
  rootCmd.PersistentFlags().StringVarP(&userLicense, "license", "l", "", "Name of license for the project (can provide `licensetext` in config)")
  rootCmd.PersistentFlags().Bool("viper", true, "Use Viper for configuration")
  viper.BindPFlag("author", rootCmd.PersistentFlags().Lookup("author"))
  viper.BindPFlag("projectbase", rootCmd.PersistentFlags().Lookup("projectbase"))
  viper.BindPFlag("useViper", rootCmd.PersistentFlags().Lookup("viper"))
  viper.SetDefault("author", "NAME HERE <EMAIL ADDRESS>")
  viper.SetDefault("license", "apache")
}

func initConfig() {
  // Don't forget to read config either from cfgFile or from home directory!
  if cfgFile != "" {
    // Use config file from the flag.
    viper.SetConfigFile(cfgFile)
  } else {
    // Find home directory.
    home, err := homedir.Dir()
    if err != nil {
      fmt.Println(err)
      os.Exit(1)
    }

    // Search config in home directory with name ".cobra" (without extension).
    viper.AddConfigPath(home)
    viper.SetConfigName(".cobra")
  }

  if err := viper.ReadInConfig(); err != nil {
    fmt.Println("Can't read config:", err)
    os.Exit(1)
  }
}
```

创建 main.go。我们还需要一个 main 函数来调用 rootCmd，通常我们会创建一个 main.go 文件，在 main.go 中调用 rootCmd.Execute() 来执行命令：


```go
package main

import (
  "{pathToYourApp}/cmd"
)

func main() {
  cmd.Execute()
}
```
需要**注意**，main.go 中不**建议**放很多代码，通常只需要调用 cmd.Execute() 即可。



添加命令。除了 rootCmd，我们还可以调用 AddCommand 添加其他命令，通常情况下，我们会把其他命令的源码文件放在 cmd/ 目录下，例如，我们添加一个 version 命令，可以创建 cmd/version.go 文件，内容为：



```go
package cmd

import (
  "fmt"

  "github.com/spf13/cobra"
)

func init() {
  rootCmd.AddCommand(versionCmd)
}

var versionCmd = &cobra.Command{
  Use:   "version",
  Short: "Print the version number of Hugo",
  Long:  `All software has versions. This is Hugo's`,
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hugo Static Site Generator v0.9 -- HEAD")
  },
}

```
本示例中，我们通过调用rootCmd.AddCommand(versionCmd)给 rootCmd 命令添加了一个 versionCmd 命令。


编译并运行。将 main.go 中{pathToYourApp}替换为对应的路径，例如本示例中 pathToYourApp 为github.com/marmotedu/gopractise-demo/cobra/newApp2。


```
$ go mod init github.com/marmotedu/gopractise-demo/cobra/newApp2
$ go build -v .
$ ./newApp2 -h
A Fast and Flexible Static Site Generator built with
love by spf13 and friends in Go.
Complete documentation is available at http://hugo.spf13.com
 
Usage:
hugo [flags]
hugo [command]
 
Available Commands:
help Help about any command
version Print the version number of Hugo
 
Flags:
-a, --author string Author name for copyright attribution (default "YOUR NAME")
--config string config file (default is $HOME/.cobra.yaml)
-h, --help help for hugo
-l, --license licensetext Name of license for the project (can provide licensetext in config)
-b, --projectbase string base project directory eg. github.com/spf13/
--viper Use Viper for configuration (default true)
 
Use "hugo [command] --help" for more information about a command.
```

通过步骤一、步骤二、步骤三，我们就成功创建和添加了 Cobra 应用程序及其命令。接下来，我再来详细介绍下 Cobra 的核心特性。



#### 使用标志
Cobra 可以跟 Pflag 结合使用，实现强大的标志功能。使用步骤如下：


使用持久化的标志。标志可以是“持久的”，这意味着该标志可用于它所分配的命令以及该命令下的每个子命令。可以在 rootCmd 上定义持久标志：


```go
rootCmd.PersistentFlags().BoolVarP(&Verbose, "verbose", "v", false, "verbose output")
```
使用本地标志。也可以分配一个本地标志，本地标志只能在它所绑定的命令上使用：


```go
rootCmd.Flags().StringVarP(&Source, "source", "s", "", "Source directory to read from")
```
--source标志只能在 rootCmd 上引用，而不能在 rootCmd 的子命令上引用。


将标志绑定到 Viper。我们可以将标志绑定到 Viper，这样就可以使用 viper.Get() 获取标志的值。


```go
var author string

func init() {
  rootCmd.PersistentFlags().StringVar(&author, "author", "YOUR NAME", "Author name for copyright attribution")
  viper.BindPFlag("author", rootCmd.PersistentFlags().Lookup("author"))
}
```

设置标志为必选。默认情况下，标志是可选的，我们也可以设置标志为必选，当设置标志为必选，但是没有提供标志时，Cobra 会报错。


```go
rootCmd.Flags().StringVarP(&Region, "region", "r", "", "AWS region (required)")
rootCmd.MarkFlagRequired("region")
```
#### 非选项参数验证
在命令的过程中，经常会传入非选项参数，并且需要对这些非选项参数进行验证，Cobra 提供了机制来对非选项参数进行验证。可以使用 Command 的 Args 字段来验证非选项参数。Cobra 也内置了一些验证函数：


- NoArgs：如果存在任何非选项参数，该命令将报错。
- ArbitraryArgs：该命令将接受任何非选项参数。
- OnlyValidArgs：如果有任何非选项参数不在 Command 的 ValidArgs 字段中，该命令将报错。
- MinimumNArgs(int)：如果没有至少 N 个非选项参数，该命令将报错。
- MaximumNArgs(int)：如果有多于 N 个非选项参数，该命令将报错。
- ExactArgs(int)：如果非选项参数个数不为 N，该命令将报错。
- ExactValidArgs(int)：如果非选项参数的个数不为 N，或者非选项参数不在 Command 的 ValidArgs 字段中，该命令将报错。
- RangeArgs(min, max)：如果非选项参数的个数不在 min 和 max 之间，该命令将报错。


使用预定义验证函数，示例如下：


```go
var cmd = &cobra.Command{
  Short: "hello",
  Args: cobra.MinimumNArgs(1), // 使用内置的验证函数
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hello, World!")
  },
}
```
当然你也可以自定义验证函数，示例如下：


```go
var cmd = &cobra.Command{
  Short: "hello",
  // Args: cobra.MinimumNArgs(10), // 使用内置的验证函数
  Args: func(cmd *cobra.Command, args []string) error { // 自定义验证函数
    if len(args) < 1 {
      return errors.New("requires at least one arg")
    }
    if myapp.IsValidColor(args[0]) {
      return nil
    }
    return fmt.Errorf("invalid color specified: %s", args[0])
  },
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hello, World!")
  },
}
```

#### PreRun and PostRun Hooks
在运行 Run 函数时，我们可以运行一些钩子函数，比如 PersistentPreRun 和 PreRun 函数在 Run 函数之前执行，PersistentPostRun 和 PostRun 在 Run 函数之后执行。如果子命令没有指定Persistent*Run函数，则子命令将会继承父命令的Persistent*Run函数。这些函数的运行顺序如下：


1. PersistentPreRun
2. PreRun
3. Run
4. PostRun
5. PersistentPostRun


**注意**，父级的 PreRun 只会在父级命令运行时调用，子命令是不会调用的。Cobra 还支持很多其他有用的特性，比如：自定义 Help 命令；可以自动添加--version标志，输出程序版本信息；当用户提供无效标志或无效命令时，Cobra 可以打印出 usage 信息；当我们输入的命令有误时，Cobra 会根据注册的命令，推算出可能的命令，等等。


### 总结
在开发 Go 项目时，我们可以通过 Pflag 来解析命令行参数，通过 Viper 来解析配置文件，用 Cobra 来实现命令行框架。你可以通过 pflag.String()、 pflag.StringP()、pflag.StringVar()、pflag.StringVarP() 方法来设置命令行参数，并使用 Get来获取参数的值。


同时，你也可以使用 Viper 从命令行参数、环境变量、配置文件等位置读取配置项。最常用的是从配置文件中读取，可以通过 viper.AddConfigPath 来设置配置文件搜索路径，通过 viper.SetConfigFile 和 viper.SetConfigType 来设置配置文件名，通过 viper.ReadInConfig 来读取配置文件。读取完配置文件，然后在程序中使用 Get/Get来读取配置项的值。最后，你可以使用 Cobra 来构建一个命令行框架，Cobra 可以很好地集成 Pflag 和 Viper。

## 23 | 应用构建实战：如何构建一个优秀的企业应用框架？

应用开发是软件开发工程师最核心的工作。在我这 7 年的 Go 开发生涯中，我构建了大大小小不下 50 个后端应用，深谙其中的痛点，比如：**重复造轮子**。同样的功能却每次都要重新开发，浪费非常多的时间和精力不说，每次实现的代码质量更是参差不齐。**理解成本高**。相同的功能，有 N 个服务对应着 N 种不同的实现方式，如果功能升级，或者有新成员加入，都可能得重新理解 N 次。**功能升级的开发工作量大**。一个应用由 N 个服务组成，如果要升级其中的某个功能，你需要同时更新 N 个服务的代码。


**想要解决上面这些问题，一个比较好的思路是：找出相同的功能，然后用一种优雅的方式去实现它，并通过 Go 包的形式，供所有的服务使用。**

我会带你找出服务的通用功能，并给出优雅的构建方式，帮助你一劳永逸地解决这些问题。在提高开发效率的同时，也能提高你的代码质量。

接下来，我们先来分析并找出 Go 服务通用的功能。



### 构建应用的基础：应用的三大基本功能
我们目前见到的 Go 后端服务，基本上可以分为 API 服务和非 API 服务两类。


- API 服务：通过对外提供 HTTP/RPC 接口来完成指定的功能。比如订单服务，通过调用创建订单的 API 接口，来创建商品订单。
- 非 API 服务：通过监听、定时运行等方式，而不是通过 API 调用来完成某些任务。比如数据处理服务，定时从 Redis 中获取数据，处理后存入后端存储中。再比如消息处理服务，监听消息队列（如 NSQ/Kafka/RabbitMQ），收到消息后进行处理。


对于 API 服务和非 API 服务来说，它们的启动流程基本一致，都可以分为三步：**应用框架的构建，这是最基础的一步。应用初始化。服务启动。**

如下图所示：


![img](https://static001.geekbang.org/resource/image/2a/9e/2a4ef770df3df2439ae595ef301e4d9e.png?wh=3075x1819)

图中，命令行程序、命令行参数解析和配置文件解析，是所有服务都需要具备的功能，这些功能有机结合到一起，共同构成了应用框架。所以，我们要构建的任何一个应用程序，至少要具备**命令行程序、命令行参数解析和配置文件解析**这 3 种功能。

命令行程序：用来启动一个应用。命令行程序需要实现诸如应用描述、help、参数校验等功能。根据需要，还可以实现命令自动补全、打印命令行参数等高级功能。命令行参数解析：用来在启动时指定应用程序的命令行参数，以控制应用的行为。配置文件解析：用来解析不同格式的配置文件。

另外，上述 3 类功能跟业务关系不大，可以抽象成一个统一的框架。应用初始化、创建 API/ 非 API 服务、启动服务，跟业务联系比较紧密，难以抽象成一个统一的框架。



### iam-apiserver 是如何构建应用框架的？
这里，我通过讲解 iam-apiserver 的应用构建方式，来给你讲解下如何构建应用。iam-apiserver 程序的 main 函数位于 apiserver.go 文件中，其构建代码可以简化为：


```go
import (
    ...
    "github.com/marmotedu/iam/internal/apiserver"
    "github.com/marmotedu/iam/pkg/app"
)

func main() {
    ...
    apiserver.NewApp("iam-apiserver").Run()
}

const commandDesc = `The IAM API server validates and configures data ...`

// NewApp creates a App object with default parameters.
func NewApp(basename string) *app.App {
    opts := options.NewOptions()
    application := app.NewApp("IAM API Server",
        basename,
        app.WithOptions(opts),
        app.WithDescription(commandDesc),
        app.WithDefaultValidArgs(),
        app.WithRunFunc(run(opts)),
    )

    return application
}

func run(opts *options.Options) app.RunFunc {
    return func(basename string) error {
        log.Init(opts.Log)
        defer log.Flush()

        cfg, err := config.CreateConfigFromOptions(opts)
        if err != nil {
            return err
        }

        return Run(cfg)
    }
}
```
可以看到，我们是通过调用包 github.com/marmotedu/iam/pkg/app 来构建应用的。也就是说，我们将构建应用的功能抽象成了一个 Go 包，通过 Go 包可以提高代码的封装性和复用性。iam-authz-server 和 iam-pump 组件也都是通过 github.com/marmotedu/iam/pkg/app 来构建应用的。



构建应用的流程也很简单，只需要创建一个 application 实例即可：



```go
opts := options.NewOptions()
application := app.NewApp("IAM API Server",
    basename,
    app.WithOptions(opts),
    app.WithDescription(commandDesc),
    app.WithDefaultValidArgs(),
    app.WithRunFunc(run(opts)),
)
```
在创建应用实例时，我传入了下面这些参数。IAM API Server：应用的简短描述。basename：应用的二进制文件名。opts：应用的命令行选项。commandDesc：应用的详细描述。run(opts)：应用的启动函数，初始化应用，并最终启动 HTTP 和 GRPC Web 服务。


创建应用时，你还可以根据需要来配置应用实例，比如 iam-apiserver 组件在创建应用时，指定了 WithDefaultValidArgs 来校验命令行非选项参数的默认校验逻辑。


可以看到，iam-apiserver 通过简单的几行代码，就创建出了一个应用。**之所以这么方便，是因为应用框架的构建代码都封装在了 github.com/marmotedu/iam/pkg/app 包中。**接下来，我们来重点看下 github.com/marmotedu/iam/pkg/app 包是如何实现的。为了方便描述，我在下文中统称为 App 包。


#### App 包设计和实现
我们先来看下 App 包目录下的文件：


```
[colin@dev iam]$ ls pkg/app/
app.go  cmd.go  config.go  doc.go  flag.go  help.go  options.go
```
pkg/app 目录下的 5 个主要文件是 app.go、cmd.go、config.go、flag.go、options.go，分别实现了应用程序框架中的应用、命令行程序、命令行参数解析、配置文件解析和命令行选项 5 个部分，具体关系如下图所示：


![img](https://static001.geekbang.org/resource/image/ea/12/ea3a0e20854b23fce7a654ce5f207512.png?wh=2378x828)

我再来解释下这张图。应用由命令行程序、命令行参数解析、配置文件解析三部分组成，命令行参数解析功能通过命令行选项来构建，二者通过接口解耦合：


```go
type CliOptions interface {    
    // AddFlags adds flags to the specified FlagSet object.    
    // AddFlags(fs *pflag.FlagSet)    
    Flags() (fss cliflag.NamedFlagSets)    
    Validate() []error    
}    
```
通过接口，应用可以定制自己独有的命令行参数。接下来，我们再来看下如何具体构建应用的每一部分。


#### 第 1 步：构建应用
APP 包提供了 NewApp 函数来创建一个应用：



```go
func NewApp(name string, basename string, opts ...Option) *App {
    a := &App{
        name:     name,
        basename: basename,
    }
 
    for _, o := range opts {
        o(a)
    }
 
    a.buildCommand()
 
    return a
}
```
NewApp 中使用了设计模式中的选项模式，来动态地配置 APP，支持 WithRunFunc、WithDescription、WithValidArgs 等选项。


#### 第 2 步：命令行程序构建
这一步，我们会使用 Cobra 包来构建应用的命令行程序。

NewApp 最终会调用 buildCommand 方法来创建 Cobra Command 类型的命令，命令的功能通过指定 Cobra Command 类型的各个字段来实现。通常可以指定：Use、Short、Long、SilenceUsage、SilenceErrors、RunE、Args 等字段。



在 buildCommand 函数中，也会根据应用的设置添加不同的命令行参数，例如：



```go
if !a.noConfig {
    addConfigFlag(a.basename, namedFlagSets.FlagSet("global"))
} 
```
上述代码的意思是：如果我们设置了 noConfig=false，那么就会在命令行参数 global 分组中添加以下命令行选项：


```
-c, --config FILE                                                        Read configuration from specified FILE, support JSON, TOML, YAML, HCL, or Java properties formats.
```

为了更加易用和人性化，命令还具有如下 3 个功能。
- 帮助信息：执行 -h/--help 时，输出的帮助信息。通过 cmd.SetHelpFunc 函数可以指定帮助信息。
- 使用信息（可选）：当用户提供无效的标志或命令时，向用户显示“使用信息”。通过 cmd.SetUsageFunc 函数，可以指定使用信息。如果不想每次输错命令打印一大堆 usage 信息，你可以通过设置 SilenceUsage: true 来关闭掉 usage。
- 版本信息：打印应用的版本。知道应用的版本号，对故障排查非常有帮助。通过 verflag.AddFlags 可以指定版本信息。例如，App 包通过 github.com/marmotedu/component-base/pkg/version 指定了以下版本信息：



```
$ ./iam-apiserver --version
  gitVersion: v0.3.0
   gitCommit: ccc31e292f66e6bad94efb1406b5ced84e64675c
gitTreeState: dirty
   buildDate: 2020-12-17T12:24:37Z
   goVersion: go1.15.1
    compiler: gc
    platform: linux/amd64
$ ./iam-apiserver --version=raw
version.Info{GitVersion:"v0.3.0", GitCommit:"ccc31e292f66e6bad94efb1406b5ced84e64675c", GitTreeState:"dirty", BuildDate:"2020-12-17T12:24:37Z", GoVersion:"go1.15.1", Compiler:"gc", Platform:"linux/amd64"}
```
接下来，再来看下应用需要实现的另外一个重要功能，也就是命令行参数解析。


#### 第 3 步：命令行参数解析
App 包在构建应用和执行应用两个阶段来实现命令行参数解析。


我们先看构建应用这个阶段。App 包在 buildCommand 方法中通过以下代码段，给应用添加了命令行参数：




```go
var namedFlagSets cliflag.NamedFlagSets
if a.options != nil {
    namedFlagSets = a.options.Flags()
    fs := cmd.Flags()
    for _, f := range namedFlagSets.FlagSets {
        fs.AddFlagSet(f)
    }
 
    ...
}
 
if !a.noVersion {
    verflag.AddFlags(namedFlagSets.FlagSet("global"))
}
if !a.noConfig {
    addConfigFlag(a.basename, namedFlagSets.FlagSet("global"))
}
globalflag.AddGlobalFlags(namedFlagSets.FlagSet("global"), cmd.Name())
```
namedFlagSets 中引用了 Pflag 包，上述代码先通过 a.options.Flags() 创建并返回了一批 FlagSet，a.options.Flags() 函数会将 FlagSet 进行分组。通过一个 for 循环，将 namedFlagSets 中保存的 FlagSet 添加到 Cobra 应用框架中的 FlagSet 中。buildCommand 还会根据应用的配置，选择性添加一些 flag。例如，在 global 分组下添加 --version 和 --config 选项。


执行 -h 打印命令行参数如下：


```
..
 
Usage:
  iam-apiserver [flags]
 
Generic flags:
 
      --server.healthz               Add self readiness check and install /healthz router. (default true)
      --server.max-ping-count int    The max number of ping attempts when server failed to startup. (default 3)
 
...
 
Global flags:
 
  -h, --help                     help for iam-apiserver
      --version version[=true]   Print version information and quit.
```

这里有两个技巧，你可以借鉴。**第一个技巧，将 flag 分组。**


一个大型系统，可能会有很多个 flag，例如 kube-apiserver 就有 200 多个 flag，这时对 flag 分组就很有必要了。通过分组，我们可以很快地定位到需要的分组及该分组具有的标志。例如，我们想了解 MySQL 有哪些标志，可以找到 MySQL 分组：


```
Mysql flags:
 
      --mysql.database string
                Database name for the server to use.
      --mysql.host string
                MySQL service host address. If left blank, the following related mysql options will be ignored. (default "127.0.0.1:3306")
      --mysql.log-mode int
                Specify gorm log level. (default 1)
      ...

```
**第二个技巧，flag 的名字带有层级关系**。这样不仅可以知道该 flag 属于哪个分组，而且能够避免重名。例如：


```
$ ./iam-apiserver -h |grep host
      --mysql.host string                         MySQL service host address. If left blank, the following related mysql options will be ignored. (default "127.0.0.1:3306")
      --redis.host string                   Hostname of your Redis server. (default "127.0.0.1")
```
对于 MySQL 和 Redis， 都可以指定相同的 host 标志，通过 --mysql.host 也可以知道该 flag 隶属于 mysql 分组，代表的是 MySQL 的 host。


我们再看应用执行阶段。这时会通过 viper.Unmarshal，将配置 Unmarshal 到 Options 变量中。这样我们就可以使用 Options 变量中的值，来执行后面的业务逻辑。我们传入的 Options 是一个实现了 CliOptions 接口的结构体变量，CliOptions 接口定义为：


```go
type CliOptions interface {
    Flags() (fss cliflag.NamedFlagSets)
    Validate() []error
}
```
因为 Options 实现了 Validate 方法，所以我们就可以在应用框架中调用 Validate 方法来校验参数是否合法。另外，我们还可以通过以下代码，来判断选项是否可补全和打印：如果可以补全，则补全选项；如果可以打印，则打印选项的内容。实现代码如下：



```go
func (a *App) applyOptionRules() error {
    if completeableOptions, ok := a.options.(CompleteableOptions); ok {  
        if err := completeableOptions.Complete(); err != nil {
            return err                     
        }                                                    
    }                    
                                                                               
    if errs := a.options.Validate(); len(errs) != 0 {                           
        return errors.NewAggregate(errs)                                            
    }                                                            
                                        
    if printableOptions, ok := a.options.(PrintableOptions); ok && !a.silence {
        log.Infof("%v Config: `%s`", progressMessage, printableOptions.String())
    }                                                     
                                                                                                                                    
    return nil                                                                                                                      
}       
```
通过配置补全，可以确保一些重要的配置项具有默认值，当这些配置项没有被配置时，程序也仍然能够正常启动。一个大型项目，有很多配置项，我们不可能对每一个配置项都进行配置。所以，给重要配置项设置默认值，就显得很重要了。这里，我们来看下 iam-apiserver 提供的 Validate 方法：




```go
func (s *ServerRunOptions) Validate() []error {
    var errs []error

    errs = append(errs, s.GenericServerRunOptions.Validate()...)
    errs = append(errs, s.GrpcOptions.Validate()...)
    errs = append(errs, s.InsecureServing.Validate()...)
    errs = append(errs, s.SecureServing.Validate()...)
    errs = append(errs, s.MySQLOptions.Validate()...)
    errs = append(errs, s.RedisOptions.Validate()...)
    errs = append(errs, s.JwtOptions.Validate()...)
    errs = append(errs, s.Log.Validate()...)
    errs = append(errs, s.FeatureOptions.Validate()...)

    return errs
}
```
可以看到，每个配置分组，都实现了 Validate() 函数，对自己负责的配置进行校验。通过这种方式，程序会更加清晰。因为只有配置提供者才更清楚如何校验自己的配置项，所以最好的做法是将配置的校验放权给配置提供者（分组）。




#### 第 4 步：配置文件解析
在 buildCommand 函数中，通过 addConfigFlag 调用，添加了 -c, --config FILE 命令行参数，用来指定配置文件：


```go
addConfigFlag(a.basename, namedFlagSets.FlagSet("global"))
```

addConfigFlag 函数代码如下：




```go
func addConfigFlag(basename string, fs *pflag.FlagSet) {
    fs.AddFlag(pflag.Lookup(configFlagName))

    viper.AutomaticEnv()
    viper.SetEnvPrefix(strings.Replace(strings.ToUpper(basename), "-", "_", -1))
    viper.SetEnvKeyReplacer(strings.NewReplacer(".", "_", "-", "_"))

    cobra.OnInitialize(func() {
        if cfgFile != "" {
            viper.SetConfigFile(cfgFile)
        } else {
            viper.AddConfigPath(".")

            if names := strings.Split(basename, "-"); len(names) > 1 {
                viper.AddConfigPath(filepath.Join(homedir.HomeDir(), "."+names[0]))
            }

            viper.SetConfigName(basename)
        }

        if err := viper.ReadInConfig(); err != nil {
            _, _ = fmt.Fprintf(os.Stderr, "Error: failed to read configuration file(%s): %v\n", cfgFile, err)
            os.Exit(1)
        }
    })
}
```

addConfigFlag 函数中，指定了 Cobra Command 在执行命令之前，需要做的初始化工作：


```go
func() {
  if cfgFile != "" {
    viper.SetConfigFile(cfgFile)
  } else {
    viper.AddConfigPath(".")

    if names := strings.Split(basename, "-"); len(names) > 1 {
      viper.AddConfigPath(filepath.Join(homedir.HomeDir(), "."+names[0]))
    }

    viper.SetConfigName(basename)
  }

  if err := viper.ReadInConfig(); err != nil {
    _, _ = fmt.Fprintf(os.Stderr, "Error: failed to read configuration file(%s): %v\n", cfgFile, err)
    os.Exit(1)
  }
}
```

上述代码实现了以下功能：
- 如果命令行参数中没有指定配置文件的路径，则加载默认路径下的配置文件，通过 viper.AddConfigPath、viper.SetConfigName 来设置配置文件搜索路径和配置文件名。通过设置默认的配置文件，可以使我们不用携带任何命令行参数，即可运行程序。
- 支持环境变量，通过 viper.SetEnvPrefix 来设置环境变量前缀，避免跟系统中的环境变量重名。通过 viper.SetEnvKeyReplacer 重写了 Env 键。


上面，我们给应用添加了配置文件的命令行参数，并设置在命令执行前，读取配置文件。在命令执行时，会将配置文件中的配置项和命令行参数绑定，并将 Viper 的配置 Unmarshal 到传入的 Options 中：


```go
if !a.noConfig {    
    if err := viper.BindPFlags(cmd.Flags()); err != nil {    
        return err    
    }    
    
    if err := viper.Unmarshal(a.options); err != nil {    
        return err    
    }  
}  
```
Viper 的配置是命令行参数和配置文件配置 merge 后的配置。如果在配置文件中指定了 MySQL 的 host 配置，并且也同时指定了 --mysql.host 参数，则会优先取命令行参数设置的值。这里需要**注意**的是，不同于 YAML 格式的分级方式，配置项是通过点号 . 来分级的。


至此，我们已经成功构建了一个优秀的应用框架，接下来我们看下这个应用框架具有哪些优点吧。

### 这样构建的应用程序，有哪些优秀特性？
借助 Cobra 自带的能力，构建出的应用天然具备帮助信息、使用信息、子命令、子命令自动补全、非选项参数校验、命令别名、PreRun、PostRun 等功能，这些功能对于一个应用来说是非常有用的。

Cobra 可以集成 Pflag，通过将创建的 Pflag FlagSet 绑定到 Cobra 命令的 FlagSet 中，使得 Pflag 支持的标志能直接集成到 Cobra 命令中。集成到命令中有很多好处，例如：cobra -h 可以打印出所有设置的 flag，Cobra Command 命令提供的 GenBashCompletion 方法，可以实现命令行选项的自动补全。



通过 viper.BindPFlags 和 viper.ReadInConfig 函数，可以统一配置文件、命令行参数的配置项，使得应用的配置项更加清晰好记。面对不同场景可以选择不同的配置方式，使配置更加灵活。例如：配置 HTTPS 的绑定端口，可以通过 --secure.bind-port 配置，也可以通过配置文件配置（命令行参数优先于配置文件）：



```
secure:    
    bind-port: 8080
```
可以通过 viper.GetString("secure.bind-port") 这类方式获取应用的配置，获取方式更加灵活，而且全局可用。将应用框架的构建方法实现成了一个 Go 包，通过 Go 包可以提高应用构建代码的封装性和复用性。


### 如果你想自己构建应用，需要**注意**些什么？
当然，你也可以使用其他方式构建你的应用程序。比如，我就见过很多开发者使用如下方式来构建应用：直接在 main.go 文件中通过 gopkg.in/yaml.v3 包解析配置，通过 Go 标准库的 flag 包简单地添加一些命令行参数，例如--help、--config、--version。


但是，在你自己独立构建应用程序时，很可能会踩这么 3 个坑：
- 构建的应用功能简单，扩展性差，导致后期扩展复杂。
- 构建的应用没有帮助信息和使用信息，或者信息格式杂乱，增加应用的使用难度。
- 命令行选项和配置文件支持的配置项相互独立，导致配合应用程序的时候，不知道该使用哪种方式来配置。

**在我看来，对于小的应用，自己根据需要构建没什么问题，但是对于一个大型项目的话，还是在应用开发之初，就采用一些功能多、扩展性强的优秀包。这样，以后随着应用的迭代，可以零成本地进行功能添加和扩展，同时也能体现我们的专业性和技术深度，提高代码质量。**

如果你有特殊需求，一定要自己构建应用框架，那么我有以下几个**建议**：
- 应用框架应该清晰易读、扩展性强。
- 应用程序应该至少支持如下命令行选项：-h 打印帮助信息；-v 打印应用程序的版本；-c 支持指定配置文件的路径。
- 如果你的应用有很多命令行选项，那么**建议**支持 --secure.bind-port 这样的长选项，通过选项名字，就可以知道选项的作用。
- 配置文件使用 yaml 格式，yaml 格式的配置文件，能支持复杂的配置，还清晰易读。
- 如果你有多个服务，那么要保持所有服务的应用构建方式是一致的。


### 总结
一个应用框架由命令、命令行参数解析、配置文件解析 3 部分功能组成，我们可以通过 Cobra 来构建命令，通过 Pflag 来解析命令行参数，通过 Viper 来解析配置文件。一个项目，可能包含多个应用，这些应用都需要通过 Cobra、Viper、Pflag 来构建。为了不重复造轮子，简化应用的构建，我们可以将这些功能实现为一个 Go 包，方便直接调用构建应用。


IAM 项目的应用都是通过 github.com/marmotedu/iam/pkg/app 包来构建的，在构建时，调用 App 包提供的 NewApp 函数，来构建一个应用：


```go
func NewApp(basename string) *app.App {
    opts := options.NewOptions()
    application := app.NewApp("IAM API Server",
        basename,
        app.WithOptions(opts),
        app.WithDescription(commandDesc),
        app.WithDefaultValidArgs(),
        app.WithRunFunc(run(opts)),
    )

    return application
}
```
在构建应用时，只需要提供应用简短 / 详细描述、应用二进制文件名称和命令行选项即可。App 包会根据 Options 提供的 Flags() 方法，来给应用添加命令行选项。命令行选项中提供了 -c, --config 选项来指定配置文件，App 包也会加载并解析这个配置文件，并将配置文件和命令行选项相同配置项进行 Merge，最终将配置项的值保存在传入的 Options 变量中，供业务代码使用。

最后，如果你想自己构建应用，我给出了一些我的**建议**：设计一个清晰易读、易扩展的应用框架；支持一些常见的选项，例如 -h， -v， -c 等；如果应用的命令行选项比较多，**建议**使用 --secure.bind-port 这样的长选项。


