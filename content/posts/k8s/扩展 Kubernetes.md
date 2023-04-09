# 扩展 Kubernetes

了解针对工作环境需要来调整 Kubernetes 集群的进阶方法。

---

## [配置聚合层](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/)
配置[聚合层](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/)可以允许 Kubernetes apiserver 使用其它 API 扩展，这些 API 不是核心 Kubernetes API 的一部分。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

**说明：**

要使聚合层在你的环境中正常工作以支持代理服务器和扩展 apiserver 之间的相互 TLS 身份验证， 需要满足一些设置要求。Kubernetes 和 kube-apiserver 具有多个 CA， 因此请确保代理是由聚合层 CA 签名的，而不是由 Kubernetes 通用 CA 签名的。

**注意：**

对不同的客户端类型重复使用相同的 CA 会对集群的功能产生负面影响。 有关更多信息，请参见 [CA 重用和冲突](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#ca-reusage-and-conflicts)。

### 身份认证流程[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#authentication-flow)

与自定义资源定义（CRD）不同，除标准的 Kubernetes apiserver 外，Aggregation API 还涉及另一个服务器：扩展 apiserver。 Kubernetes apiserver 将需要与你的扩展 apiserver 通信，并且你的扩展 apiserver 也需要与 Kubernetes apiserver 通信。 为了确保此通信的安全，Kubernetes apiserver 使用 x509 证书向扩展 apiserver 认证。

本节介绍身份认证和鉴权流程的工作方式以及如何配置它们。

大致流程如下：

1. Kubernetes apiserver：对发出请求的用户身份认证，并对请求的 API 路径执行鉴权。
2. Kubernetes apiserver：将请求转发到扩展 apiserver
3. 扩展 apiserver：认证来自 Kubernetes apiserver 的请求
4. 扩展 apiserver：对来自原始用户的请求鉴权
5. 扩展 apiserver：执行

本节的其余部分详细描述了这些步骤。

该流程可以在下图中看到。

![聚合层认证流程](https://d33wubrfki0l68.cloudfront.net/3c5428678a95c3715894011d8dd4812d2cf229b9/e745c/images/docs/aggregation-api-auth-flow.png)

以上泳道的来源可以在本文档的源码中找到。

#### Kubernetes Apiserver 认证和授权[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#kubernetes-apiserver-authentication-and-authorization)

由扩展 apiserver 服务的对 API 路径的请求以与所有 API 请求相同的方式开始： 与 Kubernetes apiserver 的通信。该路径已通过扩展 apiserver 在 Kubernetes apiserver 中注册。

用户与 Kubernetes apiserver 通信，请求访问路径。 Kubernetes apiserver 使用它的标准认证和授权配置来对用户认证，以及对特定路径的鉴权。

有关对 Kubernetes 集群认证的概述， 请参见[对集群认证](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/authentication/)。 有关对 Kubernetes 集群资源的访问鉴权的概述， 请参见[鉴权概述](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/authorization/)。

到目前为止，所有内容都是标准的 Kubernetes API 请求，认证与鉴权。

Kubernetes apiserver 现在准备将请求发送到扩展 apiserver。

#### Kubernetes Apiserver 代理请求[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#kubernetes-apiserver-proxies-the-request)

Kubernetes apiserver 现在将请求发送或代理到注册以处理该请求的扩展 apiserver。 为此，它需要了解几件事：

1. Kubernetes apiserver 应该如何向扩展 apiserver 认证，以通知扩展 apiserver 通过网络发出的请求来自有效的 Kubernetes apiserver？
    
2. Kubernetes apiserver 应该如何通知扩展 apiserver 原始请求已通过认证的用户名和组？
    

为提供这两条信息，你必须使用若干标志来配置 Kubernetes apiserver。

##### Kubernetes Apiserver 客户端认证[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#kubernetes-apiserver-%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%AE%A4%E8%AF%81)

Kubernetes apiserver 通过 TLS 连接到扩展 apiserver，并使用客户端证书认证。 你必须在启动时使用提供的标志向 Kubernetes apiserver 提供以下内容：

- 通过 `--proxy-client-key-file` 指定私钥文件
- 通过 `--proxy-client-cert-file` 签名的客户端证书文件
- 通过 `--requestheader-client-ca-file` 签署客户端证书文件的 CA 证书
- 通过 `--requestheader-allowed-names` 在签署的客户端证书中有效的公用名（CN）

Kubernetes apiserver 将使用由 `--proxy-client-*-file` 指示的文件来向扩展 apiserver认证。 为了使合规的扩展 apiserver 能够将该请求视为有效，必须满足以下条件：

1. 连接必须使用由 CA 签署的客户端证书，该证书的证书位于 `--requestheader-client-ca-file` 中。
2. 连接必须使用客户端证书，该客户端证书的 CN 是 `--requestheader-allowed-names` 中列出的证书之一。

**说明：**

你可以将此选项设置为空白，即为`--requestheader-allowed-names=""`。 这将向扩展 apiserver 指示**任何** CN 都是可接受的。

使用这些选项启动时，Kubernetes apiserver 将：

1. 使用它们向扩展 apiserver 认证。
2. 在 `kube-system` 命名空间中创建一个名为 `extension-apiserver-authentication` 的 ConfigMap， 它将在其中放置 CA 证书和允许的 CN。 反过来，扩展 apiserver 可以检索这些内容以验证请求。

请注意，Kubernetes apiserver 使用相同的客户端证书对所有扩展 apiserver 认证。 它不会为每个扩展 apiserver 创建一个客户端证书，而是创建一个证书作为 Kubernetes apiserver 认证。所有扩展 apiserver 请求都重复使用相同的请求。

##### 原始请求用户名和组[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#%E5%8E%9F%E5%A7%8B%E8%AF%B7%E6%B1%82%E7%94%A8%E6%88%B7%E5%90%8D%E5%92%8C%E7%BB%84)

当 Kubernetes apiserver 将请求代理到扩展 apiserver 时， 它将向扩展 apiserver 通知原始请求已成功通过其验证的用户名和组。 它在其代理请求的 HTTP 头部中提供这些。你必须将要使用的标头名称告知 Kubernetes apiserver。

- 通过 `--requestheader-username-headers` 标明用来保存用户名的头部
- 通过 `--requestheader-group-headers` 标明用来保存 group 的头部
- 通过 `--requestheader-extra-headers-prefix` 标明用来保存拓展信息前缀的头部

这些头部名称也放置在 `extension-apiserver-authentication` ConfigMap 中， 因此扩展 apiserver 可以检索和使用它们。

#### 扩展 Apiserver 认证请求[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#extension-apiserver-authenticates-the-request)

扩展 apiserver 在收到来自 Kubernetes apiserver 的代理请求后， 必须验证该请求确实确实来自有效的身份验证代理， 该认证代理由 Kubernetes apiserver 履行。扩展 apiserver 通过以下方式对其认证：

1. 如上所述，从 `kube-system` 中的 ConfigMap 中检索以下内容：
    
    - 客户端 CA 证书
    - 允许名称（CN）列表
    - 用户名，组和其他信息的头部
2. 使用以下证书检查 TLS 连接是否已通过认证：
    
    - 由其证书与检索到的 CA 证书匹配的 CA 签名。
    - 在允许的 CN 列表中有一个 CN，除非列表为空，在这种情况下允许所有 CN。
    - 从适当的头部中提取用户名和组。

如果以上均通过，则该请求是来自合法认证代理（在本例中为 Kubernetes apiserver） 的有效代理请求。

请注意，扩展 apiserver 实现负责提供上述内容。 默认情况下，许多扩展 apiserver 实现利用 `k8s.io/apiserver/` 软件包来做到这一点。 也有一些实现可能支持使用命令行选项来覆盖这些配置。

为了具有检索 configmap 的权限，扩展 apiserver 需要适当的角色。 在 `kube-system` 名字空间中有一个默认角色 `extension-apiserver-authentication-reader` 可用于设置。

#### 扩展 Apiserver 对请求鉴权[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#extensions-apiserver-authorizes-the-request)

扩展 apiserver 现在可以验证从标头检索的`user/group`是否有权执行给定请求。 通过向 Kubernetes apiserver 发送标准 [SubjectAccessReview](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/authorization/) 请求来实现。

为了使扩展 apiserver 本身被鉴权可以向 Kubernetes apiserver 提交 SubjectAccessReview 请求， 它需要正确的权限。 Kubernetes 包含一个具有相应权限的名为 `system:auth-delegator` 的默认 `ClusterRole`， 可以将其授予扩展 apiserver 的服务帐户。

#### 扩展 Apiserver 执行[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#enable-kubernetes-apiserver-flags)

如果 `SubjectAccessReview` 通过，则扩展 apiserver 执行请求。

### 启用 Kubernetes Apiserver 标志[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#%E5%90%AF%E7%94%A8-kubernetes-apiserver-%E6%A0%87%E5%BF%97)

通过以下 `kube-apiserver` 标志启用聚合层。 你的服务提供商可能已经为你完成了这些工作：

```
    --requestheader-client-ca-file=<path to aggregator CA cert>
    --requestheader-allowed-names=front-proxy-client
    --requestheader-extra-headers-prefix=X-Remote-Extra-
    --requestheader-group-headers=X-Remote-Group
    --requestheader-username-headers=X-Remote-User
    --proxy-client-cert-file=<path to aggregator proxy cert>
    --proxy-client-key-file=<path to aggregator proxy key>
```

#### CA 重用和冲突[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#ca-reusage-and-conflicts)

Kubernetes apiserver 有两个客户端 CA 选项：

- `--client-ca-file`
- `--requestheader-client-ca-file`

这些功能中的每个功能都是独立的；如果使用不正确，可能彼此冲突。

- `--client-ca-file`：当请求到达 Kubernetes apiserver 时，如果启用了此选项， 则 Kubernetes apiserver 会检查请求的证书。 如果它是由 `--client-ca-file` 引用的文件中的 CA 证书之一签名的， 并且用户是公用名 `CN=` 的值，而组是组织 `O=` 的取值，则该请求被视为合法请求。 请参阅[关于 TLS 身份验证的文档](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/authentication/#x509-client-certs)。
    
- `--requestheader-client-ca-file`：当请求到达 Kubernetes apiserver 时， 如果启用此选项，则 Kubernetes apiserver 会检查请求的证书。 如果它是由文件引用中的 --requestheader-client-ca-file 所签署的 CA 证书之一签名的， 则该请求将被视为潜在的合法请求。 然后，Kubernetes apiserver 检查通用名称 `CN=` 是否是 `--requestheader-allowed-names` 提供的列表中的名称之一。 如果名称允许，则请求被批准；如果不是，则请求被拒绝。
    

如果同时提供了 `--client-ca-file` 和 `--requestheader-client-ca-file`， 则首先检查 `--requestheader-client-ca-file` CA，然后再检查 `--client-ca-file`。 通常，这些选项中的每一个都使用不同的 CA（根 CA 或中间 CA）。 常规客户端请求与 `--client-ca-file` 相匹配，而聚合请求要与 `--requestheader-client-ca-file` 相匹配。 但是，如果两者都使用同一个 CA，则通常会通过 `--client-ca-file` 传递的客户端请求将失败，因为 CA 将与 `--requestheader-client-ca-file` 中的 CA 匹配，但是通用名称 `CN=` 将不匹配 `--requestheader-allowed-names` 中可接受的通用名称之一。 这可能导致你的 kubelet 和其他控制平面组件以及最终用户无法向 Kubernetes apiserver 认证。

因此，请对用于控制平面组件和最终用户鉴权的 `--client-ca-file` 选项和 用于聚合 apiserver 鉴权的 `--requestheader-client-ca-file` 选项使用 不同的 CA 证书。

**警告：**

除非你了解风险和保护 CA 用法的机制，否则 **不要** 重用在不同上下文中使用的 CA。

如果你未在运行 API 服务器的主机上运行 kube-proxy，则必须确保使用以下 `kube-apiserver` 标志启用系统：

```
--enable-aggregator-routing=true
```

#### 注册 APIService 对象[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#register-apiservice-objects)

你可以动态配置将哪些客户端请求代理到扩展 apiserver。以下是注册示例：

```yaml
apiVersion: apiregistration.k8s.io/v1
kind: APIService
metadata:
  name: <注释对象名称>
spec:
  group: <扩展 Apiserver 的 API 组名>
  version: <扩展 Apiserver 的 API 版本>
  groupPriorityMinimum: <APIService 对应组的优先级, 参考 API 文档>
  versionPriority: <版本在组中的优先排序, 参考 API 文档>
  service:
    namespace: <拓展 Apiserver 服务的名字空间>
    name: <拓展 Apiserver 服务的名称>
  caBundle: <PEM 编码的 CA 证书，用于对 Webhook 服务器的证书签名>
```

APIService 对象的名称必须是合法的[路径片段名称](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#path-segment-names)。

##### 调用扩展 apiserver[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#%E8%B0%83%E7%94%A8%E6%89%A9%E5%B1%95-apiserver)

一旦 Kubernetes apiserver 确定应将请求发送到扩展 apiserver， 它需要知道如何调用它。

`service` 部分是对扩展 apiserver 的服务的引用。 服务的名字空间和名字是必需的。端口是可选的，默认为 443。

下面是一个扩展 apiserver 的配置示例，它被配置为在端口 `1234` 上调用。 并针对 ServerName `my-service-name.my-service-namespace.svc` 使用自定义的 CA 包来验证 TLS 连接使用自定义 CA 捆绑包的 `my-service-name.my-service-namespace.svc`。

```yaml
apiVersion: apiregistration.k8s.io/v1
kind: APIService
...
spec:
  ...
  service:
    namespace: my-service-namespace
    name: my-service-name
    port: 1234
  caBundle: "Ci0tLS0tQk...<base64-encoded PEM bundle>...tLS0K"
...
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 使用聚合层[安装扩展 API 服务器](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-extension-api-server/)。
- 有关高级概述，请参阅[使用聚合层扩展 Kubernetes API](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/)。
- 了解如何[使用自定义资源扩展 Kubernetes API](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)。
## [使用自定义资源](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/)
### 使用 CustomResourceDefinition 扩展 Kubernetes API

本页展示如何使用 [CustomResourceDefinition](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#customresourcedefinition-v1-apiextensions-k8s-io) 将[定制资源（Custom Resource）](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/api-extension/custom-resources/) 安装到 Kubernetes API 上。

#### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你的 Kubernetes 服务器版本必须不低于版本 1.16. 要获知版本信息，请输入 `kubectl version`.

如果你在使用较老的、仍处于被支持范围的 Kubernetes 版本， 请切换到该版本的文档查看对于你的集群而言有用的建议。

#### 创建 CustomResourceDefinition[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#create-a-customresourcedefinition)

当你创建新的 CustomResourceDefinition（CRD）时，Kubernetes API 服务器会为你所指定的每个版本生成一个新的 RESTful 资源路径。 基于 CRD 对象所创建的自定义资源可以是名字空间作用域的，也可以是集群作用域的， 取决于 CRD 对象 `spec.scope` 字段的设置。

与其它的内置对象一样，删除名字空间也将删除该名字空间中的所有自定义对象。 CustomResourceDefinitions 本身是无名字空间的，可在所有名字空间中访问。

例如，如果你将下面的 CustomResourceDefinition 保存到 `resourcedefinition.yaml` 文件：

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  # 名字必需与下面的 spec 字段匹配，并且格式为 '<名称的复数形式>.<组名>'
  name: crontabs.stable.example.com
spec:
  # 组名称，用于 REST API: /apis/<组>/<版本>
  group: stable.example.com
  # 列举此 CustomResourceDefinition 所支持的版本
  versions:
    - name: v1
      # 每个版本都可以通过 served 标志来独立启用或禁止
      served: true
      # 其中一个且只有一个版本必需被标记为存储版本
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
  # 可以是 Namespaced 或 Cluster
  scope: Namespaced
  names:
    # 名称的复数形式，用于 URL：/apis/<组>/<版本>/<名称的复数形式>
    plural: crontabs
    # 名称的单数形式，作为命令行使用时和显示时的别名
    singular: crontab
    # kind 通常是单数形式的驼峰命名（CamelCased）形式。你的资源清单会使用这一形式。
    kind: CronTab
    # shortNames 允许你在命令行使用较短的字符串来匹配资源
    shortNames:
    - ct
```

之后创建它：

```shell
kubectl apply -f resourcedefinition.yaml
```

这样一个新的受名字空间约束的 RESTful API 端点会被创建在：

```
/apis/stable.example.com/v1/namespaces/*/crontabs/...
```

此端点 URL 自此可以用来创建和管理定制对象。对象的 `kind` 将是来自你上面创建时 所用的 spec 中指定的 `CronTab`。

创建端点的操作可能需要几秒钟。你可以监测你的 CustomResourceDefinition 的 `Established` 状况变为 true，或者监测 API 服务器的发现信息等待你的资源出现在 那里。

#### 创建定制对象[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#create-custom-objects)

在创建了 CustomResourceDefinition 对象之后，你可以创建定制对象（Custom Objects）。定制对象可以包含定制字段。这些字段可以包含任意的 JSON 数据。 在下面的例子中，在类别为 `CronTab` 的定制对象中，设置了`cronSpec` 和 `image` 定制字段。类别 `CronTab` 来自你在上面所创建的 CRD 的规约。

如果你将下面的 YAML 保存到 `my-crontab.yaml`：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  cronSpec: "* * * * */5"
  image: my-awesome-cron-image
```

并执行创建命令：

```shell
kubectl apply -f my-crontab.yaml
```

你就可以使用 kubectl 来管理你的 CronTab 对象了。例如：

```shell
kubectl get crontab
```

应该会输出如下列表：

```none
NAME                 AGE
my-new-cron-object   6s
```

使用 kubectl 时，资源名称是大小写不敏感的，而且你既可以使用 CRD 中所定义的单数 形式或复数形式，也可以使用其短名称：

```shell
kubectl get ct -o yaml
```

你可以看到输出中包含了你创建定制对象时在 YAML 文件中指定的定制字段 `cronSpec` 和 `image`：

```yaml
apiVersion: v1
items:
- apiVersion: stable.example.com/v1
  kind: CronTab
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
                {"apiVersion":"stable.example.com/v1","kind":"CronTab","metadata":{"annotations":{},"name":"my-new-cron-object","namespace":"default"},"spec":{"cronSpec":"* * * * */5","image":"my-awesome-cron-image"}}
    creationTimestamp: "2021-06-20T07:35:27Z"
    generation: 1
    name: my-new-cron-object
    namespace: default
    resourceVersion: "1326"
    uid: 9aab1d66-628e-41bb-a422-57b8b3b1f5a9
  spec:
    cronSpec: '* * * * */5'
    image: my-awesome-cron-image
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

#### 删除 CustomResourceDefinition[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#delete-a-customresourcedefinition)

当你删除某 CustomResourceDefinition 时，服务器会卸载其 RESTful API 端点，并删除服务器上存储的所有定制对象。

```shell
kubectl delete -f resourcedefinition.yaml
kubectl get crontabs
```

```none
Error from server (NotFound): Unable to list {"stable.example.com" "v1" "crontabs"}: the server could not
find the requested resource (get crontabs.stable.example.com)
```

如果你在以后创建相同的 CustomResourceDefinition 时，该 CRD 会是一个空的结构。

#### 设置结构化的模式[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema)

CustomResource 对象在定制字段中保存结构化的数据，这些字段和内置的字段 `apiVersion`、`kind` 和 `metadata` 等一起存储，不过内置的字段都会被 API 服务器隐式完成合法性检查。有了 [OpenAPI v3.0 检查](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation) 能力之后，你可以设置一个模式（Schema），在创建和更新定制对象时，这一模式会被用来 对对象内容进行合法性检查。参阅下文了解这类模式的细节和局限性。

在 `apiextensions.k8s.io/v1` 版本中，CustomResourceDefinition 的这一结构化模式 定义是必需的。 在 CustomResourceDefinition 的 beta 版本中，结构化模式定义是可选的。

结构化模式本身是一个 [OpenAPI v3.0 验证模式](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation)，其中：

1. 为对象根（root）设置一个非空的 type 值（藉由 OpenAPI 中的 `type`），对每个 object 节点的每个字段（藉由 OpenAPI 中的 `properties` 或 `additionalProperties`）以及 array 节点的每个条目（藉由 OpenAPI 中的 `items`）也要设置非空的 type 值， 除非：
    - 节点包含属性 `x-kubernetes-int-or-string: true`
    - 节点包含属性 `x-kubernetes-preserve-unknown-fields: true`
2. 对于 object 的每个字段或 array 中的每个条目，如果其定义中包含 `allOf`、`anyOf`、`oneOf` 或 `not`，则模式也要指定这些逻辑组合之外的字段或条目（试比较例 1 和例 2)。
3. 在 `allOf`、`anyOf`、`oneOf` 或 `not` 上下文内不设置 `description`、`type`、`default`、 `additionalProperties` 或者 `nullable`。此规则的例外是 `x-kubernetes-int-or-string` 的两种模式（见下文）。
4. 如果 `metadata` 被设置，则只允许对 `metadata.name` 和 `metadata.generateName` 设置约束。

非结构化的例 1:

```none
allOf:
- properties:
    foo:
      ...
```

违反了第 2 条规则。下面的是正确的：

```none
properties:
  foo:
    ...
allOf:
- properties:
    foo:
      ...
```

非结构化的例 2：

```none
allOf:
- items:
    properties:
      foo:
        ...
```

违反了第 2 条规则。下面的是正确的：

```none
items:
  properties:
    foo:
      ...
allOf:
- items:
    properties:
      foo:
        ...
```

非结构化的例 3：

```none
properties:
  foo:
    pattern: "abc"
  metadata:
    type: object
    properties:
      name:
        type: string
        pattern: "^a"
      finalizers:
        type: array
        items:
          type: string
          pattern: "my-finalizer"
anyOf:
- properties:
    bar:
      type: integer
      minimum: 42
  required: ["bar"]
  description: "foo bar object"
```

不是一个结构化的模式，因为其中存在以下违例：

- 根节点缺失 type 设置（规则 1）
- `foo` 的 type 缺失（规则 1）
- `anyOf` 中的 `bar` 未在外部指定（规则 2）
- `bar` 的 `type` 位于 `anyOf` 中（规则 3）
- `anyOf` 中设置了 `description` （规则 3）
- `metadata.finalizers` 不可以被限制 (规则 4）

作为对比，下面的 YAML 所对应的模式则是结构化的：

```yaml
type: object
description: "foo bar object"
properties:
  foo:
    type: string
    pattern: "abc"
  bar:
    type: integer
  metadata:
    type: object
    properties:
      name:
        type: string
        pattern: "^a"
anyOf:
- properties:
    bar:
      minimum: 42
  required: ["bar"]
```

如果违反了结构化模式规则，CustomResourceDefinition 的 `NonStructural` 状况中会包含报告信息。

##### 字段剪裁[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#field-pruning)

CustomResourceDefinition 在集群的持久性存储 [etcd](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/configure-upgrade-etcd/) 中保存经过合法性检查的资源数据。 就像原生的 Kubernetes 资源，例如 [ConfigMap](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/)， 如果你指定了 API 服务器所无法识别的字段，则该未知字段会在保存资源之前被 **剪裁（Pruned）** 掉（删除）。

从 `apiextensions.k8s.io/v1beta1` 转换到 `apiextensions.k8s.io/v1` 的 CRD 可能没有结构化的模式定义，因此其 `spec.preserveUnknownFields` 可能为 `true`。

对于使用 `apiextensions.k8s.io/v1beta1` 且将 `spec.preserveUnknownFields` 设置为 `true` 创建的旧 CustomResourceDefinition 对象，有以下表现：

- 裁剪未启用。
- 可以存储任意数据。

为了与 `apiextensions.k8s.io/v1` 兼容，将你的定制资源定义更新为：

1. 使用结构化的 OpenAPI 模式。
2. `spec.preserveUnknownFields` 设置为 `false`。

如果你将下面的 YAML 保存到 `my-crontab.yaml` 文件：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  cronSpec: "* * * * */5"
  image: my-awesome-cron-image
  someRandomField: 42
```

并创建之：

```shell
kubectl create --validate=false -f my-crontab.yaml -o yaml
```

输出类似于：

```yaml
apiVersion: stable.example.com/v1
kind: CronTab
metadata:
  creationTimestamp: 2017-05-31T12:56:35Z
  generation: 1
  name: my-new-cron-object
  namespace: default
  resourceVersion: "285"
  uid: 9423255b-4600-11e7-af6a-28d2447dc82b
spec:
  cronSpec: '* * * * */5'
  image: my-awesome-cron-image
```

注意其中的字段 `someRandomField` 已经被剪裁掉。

本例中通过 `--validate=false` 命令行选项 关闭了客户端的合法性检查以展示 API 服务器的行为， 因为 [OpenAPI 合法性检查模式也会发布到](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#publish-validation-schema-in-openapi-v2) 客户端，`kubectl` 也会检查未知的字段并在对象被发送到 API 服务器之前就拒绝它们。

###### 控制剪裁[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#controlling-pruning)

默认情况下，定制资源的所有版本中的所有未规定的字段都会被剪裁掉。 通过在结构化的 OpenAPI v3 [检查模式定义](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema) 中为特定字段的子树添加 `x-kubernetes-preserve-unknown-fields: true` 属性， 可以选择不对其执行剪裁操作。

例如：

```yaml
type: object
properties:
  json:
    x-kubernetes-preserve-unknown-fields: true
```

字段 `json` 可以保存任何 JSON 值，其中内容不会被剪裁掉。

你也可以部分地指定允许的 JSON 数据格式；例如：

```yaml
type: object
properties:
  json:
    x-kubernetes-preserve-unknown-fields: true
    type: object
    description: this is arbitrary JSON
```

通过这样设置，JSON 中只能设置 `object` 类型的值。

对于所指定的每个属性（或 `additionalProperties`），剪裁会再次被启用。

```yaml
type: object
properties:
  json:
    x-kubernetes-preserve-unknown-fields: true
    type: object
    properties:
      spec:
        type: object
        properties:
          foo:
            type: string
          bar:
            type: string
```

对于上述定义，如果提供的数值如下：

```yaml
json:
  spec:
    foo: abc
    bar: def
    something: x
  status:
    something: x
```

则该值会被剪裁为：

```yaml
json:
  spec:
    foo: abc
    bar: def
  status:
    something: x
```

这意味着所指定的 `spec` 对象中的 `something` 字段被剪裁掉，而其外部的内容都被保留。

##### IntOrString[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#intorstring)

模式定义中标记了 `x-kubernetes-int-or-string: true` 的节点不受前述规则 1 约束，因此下面的定义是结构化的模式：

```yaml
type: object
properties:
  foo:
    x-kubernetes-int-or-string: true
```

此外，所有这类节点也不再受规则 3 约束，也就是说，下面两种模式是被允许的 （注意，仅限于这两种模式，不支持添加新字段的任何其他变种）：

```none
x-kubernetes-int-or-string: true
anyOf:
  - type: integer
  - type: string
...
```

和

```none
x-kubernetes-int-or-string: true
allOf:
  - anyOf:
      - type: integer
      - type: string
  - ... # zero or more
...
```

在以上两种规约中，整数值和字符串值都会被认为是合法的。

在[合法性检查模式定义的发布时](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#publish-validation-schema-in-openapi-v2)， `x-kubernetes-int-or-string: true` 会被展开为上述两种模式之一。

##### RawExtension[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#rawextension)

RawExtensions（就像在 [k8s.io/apimachinery](https://github.com/kubernetes/apimachinery/blob/03ac7a9ade429d715a1a46ceaa3724c18ebae54f/pkg/runtime/types.go#L94) 项目中 `runtime.RawExtension` 所定义的那样） 可以保存完整的 Kubernetes 对象，也就是，其中会包含 `apiVersion` 和 `kind` 字段。

通过 `x-kubernetes-embedded-resource: true` 来设定这些嵌套对象的规约 （无论是完全无限制还是部分指定都可以）是可能的。例如：

```yaml
type: object
properties:
  foo:
    x-kubernetes-embedded-resource: true
    x-kubernetes-preserve-unknown-fields: true
```

这里，字段 `foo` 包含一个完整的对象，例如：

```none
foo:
  apiVersion: v1
  kind: Pod
  spec:
    ...
```

由于字段上设置了 `x-kubernetes-preserve-unknown-fields: true`，其中的内容不会 被剪裁。不过，在这个语境中，`x-kubernetes-preserve-unknown-fields: true` 的 使用是可选的。

设置了 `x-kubernetes-embedded-resource: true` 之后，`apiVersion`、`kind` 和 `metadata` 都是隐式设定并隐式完成合法性验证。

#### 提供 CRD 的多个版本[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#serving-multiple-versions-of-a-crd)

关于如何为你的 CustomResourceDefinition 提供多个版本的支持， 以及如何将你的对象从一个版本迁移到另一个版本， 详细信息可参阅 [CustomResourceDefinition 的版本管理](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/)。

#### 高级主题[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#advanced-topics)

##### Finalizers[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#finalizers)

**Finalizer** 能够让控制器实现异步的删除前（Pre-delete）回调。 与内置对象类似，定制对象也支持 Finalizer。

你可以像下面一样为定制对象添加 Finalizer：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  finalizers:
  - stable.example.com/finalizer
```

自定义 Finalizer 的标识符包含一个域名、一个正向斜线和 finalizer 的名称。 任何控制器都可以在任何对象的 finalizer 列表中添加新的 finalizer。

对带有 Finalizer 的对象的第一个删除请求会为其 `metadata.deletionTimestamp` 设置一个值，但不会真的删除对象。一旦此值被设置，`finalizers` 列表中的表项只能被移除。 在列表中仍然包含 finalizer 时，无法强制删除对应的对象。

当 `metadata.deletionTimestamp` 字段被设置时，监视该对象的各个控制器会执行它们所能处理的 finalizer，并在完成处理之后将其从列表中移除。 每个控制器负责将其 finalizer 从列表中删除。

`metadata.deletionGracePeriodSeconds` 的取值控制对更新的轮询周期。

一旦 finalizers 列表为空时，就意味着所有 finalizer 都被执行过， Kubernetes 会最终删除该资源，

##### 合法性检查[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation)

定制资源是通过 [OpenAPI v3 模式定义](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md#schemaObject) 来执行合法性检查的，当启用[验证规则特性](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation-rules)时，通过 `x-kubernetes-validations` 验证， 你可以通过使用[准入控制 Webhook](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/admission-controllers/#validatingadmissionwebhook) 来添加额外的合法性检查逻辑。

此外，对模式定义存在以下限制：

- 以下字段不可设置：
    
    - `definitions`
    - `dependencies`
    - `deprecated`
    - `discriminator`
    - `id`
    - `patternProperties`
    - `readOnly`
    - `writeOnly`
    - `xml`
    - `$ref`
- 字段 `uniqueItems` 不可设置为 `true`
    
- 字段 `additionalProperties` 不可设置为 `false`
    
- 字段 `additionalProperties` 与 `properties` 互斥，不可同时使用
    

当[验证规则特性](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation-rules)被启用并且 CustomResourceDefinition 模式是一个[结构化的模式定义](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema)时， `x-kubernetes-validations` 扩展可以使用[通用表达式语言 (CEL)](https://github.com/google/cel-spec)表达式来验证定制资源。

关于对某些 CustomResourceDefinition 特性所必需的限制， 可参见[结构化的模式定义](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema)小节。

模式定义是在 CustomResourceDefinition 中设置的。在下面的例子中， CustomResourceDefinition 对定制对象执行以下合法性检查：

- `spec.cronSpec` 必须是一个字符串，必须是正则表达式所描述的形式；
- `spec.replicas` 必须是一个整数，且其最小值为 1、最大值为 10。

将此 CustomResourceDefinition 保存到 `resourcedefinition.yaml` 文件中：

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: crontabs.stable.example.com
spec:
  group: stable.example.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        # openAPIV3Schema 是验证自定义对象的模式。
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                  pattern: '^(\d+|\*)(/\d+)?(\s+(\d+|\*)(/\d+)?){4}$'
                image:
                  type: string
                replicas:
                  type: integer
                  minimum: 1
                  maximum: 10
  scope: Namespaced
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
    shortNames:
    - ct
```

并创建 CustomResourceDefinition：

```shell
kubectl apply -f resourcedefinition.yaml
```

对于一个创建 CronTab 类别对象的定制对象的请求而言，如果其字段中包含非法值，则 该请求会被拒绝。 在下面的例子中，定制对象中包含带非法值的字段：

- `spec.cronSpec` 与正则表达式不匹配
- `spec.replicas` 数值大于 10。

如果你将下面的 YAML 保存到 `my-crontab.yaml`：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  cronSpec: "* * * *"
  image: my-awesome-cron-image
  replicas: 15
```

并尝试创建定制对象：

```shell
kubectl apply -f my-crontab.yaml
```

你会看到下面的错误信息：

```console
The CronTab "my-new-cron-object" is invalid: []: Invalid value: map[string]interface {}{"apiVersion":"stable.example.com/v1", "kind":"CronTab", "metadata":map[string]interface {}{"name":"my-new-cron-object", "namespace":"default", "deletionTimestamp":interface {}(nil), "deletionGracePeriodSeconds":(*int64)(nil), "creationTimestamp":"2017-09-05T05:20:07Z", "uid":"e14d79e7-91f9-11e7-a598-f0761cb232d1", "clusterName":""}, "spec":map[string]interface {}{"cronSpec":"* * * *", "image":"my-awesome-cron-image", "replicas":15}}:
validation failure list:
spec.cronSpec in body should match '^(\d+|\*)(/\d+)?(\s+(\d+|\*)(/\d+)?){4}$'
spec.replicas in body should be less than or equal to 10
```

如果所有字段都包含合法值，则对象创建的请求会被接受。

将下面的 YAML 保存到 `my-crontab.yaml` 文件：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  cronSpec: "* * * * */5"
  image: my-awesome-cron-image
  replicas: 5
```

并创建定制对象：

```shell
kubectl apply -f my-crontab.yaml
crontab "my-new-cron-object" created
```

#### 验证规则[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#%E9%AA%8C%E8%AF%81%E8%A7%84%E5%88%99)

**特性状态：** `Kubernetes v1.25 [beta]`

验证规则从 1.25 开始处于 Beta 状态， 默认情况下，`CustomResourceValidationExpressions` [特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/) 是被启用的，以便根据**验证规则**来验证定制资源。 对于 [`kube-apiserver`](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-apiserver/) 组件， 特性门控显式设置为 false 来禁用此特性。

这个特性只有在模式是[结构化的模式](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema)时才可用。

验证规则使用[通用表达式语言（CEL）](https://github.com/google/cel-spec)来验证定制资源的值。 验证规则使用 `x-kubernetes-validations` 扩展包含在 `CustomResourceDefinition` 模式定义中。

规则的作用域是模式定义中 `x-kubernetes-validations` 扩展所在的位置。 CEL 表达式中的 `self` 变量被绑定到限定作用域的取值。

所有验证规则都是针对当前对象的：不支持跨对象或有状态的验证规则。

例如:

```none
  ...
  openAPIV3Schema:
    type: object
    properties:
      spec:
        type: object
        x-kubernetes-validations:
          - rule: "self.minReplicas <= self.replicas"
            message: "replicas should be greater than or equal to minReplicas."
          - rule: "self.replicas <= self.maxReplicas"
            message: "replicas should be smaller than or equal to maxReplicas."
        properties:
          ...
          minReplicas:
            type: integer
          replicas:
            type: integer
          maxReplicas:
            type: integer
        required:
          - minReplicas
          - replicas
          - maxReplicas 
```

将拒绝创建这个定制资源的请求:

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  minReplicas: 0
  replicas: 20
  maxReplicas: 10
```

返回响应为：

```
The CronTab "my-new-cron-object" is invalid:
* spec: Invalid value: map[string]interface {}{"maxReplicas":10, "minReplicas":0, "replicas":20}: replicas should be smaller than or equal to maxReplicas.
```

`x-kubernetes-validations` 可以有多条规则。

`x-kubernetes-validations` 下的 `rule` 代表将由 CEL 评估的表达式。

`message` 代表验证失败时显示的信息。如果消息没有设置，上述响应将是：

```
The CronTab "my-new-cron-object" is invalid:
* spec: Invalid value: map[string]interface {}{"maxReplicas":10, "minReplicas":0, "replicas":20}: failed rule: self.replicas <= self.maxReplicas
```

当 CRD 被创建/更新时，验证规则被编译。 如果验证规则的编译失败，CRD 的创建/更新请求将失败。 编译过程也包括类型检查。

编译失败：

- `no_matching_overload`：此函数没有参数类型的重载。
    
    例如，像 `self == true` 这样的规则对一个整数类型的字段将得到错误：
    
    ```
    Invalid value: apiextensions.ValidationRule{Rule:"self == true", Message:""}: compilation failed: ERROR: \<input>:1:6: found no matching overload for '_==_' applied to '(int, bool)'
    ```
    

- `no_such_field`：不包含所需的字段。
    
    例如，针对一个不存在的字段，像 `self.nonExistingField > 0` 这样的规则将返回错误：
    
    ```
    Invalid value: apiextensions.ValidationRule{Rule:"self.nonExistingField > 0", Message:""}: compilation failed: ERROR: \<input>:1:5: undefined field 'nonExistingField'
    ```
    

- `invalid argument`：对宏的无效参数。
    
    例如，像 `has(self)` 这样的规则将返回错误：
    
    ```
    Invalid value: apiextensions.ValidationRule{Rule:"has(self)", Message:""}: compilation failed: ERROR: <input>:1:4: invalid argument to has() macro
    ```
    

验证规则例子：

| 规则 | 目的 |
| --- | --- |
| `self.minReplicas <= self.replicas && self.replicas <= self.maxReplicas` | 验证定义副本数的三个字段大小顺序是否正确 |
| `'Available' in self.stateCounts` | 验证 map 中是否存在键名为 `Available`的条目 |
| `(size(self.list1) == 0) != (size(self.list2) == 0)` | 验证两个 list 之一是非空的，但不是二者都非空 |
| `!('MY_KEY' in self.map1) || self['MY_KEY'].matches('^[a-zA-Z]*$')` | 如果某个特定的 key 在 map 中，验证 map 中这个 key 的 value |
| `self.envars.filter(e, e.name = 'MY_ENV').all(e, e.value.matches('^[a-zA-Z]*$')` | 验证一个 listMap 中主键 'name' 为 'MY\_ENV' 'value' 的表项，检查其取值 'value' |
| `has(self.expired) && self.created + self.ttl < self.expired` | 验证 'Expired' 日期是否晚于 'Create' 日期加上 'ttl' 持续时间 |
| `self.health.startsWith('ok')` | 验证 'health' 字符串字段有前缀 'ok' |
| `self.widgets.exists(w, w.key == 'x' && w.foo < 10)` | 验证 key 为 'x' 的 listMap 项的 'foo' 属性是否小于 10 |
| `type(self) == string ? self == '100%' : self == 1000` | 在 int 型和 string 型两种情况下验证 int-or-string 字段 |
| `self.metadata.name.startsWith(self.prefix)` | 验证对象的名称是否具有另一个字段值的前缀 |
| `self.set1.all(e, !(e in self.set2))` | 验证两个 listSet 是否不相交 |
| `size(self.names) == size(self.details) && self.names.all(n, n in self.details)` | 验证 'details' map 是由 'names' listSet 的项目所决定的。 |
| `size(self.clusters.filter(c, c.name == self.primary)) == 1` | 验证 'primary' 属性仅在 'clusters' listMap 中出现一次且只有一次 |

参考：[CEL 中支持的求值](https://github.com/google/cel-spec/blob/v0.6.0/doc/langdef.md#evaluation)

- 如果规则的作用域是某资源的根，则它可以对 CRD 的 OpenAPIv3 模式表达式中声明的任何字段进行字段选择， 以及 `apiVersion`、`kind`、`metadata.name` 和 `metadata.generateName`。 这包括在同一表达式中对 `spec` 和 `status` 的字段进行选择：
    
    ```none
      ...
      openAPIV3Schema:
        type: object
        x-kubernetes-validations:
          - rule: "self.status.availableReplicas >= self.spec.minReplicas"
        properties:
            spec:
              type: object
              properties:
                minReplicas:
                  type: integer
                ...
            status:
              type: object
              properties:
                availableReplicas:
                  type: integer
    ```
    

- 如果规则的作用域是具有属性的对象，那么可以通过 `self.field` 对该对象的可访问属性进行字段选择， 而字段存在与否可以通过 `has(self.field)` 来检查。 在 CEL 表达式中，Null 值的字段被视为不存在的字段。
    
    ```none
      ...
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            x-kubernetes-validations:
              - rule: "has(self.foo)"
            properties:
              ...
              foo:
                type: integer
    ```
    

- 如果规则的作用域是一个带有 additionalProperties 的对象（即map），那么 map 的值 可以通过 `self[mapKey]` 访问，map 的包含性可以通过 `mapKey in self` 检查， map 中的所有条目可以通过 CEL 宏和函数如 `self.all(...)` 访问。
    
    ```none
      ...
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            x-kubernetes-validations:
              - rule: "self['xyz'].foo > 0"
            additionalProperties:
              ...
              type: object
              properties:
                foo:
                  type: integer
    ```
    

- 如果规则的作用域是 array，则 array 的元素可以通过 `self[i]` 访问，也可以通过宏和函数访问。
    
    ```none
      ...
      openAPIV3Schema:
        type: object
        properties:
          ...
          foo:
            type: array
            x-kubernetes-validations:
              - rule: "size(self) == 1"
            items:
              type: string
    ```
    

- 如果规则的作用域为标量，则 `self` 将绑定到标量值。
    
    ```none
      ...
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              ...
              foo:
                type: integer
                x-kubernetes-validations:
                - rule: "self > 0"
    ```
    

例子：

| 规则作用域字段类型 | 规则示例 |
| --- | --- |
| 根对象 | `self.status.actual <= self.spec.maxDesired` |
| 对象映射 | `self.components['Widget'].priority < 10` |
| 整数列表 | `self.values.all(value, value >= 0 && value < 100)` |
| 字符串 | `self.startsWith('kube')` |

`apiVersion`、`kind`、`metadata.name` 和 `metadata.generateName` 始终可以从对象的根目录和任何带有 `x-kubernetes-embedded-resource` 注解的对象访问。 其他元数据属性都不可访问。

通过 `x-kubernetes-preserve-unknown-fields` 保存在定制资源中的未知数据在 CEL 表达中无法访问。 这包括：

- 使用 `x-kubernetes-preserve-unknown-fields` 的对象模式保留的未知字段值。
    
- 属性模式为"未知类型（Unknown Type）"的对象属性。一个"未知类型"被递归定义为：
    
    - 一个没有类型的模式，`x-kubernetes-preserve-unknown-fields` 设置为 true。
    - 一个数组，其中项目模式为"未知类型"
    - 一个 additionalProperties 模式为"未知类型"的对象

只有 `[a-zA-Z_.-/][a-zA-Z0-9_.-/]*` 形式的属性名是可访问的。 当在表达式中访问时，可访问的属性名称会根据以下规则进行转义：

| 转义序列 | 属性名称等效为 |
| --- | --- |
| `__underscores__` | `__` |
| `__dot__` | `.` |
| `__dash__` | `-` |
| `__slash__` | `/` |
| `__{keyword}__` | [CEL 保留关键字](https://github.com/google/cel-spec/blob/v0.6.0/doc/langdef.md#syntax) |

注意：CEL 保留关键字需要与要转义的确切属性名匹配(例如，单词 `sprint` 中的 `int` 不会转义)。

转义的例子：

| 属性名 | 转义属性名规则 |
| --- | --- |
| namespace | `self.__namespace__ > 0` |
| x-prop | `self.x__dash__prop > 0` |
| redact\_\_d | `self.redact__underscores__d > 0` |
| string | `self.startsWith('kube')` |

`set` 或 `map` 的 `x-Kubernetes-list-type` 的数组的等值比较会忽略元素顺序，即 `[1，2] == [2，1]`。 使用 `x-kubernetes-list-type` 对数组进行串联时，使用 List 类型的语义：

- `set`：`X + Y` 执行一个并集操作，其中 `X` 中所有元素的数组位置被保留， `Y` 中不相交的元素被追加，保留其部分顺序。
    
- `map`：`X + Y`执行合并，其中 `X` 中所有键的数组位置被保留， 但当 `X` 和 `Y` 的键集相交时，其值被 `Y` 中的值覆盖。 `Y` 中键值不相交的元素被附加，保留其部分顺序。
    

以下是 OpenAPIV3 和 CEL 类型之间的声明类型映射：

| OpenAPIv3 类型 | CEL 类型 |
| --- | --- |
| 带有 Properties 的对象 | 对象 / "消息类型" |
| 带有 AdditionalProperties 的对象 | map |
| 带有 x-kubernetes-embedded-type 的对象 | 对象 / "消息类型"，'apiVersion'、'kind'、'metadata.name' 和 'metadata.generateName' 都隐式包含在模式中 |
| 带有 x-kubernetes-preserve-unknown-fields 的对象 | 对象 / "消息类型"，未知字段无法从 CEL 表达式中访问 |
| x-kubernetes-int-or-string | 可能是整数或字符串的动态对象，可以用 `type(value)` 来检查类型 |
| 数组 | list |
| 带有 x-kubernetes-list-type=map 的数组 | 列表，基于集合等值和唯一键名保证的 map 组成 |
| 带有 x-kubernetes-list-type=set 的数组 | 列表，基于集合等值和唯一键名保证的 set 组成 |
| 布尔值 | boolean |
| 数字 (各种格式) | double |
| 整数 (各种格式) | int (64) |
| 'null' | null\_type |
| 字符串 | string |
| 带有 format=byte （base64 编码）字符串 | bytes |
| 带有 format=date 字符串 | timestamp (google.protobuf.Timestamp) |
| 带有 format=datetime 字符串 | timestamp (google.protobuf.Timestamp) |
| 带有 format=duration 字符串 | duration (google.protobuf.Duration) |

参考：[CEL 类型](https://github.com/google/cel-spec/blob/v0.6.0/doc/langdef.md#values)、 [OpenAPI 类型](https://swagger.io/specification/#data-types)、 [Kubernetes 结构化模式](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema)。

###### 验证函数[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#available-validation-functions)

可用的函数包括：

- CEL 标准函数，在[标准定义列表](https://github.com/google/cel-spec/blob/v0.7.0/doc/langdef.md#list-of-standard-definitions)中定义
- CEL 标准[宏](https://github.com/google/cel-spec/blob/v0.7.0/doc/langdef.md#macros)
- CEL [扩展字符串函数库](https://pkg.go.dev/github.com/google/cel-go@v0.11.2/ext#Strings)
- Kubernetes [CEL 扩展库](https://pkg.go.dev/k8s.io/apiextensions-apiserver@v0.24.0/pkg/apiserver/schema/cel/library#pkg-functions)

###### 转换规则[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#%E8%BD%AC%E6%8D%A2%E8%A7%84%E5%88%99)

包含引用标识符 `oldSself` 的表达式的规则被隐式视为 **转换规则（Transition Rule）**。 转换规则允许模式作者阻止两个原本有效的状态之间的某些转换。例如：

```yaml
type: string
enum: ["low", "medium", "high"]
x-kubernetes-validations:
- rule: "!(self == 'high' && oldSelf == 'low') && !(self == 'low' && oldSelf == 'high')"
  message: cannot transition directly between 'low' and 'high'
```

与其他规则不同，转换规则仅适用于满足以下条件的操作：

- 更新现有对象的操作。转换规则从不适用于创建操作。

- 旧的值和新的值都存在。仍然可以通过在父节点上放置转换规则来检查值是否已被添加或移除。 转换规则从不应用于定制资源创建。当被放置在可选字段上时，转换规则将不适用于设置或取消设置该字段的更新操作。

- 被转换规则验证的模式节点的路径必须解析到一个在旧对象和新对象之间具有可比性的节点。 例如，列表项和它们的后代（`spec.foo[10].bar`）不一定能在现有对象和后来对同一对象的更新之间产生关联。

如果一个模式节点包含一个永远不能应用的转换规则，在 CRD 写入时将会产生错误，例如： "_path_: update rule _rule_ cannot be set on schema because the schema or its parent schema is not mergeable"。

转换规则只允许在模式的 **可关联部分（Correlatable Portions）** 中使用。 如果所有 `array` 父模式都是 `x-kubernetes-list-type=map`类型的，那么该模式的一部分就是可关联的； 任何 `set` 或者 `atomic` 数组父模式都不支持确定性地将 `self` 与 `oldSelf` 关联起来。

这是一些转换规则的例子：

| 用例 | 规则 |
| --- | --- |
| 不可变 | `self.foo == oldSelf.foo` |
| 赋值后禁止修改/删除 | `oldSelf != 'bar' || self == 'bar'` or `!has(oldSelf.field) || has(self.field)` |
| 仅附加的 set | `self.all(element, element in oldSelf)` |
| 如果之前的值为 X，则新值只能为 A 或 B，不能为 Y 或 Z | `oldSelf != 'X' || self in ['A', 'B']` |
| 单调（非递减）计数器 | `self >= oldSelf` |

###### 验证函数的资源使用[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#%E9%AA%8C%E8%AF%81%E5%87%BD%E6%95%B0%E7%9A%84%E8%B5%84%E6%BA%90%E4%BD%BF%E7%94%A8)

当你创建或更新一个使用验证规则的 CustomResourceDefinition 时， API 服务器会检查运行这些验证规则可能产生的影响。 如果一个规则的执行成本过高，API 服务器会拒绝创建或更新操作，并返回一个错误信息。

运行时也使用类似的系统来观察解释器的行动。如果解释器执行了太多的指令，规则的执行将被停止，并且会产生一个错误。

每个 CustomResourceDefinition 也被允许有一定数量的资源来完成其所有验证规则的执行。 如果在创建时估计其规则的总和超过了这个限制，那么也会发生验证错误。

如果你只指定那些无论输入量有多大都要花费相同时间的规则，你不太可能遇到验证的资源预算问题。

例如，一个断言 `self.foo == 1` 的规则本身不存在因为资源预算组验证而导致被拒绝的风险。

但是，如果 `foo` 是一个字符串，而你定义了一个验证规则 `self.foo.contains("someString")`， 这个规则需要更长的时间来执行，取决于 `foo` 有多长。

另一个例子是如果 `foo` 是一个数组，而你指定了验证规则 `self.foo.all(x, x > 5)`。 如果没有给出 `foo` 的长度限制，成本系统总是假设最坏的情况，这将发生在任何可以被迭代的事物上（list、map 等）。

因此，通过 `maxItems`，`maxProperties` 和 `maxLength` 进行限制被认为是最佳实践， 以在验证规则中处理任何内容，以防止在成本估算期间验证错误。例如，给定具有一个规则的模式：

```yaml
openAPIV3Schema:
  type: object
  properties:
    foo:
      type: array
      items:
        type: string
      x-kubernetes-validations:
        - rule: "self.all(x, x.contains('a string'))"
```

API 服务器以验证预算为由拒绝该规则，并显示错误：

```
spec.validation.openAPIV3Schema.properties[spec].properties[foo].x-kubernetes-validations[0].rule: Forbidden: 
CEL rule exceeded budget by more than 100x (try simplifying the rule, or adding maxItems, maxProperties, and 
maxLength where arrays, maps, and strings are used)
```

这个拒绝会发生是因为 `self.all` 意味着对 `foo` 中的每一个字符串调用 `contains()`， 而这又会检查给定的字符串是否包含 `'a string'`。如果没有限制，这是一个非常昂贵的规则。

如果你不指定任何验证限制，这个规则的估计成本将超过每条规则的成本限制。 但如果你在适当的地方添加限制，该规则将被允许：

```yaml
openAPIV3Schema:
  type: object
  properties:
    foo:
      type: array
      maxItems: 25
      items:
        type: string
        maxLength: 10
      x-kubernetes-validations:
        - rule: "self.all(x, x.contains('a string'))"
```

成本评估系统除了考虑规则本身的估计成本外，还考虑到规则将被执行的次数。 例如，下面这个规则的估计成本与前面的例子相同（尽管该规则现在被定义在单个数组项上）：

```yaml
openAPIV3Schema:
  type: object
  properties:
    foo:
      type: array
      maxItems: 25
      items:
        type: string
        x-kubernetes-validations:
          - rule: "self.contains('a string'))"
        maxLength: 10
```

如果在一个列表内部的一个列表有一个使用 `self.all` 的验证规则，那就会比具有相同规则的非嵌套列表的成本高得多。 一个在非嵌套列表中被允许的规则可能需要在两个嵌套列表中设置较低的限制才能被允许。 例如，即使没有设置限制，下面的规则也是允许的：

```yaml
openAPIV3Schema:
  type: object
  properties:
    foo:
      type: array
      items:
        type: integer
    x-kubernetes-validations:
      - rule: "self.all(x, x == 5)"
```

但是同样的规则在下面的模式中（添加了一个嵌套数组）产生了一个验证错误：

```yaml
openAPIV3Schema:
  type: object
  properties:
    foo:
      type: array
      items:
        type: array
        items:
          type: integer
        x-kubernetes-validations:
          - rule: "self.all(x, x == 5)"
```

这是因为 `foo` 的每一项本身就是一个数组，而每一个子数组依次调用 `self.all`。 在使用验证规则的地方，尽可能避免嵌套的列表和字典。

##### 设置默认值[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#defaulting)

**说明：**

要使用设置默认值功能，你的 CustomResourceDefinition 必须使用 API 版本 `apiextensions.k8s.io/v1`。

设置默认值的功能允许在 [OpenAPI v3 合法性检查模式定义](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation)中设置默认值：

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: crontabs.stable.example.com
spec:
  group: stable.example.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        # openAPIV3Schema 是用来检查定制对象的模式定义
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                  pattern: '^(\d+|\*)(/\d+)?(\s+(\d+|\*)(/\d+)?){4}$'
                  default: "5 0 * * *"
                image:
                  type: string
                replicas:
                  type: integer
                  minimum: 1
                  maximum: 10
                  default: 1
  scope: Namespaced
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
    shortNames:
    - ct
```

使用此 CRD 定义时，`cronSpec` 和 `replicas` 都会被设置默认值：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  image: my-awesome-cron-image
```

会生成：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  cronSpec: "5 0 * * *"
  image: my-awesome-cron-image
  replicas: 1
```

默认值设定的行为发生在定制对象上：

- 在向 API 服务器发送的请求中，基于请求版本的设定设置默认值；
- 在从 etcd 读取对象时，使用存储版本来设置默认值；
- 在 Mutating 准入控制插件执行非空的补丁操作时，基于准入 Webhook 对象 版本设置默认值。

从 etcd 中读取数据时所应用的默认值设置不会被写回到 etcd 中。 需要通过 API 执行更新请求才能将这种方式设置的默认值写回到 etcd。

默认值一定会被剪裁（除了 `metadata` 字段的默认值设置），且必须通过所提供的模式定义的检查。

针对 `x-kubernetes-embedded-resource: true` 节点（或者包含 `metadata` 字段的结构的默认值） 的 `metadata` 字段的默认值设置不会在 CustomResourceDefinition 创建时被剪裁， 而是在处理请求的字段剪裁阶段被删除。

###### 设置默认值和字段是否可为空（Nullable）[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#defaulting-and-nullable)

对于未设置其 nullable 标志的字段或者将该标志设置为 `false` 的字段，其空值（Null） 会在设置默认值之前被剪裁掉。如果对应字段存在默认值，则默认值会被赋予该字段。 当 `nullable` 被设置为 `true` 时，字段的空值会被保留，且不会在设置默认值时被覆盖。

例如，给定下面的 OpenAPI 模式定义：

```yaml
type: object
properties:
  spec:
    type: object
    properties:
      foo:
        type: string
        nullable: false
        default: "default"
      bar:
        type: string
        nullable: true
      baz:
        type: string
```

像下面这样创建一个为 `foo`、`bar` 和 `baz` 设置空值的对象时：

```yaml
spec:
  foo: null
  bar: null
  baz: null
```

其结果会是这样：

```yaml
spec:
  foo: "default"
  bar: null
```

其中的 `foo` 字段被剪裁掉并重新设置默认值，因为该字段是不可为空的。 `bar` 字段的 `nullable: true` 使得其能够保有其空值。 `baz` 字段则被完全剪裁掉，因为该字段是不可为空的，并且没有默认值设置。

##### 以 OpenAPI v2 形式发布合法性检查模式[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#publish-validation-schema-in-openapi-v2)

CustomResourceDefinition 的[结构化的](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#specifying-a-structural-schema)、 [启用了剪裁的](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#field-pruning) [OpenAPI v3 合法性检查模式](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation)会在 Kubernetes API 服务器上作为 [OpenAPI v2 规约](https://kubernetes.io/zh-cn/docs/concepts/overview/kubernetes-api/#openapi-and-swagger-definitions)的一部分发布出来。

[kubectl](https://kubernetes.io/zh-cn/docs/reference/kubectl/) 命令行工具会基于所发布的模式定义来执行客户端的合法性检查 （`kubectl create` 和 `kubectl apply`），为定制资源的模式定义提供解释（`kubectl explain`）。 所发布的模式还可被用于其他目的，例如生成客户端或者生成文档。

OpenAPI v3 合法性检查模式定义会被转换为 OpenAPI v2 模式定义，并出现在 [OpenAPI v2 规范](https://kubernetes.io/zh-cn/docs/concepts/overview/kubernetes-api/#openapi-and-swagger-definitions)的 `definitions` 和 `paths` 字段中。

在转换过程中会发生以下修改，目的是保持与 1.13 版本以前的 kubectl 工具兼容。 这些修改可以避免 kubectl 过于严格，以至于拒绝它无法理解的 OpenAPI 模式定义。 转换过程不会更改 CRD 中定义的合法性检查模式定义，因此不会影响到 API 服务器中的[合法性检查](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation)。

1. 以下字段会被移除，因为它们在 OpenAPI v2 中不支持（在将来版本中将使用 OpenAPI v3， 因而不会有这些限制）
    
    - 字段 `allOf`、`anyOf`、`oneOf` 和 `not` 会被删除
2. 如果设置了 `nullable: true`，我们会丢弃 `type`、`nullable`、`items` 和 `properties` OpenAPI v2 无法表达 Nullable。为了避免 kubectl 拒绝正常的对象，这一转换是必要的。
    

##### 额外的打印列[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#additional-printer-columns)

`kubectl` 工具依赖服务器端的输出格式化。你的集群的 API 服务器决定 `kubectl get` 命令要显示的列有哪些。 你可以为 CustomResourceDefinition 定制这些要打印的列。 下面的例子添加了 `Spec`、`Replicas` 和 `Age` 列：

将此 CustomResourceDefinition 保存到 `resourcedefinition.yaml` 文件：

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: crontabs.stable.example.com
spec:
  group: stable.example.com
  scope: Namespaced
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
    shortNames:
    - ct
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              cronSpec:
                type: string
              image:
                type: string
              replicas:
                type: integer
    additionalPrinterColumns:
    - name: Spec
      type: string
      description: The cron spec defining the interval a CronJob is run
      jsonPath: .spec.cronSpec
    - name: Replicas
      type: integer
      description: The number of jobs launched by the CronJob
      jsonPath: .spec.replicas
    - name: Age
      type: date
      jsonPath: .metadata.creationTimestamp
```

创建 CustomResourceDefinition：

```shell
kubectl apply -f resourcedefinition.yaml
```

使用前文中的 `my-crontab.yaml` 创建一个实例。

启用服务器端打印输出：

```shell
kubectl get crontab my-new-cron-object
```

注意输出中的 `NAME`、`SPEC`、`REPLICAS` 和 `AGE` 列：

```
NAME                 SPEC        REPLICAS   AGE
my-new-cron-object   * * * * *   1          7s
```

**说明：**

`NAME` 列是隐含的，不需要在 CustomResourceDefinition 中定义。

###### 优先级[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#priority)

每个列都包含一个 `priority`（优先级）字段。当前，优先级用来区分标准视图（Standard View）和宽视图（Wide View）（使用 `-o wide` 标志）中显示的列：

- 优先级为 `0` 的列会在标准视图中显示。
- 优先级大于 `0` 的列只会在宽视图中显示。

###### 类型[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#type)

列的 `type` 字段可以是以下值之一 （比较 [OpenAPI v3 数据类型](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md#dataTypes)）：

- `integer` – 非浮点数字
- `number` – 浮点数字
- `string` – 字符串
- `boolean` – `true` 或 `false`
- `date` – 显示为以自此时间戳以来经过的时长

如果定制资源中的值与列中指定的类型不匹配，该值会被忽略。 你可以通过定制资源的合法性检查来确保取值类型是正确的。

###### 格式[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#format)

列的 `format` 字段可以是以下值之一：

- `int32`
- `int64`
- `float`
- `double`
- `byte`
- `date`
- `date-time`
- `password`

列的 `format` 字段控制 `kubectl` 打印对应取值时采用的风格。

##### 子资源[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#subresources)

定制资源支持 `/status` 和 `/scale` 子资源。

通过在 CustomResourceDefinition 中定义 `status` 和 `scale`， 可以有选择地启用这些子资源。

###### Status 子资源[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#status-subresource)

当启用了 status 子资源时，对应定制资源的 `/status` 子资源会被暴露出来。

- status 和 spec 内容分别用定制资源内的 `.status` 和 `.spec` JSON 路径来表达；
    
- 对 `/status` 子资源的 `PUT` 请求要求使用定制资源对象作为其输入，但会忽略 status 之外的所有内容。
    
- 对 `/status` 子资源的 `PUT` 请求仅对定制资源的 status 内容进行合法性检查。
    
- 对定制资源的 `PUT`、`POST`、`PATCH` 请求会忽略 status 内容的改变。
    
- 对所有变更请求，除非改变是针对 `.metadata` 或 `.status`，`.metadata.generation` 的取值都会增加。
    
- 在 CRD OpenAPI 合法性检查模式定义的根节点，只允许存在以下结构：
    
    - `description`
    - `example`
    - `exclusiveMaximum`
    - `exclusiveMinimum`
    - `externalDocs`
    - `format`
    - `items`
    - `maximum`
    - `maxItems`
    - `maxLength`
    - `minimum`
    - `minItems`
    - `minLength`
    - `multipleOf`
    - `pattern`
    - `properties`
    - `required`
    - `title`
    - `type`
    - `uniqueItems`

###### Scale 子资源[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#scale-subresource)

当启用了 scale 子资源时，定制资源的 `/scale` 子资源就被暴露出来。 针对 `/scale` 所发送的对象是 `autoscaling/v1.Scale`。

为了启用 scale 子资源，CustomResourceDefinition 定义了以下字段：

- `specReplicasPath` 指定定制资源内与 `scale.spec.replicas` 对应的 JSON 路径。
    
    - 此字段为必需值。
    - 只可以使用 `.spec` 下的 JSON 路径，只可使用带句点的路径。
    - 如果定制资源的 `specReplicasPath` 下没有取值，则针对 `/scale` 子资源执行 GET 操作时会返回错误。

- `statusReplicasPath` 指定定制资源内与 `scale.status.replicas` 对应的 JSON 路径。
    
    - 此字段为必需值。
    - 只可以使用 `.status` 下的 JSON 路径，只可使用带句点的路径。
    - 如果定制资源的 `statusReplicasPath` 下没有取值，则针对 `/scale` 子资源的副本个数状态值默认为 0。

- `labelSelectorPath` 指定定制资源内与 `Scale.Status.Selector` 对应的 JSON 路径。
    
    - 此字段为可选值。
    - 此字段必须设置才能使用 HPA。
    - 只可以使用 `.status` 或 `.spec` 下的 JSON 路径，只可使用带句点的路径。
    - 如果定制资源的 `labelSelectorPath` 下没有取值，则针对 `/scale` 子资源的选择算符状态值默认为空字符串。
    - 此 JSON 路径所指向的字段必须是一个字符串字段（而不是复合的选择算符结构）， 其中包含标签选择算符串行化的字符串形式。

在下面的例子中，`status` 和 `scale` 子资源都被启用。

将此 CustomResourceDefinition 保存到 `resourcedefinition.yaml` 文件：

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: crontabs.stable.example.com
spec:
  group: stable.example.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
            status:
              type: object
              properties:
                replicas:
                  type: integer
                labelSelector:
                  type: string
      # subresources 描述定制资源的子资源
      subresources:
        # status 启用 status 子资源
        status: {}
        # scale 启用 scale 子资源
        scale:
          # specReplicasPath 定义定制资源中对应 scale.spec.replicas 的 JSON 路径
          specReplicasPath: .spec.replicas
          # statusReplicasPath 定义定制资源中对应 scale.status.replicas 的 JSON 路径 
          statusReplicasPath: .status.replicas
          # labelSelectorPath  定义定制资源中对应 scale.status.selector 的 JSON 路径 
          labelSelectorPath: .status.labelSelector
  scope: Namespaced
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
    shortNames:
    - ct
```

之后创建此 CustomResourceDefinition：

```shell
kubectl apply -f resourcedefinition.yaml
```

CustomResourceDefinition 对象创建完毕之后，你可以创建定制对象，。

如果你将下面的 YAML 保存到 `my-crontab.yaml` 文件：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  cronSpec: "* * * * */5"
  image: my-awesome-cron-image
  replicas: 3
```

并创建定制对象：

```shell
kubectl apply -f my-crontab.yaml
```

那么会创建新的、命名空间作用域的 RESTful API 端点：

```none
/apis/stable.example.com/v1/namespaces/*/crontabs/status
```

和

```none
/apis/stable.example.com/v1/namespaces/*/crontabs/scale
```

定制资源可以使用 `kubectl scale` 命令来扩缩其规模。 例如下面的命令将前面创建的定制资源的 `.spec.replicas` 设置为 5：

```shell
kubectl scale --replicas=5 crontabs/my-new-cron-object
crontabs "my-new-cron-object" scaled

kubectl get crontabs my-new-cron-object -o jsonpath='{.spec.replicas}'
5
```

你可以使用 [PodDisruptionBudget](https://kubernetes.io/zh-cn/docs/tasks/run-application/configure-pdb/) 来保护启用了 scale 子资源的定制资源。

##### 分类[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#categories)

分类（Categories）是定制资源所归属的分组资源列表（例如，`all`）。 你可以使用 `kubectl get <分类名称>` 来列举属于某分类的所有资源。

下面的示例在 CustomResourceDefinition 中将 `all` 添加到分类列表中， 并展示了如何使用 `kubectl get all` 来输出定制资源：

将下面的 CustomResourceDefinition 保存到 `resourcedefinition.yaml` 文件中：

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: crontabs.stable.example.com
spec:
  group: stable.example.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
  scope: Namespaced
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
    shortNames:
    - ct
    # categories 是定制资源所归属的分类资源列表
    categories:
    - all
```

之后创建此 CRD：

```shell
kubectl apply -f resourcedefinition.yaml
```

创建了 CustomResourceDefinition 对象之后，你可以创建定制对象。

将下面的 YAML 保存到 `my-crontab.yaml` 中：

```yaml
apiVersion: "stable.example.com/v1"
kind: CronTab
metadata:
  name: my-new-cron-object
spec:
  cronSpec: "* * * * */5"
  image: my-awesome-cron-image
```

并创建定制对象：

```shell
kubectl apply -f my-crontab.yaml
```

你可以在使用 `kubectl get` 时指定分类：

```shell
kubectl get all
```

输出中将包含类别为 `CronTab` 的定制资源：

```none
NAME                          AGE
crontabs/my-new-cron-object   3s
```

#### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 阅读了解[定制资源](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/api-extension/custom-resources/)
- 参阅 [CustomResourceDefinition](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#customresourcedefinition-v1-apiextensions-k8s-io)
- 参阅支持 CustomResourceDefinition 的[多个版本](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/)

### CustomResourceDefinition 的版本

本页介绍如何添加版本信息到 [CustomResourceDefinitions](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/extend-resources/custom-resource-definition-v1/)。 目的是标明 CustomResourceDefinitions 的稳定级别或者服务于 API 升级。 API 升级时需要在不同 API 表示形式之间进行转换。 本页还描述如何将对象从一个版本升级到另一个版本。

#### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你应该对[定制资源](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/api-extension/custom-resources/)有一些初步了解。

你的 Kubernetes 服务器版本必须不低于版本 v1.16. 要获知版本信息，请输入 `kubectl version`.

#### 概览[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#overview)

CustomResourceDefinition API 提供了引入和升级 CustomResourceDefinition 新版本所用的工作流程。

创建 CustomResourceDefinition 时，会在 CustomResourceDefinition `spec.versions` 列表设置适当的稳定级别和版本号。例如，`v1beta1` 表示第一个版本尚未稳定。 所有定制资源对象将首先用这个版本保存。

创建 CustomResourceDefinition 后，客户端可以开始使用 `v1beta1` API。

稍后可能需要添加新版本，例如 `v1`。

添加新版本：

1. 选择一种转化策略。由于定制资源对象需要能够两种版本都可用， 这意味着它们有时会以与存储版本不同的版本来提供服务。为了能够做到这一点， 有时必须在它们存储的版本和提供的版本之间进行转换。如果转换涉及模式变更， 并且需要自定义逻辑，则应该使用 Webhook 来完成。如果没有模式变更， 则可使用默认的 `None` 转换策略，为不同版本提供服务时只有 `apiVersion` 字段会被改变。
2. 如果使用转换 Webhook，请创建并部署转换 Webhook。更多详细信息请参见 [Webhook 转换](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#webhook-conversion)。
3. 更新 CustomResourceDefinition，将新版本设置为 `served：true`，加入到 `spec.versions` 列表。另外，还要设置 `spec.conversion` 字段为所选的转换策略。 如果使用转换 Webhook，请配置 `spec.conversion.webhookClientConfig` 来调用 Webhook。

添加新版本后，客户端可以逐步迁移到新版本。 让某些客户使用旧版本的同时支持其他人使用新版本是相当安全的。

将存储的对象迁移到新版本：

1. 请参阅[将现有对象升级到新的存储版本](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#upgrade-existing-objects-to-a-new-stored-version)节。
    
    对于客户来说，在将对象升级到新的存储版本之前、期间和之后使用旧版本和新版本都是安全的。
    

删除旧版本：

1. 确保所有客户端都已完全迁移到新版本。 可以查看 kube-apiserver 的日志以识别仍通过旧版本进行访问的所有客户端。
2. 在 `spec.versions` 列表中将旧版本的 `served` 设置为 `false`。 如果仍有客户端意外地使用旧版本，他们可能开始会报告采用旧版本尝试访问定制资源的错误消息。 如果发生这种情况，请将旧版本的 `served：true` 恢复，然后迁移余下的客户端使用新版本，然后重复此步骤。
3. 确保已完成[将现有对象升级到新存储版本](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#upgrade-existing-objects-to-a-new-stored-version)的步骤。
    1. 在 CustomResourceDefinition 的 `spec.versions` 列表中，确认新版本的 `storage` 已被设置为 `true`。
    2. 确认旧版本不在 CustomResourceDefinition `status.storedVersions` 中。
4. 从 CustomResourceDefinition `spec.versions` 列表中删除旧版本。
5. 在转换 Webhooks 中放弃对旧版本的转换支持。

#### 指定多个版本[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#specify-multiple-versions)

CustomResourceDefinition API 的 `versions` 字段可用于支持你所开发的定制资源的多个版本。 版本可以具有不同的模式，并且转换 Webhook 可以在多个版本之间转换定制资源。 在适当的情况下，Webhook 转换应遵循 [Kubernetes API 约定](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api-conventions.md)。 具体来说，请查阅 [API 变更文档](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api_changes.md) 以获取一些有用的问题和建议。

**说明：**

在 `apiextensions.k8s.io/v1beta1` 版本中曾经有一个 `version` 字段， 名字不叫做 `versions`。该 `version` 字段已经被废弃，成为可选项。 不过如果该字段不是空，则必须与 `versions` 字段中的第一个条目匹配。

下面的示例显示了两个版本的 CustomResourceDefinition。 第一个例子中假设所有的版本使用相同的模式而它们之间没有转换。 YAML 中的注释提供了更多背景信息。

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-1-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-1-1)

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  # name 必须匹配后面 spec 中的字段，且使用格式 <plural>.<group>
  name: crontabs.example.com
spec:
  # 组名，用于 REST API: /apis/<group>/<version>
  group: example.com
  # 此 CustomResourceDefinition 所支持的版本列表
  versions:
  - name: v1beta1
    # 每个 version 可以通过 served 标志启用或禁止
    served: true
    # 有且只能有一个 version 必须被标记为存储版本
    storage: true
    # schema 是必需字段
    schema:
      openAPIV3Schema:
        type: object
        properties:
          host:
            type: string
          port:
            type: string
  - name: v1
    served: true
    storage: false
    schema:
      openAPIV3Schema:
        type: object
        properties:
          host:
            type: string
          port:
            type: string
  # conversion 节是 Kubernetes 1.13+ 版本引入的，其默认值为无转换，即 strategy 子字段设置为 None。
  conversion:
    # None 转换假定所有版本采用相同的模式定义，仅仅将定制资源的 apiVersion 设置为合适的值.
    strategy: None
  # 可以是 Namespaced 或 Cluster
  scope: Namespaced
  names:
    # 名称的复数形式，用于 URL: /apis/<group>/<version>/<plural>
    plural: crontabs
    # 名称的单数形式，用于在命令行接口和显示时作为其别名
    singular: crontab
    # kind 通常是驼峰编码（CamelCased）的单数形式，用于资源清单中
    kind: CronTab
    # shortNames 允许你在命令行接口中使用更短的字符串来匹配你的资源
    shortNames:
    - ct
```

```yaml
# 在 v1.16 中被弃用以推荐使用 apiextensions.k8s.io/v1
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  # name 必须匹配后面 spec 中的字段，且使用格式 <plural>.<group>
  name: crontabs.example.com
spec:
  # 组名，用于 REST API: /apis/<group>/<version>
  group: example.com
  # 此 CustomResourceDefinition 所支持的版本列表
  versions:
  - name: v1beta1
    # 每个 version 可以通过 served 标志启用或禁止
    served: true
    # 有且只能有一个 version 必须被标记为存储版本
    storage: true
  - name: v1
    served: true
    storage: false
  validation:
    openAPIV3Schema:
      type: object
      properties:
        host:
          type: string
        port:
          type: string
  # conversion 节是 Kubernetes 1.13+ 版本引入的，其默认值为无转换，即 strategy 子字段设置为 None。
  conversion:
    # None 转换假定所有版本采用相同的模式定义，仅仅将定制资源的 apiVersion 设置为合适的值.
    strategy: None
  # 可以是 Namespaced 或 Cluster
  scope: Namespaced
  names:
    # 名称的复数形式，用于 URL: /apis/<group>/<version>/<plural>
    plural: crontabs
    # 名称的单数形式，用于在命令行接口和显示时作为其别名
    singular: crontab
    # kind 通常是大驼峰编码（PascalCased）的单数形式，用于资源清单中
    kind: CronTab
    # shortNames 允许你在命令行接口中使用更短的字符串来匹配你的资源
    shortNames:
    - ct
```

你可以将 CustomResourceDefinition 存储在 YAML 文件中，然后使用 `kubectl apply` 来创建它。

```shell
kubectl apply -f my-versioned-crontab.yaml
```

在创建之后，API 服务器开始在 HTTP REST 端点上为每个已启用的版本提供服务。 在上面的示例中，API 版本可以在 `/apis/example.com/v1beta1` 和 `/apis/example.com/v1` 处获得。

##### 版本优先级[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#version-priority)

不考虑 CustomResourceDefinition 中版本被定义的顺序，kubectl 使用具有最高优先级的版本作为访问对象的默认版本。 优先级是通过解析 **name** 字段来确定版本号、稳定性（GA、Beta 或 Alpha） 以及该稳定性级别内的序列。

用于对版本进行排序的算法在设计上与 Kubernetes 项目对 Kubernetes 版本进行排序的方式相同。 版本以 `v` 开头跟一个数字，一个可选的 `beta` 或者 `alpha` 和一个可选的附加数字作为版本信息。 从广义上讲，版本字符串可能看起来像 `v2` 或者 `v2beta1`。 使用以下算法对版本进行排序：

- 遵循 Kubernetes 版本模式的条目在不符合条件的条目之前进行排序。
- 对于遵循 Kubernetes 版本模式的条目，版本字符串的数字部分从最大到最小排序。
- 如果第一个数字后面有字符串 `beta` 或 `alpha`，它们首先按去掉 `beta` 或 `alpha` 之后的版本号排序（相当于 GA 版本），之后按 `beta` 先、`alpha` 后的顺序排序，
- 如果 `beta` 或 `alpha` 之后还有另一个数字，那么也会针对这些数字从大到小排序。
- 不符合上述格式的字符串按字母顺序排序，数字部分不经过特殊处理。 请注意，在下面的示例中，`foo1` 排在 `foo10` 之前。 这与遵循 Kubernetes 版本模式的条目的数字部分排序不同。

如果查看以下版本排序列表，这些规则就容易懂了：

```none
- v10
- v2
- v1
- v11beta2
- v10beta3
- v3beta1
- v12alpha1
- v11alpha2
- foo1
- foo10
```

对于[指定多个版本](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#specify-multiple-versions)中的示例，版本排序顺序为 `v1`，后跟着 `v1beta1`。 这导致了 kubectl 命令使用 `v1` 作为默认版本，除非所提供的对象指定了版本。

##### 版本废弃[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#version-deprecation)

**特性状态：** `Kubernetes v1.19 [stable]`

从 v1.19 开始，CustomResourceDefinition 可以指示其定义的资源的特定版本已废弃。 当该资源的已废弃版本发出 API 请求时，会在 API 响应中以报头的形式返回警告消息。 如果需要，可以自定义每个不推荐使用的资源版本的警告消息。

定制的警告消息应该标明废弃的 API 组、版本和类别（kind）， 并且应该标明应该使用（如果有的话）哪个 API 组、版本和类别作为替代。

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-deprecated-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-deprecated-1)

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
  name: crontabs.example.com
spec:
  group: example.com
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
  scope: Namespaced
  versions:
  - name: v1alpha1
    served: true
    storage: false
    # 此属性标明此定制资源的 v1alpha1 版本已被弃用。
    # 发给此版本的 API 请求会在服务器响应中收到警告消息头。
    deprecated: true
    # 此属性设置用来覆盖返回给发送 v1alpha1 API 请求的客户端的默认警告信息。
    deprecationWarning: "example.com/v1alpha1 CronTab is deprecated; see http://example.com/v1alpha1-v1 for instructions to migrate to example.com/v1 CronTab"
    schema: ...
  - name: v1beta1
    served: true
    # 此属性标明该定制资源的 v1beta1 版本已被弃用。
    # 发给此版本的 API 请求会在服务器响应中收到警告消息头。
    # 针对此版本的请求所返回的是默认的警告消息。
    deprecated: true
    schema: ...
  - name: v1
    served: true
    storage: true
    schema: ...
```

```yaml
# 在 v1.16 中弃用以推荐使用  apiextensions.k8s.io/v1
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: crontabs.example.com
spec:
  group: example.com
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
  scope: Namespaced
  validation: ...
  versions:
  - name: v1alpha1
    served: true
    storage: false
    # 此属性标明此定制资源的 v1alpha1 版本已被弃用。
    # 发给此版本的 API 请求会在服务器响应中收到警告消息头。
    deprecated: true
    # 此属性设置用来覆盖返回给发送 v1alpha1 API 请求的客户端的默认警告信息。
    deprecationWarning: "example.com/v1alpha1 CronTab is deprecated; see http://example.com/v1alpha1-v1 for instructions to migrate to example.com/v1 CronTab"
  - name: v1beta1
    served: true
    # 此属性标明该定制资源的 v1beta1 版本已被弃用。
    # 发给此版本的 API 请求会在服务器响应中收到警告消息头。
    # 针对此版本的请求所返回的是默认的警告消息。
    deprecated: true
  - name: v1
    served: true
    storage: true
```

##### 版本删除[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#version-removal)

在为所有提供旧版本自定义资源的集群将现有存储数据迁移到新 API 版本，并且从 CustomResourceDefinition 的 `status.storedVersions` 中删除旧版本之前，无法从 CustomResourceDefinition 清单文件中删除旧 API 版本。

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
  name: crontabs.example.com
spec:
  group: example.com
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
  scope: Namespaced
  versions:
  - name: v1beta1
    # 此属性标明该自定义资源的 v1beta1 版本已不再提供。
    # 发给此版本的 API 请求会在服务器响应中收到未找到的错误。
    served: false
    schema: ...
  - name: v1
    served: true
    # 新提供的版本应该设置为存储版本。
    storage: true
    schema: ...
```

#### Webhook 转换[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#webhook-conversion)

**特性状态：** `Kubernetes v1.16 [stable]`

**说明：**

Webhook 转换在 Kubernetes 1.13 版本作为 Alpha 功能引入，在 Kubernetes 1.15 版本中成为 Beta 功能。 要使用此功能，应启用 `CustomResourceWebhookConversion` 特性。 在大多数集群上，这类 Beta 特性应该是自动启用的。 请参阅[特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/)文档以获得更多信息。

上面的例子在版本之间有一个 None 转换，它只在转换时设置 `apiVersion` 字段而不改变对象的其余部分。 API 服务器还支持在需要转换时调用外部服务的 webhook 转换。例如：

- 定制资源的请求版本与其存储版本不同。
- 使用某版本创建了 Watch 请求，但所更改对象以另一版本存储。
- 定制资源的 PUT 请求所针对版本与存储版本不同。

为了涵盖所有这些情况并优化 API 服务器所作的转换，转换请求可以包含多个对象， 以便减少外部调用。Webhook 应该独立执行各个转换。

##### 编写一个转换 Webhook 服务器[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#write-a-conversion-webhook-server)

请参考[定制资源转换 Webhook 服务器](https://github.com/kubernetes/kubernetes/tree/v1.25.3/test/images/agnhost/crd-conversion-webhook/main.go)的实现； 该实现在 Kubernetes e2e 测试中得到验证。 Webhook 处理由 API 服务器发送的 `ConversionReview` 请求，并在 `ConversionResponse` 中封装发回转换结果。 请注意，请求包含需要独立转换的定制资源列表，这些对象在被转换之后不能改变其在列表中的顺序。 该示例服务器的组织方式使其可以复用于其他转换。大多数常见代码都位于 [framework 文件](https://github.com/kubernetes/kubernetes/tree/v1.25.3/test/images/agnhost/crd-conversion-webhook/converter/framework.go)中， 只留下[一个函数](https://github.com/kubernetes/kubernetes/tree/v1.25.3/test/images/agnhost/crd-conversion-webhook/converter/example_converter.go#L29-L80)用于实现不同的转换。

**说明：**

转换 Webhook 服务器示例中将 `ClientAuth` 字段设置为[空](https://github.com/kubernetes/kubernetes/tree/v1.25.3/test/images/agnhost/crd-conversion-webhook/config.go#L47-L48)， 默认为 `NoClientCert`。 这意味着 webhook 服务器没有验证客户端（也就是 API 服务器）的身份。 如果你需要双向 TLS 或者其他方式来验证客户端， 请参阅如何[验证 API 服务](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/extensible-admission-controllers/#authenticate-apiservers)。

###### 被允许的变更[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#%E8%A2%AB%E5%85%81%E8%AE%B8%E7%9A%84%E5%8F%98%E6%9B%B4)

转换 Webhook 不可以更改被转换对象的 `metadata` 中除 `labels` 和 `annotations` 之外的任何属性。 尝试更改 `name`、`UID` 和 `namespace` 时都会导致引起转换的请求失败。 所有其他变更都被忽略。

##### 部署转换 Webhook 服务[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#deploy-the-conversion-webhook-service)

用于部署转换 Webhook 的文档与[准入 Webhook 服务示例](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/extensible-admission-controllers/#deploy_the_admission_webhook_service)相同。 这里的假设是转换 Webhook 服务器被部署为 `default` 名字空间中名为 `example-conversion-webhook-server` 的服务，并在路径 `/crdconvert` 上处理请求。

**说明：**

当 Webhook 服务器作为一个服务被部署到 Kubernetes 集群中时，它必须通过端口 443 公开其服务（服务器本身可以使用任意端口，但是服务对象应该将它映射到端口 443）。 如果为服务器使用不同的端口，则 API 服务器和 Webhook 服务器之间的通信可能会失败。

##### 配置 CustomResourceDefinition 以使用转换 Webhook[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#configure-crd-to-use-conversion-webhooks)

通过修改 `spec` 中的 `conversion` 部分，可以扩展 `None` 转换示例来使用转换 Webhook。

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-2-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-2-1)

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  # name 必须匹配后面 spec 中的字段，且使用格式 <plural>.<group>
  name: crontabs.example.com
spec:
  # 组名，用于 REST API: /apis/<group>/<version>
  group: example.com
  # 此 CustomResourceDefinition 所支持的版本列表
  versions:
  - name: v1beta1
    # 每个 version 可以通过 served 标志启用或禁止
    served: true
    # 有且只能有一个 version 必须被标记为存储版本
    storage: true
    # 当不存在顶级模式定义时，每个版本（version）可以定义其自身的模式
    schema:
      openAPIV3Schema:
        type: object
        properties:
          hostPort:
            type: string
  - name: v1
    served: true
    storage: false
    schema:
      openAPIV3Schema:
        type: object
        properties:
          host:
            type: string
          port:
            type: string
  conversion:
    # Webhook strategy 告诉 API 服务器调用外部 Webhook 来完成定制资源之间的转换
    strategy: Webhook
    # 当 strategy 为 "Webhook" 时，webhook 属性是必需的，该属性配置将被 API 服务器调用的 Webhook 端点
    webhook:
      # conversionReviewVersions 标明 Webhook 所能理解或偏好使用的
      # ConversionReview 对象版本。
      # API 服务器所能理解的列表中的第一个版本会被发送到 Webhook
      # Webhook 必须按所接收到的版本响应一个 ConversionReview 对象
      conversionReviewVersions: ["v1","v1beta1"]
      clientConfig:
        service:
          namespace: default
          name: example-conversion-webhook-server
          path: /crdconvert
        caBundle: "Ci0tLS0tQk...<base64-encoded PEM bundle>...tLS0K"
  # 可以是 Namespaced 或 Cluster
  scope: Namespaced
  names:
    # 名称的复数形式，用于 URL: /apis/<group>/<version>/<plural>
    plural: crontabs
    # 名称的单数形式，用于在命令行接口和显示时作为其别名
    singular: crontab
    # kind 通常是驼峰编码（CamelCased）的单数形式，用于资源清单中
    kind: CronTab
    # shortNames 允许你在命令行接口中使用更短的字符串来匹配你的资源
    shortNames:
    - ct
```

```yaml
# 在 v1.16 中被弃用以推荐使用 apiextensions.k8s.io/v1
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  # name 必须匹配后面 spec 中的字段，且使用格式 <plural>.<group>
  name: crontabs.example.com
spec:
  # 组名，用于 REST API: /apis/<group>/<version>
  group: example.com
  # 裁剪掉下面的 OpenAPI 模式中未曾定义的对象字段
  preserveUnknownFields: false
  # 此 CustomResourceDefinition 所支持的版本列表
  versions:
  - name: v1beta1
    # 每个 version 可以通过 served 标志启用或禁止
    served: true
    # 有且只能有一个 version 必须被标记为存储版本
    storage: true
    # 当不存在顶级模式定义时，每个版本（version）可以定义其自身的模式
    schema:
      openAPIV3Schema:
        type: object
        properties:
          hostPort:
            type: string
  - name: v1
    served: true
    storage: false
    schema:
      openAPIV3Schema:
        type: object
        properties:
          host:
            type: string
          port:
            type: string
  conversion:
    # Webhook strategy 告诉 API 服务器调用外部 Webhook 来完成定制资源
    strategy: Webhook
    # 当 strategy 为 "Webhook" 时，webhookClientConfig 属性是必需的
    # 该属性配置将被 API 服务器调用的 Webhook 端点
    webhookClientConfig:
      service:
        namespace: default
        name: example-conversion-webhook-server
        path: /crdconvert
      caBundle: "Ci0tLS0tQk...<base64-encoded PEM bundle>...tLS0K"
  # 可以是 Namespaced 或 Cluster
  scope: Namespaced
  names:
    # 名称的复数形式，用于 URL: /apis/<group>/<version>/<plural>
    plural: crontabs
    # 名称的单数形式，用于在命令行接口和显示时作为其别名
    singular: crontab
    # kind 通常是驼峰编码（CamelCased）的单数形式，用于资源清单中
    kind: CronTab
    # shortNames 允许你在命令行接口中使用更短的字符串来匹配你的资源
    shortNames:
    - ct
```

你可以将 CustomResourceDefinition 保存在 YAML 文件中，然后使用 `kubectl apply` 来应用它。

```shell
kubectl apply -f my-versioned-crontab-with-conversion.yaml
```

在应用新更改之前，请确保转换服务器已启动并正在运行。

##### 调用 Webhook[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#contacting-the-webhook)

API 服务器一旦确定请求应发送到转换 Webhook，它需要知道如何调用 Webhook。 这是在 `webhookClientConfig` 中指定的 Webhook 配置。

转换 Webhook 可以通过 URL 或服务引用来调用，并且可以选择包含自定义 CA 包， 以用于验证 TLS 连接。

##### URL[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#url)

`url` 以标准 URL 形式给出 Webhook 的位置（`scheme://host:port/path`）。 `host` 不应引用集群中运行的服务，而应通过指定 `service` 字段来提供服务引用。 在某些 API 服务器中，`host` 可以通过外部 DNS 进行解析（即 `kube-apiserver` 无法解析集群内 DNS，那样会违反分层规则）。 `host` 也可以是 IP 地址。

请注意，除非你非常小心地在所有运行着可能调用 Webhook 的 API 服务器的主机上运行此 Webhook， 否则将 `localhost` 或 `127.0.0.1` 用作 `host` 是风险很大的。 这样的安装可能是不可移植的，或者不容易在一个新的集群中运行。

HTTP 协议必须为 `https`；URL 必须以 `https://` 开头。

尝试使用用户或基本身份验证（例如，使用 `user:password@`）是不允许的。 URL 片段（`#...`）和查询参数（`?...`）也是不允许的。

下面是为调用 URL 来执行转换 Webhook 的示例，其中期望使用系统信任根来验证 TLS 证书，因此未指定 caBundle：

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-3-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-3-1)

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
...
spec:
  ...
  conversion:
    strategy: Webhook
    webhook:
      clientConfig:
        url: "https://my-webhook.example.com:9443/my-webhook-path"
...
```

```yaml
# 在 v1.16 中已弃用以推荐使用 apiextensions.k8s.io/v1
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
...
spec:
  ...
  conversion:
    strategy: Webhook
    webhookClientConfig:
      url: "https://my-webhook.example.com:9443/my-webhook-path"
...
```

##### 服务引用[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#service-reference)

`webhookClientConfig` 内部的 `service` 段是对转换 Webhook 服务的引用。 如果 Webhook 在集群中运行，则应使用 `service` 而不是 `url`。 服务的名字空间和名称是必需的。端口是可选的，默认为 443。 路径是可选的，默认为`/`。

下面配置中，服务配置为在端口 `1234`、子路径 `/my-path` 上被调用。 例子中针对 ServerName `my-service-name.my-service-namespace.svc`， 使用自定义 CA 包验证 TLS 连接。

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-4-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#customresourcedefinition-versioning-example-4-1)

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
...
spec:
  ...
  conversion:
    strategy: Webhook
    webhook:
      clientConfig:
        service:
          namespace: my-service-namespace
          name: my-service-name
          path: /my-path
          port: 1234
        caBundle: "Ci0tLS0tQk...<base64-encoded PEM bundle>...tLS0K"
...
```

```yaml
#  v1.16 中被弃用以推荐使用 apiextensions.k8s.io/v1
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
...
spec:
  ...
  conversion:
    strategy: Webhook
    webhookClientConfig:
      service:
        namespace: my-service-namespace
        name: my-service-name
        path: /my-path
        port: 1234
      caBundle: "Ci0tLS0tQk...<base64-encoded PEM bundle>...tLS0K"
...
```

#### Webhook 请求和响应[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#webhook-request-and-response)

##### 请求[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#request)

向 Webhook 发起请求的动词是 POST，请求的 `Content-Type` 为 `application/json`。 请求的主题为 JSON 序列化形式的 apiextensions.k8s.io API 组的 ConversionReview API 对象。

Webhook 可以在其 CustomResourceDefinition 中使用 `conversionReviewVersions` 字段设置它们接受的 `ConversionReview` 对象的版本：

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreviewversions-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreviewversions-1)

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
...
spec:
  ...
  conversion:
    strategy: Webhook
    webhook:
      conversionReviewVersions: ["v1", "v1beta1"]
      ...
```

创建 `apiextensions.k8s.io/v1` 版本的自定义资源定义时， `conversionReviewVersions` 是必填字段。 Webhook 要求支持至少一个 `ConversionReview` 当前和以前的 API 服务器可以理解的版本。

```yaml
# v1.16 已弃用以推荐使用 apiextensions.k8s.io/v1
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
...
spec:
  ...
  conversion:
    strategy: Webhook
    conversionReviewVersions: ["v1", "v1beta1"]
    ...
```

创建 apiextensions.k8s.io/v1beta1 定制资源定义时若未指定 `conversionReviewVersions`，则默认值为 v1beta1。

API 服务器将 `conversionReviewVersions` 列表中他们所支持的第一个 `ConversionReview` 资源版本发送给 Webhook。 如果列表中的版本都不被 API 服务器支持，则无法创建自定义资源定义。 如果某 API 服务器遇到之前创建的转换 Webhook 配置，并且该配置不支持 API 服务器知道如何发送的任何 `ConversionReview` 版本，调用 Webhook 的尝试会失败。

下面的示例显示了包含在 `ConversionReview` 对象中的数据， 该请求意在将 `CronTab` 对象转换为 `example.com/v1`：

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreview-request-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreview-request-1)

```yaml
{
  "apiVersion": "apiextensions.k8s.io/v1",
  "kind": "ConversionReview",
  "request": {
    # 用来唯一标识此转换调用的随机 UID
    "uid": "705ab4f5-6393-11e8-b7cc-42010a800002",
    
    # 对象要转换到的目标 API 组和版本
    "desiredAPIVersion": "example.com/v1",
    
    # 要转换的对象列表，其中可能包含一个或多个对象，版本可能相同也可能不同
    "objects": [
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1beta1",
        "metadata": {
          "creationTimestamp": "2019-09-04T14:03:02Z",
          "name": "local-crontab",
          "namespace": "default",
          "resourceVersion": "143",
          "uid": "3415a7fc-162b-4300-b5da-fd6083580d66"
        },
        "hostPort": "localhost:1234"
      },
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1beta1",
        "metadata": {
          "creationTimestamp": "2019-09-03T13:02:01Z",
          "name": "remote-crontab",
          "resourceVersion": "12893",
          "uid": "359a83ec-b575-460d-b553-d859cedde8a0"
        },
        "hostPort": "example.com:2345"
      }
    ]
  }
}
```

```yaml
{
  # v1.16 中已废弃以推荐使用 apiextensions.k8s.io/v1
  "apiVersion": "apiextensions.k8s.io/v1beta1",
  "kind": "ConversionReview",
  "request": {
    # 用来唯一标识此转换调用的随机 UID
    "uid": "705ab4f5-6393-11e8-b7cc-42010a800002",
    
    # 对象要转换到的目标 API 组和版本
    "desiredAPIVersion": "example.com/v1",
    
    # 要转换的对象列表，其中可能包含一个或多个对象，版本可能相同也可能不同
    "objects": [
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1beta1",
        "metadata": {
          "creationTimestamp": "2019-09-04T14:03:02Z",
          "name": "local-crontab",
          "namespace": "default",
          "resourceVersion": "143",
          "uid": "3415a7fc-162b-4300-b5da-fd6083580d66"
        },
        "hostPort": "localhost:1234"
      },
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1beta1",
        "metadata": {
          "creationTimestamp": "2019-09-03T13:02:01Z",
          "name": "remote-crontab",
          "resourceVersion": "12893",
          "uid": "359a83ec-b575-460d-b553-d859cedde8a0"
        },
        "hostPort": "example.com:2345"
      }
    ]
  }
}
```

##### 响应[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#response)

Webhook 响应包含 200 HTTP 状态代码、`Content-Type: application/json`， 在主体中包含 JSON 序列化形式的数据，在 `response` 节中给出 `ConversionReview` 对象（与发送的版本相同）。

如果转换成功，则 Webhook 应该返回包含以下字段的 `response` 节：

- `uid`，从发送到 webhook 的 `request.uid` 复制而来
- `result`，设置为 `{"status":"Success"}}`
- `convertedObjects`，包含来自 `request.objects` 的所有对象，均已转换为 `request.desiredVersion`

Webhook 的最简单成功响应示例：

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreview-response-success-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreview-response-success-1)

```yaml
{
  "apiVersion": "apiextensions.k8s.io/v1",
  "kind": "ConversionReview",
  "response": {
    # 必须与 <request.uid> 匹配
    "uid": "705ab4f5-6393-11e8-b7cc-42010a800002",
    "result": {
      "status": "Success"
    },
    # 这里的对象必须与 request.objects 中的对象顺序相同并且其 apiVersion 被设置为 <request.desiredAPIVersion>。
    # kind、metadata.uid、metadata.name 和 metadata.namespace 等字段都不可被 Webhook 修改。
    # Webhook 可以更改 metadata.labels 和 metadata.annotations 字段值。
    # Webhook 对 metadata 中其他字段的更改都会被忽略
    "convertedObjects": [
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1",
        "metadata": {
          "creationTimestamp": "2019-09-04T14:03:02Z",
          "name": "local-crontab",
          "namespace": "default",
          "resourceVersion": "143",
          "uid": "3415a7fc-162b-4300-b5da-fd6083580d66"
        },
        "host": "localhost",
        "port": "1234"
      },
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1",
        "metadata": {
          "creationTimestamp": "2019-09-03T13:02:01Z",
          "name": "remote-crontab",
          "resourceVersion": "12893",
          "uid": "359a83ec-b575-460d-b553-d859cedde8a0"
        },
        "host": "example.com",
        "port": "2345"
      }
    ]
  }
}
```

```yaml
{
  # v1.16 中已弃用以推荐使用  apiextensions.k8s.io/v1
  "apiVersion": "apiextensions.k8s.io/v1beta1",
  "kind": "ConversionReview",
  "response": {
    # 必须与 <request.uid> 匹配
    "uid": "705ab4f5-6393-11e8-b7cc-42010a800002",
    "result": {
      "status": "Failed"
    },
    # 这里的对象必须与 request.objects 中的对象顺序相同并且其 apiVersion 被设置为 <request.desiredAPIVersion>。
    # kind、metadata.uid、metadata.name 和 metadata.namespace 等字段都不可被 Webhook 修改。
    # Webhook 可以更改 metadata.labels 和 metadata.annotations 字段值。
    # Webhook 对 metadata 中其他字段的更改都会被忽略。
    "convertedObjects": [
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1",
        "metadata": {
          "creationTimestamp": "2019-09-04T14:03:02Z",
          "name": "local-crontab",
          "namespace": "default",
          "resourceVersion": "143",
          "uid": "3415a7fc-162b-4300-b5da-fd6083580d66"
        },
        "host": "localhost",
        "port": "1234"
      },
      {
        "kind": "CronTab",
        "apiVersion": "example.com/v1",
        "metadata": {
          "creationTimestamp": "2019-09-03T13:02:01Z",
          "name": "remote-crontab",
          "resourceVersion": "12893",
          "uid": "359a83ec-b575-460d-b553-d859cedde8a0"
        },
        "host": "example.com",
        "port": "2345"
      }
    ]
  }
}
```

如果转换失败，则 Webhook 应该返回包含以下字段的 `response` 节：

- `uid`，从发送到 Webhook 的 `request.uid` 复制而来
- `result`，设置为 `{"status": "Failed"}`

**警告：**

转换失败会破坏对定制资源的读写访问，包括更新或删除资源的能力。 转换失败应尽可能避免，并且不可用于实施合法性检查约束 （应改用验证模式或 Webhook 准入插件）。

来自 Webhook 的响应示例，指示转换请求失败，并带有可选消息：

- [apiextensions.k8s.io/v1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreview-response-failure-0)
- [apiextensions.k8s.io/v1beta1](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#conversionreview-response-failure-1)

```yaml
{
  "apiVersion": "apiextensions.k8s.io/v1",
  "kind": "ConversionReview",
  "response": {
    "uid": "<value from request.uid>",
    "result": {
      "status": "Failed",
      "message": "hostPort could not be parsed into a separate host and port"
    }
  }
}
```

```yaml
{
  # v1.16 中弃用以推荐使用 apiextensions.k8s.io/v1
  "apiVersion": "apiextensions.k8s.io/v1beta1",
  "kind": "ConversionReview",
  "response": {
    "uid": "<value from request.uid>",
    "result": {
      "status": "Failed",
      "message": "hostPort could not be parsed into a separate host and port"
    }
  }
}
```

#### 编写、读取和更新版本化的 CustomResourceDefinition 对象[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#write-read-and-update-versioned-crd-objects)

写入对象时，将存储为写入时指定的存储版本。如果存储版本发生变化， 现有对象永远不会被自动转换。然而，新创建或被更新的对象将以新的存储版本写入。 对象写入的版本不再被支持是有可能的。

当读取对象时，你需要在路径中指定版本。 你可以请求当前提供的任意版本的对象。 如果所指定的版本与对象的存储版本不同，Kubernetes 会按所请求的版本将对象返回， 但磁盘上存储的对象不会更改。

在为读取请求提供服务时正返回的对象会发生什么取决于 CRD 的 `spec.conversion` 中指定的内容：

- 如果所指定的 `strategy` 值是默认的 `None`，则针对对象的唯一修改是更改其 `apiVersion` 字符串， 并且可能[修剪未知字段](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#field-pruning)（取决于配置）。 请注意，如果存储和请求版本之间的模式不同，这不太可能导致好的结果。 尤其是如果在相同的数据类不同版本中采用不同字段来表示时，不应使用此策略。
- 如果指定了 [Webhook 转换](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#webhook-conversion)，则此机制将控制转换。

如果你更新一个现有对象，它将以当前的存储版本被重写。 这是可以将对象从一个版本改到另一个版本的唯一办法。

为了说明这一点，请考虑以下假设的一系列事件：

1. 存储版本是 `v1beta1`。你创建一个对象。该对象以版本 `v1beta1` 存储。
2. 你将为 CustomResourceDefinition 添加版本 `v1`，并将其指定为存储版本。 此处 `v1` 和 `v1beta1` 的模式是相同的，这通常是在 Kubernetes 生态系统中将 API 提升为稳定版时的情况。
3. 你使用版本 `v1beta1` 来读取你的对象，然后你再次用版本 `v1` 读取对象。 除了 apiVersion 字段之外，返回的两个对象是完全相同的。
4. 你创建一个新对象。该对象存储为版本 `v1`。 你现在有两个对象，其中一个是 `v1beta1`，另一个是 `v1`。
5. 你更新第一个对象。该对象现在以版本 `v1` 保存，因为 `v1` 是当前的存储版本。

##### 以前的存储版本[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#previous-storage-versions)

API 服务器在状态字段 `storedVersions` 中记录曾被标记为存储版本的每个版本。 对象可能以任何曾被指定为存储版本的版本保存。 存储中不会出现从未成为存储版本的版本的对象。

#### 将现有对象升级到新的存储版本[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/#upgrade-existing-objects-to-a-new-stored-version)

弃用版本并删除其支持时，请选择存储升级过程。

**选项 1：** 使用存储版本迁移程序（Storage Version Migrator）

1. 运行[存储版本迁移程序](https://github.com/kubernetes-sigs/kube-storage-version-migrator)
2. 从 CustomResourceDefinition 的 `status.storedVersions` 字段中去掉老的版本。

**选项 2：** 手动将现有对象升级到新的存储版本

以下是从 `v1beta1` 升级到 `v1` 的示例过程。

1. 在 CustomResourceDefinition 文件中将 `v1` 设置为存储版本，并使用 kubectl 应用它。 `storedVersions`现在是 `v1beta1, v1`。
2. 编写升级过程以列出所有现有对象并使用相同内容将其写回存储。 这会强制后端使用当前存储版本（即 `v1`）写入对象。
3. 从 CustomResourceDefinition 的 `status.storedVersions` 字段中删除 `v1beta1`。

**说明：**

`--subresource` 标志在 kubectl get、patch、edit 和 replace 命令中用于获取和更新所有支持它们的 API 资源的子资源、`status` 和 `scale`。此标志从 kubectl 版本 v1.24 开始可用。 以前通过 kubectl 读取子资源（如 `status`）涉及使用 `kubectl --raw`，并且根本不可能使用 kubectl 更新子资源。 从 v1.24 开始，`kubectl` 工具可用于编辑或修补有关 CRD 对象的 `status` 子资源。 请参阅[如何使用子资源标志修补 Deployment](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#scale-kubectl-patch)。

此页面是 Kubernetes v1.26 文档的一部分。 如果你运行的是不同版本的 Kubernetes，请查阅相应版本的文档。

以下是如何使用 `kubectl` 为一个 CRD 对象修补 `status` 子资源的示例：

```bash
kubectl patch customresourcedefinitions <CRD_Name> --subresource='status' --type='merge' -p '{"status":{"storedVersions":["v1"]}}'
```



## [安装一个扩展的 API server](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-extension-api-server/)
安装扩展的 API 服务器来使用聚合层以让 Kubernetes API 服务器使用 其它 API 进行扩展， 这些 API 不是核心 Kubernetes API 的一部分。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-extension-api-server/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

- 你必须[配置聚合层](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/) 并且启用 API 服务器的相关参数。

### 安装一个扩展的 API 服务器来使用聚合层[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-extension-api-server/#%E5%AE%89%E8%A3%85%E4%B8%80%E4%B8%AA%E6%89%A9%E5%B1%95%E7%9A%84-api-%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%9D%A5%E4%BD%BF%E7%94%A8%E8%81%9A%E5%90%88%E5%B1%82)

以下步骤描述如何 _在一个高层次_ 设置一个扩展的 apiserver。无论你使用的是 YAML 配置还是使用 API，这些步骤都适用。 目前我们正在尝试区分出两者的区别。有关使用 YAML 配置的具体示例，你可以在 Kubernetes 库中查看 [sample-apiserver](https://github.com/kubernetes/sample-apiserver/blob/master/README.md)。

或者，你可以使用现有的第三方解决方案，例如 [apiserver-builder](https://github.com/Kubernetes-incubator/apiserver-builder/blob/master/README.md)， 它将生成框架并自动执行以下所有步骤。

1. 确保启用了 APIService API（检查 `--runtime-config`）。默认应该是启用的，除非被特意关闭了。
2. 你可能需要制定一个 RBAC 规则，以允许你添加 APIService 对象，或让你的集群管理员创建一个。 （由于 API 扩展会影响整个集群，因此不建议在实时集群中对 API 扩展进行测试/开发/调试）
3. 创建 Kubernetes 命名空间，扩展的 api-service 将运行在该命名空间中。
4. 创建（或获取）用来签署服务器证书的 CA 证书，扩展 api-server 中将使用该证书做 HTTPS 连接。
5. 为 api-server 创建一个服务端的证书（或秘钥）以使用 HTTPS。这个证书应该由上述的 CA 签署。 同时应该还要有一个 Kube DNS 名称的 CN，这是从 Kubernetes 服务派生而来的， 格式为 `<service name>.<service name namespace>.svc`。
6. 使用命名空间中的证书（或秘钥）创建一个 Kubernetes secret。
7. 为扩展 api-server 创建一个 Kubernetes Deployment，并确保以卷的方式挂载了 Secret。 它应该包含对扩展 api-server 镜像的引用。Deployment 也应该在同一个命名空间中。

8. 确保你的扩展 apiserver 从该卷中加载了那些证书，并在 HTTPS 握手过程中使用它们。
9. 在你的命名空间中创建一个 Kubernetes 服务账号。
10. 为资源允许的操作创建 Kubernetes 集群角色。
11. 用你命名空间中的服务账号创建一个 Kubernetes 集群角色绑定，绑定到你创建的角色上。
12. 用你命名空间中的服务账号创建一个 Kubernetes 集群角色绑定，绑定到 `system:auth-delegator` 集群角色，以将 auth 决策委派给 Kubernetes 核心 API 服务器。
13. 以你命名空间中的服务账号创建一个 Kubernetes 集群角色绑定，绑定到 `extension-apiserver-authentication-reader` 角色。 这将让你的扩展 api-server 能够访问 `extension-apiserver-authentication` configmap。

14. 创建一个 Kubernetes apiservice。 上述的 CA 证书应该使用 base64 编码，剥离新行并用作 apiservice 中的 spec.caBundle。 该资源不应放到任何名字空间。如果使用了 [kube-aggregator API](https://github.com/kubernetes/kube-aggregator/)，那么只需要传入 PEM 编码的 CA 绑定，因为 base 64 编码已经完成了。
15. 使用 kubectl 来获得你的资源。 它应该返回 "找不到资源"。此消息表示一切正常，但你目前还没有创建该资源类型的对象。

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-extension-api-server/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 如果你还未配置，请[配置聚合层](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-aggregation-layer/) 并启用 apiserver 的相关参数。
- 高级概述，请参阅[使用聚合层扩展 Kubernetes API](https://kubernetes.io/zh-cn/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation)。
- 了解如何[使用 Custom Resource Definition 扩展 Kubernetes API](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)。
## [配置多个调度器](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/)
Kubernetes 自带了一个默认调度器，其详细描述请查阅 [这里](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kube-scheduler/)。 如果默认调度器不适合你的需求，你可以实现自己的调度器。 而且，你甚至可以和默认调度器一起同时运行多个调度器，并告诉 Kubernetes 为每个 Pod 使用哪个调度器。 让我们通过一个例子讲述如何在 Kubernetes 中运行多个调度器。

关于实现调度器的具体细节描述超出了本文范围。 请参考 kube-scheduler 的实现，规范示例代码位于 [pkg/scheduler](https://github.com/kubernetes/kubernetes/tree/master/pkg/scheduler)。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 打包调度器[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/#%E6%89%93%E5%8C%85%E8%B0%83%E5%BA%A6%E5%99%A8)

将调度器可执行文件打包到容器镜像中。出于示例目的，可以使用默认调度器 （kube-scheduler）作为第二个调度器。 克隆 [GitHub 上 Kubernetes 源代码](https://github.com/kubernetes/kubernetes)， 并编译构建源代码。

```shell
git clone https://github.com/kubernetes/kubernetes.git
cd kubernetes
make
```

创建一个包含 kube-scheduler 二进制文件的容器镜像。用于构建镜像的 `Dockerfile` 内容如下：

```docker
FROM busybox
ADD ./_output/local/bin/linux/amd64/kube-scheduler /usr/local/bin/kube-scheduler
```

将文件保存为 `Dockerfile`，构建镜像并将其推送到镜像仓库。 此示例将镜像推送到 [Google 容器镜像仓库（GCR）](https://cloud.google.com/container-registry/)。 有关详细信息，请阅读 GCR [文档](https://cloud.google.com/container-registry/docs/)。

```shell
docker build -t gcr.io/my-gcp-project/my-kube-scheduler:1.0 .
gcloud docker -- push gcr.io/my-gcp-project/my-kube-scheduler:1.0
```

### 为调度器定义 Kubernetes Deployment[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/#%E4%B8%BA%E8%B0%83%E5%BA%A6%E5%99%A8%E5%AE%9A%E4%B9%89-kubernetes-deployment)

现在将调度器放在容器镜像中，为它创建一个 Pod 配置，并在 Kubernetes 集群中 运行它。但是与其在集群中直接创建一个 Pod，不如使用 [Deployment](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/)。 Deployment 管理一个 [ReplicaSet](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/replicaset/)， ReplicaSet 再管理 Pod，从而使调度器能够免受一些故障的影响。 以下是 Deployment 配置，将其保存为 `my-scheduler.yaml`：

[`admin/sched/my-scheduler.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/sched/my-scheduler.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/sched/my-scheduler.yaml to clipboard")

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-scheduler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: my-scheduler-as-kube-scheduler
subjects:
- kind: ServiceAccount
  name: my-scheduler
  namespace: kube-system
roleRef:
  kind: ClusterRole
  name: system:kube-scheduler
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-scheduler-config
  namespace: kube-system
data:
  my-scheduler-config.yaml: |
    apiVersion: kubescheduler.config.k8s.io/v1beta2
    kind: KubeSchedulerConfiguration
    profiles:
      - schedulerName: my-scheduler
    leaderElection:
      leaderElect: false    
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: my-scheduler-as-volume-scheduler
subjects:
- kind: ServiceAccount
  name: my-scheduler
  namespace: kube-system
roleRef:
  kind: ClusterRole
  name: system:volume-scheduler
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    component: scheduler
    tier: control-plane
  name: my-scheduler
  namespace: kube-system
spec:
  selector:
    matchLabels:
      component: scheduler
      tier: control-plane
  replicas: 1
  template:
    metadata:
      labels:
        component: scheduler
        tier: control-plane
        version: second
    spec:
      serviceAccountName: my-scheduler
      containers:
      - command:
        - /usr/local/bin/kube-scheduler
        - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
        image: gcr.io/my-gcp-project/my-kube-scheduler:1.0
        livenessProbe:
          httpGet:
            path: /healthz
            port: 10259
            scheme: HTTPS
          initialDelaySeconds: 15
        name: kube-second-scheduler
        readinessProbe:
          httpGet:
            path: /healthz
            port: 10259
            scheme: HTTPS
        resources:
          requests:
            cpu: '0.1'
        securityContext:
          privileged: false
        volumeMounts:
          - name: config-volume
            mountPath: /etc/kubernetes/my-scheduler
      hostNetwork: false
      hostPID: false
      volumes:
        - name: config-volume
          configMap:
            name: my-scheduler-config
```

在以上的清单中，你使用 [KubeSchedulerConfiguration](https://kubernetes.io/zh-cn/docs/reference/scheduling/config/) 来自定义调度器实现的行为。当使用 `--config` 选项进行初始化时，该配置被传递到 `kube-scheduler`。 `my-scheduler-config` ConfigMap 存储配置数据。 `my-scheduler` Deployment 的 Pod 将 `my-scheduler-config` ConfigMap 挂载为一个卷。

在前面提到的调度器配置中，你的调度器通过 [KubeSchedulerProfile](https://kubernetes.io/docs/reference/config-api/kube-scheduler-config.v1beta3/#kubescheduler-config-k8s-io-v1beta3-KubeSchedulerProfile) 进行实现。

**说明：** 要确定一个调度器是否可以调度特定的 Pod，PodTemplate 或 Pod 清单中的 `spec.schedulerName` 字段必须匹配 `KubeSchedulerProfile` 中的 `schedulerName` 字段。 所有运行在集群中的调度器必须拥有唯一的名称。

还要注意，我们创建了一个专用服务账号 `my-scheduler` 并将集群角色 `system:kube-scheduler` 绑定到它，以便它可以获得与 `kube-scheduler` 相同的权限。

请参阅 [kube-scheduler 文档](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/) 获取其他命令行参数以及 [Scheduler 配置参考](https://kubernetes.io/docs/reference/config-api/kube-scheduler-config.v1beta3/) 获取自定义 `kube-scheduler` 配置的详细说明。

### 在集群中运行第二个调度器[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/#%E5%9C%A8%E9%9B%86%E7%BE%A4%E4%B8%AD%E8%BF%90%E8%A1%8C%E7%AC%AC%E4%BA%8C%E4%B8%AA%E8%B0%83%E5%BA%A6%E5%99%A8)

为了在 Kubernetes 集群中运行我们的第二个调度器，在 Kubernetes 集群中创建上面配置中指定的 Deployment：

```shell
kubectl create -f my-scheduler.yaml
```

验证调度器 Pod 正在运行：

```shell
kubectl get pods --namespace=kube-system
```

输出类似于：

```
NAME                                           READY     STATUS    RESTARTS   AGE
....
my-scheduler-lnf4s-4744f                       1/1       Running   0          2m
...
```

此列表中，除了默认的 `kube-scheduler` Pod 之外，你应该还能看到处于 “Running” 状态的 `my-scheduler` Pod。

#### 启用领导者选举[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/#%E5%90%AF%E7%94%A8%E9%A2%86%E5%AF%BC%E8%80%85%E9%80%89%E4%B8%BE)

要在启用了 leader 选举的情况下运行多调度器，你必须执行以下操作：

更新你的 YAML 文件中的 `my-scheduler-config` ConfigMap 里的 KubeSchedulerConfiguration 相关字段如下：

- `leaderElection.leaderElect` to `true`
- `leaderElection.resourceNamespace` to `<lock-object-namespace>`
- `leaderElection.resourceName` to `<lock-object-name>`

**说明：**

控制平面会为你创建锁对象，但是命名空间必须已经存在。 你可以使用 `kube-system` 命名空间。

如果在集群上启用了 RBAC，则必须更新 `system：kube-scheduler` 集群角色。 将调度器名称添加到应用了 `endpoints` 和 `leases` 资源的规则的 resourceNames 中，如以下示例所示：

```shell
kubectl edit clusterrole system:kube-scheduler
```

[`admin/sched/clusterrole.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/sched/clusterrole.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/sched/clusterrole.yaml to clipboard")

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:kube-scheduler
rules:
  - apiGroups:
      - coordination.k8s.io
    resources:
      - leases
    verbs:
      - create
  - apiGroups:
      - coordination.k8s.io
    resourceNames:
      - kube-scheduler
      - my-scheduler
    resources:
      - leases
    verbs:
      - get
      - update
  - apiGroups:
      - ""
    resourceNames:
      - kube-scheduler
      - my-scheduler
    resources:
      - endpoints
    verbs:
      - delete
      - get
      - patch
      - update
```

### 为 Pod 指定调度器[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/#%E4%B8%BA-pod-%E6%8C%87%E5%AE%9A%E8%B0%83%E5%BA%A6%E5%99%A8)

现在第二个调度器正在运行，创建一些 Pod，并指定它们由默认调度器或部署的调度器进行调度。 为了使用特定的调度器调度给定的 Pod，在那个 Pod 的 spec 中指定调度器的名称。让我们看看三个例子。

- Pod spec 没有任何调度器名称
    
    [`admin/sched/pod1.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/sched/pod1.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/sched/pod1.yaml to clipboard")
    
    ```yaml
    apiVersion: v1
      kind: Pod
      metadata:
        name: no-annotation
        labels:
          name: multischeduler-example
      spec:
        containers:
        - name: pod-with-no-annotation-container
          image: registry.k8s.io/pause:2.0
    ```
    
    如果未提供调度器名称，则会使用 default-scheduler 自动调度 pod。
    
    将此文件另存为 `pod1.yaml`，并将其提交给 Kubernetes 集群。
    
    ```shell
    kubectl create -f pod1.yaml
    ```
    

- Pod spec 设置为 `default-scheduler`
    
    [`admin/sched/pod2.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/sched/pod2.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/sched/pod2.yaml to clipboard")
    
    ```yaml
    apiVersion: v1
      kind: Pod
      metadata:
        name: annotation-default-scheduler
        labels:
          name: multischeduler-example
      spec:
        schedulerName: default-scheduler
        containers:
        - name: pod-with-default-annotation-container
          image: registry.k8s.io/pause:2.0
      
    ```
    
    通过将调度器名称作为 `spec.schedulerName` 参数的值来指定调度器。 在这种情况下，我们提供默认调度器的名称，即 `default-scheduler`。
    
    将此文件另存为 `pod2.yaml`，并将其提交给 Kubernetes 集群。
    
    ```shell
    kubectl create -f pod2.yaml
    ```
    

- Pod spec 设置为 `my-scheduler`
    
    [`admin/sched/pod3.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/sched/pod3.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/sched/pod3.yaml to clipboard")
    
    ```yaml
    apiVersion: v1
      kind: Pod
      metadata:
        name: annotation-second-scheduler
        labels:
          name: multischeduler-example
      spec:
        schedulerName: my-scheduler
        containers:
        - name: pod-with-second-annotation-container
          image: registry.k8s.io/pause:2.0
      
    ```
    
    在这种情况下，我们指定此 Pod 使用我们部署的 `my-scheduler` 来调度。 请注意，`spec.schedulerName` 参数的值应该与调度器提供的 `KubeSchedulerProfile` 中的 `schedulerName` 字段相匹配。
    
    将此文件另存为 `pod3.yaml`，并将其提交给 Kubernetes 集群。
    
    ```shell
    kubectl create -f pod3.yaml
    ```
    

确认所有三个 pod 都在运行。

```shell
kubectl get pods
```

#### 验证是否使用所需的调度器调度了 pod[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/configure-multiple-schedulers/#%E9%AA%8C%E8%AF%81%E6%98%AF%E5%90%A6%E4%BD%BF%E7%94%A8%E6%89%80%E9%9C%80%E7%9A%84%E8%B0%83%E5%BA%A6%E5%99%A8%E8%B0%83%E5%BA%A6%E4%BA%86-pod)

为了更容易地完成这些示例，我们没有验证 Pod 实际上是使用所需的调度程序调度的。 我们可以通过更改 Pod 的顺序和上面的部署配置提交来验证这一点。 如果我们在提交调度器部署配置之前将所有 Pod 配置提交给 Kubernetes 集群， 我们将看到注解了 `annotation-second-scheduler` 的 Pod 始终处于 “Pending” 状态， 而其他两个 Pod 被调度。 一旦我们提交调度器部署配置并且我们的新调度器开始运行，注解了 `annotation-second-scheduler` 的 pod 就能被调度。

或者，可以查看事件日志中的 “Scheduled” 条目，以验证是否由所需的调度器调度了 Pod。

```shell
kubectl get events
```

你也可以使用[自定义调度器配置](https://kubernetes.io/zh-cn/docs/reference/scheduling/config/#multiple-profiles) 或自定义容器镜像，用于集群的主调度器，方法是在相关控制平面节点上修改其静态 pod 清单。
## [使用 HTTP 代理访问 Kubernetes API](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/http-proxy-access-api/)
本文说明如何使用 HTTP 代理访问 Kubernetes API。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/http-proxy-access-api/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

- 你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：
    
    - [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
    - [玩转 Kubernetes](http://labs.play-with-k8s.com/)
    
    要获知版本信息，请输入 `kubectl version`.

如果你的集群中还没有任何应用，使用如下命令启动一个 Hello World 应用：

```shell
kubectl create deployment node-hello --image=gcr.io/google-samples/node-hello:1.0 --port=8080
```

### 使用 kubectl 启动代理服务器[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/http-proxy-access-api/#%E4%BD%BF%E7%94%A8-kubectl-%E5%90%AF%E5%8A%A8%E4%BB%A3%E7%90%86%E6%9C%8D%E5%8A%A1%E5%99%A8)

使用如下命令启动 Kubernetes API 服务器的代理：

```shell
kubectl proxy --port=8080
```

### 探究 Kubernetes API[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/http-proxy-access-api/#%E6%8E%A2%E7%A9%B6-kubernetes-api)

当代理服务器在运行时，你可以通过 `curl`、`wget` 或者浏览器访问 API。

获取 API 版本：

```shell
curl http://localhost:8080/api/
```

输出应该类似这样：

```
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "10.0.2.15:8443"
    }
  ]
}
```

获取 Pod 列表：

```shell
curl http://localhost:8080/api/v1/namespaces/default/pods
```

输出应该类似这样：

```
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "33074"
  },
  "items": [
    {
      "metadata": {
        "name": "kubernetes-bootcamp-2321272333-ix8pt",
        "generateName": "kubernetes-bootcamp-2321272333-",
        "namespace": "default",
        "uid": "ba21457c-6b1d-11e6-85f7-1ef9f1dab92b",
        "resourceVersion": "33003",
        "creationTimestamp": "2016-08-25T23:43:30Z",
        "labels": {
          "pod-template-hash": "2321272333",
          "run": "kubernetes-bootcamp"
        },
        ...
}
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/http-proxy-access-api/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

想了解更多信息，请参阅 [kubectl 代理](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#proxy)。
## [使用 SOCKS5 代理访问 Kubernetes API](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/socks5-proxy-access-api/)
**特性状态：** `Kubernetes v1.24 [stable]`

本文展示了如何使用 SOCKS5 代理访问远程 Kubernetes 集群的 API。 当你要访问的集群不直接在公共 Internet 上公开其 API 时，这很有用。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/socks5-proxy-access-api/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你的 Kubernetes 服务器版本必须不低于版本 v1.24. 要获知版本信息，请输入 `kubectl version`.

你需要 SSH 客户端软件（`ssh` 工具），并在远程服务器上运行 SSH 服务。 你必须能够登录到远程服务器上的 SSH 服务。

### 任务上下文[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/socks5-proxy-access-api/#task-context)

**说明：**

此示例使用 SSH 隧道传输流量，SSH 客户端和服务器充当 SOCKS 代理。 你可以使用其他任意类型的 [SOCKS5](https://zh.wikipedia.org/wiki/SOCKS#SOCKS5) 代理代替。

图 1 表示你将在此任务中实现的目标。

- 你有一台在后面的步骤中被称为本地计算机的客户端计算机，你将在这台计算机上创建与 Kubernetes API 对话的请求。
- Kubernetes 服务器/API 托管在远程服务器上。
- 你将使用 SSH 客户端和服务器软件在本地和远程服务器之间创建安全的 SOCKS5 隧道。 客户端和 Kubernetes API 之间的 HTTPS 流量将流经 SOCKS5 隧道，该隧道本身通过 SSH 进行隧道传输。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20230323111142.png)


图 1. SOCKS5 教程组件

### 使用 SSH 创建 SOCKS5 代理[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/socks5-proxy-access-api/#%E4%BD%BF%E7%94%A8-ssh-%E5%88%9B%E5%BB%BA-socks5-%E4%BB%A3%E7%90%86)

下面的命令在你的客户端计算机和远程 SOCKS 服务器之间启动一个 SOCKS5 代理：

```shell
# 运行此命令后，SSH 隧道继续在前台运行
ssh -D 1080 -q -N username@kubernetes-remote-server.example
```

- `-D 1080`: 在本地端口 1080 上打开一个 SOCKS 代理。
- `-q`: 静音模式。导致大多数警告和诊断消息被抑制。
- `-N`: 不执行远程命令。仅用于转发端口。
- `username@kubernetes-remote-server.example`：运行 Kubernetes 集群的远程 SSH 服务器（例如：堡垒主机）。

### 客户端配置[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/socks5-proxy-access-api/#%E5%AE%A2%E6%88%B7%E7%AB%AF%E9%85%8D%E7%BD%AE)

要通过代理访问 Kubernetes API 服务器，你必须指示 `kubectl` 通过我们之前创建的 SOCKS5 代理发送查询。 这可以通过设置适当的环境变量或通过 kubeconfig 文件中的 `proxy-url` 属性来实现。 使用环境变量：

```shell
export HTTPS_PROXY=socks5://localhost:1080
```

要始终在特定的 `kubectl` 上下文中使用此设置，请在 `~/.kube/config` 文件中为相关的 `cluster` 条目设置 `proxy-url` 属性。例如：

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LRMEMMW2 # 简化以便阅读
    # “Kubernetes API”服务器，换言之，kubernetes-remote-server.example 的 IP 地址
    server: https://<API_SERVER_IP_ADRESS>:6443
    # 上图中的“SSH SOCKS5代理”（内置 DNS 解析）
    proxy-url: socks5://localhost:1080
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: LS0tLS1CR== # 节略，为了便于阅读
    client-key-data: LS0tLS1CRUdJT=      # 节略，为了便于阅读
```

一旦你通过前面提到的 SSH 命令创建了隧道，并定义了环境变量或 `proxy-url` 属性， 你就可以通过该代理与你的集群交互。例如：

```shell
kubectl get pods
```

```console
NAMESPACE     NAME                                     READY   STATUS      RESTARTS   AGE
kube-system   coredns-85cb69466-klwq8                  1/1     Running     0          5m46s
```

**说明：**

- 在 `kubectl` 1.24 之前，大多数 `kubectl` 命令在使用 socks 代理时都有效，除了 `kubectl exec`。
- `kubectl` 支持读取 `HTTPS_PROXY` 和 `https_proxy` 环境变量。 这些被其他支持 SOCKS 的程序使用，例如 `curl`。 因此在某些情况下，在命令行上定义环境变量会更好：
    
    ```shell
    HTTPS_PROXY=socks5://localhost:1080 kubectl get pods
    ```
    

- 使用 `proxy-url` 时，代理仅用于相关的 `kubectl` 上下文，而环境变量将影响所有上下文。

- 通过使用 `socks5h` 协议名称而不是上面显示的更广为人知的 `socks5` 协议， 可以进一步保护 k8s API 服务器主机名免受 DNS 泄漏影响。 这种情况下，`kubectl` 将要求代理服务器（例如 SSH 堡垒机）解析 k8s API 服务器域名， 而不是在运行 `kubectl` 的系统上进行解析。 另外还要注意，使用 `socks5h` 时，像 `https://localhost:6443/api` 这样的 k8s API 服务器 URL 并不是指你的本地客户端计算机。 相反，它指向的是代理服务器（例如 SSH 堡垒机）上已知的 `localhost`。

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/socks5-proxy-access-api/#%E6%B8%85%E7%90%86)

通过在运行它的终端上按 `CTRL+C` 来停止 SSH 端口转发进程。

在终端中键入 `unset https_proxy` 以停止通过代理转发 http 流量。

### 进一步阅读[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/socks5-proxy-access-api/#%E8%BF%9B%E4%B8%80%E6%AD%A5%E9%98%85%E8%AF%BB)

- [OpenSSH 远程登录客户端](https://man.openbsd.org/ssh)
## [设置 Konnectivity 服务](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-konnectivity/)

Konnectivity 服务为控制平面提供集群通信的 TCP 级别代理。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-konnectivity/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你需要有一个 Kubernetes 集群，并且 kubectl 命令可以与集群通信。 建议在至少有两个不充当控制平面主机的节点的集群上运行本教程。 如果你还没有集群，可以使用 [minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 创建一个集群。

### 配置 Konnectivity 服务[](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/setup-konnectivity/#configure-the-konnectivity-service)

接下来的步骤需要出口配置，比如：

[`admin/konnectivity/egress-selector-configuration.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/konnectivity/egress-selector-configuration.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/konnectivity/egress-selector-configuration.yaml to clipboard")

```yaml
apiVersion: apiserver.k8s.io/v1beta1
kind: EgressSelectorConfiguration
egressSelections:
# 由于我们要控制集群的出站流量，所以将 “cluster” 用作 name。
# 其他支持的值有 “etcd” 和 “controlplane”。
- name: cluster
  connection:
    # 这一属性将控制 API 服务器 Konnectivity 服务器之间的协议。
    # 支持的值为 “GRPC” 和 “HTTPConnect”。
    # 最终用户不会察觉这两种模式之间的差异。
    # 你需要将 Konnectivity 服务器设为在相同模式下工作。
    proxyProtocol: GRPC
    transport:
      # 此属性控制 API 服务器使用哪种传输方式与 Konnectivity 服务器通信。
      # 如果 Konnectivity 服务器与 API 服务器位于同一台机器上，建议使用 UDS。
      # 你需要将 Konnectivity 服务器配置为侦听同一个 UDS 套接字。
      # 另一个支持的传输方式是 “tcp”。
      # 你将需要设置 TLS config 以确保 TCP 传输的安全。
      uds:
        udsName: /etc/kubernetes/konnectivity-server/konnectivity-server.socket
```

你需要配置 API 服务器来使用 Konnectivity 服务，并将网络流量定向到集群节点：

1. 确保[服务账号令牌卷投射](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)特性被启用。 该特性自 Kubernetes v1.20 起默认已被启用。
    
2. 创建一个出站流量配置文件，比如 `admin/konnectivity/egress-selector-configuration.yaml`。
    
3. 将 API 服务器的 `--egress-selector-config-file` 参数设置为你的 API 服务器的离站流量配置文件路径。
    
4. 如果你在使用 UDS 连接，须将卷配置添加到 kube-apiserver：
    
    ```yaml
    spec:
      containers:
        volumeMounts:
        - name: konnectivity-uds
          mountPath: /etc/kubernetes/konnectivity-server
          readOnly: false
      volumes:
      - name: konnectivity-uds
        hostPath:
          path: /etc/kubernetes/konnectivity-server
          type: DirectoryOrCreate
    ```
    

为 konnectivity-server 生成或者取得证书和 kubeconfig 文件。 例如，你可以使用 OpenSSL 命令行工具，基于存放在某控制面主机上 `/etc/kubernetes/pki/ca.crt` 文件中的集群 CA 证书来发放一个 X.509 证书。

```bash
openssl req -subj "/CN=system:konnectivity-server" -new -newkey rsa:2048 -nodes -out konnectivity.csr -keyout konnectivity.key
openssl x509 -req -in konnectivity.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out konnectivity.crt -days 375 -sha256
SERVER=$(kubectl config view -o jsonpath='{.clusters..server}')
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config set-credentials system:konnectivity-server --client-certificate konnectivity.crt --client-key konnectivity.key --embed-certs=true
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config set-cluster kubernetes --server "$SERVER" --certificate-authority /etc/kubernetes/pki/ca.crt --embed-certs=true
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config set-context system:konnectivity-server@kubernetes --cluster kubernetes --user system:konnectivity-server
kubectl --kubeconfig /etc/kubernetes/konnectivity-server.conf config use-context system:konnectivity-server@kubernetes
rm -f konnectivity.crt konnectivity.key konnectivity.csr
```

接下来，你需要部署 Konnectivity 服务器和代理。 [kubernetes-sigs/apiserver-network-proxy](https://github.com/kubernetes-sigs/apiserver-network-proxy) 是一个参考实现。

在控制面节点上部署 Konnectivity 服务。 下面提供的 `konnectivity-server.yaml` 配置清单假定在你的集群中 Kubernetes 组件都是部署为[静态 Pod](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/) 的。 如果不是，你可以将 Konnectivity 服务部署为 DaemonSet。

[`admin/konnectivity/konnectivity-server.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/konnectivity/konnectivity-server.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/konnectivity/konnectivity-server.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: konnectivity-server
  namespace: kube-system
spec:
  priorityClassName: system-cluster-critical
  hostNetwork: true
  containers:
  - name: konnectivity-server-container
    image: registry.k8s.io/kas-network-proxy/proxy-server:v0.0.32
    command: ["/proxy-server"]
    args: [
            "--logtostderr=true",
            # 下一行需与 egressSelectorConfiguration 中设置的值一致。
            "--uds-name=/etc/kubernetes/konnectivity-server/konnectivity-server.socket",
            # 下面两行假定 Konnectivity 服务器被部署在与 apiserver 相同的机器上，
            # 并且该 API 服务器的证书和密钥位于指定的位置。
            "--cluster-cert=/etc/kubernetes/pki/apiserver.crt",
            "--cluster-key=/etc/kubernetes/pki/apiserver.key",
            # 下一行需与 egressSelectorConfiguration 中设置的值一致。
            "--mode=grpc",
            "--server-port=0",
            "--agent-port=8132",
            "--admin-port=8133",
            "--health-port=8134",
            "--agent-namespace=kube-system",
            "--agent-service-account=konnectivity-agent",
            "--kubeconfig=/etc/kubernetes/konnectivity-server.conf",
            "--authentication-audience=system:konnectivity-server"
            ]
    livenessProbe:
      httpGet:
        scheme: HTTP
        host: 127.0.0.1
        port: 8134
        path: /healthz
      initialDelaySeconds: 30
      timeoutSeconds: 60
    ports:
    - name: agentport
      containerPort: 8132
      hostPort: 8132
    - name: adminport
      containerPort: 8133
      hostPort: 8133
    - name: healthport
      containerPort: 8134
      hostPort: 8134
    volumeMounts:
    - name: k8s-certs
      mountPath: /etc/kubernetes/pki
      readOnly: true
    - name: kubeconfig
      mountPath: /etc/kubernetes/konnectivity-server.conf
      readOnly: true
    - name: konnectivity-uds
      mountPath: /etc/kubernetes/konnectivity-server
      readOnly: false
  volumes:
  - name: k8s-certs
    hostPath:
      path: /etc/kubernetes/pki
  - name: kubeconfig
    hostPath:
      path: /etc/kubernetes/konnectivity-server.conf
      type: FileOrCreate
  - name: konnectivity-uds
    hostPath:
      path: /etc/kubernetes/konnectivity-server
      type: DirectoryOrCreate
```

在你的集群中部署 Konnectivity 代理：

[`admin/konnectivity/konnectivity-agent.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/konnectivity/konnectivity-agent.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/konnectivity/konnectivity-agent.yaml to clipboard")

```yaml
apiVersion: apps/v1
# 作为另一种替代方案，你可以将代理部署为 Deployment。
# 没有必要在每个节点上都有一个代理。
kind: DaemonSet
metadata:
  labels:
    addonmanager.kubernetes.io/mode: Reconcile
    k8s-app: konnectivity-agent
  namespace: kube-system
  name: konnectivity-agent
spec:
  selector:
    matchLabels:
      k8s-app: konnectivity-agent
  template:
    metadata:
      labels:
        k8s-app: konnectivity-agent
    spec:
      priorityClassName: system-cluster-critical
      tolerations:
        - key: "CriticalAddonsOnly"
          operator: "Exists"
      containers:
        - image: us.gcr.io/k8s-artifacts-prod/kas-network-proxy/proxy-agent:v0.0.16
          name: konnectivity-agent
          command: ["/proxy-agent"]
          args: [
                  "--logtostderr=true",
                  "--ca-cert=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt",
                  # 由于 konnectivity 服务器以 hostNetwork=true 运行，
                  # 所以这是控制面节点的 IP 地址。
                  "--proxy-server-host=35.225.206.7",
                  "--proxy-server-port=8132",
                  "--admin-server-port=8133",
                  "--health-server-port=8134",
                  "--service-account-token-path=/var/run/secrets/tokens/konnectivity-agent-token"
                  ]
          volumeMounts:
            - mountPath: /var/run/secrets/tokens
              name: konnectivity-agent-token
          livenessProbe:
            httpGet:
              port: 8134
              path: /healthz
            initialDelaySeconds: 15
            timeoutSeconds: 15
      serviceAccountName: konnectivity-agent
      volumes:
        - name: konnectivity-agent-token
          projected:
            sources:
              - serviceAccountToken:
                  path: konnectivity-agent-token
                  audience: system:konnectivity-server
```

最后，如果你的集群启用了 RBAC，请创建相关的 RBAC 规则：

[`admin/konnectivity/konnectivity-rbac.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/admin/konnectivity/konnectivity-rbac.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy admin/konnectivity/konnectivity-rbac.yaml to clipboard")

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:konnectivity-server
  labels:
    kubernetes.io/cluster-service: "true"
    addonmanager.kubernetes.io/mode: Reconcile
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:auth-delegator
subjects:
  - apiGroup: rbac.authorization.k8s.io
    kind: User
    name: system:konnectivity-server
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: konnectivity-agent
  namespace: kube-system
  labels:
    kubernetes.io/cluster-service: "true"
    addonmanager.kubernetes.io/mode: Reconcile
```