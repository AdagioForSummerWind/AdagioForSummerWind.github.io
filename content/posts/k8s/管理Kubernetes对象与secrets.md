# 管理 Kubernetes 对象

用声明式和命令式范型与 Kubernetes API 交互。

---


## [使用配置文件对 Kubernetes 对象进行声明式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/)
你可以通过在一个目录中存储多个对象配置文件、并使用 `kubectl apply` 来递归地创建和更新对象来创建、更新和删除 Kubernetes 对象。 这种方法会保留对现有对象已作出的修改，而不会将这些更改写回到对象配置文件中。 `kubectl diff` 也会给你呈现 `apply` 将作出的变更的预览。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

安装 [`kubectl`](https://kubernetes.io/zh-cn/docs/tasks/tools/)。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 权衡取舍[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#trade-offs)

`kubectl` 工具能够支持三种对象管理方式：

- 指令式命令
- 指令式对象配置
- 声明式对象配置

关于每种对象管理的优缺点的讨论，可参见 [Kubernetes 对象管理](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/object-management/)。

### 概览[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#overview)

声明式对象管理需要用户对 Kubernetes 对象定义和配置有比较深刻的理解。 如果你还没有这方面的知识储备，请先阅读下面的文档：

- [使用指令式命令管理 Kubernetes 对象](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/)
- [使用配置文件对 Kubernetes 对象进行指令式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/)

以下是本文档中使用的术语的定义：

- _对象配置文件/配置文件_：一个定义 Kubernetes 对象的配置的文件。 本主题展示如何将配置文件传递给 `kubectl apply`。 配置文件通常存储于类似 Git 这种源码控制系统中。
- _现时对象配置/现时配置_：由 Kubernetes 集群所观测到的对象的现时配置值。 这些配置保存在 Kubernetes 集群存储（通常是 etcd）中。
- _声明式配置写者/声明式写者_：负责更新现时对象的人或者软件组件。 本主题中的声明式写者负责改变对象配置文件并执行 `kubectl apply` 命令 以写入变更。

### 如何创建对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#how-to-create-objects)

使用 `kubectl apply` 来创建指定目录中配置文件所定义的所有对象，除非对应对象已经存在：

```shell
kubectl apply -f <目录>/
```

此操作会在每个对象上设置 `kubectl.kubernetes.io/last-applied-configuration: '{...}'` 注解。注解值中包含了用来创建对象的配置文件的内容。

**说明：** 添加 `-R` 标志可以递归地处理目录。

下面是一个对象配置文件示例：

[`application/simple_deployment.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/simple_deployment.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/simple_deployment.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  minReadySeconds: 5
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

执行 `kubectl diff` 可以打印出将被创建的对象：

```shell
kubectl diff -f https://k8s.io/examples/application/simple_deployment.yaml
```

**说明：**

`diff` 使用[服务器端试运行（Server-side Dry-run）](https://kubernetes.io/zh-cn/docs/reference/using-api/api-concepts/#dry-run) 功能特性；而该功能特性需要在 `kube-apiserver` 上启用。

由于 `diff` 操作会使用试运行模式执行服务器端 apply 请求，因此需要为 用户配置 `PATCH`、`CREATE` 和 `UPDATE` 操作权限。 参阅[试运行授权](https://kubernetes.io/zh-cn/docs/reference/using-api/api-concepts#dry-run-authorization) 了解详情。

使用 `kubectl apply` 来创建对象：

```shell
kubectl apply -f https://k8s.io/examples/application/simple_deployment.yaml
```

使用 `kubectl get` 打印其现时配置：

```shell
kubectl get -f https://k8s.io/examples/application/simple_deployment.yaml -o yaml
```

输出显示注解 `kubectl.kubernetes.io/last-applied-configuration` 被写入到 现时配置中，并且其内容与配置文件相同：

```yaml
kind: Deployment
metadata:
  annotations:
    # ...
    # This is the json representation of simple_deployment.yaml
    # It was written by kubectl apply when the object was created
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment",
      "metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},
      "spec":{"minReadySeconds":5,"selector":{"matchLabels":{"app":nginx}},"template":{"metadata":{"labels":{"app":"nginx"}},
      "spec":{"containers":[{"image":"nginx:1.14.2","name":"nginx",
      "ports":[{"containerPort":80}]}]}}}}      
  # ...
spec:
  # ...
  minReadySeconds: 5
  selector:
    matchLabels:
      # ...
      app: nginx
  template:
    metadata:
      # ...
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.14.2
        # ...
        name: nginx
        ports:
        - containerPort: 80
        # ...
      # ...
    # ...
  # ...
```

### 如何更新对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#how-to-update-objects)

你也可以使用 `kubectl apply` 来更新某个目录中定义的所有对象，即使那些对象已经存在。 这一操作会隐含以下行为：

1. 在现时配置中设置配置文件中出现的字段；
2. 在现时配置中清除配置文件中已删除的字段。

```shell
kubectl diff -f <目录>/
kubectl apply -f <目录>/
```

**说明：** 使用 `-R` 标志递归处理目录。

下面是一个配置文件示例：

[`application/simple_deployment.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/simple_deployment.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/simple_deployment.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  minReadySeconds: 5
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

使用 `kubectl apply` 来创建对象：

```shell
kubectl apply -f https://k8s.io/examples/application/simple_deployment.yaml
```

**说明：** 出于演示的目的，上面的命令引用的是单个文件而不是整个目录。

使用 `kubectl get` 打印现时配置：

```shell
kubectl get -f https://k8s.io/examples/application/simple_deployment.yaml -o yaml
```

输出显示，注解 `kubectl.kubernetes.io/last-applied-configuration` 被写入到 现时配置中，并且其取值与配置文件内容相同。

```yaml
kind: Deployment
metadata:
  annotations:
    # ...
    # 此为 simple_deployment.yaml 的 JSON 表示
    # 在对象创建时由 kubectl apply 命令写入
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment",
      "metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},
      "spec":{"minReadySeconds":5,"selector":{"matchLabels":{"app":nginx}},"template":{"metadata":{"labels":{"app":"nginx"}},
      "spec":{"containers":[{"image":"nginx:1.14.2","name":"nginx",
      "ports":[{"containerPort":80}]}]}}}}      
  # ...
spec:
  # ...
  minReadySeconds: 5
  selector:
    matchLabels:
      # ...
      app: nginx
  template:
    metadata:
      # ...
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.14.2
        # ...
        name: nginx
        ports:
        - containerPort: 80
        # ...
      # ...
    # ...
  # ...
```

通过 `kubectl scale` 命令直接更新现时配置中的 `replicas` 字段。 这一命令没有使用 `kubectl apply`：

```shell
kubectl scale deployment/nginx-deployment --replicas=2
```

使用 `kubectl get` 来打印现时配置：

```shell
kubectl get deployment nginx-deployment -o yaml
```

输出显示，`replicas` 字段已经被设置为 2，而 `last-applied-configuration` 注解中 并不包含 `replicas` 字段。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    # ...
    # 注意注解中并不包含 replicas
    # 这是因为更新并不是通过 kubectl apply 来执行的
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment",
      "metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},
      "spec":{"minReadySeconds":5,"selector":{"matchLabels":{"app":nginx}},"template":{"metadata":{"labels":{"app":"nginx"}},
      "spec":{"containers":[{"image":"nginx:1.14.2","name":"nginx",
      "ports":[{"containerPort":80}]}]}}}}      
  # ...
spec:
  replicas: 2 # written by scale
  # ...
  minReadySeconds: 5
  selector:
    matchLabels:
      # ...
      app: nginx
  template:
    metadata:
      # ...
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.14.2
        # ...
        name: nginx
        ports:
        - containerPort: 80
      # ...
```

现在更新 `simple_deployment.yaml` 配置文件，将镜像文件从 `nginx:1.14.2` 更改为 `nginx:1.16.1`，同时删除`minReadySeconds` 字段：

[`application/update_deployment.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/update_deployment.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/update_deployment.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16.1 # 更新该镜像
        ports:
        - containerPort: 80
```

应用对配置文件所作更改：

```shell
kubectl diff -f https://k8s.io/examples/application/update_deployment.yaml
kubectl apply -f https://k8s.io/examples/application/update_deployment.yaml
```

使用 `kubectl get` 打印现时配置：

```shell
kubectl get -f https://k8s.io/examples/application/update_deployment.yaml -o yaml
```

输出显示现时配置中发生了以下更改：

- 字段 `replicas` 保留了 `kubectl scale` 命令所设置的值：2； 之所以该字段被保留是因为配置文件中并没有设置 `replicas`。
- 字段 `image` 的内容已经从 `nginx:1.14.2` 更改为 `nginx:1.16.1`。
- 注解 `last-applied-configuration` 内容被更改为新的镜像名称。
- 字段 `minReadySeconds` 被移除。
- 注解 `last-applied-configuration` 中不再包含 `minReadySeconds` 字段。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    # ...
    # 注解中包含更新后的镜像 nginx 1.16.1
    # 但是其中并不包含更改后的 replicas 值 2
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment",
      "metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},
      "spec":{"selector":{"matchLabels":{"app":nginx}},"template":{"metadata":{"labels":{"app":"nginx"}},
      "spec":{"containers":[{"image":"nginx:1.16.1","name":"nginx",
      "ports":[{"containerPort":80}]}]}}}}      
    # ...
spec:
  replicas: 2 # 由 `kubectl scale` 设置，被 `kubectl apply` 命令忽略
  # minReadySeconds 被 `kubectl apply` 清除
  # ...
  selector:
    matchLabels:
      # ...
      app: nginx
  template:
    metadata:
      # ...
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.16.1 # 由 `kubectl apply` 设置
        # ...
        name: nginx
        ports:
        - containerPort: 80
        # ...
      # ...
    # ...
  # ...
```

**警告：** 将 `kubectl apply` 与指令式对象配置命令 `kubectl create` 或 `kubectl replace` 混合使用是不受支持的。这是因为 `create` 和 `replace` 命令都不会保留 `kubectl apply` 用来计算更新内容所使用的 `kubectl.kubernetes.io/last-applied-configuration` 注解值。

### 如何删除对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#how-to-delete-objects)

有两种方法来删除 `kubectl apply` 管理的对象。

#### 建议操作：`kubectl delete -f <文件名>`[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%BB%BA%E8%AE%AE%E6%93%8D%E4%BD%9C-kubectl-delete-f-%E6%96%87%E4%BB%B6%E5%90%8D)

使用指令式命令来手动删除对象是建议的方法，因为这种方法更为明确地给出了 要删除的内容是什么，且不容易造成用户不小心删除了其他对象的情况。

```shell
kubectl delete -f <文件名>
```

#### 替代方式：`kubectl apply -f <目录名称/> --prune -l your=label`[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E6%9B%BF%E4%BB%A3%E6%96%B9%E5%BC%8F-kubectl-apply-f-%E7%9B%AE%E5%BD%95%E5%90%8D%E7%A7%B0-prune-l-your-label)

只有在充分理解此命令背后含义的情况下才建议这样操作。

**警告：** `kubectl apply --prune` 命令本身仍处于 Alpha 状态，在后续发布版本中可能会 引入一些向后不兼容的变化。

**警告：**

在使用此命令时必须小心，这样才不会无意中删除不想删除的对象。

作为 `kubectl delete` 操作的替代方式，你可以在目录中对象配置文件被删除之后， 使用 `kubectl apply` 来辩识要删除的对象。 带 `--prune` 标志的 `apply` 命令会首先查询 API 服务器，获得与某组标签相匹配 的对象列表，之后将返回的现时对象配置与目录中的对象配置文件相比较。 如果某对象在查询中被匹配到，但在目录中没有文件与其相对应，并且其中还包含 `last-applied-configuration` 注解，则该对象会被删除。

```shell
kubectl apply -f <directory/> --prune -l <labels>
```

**警告：** 带剪裁（prune）行为的 `apply` 操作应在包含对象配置文件的目录的根目录运行。 如果在其子目录中运行，可能导致对象被不小心删除。 因为某些对象可能与 `-l <标签>` 的标签选择算符匹配，但其配置文件不在当前 子目录下。

### 如何查看对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#how-to-view-an-object)

你可以使用 `kubectl get` 并指定 `-o yaml` 选项来查看现时对象的配置：

```shell
kubectl get -f <文件名 | URL> -o yaml
```

### apply 操作是如何计算配置差异并合并变更的？[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#apply-%E6%93%8D%E4%BD%9C%E6%98%AF%E5%A6%82%E4%BD%95%E8%AE%A1%E7%AE%97%E9%85%8D%E7%BD%AE%E5%B7%AE%E5%BC%82%E5%B9%B6%E5%90%88%E5%B9%B6%E5%8F%98%E6%9B%B4%E7%9A%84)

**注意：** _patch_ 是一种更新操作，其作用域为对象的一些特定字段而不是整个对象。 这使得你可以更新对象的特定字段集合而不必先要读回对象。

`kubectl apply` 更新对象的现时配置，它是通过向 API 服务器发送一个 patch 请求 来执行更新动作的。 所提交的补丁中定义了对现时对象配置中特定字段的更新。 `kubectl apply` 命令会使用当前的配置文件、现时配置以及现时配置中保存的 `last-applied-configuration` 注解内容来计算补丁更新内容。

#### 合并补丁计算[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#merge-patch-calculation)

`kubectl apply` 命令将配置文件的内容写入到 `kubectl.kubernetes.io/last-applied-configuration` 注解中。 这些内容用来识别配置文件中已经移除的、因而也需要从现时配置中删除的字段。 用来计算要删除或设置哪些字段的步骤如下：

1. 计算要删除的字段，即在 `last-applied-configuration` 中存在但在 配置文件中不再存在的字段。
2. 计算要添加或设置的字段，即在配置文件中存在但其取值与现时配置不同的字段。

下面是一个例子。假定此文件是某 Deployment 对象的配置文件：

[`application/update_deployment.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/update_deployment.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/update_deployment.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16.1 # 更新该镜像
        ports:
        - containerPort: 80
```

同时假定同一 Deployment 对象的现时配置如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    # ...
    # 注意注解中并不包含 replicas
    # 这是因为更新并不是通过 kubectl apply 来执行的
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment",
      "metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},
      "spec":{"minReadySeconds":5,"selector":{"matchLabels":{"app":nginx}},"template":{"metadata":{"labels":{"app":"nginx"}},
      "spec":{"containers":[{"image":"nginx:1.14.2","name":"nginx",
      "ports":[{"containerPort":80}]}]}}}}      
  # ...
spec:
  replicas: 2 # written by scale
  # ...
  minReadySeconds: 5
  selector:
    matchLabels:
      # ...
      app: nginx
  template:
    metadata:
      # ...
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.14.2
        # ...
        name: nginx
        ports:
        - containerPort: 80
      # ...
```

下面是 `kubectl apply` 将执行的合并计算：

1. 通过读取 `last-applied-configuration` 并将其与配置文件中的值相比较， 计算要删除的字段。 对于本地对象配置文件中显式设置为空的字段，清除其在现时配置中的设置， 无论这些字段是否出现在 `last-applied-configuration` 中。 在此例中，`minReadySeconds` 出现在 `last-applied-configuration` 注解中，但 并不存在于配置文件中。 **动作：** 从现时配置中删除 `minReadySeconds` 字段。
2. 通过读取配置文件中的值并将其与现时配置相比较，计算要设置的字段。 在这个例子中，配置文件中的 `image` 值与现时配置中的 `image` 不匹配。 **动作**：设置现时配置中的 `image` 值。
3. 设置 `last-applied-configuration` 注解的内容，使之与配置文件匹配。
4. 将第 1、2、3 步骤得出的结果合并，构成向 API 服务器发送的补丁请求内容。

下面是此合并操作之后形成的现时配置：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    # ...
    # 注解中包含更新后的镜像 nginx 1.16.1,
    # 但是其中并不包含更改后的 replicas 值 2
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment",
      "metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},
      "spec":{"selector":{"matchLabels":{"app":nginx}},"template":{"metadata":{"labels":{"app":"nginx"}},
      "spec":{"containers":[{"image":"nginx:1.16.1","name":"nginx",
      "ports":[{"containerPort":80}]}]}}}}      
    # ...
spec:
  selector:
    matchLabels:
      # ...
      app: nginx
  replicas: 2 # 由 `kubectl scale` 设置，被 `kubectl apply` 命令忽略
  # minReadySeconds  此字段被`kubectl apply`清除
  # ...
  template:
    metadata:
      # ...
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.16.1 # 由 `kubectl apply` 设置
        # ...
        name: nginx
        ports:
        - containerPort: 80
        # ...
      # ...
    # ...
  # ...
```

#### 不同类型字段的合并方式[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E4%B8%8D%E5%90%8C%E7%B1%BB%E5%9E%8B%E5%AD%97%E6%AE%B5%E7%9A%84%E5%90%88%E5%B9%B6%E6%96%B9%E5%BC%8F)

配置文件中的特定字段与现时配置合并时，合并方式取决于字段类型。 字段类型有几种：

- _基本类型_：字段类型为 `string`、`integer` 或 `boolean` 之一。 例如：`image` 和 `replicas` 字段都是基本类型字段。
    
    **动作：** 替换。
    
- _map_：也称作 _object_。类型为 `map` 或包含子域的复杂结构。例如，`labels`、 `annotations`、`spec` 和 `metadata` 都是 map。
    
    **动作：** 合并元素或子字段。
    
- _list_：包含元素列表的字段，其中每个元素可以是基本类型或 map。 例如，`containers`、`ports` 和 `args` 都是 list。
    
    **动作：** 不一定。
    

当 `kubectl apply` 更新某个 map 或 list 字段时，它通常不会替换整个字段，而是会 更新其中的各个子元素。例如，当合并 Deployment 的 `spec` 时，`kubectl` 并不会 将其整个替换掉。相反，实际操作会是对 `replicas` 这类 `spec` 的子字段来执行比较和更新。

#### 合并对基本类型字段的更新[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%90%88%E5%B9%B6%E5%AF%B9%E5%9F%BA%E6%9C%AC%E7%B1%BB%E5%9E%8B%E5%AD%97%E6%AE%B5%E7%9A%84%E6%9B%B4%E6%96%B0)

基本类型字段会被替换或清除。

**说明：** `-` 表示的是“不适用”，因为指定数值未被使用。

| 字段在对象配置文件中 | 字段在现时对象配置中 | 字段在 `last-applied-configuration` 中 | 动作 |
| --- | --- | --- | --- |
| 是 | 是 | \- | 将配置文件中值设置到现时配置上。 |
| 是 | 否 | \- | 将配置文件中值设置到现时配置上。 |
| 否 | \- | 是 | 从现时配置中移除。 |
| 否 | \- | 否 | 什么也不做。保持现时值。 |

#### 合并对 map 字段的变更[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%90%88%E5%B9%B6%E5%AF%B9-map-%E5%AD%97%E6%AE%B5%E7%9A%84%E5%8F%98%E6%9B%B4)

用来表示映射的字段在合并时会逐个子字段或元素地比较：

**说明：** `-` 表示的是“不适用”，因为指定数值未被使用。

| 键存在于对象配置文件中 | 键存在于现时对象配置中 | 键存在于 `last-applied-configuration` 中 | 动作 |
| --- | --- | --- | --- |
| 是 | 是 | \- | 比较子域取值。 |
| 是 | 否 | \- | 将现时配置设置为本地配置值。 |
| 否 | \- | 是 | 从现时配置中删除键。 |
| 否 | \- | 否 | 什么也不做，保留现时值。 |

#### 合并 list 类型字段的变更[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%90%88%E5%B9%B6-list-%E7%B1%BB%E5%9E%8B%E5%AD%97%E6%AE%B5%E7%9A%84%E5%8F%98%E6%9B%B4)

对 list 类型字段的变更合并会使用以下三种策略之一：

- 如果 list 所有元素都是基本类型则替换整个 list。
- 如果 list 中元素是复合结构则逐个元素执行合并操作。
- 合并基本类型元素构成的 list。

策略的选择是基于各个字段做出的。

##### 如果 list 中元素都是基本类型则替换整个 list[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%A6%82%E6%9E%9C-list-%E4%B8%AD%E5%85%83%E7%B4%A0%E9%83%BD%E6%98%AF%E5%9F%BA%E6%9C%AC%E7%B1%BB%E5%9E%8B%E5%88%99%E6%9B%BF%E6%8D%A2%E6%95%B4%E4%B8%AA-list)

将整个 list 视为一个基本类型字段。或者整个替换或者整个删除。 此操作会保持 list 中元素顺序不变

**示例：** 使用 `kubectl apply` 来更新 Pod 中 Container 的 `args` 字段。此操作会 将现时配置中的 `args` 值设为配置文件中的值。 所有之前添加到现时配置中的 `args` 元素都会丢失。 配置文件中的 `args` 元素的顺序在被添加到现时配置中时保持不变。

```yaml
# last-applied-configuration 值
    args: ["a", "b"]

# 配置文件值
    args: ["a", "c"]

# 现时配置
    args: ["a", "b", "d"]

# 合并结果
    args: ["a", "c"]
```

**解释：** 合并操作将配置文件中的值当做新的 list 值。

##### 如果 list 中元素为复合类型则逐个执行合并[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%A6%82%E6%9E%9C-list-%E4%B8%AD%E5%85%83%E7%B4%A0%E4%B8%BA%E5%A4%8D%E5%90%88%E7%B1%BB%E5%9E%8B%E5%88%99%E9%80%90%E4%B8%AA%E6%89%A7%E8%A1%8C%E5%90%88%E5%B9%B6)

此操作将 list 视为 map，并将每个元素中的特定字段当做其主键。 逐个元素地执行添加、删除或更新操作。结果顺序无法得到保证。

此合并策略会使用每个字段上的一个名为 `patchMergeKey` 的特殊标签。 Kubernetes 源代码中为每个字段定义了 `patchMergeKey`： [types.go](https://github.com/kubernetes/api/blob/d04500c8c3dda9c980b668c57abc2ca61efcf5c4/core/v1/types.go#L2747) 当合并由 map 组成的 list 时，给定元素中被设置为 `patchMergeKey` 的字段会被 当做该元素的 map 键值来使用。

**例如：** 使用 `kubectl apply` 来更新 Pod 规约中的 `containers` 字段。 此操作会将 `containers` 列表视作一个映射来执行合并，每个元素的主键为 `name`。

```yaml
# last-applied-configuration 值
    containers:
    - name: nginx
      image: nginx:1.16
    - name: nginx-helper-a # 键 nginx-helper-a 会被删除
      image: helper:1.3
    - name: nginx-helper-b # 键 nginx-helper-b 会被保留
      image: helper:1.3

# 配置文件值
    containers:
    - name: nginx
      image: nginx:1.16
    - name: nginx-helper-b
      image: helper:1.3
    - name: nginx-helper-c # 键 nginx-helper-c 会被添加
      image: helper:1.3

# 现时配置
    containers:
    - name: nginx
      image: nginx:1.16
    - name: nginx-helper-a
      image: helper:1.3
    - name: nginx-helper-b
      image: helper:1.3
      args: ["run"]        # 字段会被保留
    - name: nginx-helper-d # 键 nginx-helper-d 会被保留
      image: helper:1.3

# 合并结果
    containers:
    - name: nginx
      image: nginx:1.16
      # 元素 nginx-helper-a 被删除
    - name: nginx-helper-b
      image: helper:1.3
      args: ["run"]        # 字段被保留
    - name: nginx-helper-c # 新增元素
      image: helper:1.3
    - name: nginx-helper-d # 此元素被忽略（保留）
      image: helper:1.3
```

**解释：**

- 名为 "nginx-helper-a" 的容器被删除，因为配置文件中不存在同名的容器。
- 名为 "nginx-helper-b" 的容器的现时配置中的 `args` 被保留。 `kubectl apply` 能够辩识出现时配置中的容器 "nginx-helper-b" 与配置文件 中的容器 "nginx-helper-b" 相同，即使它们的字段值有些不同（配置文件中未给定 `args` 值）。这是因为 `patchMergeKey` 字段（name）的值在两个版本中都一样。
- 名为 "nginx-helper-c" 的容器是新增的，因为在配置文件中的这个容器尚不存在 于现时配置中。
- 名为 "nginx-helper-d" 的容器被保留下来，因为在 last-applied-configuration 中没有与之同名的元素。

##### 合并基本类型元素 list[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%90%88%E5%B9%B6%E5%9F%BA%E6%9C%AC%E7%B1%BB%E5%9E%8B%E5%85%83%E7%B4%A0-list)

在 Kubernetes 1.5 中，尚不支持对由基本类型元素构成的 list 进行合并。

**说明：** 选择上述哪种策略是由源码中给定字段的 `patchStrategy` 标记来控制的： [types.go](https://github.com/kubernetes/api/blob/d04500c8c3dda9c980b668c57abc2ca61efcf5c4/core/v1/types.go#L2748) 如果 list 类型字段未设置 `patchStrategy`，则整个 list 会被替换掉。

### 默认字段值[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#default-field-values)

API 服务器会在对象创建时其中某些字段未设置的情况下在现时配置中为其设置默认值。

下面是一个 Deployment 的配置文件。文件未设置 `strategy`：

[`application/simple_deployment.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/simple_deployment.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/simple_deployment.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  minReadySeconds: 5
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

使用 `kubectl apply` 创建对象：

```shell
kubectl apply -f https://k8s.io/examples/application/simple_deployment.yaml
```

使用 `kubectl get` 打印现时配置：

```shell
kubectl get -f https://k8s.io/examples/application/simple_deployment.yaml -o yaml
```

输出显示 API 在现时配置中为某些字段设置了默认值。 这些字段在配置文件中并未设置。

```yaml
apiVersion: apps/v1
kind: Deployment
# ...
spec:
  selector:
    matchLabels:
      app: nginx
  minReadySeconds: 5
  replicas: 1           # API 服务器所设默认值
  strategy:
    rollingUpdate:      # API 服务器基于 strategy.type 所设默认值
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate # API 服务器所设默认值
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.14.2
        imagePullPolicy: IfNotPresent    # API 服务器所设默认值
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP       # API 服务器所设默认值
        resources: {}         # API 服务器所设默认值
        terminationMessagePath: /dev/termination-log    # API 服务器所设默认值
      dnsPolicy: ClusterFirst       # API 服务器所设默认值
      restartPolicy: Always         # API 服务器所设默认值
      securityContext: {}           # API 服务器所设默认值
      terminationGracePeriodSeconds: 30        # API 服务器所设默认值
# ...
```

在补丁请求中，已经设置了默认值的字段不会被重新设回其默认值，除非 在补丁请求中显式地要求清除。对于默认值取决于其他字段的某些字段而言， 这可能会引发一些意想不到的行为。当所依赖的其他字段后来发生改变时， 基于它们所设置的默认值只能在显式执行清除操作时才会被更新。

为此，建议在配置文件中为服务器设置默认值的字段显式提供定义，即使所 给的定义与服务器端默认值设定相同。这样可以使得辩识无法被服务器重新 基于默认值来设置的冲突字段变得容易。

**示例：**

```yaml
# last-applied-configuration
spec:
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

# 配置文件
spec:
  strategy:
    type: Recreate   # 更新的值
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

# 现时配置
spec:
  strategy:
    type: RollingUpdate    # 默认设置的值
    rollingUpdate:         # 基于 type 设置的默认值
      maxSurge : 1
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

# 合并后的结果 - 出错！
spec:
  strategy:
    type: Recreate     # 更新的值：与 rollingUpdate 不兼容
    rollingUpdate:     # 默认设置的值：与 "type: Recreate" 冲突
      maxSurge : 1
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

**解释：**

1. 用户创建 Deployment，未设置 `strategy.type`。
2. 服务器为 `strategy.type` 设置默认值 `RollingUpdate`，并为 `strategy.rollingUpdate` 设置默认值。
3. 用户改变 `strategy.type` 为 `Recreate`。字段 `strategy.rollingUpdate` 仍会取其 默认设置值，尽管服务器期望该字段被清除。 如果 `strategy.rollingUpdate` 值最初于配置文件中定义，则它们需要被清除 这一点就更明确一些。
4. `apply` 操作失败，因为 `strategy.rollingUpdate` 未被清除。 `strategy.rollingupdate` 在 `strategy.type` 为 `Recreate` 不可被设定。

建议：以下字段应该在对象配置文件中显式定义：

- 如 Deployment、StatefulSet、Job、DaemonSet、ReplicaSet 和 ReplicationController 这类负载的选择算符和 `PodTemplate` 标签
- Deployment 的上线策略

#### 如何清除服务器端按默认值设置的字段或者被其他写者设置的字段[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%A6%82%E4%BD%95%E6%B8%85%E9%99%A4%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E6%8C%89%E9%BB%98%E8%AE%A4%E5%80%BC%E8%AE%BE%E7%BD%AE%E7%9A%84%E5%AD%97%E6%AE%B5%E6%88%96%E8%80%85%E8%A2%AB%E5%85%B6%E4%BB%96%E5%86%99%E8%80%85%E8%AE%BE%E7%BD%AE%E7%9A%84%E5%AD%97%E6%AE%B5)

没有出现在配置文件中的字段可以通过将其值设置为 `null` 并应用配置文件来清除。 对于由服务器按默认值设置的字段，清除操作会触发重新为字段设置新的默认值。

### 如何将字段的属主在配置文件和直接指令式写者之间切换[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%A6%82%E4%BD%95%E5%B0%86%E5%AD%97%E6%AE%B5%E7%9A%84%E5%B1%9E%E4%B8%BB%E5%9C%A8%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%92%8C%E7%9B%B4%E6%8E%A5%E6%8C%87%E4%BB%A4%E5%BC%8F%E5%86%99%E8%80%85%E4%B9%8B%E9%97%B4%E5%88%87%E6%8D%A2)

更改某个对象字段时，应该采用下面的方法：

- 使用 `kubectl apply`.
- 直接写入到现时配置，但不更改配置文件本身，例如使用 `kubectl scale`。

#### 将属主从直接指令式写者更改为配置文件[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%B0%86%E5%B1%9E%E4%B8%BB%E4%BB%8E%E7%9B%B4%E6%8E%A5%E6%8C%87%E4%BB%A4%E5%BC%8F%E5%86%99%E8%80%85%E6%9B%B4%E6%94%B9%E4%B8%BA%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)

将字段添加到配置文件。针对该字段，不再直接执行对现时配置的修改。 修改均通过 `kubectl apply` 来执行。

#### 将属主从配置文件改为直接指令式写者[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%B0%86%E5%B1%9E%E4%B8%BB%E4%BB%8E%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E6%94%B9%E4%B8%BA%E7%9B%B4%E6%8E%A5%E6%8C%87%E4%BB%A4%E5%BC%8F%E5%86%99%E8%80%85)

在 Kubernetes 1.5 中，将字段的属主从配置文件切换到某指令式写者需要手动 执行以下步骤：

- 从配置文件中删除该字段；
- 将字段从现时对象的 `kubectl.kubernetes.io/last-applied-configuration` 注解 中删除。

### 更改管理方法[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#changing-management-methods)

Kubernetes 对象在同一时刻应该只用一种方法来管理。 从一种方法切换到另一种方法是可能的，但这一切换是一个手动过程。

**说明：** 在声明式管理方法中使用指令式命令来删除对象是可以的。

#### 从指令式命令管理切换到声明式对象配置[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E4%BB%8E%E6%8C%87%E4%BB%A4%E5%BC%8F%E5%91%BD%E4%BB%A4%E7%AE%A1%E7%90%86%E5%88%87%E6%8D%A2%E5%88%B0%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%AF%B9%E8%B1%A1%E9%85%8D%E7%BD%AE)

从指令式命令管理切换到声明式对象配置管理的切换包含以下几个手动步骤：

1. 将现时对象导出到本地配置文件：
    
    ```shell
    kubectl get <kind>/<name> -o yaml > <kind>_<name>.yaml
    ```
    
2. 手动移除配置文件中的 `status` 字段。
    
    **说明：** 这一步骤是可选的，因为 `kubectl apply` 并不会更新 status 字段，即便 配置文件中包含 status 字段。
    
3. 设置对象上的 `kubectl.kubernetes.io/last-applied-configuration` 注解：
    
    ```shell
    kubectl replace --save-config -f <kind>_<name>.yaml
    ```
    
4. 更改过程，使用 `kubectl apply` 专门管理对象。
    

#### 从指令式对象配置切换到声明式对象配置[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E4%BB%8E%E6%8C%87%E4%BB%A4%E5%BC%8F%E5%AF%B9%E8%B1%A1%E9%85%8D%E7%BD%AE%E5%88%87%E6%8D%A2%E5%88%B0%E5%A3%B0%E6%98%8E%E5%BC%8F%E5%AF%B9%E8%B1%A1%E9%85%8D%E7%BD%AE)

1. 在对象上设置 `kubectl.kubernetes.io/last-applied-configuration` 注解：
    
    ```shell
    kubectl replace --save-config -f <kind>_<name>.yaml
    ```
    
2. 自此排他性地使用 `kubectl apply` 来管理对象。
    

### 定义控制器选择算符和 PodTemplate 标签[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E5%AE%9A%E4%B9%89%E6%8E%A7%E5%88%B6%E5%99%A8%E9%80%89%E6%8B%A9%E7%AE%97%E7%AC%A6%E5%92%8C-podtemplate-%E6%A0%87%E7%AD%BE)

**警告：** 强烈不建议更改控制器上的选择算符。

建议的方法是定义一个不可变更的 PodTemplate 标签，仅用于控制器选择算符且 不包含其他语义性的含义。

**示例：**

```yaml
selector:
  matchLabels:
      controller-selector: "apps/v1/deployment/nginx"
template:
  metadata:
    labels:
      controller-selector: "apps/v1/deployment/nginx"
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [使用指令式命令管理 Kubernetes 对象](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/)
- [使用配置文件对 Kubernetes 对象执行指令式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/)
- [Kubectl 命令参考](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/)
- [Kubernetes API 参考](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/)
## [使用 Kustomize 对 Kubernetes 对象进行声明式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/)
[Kustomize](https://github.com/kubernetes-sigs/kustomize) 是一个独立的工具，用来通过 [kustomization 文件](https://kubectl.docs.kubernetes.io/references/kustomize/glossary/#kustomization) 定制 Kubernetes 对象。

从 1.14 版本开始，`kubectl` 也开始支持使用 kustomization 文件来管理 Kubernetes 对象。 要查看包含 kustomization 文件的目录中的资源，执行下面的命令：

```shell
kubectl kustomize <kustomization_directory>
```

要应用这些资源，使用 `--kustomize` 或 `-k` 参数来执行 `kubectl apply`：

```shell
kubectl apply -k <kustomization_directory>
```

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

安装 [`kubectl`](https://kubernetes.io/zh-cn/docs/tasks/tools/)。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### Kustomize 概述[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#overview-of-kustomize)

Kustomize 是一个用来定制 Kubernetes 配置的工具。它提供以下功能特性来管理应用配置文件：

- 从其他来源生成资源
- 为资源设置贯穿性（Cross-Cutting）字段
- 组织和定制资源集合

#### 生成资源[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#generating-resources)

ConfigMap 和 Secret 包含其他 Kubernetes 对象（如 Pod）所需要的配置或敏感数据。 ConfigMap 或 Secret 中数据的来源往往是集群外部，例如某个 `.properties` 文件或者 SSH 密钥文件。 Kustomize 提供 `secretGenerator` 和 `configMapGenerator`，可以基于文件或字面值来生成 Secret 和 ConfigMap。

##### configMapGenerator[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#configmapgenerator)

要基于文件来生成 ConfigMap，可以在 `configMapGenerator` 的 `files` 列表中添加表项。 下面是一个根据 `.properties` 文件中的数据条目来生成 ConfigMap 的示例：

```shell
# 生成一个  application.properties 文件
cat <<EOF >application.properties
FOO=Bar
EOF

cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - application.properties
EOF
```

所生成的 ConfigMap 可以使用下面的命令来检查：

```shell
kubectl kustomize ./
```

所生成的 ConfigMap 为：

```yaml
apiVersion: v1
data:
  application.properties: |
        FOO=Bar
kind: ConfigMap
metadata:
  name: example-configmap-1-8mbdf7882g
```

要从 env 文件生成 ConfigMap，请在 `configMapGenerator` 中的 `envs` 列表中添加一个条目。 下面是一个用来自 `.env` 文件的数据生成 ConfigMap 的例子：

```shell
# 创建一个 .env 文件
cat <<EOF >.env
FOO=Bar
EOF

cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  envs:
  - .env
EOF
```

可以使用以下命令检查生成的 ConfigMap：

```shell
kubectl kustomize ./
```

生成的 ConfigMap 为：

```yaml
apiVersion: v1
data:
  FOO: Bar
kind: ConfigMap
metadata:
  name: example-configmap-1-42cfbf598f
```

**说明：**

`.env` 文件中的每个变量在生成的 ConfigMap 中成为一个单独的键。这与之前的示例不同， 前一个示例将一个名为 `application.properties` 的文件（及其所有条目）嵌入到同一个键的值中。

ConfigMap 也可基于字面的键值偶对来生成。要基于键值偶对来生成 ConfigMap， 在 `configMapGenerator` 的 `literals` 列表中添加表项。下面是一个例子， 展示如何使用键值偶对中的数据条目来生成 ConfigMap 对象：

```shell
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-2
  literals:
  - FOO=Bar
EOF
```

可以用下面的命令检查所生成的 ConfigMap：

```shell
kubectl kustomize ./
```

所生成的 ConfigMap 为：

```yaml
apiVersion: v1
data:
  FOO: Bar
kind: ConfigMap
metadata:
  name: example-configmap-2-g2hdhfc6tk
```

要在 Deployment 中使用生成的 ConfigMap，使用 configMapGenerator 的名称对其进行引用。 Kustomize 将自动使用生成的名称替换该名称。

这是使用生成的 ConfigMap 的 deployment 示例：

```yaml
# 创建一个 application.properties 文件
cat <<EOF >application.properties
FOO=Bar
EOF

cat <<EOF >deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: my-app
        volumeMounts:
        - name: config
          mountPath: /config
      volumes:
      - name: config
        configMap:
          name: example-configmap-1
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - application.properties
EOF
```

生成 ConfigMap 和 Deployment：

```shell
kubectl kustomize ./
```

生成的 Deployment 将通过名称引用生成的 ConfigMap：

```yaml
apiVersion: v1
data:
  application.properties: |
        FOO=Bar
kind: ConfigMap
metadata:
  name: example-configmap-1-g4hk9g2ff8
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-app
  name: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - image: my-app
        name: app
        volumeMounts:
        - mountPath: /config
          name: config
      volumes:
      - configMap:
          name: example-configmap-1-g4hk9g2ff8
        name: config
```

##### secretGenerator[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#secretgenerator)

你可以基于文件或者键值偶对来生成 Secret。要使用文件内容来生成 Secret， 在 `secretGenerator` 下面的 `files` 列表中添加表项。 下面是一个根据文件中数据来生成 Secret 对象的示例：

```shell
# 创建一个 password.txt 文件
cat <<EOF >./password.txt
username=admin
password=secret
EOF

cat <<EOF >./kustomization.yaml
secretGenerator:
- name: example-secret-1
  files:
  - password.txt
EOF
```

所生成的 Secret 如下：

```yaml
apiVersion: v1
data:
  password.txt: dXNlcm5hbWU9YWRtaW4KcGFzc3dvcmQ9c2VjcmV0Cg==
kind: Secret
metadata:
  name: example-secret-1-t2kt65hgtb
type: Opaque
```

要基于键值偶对字面值生成 Secret，先要在 `secretGenerator` 的 `literals` 列表中添加表项。下面是基于键值偶对中数据条目来生成 Secret 的示例：

```shell
cat <<EOF >./kustomization.yaml
secretGenerator:
- name: example-secret-2
  literals:
  - username=admin
  - password=secret
EOF
```

所生成的 Secret 如下：

```yaml
apiVersion: v1
data:
  password: c2VjcmV0
  username: YWRtaW4=
kind: Secret
metadata:
  name: example-secret-2-t52t6g96d8
type: Opaque
```

与 ConfigMap 一样，生成的 Secret 可以通过引用 secretGenerator 的名称在 Deployment 中使用：

```shell
# 创建一个 password.txt 文件
cat <<EOF >./password.txt
username=admin
password=secret
EOF

cat <<EOF >deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: my-app
        volumeMounts:
        - name: password
          mountPath: /secrets
      volumes:
      - name: password
        secret:
          secretName: example-secret-1
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
secretGenerator:
- name: example-secret-1
  files:
  - password.txt
EOF
```

##### generatorOptions[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#generatoroptions)

所生成的 ConfigMap 和 Secret 都会包含内容哈希值后缀。 这是为了确保内容发生变化时，所生成的是新的 ConfigMap 或 Secret。 要禁止自动添加后缀的行为，用户可以使用 `generatorOptions`。 除此以外，为生成的 ConfigMap 和 Secret 指定贯穿性选项也是可以的。

```shell
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-3
  literals:
  - FOO=Bar
generatorOptions:
  disableNameSuffixHash: true
  labels:
    type: generated
  annotations:
    note: generated
EOF
```

运行 `kubectl kustomize ./` 来查看所生成的 ConfigMap：

```yaml
apiVersion: v1
data:
  FOO: Bar
kind: ConfigMap
metadata:
  annotations:
    note: generated
  labels:
    type: generated
  name: example-configmap-3
```

#### 设置贯穿性字段[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#setting-cross-cutting-fields)

在项目中为所有 Kubernetes 对象设置贯穿性字段是一种常见操作。 贯穿性字段的一些使用场景如下：

- 为所有资源设置相同的名字空间
- 为所有对象添加相同的前缀或后缀
- 为对象添加相同的标签集合
- 为对象添加相同的注解集合

下面是一个例子：

```shell
# 创建一个 deployment.yaml
cat <<EOF >./deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
EOF

cat <<EOF >./kustomization.yaml
namespace: my-namespace
namePrefix: dev-
nameSuffix: "-001"
commonLabels:
  app: bingo
commonAnnotations:
  oncallPager: 800-555-1212
resources:
- deployment.yaml
EOF
```

执行 `kubectl kustomize ./` 查看这些字段都被设置到 Deployment 资源上：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    oncallPager: 800-555-1212
  labels:
    app: bingo
  name: dev-nginx-deployment-001
  namespace: my-namespace
spec:
  selector:
    matchLabels:
      app: bingo
  template:
    metadata:
      annotations:
        oncallPager: 800-555-1212
      labels:
        app: bingo
    spec:
      containers:
      - image: nginx
        name: nginx
```

#### 组织和定制资源[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#composing-and-customizing-resources)

一种常见的做法是在项目中构造资源集合并将其放到同一个文件或目录中管理。 Kustomize 提供基于不同文件来组织资源并向其应用补丁或者其他定制的能力。

##### 组织[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#composing)

Kustomize 支持组合不同的资源。`kustomization.yaml` 文件的 `resources` 字段定义配置中要包含的资源列表。 你可以将 `resources` 列表中的路径设置为资源配置文件的路径。 下面是由 Deployment 和 Service 构成的 NGINX 应用的示例：

```shell
# 创建 deployment.yaml 文件
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# 创建 service.yaml 文件
cat <<EOF > service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
EOF

# 创建 kustomization.yaml 来组织以上两个资源
cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
- service.yaml
EOF
```

`kubectl kustomize ./` 所得到的资源中既包含 Deployment 也包含 Service 对象。

##### 定制[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#customizing)

补丁文件（Patches）可以用来对资源执行不同的定制。 Kustomize 通过 `patchesStrategicMerge` 和 `patchesJson6902` 支持不同的打补丁机制。 `patchesStrategicMerge` 的内容是一个文件路径的列表，其中每个文件都应可解析为 [策略性合并补丁（Strategic Merge Patch）](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-api-machinery/strategic-merge-patch.md)。 补丁文件中的名称必须与已经加载的资源的名称匹配。 建议构造规模较小的、仅做一件事情的补丁。 例如，构造一个补丁来增加 Deployment 的副本个数；构造另外一个补丁来设置内存限制。

```shell
# 创建 deployment.yaml 文件
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# 生成一个补丁 increase_replicas.yaml
cat <<EOF > increase_replicas.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3
EOF

# 生成另一个补丁 set_memory.yaml
cat <<EOF > set_memory.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  template:
    spec:
      containers:
      - name: my-nginx
        resources:
          limits:
            memory: 512Mi
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
patchesStrategicMerge:
- increase_replicas.yaml
- set_memory.yaml
EOF
```

执行 `kubectl kustomize ./` 来查看 Deployment：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      run: my-nginx
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - image: nginx
        name: my-nginx
        ports:
        - containerPort: 80
        resources:
          limits:
            memory: 512Mi
```

并非所有资源或者字段都支持策略性合并补丁。为了支持对任何资源的任何字段进行修改， Kustomize 提供通过 `patchesJson6902` 来应用 [JSON 补丁](https://tools.ietf.org/html/rfc6902)的能力。 为了给 JSON 补丁找到正确的资源，需要在 `kustomization.yaml` 文件中指定资源的组（group）、 版本（version）、类别（kind）和名称（name）。 例如，为某 Deployment 对象增加副本个数的操作也可以通过 `patchesJson6902` 来完成：

```shell
# 创建一个 deployment.yaml 文件
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# 创建一个 JSON 补丁文件
cat <<EOF > patch.yaml
- op: replace
  path: /spec/replicas
  value: 3
EOF

# 创建一个 kustomization.yaml
cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml

patchesJson6902:
- target:
    group: apps
    version: v1
    kind: Deployment
    name: my-nginx
  path: patch.yaml
EOF
```

执行 `kubectl kustomize ./` 以查看 `replicas` 字段被更新：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      run: my-nginx
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - image: nginx
        name: my-nginx
        ports:
        - containerPort: 80
```

除了补丁之外，Kustomize 还提供定制容器镜像或者将其他对象的字段值注入到容器中的能力，并且不需要创建补丁。 例如，你可以通过在 `kustomization.yaml` 文件的 `images` 字段设置新的镜像来更改容器中使用的镜像。

```shell
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
images:
- name: nginx
  newName: my.image.registry/nginx
  newTag: 1.4.0
EOF
```

执行 `kubectl kustomize ./` 以查看所使用的镜像已被更新：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      run: my-nginx
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - image: my.image.registry/nginx:1.4.0
        name: my-nginx
        ports:
        - containerPort: 80
```

有些时候，Pod 中运行的应用可能需要使用来自其他对象的配置值。 例如，某 Deployment 对象的 Pod 需要从环境变量或命令行参数中读取读取 Service 的名称。 由于在 `kustomization.yaml` 文件中添加 `namePrefix` 或 `nameSuffix` 时 Service 名称可能发生变化，建议不要在命令参数中硬编码 Service 名称。 对于这种使用场景，Kustomize 可以通过 `vars` 将 Service 名称注入到容器中。

```shell
# 创建一个 deployment.yaml 文件（引用此处的文档分隔符）
cat <<'EOF' > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        command: ["start", "--host", "$(MY_SERVICE_NAME)"]
EOF

# 创建一个 service.yaml 文件
cat <<EOF > service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
EOF

cat <<EOF >./kustomization.yaml
namePrefix: dev-
nameSuffix: "-001"

resources:
- deployment.yaml
- service.yaml

vars:
- name: MY_SERVICE_NAME
  objref:
    kind: Service
    name: my-nginx
    apiVersion: v1
EOF
```

执行 `kubectl kustomize ./` 以查看注入到容器中的 Service 名称是 `dev-my-nginx-001`：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dev-my-nginx-001
spec:
  replicas: 2
  selector:
    matchLabels:
      run: my-nginx
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - command:
        - start
        - --host
        - dev-my-nginx-001
        image: nginx
        name: my-nginx
```

### 基准（Bases）与覆盖（Overlays）[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#%E5%9F%BA%E5%87%86-bases-%E4%B8%8E%E8%A6%86%E7%9B%96-overlays)

Kustomize 中有 **基准（bases）** 和 **覆盖（overlays）** 的概念区分。 **基准** 是包含 `kustomization.yaml` 文件的一个目录，其中包含一组资源及其相关的定制。 基准可以是本地目录或者来自远程仓库的目录，只要其中存在 `kustomization.yaml` 文件即可。 **覆盖** 也是一个目录，其中包含将其他 kustomization 目录当做 `bases` 来引用的 `kustomization.yaml` 文件。 **基准**不了解覆盖的存在，且可被多个覆盖所使用。 覆盖则可以有多个基准，且可针对所有基准中的资源执行组织操作，还可以在其上执行定制。

```shell
# 创建一个包含基准的目录 
mkdir base
# 创建 base/deployment.yaml
cat <<EOF > base/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
EOF

# 创建 base/service.yaml 文件
cat <<EOF > base/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
EOF

# 创建 base/kustomization.yaml
cat <<EOF > base/kustomization.yaml
resources:
- deployment.yaml
- service.yaml
EOF
```

此基准可在多个覆盖中使用。你可以在不同的覆盖中添加不同的 `namePrefix` 或其他贯穿性字段。 下面是两个使用同一基准的覆盖：

```shell
mkdir dev
cat <<EOF > dev/kustomization.yaml
bases:
- ../base
namePrefix: dev-
EOF

mkdir prod
cat <<EOF > prod/kustomization.yaml
bases:
- ../base
namePrefix: prod-
EOF
```

### 如何使用 Kustomize 来应用、查看和删除对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-kustomize-%E6%9D%A5%E5%BA%94%E7%94%A8-%E6%9F%A5%E7%9C%8B%E5%92%8C%E5%88%A0%E9%99%A4%E5%AF%B9%E8%B1%A1)

在 `kubectl` 命令中使用 `--kustomize` 或 `-k` 参数来识别被 `kustomization.yaml` 所管理的资源。 注意 `-k` 要指向一个 kustomization 目录。例如：

```shell
kubectl apply -k <kustomization 目录>/
```

假定使用下面的 `kustomization.yaml`：

```shell
# 创建 deployment.yaml 文件
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF

# 创建 kustomization.yaml
cat <<EOF >./kustomization.yaml
namePrefix: dev-
commonLabels:
  app: my-nginx
resources:
- deployment.yaml
EOF
```

执行下面的命令来应用 Deployment 对象 `dev-my-nginx`：

```shell
> kubectl apply -k ./
deployment.apps/dev-my-nginx created
```

运行下面的命令之一来查看 Deployment 对象 `dev-my-nginx`：

```shell
kubectl get -k ./
```

```shell
kubectl describe -k ./
```

执行下面的命令来比较 Deployment 对象 `dev-my-nginx` 与清单被应用之后集群将处于的状态：

```shell
kubectl diff -k ./
```

执行下面的命令删除 Deployment 对象 `dev-my-nginx`：

```shell
> kubectl delete -k ./
deployment.apps "dev-my-nginx" deleted
```

### Kustomize 功能特性列表[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#kustomize-feature-list)

| 字段 | 类型 | 解释 |
| --- | --- | --- |
| namespace | string | 为所有资源添加名字空间 |
| namePrefix | string | 此字段的值将被添加到所有资源名称前面 |
| nameSuffix | string | 此字段的值将被添加到所有资源名称后面 |
| commonLabels | map\[string\]string | 要添加到所有资源和选择算符的标签 |
| commonAnnotations | map\[string\]string | 要添加到所有资源的注解 |
| resources | \[\]string | 列表中的每个条目都必须能够解析为现有的资源配置文件 |
| configMapGenerator | \[\][ConfigMapArgs](https://github.com/kubernetes-sigs/kustomize/blob/master/api/types/configmapargs.go#L7) | 列表中的每个条目都会生成一个 ConfigMap |
| secretGenerator | \[\][SecretArgs](https://github.com/kubernetes-sigs/kustomize/blob/master/api/types/secretargs.go#L7) | 列表中的每个条目都会生成一个 Secret |
| generatorOptions | [GeneratorOptions](https://github.com/kubernetes-sigs/kustomize/blob/master/api/types/generatoroptions.go#L7) | 更改所有 ConfigMap 和 Secret 生成器的行为 |
| bases | \[\]string | 列表中每个条目都应能解析为一个包含 kustomization.yaml 文件的目录 |
| patchesStrategicMerge | \[\]string | 列表中每个条目都能解析为某 Kubernetes 对象的策略性合并补丁 |
| patchesJson6902 | \[\][Patch](https://github.com/kubernetes-sigs/kustomize/blob/master/api/types/patch.go#L10) | 列表中每个条目都能解析为一个 Kubernetes 对象和一个 JSON 补丁 |
| vars | \[\][Var](https://github.com/kubernetes-sigs/kustomize/blob/master/api/types/var.go#L19) | 每个条目用来从某资源的字段来析取文字 |
| images | \[\][Image](https://github.com/kubernetes-sigs/kustomize/blob/master/api/types/image.go#L8) | 每个条目都用来更改镜像的名称、标记与/或摘要，不必生成补丁 |
| configurations | \[\]string | 列表中每个条目都应能解析为一个包含 [Kustomize 转换器配置](https://github.com/kubernetes-sigs/kustomize/tree/master/examples/transformerconfigs) 的文件 |
| crds | \[\]string | 列表中每个条目都应能够解析为 Kubernetes 类别的 OpenAPI 定义文件 |

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [Kustomize](https://github.com/kubernetes-sigs/kustomize)
- [Kubectl Book](https://kubectl.docs.kubernetes.io/)
- [Kubectl 命令参考](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/)
- [Kubernetes API 参考](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/)
## [使用指令式命令管理 Kubernetes 对象](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/)
使用构建在 `kubectl` 命令行工具中的指令式命令可以直接快速创建、更新和删除 Kubernetes 对象。本文档解释这些命令的组织方式以及如何使用它们来管理活跃对象。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

安装[`kubectl`](https://kubernetes.io/zh-cn/docs/tasks/tools/)。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 权衡取舍[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#trade-offs)

`kubectl` 工具能够支持三种对象管理方式：

- 指令式命令
- 指令式对象配置
- 声明式对象配置

关于每种对象管理的优缺点的讨论，可参见 [Kubernetes 对象管理](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/object-management/)。

### 如何创建对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#how-to-create-objects)

`kubectl` 工具支持动词驱动的命令，用来创建一些最常见的对象类别。 命令的名称设计使得不熟悉 Kubernetes 对象类型的用户也能做出判断。

- `run`：创建一个新的 Pod 来运行一个容器。
- `expose`：创建一个新的 Service 对象为若干 Pod 提供流量负载均衡。
- `autoscale`：创建一个新的 Autoscaler 对象来自动对某控制器（例如：Deployment） 执行水平扩缩。

`kubectl` 命令也支持一些对象类型驱动的创建命令。 这些命令可以支持更多的对象类别，并且在其动机上体现得更为明显， 不过要求用户了解它们所要创建的对象的类别。

- `create <对象类别> [<子类别>] <实例名称>`

某些对象类别拥有自己的子类别，可以在 `create` 命令中设置。 例如，Service 对象有 ClusterIP、LoadBalancer 和 NodePort 三种子类别。 下面是一个创建 NodePort 子类别的 Service 的示例：

```shell
kubectl create service nodeport <服务名称>
```

在前述示例中，`create service nodeport` 命令也称作 `create service` 命令的子命令。 可以使用 `-h` 标志找到一个子命令所支持的参数和标志。

```shell
kubectl create service nodeport -h
```

### 如何更新对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#how-to-update-objects)

`kubectl` 命令也支持一些动词驱动的命令，用来执行一些常见的更新操作。 这些命令的设计是为了让一些不了解 Kubernetes 对象的用户也能执行更新操作， 但不需要了解哪些字段必须设置：

- `scale`：对某控制器进行水平扩缩以便通过更新控制器的副本个数来添加或删除 Pod。
- `annotate`：为对象添加或删除注解。
- `label`：为对象添加或删除标签。

`kubectl` 命令也支持由对象的某一方面来驱动的更新命令。 设置对象的这一方面可能对不同类别的对象意味着不同的字段：

- `set <字段>`：设置对象的某一方面。

**说明：**

在 Kubernetes 1.5 版本中，并非所有动词驱动的命令都有对应的方面驱动的命令。

`kubectl` 工具支持以下额外的方式用来直接更新活跃对象，不过这些操作要求 用户对 Kubernetes 对象的模式定义有很好的了解：

- `edit`：通过在编辑器中打开活跃对象的配置，直接编辑其原始配置。
- `patch`：通过使用补丁字符串（Patch String）直接更改某活跃对象的特定字段。 关于补丁字符串的更详细信息，参见 [API 约定](https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#patch-operations) 的 patch 节。

### 如何删除对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#how-to-delete-objects)

你可以使用 `delete` 命令从集群中删除一个对象：

- `delete <类别>/<名称>`

**说明：**

你可以使用 `kubectl delete` 来执行指令式命令或者指令式对象配置。不同之处在于传递给命令的参数。 要将 `kubectl delete` 作为指令式命令使用，将要删除的对象作为参数传递给它。 下面是一个删除名为 `nginx` 的 Deployment 对象的命令：

```shell
kubectl delete deployment/nginx
```

### 如何查看对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#how-to-view-an-object)

用来打印对象信息的命令有好几个：

- `get`：打印匹配到的对象的基本信息。使用 `get -h` 可以查看选项列表。
- `describe`：打印匹配到的对象的详细信息的汇集版本。
- `logs`：打印 Pod 中运行的容器的 stdout 和 stderr 输出。

### 使用 `set` 命令在创建对象之前修改对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#using-set-commands-to-modify-objects-before-creation)

有些对象字段在 `create` 命令中没有对应的标志。 在这些场景中，你可以使用 `set` 和 `create` 命令的组合来在对象创建之前设置字段值。 这是通过将 `create` 命令的输出用管道方式传递给 `set` 命令来实现的，最后执行 `create` 命令来创建对象。 下面是一个例子：

```sh
kubectl create service clusterip my-svc --clusterip="None" -o yaml --dry-run=client | kubectl set selector --local -f - 'environment=qa' -o yaml | kubectl create -f -
```

1. 命令 `kubectl create service -o yaml --dry-run=client` 创建 Service 的配置， 但将其以 YAML 格式在标准输出上打印而不是发送给 API 服务器。
2. 命令 `kubectl set selector --local -f - -o yaml` 从标准输入读入配置， 并将更新后的配置以 YAML 格式输出到标准输出。
3. 命令 `kubectl create -f -` 使用标准输入上获得的配置创建对象。

### 在创建之前使用 `--edit` 更改对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#using-edit-to-modify-objects-before-creation)

你可以用 `kubectl create --edit` 来在对象被创建之前执行任意的变更。 下面是一个例子：

```sh
kubectl create service clusterip my-svc --clusterip="None" -o yaml --dry-run=client > /tmp/srv.yaml
kubectl create --edit -f /tmp/srv.yaml
```

1. 命令 `kubectl create service` 创建 Service 的配置并将其保存到 `/tmp/srv.yaml` 文件。
2. 命令 `kubectl create --edit` 在创建 Service 对象打开其配置文件进行编辑。

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [使用配置文件对 Kubernetes 对象进行命令式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/)
- [使用配置文件对 Kubernetes 对象进行声明式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/)
- [Kubectl 命令参考](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/)
- [Kubernetes API 参考](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/)
## [使用配置文件对 Kubernetes 对象进行命令式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/)
可以使用 `kubectl` 命令行工具以及用 YAML 或 JSON 编写的对象配置文件来创建、更新和删除 Kubernetes 对象。 本文档说明了如何使用配置文件定义和管理对象。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

安装 [`kubectl`](https://kubernetes.io/zh-cn/docs/tasks/tools/) 。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 权衡[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E6%9D%83%E8%A1%A1)

`kubectl` 工具支持三种对象管理：

- 命令式命令
- 命令式对象配置
- 声明式对象配置

参看 [Kubernetes 对象管理](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/object-management/) 中关于每种对象管理的优缺点的讨论。

### 如何创建对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E5%A6%82%E4%BD%95%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1)

你可以使用 `kubectl create -f` 从配置文件创建一个对象。 请参考 [kubernetes API 参考](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/) 有关详细信息。

- `kubectl create -f <filename|url>`

### 如何更新对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E5%A6%82%E4%BD%95%E6%9B%B4%E6%96%B0%E5%AF%B9%E8%B1%A1)

**警告：** 使用 `replace` 命令更新对象会删除所有未在配置文件中指定的规范的某些部分。 不应将其规范由集群部分管理的对象使用，比如类型为 `LoadBalancer` 的服务， 其中 `externalIPs` 字段独立于配置文件进行管理。 必须将独立管理的字段复制到配置文件中，以防止 `replace` 删除它们。

你可以使用 `kubectl replace -f` 根据配置文件更新活动对象。

- `kubectl replace -f <filename|url>`

### 如何删除对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E5%A6%82%E4%BD%95%E5%88%A0%E9%99%A4%E5%AF%B9%E8%B1%A1)

你可以使用 `kubectl delete -f` 删除配置文件中描述的对象。

- `kubectl delete -f <filename|url>`

**说明：**

如果配置文件在 `metadata` 节中设置了 `generateName` 字段而非 `name` 字段， 你无法使用 `kubectl delete -f <filename|url>` 来删除该对象。 你必须使用其他标志才能删除对象。例如：

```shell
kubectl delete <type> <name>
kubectl delete <type> -l <label>
```

### 如何查看对象[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E5%A6%82%E4%BD%95%E6%9F%A5%E7%9C%8B%E5%AF%B9%E8%B1%A1)

你可以使用 `kubectl get -f` 查看有关配置文件中描述的对象的信息。

- `kubectl get -f <filename|url> -o yaml`

`-o yaml` 标志指定打印完整的对象配置。 使用 `kubectl get -h` 查看选项列表。

### 局限性[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E5%B1%80%E9%99%90%E6%80%A7)

当完全定义每个对象的配置并将其记录在其配置文件中时，`create`、 `replace` 和`delete` 命令会很好的工作。 但是，当更新一个活动对象，并且更新没有合并到其配置文件中时，下一次执行 `replace` 时，更新将丢失。 如果控制器,例如 HorizontalPodAutoscaler ,直接对活动对象进行更新，则会发生这种情况。 这有一个例子：

1. 从配置文件创建一个对象。
2. 另一个源通过更改某些字段来更新对象。
3. 从配置文件中替换对象。在步骤2中所做的其他源的更改将丢失。

如果需要支持同一对象的多个编写器，则可以使用 `kubectl apply` 来管理该对象。

### 从 URL 创建和编辑对象而不保存配置[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E4%BB%8E-url-%E5%88%9B%E5%BB%BA%E5%92%8C%E7%BC%96%E8%BE%91%E5%AF%B9%E8%B1%A1%E8%80%8C%E4%B8%8D%E4%BF%9D%E5%AD%98%E9%85%8D%E7%BD%AE)

假设你具有对象配置文件的 URL。 你可以在创建对象之前使用 `kubectl create --edit` 对配置进行更改。 这对于指向可以由读者修改的配置文件的教程和任务特别有用。

```shell
kubectl create -f <url> --edit
```

### 从命令式命令迁移到命令式对象配置[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E4%BB%8E%E5%91%BD%E4%BB%A4%E5%BC%8F%E5%91%BD%E4%BB%A4%E8%BF%81%E7%A7%BB%E5%88%B0%E5%91%BD%E4%BB%A4%E5%BC%8F%E5%AF%B9%E8%B1%A1%E9%85%8D%E7%BD%AE)

从命令式命令迁移到命令式对象配置涉及几个手动步骤。

1. 将活动对象导出到本地对象配置文件：
    
    ```shell
    kubectl get <kind>/<name> -o yaml > <kind>_<name>.yaml
    ```
    

2. 从对象配置文件中手动删除状态字段。

3. 对于后续的对象管理，只能使用 `replace` 。
    
    ```shell
    kubectl replace -f <kind>_<name>.yaml
    ```
    

### 定义控制器选择器和 PodTemplate 标签[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E5%AE%9A%E4%B9%89%E6%8E%A7%E5%88%B6%E5%99%A8%E9%80%89%E6%8B%A9%E5%99%A8%E5%92%8C-podtemplate-%E6%A0%87%E7%AD%BE)

**警告：** 不建议在控制器上更新选择器。

推荐的方法是定义单个不变的 PodTemplate 标签，该标签仅由控制器选择器使用，而没有其他语义。

标签示例：

```yaml
selector:
  matchLabels:
      controller-selector: "apps/v1/deployment/nginx"
template:
  metadata:
    labels:
      controller-selector: "apps/v1/deployment/nginx"
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [使用命令式命令管理 Kubernetes 对象](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/)
- [使用配置文件对 Kubernetes 对象进行声明式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/)
- [Kubectl 命令参考](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/)
- [Kubernetes API 参考](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/)
## [使用 kubectl patch 更新 API 对象](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/)

使用 kubectl patch 更新 Kubernetes API 对象。做一个策略性的合并 patch 或 JSON 合并 patch。
这个任务展示如何使用 `kubectl patch` 就地更新 API 对象。 这个任务中的练习演示了一个策略性合并 patch 和一个 JSON 合并 patch。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 使用策略合并 patch 更新 Deployment[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#use-a-strategic-merge-patch-to-update-a-deployment)

下面是具有两个副本的 Deployment 的配置文件。每个副本是一个 Pod，有一个容器：

[`application/deployment-patch.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/deployment-patch.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/deployment-patch.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: patch-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: patch-demo-ctr
        image: nginx
      tolerations:
      - effect: NoSchedule
        key: dedicated
        value: test-team
```

创建 Deployment：

```shell
kubectl apply -f https://k8s.io/examples/application/deployment-patch.yaml
```

查看与 Deployment 相关的 Pod：

```shell
kubectl get pods
```

输出显示 Deployment 有两个 Pod。`1/1` 表示每个 Pod 有一个容器：

```
NAME                        READY     STATUS    RESTARTS   AGE
patch-demo-28633765-670qr   1/1       Running   0          23s
patch-demo-28633765-j5qs3   1/1       Running   0          23s
```

把运行的 Pod 的名字记下来。稍后，你将看到这些 Pod 被终止并被新的 Pod 替换。

此时，每个 Pod 都有一个运行 nginx 镜像的容器。现在假设你希望每个 Pod 有两个容器：一个运行 nginx，另一个运行 redis。

创建一个名为 `patch-file.yaml` 的文件。内容如下：

```yaml
spec:
  template:
    spec:
      containers:
      - name: patch-demo-ctr-2
        image: redis
```

修补你的 Deployment：

```shell
kubectl patch deployment patch-demo --patch-file patch-file.yaml
```

查看修补后的 Deployment：

```shell
kubectl get deployment patch-demo --output yaml
```

输出显示 Deployment 中的 PodSpec 有两个容器：

```yaml
containers:
- image: redis
  imagePullPolicy: Always
  name: patch-demo-ctr-2
  ...
- image: nginx
  imagePullPolicy: Always
  name: patch-demo-ctr
  ...
```

查看与 patch Deployment 相关的 Pod：

```shell
kubectl get pods
```

输出显示正在运行的 Pod 与以前运行的 Pod 有不同的名称。Deployment 终止了旧的 Pod， 并创建了两个符合更新后的 Deployment 规约的新 Pod。`2/2` 表示每个 Pod 有两个容器:

```
NAME                          READY     STATUS    RESTARTS   AGE
patch-demo-1081991389-2wrn5   2/2       Running   0          1m
patch-demo-1081991389-jmg7b   2/2       Running   0          1m
```

仔细查看其中一个 patch-demo Pod：

```shell
kubectl get pod <your-pod-name> --output yaml
```

输出显示 Pod 有两个容器：一个运行 nginx，一个运行 redis：

```
containers:
- image: redis
  ...
- image: nginx
  ...
```

#### 策略性合并类的 patch 的说明[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#notes-on-the-strategic-merge-patch)

你在前面的练习中所做的 patch 称为 `策略性合并 patch（Strategic Merge Patch）`。 请注意，patch 没有替换 `containers` 列表。相反，它向列表中添加了一个新 Container。换句话说， patch 中的列表与现有列表合并。当你在列表中使用策略性合并 patch 时，并不总是这样。 在某些情况下，列表是替换的，而不是合并的。

对于策略性合并 patch，列表可以根据其 patch 策略进行替换或合并。 patch 策略由 Kubernetes 源代码中字段标记中的 `patchStrategy` 键的值指定。 例如，`PodSpec` 结构体的 `Containers` 字段的 `patchStrategy` 为 `merge`：

```go
type PodSpec struct {
  ...
  Containers []Container `json:"containers" patchStrategy:"merge" patchMergeKey:"name" ...`
  ...
}
```

你还可以在 [OpenApi 规范](https://raw.githubusercontent.com/kubernetes/kubernetes/master/api/openapi-spec/swagger.json)中看到 patch 策略：

```yaml
"io.k8s.api.core.v1.PodSpec": {
    ...,
    "containers": {
        "description": "List of containers belonging to the pod.  ...."
    },
    "x-kubernetes-patch-merge-key": "name",
    "x-kubernetes-patch-strategy": "merge"
}
```

你可以在 [Kubernetes API 文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#podspec-v1-core) 中看到 patch 策略。

创建一个名为 `patch-file-tolerations.yaml` 的文件。内容如下:

```yaml
spec:
  template:
    spec:
      tolerations:
      - effect: NoSchedule
        key: disktype
        value: ssd
```

对 Deployment 执行 patch 操作：

```shell
kubectl patch deployment patch-demo --patch-file patch-file-tolerations.yaml
```

查看修补后的 Deployment：

```shell
kubectl get deployment patch-demo --output yaml
```

输出结果显示 Deployment 中的 PodSpec 只有一个容忍度设置：

```yaml
tolerations:
- effect: NoSchedule
  key: disktype
  value: ssd
```

请注意，PodSpec 中的 `tolerations` 列表被替换，而不是合并。这是因为 PodSpec 的 `tolerations` 的字段标签中没有 `patchStrategy` 键。所以策略合并 patch 操作使用默认的 patch 策略，也就是 `replace`。

```go
type PodSpec struct {
  ...
  Tolerations []Toleration `json:"tolerations,omitempty" protobuf:"bytes,22,opt,name=tolerations"`
  ...
}
```

### 使用 JSON 合并 patch 更新 Deployment[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#use-a-json-merge-patch-to-update-a-deployment)

策略性合并 patch 不同于 [JSON 合并 patch](https://tools.ietf.org/html/rfc7386)。 使用 JSON 合并 patch，如果你想更新列表，你必须指定整个新列表。新的列表完全取代现有的列表。

`kubectl patch` 命令有一个 `type` 参数，你可以将其设置为以下值之一：

| 参数值 | 合并类型 |
| --- | --- |
| json | [JSON Patch, RFC 6902](https://tools.ietf.org/html/rfc6902) |
| merge | [JSON Merge Patch, RFC 7386](https://tools.ietf.org/html/rfc7386) |
| strategic | 策略合并 patch |

有关 JSON patch 和 JSON 合并 patch 的比较，查看 [JSON patch 和 JSON 合并 patch](https://erosb.github.io/post/json-patch-vs-merge-patch/)。

`type` 参数的默认值是 `strategic`。在前面的练习中，我们做了一个策略性的合并 patch。

下一步，在相同的 Deployment 上执行 JSON 合并 patch。创建一个名为 `patch-file-2` 的文件。内容如下：

```yaml
spec:
  template:
    spec:
      containers:
      - name: patch-demo-ctr-3
        image: gcr.io/google-samples/node-hello:1.0
```

在 patch 命令中，将 `type` 设置为 `merge`：

```shell
kubectl patch deployment patch-demo --type merge --patch-file patch-file-2.yaml
```

查看修补后的 Deployment：

```shell
kubectl get deployment patch-demo --output yaml
```

patch 中指定的 `containers` 列表只有一个 Container。 输出显示你所给出的 Contaier 列表替换了现有的 `containers` 列表。

```yaml
spec:
  containers:
  - image: gcr.io/google-samples/node-hello:1.0
    ...
    name: patch-demo-ctr-3
```

列出正运行的 Pod：

```shell
kubectl get pods
```

在输出中，你可以看到已经终止了现有的 Pod，并创建了新的 Pod。`1/1` 表示每个新 Pod 只运行一个容器。

```shell
NAME                          READY     STATUS    RESTARTS   AGE
patch-demo-1307768864-69308   1/1       Running   0          1m
patch-demo-1307768864-c86dc   1/1       Running   0          1m
```

### 使用带 retainKeys 策略的策略合并 patch 更新 Deployment[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#use-strategic-merge-patch-to-update-a-deployment-using-the-retainkeys-strategy)

[`application/deployment-retainkeys.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/deployment-retainkeys.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/deployment-retainkeys.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: retainkeys-demo
spec:
  selector:
    matchLabels:
      app: nginx
  strategy:
    rollingUpdate:
      maxSurge: 30%
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: retainkeys-demo-ctr
        image: nginx
```

创建 Deployment：

```shell
kubectl apply -f https://k8s.io/examples/application/deployment-retainkeys.yaml
```

这时，Deployment 被创建，并使用 `RollingUpdate` 策略。

创建一个名为 `patch-file-no-retainkeys.yaml` 的文件，内容如下：

```yaml
spec:
  strategy:
    type: Recreate
```

修补你的 Deployment:

```shell
kubectl patch deployment retainkeys-demo --type strategic --patch-file patch-file-no-retainkeys.yaml
```

在输出中，你可以看到，当 `spec.strategy.rollingUpdate` 已经拥有取值定义时， 将其 `type` 设置为 `Recreate` 是不可能的。

```
The Deployment "retainkeys-demo" is invalid: spec.strategy.rollingUpdate: Forbidden: may not be specified when strategy `type` is 'Recreate'
```

更新 `type` 取值的同时移除 `spec.strategy.rollingUpdate` 现有值的方法是为策略性合并操作设置 `retainKeys` 策略：

创建另一个名为 `patch-file-retainkeys.yaml` 的文件，内容如下：

```yaml
spec:
  strategy:
    $retainKeys:
    - type
    type: Recreate
```

使用此 patch，我们表达了希望只保留 `strategy` 对象的 `type` 键。 这样，在 patch 操作期间 `rollingUpdate` 会被删除。

使用新的 patch 重新修补 Deployment：

```shell
kubectl patch deployment retainkeys-demo --type strategic --patch-file patch-file-retainkeys.yaml
```

检查 Deployment 的内容：

```shell
kubectl get deployment retainkeys-demo --output yaml
```

输出显示 Deployment 中的 `strategy` 对象不再包含 `rollingUpdate` 键：

```yaml
spec:
  strategy:
    type: Recreate
  template:
```

#### 关于使用 retainKeys 策略的策略合并 patch 操作的说明[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#notes-on-the-strategic-merge-patch-using-the-retainkeys-strategy)

在前文练习中所执行的称作 **带 `retainKeys` 策略的策略合并 patch（Strategic Merge Patch with retainKeys Strategy）**。 这种方法引入了一种新的 `$retainKey` 指令，具有如下策略：

- 其中包含一个字符串列表；
- 所有需要被保留的字段必须在 `$retainKeys` 列表中给出；
- 对于已有的字段，会和对象上对应的内容合并；
- 在修补操作期间，未找到的字段都会被清除；
- 列表 `$retainKeys` 中的所有字段必须 patch 操作所给字段的超集，或者与之完全一致。

策略 `retainKeys` 并不能对所有对象都起作用。它仅对那些 Kubernetes 源码中 `patchStrategy` 字段标志值包含 `retainKeys` 的字段有用。 例如 `DeploymentSpec` 结构的 `Strategy` 字段就包含了 `patchStrategy` 为 `retainKeys` 的标志。

```go
type DeploymentSpec struct {
  ...
  // +patchStrategy=retainKeys
  Strategy DeploymentStrategy `json:"strategy,omitempty" patchStrategy:"retainKeys" ...`
  ...
}
```

你也可以查看 [OpenAPI 规范](https://raw.githubusercontent.com/kubernetes/kubernetes/master/api/openapi-spec/swagger.json)中的 `retainKeys` 策略：

```yaml
"io.k8s.api.apps.v1.DeploymentSpec": {
    ...,
    "strategy": {
        "$ref": "#/definitions/io.k8s.api.apps.v1.DeploymentStrategy",
        "description": "The deployment strategy to use to replace existing pods with new ones.",
        "x-kubernetes-patch-strategy": "retainKeys"
    },
    ....
}
```

而且你也可以在 [Kubernetes API 文档](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#deploymentspec-v1-apps)中看到 `retainKey` 策略。

#### kubectl patch 命令的其他形式[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#alternate-forms-of-the-kubectl-patch-command)

`kubectl patch` 命令使用 YAML 或 JSON。它可以接受以文件形式提供的补丁，也可以接受直接在命令行中给出的补丁。

创建一个文件名称是 `patch-file.json` 内容如下：

```json
{
   "spec": {
      "template": {
         "spec": {
            "containers": [
               {
                  "name": "patch-demo-ctr-2",
                  "image": "redis"
               }
            ]
         }
      }
   }
}
```

以下命令是等价的：

```shell
kubectl patch deployment patch-demo --patch-file patch-file.yaml
kubectl patch deployment patch-demo --patch 'spec:\n template:\n  spec:\n   containers:\n   - name: patch-demo-ctr-2\n     image: redis'

kubectl patch deployment patch-demo --patch-file patch-file.json
kubectl patch deployment patch-demo --patch '{"spec": {"template": {"spec": {"containers": [{"name": "patch-demo-ctr-2","image": "redis"}]}}}}'
```

#### 使用 `kubectl patch` 和 `--subresource` 更新一个对象的副本数[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#scale-kubectl-patch)

**特性状态：** `Kubernetes v1.24 [alpha]`

使用 kubectl 命令（如 get、patch、edit 和 replace）时带上 `--subresource=[subresource-name]` 标志， 可以获取和更新资源的 `status` 和 `scale` 子资源（适用于 kubectl v1.24 或更高版本）。 这个标志可用于带有 `status` 或 `scale` 子资源的所有 API 资源 (内置资源和 CR 资源)。 Deployment 是支持这些子资源的其中一个例子。

下面是有两个副本的 Deployment 的清单。

[`application/deployment.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/deployment.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/deployment.yaml to clipboard")

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # 告知 Deployment 运行 2 个与该模板匹配的 Pod
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

创建 Deployment：

```shell
kubectl apply -f https://k8s.io/examples/application/deployment.yaml
```

查看与 Deployment 关联的 Pod：

```shell
kubectl get pods -l app=nginx
```

在输出中，你可以看到此 Deployment 有两个 Pod。例如：

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7fb96c846b-22567   1/1     Running   0          47s
nginx-deployment-7fb96c846b-mlgns   1/1     Running   0          47s
```

现在用 `--subresource=[subresource-name]` 标志修补此 Deployment：

```shell
kubectl patch deployment nginx-deployment --subresource='scale' --type='merge' -p '{"spec":{"replicas":3}}'
```

输出为：

```shell
scale.autoscaling/nginx-deployment patched
```

查看与你所修补的 Deployment 关联的 Pod：

```shell
kubectl get pods -l app=nginx
```

在输出中，你可以看到一个新的 Pod 被创建，因此现在你有 3 个正在运行的 Pod。

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7fb96c846b-22567   1/1     Running   0          107s
nginx-deployment-7fb96c846b-lxfr2   1/1     Running   0          14s
nginx-deployment-7fb96c846b-mlgns   1/1     Running   0          107s
```

查看所修补的 Deployment：

```shell
kubectl get deployment nginx-deployment -o yaml
```

```yaml
...
spec:
  replicas: 3
  ...
status:
  ...
  availableReplicas: 3
  readyReplicas: 3
  replicas: 3
```

**说明：**

如果你运行 `kubectl patch` 并指定 `--subresource` 标志时，所针对的是不支持特定子资源的资源， 则 API 服务器会返回一个 404 Not Found 错误。

### 总结[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#summary)

在本练习中，你使用 `kubectl patch` 更改了 Deployment 对象的当前配置。 你没有更改最初用于创建 Deployment 对象的配置文件。 用于更新 API 对象的其他命令包括 [`kubectl annotate`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#annotate)、 [`kubectl edit`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#edit)、 [`kubectl replace`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#replace)、 [`kubectl scale`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#scale) 和 [`kubectl apply`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#apply)。

**说明：**

定制资源不支持策略性合并 patch。

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/update-api-object-kubectl-patch/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [Kubernetes 对象管理](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/object-management/)
- [使用指令式命令管理 Kubernetes 对象](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-command/)
- [使用配置文件对 Kubernetes 对象进行命令式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/imperative-config/)
- [使用配置文件对 Kubernetes 对象进行声明式管理](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/declarative-config/)


# 管理 Secrets

使用 Secrets 管理机密配置数据。

---

## [使用 kubectl 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/)

使用 kubectl 命令行创建 Secret 对象。
本页向你展示如何使用 `kubectl` 命令行工具来创建、编辑、管理和删除。 Kubernetes [Secrets](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 创建 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#create-a-secret)

`Secret` 对象用来存储敏感数据，如 Pod 用于访问服务的凭据。例如，为访问数据库，你可能需要一个 Secret 来存储所需的用户名及密码。

你可以通过在命令中传递原始数据，或将凭据存储文件中，然后再在命令行中创建 Secret。以下命令 将创建一个存储用户名 `admin` 和密码 `S!B\*d$zDsb=` 的 Secret。

#### 使用原始数据[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#%E4%BD%BF%E7%94%A8%E5%8E%9F%E5%A7%8B%E6%95%B0%E6%8D%AE)

执行以下命令：

```shell
kubectl create secret generic db-user-pass \
    --from-literal=username=admin \
    --from-literal=password='S!B\*d$zDsb='
```

你必须使用单引号 `''` 转义字符串中的特殊字符，如 `$`、`\`、`*`、`=`和`!` 。否则，你的 shell 将会解析这些字符。

#### 使用源文件[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#%E4%BD%BF%E7%94%A8%E6%BA%90%E6%96%87%E4%BB%B6)

1. 将凭据保存到文件：
    
    ```shell
    echo -n 'admin' > ./username.txt
    echo -n 'S!B\*d$zDsb=' > ./password.txt
    ```
    
    `-n` 标志用来确保生成文件的文末没有多余的换行符。这很重要，因为当 `kubectl` 读取文件并将内容编码为 base64 字符串时，额外的换行符也会被编码。 你不需要对文件中包含的字符串中的特殊字符进行转义。
    

2. 在 `kubectl` 命令中传递文件路径：
    
    ```shell
    kubectl create secret generic db-user-pass \
        --from-file=./username.txt \
        --from-file=./password.txt
    ```
    
    默认键名为文件名。你也可以通过 `--from-file=[key=]source` 设置键名，例如：
    
    ```shell
    kubectl create secret generic db-user-pass \
        --from-file=username=./username.txt \
        --from-file=password=./password.txt
    ```
    

无论使用哪种方法，输出都类似于：

```
secret/db-user-pass created
```

### 验证 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#verify-the-secret)

检查 Secret 是否已创建：

```shell
kubectl get secrets
```

输出类似于：

```
NAME              TYPE       DATA      AGE
db-user-pass      Opaque     2         51s
```

查看 Secret 的细节：

```shell
kubectl describe secret db-user-pass
```

输出类似于：

```
Name:            db-user-pass
Namespace:       default
Labels:          <none>
Annotations:     <none>

Type:            Opaque

Data
====
password:    12 bytes
username:    5 bytes
```

`kubectl get` 和 `kubectl describe` 命令默认不显示 `Secret` 的内容。 这是为了防止 `Secret` 被意外暴露或存储在终端日志中。

#### 解码 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#decoding-secret)

1. 查看你所创建的 Secret 内容
    
    ```shell
    kubectl get secret db-user-pass -o jsonpath='{.data}'
    ```
    
    输出类似于：
    
    ```json
    { "password": "UyFCXCpkJHpEc2I9", "username": "YWRtaW4=" }
    ```
    

2. 解码 `password` 数据:
    
    ```shell
    echo 'UyFCXCpkJHpEc2I9' | base64 --decode
    ```
    
    输出类似于：
    
    ```
    S!B\*d$zDsb=
    ```
    
    **注意：**
    
    这是一个出于文档编制目的的示例。实际上，该方法可能会导致包含编码数据的命令存储在 Shell 的历史记录中。任何可以访问你的计算机的人都可以找到该命令并对 Secret 进行解码。 更好的办法是将查看和解码命令一同使用。
    
    ```shell
    kubectl get secret db-user-pass -o jsonpath='{.data.password}' | base64 --decode
    ```
    

### 编辑 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#edit-secret)

你可以编辑一个现存的 `Secret` 对象，除非它是[不可改变的](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/#secret-immutable)。 要想编辑一个 Secret，请执行以下命令：

```shell
kubectl edit secrets <secret-name>
```

这将打开默认编辑器，并允许你更新 `data` 字段中的 base64 编码的 Secret 值，示例如下：

```yaml
#请编辑下面的对象。以“#”开头的行将被忽略，
#空文件将中止编辑。如果在保存此文件时发生错误，
#则将重新打开该文件并显示相关的失败。
apiVersion: v1
data:
  password: UyFCXCpkJHpEc2I9
  username: YWRtaW4=
kind: Secret
metadata:
  creationTimestamp: "2022-06-28T17:44:13Z"
  name: db-user-pass
  namespace: default
  resourceVersion: "12708504"
  uid: 91becd59-78fa-4c85-823f-6d44436242ac
type: Opaque
```

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#clean-up)

要想删除一个 Secret，请执行以下命令：

```shell
kubectl delete secret db-user-pass
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 进一步阅读 [Secret 概念](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)
- 了解如何[使用配置文件管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/)
- 了解如何[使用 Kustomize 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/)
## [使用配置文件管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/)

使用资源配置文件创建 Secret 对象。
### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 创建 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/#create-the-config-file)

你可以先用 JSON 或 YAML 格式在一个清单文件中定义 `Secret` 对象，然后创建该对象。 [Secret](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#secret-v1-core) 资源包含 2 个键值对：`data` 和 `stringData`。 `data` 字段用来存储 base64 编码的任意数据。 提供 `stringData` 字段是为了方便，它允许 Secret 使用未编码的字符串。 `data` 和 `stringData` 的键必须由字母、数字、`-`、`_` 或 `.` 组成。

以下示例使用 `data` 字段在 Secret 中存储两个字符串：

1. 将这些字符串转换为 base64：
    
    ```shell
    echo -n 'admin' | base64
    echo -n '1f2d1e2e67df' | base64
    ```
    
    **说明：**
    
    Secret 数据的 JSON 和 YAML 序列化结果是以 base64 编码的。 换行符在这些字符串中无效，必须省略。 在 Darwin/macOS 上使用 `base64` 工具时，用户不应该使用 `-b` 选项分割长行。 相反地，Linux 用户**应该**在 `base64` 地命令中添加 `-w 0` 选项， 或者在 `-w` 选项不可用的情况下，输入 `base64 | tr -d '\n'`。
    
    输出类似于：
    
    ```
    YWRtaW4=
    MWYyZDFlMmU2N2Rm
    ```
    

2. 创建清单：
    
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    type: Opaque
    data:
      username: YWRtaW4=
      password: MWYyZDFlMmU2N2Rm
    ```
    
    注意，Secret 对象的名称必须是有效的 [DNS 子域名](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#dns-subdomain-names)。
    

3. 使用 [`kubectl apply`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 创建 Secret：
    
    ```shell
    kubectl apply -f ./secret.yaml
    ```
    
    输出类似于：
    
    ```
    secret/mysecret created
    ```
    

若要验证 Secret 被创建以及想要解码 Secret 数据， 请参阅[使用 kubectl 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#verify-the-secret)

#### 创建 Secret 时提供未编码的数据[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/#specify-unencoded-data-when-creating-a-secret)

对于某些场景，你可能希望使用 `stringData` 字段。 这个字段可以将一个非 base64 编码的字符串直接放入 Secret 中， 当创建或更新该 Secret 时，此字段将被编码。

上述用例的实际场景可能是这样：当你部署应用时，使用 Secret 存储配置文件， 你希望在部署过程中，填入部分内容到该配置文件。

例如，如果你的应用程序使用以下配置文件:

```yaml
apiUrl: "https://my.api.com/api/v1"
username: "<user>"
password: "<password>"
```

你可以使用以下定义将其存储在 Secret 中:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
stringData:
  config.yaml: |
    apiUrl: "https://my.api.com/api/v1"
    username: <user>
    password: <password>    
```

当你检索 Secret 数据时，此命令将返回编码的值，并不是你在 `stringData` 中提供的纯文本值。

例如，如果你运行以下命令：

```shell
kubectl get secret mysecret -o yaml
```

输出类似于：

```yaml
apiVersion: v1
data:
  config.yaml: YXBpVXJsOiAiaHR0cHM6Ly9teS5hcGkuY29tL2FwaS92MSIKdXNlcm5hbWU6IHt7dXNlcm5hbWV9fQpwYXNzd29yZDoge3twYXNzd29yZH19
kind: Secret
metadata:
  creationTimestamp: 2018-11-15T20:40:59Z
  name: mysecret
  namespace: default
  resourceVersion: "7225"
  uid: c280ad2e-e916-11e8-98f2-025000000001
type:
```

#### 同时指定 `data` 和 `stringData`[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/#specifying-both-data-and-stringdata)

如果你在 `data` 和 `stringData` 中设置了同一个字段，则使用来自 `stringData` 中的值。

例如，如果你定义以下 Secret：

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
stringData:
  username: administrator
```

所创建的 `Secret` 对象如下：

```yaml
apiVersion: v1
data:
  username: YWRtaW5pc3RyYXRvcg==
kind: Secret
metadata:
  creationTimestamp: 2018-11-15T20:46:46Z
  name: mysecret
  namespace: default
  resourceVersion: "7579"
  uid: 91460ecb-e917-11e8-98f2-025000000001
type: Opaque
```

`YWRtaW5pc3RyYXRvcg==` 解码成 `administrator`。

### 编辑 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/#edit-secret)

要编辑使用清单创建的 Secret 中的数据，请修改清单中的 `data` 或 `stringData` 字段并将此清单文件应用到集群。 你可以编辑现有的 `Secret` 对象，除非它是[不可变的](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/#secret-immutable)。

例如，如果你想将上一个示例中的密码更改为 `birdsarentreal`，请执行以下操作：

1. 编码新密码字符串：
    
    ```shell
    echo -n 'birdsarentreal' | base64
    ```
    
    输出类似于：
    
    ```
    YmlyZHNhcmVudHJlYWw=
    ```
    

2. 使用你的新密码字符串更新 `data` 字段：
    
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    type: Opaque
    data:
      username: YWRtaW4=
      password: YmlyZHNhcmVudHJlYWw=
    ```
    

3. 将清单应用到你的集群：
    
    ```shell
    kubectl apply -f ./secret.yaml
    ```
    
    输出类似于：
    
    ```
    secret/mysecret configured
    ```
    

Kubernetes 更新现有的 `Secret` 对象。具体而言，`kubectl` 工具发现存在一个同名的现有 `Secret` 对象。 `kubectl` 获取现有对象，计划对其进行更改，并将更改后的 `Secret` 对象提交到你的集群控制平面。

如果你指定了 `kubectl apply --server-side`，则 `kubectl` 使用[服务器端应用（Server-Side Apply）](https://kubernetes.io/zh-cn/docs/reference/using-api/server-side-apply/)。

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/#clean-up)

删除你创建的 Secret：

```shell
kubectl delete secret mysecret
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 进一步阅读 [Secret 概念](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)
- 了解如何[使用 `kubectl` 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/)
- 了解如何[使用 kustomize 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/)
## [使用 Kustomize 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/)

使用 kustomization.yaml 文件创建 Secret 对象。
`kubectl` 支持使用 [Kustomize 对象管理工具](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/)来管理 Secret 和 ConfigMap。你可以使用 Kustomize 创建**资源生成器（Resource Generator）**， 该生成器会生成一个 Secret，让你能够通过 `kubectl` 应用到 API 服务器。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 创建 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#create-a-secret)

你可以在 `kustomization.yaml` 文件中定义 `secreteGenerator` 字段， 并在定义中引用其它本地文件、`.env` 文件或文字值生成 Secret。 例如：下面的指令为用户名 `admin` 和密码 `1f2d1e2e67df` 创建 Kustomization 文件。

#### 创建 Kustomization 文件[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#create-the-kustomization-file)

- [文字](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#secret-data-0)
- [文件](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#secret-data-1)
- [.env 文件](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#secret-data-2)

```yaml

secretGenerator:
- name: database-creds
  literals:
  - username=admin
  - password=1f2d1e2e67df
```

1. 用 base64 编码的值存储凭据到文件中：
    
    ```shell
    echo -n 'admin' > ./username.txt
    echo -n '1f2d1e2e67df' > ./password.txt
    ```
    
    `-n` 标志确保文件结尾处没有换行符。
    

2. 创建 `kustomization.yaml` 文件：
    
    ```yaml
    secretGenerator:
    - name: database-creds
      files:
      - username.txt
      - password.txt
    ```
    

你也可以使用 `.env` 文件在 `kustomization.yaml` 中定义 `secretGenerator`。 例如下面的 `kustomization.yaml` 文件从 `.env.secret` 文件获取数据：

```yaml
secretGenerator:
- name: db-user-pass
  envs:
  - .env.secret
```

在所有情况下，你都不需要对取值作 base64 编码。 YAML 文件的名称**必须**是 `kustomization.yaml` 或 `kustomization.yml`。

#### 应用 kustomization 文件[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#apply-the-kustomization-file)

若要创建 Secret，应用包含 kustomization 文件的目录。

```shell
kubectl apply -k <目录路径>
```

输出类似于：

```
secret/database-creds-5hdh7hhgfk created
```

生成 Secret 时，Secret 的名称最终是由 `name` 字段和数据的哈希值拼接而成。 这将保证每次修改数据时生成一个新的 Secret。

要验证 Secret 是否已创建并解码 Secret 数据， 请参阅[使用 kubectl 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/#verify-the-secret)。

### 编辑 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#edit-secret)

1. 在 `kustomization.yaml` 文件中，修改诸如 `password` 等数据。
    
2. 应用包含 kustomization 文件的目录：
    
    ```shell
    kubectl apply -k <目录路径>
    ```
    
    输出类似于：
    
    ```
    secret/db-user-pass-6f24b56cc8 created
    ```
    

编辑过的 Secret 被创建为一个新的 `Secret` 对象，而不是更新现有的 `Secret` 对象。 你可能需要在 Pod 中更新对该 Secret 的引用。

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#clean-up)

要删除 Secret，请使用 `kubectl`：

```shell
kubectl delete secret db-user-pass
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kustomize/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 进一步阅读 [Secret 概念](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)
- 了解如何[使用 kubectl 管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-kubectl/)
- 了解如何[使用配置文件管理 Secret](https://kubernetes.io/zh-cn/docs/tasks/configmap-secret/managing-secret-using-config-file/)