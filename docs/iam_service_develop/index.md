# iam_service_develop

# 服务开发

## 24 | Web 服务：Web 服务核心功能有哪些，如何实现？
在 Go 项目开发中，绝大部分情况下，我们是在写能提供某种功能的后端服务，这些功能以 RPC API 接口或者 RESTful API 接口的形式对外提供，能提供这两种 API 接口的服务也统称为 Web 服务。今天这一讲，我就通过介绍 RESTful API 风格的 Web 服务，来给你介绍下如何实现 Web 服务的核心功能。



### Web 服务的核心功能
Web 服务有很多功能，为了便于你理解，我将这些功能分成了基础功能和高级功能两大类，并总结在了下面这张图中：


![img](https://static001.geekbang.org/resource/image/1a/2e/1a6d38450cdd0e115e505ab30113602e.jpg?wh=2248x1835)

下面，我就按图中的顺序，来串讲下这些功能。


要实现一个 Web 服务，首先我们要选择通信协议和通信格式。在 Go 项目开发中，有 HTTP+JSON 和 gRPC+Protobuf 两种组合可选。因为 iam-apiserver 主要提供的是 REST 风格的 API 接口，所以选择的是 HTTP+JSON 组合。



Web 服务最核心的功能是路由匹配。路由匹配其实就是根据(HTTP方法, 请求路径)匹配到处理这个请求的函数，最终由该函数处理这次请求，并返回结果，过程如下图所示：


![img](https://static001.geekbang.org/resource/image/1f/9d/1f5yydeffb32732e7d0e23a0a9cd369d.jpg?wh=2248x975)

一次 HTTP 请求经过路由匹配，最终将请求交由Delete(c *gin.Context)函数来处理。变量c中存放了这次请求的参数，在 Delete 函数中，我们可以进行参数解析、参数校验、逻辑处理，最终返回结果。对于大型系统，可能会有很多个 API 接口，API 接口随着需求的更新迭代，可能会有多个版本，为了便于管理，我们需要**对路由进行分组**。有时候，我们需要在一个服务进程中，同时开启 HTTP 服务的 80 端口和 HTTPS 的 443 端口，这样我们就可以做到：对内的服务，访问 80 端口，简化服务访问复杂度；对外的服务，访问更为安全的 HTTPS 服务。显然，我们没必要为相同功能启动多个服务进程，所以这时候就需要 Web 服务能够支持**一进程多服务**的功能。


我们开发 Web 服务最核心的诉求是：输入一些参数，校验通过后，进行业务逻辑处理，然后返回结果。所以 Web 服务还应该能够进行参数解析、参数校验、逻辑处理、返回结果。这些都是 Web 服务的业务处理功能。


上面这些是 Web 服务的基本功能，此外，我们还需要支持一些高级功能。在进行 HTTP 请求时，经常需要针对每一次请求都设置一些通用的操作，比如添加 Header、添加 RequestID、统计请求次数等，这就要求我们的 Web 服务能够支持中间件特性。为了保证系统安全，对于每一个请求，我们都需要进行认证。Web 服务中，通常有两种认证方式，一种是基于用户名和密码，一种是基于 Token。认证通过之后，就可以继续处理请求了。为了方便定位和跟踪某一次请求，需要支持 RequestID，定位和跟踪 RequestID 主要是为了排障。


最后，当前的软件架构中，很多采用了前后端分离的架构。在前后端分离的架构中，前端访问地址和后端访问地址往往是不同的，浏览器为了安全，会针对这种情况设置跨域请求，所以 Web 服务需要能够处理浏览器的跨域请求。


到这里，我就把 Web 服务的基础功能和高级功能串讲了一遍。当然，上面只介绍了 Web 服务的核心功能，还有很多其他的功能，你可以通过学习[Gin 的官方文档](https://github.com/gin-gonic/gin)来了解。


你可以看到，Web 服务有很多核心功能，这些功能我们可以基于 net/http 包自己封装。但在实际的项目开发中， 我们更多会选择使用基于 net/http 包进行封装的优秀开源 Web 框架。本实战项目选择了 Gin 框架。接下来，我们主要看下 Gin 框架是如何实现以上核心功能的，这些功能我们在实际的开发中可以直接拿来使用。


### 为什么选择 Gin 框架？
优秀的 Web 框架有很多，我们为什么要选择 Gin 呢？在回答这个问题之前，我们先来看下选择 Web 框架时的关注点。


在选择 Web 框架时，我们可以关注如下几点：路由功能；是否具备 middleware/filter 能力；HTTP 参数（path、query、form、header、body）解析和返回；性能和稳定性；使用复杂度；社区活跃度。


按 GitHub Star 数来排名，当前比较火的 Go Web 框架有 Gin、Beego、Echo、Revel 、Martini。经过调研，我从中选择了 Gin 框架，原因是 Gin 具有如下特性：


轻量级，代码质量高，性能比较高；项目目前很活跃，并有很多可用的 Middleware；作为一个 Web 框架，功能齐全，使用起来简单。


那接下来，我就先详细介绍下 Gin 框架。Gin是用 Go 语言编写的 Web 框架，功能完善，使用简单，性能很高。Gin 核心的路由功能是通过一个定制版的HttpRouter来实现的，具有很高的路由性能。



Gin 有很多功能，这里我给你列出了它的一些核心功能：
- 支持 HTTP 方法：GET、POST、PUT、PATCH、DELETE、OPTIONS。
- 支持不同位置的 HTTP 参数：路径参数（path）、查询字符串参数（query）、表单参数（form）、HTTP 头参数（header）、消息体参数（body）。
- 支持 HTTP 路由和路由分组。
- 支持 middleware 和自定义 middleware。
- 支持自定义 Log。
- 支持 binding 和 validation，
- 支持自定义 validator。可以 bind 如下参数：query、path、body、header、form。
- 支持重定向。
- 支持 basic auth middleware。
- 支持自定义 HTTP 配置。
- 支持优雅关闭。
- 支持 HTTP2。
- 支持设置和获取 cookie。


### Gin 是如何支持 Web 服务基础功能的？
接下来，我们先通过一个具体的例子，看下 Gin 是如何支持 Web 服务基础功能的，后面再详细介绍这些功能的用法。我们创建一个 webfeature 目录，用来存放示例代码。因为要演示 HTTPS 的用法，所以需要创建证书文件。具体可以分为两步。

第一步，执行以下命令创建证书：


```
cat << 'EOF' > ca.pem
-----BEGIN CERTIFICATE-----
MIICSjCCAbOgAwIBAgIJAJHGGR4dGioHMA0GCSqGSIb3DQEBCwUAMFYxCzAJBgNV
BAYTAkFVMRMwEQYDVQQIEwpTb21lLVN0YXRlMSEwHwYDVQQKExhJbnRlcm5ldCBX
aWRnaXRzIFB0eSBMdGQxDzANBgNVBAMTBnRlc3RjYTAeFw0xNDExMTEyMjMxMjla
Fw0yNDExMDgyMjMxMjlaMFYxCzAJBgNVBAYTAkFVMRMwEQYDVQQIEwpTb21lLVN0
YXRlMSEwHwYDVQQKExhJbnRlcm5ldCBXaWRnaXRzIFB0eSBMdGQxDzANBgNVBAMT
BnRlc3RjYTCBnzANBgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEAwEDfBV5MYdlHVHJ7
+L4nxrZy7mBfAVXpOc5vMYztssUI7mL2/iYujiIXM+weZYNTEpLdjyJdu7R5gGUu
g1jSVK/EPHfc74O7AyZU34PNIP4Sh33N+/A5YexrNgJlPY+E3GdVYi4ldWJjgkAd
Qah2PH5ACLrIIC6tRka9hcaBlIECAwEAAaMgMB4wDAYDVR0TBAUwAwEB/zAOBgNV
HQ8BAf8EBAMCAgQwDQYJKoZIhvcNAQELBQADgYEAHzC7jdYlzAVmddi/gdAeKPau
sPBG/C2HCWqHzpCUHcKuvMzDVkY/MP2o6JIW2DBbY64bO/FceExhjcykgaYtCH/m
oIU63+CFOTtR7otyQAWHqXa7q4SbCDlG7DyRFxqG0txPtGvy12lgldA2+RgcigQG
Dfcog5wrJytaQ6UA0wE=
-----END CERTIFICATE-----
EOF

cat << 'EOF' > server.key
-----BEGIN PRIVATE KEY-----
MIICdQIBADANBgkqhkiG9w0BAQEFAASCAl8wggJbAgEAAoGBAOHDFScoLCVJpYDD
M4HYtIdV6Ake/sMNaaKdODjDMsux/4tDydlumN+fm+AjPEK5GHhGn1BgzkWF+slf
3BxhrA/8dNsnunstVA7ZBgA/5qQxMfGAq4wHNVX77fBZOgp9VlSMVfyd9N8YwbBY
AckOeUQadTi2X1S6OgJXgQ0m3MWhAgMBAAECgYAn7qGnM2vbjJNBm0VZCkOkTIWm
V10okw7EPJrdL2mkre9NasghNXbE1y5zDshx5Nt3KsazKOxTT8d0Jwh/3KbaN+YY
tTCbKGW0pXDRBhwUHRcuRzScjli8Rih5UOCiZkhefUTcRb6xIhZJuQy71tjaSy0p
dHZRmYyBYO2YEQ8xoQJBAPrJPhMBkzmEYFtyIEqAxQ/o/A6E+E4w8i+KM7nQCK7q
K4JXzyXVAjLfyBZWHGM2uro/fjqPggGD6QH1qXCkI4MCQQDmdKeb2TrKRh5BY1LR
81aJGKcJ2XbcDu6wMZK4oqWbTX2KiYn9GB0woM6nSr/Y6iy1u145YzYxEV/iMwff
DJULAkB8B2MnyzOg0pNFJqBJuH29bKCcHa8gHJzqXhNO5lAlEbMK95p/P2Wi+4Hd
aiEIAF1BF326QJcvYKmwSmrORp85AkAlSNxRJ50OWrfMZnBgzVjDx3xG6KsFQVk2
ol6VhqL6dFgKUORFUWBvnKSyhjJxurlPEahV6oo6+A+mPhFY8eUvAkAZQyTdupP3
XEFQKctGz+9+gKkemDp7LBBMEMBXrGTLPhpEfcjv/7KPdnFHYmhYeBTBnuVmTVWe
F98XJ7tIFfJq
-----END PRIVATE KEY-----
EOF

cat << 'EOF' > server.pem
-----BEGIN CERTIFICATE-----
MIICnDCCAgWgAwIBAgIBBzANBgkqhkiG9w0BAQsFADBWMQswCQYDVQQGEwJBVTET
MBEGA1UECBMKU29tZS1TdGF0ZTEhMB8GA1UEChMYSW50ZXJuZXQgV2lkZ2l0cyBQ
dHkgTHRkMQ8wDQYDVQQDEwZ0ZXN0Y2EwHhcNMTUxMTA0MDIyMDI0WhcNMjUxMTAx
MDIyMDI0WjBlMQswCQYDVQQGEwJVUzERMA8GA1UECBMISWxsaW5vaXMxEDAOBgNV
BAcTB0NoaWNhZ28xFTATBgNVBAoTDEV4YW1wbGUsIENvLjEaMBgGA1UEAxQRKi50
ZXN0Lmdvb2dsZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAOHDFSco
LCVJpYDDM4HYtIdV6Ake/sMNaaKdODjDMsux/4tDydlumN+fm+AjPEK5GHhGn1Bg
zkWF+slf3BxhrA/8dNsnunstVA7ZBgA/5qQxMfGAq4wHNVX77fBZOgp9VlSMVfyd
9N8YwbBYAckOeUQadTi2X1S6OgJXgQ0m3MWhAgMBAAGjazBpMAkGA1UdEwQCMAAw
CwYDVR0PBAQDAgXgME8GA1UdEQRIMEaCECoudGVzdC5nb29nbGUuZnKCGHdhdGVy
em9vaS50ZXN0Lmdvb2dsZS5iZYISKi50ZXN0LnlvdXR1YmUuY29thwTAqAEDMA0G
CSqGSIb3DQEBCwUAA4GBAJFXVifQNub1LUP4JlnX5lXNlo8FxZ2a12AFQs+bzoJ6
hM044EDjqyxUqSbVePK0ni3w1fHQB5rY9yYC5f8G7aqqTY1QOhoUk8ZTSTRpnkTh
y4jjdvTZeLDVBlueZUTDRmy2feY5aZIU18vFDK08dTG0A87pppuv1LNIR3loveU8
-----END CERTIFICATE-----
EOF
```
第二步，创建 main.go 文件：


```go
package main

import (
  "fmt"
  "log"
  "net/http"
  "sync"
  "time"

  "github.com/gin-gonic/gin"
  "golang.org/x/sync/errgroup"
)

type Product struct {
  Username    string    `json:"username" binding:"required"`
  Name        string    `json:"name" binding:"required"`
  Category    string    `json:"category" binding:"required"`
  Price       int       `json:"price" binding:"gte=0"`
  Description string    `json:"description"`
  CreatedAt   time.Time `json:"createdAt"`
}

type productHandler struct {
  sync.RWMutex
  products map[string]Product
}

func newProductHandler() *productHandler {
  return &productHandler{
    products: make(map[string]Product),
  }
}

func (u *productHandler) Create(c *gin.Context) {
  u.Lock()
  defer u.Unlock()

  // 1. 参数解析
  var product Product
  if err := c.ShouldBindJSON(&product); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    return
  }

  // 2. 参数校验
  if _, ok := u.products[product.Name]; ok {
    c.JSON(http.StatusBadRequest, gin.H{"error": fmt.Sprintf("product %s already exist", product.Name)})
    return
  }
  product.CreatedAt = time.Now()

  // 3. 逻辑处理
  u.products[product.Name] = product
  log.Printf("Register product %s success", product.Name)

  // 4. 返回结果
  c.JSON(http.StatusOK, product)
}

func (u *productHandler) Get(c *gin.Context) {
  u.Lock()
  defer u.Unlock()

  product, ok := u.products[c.Param("name")]
  if !ok {
    c.JSON(http.StatusNotFound, gin.H{"error": fmt.Errorf("can not found product %s", c.Param("name"))})
    return
  }

  c.JSON(http.StatusOK, product)
}

func router() http.Handler {
  router := gin.Default()
  productHandler := newProductHandler()
  // 路由分组、中间件、认证
  v1 := router.Group("/v1")
  {
    productv1 := v1.Group("/products")
    {
      // 路由匹配
      productv1.POST("", productHandler.Create)
      productv1.GET(":name", productHandler.Get)
    }
  }

  return router
}

func main() {
  var eg errgroup.Group

  // 一进程多端口
  insecureServer := &http.Server{
    Addr:         ":8080",
    Handler:      router(),
    ReadTimeout:  5 * time.Second,
    WriteTimeout: 10 * time.Second,
  }

  secureServer := &http.Server{
    Addr:         ":8443",
    Handler:      router(),
    ReadTimeout:  5 * time.Second,
    WriteTimeout: 10 * time.Second,
  }

  eg.Go(func() error {
    err := insecureServer.ListenAndServe()
    if err != nil && err != http.ErrServerClosed {
      log.Fatal(err)
    }
    return err
  })

  eg.Go(func() error {
    err := secureServer.ListenAndServeTLS("server.pem", "server.key")
    if err != nil && err != http.ErrServerClosed {
      log.Fatal(err)
    }
    return err
  })

  if err := eg.Wait(); err != nil {
    log.Fatal(err)
  }
}

```
运行以上代码：


```
$ go run main.go
```

打开另外一个终端，请求 HTTP 接口：



```
# 创建产品
$ curl -XPOST -H"Content-Type: application/json" -d'{"username":"colin","name":"iphone12","category":"phone","price":8000,"description":"cannot afford"}' http://127.0.0.1:8080/v1/products
{"username":"colin","name":"iphone12","category":"phone","price":8000,"description":"cannot afford","createdAt":"2021-06-20T11:17:03.818065988+08:00"}

# 获取产品信息
$ curl -XGET http://127.0.0.1:8080/v1/products/iphone12
{"username":"colin","name":"iphone12","category":"phone","price":8000,"description":"cannot afford","createdAt":"2021-06-20T11:17:03.818065988+08:00"}
```
示例代码存放地址为[webfeature](https://github.com/marmotedu/gopractise-demo/tree/master/gin/webfeature)。另外，Gin 项目仓库中也包含了很多使用示例，如果你想详细了解，可以参考 [gin examples](https://github.com/gin-gonic/examples)。下面，我来详细介绍下 Gin 是如何支持 Web 服务基础功能的。



#### HTTP/HTTPS 支持
因为 Gin 是基于 net/http 包封装的一个 Web 框架，所以它天然就支持 HTTP/HTTPS。在上述代码中，通过以下方式开启一个 HTTP 服务：


```go
insecureServer := &http.Server{
  Addr:         ":8080",
  Handler:      router(),
  ReadTimeout:  5 * time.Second,
  WriteTimeout: 10 * time.Second,
}
...
err := insecureServer.ListenAndServe()
```

通过以下方式开启一个 HTTPS 服务：



```go
secureServer := &http.Server{
  Addr:         ":8443",
  Handler:      router(),
  ReadTimeout:  5 * time.Second,
  WriteTimeout: 10 * time.Second,
}
...
err := secureServer.ListenAndServeTLS("server.pem", "server.key")
```
#### JSON 数据格式支持
Gin 支持多种数据通信格式，例如 application/json、application/xml。可以通过c.ShouldBindJSON函数，将 Body 中的 JSON 格式数据解析到指定的 Struct 中，通过c.JSON函数返回 JSON 格式的数据。


#### 路由匹配
Gin 支持两种路由匹配规则。第一种匹配规则是精确匹配。例如，路由为 /products/:name，匹配情况如下表所示：


![img](https://static001.geekbang.org/resource/image/11/df/11be05d7fe7f935e01725e2635f315df.jpg?wh=2248x1418)
第二种匹配规则是模糊匹配。例如，路由为 /products/*name，匹配情况如下表所示：


![img](https://static001.geekbang.org/resource/image/b5/7b/b5ccd9924e53dd90a64af6002967b67b.jpg?wh=2248x1636)

#### 路由分组
Gin 通过 Group 函数实现了路由分组的功能。路由分组是一个非常常用的功能，可以将相同版本的路由分为一组，也可以将相同 RESTful 资源的路由分为一组。例如：


```go
v1 := router.Group("/v1", gin.BasicAuth(gin.Accounts{"foo": "bar", "colin": "colin404"}))
{
    productv1 := v1.Group("/products")
    {
        // 路由匹配
        productv1.POST("", productHandler.Create)
        productv1.GET(":name", productHandler.Get)
    }

    orderv1 := v1.Group("/orders")
    {
        // 路由匹配
        orderv1.POST("", orderHandler.Create)
        orderv1.GET(":name", orderHandler.Get)
    }
}

v2 := router.Group("/v2", gin.BasicAuth(gin.Accounts{"foo": "bar", "colin": "colin404"}))
{
    productv2 := v2.Group("/products")
    {
        // 路由匹配
        productv2.POST("", productHandler.Create)
        productv2.GET(":name", productHandler.Get)
    }
}
```

通过将路由分组，可以对相同分组的路由做统一处理。比如上面那个例子，我们可以通过代码


```go
v1 := router.Group("/v1", gin.BasicAuth(gin.Accounts{"foo": "bar", "colin": "colin404"}))
```
给所有属于 v1 分组的路由都添加 gin.BasicAuth 中间件，以实现认证功能。中间件和认证，这里你先不用深究，下面讲高级功能的时候会介绍到。


#### 一进程多服务
我们可以通过以下方式实现一进程多服务：


```go
var eg errgroup.Group
insecureServer := &http.Server{...}
secureServer := &http.Server{...}

eg.Go(func() error {
  err := insecureServer.ListenAndServe()
  if err != nil && err != http.ErrServerClosed {
    log.Fatal(err)
  }
  return err
})
eg.Go(func() error {
  err := secureServer.ListenAndServeTLS("server.pem", "server.key")
  if err != nil && err != http.ErrServerClosed {
    log.Fatal(err)
  }
  return err
}

if err := eg.Wait(); err != nil {
  log.Fatal(err)
})
```
上述代码实现了两个相同的服务，分别监听在不同的端口。这里需要**注意**的是，为了不阻塞启动第二个服务，我们需要把 ListenAndServe 函数放在 goroutine 中执行，并且调用 eg.Wait() 来阻塞程序进程，从而让两个 HTTP 服务在 goroutine 中持续监听端口，并提供服务。


#### 参数解析、参数校验、逻辑处理、返回结果
此外，Web 服务还应该具有参数解析、参数校验、逻辑处理、返回结果 4 类功能，因为这些功能联系紧密，我们放在一起来说。

在 productHandler 的 Create 方法中，我们通过c.ShouldBindJSON来解析参数，接下来自己编写校验代码，然后将 product 信息保存在内存中（也就是业务逻辑处理），最后通过c.JSON返回创建的 product 信息。代码如下：


```go
func (u *productHandler) Create(c *gin.Context) {
  u.Lock()
  defer u.Unlock()

  // 1. 参数解析
  var product Product
  if err := c.ShouldBindJSON(&product); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    return
  }

  // 2. 参数校验
  if _, ok := u.products[product.Name]; ok {
    c.JSON(http.StatusBadRequest, gin.H{"error": fmt.Sprintf("product %s already exist", product.Name)})
    return
  }
  product.CreatedAt = time.Now()

  // 3. 逻辑处理
  u.products[product.Name] = product
  log.Printf("Register product %s success", product.Name)

  // 4. 返回结果
  c.JSON(http.StatusOK, product)
}
```

那这个时候，你可能会问：HTTP 的请求参数可以存在不同的位置，Gin 是如何解析的呢？这里，我们先来看下 HTTP 有哪些参数类型。HTTP 具有以下 5 种参数类型：

- 路径参数（path）。例如gin.Default().GET("/user/:name", nil)， name 就是路径参数。
- 查询字符串参数（query）。例如/welcome?firstname=Lingfei&lastname=Kong，firstname 和 lastname 就是查询字符串参数。
- 表单参数（form）。例如curl -X POST -F 'username=colin' -F 'password=colin1234' http://mydomain.com/login，username 和 password 就是表单参数。
- HTTP 头参数（header）。例如curl -X POST -H 'Content-Type: application/json' -d '{"username":"colin","password":"colin1234"}' http://mydomain.com/login，Content-Type 就是 HTTP 头参数。
- 消息体参数（body）。例如curl -X POST -H 'Content-Type: application/json' -d '{"username":"colin","password":"colin1234"}' http://mydomain.com/login，username 和 password 就是消息体参数。


Gin 提供了一些函数，来分别读取这些 HTTP 参数，每种类别会提供两种函数，一种函数可以直接读取某个参数的值，另外一种函数会把同类 HTTP 参数绑定到一个 Go 结构体中。比如，有如下路径参数：


```go
gin.Default().GET("/:name/:id", nil)
```
我们可以直接读取每个参数：


```go
name := c.Param("name")
action := c.Param("action")
```
也可以将所有的路径参数，绑定到结构体中：


```go
type Person struct {
    ID string `uri:"id" binding:"required,uuid"`
    Name string `uri:"name" binding:"required"`
}

if err := c.ShouldBindUri(&person); err != nil {
    // normal code
    return
}
```
Gin 在绑定参数时，是通过结构体的 tag 来判断要绑定哪类参数到结构体中的。这里要**注意**，不同的 HTTP 参数有不同的结构体 tag。
- 路径参数：uri。
- 查询字符串参数：form。
- 表单参数：form。
- HTTP 头参数：header。
- 消息体参数：会根据 Content-Type，自动选择使用 json 或者 xml，也可以调用 ShouldBindJSON 或者 ShouldBindXML 直接指定使用哪个 tag。


针对每种参数类型，Gin 都有对应的函数来获取和绑定这些参数。这些函数都是基于如下两个函数进行封装的：



ShouldBindWith(obj interface{}, b binding.Binding) error非常重要的一个函数，很多 ShouldBindXXX 函数底层都是调用 ShouldBindWith 函数来完成参数绑定的。该函数会根据传入的绑定引擎，将参数绑定到传入的结构体指针中，**如果绑定失败，只返回错误内容，但不终止 HTTP 请求**。ShouldBindWith 支持多种绑定引擎，例如 binding.JSON、binding.Query、binding.Uri、binding.Header 等，更详细的信息你可以参考 [binding.go](https://github.com/gin-gonic/gin/blob/v1.7.2/binding/binding.go#L72)。


MustBindWith(obj interface{}, b binding.Binding) error这是另一个非常重要的函数，很多 BindXXX 函数底层都是调用 MustBindWith 函数来完成参数绑定的。该函数会根据传入的绑定引擎，将参数绑定到传入的结构体指针中，**如果绑定失败，返回错误并终止请求，返回 HTTP 400 错误**。MustBindWith 所支持的绑定引擎跟 ShouldBindWith 函数一样。


Gin 基于 ShouldBindWith 和 MustBindWith 这两个函数，又衍生出很多新的 Bind 函数。这些函数可以满足不同场景下获取 HTTP 参数的需求。Gin 提供的函数可以获取 5 个类别的 HTTP 参数。
- 路径参数：ShouldBindUri、BindUri；
- 查询字符串参数：ShouldBindQuery、BindQuery；
- 表单参数：ShouldBind；
- HTTP 头参数：ShouldBindHeader、BindHeader；
- 消息体参数：ShouldBindJSON、BindJSON 等。


每个类别的 Bind 函数，详细信息你可以参考[Gin 提供的 Bind 函数](https://github.com/marmotedu/geekbang-go/blob/master/Gin%E6%8F%90%E4%BE%9B%E7%9A%84Bind%E5%87%BD%E6%95%B0.md)。这里要**注意**，Gin 并没有提供类似 ShouldBindForm、BindForm 这类函数来绑定表单参数，但我们可以通过 ShouldBind 来绑定表单参数。当 HTTP 方法为 GET 时，ShouldBind 只绑定 Query 类型的参数；当 HTTP 方法为 POST 时，会先检查 content-type 是否是 json 或者 xml，如果不是，则绑定 Form 类型的参数。


所以，ShouldBind 可以绑定 Form 类型的参数，但前提是 HTTP 方法是 POST，并且 content-type 不是 application/json、application/xml。在 Go 项目开发中，我建议使用 ShouldBindXXX，这样可以确保我们设置的 HTTP Chain（Chain 可以理解为一个 HTTP 请求的一系列处理插件）能够继续被执行。


### Gin 是如何支持 Web 服务高级功能的？
上面介绍了 Web 服务的基础功能，这里我再来介绍下高级功能。Web 服务可以具备多个高级功能，但比较核心的高级功能是中间件、认证、RequestID、跨域和优雅关停。


#### 中间件
Gin 支持中间件，HTTP 请求在转发到实际的处理函数之前，会被一系列加载的中间件进行处理。在中间件中，可以解析 HTTP 请求做一些逻辑处理，例如：跨域处理或者生成 X-Request-ID 并保存在 context 中，以便追踪某个请求。处理完之后，可以选择中断并返回这次请求，也可以选择将请求继续转交给下一个中间件处理。当所有的中间件都处理完之后，请求才会转给路由函数进行处理。具体流程如下图：


![img](https://static001.geekbang.org/resource/image/f0/80/f0783cb9ee8cffa969f846ebe8eae880.jpg?wh=2248x1655)


通过中间件，可以实现对所有请求都做统一的处理，提高开发效率，并使我们的代码更简洁。但是，因为所有的请求都需要经过中间件的处理，可能会增加请求延时。对于中间件特性，我有如下建议：
- 中间件做成可加载的，通过配置文件指定程序启动时加载哪些中间件。
- 只将一些通用的、必要的功能做成中间件。
- 在编写中间件时，一定要保证中间件的代码质量和性能。


在 Gin 中，可以通过 gin.Engine 的 Use 方法来加载中间件。中间件可以加载到不同的位置上，而且不同的位置作用范围也不同，例如：


```go
router := gin.New()
router.Use(gin.Logger(), gin.Recovery()) // 中间件作用于所有的HTTP请求
v1 := router.Group("/v1").Use(gin.BasicAuth(gin.Accounts{"foo": "bar", "colin": "colin404"})) // 中间件作用于v1 group
v1.POST("/login", Login).Use(gin.BasicAuth(gin.Accounts{"foo": "bar", "colin": "colin404"})) //中间件只作用于/v1/login API接口
```
Gin 框架本身支持了一些中间件。
- gin.Logger()：Logger 中间件会将日志写到 gin.DefaultWriter，gin.DefaultWriter 默认为 os.Stdout。
- gin.Recovery()：Recovery 中间件可以从任何 panic 恢复，并且写入一个 500 状态码。
- gin.CustomRecovery(handle gin.RecoveryFunc)：类似 Recovery 中间件，但是在恢复时还会调用传入的 handle 方法进行处理。
- gin.BasicAuth()：HTTP 请求基本认证（使用用户名和密码进行认证）。


另外，Gin 还支持自定义中间件。中间件其实是一个函数，函数类型为 gin.HandlerFunc，HandlerFunc 底层类型为 func(*Context)。如下是一个 Logger 中间件的实现：



```go
package main

import (
  "log"
  "time"

  "github.com/gin-gonic/gin"
)

func Logger() gin.HandlerFunc {
  return func(c *gin.Context) {
    t := time.Now()

    // 设置变量example
    c.Set("example", "12345")

    // 请求之前

    c.Next()

    // 请求之后
    latency := time.Since(t)
    log.Print(latency)

    // 访问我们发送的状态
    status := c.Writer.Status()
    log.Println(status)
  }
}

func main() {
  r := gin.New()
  r.Use(Logger())

  r.GET("/test", func(c *gin.Context) {
    example := c.MustGet("example").(string)

    // it would print: "12345"
    log.Println(example)
  })

  // Listen and serve on 0.0.0.0:8080
  r.Run(":8080")
}
```

另外，还有很多开源的中间件可供我们选择，我把一些常用的总结在了表格里：


![img](https://static001.geekbang.org/resource/image/67/10/67137697a09d9f37bd87a81bf322f510.jpg?wh=1832x1521)

#### 认证、RequestID、跨域
认证、RequestID、跨域这三个高级功能，都可以通过 Gin 的中间件来实现，例如：


```go
router := gin.New()

// 认证
router.Use(gin.BasicAuth(gin.Accounts{"foo": "bar", "colin": "colin404"}))

// RequestID
router.Use(requestid.New(requestid.Config{
    Generator: func() string {
        return "test"
    },
}))

// 跨域
// CORS for https://foo.com and https://github.com origins, allowing:
// - PUT and PATCH methods
// - Origin header
// - Credentials share
// - Preflight requests cached for 12 hours
router.Use(cors.New(cors.Config{
    AllowOrigins:     []string{"https://foo.com"},
    AllowMethods:     []string{"PUT", "PATCH"},
    AllowHeaders:     []string{"Origin"},
    ExposeHeaders:    []string{"Content-Length"},
    AllowCredentials: true,
    AllowOriginFunc: func(origin string) bool {
        return origin == "https://github.com"
    },
    MaxAge: 12 * time.Hour,
}))
```

#### 优雅关停
Go 项目上线后，我们还需要不断迭代来丰富项目功能、修复 Bug 等，这也就意味着，我们要不断地重启 Go 服务。对于 HTTP 服务来说，如果访问量大，重启服务的时候可能还有很多连接没有断开，请求没有完成。如果这时候直接关闭服务，这些连接会直接断掉，请求异常终止，这就会对用户体验和产品口碑造成很大影响。因此，这种关闭方式不是一种优雅的关闭方式。

这时候，我们期望 HTTP 服务可以在处理完所有请求后，正常地关闭这些连接，也就是优雅地关闭服务。我们有两种方法来优雅关闭 HTTP 服务，分别是借助第三方的 Go 包和自己编码实现。

方法一：借助第三方的 Go 包如果使用第三方的 Go 包来实现优雅关闭，目前用得比较多的包是fvbock/endless。我们可以使用 [fvbock/endless](https://github.com/fvbock/endless) 来替换掉 net/http 的 ListenAndServe 方法，例如：


```go
router := gin.Default()
router.GET("/", handler)
// [...]
endless.ListenAndServe(":4242", router)
```

方法二：编码实现借助第三方包的好处是可以稍微减少一些编码工作量，但缺点是引入了一个新的依赖包，因此**我更倾向于自己编码实现**。Go 1.8 版本或者更新的版本，http.Server 内置的 Shutdown 方法，已经实现了优雅关闭。下面是一个示例：


```go
// +build go1.8

package main

import (
  "context"
  "log"
  "net/http"
  "os"
  "os/signal"
  "syscall"
  "time"

  "github.com/gin-gonic/gin"
)

func main() {
  router := gin.Default()
  router.GET("/", func(c *gin.Context) {
    time.Sleep(5 * time.Second)
    c.String(http.StatusOK, "Welcome Gin Server")
  })

  srv := &http.Server{
    Addr:    ":8080",
    Handler: router,
  }

  // Initializing the server in a goroutine so that
  // it won't block the graceful shutdown handling below
  go func() {
    if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
      log.Fatalf("listen: %s\n", err)
    }
  }()

  // Wait for interrupt signal to gracefully shutdown the server with
  // a timeout of 5 seconds.
  quit := make(chan os.Signal, 1)
  // kill (no param) default send syscall.SIGTERM
  // kill -2 is syscall.SIGINT
  // kill -9 is syscall.SIGKILL but can't be catch, so don't need add it
  signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
  <-quit
  log.Println("Shutting down server...")

  // The context is used to inform the server it has 5 seconds to finish
  // the request it is currently handling
  ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
  defer cancel()
  if err := srv.Shutdown(ctx); err != nil {
    log.Fatal("Server forced to shutdown:", err)
  }

  log.Println("Server exiting")
}
```

上面的示例中，需要把 srv.ListenAndServe 放在 goroutine 中执行，这样才不会阻塞到 srv.Shutdown 函数。因为我们把 srv.ListenAndServe 放在了 goroutine 中，所以需要一种可以让整个进程常驻的机制。


这里，我们借助了有缓冲 channel，并且调用 signal.Notify 函数将该 channel 绑定到 SIGINT、SIGTERM 信号上。这样，收到 SIGINT、SIGTERM 信号后，quilt 通道会被写入值，从而结束阻塞状态，程序继续运行，执行 srv.Shutdown(ctx)，优雅关停 HTTP 服务。



### 总结
今天我们主要学习了 Web 服务的核心功能，以及如何开发这些功能。在实际的项目开发中， 我们一般会使用基于 net/http 包进行封装的优秀开源 Web 框架。当前比较火的 Go Web 框架有 Gin、Beego、Echo、Revel、Martini。你可以根据需要进行选择。我比较推荐 Gin，Gin 也是目前比较受欢迎的 Web 框架。Gin Web 框架支持 Web 服务的很多基础功能，例如 HTTP/HTTPS、JSON 格式的数据、路由分组和匹配、一进程多服务等。另外，Gin 还支持 Web 服务的一些高级功能，例如 中间件、认证、RequestID、跨域和优雅关停等。


## 25 | 认证机制：应用程序如何进行访问认证？

保证应用的安全是软件开发的最基本要求，我们有多种途径来保障应用的安全，例如网络隔离、设置防火墙、设置 IP 黑白名单等。不过在我看来，这些更多是从运维角度来解决应用的安全问题。作为开发者，我们也可以从软件层面来保证应用的安全，这可以通过认证来实现。

这一讲，我以 HTTP 服务为例，来给你介绍下当前常见的四种认证方法：Basic、Digest、OAuth、Bearer。还有很多基于这四种方法的变种，这里就不再介绍了。IAM 项目使用了 Basic、Bearer 两种认证方法。这一讲，我先来介绍下这四种认证方法，下一讲，我会给你介绍下 IAM 项目是如何设计和实现访问认证功能的。


### 认证和授权有什么区别？
在介绍四种基本的认证方法之前，我想先带你区分下认证和授权，这是很多开发者都容易搞混的两个概念。


认证（Authentication，英文缩写 authn）：用来验证某个用户是否具有访问系统的权限。如果认证通过，该用户就可以访问系统，从而创建、修改、删除、查询平台支持的资源。

授权（Authorization，英文缩写 authz）：用来验证某个用户是否具有访问某个资源的权限，如果授权通过，该用户就能对资源做增删改查等操作。

这里，我通过下面的图片，来让你明白二者的区别：


![img](https://static001.geekbang.org/resource/image/8b/96/8b63cc7a624dbdb32b37898180a37596.jpg?wh=2248x1747)

图中，我们有一个仓库系统，用户 james、colin、aaron 分别创建了 Product-A、Product-B、Product-C。现在用户 colin 通过用户名和密码（认证）成功登陆到仓库系统中，但他尝试访问 Product-A、Product-C 失败，因为这两个产品不属于他（授权失败），但他可以成功访问自己创建的资源 Product-B（授权成功）。由此可见：**认证证明了你是谁，授权决定了你能做什么。**


上面，我们介绍了认证和授权的区别。那么接下来，我们就回到这一讲的重心：应用程序如何进行访问认证。


### 四种基本的认证方式
常见的认证方式有四种，分别是 Basic、Digest、OAuth 和 Bearer。先来看下 Basic 认证。


#### Basic
Basic 认证（基础认证），是最简单的认证方式。它简单地将用户名:密码进行 base64 编码后，放到 HTTP Authorization Header 中。HTTP 请求到达后端服务后，后端服务会解析出 Authorization Header 中的 base64 字符串，解码获取用户名和密码，并将用户名和密码跟数据库中记录的值进行比较，如果匹配则认证通过。例如：

```
$ basic=`echo -n 'admin:Admin@2021'|base64`
$ curl -XPOST -H"Authorization: Basic ${basic}" http://127.0.0.1:8080/login
```
通过 base64 编码，可以将密码以非明文的方式传输，增加一定的安全性。但是，base64 不是加密技术，入侵者仍然可以截获 base64 字符串，并反编码获取用户名和密码。另外，即使 Basic 认证中密码被加密，入侵者仍可通过加密后的用户名和密码进行重放攻击。


所以，Basic 认证虽然简单，但极不安全。使用 Basic 认证的唯一方式就是将它和 SSL 配合使用，来确保整个认证过程是安全的。


IAM 项目中，为了支持前端通过用户名和密码登录，仍然使用了 Basic 认证，但前后端使用 HTTPS 来通信，保证了认证的安全性。


这里需要注意，在设计系统时，要遵循一个通用的原则：**不要在请求参数中使用明文密码，也不要在任何存储中保存明文密码。**


#### Digest
Digest 认证（摘要认证），是另一种 HTTP 认证协议，它与基本认证兼容，但修复了基本认证的严重缺陷。Digest 具有如下特点：


- 绝不会用明文方式在网络上发送密码。
- 可以有效防止恶意用户进行重放攻击。
- 可以有选择地防止对报文内容的篡改。


摘要认证的过程见下图：


![img](https://static001.geekbang.org/resource/image/c6/b5/c693394977b4f91ae14b8c06f69056b5.jpg?wh=2248x1872)


在上图中，完成摘要认证需要下面这四步：
1. 客户端请求服务端的资源。
2. 在客户端能够证明它知道密码从而确认其身份之前，服务端认证失败，返回401 Unauthorized，并返回WWW-Authenticate头，里面包含认证需要的信息。
3. 客户端根据WWW-Authenticate头中的信息，选择加密算法，并使用密码随机数 nonce，计算出密码摘要 response，并再次请求服务端。
4. 服务器将客户端提供的密码摘要与服务器内部计算出的摘要进行对比。如果匹配，就说明客户端知道密码，认证通过，并返回一些与授权会话相关的附加信息，放在 Authorization-Info 中。


WWW-Authenticate头中包含的信息见下表：


![img](https://static001.geekbang.org/resource/image/59/9e/593e48602465b84165678bdc98467d9e.jpg?wh=2248x1755)

虽然使用摘要可以避免密码以明文方式发送，一定程度上保护了密码的安全性，但是仅仅隐藏密码并不能保证请求是安全的。因为请求（包括密码摘要）仍然可以被截获，这样就可以重放给服务器，带来安全问题。


为了防止重放攻击，服务器向客户端发送了密码随机数 nonce，nonce 每次请求都会变化。客户端会根据 nonce 生成密码摘要，这种方式，可以使摘要随着随机数的变化而变化。服务端收到的密码摘要只对特定的随机数有效，而没有密码的话，攻击者就无法计算出正确的摘要，这样我们就可以防止重放攻击。


**摘要认证可以保护密码，比基本认证安全很多。但摘要认证并不能保护内容，所以仍然要与 HTTPS 配合使用，来确保通信的安全。**


##### OAuth
OAuth（开放授权）是一个开放的授权标准，允许用户让第三方应用访问该用户在某一 Web 服务上存储的私密资源（例如照片、视频、音频等），而无需将用户名和密码提供给第三方应用。OAuth 目前的版本是 2.0 版。


OAuth2.0 一共分为四种授权方式，分别为密码式、隐藏式、凭借式和授权码模式。接下来，我们就具体介绍下每一种授权方式。


第一种，密码式。密码式的授权方式，就是用户把用户名和密码直接告诉给第三方应用，然后第三方应用使用用户名和密码换取令牌。所以，使用此授权方式的前提是无法采用其他授权方式，并且用户高度信任某应用。


认证流程如下：
1. 网站 A 向用户发出获取用户名和密码的请求；
2. 用户同意后，网站 A 凭借用户名和密码向网站 B 换取令牌；
3. 网站 B 验证用户身份后，给出网站 A 令牌，网站 A 凭借令牌可以访问网站 B 对应权限的资源。


第二种，隐藏式。这种方式适用于前端应用。认证流程如下：
1. A 网站提供一个跳转到 B 网站的链接，用户点击后跳转至 B 网站，并向用户请求授权；
2. 用户登录 B 网站，同意授权后，跳转回 A 网站指定的重定向 redirect_url 地址，并携带 B 网站返回的令牌，用户在 B 网站的数据给 A 网站使用。


这个授权方式存在着“中间人攻击”的风险，因此只能用于一些安全性要求不高的场景，并且令牌的有效时间要非常短。



第三种，凭借式。这种方式是在命令行中请求授权，适用于没有前端的命令行应用。认证流程如下：
1. 应用 A 在命令行向应用 B 请求授权，此时应用 A 需要携带应用 B 提前颁发的 secretID 和 secretKey，其中 secretKey 出于安全性考虑，需在后端发送；
2. 应用 B 接收到 secretID 和 secretKey，并进行身份验证，验证通过后返回给应用 A 令牌。

第四种，授权码模式。这种方式就是第三方应用先提前申请一个授权码，然后再使用授权码来获取令牌。相对来说，这种方式安全性更高，前端传送授权码，后端存储令牌，与资源的通信都是在后端，可以避免令牌的泄露导致的安全问题。认证流程如下：


![img](https://static001.geekbang.org/resource/image/54/a6/547b6362aba9e9ce8b72b511afee94a6.jpg?wh=2248x1127)

1. A 网站提供一个跳转到 B 网站的链接 +redirect_url，用户点击后跳转至 B 网站；
2. 用户携带向 B 网站提前申请的 client_id，向 B 网站发起身份验证请求；
3. 用户登录 B 网站，通过验证，授予 A 网站权限，此时网站跳转回 redirect_url，其中会有 B 网站通过验证后的授权码附在该 url 后；
4. 网站 A 携带授权码向网站 B 请求令牌，网站 B 验证授权码后，返回令牌即 access_token。


#### Bearer
Bearer 认证，也称为令牌认证，是一种 HTTP 身份验证方法。Bearer 认证的核心是 bearer token。bearer token 是一个加密字符串，通常由服务端根据密钥生成。客户端在请求服务端时，必须在请求头中包含Authorization: Bearer 。服务端收到请求后，解析出 ，并校验 的合法性，如果校验通过，则认证通过。跟基本认证一样，Bearer 认证需要配合 HTTPS 一起使用，来保证认证安全性。


当前最流行的 token 编码方式是 JSON Web Token（JWT，音同 jot，详见 JWT RFC 7519）。接下来，我通过讲解 JWT 认证来帮助你了解 Bearer 认证的原理。


### 基于 JWT 的 Token 认证机制实现
在典型业务场景中，为了区分用户和保证安全，必须对 API 请求进行鉴权，但是不能要求每一个请求都进行登录操作。合理做法是，在第一次登录之后产生一个有一定有效期的 token，并将它存储在浏览器的 Cookie 或 LocalStorage 之中。之后的请求都携带这个 token ，请求到达服务器端后，服务器端用这个 token 对请求进行认证。在第一次登录之后，服务器会将这个 token 用文件、数据库或缓存服务器等方法存下来，用于之后请求中的比对。


或者也可以采用更简单的方法：直接用密钥来签发 Token。这样，就可以省下额外的存储，也可以减少每一次请求时对数据库的查询压力。这种方法在业界已经有一种标准的实现方式，就是 JWT。接下来，我就来具体介绍下 JWT。


#### JWT 简介
JWT 是 Bearer Token 的一个具体实现，由 JSON 数据格式组成，通过 HASH 散列算法生成一个字符串。该字符串可以用来进行授权和信息交换。使用 JWT Token 进行认证有很多优点，比如说无需在服务端存储用户数据，可以减轻服务端压力；而且采用 JSON 数据格式，比较易读。除此之外，使用 JWT Token 还有跨语言、轻量级等优点。


#### JWT 认证流程
使用 JWT Token 进行认证的流程如下图：


![img](https://static001.geekbang.org/resource/image/48/01/480397e0a0e1503a350a082f44ec5901.jpg?wh=2248x1471)

具体可以分为四步：
1. 客户端使用用户名和密码请求登录。
2. 服务端收到请求后，会去验证用户名和密码。如果用户名和密码跟数据库记录不一致，则验证失败；如果一致则验证通过，服务端会签发一个 Token 返回给客户端。
3. 客户端收到请求后会将 Token 缓存起来，比如放在浏览器 Cookie 中或者 LocalStorage 中，之后每次请求都会携带该 Token。
4. 服务端收到请求后，会验证请求中的 Token，验证通过则进行业务逻辑处理，处理完后返回处理后的结果。


#### JWT 格式
JWT 由三部分组成，分别是 Header、Payload 和 Signature，它们之间用圆点.连接，例如：


```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2NDI4NTY2MzcsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MzUwODA2MzcsInN1YiI6ImFkbWluIn0.Shw27RKENE_2MVBq7-c8OmgYdF92UmdwS8xE-Fts2FM
```
JWT 中，每部分包含的信息见下图：


![img](https://static001.geekbang.org/resource/image/0c/08/0c6657bc2d0fd2a98737660c7c373e08.jpg?wh=2248x1732)

下面我来具体介绍下这三部分，以及它们包含的信息。


Header
JWT Token 的 Header 中，包含两部分信息：一是 Token 的类型，二是 Token 所使用的加密算法。例如：

```
{
  "typ": "JWT",
  "alg": "HS256"
}
```
参数说明：typ：说明 Token 类型是 JWT。alg：说明 Token 的加密算法，这里是 HS256（alg 算法可以有多种）。


这里，我们将 Header 进行 base64 编码：


```
$ echo -n '{"typ":"JWT","alg":"HS256"}'|base64
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```
在某些场景下，可能还会有 kid 选项，用来标识一个密钥 ID，例如：


```
{
    "alg": "HS256",
    "kid": "XhbY3aCrfjdYcP1OFJRu9xcno8JzSbUIvGE2",
    "typ": "JWT"
}
```

Payload（载荷）
Payload 中携带 Token 的具体内容由三部分组成：JWT 标准中注册的声明（可选）、公共的声明、私有的声明。下面来分别看下。


JWT 标准中注册的声明部分，有以下标准字段：


![img](https://static001.geekbang.org/resource/image/c2/e3/c271d01d41dc7f4a45a9f2e8892057e3.png?wh=2248x2077)

本例中的 payload 内容为：


```
{
  "aud": "iam.authz.marmotedu.com",
  "exp": 1604158987,
  "iat": 1604151787,
  "iss": "iamctl",
  "nbf": 1604151787
}
```
这里，我们将 Payload 进行 base64 编码：



```
$ echo -n '{"aud":"iam.authz.marmotedu.com","exp":1604158987,"iat":1604151787,"iss":"iamctl","nbf":1604151787}'|base64
eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYwNDE1ODk4NywiaWF0Ijox
NjA0MTUxNzg3LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MDQxNTE3ODd9
```

除此之外，还有公共的声明和私有的声明。公共的声明可以添加任何的需要的信息，一般添加用户的相关信息或其他业务需要的信息，注意不要添加敏感信息；私有声明是客户端和服务端所共同定义的声明，因为 base64 是对称解密的，所以一般不建议存放敏感信息。



Signature（签名）
Signature 是 Token 的签名部分，通过如下方式生成：将 Header 和 Payload 分别 base64 编码后，用 . 连接。然后再使用 Header 中声明的加密方式，利用 secretKey 对连接后的字符串进行加密，加密后的字符串即为最终的 Signature。

secretKey 是密钥，保存在服务器中，一般通过配置文件来保存，例如：


![img](https://static001.geekbang.org/resource/image/b1/d3/b183d2695c01cd863f782edf0a6d12d3.png?wh=1024x256)

这里要注意，密钥一定不能泄露。**密钥泄露后，入侵者可以使用该密钥来签发 JWT Token，从而入侵系统**。最后生成的 Token 如下：



```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYwNDE1ODk4NywiaWF0IjoxNjA0MTUxNzg3LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MDQxNTE3ODd9.LjxrK9DuAwAzUD8-9v43NzWBN7HXsSLfebw92DKd1JQ
```
签名后服务端会返回生成的 Token，客户端下次请求会携带该 Token。服务端收到 Token 后会解析出 header.payload，然后用相同的加密算法和密钥对 header.payload 再进行一次加密，得到 Signature。并且，对比加密后的 Signature 和收到的 Signature 是否相同，如果相同则验证通过，不相同则返回 HTTP 401 Unauthorized 的错误。


最后，关于 JWT 的使用，我还有两点建议：
1. 不要存放敏感信息在 Token 里；
2. Payload 中的 exp 值不要设置得太大，一般开发版本 7 天，线上版本 2 小时。当然，你也可以根据需要自行设置。


### 总结
在开发 Go 应用时，我们需要通过认证来保障应用的安全。认证，用来验证某个用户是否具有访问系统的权限，如果认证通过，该用户就可以访问系统，从而创建、修改、删除、查询平台支持的资源。业界目前有四种常用的认证方式：Basic、Digest、OAuth、Bearer。其中 Basic 和 Bearer 用得最多。

Basic 认证通过用户名和密码来进行认证，主要用在用户登录场景；Bearer 认证通过 Token 来进行认证，通常用在 API 调用场景。不管是 Basic 认证还是 Bearer 认证，都需要结合 HTTPS 来使用，来最大程度地保证请求的安全性。

Basic 认证简单易懂，但是 Bearer 认证有一定的复杂度，所以这一讲的后半部分通过 JWT Token，讲解了 Bearer Token 认证的原理。


JWT Token 是 Bearer 认证的一种比较好的实现，主要包含了 3 个部分：
- Header：包含了 Token 的类型、Token 使用的加密算法。在某些场景下，你还可以添加 kid 字段，用来标识一个密钥 ID。
- Payload：Payload 中携带 Token 的具体内容，由 JWT 标准中注册的声明、公共的声明和私有的声明三部分组成。
- Signature：Signature 是 Token 的签名部分，程序通过验证 Signature 是否合法，来决定认证是否通过。

## 26 | IAM项目是如何设计和实现访问认证功能的？

上一讲，我们学习了应用认证常用的四种方式：Basic、Digest、OAuth、Bearer。这一讲，我们再来看下 IAM 项目是如何设计和实现认证功能的。IAM 项目用到了 Basic 认证和 Bearer 认证。其中，Basic 认证用在前端登陆的场景，Bearer 认证用在调用后端 API 服务的场景下。接下来，我们先来看下 IAM 项目认证功能的整体设计思路。


### 如何设计 IAM 项目的认证功能？
在认证功能开发之前，我们要根据需求，认真考虑下如何设计认证功能，并在设计阶段通过技术评审。那么我们先来看下，如何设计 IAM 项目的认证功能。


首先，我们要梳理清楚认证功能的使用场景和需求。
- IAM 项目的 iam-apiserver 服务，提供了 IAM 系统的管理流功能接口，它的客户端可以是前端（这里也叫控制台），也可以是 App 端。
- 为了方便用户在 Linux 系统下调用，IAM 项目还提供了 iamctl 命令行工具。
- 为了支持在第三方代码中调用 iam-apiserver 提供的 API 接口，还支持了 API 调用。
- 为了提高用户在代码中调用 API 接口的效率，IAM 项目提供了 Go SDK。


可以看到，iam-apiserver 有很多客户端，每种客户端适用的认证方式是有区别的。

控制台、App 端需要登录系统，所以需要使用用户名：密码这种认证方式，也即 Basic 认证。iamctl、API 调用、Go SDK 因为可以不用登录系统，所以可以采用更安全的认证方式：Bearer 认证。同时，Basic 认证作为 iam-apiserver 已经集成的认证方式，仍然可以供 iamctl、API 调用、Go SDK 使用。

这里有个地方需要注意：如果 iam-apiserver 采用 Bearer Token 的认证方式，目前最受欢迎的 Token 格式是 JWT Token。而 JWT Token 需要密钥（后面统一用 secretKey 来指代），因此需要在 iam-apiserver 服务中为每个用户维护一个密钥，这样会增加开发和维护成本。


业界有一个更好的实现方式：将 iam-apiserver 提供的 API 接口注册到 API 网关中，通过 API 网关中的 Token 认证功能，来实现对 iam-apiserver API 接口的认证。有很多 API 网关可供选择，例如腾讯云 API 网关、Tyk、Kong 等。这里需要你注意：通过 iam-apiserver 创建的密钥对是提供给 iam-authz-server 使用的。


另外，我们还需要调用 iam-authz-server 提供的 RESTful API 接口：/v1/authz，来进行资源授权。API 调用比较适合采用的认证方式是 Bearer 认证。当然，/v1/authz也可以直接注册到 API 网关中。在实际的 Go 项目开发中，也是我推荐的一种方式。但在这里，为了展示实现 Bearer 认证的过程，iam-authz-server 自己实现了 Bearer 认证。讲到 iam-authz-server Bearer 认证实现的时候，我会详细介绍这一点。

Basic 认证需要用户名和密码，Bearer 认证则需要密钥，所以 iam-apiserver 需要将用户名 / 密码、密钥等信息保存在后端的 MySQL 中，持久存储起来。


在进行认证的时候，需要获取密码或密钥进行反加密，这就需要查询密码或密钥。查询密码或密钥有两种方式。一种是在请求到达时查询数据库。因为数据库的查询操作延时高，会导致 API 接口延时较高，所以不太适合用在数据流组件中。另外一种是将密码或密钥缓存在内存中，这样请求到来时，就可以直接从内存中查询，从而提升查询速度，提高接口性能。但是，将密码或密钥缓存在内存中时，就要考虑内存和数据库的数据一致性，这会增加代码实现的复杂度。因为管控流组件对性能延时要求不那么敏感，而数据流组件则一定要实现非常高的接口性能，所以 iam-apiserver 在请求到来时查询数据库，而 iam-authz-server 则将密钥信息缓存在内存中。


那在这里，可以总结出一张 IAM 项目的认证设计图：


![img](https://static001.geekbang.org/resource/image/7e/b6/7eed8e2364d358a8483c671d972fd2b6.jpg?wh=2248x1094)

另外，为了将控制流和数据流区分开来，密钥的 CURD 操作也放在了 iam-apiserver 中，但是 iam-authz-server 需要用到这些密钥信息。为了解决这个问题，目前的做法是：
- iam-authz-server 通过 gRPC API 请求 iam-apiserver，获取所有的密钥信息；
- 当 iam-apiserver 有密钥更新时，会 Pub 一条消息到 Redis Channel 中。因为 iam-authz-server 订阅了同一个 Redis Channel，iam-authz-searver 监听到 channel 有新消息时，会获取、解析消息，并更新它缓存的密钥信息。这样，我们就能确保 iam-authz-server 内存中缓存的密钥和 iam-apiserver 中的密钥保持一致。


学到这里，你可能会问：将所有密钥都缓存在 iam-authz-server 中，那岂不是要占用很大的内存？别担心，这个问题我也想过，并且替你计算好了：8G 的内存大概能保存约 8 千万个密钥信息，完全够用。后期不够用的话，可以加大内存。不过这里还是有个小缺陷：如果 Redis down 掉，或者出现网络抖动，可能会造成 iam-apiserver 中和 iam-authz-server 内存中保存的密钥数据不一致，但这不妨碍我们学习认证功能的设计和实现。至于如何保证缓存系统的数据一致性，我会在新一期的特别放送里专门介绍下。


最后注意一点：Basic 认证请求和 Bearer 认证请求都可能被截获并重放。**所以，为了确保 Basic 认证和 Bearer 认证的安全性，和服务端通信时都需要配合使用 HTTPS 协议。**



### IAM 项目是如何实现 Basic 认证的？
我们已经知道，IAM 项目中主要用了 Basic 和 Bearer 这两种认证方式。我们要支持 Basic 认证和 Bearer 认证，并根据需要选择不同的认证方式，这很容易让我们想到使用设计模式中的策略模式来实现。所以，在 IAM 项目中，我将每一种认证方式都视作一个策略，通过选择不同的策略，来使用不同的认证方法。


IAM 项目实现了如下策略：
- auto 策略：该策略会根据 HTTP 头Authorization: Basic XX.YY.ZZ和Authorization: Bearer XX.YY.ZZ自动选择使用 Basic 认证还是 Bearer 认证。
- basic 策略：该策略实现了 Basic 认证。
- jwt 策略：该策略实现了 Bearer 认证，JWT 是 Bearer 认证的具体实现。
- cache 策略：该策略其实是一个 Bearer 认证的实现，Token 采用了 JWT 格式，因为 Token 中的密钥 ID 是从内存中获取的，所以叫 Cache 认证。这一点后面会详细介绍。


iam-apiserver 通过创建需要的认证策略，并加载到需要认证的 API 路由上，来实现 API 认证。具体代码如下：

```go
jwtStrategy, _ := newJWTAuth().(auth.JWTStrategy)
g.POST("/login", jwtStrategy.LoginHandler)
g.POST("/logout", jwtStrategy.LogoutHandler)
// Refresh time can be longer than token timeout
g.POST("/refresh", jwtStrategy.RefreshHandler)

```
上述代码中，我们通过newJWTAuth函数创建了auth.JWTStrategy类型的变量，该变量包含了一些认证相关函数。


- LoginHandler：实现了 Basic 认证，完成登陆认证。
- RefreshHandler：重新刷新 Token 的过期时间。
- LogoutHandler：用户注销时调用。登陆成功后，如果在 Cookie 中设置了认证相关的信息，执行 LogoutHandler 则会清空这些信息。


下面，我来分别介绍下 LoginHandler、RefreshHandler 和 LogoutHandler。



LoginHandler这里，我们来看下 LoginHandler Gin 中间件，该函数定义位于github.com/appleboy/gin-jwt包的auth_jwt.go文件中。

```go
func (mw *GinJWTMiddleware) LoginHandler(c *gin.Context) {
  if mw.Authenticator == nil {
    mw.unauthorized(c, http.StatusInternalServerError, mw.HTTPStatusMessageFunc(ErrMissingAuthenticatorFunc, c))
    return
  }

  data, err := mw.Authenticator(c)

  if err != nil {
    mw.unauthorized(c, http.StatusUnauthorized, mw.HTTPStatusMessageFunc(err, c))
    return
  }

  // Create the token
  token := jwt.New(jwt.GetSigningMethod(mw.SigningAlgorithm))
  claims := token.Claims.(jwt.MapClaims)

  if mw.PayloadFunc != nil {
    for key, value := range mw.PayloadFunc(data) {
      claims[key] = value
    }
  }

  expire := mw.TimeFunc().Add(mw.Timeout)
  claims["exp"] = expire.Unix()
  claims["orig_iat"] = mw.TimeFunc().Unix()
  tokenString, err := mw.signedString(token)

  if err != nil {
    mw.unauthorized(c, http.StatusUnauthorized, mw.HTTPStatusMessageFunc(ErrFailedTokenCreation, c))
    return
  }

  // set cookie
  if mw.SendCookie {
    expireCookie := mw.TimeFunc().Add(mw.CookieMaxAge)
    maxage := int(expireCookie.Unix() - mw.TimeFunc().Unix())

    if mw.CookieSameSite != 0 {
      c.SetSameSite(mw.CookieSameSite)
    }

    c.SetCookie(
      mw.CookieName,
      tokenString,
      maxage,
      "/",
      mw.CookieDomain,
      mw.SecureCookie,
      mw.CookieHTTPOnly,
    )
  }

  mw.LoginResponse(c, http.StatusOK, tokenString, expire)
}
```
从 LoginHandler 函数的代码实现中，我们可以知道，LoginHandler 函数会执行Authenticator函数，来完成 Basic 认证。如果认证通过，则会签发 JWT Token，并执行 PayloadFunc函数设置 Token Payload。如果我们设置了 SendCookie=true ，还会在 Cookie 中添加认证相关的信息，例如 Token、Token 的生命周期等，最后执行 LoginResponse 方法返回 Token 和 Token 的过期时间。



Authenticator、PayloadFunc、LoginResponse这三个函数，是我们在创建 JWT 认证策略时指定的。下面我来分别介绍下。先来看下Authenticator函数。Authenticator 函数从 HTTP Authorization Header 中获取用户名和密码，并校验密码是否合法。


```go
func authenticator() func(c *gin.Context) (interface{}, error) {
  return func(c *gin.Context) (interface{}, error) {
    var login loginInfo
    var err error

    // support header and body both
    if c.Request.Header.Get("Authorization") != "" {
      login, err = parseWithHeader(c)
    } else {
      login, err = parseWithBody(c)
    }
    if err != nil {
      return "", jwt.ErrFailedAuthentication
    }

    // Get the user information by the login username.
    user, err := store.Client().Users().Get(c, login.Username, metav1.GetOptions{})
    if err != nil {
      log.Errorf("get user information failed: %s", err.Error())

      return "", jwt.ErrFailedAuthentication
    }

    // Compare the login password with the user password.
    if err := user.Compare(login.Password); err != nil {
      return "", jwt.ErrFailedAuthentication
    }

    return user, nil
  }
}
```
Authenticator函数需要获取用户名和密码。它首先会判断是否有Authorization请求头，如果有，则调用parseWithHeader函数获取用户名和密码，否则调用parseWithBody从 Body 中获取用户名和密码。如果都获取失败，则返回认证失败错误。所以，IAM 项目的 Basic 支持以下两种请求方式：


```
$ curl -XPOST -H"Authorization: Basic YWRtaW46QWRtaW5AMjAyMQ==" http://127.0.0.1:8080/login # 用户名:密码通过base64加码后，通过HTTP Authorization Header进行传递，因为密码非明文，建议使用这种方式。
$ curl -s -XPOST -H'Content-Type: application/json' -d'{"username":"admin","password":"Admin@2021"}' http://127.0.0.1:8080/login # 用户名和密码在HTTP Body中传递，因为密码是明文，所以这里不建议实际开发中，使用这种方式。
```
这里，我们来看下 parseWithHeader 是如何获取用户名和密码的。假设我们的请求为：


```
$ curl -XPOST -H"Authorization: Basic YWRtaW46QWRtaW5AMjAyMQ==" http://127.0.0.1:8080/login
```
其中，YWRtaW46QWRtaW5AMjAyMQ==值由以下命令生成：



```
$ echo -n 'admin:Admin@2021'|base64
YWRtaW46QWRtaW5AMjAyMQ==
```


parseWithHeader实际上执行的是上述命令的逆向步骤：
1. 获取Authorization头的值，并调用 strings.SplitN 函数，获取一个切片变量 auth，其值为 ["Basic","YWRtaW46QWRtaW5AMjAyMQ=="] 。
2. 将YWRtaW46QWRtaW5AMjAyMQ==进行 base64 解码，得到admin:Admin@2021。
3. 调用strings.SplitN函数获取 admin:Admin@2021 ，得到用户名为admin，密码为Admin@2021。


parseWithBody则是调用了 Gin 的ShouldBindJSON函数，来从 Body 中解析出用户名和密码。


获取到用户名和密码之后，程序会从数据库中查询出该用户对应的加密后的密码，这里我们假设是xxxx。最后authenticator函数调用user.Compare来判断 xxxx 是否和通过user.Compare加密后的字符串相匹配，如果匹配则认证成功，否则返回认证失败。


再来看下PayloadFunc函数：


```go
func payloadFunc() func(data interface{}) jwt.MapClaims {
    return func(data interface{}) jwt.MapClaims {
        claims := jwt.MapClaims{
            "iss": APIServerIssuer,
            "aud": APIServerAudience,
        }
        if u, ok := data.(*v1.User); ok {
            claims[jwt.IdentityKey] = u.Name
            claims["sub"] = u.Name
        }

        return claims
    }
}
```
PayloadFunc 函数会设置 JWT Token 中 Payload 部分的 iss、aud、sub、identity 字段，供后面使用。再来看下我们刚才说的第三个函数，LoginResponse 函数：


```go
func loginResponse() func(c *gin.Context, code int, token string, expire time.Time) {
    return func(c *gin.Context, code int, token string, expire time.Time) {
        c.JSON(http.StatusOK, gin.H{
            "token":  token,
            "expire": expire.Format(time.RFC3339),
        })
    }
}
```
该函数用来在 Basic 认证成功之后，返回 Token 和 Token 的过期时间给调用者：


```
$ curl -XPOST -H"Authorization: Basic YWRtaW46QWRtaW5AMjAyMQ==" http://127.0.0.1:8080/login
{"expire":"2021-09-29T01:38:49+08:00","token":"XX.YY.ZZ"}
```
登陆成功后，iam-apiserver 会返回 Token 和 Token 的过期时间，前端可以将这些信息缓存在 Cookie 中或 LocalStorage 中，之后的请求都可以使用 Token 来进行认证。使用 Token 进行认证，不仅能够提高认证的安全性，还能够避免查询数据库，从而提高认证效率。


RefreshHandler
RefreshHandler函数会先执行 Bearer 认证，如果认证通过，则会重新签发 Token。


LogoutHandler
最后，来看下LogoutHandler函数：


```go
func (mw *GinJWTMiddleware) LogoutHandler(c *gin.Context) {
    // delete auth cookie
    if mw.SendCookie {
        if mw.CookieSameSite != 0 {
            c.SetSameSite(mw.CookieSameSite)
        }

        c.SetCookie(
            mw.CookieName,
            "",
            -1,
            "/",
            mw.CookieDomain,
            mw.SecureCookie,
            mw.CookieHTTPOnly,
        )
    }

    mw.LogoutResponse(c, http.StatusOK)
}
```
可以看到，LogoutHandler 其实是用来清空 Cookie 中 Bearer 认证相关信息的。最后，我们来做个总结：Basic 认证通过用户名和密码来进行认证，通常用在登陆接口 /login 中。用户登陆成功后，会返回 JWT Token，前端会保存该 JWT Token 在浏览器的 Cookie 或 LocalStorage 中，供后续请求使用。



后续请求时，均会携带该 Token，以完成 Bearer 认证。另外，有了登陆接口，一般还会配套 /logout 接口和 /refresh 接口，分别用来进行注销和刷新 Token。这里你可能会问，为什么要刷新 Token？因为通过登陆接口签发的 Token 有过期时间，有了刷新接口，前端就可以根据需要，自行刷新 Token 的过期时间。过期时间可以通过 iam-apiserver 配置文件的jwt.timeout配置项来指定。登陆后签发 Token 时，使用的密钥（secretKey）由jwt.key配置项来指定。



### IAM 项目是如何实现 Bearer 认证的？
上面我们介绍了 Basic 认证。这里，我再来介绍下 IAM 项目中 Bearer 认证的实现方式。


IAM 项目中有两个地方实现了 Bearer 认证，分别是 iam-apiserver 和 iam-authz-server。下面我来分别介绍下它们是如何实现 Bearer 认证的。


#### iam-authz-server Bearer 认证实现
先来看下 iam-authz-server 是如何实现 Bearer 认证的。


iam-authz-server 通过在 /v1 路由分组中加载 cache 认证中间件来使用 cache 认证策略：


```go
auth := newCacheAuth()
apiv1 := g.Group("/v1", auth.AuthFunc())
```
来看下newCacheAuth函数：


```go
func newCacheAuth() middleware.AuthStrategy {
    return auth.NewCacheStrategy(getSecretFunc())
}

func getSecretFunc() func(string) (auth.Secret, error) {
    return func(kid string) (auth.Secret, error) {
        cli, err := store.GetStoreInsOr(nil)
        if err != nil {
            return auth.Secret{}, errors.Wrap(err, "get store instance failed")
        }

        secret, err := cli.GetSecret(kid)
        if err != nil {
            return auth.Secret{}, err
        }

        return auth.Secret{
            Username: secret.Username,
            ID:       secret.SecretId,
            Key:      secret.SecretKey,
            Expires:  secret.Expires,
        }, nil
    }
}
```

newCacheAuth 函数调用auth.NewCacheStrategy创建了一个 cache 认证策略，创建时传入了getSecretFunc函数，该函数会返回密钥的信息。密钥信息包含了以下字段：



```go
type Secret struct {
    Username string
    ID       string
    Key      string
    Expires  int64
}
```
再来看下 cache 认证策略实现的AuthFunc方法：



```go
func (cache CacheStrategy) AuthFunc() gin.HandlerFunc {
  return func(c *gin.Context) {
    header := c.Request.Header.Get("Authorization")
    if len(header) == 0 {
      core.WriteResponse(c, errors.WithCode(code.ErrMissingHeader, "Authorization header cannot be empty."), nil)
      c.Abort()

      return
    }

    var rawJWT string
    // Parse the header to get the token part.
    fmt.Sscanf(header, "Bearer %s", &rawJWT)

    // Use own validation logic, see below
    var secret Secret

    claims := &jwt.MapClaims{}
    // Verify the token
    parsedT, err := jwt.ParseWithClaims(rawJWT, claims, func(token *jwt.Token) (interface{}, error) {
      // Validate the alg is HMAC signature
      if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
        return nil, fmt.Errorf("unexpected signing method: %v", token.Header["alg"])
      }

      kid, ok := token.Header["kid"].(string)
      if !ok {
        return nil, ErrMissingKID
      }

      var err error
      secret, err = cache.get(kid)
      if err != nil {
        return nil, ErrMissingSecret
      }

      return []byte(secret.Key), nil
    }, jwt.WithAudience(AuthzAudience))
    if err != nil || !parsedT.Valid {
      core.WriteResponse(c, errors.WithCode(code.ErrSignatureInvalid, err.Error()), nil)
      c.Abort()

      return
    }

    if KeyExpired(secret.Expires) {
      tm := time.Unix(secret.Expires, 0).Format("2006-01-02 15:04:05")
      core.WriteResponse(c, errors.WithCode(code.ErrExpired, "expired at: %s", tm), nil)
      c.Abort()

      return
    }

    c.Set(CtxUsername, secret.Username)
    c.Next()
  }
}

// KeyExpired checks if a key has expired, if the value of user.SessionState.Expires is 0, it will be ignored.
func KeyExpired(expires int64) bool {
  if expires >= 1 {
    return time.Now().After(time.Unix(expires, 0))
  }

  return false
}
```
AuthFunc 函数依次执行了以下四大步来完成 JWT 认证，每一步中又有一些小步骤，下面我们来一起看看。


第一步，从 Authorization: Bearer XX.YY.ZZ 请求头中获取 XX.YY.ZZ，XX.YY.ZZ 即为 JWT Token。第二步，调用 github.com/dgrijalva/jwt-go 包提供的 ParseWithClaims 函数，该函数会依次执行下面四步操作。


调用 ParseUnverified 函数，依次执行以下操作：



从 Token 中获取第一段 XX，base64 解码后得到 JWT Token 的 Header{“alg”:“HS256”,“kid”:“a45yPqUnQ8gljH43jAGQdRo0bXzNLjlU0hxa”,“typ”:“JWT”}。从 Token 中获取第二段 YY，base64 解码后得到 JWT Token 的 Payload{“aud”:“iam.authz.marmotedu.com”,“exp”:1625104314,“iat”:1625097114,“iss”:“iamctl”,“nbf”:1625097114}。根据 Token Header 中的 alg 字段，获取 Token 加密函数。最终 ParseUnverified 函数会返回 Token 类型的变量，Token 类型包含 Method、Header、Claims、Valid 这些重要字段，这些字段会用于后续的认证步骤中。调用传入的 keyFunc 获取密钥，这里来看下 keyFunc 的实现：


```go
func(token *jwt.Token) (interface{}, error) {
  // Validate the alg is HMAC signature
  if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
    return nil, fmt.Errorf("unexpected signing method: %v", token.Header["alg"])
  }

  kid, ok := token.Header["kid"].(string)
  if !ok {
    return nil, ErrMissingKID
  }

  var err error
  secret, err = cache.get(kid)
  if err != nil {
    return nil, ErrMissingSecret
  }

  return []byte(secret.Key), nil
}
```
可以看到，keyFunc 接受 *Token 类型的变量，并获取 Token Header 中的 kid，kid 即为密钥 ID：secretID。接着，调用 cache.get(kid) 获取密钥 secretKey。cache.get 函数即为 getSecretFunc，getSecretFunc 函数会根据 kid，从内存中查找密钥信息，密钥信息中包含了 secretKey。



从 Token 中获取 Signature 签名字符串 ZZ，也即 Token 的第三段。获取到 secretKey 之后，token.Method.Verify 验证 Signature 签名字符串 ZZ，也即 Token 的第三段是否合法。token.Method.Verify 实际上是使用了相同的加密算法和相同的 secretKey 加密 XX.YY 字符串。假设加密之后的字符串为 WW，接下来会用 WW 和 ZZ base64 解码后的字符串进行比较，如果相等则认证通过，如果不相等则认证失败。

第三步，调用 KeyExpired，验证 secret 是否过期。secret 信息中包含过期时间，你只需要拿该过期时间和当前时间对比就行。第四步，设置 HTTP Headerusername: colin。


到这里，iam-authz-server 的 Bearer 认证分析就完成了。我们来做个总结：iam-authz-server 通过加载 Gin 中间件的方式，在请求/v1/authz接口时进行访问认证。因为 Bearer 认证具有过期时间，而且可以在认证字符串中携带更多有用信息，还具有不可逆加密等优点，**所以 /v1/authz 采用了 Bearer 认证，Token 格式采用了 JWT 格式，这也是业界在 API 认证中最受欢迎的认证方式。**


Bearer 认证需要 secretID 和 secretKey，这些信息会通过 gRPC 接口调用，从 iam-apisaerver 中获取，并缓存在 iam-authz-server 的内存中供认证时查询使用。当请求来临时，iam-authz-server Bearer 认证中间件从 JWT Token 中解析出 Header，并从 Header 的 kid 字段中获取到 secretID，根据 secretID 查找到 secretKey，最后使用 secretKey 加密 JWT Token 的 Header 和 Payload，并与 Signature 部分进行对比。如果相等，则认证通过；如果不等，则认证失败。



#### iam-apiserver Bearer 认证实现
再来看下 iam-apiserver 的 Bearer 认证。


iam-apiserver 的 Bearer 认证通过以下代码（位于router.go文件中）指定使用了 auto 认证策略：

```go
v1.Use(auto.AuthFunc())
```
我们来看下auto.AuthFunc()的实现：


```go
func (a AutoStrategy) AuthFunc() gin.HandlerFunc {
  return func(c *gin.Context) {
    operator := middleware.AuthOperator{}
    authHeader := strings.SplitN(c.Request.Header.Get("Authorization"), " ", 2)

    if len(authHeader) != authHeaderCount {
      core.WriteResponse(
        c,
        errors.WithCode(code.ErrInvalidAuthHeader, "Authorization header format is wrong."),
        nil,
      )
      c.Abort()

      return
    }

    switch authHeader[0] {
    case "Basic":
      operator.SetStrategy(a.basic)
    case "Bearer":
      operator.SetStrategy(a.jwt)
      // a.JWT.MiddlewareFunc()(c)
    default:
      core.WriteResponse(c, errors.WithCode(code.ErrSignatureInvalid, "unrecognized Authorization header."), nil)
      c.Abort()

      return
    }

    operator.AuthFunc()(c)

    c.Next()
  }
}
```
从上面代码中可以看到，AuthFunc 函数会从 Authorization Header 中解析出认证方式是 Basic 还是 Bearer。如果是 Bearer，就会使用 JWT 认证策略；如果是 Basic，就会使用 Basic 认证策略。


我们再来看下 JWT 认证策略的AuthFunc函数实现：


```go
func (j JWTStrategy) AuthFunc() gin.HandlerFunc {
  return j.MiddlewareFunc()
}
```
我们跟随代码，可以定位到MiddlewareFunc函数最终调用了github.com/appleboy/gin-jwt包GinJWTMiddleware结构体的middlewareImpl方法：



```go
func (mw *GinJWTMiddleware) middlewareImpl(c *gin.Context) {
  claims, err := mw.GetClaimsFromJWT(c)
  if err != nil {
    mw.unauthorized(c, http.StatusUnauthorized, mw.HTTPStatusMessageFunc(err, c))
    return
  }

  if claims["exp"] == nil {
    mw.unauthorized(c, http.StatusBadRequest, mw.HTTPStatusMessageFunc(ErrMissingExpField, c))
    return
  }

  if _, ok := claims["exp"].(float64); !ok {
    mw.unauthorized(c, http.StatusBadRequest, mw.HTTPStatusMessageFunc(ErrWrongFormatOfExp, c))
    return
  }

  if int64(claims["exp"].(float64)) < mw.TimeFunc().Unix() {
    mw.unauthorized(c, http.StatusUnauthorized, mw.HTTPStatusMessageFunc(ErrExpiredToken, c))
    return
  }

  c.Set("JWT_PAYLOAD", claims)
  identity := mw.IdentityHandler(c)

  if identity != nil {
    c.Set(mw.IdentityKey, identity)
  }

  if !mw.Authorizator(identity, c) {
    mw.unauthorized(c, http.StatusForbidden, mw.HTTPStatusMessageFunc(ErrForbidden, c))
    return
  }

  c.Next()
}
```
分析上面的代码，我们可以知道，middlewareImpl 的 Bearer 认证流程为：第一步：调用GetClaimsFromJWT函数，从 HTTP 请求中获取 Authorization Header，并解析出 Token 字符串，进行认证，最后返回 Token Payload。第二步：校验 Payload 中的exp是否超过当前时间，如果超过就说明 Token 过期，校验不通过。第三步：给 gin.Context 中添加JWT_PAYLOAD键，供后续程序使用（当然也可能用不到）。第四步：通过以下代码，在 gin.Context 中添加 IdentityKey 键，IdentityKey 键可以在创建GinJWTMiddleware结构体时指定，这里我们设置为middleware.UsernameKey，也就是 username。


```go
identity := mw.IdentityHandler(c)

if identity != nil {
    c.Set(mw.IdentityKey, identity)
}
```
IdentityKey 键的值由 IdentityHandler 函数返回，IdentityHandler 函数为：


```go
func(c *gin.Context) interface{} {
    claims := jwt.ExtractClaims(c)

    return claims[jwt.IdentityKey]
}
```
上述函数会从 Token 的 Payload 中获取 identity 域的值，identity 域的值是在签发 Token 时指定的，它的值其实是用户名，你可以查看payloadFunc函数了解。

第五步：接下来，会调用Authorizator方法，Authorizator是一个 callback 函数，成功时必须返回真，失败时必须返回假。Authorizator也是在创建 GinJWTMiddleware 时指定的，例如：


```go
func authorizator() func(data interface{}, c *gin.Context) bool {    
    return func(data interface{}, c *gin.Context) bool {    
        if v, ok := data.(string); ok {    
            log.L(c).Infof("user `%s` is authenticated.", v)         
                                                                     
            return true                            
        }                                                        
                                                                 
        return false                     
    }    
}    

```
authorizator函数返回了一个匿名函数，匿名函数在认证成功后，会打印一条认证成功日志。

### IAM 项目认证功能设计技巧
我在设计 IAM 项目的认证功能时，也运用了一些技巧，这里分享给你。


#### 技巧 1：面向接口编程
在使用NewAutoStrategy函数创建 auto 认证策略时，传入了middleware.AuthStrategy接口类型的参数，这意味着 Basic 认证和 Bearer 认证都可以有不同的实现，这样后期可以根据需要扩展新的认证方式。


#### 技巧 2：使用抽象工厂模式
auth.go文件中，通过 newBasicAuth、newJWTAuth、newAutoAuth 创建认证策略时，返回的都是接口。通过返回接口，可以在不公开内部实现的情况下，让调用者使用你提供的各种认证功能。



#### 技巧 3：使用策略模式
在 auto 认证策略中，我们会根据 HTTP 请求头Authorization: XXX X.Y.X中的 XXX 来选择并设置认证策略（Basic 或 Bearer）。具体可以查看AutoStrategy的[AuthFunc](https://github.com/marmotedu/iam/blob/v1.0.0/internal/pkg/middleware/auth/auto.go#L38)函数：


```go
func (a AutoStrategy) AuthFunc() gin.HandlerFunc {
  return func(c *gin.Context) {
    operator := middleware.AuthOperator{}
    authHeader := strings.SplitN(c.Request.Header.Get("Authorization"), " ", 2)
        ...
    switch authHeader[0] {
    case "Basic":
      operator.SetStrategy(a.basic)
    case "Bearer":
      operator.SetStrategy(a.jwt)
      // a.JWT.MiddlewareFunc()(c)
    default:
      core.WriteResponse(c, errors.WithCode(code.ErrSignatureInvalid, "unrecognized Authorization header."), nil)
      c.Abort()

      return
    }

    operator.AuthFunc()(c)

    c.Next()
  }
}
```

上述代码中，如果是 Basic，则设置为 Basic 认证方法operator.SetStrategy(a.basic)；如果是 Bearer，则设置为 Bearer 认证方法operator.SetStrategy(a.jwt)。 SetStrategy方法的入参是 AuthStrategy 类型的接口，都实现了AuthFunc() gin.HandlerFunc函数，用来进行认证，所以最后我们调用operator.AuthFunc()(c)即可完成认证。

### 总结
在 IAM 项目中，iam-apiserver 实现了 Basic 认证和 Bearer 认证，iam-authz-server 实现了 Bearer 认证。这一讲重点介绍了 iam-apiserver 的认证实现。


用户要访问 iam-apiserver，首先需要通过 Basic 认证，认证通过之后，会返回 JWT Token 和 JWT Token 的过期时间。前端将 Token 缓存在 LocalStorage 或 Cookie 中，后续的请求都通过 Token 来认证。执行 Basic 认证时，iam-apiserver 会从 HTTP Authorization Header 中解析出用户名和密码，将密码再加密，并和数据库中保存的值进行对比。如果不匹配，则认证失败，否则认证成功。认证成功之后，会返回 Token，并在 Token 的 Payload 部分设置用户名，Key 为 username 。


执行 Bearer 认证时，iam-apiserver 会从 JWT Token 中解析出 Header 和 Payload，并从 Header 中获取加密算法。接着，用获取到的加密算法和从配置文件中获取到的密钥对 Header.Payload 进行再加密，得到 Signature，并对比两次的 Signature 是否相等。如果不相等，则返回 HTTP 401 Unauthorized 错误；如果相等，接下来会判断 Token 是否过期，如果过期则返回认证不通过，否则认证通过。认证通过之后，会将 Payload 中的 username 添加到 gin.Context 类型的变量中，供后面的业务逻辑使用。我绘制了整个流程的示意图，你可以对照着再回顾一遍。


![img](https://static001.geekbang.org/resource/image/64/7e/642a010388e759dd76d411055bbd637e.jpg?wh=2248x1104)

## 27 | 权限模型：5大权限模型是如何进行资源授权的？
在开始讲解如何开发服务之前，我先来介绍一个比较重要的背景知识：权限模型。在你的研发生涯中，应该会遇到这样一种恐怖的操作：张三因为误操作删除了李四的资源。你在刷新闻时，也可能会刷到这么一个爆款新闻：某某程序员删库跑路。操作之所以恐怖，新闻之所以爆款，是因为这些行为往往会带来很大的损失。


那么如何避免这些风险呢？答案就是对资源做好权限管控，这也是项目开发中绕不开的话题。腾讯云会强制要求所有的云产品都对接 访问管理（CAM） 服务（阿里云也有这种要求），之所以这么做，是因为保证资源的安全是一件非常非常重要的事情。

可以说，保证应用的资源安全，已经成为一个应用的必备能力。作为开发人员，你也一定要知道如何保障应用的资源安全。那么如何才能保障资源的安全呢？我认为你至少需要掌握下面这两点：


- 权限模型：你需要了解业界成熟的权限模型，以及这些模型的适用场景。只有具备足够宽广的知识面和视野，我们才能避免闭门造车，设计出优秀的资源授权方案。
- 编码实现：选择或设计出了优秀的资源授权方案后，你就要编写代码实现该方案。这门课的 IAM 应用，就是一个资源授权方案的落地项目。你可以通过对 IAM 应用的学习，来掌握如何实现一个资源授权系统。


无论是第一点还是第二点，都需要你掌握基本的权限模型知识。那么这一讲，我就来介绍下业界优秀的权限模型，以及这些模型的适用场景，以使你今后设计出更好的**资源授权系统。**


### 权限相关术语介绍
在介绍业界常见的权限模型前，我们先来看下在权限模型中出现的术语。我把常见的术语总结在了下面的表格里：


![img](https://static001.geekbang.org/resource/image/6a/1d/6aa623500bb76b3d40a5c4c6d15be91d.jpg?wh=2248x1623)

为了方便你理解，这一讲我分别用用户、操作和资源来替代 Subject、Action 和 Object。



### 权限模型介绍
接下来，我就详细介绍下一些常见的权限模型，让你今后在设计权限系统时，能够根据需求选择合适的权限模型。


不同的权限模型具有不同的特点，可以满足不同的需求。常见的权限模型有下面这 5 种：
- 权限控制列表（ACL，Access Control List）。
- 自主访问控制（DAC，Discretionary Access Control）。
- 强制访问控制（MAC，Mandatory Access Control）。
- 基于角色的访问控制（RBAC，Role-Based Access Control）。
- 基于属性的权限验证（ABAC，Attribute-Based Access Control）。


这里先简单介绍下这 5 种权限模型。ACL 是一种简单的权限模型；DAC 基于 ACL，将权限下放给具有此权限的主题；但 DAC 因为权限下放，导致它对权限的控制过于分散，为了弥补 DAC 的这个缺陷，诞生了 MAC 权限模型。



DAC 和 MAC 都是基于 ACL 的权限模型。ACL 及其衍生的权限模型可以算是旧时代的权限模型，灵活性和功能性都满足不了现代应用的权限需求，所以诞生了 RBAC。RBAC 也是迄今为止最为普及的权限模型。但是，随着组织和应用规模的增长，所需的角色数量越来越多，变得难以管理，进而导致角色爆炸和职责分离（SoD）失败。最后，引入了一种新的、更动态的访问控制形式，称为基于属性的访问控制，也就是 ABAC。ABAC 被一些人看作是权限系统设计的未来。腾讯云的 CAM、AWS 的 IAM、阿里云的 RAM 都是 ABAC 类型的权限访问服务。

接下来，我会详细介绍这些权限模型的基本概念。


#### 简单的权限模型：权限控制列表（ACL）
ACL（Access Control List，权限控制列表），用来判断用户是否可以对资源做特定的操作。例如，允许 Colin 创建文章的 ACL 策略为：


```
Subject: Colin
Action: Create
Object: Article
```
在 ACL 权限模型下，权限管理是围绕资源 Object 来设定的，ACL 权限模型也是比较简单的一种模型。




#### 基于 ACL 下放权限的权限模型：自主访问控制（DAC）
DAC (Discretionary Access Control，自主访问控制)，是 ACL 的扩展模型，灵活性更强。使用这种模型，不仅可以判断 Subject 是否可以对 Object 做 Action 操作，同时也能让 Subject 将 Object、Action 的相同权限授权给其他的 Subject。例如，Colin 可以创建文章：


```
Subject: Colin
Action: Create
Object: Article
```

因为 Colin 具有创建文章的权限，所以 Colin 也可以授予 James 创建文章的权限：


```
Subject: James
Action: Create
Object: Article
```
经典的 ACL 模型权限集中在同一个 Subject 上，缺乏灵活性，为了加强灵活性，在 ACL 的基础上，DAC 模型将权限下放，允许拥有权限的 Subject 自主地将权限授予其他 Subject。

#### 基于 ACL 且安全性更高的权限模型：强制访问控制（MAC）
MAC (Mandatory Access Control，强制访问控制)，是 ACL 的扩展模型，安全性更高。MAC 权限模型下，Subject 和 Object 同时具有安全属性。在做授权时，需要同时满足两点才能授权通过：


- Subject 可以对 Object 做 Action 操作。
- Object 可以被 Subject 做 Action 操作。


例如，我们设定了“Colin 和 James 可以创建文章”这个 MAC 策略：


```
Subject: Colin
Action: Create
Object: Article

Subject: James
Action: Create
Object: Article
```
我们还有另外一个 MAC 策略“文章可以被 Colin 创建”：


```
Subject: Article
Action: Create
Object: Colin
```

在上述策略中，Colin 可以创建文章，但是 James 不能创建文章，因为第二条要求没有满足。这里你需要注意，在 ACL 及其扩展模型中，Subject 可以是用户，也可以是组或群组。ACL、DAC 和 MAC 是旧时代的权限控制模型，无法满足现代应用对权限控制的需求，于是诞生了新时代的权限模型：RBAC 和 ABAC。


#### 最普及的权限模型：基于角色的访问控制（RBAC）
RBAC (Role-Based Access Control，基于角色的访问控制)，引入了 Role（角色）的概念，并且将权限与角色进行关联。用户通过扮演某种角色，具有该角色的所有权限。具体如下图所示：


![img](https://static001.geekbang.org/resource/image/c5/cf/c5ab6b1a77069caac2c5de709dff32cf.jpg?wh=2248x902)

如图所示，每个用户关联一个或多个角色，每个角色关联一个或多个权限，每个权限又包含了一个或者多个操作，操作包含了对资源的操作集合。通过用户和权限解耦，可以实现非常灵活的权限管理。例如，可以满足以下两个权限场景：


第一，可以通过角色批量给一个用户授权。例如，公司新来了一位同事，需要授权虚拟机的生产、销毁、重启和登录权限。这时候，我们可以将这些权限抽象成一个运维角色。如果再有新同事来，就可以通过授权运维角色，直接批量授权这些权限，不用一个个地给用户授权这些权限。第二，可以批量修改用户的权限。例如，我们有很多用户，同属于运维角色，这时候对运维角色的任何权限变更，就相当于对运维角色关联的所有用户的权限变更，不用一个个去修改这些用户的权限。


RBAC 又分为 RBAC0、RBAC1、RBAC2、RBAC3。RBAC0 是 RBAC 的核心思想，RBAC1 是基于 RBAC 的角色分层模型，RBAC2 增加了 RBAC 的约束模型。而 RBAC3，其实相当于 RBAC1 + RBAC2。

下面我来详细介绍下这四种 RBAC。

RBAC0：基础模型，只包含核心的四要素，也就是用户（User）、角色（Role）、权限（Permission：Objects-Operations）、会话（Session）。用户和角色可以是多对多的关系，权限和角色也是多对多的关系。RBAC1：包括了 RBAC0，并且添加了角色继承。角色继承，即角色可以继承自其他角色，在拥有其他角色权限的同时，还可以关联额外的权限。RBAC2：包括 RBAC0，并且添加了约束。具有以下核心特性：


- 互斥约束：包括互斥用户、互斥角色、互斥权限。同一个用户不能拥有相互排斥的角色，两个互斥角色不能分配一样的权限集，互斥的权限不能分配给同一个角色，在 Session 中，同一个角色不能拥有互斥权限。
- 基数约束：一个角色被分配的用户数量受限，它指的是有多少用户能拥有这个角色。例如，一个角色是专门为公司 CEO 创建的，那这个角色的数量就是有限的。
- 先决条件角色：指要想获得较高的权限，要首先拥有低一级的权限。例如，先有副总经理权限，才能有总经理权限。
- 静态职责分离(Static Separation of Duty)：用户无法同时被赋予有冲突的角色。
- 动态职责分离(Dynamic Separation of Duty)：用户会话中，无法同时激活有冲突的角色。


RBAC3：全功能的 RBAC，合并了 RBAC0、RBAC1、RBAC2。此外，RBAC 也可以很方便地模拟出 DAC 和 MAC 的效果。这里举个例子，来协助你理解 RBAC。例如，我们有 write article 和 manage article 的权限：


```
Permission:
    - Name: write_article
        - Effect: "allow"
        - Action: ["Create", "Update", "Read"]
        - Object: ["Article"]
    - Name: manage_article
        - Effect: "allow"
        - Action: ["Delete", "Read"]
        - Object: ["Article"]
```
同时，我们也有 Writer、Manager 和 CEO 3 个角色，Writer 具有 write_article 权限，Manager 具有 manage_article 权限，CEO 具有所有权限：



```
Role:
    - Name: Writer
      Permissions:
        - write_article
    - Name: Manager
      Permissions:
        - manage_article
    - Name: CEO
      Permissions:
        - write_article
        - manage_article
```

接下来，我们对 Colin 用户授予 Writer 角色：


```
Subject: Colin
Roles:
    - Writer
```
那么现在 Colin 就具有 Writer 角色的所有权限 write_article，write_article 权限可以创建文章。接下来，再对 James 用户授予 Writer 和 Manager 角色：


```
Subject: James
Roles:
    - Writer
    - Manager
```

那么现在 James 就具有 Writer 角色和 Manager 角色的所有权限：write_article、manage_article，这些权限允许 James 创建和删除文章。


#### 最强大的权限模型：基于属性的权限验证（ABAC）
ABAC (Attribute-Based Access Control，基于属性的权限验证），规定了哪些属性的用户可以对哪些属性的资源在哪些限制条件下进行哪些操作。跟 RBAC 相比，ABAC 对权限的控制粒度更细，主要规定了下面这四类属性：


用户属性，例如性别、年龄、工作等。资源属性，例如创建时间、所属位置等。操作属性，例如创建、修改等。环境属性，例如来源 IP、当前时间等。


下面是一个 ABAC 策略：



```
Subject:
    Name: Colin
    Department: Product
    Role: Writer
Action:
    - create
    - update
Resource:
    Type: Article
    Tag:
        - technology
        - software
    Mode:
        - draft
Contextual:
    IP: 10.0.0.10
```
上面权限策略描述的意思是，产品部门的 Colin 作为一个 Writer 角色，可以通过来源 IP 是 10.0.0.10 的客户端，创建和更新带有 technology 和 software 标签的草稿文章。这里提示一点：ABAC 有时也被称为 PBAC（Policy-Based Access Control）或 CBAC（Claims-Based Access Control）。



这里，我通过现实中的 ABAC 授权策略，帮你理解 ABAC 权限模型。下面是一个腾讯云的 CAM 策略，也是一种 ABAC 授权模式：



```
{
  "version": "2.0",
  "statement": [
    {
      "effect": "allow",
      "action": [
        "cos:List*",
        "cos:Get*",
        "cos:Head*",
        "cos:OptionsObject"
      ],
      "resource": "qcs::cos:ap-shanghai:uid/1250000000:Bucket1-1250000000/dir1/*",
      "condition": {
        "ip_equal": {
          "qcs:ip": [
            "10.217.182.3/24",
            "111.21.33.72/24"
          ]
        }
      }
    }
  ]
}
```


上面的授权策略表示：用户必须在 10.217.182.3/24 或者 111.21.33.72/24 网段才能调用云 API（cos:List*、cos:Get*、cos:Head*、cos:OptionsObject），对 1250000000 用户下的 dir1 目录下的文件进行读取操作。这里，ABAC 规定的四类属性分别是：


- 用户属性：用户为 1250000000。
- 资源属性：dir1 目录下的文件。
- 操作属性：读取（cos:List*、cos:Get*、cos:Head*、cos:OptionsObject 都是读取 API）。
- 环境属性：10.217.182.3/24 或者 111.21.33.72/24 网段。



### 相关开源项目
上面我介绍了权限模型的相关知识，但是现在如果让你真正去实现一个权限系统，你可能还是不知从何入手。在这里，我列出了一些 GitHub 上比较优秀的开源项目，你可以学习这些项目是如何落地一个权限模型的，也可以基于这些项目进行二次开发，开发一个满足业务需求的权限系统。


#### Casbin
Casbin 是一个用 Go 语言编写的访问控制框架，功能强大，支持 ACL、RBAC、ABAC 等访问模型，很多优秀的权限管理系统都是基于 Casbin 来构建的。Casbin 的核心功能都是围绕着访问控制来构建的，不负责身份认证。如果以后老板让你实现一个权限管理系统，Casbin 是一定要好好研究的开源项目。


#### keto
keto 是一个云原生权限控制服务，通过提供 REST API 进行授权，支持 RBAC、ABAC、ACL、AWS IAM 策略、Kubernetes Roles 等权限模型，可以解决下面这些问题：是否允许某些用户修改此博客文章？是否允许某个服务打印该文档？是否允许 ACME 组织的成员修改其租户中的数据？是否允许在星期一的下午 4 点到下午 5 点，从 IP 10.0.0.2 发出的请求执行某个 Job？


#### go-admin
go-admin 是一个基于 Gin + Vue + Element UI 的前后端分离权限管理系统脚手架，它的访问控制模型采用了 Casbin 的 RBAC 访问控制模型，功能强大，包含了如下功能：

基础用户管理功能；JWT 鉴权；代码生成器；RBAC 权限控制；表单构建；……

该项目还支持 RESTful API 设计规范、Swagger 文档、GORM 库等。go-admin 不仅是一个优秀的权限管理系统，也是一个优秀的、功能齐全的 Go 开源项目。你在做项目开发时，也可以参考该项目的构建思路。go-admin 管理系统自带前端，如下图所示。


![img](https://static001.geekbang.org/resource/image/21/98/21c8307e034fc6e082833d1c9fd0f498.png?wh=2535x827)

#### LyricTian/gin-admin
[gin-admin](https://github.com/LyricTian/gin-admin) 类似于 go-admin，是一个基于 Gin+Gorm+Casbin+Wire 实现的权限管理脚手架，并自带前端，在做权限管理系统调研时，也非常值得参考。gin-admin 大量采用了 Go 后端开发常用的技术，比如 Gin、GORM、JWT 认证、RESTful API、Logrus 日志包、Swagger 文档等。因此，你在做 Go 后端服务开发时，也可以学习该项目的构建方法。


#### gin-vue-admin
gin-vue-admin 是一个基于 Gin 和 Vue 开发的全栈前后端分离的后台管理系统，集成了 JWT 鉴权、动态路由、动态菜单、Casbin 鉴权、表单生成器、代码生成器等功能。gin-vue-admin 集成了 RBAC 权限管理模型，界面如下图所示：


![img](https://static001.geekbang.org/resource/image/a3/f3/a391723e154f862d5c7bf796edcf5bf3.png?wh=2527x627)

### 选择建议
介绍了那么多优秀的开源项目，最后我想给你一些选择建议。如果你想研究 ACL、RBAC、ABAC 等权限模型如何落地，我强烈建议你学习 Casbin 项目，Casbin 目前有近万的 GitHub star 数，处于活跃的开发状态。有很多项目在使用 Casbin，例如 go-admin、 gin-admin 、 gin-vue-admin 等。


keto 类似于 Casbin，主要通过 Go 包的方式，对外提供授权能力。keto 也是一个非常优秀的权限类项目，当你研究完 Casbin 后，如果还想再研究下其他授权类项目，建议你读下 keto 的源码。


go-admin、gin-vue-admin、gin-admin 这 3 个都是基于 Casbin 的 Go 项目。其中，gin-vue-admin 是后台管理系统框架，里面包含了 RBAC 权限管理模块；go-admin 和 gin-admin 都是 RBAC 权限管理脚手架。所以，如果你想找一个比较完整的 RBAC 授权系统（自带前后端），建议你优先研究下 go-admin，如果还有精力，可以再研究下 gin-admin、gin-vue-admin。


### 总结
这一讲，我介绍了 5 种常见的权限模型。其中，ACL 最简单，ABAC 最复杂，但是功能最强大，也最灵活。RBAC 则介于二者之间。对于一些云计算厂商来说，因为它们面临的授权场景复杂多样，需要一个非常强大的授权模型，所以腾讯云、阿里云和 AWS 等云厂商普遍采用了 ABAC 模型。


如果你的资源授权需求不复杂，可以考虑 RBAC；如果你需要一个能满足复杂场景的资源授权系统，建议选择 ABAC，ABAC 的设计思路可以参考下腾讯云的 CAM、阿里云的 RAM 和 AWS 的 IAM。另外，如果你想深入了解权限模型如何具体落地，建议你阅读 [Casbin](https://github.com/casbin/casbin) 源码。

## 28 | 控制流（上）：通过iam-apiserver设计，看Web服务的构建

前面我们讲了很多关于应用构建的内容，你一定迫不及待地想看下 IAM 项目的应用是如何构建的。那么接下来，我就**讲解下 IAM 应用的源码**。在讲解过程中，我不会去讲解具体如何 Code，但会讲解一些构建过程中的重点、难点，以及 Code 背后的设计思路、想法。我相信这是对你更有帮助的。IAM 项目有很多组件，这一讲，我先来介绍下 IAM 项目的门面服务：iam-apiserver（管理流服务）。我会先给你介绍下 iam-apiserver 的功能和使用方法，再介绍下 iam-apiserver 的代码实现。


### iam-apiserver 服务介绍
iam-apiserver 是一个 Web 服务，通过一个名为 iam-apiserver 的进程，对外提供 RESTful API 接口，完成用户、密钥、策略三种 REST 资源的增删改查。接下来，我从功能和使用方法两个方面来具体介绍下。


### iam-apiserver 功能介绍
这里，我们可以通过 iam-apiserver 提供的 RESTful API 接口，来看下 iam-apiserver 具体提供的功能。iam-apiserver 提供的 RESTful API 接口可以分为四类，具体如下：



认证相关接口


![img](https://static001.geekbang.org/resource/image/43/6d/43ec9261ccdb165c56e9c25b45e6af6d.jpg?wh=1920x1062)

用户相关接口


![img](https://static001.geekbang.org/resource/image/60/24/60f8a05f4cb43cbac84c0fb12c40c824.jpg?wh=1920x1314)

密钥相关接口


![img](https://static001.geekbang.org/resource/image/e8/95/e8f6aee66a29ff2a5aefeb00c5045c95.jpg?wh=1920x1326)

策略相关接口


![img](https://static001.geekbang.org/resource/image/0f/9e/0f3fcaa80020c3f72229fbab2f014a9e.jpg?wh=1920x1275)


### iam-apiserver 使用方法介绍
上面我介绍了 iam-apiserver 的功能，接下来就介绍下如何使用这些功能。


我们可以通过不同的客户端来访问 iam-apiserver，例如前端、API 调用、SDK、iamctl 等。这些客户端最终都会执行 HTTP 请求，调用 iam-apiserver 提供的 RESTful API 接口。所以，我们首先需要有一个顺手的 REST API 客户端工具来执行 HTTP 请求，完成开发测试。



因为不同的开发者执行 HTTP 请求的方式、习惯不同，为了方便讲解，这里我统一通过 cURL 工具来执行 HTTP 请求。接下来先介绍下 cURL 工具。


标准的 Linux 发行版都安装了 cURL 工具。cURL 可以很方便地完成 RESTful API 的调用场景，比如设置 Header、指定 HTTP 请求方法、指定 HTTP 消息体、指定权限认证信息等。通过-v选项，也能输出 REST 请求的所有返回信息。cURL 功能很强大，有很多参数，这里列出 cURL 工具常用的参数：


```
-X/--request [GET|POST|PUT|DELETE|…]  指定请求的 HTTP 方法
-H/--header                           指定请求的 HTTP Header
-d/--data                             指定请求的 HTTP 消息体（Body）
-v/--verbose                          输出详细的返回信息
-u/--user                             指定账号、密码
-b/--cookie                           读取 cookie
```

此外，如果你想使用带 UI 界面的工具，这里我推荐你使用 Insomnia 。Insomnia 是一个跨平台的 REST API 客户端，与 Postman、Apifox 是一类工具，用于接口管理、测试。Insomnia 功能强大，支持以下功能：

发送 HTTP 请求；创建工作区或文件夹；导入和导出数据；导出 cURL 格式的 HTTP 请求命令；支持编写 swagger 文档；快速切换请求；URL 编码和解码。…


Insomnia 界面如下图所示：


![img](https://static001.geekbang.org/resource/image/63/e0/635aa6f3374af05ec2bff7e193314ae0.png?wh=1920x749)

当然了，也有很多其他优秀的带 UI 界面的 REST API 客户端，例如 Postman、Apifox 等，你可以根据需要自行选择。接下来，我用对 secret 资源的 CURD 操作，来给你演示下如何使用 iam-apiserver 的功能。你需要执行 6 步操作。


1. 登录 iam-apiserver，获取 token。
2. 创建一个名为 secret0 的 secret。
3. 获取 secret0 的详细信息。
4. 更新 secret0 的描述。
5. 获取 secret 列表。
6. 删除 secret0。


具体操作如下：登录 iam-apiserver，获取 token：


```
$ curl -s -XPOST -H"Authorization: Basic `echo -n 'admin:Admin@2021'|base64`" http://127.0.0.1:8080/login | jq -r .token
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MzUwNTk4NDIsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MjcyODM4NDIsInN1YiI6ImFkbWluIn0.gTS0n-7njLtpCJ7mvSnct2p3TxNTUQaduNXxqqLwGfI
```
这里，为了便于使用，我们将 token 设置为环境变量：



```
TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MzUwNTk4NDIsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MjcyODM4NDIsInN1YiI6ImFkbWluIn0.gTS0n-7njLtpCJ7mvSnct2p3TxNTUQaduNXxqqLwGfI
```
创建一个名为 secret0 的 secret：

```
$ curl -v -XPOST -H "Content-Type: application/json" -H"Authorization: Bearer ${TOKEN}" -d'{"metadata":{"name":"secret0"},"expires":0,"description":"admin secret"}' http://iam.api.marmotedu.com:8080/v1/secrets
* About to connect() to iam.api.marmotedu.com port 8080 (#0)
*   Trying 127.0.0.1...
* Connected to iam.api.marmotedu.com (127.0.0.1) port 8080 (#0)
> POST /v1/secrets HTTP/1.1
> User-Agent: curl/7.29.0
> Host: iam.api.marmotedu.com:8080
> Accept: */*
> Content-Type: application/json
> Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXBpLm1hcm1vdGVkdS5jb20iLCJleHAiOjE2MzUwNTk4NDIsImlkZW50aXR5IjoiYWRtaW4iLCJpc3MiOiJpYW0tYXBpc2VydmVyIiwib3JpZ19pYXQiOjE2MjcyODM4NDIsInN1YiI6ImFkbWluIn0.gTS0n-7njLtpCJ7mvSnct2p3TxNTUQaduNXxqqLwGfI
> Content-Length: 72
> 
* upload completely sent off: 72 out of 72 bytes
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< X-Request-Id: ff825bea-53de-4020-8e68-4e87574bd1ba
< Date: Mon, 26 Jul 2021 07:20:26 GMT
< Content-Length: 313
< 
* Connection #0 to host iam.api.marmotedu.com left intact
{"metadata":{"id":60,"instanceID":"secret-jedr3e","name":"secret0","createdAt":"2021-07-26T15:20:26.885+08:00","updatedAt":"2021-07-26T15:20:26.907+08:00"},"username":"admin","secretID":"U6CxKs0YVWyOp5GrluychYIRxDmMDFd1mOOD","secretKey":"fubNIn8jLA55ktuuTpXM8Iw5ogdR2mlf","expires":0,"description":"admin secret"}
```
可以看到，请求返回头中返回了X-Request-Id Header，X-Request-Id唯一标识这次请求。如果这次请求失败，就可以将X-Request-Id提供给运维或者开发，通过X-Request-Id定位出失败的请求，进行排障。另外X-Request-Id在微服务场景中，也可以透传给其他服务，从而实现请求调用链。

获取 secret0 的详细信息：


```
$ curl -XGET -H"Authorization: Bearer ${TOKEN}" http://iam.api.marmotedu.com:8080/v1/secrets/secret0
{"metadata":{"id":60,"instanceID":"secret-jedr3e","name":"secret0","createdAt":"2021-07-26T15:20:26+08:00","updatedAt":"2021-07-26T15:20:26+08:00"},"username":"admin","secretID":"U6CxKs0YVWyOp5GrluychYIRxDmMDFd1mOOD","secretKey":"fubNIn8jLA55ktuuTpXM8Iw5ogdR2mlf","expires":0,"description":"admin secret"}
```
更新 secret0 的描述：


```
$ curl -XPUT -H"Authorization: Bearer ${TOKEN}" -d'{"metadata":{"name":"secret"},"expires":0,"description":"admin secret(modify)"}' http://iam.api.marmotedu.com:8080/v1/secrets/secret0
{"metadata":{"id":60,"instanceID":"secret-jedr3e","name":"secret0","createdAt":"2021-07-26T15:20:26+08:00","updatedAt":"2021-07-26T15:23:35.878+08:00"},"username":"admin","secretID":"U6CxKs0YVWyOp5GrluychYIRxDmMDFd1mOOD","secretKey":"fubNIn8jLA55ktuuTpXM8Iw5ogdR2mlf","expires":0,"description":"admin secret(modify)"}
```
获取 secret 列表：


```
$ curl -XGET -H"Authorization: Bearer ${TOKEN}" http://iam.api.marmotedu.com:8080/v1/secrets
{"totalCount":1,"items":[{"metadata":{"id":60,"instanceID":"secret-jedr3e","name":"secret0","createdAt":"2021-07-26T15:20:26+08:00","updatedAt":"2021-07-26T15:23:35+08:00"},"username":"admin","secretID":"U6CxKs0YVWyOp5GrluychYIRxDmMDFd1mOOD","secretKey":"fubNIn8jLA55ktuuTpXM8Iw5ogdR2mlf","expires":0,"description":"admin secret(modify)"}]}
```
删除 secret0：


```
$ curl -XDELETE -H"Authorization: Bearer ${TOKEN}" http://iam.api.marmotedu.com:8080/v1/secrets/secret0
null
```

上面，我给你演示了密钥的使用方法。用户和策略资源类型的使用方法跟密钥类似。详细的使用方法你可以参考 [test.sh](https://github.com/marmotedu/iam/blob/v1.0.6/scripts/install/test.sh) 脚本，该脚本是用来测试 IAM 应用的，里面包含了各个接口的请求方法。


这里，我还想顺便介绍下如何测试 IAM 应用中的各个部分。确保 iam-apiserver、iam-authz-server、iam-pump 等服务正常运行后，进入到 IAM 项目的根目录，执行以下命令：


```
$ ./scripts/install/test.sh iam::test::test # 测试整个IAM应用是否正常运行
$ ./scripts/install/test.sh iam::test::login # 测试登陆接口是否可以正常访问
$ ./scripts/install/test.sh iam::test::user # 测试用户接口是否可以正常访问
$ ./scripts/install/test.sh iam::test::secret # 测试密钥接口是否可以正常访问
$ ./scripts/install/test.sh iam::test::policy # 测试策略接口是否可以正常访问
$ ./scripts/install/test.sh iam::test::apiserver # 测试iam-apiserver服务是否正常运行
$ ./scripts/install/test.sh iam::test::authz # 测试authz接口是否可以正常访问
$ ./scripts/install/test.sh iam::test::authzserver # 测试iam-authz-server服务是否正常运行
$ ./scripts/install/test.sh iam::test::pump # 测试iam-pump是否正常运行
$ ./scripts/install/test.sh iam::test::iamctl # 测试iamctl工具是否可以正常使用
$ ./scripts/install/test.sh iam::test::man # 测试man文件是否正确安装
```
所以，每次发布完 iam-apiserver 后，你可以执行以下命令来完成 iam-apiserver 的冒烟测试：


```
$ export IAM_APISERVER_HOST=127.0.0.1 # iam-apiserver部署服务器的IP地址
$ export IAM_APISERVER_INSECURE_BIND_PORT=8080 # iam-apiserver HTTP服务的监听端口
$ ./scripts/install/test.sh iam::test::apiserver
```

### iam-apiserver 代码实现
上面，我介绍了 iam-apiserver 的功能和使用方法，这里我们再来看下 iam-apiserver 具体的代码实现。我会从配置处理、启动流程、请求处理流程、代码架构 4 个方面来讲解。


#### iam-apiserver 配置处理
iam-apiserver 服务的 main 函数位于apiserver.go文件中，你可以跟读代码，了解 iam-apiserver 的代码实现。这里，我来介绍下 iam-apiserver 服务的一些设计思想。


首先，来看下 iam-apiserver 中的 3 种配置：
- Options 配置、应用配置和 HTTP/GRPC 服务配置。Options 配置：用来构建命令行参数，它的值来自于命令行选项或者配置文件（也可能是二者 Merge 后的配置）。Options 可以用来构建应用框架，Options 配置也是应用配置的输入。
- 应用配置：iam-apiserver 组件中需要的一切配置。有很多地方需要配置，例如，启动 HTTP/GRPC 需要配置监听地址和端口，初始化数据库需要配置数据库地址、用户名、密码等。
- HTTP/GRPC 服务配置：启动 HTTP 服务或者 GRPC 服务需要的配置。


这三种配置的关系如下图：


![img](https://static001.geekbang.org/resource/image/8c/b5/8ca8d9fa1efaab21e012471874e89cb5.jpg?wh=1346x727)

Options 配置接管命令行选项，应用配置接管整个应用的配置，HTTP/GRPC 服务配置接管跟 HTTP/GRPC 服务相关的配置。这 3 种配置独立开来，可以解耦命令行选项、应用和应用内的服务，使得这 3 个部分可以独立扩展，又不相互影响。


iam-apiserver 根据 Options 配置来构建命令行参数和应用配置。



我们通过github.com/marmotedu/iam/pkg/app包的buildCommand方法来构建命令行参数。这里的核心是，通过NewApp函数构建 Application 实例时，传入的Options实现了Flags() (fss cliflag.NamedFlagSets)方法，通过 buildCommand 方法中的以下代码，将 option 的 Flag 添加到 cobra 实例的 FlagSet 中：


```go
  if a.options != nil {
      namedFlagSets = a.options.Flags()
      fs := cmd.Flags()
      for _, f := range namedFlagSets.FlagSets {
        fs.AddFlagSet(f)
      }
  
            ...
    }
```

通过[CreateConfigFromOptions](https://github.com/marmotedu/iam/blob/v1.0.4/internal/apiserver/config/config.go#L16)函数来构建应用配置：


```
        cfg, err := config.CreateConfigFromOptions(opts)                      
        if err != nil {                                               
            return err                                                
        }  
```
根据应用配置来构建 HTTP/GRPC 服务配置。例如，以下代码根据应用配置，构建了 HTTP 服务器的 Address 参数：



```go
func (s *InsecureServingOptions) ApplyTo(c *server.Config) error {
    c.InsecureServing = &server.InsecureServingInfo{
        Address: net.JoinHostPort(s.BindAddress, strconv.Itoa(s.BindPort)),
    }

    return nil
}

```
其中，c *server.Config是 HTTP 服务器的配置，s *InsecureServingOptions是应用配置。


#### iam-apiserver 启动流程设计
接下来，我们来详细看下 iam-apiserver 的启动流程设计。启动流程如下图所示：


![img](https://static001.geekbang.org/resource/image/8a/c7/8a94938bc087ed96d0ec87261db292c7.jpg?wh=4770x1487)

首先，通过opts := options.NewOptions()创建带有默认值的 Options 类型变量 opts。opts 变量作为github.com/marmotedu/iam/pkg/app包的NewApp函数的输入参数，最终在 App 框架中，被来自于命令行参数或配置文件的配置（也可能是二者 Merge 后的配置）所填充，opts 变量中各个字段的值会用来创建应用配置。


接着，会注册run函数到 App 框架中。run 函数是 iam-apiserver 的启动函数，里面封装了我们自定义的启动逻辑。run 函数中，首先会初始化日志包，这样我们就可以根据需要，在后面的代码中随时记录日志了。


然后，会创建应用配置。应用配置和 Options 配置其实是完全独立的，二者可能完全不同，但在 iam-apiserver 中，二者配置项是相同的。之后，根据应用配置，创建 HTTP/GRPC 服务器所使用的配置。在创建配置后，会先分别进行配置补全，再使用补全后的配置创建 Web 服务实例，例如：


```go
genericServer, err := genericConfig.Complete().New()
if err != nil {
    return nil, err
}
extraServer, err := extraConfig.complete().New()
if err != nil {
    return nil, err
}
...
func (c *ExtraConfig) complete() *completedExtraConfig {
    if c.Addr == "" {
        c.Addr = "127.0.0.1:8081"
    }

    return &completedExtraConfig{c}
}
```
上面的代码中，首先调用Complete/complete函数补全配置，再基于补全后的配置，New 一个 HTTP/GRPC 服务实例。


这里有个设计技巧：complete函数返回的是一个*completedExtraConfig类型的实例，在创建 GRPC 实例时，是调用completedExtraConfig结构体提供的New方法，这种设计方法可以确保我们创建的 GRPC 实例一定是基于 complete 之后的配置（completed）。

在实际的 Go 项目开发中，我们需要提供一种机制来处理或补全配置，这在 Go 项目开发中是一个非常有用的步骤。

最后，调用PrepareRun方法，进行 HTTP/GRPC 服务器启动前的准备。在准备函数中，我们可以做各种初始化操作，例如初始化数据库，安装业务相关的 Gin 中间件、RESTful API 路由等。完成 HTTP/GRPC 服务器启动前的准备之后，调用Run方法启动 HTTP/GRPC 服务。在Run方法中，分别启动了 GRPC 和 HTTP 服务。


可以看到，整个 iam-apiserver 的软件框架是比较清晰的。服务启动后，就可以处理请求了。所以接下来，我们再来看下 iam-apiserver 的 RESTAPI 请求处理流程。


#### iam-apiserver 的 REST API 请求处理流程
iam-apiserver 的请求处理流程也是清晰、规范的，具体流程如下图所示：


![img](https://static001.geekbang.org/resource/image/94/76/9400e9855b10yyac47871a7af87e9776.jpg?wh=5771x1691)

结合上面这张图，我们来看下 iam-apiserver 的 REST API 请求处理流程，来帮你更好地理解 iam-apiserver 是如何处理 HTTP 请求的。


首先，我们通过 API 调用（ + ）请求 iam-apiserver 提供的 RESTful API 接口。接着，Gin Web 框架接收到 HTTP 请求之后，会通过认证中间件完成请求的认证，iam-apiserver 提供了 Basic 认证和 Bearer 认证两种认证方式。认证通过后，请求会被我们加载的一系列中间件所处理，例如跨域、RequestID、Dump 等中间件。最后，根据 + 进行路由匹配。


举个例子，假设我们请求的 RESTful API 是POST + /v1/secrets，Gin Web 框架会根据 HTTP Method 和 HTTP Request Path，查找注册的 Controllers，最终匹配到[secretController.Create](https://github.com/marmotedu/iam/blob/v1.0.4/internal/apiserver/controller/v1/secret/create.go)Controller。在 Create Controller 中，我们会依次执行请求参数解析、请求参数校验、调用业务层的方法创建 Secret、处理业务层的返回结果，最后返回最终的 HTTP 请求结果。


#### iam-apiserver 代码架构
iam-apiserver 代码设计遵循简洁架构设计，一个简洁架构具有以下 5 个特性：


- 独立于框架：该架构不会依赖于某些功能强大的软件库存在。这可以让你使用这样的框架作为工具，而不是让你的系统陷入到框架的约束中。
- 可测试性：业务规则可以在没有 UI、数据库、Web 服务或其他外部元素的情况下进行测试，在实际的开发中，我们通过 Mock 来解耦这些依赖。
- 独立于 UI ：在无需改变系统其他部分的情况下，UI 可以轻松地改变。例如，在没有改变业务规则的情况下，Web UI 可以替换为控制台 UI。
- 独立于数据库：你可以用 Mongo、Oracle、Etcd 或者其他数据库来替换 MariaDB，你的业务规则不要绑定到数据库。
- 独立于外部媒介：实际上，你的业务规则可以简单到根本不去了解外部世界。


所以，基于这些约束，每一层都必须是独立的和可测试的。iam-apiserver 代码架构分为 4 层：模型层（Models）、控制层（Controller）、业务层 （Service）、仓库层（Repository）。从控制层、业务层到仓库层，从左到右层级依次加深。模型层独立于其他层，可供其他层引用。如下图所示：


![img](https://static001.geekbang.org/resource/image/f2/f0/f2fffd84dfbc1a6643887db3d5d541f0.jpg?wh=2498x747)


层与层之间导入包时，都有严格的导入关系，这可以防止包的循环导入问题。导入关系如下：
- 模型层的包可以被仓库层、业务层和控制层导入；
- 控制层能够导入业务层和仓库层的包。这里需要注意，如果没有特殊需求，控制层要避免导入仓库层的包，控制层需要完成的业务功能都通过业务层来完成。这样可以使代码逻辑更加清晰、规范。
- 业务层能够导入仓库层的包。


接下来，我们就来详细看下每一层所完成的功能，以及其中的一些注意点。


模型层（Models）模型层在有些软件架构中也叫做实体层（Entities），模型会在每一层中使用，在这一层中存储对象的结构和它的方法。IAM 项目模型层中的模型存放在github.com/marmotedu/api/apiserver/v1目录下，定义了User、UserList、Secret、SecretList、Policy、PolicyList、AuthzPolicy模型及其方法。例如：

```go
type Secret struct {
  // May add TypeMeta in the future.
  // metav1.TypeMeta `json:",inline"`

  // Standard object's metadata.
  metav1.ObjectMeta `       json:"metadata,omitempty"`
  Username          string `json:"username"           gorm:"column:username"  validate:"omitempty"`
  SecretID          string `json:"secretID"           gorm:"column:secretID"  validate:"omitempty"`
  SecretKey         string `json:"secretKey"          gorm:"column:secretKey" validate:"omitempty"`

  // Required: true
  Expires     int64  `json:"expires"     gorm:"column:expires"     validate:"omitempty"`
  Description string `json:"description" gorm:"column:description" validate:"description"`
}
```
之所以将模型层的模型存放在github.com/marmotedu/api项目中，而不是github.com/marmotedu/iam项目中，是为了让这些模型能够被其他项目使用。例如，iam 的模型可以被github.com/marmotedu/shippy应用导入。同样，shippy 应用的模型也可以被 iam 项目导入，导入关系如下图所示：


![img](https://static001.geekbang.org/resource/image/13/c9/1307e374f4193ecc3d5b73a987cdd0c9.jpg?wh=3896x1433)
上面的依赖关系都是单向的，依赖关系清晰，不存在循环依赖的情况。要增加 shippy 的模型定义，只需要在 api 目录下创建新的目录即可。例如，shippy 应用中有一个 vessel 服务，其模型所在的包可以为github.com/marmotedu/api/vessel。

另外，这里的模型既可以作为数据库模型，又可以作为 API 接口的请求模型（入参、出参）。如果我们能够确保**创建资源时的属性、资源保存在数据库中的属性、返回资源的属性三者一致**，就可以使用同一个模型。通过使用同一个模型，可以使我们的代码更加简洁、易维护，并能提高开发效率。如果这三个属性有差异，你可以另外新建模型来适配。


仓库层（Repository)
仓库层用来跟数据库 / 第三方服务进行 CURD 交互，作为应用程序的数据引擎进行应用数据的输入和输出。这里需要注意，仓库层仅对数据库 / 第三方服务执行 CRUD 操作，不封装任何业务逻辑。仓库层也负责选择应用中将要使用什么样的数据库，可以是 MySQL、MongoDB、MariaDB、Etcd 等。无论使用哪种数据库，都要在这层决定。仓库层依赖于连接数据库或其他第三方服务（如果存在的话）。

这一层也会起到数据转换的作用：将从数据库 / 微服务中获取的数据转换为控制层、业务层能识别的数据结构，将控制层、业务层的数据格式转换为数据库或微服务能识别的数据格式。iam-apiserver 的仓库层位于internal/apiserver/store/mysql目录下，里面的方法用来跟 MariaDB 进行交互，完成 CURD 操作，例如，从数据库中获取密钥：



```go
func (s *secrets) Get(ctx context.Context, username, name string, opts metav1.GetOptions) (*v1.Secret, error) {
    secret := &v1.Secret{}
    err := s.db.Where("username = ? and name= ?", username, name).First(&secret).Error
    if err != nil {
        if errors.Is(err, gorm.ErrRecordNotFound) {
            return nil, errors.WithCode(code.ErrSecretNotFound, err.Error())
        }

        return nil, errors.WithCode(code.ErrDatabase, err.Error())
    }

    return secret, nil
}

```


业务层 (Service)
业务层主要用来完成业务逻辑处理，我们可以把所有的业务逻辑处理代码放在业务层。业务层会处理来自控制层的请求，并根据需要请求仓库层完成数据的 CURD 操作。业务层功能如下图所示：


![img](https://static001.geekbang.org/resource/image/61/b6/6103c58d837fd81769977bc3c947ffb6.jpg?wh=1796x1236)

iam-apiserver 的业务层位于internal/apiserver/service目录下。下面是 iam-apiserver 业务层中，用来创建密钥的函数：



```go
func (s *secretService) Create(ctx context.Context, secret *v1.Secret, opts metav1.CreateOptions) error {
    if err := s.store.Secrets().Create(ctx, secret, opts); err != nil {
        return errors.WithCode(code.ErrDatabase, err.Error())
    }

    return nil
}
```
可以看到，业务层最终请求仓库层的s.store的Create方法，将密钥信息保存在 MariaDB 数据库中。


控制层（Controller）
控制层接收 HTTP 请求，并进行参数解析、参数校验、逻辑分发处理、请求返回这些操作。控制层会将逻辑分发给业务层，业务层处理后返回，返回数据在控制层中被整合再加工，最终返回给请求方。控制层相当于实现了业务路由的功能。具体流程如下图所示：


![img](https://static001.geekbang.org/resource/image/12/08/120137fc2749aa12a013099ec11e1b08.jpg?wh=960x1029)


这里我有个建议，不要在控制层写复杂的代码，如果需要，请将这些代码分发到业务层或其他包中。iam-apiserver 的控制层位于internal/apiserver/controller目录下。下面是 iam-apiserver 控制层中创建密钥的代码：


```go
func (s *SecretHandler) Create(c *gin.Context) {
  log.L(c).Info("create secret function called.")

  var r v1.Secret

  if err := c.ShouldBindJSON(&r); err != nil {
    core.WriteResponse(c, errors.WithCode(code.ErrBind, err.Error()), nil)

    return
  }

  if errs := r.Validate(); len(errs) != 0 {
    core.WriteResponse(c, errors.WithCode(code.ErrValidation, errs.ToAggregate().Error()), nil)

    return
  }

  username := c.GetString(middleware.UsernameKey)

  secrets, err := s.srv.Secrets().List(c, username, metav1.ListOptions{
    Offset: pointer.ToInt64(0),
    Limit:  pointer.ToInt64(-1),
  })
  if err != nil {
    core.WriteResponse(c, errors.WithCode(code.ErrDatabase, err.Error()), nil)

    return
  }

  if secrets.TotalCount >= maxSecretCount {
    core.WriteResponse(c, errors.WithCode(code.ErrReachMaxCount, "secret count: %d", secrets.TotalCount), nil)

    return
  }

  // must reassign username
  r.Username = username

  if err := s.srv.Secrets().Create(c, &r, metav1.CreateOptions{}); err != nil {
    core.WriteResponse(c, err, nil)

    return
  }

  core.WriteResponse(c, nil, r)
}

```

上面的代码完成了以下操作：
- 解析 HTTP 请求参数。
- 进行参数验证，这里可以添加一些业务性质的参数校验，例如：secrets.TotalCount >= maxSecretCount。
- 调用业务层s.srv的Create方法，完成密钥的创建。
- 返回 HTTP 请求参数。


上面，我们介绍了 iam-apiserver 采用的 4 层结构，接下来我们再看看每一层之间是如何通信的。

除了模型层，控制层、业务层、仓库层之间都是通过接口进行通信的。通过接口通信，一方面可以使相同的功能支持不同的实现（也就是说具有插件化能力），另一方面也使得每一层的代码变得可测试。


这里，我用创建密钥 API 请求的例子，来给你讲解下层与层之间是如何进行通信的。



首先，**来看下控制层如何跟业务层进行通信。**对密钥的请求处理都是通过 SecretController 提供的方法来处理的，创建密钥调用的是它的Create方法：



```go
func (s *SecretController) Create(c *gin.Context) {
    ...
  if err := s.srv.Secrets().Create(c, &r, metav1.CreateOptions{}); err != nil {
    core.WriteResponse(c, err, nil)

    return
  }
  ...
}
```
在Create方法中，调用了s.srv.Secrets().Create()来创建密钥，s.srv是一个接口类型，定义如下：


```go
type Service interface {
    Users() UserSrv
    Secrets() SecretSrv
    Policies() PolicySrv
}

type SecretSrv interface {                                                             
    Create(ctx context.Context, secret *v1.Secret, opts metav1.CreateOptions) error    
    Update(ctx context.Context, secret *v1.Secret, opts metav1.UpdateOptions) error            
    Delete(ctx context.Context, username, secretID string, opts metav1.DeleteOptions) error                        
    DeleteCollection(ctx context.Context, username string, secretIDs []string, opts metav1.DeleteOptions) error    
    Get(ctx context.Context, username, secretID string, opts metav1.GetOptions) (*v1.Secret, error)    
    List(ctx context.Context, username string, opts metav1.ListOptions) (*v1.SecretList, error)    
} 
```
可以看到，控制层通过业务层提供的Service接口类型，剥离了业务层的具体实现。业务层的 Service 接口类型提供了Secrets()方法，该方法返回了一个实现了SecretSrv接口的实例。在控制层中，通过调用该实例的Create(ctx context.Context, secret *v1.Secret, opts metav1.CreateOptions) error方法来完成密钥的创建。至于业务层是如何创建密钥的，控制层不需要知道，也就是说创建密钥可以有多种实现。


这里使用到了设计模式中的**工厂方法模式。**Service是工厂接口，里面包含了一系列创建具体业务层对象的工厂函数：Users()、Secrets()、Policies()。通过工厂方法模式，不仅隐藏了业务层对象的创建细节，而且还可以很方便地在Service工厂接口实现方法中添加新的业务层对象。例如，我们想新增一个Template业务层对象，用来在 iam-apiserver 中预置一些策略模板，可以这么来加：


```go
type Service interface {
    Users() UserSrv
    Secrets() SecretSrv
    Policies() PolicySrv
    Templates() TemplateSrv
}

func (s *service) Templates() TemplateSrv {
    return newTemplates(s)
}

```

接下来，新建一个template.go文件：



```go
type TemplateSrv interface {
    Create(ctx context.Context, template *v1.Template, opts metav1.CreateOptions) error
    // Other methods
}

type templateService struct {
    store store.Factory
}

var _ TemplateSrv = (*templateService)(nil)

func newTemplates(srv *service) *TemplateService {
    // more create logic
    return &templateService{store: srv.store}
}

func (u *templateService) Create(ctx context.Context, template *v1.Template, opts metav1.CreateOptions) error {
    // normal code

    return nil
}
```
可以看到，我们通过以下三步新增了一个业务层对象：
1. 在Service接口定义中，新增了一个入口：Templates() TemplateSrv。
2. 在service.go文件中，新增了一个函数：Templates()。
3. 新建了template.go文件，在template.go中定义了 templateService 结构体，并为它实现了TemplateSrv接口。

可以看到，我们新增的 Template 业务对象的代码几乎都闭环在template.go文件中。对已有的Service工厂接口的创建方法，除了新增一个工厂方法Templates() TemplateSrv外，没有其他任何入侵。这样做可以避免影响已有业务。


在实际项目开发中，你也有可能会想到下面这种错误的创建方式：



```go
// 错误方法一
type Service interface {
    UserSrv
    SecretSrv
    PolicySrv
    TemplateSrv
}
```

上面的创建方式中，我们如果想创建 User 和 Secret，那只能定义两个不同的方法：CreateUser 和 CreateSecret，远没有在 User 和 Secret 各自的域中提供同名的 Create 方法来得优雅。IAM 项目中还有其他地方也使用了工厂方法模式，例如[Factory](https://github.com/marmotedu/iam/blob/v1.0.4/internal/apiserver/store/store.go#L12)工厂接口。

再来看下**业务层和仓库层是如何通信的**。业务层和仓库层也是通过接口来通信的。例如，在业务层中创建密钥的代码如下：


```go
func (s *secretService) Create(ctx context.Context, secret *v1.Secret, opts metav1.CreateOptions) error {
    if err := s.store.Secrets().Create(ctx, secret, opts); err != nil {
        return errors.WithCode(code.ErrDatabase, err.Error())
    }

    return nil
}
```
Create方法中调用了s.store.Secrets().Create()方法来将密钥保存到数据库中。s.store是一个接口类型，定义如下：


```go
type Factory interface {
    Users() UserStore
    Secrets() SecretStore
    Policies() PolicyStore
    Close() error
}
```

业务层与仓库层的通信实现，和控制层与业务层的通信实现类似，所以这里不再详细介绍。到这里我们知道了，控制层、业务层和仓库层之间是通过接口来通信的。通过接口通信有一个好处，就是可以让各层变得可测。那接下来，我们就来看下如何测试各层的代码。因为第 38 讲和第 39 讲会详细介绍**如何测试 Go 代码**，所以这里只介绍下测试思路。


模型层
因为模型层不依赖其他任何层，我们只需要测试其中定义的结构及其函数和方法即可。


控制层
控制层依赖于业务层，意味着该层需要业务层来支持测试。你可以通过golang/mock来 mock 业务层，测试用例可参考[TestUserController_Create](https://github.com/marmotedu/iam/blob/v1.0.4/internal/apiserver/controller/v1/user/create_test.go#L19)。


业务层
因为该层依赖于仓库层，意味着该层需要仓库层来支持测试。我们有两种方法来模拟仓库层：通过golang/mock来 mock 仓库层。自己开发一个 fake 仓库层。


使用golang/mock的测试用例，你可以参考[Test_secretService_Create](https://github.com/marmotedu/iam/blob/v1.0.4/internal/apiserver/service/v1/secret_test.go#L19)。fake 的仓库层可以参考[fake](https://github.com/marmotedu/iam/tree/v1.0.4/internal/apiserver/store/fake)，使用该 fake 仓库层进行测试的测试用例为 [Test_userService_List](https://github.com/marmotedu/iam/blob/v1.0.4/internal/apiserver/service/v1/user_test.go#L76)。


仓库层
仓库层依赖于数据库，如果调用了其他微服务，那还会依赖第三方服务。我们可以通过sqlmock来模拟数据库连接，通过httpmock来模拟 HTTP 请求。


### 总结
这一讲，我主要介绍了 iam-apiserver 的功能和使用方法，以及它的代码实现。iam-apiserver 是一个 Web 服务，提供了 REST API 来完成用户、密钥、策略三种 REST 资源的增删改查。我们可以通过 cURL、Insomnia 等工具，来完成 REST API 请求。


iam-apiserver 包含了 3 种配置：Options 配置、应用配置、HTTP/GRPC 服务配置。这三种配置分别用来构建命令行参数、应用和 HTTP/GRPC 服务。


iam-apiserver 在启动时，会先构建应用框架，接着会设置应用选项，然后对应用进行初始化，最后创建 HTTP/GRPC 服务的配置和实例，最终启动 HTTP/GRPC 服务。服务启动之后，就可以接收 HTTP 请求了。一个 HTTP 请求会先进行认证，接着会被注册的中间件处理，然后，会根据(HTTP Method, HTTP Request Path)匹配到处理函数。在处理函数中，会解析请求参数、校验参数、调用业务逻辑处理函数，最终返回请求结果。


iam-apiserver 采用了简洁架构，整个应用分为 4 层：模型层、控制层、业务层和仓库层。模型层存储对象的结构和它的方法；仓库层用来跟数据库 / 第三方服务进行 CURD 交互；业务层主要用来完成业务逻辑处理；控制层接收 HTTP 请求，并进行参数解析、参数校验、逻辑分发处理、请求返回操作。控制层、业务层、仓库层之间通过接口通信，通过接口通信可以使相同的功能支持不同的实现，并使每一层的代码变得可测试。

## 29｜控制流（下）：iam-apiserver服务核心功能实现讲解
上一讲，我介绍了 iam-apiserver 是如何构建 Web 服务的。这一讲，我们再来看下 iam-apiserver 中的核心功能实现。在对这些核心功能的讲解中，我会向你传达我的程序设计思路。


iam-apiserver 中包含了很多优秀的设计思想和实现，这些点可能比较零碎，但我觉得很值得分享给你。我将这些关键代码设计分为 3 类，分别是**应用框架相关的特性、编程规范相关的特性和其他特性**。接下来，我们就来详细看看这些设计点，以及它们背后的设计思想。


### 应用框架相关的特性
应用框架相关的特性包括三个，分别是优雅关停、健康检查和插件化加载中间件。


#### 优雅关停
在讲优雅关停之前，先来看看不优雅的停止服务方式是什么样的。当我们需要重启服务时，首先需要停止服务，这时可以通过两种方式来停止我们的服务：

- 在 Linux 终端键入 Ctrl + C（其实是发送 SIGINT 信号）。
- 发送 SIGTERM 信号，例如 kill 或者 systemctl stop 等。



当我们使用以上两种方式停止服务时，都会产生下面两个问题：


- 有些请求正在处理，如果服务端直接退出，会造成客户端连接中断，请求失败。
- 我们的程序可能需要做一些清理工作，比如等待进程内任务队列的任务执行完成，或者拒绝接受新的消息等。

这些问题都会对业务造成影响，所以我们需要一种优雅的方式来关停我们的应用。在 Go 开发中，通常通过拦截 SIGINT 和 SIGTERM 信号，来实现优雅关停。当收到这两个信号时，应用进程会做一些清理工作，然后结束阻塞状态，继续执行余下的代码，最后自然退出进程。

先来看一个简单的优雅关停的示例代码：

```go
package main

import (
    "context"
    "log"
    "net/http"
    "os"
    "os/signal"
    "time"

    "github.com/gin-gonic/gin"
)

func main() {
    router := gin.Default()
    router.GET("/", func(c *gin.Context) {
        time.Sleep(5 * time.Second)
        c.String(http.StatusOK, "Welcome Gin Server")
    })

    srv := &http.Server{
        Addr:    ":8080",
        Handler: router,
    }

    go func() {
        // 将服务在 goroutine 中启动
        if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
            log.Fatalf("listen: %s\n", err)
        }
    }()

    quit := make(chan os.Signal)
    signal.Notify(quit, os.Interrupt)
    <-quit // 阻塞等待接收 channel 数据
    log.Println("Shutdown Server ...")

    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second) // 5s 缓冲时间处理已有请求
    defer cancel()
    if err := srv.Shutdown(ctx); err != nil { // 调用 net/http 包提供的优雅关闭函数：Shutdown
        log.Fatal("Server Shutdown:", err)
    }
    log.Println("Server exiting")
}

```

上面的代码实现优雅关停的思路如下：
1. 将 HTTP 服务放在 goroutine 中运行，程序不阻塞，继续执行。
2. 创建一个无缓冲的 channel quit，调用 signal.Notify(quit, os.Interrupt)。通过 signal.Notify 函数调用，可以将进程收到的 os.Interrupt（SIGINT）信号，发送给 channel quit。
3. <-quit 阻塞当前 goroutine（也就是 main 函数所在的 goroutine），等待从 channel quit 接收关停信号。通过以上步骤，我们成功启动了 HTTP 服务，并且 main 函数阻塞，防止启动 HTTP 服务的 goroutine 退出。当我们键入 Ctrl + C时，进程会收到 SIGINT 信号，并将该信号发送到 channel quit 中，这时候<-quit收到了 channel 另一端传来的数据，结束阻塞状态，程序继续执行。这里，<-quit唯一目的是阻塞当前的 goroutine，所以对收到的数据直接丢弃。
4. 打印退出消息，提示准备退出当前服务。
5. 调用 net/http 包提供的 Shutdown 方法，Shutdown 方法会在指定的时间内处理完现有请求，并返回。
6. 最后，程序执行完 log.Println("Server exiting") 代码后，退出 main 函数。


iam-apiserver 也实现了优雅关停，优雅关停思路跟上面的代码类似。具体可以分为三个步骤，流程如下：

第一步，创建 channel 用来接收 os.Interrupt（SIGINT）和 syscall.SIGTERM（SIGKILL）信号。代码见 internal/pkg/server/signal.go 。


```go
var onlyOneSignalHandler = make(chan struct{})

var shutdownHandler chan os.Signal

func SetupSignalHandler() <-chan struct{} {
    close(onlyOneSignalHandler) // panics when called twice

    shutdownHandler = make(chan os.Signal, 2)

    stop := make(chan struct{})

    signal.Notify(shutdownHandler, shutdownSignals...)

    go func() {
        <-shutdownHandler
        close(stop)
        <-shutdownHandler
        os.Exit(1) // second signal. Exit directly.
    }()

    return stop
}
```
SetupSignalHandler 函数中，通过 close(onlyOneSignalHandler)来确保 iam-apiserver 组件的代码只调用一次 SetupSignalHandler 函数。否则，可能会因为信号传给了不同的 shutdownHandler，而造成信号丢失。SetupSignalHandler 函数还实现了一个功能：收到一次 SIGINT/ SIGTERM 信号，程序优雅关闭。收到两次 SIGINT/ SIGTERM 信号，程序强制关闭。实现代码如下：

```go
go func() {
    <-shutdownHandler
    close(stop)
    <-shutdownHandler
    os.Exit(1) // second signal. Exit directly.
}()
```
这里要注意：signal.Notify(c chan<- os.Signal, sig ...os.Signal)函数不会为了向 c 发送信息而阻塞。也就是说，如果发送时 c 阻塞了，signal 包会直接丢弃信号。为了不丢失信号，我们创建了有缓冲的 channel shutdownHandler。最后，SetupSignalHandler 函数会返回 stop，后面的代码可以通过关闭 stop 来结束代码的阻塞状态。



第二步，将 channel stop 传递给启动 HTTP（S）、gRPC 服务的函数，在函数中以 goroutine 的方式启动 HTTP（S）、gRPC 服务，然后执行 <-stop 阻塞 goroutine。


第三步，当 iam-apiserver 进程收到 SIGINT/SIGTERM 信号后，关闭 stop channel，继续执行 <-stop 后的代码，在后面的代码中，我们可以执行一些清理逻辑，或者调用 google.golang.org/grpc和 net/http包提供的优雅关停函数 GracefulStop 和 Shutdown。例如下面这个代码（位于 internal/apiserver/grpc.go 文件中）：


```go
func (s *grpcAPIServer) Run(stopCh <-chan struct{}) {
    listen, err := net.Listen("tcp", s.address)
    if err != nil {
        log.Fatalf("failed to listen: %s", err.Error())
    }

    log.Infof("Start grpc server at %s", s.address)

    go func() {
        if err := s.Serve(listen); err != nil {
            log.Fatalf("failed to start grpc server: %s", err.Error())
        }
    }()

    <-stopCh

    log.Infof("Grpc server on %s stopped", s.address)
    s.GracefulStop()
}
```

除了上面说的方法，iam-apiserver 还通过 github.com/marmotedu/iam/pkg/shutdown 包，实现了另外一种优雅关停方法，这个方法更加友好、更加灵活。实现代码见 [PrepareRun](https://github.com/marmotedu/iam/blob/v1.0.4/internal/apiserver/server.go#L81) 函数。github.com/marmotedu/iam/pkg/shutdown 包的使用方法如下：


```go
package main
import (
  "fmt"
  "time"
  "github.com/marmotedu/iam/pkg/shutdown"
  "github.com/marmotedu/iam/pkg/shutdown/shutdownmanagers/posixsignal"
)
func main() {
  // initialize shutdown
  gs := shutdown.New()
  // add posix shutdown manager
  gs.AddShutdownManager(posixsignal.NewPosixSignalManager())
  // add your tasks that implement ShutdownCallback
  gs.AddShutdownCallback(shutdown.ShutdownFunc(func(string) error {
    fmt.Println("Shutdown callback start")
    time.Sleep(time.Second)
    fmt.Println("Shutdown callback finished")
    return nil
  }))
  // start shutdown managers
  if err := gs.Start(); err != nil {
    fmt.Println("Start:", err)
    return
  }
  // do other stuff
  time.Sleep(time.Hour)
}
```
上面的代码中，通过 gs := shutdown.New() 创建 shutdown 实例；通过 AddShutdownManager 方法添加监听的信号；通过 AddShutdownCallback 方法设置监听到指定信号时，需要执行的回调函数。这些回调函数可以执行一些清理工作。最后，通过 Start 方法启动 shutdown 实例。


#### 健康检查
通常，我们会根据进程是否存在来判定 iam-apiserver 是否健康，例如执行 ps -ef|grep iam-apiserver。在实际开发中，我发现有时候服务进程仍然存在，但是 HTTP 服务却不能接收和处理请求，所以更加靠谱的检查方法是，直接请求 iam-apiserver 的健康检查接口。



我们可以在启动 iam-apiserver 进程后，手动调用 iam-apiserver 健康检查接口进行检查。但还有更方便的方法：启动服务后自动调用健康检查接口。这个方法的具体实现，你可以查看 GenericAPIServer 提供的 ping 方法。在 [ping](https://github.com/marmotedu/iam/blob/v1.0.4/internal/pkg/server/genericapiserver.go#L219) 方法中，你需要注意函数中的如下代码：


```go
url := fmt.Sprintf("http://%s/healthz", s.InsecureServingInfo.Address)
if strings.Contains(s.InsecureServingInfo.Address, "0.0.0.0") {
    url = fmt.Sprintf("http://127.0.0.1:%s/healthz", strings.Split(s.InsecureServingInfo.Address, ":")[1])
}
```
当 HTTP 服务监听在所有网卡时，请求 IP 为 127.0.0.1；当 HTTP 服务监听在指定网卡时，我们需要请求该网卡的 IP 地址。


#### 插件化加载中间件
iam-apiserver 支持插件化地加载 Gin 中间件，通过这种插件机制，我们可以根据需要选择中间件。


那么，为什么要将中间件做成一种插件化的机制呢？一方面，每个中间件都完成某种功能，这些功能不是所有情况下都需要的；另一方面，中间件是追加在 HTTP 请求链路上的一个处理函数，会影响 API 接口的性能。为了保证 API 接口的性能，我们也需要选择性地加载中间件。



例如，在测试环境中为了方便 Debug，可以选择加载 dump 中间件。dump 中间件可以打印请求包和返回包信息，这些信息可以协助我们 Debug。但是在现网环境中，我们不需要 dump 中间件来协助 Debug，而且如果加载了 dump 中间件，请求时会打印大量的请求信息，严重影响 API 接口的性能。这时候，我们就期望中间件能够按需加载。

iam-apiserver 通过 InstallMiddlewares 函数来安装 Gin 中间件，函数代码如下：


```go
func (s *GenericAPIServer) InstallMiddlewares() {
    // necessary middlewares
    s.Use(middleware.RequestID())
    s.Use(middleware.Context())

    // install custom middlewares
    for _, m := range s.middlewares {
        mw, ok := middleware.Middlewares[m]
        if !ok {
            log.Warnf("can not find middleware: %s", m)

            continue
        }

        log.Infof("install middleware: %s", m)
        s.Use(mw)
    }
}
```
可以看到，安装中间件时，我们不仅安装了一些必备的中间件，还安装了一些可配置的中间件。上述代码安装了两个默认的中间件： [RequestID](https://github.com/marmotedu/iam/blob/v1.0.4/internal/pkg/middleware/requestid.go#L22) 和 [Context](https://github.com/marmotedu/iam/blob/v1.0.4/internal/pkg/middleware/context.go#L17) 。


RequestID 中间件，主要用来在 HTTP 请求头和返回头中设置 X-Request-ID Header。如果 HTTP 请求头中没有 X-Request-ID HTTP 头，则创建 64 位的 UUID，如果有就复用。UUID 是调用 github.com/satori/go.uuid包提供的 NewV4().String()方法来生成的：

```go
rid = uuid.NewV4().String()
```
另外，这里有个 Go 常量的设计规范需要你注意：常量要跟该常量相关的功能包放在一起，不要将一个项目的常量都集中放在 const 这类包中。例如， [requestid.go](https://github.com/marmotedu/iam/blob/v1.0.4/internal/pkg/middleware/requestid.go) 文件中，我们定义了 XRequestIDKey = "X-Request-ID"常量，其他地方如果需要使用 XRequestIDKey，只需要引入 XRequestIDKey所在的包，并使用即可。


Context 中间件，用来在 gin.Context 中设置 requestID和 username键，在打印日志时，将 gin.Context 类型的变量传递给 log.L() 函数，log.L() 函数会在日志输出中输出 requestID和 username域：

```
2021-07-09 13:33:21.362 DEBUG   apiserver       v1/user.go:106  get 2 users from backend storage.       {"requestID": "f8477cf5-4592-4e47-bdcf-82f7bde2e2d0", "username": "admin"}
```
requestID和 username字段可以方便我们后期过滤并查看日志。除了默认的中间件，iam-apiserver 还支持一些可配置的中间件，我们可以通过配置 iam-apiserver 配置文件中的 server.middlewares 配置项，来配置这些这些中间件。


可配置以下中间件：
- recovery：捕获任何 panic，并恢复。
- secure：添加一些安全和资源访问相关的 HTTP 头。
- nocache：禁止客户端缓存 HTTP 请求的返回结果。
- cors：HTTP 请求跨域中间件。
- dump：打印出 HTTP 请求包和返回包的内容，方便 debug。注意，生产环境禁止加载该中间件。


当然，你还可以根据需要，添加更多的中间件。方法很简单，只需要编写中间件，并将中间件添加到一个 map[string]gin.HandlerFunc 类型的变量中即可：


```go
func defaultMiddlewares() map[string]gin.HandlerFunc {      
    return map[string]gin.HandlerFunc{                          
        "recovery":  gin.Recovery(),                            
        "secure":    Secure,                                
        "options":   Options,             
        "nocache":   NoCache,             
        "cors":      Cors(),                        
        "requestid": RequestID(),                       
        "logger":    Logger(),                                                           
        "dump":      gindump.Dump(),                                                               
    }                                                                                              
}  

```
上述代码位于 internal/pkg/middleware/middleware.go 文件中。


### 编程规范相关的特性
编程规范相关的特性有四个，分别是 API 版本、统一的资源元数据、统一的返回、并发处理模板。


#### API 版本
RESTful API 为了方便以后扩展，都需要支持 API 版本。在 12 讲 中，我们介绍了 API 版本号的 3 种标识方法，iam-apiserver 选择了将 API 版本号放在 URL 中，例如/v1/secrets。放在 URL 中的好处是很直观，看 API 路径就知道版本号。另外，API 的路径也可以很好地跟控制层、业务层、模型层的代码路径相映射。例如，密钥资源相关的代码存放位置如下：


```
internal/apiserver/controller/v1/secret/  # 控制几层代码存放位置
internal/apiserver/service/v1/secret.go # 业务层代码存放位置
github.com/marmotedu/api/apiserver/v1/secret.go # 模型层代码存放位置
```
关于代码存放路径，我还有一些地方想跟你分享。对于 Secret 资源，通常我们需要提供 CRUD 接口。

C：Create（创建 Secret）。R：Get（获取详情）、List（获取 Secret 资源列表）。U：Update（更新 Secret）。D：Delete（删除指定的 Secret）、DeleteCollection（批量删除 Secret）。


每个接口相互独立，为了减少更新 A 接口代码时因为误操作影响到 B 接口代码的情况，这里建议 CRUD 接口每个接口一个文件，从物理上将不同接口的代码隔离开。这种接口还可以方便我们查找 A 接口的代码所在位置。例如，Secret 控制层相关代码的存放方式如下：


```
$ ls internal/apiserver/controller/v1/secret/
create.go  delete_collection.go  delete.go  doc.go  get.go  list.go  secret.go  update.go
```
业务层和模型层的代码也可以这么组织。iam-apiserver 中，因为 Secret 的业务层和模型层代码比较少，所以我放在了 internal/apiserver/service/v1/secret.go和 github.com/marmotedu/api/apiserver/v1/secret.go文件中。如果后期 Secret 业务代码增多，我们也可以修改成下面这种方式：


```
 $ ls internal/apiserver/service/v1/secret/
 create.go  delete_collection.go  delete.go  doc.go  get.go  list.go  secret.go  update.go
```

这里再说个题外话：/v1/secret/和/secret/v1/这两种目录组织方式都可以，你选择一个自己喜欢的就行。当我们需要升级 API 版本时，相关代码可以直接放在 v2 目录下，例如：

```
internal/apiserver/controller/v2/secret/ # v2 版本控制几层代码存放位置
internal/apiserver/service/v2/secret.go # v2 版本业务层代码存放位置
github.com/marmotedu/api/apiserver/v2/secret.go # v2 版本模型层代码存放位置
```
这样既能够跟 v1 版本的代码物理隔离开，互不影响，又方便查找 v2 版本的代码。


#### 统一的资源元数据
iam-apiserver 设计的一大亮点是，像 Kubernetes REST 资源一样，支持统一的资源元数据。

iam-apiserver 中所有的资源都是 REST 资源，iam-apiserver 将 REST 资源的属性也进一步规范化了，这里的规范化是指所有的 REST 资源均支持两种属性：公共属性。资源自有的属性。



例如，Secret 资源的定义方式如下：


```go
type Secret struct {
    // May add TypeMeta in the future.
    // metav1.TypeMeta `json:",inline"`

    // Standard object's metadata.
    metav1.ObjectMeta `       json:"metadata,omitempty"`
    Username          string `json:"username"           gorm:"column:username"  validate:"omitempty"`
    SecretID          string `json:"secretID"           gorm:"column:secretID"  validate:"omitempty"`
    SecretKey         string `json:"secretKey"          gorm:"column:secretKey" validate:"omitempty"`

    // Required: true
    Expires     int64  `json:"expires"     gorm:"column:expires"     validate:"omitempty"`
    Description string `json:"description" gorm:"column:description" validate:"description"`
}
```
资源自有的属性，会因资源不同而不同。这里，我们来重点看一下公共属性 ObjectMeta ，它的定义如下：

```
type ObjectMeta struct {
  ID uint64 `json:"id,omitempty" gorm:"primary_key;AUTO_INCREMENT;column:id"`
  InstanceID string `json:"instanceID,omitempty" gorm:"unique;column:instanceID;type:varchar(32);not null"`
  Name string `json:"name,omitempty" gorm:"column:name;type:varchar(64);not null" validate:"name"`
  Extend Extend `json:"extend,omitempty" gorm:"-" validate:"omitempty"`
  ExtendShadow string `json:"-" gorm:"column:extendShadow" validate:"omitempty"`
  CreatedAt time.Time `json:"createdAt,omitempty" gorm:"column:createdAt"`
  UpdatedAt time.Time `json:"updatedAt,omitempty" gorm:"column:updatedAt"`
}
```
接下来，我来详细介绍公共属性中每个字段的含义及作用。


ID
这里的 ID，映射为 MariaDB 数据库中的 id 字段。id 字段在一些应用中，会作为资源的唯一标识。但 iam-apiserver 中没有使用 ID 作为资源的唯一标识，而是使用了 InstanceID。iam-apiserver 中 ID 唯一的作用是跟数据库 id 字段进行映射，代码中并没有使用到 ID。

InstanceID
InstanceID 是资源的唯一标识，格式为-xxxxxx。其中，是资源的英文标识符号，xxxxxx是随机字符串。字符集合为 abcdefghijklmnopqrstuvwxyz1234567890，长度 >=6，例如 secret-yj8m30、user-j4lz3g、policy-3v18jq。


**腾讯云、阿里云、华为云也都是采用这种格式的字符串作为资源唯一标识的。**InstanceID 的生成和更新都是自动化的，通过 gorm 提供的 AfterCreate Hooks 在记录插入数据库之后，生成并更新到数据库的 instanceID字段：

```go
func (s *Secret) AfterCreate(tx *gorm.DB) (err error) {
    s.InstanceID = idutil.GetInstanceID(s.ID, "secret-")

    return tx.Save(s).Error
}
```
上面的代码，在 Secret 记录插入到 iam 数据库的 secret 表之后，调用 idutil.GetInstanceID生成 InstanceID，并通过 tx.Save(s)更新到数据库 secret 表的 instanceID字段。


因为通常情况下，应用中的 REST 资源只会保存到数据库中的一张表里，这样就能保证应用中每个资源的数据库 ID 是唯一的。所以 GetInstanceID(uid uint64, prefix string) string函数使用 github.com/speps/go-hashids包提供的方法，对这个数据库 ID 进行哈希，最终得到一个数据库级别的唯一的字符串（例如：3v18jq），并根据传入的 prefix，得到资源的 InstanceID。


使用这种方式生成资源的唯一标识，有下面这两个优点：
- 数据库级别唯一。
- InstanceID 是长度可控的字符串，长度最小是 6 个字符，但会根据表中的记录个数动态变长。根据我的测试，2176782336 条记录以内生成的 InstanceID 长度都在 6 个字符以内。长度可控的另外一个好处是方便记忆和传播。


这里需要你注意：如果同一个资源分别存放在不同的表中，那在使用这种方式时，生成的 InstanceID 可能相同，不过概率很小，几乎为零。这时候，我们就需要使用分布式 ID 生成技术。这又是另外一个话题了，这里不再扩展讲解。

在实际的开发中，不少开发者会使用数据库数字段 ID（例如 121）和 36/64 位的 UUID（例如 20cd59d4-08c6-4e86-a9d4-a0e51c420a04 ）来作为资源的唯一标识。相较于这两种资源标识方式，使用-xxxxxx这种标识方式具有以下优点：
- 看标识名就知道是什么类型的资源，例如：secret-yj8m30说明该资源是 secret 类型的资源。在实际的排障过程中，能够有效减少误操作。
- 长度可控，占用数据库空间小。iam-apiserver 的资源标识长度基本可以认为是 12 个字符（secret/policy 是 6 个字符，再加 6 位随机字符）。
- 如果使用 121 这类数值作为资源唯一标识，相当于间接向友商透漏系统的规模，是一定要禁止的。

另外，还有一些系统如 Kubernetes 中，使用资源名作为资源唯一标识。这种方式有个弊端，就是当系统中同类资源太多时，创建资源很容易重名，你自己想要的名字往往填不了，所以 iam-apiserver 不采用这种设计方式。

我们使用 instanceID 来作为资源的唯一标识，在代码中，就经常需要根据 instanceID 来查询资源。所以，在数据库中要设置该字段为唯一索引，一方面可以防止 instanceID 不唯一，另一方面也能加快查询速度。



Name
Name 即资源的名字，我们可以通过名字很容易地辨别一个资源。


Extend、ExtendShadow
**Extend 和 ExtendShadow 是 iam-apiserver 设计的又一大亮点。**

在实际开发中，我们经常会遇到这个问题：随着业务发展，某个资源需要增加一些属性，这时，我们可能会选择在数据库中新增一个数据库字段。但是，随着业务系统的演进，数据库中的字段越来越多，我们的 Code 也要做适配，最后就会越来越难维护。


我们还可能遇到这种情况：我们将上面说的字段保存在数据库中叫 meta的字段中，数据库中 meta字段的数据格式是{"disable":true,"tag":"colin"}。但是，我们如果想在代码中使用这些字段，需要 Unmarshal 到一个结构体中，例如：


```go
metaData := `{"disable":true,"tag":"colin"}`
meta := make(map[string]interface{})
if err := json.Unmarshal([]byte(metaData), &meta); err != nil {
    return err
}
```
再存入数据中时，又要 Marshal 成 JSON 格式的字符串，例如：


```go
meta := map[string]interface{}{"disable": true, "tag": "colin"}
data, err := json.Marshal(meta)
if err != nil {
    return err
}
```

你可以看到，这种 Unmarshal 和 Marshal 操作有点繁琐。


因为每个资源都可能需要用到扩展字段，那么有没有一种通用的解决方案呢？iam-apiserver 就通过 Extend 和 ExtendShadow 解决了这个问题。


Extend 是 Extend 类型的字段，Extend 类型其实是 map[string]interface{}的类型别名。在程序中，我们可以很方便地引用 Extend 包含的属性，也就是 map 的 key。Extend 字段在保存到数据库中时，会自动 Marshal 成字符串，保存在 ExtendShadow 字段中。


ExtendShadow 是 Extend 在数据库中的影子。同样，当从数据库查询数据时，ExtendShadow 的值会自动 Unmarshal 到 Extend 类型的变量中，供程序使用。具体实现方式如下：

- 借助 gorm 提供的 BeforeCreate、BeforeUpdate Hooks，在插入记录、更新记录时，将 Extend 的值转换成字符串，保存在 ExtendShadow 字段中，并最终保存在数据库的 ExtendShadow 字段中。
- 借助 gorm 提供的 AfterFind Hooks，在查询数据后，将 ExtendShadow 的值 Unmarshal 到 Extend 字段中，之后程序就可以通过 Extend 字段来使用其中的属性。


CreatedAt
资源的创建时间。每个资源在创建时，我们都应该记录资源的创建时间，可以帮助后期进行排障、分析等。


UpdatedAt
资源的更新时间。每个资源在更新时，我们都应该记录资源的更新时间。资源更新时，该字段由 gorm 自动更新。可以看到，ObjectMeta 结构体包含了很多字段，每个字段都完成了很酷的功能。那么，如果把 ObjectMeta 作为所有资源的公共属性，这些资源就会自带这些能力。


当然，有些开发者可能会说，User 资源其实是不需要 user-xxxxxx这种资源标识的，所以 InstanceID 这个字段其实是无用的字段。但是在我看来，和功能冗余相比，功能规范化、不重复造轮子，以及 ObjectMeta 的其他功能更加重要。所以，也建议所有的 REST 资源都使用统一的资源元数据。


#### 统一的返回
在18 讲 中，我们介绍过 API 的接口返回格式应该是统一的。要想返回一个固定格式的消息，最好的方式就是使用同一个返回函数。因为 API 接口都是通过同一个函数来返回的，其返回格式自然是统一的。


IAM 项目通过 github.com/marmotedu/component-base/pkg/core 包提供的 WriteResponse 函数来返回结果。WriteResponse 函数定义如下：

```go
func WriteResponse(c *gin.Context, err error, data interface{}) {
    if err != nil {
        log.Errorf("%#+v", err)
        coder := errors.ParseCoder(err)
        c.JSON(coder.HTTPStatus(), ErrResponse{
            Code:      coder.Code(),
            Message:   coder.String(),
            Reference: coder.Reference(),
        })

        return
    }

    c.JSON(http.StatusOK, data)
}
```
可以看到，WriteResponse 函数会判断 err 是否为 nil。如果不为 nil，则将 err 解析为 github.com/marmotedu/errors包中定义的 Coder 类型的错误，并调用 Coder 接口提供的 Code() 、String() 、Reference() 方法，获取该错误的业务码、对外展示的错误信息和排障文档。如果 err 为 nil，则调用 c.JSON返回 JSON 格式的数据。

#### 并发处理模板
在 Go 项目开发中，经常会遇到这样一种场景：查询列表接口时，查询出了多条记录，但是需要针对每一条记录做一些其他逻辑处理。因为是多条记录，比如 100 条，处理每条记录延时如果为 X 毫秒，串行处理完 100 条记录，整体延时就是 100 * X 毫秒。如果 X 比较大，那整体处理完的延时是非常高的，会严重影响 API 接口的性能。


这时候，我们自然就会想到利用 CPU 的多核能力，并发来处理这 100 条记录。这种场景我们在实际开发中经常遇到，有必要抽象成一个并发处理模板，这样以后在查询时，就可以使用这个模板了。例如，iam-apiserver 中，查询用户列表接口 List ，还需要返回每个用户所拥有的策略个数。这就用到了并发处理。这里，我试着将其抽象成一个模板，模板如下：


```go
func (u *userService) List(ctx context.Context, opts metav1.ListOptions) (*v1.UserList, error) {
  users, err := u.store.Users().List(ctx, opts)
  if err != nil {
    log.L(ctx).Errorf("list users from storage failed: %s", err.Error())

    return nil, errors.WithCode(code.ErrDatabase, err.Error())
  }

  wg := sync.WaitGroup{}
  errChan := make(chan error, 1)
  finished := make(chan bool, 1)

  var m sync.Map

  // Improve query efficiency in parallel
  for _, user := range users.Items {
    wg.Add(1)

    go func(user *v1.User) {
      defer wg.Done()

            // some cost time process
      policies, err := u.store.Policies().List(ctx, user.Name, metav1.ListOptions{})
      if err != nil {
        errChan <- errors.WithCode(code.ErrDatabase, err.Error())

        return
      }

      m.Store(user.ID, &v1.User{
                ...
        Phone:       user.Phone,
        TotalPolicy: policies.TotalCount,
      })
    }(user)
  }

  go func() {
    wg.Wait()
    close(finished)
  }()

  select {
  case <-finished:
  case err := <-errChan:
    return nil, err
  }

  // infos := make([]*v1.User, 0)
  infos := make([]*v1.User, 0, len(users.Items))
  for _, user := range users.Items {
    info, _ := m.Load(user.ID)
    infos = append(infos, info.(*v1.User))
  }

  log.L(ctx).Debugf("get %d users from backend storage.", len(infos))

  return &v1.UserList{ListMeta: users.ListMeta, Items: infos}, nil
}
```
在上面的并发模板中，我实现了并发处理查询结果中的三个功能：


第一个功能，**goroutine 报错即返回**。goroutine 中代码段报错时，会将错误信息写入 errChan中。我们通过 List 函数中的 select 语句，实现只要有一个 goroutine 发生错误，即返回：


```go
select {
case <-finished:
case err := <-errChan:
    return nil, err
}
```

第二个功能，**保持查询顺序**。我们从数据库查询出的列表是有顺序的，比如默认按数据库 ID 字段升序排列，或者我们指定的其他排序方法。在并发处理中，这些顺序会被打断。但为了确保最终返回的结果跟我们预期的排序效果一样，在并发模板中，我们还需要保证最终返回结果跟查询结果保持一致的排序。上面的模板中，我们将处理后的记录保存在 map 中，map 的 key 为数据库 ID。并且，在最后按照查询的 ID 顺序，依次从 map 中取出 ID 的记录，例如：



```go
    var m sync.Map
  for _, user := range users.Items {
        ...
    go func(user *v1.User) {
            ...
      m.Store(user.ID, &v1.User{})
    }(user)
  }
    ...
  infos := make([]*v1.User, 0, len(users.Items))
  for _, user := range users.Items {
    info, _ := m.Load(user.ID)
    infos = append(infos, info.(*v1.User))
  }
```

通过上面这种方式，可以确保最终返回的结果跟从数据库中查询的结果保持一致的排序。



第三个功能，**并发安全**。Go 语言中的 map 不是并发安全的，要想实现并发安全，需要自己实现（如加锁），或者使用 sync.Map。上面的模板使用了 sync.Map。


当然了，如果期望 List 接口能在期望时间内返回，还可以添加超时机制，例如：


```go
    select {
    case <-finished:
    case err := <-errChan:
        return nil, err
    case <-time.After(time.Duration(30 * time.Second)):
        return nil, fmt.Errorf("list users timeout after 30 seconds")

    }
```
goroutine 虽然很轻量，但还是会消耗资源，如果我们需要处理几百上千的并发，就需要用协程池来复用协程，达到节省资源的目的。有很多优秀的协程包可供我们直接使用，比如 ants 、 tunny 等。


### 其他特性
除了上面那两大类，这里我还想给你介绍下关键代码设计中的其他特性，包括插件化选择 JSON 库、调用链实现、数据一致性。


#### 插件化选择 JSON 库
Golang 提供的标准 JSON 解析库 encoding/json，在开发高性能、高并发的网络服务时会产生性能问题。所以很多开发者在实际的开发中，往往会选用第三方的高性能 JSON 解析库，例如 jsoniter 、 easyjson 、 jsonparser 等。


我见过的很多开发者选择了 jsoniter，也有一些开发者使用了 easyjson。jsoniter 的性能略高于 encoding/json。但随着 go 版本的迭代，encoding/json 库的性能也越来越高，jsoniter 的性能优势也越来越有限。所以，IAM 项目使用了 jsoniter 库，并准备随时切回 encoding/json 库。

为了方便切换不同的 JSON 包，iam-apiserver 采用了一种插件化的机制来使用不同的 JSON 包。具体是通过使用 go 的标签编译选择运行的解析库来实现的。


标签编译就是在源代码里添加标注，通常称之为编译标签（build tag）。编译标签通过注释的方式在靠近源代码文件顶部的地方添加。go build 在构建一个包的时候，会读取这个包里的每个源文件并且分析编译便签，这些标签决定了这个源文件是否参与本次编译。例如：



```go
// +build jsoniter

package json

import jsoniter "github.com/json-iterator/go"
```
+build jsoniter就是编译标签。这里要注意，一个源文件可以有多个编译标签，多个编译标签之间是逻辑“与”的关系；一个编译标签可以包括由空格分割的多个标签，这些标签是逻辑“或”的关系。例如：



```
// +build linux darwin
// +build 386
```
这里要注意，编译标签和包的声明之间应该使用空行隔开，否则编译标签会被当作包声明的注释，而不是编译标签。那具体来说，**我们是如何实现插件化选择 JSON 库的呢？**

首先，我自定义了一个 github.com/marmotedu/component-base/pkg/json json 包，来适配 encoding/json 和 json-iterator。github.com/marmotedu/component-base/pkg/json 包中有两个文件：


- json.go：映射了 encoding/json 包的 Marshal、Unmarshal、MarshalIndent、NewDecoder、NewEncoder 方法。
- jsoniter.go：映射了 github.com/json-iterator/go 包的 Marshal、Unmarshal、MarshalIndent、NewDecoder、NewEncoder。


json.go 和 jsoniter.go 通过编译标签，让 Go 编译器在构建代码时选择使用哪一个 json 文件。接着，通过在执行 go build时指定 [-tags](https://github.com/marmotedu/iam/blob/master/scripts/make-rules/golang.mk#L19) 参数，来选择编译哪个 json 文件。


json/json.go、json/jsoniter.go 这两个 Go 文件的顶部，都有一行注释：


```
// +build !jsoniter

// +build jsoniter
```
// +build !jsoniter表示，tags 不是 jsoniter 的时候编译这个 Go 文件。// +build jsoniter表示，tags 是 jsoniter 的时候编译这个 Go 文件。也就是说，这两种条件是互斥的，只有当 tags=jsoniter 的时候，才会使用 json-iterator，其他情况使用 encoding/json。

例如，如果我们想使用包，可以这么编译项目：


```
$ go build -tags=jsoniter
```
在实际开发中，**我们需要根据场景来选择合适的 JSON 库**。这里我给你一些建议。


场景一：结构体序列化和反序列化场景

在这个场景中，我个人首推的是官方的 JSON 库。可能你会比较意外，那我就来说说我的理由：

首先，虽然 easyjson 的性能压倒了其他所有开源项目，但它有一个最大的缺陷，那就是需要额外使用工具来生成这段代码，而对额外工具的版本控制就增加了运维成本。当然，如果你的团队已经能够很好地处理 protobuf 了，也是可以用同样的思路来管理 easyjson 的。其次，虽然 Go 1.8 之前，官方 JSON 库的性能总是被大家吐槽，但现在（1.16.3）官方 JSON 库的性能已不可同日而语。此外，作为使用最为广泛，而且没有之一的 JSON 库，官方库的 bug 是最少的，兼容性也是最好的最后，jsoniter 的性能虽然依然优于官方，但没有达到逆天的程度。如果你追求的是极致的性能，那么你应该选择 easyjson 而不是 jsoniter。jsoniter 近年已经不活跃了，比如说，我前段时间提了一个 issue 没人回复，于是就上去看了下 issue 列表，发现居然还遗留着一些 2018 年的 issue。


场景二：非结构化数据的序列化和反序列化场景


这个场景下，我们要分高数据利用率和低数据利用率两种情况来看。你可能对数据利用率的高低没啥概念，那我举个例子：JSON 数据的正文中，如果说超过四分之一的数据都是业务需要关注和处理的，那就算是高数据利用率。

在高数据利用率的情况下，我推荐使用 jsonvalue。至于低数据利用率的情况，还可以根据 JSON 数据是否需要重新序列化，分成两种情况。


如果无需重新序列化，这个时候选择 jsonparser 就行了，因为它的性能实在是耀眼。如果需要重新序列化，这种情况下你有两种选择：如果对性能要求相对较低，可以使用 jsonvalue；如果对性能的要求高，并且只需要往二进制序列中插入一条数据，那么可以采用 jsoniter 的 Set 方法。



实际操作中，超大 JSON 数据量，并且同时需要重新序列化的情况非常少，往往是在代理服务器、网关、overlay 中继服务等，同时又需要往原数据中注入额外信息的时候。换句话说，jsoniter 的适用场景比较有限。下面是从 10% 到 60% 数据覆盖率下，不同库的操作效率对比（纵坐标单位：μs/op）：


![img](https://static001.geekbang.org/resource/image/fc/a6/fcc79727a26a37345af705df1179c1a6.png?wh=1920x1266)

可以看到，当 jsoniter 的数据利用率达到 25% 时，和 jsonvalue、jsonparser 相比就已经没有任何优势；至于 jsonvalue，由于对数据做了一次性的全解析，因此解析后的数据存取耗时极少，因此在不同数据覆盖率下的耗时都很稳定。


#### 调用链实现
调用链对查日志、排障帮助非常大。所以，在 iam-apiserver 中也实现了调用链，通过 requestID来串联整个调用链。


具体是通过以下两步来实现的：第一步，将 ctx context.Context 类型的变量作为函数的第一个参数，在函数调用时传递。第二步，不同函数中，通过 log.L(ctx context.Context)来记录日志。


在请求到来时，请求会通过 [Context](https://github.com/marmotedu/iam/blob/v1.0.4/internal/pkg/middleware/context.go#L17) 中间件处理：


```go
func Context() gin.HandlerFunc {
  return func(c *gin.Context) {
    c.Set(log.KeyRequestID, c.GetString(XRequestIDKey))
    c.Set(log.KeyUsername, c.GetString(UsernameKey))
    c.Next()
  }
}
```
在 Context 中间件中，会在 gin.Context 类型的变量中设置 log.KeyRequestID键，其值为 36 位的 UUID。UUID 通过 [RequestID](https://github.com/marmotedu/iam/blob/v1.0.4/internal/pkg/middleware/requestid.go#L22) 中间件来生成，并设置在 gin 请求的 Context 中。


RequestID 中间件在 Context 中间件之前被加载，所以在 Context 中间件被执行时，能够获取到 RequestID 生成的 UUID。log.L(ctx context.Context)函数在记录日志时，会从头 ctx 中获取到 log.KeyRequestID，并作为一个附加字段随日志打印。

通过以上方式，我们最终可以形成 iam-apiserver 的请求调用链，日志示例如下：


```
2021-07-19 19:41:33.472 INFO    apiserver       apiserver/auth.go:205   user `admin` is authenticated.  {"requestID": "b6c56cd3-d095-4fd5-a928-291a2e33077f", "username": "admin"}
2021-07-19 19:41:33.472 INFO    apiserver       policy/create.go:22     create policy function called.  {"requestID": "b6c56cd3-d095-4fd5-a928-291a2e33077f", "username": "admin"}
...
```
另外，ctx context.Context作为函数 / 方法的第一个参数，还有一个好处是方便后期扩展。例如，如果我们有以下调用关系：


```go
package main

import "fmt"

func B(name, address string) string {
    return fmt.Sprintf("name: %s, address: %s", name, address)
}

func A() string {
    return B("colin", "sz")
}

func main() {
    fmt.Println(A())
}
```
上面的代码最终调用 B函数打印出用户名及其地址。如果随着业务的发展，希望 A 调用 B 时，传入用户的电话，B 中打印出用户的电话号码。这时候，我们可能会考虑给 B 函数增加一个电话号参数，例如：


```go
func B(name, address, phone string) string {
    return fmt.Sprintf("name: %s, address: %s, phone: %s", name, address)
}
```
如果我们后面还要增加年龄、性别等属性呢？按这种方式不断增加 B 函数的参数，不仅麻烦，而且还要改动所有调用 B 的函数，工作量也很大。这时候，可以考虑通过 ctx context.Context 来传递这些扩展参数，实现如下：


```go
package main

import (
    "context"
    "fmt"
)

func B(ctx context.Context, name, address string) string {
    return fmt.Sprintf("name: %s, address: %s, phone: %v", name, address, ctx.Value("phone"))
}

func A() string {
    ctx := context.WithValue(context.TODO(), "phone", "1812884xxxx")
    return B(ctx, "colin", "sz")
}

func main() {
    fmt.Println(A())
}
```
这样，我们下次需要新增参数的话，只需要调用 context 的 WithValue 方法：


```go
ctx = context.WithValue(ctx, "sex", "male")
```

在 B 函数中，通过 context.Context 类型的变量提供的 Value 方法，从 context 中获取 sex key 即可：


```go
return fmt.Sprintf("name: %s, address: %s, phone: %v, sex: %v", name, address, ctx.Value("phone"), ctx.Value("sex"))
```
#### 数据一致性
为了提高 iam-authz-server 的响应性能，我将密钥和授权策略信息缓存在 iam-authz-server 部署机器的内存中。同时，为了实现高可用，我们需要保证 iam-authz-server 启动的实例个数至少为两个。这时候，我们会面临数据一致性的问题：所有 iam-authz-server 缓存的数据要一致，并且跟 iam-apiserver 数据库中保存的一致。iam-apiserver 通过如下方式来实现数据一致性：


![img](https://static001.geekbang.org/resource/image/4c/b7/4ce0ff51e4cecb12f7234241137c20b7.jpg?wh=2248x798)

具体流程如下：



第一步，iam-authz-server 启动时，会通过 grpc 调用 iam-apiserver 的 GetSecrets 和 GetPolicies 接口，获取所有的密钥和授权策略信息。第二步，当我们通过控制台调用 iam-apiserver 密钥 / 授权策略的写接口（POST、PUT、DELETE）时，会向 Redis 的 iam.cluster.notifications通道发送 SecretChanged/PolicyChanged 消息。第三步，iam-authz-server 会订阅 iam.cluster.notifications通道，当监听到有 SecretChanged/PolicyChanged 消息时，会请求 iam-apiserver 拉取所有的密钥 / 授权策略。



通过 Redis 的 Sub/Pub 机制，保证每个 iam-authz-server 节点的缓存数据跟 iam-apiserver 数据库中保存的数据一致。所有节点都调用 iam-apiserver 的同一个接口来拉取数据，通过这种方式保证所有 iam-authz-server 节点的数据是一致的。


### 总结
今天，我和你分享了 iam-apiserver 的一些关键功能实现，并介绍了我的设计思路。这里我再简要梳理下。


- 为了保证进程关停时，HTTP 请求执行完后再断开连接，进程中的任务正常完成，iam-apiserver 实现了优雅关停功能。
- 为了避免进程存在，但服务没成功启动的异常场景，iam-apiserver 实现了健康检查机制。
- Gin 中间件可通过配置文件配置，从而实现按需加载的特性。
- 为了能够直接辨别出 API 的版本，iam-apiserver 将 API 的版本标识放在 URL 路径中，例如 /v1/secrets。
- 为了能够最大化地共享功能代码，iam-apiserver 抽象出了统一的元数据，每个 REST 资源都具有这些元数据。
- 因为 API 接口都是通过同一个函数来返回的，其返回格式自然是统一的。
- 因为程序中经常需要处理并发逻辑，iam-apiserver 抽象出了一个通用的并发模板。
- 为了方便根据需要切换 JSON 库，我们实现了插件化选择 JSON 库的功能。
- 为了实现调用链功能，iam-apiserver 不同函数之间通过 ctx context.Context 来传递 RequestID。
- iam-apiserver 通过 Redis 的 Sub/Pub 机制来保证数据一致性。

## 30 | ORM：CURD 神器 GORM 包介绍及实战

在用 Go 开发项目时，我们免不了要和数据库打交道。每种语言都有优秀的 ORM 可供选择，在 Go 中也不例外，比如gorm、xorm、gorose等。目前，GitHub 上 star 数最多的是 GORM，它也是当前 Go 项目中使用最多的 ORM。


IAM 项目也使用了 GORM。这一讲，我就来详细讲解下 GORM 的基础知识，并介绍 iam-apiserver 是如何使用 GORM，对数据进行 CURD 操作的。


### GORM 基础知识介绍
GORM 是 Go 语言的 ORM 包，功能强大，调用方便。像腾讯、华为、阿里这样的大厂，都在使用 GORM 来构建企业级的应用。GORM 有很多特性，开发中常用的核心特性如下：

功能全。使用 ORM 操作数据库的接口，GORM 都有，可以满足我们开发中对数据库调用的各类需求。支持钩子方法。这些钩子方法可以应用在 Create、Save、Update、Delete、Find 方法中。开发者友好，调用方便。支持 Auto Migration。支持关联查询。支持多种关系数据库，例如 MySQL、Postgres、SQLite、SQLServer 等。


GORM 有两个版本，V1和V2。遵循用新不用旧的原则，IAM 项目使用了最新的 V2 版本。



### 通过示例学习 GORM
接下来，我们先快速看一个使用 GORM 的示例，通过该示例来学习 GORM。示例代码存放在marmotedu/gopractise-demo/gorm/main.go文件中。因为代码比较长，你可以使用以下命令克隆到本地查看：


```
$ mkdir -p $GOPATH/src/github.com/marmotedu
$ cd $GOPATH/src/github.com/marmotedu
$ git clone https://github.com/marmotedu/gopractise-demo
$ cd gopractise-demo/gorm/
```

假设我们有一个 MySQL 数据库，连接地址和端口为 127.0.0.1:3306 ，用户名为 iam ，密码为 iam1234 。创建完 main.go 文件后，执行以下命令来运行：


```go
$ go run main.go -H 127.0.0.1:3306 -u iam -p iam1234 -d test
2020/10/17 15:15:50 totalcount: 1
2020/10/17 15:15:50   code: D42, price: 100
2020/10/17 15:15:51 totalcount: 1
2020/10/17 15:15:51   code: D42, price: 200
2020/10/17 15:15:51 totalcount: 0
```
在企业级 Go 项目开发中，使用 GORM 库主要用来完成以下数据库操作：
- 连接和关闭数据库。连接数据库时，可能需要设置一些参数，比如最大连接数、最大空闲连接数、最大连接时长等。
- 插入表记录。可以插入一条记录，也可以批量插入记录。
- 更新表记录。可以更新某一个字段，也可以更新多个字段。
- 查看表记录。可以查看某一条记录，也可以查看符合条件的记录列表。
- 删除表记录。可以删除某一个记录，也可以批量删除。删除还支持永久删除和软删除。
- **在一些小型项目中，还会用到 GORM 的表结构自动迁移功能。**


GORM 功能强大，上面的示例代码展示的是比较通用的一种操作方式。


上述代码中，首先定义了一个 GORM 模型（Models），Models 是标准的 Go struct，用来代表数据库中的一个表结构。我们可以给 Models 添加 TableName 方法，来告诉 GORM 该 Models 映射到数据库中的哪张表。Models 定义如下：


```go
type Product struct {
    gorm.Model
    Code  string `gorm:"column:code"`
    Price uint   `gorm:"column:price"`
}

// TableName maps to mysql table name.
func (p *Product) TableName() string {
    return "product"
}
```
如果没有指定表名，则 GORM 使用结构体名的蛇形复数作为表名。例如：结构体名为 DockerInstance ，则表名为 dockerInstances 。


在之后的代码中，使用 Pflag 来解析命令行的参数，通过命令行参数指定数据库的地址、用户名、密码和数据库名。之后，使用这些参数生成建立 MySQL 连接需要的配置文件，并调用 gorm.Open 建立数据库连接：


```go
var (
    host     = pflag.StringP("host", "H", "127.0.0.1:3306", "MySQL service host address")
    username = pflag.StringP("username", "u", "root", "Username for access to mysql service")
    password = pflag.StringP("password", "p", "root", "Password for access to mysql, should be used pair with password")
    database = pflag.StringP("database", "d", "test", "Database name to use")
    help     = pflag.BoolP("help", "h", false, "Print this help message")
)

func main() {
    // Parse command line flags
    pflag.CommandLine.SortFlags = false
    pflag.Usage = func() {
        pflag.PrintDefaults()
    }
    pflag.Parse()
    if *help {
        pflag.Usage()
        return
    }

    dsn := fmt.Sprintf(`%s:%s@tcp(%s)/%s?charset=utf8&parseTime=%t&loc=%s`,
        *username,
        *password,
        *host,
        *database,
        true,
        "Local")
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
    if err != nil {
        panic("failed to connect database")
    }
}

```
创建完数据库连接之后，会返回数据库实例 db ，之后就可以调用 db 实例中的方法，完成数据库的 CURD 操作。具体操作如下，一共可以分为六个操作：


第一个操作，自动迁移表结构。


```go
// 1. Auto migration for given models
db.AutoMigrate(&Product{})
```
**我不建议你在正式的代码中自动迁移表结构。**因为变更现网数据库是一个高危操作，现网数据库字段的添加、类型变更等，都需要经过严格的评估才能实施。这里将变更隐藏在代码中，在组件发布时很难被研发人员感知到，如果组件启动，就可能会自动修改现网表结构，也可能会因此引起重大的现网事故。

GORM 的 AutoMigrate 方法，只对新增的字段或索引进行变更，理论上是没有风险的。在实际的 Go 项目开发中，也有很多人使用 AutoMigrate 方法自动同步表结构。但我更倾向于规范化、可感知的操作方式，所以我在实际开发中，都是手动变更表结构的。当然，具体使用哪种方法，你可以根据需要自行选择。


第二个操作，插入表记录。


```go
// 2. Insert the value into database
if err := db.Create(&Product{Code: "D42", Price: 100}).Error; err != nil {
    log.Fatalf("Create error: %v", err)
}
PrintProducts(db)
```
通过 db.Create 方法创建了一条记录。插入记录后，通过调用 PrintProducts 方法打印当前表中的所有数据记录，来测试是否成功插入。第三个操作，获取符合条件的记录。


```go
// 3. Find first record that match given conditions
product := &Product{}
if err := db.Where("code= ?", "D42").First(&product).Error; err != nil {
    log.Fatalf("Get product error: %v", err)
}
```
First 方法只会返回符合条件的记录列表中的第一条，你可以使用 First 方法来获取某个资源的详细信息。第四个操作，更新表记录。


```go
// 4. Update value in database, if the value doesn't have primary key, will insert it
product.Price = 200
if err := db.Save(product).Error; err != nil {
    log.Fatalf("Update product error: %v", err)
}
PrintProducts(db)
```
通过 Save 方法，可以把 product 变量中所有跟数据库不一致的字段更新到数据库中。具体操作是：先获取某个资源的详细信息，再通过 product.Price = 200 这类赋值语句，对其中的一些字段重新赋值。最后，调用 Save 方法更新这些字段。你可以将这些操作看作一种更新数据库的更新模式。

第五个操作，删除表记录。通过 Delete 方法删除表记录，代码如下：

```go
// 5. Delete value match given conditions
if err := db.Where("code = ?", "D42").Delete(&Product{}).Error; err != nil {
    log.Fatalf("Delete product error: %v", err)
}
PrintProducts(db)
```
这里需要注意，因为 Product 中有 gorm.DeletedAt 字段，所以，上述删除操作不会真正把记录从数据库表中删除掉，而是通过设置数据库 product 表 deletedAt 字段为当前时间的方法来删除。第六个操作，获取表记录列表。


```go
products := make([]*Product, 0)
var count int64
d := db.Where("code like ?", "%D%").Offset(0).Limit(2).Order("id desc").Find(&products).Offset(-1).Limit(-1).Count(&count)
if d.Error != nil {
    log.Fatalf("List products error: %v", d.Error)
}
```
在 PrintProducts 函数中，会打印当前的所有记录，你可以根据输出，判断数据库操作是否成功。


### GORM 常用操作讲解
看完上面的示例，我想你已经初步掌握了 GORM 的使用方法。接下来，我再来给你详细介绍下 GORM 所支持的数据库操作。



#### 模型定义
GORM 使用模型（Models）来映射一个数据库表。默认情况下，使用 ID 作为主键，使用结构体名的 snake_cases 作为表名，使用字段名的 snake_case 作为列名，并使用 CreatedAt、UpdatedAt、DeletedAt 字段追踪创建、更新和删除时间。


使用 GORM 的默认规则，可以减少代码量，但**我更喜欢的方式是直接指明字段名和表名。**例如，有以下模型：


```go
type Animal struct {
  AnimalID int64        // 列名 `animal_id`
  Birthday time.Time    // 列名 `birthday`
  Age      int64        // 列名 `age`
}
```
上述模型对应的表名为 animals ，列名分别为 animal_id 、 birthday 和 age 。我们可以通过以下方式来重命名表名和列名，并将 AnimalID 设置为表的主键：


```go
type Animal struct {
    AnimalID int64     `gorm:"column:animalID;primarykey"` // 将列名设为 `animalID`
    Birthday time.Time `gorm:"column:birthday"`            // 将列名设为 `birthday`
    Age      int64     `gorm:"column:age"`                 // 将列名设为 `age`
}

func (a *Animal) TableName() string {
    return "animal"
}
```
上面的代码中，通过 primaryKey 标签指定主键，使用 column 标签指定列名，通过给 Models 添加 TableName 方法指定表名。



数据库表通常会包含 4 个字段。ID：自增字段，也作为主键。CreatedAt：记录创建时间。UpdatedAt：记录更新时间。DeletedAt：记录删除时间（软删除时有用）。


GORM 也预定义了包含这 4 个字段的 Models，在我们定义自己的 Models 时，可以直接内嵌到结构体内，例如：


```go
type Animal struct {
    gorm.Model
    AnimalID int64     `gorm:"column:animalID"` // 将列名设为 `animalID`
    Birthday time.Time `gorm:"column:birthday"` // 将列名设为 `birthday`
    Age      int64     `gorm:"column:age"`      // 将列名设为 `age`
}
```
Models 中的字段能支持很多 GORM 标签，但如果我们不使用 GORM 自动创建表和迁移表结构的功能，很多标签我们实际上是用不到的。在开发中，用得最多的是 column 标签。

#### 连接数据库
在进行数据库的 CURD 操作之前，我们首先需要连接数据库。你可以通过以下代码连接 MySQL 数据库：


```go
import (
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)

func main() {
  // 参考 https://github.com/go-sql-driver/mysql#dsn-data-source-name 获取详情
  dsn := "user:pass@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
  db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
}
```
如果需要 GORM 正确地处理 time.Time 类型，在连接数据库时需要带上 parseTime 参数。如果要支持完整的 UTF-8 编码，可将charset=utf8更改为charset=utf8mb4。

GORM 支持连接池，底层是用 database/sql 包来维护连接池的，连接池设置如下：


```go
sqlDB, err := db.DB()
sqlDB.SetMaxIdleConns(100)              // 设置MySQL的最大空闲连接数（推荐100）
sqlDB.SetMaxOpenConns(100)             // 设置MySQL的最大连接数（推荐100）
sqlDB.SetConnMaxLifetime(time.Hour)    // 设置MySQL的空闲连接最大存活时间（推荐10s）
```
上面这些设置，也可以应用在大型后端项目中。


#### 创建记录
我们可以通过 db.Create 方法来创建一条记录：

```go
type User struct {
  gorm.Model
  Name         string
  Age          uint8
  Birthday     *time.Time
}
user := User{Name: "Jinzhu", Age: 18, Birthday: time.Now()}
result := db.Create(&user) // 通过数据的指针来创建
```

db.Create 函数会返回如下 3 个值：user.ID：返回插入数据的主键，这个是直接赋值给 user 变量。result.Error：返回 error。result.RowsAffected：返回插入记录的条数。

当需要插入的数据量比较大时，可以批量插入，以提高插入性能：


```go
var users = []User{ {Name: "jinzhu1"}, {Name: "jinzhu2"}, {Name: "jinzhu3"} }
DB.Create(&users)

for _, user := range users {
  user.ID // 1,2,3
}
```

#### 删除记录
我们可以通过 Delete 方法删除记录：


```go
// DELETE from users where id = 10 AND name = "jinzhu";
db.Where("name = ?", "jinzhu").Delete(&user)
```
GORM 也支持根据主键进行删除，例如：


```go
// DELETE FROM users WHERE id = 10;
db.Delete(&User{}, 10)
```
不过，**我更喜欢使用 db.Where 的方式进行删除，这种方式有两个优点。**


第一个优点是删除方式更通用。使用 db.Where 不仅可以根据主键删除，还能够随意组合条件进行删除。第二个优点是删除方式更显式，这意味着更易读。如果使用db.Delete(&User{}, 10)，你还需要确认 User 的主键，如果记错了主键，还可能会引入 Bug。


此外，GORM 也支持批量删除：


```go
db.Where("name in (?)", []string{"jinzhu", "colin"}).Delete(&User{})
```
GORM 支持两种删除方法：软删除和永久删除。下面我来分别介绍下。



软删除
软删除是指执行 Delete 时，记录不会被从数据库中真正删除。GORM 会将 DeletedAt 设置为当前时间，并且不能通过正常的方式查询到该记录。如果模型包含了一个 gorm.DeletedAt 字段，GORM 在执行删除操作时，会软删除该记录。

下面的删除方法就是一个软删除：


```go
// UPDATE users SET deleted_at="2013-10-29 10:23" WHERE age = 20;
db.Where("age = ?", 20).Delete(&User{})

// SELECT * FROM users WHERE age = 20 AND deleted_at IS NULL;
db.Where("age = 20").Find(&user)
```
可以看到，GORM 并没有真正把记录从数据库删除掉，而是只更新了 deleted_at 字段。在查询时，GORM 查询条件中新增了AND deleted_at IS NULL条件，所以这些被设置过 deleted_at 字段的记录不会被查询到。对于一些比较重要的数据，我们可以通过软删除的方式删除记录，软删除可以使这些重要的数据后期能够被恢复，并且便于以后的排障。

我们可以通过下面的方式查找被软删除的记录：


```go
// SELECT * FROM users WHERE age = 20;
db.Unscoped().Where("age = 20").Find(&users)
```
永久删除
如果想永久删除一条记录，可以使用 Unscoped：


```go
// DELETE FROM orders WHERE id=10;
db.Unscoped().Delete(&order)
```
或者，你也可以在模型中去掉 gorm.DeletedAt。



#### 更新记录
GORM 中，最常用的更新方法如下：


```go
db.First(&user)

user.Name = "jinzhu 2"
user.Age = 100
// UPDATE users SET name='jinzhu 2', age=100, birthday='2016-01-01', updated_at = '2013-11-17 21:34:10' WHERE id=111;
db.Save(&user)
```
上述方法会保留所有字段，所以执行 Save 时，需要先执行 First，获取某个记录的所有列的值，然后再对需要更新的字段设置值。还可以指定更新单个列：


```go
// UPDATE users SET age=200, updated_at='2013-11-17 21:34:10' WHERE name='colin';
db.Model(&User{}).Where("name = ?", "colin").Update("age", 200)
```
也可以指定更新多个列：


```go
// UPDATE users SET name='hello', age=18, updated_at = '2013-11-17 21:34:10' WHERE name = 'colin';
db.Model(&user).Where("name", "colin").Updates(User{Name: "hello", Age: 18, Active: false})
```
这里要注意，这个方法只会更新非零值的字段。


#### 查询数据
GORM 支持不同的查询方法，下面我来讲解三种在开发中经常用到的查询方式，分别是检索单个记录、查询所有符合条件的记录和智能选择字段。


检索单个记录下面是检索单个记录的示例代码：


```go
// 获取第一条记录（主键升序）
// SELECT * FROM users ORDER BY id LIMIT 1;
db.First(&user)

// 获取最后一条记录（主键降序）
// SELECT * FROM users ORDER BY id DESC LIMIT 1;
db.Last(&user)
result := db.First(&user)
result.RowsAffected // 返回找到的记录数
result.Error        // returns error

// 检查 ErrRecordNotFound 错误
errors.Is(result.Error, gorm.ErrRecordNotFound)
```
如果 model 类型没有定义主键，则按第一个字段排序。


查询所有符合条件的记录
示例代码如下：


```go
users := make([]*User, 0)

// SELECT * FROM users WHERE name <> 'jinzhu';
db.Where("name <> ?", "jinzhu").Find(&users)
```
智能选择字段
你可以通过 Select 方法，选择特定的字段。我们可以定义一个较小的结构体来接受选定的字段：


```go
type APIUser struct {
  ID   uint
  Name string
}

// SELECT `id`, `name` FROM `users` LIMIT 10;
db.Model(&User{}).Limit(10).Find(&APIUser{})
```
除了上面讲的三种常用的基本查询方法，GORM 还支持高级查询，下面我来介绍下。



#### 高级查询
GORM 支持很多高级查询功能，这里我主要介绍 4 种。

指定检索记录时的排序方式示例代码如下：

```go
// SELECT * FROM users ORDER BY age desc, name;
db.Order("age desc, name").Find(&users)
```
Limit & Offset
Offset 指定从第几条记录开始查询，Limit 指定返回的最大记录数。Offset 和 Limit 值为 -1 时，消除 Offset 和 Limit 条件。另外，Limit 和 Offset 位置不同，效果也不同。

```go
// SELECT * FROM users OFFSET 5 LIMIT 10;
db.Limit(10).Offset(5).Find(&users)
```
Distinct
Distinct 可以从数据库记录中选择不同的值。


```go
db.Distinct("name", "age").Order("name, age desc").Find(&results)
```
Count
Count 可以获取匹配的条数。


```go
var count int64
// SELECT count(1) FROM users WHERE name = 'jinzhu'; (count)
db.Model(&User{}).Where("name = ?", "jinzhu").Count(&count)
```

GORM 还支持很多高级查询功能，比如内联条件、Not 条件、Or 条件、Group & Having、Joins、Group、FirstOrInit、FirstOrCreate、迭代、FindInBatches 等。因为 IAM 项目中没有用到这些高级特性，我在这里就不展开介绍了。你如果感兴趣，可以看下GORM 的官方文档。


#### 原生 SQL
GORM 支持原生查询 SQL 和执行 SQL。原生查询 SQL 用法如下：


```go
type Result struct {
  ID   int
  Name string
  Age  int
}

var result Result
db.Raw("SELECT id, name, age FROM users WHERE name = ?", 3).Scan(&result)
```
原生执行 SQL 用法如下；

```go
db.Exec("DROP TABLE users")
db.Exec("UPDATE orders SET shipped_at=? WHERE id IN ?", time.Now(), []int64{1,2,3})
```


#### GORM 钩子
GORM 支持钩子功能，例如下面这个在插入记录前执行的钩子：


```go
func (u *User) BeforeCreate(tx *gorm.DB) (err error) {
  u.UUID = uuid.New()

    if u.Name == "admin" {
        return errors.New("invalid name")
    }
    return
}
```
GORM 支持的钩子见下表：


![img](https://static001.geekbang.org/resource/image/20/2c/20fb0b6a11dbcebd9ddf428517240d2c.jpg?wh=1920x1338)

### iam-apiserver 中的 CURD 操作实战
接下来，我来介绍下 iam-apiserver 是如何使用 GORM，对数据进行 CURD 操作的。


首先，我们需要配置连接 MySQL 的各类参数。iam-apiserver 通过NewMySQLOptions函数创建了一个带有默认值的MySQLOptions类型的变量，将该变量传给NewApp函数。在 App 框架中，最终会调用 MySQLOptions 提供的 AddFlags 方法，将 MySQLOptions 提供的命令行参数添加到 Cobra 命令行中。

接着，在PrepareRun函数中，调用GetMySQLFactoryOr函数，初始化并获取仓库层的实例mysqlFactory。实现了仓库层store.Factory接口：


```go
type Factory interface {
    Users() UserStore
    Secrets() SecretStore
    Policies() PolicyStore
    Close() error
}
```
GetMySQLFactoryOr 函数采用了我们在 11 讲 中提过的单例模式，确保 iam-apiserver 进程中只有一个仓库层的实例，这样可以减少内存开支和系统的性能开销。


GetMySQLFactoryOr 函数中，使用github.com/marmotedu/iam/pkg/db包提供的 New 函数，创建了 MySQL 实例。New 函数代码如下：


```go
func New(opts *Options) (*gorm.DB, error) {    
    dsn := fmt.Sprintf(`%s:%s@tcp(%s)/%s?charset=utf8&parseTime=%t&loc=%s`,    
        opts.Username,                                                                 
        opts.Password,                                 
        opts.Host,                   
        opts.Database,    
        true,                               
        "Local")    
                                                  
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{    
        Logger: logger.New(opts.LogLevel),                                                                             
    })    
    if err != nil {                                   
        return nil, err         
    }    
    
    sqlDB, err := db.DB()                              
    if err != nil {                                                                
        return nil, err                              
    }                                                                  
                                                             
    // SetMaxOpenConns sets the maximum number of open connections to the database.
    sqlDB.SetMaxOpenConns(opts.MaxOpenConnections)

    // SetConnMaxLifetime sets the maximum amount of time a connection may be reused.
    sqlDB.SetConnMaxLifetime(opts.MaxConnectionLifeTime)

    // SetMaxIdleConns sets the maximum number of connections in the idle connection pool.
    sqlDB.SetMaxIdleConns(opts.MaxIdleConnections)

    return db, nil
}
```

上述代码中，我们先创建了一个 *gorm.DB 类型的实例，并对该实例进行了如下设置：
- 通过 SetMaxOpenConns 方法，设置了 MySQL 的最大连接数（推荐 100）。
- 通过 SetConnMaxLifetime 方法，设置了 MySQL 的空闲连接最大存活时间（推荐 10s）。
- 通过 SetMaxIdleConns 方法，设置了 MySQL 的最大空闲连接数（推荐 100）。


GetMySQLFactoryOr 函数最后创建了 datastore 类型的变量 mysqlFactory，该变量是仓库层的变量。mysqlFactory 变量中，又包含了 *gorm.DB 类型的字段 db 。

最终，我们通过仓库层的变量 mysqlFactory，调用其 db 字段提供的方法来完成数据库的 CURD 操作。例如，创建密钥、更新密钥、删除密钥、获取密钥详情、查询密钥列表，具体代码如下（代码位于secret.go文件中）：


```go
// Create creates a new secret.
func (s *secrets) Create(ctx context.Context, secret *v1.Secret, opts metav1.CreateOptions) error {
  return s.db.Create(&secret).Error
}

// Update updates an secret information by the secret identifier.
func (s *secrets) Update(ctx context.Context, secret *v1.Secret, opts metav1.UpdateOptions) error {
  return s.db.Save(secret).Error
}

// Delete deletes the secret by the secret identifier.
func (s *secrets) Delete(ctx context.Context, username, name string, opts metav1.DeleteOptions) error {
  if opts.Unscoped {
    s.db = s.db.Unscoped()
  }

  err := s.db.Where("username = ? and name = ?", username, name).Delete(&v1.Secret{}).Error
  if err != nil && !errors.Is(err, gorm.ErrRecordNotFound) {
    return errors.WithCode(code.ErrDatabase, err.Error())
  }

  return nil
}

// Get return an secret by the secret identifier.
func (s *secrets) Get(ctx context.Context, username, name string, opts metav1.GetOptions) (*v1.Secret, error) {
  secret := &v1.Secret{}
  err := s.db.Where("username = ? and name= ?", username, name).First(&secret).Error
  if err != nil {
    if errors.Is(err, gorm.ErrRecordNotFound) {
      return nil, errors.WithCode(code.ErrSecretNotFound, err.Error())
    }

    return nil, errors.WithCode(code.ErrDatabase, err.Error())
  }

  return secret, nil
}

// List return all secrets.
func (s *secrets) List(ctx context.Context, username string, opts metav1.ListOptions) (*v1.SecretList, error) {
  ret := &v1.SecretList{}
  ol := gormutil.Unpointer(opts.Offset, opts.Limit)

  if username != "" {
    s.db = s.db.Where("username = ?", username)
  }

  selector, _ := fields.ParseSelector(opts.FieldSelector)
  name, _ := selector.RequiresExactMatch("name")

  d := s.db.Where(" name like ?", "%"+name+"%").
    Offset(ol.Offset).
    Limit(ol.Limit).
    Order("id desc").
    Find(&ret.Items).
    Offset(-1).
    Limit(-1).
    Count(&ret.TotalCount)

  return ret, d.Error
}
```
上面的代码中， s.db 就是 *gorm.DB 类型的字段。

上面的代码段执行了以下操作：
- 通过 s.db.Save 来更新数据库表的各字段；
- 通过 s.db.Unscoped 来永久性从表中删除一行记录。对于支持软删除的资源，我们还可以通过 opts.Unscoped 选项来控制是否永久删除记录。 true 永久删除， false 软删除，默认软删除。
- 通过 errors.Is(err, gorm.ErrRecordNotFound) 来判断 GORM 返回的错误是否是没有找到记录的错误类型。
- 通过下面两行代码，来获取查询条件 name 的值：


```go
selector, _ := fields.ParseSelector(opts.FieldSelector)    
name, _ := selector.RequiresExactMatch("name")
```
我们的整个调用链是：控制层 -> 业务层 -> 仓库层。这里你可能要问：**我们是如何调用到仓库层的实例 mysqlFactory的呢？**

这是因为我们的控制层实例包含了业务层的实例。在创建控制层实例时，我们传入了业务层的实例：


```go
type UserController struct {                                        
    srv srvv1.Service                                                      
}                                                                          
                                                                                            
// NewUserController creates a user handler.          
func NewUserController(store store.Factory) *UserController {
    return &UserController{                                     
        srv: srvv1.NewService(store),                                             
    }                                                      
} 
```
业务层的实例包含了仓库层的实例。在创建业务层实例时，传入了仓库层的实例：


```go
type service struct {                                                      
    store store.Factory                                                                     
}                                                     
                                                             
// NewService returns Service interface.                        
func NewService(store store.Factory) Service {                                    
    return &service{                                       
        store: store,                                             
    }
}

```
通过这种包含关系，我们在控制层可以调用业务层的实例，在业务层又可以调用仓库层的实例。这样，我们最终通过仓库层实例的 db 字段（*gorm.DB 类型）完成数据库的 CURD 操作。


### 总结
在 Go 项目中，我们需要使用 ORM 来进行数据库的 CURD 操作。在 Go 生态中，当前最受欢迎的 ORM 是 GORM，IAM 项目也使用了 GORM。GORM 有很多功能，常用的功能有模型定义、连接数据库、创建记录、删除记录、更新记录和查询数据。这些常用功能的常见使用方式如下：

```go
package main

import (
  "fmt"
  "log"

  "github.com/spf13/pflag"
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)

type Product struct {
  gorm.Model
  Code  string `gorm:"column:code"`
  Price uint   `gorm:"column:price"`
}

// TableName maps to mysql table name.
func (p *Product) TableName() string {
  return "product"
}

var (
  host     = pflag.StringP("host", "H", "127.0.0.1:3306", "MySQL service host address")
  username = pflag.StringP("username", "u", "root", "Username for access to mysql service")
  password = pflag.StringP("password", "p", "root", "Password for access to mysql, should be used pair with password")
  database = pflag.StringP("database", "d", "test", "Database name to use")
  help     = pflag.BoolP("help", "h", false, "Print this help message")
)

func main() {
  // Parse command line flags
  pflag.CommandLine.SortFlags = false
  pflag.Usage = func() {
    pflag.PrintDefaults()
  }
  pflag.Parse()
  if *help {
    pflag.Usage()
    return
  }

  dsn := fmt.Sprintf(`%s:%s@tcp(%s)/%s?charset=utf8&parseTime=%t&loc=%s`,
    *username,
    *password,
    *host,
    *database,
    true,
    "Local")
  db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
  if err != nil {
    panic("failed to connect database")
  }

  // 1. Auto migration for given models
  db.AutoMigrate(&Product{})

  // 2. Insert the value into database
  if err := db.Create(&Product{Code: "D42", Price: 100}).Error; err != nil {
    log.Fatalf("Create error: %v", err)
  }
  PrintProducts(db)

  // 3. Find first record that match given conditions
  product := &Product{}
  if err := db.Where("code= ?", "D42").First(&product).Error; err != nil {
    log.Fatalf("Get product error: %v", err)
  }

  // 4. Update value in database, if the value doesn't have primary key, will insert it
  product.Price = 200
  if err := db.Save(product).Error; err != nil {
    log.Fatalf("Update product error: %v", err)
  }
  PrintProducts(db)

  // 5. Delete value match given conditions
  if err := db.Where("code = ?", "D42").Delete(&Product{}).Error; err != nil {
    log.Fatalf("Delete product error: %v", err)
  }
  PrintProducts(db)
}

// List products
func PrintProducts(db *gorm.DB) {
  products := make([]*Product, 0)
  var count int64
  d := db.Where("code like ?", "%D%").Offset(0).Limit(2).Order("id desc").Find(&products).Offset(-1).Limit(-1).Count(&count)
  if d.Error != nil {
    log.Fatalf("List products error: %v", d.Error)
  }

  log.Printf("totalcount: %d", count)
  for _, product := range products {
    log.Printf("\tcode: %s, price: %d\n", product.Code, product.Price)
  }
}
```

此外，GORM 还支持原生查询 SQL 和原生执行 SQL，可以满足更加复杂的 SQL 场景。GORM 中，还有一个非常有用的功能是支持 Hooks。Hooks 可以在执行某个 CURD 操作前被调用。在 Hook 中，可以添加一些非常有用的功能，例如生成唯一 ID。目前，GORM 支持 BeforeXXX 、 AfterXXX 和 AfterFind Hook，其中 XXX 可以是 Save、Create、Delete、Update。最后，我还介绍了 IAM 项目的 GORM 实战，具体使用方式跟总结中的示例代码大体保持一致，你可以返回文稿查看。

## 31 | 数据流：通过iam-authz-server设计，看数据流服务的设计
在 28 讲 和 29 讲 ，我介绍了 IAM 的控制流服务 iam-apiserver 的设计和实现。这一讲，我们再来看下 IAM 数据流服务 iam-authz-server 的设计和实现。因为 iam-authz-server 是数据流服务，对性能要求较高，所以采用了一些机制来最大化 API 接口的性能。另外，为了提高开发效率，避免重复造轮子，iam-authz-server 和 iam-apiserver 共享了大部分的功能代码。接下来，我们就来看下，iam-authz-server 是如何跟 iam-apiserver 共享代码的，以及 iam-authz-server 是如何保证 API 接口性能的。


### iam-authz-server 的功能介绍
iam-authz-server 目前的唯一功能，是通过提供 /v1/authz RESTful API 接口完成资源授权。 /v1/authz 接口是通过github.com/ory/ladon来完成资源授权的。

因为 iam-authz-server 承载了数据流的请求，需要确保 API 接口具有较高的性能。为了保证 API 接口的性能，iam-authz-server 在设计上使用了大量的缓存技术。


### github.com/ory/ladon 包介绍
因为 iam-authz-server 资源授权是通过 github.com/ory/ladon 来完成的，为了让你更好地理解 iam-authz-server 的授权策略，在这里我先介绍下 github.com/ory/ladon 包。

Ladon 是用 Go 语言编写的用于实现访问控制策略的库，类似于 RBAC（基于角色的访问控制系统，Role Based Access Control）和 ACL（访问控制列表，Access Control Lists）。但是与 RBAC 和 ACL 相比，Ladon 可以实现更细粒度的访问控制，并且能够在更为复杂的环境中（例如多租户、分布式应用程序和大型组织）工作。


Ladon 解决了这个问题：在特定的条件下，谁能够 / 不能够对哪些资源做哪些操作。为了解决这个问题，Ladon 引入了授权策略。授权策略是一个有语法规范的文档，这个文档描述了谁在什么条件下能够对哪些资源做哪些操作。Ladon 可以用请求的上下文，去匹配设置的授权策略，最终判断出当前授权请求是否通过。下面是一个 Ladon 的授权策略样例：


```
{
  "description": "One policy to rule them all.",
  "subjects": ["users:<peter|ken>", "users:maria", "groups:admins"],
  "actions" : ["delete", "<create|update>"],
  "effect": "allow",
  "resources": [
    "resources:articles:<.*>",
    "resources:printer"
  ],
  "conditions": {
    "remoteIP": {
        "type": "CIDRCondition",
        "options": {
            "cidr": "192.168.0.1/16"
        }
    }
  }
}
```
策略（Policy）由若干元素构成，用来描述授权的具体信息，你可以把它们看成一组规则。核心元素包括主题（Subject）、操作（Action）、效力（Effect）、资源（Resource）以及生效条件（Condition）。元素保留字仅支持小写，它们在描述上没有顺序要求。对于没有特定约束条件的策略，Condition 元素是可选项。一条策略包含下面 6 个元素：

- 主题（Subject），主题名是唯一的，代表一个授权主题。例如，“ken” or “printer-service.mydomain.com”。
- 操作（Action），描述允许或拒绝的操作。
- 效力（Effect），描述策略产生的结果是“允许”还是“拒绝”，包括 allow（允许）和 deny（拒绝）。
- 资源（Resource），描述授权的具体数据。
- 生效条件（Condition），描述策略生效的约束条件。
- 描述（Description），策略的描述。


有了授权策略，我们就可以传入请求上下文，由 Ladon 来决定请求是否能通过授权。下面是一个请求示例：



```
{
  "subject": "users:peter",
  "action" : "delete",
  "resource": "resources:articles:ladon-introduction",
  "context": {
    "remoteIP": "192.168.0.5"
  }
}
```
可以看到，在 remoteIP="192.168.0.5" 生效条件（Condition）下，针对主题（Subject） users:peter 对资源（Resource） resources:articles:ladon-introduction 的 delete 操作（Action），授权策略的效力（Effect）是 allow 的。所以 Ladon 会返回如下结果：



```
{
    "allowed": true
}
```
Ladon 支持很多 Condition，具体见下表：


![img](https://static001.geekbang.org/resource/image/b8/dd/b84d2a1dc0e9ac07605a867594d734dd.jpg?wh=1920x1521)

至于如何使用这些 Condition，你可以参考 [Ladon Condition 使用示例](https://github.com/marmotedu/geekbang-go/blob/master/LadonCondition%E4%BD%BF%E7%94%A8%E7%A4%BA%E4%BE%8B.md)。此外，Ladon 还支持自定义 Condition。


另外，Ladon 还支持授权审计，用来记录授权历史。我们可以通过在 ladon.Ladon 中附加一个 ladon.AuditLogger 来实现：


```go
import "github.com/ory/ladon"
import manager "github.com/ory/ladon/manager/memory"

func main() {

    warden := ladon.Ladon{
        Manager: manager.NewMemoryManager(),
        AuditLogger: &ladon.AuditLoggerInfo{}
    }

    // ...
}
```
在上面的示例中，我们提供了 ladon.AuditLoggerInfo，该 AuditLogger 会在授权时打印调用的策略到标准错误。AuditLogger 是一个 interface：


```go
// AuditLogger tracks denied and granted authorizations.
type AuditLogger interface {
    LogRejectedAccessRequest(request *Request, pool Policies, deciders Policies)
    LogGrantedAccessRequest(request *Request, pool Policies, deciders Policies)
}
```
要实现一个新的 AuditLogger，你只需要实现 AuditLogger 接口就可以了。比如，我们可以实现一个 AuditLogger，将授权日志保存到 Redis 或者 MySQL 中。

Ladon 支持跟踪一些授权指标，比如 deny、allow、not match、error。你可以通过实现 ladon.Metric 接口，来对这些指标进行处理。ladon.Metric 接口定义如下：


```go
// Metric is used to expose metrics about authz
type Metric interface {
    // RequestDeniedBy is called when we get explicit deny by policy
    RequestDeniedBy(Request, Policy)
    // RequestAllowedBy is called when a matching policy has been found.
    RequestAllowedBy(Request, Policies)
    // RequestNoMatch is called when no policy has matched our request
    RequestNoMatch(Request)
    // RequestProcessingError is called when unexpected error occured
    RequestProcessingError(Request, Policy, error)
}
```
例如，你可以通过下面的示例，将这些指标暴露给 prometheus：



```go
type prometheusMetrics struct{}

func (mtr *prometheusMetrics) RequestDeniedBy(r ladon.Request, p ladon.Policy) {}
func (mtr *prometheusMetrics) RequestAllowedBy(r ladon.Request, policies ladon.Policies) {}
func (mtr *prometheusMetrics) RequestNoMatch(r ladon.Request) {}
func (mtr *prometheusMetrics) RequestProcessingError(r ladon.Request, err error) {}

func main() {

    warden := ladon.Ladon{
        Manager: manager.NewMemoryManager(),
        Metric:  &prometheusMetrics{},
    }

    // ...
}
```
在使用 Ladon 的过程中，有两个地方需要你注意：
- 所有检查都区分大小写，因为主题值可能是区分大小写的 ID。
- 如果 ladon.Ladon 无法将策略与请求匹配，会默认授权结果为拒绝，并返回错误。


### iam-authz-server 使用方法介绍
上面，我介绍了 iam-authz-server 的资源授权功能，这里介绍下如何使用 iam-authz-server，也就是如何调用 /v1/authz 接口完成资源授权。你可以通过下面的 3 大步骤，来完成资源授权请求。



**第一步，登陆 iam-apiserver，创建授权策略和密钥。**

这一步又分为 3 个小步骤。登陆 iam-apiserver 系统，获取访问令牌：

```
$ token=`curl -s -XPOST -H'Content-Type: application/json' -d'{"username":"admin","password":"Admin@2021"}' http://127.0.0.1:8080/login | jq -r .token`
```

创建授权策略：


```
$ curl -s -XPOST -H"Content-Type: application/json" -H"Authorization: Bearer $token" -d'{"metadata":{"name":"authztest"},"policy":{"description":"One policy to rule them all.","subjects":["users:<peter|ken>","users:maria","groups:admins"],"actions":["delete","<create|update>"],"effect":"allow","resources":["resources:articles:<.*>","resources:printer"],"conditions":{"remoteIP":{"type":"CIDRCondition","options":{"cidr":"192.168.0.1/16"}}}}}' http://127.0.0.1:8080/v1/policies
```
创建密钥，并从请求结果中提取 secretID 和 secretKey：


```
$ curl -s -XPOST -H"Content-Type: application/json" -H"Authorization: Bearer $token" -d'{"metadata":{"name":"authztest"},"expires":0,"description":"admin secret"}' http://127.0.0.1:8080/v1/secrets
{"metadata":{"id":23,"name":"authztest","createdAt":"2021-04-08T07:24:50.071671422+08:00","updatedAt":"2021-04-08T07:24:50.071671422+08:00"},"username":"admin","secretID":"ZuxvXNfG08BdEMqkTaP41L2DLArlE6Jpqoox","secretKey":"7Sfa5EfAPIwcTLGCfSvqLf0zZGCjF3l8","expires":0,"description":"admin secret"}
```
**第二步，生成访问 iam-authz-server 的 token。**

iamctl 提供了 jwt sigin 子命令，可以根据 secretID 和 secretKey 签发 Token，方便使用。



```
$ iamctl jwt sign ZuxvXNfG08BdEMqkTaP41L2DLArlE6Jpqoox 7Sfa5EfAPIwcTLGCfSvqLf0zZGCjF3l8 # iamctl jwt sign $secretID $secretKey
eyJhbGciOiJIUzI1NiIsImtpZCI6Ilp1eHZYTmZHMDhCZEVNcWtUYVA0MUwyRExBcmxFNkpwcW9veCIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYxNzg0NTE5NSwiaWF0IjoxNjE3ODM3OTk1LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MTc4Mzc5OTV9.za9yLM7lHVabPAlVQLCqXEaf8sTU6sodAsMXnmpXjMQ
```
你可以通过 iamctl jwt show 来查看 Token 的内容：


```
$ iamctl jwt show eyJhbGciOiJIUzI1NiIsImtpZCI6Ilp1eHZYTmZHMDhCZEVNcWtUYVA0MUwyRExBcmxFNkpwcW9veCIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYxNzg0NTE5NSwiaWF0IjoxNjE3ODM3OTk1LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MTc4Mzc5OTV9.za9yLM7lHVabPAlVQLCqXEaf8sTU6sodAsMXnmpXjMQ
Header:
{
    "alg": "HS256",
    "kid": "ZuxvXNfG08BdEMqkTaP41L2DLArlE6Jpqoox",
    "typ": "JWT"
}
Claims:
{
    "aud": "iam.authz.marmotedu.com",
    "exp": 1617845195,
    "iat": 1617837995,
    "iss": "iamctl",
    "nbf": 1617837995
}
```
我们生成的 Token 包含了下面这些信息。


Header
alg：生成签名的算法。kid：密钥 ID。typ：Token 的类型，这里是 JWT。



Claims
aud：JWT Token 的接受者。exp：JWT Token 的过期时间（UNIX 时间格式）。iat：JWT Token 的签发时间（UNIX 时间格式）。iss：签发者，因为我们是用 iamctl 工具签发的，所以这里的签发者是 iamctl。nbf：JWT Token 的生效时间（UNIX 时间格式），默认是签发时间。




**第三步，调用/v1/authz接口，完成资源授权请求**。请求方法如下：


```
$ curl -s -XPOST -H'Content-Type: application/json' -H'Authorization: Bearer eyJhbGciOiJIUzI1NiIsImtpZCI6Ilp1eHZYTmZHMDhCZEVNcWtUYVA0MUwyRExBcmxFNkpwcW9veCIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJpYW0uYXV0aHoubWFybW90ZWR1LmNvbSIsImV4cCI6MTYxNzg0NTE5NSwiaWF0IjoxNjE3ODM3OTk1LCJpc3MiOiJpYW1jdGwiLCJuYmYiOjE2MTc4Mzc5OTV9.za9yLM7lHVabPAlVQLCqXEaf8sTU6sodAsMXnmpXjMQ' -d'{"subject":"users:maria","action":"delete","resource":"resources:articles:ladon-introduction","context":{"remoteIP":"192.168.0.5"}}' http://127.0.0.1:9090/v1/authz
{"allowed":true}
```
如果授权通过，会返回：{"allowed":true} 。 如果授权失败，则返回：


```
{"allowed":false,"denied":true,"reason":"Request was denied by default"}
```

### iam-authz-server 的代码实现
接下来，我们来看下 iam-authz-server 的具体实现，我会从配置处理、启动流程、请求处理流程和代码架构 4 个方面来讲解。



#### iam-authz-server 的配置处理
iam-authz-server 服务的 main 函数位于authzserver.go文件中，你可以跟读代码，了解 iam-authz-server 的代码实现。iam-authz-server 的服务框架设计跟 iam-apiserver 的服务框架设计保持一致，也是有 3 种配置：Options 配置、组件配置和 HTTP 服务配置。


Options 配置见options.go文件：


```go
type Options struct {
    RPCServer               string
    ClientCA                string
    GenericServerRunOptions *genericoptions.ServerRunOptions
    InsecureServing         *genericoptions.InsecureServingOptions
    SecureServing           *genericoptions.SecureServingOptions
    RedisOptions            *genericoptions.RedisOptions
    FeatureOptions          *genericoptions.FeatureOptions
    Log                     *log.Options
    AnalyticsOptions        *analytics.AnalyticsOptions
}
```

和 iam-apiserver 相比，iam-authz-server 多了 AnalyticsOptions，用来配置 iam-authz-server 内的 Analytics 服务，Analytics 服务会将授权日志异步写入到 Redis 中。

iam-apiserver 和 iam-authz-server 共用了 GenericServerRunOptions、InsecureServing、SecureServing、FeatureOptions、RedisOptions、Log 这些配置。所以，我们只需要用简单的几行代码，就可以将很多配置项都引入到 iam-authz-server 的命令行参数中，这也是命令行参数分组带来的好处：批量共享。


#### iam-authz-server 启动流程设计
接下来，我们来详细看下 iam-authz-server 的启动流程。

iam-authz-server 的启动流程也和 iam-apiserver 基本保持一致。二者比较大的不同在于 Options 参数配置和应用初始化内容。另外，和 iam-apiserver 相比，iam-authz-server 只提供了 REST API 服务。启动流程如下图所示：


![img](https://static001.geekbang.org/resource/image/19/35/195178d37854bac7d5243d80e42a4c35.jpg?wh=2248x799)

#### iam-authz-server 的 RESTful API 请求处理流程
iam-authz-server 的请求处理流程也是清晰、规范的，具体流程如下图所示：


![img](https://static001.geekbang.org/resource/image/5a/89/5a83384f5762c41831190628bfa60989.jpg?wh=2248x780)

首先，我们通过 API 调用（ + ）请求 iam-authz-server 提供的 RESTful API 接口 POST /v1/authz 。接着，Gin Web 框架接收到 HTTP 请求之后，会通过认证中间件完成请求的认证，iam-authz-server 采用了 Bearer 认证方式。


然后，请求会被我们加载的一系列中间件所处理，例如跨域、RequestID、Dump 等中间件。最后，根据 + 进行路由匹配。

比如，我们请求的 RESTful API 是POST /v1/authz，Gin Web 框架会根据 HTTP Method 和 HTTP Request Path，查找注册的 Controllers，最终匹配到 authzController.Authorize Controller。在 Authorize Controller 中，会先解析请求参数，接着校验请求参数、调用业务层的方法进行资源授权，最后处理业务层的返回结果，返回最终的 HTTP 请求结果。


#### iam-authz-server 的代码架构
iam-authz-server 的代码设计和 iam-apiserver 一样，遵循简洁架构设计。


iam-authz-server 的代码架构也分为 4 层，分别是模型层（Models）、控制层（Controller）、业务层 （Service）和仓库层（Repository）。从控制层、业务层到仓库层，从左到右层级依次加深。模型层独立于其他层，可供其他层引用。如下图所示：


![img](https://static001.geekbang.org/resource/image/a5/dd/a57832495c9e031a94282f0a8a3a61dd.jpg?wh=2248x702)

iam-authz-server 和 iam-apiserver 的代码架构有这三点不同：iam-authz-server 客户端不支持前端和命令行。iam-authz-server 仓库层对接的是 iam-apiserver 微服务，而非数据库。iam-authz-server 业务层的代码存放在目录authorization中。



### iam-authz-server 关键代码分析
和 iam-apiserver 一样，iam-authz-server 也包含了一些优秀的设计思路和关键代码，这里我来一一介绍下。


#### 资源授权
先来看下，iam-authz-server 是如何实现资源授权的。

我们可以调用 iam-authz-server 的 /v1/authz API 接口，实现资源的访问授权。 /v1/authz 对应的 controller 方法是Authorize：


```go
func (a *AuthzController) Authorize(c *gin.Context) {
  var r ladon.Request
  if err := c.ShouldBind(&r); err != nil {
    core.WriteResponse(c, errors.WithCode(code.ErrBind, err.Error()), nil)

    return
  }

  auth := authorization.NewAuthorizer(authorizer.NewAuthorization(a.store))
  if r.Context == nil {
    r.Context = ladon.Context{}
  }

  r.Context["username"] = c.GetString("username")
  rsp := auth.Authorize(&r)

  core.WriteResponse(c, nil, rsp)
}
```
该函数使用 github.com/ory/ladon 包进行资源访问授权，授权流程如下图所示：


![img](https://static001.geekbang.org/resource/image/7c/a6/7c251c61cb535714edd390eac18df8a6.jpg?wh=2248x920)
具体分为以下几个步骤：第一步，在 Authorize 方法中调用 c.ShouldBind(&r) ，将 API 请求参数解析到 ladon.Request 类型的结构体变量中。第二步，调用authorization.NewAuthorizer函数，该函数会创建并返回包含 Manager 和 AuditLogger 字段的Authorizer类型的变量。

Manager 包含一些函数，比如 Create、Update 和 FindRequestCandidates 等，用来对授权策略进行增删改查。AuditLogger 包含 LogRejectedAccessRequest 和 LogGrantedAccessRequest 函数，分别用来记录被拒绝的授权请求和被允许的授权请求，将其作为审计数据使用。



第三步，调用auth.Authorize函数，对请求进行访问授权。auth.Authorize 函数内容如下：


```go
func (a *Authorizer) Authorize(request *ladon.Request) *authzv1.Response {
  log.Debug("authorize request", log.Any("request", request))

  if err := a.warden.IsAllowed(request); err != nil {
    return &authzv1.Response{
      Denied: true,
      Reason: err.Error(),
    }
  }

  return &authzv1.Response{
    Allowed: true,
  }
}
```
该函数会调用 a.warden.IsAllowed(request) 完成资源访问授权。IsAllowed 函数会调用 FindRequestCandidates(r) 查询所有的策略列表，这里要注意，我们只需要查询请求用户的 policy 列表。在 Authorize 函数中，我们将 username 存入 ladon Request 的 context 中：



```
r.Context["username"] = c.GetHeader("username")
```

在FindRequestCandidates函数中，我们可以从 Request 中取出 username，并根据 username 查询缓存中的 policy 列表，FindRequestCandidates 实现如下：



```go
func (m *PolicyManager) FindRequestCandidates(r *ladon.Request) (ladon.Policies, error) {
    username := ""
  
    if user, ok := r.Context["username"].(string); ok {
      username = user
    }
  
    policies, err := m.client.List(username)
    if err != nil {
      return nil, errors.Wrap(err, "list policies failed")
    }
  
    ret := make([]ladon.Policy, 0, len(policies))
    for _, policy := range policies {
      ret = append(ret, policy)
    }
  
    return ret, nil
  }

```

IsAllowed 函数代码如下：



```go
func (l *Ladon) IsAllowed(r *Request) (err error) {
    policies, err := l.Manager.FindRequestCandidates(r)
    if err != nil {
        go l.metric().RequestProcessingError(*r, nil, err)
        return err
    }

    return l.DoPoliciesAllow(r, policies)
}
```
IsAllowed 会调用 DoPoliciesAllow(r, policies) 函数进行权限校验。如果权限校验不通过（请求在指定条件下不能够对资源做指定操作），就调用 LogRejectedAccessRequest 函数记录拒绝的请求，并返回值为非 nil 的 error，error 中记录了授权失败的错误信息。如果权限校验通过，则调用 LogGrantedAccessRequest 函数记录允许的请求，并返回值为 nil 的 error。


为了降低请求延时，LogRejectedAccessRequest 和 LogGrantedAccessRequest 会将授权记录存储在 Redis 中，之后由 iam-pump 进程读取 Redis，并将授权记录持久化存储在 MongoDB 中。



#### 缓存设计
iam-authz-server 主要用来做资源访问授权，属于数据流的组件，对接口访问性能有比较高的要求，所以该组件采用了缓存的机制。如下图所示：


![img](https://static001.geekbang.org/resource/image/05/51/05d1c9a9acdc451f915684c18c8b9f51.jpg?wh=2248x822)
iam-authz-server 组件通过缓存密钥和授权策略信息到内存中，加快密钥和授权策略的查询速度。通过缓存授权记录到内存中，提高了授权数据的写入速度，从而大大降低了授权请求接口的延时。

上面的缓存机制用到了 Redis key-value 存储，所以在 iam-authz-server 初始化阶段，需要先建立 Redis 连接（位于initialize函数中）：


```
go storage.ConnectToRedis(ctx, s.buildStorageConfig())
```

这个代码会维护一个 Redis 连接，如果 Redis 连接断掉，会尝试重连。这种方式可以使我们在调用 Redis 接口进行数据读写时，不用考虑连接断开的问题。

接下来，我们就来详细看看，iam-authz-server 是如何实现缓存机制的。


**先来看下密钥和策略缓存。**iam-authz-server 通过load包来完成密钥和策略的缓存。


在 iam-authz-server 进程启动时，会创建并启动一个 Load 服务（位于initialize函数中）：


```
load.NewLoader(ctx, cacheIns).Start() 
```

**先来看创建 Load 服务**。创建 Load 服务时，传入了 cacheIns 参数，cacheIns 是一个实现了Loader接口的实例：


```
type Loader interface {
    Reload() error
}
```

然后看启动 Load 服务。通过 Load 实例的 Start 方法来启动 Load 服务：


```go
func (l *Load) Start() {
    go startPubSubLoop()
    go l.reloadQueueLoop()
    go l.reloadLoop()

    l.DoReload()
}
```
Start 函数先启动了 3 个协程，再调用 l.DoReload() 完成一次密钥和策略的同步：


```go
func (l *Load) DoReload() {
    l.lock.Lock()
    defer l.lock.Unlock()

    if err := l.loader.Reload(); err != nil {
        log.Errorf("faild to refresh target storage: %s", err.Error())
    }

    log.Debug("refresh target storage succ")
}
```
上面我们说了，创建 Load 服务时，传入的 cacheIns 实例是一个实现了 Loader 接口的实例，所以在DoReload方法中，可以直接调用 Reload 方法。cacheIns 的 Reload 方法会从 iam-apiserver 中同步密钥和策略信息到 iam-authz-server 缓存中。

我们再来看下，startPubSubLoop、reloadQueueLoop、reloadLoop 这 3 个 Go 协程分别完成了什么功能。



startPubSubLoop 协程
startPubSubLoop函数通过StartPubSubHandler函数，订阅 Redis 的 iam.cluster.notifications channel，并注册一个回调函数：

```go
func(v interface{}) {
    handleRedisEvent(v, nil, nil)
}
```
handleRedisEvent函数中，会将消息解析为Notification类型的消息，并判断 Command 的值。如果是 NoticePolicyChanged 或 NoticeSecretChanged，就会向 reloadQueue channel 中写入一个回调函数。因为我们不需要用回调函数做任何事情，所以这里回调函数是 nil。 reloadQueue 主要用来告诉程序，需要完成一次密钥和策略的同步。


reloadQueueLoop 协程
reloadQueueLoop 函数会监听 reloadQueue ，当发现有新的消息（这里是回调函数）写入时，会实时将消息缓存到 requeue 切片中，代码如下：

```go
func (l *Load) reloadQueueLoop(cb ...func()) {
    for {
      select {
      case <-l.ctx.Done():
        return
      case fn := <-reloadQueue:
        requeueLock.Lock()
        requeue = append(requeue, fn)
        requeueLock.Unlock()
        log.Info("Reload queued")
        if len(cb) != 0 {
          cb[0]()
        }
      }
    }
  }

```

reloadLoop 协程
通过reloadLoop函数启动一个 timer 定时器，每隔 1 秒会检查 requeue 切片是否为空，如果不为空，则调用 l.DoReload 方法，从 iam-apiserver 中拉取密钥和策略，并缓存在内存中。


密钥和策略的缓存模型如下图所示：


![img](https://static001.geekbang.org/resource/image/a2/11/a2f5694e5d6291ca610b84ee49469211.jpg?wh=2248x890)


**密钥和策略缓存的具体流程如下：**接收上游消息（这里是从 Redis 中接收），将消息缓存到切片或者带缓冲的 channel 中，并启动一个消费协程去消费这些消息。这里的消费协程是 reloadLoop，reloadLoop 会每隔 1s 判断 requeue 切片是否长度为 0，如果不为 0，则执行 l.DoReload() 缓存密钥和策略。


讲完了密钥和策略缓存，再**来看下授权日志缓存。**


在启动 iam-authz-server 时，还会启动一个 Analytics 服务，代码如下（位于internal/authzserver/server.go文件中）：


```go
    if s.analyticsOptions.Enable {    
        analyticsStore := storage.RedisCluster{KeyPrefix: RedisKeyPrefix}    
        analyticsIns := analytics.NewAnalytics(s.analyticsOptions, &analyticsStore)    
        analyticsIns.Start()    
        s.gs.AddShutdownCallback(shutdown.ShutdownFunc(func(string) error {    
            analyticsIns.Stop()    
    
            return nil    
        }))    
    }
```
NewAnalytics函数会根据配置，创建一个 Analytics 实例：


```go
func NewAnalytics(options *AnalyticsOptions, store storage.AnalyticsHandler) *Analytics {
    ps := options.PoolSize
    recordsBufferSize := options.RecordsBufferSize
    workerBufferSize := recordsBufferSize / uint64(ps)
    log.Debug("Analytics pool worker buffer size", log.Uint64("workerBufferSize", workerBufferSize))
  
    recordsChan := make(chan *AnalyticsRecord, recordsBufferSize)
  
    return &Analytics{
      store:                      store,
      poolSize:                   ps,
      recordsChan:                recordsChan,
      workerBufferSize:           workerBufferSize,
      recordsBufferFlushInterval: options.FlushInterval,
    }
  } 
```
上面的代码创建了一个带缓冲的 recordsChan ：


```go
recordsChan := make(chan *AnalyticsRecord, recordsBufferSize)
```
recordsChan 存放的数据类型为AnalyticsRecord，缓冲区的大小为 recordsBufferSize （通过 --analytics.records-buffer-size 选项指定）。可以通过RecordHit函数，向recordsChan 中写入 AnalyticsRecord 类型的数据：


```go
func (r *Analytics) RecordHit(record *AnalyticsRecord) error {                                                         
    // check if we should stop sending records 1st                                                                     
    if atomic.LoadUint32(&r.shouldStop) > 0 {                                                                          
        return nil                                                                                                     
    }                                                                                                                  
                                                                                                                       
    // just send record to channel consumed by pool of workers                                                         
    // leave all data crunching and Redis I/O work for pool workers                                                    
    r.recordsChan <- record                                                                                            
                                                                                                                       
    return nil                                                                                                         
}   
```
iam-authz-server 是通过调用 LogGrantedAccessRequest 和 LogRejectedAccessRequest 函数来记录授权日志的。在记录授权日志时，会将授权日志写入 recordsChan channel 中。LogGrantedAccessRequest函数代码如下：


```go
func (auth *Authorization) LogGrantedAccessRequest(r *ladon.Request, p ladon.Policies, d ladon.Policies) {
    conclusion := fmt.Sprintf("policies %s allow access", joinPoliciesNames(d))                               
    rstring, pstring, dstring := convertToString(r, p, d)                          
    record := analytics.AnalyticsRecord{                     
        TimeStamp:  time.Now().Unix(),                              
        Username:   r.Context["username"].(string),                 
        Effect:     ladon.AllowAccess,                       
        Conclusion: conclusion,                              
        Request:    rstring,       
        Policies:   pstring,                                                   
        Deciders:   dstring,                                                   
    }                           
                           
    record.SetExpiry(0)
    _ = analytics.GetAnalytics().RecordHit(&record)         
} 
```
上面的代码，会创建 AnalyticsRecord 类型的结构体变量，并调用 RecordHit 将变量的值写入 recordsChan channel 中。将授权日志写入 recordsChan   channel 中，而不是直接写入 Redis 中，这可以大大减少写入延时，减少接口的响应延时。


还有一个 worker 进程从 recordsChan 中读取数据，并在数据达到一定阈值之后，批量写入 Redis 中。在Start函数中，我们创建了一批 worker，worker 个数可以通过 --analytics.pool-size 来指定 。Start 函数内容如下：



```go
func (r *Analytics) Start() {
    analytics = r
    r.store.Connect()
  
    // start worker pool
    atomic.SwapUint32(&r.shouldStop, 0)
    for i := 0; i < r.poolSize; i++ {
      r.poolWg.Add(1)
      go r.recordWorker()
    }
  
    // stop analytics workers
    go r.Stop()
  }
```
上面的代码通过 go r.recordWorker() 创建了 由poolSize 指定个数的recordWorker（worker），recordWorker 函数会从 recordsChan 中读取授权日志并存入 recordsBuffer 中，recordsBuffer 的大小为 workerBufferSize，workerBufferSize 计算公式为：


```go
ps := options.PoolSize
recordsBufferSize := options.RecordsBufferSize
workerBufferSize := recordsBufferSize / uint64(ps)
```
其中，options.PoolSize 由命令行参数 --analytics.pool-size 指定，代表 worker 的个数，默认 50；options.RecordsBufferSize 由命令行参数 --analytics.records-buffer-size 指定，代表缓存的授权日志消息数。也就是说，我们把缓存的记录平均分配给所有的 worker。


当 recordsBuffer 存满或者达到投递最大时间后，调用 r.Store.AppendToSetPipelined(analyticsKeyName, recordsBuffer) 将记录批量发送给 Redis，为了提高传输速率，这里将日志内容编码为 msgpack 格式后再传输。


上面的缓存方法可以抽象成一个缓存模型，满足实际开发中的大部分需要异步转存的场景，如下图所示：


![img](https://static001.geekbang.org/resource/image/47/95/479fyy2cd16a6c1fa5f6074f7ce6fe95.jpg?wh=2248x668)

Producer 将数据投递到带缓冲的 channel 中，后端有多个 worker 消费 channel 中的数据，并进行批量投递。你可以设置批量投递的条件，**一般至少包含最大投递日志数和最大投递时间间隔这两个。**

通过以上缓冲模型，你可以将日志转存的时延降到最低。



#### 数据一致性
上面介绍了 iam-authz-server 的 /v1/authz 接口，为了最大化地提高性能，采用了大量的缓存设计。因为数据会分别在持久化存储和内存中都存储一份，就可能会出现数据不一致的情况。所以，我们也要确保缓存中的数据和数据库中的数据是一致的。数据一致性架构如下图所示：


![img](https://static001.geekbang.org/resource/image/72/a4/72c2afe63d197e7335deec1ac9f550a4.jpg?wh=2248x1006)

密钥和策略同步流程如下：


1. 通过 iam-webconsole 请求 iam-apiserver 创建（或更新、删除）密钥（或策略）。
2. iam-apiserver 收到“写”请求后，会向 Redis iam.cluster.notifications channel 发送 PolicyChanged 或 SecretChanged 消息。
3. Loader 收到消息后，会触发 cache loader 实例执行 Reload 方法，重新从 iam-apiserver 中同步密钥和策略信息。

Loader 不会关心 Reload 方法的具体实现，只会在收到指定消息时，执行 Reload 方法。通过这种方式，我们可以实现不同的缓存策略。


在 cache 实例的 Reload 方法中，我们其实是调用仓库层 Secret 和 Policy 的 List 方法来获取密钥和策略列表。仓库层又是通过执行 gRPC 请求，从 iam-apiserver 中获取密钥和策略列表。

cache 的Reload方法，会将获取到的密钥和策略列表缓存在ristretto类型的 Cache 中，供业务层调用。业务层代码位于internal/authzserver/authorization目录下。



### 总结
这一讲中，我介绍了 IAM 数据流服务 iam-authz-server 的设计和实现。iam-authz-server 提供了 /v1/authz RESTful API 接口，供第三方用户完成资源授权功能，具体是使用 Ladon 包来完成资源授权的。Ladon 包解决了“在特定的条件下，谁能够 / 不能够对哪些资源做哪些操作”的问题。


iam-authz-server 的配置处理、启动流程和请求处理流程跟 iam-apiserver 保持一致。此外，iam-authz-server 也实现了简洁架构。iam-authz-server 通过缓存密钥和策略信息、缓存授权日志来提高 /v1/authz 接口的性能。

在缓存密钥和策略信息时，为了和 iam-apiserver 中的密钥和策略信息保持一致，使用了 Redis Pub/Sub 机制。当 iam-apiserver 有密钥 / 策略变更时，会往指定的 Redis channel Pub 一条消息。iam-authz-server 订阅相同的 channel，在收到新消息时，会解析消息，并重新从 iam-apiserver 中获取密钥和策略信息，缓存在内存中。

iam-authz-server 执行完资源授权之后，会将授权日志存放在一个带缓冲的 channel 中。后端有多个 worker 消费 channel 中的数据，并进行批量投递。可以设置批量投递的条件，例如最大投递日志数和最大投递时间间隔。

## 32 | 数据处理：如何高效处理应用程序产生的数据？
我们来聊聊，如何更好地进行异步数据处理。一个大型应用为了后期的排障、运营等，会将一些请求数据保存在存储系统中，供日后使用。例如：应用将请求日志保存到 Elasticsearch 中，方便排障；网关将 API 请求次数、请求消息体等数据保存在数据库中，供控制台查询展示。为了满足这些需求，我们需要进行数据采集，数据采集在大型应用中很常见，但我发现不少开发者设计的数据采集服务，通常会存在下面这些问题：

- 采集服务只针对某个采集需求开发，如果采集需求有变，需要修改主代码逻辑，代码改动势必会带来潜在的 Bug，增加开发测试工作量。
- 数据采集服务会导致已有的服务请求延时变高。
- 采集数据性能差，需要较长时间才能采集完一批数据。
- 启停服务时，会导致采集的数据丢失。


这一讲，我就来详细教你如何设计和落地一个数据采集服务，解决上面这些问题。



### 数据采集方式的分类
首先，你需要知道当前数据采集有哪些方式，以便更好地理解异步数据处理方案。


目前，数据采集主要有两种方式，分别是同步采集和异步采集。二者的概念和优缺点如下表所示：


![img](https://static001.geekbang.org/resource/image/d4/b9/d4d4d6547225de5b565f99957106dbb9.jpg?wh=1920x1058)

现代应用对性能的要求越来越高，而异步采集对应用程序的性能影响更小，因此异步采集更受开发者欢迎，得到了大规模的应用。接下来，我要介绍的 IAM Pump Server 服务，采用的就是异步采集的方式。


### 数据采集系统设计
这一讲，我采用**理论 + 实战的方式来展示如何设计一个数据采集服务**，这里先来介绍下关于数据采集的理论知识，后面会有具体的实战案例。


在过往的项目开发中，我发现很多开发人员添加了数据采集功能后，因为同步上报数据、单线程、上报逻辑不对等原因，让整个应用程序的性能受到了严重影响。那么，如何在采集过程中不影响程序的性能？答案就是让数据采集模型化。通过模型化，可以使设计出来的采集系统功能更加通用，能够满足未来的很多同类需求，我们也就不需要重复开发相同的系统了。我今天就来给你详细介绍下，**如何将数据采集功能模型化，以及该模型是如何解决上面说的的各种问题的。**



#### 设计数据采集系统时需要解决的核心问题
采集系统首先需要一个数据源 Input，Input 可以是一个或者多个，Input 中的数据来自于应用程序上报。采集后的数据通常需要经过处理，比如格式化、增删字段、过滤无用的数据等，然后将处理后的数据存储到下游系统（Output）中，如下图所示：


![img](https://static001.geekbang.org/resource/image/a9/75/a91db8c7818af0898a1774073e9bfe75.jpg?wh=1920x1145)


这里，我们需要解决这 3 个核心问题：
- 进行数据采集，就需要在正常流程中多加一个上报数据环节，这势必会影响程序的性能。那么，如何让程序的性能损失最小化？
- 如果 Input 产生数据的速度大于 Output 的消费能力，产生数据堆积怎么办？
- 数据采集后需要存储到下游系统。在存储之前，我们需要对数据进行不同的处理，并可能会存储到不同的下游系统，这种可变的需求如何满足？


对于让程序性能损失最小化这一点，最好的方法是异步上报。如果是异步，我们需要先把数据缓存在内存中，然后再异步上报到目标系统中。当然，为了提高上报的效率，可以采用批量上报的方式。对于数据堆积这个问题，比较好的解决方法是，将采集的数据先上报到一些具有高吞吐量、可以存储大量数据的中间组件，比如 Kafka、Redis 中。这种方式也是业界标准的处理方式。对于采集需求多样化这个问题，我们可以将采集程序做成插件化、可扩展的，满足可变的需求。要解决这 3 个问题，其实就涉及到了数据采集系统中的两个功能点的设计，它们分别是数据上报功能和数据采集功能。接下来我们就来看下，如何设计这两个功能点。



#### 数据上报功能设计
为了提高异步上报的吞吐量，你可以将数据缓存在内存中（Go 中可以使用有缓冲 channel），并使用多个 worker 去消费内存中的数据。使用多个 worker ，可以充分发挥 CPU 的多核能力。另外，上报给下游系统时，你也可以采用批量上报的方式。


#### 数据采集功能设计
现代应用程序越来越讲究插件化、扩展性，在设计采集系统时，也应该考虑到未来的需求。比如，未来你可能需要将数据从上报到 MongoDB 切换到 HBase 中，或者同时将数据上报到 MongoDB 和 HBase 中。因此，上报给下游的程序逻辑要具有插件化的能力，并能通过配置选择需要的插件。


为了提高程序性能，会先把数据缓存在内存中。但是这样有个缺点：在关停程序时，内存中的数据就会丢失。所以，在程序结束之前，我们需要确保内存中的数据能够上报成功，也就是说采集程序需要实现优雅关停功能。优雅关停不仅要确保缓存中的数据被成功上报，还要确保正在处理的数据被成功上报。当然了，既然是数据采集，还要能够配置采集的频率。最后，因为采集程序通常是非 API 类型的，所以还需要对外暴露一个特殊的 API，用来返回采集程序的健康状态。


#### 数据采集应用模型
通过上面的分析和设计，可以绘制出下面这个采集模型：


![img](https://static001.geekbang.org/resource/image/2e/34/2ecccdb3c851577f9cd5a56bb7197c34.jpg?wh=1920x910)

异步上报需要额外的异步逻辑，会增加开发工作量和程序复杂度，所以，对于一些 Input 数据生产速度小于 Output 消费速度，并且 Output 具有高吞吐量、低延时特性的场景，也可以采用同步上报，例如同步上报给 Redis。


### 数据采集系统落地项目：iam-authz-server + iam-pump
上面，我介绍了数据采集系统的架构，但是只有模型和理论，肯定还不足以解决你对数据采集程序的开发需求。所以，接下来我来介绍下如何落地上面的数据采集架构。整个架构包括两个部分，分别由不同的服务实现：


iam-authz-server：实现数据上报功能。iam-pump：实现数据采集功能。



整个采集系统的架构，跟上面描述的数据采集架构完全一致，这里就不重复说明了。



#### iam-authz-server：数据上报
数据上报的最大难点，就是如何减少上报逻辑对应用性能的影响。对此，我们主要的解决思路就是异步上报数据。


接下来我会介绍 iam-authz-server 的数据上报设计。这是一个非常成熟的设计，在我所开发和了解的项目中被大量采用，有些项目可以承载十亿级 / 天的请求量。通过介绍这个设计，我们来看看异步上报的具体方法，以及上报过程中要考虑的因素。iam-authz-server 的数据上报架构如下图所示：


![img](https://static001.geekbang.org/resource/image/4d/3f/4d288a15fa6ebaae5ef25df8af5ac13f.jpg?wh=1920x764)

iam-authz-server 服务中的数据上报功能可以选择性开启，开启代码见 internal/authzserver/server.go ，代码如下：



```go
 if s.analyticsOptions.Enable {                                           
        analyticsStore := storage.RedisCluster{KeyPrefix: RedisKeyPrefix}              
        analyticsIns := analytics.NewAnalytics(s.analyticsOptions, &analyticsStore)    
        analyticsIns.Start()                                                   
        s.gs.AddShutdownCallback(shutdown.ShutdownFunc(func(string) error {    
            analyticsIns.Stop()    
                          
            return nil    
        }))    
    }     
```
上面的代码中，当 s.analyticsOptions.Enable 为 true 时，开启数据上报功能。因为数据上报会影响程序的性能，而且在未来可能会存在禁掉数据上报功能的场景，所以在设计 iam-authz-server 时，就把数据上报功能做成了可配置的，也就是说可以通过配置文件来启用 / 禁用数据上报功能。配置方式也很简单：将 iam-authz-server.yaml 的 analytics.enable 设置为 true，代表开启数据上报功能；设置为 false ，则代表关闭数据上报功能。


这里，我建议你在设计程序时，将未来的可能变量考虑进去，并将这些变量做成可配置的。这样，如果哪天需求变化，我们就能通过修改配置文件，而不是修改代码的方式来满足需求。这种方式可以将应用程序的变动局限在配置文件中，从而大大减小现网服务出现故障的概率，做到只变更配置文件就可以缩短发布变更的周期。

在上面的代码中，通过 NewAnalytics 创建一个数据上报服务，代码如下：


```go
func NewAnalytics(options *AnalyticsOptions, store storage.AnalyticsHandler) *Analytics {                              
    ps := options.PoolSize                                                                                             
    recordsBufferSize := options.RecordsBufferSize                                                                     
    workerBufferSize := recordsBufferSize / uint64(ps)                                                                 
    log.Debug("Analytics pool worker buffer size", log.Uint64("workerBufferSize", workerBufferSize))                   
                                                                                                                       
    recordsChan := make(chan *AnalyticsRecord, recordsBufferSize)                                                      
                                                                                                                       
    return &Analytics{                                                                                                 
        store:                      store,                                                                             
        poolSize:                   ps,                                                                                
        recordsChan:                recordsChan,                                                                       
        workerBufferSize:           workerBufferSize,                                                                  
        recordsBufferFlushInterval: options.FlushInterval,                                                             
    }                                                                                                                  
}      
```

这里的代码根据传入的参数，创建 Analytics 类型的变量并返回，变量中有 5 个字段需要你关注：

- store： storage.AnalyticsHandler 接口类型，提供了 Connect() bool和 AppendToSetPipelined(string, byte)函数，分别用来连接 storage 和上报数据给 storage。iam-authz-server 用了 redis storage。
- recordsChan：授权日志会缓存在 recordsChan 带缓冲 channel 中，其长度可以通过 iam-authz-server.yaml 配置文件中的 analytics.records-buffer-size 配置。
- poolSize：指定开启 worker 的个数，也就是开启多少个 Go 协程来消费 recordsChan 中的消息。
- workerBufferSize：批量投递给下游系统的的消息数。通过批量投递，可以进一步提高消费能力、减少 CPU 消耗。
- recordsBufferFlushInterval：设置最迟多久投递一次，也就是投递数据的超时时间。


analytics.ecords-buffer-size 和 analytics.pool-size 建议根据部署机器的 CPU 和内存来配置。在应用真正上线前，我建议你通过压力和负载测试，来配置一个合适的值。


Analytics 提供了 3 种方法：
- Start()，用来启动数据上报服务。
- Stop()，用来关停数据上报服务。主程序在收到系统的终止命令后，调用 Stop 方法优雅关停数据上报服务，确保缓存中的数据都上报成功。
- RecordHit(record *AnalyticsRecord) error，用来记录 AnalyticsRecord 的数据。


通过 NewXxx （NewAnalytics）返回一个 Xxx （Analytics）类型的结构体，Xxx（Analytics） 类型带有一些方法，如下：


```go
func NewAnalytics(options) *Analytics {
    ...
}

func (r *Analytics) Start() {
    ...
}
func (r *Analytics) Stop() {
    ...
}
func (r *Analytics) RecordHit(record *AnalyticsRecord) error {
    ...
}
```
其实，上述代码段是一种常见的 Go 代码编写方式 / 设计模式。你在以后的开发生涯中，会经常遇到这种设计方式。使用上述代码设计方式有下面两个好处。


- 功能模块化：将数据上报的功能封装成一个服务模块，数据和方法都围绕着 Xxx 结构体来展开。这和 C++、Java、Python 的类有相似的地方，你可以这么理解：Xxx 相当于类，NewXxx 相当于初始化一个类实例，Start、Stop、RecordHit 是这个类提供的方法。功能模块化可以使程序逻辑更加清晰，功能更独立、更好维护，也可以供其他应用使用。
- 方便数据传递：可以将数据存放在 Xxx 结构体字段中，供不同的方法共享使用，如果有并发，数据共享时，注意要给非并发安全的类型加锁，例如 recordsChan。


接下来，我会介绍 iam-authz-server 服务中跟数据上报相关的 3 部分核心代码，分别是启动数据上报服务、异步上报授权日志和优雅关停数据上报。


#### 启动服务：启动数据上报服务
在服务启动时，首先要启动数据上报功能模块。我们通过调用 analyticsIns.Start() 启动数据上报服务。Start 代码如下：


```go
func (r *Analytics) Start() {
    analytics = r
    r.store.Connect()

    // start worker pool
    atomic.SwapUint32(&r.shouldStop, 0)
    for i := 0; i < r.poolSize; i++ {
        r.poolWg.Add(1)
        go r.recordWorker()
    }

    // stop analytics workers
    go r.Stop()
}
```
这里有一点需要你注意，数据上报和数据采集都大量应用了 Go 协程来并发地执行操作，为了防止潜在的并发读写引起的 Bug，建议你的测试程序编译时加上 -race，例如 go build -race cmd/iam-authz-server/authzserver.go。然后，在测试过程中，观察程序日志，看有无并发问题出现。

Start 中会开启 poolSize 个数的 worker 协程，这些协程共同消费 recordsChan 中的消息，消费逻辑见 recordWorker() ，代码如下：


```go
func (r *Analytics) recordWorker() {
  defer r.poolWg.Done()

  // this is buffer to send one pipelined command to redis
  // use r.recordsBufferSize as cap to reduce slice re-allocations
  recordsBuffer := make([][]byte, 0, r.workerBufferSize)

  // read records from channel and process
  lastSentTS := time.Now()
  for {
    readyToSend := false
    select {
    case record, ok := <-r.recordsChan:
      // check if channel was closed and it is time to exit from worker
      if !ok {
        // send what is left in buffer
        r.store.AppendToSetPipelined(analyticsKeyName, recordsBuffer)
        return
      }

      // we have new record - prepare it and add to buffer

      if encoded, err := msgpack.Marshal(record); err != nil {
        log.Errorf("Error encoding analytics data: %s", err.Error())
      } else {
        recordsBuffer = append(recordsBuffer, encoded)
      }

      // identify that buffer is ready to be sent
      readyToSend = uint64(len(recordsBuffer)) == r.workerBufferSize

    case <-time.After(r.recordsBufferFlushInterval):
      // nothing was received for that period of time
      // anyways send whatever we have, don't hold data too long in buffer
      readyToSend = true
    }

    // send data to Redis and reset buffer
    if len(recordsBuffer) > 0 && (readyToSend || time.Since(lastSentTS) >= recordsBufferForcedFlushInterval) {
      r.store.AppendToSetPipelined(analyticsKeyName, recordsBuffer)
      recordsBuffer = recordsBuffer[:0]
      lastSentTS = time.Now()
    }
  }
}
```
recordWorker 函数会将接收到的授权日志保存在 recordsBuffer 切片中，当数组内元素个数为 workerBufferSize ，或者距离上一次投递时间间隔为 recordsBufferFlushInterval 时，就会将 recordsBuffer 数组中的数据上报给目标系统（Input）。


recordWorker() 中有些设计技巧，很值得你参考。

- 使用 msgpack 序列化消息：msgpack 是一个高效的二进制序列化格式。它像 JSON 一样，让你可以在各种语言之间交换数据。但是它比 JSON 更快、更小。
- 支持 Batch Windows：当 worker 的消息数达到指定阈值时，会批量投递消息给 Redis，阈值判断代码为readyToSend = uint64(len(recordsBuffer)) == r.workerBufferSize。
- 超时投递：为了避免因为产生消息太慢，一直达不到 Batch Windows，无法投递消息这种情况，投递逻辑也支持超时投递，通过 case <-time.After(r.recordsBufferFlushInterval)代码段实现。
- 支持优雅关停：当 recordsChan 关闭时，将 recordsBuffer 中的消息批量投递给 Redis，之后退出 worker 协程。


这里有个注意事项：投递完成后，你需要重置 recordsBuffer 和计时器，否则会重复投递数据：


```go
recordsBuffer = recordsBuffer[:0]
lastSentTS = time.Now()
```
这里还设置了一个最大的超时时间 recordsBufferForcedFlushInterval，确保消息最迟被投递的时间间隔。也就是说， iam-authz-server 强制要求最大投递间隔为 recordsBufferForcedFlushInterval 秒，这是为了防止配置文件将 recordsBufferFlushInterval 设得过大。

#### 运行服务：异步上报授权日志
开启了数据上报服务后，当有授权日志产生时，程序就会自动上报数据。接下来，我会详细介绍下如何高效上报数据。

iam-authz-server 会在授权成功时调用 LogGrantedAccessRequest 函数，在授权失败时调用 LogRejectedAccessRequest 函数。并且，在这两个函数中，调用 RecordHit 函数，记录授权日志。iam-authz-server 通过调用 RecordHit(record *AnalyticsRecord) error 函数，异步缓存授权日志。调用 RecordHit 后，会将 AnalyticsRecord 类型的消息存放到 recordsChan 有缓冲 channel 中。

这里要注意：在缓存前，需要判断上报服务是否在优雅关停中，如果在关停中，则丢弃该消息：

```go
if atomic.LoadUint32(&r.shouldStop) > 0 {
    return nil
}
```
通过将授权日志缓写入 recordsChan 有缓冲 channel 中，LogGrantedAccessRequest 和 LogRejectedAccessRequest 函数可以不用等待授权日志上报成功就返回，这样就使得整个授权请求的性能损耗几乎为零。



#### 关停服务：优雅关停数据上报
完成数据上报之后的下一步，就是要优雅地将数据上报关停。为了确保在应用关停时，缓存中的数据和正在投递中的数据都能够投递到 Redis，iam-authz-server 实现了数据上报关停功能，代码如下：


```go
gs.AddShutdownCallback(shutdown.ShutdownFunc(func(string) error {
    analyticsIns.Stop()
    return nil
}))
```
当收到 os.Interrupt 和 syscall.SIGTERM 系统信号后，调用 analyticsIns.Stop() 函数，关停数据上报服务， Stop 函数会停止接收新的授权日志，并等待正在上报的数据上报完成。上面我介绍了数据上报部分的功能设计，接下来，我来介绍下数据采集部分的功能设计。

### iam-pump：数据采集
iam-authz-server 将数据上报到 Redis，iam-pump 消费 Redis 中的数据，并保存在 MongoDB 中做持久化存储。

iam-pump 的设计要点是：插件化、可配置地将 Redis 中的数据处理后存储到下游系统中，并且实现优雅关停功能，这些也是设计数据采集程序的要点和难点所在。下面，我们就来看下 iam-pump 是如何插件化地实现一个数据采集程序的。这个数据采集程序的设计思路，在我开发的大型企业应用中有实际的落地验证，你可以放心使用。


iam-pump 数据采集架构如下图所示：


![img](https://static001.geekbang.org/resource/image/91/ed/913b92d58cfd7cba0dff26612be9e9ed.jpg?wh=1920x1129)

在 iam-pump 服务启动时，要启动数据采集功能，启动代码见 internal/pump/server.go。接下来，我会介绍下 iam-pump 服务中的 5 部分核心代码：


数据采集插件定义。初始化数据采集插件。健康检查。启动 Loop 周期性消费 Redis 数据。优雅关停数据采集服务。


#### 初始化服务：数据采集插件定义
数据采集组件设计的核心是插件化，这里我将需要上报的系统抽象成一个个的 pump，那么如何定义 pump 接口呢？接口定义需要参考实际的采集需求，通常来说，至少需要下面这几个函数。


- New：创建一个 pump。
- Init：初始化一个 pump，例如，可以在 Init 中创建下游系统的网络连接。
- WriteData：往下游系统写入数据。为了提高性能，最好支持批量写入。
- SetFilters：设置是否过滤某条数据，这也是一个非常常见的需求，因为不是所有的数据都是需要的。
- SetTimeout：设置超时时间。我就在开发过程中遇到过一个坑，连接 Kafka 超时，导致整个采集程序超时。所以这里需要有超时处理，通过超时处理，可以保证整个采集框架正常运行。

我之前开发过公有云的网关服务，网关服务需要把网关的请求数据转存到 MongoDB 中。我们的网关服务曾经遇到一个比较大的坑：有些用户会通过网关上传非常大的文件（百 M 级别），这些数据转存到 MongoDB 中，快速消耗了 MongoDB 的存储空间（500G 存储空间）。为了避免这个问题，在转存数据时，需要过滤掉一些比较详细的数据，所以 iam-pump 添加了 SetOmitDetailedRecording 来过滤掉详细的数据。


所以，最后 iam-pump 的插件接口定义为 internal/pump/pumps/pump.go ：


```go
type Pump interface {
  GetName() string
  New() Pump
  Init(interface{}) error
  WriteData(context.Context, []interface{}) error
  SetFilters(analytics.AnalyticsFilters)
  GetFilters() analytics.AnalyticsFilters
  SetTimeout(timeout int)
  GetTimeout() int
  SetOmitDetailedRecording(bool)
  GetOmitDetailedRecording() bool
}
```
你在实际开发中，如果有更多的需求，可以在 Pump interface 定义中继续添加需要的处理函数。



#### 初始化服务：初始化数据采集插件
定义好插件之后，需要初始化插件。在 initialize 函数中初始化 pumps：


```go
func (s *pumpServer) initialize() {
  pmps = make([]pumps.Pump, len(s.pumps))
  i := 0
  for key, pmp := range s.pumps {
    pumpTypeName := pmp.Type
    if pumpTypeName == "" {
      pumpTypeName = key
    }

    pmpType, err := pumps.GetPumpByName(pumpTypeName)
    if err != nil {
      log.Errorf("Pump load error (skipping): %s", err.Error())
    } else {
      pmpIns := pmpType.New()
      initErr := pmpIns.Init(pmp.Meta)
      if initErr != nil {
        log.Errorf("Pump init error (skipping): %s", initErr.Error())
      } else {
        log.Infof("Init Pump: %s", pmpIns.GetName())
        pmpIns.SetFilters(pmp.Filters)
        pmpIns.SetTimeout(pmp.Timeout)
        pmpIns.SetOmitDetailedRecording(pmp.OmitDetailedRecording)
        pmps[i] = pmpIns
      }
    }
    i++
  }
}
```

initialize 会创建、初始化，并调用 SetFilters、SetTimeout、SetOmitDetailedRecording 来设置这些 pump。Filters、Timeout、OmitDetailedRecording 等信息在 pump 的配置文件中指定。


这里有个技巧你也可以注意下：pump 配置文件支持通用的配置，也支持自定义的配置，配置结构为 PumpConfig ：

```go
type PumpConfig struct {
  Type                  string
  Filters               analytics.AnalyticsFilters
  Timeout               int
  OmitDetailedRecording bool
  Meta                  map[string]interface{}
}
```
pump 自定义的配置可以存放在 map 类型的变量 Meta 中。通用配置可以使配置共享，减少开发和维护工作量，自定义配置可以适配不同 pump 的差异化配置。


#### 初始化服务：健康检查
因为 iam-pump 是一个非 API 服务，为了监控其运行状态，这里也设置了一个健康检查接口。iam-pump 组件通过调用 server.ServeHealthCheck 函数启动一个 HTTP 服务，ServeHealthCheck 函数代码如下：


```go
func ServeHealthCheck(healthPath string, healthAddress string) {
  http.HandleFunc("/"+healthPath, func(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-type", "application/json")
    w.WriteHeader(http.StatusOK)
    _, _ = w.Write([]byte(`{"status": "ok"}`))
  })

  if err := http.ListenAndServe(healthAddress, nil); err != nil {
    log.Fatalf("Error serving health check endpoint: %s", err.Error())
  }
}
```

该函数启动了一个 HTTP 服务，服务监听地址通过 health-check-address 配置，健康检查路径通过 health-check-path 配置。如果请求 http:///返回{"status": "ok"}，说明 iam-pump 可以正常工作。

这里的健康检查只是简单返回了一个字符串，实际开发中，可以封装更复杂的逻辑。比如，检查进程是否可以成功 ping 通数据库，进程内的工作进程是否处于 worker 状态等。

iam-pump 默认的健康检查请求地址为http://127.0.0.1:7070/healthz 。



#### 运行服务：启动 Loop 周期性消费 Redis 数据
初始化 pumps 之后，就可以通过 Run 函数启动消费逻辑了。在 Run 函数中，会定期（通过配置 purge-delay 设置轮训时间）从 Redis 中获取所有数据，经过 msgpack.Unmarshal 解压后，传给 writeToPumps 处理：



```go
func (s preparedPumpServer) Run(stopCh <-chan struct{}) error {
  ticker := time.NewTicker(time.Duration(s.secInterval) * time.Second)
  defer ticker.Stop()

  for {
    select {
    case <-ticker.C:
      analyticsValues := s.analyticsStore.GetAndDeleteSet(storage.AnalyticsKeyName)
      if len(analyticsValues) > 0 {
        // Convert to something clean
        keys := make([]interface{}, len(analyticsValues))

        for i, v := range analyticsValues {
          decoded := analytics.AnalyticsRecord{}
          err := msgpack.Unmarshal([]byte(v.(string)), &decoded)
          log.Debugf("Decoded Record: %v", decoded)
          if err != nil {
            log.Errorf("Couldn't unmarshal analytics data: %s", err.Error())
          } else {
            if s.omitDetails {
              decoded.Policies = ""
              decoded.Deciders = ""
            }
            keys[i] = interface{}(decoded)
          }
        }

        // Send to pumps
        writeToPumps(keys, s.secInterval)
      }
    // exit consumption cycle when receive SIGINT and SIGTERM signal
    case <-stopCh:
      log.Info("stop purge loop")

      return nil
    }
  }
}
```
writeToPumps 函数通过调用 execPumpWriting 函数，异步调用 pump 的 WriteData 函数写入数据。execPumpWriting 函数中有一些设计技巧，你可以注意下这两个：
- 将一些通用的处理，例如 Filters、Timeout、OmitDetailedRecording 放在 pump 之外处理，这样可以减少 pump 中代码的重复性。
- 优雅关停。通过如下代码实现优雅关停功能：


```go
select {
    case <-stopCh:
        log.Info("stop purge loop")
        return
    default:
}
```
上面的代码需要放在 writeToPumps 之后，这样可以确保所有数据都成功写入 pumps 之后，再停止采集逻辑。


#### 关停服务：优雅关停数据采集服务
在关停服务时，为了确保正在处理的数据被成功存储，还需要提供优雅关停功能。iam-pump 通过 channel 传递 SIGINT 和 SIGTERM 信号，当消费逻辑收到这两个信号后，会退出消费循环，见 Run 函数。代码如下：


```go
func (s preparedPumpServer) Run(stopCh <-chan struct{}) error {    
    ticker := time.NewTicker(time.Duration(s.secInterval) * time.Second)    
    defer ticker.Stop()                                                     
    
    for {    
        select {    
        case <-ticker.C:    
         // 消费逻辑
         ...
        // exit consumption cycle when receive SIGINT and SIGTERM signal
        case <-stopCh:    
            log.Info("stop purge loop")    
    
            return nil
        }
    }
}
```
### 总结
这一讲，我主要介绍了如何将数据采集需求转化成一个数据采集模型，并从这个模型出发，设计出一个可扩展、高性能的数据采集服务，并通过 iam-pump 组件来落地该采集模型。最后，我还想给你一个建议：在开发中，你也可以将一些功能抽象成一些通用的模型，并为该模型实现基本框架（引擎），然后将一些需要定制化的部分插件化。通过这种方式，可以设计出一个高扩展的服务，使得服务不仅能够满足现在的需求，还能够满足未来的需求。

## 33 | SDK 设计（上）：如何设计出一个优秀的 Go SDK？

后端服务通过 API 接口对外提供应用的功能，但是用户直接调用 API 接口，需要编写 API 接口调用的逻辑，并且需要构造入参和解析返回的数据包，使用起来效率低，而且有一定的开发工作量。


在实际的项目开发中，通常会提供对开发者更友好的 SDK 包，供客户端调用。很多大型服务在发布时都会伴随着 SDK 的发布，例如腾讯云很多产品都提供了 SDK：


![img](https://static001.geekbang.org/resource/image/e1/fa/e1bb8eb03c2f26f546710e95751c17fa.png?wh=1920x747)

既然 SDK 如此重要，那么如何设计一个优秀的 Go SDK 呢？这一讲我就来详细介绍一下。


### 什么是 SDK？首先，我们来看下什么是 SDK。

对于 SDK（Software Development Kit，软件开发工具包），不同场景下有不同的解释。但是对于一个 Go 后端服务来说，SDK 通常是指**封装了 Go 后端服务 API 接口的软件包**，里面通常包含了跟软件相关的库、文档、使用示例、封装好的 API 接口和工具。


调用 SDK 跟调用本地函数没有太大的区别，所以可以极大地提升开发者的开发效率和体验。SDK 可以由服务提供者提供，也可以由其他组织或个人提供。为了鼓励开发者使用其系统或语言，SDK 通常都是免费提供的。


通常，服务提供者会提供不同语言的 SDK，比如针对 Python 开发者会提供 Python 版的 SDK，针对 Go 开发者会提供 Go 版的 SDK。一些比较专业的团队还会有 SDK 自动生成工具，可以根据 API 接口定义，自动生成不同语言的 SDK。例如，Protocol Buffers 的编译工具 protoc，就可以基于 Protobuf 文件生成 C++、Python、Java、JavaScript、PHP 等语言版本的 SDK。阿里云、腾讯云这些一线大厂，也可以基于 API 定义，生成不同编程语言的 SDK。



### SDK 设计方法
那么，我们如何才能设计一个好的 SDK 呢？对于 SDK，不同团队会有不同的设计方式，我调研了一些优秀 SDK 的实现，发现这些 SDK 有一些共同点。根据我的调研结果，结合我在实际开发中的经验，我总结出了一套 SDK 设计方法，接下来就分享给你。


### 如何给 SDK 命名？
在讲设计方法之前，我先来介绍两个重要的知识点：SDK 的命名方式和 SDK 的目录结构。SDK 的名字目前没有统一的规范，但比较常见的命名方式是 xxx-sdk-go / xxx-sdk-python / xxx-sdk-java 。其中， xxx 可以是项目名或者组织名，例如腾讯云在 GitHub 上的组织名为 tencentcloud，那它的 SDK 命名如下图所示：


![img](https://static001.geekbang.org/resource/image/e2/1e/e269d5d0e19a73d45ccdf5f5561c611e.png?wh=1210x863)

### SDK 的目录结构
不同项目 SDK 的目录结构也不相同，但一般需要包含下面这些文件或目录。目录名可能会有所不同，但目录功能是类似的。


- README.md：SDK 的帮助文档，里面包含了安装、配置和使用 SDK 的方法。
- examples/sample/：SDK 的使用示例。
- sdk/：SDK 共享的包，里面封装了最基础的通信功能。如果是 HTTP 服务，基本都是基于 net/http 包进行封装。
- api：如果 xxx-sdk-go 只是为某一个服务提供 SDK，就可以把该服务的所有 API 接口封装代码存放在 api 目录下。
- services/{iam, tms} ：如果 xxx-sdk-go 中， xxx 是一个组织，那么这个 SDK 很可能会集成该组织中很多服务的 API，就可以把某类服务的 API 接口封装代码存放在 services/<服务名>下，例如 AWS 的[Go SDK](https://github.com/aws/aws-sdk-go/tree/main/service)。

一个典型的目录结构如下：


```
├── examples            # 示例代码存放目录
│   └── authz.go
├── README.md           # SDK使用文档
├── sdk                 # 公共包，封装了SDK配置、API请求、认证等代码
│   ├── client.go
│   ├── config.go
│   ├── credential.go
│   └── ...
└── services            # API封装
    ├── common
    │   └── model
    ├── iam             # iam服务的API接口
    │   ├── authz.go
    │   ├── client.go
    │   └── ...
    └── tms             # tms服务的API接口
```

### SDK 设计方法
SDK 的设计方法如下图所示：


![img](https://static001.geekbang.org/resource/image/9f/ca/9fb7aa8d3da4210223e9b0c87943e8ca.jpg?wh=1920x841)

我们可以通过 Config 配置创建客户端 Client，例如 func NewClient(config sdk.Config) (Client, error)，配置中可以指定下面的信息。
- 服务的后端地址：服务的后端地址可以通过配置文件来配置，也可以直接固化在 SDK 中，推荐后端服务地址可通过配置文件配置。
- 认证信息：最常用的认证方式是通过密钥认证，也有一些是通过用户名和密码认证。
- 其他配置：例如超时时间、重试次数、缓存时间等。


创建的 Client 是一个结构体或者 Go interface。这里我建议你使用 interface 类型，这样可以将定义和具体实现解耦。Client 具有一些方法，例如 CreateUser、DeleteUser 等，每一个方法对应一个 API 接口，下面是一个 Client 定义：

```go
type Client struct {
    client *sdk.Request
}

func (c *Client) CreateUser(req *CreateUserRequest) (*CreateUserResponse, error) {
    // normal code
    resp := &CreateUserResponse{}
    err := c.client.Send(req, resp)
    return resp, err
}

```
调用 client.CreateUser(req) 会执行 HTTP 请求，在 req 中可以指定 HTTP 请求的方法 Method、路径 Path 和请求 Body。 CreateUser 函数中，会调用 c.client.Send(req) 执行具体的 HTTP 请求。


c.client 是 *Request 类型的变量， *Request 类型的变量具有一些方法，可以根据传入的请求参数 req 和 config 配置构造出请求路径、认证头和请求 Body，并调用 net/http 包完成最终的 HTTP 请求，最后将返回结果 Unmarshal 到传入的 resp 结构体中。


根据我的调研，目前有两种 SDK 设计方式可供参考，一种是各大公有云厂商采用的 SDK 设计方式，一种是 Kubernetes client-go 的设计方式。IAM 项目分别实现了这两种 SDK 设计方式，但我还是更倾向于对外提供 client-go 方式的 SDK，我会在下一讲详细介绍它。这两种设计方式的设计思路跟上面介绍的是一致的。


### 公有云厂商采用的 SDK 设计方式
这里，我先来简单介绍下公有云厂商采用的 SDK 设计模式。SDK 架构如下图所示：


![img](https://static001.geekbang.org/resource/image/82/ce/82ebe90b0490b9a2a76e2f302dd896ce.jpg?wh=1920x866)

SDK 框架分为两层，分别是 API 层和基础层。API 层主要用来构建客户端实例，并调用客户端实例提供的方法来完成 API 请求，每一个方法对应一个 API 接口。API 层最终会调用基础层提供的能力，来完成 REST API 请求。基础层通过依次执行构建请求参数（Builder）、签发并添加认证头（Signer）、执行 HTTP 请求（Request）三大步骤，来完成具体的 REST API 请求。


为了让你更好地理解公有云 SDK 的设计方式，接下来我会结合一些真实的代码，给你讲解 API 层和基础层的具体设计，SDK 代码见medu-sdk-go。



### API 层：创建客户端实例
客户端在使用服务 A 的 SDK 时，首先需要根据 Config 配置创建一个服务 A 的客户端 Client，Client 实际上是一个 struct，定义如下：


```go
type Client struct {
    sdk.Client
}
```
在创建客户端时，需要传入认证（例如密钥、用户名 / 密码）、后端服务地址等配置信息。例如，可以通过NewClientWithSecret方法来构建一个带密钥对的客户端：


```go
func NewClientWithSecret(secretID, secretKey string) (client *Client, err error) {
    client = &Client{}
    config := sdk.NewConfig().WithEndpoint(defaultEndpoint)
    client.Init(serviceName).WithSecret(secretID, secretKey).WithConfig(config)
    return
}
```
这里要注意，上面创建客户端时，传入的密钥对最终会在基础层中被使用，用来签发 JWT Token。


Client 有多个方法（Sender），例如 Authz 等，每个方法代表一个 API 接口。Sender 方法会接收 AuthzRequest 等结构体类型的指针作为输入参数。我们可以调用 client.Authz(req) 来执行 REST API 调用。可以在 client.Authz 方法中添加一些业务逻辑处理。client.Authz 代码如下：


```go
type AuthzRequest struct {
    *request.BaseRequest
    Resource *string `json:"resource"`
    Action *string `json:"action"`
    Subject *string `json:"subject"`
    Context *ladon.Context
}

func (c *Client) Authz(req *AuthzRequest) (resp *AuthzResponse, err error) {
    if req == nil {
        req = NewAuthzRequest()
    }

    resp = NewAuthzResponse()
    err = c.Send(req, resp)
    return
}
```
请求结构体中的字段都是指针类型的，使用指针的好处是可以判断入参是否有被指定，如果req.Subject == nil 就说明传参中没有 Subject 参数，如果req.Subject != nil就说明参数中有传 Subject 参数。根据某个参数是否被传入，执行不同的业务逻辑，这在 Go API 接口开发中非常常见。


另外，因为 Client 通过匿名的方式继承了基础层中的Client：


```go
type Client struct {
  sdk.Client
}
```
所以，API 层创建的 Client 最终可以直接调用基础层中的 Client 提供的Send(req, resp) 方法，来执行 RESTful API 调用，并将结果保存在 resp 中。为了方便和 API 层的 Client 进行区分，我下面统一将基础层中的 Client 称为 **sdk.Client**。最后，一个完整的客户端调用示例代码如下：


```go
package main

import (
  "fmt"

  "github.com/ory/ladon"

  "github.com/marmotedu/medu-sdk-go/sdk"
  iam "github.com/marmotedu/medu-sdk-go/services/iam/authz"
)

func main() {
  client, _ := iam.NewClientWithSecret("XhbY3aCrfjdYcP1OFJRu9xcno8JzSbUIvGE2", "bfJRvlFwsoW9L30DlG87BBW0arJamSeK")

  req := iam.NewAuthzRequest()
  req.Resource = sdk.String("resources:articles:ladon-introduction")
  req.Action = sdk.String("delete")
  req.Subject = sdk.String("users:peter")
  ctx := ladon.Context(map[string]interface{}{"remoteIPAddress": "192.168.0.5"})
  req.Context = &ctx

  resp, err := client.Authz(req)
  if err != nil {
    fmt.Println("err1", err)
    return
  }
  fmt.Printf("get response body: `%s`\n", resp.String())
  fmt.Printf("allowed: %v\n", resp.Allowed)
}
```

### 基础层：构建并执行 HTTP 请求
上面我们创建了客户端实例，并调用了它的 Send 方法来完成最终的 HTTP 请求。这里，我们来看下 Send 方法具体是如何构建 HTTP 请求的。


sdk.Client 通过 Send 方法，完成最终的 API 调用，代码如下：


```go
func (c *Client) Send(req request.Request, resp response.Response) error {
  method := req.GetMethod()
  builder := GetParameterBuilder(method, c.Logger)
  jsonReq, _ := json.Marshal(req)
  encodedUrl, err := builder.BuildURL(req.GetURL(), jsonReq)
  if err != nil {
    return err
  }

  endPoint := c.Config.Endpoint
  if endPoint == "" {
    endPoint = fmt.Sprintf("%s/%s", defaultEndpoint, c.ServiceName)
  }
  reqUrl := fmt.Sprintf("%s://%s/%s%s", c.Config.Scheme, endPoint, req.GetVersion(), encodedUrl)

  body, err := builder.BuildBody(jsonReq)
  if err != nil {
    return err
  }

  sign := func(r *http.Request) error {
    signer := NewSigner(c.signMethod, c.Credential, c.Logger)
    _ = signer.Sign(c.ServiceName, r, strings.NewReader(body))
    return err
  }

  rawResponse, err := c.doSend(method, reqUrl, body, req.GetHeaders(), sign)
  if err != nil {
    return err
  }

  return response.ParseFromHttpResponse(rawResponse, resp)
}
```

上面的代码大体上可以分为四个步骤。


#### 第一步，Builder：构建请求参数。
根据传入的 AuthzRequest 和客户端配置 Config，构造 HTTP 请求参数，包括请求路径和请求 Body。接下来，我们来看下如何构造 HTTP 请求参数。



HTTP 请求路径构建
在创建客户端时，我们通过NewAuthzRequest函数创建了 /v1/authz REST API 接口请求结构体 AuthzRequest，代码如下：

```go
func NewAuthzRequest() (req *AuthzRequest) {    
    req = &AuthzRequest{    
        BaseRequest: &request.BaseRequest{    
            URL:     "/authz",    
            Method:  "POST",    
            Header:  nil,    
            Version: "v1",    
        },    
    }    
    return                                
}
```
可以看到，我们创建的 req 中包含了 API 版本（Version）、API 路径（URL）和请求方法（Method）。这样，我们就可以在 Send 方法中，构建出请求路径：


```go
endPoint := c.Config.Endpoint                                                 
if endPoint == "" {                                                          
    endPoint = fmt.Sprintf("%s/%s", defaultEndpoint, c.ServiceName)           
}                                                                  
reqUrl := fmt.Sprintf("%s://%s/%s%s", c.Config.Scheme, endPoint, req.GetVersion(), encodedUrl) 

```

上述代码中，c.Config.Scheme=http/https、endPoint=iam.api.marmotedu.com:8080、req.GetVersion()=v1 和 encodedUrl，我们可以认为它们等于 /authz。所以，最终构建出的请求路径为http://iam.api.marmotedu.com:8080/v1/authz 。



HTTP 请求 Body 构建
在BuildBody方法中构建请求 Body。BuildBody 会将 req Marshal 成 JSON 格式的 string。HTTP 请求会以该字符串作为 Body 参数。


#### 第二步，Signer：签发并添加认证头。
访问 IAM 的 API 接口需要进行认证，所以在发送 HTTP 请求之前，还需要给 HTTP 请求添加认证 Header。


medu-sdk-go 代码提供了 JWT 和 HMAC 两种认证方式，最终采用了 JWT 认证方式。JWT 认证签发方法为[Sign](https://github.com/marmotedu/medu-sdk-go/blob/v1.0.0/sdk/signer.go#L108-L113)，代码如下：


```go
func (v1 SignatureV1) Sign(serviceName string, r *http.Request, body io.ReadSeeker) http.Header {
  tokenString := auth.Sign(v1.Credentials.SecretID, v1.Credentials.SecretKey, "medu-sdk-go", serviceName+".marmotedu.com")
  r.Header.Set("Authorization", fmt.Sprintf("Bearer %s", tokenString))
  return r.Header

}
```
auth.Sign 方法根据 SecretID 和 SecretKey 签发 JWT Token。接下来，我们就可以调用doSend方法来执行 HTTP 请求了。调用代码如下：


```go
rawResponse, err := c.doSend(method, reqUrl, body, req.GetHeaders(), sign)
if err != nil {                                                               
    return err     
} 
```
可以看到，我们传入了 HTTP 请求方法 method 、HTTP 请求 URL reqUrl 、HTTP 请求 Body body，以及用来签发 JWT Token 的 sign 方法。我们在调用 NewAuthzRequest 创建 req 时，指定了 HTTP Method，所以这里的 method := req.GetMethod() 、reqUrl 和请求 Body 都是通过 Builder 来构建的。


#### 第三步，Request：执行 HTTP请求。
调用doSend方法执行 HTTP 请求，doSend 通过调用 net/http 包提供的 http.NewRequest 方法来发送 HTTP 请求，执行完 HTTP 请求后，会返回 *http.Response 类型的 Response。代码如下：


```go
func (c *Client) doSend(method, url, data string, header map[string]string, sign SignFunc) (*http.Response, error) {
    client := &http.Client{Timeout: c.Config.Timeout}

    req, err := http.NewRequest(method, url, strings.NewReader(data))
    if err != nil {
        c.Logger.Errorf("%s", err.Error())
        return nil, err
    }

    c.setHeader(req, header)

    err = sign(req)
    if err != nil {
        return nil, err
    }

    return client.Do(req)
}

```
#### 第四步，处理 HTTP请求返回结果。
调用 doSend 方法返回 *http.Response 类型的 Response 后，Send 方法会调用ParseFromHttpResponse函数来处理 HTTP Response，ParseFromHttpResponse 函数代码如下：


```go
func ParseFromHttpResponse(rawResponse *http.Response, response Response) error {
  defer rawResponse.Body.Close()
  body, err := ioutil.ReadAll(rawResponse.Body)
  if err != nil {
    return err
  }
  if rawResponse.StatusCode != 200 {
    return fmt.Errorf("request fail with status: %s, with body: %s", rawResponse.Status, body)
  }

  if err := response.ParseErrorFromHTTPResponse(body); err != nil {
    return err
  }

  return json.Unmarshal(body, &response)
}
```
可以看到，在 ParseFromHttpResponse 函数中，会先判断 HTTP Response 中的 StatusCode 是否为 200，如果不是 200，则会报错。如果是 200，会调用传入的 resp 变量提供的ParseErrorFromHTTPResponse方法，来将 HTTP Response 的 Body Unmarshal 到 resp 变量中。


通过以上四步，SDK 调用方调用了 API，并获得了 API 的返回结果 resp 。


下面这些公有云厂商的 SDK 采用了此设计模式：腾讯云 SDK：tencentcloud-sdk-go。AWS SDK：aws-sdk-go。阿里云 SDK：alibaba-cloud-sdk-go。京东云 SDK：jdcloud-sdk-go。Ucloud SDK：ucloud-sdk-go。

IAM 公有云方式的 SDK 实现为 medu-sdk-go。

此外，IAM 还设计并实现了 Kubernetes client-go 方式的 Go SDK：marmotedu-sdk-go，marmotedu-sdk-go 也是 IAM Go SDK 所采用的 SDK。下一讲中，我会具体介绍 marmotedu-sdk-go 的设计和实现。


### 总结
这一讲，我主要介绍了如何设计一个优秀的 Go SDK。通过提供 SDK，可以提高 API 调用效率，减少 API 调用难度，所以大型应用通常都会提供 SDK。不同团队有不同的 SDK 设计方法，但目前比较好的实现是公有云厂商采用的 SDK 设计方式。公有云厂商的 SDK 设计方式中，SDK 按调用顺序从上到下可以分为 3 个模块，如下图所示：


![img](https://static001.geekbang.org/resource/image/b9/a9/b9bd3020ae56f6bb49bc3a38bcaf64a9.jpg?wh=1920x878)
Client 构造 SDK 客户端，在构造客户端时，会创建请求参数 req ， req 中会指定 API 版本、HTTP 请求方法、API 请求路径等信息。Client 会请求 Builder 和 Signer 来构建 HTTP 请求的各项参数：HTTP 请求方法、HTTP 请求路径、HTTP 认证头、HTTP 请求 Body。Builder 和 Signer 是根据 req 配置来构造这些 HTTP 请求参数的。构造完成之后，会请求 Request 模块，Request 模块通过调用 net/http 包，来执行 HTTP 请求，并返回请求结果。

## 34 | SDK 设计（下）：IAM项目Go SDK设计和实现

上一讲，我介绍了公有云厂商普遍采用的 SDK 设计方式。其实，还有一些比较优秀的 SDK 设计方式，比如 Kubernetes 的 client-go SDK 设计方式。IAM 项目参考 client-go，也实现了 client-go 风格的 SDK：marmotedu-sdk-go。

和 33 讲 介绍的 SDK 设计方式相比，client-go 风格的 SDK 具有以下优点：大量使用了 Go interface 特性，将接口的定义和实现解耦，可以支持多种实现方式。接口调用层级跟资源的层级相匹配，调用方式更加友好。多版本共存。

所以，我更推荐你使用 marmotedu-sdk-go。接下来，我们就来看下 marmotedu-sdk-go 是如何设计和实现的。


### marmotedu-sdk-go 设计
和 medu-sdk-go 相比，marmotedu-sdk-go 的设计和实现要复杂一些，但功能更强大，使用体验也更好。

这里，我们先来看一个使用 SDK 调用 iam-authz-server /v1/authz 接口的示例，代码保存在 marmotedu-sdk-go/examples/authz_clientset/main.go文件中：


```go
package main

import (
  "context"
  "flag"
  "fmt"
  "path/filepath"

  "github.com/ory/ladon"

  metav1 "github.com/marmotedu/component-base/pkg/meta/v1"
  "github.com/marmotedu/component-base/pkg/util/homedir"

  "github.com/marmotedu/marmotedu-sdk-go/marmotedu"
  "github.com/marmotedu/marmotedu-sdk-go/tools/clientcmd"
)

func main() {
  var iamconfig *string
  if home := homedir.HomeDir(); home != "" {
    iamconfig = flag.String(
      "iamconfig",
      filepath.Join(home, ".iam", "config"),
      "(optional) absolute path to the iamconfig file",
    )
  } else {
    iamconfig = flag.String("iamconfig", "", "absolute path to the iamconfig file")
  }
  flag.Parse()

  // use the current context in iamconfig
  config, err := clientcmd.BuildConfigFromFlags("", *iamconfig)
  if err != nil {
    panic(err.Error())
  }

  // create the clientset
  clientset, err := marmotedu.NewForConfig(config)
  if err != nil {
    panic(err.Error())
  }

  request := &ladon.Request{
    Resource: "resources:articles:ladon-introduction",
    Action:   "delete",
    Subject:  "users:peter",
    Context: ladon.Context{
      "remoteIP": "192.168.0.5",
    },
  }

  // Authorize the request
  fmt.Println("Authorize request...")
  ret, err := clientset.Iam().AuthzV1().Authz().Authorize(context.TODO(), request, metav1.AuthorizeOptions{})
  if err != nil {
    panic(err.Error())
  }

  fmt.Printf("Authorize response: %s.\n", ret.ToString())
}
```

在上面的代码示例中，包含了下面的操作。首先，调用 BuildConfigFromFlags 函数，创建出 SDK 的配置实例 config；接着，调用 marmotedu.NewForConfig(config) 创建了 IAM 项目的客户端 clientset ;最后，调用以下代码请求 /v1/authz 接口执行资源授权请求：


```go
ret, err := clientset.Iam().AuthzV1().Authz().Authorize(context.TODO(), request, metav1.AuthorizeOptions{})    
if err != nil {           
    panic(err.Error())    
}    

fmt.Printf("Authorize response: %s.\n", ret.ToString())
```
调用格式为项目客户端.应用客户端.服务客户端.资源名.接口 。所以，上面的代码通过创建项目级别的客户端、应用级别的客户端和服务级别的客户端，来调用资源的 API 接口。接下来，我们来看下如何创建这些客户端。


### marmotedu-sdk-go 客户端设计
在讲客户端创建之前，我们先来看下客户端的设计思路。

Go 项目的组织方式是有层级的：Project -> Application -> Service。marmotedu-sdk-go 很好地体现了这种层级关系，使得 SDK 的调用更加易懂、易用。marmotedu-sdk-go 的层级关系如下图所示：


![img](https://static001.geekbang.org/resource/image/3a/21/3a4721afa7fe365c0954019087d82021.jpg?wh=2248x1043)

marmotedu-sdk-go 定义了 3 类接口，分别代表了项目、应用和服务级别的 API 接口：


```go
// 项目级别的接口
type Interface interface {
    Iam() iam.IamInterface
    Tms() tms.TmsInterface
}

// 应用级别的接口
type IamInterface interface {
    APIV1() apiv1.APIV1Interface
    AuthzV1() authzv1.AuthzV1Interface
}

// 服务级别的接口
type APIV1Interface interface {
    RESTClient() rest.Interface
    SecretsGetter
    UsersGetter
    PoliciesGetter
}

// 资源级别的客户端
type SecretsGetter interface {
    Secrets() SecretInterface
}

// 资源的接口定义
type SecretInterface interface {
    Create(ctx context.Context, secret *v1.Secret, opts metav1.CreateOptions) (*v1.Secret, error)
    Update(ctx context.Context, secret *v1.Secret, opts metav1.UpdateOptions) (*v1.Secret, error)
    Delete(ctx context.Context, name string, opts metav1.DeleteOptions) error
    DeleteCollection(ctx context.Context, opts metav1.DeleteOptions, listOpts metav1.ListOptions) error
    Get(ctx context.Context, name string, opts metav1.GetOptions) (*v1.Secret, error)
    List(ctx context.Context, opts metav1.ListOptions) (*v1.SecretList, error)
    SecretExpansion
}
```
Interface 代表了项目级别的接口，里面包含了 Iam 和 Tms 两个应用； IamInterface 代表了应用级别的接口，里面包含了 api（iam-apiserver）和 authz（iam-authz-server）两个服务级别的接口。api 和 authz 服务中，又包含了各自服务中 REST 资源的 CURD 接口。


marmotedu-sdk-go 通过 XxxV1 这种命名方式来支持不同版本的 API 接口，好处是可以在程序中同时调用同一个 API 接口的不同版本，例如：clientset.Iam().AuthzV1().Authz().Authorize() 、clientset.Iam().AuthzV2().Authz().Authorize() 分别调用了 /v1/authz 和 /v2/authz 两个版本的 API 接口。

上述关系也可以从目录结构中反映出来，marmotedu-sdk-go 目录设计如下（只列出了一些重要的文件）：


```

├── examples                        # 存放SDK的使用示例
├── Makefile                        # 管理SDK源码，静态代码检查、代码格式化、测试、添加版权信息等
├── marmotedu
│   ├── clientset.go                # clientset实现，clientset中包含多个应用，多个服务的API接口
│   ├── fake                        # clientset的fake实现，主要用于单元测试
│   └── service                     # 按应用进行分类，存放应用中各服务API接口的具体实现
│       ├── iam                     # iam应用的API接口实现，包含多个服务
│       │   ├── apiserver           # iam应用中，apiserver服务的API接口，包含多个版本
│       │   │   └── v1              # apiserver v1版本API接口
│       │   ├── authz               # iam应用中，authz服务的API接口
│       │   │   └── v1              # authz服务v1版本接口
│       │   └── iam_client.go       # iam应用的客户端，包含了apiserver和authz 2个服务的客户端
│       └── tms                     # tms应用的API接口实现
├── pkg                             # 存放一些共享包，可对外暴露
├── rest                            # HTTP请求的底层实现
├── third_party                     # 存放修改过的第三方包，例如：gorequest
└── tools
    └── clientcmd                   # 一些函数用来帮助创建rest.Config配置
```
每种类型的客户端，都可以通过以下相似的方式来创建：


```go
config, err := clientcmd.BuildConfigFromFlags("", "/root/.iam/config")
clientset, err := xxxx.NewForConfig(config)
```
/root/.iam/config 为配置文件，里面包含了服务的地址和认证信息。BuildConfigFromFlags 函数加载配置文件，创建并返回 rest.Config 类型的配置变量，并通过 xxxx.NewForConfig 函数创建需要的客户端。xxxx 是所在层级的 client 包，例如 iam、tms。



marmotedu-sdk-go 客户端定义了 3 类接口，这可以带来两个好处。


第一，API 接口调用格式规范，层次清晰，可以使 API 接口调用更加清晰易记。第二，可以根据需要，自行选择客户端类型，调用灵活。举个例子，在 A 服务中需要同时用到 iam-apiserver 和 iam-authz-server 提供的接口，就可以创建应用级别的客户端 IamClient，然后通过 iamclient.APIV1() 和 iamclient.AuthzV1() ，来切换调用不同服务的 API 接口。


接下来，我们来看看如何创建三个不同级别的客户端。


#### 项目级别客户端创建
Interface 对应的客户端实现为Clientset，所在的包为 marmotedu-sdk-go/marmotedu，Clientset 客户端的创建方式为：


```go
config, err := clientcmd.BuildConfigFromFlags("", "/root/.iam/config")
clientset, err := marmotedu.NewForConfig(config)
```
调用方式为 clientset.应用.服务.资源名.接口 ，例如：


```go
rsp, err := clientset.Iam().AuthzV1().Authz().Authorize()

```
参考示例为 marmotedu-sdk-go/examples/authz_clientset/main.go。


#### 应用级别客户端创建
IamInterface 对应的客户端实现为IamClient，所在的包为 marmotedu-sdk-go/marmotedu/service/iam，IamClient 客户端的创建方式为：


```go
config, err := clientcmd.BuildConfigFromFlags("", "/root/.iam/config")
iamclient,, err := iam.NewForConfig(config)
```
调用方式为 iamclient.服务.资源名.接口 ，例如：

```go
rsp, err := iamclient.AuthzV1().Authz().Authorize()
```

参考示例为 marmotedu-sdk-go/examples/authz_iam/main.go。


#### 服务级别客户端创建
AuthzV1Interface 对应的客户端实现为AuthzV1Client，所在的包为 marmotedu-sdk-go/marmotedu/service/iam/authz/v1，AuthzV1Client 客户端的创建方式为：


```go
config, err := clientcmd.BuildConfigFromFlags("", "/root/.iam/config")
client, err := v1.NewForConfig(config)
```
调用方式为 client.资源名.接口 ，例如：

```go
rsp, err := client.Authz().Authorize()
```
参考示例为 marmotedu-sdk-go/examples/authz/main.go。上面我介绍了 marmotedu-sdk-go 的客户端创建方法，接下来我们再来看下，这些客户端具体是如何执行 REST API 请求的。

### marmotedu-sdk-go 的实现
marmotedu-sdk-go 的实现和 medu-sdk-go 一样，也是采用分层结构，分为 API 层和基础层。如下图所示：


![img](https://static001.geekbang.org/resource/image/c4/b2/c40439c97998a01758923394116c33b2.jpg?wh=2248x2097)

[RESTClient](https://github.com/marmotedu/marmotedu-sdk-go/blob/v1.0.2/rest/client.go#L95-L105)是整个 SDK 的核心，RESTClient 向下通过调用Request模块，来完成 HTTP 请求方法、请求路径、请求体、认证信息的构建。Request 模块最终通过调用gorequest包提供的方法，完成 HTTP 的 POST、PUT、GET、DELETE 等请求，获取 HTTP 返回结果，并解析到指定的结构体中。RESTClient 向上提供 Post() 、 Put() 、 Get() 、 Delete() 等方法来供客户端完成 HTTP 请求。


marmotedu-sdk-go 提供了两类客户端，分别是 RESTClient 客户端和基于 RESTClient 封装的客户端。


- RESTClient：Raw 类型的客户端，可以通过指定 HTTP 的请求方法、请求路径、请求参数等信息，直接发送 HTTP 请求，例如 client.Get().AbsPath("/version").Do().Into() 。
- 基于 RESTClient 封装的客户端：例如 AuthzV1Client、APIV1Client 等，执行特定 REST 资源、特定 API 接口的请求，方便开发者调用。


接下来，我们具体看下如何创建 RESTClient 客户端，以及 Request 模块的实现。


### RESTClient 客户端实现
我通过下面两个步骤，实现了 RESTClient 客户端。



#### 第一步，创建rest.Config类型的变量。
BuildConfigFromFlags函数通过加载 yaml 格式的配置文件，来创建 rest.Config 类型的变量，加载的 yaml 格式配置文件内容为：


```go
apiVersion: v1
user:
  #token: # JWT Token
  username: admin # iam 用户名
  password: Admin@2020 # iam 密码
  #secret-id: # 密钥 ID
  #secret-key: # 密钥 Key
  client-certificate: /home/colin/.iam/cert/admin.pem # 用于 TLS 的客户端证书文件路径
  client-key: /home/colin/.iam/cert/admin-key.pem # 用于 TLS 的客户端 key 文件路径
  #client-certificate-data:
  #client-key-data:

server:
  address: https://127.0.0.1:8443 # iam api-server 地址
  timeout: 10s # 请求 api-server 超时时间
  #max-retries: # 最大重试次数，默认为 0
  #retry-interval: # 重试间隔，默认为 1s
  #tls-server-name: # TLS 服务器名称
  #insecure-skip-tls-verify: # 设置为 true 表示跳过 TLS 安全验证模式，将使得 HTTPS 连接不安全
  certificate-authority: /home/colin/.iam/cert/ca.pem # 用于 CA 授权的 cert 文件路径
  #certificate-authority-data:
```

在配置文件中，我们可以指定服务的地址、用户名 / 密码、密钥、TLS 证书、超时时间、重试次数等信息。创建方法如下：


```go
config, err := clientcmd.BuildConfigFromFlags("", *iamconfig)    
if err != nil {                                                  
    panic(err.Error())    
}  
```
这里的代码中，*iamconfig 是 yaml 格式的配置文件路径。BuildConfigFromFlags 函数中，调用LoadFromFile函数来解析 yaml 配置文件。LoadFromFile 最终是通过 yaml.Unmarshal 的方式来解析 yaml 格式的配置文件的。


#### 第二步，根据 rest.Config 类型的变量，创建 RESTClient 客户端。


通过RESTClientFor函数来创建 RESTClient 客户端：


```go
func RESTClientFor(config *Config) (*RESTClient, error) {
    ...
    baseURL, versionedAPIPath, err := defaultServerURLFor(config)
    if err != nil {
        return nil, err
    }

    // Get the TLS options for this client config
    tlsConfig, err := TLSConfigFor(config)
    if err != nil {
        return nil, err
    }

    // Only retry when get a server side error.
    client := gorequest.New().TLSClientConfig(tlsConfig).Timeout(config.Timeout).
        Retry(config.MaxRetries, config.RetryInterval, http.StatusInternalServerError)
    // NOTICE: must set DoNotClearSuperAgent to true, or the client will clean header befor http.Do
    client.DoNotClearSuperAgent = true

    ...

    clientContent := ClientContentConfig{
        Username:           config.Username,
        Password:           config.Password,
        SecretID:           config.SecretID,
        SecretKey:          config.SecretKey,
        ...
    }

    return NewRESTClient(baseURL, versionedAPIPath, clientContent, client)
}
```
RESTClientFor 函数调用defaultServerURLFor(config)生成基本的 HTTP 请求路径：baseURL=http://127.0.0.1:8080，versionedAPIPath=/v1。然后，通过TLSConfigFor函数生成 TLS 配置，并调用 gorequest.New() 创建 gorequest 客户端，将客户端配置信息保存在变量中。最后，调用NewRESTClient函数创建 RESTClient 客户端。


RESTClient 客户端提供了以下方法，来供调用者完成 HTTP 请求：


```go
func (c *RESTClient) APIVersion() scheme.GroupVersion
func (c *RESTClient) Delete() *Request
func (c *RESTClient) Get() *Request
func (c *RESTClient) Post() *Request
func (c *RESTClient) Put() *Request
func (c *RESTClient) Verb(verb string) *Request
```
可以看到，RESTClient 提供了 Delete 、 Get 、 Post 、 Put 方法，分别用来执行 HTTP 的 DELETE、GET、POST、PUT 方法，提供的 Verb 方法可以灵活地指定 HTTP 方法。这些方法都返回了 Request 类型的变量。Request 类型的变量提供了一些方法，用来完成具体的 HTTP 请求，例如：


```go
  type Response struct {
    Allowed bool   `json:"allowed"`
    Denied  bool   `json:"denied,omitempty"`
    Reason  string `json:"reason,omitempty"`
    Error   string `json:"error,omitempty"`
}

func (c *authz) Authorize(ctx context.Context, request *ladon.Request, opts metav1.AuthorizeOptions) (result *Response, err error) {
    result = &Response{}                                         
    err = c.client.Post().
        Resource("authz").
        VersionedParams(opts).
        Body(request).
        Do(ctx).
        Into(result)

    return
}
```
上面的代码中， c.client 是 RESTClient 客户端，通过调用 RESTClient 客户端的 Post 方法，返回了 *Request 类型的变量。


*Request 类型的变量提供了 Resource 和 VersionedParams 方法，来构建请求 HTTP URL 中的路径 /v1/authz ；通过 Body 方法，指定了 HTTP 请求的 Body。

到这里，我们分别构建了 HTTP 请求需要的参数：HTTP Method、请求 URL、请求 Body。所以，之后就可以调用 Do 方法来执行 HTTP 请求，并将返回结果通过 Into 方法保存在传入的 result 变量中。



### Request 模块实现
RESTClient 客户端的方法会返回 Request 类型的变量，Request 类型的变量提供了一系列的方法用来构建 HTTP 请求参数，并执行 HTTP 请求。


所以，Request 模块可以理解为最底层的通信层，我们来看下 Request 模块具体是如何完成 HTTP 请求的。我们先来看下Request 结构体的定义：


```go
type RESTClient struct {           
    // base is the root URL for all invocations of the client    
    base *url.URL    
    // group stand for the client group, eg: iam.api, iam.authz                       
    group string                                                                          
    // versionedAPIPath is a path segment connecting the base URL to the resource root    
    versionedAPIPath string                                      
    // content describes how a RESTClient encodes and decodes responses.    
    content ClientContentConfig    
    Client  *gorequest.SuperAgent    
}

type Request struct {
  c *RESTClient

  timeout time.Duration

  // generic components accessible via method setters
  verb       string
  pathPrefix string
  subpath    string
  params     url.Values
  headers    http.Header

  // structural elements of the request that are part of the IAM API conventions
  // namespace    string
  // namespaceSet bool
  resource     string
  resourceName string
  subresource  string

  // output
  err  error
  body interface{}
}  
```
再来看下 Request 结构体提供的方法：


```go
func (r *Request) AbsPath(segments ...string) *Request
func (r *Request) Body(obj interface{}) *Request
func (r *Request) Do(ctx context.Context) Result
func (r *Request) Name(resourceName string) *Request
func (r *Request) Param(paramName, s string) *Request
func (r *Request) Prefix(segments ...string) *Request
func (r *Request) RequestURI(uri string) *Request
func (r *Request) Resource(resource string) *Request
func (r *Request) SetHeader(key string, values ...string) *Request
func (r *Request) SubResource(subresources ...string) *Request
func (r *Request) Suffix(segments ...string) *Request
func (r *Request) Timeout(d time.Duration) *Request
func (r *Request) URL() *url.URL
func (r *Request) Verb(verb string) *Request
func (r *Request) VersionedParams(v interface{}) *Request
```

通过 Request 结构体的定义和使用方法，我们不难猜测出：Request 模块通过 Name 、 Resource 、 Body 、 SetHeader 等方法来设置 Request 结构体中的各个字段。这些字段最终用来构建出一个 HTTP 请求，并通过 Do 方法来执行 HTTP 请求。那么，如何构建并执行一个 HTTP 请求呢？我们可以通过以下 5 步，来构建并执行 HTTP 请求：


构建 HTTP URL；构建 HTTP Method；构建 HTTP Body；执行 HTTP 请求；保存 HTTP 返回结果。



接下来，我们就来具体看下 Request 模块是如何构建这些请求参数，并发送 HTTP 请求的。


#### 第一步，构建 HTTP URL。
首先，通过defaultServerURLFor函数返回了http://iam.api.marmotedu.com:8080 和 /v1 ，并将二者分别保存在了 Request 类型结构体变量中 c 字段的 base 字段和 versionedAPIPath 字段中。


通过 [Do](https://github.com/marmotedu/marmotedu-sdk-go/blob/v1.0.3/rest/request.go#L379-L416) 方法执行 HTTP 时，会调用r.URL()方法来构建请求 URL。 r.URL 方法中，通过以下代码段构建了 HTTP 请求 URL：


```go
func (r *Request) URL() *url.URL {
    p := r.pathPrefix
    if len(r.resource) != 0 {
        p = path.Join(p, strings.ToLower(r.resource))
    }

    if len(r.resourceName) != 0 || len(r.subpath) != 0 || len(r.subresource) != 0 {
        p = path.Join(p, r.resourceName, r.subresource, r.subpath)
    }
                                                                                   
    finalURL := &url.URL{}
    if r.c.base != nil {
        *finalURL = *r.c.bas
    }
 
    finalURL.Path = p
    ...    
}
```
p := r.pathPrefix 和 r.c.base ，是通过 defaultServerURLFor 调用返回的 v1 和 http://iam.api.marmotedu.com:8080 来构建的。resourceName 通过 func (r *Request) Resource(resource string) *Request 来指定，例如 authz 。所以，最终我们构建的请求 URL 为 http://iam.api.marmotedu.com:8080/v1/authz 。


#### 第二步，构建 HTTP Method。
HTTP Method 通过 RESTClient 提供的 Post 、Delete 、Get 等方法来设置，例如：


```go
func (c *RESTClient) Post() *Request {                                                                                 
    return c.Verb("POST")                                                                                              
}

func (c *RESTClient) Verb(verb string) *Request {                                                                      
    return NewRequest(c).Verb(verb)                                                                                    
}
```
NewRequest(c).Verb(verb) 最终设置了 Request 结构体的 verb 字段，供 Do 方法使用。

#### 第三步，构建 HTTP Body。
HTTP Body 通过 Request 结构体提供的 Body 方法来指定：


```go
func (r *Request) Body(obj interface{}) *Request {                    
    if v := reflect.ValueOf(obj); v.Kind() == reflect.Struct {              
        r.SetHeader("Content-Type", r.c.content.ContentType)                
    }                                                                                                                  
                                                                                                                       
    r.body = obj                                                                                                       
                                                                                                                       
    return r                                                                                                           
} 
```

#### 第四步，执行 HTTP 请求。
通过 Request 结构体提供的 Do 方法来执行具体的 HTTP 请求，代码如下：


```go
func (r *Request) Do(ctx context.Context) Result {
  client := r.c.Client
  client.Header = r.headers

  if r.timeout > 0 {
    var cancel context.CancelFunc
    ctx, cancel = context.WithTimeout(ctx, r.timeout)

    defer cancel()
  }

  client.WithContext(ctx)

  resp, body, errs := client.CustomMethod(r.verb, r.URL().String()).Send(r.body).EndBytes()
  if err := combineErr(resp, body, errs); err != nil {
    return Result{
      response: &resp,
      err:      err,
      body:     body,
    }
  }

  decoder, err := r.c.content.Negotiator.Decoder()
  if err != nil {
    return Result{
      response: &resp,
      err:      err,
      body:     body,
      decoder:  decoder,
    }
  }

  return Result{
    response: &resp,
    body:     body,
    decoder:  decoder,
  }
}
```

在 Do 方法中，使用了 Request 结构体变量中各个字段的值，通过 client.CustomMethod 来执行 HTTP 请求。 client 是 *gorequest.SuperAgent 类型的客户端。


#### 第五步，保存 HTTP 返回结果。
通过 Request 结构体的 Into 方法来保存 HTTP 返回结果：

```go
func (r Result) Into(v interface{}) error {
    if r.err != nil {                                          
        return r.Error()
    }                                                                                 
                                                         
    if r.decoder == nil {                                                                    
        return fmt.Errorf("serializer doesn't exist")
    }                            
                             
    if err := r.decoder.Decode(r.body, &v); err != nil {
        return err                                                                    
    }                                                                                        
                                                             
    return nil                                                                      
}
```

r.body 是在 Do 方法中，执行完 HTTP 请求后设置的，它的值为 HTTP 请求返回的 Body。

### 请求认证
接下来，我再来介绍下 marmotedu-sdk-go 另外一个比较核心的功能：请求认证。


marmotedu-sdk-go 支持两种认证方式：
- Basic 认证：通过给请求添加 Authorization: Basic xxxx 来实现。
- Bearer 认证：通过给请求添加 Authorization: Bearer xxxx 来实现。这种方式又支持直接指定 JWT Token，或者通过指定密钥对由 SDK 自动生成 JWT Token。


Basic 认证和 Bearer 认证，我在 25 讲介绍过，你可以返回查看下。认证头是 RESTClient 客户端发送 HTTP 请求时指定的，具体实现位于NewRequest函数中：



```go
switch {
    case c.content.HasTokenAuth():
        r.SetHeader("Authorization", fmt.Sprintf("Bearer %s", c.content.BearerToken))
    case c.content.HasKeyAuth():
        tokenString := auth.Sign(c.content.SecretID, c.content.SecretKey, "marmotedu-sdk-go", c.group+".marmotedu.com")
        r.SetHeader("Authorization", fmt.Sprintf("Bearer %s", tokenString))
    case c.content.HasBasicAuth():
        // TODO: get token and set header
        r.SetHeader("Authorization", "Basic "+basicAuth(c.content.Username, c.content.Password))
}
```
上面的代码会根据配置信息，自动判断使用哪种认证方式。


### 总结
这一讲中，我介绍了 Kubernetes client-go 风格的 SDK 实现方式。和公有云厂商的 SDK 设计相比，client-go 风格的 SDK 设计有很多优点。marmotedu-sdk-go 在设计时，通过接口实现了 3 类客户端，分别是项目级别的客户端、应用级别的客户端和服务级别的客户端。开发人员可以根据需要，自行创建客户端类型。


marmotedu-sdk-go 通过RESTClientFor，创建了 RESTClient 类型的客户端，RESTClient 向下通过调用Request模块，来完成 HTTP 请求方法、请求路径、请求体、认证信息的构建。Request 模块最终通过调用gorequest包提供的方法，完成 HTTP 的 POST、PUT、GET、DELETE 等请求，获取 HTTP 返回结果，并解析到指定的结构体中。RESTClient 向上提供 Post() 、 Put() 、 Get() 、 Delete() 等方法，来供客户端完成 HTTP 请求。

## 35 | 效率神器：如何设计和实现一个命令行客户端工具？

如果你用过 Kubernetes、Istio、etcd，那你一定用过这些开源项目所提供的命令行工具：kubectl、istioctl、etcdctl。一个 xxx 项目，伴随着一个 xxxctl 命令行工具，这似乎已经成为一种趋势，在一些大型系统中更是常见。提供 xxxctl 命令行工具有这两个好处：


- 实现自动化：可以通过在脚本中调用 xxxctl 工具，实现自动化。
- 提高效率：通过将应用的功能封装成命令和参数，方便运维、开发人员在 Linux 服务器上调用。

其中，kubectl 命令设计的功能最为复杂，也是非常优秀的命令行工具，IAM 项目的 iamctl 客户端工具就是仿照 kubectl 来实现的。这一讲，我就通过剖析 iamctl 命令行工具的实现，来介绍下如何实现一个优秀的客户端工具。

### 常见客户端介绍
在介绍 iamctl 命令行工具的实现之前，我们先来看下常见的客户端。


客户端又叫用户端，与后端服务相对应，安装在客户机上，用户可以使用这些客户端访问后端服务。不同的客户端面向的人群不同，所能提供的访问能力也有差异。常见的客户端有下面这几种：

前端，包括浏览器、手机应用；SDK；命令行工具；其他终端。

接下来，我就来分别介绍下。


浏览器和手机应用提供一个交互界面供用户访问后端服务，使用体验最好，面向的人群是最终的用户。这两类客户端也称为前端。前端由前端开发人员进行开发，并通过 API 接口，调用后端的服务。后端开发人员不需要关注这两类客户端，只需要关注如何提供 API 接口即可。SDK（Software Development Kit）也是一个客户端，供开发者调用。开发者调用 API 时，如果是通过 HTTP 协议，需要编写 HTTP 的调用代码、HTTP 请求包的封装和返回包的解封，还要处理 HTTP 的状态码，使用起来不是很方便。SDK 其实是封装了 API 接口的一系列函数集合，开发者通过调用 SDK 中的函数调用 API 接口，提供 SDK 主要是方便开发者调用，减少工作量。命令行工具是可以在操作系统上执行的一个二进制程序，提供了一种比 SDK 和 API 接口更方便快捷的访问后端服务的途径，供运维或者开发人员在服务器上直接执行使用，或者在自动化脚本中调用。


还有其他各类客户端，这里我列举一些常见的。终端设备：POS 机、学习机、智能音箱等。第三方应用程序：通过调用 API 接口或者 SDK，调用我们提供的后端服务，从而实现自身的功能。脚本：脚本中通过 API 接口或者命令行工具，调用我们提供的后端服务，实现自动化。


这些其他的各类客户端，都是通过调用 API 接口使用后端服务的，它们跟前端一样，也不需要后台开发人员开发。需要后台开发人员投入工作量进行研发的客户端是 SDK 和命令行工具。这两类客户端工具有个调用和被调用的顺序，如下图所示：


![img](https://static001.geekbang.org/resource/image/e9/91/e97e547bec77dc7129615b11792f1291.jpg?wh=1920x568)

你可以看到，命令行工具和 SDK 最终都是通过 API 接口调用后端服务的，通过这种方式可以保证服务的一致性，并减少为适配多个客户端所带来的额外开发工作量。


### 大型系统客户端（xxxctl）的特点
通过学习 kubectl、istioctl、etcdctl 这些优秀的命令行工具，可以发现一个大型系统的命令行工具，通常具有下面这些特点：


- 支持命令和子命令，命令 / 子命名有自己独有的命令行参数。
- 支持一些特殊的命令。比如支持 completion 命令，completion 命令可以输出 bash/zsh 自动补全脚本，实现命令行及参数的自动补全。还支持 version 命令，version 命令不仅可以输出客户端的版本，还可以输出服务端的版本（如果有需要）。
- 支持全局 option，全局 option 可以作为所有命令及子命令的命令行参数。
- 支持 -h/help，-h/help 可以打印 xxxctl 的帮助信息，例如：


```
$ iamctl -h
iamctl controls the iam platform, is the client side tool for iam platform.

 Find more information at:
https://github.com/marmotedu/iam/blob/master/docs/guide/en-US/cmd/iamctl/iamctl.md

Basic Commands:
  info        Print the host information
  color       Print colors supported by the current terminal
  new         Generate demo command code
  jwt         JWT command-line tool

Identity and Access Management Commands:
  user        Manage users on iam platform
  secret      Manage secrets on iam platform
  policy      Manage authorization policies on iam platform

Troubleshooting and Debugging Commands:
  validate    Validate the basic environment for iamctl to run

Settings Commands:
  set         Set specific features on objects
  completion  Output shell completion code for the specified shell (bash or zsh)

Other Commands:
  version     Print the client and server version information

Usage:
  iamctl [flags] [options]

Use "iamctl <command> --help" for more information about a given command.
Use "iamctl options" for a list of global command-line options (applies to all commands).
```
支持 xxxctl help [command | command subcommand] [command | command subcommand] -h ，打印命令 / 子命令的帮助信息，格式通常为 命令描述 + 使用方法 。例如：



```
$ istioctl help register
Registers a service instance (e.g. VM) joining the mesh
 
Usage:
  istioctl register <svcname> <ip> [name1:]port1 [name2:]port2 ... [flags]
```
除此之外，一个大型系统的命令行工具还可以支持一些更高阶的功能，例如：支持命令分组，支持配置文件，支持命令的使用 example，等等。


在 Go 生态中，如果我们要找一个符合上面所有特点的命令行工具，那非kubectl莫属。因为我今天要重点讲的 iamctl 客户端工具，就是仿照它来实现的，所以这里就不展开介绍 kubectl 了，不过还是建议你认真研究下 kubectl 的实现。


### iamctl 的核心实现
接下来，我就来介绍 IAM 系统自带的 iamctl 客户端工具，它是仿照 kubectl 来实现的，能够满足一个大型系统客户端工具的需求。我会从 iamctl 的功能、代码结构、命令行选项和配置文件解析 4 个方面来介绍。


#### iamctl 的功能
iamctl 将命令进行了分类。这里，我也建议你对命令进行分类，因为通过分类，不仅可以协助你理解命令的用途，还能帮你快速定位某类命令。另外，当命令很多时，分类也可以使命令看起来更规整。iamctl 实现的命令如下：


![img](https://static001.geekbang.org/resource/image/1d/da/1dee217f8be94ae1c3c1d9b29d627eda.jpg?wh=1920x1696)

更详细的功能，你可以参考 iamctl -h 。我建议你在实现 xxxctl 工具时，考虑实现下面这几个功能。
- API 功能：平台具有的 API 功能，都能通过 xxxctl 方便地进行调用。
- 工具：一些使用 IAM 系统时有用的功能，比如签发 JWT Token。
- version、completion、validate 命令。


#### 代码结构
iamctl 工具的 main 函数位于iamctl.go文件中。命令的实现存放在internal/iamctl/cmd/cmd.go文件中。iamctl 的命令统一存放在internal/iamctl/cmd目录下，每个命令都是一个 Go 包，包名即为命令名，具体实现存放在 internal/iamctl/cmd/<命令>/<命令>.go 文件中。如果命令有子命令，则子命令的实现存放在 internal/iamctl/cmd/<命令>/<命令>_<子命令>.go 文件中。



使用这种代码组织方式，即使是在命令很多的情况下，也能让代码井然有序，方便定位和维护代码。


#### 命令行选项
添加命令行选项的代码在[NewIAMCtlCommand](https://github.com/marmotedu/iam/blob/v1.0.6/internal/iamctl/cmd/cmd.go#L41-L130)函数中，核心代码为：

```
flags := cmds.PersistentFlags()
...                                                                             
iamConfigFlags := genericclioptions.NewConfigFlags(true).WithDeprecatedPasswordFlag().WithDeprecatedSecretFlag()
iamConfigFlags.AddFlags(flags)                                   
matchVersionIAMConfigFlags := cmdutil.NewMatchVersionFlags(iamConfigFlags)                
matchVersionIAMConfigFlags.AddFlags(cmds.PersistentFlags())
```
NewConfigFlags(true) 返回带有默认值的参数，并通过 iamConfigFlags.AddFlags(flags) 添加到 cobra 的命令行 flag 中。NewConfigFlags(true) 返回结构体类型的值都是指针类型，这样做的好处是：程序可以判断出是否指定了某个参数，从而可以根据需要添加参数。例如：可以通过 WithDeprecatedPasswordFlag() 和 WithDeprecatedSecretFlag() 添加密码和密钥认证参数。

NewMatchVersionFlags 指定是否需要服务端版本和客户端版本一致。如果不一致，在调用服务接口时会报错。


#### 配置文件解析
iamctl 需要连接 iam-apiserver，来完成用户、策略和密钥的增删改查，并且需要进行认证。要完成这些功能，需要有比较多的配置项。这些配置项如果每次都在命令行选项指定，会很麻烦，也容易出错。


最好的方式是保存到配置文件中，并加载配置文件。加载配置文件的代码位于 NewIAMCtlCommand 函数中，代码如下：

```go
_ = viper.BindPFlags(cmds.PersistentFlags())
cobra.OnInitialize(func() {
    genericapiserver.LoadConfig(viper.GetString(genericclioptions.FlagIAMConfig), "iamctl")
})  
```
iamctl 会按以下优先级加载配置文件：
- 命令行参 --iamconfig 指定的配置文件。
- 当前目录下的 iamctl.yaml 文件。
- $HOME/.iam/iamctl.yaml 文件。

这种加载方式具有两个好处。首先是可以手动指定不同的配置文件，这在多环境、多配置下尤为重要。其次是方便使用，可以把配置存放在默认的加载路径中，在执行命令时，就不用再指定 --iamconfig 参数。加载完配置文件之后，就可以通过 viper.Get() 函数来获取配置。例如，iamctl 使用了以下 viper.Get 方法：


![img](https://static001.geekbang.org/resource/image/8b/42/8bce5d0b9ab45b5238d70b73175cf642.png?wh=1920x813)


### iamctl 中子命令是如何构建的？
讲完了 iamctl 命令行工具的核心实现，我们再来看看 iamctl 命令行工具中，子命令是如何构建的。命令行工具的核心是命令，有很多种方法可以构建一个命令，但还是有一些比较好的构建方法，值得我们去参考。接下来，我来介绍下如何用比较好的方式去构建命令。


#### 命令构建
命令行工具的核心能力是提供各类命令，来完成不同功能，每个命令构建的方式可以完全不同，但最好能按相同的方式去构建，并抽象成一个模型。如下图所示：


![img](https://static001.geekbang.org/resource/image/1e/93/1e78d2f387be0bcbae573d486e391e93.jpg?wh=1920x916)
你可以将一个命令行工具提供的命令进行分组。每个分组包含多个命令，每个命令又可以具有多个子命令，子命令和父命令在构建方式上完全一致。每个命令可以按下面的四种方式构建。具体代码你可以参考internal/iamctl/cmd/user/user_update.go。

- 通过 NewCmdXyz 函数创建命令框架。 NewCmdXyz 函数通过创建一个 cobra.Command 类型的变量来创建命令；通过指定 cobra.Command 结构体类型的 Short、Long、Example 字段，来指定该命令的使用文档iamctl -h 、详细使用文档iamctl xyz -h 和使用示例。
- 通过 cmd.Flags().XxxxVar 来给该命令添加命令行选项。
- 为了在不指定命令行参数时，能够按照默认的方式执行命令，可以通过 NewXyzOptions 函数返回一个设置了默认选项的 XyzOptions 类型的变量。
- XyzOptions 选项具有 Complete 、Validate 和 Run 三个方法，分别完成选项补全、选项验证和命令执行。命令的执行逻辑可以在 func (o *XyzOptions) Run(args []string) error 函数中编写。


按相同的方式去构建命令，抽象成一个通用模型，这种方式有下面四个好处。
- 减少理解成本：理解一个命令的构建方式，就可以理解其他命令的构建方式。
- 提高新命令的开发效率：可以复用其他命令的开发框架，新命令只需填写业务逻辑即可。
- 自动生成命令：可以按照规定的命令模型，自动生成新的命令。
- 易维护：因为所有的命令都来自于同一个命令模型，所以可以保持一致的代码风格，方便后期维护。


#### 自动生成命令
上面讲到，自动生成命令模型的好处之一是可以自动生成命令，下面让我们来具体看下。iamctl 自带了命令生成工具，下面我们看看生成方法，一共可以分成 5 步。这里假设生成 xyz 命令。


第一步，新建一个 xyz 目录，用来存放 xyz 命令源码：


```
$ mkdir internal/iamctl/cmd/xyz
```
第二步，在 xyz 目录下，使用 iamctl new 命令生成 xyz 命令源码：


```
$ cd internal/iamctl/cmd/xyz/
$ iamctl new xyz
Command file generated: xyz.go
```
第三步，将 xyz 命令添加到 root 命令中，假设 xyz 属于 Settings Commands 命令分组。


在 NewIAMCtlCommand 函数中，找到 Settings Commands 分组，将 NewCmdXyz 追加到 Commands 数组后面：


```

       {
            Message: "Settings Commands:",
            Commands: []*cobra.Command{
                set.NewCmdSet(f, ioStreams),
                completion.NewCmdCompletion(ioStreams.Out, ""),
                xyz.NewCmdXyz(f, ioStreams),
            },
        }, 

```
第四步，编译 iamctl：


```
$ make build BINS=iamctl  
```
第五步，测试：


```
$ iamctl xyz -h
A longer description that spans multiple lines and likely contains examples and usage of using your command. For
example:
 
 Cobra is a CLI library for Go that empowers applications. This application is a tool to generate the needed files to
quickly create a Cobra application.
 
Examples:
  # Print all option values for xyz
  iamctl xyz marmotedu marmotedupass
 
Options:
  -b, --bool=false: Bool option.
  -i, --int=0: Int option.
      --slice=[]: String slice option.
      --string='default': String option.
 
Usage:
  iamctl xyz USERNAME PASSWORD [options]
 
Use "iamctl options" for a list of global command-line options (applies to all commands).
$ iamctl xyz marmotedu marmotedupass
The following is option values:
==> --string: default(complete)
==> --slice: []
==> --int: 0
==> --bool: false
 
The following is args values:
==> username: marmotedu
==> password: marmotedupass
```

你可以看到，经过短短的几步，就添加了一个新的命令 xyz 。 iamctl new 命令不仅可以生成不带子命令的命令，还可以生成带有子命令的命令，生成方式如下：



```
$ iamctl new -g xyz
Command file generated: xyz.go
Command file generated: xyz_subcmd1.go
Command file generated: xyz_subcmd2.go
```
#### 命令自动补全
cobra 会根据注册的命令自动生成补全脚本，可以补全父命令、子命令和选项参数。在 bash 下，可以按下面的方式配置自动补全功能。


第一步，生成自动补全脚本：


```
$ iamctl completion bash > ~/.iam/completion.bash.inc
```
第二步，登陆时加载 bash，自动补全脚本：


```
$ echo "source '$HOME/.iam/completion.bash.inc'" >> $HOME/.bash_profile
$ source $HOME/.bash_profile
```
第三步，测试自动补全功能：


```
$ iamctl xy<TAB> # 按TAB键，自动补全为：iamctl xyz
$ iamctl xyz --b<TAB> # 按TAB键，自动补全为：iamctl xyz --bool
```

#### 更友好的输出
在开发命令时，可以通过一些技巧来提高使用体验。我经常会在输出中打印一些彩色输出，或者将一些输出以表格的形式输出，如下图所示：


![img](https://static001.geekbang.org/resource/image/74/42/74ef80708c853c20811e1e7bed7bde42.png?wh=651x226)

这里，使用 github.com/olekukonko/tablewriter 包来实现表格功能，使用 github.com/fatih/color 包来打印带色彩的字符串。具体使用方法，你可以参考internal/iamctl/cmd/validate/validate.go文件。

github.com/fatih/color 包可以给字符串标示颜色，字符串和颜色的对应关系可通过 iamctl color 来查看，如下图所示：


![img](https://static001.geekbang.org/resource/image/47/b9/47593869e1b10b15a35e16c661d818b9.png?wh=991x672)


### iamctl 是如何进行 API 调用的？
上面我介绍了 iamctl 命令的构建方式，那么这里我们再来看下 iamctl 是如何请求服务端 API 接口的。

Go 后端服务的功能通常通过 API 接口来对外暴露，一个后端服务可能供很多个终端使用，比如浏览器、命令行工具、手机等。为了保持功能的一致性，这些终端都会调用同一套 API 来完成相同的功能，如下图所示：


![img](https://static001.geekbang.org/resource/image/fb/bb/fb6de4f63454dd6471e023d73b8548bb.jpg?wh=1920x742)
如果命令行工具需要用到后端服务的功能，也需要通过 API 调用的方式。理想情况下，Go 后端服务对外暴露的所有 API 功能，都可以通过命令行工具来完成。一个 API 接口对应一个命令，API 接口的参数映射到命令的参数。

要调用服务端的 API 接口，最便捷的方法是通过 SDK 来调用，对于一些没有实现 SDK 的接口，也可以直接调用。所以，在命令行工具中，需要支持以下两种调用方式：

通过 SDK 调用服务端 API 接口。直接调用服务端的 API 接口（本专栏是 REST API 接口）。


iamctl 通过cmdutil.NewFactory创建一个 Factory 类型的变量 f ， Factory 定义为：



```go
type Factory interface {
    genericclioptions.RESTClientGetter
    IAMClientSet() (*marmotedu.Clientset, error)
    RESTClient() (*restclient.RESTClient, error)
}
```
将变量 f 传入到命令中，在命令中使用 Factory 接口提供的 RESTClient() 和 IAMClientSet() 方法，分别返回 RESTful API 客户端和 SDK 客户端，从而使用客户端提供的接口函数。代码可参考internal/iamctl/cmd/version/version.go。

### 客户端配置文件
如果要创建 RESTful API 客户端和 SDK 的客户端，需要调用 f.ToRESTConfig() 函数返回 *github.com/marmotedu/marmotedu-sdk-go/rest.Config 类型的配置变量，然后再基于 rest.Config 类型的配置变量创建客户端。


f.ToRESTConfig 函数最终是调用toRawIAMConfigLoader函数来生成配置的，代码如下：

```go
func (f *ConfigFlags) toRawIAMConfigLoader() clientcmd.ClientConfig {
    config := clientcmd.NewConfig()
    if err := viper.Unmarshal(&config); err != nil {
        panic(err)
    }

    return clientcmd.NewClientConfigFromConfig(config)
}
```
toRawIAMConfigLoader 返回 clientcmd.ClientConfig 类型的变量， clientcmd.ClientConfig 类型提供了 ClientConfig 方法，用来返回*rest.Config类型的变量。


在 toRawIAMConfigLoader 函数内部，通过 viper.Unmarshal 将 viper 中存储的配置解析到 clientcmd.Config 类型的结构体变量中。viper 中存储的配置，是在 cobra 命令启动时通过 LoadConfig 函数加载的，代码如下（位于 NewIAMCtlCommand 函数中）：

```go
cobra.OnInitialize(func() {                     
    genericapiserver.LoadConfig(viper.GetString(genericclioptions.FlagIAMConfig), "config")
}) 
```
你可以通过 --config 选项，指定配置文件的路径。



### SDK 调用
通过[IAMClient](https://github.com/marmotedu/iam/blob/v1.0.6/internal/iamctl/cmd/util/factory_client_access.go#L41-L47)返回 SDK 客户端，代码如下：

```go
func (f *factoryImpl) IAMClient() (*iam.IamClient, error) {
  clientConfig, err := f.ToRESTConfig()
  if err != nil {
    return nil, err
  }
  return iam.NewForConfig(clientConfig)
}
```
marmotedu.Clientset 提供了 iam-apiserver 的所有接口。


### REST API 调用
通过RESTClient()返回 RESTful API 客户端，代码如下：


```go
func (f *factoryImpl) RESTClient() (*restclient.RESTClient, error) {
  clientConfig, err := f.ToRESTConfig()
  if err != nil {
    return nil, err
  }
  setIAMDefaults(clientConfig)
  return restclient.RESTClientFor(clientConfig)
}
```
可以通过下面的方式访问 RESTful API 接口：

```go
serverVersion *version.Info

client, _ := f.RESTClient()
if err := client.Get().AbsPath("/version").Do(context.TODO()).Into(&serverVersion); err != nil {
    return err
}
```
上面的代码请求了 iam-apiserver 的 /version 接口，并将返回结果保存在 serverVersion 变量中。


### 总结
这一讲，我主要剖析了 iamctl 命令行工具的实现，进而向你介绍了如何实现一个优秀的客户端工具。


对于一个大型系统 xxx 来说，通常需要有一个 xxxctl 命令行工具， xxxctl 命令行工具可以方便开发、运维使用系统功能，并能实现功能自动化。

IAM 项目参考 kubectl，实现了命令行工具 iamctl。iamctl 集成了很多功能，我们可以通过 iamctl 子命令来使用这些功能。例如，我们可以通过 iamctl 对用户、密钥和策略进行 CURD 操作；可以设置 iamctl 自动补全脚本；可以查看 IAM 系统的版本信息。甚至，你还可以使用 iamctl new 命令，快速创建一个 iamctl 子命令模板。


iamctl 使用了 cobra、pflag、viper 包来构建，每个子命令又包含了一些基本的功能，例如短描述、长描述、使用示例、命令行选项、选项校验等。iamctl 命令可以加载不同的配置文件，来连接不同的客户端。iamctl 通过 SDK 调用、REST API 调用两种方式来调用服务端 API 接口。


