
# 配置 Pods 和容器

对 Pod 和容器执行常见的配置任务。

---

## [为容器和 Pod 分配内存资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/)
此页面展示如何将内存**请求**（request）和内存**限制**（limit）分配给一个容器。 我们保障容器拥有它请求数量的内存，但不允许使用超过限制数量的内存。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

你集群中的每个节点必须拥有至少 300 MiB 的内存。

该页面上的一些步骤要求你在集群中运行 [metrics-server](https://github.com/kubernetes-sigs/metrics-server) 服务。 如果你已经有在运行中的 metrics-server，则可以跳过这些步骤。

如果你运行的是 Minikube，可以运行下面的命令启用 metrics-server：

```shell
minikube addons enable metrics-server
```

要查看 metrics-server 或资源指标 API (`metrics.k8s.io`) 是否已经运行，请运行以下命令：

```shell
kubectl get apiservices
```

如果资源指标 API 可用，则输出结果将包含对 `metrics.k8s.io` 的引用信息。

```
NAME
v1beta1.metrics.k8s.io
```

### 创建命名空间[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#create-a-namespace)

创建一个命名空间，以便将本练习中创建的资源与集群的其余部分隔离。

```shell
kubectl create namespace mem-example
```

### 指定内存请求和限制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#specify-a-memory-request-and-a-memory-limit)

要为容器指定内存请求，请在容器资源清单中包含 `resources：requests` 字段。 同理，要指定内存限制，请包含 `resources：limits`。

在本练习中，你将创建一个拥有一个容器的 Pod。 容器将会请求 100 MiB 内存，并且内存会被限制在 200 MiB 以内。 这是 Pod 的配置文件：

[`pods/resource/memory-request-limit.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/resource/memory-request-limit.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/resource/memory-request-limit.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "100Mi"
      limits:
        memory: "200Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
```

配置文件的 `args` 部分提供了容器启动时的参数。 `"--vm-bytes", "150M"` 参数告知容器尝试分配 150 MiB 内存。

开始创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/memory-request-limit.yaml --namespace=mem-example
```

验证 Pod 中的容器是否已运行：

```shell
kubectl get pod memory-demo --namespace=mem-example
```

查看 Pod 相关的详细信息：

```shell
kubectl get pod memory-demo --output=yaml --namespace=mem-example
```

输出结果显示：该 Pod 中容器的内存请求为 100 MiB，内存限制为 200 MiB。

```yaml
...
resources:
  requests:
    memory: 100Mi
  limits:
    memory: 200Mi
...
```

运行 `kubectl top` 命令，获取该 Pod 的指标数据：

```shell
kubectl top pod memory-demo --namespace=mem-example
```

输出结果显示：Pod 正在使用的内存大约为 162,900,000 字节，约为 150 MiB。 这大于 Pod 请求的 100 MiB，但在 Pod 限制的 200 MiB之内。

```
NAME                        CPU(cores)   MEMORY(bytes)
memory-demo                 <something>  162856960
```

删除 Pod：

```shell
kubectl delete pod memory-demo --namespace=mem-example
```

### 超过容器限制的内存[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#exceed-a-container-s-memory-limit)

当节点拥有足够的可用内存时，容器可以使用其请求的内存。 但是，容器不允许使用超过其限制的内存。 如果容器分配的内存超过其限制，该容器会成为被终止的候选容器。 如果容器继续消耗超出其限制的内存，则终止容器。 如果终止的容器可以被重启，则 kubelet 会重新启动它，就像其他任何类型的运行时失败一样。

在本练习中，你将创建一个 Pod，尝试分配超出其限制的内存。 这是一个 Pod 的配置文件，其拥有一个容器，该容器的内存请求为 50 MiB，内存限制为 100 MiB：

[`pods/resource/memory-request-limit-2.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/resource/memory-request-limit-2.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/resource/memory-request-limit-2.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-2
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-2-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "50Mi"
      limits:
        memory: "100Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
```

在配置文件的 `args` 部分中，你可以看到容器会尝试分配 250 MiB 内存，这远高于 100 MiB 的限制。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/memory-request-limit-2.yaml --namespace=mem-example
```

查看 Pod 相关的详细信息：

```shell
kubectl get pod memory-demo-2 --namespace=mem-example
```

此时，容器可能正在运行或被杀死。重复前面的命令，直到容器被杀掉：

```shell
NAME            READY     STATUS      RESTARTS   AGE
memory-demo-2   0/1       OOMKilled   1          24s
```

获取容器更详细的状态信息：

```shell
kubectl get pod memory-demo-2 --output=yaml --namespace=mem-example
```

输出结果显示：由于内存溢出（OOM），容器已被杀掉：

```yaml
lastState:
   terminated:
     containerID: 65183c1877aaec2e8427bc95609cc52677a454b56fcb24340dbd22917c23b10f
     exitCode: 137
     finishedAt: 2017-06-20T20:52:19Z
     reason: OOMKilled
     startedAt: null
```

本练习中的容器可以被重启，所以 kubelet 会重启它。 多次运行下面的命令，可以看到容器在反复的被杀死和重启：

```shell
kubectl get pod memory-demo-2 --namespace=mem-example
```

输出结果显示：容器被杀掉、重启、再杀掉、再重启……：

```
kubectl get pod memory-demo-2 --namespace=mem-example
NAME            READY     STATUS      RESTARTS   AGE
memory-demo-2   0/1       OOMKilled   1          37s
```

```

kubectl get pod memory-demo-2 --namespace=mem-example
NAME            READY     STATUS    RESTARTS   AGE
memory-demo-2   1/1       Running   2          40s
```

查看关于该 Pod 历史的详细信息：

```
kubectl describe pod memory-demo-2 --namespace=mem-example
```

输出结果显示：该容器反复的在启动和失败：

```
... Normal  Created   Created container with id 66a3a20aa7980e61be4922780bf9d24d1a1d8b7395c09861225b0eba1b1f8511
... Warning BackOff   Back-off restarting failed container
```

查看关于集群节点的详细信息：

```
kubectl describe nodes
```

输出结果包含了一条练习中的容器由于内存溢出而被杀掉的记录：

```
Warning OOMKilling Memory cgroup out of memory: Kill process 4481 (stress) score 1994 or sacrifice child
```

删除 Pod:

```shell
kubectl delete pod memory-demo-2 --namespace=mem-example
```

### 超过整个节点容量的内存[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#specify-a-memory-request-that-is-too-big-for-your-nodes)

内存请求和限制是与容器关联的，但将 Pod 视为具有内存请求和限制，也是很有用的。 Pod 的内存请求是 Pod 中所有容器的内存请求之和。 同理，Pod 的内存限制是 Pod 中所有容器的内存限制之和。

Pod 的调度基于请求。只有当节点拥有足够满足 Pod 内存请求的内存时，才会将 Pod 调度至节点上运行。

在本练习中，你将创建一个 Pod，其内存请求超过了你集群中的任意一个节点所拥有的内存。 这是该 Pod 的配置文件，其拥有一个请求 1000 GiB 内存的容器，这应该超过了你集群中任何节点的容量。

[`pods/resource/memory-request-limit-3.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/resource/memory-request-limit-3.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/resource/memory-request-limit-3.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-3
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-3-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "1000Gi"
      limits:
        memory: "1000Gi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/memory-request-limit-3.yaml --namespace=mem-example
```

查看 Pod 状态：

```shell
kubectl get pod memory-demo-3 --namespace=mem-example
```

输出结果显示：Pod 处于 PENDING 状态。 这意味着，该 Pod 没有被调度至任何节点上运行，并且它会无限期的保持该状态：

```
kubectl get pod memory-demo-3 --namespace=mem-example
NAME            READY     STATUS    RESTARTS   AGE
memory-demo-3   0/1       Pending   0          25s
```

查看关于 Pod 的详细信息，包括事件：

```shell
kubectl describe pod memory-demo-3 --namespace=mem-example
```

输出结果显示：由于节点内存不足，该容器无法被调度：

```
Events:
  ...  Reason            Message
       ------            -------
  ...  FailedScheduling  No nodes are available that match all of the following predicates:: Insufficient memory (3).
```

### 内存单位[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#memory-units)

内存资源的基本单位是字节（byte）。你可以使用这些后缀之一，将内存表示为 纯整数或定点整数：E、P、T、G、M、K、Ei、Pi、Ti、Gi、Mi、Ki。 例如，下面是一些近似相同的值：

```
128974848, 129e6, 129M, 123Mi
```

删除 Pod：

```shell
kubectl delete pod memory-demo-3 --namespace=mem-example
```

### 如果你没有指定内存限制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#if-you-do-not-specify-a-memory-limit)

如果你没有为一个容器指定内存限制，则自动遵循以下情况之一：

- 容器可无限制地使用内存。容器可以使用其所在节点所有的可用内存， 进而可能导致该节点调用 OOM Killer。 此外，如果发生 OOM Kill，没有资源限制的容器将被杀掉的可行性更大。
    
- 运行的容器所在命名空间有默认的内存限制，那么该容器会被自动分配默认限制。 集群管理员可用使用 [LimitRange](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#limitrange-v1-core) 来指定默认的内存限制。
    

### 内存请求和限制的目的[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#motivation-for-memory-requests-and-limits)

通过为集群中运行的容器配置内存请求和限制，你可以有效利用集群节点上可用的内存资源。 通过将 Pod 的内存请求保持在较低水平，你可以更好地安排 Pod 调度。 通过让内存限制大于内存请求，你可以完成两件事：

- Pod 可以进行一些突发活动，从而更好的利用可用内存。
- Pod 在突发活动期间，可使用的内存被限制为合理的数量。

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#clean-up)

删除命名空间。下面的命令会删除你根据这个任务创建的所有 Pod：

```shell
kubectl delete namespace mem-example
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

#### 应用开发者扩展阅读[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#for-app-developers)

- [为容器和 Pod 分配 CPU 资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/)
    
- [配置 Pod 的服务质量](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/)
    

#### 集群管理员扩展阅读[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/#for-cluster-administrators)

- [为命名空间配置默认的内存请求和限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
- [为命名空间配置默认的 CPU 请求和限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)
- [配置命名空间的最小和最大内存约束](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/memory-constraint-namespace/)
- [配置命名空间的最小和最大 CPU 约束](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/)
- [为命名空间配置内存和 CPU 配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)
- [配置命名空间下 Pod 总数](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/quota-pod-namespace/)
- [配置 API 对象配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/quota-api-object/)

## [为容器和 Pods 分配 CPU 资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/)
本页面展示如何为容器设置 CPU **request（请求）** 和 CPU **limit（限制）**。 容器使用的 CPU 不能超过所配置的限制。 如果系统有空闲的 CPU 时间，则可以保证给容器分配其所请求数量的 CPU 资源。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

你的集群必须至少有 1 个 CPU 可用才能运行本任务中的示例。

本页的一些步骤要求你在集群中运行 [metrics-server](https://github.com/kubernetes-sigs/metrics-server) 服务。如果你的集群中已经有正在运行的 metrics-server 服务，可以跳过这些步骤。

如果你正在运行 [Minikube](https://kubernetes.io/docs/getting-started-guides/minikube/)，请运行以下命令启用 metrics-server：

```shell
minikube addons enable metrics-server
```

查看 metrics-server（或者其他资源指标 API `metrics.k8s.io` 服务提供者）是否正在运行， 请键入以下命令：

```shell
kubectl get apiservices
```

如果资源指标 API 可用，则会输出将包含一个对 `metrics.k8s.io` 的引用。

```
NAME
v1beta1.metrics.k8s.io
```

### 创建一个名字空间[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#create-a-namespace)

创建一个[名字空间](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/namespaces/)，以便将 本练习中创建的资源与集群的其余部分资源隔离。

```shell
kubectl create namespace cpu-example
```

### 指定 CPU 请求和 CPU 限制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#specify-a-CPU-request-and-a-CPU-limit)

要为容器指定 CPU 请求，请在容器资源清单中包含 `resources: requests` 字段。 要指定 CPU 限制，请包含 `resources:limits`。

在本练习中，你将创建一个具有一个容器的 Pod。容器将会请求 0.5 个 CPU，而且最多限制使用 1 个 CPU。 这是 Pod 的配置文件：

[`pods/resource/cpu-request-limit.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/resource/cpu-request-limit.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/resource/cpu-request-limit.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cpu-demo
  namespace: cpu-example
spec:
  containers:
  - name: cpu-demo-ctr
    image: vish/stress
    resources:
      limits:
        cpu: "1"
      requests:
        cpu: "0.5"
    args:
    - -cpus
    - "2"
```

配置文件的 `args` 部分提供了容器启动时的参数。 `-cpus "2"` 参数告诉容器尝试使用 2 个 CPU。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/cpu-request-limit.yaml --namespace=cpu-example
```

验证所创建的 Pod 处于 Running 状态

```shell
kubectl get pod cpu-demo --namespace=cpu-example
```

查看显示关于 Pod 的详细信息：

```shell
kubectl get pod cpu-demo --output=yaml --namespace=cpu-example
```

输出显示 Pod 中的一个容器的 CPU 请求为 500 milliCPU，并且 CPU 限制为 1 个 CPU。

```yaml
resources:
  limits:
    cpu: "1"
  requests:
    cpu: 500m
```

使用 `kubectl top` 命令来获取该 Pod 的指标：

```shell
kubectl top pod cpu-demo --namespace=cpu-example
```

此示例输出显示 Pod 使用的是 974 milliCPU，即略低于 Pod 配置中指定的 1 个 CPU 的限制。

```
NAME                        CPU(cores)   MEMORY(bytes)
cpu-demo                    974m         <something>
```

回想一下，通过设置 `-cpu "2"`，你将容器配置为尝试使用 2 个 CPU， 但是容器只被允许使用大约 1 个 CPU。 容器的 CPU 用量受到限制，因为该容器正尝试使用超出其限制的 CPU 资源。

**说明：**

CPU 使用率低于 1.0 的另一种可能的解释是，节点可能没有足够的 CPU 资源可用。 回想一下，此练习的先决条件需要你的集群至少具有 1 个 CPU 可用。 如果你的容器在只有 1 个 CPU 的节点上运行，则容器无论为容器指定的 CPU 限制如何， 都不能使用超过 1 个 CPU。

### CPU 单位[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#cpu-units)

CPU 资源以 **CPU** 单位度量。Kubernetes 中的一个 CPU 等同于：

- 1 个 AWS vCPU
- 1 个 GCP核心
- 1 个 Azure vCore
- 裸机上具有超线程能力的英特尔处理器上的 1 个超线程

小数值是可以使用的。一个请求 0.5 CPU 的容器保证会获得请求 1 个 CPU 的容器的 CPU 的一半。 你可以使用后缀 `m` 表示毫。例如 `100m` CPU、100 milliCPU 和 0.1 CPU 都相同。 精度不能超过 1m。

CPU 请求只能使用绝对数量，而不是相对数量。0.1 在单核、双核或 48 核计算机上的 CPU 数量值是一样的。

删除 Pod：

```shell
kubectl delete pod cpu-demo --namespace=cpu-example
```

### 设置超过节点能力的 CPU 请求[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#specify-a-CPU-request-that-is-too-big-for-your-nodes)

CPU 请求和限制与都与容器相关，但是我们可以考虑一下 Pod 具有对应的 CPU 请求和限制这样的场景。 Pod 对 CPU 用量的请求等于 Pod 中所有容器的请求数量之和。 同样，Pod 的 CPU 资源限制等于 Pod 中所有容器 CPU 资源限制数之和。

Pod 调度是基于资源请求值来进行的。 仅在某节点具有足够的 CPU 资源来满足 Pod CPU 请求时，Pod 将会在对应节点上运行：

在本练习中，你将创建一个 Pod，该 Pod 的 CPU 请求对于集群中任何节点的容量而言都会过大。 下面是 Pod 的配置文件，其中有一个容器。容器请求 100 个 CPU，这可能会超出集群中任何节点的容量。

[`pods/resource/cpu-request-limit-2.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/resource/cpu-request-limit-2.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/resource/cpu-request-limit-2.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cpu-demo-2
  namespace: cpu-example
spec:
  containers:
  - name: cpu-demo-ctr-2
    image: vish/stress
    resources:
      limits:
        cpu: "100"
      requests:
        cpu: "100"
    args:
    - -cpus
    - "2"
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/cpu-request-limit-2.yaml --namespace=cpu-example
```

查看该 Pod 的状态：

```shell
kubectl get pod cpu-demo-2 --namespace=cpu-example
```

输出显示 Pod 状态为 Pending。也就是说，Pod 未被调度到任何节点上运行， 并且 Pod 将无限期地处于 Pending 状态：

```
NAME         READY     STATUS    RESTARTS   AGE
cpu-demo-2   0/1       Pending   0          7m
```

查看有关 Pod 的详细信息，包含事件：

```shell
kubectl describe pod cpu-demo-2 --namespace=cpu-example
```

输出显示由于节点上的 CPU 资源不足，无法调度容器：

```
Events:
  Reason                        Message
  ------                        -------
  FailedScheduling      No nodes are available that match all of the following predicates:: Insufficient cpu (3).
```

删除你的 Pod：

```shell
kubectl delete pod cpu-demo-2 --namespace=cpu-example
```

### 如果不指定 CPU 限制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#if-you-do-not-specify-a-cpu-limit)

如果你没有为容器指定 CPU 限制，则会发生以下情况之一：

- 容器在可以使用的 CPU 资源上没有上限。因而可以使用所在节点上所有的可用 CPU 资源。
    
- 容器在具有默认 CPU 限制的名字空间中运行，系统会自动为容器设置默认限制。 集群管理员可以使用 [LimitRange](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#limitrange-v1-core/) 指定 CPU 限制的默认值。
    

### 如果你设置了 CPU 限制但未设置 CPU 请求[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#if-you-specify-a-CPU-limit-but-do-not-specify-a-CPU-request)

如果你为容器指定了 CPU 限制值但未为其设置 CPU 请求，Kubernetes 会自动为其 设置与 CPU 限制相同的 CPU 请求值。类似的，如果容器设置了内存限制值但未设置 内存请求值，Kubernetes 也会为其设置与内存限制值相同的内存请求。

### CPU 请求和限制的初衷[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#motivation-for-CPU-requests-and-limits)

通过配置你的集群中运行的容器的 CPU 请求和限制，你可以有效利用集群上可用的 CPU 资源。 通过将 Pod CPU 请求保持在较低水平，可以使 Pod 更有机会被调度。 通过使 CPU 限制大于 CPU 请求，你可以完成两件事：

- Pod 可能会有突发性的活动，它可以利用碰巧可用的 CPU 资源。
    
- Pod 在突发负载期间可以使用的 CPU 资源数量仍被限制为合理的数量。
    

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#clean-up)

删除名字空间：

```shell
kubectl delete namespace cpu-example
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

#### 针对应用开发者[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#for-app-developers)

- [将内存资源分配给容器和 Pod](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/)
    
- [配置 Pod 服务质量](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/)
    

#### 针对集群管理员 {for-cluster-administrators}[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/#%E9%92%88%E5%AF%B9%E9%9B%86%E7%BE%A4%E7%AE%A1%E7%90%86%E5%91%98-for-cluster-administrators)

- [配置名字空间的默认内存请求和限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
- [为名字空间配置默认 CPU 请求和限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)
- [为名字空间配置最小和最大内存限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/memory-constraint-namespace/)
- [为名字空间配置最小和最大 CPU 约束](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/)
- [为名字空间配置内存和 CPU 配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)
- [为名字空间配置 Pod 配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/quota-pod-namespace/)
- [配置 API 对象的配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/quota-api-object/)

## [为 Windows Pod 和容器配置 GMSA](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/)

**特性状态：** `Kubernetes v1.18 [stable]`

本页展示如何为将运行在 Windows 节点上的 Pod 和容器配置 [组管理的服务账号（Group Managed Service Accounts，GMSA）](https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。 组管理的服务账号是活动目录（Active Directory）的一种特殊类型，提供自动化的 密码管理、简化的服务主体名称（Service Principal Name，SPN）管理以及跨多个 服务器将管理操作委派给其他管理员等能力。

在 Kubernetes 环境中，GMSA 凭据规约配置为 Kubernetes 集群范围的自定义资源 （Custom Resources）形式。Windows Pod 以及各 Pod 中的每个容器可以配置为 使用 GMSA 来完成基于域（Domain）的操作（例如，Kerberos 身份认证），以便 与其他 Windows 服务相交互。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你需要一个 Kubernetes 集群，以及 `kubectl` 命令行工具，且工具必须已配置 为能够与你的集群通信。集群预期包含 Windows 工作节点。 本节讨论需要为每个集群执行一次的初始操作。

#### 安装 GMSACredentialSpec CRD[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E5%AE%89%E8%A3%85-gmsacredentialspec-crd)

你需要在集群上配置一个用于 GMSA 凭据规约资源的 [CustomResourceDefinition](https://kubernetes.io/zh-cn/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)(CRD)， 以便定义类型为 `GMSACredentialSpec` 的自定义资源。 首先下载 GMSA CRD [YAML](https://github.com/kubernetes-sigs/windows-gmsa/blob/master/admission-webhook/deploy/gmsa-crd.yml) 并将其保存为 `gmsa-crd.yaml`。接下来执行 `kubectl apply -f gmsa-crd.yaml` 安装 CRD。

#### 安装 Webhook 来验证 GMSA 用户[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E5%AE%89%E8%A3%85-webhook-%E6%9D%A5%E9%AA%8C%E8%AF%81-gmsa-%E7%94%A8%E6%88%B7)

你需要为 Kubernetes 集群配置两个 Webhook，在 Pod 或容器级别填充和检查 GMSA 凭据规约引用。

1. 一个修改模式（Mutating）的 Webhook，将对 GMSA 的引用（在 Pod 规约中体现为名字） 展开为完整凭据规约的 JSON 形式，并保存回 Pod 规约中。
    
2. 一个验证模式（Validating）的 Webhook，确保对 GMSA 的所有引用都是已经授权 给 Pod 的服务账号使用的。
    

安装以上 Webhook 及其相关联的对象需要执行以下步骤：

1. 创建一个证书密钥对（用于允许 Webhook 容器与集群通信）
    
2. 安装一个包含如上证书的 Secret
    
3. 创建一个包含核心 Webhook 逻辑的 Deployment
    
4. 创建引用该 Deployment 的 Validating Webhook 和 Mutating Webhook 配置
    

你可以使用[这个脚本](https://github.com/kubernetes-sigs/windows-gmsa/blob/master/admission-webhook/deploy/deploy-gmsa-webhook.sh) 来部署和配置上述 GMSA Webhook 及相关联的对象。你还可以在运行脚本时设置 `--dry-run=server` 选项以便审查脚本将会对集群做出的变更。

脚本所使用的 [YAML 模板](https://github.com/kubernetes-sigs/windows-gmsa/blob/master/admission-webhook/deploy/gmsa-webhook.yml.tpl) 也可用于手动部署 Webhook 及相关联的对象，不过需要对其中的参数作适当替换。

### 在活动目录中配置 GMSA 和 Windows 节点[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E5%9C%A8%E6%B4%BB%E5%8A%A8%E7%9B%AE%E5%BD%95%E4%B8%AD%E9%85%8D%E7%BD%AE-gmsa-%E5%92%8C-windows-%E8%8A%82%E7%82%B9)

在配置 Kubernetes 中的 Pod 以使用 GMSA 之前，需要按 [Windows GMSA 文档](https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts#BKMK_Step1) 中描述的那样先在活动目录中准备好期望的 GMSA。 Windows 工作节点（作为 Kubernetes 集群的一部分）需要被配置到活动目录中，以便 访问与期望的 GSMA 相关联的秘密凭据数据。这一操作的描述位于 [Windows GMSA 文档](https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts#to-add-member-hosts-using-the-set-adserviceaccount-cmdlet) 中。

### 创建 GMSA 凭据规约资源[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E5%88%9B%E5%BB%BA-gmsa-%E5%87%AD%E6%8D%AE%E8%A7%84%E7%BA%A6%E8%B5%84%E6%BA%90)

当（如前所述）安装了 GMSACredentialSpec CRD 之后，你就可以配置包含 GMSA 凭据 规约的自定义资源了。GMSA 凭据规约中并不包含秘密或敏感数据。 其中包含的信息主要用于容器运行时，便于后者向 Windows 描述容器所期望的 GMSA。 GMSA 凭据规约可以使用 [PowerShell 脚本](https://github.com/kubernetes-sigs/windows-gmsa/tree/master/scripts/GenerateCredentialSpecResource.ps1) 以 YAML 格式生成。

下面是手动以 JSON 格式生成 GMSA 凭据规约并对其进行 YAML 转换的步骤：

1. 导入 CredentialSpec [模块](https://github.com/MicrosoftDocs/Virtualization-Documentation/blob/live/windows-server-container-tools/ServiceAccounts/CredentialSpec.psm1): `ipmo CredentialSpec.psm1`
    
2. 使用 `New-CredentialSpec` 来创建一个 JSON 格式的凭据规约。 要创建名为 `WebApp1` 的 GMSA 凭据规约，调用 `New-CredentialSpec -Name WebApp1 -AccountName WebApp1 -Domain $(Get-ADDomain -Current LocalComputer)`。
    
3. 使用 `Get-CredentialSpec` 来显示 JSON 文件的路径。
    
4. 将凭据规约从 JSON 格式转换为 YAML 格式，并添加必要的头部字段 `apiVersion`、`kind`、`metadata` 和 `credspec`，使其成为一个可以在 Kubernetes 中配置的 GMSACredentialSpec 自定义资源。
    

下面的 YAML 配置描述的是一个名为 `gmsa-WebApp1` 的 GMSA 凭据规约：

```yaml
apiVersion: windows.k8s.io/v1
kind: GMSACredentialSpec
metadata:
  name: gmsa-WebApp1  # 这是随意起的一个名字，将用作引用
credspec:
  ActiveDirectoryConfig:
    GroupManagedServiceAccounts:
    - Name: WebApp1   # GMSA 账号的用户名
      Scope: CONTOSO  # NETBIOS 域名
    - Name: WebApp1   # GMSA 账号的用户名
      Scope: contoso.com # DNS 域名
  CmsPlugins:
  - ActiveDirectory
  DomainJoinConfig:
    DnsName: contoso.com  # DNS 域名
    DnsTreeName: contoso.com # DNS 域名根
    Guid: 244818ae-87ac-4fcd-92ec-e79e5252348a  # GUID
    MachineAccountName: WebApp1 # GMSA 账号的用户名
    NetBiosName: CONTOSO  # NETBIOS 域名
    Sid: S-1-5-21-2126449477-2524075714-3094792973 # GMSA 的 SID
```

上面的凭据规约资源可以保存为 `gmsa-Webapp1-credspec.yaml`，之后使用 `kubectl apply -f gmsa-Webapp1-credspec.yml` 应用到集群上。

### 配置集群角色以启用对特定 GMSA 凭据规约的 RBAC[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E9%85%8D%E7%BD%AE%E9%9B%86%E7%BE%A4%E8%A7%92%E8%89%B2%E4%BB%A5%E5%90%AF%E7%94%A8%E5%AF%B9%E7%89%B9%E5%AE%9A-gmsa-%E5%87%AD%E6%8D%AE%E8%A7%84%E7%BA%A6%E7%9A%84-rbac)

你需要为每个 GMSA 凭据规约资源定义集群角色。 该集群角色授权某主体（通常是一个服务账号）对特定的 GMSA 资源执行 `use` 动作。 下面的示例显示的是一个集群角色，对前文创建的凭据规约 `gmsa-WebApp1` 执行鉴权。 将此文件保存为 `gmsa-webapp1-role.yaml` 并执行 `kubectl apply -f gmsa-webapp1-role.yaml`。

```yaml
# 创建集群角色读取凭据规约
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: webapp1-role
rules:
- apiGroups: ["windows.k8s.io"]
  resources: ["gmsacredentialspecs"]
  verbs: ["use"]
  resourceNames: ["gmsa-WebApp1"]
```

### 将角色指派给要使用特定 GMSA 凭据规约的服务账号[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E5%B0%86%E8%A7%92%E8%89%B2%E6%8C%87%E6%B4%BE%E7%BB%99%E8%A6%81%E4%BD%BF%E7%94%A8%E7%89%B9%E5%AE%9A-gmsa-%E5%87%AD%E6%8D%AE%E8%A7%84%E7%BA%A6%E7%9A%84%E6%9C%8D%E5%8A%A1%E8%B4%A6%E5%8F%B7)

你需要将某个服务账号（Pod 配置所对应的那个）绑定到前文创建的集群角色上。 这一绑定操作实际上授予该服务账号使用所指定的 GMSA 凭据规约资源的访问权限。 下面显示的是一个绑定到集群角色 `webapp1-role` 上的 default 服务账号，使之 能够使用前面所创建的 `gmsa-WebApp1` 凭据规约资源。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: allow-default-svc-account-read-on-gmsa-WebApp1
  namespace: default
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
roleRef:
  kind: ClusterRole
  name: webapp1-role
  apiGroup: rbac.authorization.k8s.io
```

### 在 Pod 规约中配置 GMSA 凭据规约引用[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E5%9C%A8-pod-%E8%A7%84%E7%BA%A6%E4%B8%AD%E9%85%8D%E7%BD%AE-gmsa-%E5%87%AD%E6%8D%AE%E8%A7%84%E7%BA%A6%E5%BC%95%E7%94%A8)

Pod 规约字段 `securityContext.windowsOptions.gmsaCredentialSpecName` 可用来 设置对指定 GMSA 凭据规约自定义资源的引用。 设置此引用将会配置 Pod 中的所有容器使用所给的 GMSA。 下面是一个 Pod 规约示例，其中包含了对 `gmsa-WebApp1` 凭据规约的引用：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: with-creds
  name: with-creds
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: with-creds
  template:
    metadata:
      labels:
        run: with-creds
    spec:
      securityContext:
        windowsOptions:
          gmsaCredentialSpecName: gmsa-webapp1
      containers:
      - image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019
        imagePullPolicy: Always
        name: iis
      nodeSelector:
        kubernetes.io/os: windows
```

Pod 中的各个容器也可以使用对应容器的 `securityContext.windowsOptions.gmsaCredentialSpecName` 字段来设置期望使用的 GMSA 凭据规约。 例如：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: with-creds
  name: with-creds
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: with-creds
  template:
    metadata:
      labels:
        run: with-creds
    spec:
      containers:
      - image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019
        imagePullPolicy: Always
        name: iis
        securityContext:
          windowsOptions:
            gmsaCredentialSpecName: gmsa-Webapp1
      nodeSelector:
        kubernetes.io/os: windows
```

当 Pod 规约中填充了 GMSA 相关字段（如上所述），在集群中应用 Pod 规约时会依次 发生以下事件：

1. Mutating Webhook 解析对 GMSA 凭据规约资源的引用，并将其全部展开， 得到 GMSA 凭据规约的实际内容。
    
2. Validating Webhook 确保与 Pod 相关联的服务账号有权在所给的 GMSA 凭据规约 上执行 `use` 动作。
    
3. 容器运行时为每个 Windows 容器配置所指定的 GMSA 凭据规约，这样容器就可以以 活动目录中该 GMSA 所代表的身份来执行操作，使用该身份来访问域中的服务。
    

### 使用主机名或 FQDN 对网络共享进行身份验证[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E4%BD%BF%E7%94%A8%E4%B8%BB%E6%9C%BA%E5%90%8D%E6%88%96-fqdn-%E5%AF%B9%E7%BD%91%E7%BB%9C%E5%85%B1%E4%BA%AB%E8%BF%9B%E8%A1%8C%E8%BA%AB%E4%BB%BD%E9%AA%8C%E8%AF%81)

如果你在使用主机名或 FQDN 从 Pod 连接到 SMB 共享时遇到问题，但能够通过其 IPv4 地址访问共享， 请确保在 Windows 节点上设置了以下注册表项。

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Services\hns\State" /v EnableCompartmentNamespace /t REG_DWORD /d 1
```

然后需要重新创建正在运行的 Pod 以使行为更改生效。 有关如何使用此注册表项的更多信息，请参见[此处](https://github.com/microsoft/hcsshim/blob/885f896c5a8548ca36c88c4b87fd2208c8d16543/internal/uvm/create.go#L74-L83)。

### 故障排查[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/#%E6%95%85%E9%9A%9C%E6%8E%92%E6%9F%A5)

如果在你的环境中配置 GMSA 时遇到了困难，你可以采取若干步骤来排查可能的故障。

首先，确保 credspec 已传递给 Pod。为此，你需要先运行 `exec` 进入到你的一个 Pod 中并检查 `nltest.exe /parentdomain` 命令的输出。

在下面的例子中，Pod 未能正确地获得凭据规约：

```PowerShell
kubectl exec -it iis-auth-7776966999-n5nzr powershell.exe
```

`nltest.exe /parentdomain` 导致以下错误：

```output
Getting parent domain failed: Status = 1722 0x6ba RPC_S_SERVER_UNAVAILABLE
```

如果 Pod 未能正确获得凭据规约，则下一步就要检查与域之间的通信。 首先，从 Pod 内部快速执行一个 nslookup 操作，找到域根。

这一操作会告诉我们三件事情：

1. Pod 能否访问域控制器（DC）
2. DC 能否访问 Pod
3. DNS 是否正常工作

如果 DNS 和通信测试通过，接下来你需要检查是否 Pod 已经与域之间建立了 安全通信通道。要执行这一检查，你需要再次通过 `exec` 进入到你的 Pod 中 并执行 `nltest.exe /query` 命令。

```PowerShell
nltest.exe /query
```

结果输出如下：

```output
I_NetLogonControl failed: Status = 1722 0x6ba RPC_S_SERVER_UNAVAILABLE
```

这告诉我们，由于某种原因，Pod 无法使用 credspec 中指定的帐户登录到域。 你可以尝试通过运行以下命令来修复安全通道：

```PowerShell
nltest /sc_reset:domain.example
```

如果命令成功，你将看到类似以下内容的输出：

```
Flags: 30 HAS_IP  HAS_TIMESERV
Trusted DC Name \\dc10.domain.example
Trusted DC Connection Status Status = 0 0x0 NERR_Success
The command completed successfully
```

如果以上命令修复了错误，你可以通过将以下生命周期回调添加到你的 Pod 规约中来自动执行该步骤。 如果这些操作没有修复错误，你将需要再次检查你的 credspec 并确认它是正确和完整的。

```yaml
        image: registry.domain.example/iis-auth:1809v1
        lifecycle:
          postStart:
            exec:
              command: ["powershell.exe","-command","do { Restart-Service -Name netlogon } while ( $($Result = (nltest.exe /query); if ($Result -like '*0x0 NERR_Success*') {return $true} else {return $false}) -eq $false)"]
        imagePullPolicy: IfNotPresent
```

如果你向你的 Pod 规约中添加如上所示的 `lifecycle` 节，则 Pod 会自动执行所 列举的命令来重启 `netlogon` 服务，直到 `nltest.exe /query` 命令返回时没有错误信息。

## [为 Windows 的 Pod 和容器配置 RunAsUserName](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-runasusername/)
**特性状态：** `Kubernetes v1.18 [stable]`

本页展示如何为运行为在 Windows 节点上运行的 Pod 和容器配置 `RunAsUserName`。 大致相当于 Linux 上的 `runAsUser`，允许在容器中以与默认值不同的用户名运行应用。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-runasusername/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须有一个 Kubernetes 集群，并且 kubectl 必须能和集群通信。 集群应该要有 Windows 工作节点，将在其中调度运行 Windows 工作负载的 pod 和容器。

### 为 Pod 设置 Username[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-runasusername/#set-the-username-for-a-pod)

要指定运行 Pod 容器时所使用的用户名，请在 Pod 声明中包含 `securityContext` ([PodSecurityContext](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#podsecuritycontext-v1-core)) 字段， 并在其内部包含 `windowsOptions` ([WindowsSecurityContextOptions](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#windowssecuritycontextoptions-v1-core)) 字段的 `runAsUserName` 字段。

你为 Pod 指定的 Windows SecurityContext 选项适用于该 Pod 中（包括 init 容器）的所有容器。

这儿有一个已经设置了 `runAsUserName` 字段的 Windows Pod 的配置文件：

[`windows/run-as-username-pod.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/windows/run-as-username-pod.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy windows/run-as-username-pod.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: run-as-username-pod-demo
spec:
  securityContext:
    windowsOptions:
      runAsUserName: "ContainerUser"
  containers:
  - name: run-as-username-demo
    image: mcr.microsoft.com/windows/servercore:ltsc2019
    command: ["ping", "-t", "localhost"]
  nodeSelector:
    kubernetes.io/os: windows
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/windows/run-as-username-pod.yaml
```

验证 Pod 容器是否在运行：

```shell
kubectl get pod run-as-username-pod-demo
```

获取该容器的 shell：

```shell
kubectl exec -it run-as-username-pod-demo -- powershell
```

检查运行 shell 的用户的用户名是否正确：

```powershell
echo $env:USERNAME
```

输出结果应该是这样：

```
ContainerUser
```

### 为容器设置 Username[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-runasusername/#set-the-username-for-a-container)

要指定运行容器时所使用的用户名，请在容器清单中包含 `securityContext` ([SecurityContext](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#securitycontext-v1-core)) 字段，并在其内部包含 `windowsOptions` （[WindowsSecurityContextOptions](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#windowssecuritycontextoptions-v1-core)） 字段的 `runAsUserName` 字段。

你为容器指定的 Windows SecurityContext 选项仅适用于该容器，并且它会覆盖 Pod 级别设置。

这里有一个 Pod 的配置文件，其中只有一个容器，并且在 Pod 级别和容器级别都设置了 `runAsUserName`：

[`windows/run-as-username-container.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/windows/run-as-username-container.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy windows/run-as-username-container.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: run-as-username-container-demo
spec:
  securityContext:
    windowsOptions:
      runAsUserName: "ContainerUser"
  containers:
  - name: run-as-username-demo
    image: mcr.microsoft.com/windows/servercore:ltsc2019
    command: ["ping", "-t", "localhost"]
    securityContext:
        windowsOptions:
            runAsUserName: "ContainerAdministrator"
  nodeSelector:
    kubernetes.io/os: windows
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/windows/run-as-username-container.yaml
```

验证 Pod 容器是否在运行：

```shell
kubectl get pod run-as-username-container-demo
```

获取该容器的 shell：

```shell
kubectl exec -it run-as-username-container-demo -- powershell
```

检查运行 shell 的用户的用户名是否正确（应该是容器级别设置的那个）：

```powershell
echo $env:USERNAME
```

输出结果应该是这样：

```
ContainerAdministrator
```

### Windows Username 的局限性[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-runasusername/#windows-username-limitations)

想要使用此功能，在 `runAsUserName` 字段中设置的值必须是有效的用户名。 它必须是 `DOMAIN\USER` 这种格式，其中 `DOMAIN\` 是可选的。 Windows 用户名不区分大小写。此外，关于 `DOMAIN` 和 `USER` 还有一些限制：

- `runAsUserName` 字段不能为空，并且不能包含控制字符（ASCII 值：`0x00-0x1F`、`0x7F`）
- `DOMAIN` 必须是 NetBios 名称或 DNS 名称，每种名称都有各自的局限性：
    - NetBios 名称：最多 15 个字符，不能以 `.`（点）开头，并且不能包含以下字符：`\ / : * ? " < > |`
    - DNS 名称：最多 255 个字符，只能包含字母、数字、点和中划线，并且不能以 `.`（点）或 `-`（中划线）开头和结尾。
- `USER` 最多不超过 20 个字符，不能 **只** 包含点或空格，并且不能包含以下字符：`" / \ [ ] : ; | = , + * ? < > @`

`runAsUserName` 字段接受的值的一些示例：`ContainerAdministrator`、`ContainerUser`、 `NT AUTHORITY\NETWORK SERVICE`、`NT AUTHORITY\LOCAL SERVICE`。

关于这些限制的更多信息，可以查看[这里](https://support.microsoft.com/en-us/help/909264/naming-conventions-in-active-directory-for-computers-domains-sites-and)和[这里](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.localaccounts/new-localuser?view=powershell-5.1)。

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-runasusername/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [Kubernetes 中调度 Windows 容器的指南](https://kubernetes.io/zh-cn/docs/concepts/windows/user-guide/)
- [使用组托管服务帐户（GMSA）管理工作负载身份](https://kubernetes.io/zh-cn/docs/concepts/windows/user-guide/#managing-workload-identity-with-group-managed-service-accounts)
- [Windows 下 pod 和容器的 GMSA 配置](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-gmsa/)

## [创建 Windows HostProcess Pod](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/)
**特性状态：** `Kubernetes v1.26 [stable]`

Windows HostProcess 容器让你能够在 Windows 主机上运行容器化负载。 这类容器以普通的进程形式运行，但能够在具有合适用户特权的情况下， 访问主机网络名字空间、存储和设备。HostProcess 容器可用来在 Windows 节点上部署网络插件、存储配置、设备插件、kube-proxy 以及其他组件， 同时不需要配置专用的代理或者直接安装主机服务。

类似于安装安全补丁、事件日志收集等这类管理性质的任务可以在不需要集群操作员登录到每个 Windows 节点的前提下执行。HostProcess 容器可以以主机上存在的任何用户账号来运行， 也可以以主机所在域中的用户账号运行，这样管理员可以通过用户许可权限来限制资源访问。 尽管文件系统和进程隔离都不支持，在启动容器时会在主机上创建一个新的卷， 为其提供一个干净的、整合的工作空间。HostProcess 容器也可以基于现有的 Windows 基础镜像来制作，并且不再有 Windows 服务器容器所带有的那些 [兼容性需求](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)， 这意味着基础镜像的版本不必与主机操作系统的版本匹配。 不过，仍然建议你像使用 Windows 服务器容器负载那样，使用相同的基础镜像版本， 这样你就不会有一些未使用的镜像占用节点上的存储空间。HostProcess 容器也支持 在容器卷内执行[卷挂载](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#volume-mounts)。

#### 我何时该使用 Windows HostProcess 容器？[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#%E6%88%91%E4%BD%95%E6%97%B6%E8%AF%A5%E4%BD%BF%E7%94%A8-windows-hostprocess-%E5%AE%B9%E5%99%A8)

- 当你准备执行需要访问主机上网络名字空间的任务时，HostProcess 容器能够访问主机上的网络接口和 IP 地址。
- 当你需要访问主机上的资源，如文件系统、事件日志等等。
- 需要安装特定的设备驱动或者 Windows 服务时。
- 需要对管理任务和安全策略进行整合时。使用 HostProcess 容器能够缩小 Windows 节点上所需要的特权范围。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

本任务指南是特定于 Kubernetes v1.26 的。 如果你运行的不是 Kubernetes v1.26，请移步访问正确 版本的 Kubernetes 文档。

在 Kubernetes v1.26 中，HostProcess 容器功能特性默认是启用的。 kubelet 会直接与 containerd 通信，通过 CRI 将主机进程标志传递过去。 你可以使用 containerd 的最新版本（v1.6+）来运行 HostProcess 容器。 参阅[如何安装 containerd](https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes/#containerd)。

### 限制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#limitations)

以下限制是与 Kubernetes v1.26 相关的：

- HostProcess 容器需要 containerd 1.6 或更高版本的 [容器运行时](https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes)， 推荐 containerd 1.7。
- HostProcess Pods 只能包含 HostProcess 容器。这是在 Windows 操作系统上的约束； 非特权的 Windows 容器不能与主机 IP 名字空间共享虚拟网卡（vNIC）。
- HostProcess 在主机上以一个进程的形式运行，除了通过 HostProcess 用户账号所实施的资源约束外，不提供任何形式的隔离。HostProcess 容器不支持文件系统或 Hyper-V 隔离。

- 卷挂载是被支持的，并且要花在到容器卷下。参见[卷挂载](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#volume-mounts)。
- 默认情况下有一组主机用户账号可供 HostProcess 容器使用。 参见[选择用户账号](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#choosing-a-user-account)。
- 对资源约束（磁盘、内存、CPU 个数）的支持与主机上进程相同。
- **不支持**命名管道或者 UNIX 域套接字形式的挂载，需要使用主机上的路径名来访问 （例如，\\\\.\\pipe\\\*）。

### HostProcess Pod 配置需求[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#hostprocess-pod-configuration-requirements)

启用 Windows HostProcess Pod 需要在 Pod 安全配置中设置合适的选项。 在 [Pod 安全标准](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards)中所定义的策略中， HostProcess Pod 默认是不被 basline 和 restricted 策略支持的。因此建议 HostProcess 运行在与 privileged 模式相看齐的策略下。

当运行在 privileged 策略下时，下面是要启用 HostProcess Pod 创建所需要设置的选项：

| 控制 | 策略 |
| --- | --- |
| [securityContext.windowsOptions.hostProcess](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards) | 
Windows Pods 提供运行 [HostProcess 容器](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod)的能力，这类容器能够具有对 Windows 节点的特权访问权限。

**可选值**

- `true`

 |
| [hostNetwork](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards) | 

Pod 容器 HostProcess 容器必须使用主机的网络名字空间。

**可选值**

- `true`

 |
| [securityContext.windowsOptions.runAsUserName](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-runasusername/) | 

关于 HostProcess 容器所要使用的用户的规约，需要设置在 Pod 的规约中。

**可选值**

- `NT AUTHORITY\SYSTEM`
- `NT AUTHORITY\Local service`
- `NT AUTHORITY\NetworkService`
- 本地用户组名称（参见下文）

 |
| [runAsNonRoot](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards) | 

因为 HostProcess 容器有访问主机的特权，runAsNonRoot 字段不可以设置为 true。

**可选值**

- 未定义/Nil
- `false`

 |

#### 配置清单示例（片段）[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#manifest-example)

```yaml
spec:
  securityContext:
    windowsOptions:
      hostProcess: true
      runAsUserName: "NT AUTHORITY\\Local service"
  hostNetwork: true
  containers:
  - name: test
    image: image1:latest
    command:
      - ping
      - -t
      - 127.0.0.1
  nodeSelector:
    "kubernetes.io/os": windows
```

### 卷挂载[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#volume-mounts)

HostProcess 容器支持在容器卷空间中挂载卷的能力。 卷挂载行为将因节点所使用的 containerd 运行时版本而异。

#### containerd v1.6[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#containerd-v1-6)

在容器内运行的应用能够通过相对或者绝对路径直接访问卷挂载。 环境变量 `$CONTAINER_SANDBOX_MOUNT_POINT` 在容器创建时被设置为指向容器卷的绝对主机路径。 相对路径是基于 `.spec.containers.volumeMounts.mountPath` 配置来推导的。

容器内支持通过下面的路径结构来访问服务账号令牌：

- `.\var\run\secrets\kubernetes.io\serviceaccount\`
- `$CONTAINER_SANDBOX_MOUNT_POINT\var\run\secrets\kubernetes.io\serviceaccount\`

#### containerd v1.7（及更高版本）[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#containerd-v1-7-and-greater)

容器内运行的应用可以通过 volumeMount 指定的 `mountPath` 直接访问卷挂载 （就像 Linux 和非 HostProcess Windows 容器一样）。

为了向后兼容性，卷也可以通过使用由 containerd v1.6 配置的相同相对路径进行访问。

例如，要在容器中访问服务帐户令牌，你将使用以下路径之一：

- `c:\var\run\secrets\kubernetes.io\serviceaccount`
- `/var/run/secrets/kubernetes.io/serviceaccount/`
- `$CONTAINER_SANDBOX_MOUNT_POINT\var\run\secrets\kubernetes.io\serviceaccount\`

### 资源限制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#resource-limits)

资源限制（磁盘、内存、CPU 个数）作用到任务之上，并在整个任务上起作用。 例如，如果内存限制设置为 10MB，任何 HostProcess 任务对象所分配的内存不会超过 10MB。 这一行为与其他 Windows 容器类型相同。资源限制的设置方式与编排系统或容器运行时无关。 唯一的区别是用来跟踪资源所进行的磁盘资源用量的计算，出现差异的原因是因为 HostProcess 容器启动引导的方式造成的。

### 选择用户账号[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#choosing-a-user-account)

#### 系统账号[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#system-accounts)

默认情况下，HostProcess 容器支持以三种被支持的 Windows 服务账号之一来运行：

- **[LocalSystem](https://docs.microsoft.com/windows/win32/services/localsystem-account)**
- **[LocalService](https://docs.microsoft.com/windows/win32/services/localservice-account)**
- **[NetworkService](https://docs.microsoft.com/windows/win32/services/networkservice-account)**

你应该为每个 HostProcess 容器选择一个合适的 Windows 服务账号，尝试限制特权范围， 避免给主机代理意外的（甚至是恶意的）伤害。LocalSystem 服务账号的特权级 在三者之中最高，只有在绝对需要的时候才应该使用。只要可能，应该使用 LocalService 服务账号，因为该账号在三者中特权最低。

#### 本地账号[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#local-accounts)

取决于配置，HostProcess 容器也能够以本地用户账号运行， 从而允许节点操作员为工作负载提供细粒度的访问权限。

要以本地用户运行 HostProcess 容器，必须首先在节点上创建一个本地用户组， 并在部署中在 `runAsUserName` 字段中指定该本地用户组的名称。 在初始化 HostProcess 容器之前，将创建一个新的**临时**本地用户账号，并加入到指定的用户组中， 使用这个账号来运行容器。这样做有许多好处，包括不再需要管理本地用户账号的密码。 作为服务账号运行的初始 HostProcess 容器可用于准备用户组，以供后续的 HostProcess 容器使用。

**说明：**

以本地用户账号运行 HostProcess 容器需要 containerd v1.7+。

例如：

1. 在节点上创建本地用户组（这可以在另一个 HostProcess 容器中完成）。
    
    ```cmd
    net localgroup hpc-localgroup /add
    ```
    

2. 为本地用户组授予访问所需资源的权限。这可以通过使用 [icacls](https://learn.microsoft.com/zh-cn/windows-server/administration/windows-commands/icacls) 这类工具达成。

3. 针对 Pod 或个别容器，将 `runAsUserName` 设置为本地用户组的名称。
    
    ```yaml
    securityContext:
      windowsOptions:
        hostProcess: true
        runAsUserName: hpc-localgroup
    ```
    

4. 调度 Pod！

### HostProcess 容器的基础镜像[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#base-image-for-hostprocess-containers)

HostProcess 容器可以基于任何现有的 [Windows Container 基础镜像](https://learn.microsoft.com/zh-cn/virtualization/windowscontainers/manage-containers/container-base-images)进行构建。

此外，还专为 HostProcess 容器创建了一个新的基础镜像！有关更多信息，请查看 [windows-host-process-containers-base-image github 项目](https://github.com/microsoft/windows-host-process-containers-base-image#overview)。

### HostProcess 容器的故障排查[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#troubleshooting-hostprocess-containers)

- HostProcess 容器因 `failed to create user process token: failed to logon user: Access is denied.: unknown` 启动失败。
    
    确保 containerd 以 `LocalSystem` 或 `LocalService` 服务帐户运行。 用户账号（即使是管理员账号）没有权限为任何支持的[用户账号](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/create-hostprocess-pod/#choosing-a-user-account)创建登录令牌。

## [配置 Pod 的服务质量](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/)
本页介绍怎样配置 Pod 以让其归属于特定的 [服务质量类（Quality of Service class，QoS class）](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-qos-class). Kubernetes 在 Node 资源不足时使用 QoS 类来就驱逐 Pod 作出决定。

Kubernetes 创建 Pod 时，会将如下 QoS 类之一设置到 Pod 上：

- [Guaranteed](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-qos/#guaranteed)
- [Burstable](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-qos/#burstable)
- [BestEffort](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-qos/#besteffort)

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你还需要能够创建和删除名字空间。

### 创建名字空间[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#create-a-namespace)

创建一个名字空间，以便将本练习所创建的资源与集群的其余资源相隔离。

```shell
kubectl create namespace qos-example
```

### 创建一个 QoS 类为 Guaranteed 的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#create-a-pod-that-gets-assigned-a-qos-class-of-guaranteed)

对于 QoS 类为 `Guaranteed` 的 Pod：

- Pod 中的每个容器都必须指定内存限制和内存请求。
- 对于 Pod 中的每个容器，内存限制必须等于内存请求。
- Pod 中的每个容器都必须指定 CPU 限制和 CPU 请求。
- 对于 Pod 中的每个容器，CPU 限制必须等于 CPU 请求。

这些限制同样适用于初始化容器和应用程序容器。 [临时容器（Ephemeral Container）](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/ephemeral-containers/) 无法定义资源，因此不受这些约束限制。

下面是包含一个 Container 的 Pod 清单。该 Container 设置了内存请求和内存限制，值都是 200 MiB。 该 Container 设置了 CPU 请求和 CPU 限制，值都是 700 milliCPU：

[`pods/qos/qos-pod.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/qos/qos-pod.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/qos/qos-pod.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: qos-demo
  namespace: qos-example
spec:
  containers:
  - name: qos-demo-ctr
    image: nginx
    resources:
      limits:
        memory: "200Mi"
        cpu: "700m"
      requests:
        memory: "200Mi"
        cpu: "700m"
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/qos/qos-pod.yaml --namespace=qos-example
```

查看 Pod 详情：

```shell
kubectl get pod qos-demo --namespace=qos-example --output=yaml
```

结果表明 Kubernetes 为 Pod 配置的 QoS 类为 `Guaranteed`。 结果也确认了 Pod 容器设置了与内存限制匹配的内存请求，设置了与 CPU 限制匹配的 CPU 请求。

```yaml
spec:
  containers:
    ...
    resources:
      limits:
        cpu: 700m
        memory: 200Mi
      requests:
        cpu: 700m
        memory: 200Mi
    ...
status:
  qosClass: Guaranteed
```

**说明：**

如果某 Container 指定了自己的内存限制，但没有指定内存请求，Kubernetes 会自动为它指定与内存限制相等的内存请求。同样，如果容器指定了自己的 CPU 限制， 但没有指定 CPU 请求，Kubernetes 会自动为它指定与 CPU 限制相等的 CPU 请求。

##### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#clean-up-guaranteed)

删除 Pod：

```shell
kubectl delete pod qos-demo --namespace=qos-example
```

### 创建一个 QoS 类为 Burstable 的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#create-a-pod-that-gets-assigned-a-qos-class-of-burstable)

如果满足下面条件，Kubernetes 将会指定 Pod 的 QoS 类为 `Burstable`：

- Pod 不符合 `Guaranteed` QoS 类的标准。
- Pod 中至少一个 Container 具有内存或 CPU 的请求或限制。

下面是包含一个 Container 的 Pod 清单。该 Container 设置的内存限制为 200 MiB， 内存请求为 100 MiB。

[`pods/qos/qos-pod-2.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/qos/qos-pod-2.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/qos/qos-pod-2.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: qos-demo-2
  namespace: qos-example
spec:
  containers:
  - name: qos-demo-2-ctr
    image: nginx
    resources:
      limits:
        memory: "200Mi"
      requests:
        memory: "100Mi"
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/qos/qos-pod-2.yaml --namespace=qos-example
```

查看 Pod 详情：

```shell
kubectl get pod qos-demo-2 --namespace=qos-example --output=yaml
```

结果表明 Kubernetes 为 Pod 配置的 QoS 类为 `Burstable`。

```yaml
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: qos-demo-2-ctr
    resources:
      limits:
        memory: 200Mi
      requests:
        memory: 100Mi
  ...
status:
  qosClass: Burstable
```

##### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#clean-up-burstable)

删除 Pod：

```shell
kubectl delete pod qos-demo-2 --namespace=qos-example
```

### 创建一个 QoS 类为 BestEffort 的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#create-a-pod-that-gets-assigned-a-qos-class-of-besteffort)

对于 QoS 类为 `BestEffort` 的 Pod，Pod 中的 Container 必须没有设置内存和 CPU 限制或请求。

下面是包含一个 Container 的 Pod 清单。该 Container 没有设置内存和 CPU 限制或请求。

[`pods/qos/qos-pod-3.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/qos/qos-pod-3.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/qos/qos-pod-3.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: qos-demo-3
  namespace: qos-example
spec:
  containers:
  - name: qos-demo-3-ctr
    image: nginx
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/qos/qos-pod-3.yaml --namespace=qos-example
```

查看 Pod 详情：

```shell
kubectl get pod qos-demo-3 --namespace=qos-example --output=yaml
```

结果表明 Kubernetes 为 Pod 配置的 QoS 类为 `BestEffort`。

```yaml
spec:
  containers:
    ...
    resources: {}
  ...
status:
  qosClass: BestEffort
```

##### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#clean-up-besteffort)

删除 Pod：

```shell
kubectl delete pod qos-demo-3 --namespace=qos-example
```

### 创建包含两个容器的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#create-a-pod-that-has-two-containers)

下面是包含两个 Container 的 Pod 清单。一个 Container 指定内存请求为 200 MiB。 另外一个 Container 没有指定任何请求或限制。

[`pods/qos/qos-pod-4.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/qos/qos-pod-4.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/qos/qos-pod-4.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: qos-demo-4
  namespace: qos-example
spec:
  containers:

  - name: qos-demo-4-ctr-1
    image: nginx
    resources:
      requests:
        memory: "200Mi"

  - name: qos-demo-4-ctr-2
    image: redis
```

注意此 Pod 满足 `Burstable` QoS 类的标准。也就是说它不满足 `Guaranteed` QoS 类标准， 因为它的 Container 之一设有内存请求。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/qos/qos-pod-4.yaml --namespace=qos-example
```

查看 Pod 详情：

```shell
kubectl get pod qos-demo-4 --namespace=qos-example --output=yaml
```

结果表明 Kubernetes 为 Pod 配置的 QoS 类为 `Burstable`：

```yaml
spec:
  containers:
    ...
    name: qos-demo-4-ctr-1
    resources:
      requests:
        memory: 200Mi
    ...
    name: qos-demo-4-ctr-2
    resources: {}
    ...
status:
  qosClass: Burstable
```

### 检视 Pod 的 QoS 类[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#retrieve-the-qos-class-for-a-pod)

你也可以只查看你所需要的字段，而不是查看所有字段：

```bash
kubectl --namespace=qos-example get pod qos-demo-4 -o jsonpath='{ .status.qosClass}{"\n"}'
```

```none
Burstable
```

### 环境清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#clean-up)

删除名字空间：

```shell
kubectl delete namespace qos-example
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

#### 应用开发者参考[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E8%80%85%E5%8F%82%E8%80%83)

- [为 Pod 和容器分配内存资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/)
- [为 Pod 和容器分配 CPU 资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/)

#### 集群管理员参考[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/quality-service-pod/#%E9%9B%86%E7%BE%A4%E7%AE%A1%E7%90%86%E5%91%98%E5%8F%82%E8%80%83)

- [为名字空间配置默认的内存请求和限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
- [为名字空间配置默认的 CPU 请求和限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)
- [为名字空间配置最小和最大内存限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/memory-constraint-namespace/)
- [为名字空间配置最小和最大 CPU 限制](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/)
- [为名字空间配置内存和 CPU 配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)
- [为名字空间配置 Pod 配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/manage-resources/quota-pod-namespace/)
- [为 API 对象配置配额](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/quota-api-object/)
- [控制节点上的拓扑管理策略](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/topology-manager/)

## [为容器分派扩展资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/)

**特性状态：** `Kubernetes v1.26 [stable]`

本文介绍如何为容器指定扩展资源。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

在你开始此练习前，请先练习 [为节点广播扩展资源](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/extended-resource-node/)。 在那个练习中将配置你的一个节点来广播 dongle 资源。

### 给 Pod 分派扩展资源[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/#%E7%BB%99-pod-%E5%88%86%E6%B4%BE%E6%89%A9%E5%B1%95%E8%B5%84%E6%BA%90)

要请求扩展资源，需要在你的容器清单中包括 `resources:requests` 字段。 扩展资源可以使用任何完全限定名称，只是不能使用 `*.kubernetes.io/`。 有效的扩展资源名的格式为 `example.com/foo`，其中 `example.com` 应被替换为 你的组织的域名，而 `foo` 则是描述性的资源名称。

下面是包含一个容器的 Pod 配置文件：

[`pods/resource/extended-resource-pod.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/resource/extended-resource-pod.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/resource/extended-resource-pod.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: extended-resource-demo
spec:
  containers:
  - name: extended-resource-demo-ctr
    image: nginx
    resources:
      requests:
        example.com/dongle: 3
      limits:
        example.com/dongle: 3
```

在配置文件中，你可以看到容器请求了 3 个 dongles。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/extended-resource-pod.yaml
```

检查 Pod 是否运行正常：

```shell
kubectl get pod extended-resource-demo
```

描述 Pod:

```shell
kubectl describe pod extended-resource-demo
```

输出结果显示 dongle 请求如下：

```yaml
Limits:
  example.com/dongle: 3
Requests:
  example.com/dongle: 3
```

### 尝试创建第二个 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/#%E5%B0%9D%E8%AF%95%E5%88%9B%E5%BB%BA%E7%AC%AC%E4%BA%8C%E4%B8%AA-pod)

下面是包含一个容器的 Pod 配置文件，容器请求了 2 个 dongles。

[`pods/resource/extended-resource-pod-2.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/resource/extended-resource-pod-2.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/resource/extended-resource-pod-2.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: extended-resource-demo-2
spec:
  containers:
  - name: extended-resource-demo-2-ctr
    image: nginx
    resources:
      requests:
        example.com/dongle: 2
      limits:
        example.com/dongle: 2
```

Kubernetes 将不能满足 2 个 dongles 的请求，因为第一个 Pod 已经使用了 4 个可用 dongles 中的 3 个。

尝试创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/resource/extended-resource-pod-2.yaml
```

描述 Pod：

```shell
kubectl describe pod extended-resource-demo-2
```

输出结果表明 Pod 不能被调度，因为没有一个节点上存在两个可用的 dongles。

```
Conditions:
  Type    Status
  PodScheduled  False
...
Events:
  ...
  ... Warning   FailedScheduling  pod (extended-resource-demo-2) failed to fit in any node
fit failure summary on nodes : Insufficient example.com/dongle (1)
```

查看 Pod 的状态：

```shell
kubectl get pod extended-resource-demo-2
```

输出结果表明 Pod 虽然被创建了，但没有被调度到节点上正常运行。Pod 的状态为 Pending：

```yaml
NAME                       READY     STATUS    RESTARTS   AGE
extended-resource-demo-2   0/1       Pending   0          6m
```

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/#%E6%B8%85%E7%90%86)

删除本练习中创建的 Pod：

```shell
kubectl delete pod extended-resource-demo
kubectl delete pod extended-resource-demo-2
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

### 应用开发者参考[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/#%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E8%80%85%E5%8F%82%E8%80%83)

- [为容器和 Pod 分配内存资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-memory-resource/)
- [为容器和 Pod 分配 CPU 资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-cpu-resource/)

#### 集群管理员参考[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/extended-resource/#%E9%9B%86%E7%BE%A4%E7%AE%A1%E7%90%86%E5%91%98%E5%8F%82%E8%80%83)

- [为节点广播扩展资源](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/extended-resource-node/)
## [配置 Pod 以使用卷进行存储](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-volume-storage/)
此页面展示了如何配置 Pod 以使用卷进行存储。

只要容器存在，容器的文件系统就会存在，因此当一个容器终止并重新启动，对该容器的文件系统改动将丢失。 对于独立于容器的持久化存储，你可以使用[卷](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/)。 这对于有状态应用程序尤为重要，例如键值存储（如 Redis）和数据库。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-volume-storage/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 为 Pod 配置卷[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-volume-storage/#configure-a-volume-for-a-pod)

在本练习中，你将创建一个运行 Pod，该 Pod 仅运行一个容器并拥有一个类型为 [emptyDir](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#emptydir) 的卷， 在整个 Pod 生命周期中一直存在，即使 Pod 中的容器被终止和重启。以下是 Pod 的配置：

[`pods/storage/redis.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/storage/redis.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/storage/redis.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage
      mountPath: /data/redis
  volumes:
  - name: redis-storage
    emptyDir: {}
```

1. 创建 Pod:
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/storage/redis.yaml
    ```
    

2. 验证 Pod 中的容器是否正在运行，然后留意 Pod 的更改：
    
    ```shell
    kubectl get pod redis --watch
    ```
    
    输出如下：
    
    ```console
    NAME      READY     STATUS    RESTARTS   AGE
    redis     1/1       Running   0          13s
    ```
    

3. 在另一个终端，用 Shell 连接正在运行的容器：
    
    ```shell
    kubectl exec -it redis -- /bin/bash
    ```
    

4. 在你的 Shell 中，切换到 `/data/redis` 目录下，然后创建一个文件：
    
    ```shell
    root@redis:/data# cd /data/redis/
    root@redis:/data/redis# echo Hello > test-file
    ```
    

5. 在你的 Shell 中，列出正在运行的进程：
    
    ```shell
    root@redis:/data/redis# apt-get update
    root@redis:/data/redis# apt-get install procps
    root@redis:/data/redis# ps aux
    ```
    
    输出类似于：
    
    ```console
    USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    redis        1  0.1  0.1  33308  3828 ?        Ssl  00:46   0:00 redis-server *:6379
    root        12  0.0  0.0  20228  3020 ?        Ss   00:47   0:00 /bin/bash
    root        15  0.0  0.0  17500  2072 ?        R+   00:48   0:00 ps aux
    ```
    

6. 在你的 Shell 中，结束 Redis 进程：
    
    ```shell
    root@redis:/data/redis# kill <pid>
    ```
    
    其中 `<pid>` 是 Redis 进程的 ID (PID)。
    

7. 在你原先终端中，留意 Redis Pod 的更改。最终你将会看到和下面类似的输出：
    
    ```console
    NAME      READY     STATUS     RESTARTS   AGE
    redis     1/1       Running    0          13s
    redis     0/1       Completed  0         6m
    redis     1/1       Running    1         6m
    ```
    

此时，容器已经终止并重新启动。这是因为 Redis Pod 的 [restartPolicy](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#podspec-v1-core) 为 `Always`。

1. 用 Shell 进入重新启动的容器中：
    
    ```shell
    kubectl exec -it redis -- /bin/bash
    ```
    

2. 在你的 Shell 中，进入到 `/data/redis` 目录下，并确认 `test-file` 文件是否仍然存在。
    
    ```shell
    root@redis:/data/redis# cd /data/redis/
    root@redis:/data/redis# ls
    test-file
    ```
    

3. 删除为此练习所创建的 Pod：
    
    ```shell
    kubectl delete pod redis
    ```
    

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-volume-storage/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 参阅 [Volume](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#volume-v1-core)。
- 参阅 [Pod](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#pod-v1-core)。
- 除了 `emptyDir` 提供的本地磁盘存储外，Kubernetes 还支持许多不同的网络附加存储解决方案， 包括 GCE 上的 PD 和 EC2 上的 EBS，它们是关键数据的首选，并将处理节点上的一些细节， 例如安装和卸载设备。了解更多详情请参阅[卷](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/)。
## [配置 Pod 以使用 PersistentVolume 作为存储](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)
本文将向你介绍如何配置 Pod 使用 [PersistentVolumeClaim](https://kubernetes.io/zh-cn/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) 作为存储。 以下是该过程的总结：

1. 你作为集群管理员创建由物理存储支持的 PersistentVolume。你不会将该卷与任何 Pod 关联。
    
2. 你现在以开发人员或者集群用户的角色创建一个 PersistentVolumeClaim， 它将自动绑定到合适的 PersistentVolume。
    
3. 你创建一个使用以上 PersistentVolumeClaim 作为存储的 Pod。
    

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

- 你需要一个包含单个节点的 Kubernetes 集群，并且必须配置 [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/) 命令行工具以便与集群交互。 如果还没有单节点集群，可以使用 [Minikube](https://minikube.sigs.k8s.io/docs/) 创建一个。
    
- 熟悉[持久卷](https://kubernetes.io/zh-cn/docs/concepts/storage/persistent-volumes/)文档。
    

### 在你的节点上创建一个 index.html 文件[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-an-index-file-on-your-node)

打开集群中的某个节点的 Shell。 如何打开 Shell 取决于集群的设置。 例如，如果你正在使用 Minikube，那么可以通过输入 `minikube ssh` 来打开节点的 Shell。

在该节点的 Shell 中，创建一个 `/mnt/data` 目录：

```shell
# 这里假定你的节点使用 "sudo" 来以超级用户角色执行命令
sudo mkdir /mnt/data
```

在 `/mnt/data` 目录中创建一个 index.html 文件：

```shell
# 这里再次假定你的节点使用 "sudo" 来以超级用户角色执行命令
sudo sh -c "echo 'Hello from Kubernetes storage' > /mnt/data/index.html"
```

**说明：**

如果你的节点使用某工具而不是 `sudo` 来完成超级用户访问，你可以将上述命令中的 `sudo` 替换为该工具的名称。

测试 `index.html` 文件确实存在：

```shell
cat /mnt/data/index.html
```

输出应该是：

```
Hello from Kubernetes storage
```

现在你可以关闭节点的 Shell 了。

### 创建 PersistentVolume[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-pv)

在本练习中，你将创建一个 **hostPath** 类型的 PersistentVolume。 Kubernetes 支持用于在单节点集群上开发和测试的 hostPath 类型的 PersistentVolume。 hostPath 类型的 PersistentVolume 使用节点上的文件或目录来模拟网络附加存储。

在生产集群中，你不会使用 hostPath。 集群管理员会提供网络存储资源，比如 Google Compute Engine 持久盘卷、NFS 共享卷或 Amazon Elastic Block Store 卷。 集群管理员还可以使用 [StorageClasses](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#storageclass-v1-storage-k8s-io) 来设置[动态制备存储](https://kubernetes.io/zh-cn/docs/concepts/storage/dynamic-provisioning/)。

下面是 hostPath PersistentVolume 的配置文件：

[`pods/storage/pv-volume.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/storage/pv-volume.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/storage/pv-volume.yaml to clipboard")

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

此配置文件指定卷位于集群节点上的 `/mnt/data` 路径。 其配置还指定了卷的容量大小为 10 GB，访问模式为 `ReadWriteOnce`， 这意味着该卷可以被单个节点以读写方式安装。 此配置文件还在 PersistentVolume 中定义了 [StorageClass 的名称](https://kubernetes.io/zh-cn/docs/concepts/storage/persistent-volumes/#class)为 `manual`。 它将用于将 PersistentVolumeClaim 的请求绑定到此 PersistentVolume。

创建 PersistentVolume：

```shell
kubectl apply -f https://k8s.io/examples/pods/storage/pv-volume.yaml
```

查看 PersistentVolume 的信息：

```shell
kubectl get pv task-pv-volume
```

输出结果显示该 PersistentVolume 的`状态（STATUS）`为 `Available`。 这意味着它还没有被绑定给 PersistentVolumeClaim。

```
NAME             CAPACITY   ACCESSMODES   RECLAIMPOLICY   STATUS      CLAIM     STORAGECLASS   REASON    AGE
task-pv-volume   10Gi       RWO           Retain          Available             manual                   4s
```

### 创建 PersistentVolumeClaim[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-pvc)

下一步是创建一个 PersistentVolumeClaim。 Pod 使用 PersistentVolumeClaim 来请求物理存储。 在本练习中，你将创建一个 PersistentVolumeClaim，它请求至少 3 GB 容量的卷， 该卷至少可以为一个节点提供读写访问。

下面是 PersistentVolumeClaim 的配置文件：

[`pods/storage/pv-claim.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/storage/pv-claim.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/storage/pv-claim.yaml to clipboard")

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

创建 PersistentVolumeClaim：

```shell
kubectl apply -f https://k8s.io/examples/pods/storage/pv-claim.yaml
```

创建 PersistentVolumeClaim 之后，Kubernetes 控制平面将查找满足申领要求的 PersistentVolume。 如果控制平面找到具有相同 StorageClass 的适当的 PersistentVolume， 则将 PersistentVolumeClaim 绑定到该 PersistentVolume 上。

再次查看 PersistentVolume 信息：

```shell
kubectl get pv task-pv-volume
```

现在输出的 `STATUS` 为 `Bound`。

```
NAME             CAPACITY   ACCESSMODES   RECLAIMPOLICY   STATUS    CLAIM                   STORAGECLASS   REASON    AGE
task-pv-volume   10Gi       RWO           Retain          Bound     default/task-pv-claim   manual                   2m
```

查看 PersistentVolumeClaim：

```shell
kubectl get pvc task-pv-claim
```

输出结果表明该 PersistentVolumeClaim 绑定了你的 PersistentVolume `task-pv-volume`。

```
NAME            STATUS    VOLUME           CAPACITY   ACCESSMODES   STORAGECLASS   AGE
task-pv-claim   Bound     task-pv-volume   10Gi       RWO           manual         30s
```

### 创建 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-pod)

下一步是创建一个使用你的 PersistentVolumeClaim 作为存储卷的 Pod。

下面是此 Pod 的配置文件：

[`pods/storage/pv-pod.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/storage/pv-pod.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/storage/pv-pod.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage


```

注意 Pod 的配置文件指定了 PersistentVolumeClaim，但没有指定 PersistentVolume。 对 Pod 而言，PersistentVolumeClaim 就是一个存储卷。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/storage/pv-pod.yaml
```

检查 Pod 中的容器是否运行正常：

```shell
kubectl get pod task-pv-pod
```

打开一个 Shell 访问 Pod 中的容器：

```shell
kubectl exec -it task-pv-pod -- /bin/bash
```

在 Shell 中，验证 Nginx 是否正在从 hostPath 卷提供 `index.html` 文件：

```shell
# 一定要在上一步 "kubectl exec" 所返回的 Shell 中执行下面三个命令
apt update
apt install curl
curl http://localhost/
```

输出结果是你之前写到 hostPath 卷中的 `index.html` 文件中的内容：

```
Hello from Kubernetes storage
```

如果你看到此消息，则证明你已经成功地配置了 Pod 使用 PersistentVolumeClaim 的存储。

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#clean-up)

删除 Pod、PersistentVolumeClaim 和 PersistentVolume 对象：

```shell
kubectl delete pod task-pv-pod
kubectl delete pvc task-pv-claim
kubectl delete pv task-pv-volume
```

如果你还没有连接到集群中节点的 Shell，可以按之前所做操作，打开一个新的 Shell。

在节点的 Shell 上，删除你所创建的目录和文件：

```shell
# 这里假定你使用 "sudo" 来以超级用户的角色执行命令
sudo rm /mnt/data/index.html
sudo rmdir /mnt/data
```

你现在可以关闭连接到节点的 Shell。

### 在两个地方挂载相同的 persistentVolume[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#mounting-the-same-pv-in-two-places)

[`pods/storage/pv-duplicate.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/storage/pv-duplicate.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/storage/pv-duplicate.yaml to clipboard")

```yaml

apiVersion: v1
kind: Pod
metadata:
  name: test
spec:
  containers:
    - name: test
      image: nginx
      volumeMounts:
        # 网站数据挂载
        - name: config
          mountPath: /usr/share/nginx/html
          subPath: html
        # Nginx 配置挂载
        - name: config
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf
  volumes:
    - name: config
      persistentVolumeClaim:
        claimName: test-nfs-claim
```

你可以在 nginx 容器上执行两个卷挂载:

- `/usr/share/nginx/html` 用于静态网站
- `/etc/nginx/nginx.conf` 作为默认配置

### 访问控制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#access-control)

使用组 ID（GID）配置的存储仅允许 Pod 使用相同的 GID 进行写入。 GID 不匹配或缺失将会导致无权访问错误。 为了减少与用户的协调，管理员可以对 PersistentVolume 添加 GID 注解。 这样 GID 就能自动添加到使用 PersistentVolume 的任何 Pod 中。

使用 `pv.beta.kubernetes.io/gid` 注解的方法如下所示：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv1
  annotations:
    pv.beta.kubernetes.io/gid: "1234"
```

当 Pod 使用带有 GID 注解的 PersistentVolume 时，注解的 GID 会被应用于 Pod 中的所有容器， 应用的方法与 Pod 的安全上下文中指定的 GID 相同。 每个 GID，无论是来自 PersistentVolume 注解还是来自 Pod 规约，都会被应用于每个容器中运行的第一个进程。

**说明：**

当 Pod 使用 PersistentVolume 时，与 PersistentVolume 关联的 GID 不会在 Pod 资源本身的对象上出现。

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 进一步了解 [PersistentVolumes](https://kubernetes.io/zh-cn/docs/concepts/storage/persistent-volumes/)
- 阅读[持久存储设计文档](https://git.k8s.io/design-proposals-archive/storage/persistent-storage.md)

#### 参考[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#reference)

- [PersistentVolume](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#persistentvolume-v1-core)
- [PersistentVolumeSpec](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#persistentvolumespec-v1-core)
- [PersistentVolumeClaim](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#persistentvolumeclaim-v1-core)
- [PersistentVolumeClaimSpec](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#persistentvolumeclaimspec-v1-core)
## [配置 Pod 使用投射卷作存储](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-projected-volume-storage/)
本文介绍怎样通过[`projected`](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#projected) 卷将现有的多个卷资源挂载到相同的目录。 当前，`secret`、`configMap`、`downwardAPI` 和 `serviceAccountToken` 卷可以被投射。

**说明：** `serviceAccountToken` 不是一种卷类型

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-projected-volume-storage/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 为 Pod 配置投射卷[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-projected-volume-storage/#%E4%B8%BA-pod-%E9%85%8D%E7%BD%AE%E6%8A%95%E5%B0%84%E5%8D%B7)

本练习中，你将使用本地文件来创建用户名和密码 [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)， 然后创建运行一个容器的 Pod， 该 Pod 使用[`projected`](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#projected) 卷将 Secret 挂载到相同的路径下。

下面是 Pod 的配置文件：

[`pods/storage/projected.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/storage/projected.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/storage/projected.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-projected-volume
spec:
  containers:
  - name: test-projected-volume
    image: busybox:1.28
    args:
    - sleep
    - "86400"
    volumeMounts:
    - name: all-in-one
      mountPath: "/projected-volume"
      readOnly: true
  volumes:
  - name: all-in-one
    projected:
      sources:
      - secret:
          name: user
      - secret:
          name: pass
```

1. 创建 Secret:
    
    ```shell
    # 创建包含用户名和密码的文件:
    echo -n "admin" > ./username.txt
    echo -n "1f2d1e2e67df" > ./password.txt
    
    # 在 Secret 中引用上述文件
    kubectl create secret generic user --from-file=./username.txt
    kubectl create secret generic pass --from-file=./password.txt
    ```
    
2. 创建 Pod：
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/storage/projected.yaml
    ```
    
3. 确认 Pod 中的容器运行正常，然后监视 Pod 的变化：
    
    ```shell
    kubectl get --watch pod test-projected-volume
    ```
    
    输出结果和下面类似：
    
    ```
    NAME                    READY     STATUS    RESTARTS   AGE
    test-projected-volume   1/1       Running   0          14s
    ```
    
4. 在另外一个终端中，打开容器的 shell：
    
    ```shell
    kubectl exec -it test-projected-volume -- /bin/sh
    ```
    
5. 在 shell 中，确认 `projected-volume` 目录包含你的投射源：
    
    ```shell
    ls /projected-volume/
    ```
    

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-projected-volume-storage/#%E6%B8%85%E7%90%86)

删除 Pod 和 Secret:

```shell
kubectl delete pod test-projected-volume
kubectl delete secret user pass
```
## [为 Pod 或容器配置安全上下文](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/)
安全上下文（Security Context）定义 Pod 或 Container 的特权与访问控制设置。 安全上下文包括但不限于：

- 自主访问控制（Discretionary Access Control）： 基于[用户 ID（UID）和组 ID（GID）](https://wiki.archlinux.org/index.php/users_and_groups) 来判定对对象（例如文件）的访问权限。
    
- [安全性增强的 Linux（SELinux）](https://zh.wikipedia.org/wiki/%E5%AE%89%E5%85%A8%E5%A2%9E%E5%BC%BA%E5%BC%8FLinux)： 为对象赋予安全性标签。
    
- 以特权模式或者非特权模式运行。
    
- [Linux 权能](https://linux-audit.com/linux-capabilities-hardening-linux-binaries-by-removing-setuid/): 为进程赋予 root 用户的部分特权而非全部特权。
    

- [AppArmor](https://kubernetes.io/zh-cn/docs/tutorials/security/apparmor/)：使用程序配置来限制个别程序的权能。
    
- [Seccomp](https://kubernetes.io/zh-cn/docs/tutorials/security/seccomp/)：过滤进程的系统调用。
    
- `allowPrivilegeEscalation`：控制进程是否可以获得超出其父进程的特权。 此布尔值直接控制是否为容器进程设置 [`no_new_privs`](https://www.kernel.org/doc/Documentation/prctl/no_new_privs.txt)标志。 当容器满足一下条件之一时，`allowPrivilegeEscalation` 总是为 true：
    
    - 以特权模式运行，或者
    - 具有 `CAP_SYS_ADMIN` 权能
- `readOnlyRootFilesystem`：以只读方式加载容器的根文件系统。
    

以上条目不是安全上下文设置的完整列表 -- 请参阅 [SecurityContext](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#securitycontext-v1-core) 了解其完整列表。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 为 Pod 设置安全性上下文[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#set-the-security-context-for-a-pod)

要为 Pod 设置安全性设置，可在 Pod 规约中包含 `securityContext` 字段。`securityContext` 字段值是一个 [PodSecurityContext](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#podsecuritycontext-v1-core) 对象。你为 Pod 所设置的安全性配置会应用到 Pod 中所有 Container 上。 下面是一个 Pod 的配置文件，该 Pod 定义了 `securityContext` 和一个 `emptyDir` 卷：

[`pods/security/security-context.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/security/security-context.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/security/security-context.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  volumes:
  - name: sec-ctx-vol
    emptyDir: {}
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
```

在配置文件中，`runAsUser` 字段指定 Pod 中的所有容器内的进程都使用用户 ID 1000 来运行。`runAsGroup` 字段指定所有容器中的进程都以主组 ID 3000 来运行。 如果忽略此字段，则容器的主组 ID 将是 root（0）。 当 `runAsGroup` 被设置时，所有创建的文件也会划归用户 1000 和组 3000。 由于 `fsGroup` 被设置，容器中所有进程也会是附组 ID 2000 的一部分。 卷 `/data/demo` 及在该卷中创建的任何文件的属主都会是组 ID 2000。

创建该 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/security/security-context.yaml
```

检查 Pod 的容器处于运行状态：

```shell
kubectl get pod security-context-demo
```

开启一个 Shell 进入到运行中的容器：

```shell
kubectl exec -it security-context-demo -- sh
```

在你的 Shell 中，列举运行中的进程：

```shell
ps
```

输出显示进程以用户 1000 运行，即 `runAsUser` 所设置的值：

```none
PID   USER     TIME  COMMAND
    1 1000      0:00 sleep 1h
    6 1000      0:00 sh
...
```

在你的 Shell 中，进入 `/data` 目录列举其内容：

```shell
cd /data
ls -l
```

输出显示 `/data/demo` 目录的组 ID 为 2000，即 `fsGroup` 的设置值：

```none
drwxrwsrwx 2 root 2000 4096 Jun  6 20:08 demo
```

在你的 Shell 中，进入到 `/data/demo` 目录下创建一个文件：

```shell
cd demo
echo hello > testfile
```

列举 `/data/demo` 目录下的文件：

```shell
ls -l
```

输出显示 `testfile` 的组 ID 为 2000，也就是 `fsGroup` 所设置的值：

```none
-rw-r--r-- 1 1000 2000 6 Jun  6 20:08 testfile
```

运行下面的命令：

```shell
id
```

输出类似于：

```none
uid=1000 gid=3000 groups=2000
```

从输出中你会看到 `gid` 值为 3000，也就是 `runAsGroup` 字段的值。 如果 `runAsGroup` 被忽略，则 `gid` 会取值 0（root），而进程就能够与 root 用户组所拥有以及要求 root 用户组访问权限的文件交互。

退出你的 Shell：

```shell
exit
```

### 为 Pod 配置卷访问权限和属主变更策略[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E4%B8%BA-pod-%E9%85%8D%E7%BD%AE%E5%8D%B7%E8%AE%BF%E9%97%AE%E6%9D%83%E9%99%90%E5%92%8C%E5%B1%9E%E4%B8%BB%E5%8F%98%E6%9B%B4%E7%AD%96%E7%95%A5)

**特性状态：** `Kubernetes v1.23 [stable]`

默认情况下，Kubernetes 在挂载一个卷时，会递归地更改每个卷中的内容的属主和访问权限， 使之与 Pod 的 `securityContext` 中指定的 `fsGroup` 匹配。 对于较大的数据卷，检查和变更属主与访问权限可能会花费很长时间，降低 Pod 启动速度。 你可以在 `securityContext` 中使用 `fsGroupChangePolicy` 字段来控制 Kubernetes 检查和管理卷属主和访问权限的方式。

**fsGroupChangePolicy** - `fsGroupChangePolicy` 定义在卷被暴露给 Pod 内部之前对其 内容的属主和访问许可进行变更的行为。此字段仅适用于那些支持使用 `fsGroup` 来 控制属主与访问权限的卷类型。此字段的取值可以是：

- `OnRootMismatch`：只有根目录的属主与访问权限与卷所期望的权限不一致时， 才改变其中内容的属主和访问权限。这一设置有助于缩短更改卷的属主与访问 权限所需要的时间。
- `Always`：在挂载卷时总是更改卷中内容的属主和访问权限。

例如：

```yaml
securityContext:
  runAsUser: 1000
  runAsGroup: 3000
  fsGroup: 2000
  fsGroupChangePolicy: "OnRootMismatch"
```

**说明：** 此字段对于 [`secret`](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#secret)、 [`configMap`](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#configmap) 和 [`emptydir`](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#emptydir) 这类临时性存储无效。

### 将卷权限和所有权更改委派给 CSI 驱动程序[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E5%B0%86%E5%8D%B7%E6%9D%83%E9%99%90%E5%92%8C%E6%89%80%E6%9C%89%E6%9D%83%E6%9B%B4%E6%94%B9%E5%A7%94%E6%B4%BE%E7%BB%99-csi-%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F)

**特性状态：** `Kubernetes v1.26 [stable]`

如果你部署了一个[容器存储接口 (CSI)](https://github.com/container-storage-interface/spec/blob/master/spec.md) 驱动，而该驱动支持 `VOLUME_MOUNT_GROUP` `NodeServiceCapability`， 在 `securityContext` 中指定 `fsGroup` 来设置文件所有权和权限的过程将由 CSI 驱动而不是 Kubernetes 来执行。在这种情况下，由于 Kubernetes 不执行任何所有权和权限更改， `fsGroupChangePolicy` 不会生效，并且按照 CSI 的规定，CSI 驱动应该使用所指定的 `fsGroup` 来挂载卷，从而生成了一个对 `fsGroup` 可读/可写的卷.

### 为 Container 设置安全性上下文[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#set-the-security-context-for-a-container)

若要为 Container 设置安全性配置，可以在 Container 清单中包含 `securityContext` 字段。`securityContext` 字段的取值是一个 [SecurityContext](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#securitycontext-v1-core) 对象。你为 Container 设置的安全性配置仅适用于该容器本身，并且所指定的设置在与 Pod 层面设置的内容发生重叠时，会重写 Pod 层面的设置。Container 层面的设置不会影响到 Pod 的卷。

下面是一个 Pod 的配置文件，其中包含一个 Container。Pod 和 Container 都有 `securityContext` 字段：

[`pods/security/security-context-2.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/security/security-context-2.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/security/security-context-2.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-2
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: sec-ctx-demo-2
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 2000
      allowPrivilegeEscalation: false
```

创建该 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/security/security-context-2.yaml
```

验证 Pod 中的容器处于运行状态：

```shell
kubectl get pod security-context-demo-2
```

启动一个 Shell 进入到运行中的容器内：

```shell
kubectl exec -it security-context-demo-2 -- sh
```

在你的 Shell 中，列举运行中的进程：

```shell
ps aux
```

输出显示进程以用户 2000 运行。该值是在 Container 的 `runAsUser` 中设置的。 该设置值重写了 Pod 层面所设置的值 1000。

```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
2000         1  0.0  0.0   4336   764 ?        Ss   20:36   0:00 /bin/sh -c node server.js
2000         8  0.1  0.5 772124 22604 ?        Sl   20:36   0:00 node server.js
...
```

退出你的 Shell：

```shell
exit
```

### 为 Container 设置权能[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#set-capabilities-for-a-container)

使用 [Linux 权能](https://man7.org/linux/man-pages/man7/capabilities.7.html)， 你可以赋予进程 root 用户所拥有的某些特权，但不必赋予其全部特权。 要为 Container 添加或移除 Linux 权能，可以在 Container 清单的 `securityContext` 节包含 `capabilities` 字段。

首先，看一下不包含 `capabilities` 字段时候会发生什么。 下面是一个配置文件，其中没有添加或移除容器的权能：

[`pods/security/security-context-3.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/security/security-context-3.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/security/security-context-3.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-3
spec:
  containers:
  - name: sec-ctx-3
    image: gcr.io/google-samples/node-hello:1.0
```

创建该 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/security/security-context-3.yaml
```

验证 Pod 的容器处于运行状态：

```shell
kubectl get pod security-context-demo-3
```

启动一个 Shell 进入到运行中的容器：

```shell
kubectl exec -it security-context-demo-3 -- sh
```

在你的 Shell 中，列举运行中的进程：

```shell
ps aux
```

输出显示容器中进程 ID（PIDs）：

```
USER  PID %CPU %MEM    VSZ   RSS TTY   STAT START   TIME COMMAND
root    1  0.0  0.0   4336   796 ?     Ss   18:17   0:00 /bin/sh -c node server.js
root    5  0.1  0.5 772124 22700 ?     Sl   18:17   0:00 node server.js
```

在你的 Shell 中，查看进程 1 的状态：

```shell
cd /proc/1
cat status
```

输出显示进程的权能位图：

```
...
CapPrm:	00000000a80425fb
CapEff:	00000000a80425fb
...
```

记下进程权能位图，之后退出你的 Shell：

```shell
exit
```

接下来运行一个与前例中容器相同的容器，只是这个容器有一些额外的权能设置。

下面是一个 Pod 的配置，其中运行一个容器。配置为容器添加 `CAP_NET_ADMIN` 和 `CAP_SYS_TIME` 权能：

[`pods/security/security-context-4.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/security/security-context-4.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/security/security-context-4.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-4
spec:
  containers:
  - name: sec-ctx-4
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/security/security-context-4.yaml
```

启动一个 Shell，进入到运行中的容器：

```shell
kubectl exec -it security-context-demo-4 -- sh
```

在你的 Shell 中，查看进程 1 的权能：

```shell
cd /proc/1
cat status
```

输出显示的是进程的权能位图：

```
...
CapPrm:	00000000aa0435fb
CapEff:	00000000aa0435fb
...
```

比较两个容器的权能位图：

```
00000000a80425fb
00000000aa0435fb
```

在第一个容器的权能位图中，位 12 和 25 是没有设置的。在第二个容器中，位 12 和 25 是设置了的。位 12 是 `CAP_NET_ADMIN` 而位 25 则是 `CAP_SYS_TIME`。 参见 [capability.h](https://github.com/torvalds/linux/blob/master/include/uapi/linux/capability.h) 了解权能常数的定义。

**说明：** Linux 权能常数定义的形式为 `CAP_XXX`。但是你在 container 清单中列举权能时， 要将权能名称中的 `CAP_` 部分去掉。例如，要添加 `CAP_SYS_TIME`， 可在权能列表中添加 `SYS_TIME`。

### 为容器设置 Seccomp 配置[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E4%B8%BA%E5%AE%B9%E5%99%A8%E8%AE%BE%E7%BD%AE-seccomp-%E9%85%8D%E7%BD%AE)

若要为容器设置 Seccomp 配置（Profile），可在你的 Pod 或 Container 清单的 `securityContext` 节中包含 `seccompProfile` 字段。该字段是一个 [SeccompProfile](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#seccompprofile-v1-core) 对象，包含 `type` 和 `localhostProfile` 属性。 `type` 的合法选项包括 `RuntimeDefault`、`Unconfined` 和 `Localhost`。 `localhostProfile` 只能在 `type: Localhost` 配置下才可以设置。 该字段标明节点上预先设定的配置的路径，路径是相对于 kubelet 所配置的 Seccomp 配置路径（使用 `--root-dir` 设置）而言的。

下面是一个例子，设置容器使用节点上容器运行时的默认配置作为 Seccomp 配置：

```yaml
...
securityContext:
  seccompProfile:
    type: RuntimeDefault
```

下面是另一个例子，将 Seccomp 的样板设置为位于 `<kubelet-根目录>/seccomp/my-profiles/profile-allow.json` 的一个预先配置的文件。

```yaml
...
securityContext:
  seccompProfile:
    type: Localhost
    localhostProfile: my-profiles/profile-allow.json
```

### 为 Container 赋予 SELinux 标签[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E4%B8%BA-container-%E8%B5%8B%E4%BA%88-selinux-%E6%A0%87%E7%AD%BE)

若要给 Container 设置 SELinux 标签，可以在 Pod 或 Container 清单的 `securityContext` 节包含 `seLinuxOptions` 字段。 `seLinuxOptions` 字段的取值是一个 [SELinuxOptions](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#selinuxoptions-v1-core) 对象。下面是一个应用 SELinux 标签的例子：

```yaml
...
securityContext:
  seLinuxOptions:
    level: "s0:c123,c456"
```

**说明：** 要指定 SELinux，需要在宿主操作系统中装载 SELinux 安全性模块。

#### 高效重打 SELinux 卷标签[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E9%AB%98%E6%95%88%E9%87%8D%E6%89%93-selinux-%E5%8D%B7%E6%A0%87%E7%AD%BE)

**特性状态：** `Kubernetes v1.25 [alpha]`

默认情况下，容器运行时递归地将 SELinux 标签赋予所有 Pod 卷上的所有文件。 为了加快该过程，Kubernetes 使用挂载可选项 `-o context=<label>` 可以立即改变卷的 SELinux 标签。

要使用这项加速功能，必须满足下列条件：

- 必须启用 Alpha 特性门控 `ReadWriteOncePod` 和 `SELinuxMountReadWriteOncePod`。

- Pod 必须以 `accessModes: ["ReadWriteOncePod"]` 模式使用 PersistentVolumeClaim。

- Pod（或其中使用 PersistentVolumeClaim 的所有容器）必须设置 `seLinuxOptions`。

- 对应的 PersistentVolume 必须是使用 {< glossary\_tooltip text="CSI" term\_id="csi" >}} 驱动程序的卷，或者是传统的 `iscsi` 卷类型的卷。
    - 如果使用基于 CSI 驱动程序的卷，CSI 驱动程序必须能够通过在 CSIDriver 实例中设置 `spec.seLinuxMount: true` 以支持 `-o context` 挂载。

对于所有其他卷类型，重打 SELinux 标签的方式有所不同： 容器运行时为卷中的所有节点（文件和目录）递归地修改 SELinux 标签。 卷中的文件和目录越多，重打标签需要耗费的时间就越长。

**说明：**

在 Kubernetes 1.25 中，kubelet 在重启后会丢失对卷标签的追踪记录。 换言之，kubelet 可能会拒绝启动 Pod，原因类似于 “conflicting SELinux labels of volume”， 但实际上 Pod 中并没有冲突的标签。在重启 kubelet 之前确保节点已被[完全腾空](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/safely-drain-node/)。

### 讨论[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#discussion)

Pod 的安全上下文适用于 Pod 中的容器，也适用于 Pod 所挂载的卷（如果有的话）。 尤其是，`fsGroup` 和 `seLinuxOptions` 按下面的方式应用到挂载卷上：

- `fsGroup`：支持属主管理的卷会被修改，将其属主变更为 `fsGroup` 所指定的 GID， 并且对该 GID 可写。进一步的细节可参阅 [属主变更设计文档](https://git.k8s.io/design-proposals-archive/storage/volume-ownership-management.md)。
    
- `seLinuxOptions`：支持 SELinux 标签的卷会被重新打标签，以便可被 `seLinuxOptions` 下所设置的标签访问。通常你只需要设置 `level` 部分。 该部分设置的是赋予 Pod 中所有容器及卷的 [多类别安全性（Multi-Category Security，MCS)](https://selinuxproject.org/page/NB_MLS)标签。
    

**警告：** 在为 Pod 设置 MCS 标签之后，所有带有相同标签的 Pod 可以访问该卷。 如果你需要跨 Pod 的保护，你必须为每个 Pod 赋予独特的 MCS 标签。

### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E6%B8%85%E7%90%86)

删除之前创建的所有 Pod：

```shell
kubectl delete pod security-context-demo
kubectl delete pod security-context-demo-2
kubectl delete pod security-context-demo-3
kubectl delete pod security-context-demo-4
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/security-context/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [PodSecurityContext](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#podsecuritycontext-v1-core) API 定义
- [SecurityContext](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#securitycontext-v1-core) API 定义
- [使用最新的安全性增强来调优 Docker（英文）](https://github.com/containerd/containerd/blob/main/docs/cri/config.md)
- [安全上下文的设计文档（英文）](https://github.com/kubernetes/design-proposals-archive/blob/main/auth/security_context.md)
- [属主管理的设计文档（英文）](https://github.com/kubernetes/design-proposals-archive/blob/main/storage/volume-ownership-management.md)
- [Pod 安全性准入](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/)
- [AllowPrivilegeEscalation 的设计文档（英文）](https://github.com/kubernetes/design-proposals-archive/blob/main/auth/no-new-privs.md)
- 关于在 Linux 系统中的安全机制的更多信息，可参阅 [Linux 内核安全性能力概述](https://www.linux.com/learn/overview-linux-kernel-security-features)（注意：部分信息已过时）。
## [为 Pod 配置服务账号](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/)
Kubernetes 提供两种完全不同的方式来为客户端提供支持，这些客户端可能运行在你的集群中， 也可能与你的集群的[控制面](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-control-plane)相关， 需要向 [API 服务器](https://kubernetes.io/zh-cn/docs/concepts/overview/components/#kube-apiserver)完成身份认证。

**服务账号（Service Account）**为 Pod 中运行的进程提供身份标识， 并映射到 ServiceAccount 对象。当你向 API 服务器执行身份认证时， 你会将自己标识为某个**用户（User）**。Kubernetes 能够识别用户的概念， 但是 Kubernetes 自身**并不**提供 User API。

本服务是关于 ServiceAccount 的，而 ServiceAccount 则确实存在于 Kubernetes 的 API 中。 本指南为你展示为 Pod 配置 ServiceAccount 的一些方法。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 使用默认的服务账号访问 API 服务器[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#use-the-default-service-account-to-access-the-api-server)

当 Pod 与 API 服务器联系时，Pod 会被认证为某个特定的 ServiceAccount（例如：`default`）。 在每个[名字空间](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/namespaces/)中，至少存在一个 ServiceAccount。

每个 Kubernetes 名字空间至少包含一个 ServiceAccount：也就是该名字空间的默认服务账号， 名为 `default`。如果你在创建 Pod 时没有指定 ServiceAccount，Kubernetes 会自动将该名字空间中 名为 `default` 的 ServiceAccount 分配给该 Pod。

你可以检视你刚刚创建的 Pod 的细节。例如：

```shell
kubectl get pods/<podname> -o yaml
```

在输出中，你可以看到字段 `spec.serviceAccountName`。当你在创建 Pod 时未设置该字段时， Kubernetes [自动](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/object-management/)为 Pod 设置这一属性的取值。

Pod 中运行的应用可以使用这一自动挂载的服务账号凭据来访问 Kubernetes API。 参阅[访问集群](https://kubernetes.io/zh-cn/docs/tasks/access-application-cluster/access-cluster/)以进一步了解。

当 Pod 被身份认证为某个 ServiceAccount 时， 其访问能力取决于所使用的[鉴权插件和策略](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/authorization/#authorization-modules)。

#### 放弃 API 凭据的自动挂载[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#opt-out-of-api-credential-automounting)

如果你不希望 [kubelet](https://kubernetes.io/docs/reference/generated/kubelet) 自动挂载某 ServiceAccount 的 API 访问凭据，你可以选择不采用这一默认行为。 通过在 ServiceAccount 对象上设置 `automountServiceAccountToken: false`，可以放弃在 `/var/run/secrets/kubernetes.io/serviceaccount/token` 处自动挂载该服务账号的 API 凭据。

例如：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: build-robot
automountServiceAccountToken: false
...
```

你也可以选择不给特定 Pod 自动挂载 API 凭据：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: build-robot
  automountServiceAccountToken: false
  ...
```

如果 ServiceAccount 和 Pod 的 `.spec` 都设置了 `automountServiceAccountToken` 值， 则 Pod 上 spec 的设置优先于服务账号的设置。

### 使用多个服务账号[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#use-multiple-service-accounts)

每个名字空间都至少有一个 ServiceAccount：名为 `default` 的默认 ServiceAccount 资源。 你可以用下面的命令列举你[当前名字空间](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/namespaces/#setting-the-namespace-preference) 中的所有 ServiceAccount 资源：

```shell
kubectl get serviceaccounts
```

输出类似于：

```
NAME      SECRETS    AGE
default   1          1d
```

你可以像这样来创建额外的 ServiceAccount 对象：

```shell
kubectl apply -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: build-robot
EOF
```

ServiceAccount 对象的名字必须是一个有效的 [DNS 子域名](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#dns-subdomain-names).

如果你查询服务账号对象的完整信息，如下所示：

```shell
kubectl get serviceaccounts/build-robot -o yaml
```

输出类似于：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2019-06-16T00:12:34Z
  name: build-robot
  namespace: default
  resourceVersion: "272500"
  uid: 721ab723-13bc-11e5-aec2-42010af0021e
```

你可以使用鉴权插件来[设置服务账号的访问许可](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/rbac/#service-account-permissions)。

要使用非默认的服务账号，将 Pod 的 `spec.serviceAccountName` 字段设置为你想用的服务账号名称。

只能在创建 Pod 时或者为新 Pod 指定模板时，你才可以设置 `serviceAccountName`。 你不能更新已经存在的 Pod 的 `.spec.serviceAccountName` 字段。

**说明：**

`.spec.serviceAccount` 字段是 `.spec.serviceAccountName` 的已弃用别名。 如果要从工作负载资源中删除这些字段，请在 [Pod 模板](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods#pod-templates)上将这两个字段显式设置为空。

#### 清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#cleanup-use-multiple-service-accounts)

如果你尝试了创建前文示例中所给的 `build-robot` ServiceAccount， 你可以通过运行下面的命令来完成清理操作：

```shell
kubectl delete serviceaccount/build-robot
```

### 手动为 ServiceAccount 创建 API 令牌[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#manually-create-an-api-token-for-a-serviceaccount)

假设你已经有了一个前文所提到的名为 "build-robot" 的服务账号。 你可以使用 `kubectl` 为该 ServiceAccount 获得一个时间上受限的 API 令牌：

```shell
kubectl create token build-robot
```

这一命令的输出是一个令牌，你可以使用该令牌来将身份认证为对应的 ServiceAccount。 你可以使用 `kubectl create token` 命令的 `--duration` 参数来请求特定的令牌有效期 （实际签发的令牌的有效期可能会稍短一些，也可能会稍长一些）。

**说明：**

Kubernetes 在 v1.22 版本之前自动创建用来访问 Kubernetes API 的长期凭据。 这一较老的机制是基于创建令牌 Secret 对象来实现的，Secret 对象可被挂载到运行中的 Pod 内。 在最近的版本中，包括 Kubernetes v1.26，API 凭据可以直接使用 [TokenRequest](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/authentication-resources/token-request-v1/) API 来获得，并使用一个[投射卷](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume)挂载到 Pod 中。使用此方法获得的令牌具有受限的生命期长度，并且能够在挂载它们的 Pod 被删除时自动被废弃。

你仍然可以通过手动方式来创建服务账号令牌 Secret 对象，例如你需要一个永远不过期的令牌时。 不过，使用 [TokenRequest](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/authentication-resources/token-request-v1/) 子资源来获得访问 API 的令牌的做法仍然是推荐的方式。

#### 手动为 ServiceAccount 创建长期有效的 API 令牌 {#manually-create-a-long-lived-api-token-for-a-serviceaccount}」[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#%E6%89%8B%E5%8A%A8%E4%B8%BA-serviceaccount-%E5%88%9B%E5%BB%BA%E9%95%BF%E6%9C%9F%E6%9C%89%E6%95%88%E7%9A%84-api-%E4%BB%A4%E7%89%8C-manually-create-a-long-lived-api-token-for-a-serviceaccount)

如果你需要为 ServiceAccount 获得一个 API 令牌，你可以创建一个新的、带有特殊注解 `kubernetes.io/service-account.name` 的 Secret 对象。

```shell
kubectl apply -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: build-robot-secret
  annotations:
    kubernetes.io/service-account.name: build-robot
type: kubernetes.io/service-account-token
EOF
```

如果你通过下面的命令来查看 Secret：

```shell
kubectl get secret/build-robot-secret -o yaml
```

你可以看到 Secret 中现在包含针对 "build-robot" ServiceAccount 的 API 令牌。

鉴于你所设置的注解，控制面会自动为该 ServiceAccount 生成一个令牌，并将其保存到相关的 Secret 中。控制面也会为已删除的 ServiceAccount 执行令牌清理操作。

```shell
kubectl describe secrets/build-robot-secret
```

输出类似于这样：

```
Name:           build-robot-secret
Namespace:      default
Labels:         <none>
Annotations:    kubernetes.io/service-account.name: build-robot
                kubernetes.io/service-account.uid: da68f9c6-9d26-11e7-b84e-002dc52800da

Type:   kubernetes.io/service-account-token

Data
====
ca.crt:         1338 bytes
namespace:      7 bytes
token:          ...
```

**说明：**

这里将 `token` 的内容抹去了。

注意在你的终端或者计算机屏幕可能被旁观者看到的场合，不要显示 `kubernetes.io/service-account-token` 的内容。

当你删除一个与某 Secret 相关联的 ServiceAccount 时，Kubernetes 的控制面会自动清理该 Secret 中长期有效的令牌。

### 为服务账号添加 ImagePullSecrets[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)

首先，[生成一个 imagePullSecret](https://kubernetes.io/zh-cn/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)； 接下来，验证该 Secret 已被创建。例如：

- 按[为 Pod 设置 imagePullSecret](https://kubernetes.io/zh-cn/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) 所描述的，生成一个镜像拉取 Secret：
    
    ```shell
    kubectl create secret docker-registry myregistrykey --docker-server=DUMMY_SERVER \
            --docker-username=DUMMY_USERNAME --docker-password=DUMMY_DOCKER_PASSWORD \
            --docker-email=DUMMY_DOCKER_EMAIL
    ```
    

- 检查该 Secret 已经被创建。
    
    ```shell
    kubectl get secrets myregistrykey
    ```
    
    输出类似于这样：
    
    ```
    NAME             TYPE                              DATA    AGE
    myregistrykey    kubernetes.io/.dockerconfigjson   1       1d
    ```
    

#### 将镜像拉取 Secret 添加到服务账号[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#add-image-pull-secret-to-service-account)

接下来更改名字空间的默认服务账号，将该 Secret 用作 imagePullSecret。

```shell
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "myregistrykey"}]}'
```

你也可以通过手动编辑该对象来实现同样的效果：

```shell
kubectl edit serviceaccount/default
```

`sa.yaml` 文件的输出类似于：

你所选择的文本编辑器会被打开，展示如下所示的配置：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2021-07-07T22:02:39Z
  name: default
  namespace: default
  resourceVersion: "243024"
  uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
```

使用你的编辑器，删掉包含 `resourceVersion` 主键的行，添加包含 `imagePullSecrets:` 的行并保存文件。对于 `uid` 而言，保持其取值与你读到的值一样。

当你完成这些变更之后，所编辑的 ServiceAccount 看起来像是这样：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2021-07-07T22:02:39Z
  name: default
  namespace: default
  uid: 052fb0f4-3d50-11e5-b066-42010af0d7b6
imagePullSecrets:
  - name: myregistrykey
```

#### 检查 imagePullSecrets 已经被设置到新 Pod 上[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#verify-that-imagepullsecrets-are-set-for-new-pods)

现在，在当前名字空间中创建新 Pod 并使用默认 ServiceAccount 时， 新 Pod 的 `spec.imagePullSecrets` 会被自动设置。

```shell
kubectl run nginx --image=nginx --restart=Never
kubectl get pod nginx -o=jsonpath='{.spec.imagePullSecrets[0].name}{"\n"}'
```

输出为：

```
myregistrykey
```

### 服务账号令牌卷投射[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)

**特性状态：** `Kubernetes v1.20 [stable]`

**说明：**

为了启用令牌请求投射，你必须为 `kube-apiserver` 设置以下命令行参数：

`--service-account-issuer`

定义服务账号令牌发放者的身份标识（Identifier）。你可以多次指定 `--service-account-issuer` 参数，对于需要变更发放者而又不想带来业务中断的场景， 这样做是有用的。如果这个参数被多次指定，其第一个参数值会被用来生成令牌， 而所有参数值都会被用来确定哪些发放者是可接受的。你所运行的 Kubernetes 集群必须是 v1.22 或更高版本才能多次指定 `--service-account-issuer`。

`--service-account-key-file`

给出某文件的路径，其中包含 PEM 编码的 x509 RSA 或 ECDSA 私钥或公钥，用来检查 ServiceAccount 的令牌。所指定的文件中可以包含多个秘钥，并且你可以多次使用此参数，每个参数值为不同的文件。 多次使用此参数时，由所给的秘钥之一签名的令牌会被 Kubernetes API 服务器认为是合法令牌。

`--service-account-signing-key-file`

指向某文件的路径，其中包含当前服务账号令牌发放者的私钥。 此发放者使用此私钥来签署所发放的 ID 令牌。

`--api-audiences` (可以省略)

为 ServiceAccount 令牌定义其受众（Audiences）。 服务账号令牌身份检查组件会检查针对 API 访问所使用的令牌， 确认令牌至少是被绑定到这里所给的受众之一。 如果 `api-audiences` 被多次指定，则针对所给的多个受众中任何目标的令牌都会被 Kubernetes API 服务器当做合法的令牌。如果你指定了 `--service-account-issuer` 参数，但沒有設置 `--api-audiences`，则控制面认为此参数的默认值为一个只有一个元素的列表， 且该元素为令牌发放者的 URL。

kubelet 还可以将 ServiceAccount 令牌投射到 Pod 中。你可以指定令牌的期望属性， 例如受众和有效期限。这些属性在 default ServiceAccount 令牌上**无法**配置。 当 Pod 或 ServiceAccount 被删除时，该令牌也将对 API 无效。

你可以使用类型为 `ServiceAccountToken` 的[投射卷](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#projected) 来为 Pod 的 `spec` 配置此行为。

#### 启动使用服务账号令牌投射的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#launch-a-pod-using-service-account-token-projection)

要为某 Pod 提供一个受众为 `vault` 并且有效期限为 2 小时的令牌，你可以定义一个与下面类似的 Pod 清单：

[`pods/pod-projected-svc-token.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-projected-svc-token.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-projected-svc-token.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /var/run/secrets/tokens
      name: vault-token
  serviceAccountName: build-robot
  volumes:
  - name: vault-token
    projected:
      sources:
      - serviceAccountToken:
          path: vault-token
          expirationSeconds: 7200
          audience: vault
```

创建此 Pod：

```shell
kubectl create -f https://k8s.io/examples/pods/pod-projected-svc-token.yaml
```

kubelet 组件会替 Pod 请求令牌并将其保存起来；通过将令牌存储到一个可配置的路径以使之在 Pod 内可用；在令牌快要到期的时候刷新它。kubelet 会在令牌存在期达到其 TTL 的 80% 的时候或者令牌生命期超过 24 小时的时候主动请求将其轮换掉。

应用负责在令牌被轮换时重新加载其内容。通常而言，周期性地（例如，每隔 5 分钟） 重新加载就足够了，不必跟踪令牌的实际过期时间。

### 发现服务账号分发者[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#%E5%8F%91%E7%8E%B0%E6%9C%8D%E5%8A%A1%E8%B4%A6%E5%8F%B7%E5%88%86%E5%8F%91%E8%80%85)

**特性状态：** `Kubernetes v1.21 [stable]`

如果你在你的集群中已经为 ServiceAccount 启用了[令牌投射](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#serviceaccount-token-volume-projection)， 那么你也可以利用其发现能力。Kubernetes 提供一种方式来让客户端将一个或多个外部系统进行联邦， 作为**标识提供者（Identity Provider）**，而这些外部系统的角色是**依赖方（Relying Party）**。

**说明：**

分发者的 URL 必须遵从 [OIDC 发现规范](https://openid.net/specs/openid-connect-discovery-1_0.html)。 实现上，这意味着 URL 必须使用 `https` 模式，并且必须在路径 `{service-account-issuer}/.well-known/openid-configuration` 处给出 OpenID 提供者（Provider）的配置信息。

如果 URL 没有遵从这一规范，ServiceAccount 分发者发现末端末端就不会被注册也无法访问。

当此特性被启用时，Kubernetes API 服务器会通过 HTTP 发布一个 OpenID 提供者配置文档。 该配置文档发布在 `/.well-known/openid-configuration` 路径。 这里的 OpenID 提供者配置（OpenID Provider Configuration）有时候也被称作 “发现文档（Discovery Document）”。 Kubernetes API 服务器也通过 HTTP 在 `/openid/v1/jwks` 处发布相关的 JSON Web Key Set（JWKS）。

**说明：**

对于在 `/.well-known/openid-configuration` 和 `/openid/v1/jwks` 上给出的响应而言， 其设计上是保证与 OIDC 兼容的，但并不严格遵从 OIDC 的规范。 响应中所包含的文档进包含对 Kubernetes 服务账号令牌进行校验所必需的参数。

使用 [RBAC](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/rbac/) 的集群都包含一个的默认 RBAC ClusterRole, 名为 `system:service-account-issuer-discovery`。 默认的 RBAC ClusterRoleBinding 将此角色分配给 `system:serviceaccounts` 组， 所有 ServiceAccount 隐式属于该组。这使得集群上运行的 Pod 能够通过它们所挂载的服务账号令牌访问服务账号发现文档。 此外，管理员可以根据其安全性需要以及期望集成的外部系统，选择是否将该角色绑定到 `system:authenticated` 或 `system:unauthenticated`。

JWKS 响应包含依赖方可以用来验证 Kubernetes 服务账号令牌的公钥数据。 依赖方先会查询 OpenID 提供者配置，之后使用返回响应中的 `jwks_uri` 来查找 JWKS。

在很多场合，Kubernetes API 服务器都不会暴露在公网上，不过对于缓存并向外提供 API 服务器响应数据的公开末端而言，用户或者服务提供商可以选择将其暴露在公网上。 在这种环境中，可能会重载 OpenID 提供者配置中的 `jwks_uri`，使之指向公网上可用的末端地址，而不是 API 服务器的地址。 这时需要向 API 服务器传递 `--service-account-jwks-uri` 参数。 与分发者 URL 类似，此 JWKS URI 也需要使用 `https` 模式。
## [从私有仓库拉取镜像](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/)
本文介绍如何使用 [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/) 从私有的镜像仓库或代码仓库拉取镜像来创建 Pod。 有很多私有镜像仓库正在使用中。这个任务使用的镜像仓库是 [Docker Hub](https://www.docker.com/products/docker-hub)。

🛇 本条目指向第三方项目或产品，而该项目（产品）不是 Kubernetes 的一部分。[更多信息](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#third-party-content-disclaimer)

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

- 你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：
    
    - [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
    - [玩转 Kubernetes](http://labs.play-with-k8s.com/)

- 要进行此练习，你需要 `docker` 命令行工具和一个知道密码的 [Docker ID](https://docs.docker.com/docker-id/)。
- 如果你要使用不同的私有的镜像仓库，你需要有对应镜像仓库的命令行工具和登录信息。

### 登录 Docker 镜像仓库[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#log-in-to-docker-hub)

在个人电脑上，要想拉取私有镜像必须在镜像仓库上进行身份验证。

使用 `docker` 命令工具来登录到 Docker Hub。 更多详细信息，请查阅 [Docker ID accounts](https://docs.docker.com/docker-id/#log-in) 中的 **log in** 部分。

```shell
docker login
```

当出现提示时，输入你的 Docker ID 和登录凭证（访问令牌、 或 Docker ID 的密码）。

登录过程会创建或更新保存有授权令牌的 `config.json` 文件。 查看 [Kubernetes 中如何解析这个文件](https://kubernetes.io/zh-cn/docs/concepts/containers/images#config-json)。

查看 `config.json` 文件：

```shell
cat ~/.docker/config.json
```

输出结果包含类似于以下内容的部分：

```json
{
    "auths": {
        "https://index.docker.io/v1/": {
            "auth": "c3R...zE2"
        }
    }
}
```

**说明：**

如果使用 Docker 凭证仓库，则不会看到 `auth` 条目，看到的将是以仓库名称作为值的 `credsStore` 条目。 在这种情况下，你可以直接创建一个 Secret。请参阅[在命令行上提供凭证来创建 Secret](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)。

### 创建一个基于现有凭证的 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#registry-secret-existing-credentials)

Kubernetes 集群使用 `kubernetes.io/dockerconfigjson` 类型的 Secret 来通过镜像仓库的身份验证，进而提取私有镜像。

如果你已经运行了 `docker login` 命令，你可以复制该镜像仓库的凭证到 Kubernetes:

```shell
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=<path/to/.docker/config.json> \
    --type=kubernetes.io/dockerconfigjson
```

如果你需要更多的设置（例如，为新 Secret 设置名字空间或标签）， 则可以在存储 Secret 之前对它进行自定义。 请务必：

- 将 data 项中的名称设置为 `.dockerconfigjson`
- 使用 base64 编码方法对 Docker 配置文件进行编码，然后粘贴该字符串的内容，作为字段 `data[".dockerconfigjson"]` 的值
- 将 `type` 设置为 `kubernetes.io/dockerconfigjson`

示例：

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: myregistrykey
  namespace: awesomeapps
data:
  .dockerconfigjson: UmVhbGx5IHJlYWxseSByZWVlZWVlZWVlZWFhYWFhYWFhYWFhYWFhYWFhYWFhYWFhYWFhYWxsbGxsbGxsbGxsbGxsbGxsbGxsbGxsbGxsbGxsbGx5eXl5eXl5eXl5eXl5eXl5eXl5eSBsbGxsbGxsbGxsbGxsbG9vb29vb29vb29vb29vb29vb29vb29vb29vb25ubm5ubm5ubm5ubm5ubm5ubm5ubm5ubmdnZ2dnZ2dnZ2dnZ2dnZ2dnZ2cgYXV0aCBrZXlzCg==
type: kubernetes.io/dockerconfigjson
```

如果你收到错误消息：`error: no objects passed to create`， 这可能意味着 base64 编码的字符串是无效的。如果你收到类似 `Secret "myregistrykey" is invalid: data[.dockerconfigjson]: invalid value ...` 的错误消息，则表示数据中的 base64 编码字符串已成功解码， 但无法解析为 `.docker/config.json` 文件。

### 在命令行上提供凭证来创建 Secret[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)

创建 Secret，命名为 `regcred`：

```shell
kubectl create secret docker-registry regcred \
  --docker-server=<你的镜像仓库服务器> \
  --docker-username=<你的用户名> \
  --docker-password=<你的密码> \
  --docker-email=<你的邮箱地址>
```

在这里：

- `<your-registry-server>` 是你的私有 Docker 仓库全限定域名（FQDN）。 DockerHub 使用 `https://index.docker.io/v1/`。
- `<your-name>` 是你的 Docker 用户名。
- `<your-pword>` 是你的 Docker 密码。
- `<your-email>` 是你的 Docker 邮箱。

这样你就成功地将集群中的 Docker 凭证设置为名为 `regcred` 的 Secret。

**说明：** 在命令行上键入 Secret 可能会将它们存储在你的 shell 历史记录中而不受保护， 并且这些 Secret 信息也可能在 `kubectl` 运行期间对你 PC 上的其他用户可见。

### 检查 Secret `regcred`[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#inspecting-the-secret-regcred)

要了解你创建的 `regcred` Secret 的内容，可以用 YAML 格式进行查看：

```shell
kubectl get secret regcred --output=yaml
```

输出和下面类似：

```yaml
apiVersion: v1
data:
  .dockerconfigjson: eyJodHRwczovL2luZGV4L ... J0QUl6RTIifX0=
kind: Secret
metadata:
  ...
  name: regcred
  ...
data:
  .dockerconfigjson: eyJodHRwczovL2luZGV4L ... J0QUl6RTIifX0=
type: kubernetes.io/dockerconfigjson
```

`.dockerconfigjson` 字段的值是 Docker 凭证的 base64 表示。

要了解 `dockerconfigjson` 字段中的内容，请将 Secret 数据转换为可读格式：

```shell
kubectl get secret regcred --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode
```

输出和下面类似：

```json
{"auths":{"your.private.registry.example.com":{"username":"janedoe","password":"xxxxxxxxxxx","email":"jdoe@example.com","auth":"c3R...zE2"}}}
```

要了解 `auth` 字段中的内容，请将 base64 编码过的数据转换为可读格式：

```shell
echo "c3R...zE2" | base64 --decode
```

输出结果中，用户名和密码用 `:` 链接，类似下面这样：

```none
janedoe:xxxxxxxxxxx
```

注意，Secret 数据包含与本地 `~/.docker/config.json` 文件类似的授权令牌。

这样你就已经成功地将 Docker 凭证设置为集群中的名为 `regcred` 的 Secret。

### 创建一个使用你的 Secret 的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-pod-that-uses-your-secret)

下面是一个 Pod 配置清单示例，该示例中 Pod 需要访问你的 Docker 凭证 `regcred`：

[`pods/private-reg-pod.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/private-reg-pod.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/private-reg-pod.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: private-reg
spec:
  containers:
  - name: private-reg-container
    image: <your-private-image>
  imagePullSecrets:
  - name: regcred

```

将上述文件下载到你的计算机中：

```shell
curl -L -o my-private-reg-pod.yaml https://k8s.io/examples/pods/private-reg-pod.yaml
```

在 `my-private-reg-pod.yaml` 文件中，使用私有仓库的镜像路径替换 `<your-private-image>`，例如：

```none
your.private.registry.example.com/janedoe/jdoe-private:v1
```

要从私有仓库拉取镜像，Kubernetes 需要凭证。 配置文件中的 `imagePullSecrets` 字段表明 Kubernetes 应该通过名为 `regcred` 的 Secret 获取凭证。

创建使用了你的 Secret 的 Pod，并检查它是否正常运行：

```shell
kubectl apply -f my-private-reg-pod.yaml
kubectl get pod private-reg
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 进一步了解 [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)
    - 或阅读 [Secret](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/config-and-storage-resources/secret-v1/) 的 API 参考
- 进一步了解[使用私有仓库](https://kubernetes.io/zh-cn/docs/concepts/containers/images/#using-a-private-registry)
- 进一步了解[为服务账户添加拉取镜像凭证](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)
- 查看 [kubectl 创建 docker-registry 凭证](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#-em-secret-docker-registry-em-)
- 查看 Pod [容器定义](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/workload-resources/pod-v1/#containers)中的 `imagePullSecrets` 字段。
## [配置存活、就绪和启动探针](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
这篇文章介绍如何给容器配置存活（Liveness）、就绪（Readiness）和启动（Startup）探针。

[kubelet](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/kubelet/) 使用存活探针来确定什么时候要重启容器。 例如，存活探针可以探测到应用死锁（应用程序在运行，但是无法继续执行后面的步骤）情况。 重启这种状态下的容器有助于提高应用的可用性，即使其中存在缺陷。

kubelet 使用就绪探针可以知道容器何时准备好接受请求流量，当一个 Pod 内的所有容器都就绪时，才能认为该 Pod 就绪。 这种信号的一个用途就是控制哪个 Pod 作为 Service 的后端。 若 Pod 尚未就绪，会被从 Service 的负载均衡器中剔除。

kubelet 使用启动探针来了解应用容器何时启动。 如果配置了这类探针，你就可以控制容器在启动成功后再进行存活性和就绪态检查， 确保这些存活、就绪探针不会影响应用的启动。 启动探针可以用于对慢启动容器进行存活性检测，避免它们在启动运行之前就被杀掉。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 定义存活命令[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-a-liveness-command)

许多长时间运行的应用最终会进入损坏状态，除非重新启动，否则无法被恢复。 Kubernetes 提供了存活探针来发现并处理这种情况。

在本练习中，你会创建一个 Pod，其中运行一个基于 `registry.k8s.io/busybox` 镜像的容器。 下面是这个 Pod 的配置文件。

[`pods/probe/exec-liveness.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/probe/exec-liveness.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/probe/exec-liveness.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

在这个配置文件中，可以看到 Pod 中只有一个 `Container`。 `periodSeconds` 字段指定了 kubelet 应该每 5 秒执行一次存活探测。 `initialDelaySeconds` 字段告诉 kubelet 在执行第一次探测前应该等待 5 秒。 kubelet 在容器内执行命令 `cat /tmp/healthy` 来进行探测。 如果命令执行成功并且返回值为 0，kubelet 就会认为这个容器是健康存活的。 如果这个命令返回非 0 值，kubelet 会杀死这个容器并重新启动它。

当容器启动时，执行如下的命令：

```shell
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 600"
```

这个容器生命的前 30 秒，`/tmp/healthy` 文件是存在的。 所以在这最开始的 30 秒内，执行命令 `cat /tmp/healthy` 会返回成功代码。 30 秒之后，执行命令 `cat /tmp/healthy` 就会返回失败代码。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/probe/exec-liveness.yaml
```

在 30 秒内，查看 Pod 的事件：

```shell
kubectl describe pod liveness-exec
```

输出结果表明还没有存活探针失败：

```
Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  11s   default-scheduler  Successfully assigned default/liveness-exec to node01
  Normal  Pulling    9s    kubelet, node01    Pulling image "registry.k8s.io/busybox"
  Normal  Pulled     7s    kubelet, node01    Successfully pulled image "registry.k8s.io/busybox"
  Normal  Created    7s    kubelet, node01    Created container liveness
  Normal  Started    7s    kubelet, node01    Started container liveness
```

35 秒之后，再来看 Pod 的事件：

```shell
kubectl describe pod liveness-exec
```

在输出结果的最下面，有信息显示存活探针失败了，这个失败的容器被杀死并且被重建了。

```
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  57s                default-scheduler  Successfully assigned default/liveness-exec to node01
  Normal   Pulling    55s                kubelet, node01    Pulling image "registry.k8s.io/busybox"
  Normal   Pulled     53s                kubelet, node01    Successfully pulled image "registry.k8s.io/busybox"
  Normal   Created    53s                kubelet, node01    Created container liveness
  Normal   Started    53s                kubelet, node01    Started container liveness
  Warning  Unhealthy  10s (x3 over 20s)  kubelet, node01    Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory
  Normal   Killing    10s                kubelet, node01    Container liveness failed liveness probe, will be restarted
```

再等 30 秒，确认这个容器被重启了：

```shell
kubectl get pod liveness-exec
```

输出结果显示 `RESTARTS` 的值增加了 1。 请注意，一旦失败的容器恢复为运行状态，`RESTARTS` 计数器就会增加 1：

```
NAME            READY     STATUS    RESTARTS   AGE
liveness-exec   1/1       Running   1          1m
```

### 定义一个存活态 HTTP 请求接口[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-a-liveness-HTTP-request)

另外一种类型的存活探测方式是使用 HTTP GET 请求。 下面是一个 Pod 的配置文件，其中运行一个基于 `registry.k8s.io/liveness` 镜像的容器。

[`pods/probe/http-liveness.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/probe/http-liveness.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/probe/http-liveness.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
```

在这个配置文件中，你可以看到 Pod 也只有一个容器。 `periodSeconds` 字段指定了 kubelet 每隔 3 秒执行一次存活探测。 `initialDelaySeconds` 字段告诉 kubelet 在执行第一次探测前应该等待 3 秒。 kubelet 会向容器内运行的服务（服务在监听 8080 端口）发送一个 HTTP GET 请求来执行探测。 如果服务器上 `/healthz` 路径下的处理程序返回成功代码，则 kubelet 认为容器是健康存活的。 如果处理程序返回失败代码，则 kubelet 会杀死这个容器并将其重启。

返回大于或等于 200 并且小于 400 的任何代码都标示成功，其它返回代码都标示失败。

你可以访问 [server.go](https://github.com/kubernetes/kubernetes/blob/master/test/images/agnhost/liveness/server.go)。 阅读服务的源码。 容器存活期间的最开始 10 秒中，`/healthz` 处理程序返回 200 的状态码。 之后处理程序返回 500 的状态码。

```go
http.HandleFunc("/healthz", func(w http.ResponseWriter, r *http.Request) {
    duration := time.Now().Sub(started)
    if duration.Seconds() > 10 {
        w.WriteHeader(500)
        w.Write([]byte(fmt.Sprintf("error: %v", duration.Seconds())))
    } else {
        w.WriteHeader(200)
        w.Write([]byte("ok"))
    }
})
```

kubelet 在容器启动之后 3 秒开始执行健康检测。所以前几次健康检查都是成功的。 但是 10 秒之后，健康检查会失败，并且 kubelet 会杀死容器再重新启动容器。

创建一个 Pod 来测试 HTTP 的存活检测：

```shell
kubectl apply -f https://k8s.io/examples/pods/probe/http-liveness.yaml
```

10 秒之后，通过查看 Pod 事件来确认存活探针已经失败，并且容器被重新启动了。

```shell
kubectl describe pod liveness-http
```

在 1.13 之前（包括 1.13）的版本中，如果在 Pod 运行的节点上设置了环境变量 `http_proxy`（或者 `HTTP_PROXY`），HTTP 的存活探测会使用这个代理。 在 1.13 之后的版本中，设置本地的 HTTP 代理环境变量不会影响 HTTP 的存活探测。

### 定义 TCP 的存活探测[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-a-TCP-liveness-probe)

第三种类型的存活探测是使用 TCP 套接字。 使用这种配置时，kubelet 会尝试在指定端口和容器建立套接字链接。 如果能建立连接，这个容器就被看作是健康的，如果不能则这个容器就被看作是有问题的。

[`pods/probe/tcp-liveness-readiness.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/probe/tcp-liveness-readiness.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/probe/tcp-liveness-readiness.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
  - name: goproxy
    image: registry.k8s.io/goproxy:0.1
    ports:
    - containerPort: 8080
    readinessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
```

如你所见，TCP 检测的配置和 HTTP 检测非常相似。 下面这个例子同时使用就绪和存活探针。kubelet 会在容器启动 5 秒后发送第一个就绪探针。 探针会尝试连接 `goproxy` 容器的 8080 端口。 如果探测成功，这个 Pod 会被标记为就绪状态，kubelet 将继续每隔 10 秒运行一次探测。

除了就绪探针，这个配置包括了一个存活探针。 kubelet 会在容器启动 15 秒后进行第一次存活探测。 与就绪探针类似，存活探针会尝试连接 `goproxy` 容器的 8080 端口。 如果存活探测失败，容器会被重新启动。

```shell
kubectl apply -f https://k8s.io/examples/pods/probe/tcp-liveness-readiness.yaml
```

15 秒之后，通过看 Pod 事件来检测存活探针：

```shell
kubectl describe pod goproxy
```

### 定义 gRPC 存活探针[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#%E5%AE%9A%E4%B9%89-grpc-%E5%AD%98%E6%B4%BB%E6%8E%A2%E9%92%88)

**特性状态：** `Kubernetes v1.24 [beta]`

如果你的应用实现了 [gRPC 健康检查协议](https://github.com/grpc/grpc/blob/master/doc/health-checking.md)， kubelet 可以配置为使用该协议来执行应用存活性检查。 你必须启用 `GRPCContainerProbe` [特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/) 才能配置依赖于 gRPC 的检查机制。

下面是一个示例清单：

[`pods/probe/grpc-liveness.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/probe/grpc-liveness.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/probe/grpc-liveness.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: etcd-with-grpc
spec:
  containers:
  - name: etcd
    image: registry.k8s.io/etcd:3.5.1-0
    command: [ "/usr/local/bin/etcd", "--data-dir",  "/var/lib/etcd", "--listen-client-urls", "http://0.0.0.0:2379", "--advertise-client-urls", "http://127.0.0.1:2379", "--log-level", "debug"]
    ports:
    - containerPort: 2379
    livenessProbe:
      grpc:
        port: 2379
      initialDelaySeconds: 10
```

要使用 gRPC 探针，必须配置 `port` 属性。如果健康状态端点配置在非默认服务之上， 你还必须设置 `service` 属性。

**说明：**

与 HTTP 和 TCP 探针不同，gRPC 探测不能使用命名端口或定制主机。

配置问题（例如：错误的 `port` 和 `service`、未实现健康检查协议） 都被认作是探测失败，这一点与 HTTP 和 TCP 探针类似。

```shell
kubectl apply -f https://k8s.io/examples/pods/probe/grpc-liveness.yaml
```

15 秒钟之后，查看 Pod 事件确认存活性检查并未失败：

```shell
kubectl describe pod etcd-with-grpc
```

在 Kubernetes 1.23 之前，gRPC 健康探测通常使用 [grpc-health-probe](https://github.com/grpc-ecosystem/grpc-health-probe/) 来实现，如博客 [Health checking gRPC servers on Kubernetes（对 Kubernetes 上的 gRPC 服务器执行健康检查）](https://kubernetes.io/blog/2018/10/01/health-checking-grpc-servers-on-kubernetes/)所描述。 内置的 gRPC 探针行为与 `grpc-health-probe` 所实现的行为类似。 从 `grpc-health-probe` 迁移到内置探针时，请注意以下差异：

- 内置探针运行时针对的是 Pod 的 IP 地址，不像 `grpc-health-probe` 那样通常针对 `127.0.0.1` 执行探测； 请一定配置你的 gRPC 端点使之监听于 Pod 的 IP 地址之上。
- 内置探针不支持任何身份认证参数（例如 `-tls`）。
- 对于内置的探针而言，不存在错误代码。所有错误都被视作探测失败。
- 如果 `ExecProbeTimeout` 特性门控被设置为 `false`，则 `grpc-health-probe` 不会考虑 `timeoutSeconds` 设置状态（默认值为 1s）， 而内置探针则会在超时时返回失败。

### 使用命名端口[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#use-a-named-port)

对于 HTTP 和 TCP 存活检测可以使用命名的 [`port`](https://kubernetes.io/zh-cn/docs/reference/kubernetes-api/workload-resources/pod-v1/#ports)（gRPC 探针不支持使用命名端口）。

例如：

```yaml
ports:
- name: liveness-port
  containerPort: 8080
  hostPort: 8080

livenessProbe:
  httpGet:
    path: /healthz
    port: liveness-port
```

### 使用启动探针保护慢启动容器[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-startup-probes)

有时候，会有一些现有的应用在启动时需要较长的初始化时间。 要这种情况下，若要不影响对死锁作出快速响应的探测，设置存活探测参数是要技巧的。 技巧就是使用相同的命令来设置启动探测，针对 HTTP 或 TCP 检测，可以通过将 `failureThreshold * periodSeconds` 参数设置为足够长的时间来应对糟糕情况下的启动时间。

这样，前面的例子就变成了：

```yaml
ports:
- name: liveness-port
  containerPort: 8080
  hostPort: 8080

livenessProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 1
  periodSeconds: 10

startupProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 30
  periodSeconds: 10
```

幸亏有启动探测，应用程序将会有最多 5 分钟（30 \* 10 = 300s）的时间来完成其启动过程。 一旦启动探测成功一次，存活探测任务就会接管对容器的探测，对容器死锁作出快速响应。 如果启动探测一直没有成功，容器会在 300 秒后被杀死，并且根据 `restartPolicy` 来执行进一步处置。

### 定义就绪探针[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-readiness-probes)

有时候，应用会暂时性地无法为请求提供服务。 例如，应用在启动时可能需要加载大量的数据或配置文件，或是启动后要依赖等待外部服务。 在这种情况下，既不想杀死应用，也不想给它发送请求。 Kubernetes 提供了就绪探针来发现并缓解这些情况。 容器所在 Pod 上报还未就绪的信息，并且不接受通过 Kubernetes Service 的流量。

**说明：**

就绪探针在容器的整个生命周期中保持运行状态。

**注意：**

存活探针 **不等待** 就绪性探针成功。 如果要在执行存活探针之前等待，应该使用 `initialDelaySeconds` 或 `startupProbe`。

就绪探针的配置和存活探针的配置相似。 唯一区别就是要使用 `readinessProbe` 字段，而不是 `livenessProbe` 字段。

```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

HTTP 和 TCP 的就绪探针配置也和存活探针的配置完全相同。

就绪和存活探测可以在同一个容器上并行使用。 两者共同使用，可以确保流量不会发给还未就绪的容器，当这些探测失败时容器会被重新启动。

### 配置探针[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#configure-probes)

[Probe](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#probe-v1-core) 有很多配置字段，可以使用这些字段精确地控制启动、存活和就绪检测的行为：

- `initialDelaySeconds`：容器启动后要等待多少秒后才启动启动、存活和就绪探针， 默认是 0 秒，最小值是 0。
- `periodSeconds`：执行探测的时间间隔（单位是秒）。默认是 10 秒。最小值是 1。
- `timeoutSeconds`：探测的超时后等待多少秒。默认值是 1 秒。最小值是 1。
- `successThreshold`：探针在失败后，被视为成功的最小连续成功数。默认值是 1。 存活和启动探测的这个值必须是 1。最小值是 1。

- `failureThreshold`：探针连续失败了 `failureThreshold` 次之后， Kubernetes 认为总体上检查已失败：容器状态未就绪、不健康、不活跃。 对于启动探针或存活探针而言，如果至少有 `failureThreshold` 个探针已失败， Kubernetes 会将容器视为不健康并为这个特定的容器触发重启操作。 kubelet 会考虑该容器的 `terminationGracePeriodSeconds` 设置。 对于失败的就绪探针，kubelet 继续运行检查失败的容器，并继续运行更多探针； 因为检查失败，kubelet 将 Pod 的 `Ready` [状况](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/#pod-conditions)设置为 `false`。

- `terminationGracePeriodSeconds`：为 kubelet 配置从为失败的容器触发终止操作到强制容器运行时停止该容器之前等待的宽限时长。 默认值是继承 Pod 级别的 `terminationGracePeriodSeconds` 值（如果不设置则为 30 秒），最小值为 1。 更多细节请参见[探针级别 `terminationGracePeriodSeconds`](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#probe-level-terminationgraceperiodseconds)。

**说明：**

在 Kubernetes 1.20 版本之前，`exec` 探针会忽略 `timeoutSeconds`： 探针会无限期地持续运行，甚至可能超过所配置的限期，直到返回结果为止。

这一缺陷在 Kubernetes v1.20 版本中得到修复。你可能一直依赖于之前错误的探测行为， 甚至都没有觉察到这一问题的存在，因为默认的超时值是 1 秒钟。 作为集群管理员，你可以在所有的 kubelet 上禁用 `ExecProbeTimeout` [特性门控](https://kubernetes.io/zh-cn/docs/reference/command-line-tools-reference/feature-gates/) （将其设置为 `false`），从而恢复之前版本中的运行行为。之后当集群中所有的 exec 探针都设置了 `timeoutSeconds` 参数后，移除此标志重载。 如果你有 Pod 受到此默认 1 秒钟超时值的影响，你应该更新这些 Pod 对应的探针的超时值， 这样才能为最终去除该特性门控做好准备。

当此缺陷被修复之后，在使用 `dockershim` 容器运行时的 Kubernetes `1.20+` 版本中，对于 exec 探针而言，容器中的进程可能会因为超时值的设置保持持续运行， 即使探针返回了失败状态。

**注意：**

如果就绪态探针的实现不正确，可能会导致容器中进程的数量不断上升。 如果不对其采取措施，很可能导致资源枯竭的状况。

#### HTTP 探测[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#http-probes)

[HTTP Probes](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#httpgetaction-v1-core) 允许针对 `httpGet` 配置额外的字段：

- `host`：连接使用的主机名，默认是 Pod 的 IP。也可以在 HTTP 头中设置 “Host” 来代替。
- `scheme` ：用于设置连接主机的方式（HTTP 还是 HTTPS）。默认是 "HTTP"。
- `path`：访问 HTTP 服务的路径。默认值为 "/"。
- `httpHeaders`：请求中自定义的 HTTP 头。HTTP 头字段允许重复。
- `port`：访问容器的端口号或者端口名。如果数字必须在 1～65535 之间。

对于 HTTP 探测，kubelet 发送一个 HTTP 请求到指定的路径和端口来执行检测。 除非 `httpGet` 中的 `host` 字段设置了，否则 kubelet 默认是给 Pod 的 IP 地址发送探测。 如果 `scheme` 字段设置为了 `HTTPS`，kubelet 会跳过证书验证发送 HTTPS 请求。 大多数情况下，不需要设置 `host` 字段。 这里有个需要设置 `host` 字段的场景，假设容器监听 127.0.0.1，并且 Pod 的 `hostNetwork` 字段设置为了 `true`。那么 `httpGet` 中的 `host` 字段应该设置为 127.0.0.1。 可能更常见的情况是如果 Pod 依赖虚拟主机，你不应该设置 `host` 字段，而是应该在 `httpHeaders` 中设置 `Host`。

针对 HTTP 探针，kubelet 除了必需的 `Host` 头部之外还发送两个请求头部字段： `User-Agent` 和 `Accept`。这些头部的默认值分别是 `kube-probe/{{ skew currentVersion >}}` （其中 `1.26` 是 kubelet 的版本号）和 `*/*`。

你可以通过为探测设置 `.httpHeaders` 来重载默认的头部字段值；例如：

```yaml
livenessProbe:
  httpGet:
    httpHeaders:
      - name: Accept
        value: application/json

startupProbe:
  httpGet:
    httpHeaders:
      - name: User-Agent
        value: MyUserAgent
```

你也可以通过将这些头部字段定义为空值，从请求中去掉这些头部字段。

```yaml
livenessProbe:
  httpGet:
    httpHeaders:
      - name: Accept
        value: ""

startupProbe:
  httpGet:
    httpHeaders:
      - name: User-Agent
        value: ""
```

#### TCP 探测[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#tcp-probes)

对于 TCP 探测而言，kubelet 在节点上（不是在 Pod 里面）发起探测连接， 这意味着你不能在 `host` 参数上配置服务名称，因为 kubelet 不能解析服务名称。

#### 探针层面的 `terminationGracePeriodSeconds`[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#%E6%8E%A2%E9%92%88%E5%B1%82%E9%9D%A2%E7%9A%84-terminationgraceperiodseconds)

**特性状态：** `Kubernetes v1.25 [beta]`

在 1.21 发行版之前，Pod 层面的 `terminationGracePeriodSeconds` 被用来终止存活探测或启动探测失败的容器。 这一行为上的关联不是我们想要的，可能导致 Pod 层面设置了 `terminationGracePeriodSeconds` 时容器要花非常长的时间才能重新启动。

在 1.21 及更高版本中，用户可以指定一个探针层面的 `terminationGracePeriodSeconds` 作为探针规约的一部分。 当 Pod 层面和探针层面的 `terminationGracePeriodSeconds` 都已设置，kubelet 将使用探针层面设置的值。

**说明：**

从 Kubernetes 1.25 开始，默认启用 `ProbeTerminationGracePeriod` 特性。 选择禁用此特性的用户，请注意以下事项:

- `ProbeTerminationGracePeriod` 特性门控只能用在 API 服务器上。 kubelet 始终优先选用探针级别 `terminationGracePeriodSeconds` 字段 （如果它存在于 Pod 上）。

- 如果你已经为现有 Pod 设置了 `terminationGracePeriodSeconds` 字段并且不再希望使用针对每个探针的终止宽限期，则必须删除现有的这类 Pod。

- 当你（或控制平面或某些其他组件）创建替换 Pod，并且特性门控 `ProbeTerminationGracePeriod` 被禁用时，即使 Pod 或 Pod 模板指定了 `terminationGracePeriodSeconds` 字段， API 服务器也会忽略探针级别的 `terminationGracePeriodSeconds` 字段设置。

例如:

```yaml
spec:
  terminationGracePeriodSeconds: 3600  # Pod 级别设置
  containers:
  - name: test
    image: ...

    ports:
    - name: liveness-port
      containerPort: 8080
      hostPort: 8080

    livenessProbe:
      httpGet:
        path: /healthz
        port: liveness-port
      failureThreshold: 1
      periodSeconds: 60
      # 重载 Pod 级别的 terminationGracePeriodSeconds
      terminationGracePeriodSeconds: 60
```

探针层面的 `terminationGracePeriodSeconds` 不能用于就绪态探针。 这一设置将被 API 服务器拒绝。
## [将 Pod 分配给节点](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes/)
此页面显示如何将 Kubernetes Pod 指派给 Kubernetes 集群中的特定节点。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 给节点添加标签[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes/#add-a-label-to-a-node)

1. 列出你的集群中的[节点](https://kubernetes.io/zh-cn/docs/concepts/architecture/nodes/)， 包括这些节点上的标签：
    
    ```shell
    kubectl get nodes --show-labels
    ```
    
    输出类似如下：
    
    ```shell
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```
    

2. 从你的节点中选择一个，为它添加标签：
    
    ```shell
    kubectl label nodes <your-node-name> disktype=ssd
    ```
    
    `<your-node-name>` 是你选择的节点的名称。
    

3. 验证你选择的节点确实带有 `disktype=ssd` 标签：
    
    ```shell
    kubectl get nodes --show-labels
    ```
    
    输出类似如下：
    
    ```shell
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,disktype=ssd,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```
    
    在前面的输出中，你可以看到 `worker0` 节点有 `disktype=ssd` 标签。
    

### 创建一个将被调度到你选择的节点的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes/#create-a-pod-scheduled-to-chosen-node)

此 Pod 配置文件描述了一个拥有节点选择器 `disktype: ssd` 的 Pod。这表明该 Pod 将被调度到有 `disktype=ssd` 标签的节点。

[`pods/pod-nginx.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-nginx.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-nginx.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```

1. 使用该配置文件创建一个 Pod，该 Pod 将被调度到你选择的节点上：
    
    ```shell
    kubectl create -f https://k8s.io/examples/pods/pod-nginx.yaml
    ```
    

2. 验证 Pod 确实运行在你选择的节点上：
    
    ```shell
    kubectl get pods --output=wide
    ```
    
    输出类似如下：
    
    ```shell
    NAME     READY     STATUS    RESTARTS   AGE    IP           NODE
    nginx    1/1       Running   0          13s    10.200.0.4   worker0
    ```
    

### 创建一个会被调度到特定节点上的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes/#create-a-pod-scheduled-to-specific-node)

你也可以通过设置 `nodeName` 将某个 Pod 调度到特定的节点。

[`pods/pod-nginx-specific-node.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-nginx-specific-node.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-nginx-specific-node.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: foo-node # 调度 Pod 到特定的节点
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
```

使用此配置文件来创建一个 Pod，该 Pod 将只能被调度到 `foo-node` 节点。
## [用节点亲和性把 Pod 分配到节点](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/)
本页展示在 Kubernetes 集群中，如何使用节点亲和性把 Kubernetes Pod 分配到特定节点。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你的 Kubernetes 服务器版本必须不低于版本 v1.10. 要获知版本信息，请输入 `kubectl version`.

### 给节点添加标签[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/#%E7%BB%99%E8%8A%82%E7%82%B9%E6%B7%BB%E5%8A%A0%E6%A0%87%E7%AD%BE)

1. 列出集群中的节点及其标签：
    
    ```shell
    kubectl get nodes --show-labels
    ```
    
    输出类似于此：
    
    ```
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```
    

2. 选择一个节点，给它添加一个标签：
    
    ```shell
    kubectl label nodes <your-node-name> disktype=ssd
    ```
    
    其中 `<your-node-name>` 是你所选节点的名称。
    

3. 验证你所选节点具有 `disktype=ssd` 标签：
    
    ```shell
    kubectl get nodes --show-labels
    ```
    
    输出类似于此：
    
    ```
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,disktype=ssd,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```
    
    在前面的输出中，可以看到 `worker0` 节点有一个 `disktype=ssd` 标签。
    

### 依据强制的节点亲和性调度 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/#schedule-a-Pod-using-required-node-affinity)

下面清单描述了一个 Pod，它有一个节点亲和性配置 `requiredDuringSchedulingIgnoredDuringExecution`，`disktype=ssd`。 这意味着 pod 只会被调度到具有 `disktype=ssd` 标签的节点上。

[`pods/pod-nginx-required-affinity.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-nginx-required-affinity.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-nginx-required-affinity.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd            
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
```

1. 执行（Apply）此清单来创建一个调度到所选节点上的 Pod：
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/pod-nginx-required-affinity.yaml
    ```
    

2. 验证 Pod 已经在所选节点上运行：
    
    ```shell
    kubectl get pods --output=wide
    ```
    
    输出类似于此：
    
    ```
    NAME     READY     STATUS    RESTARTS   AGE    IP           NODE
    nginx    1/1       Running   0          13s    10.200.0.4   worker0
    ```
    

### 使用首选的节点亲和性调度 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/#schedule-a-Pod-using-preferred-node-affinity)

本清单描述了一个 Pod，它有一个节点亲和性设置 `preferredDuringSchedulingIgnoredDuringExecution`，`disktype: ssd`。 这意味着 Pod 将首选具有 `disktype=ssd` 标签的节点。

[`pods/pod-nginx-preferred-affinity.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-nginx-preferred-affinity.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-nginx-preferred-affinity.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd          
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
```

1. 执行此清单创建一个会调度到所选节点上的 Pod：
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/pod-nginx-preferred-affinity.yaml
    ```
    

2. 验证 Pod 是否在所选节点上运行：
    
    ```shell
    kubectl get pods --output=wide
    ```
    
    输出类似于此：
    
    ```
    NAME     READY     STATUS    RESTARTS   AGE    IP           NODE
    nginx    1/1       Running   0          13s    10.200.0.4   worker0
    ```
## [配置 Pod 初始化](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-initialization/)
本文介绍在应用容器运行前，怎样利用 Init 容器初始化 Pod。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-initialization/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 创建一个包含 Init 容器的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-initialization/#creating-a-pod-that-has-an-init-container)

本例中你将创建一个包含一个应用容器和一个 Init 容器的 Pod。Init 容器在应用容器启动前运行完成。

下面是 Pod 的配置文件：

[`pods/init-containers.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/init-containers.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/init-containers.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    volumeMounts:
    - name: workdir
      mountPath: /usr/share/nginx/html
  # 这些容器在 Pod 初始化期间运行
  initContainers:
  - name: install
    image: busybox:1.28
    command:
    - wget
    - "-O"
    - "/work-dir/index.html"
    - http://info.cern.ch
    volumeMounts:
    - name: workdir
      mountPath: "/work-dir"
  dnsPolicy: Default
  volumes:
  - name: workdir
    emptyDir: {}

```

配置文件中，你可以看到应用容器和 Init 容器共享了一个卷。

Init 容器将共享卷挂载到了 `/work-dir` 目录，应用容器将共享卷挂载到了 `/usr/share/nginx/html` 目录。 Init 容器执行完下面的命令就终止：

```shell
wget -O /work-dir/index.html http://info.cern.ch
```

请注意 Init 容器在 nginx 服务器的根目录写入 `index.html`。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/init-containers.yaml
```

检查 nginx 容器运行正常：

```shell
kubectl get pod init-demo
```

结果表明 nginx 容器运行正常：

```
NAME        READY     STATUS    RESTARTS   AGE
init-demo   1/1       Running   0          1m
```

通过 shell 进入 init-demo Pod 中的 nginx 容器：

```shell
kubectl exec -it init-demo -- /bin/bash
```

在 shell 中，发送个 GET 请求到 nginx 服务器：

```
root@nginx:~# apt-get update
root@nginx:~# apt-get install curl
root@nginx:~# curl localhost
```

结果表明 nginx 正在为 Init 容器编写的 web 页面服务：

```
<html><head></head><body><header>
<title>http://info.cern.ch</title>
</header>

<h1>http://info.cern.ch - home of the first website</h1>
  ...
<li><a href="http://info.cern.ch/hypertext/WWW/TheProject.html">Browse the first website</a></li>
  ...
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-initialization/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 进一步了解[同一 Pod 中的容器间的通信](https://kubernetes.io/zh-cn/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/)。
- 进一步了解 [Init 容器](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/init-containers/)。
- 进一步了解[卷](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/)。
- 进一步了解 [Init 容器排错](https://kubernetes.io/zh-cn/docs/tasks/debug/debug-application/debug-init-containers/)。
## [为容器的生命周期事件设置处理函数](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)
这个页面将演示如何为容器的生命周期事件挂接处理函数。Kubernetes 支持 postStart 和 preStop 事件。 当一个容器启动后，Kubernetes 将立即发送 postStart 事件；在容器被终结之前， Kubernetes 将发送一个 preStop 事件。容器可以为每个事件指定一个处理程序。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 定义 postStart 和 preStop 处理函数[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/#define-poststart-and-prestop-handlers)

在本练习中，你将创建一个包含一个容器的 Pod，该容器为 postStart 和 preStop 事件提供对应的处理函数。

下面是对应 Pod 的配置文件：

[`pods/lifecycle-events.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/lifecycle-events.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/lifecycle-events.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: lifecycle-demo
spec:
  containers:
  - name: lifecycle-demo-container
    image: nginx
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]
      preStop:
        exec:
          command: ["/bin/sh","-c","nginx -s quit; while killall -0 nginx; do sleep 1; done"]

```

在上述配置文件中，你可以看到 postStart 命令在容器的 `/usr/share` 目录下写入文件 `message`。 命令 preStop 负责优雅地终止 nginx 服务。当因为失效而导致容器终止时，这一处理方式很有用。

创建 Pod：

```shell
kubectl apply -f https://k8s.io/examples/pods/lifecycle-events.yaml
```

验证 Pod 中的容器已经运行：

```shell
kubectl get pod lifecycle-demo
```

使用 shell 连接到你的 Pod 里的容器：

```shell
kubectl exec -it lifecycle-demo -- /bin/bash
```

在 shell 中，验证 `postStart` 处理函数创建了 `message` 文件：

```
root@lifecycle-demo:/# cat /usr/share/message
```

命令行输出的是 `postStart` 处理函数所写入的文本：

```
Hello from the postStart handler
```

### 讨论[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/#discussion)

Kubernetes 在容器创建后立即发送 postStart 事件。 然而，postStart 处理函数的调用不保证早于容器的入口点（entrypoint） 的执行。postStart 处理函数与容器的代码是异步执行的，但 Kubernetes 的容器管理逻辑会一直阻塞等待 postStart 处理函数执行完毕。 只有 postStart 处理函数执行完毕，容器的状态才会变成 RUNNING。

Kubernetes 在容器结束前立即发送 preStop 事件。除非 Pod 宽限期限超时， Kubernetes 的容器管理逻辑会一直阻塞等待 preStop 处理函数执行完毕。 更多细节请参阅 [Pod 的生命周期](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/)。

**说明：**

Kubernetes 只有在一个 Pod 或该 Pod 中的容器**结束（Terminated）** 的时候才会发送 preStop 事件， 这意味着在 Pod **完成（Completed）** 时 preStop 的事件处理逻辑不会被触发。有关这个限制， 请参阅[容器回调](https://kubernetes.io/zh-cn/docs/concepts/containers/container-lifecycle-hooks/#container-hooks)了解详情。

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 进一步了解[容器生命周期回调](https://kubernetes.io/zh-cn/docs/concepts/containers/container-lifecycle-hooks/)。
- 进一步了解 [Pod 的生命周期](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/pod-lifecycle/)。

#### 参考[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/#%E5%8F%82%E8%80%83)

- [Lifecycle](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#lifecycle-v1-core)
- [Container](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#container-v1-core)
- 参阅 [PodSpec](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#podspec-v1-core) 中关于 `terminationGracePeriodSeconds` 的部分
## [配置 Pod 使用 ConfigMap](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/)
很多应用在其初始化或运行期间要依赖一些配置信息。 大多数时候，存在要调整配置参数所设置的数值的需求。 ConfigMap 是 Kubernetes 的一种机制，可让你将配置数据注入到应用的 [Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/) 内部。

ConfigMap 概念允许你将配置清单与镜像内容分离，以保持容器化的应用程序的可移植性。 例如，你可以下载并运行相同的[容器镜像](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-image)来启动容器， 用于本地开发、系统测试或运行实时终端用户工作负载。

本页提供了一系列使用示例，这些示例演示了如何创建 ConfigMap 以及配置 Pod 使用存储在 ConfigMap 中的数据。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你需要安装 `wget` 工具。如果你有不同的工具，例如 `curl`，而没有 `wget`， 则需要调整下载示例数据的步骤。

### 创建 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-a-configmap)

你可以使用 `kubectl create configmap` 或者在 `kustomization.yaml` 中的 ConfigMap 生成器来创建 ConfigMap。

#### 使用 `kubectl create configmap` 创建 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-a-configmap-using-kubectl-create-configmap)

你可以使用 `kubectl create configmap` 命令基于[目录](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-directories)、 [文件](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-files)或者[字面值](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-literal-values)来创建 ConfigMap：

```shell
kubectl create configmap <映射名称> <数据源>
```

其中，`<映射名称>` 是为 ConfigMap 指定的名称，`<数据源>` 是要从中提取数据的目录、 文件或者字面值。ConfigMap 对象的名称必须是合法的 [DNS 子域名](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names#dns-subdomain-names).

在你基于文件来创建 ConfigMap 时，`<数据源>` 中的键名默认取自文件的基本名， 而对应的值则默认为文件的内容。

你可以使用 [`kubectl describe`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#describe) 或者 [`kubectl get`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#get) 获取有关 ConfigMap 的信息。

##### 基于一个目录来创建 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-directories)

你可以使用 `kubectl create configmap` 基于同一目录中的多个文件创建 ConfigMap。 当你基于目录来创建 ConfigMap 时，kubectl 识别目录下文件名可以作为合法键名的文件， 并将这些文件打包到新的 ConfigMap 中。普通文件之外的所有目录项都会被忽略 （例如：子目录、符号链接、设备、管道等等）。

**说明：**

用于创建 ConfigMap 的每个文件名必须由可接受的字符组成，即：字母（`A` 到 `Z` 和 `a` 到 `z`）、数字（`0` 到 `9`）、'-'、'\_'或'.'。 如果在一个目录中使用 `kubectl create configmap`，而其中任一文件名包含不可接受的字符， 则 `kubectl` 命令可能会失败。

`kubectl` 命令在遇到不合法的文件名时不会打印错误。

创建本地目录：

```shell
mkdir -p configure-pod-container/configmap/
```

现在，下载示例的配置并创建 ConfigMap：

```shell
# 将示例文件下载到 `configure-pod-container/configmap/` 目录
wget https://kubernetes.io/examples/configmap/game.properties -O configure-pod-container/configmap/game.properties
wget https://kubernetes.io/examples/configmap/ui.properties -O configure-pod-container/configmap/ui.properties

# 创建 ConfigMap
kubectl create configmap game-config --from-file=configure-pod-container/configmap/
```

以上命令将 `configure-pod-container/configmap` 目录下的所有文件，也就是 `game.properties` 和 `ui.properties` 打包到 game-config ConfigMap 中。你可以使用下面的命令显示 ConfigMap 的详细信息：

```shell
kubectl describe configmaps game-config
```

输出类似以下内容：

```
Name:         game-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
ui.properties:
----
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice
```

`configure-pod-container/configmap/` 目录中的 `game.properties` 和 `ui.properties` 文件出现在 ConfigMap 的 `data` 部分。

```shell
kubectl get configmaps game-config -o yaml
```

输出类似以下内容:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: 2022-02-18T18:52:05Z
  name: game-config
  namespace: default
  resourceVersion: "516"
  uid: b4952dc3-d670-11e5-8cd0-68f728db1985
data:
  game.properties: |
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30    
  ui.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice    
```

##### 基于文件创建 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-files)

你可以使用 `kubectl create configmap` 基于单个文件或多个文件创建 ConfigMap。

例如：

```shell
kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/game.properties
```

将产生以下 ConfigMap:

```shell
kubectl describe configmaps game-config-2
```

输出类似以下内容:

```
Name:         game-config-2
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
```

你可以多次使用 `--from-file` 参数，从多个数据源创建 ConfigMap。

```shell
kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/game.properties --from-file=configure-pod-container/configmap/ui.properties
```

你可以使用以下命令显示 `game-config-2` ConfigMap 的详细信息：

```shell
kubectl describe configmaps game-config-2
```

输出类似以下内容:

```
Name:         game-config-2
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
ui.properties:
----
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice
```

使用 `--from-env-file` 选项基于 env 文件创建 ConfigMap，例如：

```shell
# Env 文件包含环境变量列表。其中适用以下语法规则:
# 这些语法规则适用：
#   Env 文件中的每一行必须为 VAR=VAL 格式。
#   以＃开头的行（即注释）将被忽略。
#   空行将被忽略。
#   引号不会被特殊处理（即它们将成为 ConfigMap 值的一部分）。

# 将示例文件下载到 `configure-pod-container/configmap/` 目录
wget https://kubernetes.io/examples/configmap/game-env-file.properties -O configure-pod-container/configmap/game-env-file.properties
wget https://kubernetes.io/examples/configmap/ui-env-file.properties -O configure-pod-container/configmap/ui-env-file.properties

# Env 文件 `game-env-file.properties` 如下所示
cat configure-pod-container/configmap/game-env-file.properties
enemies=aliens
lives=3
allowed="true"

# This comment and the empty line above it are ignored
```

```shell
kubectl create configmap game-config-env-file \
       --from-env-file=configure-pod-container/configmap/game-env-file.properties
```

将产生以下 ConfigMap。查看 ConfigMap：

```shell
kubectl get configmap game-config-env-file -o yaml
```

输出类似以下内容：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: 2019-12-27T18:36:28Z
  name: game-config-env-file
  namespace: default
  resourceVersion: "809965"
  uid: d9d1ca5b-eb34-11e7-887b-42010a8002b8
data:
  allowed: '"true"'
  enemies: aliens
  lives: "3"
```

从 Kubernetes 1.23 版本开始，`kubectl` 支持多次指定 `--from-env-file` 参数来从多个数据源创建 ConfigMap。

```shell
kubectl create configmap config-multi-env-files \
        --from-env-file=configure-pod-container/configmap/game-env-file.properties \
        --from-env-file=configure-pod-container/configmap/ui-env-file.properties
```

将产生以下 ConfigMap:

```shell
kubectl get configmap config-multi-env-files -o yaml
```

输出类似以下内容:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: 2019-12-27T18:38:34Z
  name: config-multi-env-files
  namespace: default
  resourceVersion: "810136"
  uid: 252c4572-eb35-11e7-887b-42010a8002b8
data:
  allowed: '"true"'
  color: purple
  enemies: aliens
  how: fairlyNice
  lives: "3"
  textmode: "true"
```

##### 定义从文件创建 ConfigMap 时要使用的键[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#define-the-key-to-use-when-generating-a-configmap-from-a-file)

在使用 `--from-file` 参数时，你可以定义在 ConfigMap 的 `data` 部分出现键名， 而不是按默认行为使用文件名：

```shell
kubectl create configmap game-config-3 --from-file=<我的键名>=<文件路径>
```

`<我的键名>` 是你要在 ConfigMap 中使用的键名，`<文件路径>` 是你想要键所表示的数据源文件的位置。

例如:

```shell
kubectl create configmap game-config-3 --from-file=game-special-key=configure-pod-container/configmap/game.properties
```

将产生以下 ConfigMap:

```shell
kubectl get configmaps game-config-3 -o yaml
```

输出类似以下内容：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: 2022-02-18T18:54:22Z
  name: game-config-3
  namespace: default
  resourceVersion: "530"
  uid: 05f8da22-d671-11e5-8cd0-68f728db1985
data:
  game-special-key: |
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30    
```

##### 根据字面值创建 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-literal-values)

你可以将 `kubectl create configmap` 与 `--from-literal` 参数一起使用， 通过命令行定义文字值：

```shell
kubectl create configmap special-config --from-literal=special.how=very --from-literal=special.type=charm
```

你可以传入多个键值对。命令行中提供的每对键值在 ConfigMap 的 `data` 部分中均表示为单独的条目。

```shell
kubectl get configmaps special-config -o yaml
```

输出类似以下内容:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: 2022-02-18T19:14:38Z
  name: special-config
  namespace: default
  resourceVersion: "651"
  uid: dadce046-d673-11e5-8cd0-68f728db1985
data:
  special.how: very
  special.type: charm
```

#### 基于生成器创建 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-a-configmap-from-generator)

你还可以基于生成器（Generators）创建 ConfigMap，然后将其应用于集群的 API 服务器上创建对象。 生成器应在目录内的 `kustomization.yaml` 中指定。

##### 基于文件生成 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#generate-configmaps-from-files)

例如，要基于 `configure-pod-container/configmap/game.properties` 文件生成一个 ConfigMap：

```shell
# 创建包含 ConfigMapGenerator 的 kustomization.yaml 文件
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: game-config-4
  labels:
    game-config: config-4
  files:
  - configure-pod-container/configmap/game.properties
EOF
```

应用（Apply）kustomization 目录创建 ConfigMap 对象：

```shell
kubectl apply -k .
```

```
configmap/game-config-4-m9dm2f92bt created
```

你可以像这样检查 ConfigMap 已经被创建：

```shell
kubectl get configmap
```

```
NAME                       DATA   AGE
game-config-4-m9dm2f92bt   1      37s
```

也可以这样：

```shell
kubectl describe configmaps/game-config-4-m9dm2f92bt
```

```
Name:         game-config-4-m9dm2f92bt
Namespace:    default
Labels:       game-config=config-4
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"v1","data":{"game.properties":"enemies=aliens\nlives=3\nenemies.cheat=true\nenemies.cheat.level=noGoodRotten\nsecret.code.p...

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
Events:  <none>
```

请注意，生成的 ConfigMap 名称具有通过对内容进行散列而附加的后缀， 这样可以确保每次修改内容时都会生成新的 ConfigMap。

##### 定义从文件生成 ConfigMap 时要使用的键[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#define-the-key-to-use-when-generating-a-configmap-from-a-file)

在 ConfigMap 生成器中，你可以定义一个非文件名的键名。 例如，从 `configure-pod-container/configmap/game.properties` 文件生成 ConfigMap， 但使用 `game-special-key` 作为键名：

```shell
# 创建包含 ConfigMapGenerator 的 kustomization.yaml 文件
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: game-config-5
  labels:
    game-config: config-5
  files:
  - game-special-key=configure-pod-container/configmap/game.properties
EOF
```

应用 Kustomization 目录创建 ConfigMap 对象。

```shell
kubectl apply -k .
```

```
configmap/game-config-5-m67dt67794 created
```

##### 基于字面值生成 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#generate-configmaps-from-literals)

此示例向你展示如何使用 Kustomize 和 kubectl，基于两个字面键/值对 `special.type=charm` 和 `special.how=very` 创建一个 `ConfigMap`。 为了实现这一点，你可以配置 `ConfigMap` 生成器。 创建（或替换）`kustomization.yaml`，使其具有以下内容。

```yaml
---
# 基于字面创建 ConfigMap 的 kustomization.yaml 内容
configMapGenerator:
- name: special-config-2
  literals:
  - special.how=very
  - special.type=charm
EOF
```

应用 Kustomization 目录创建 ConfigMap 对象。

```shell
kubectl apply -k .
```

```
configmap/special-config-2-c92b5mmcf2 created
```

### 临时清理[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#interim-cleanup)

在继续之前，清理你创建的一些 ConfigMap：

```bash
kubectl delete configmap special-config
kubectl delete configmap env-config
kubectl delete configmap -l 'game-config in (config-4,config-5)’
```

现在你已经学会了定义 ConfigMap，你可以继续下一节，学习如何将这些对象与 Pod 一起使用。

---

### 使用 ConfigMap 数据定义容器环境变量[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#define-container-environment-variables-using-configmap-data)

#### 使用单个 ConfigMap 中的数据定义容器环境变量[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#define-a-container-environment-variable-with-data-from-a-single-configmap)

1. 在 ConfigMap 中将环境变量定义为键值对:
    
    ```shell
    kubectl create configmap special-config --from-literal=special.how=very
    ```
    

2. 将 ConfigMap 中定义的 `special.how` 赋值给 Pod 规约中的 `SPECIAL_LEVEL_KEY` 环境变量。
    
    [`pods/pod-single-configmap-env-variable.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-single-configmap-env-variable.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-single-configmap-env-variable.yaml to clipboard")
    
    ```yaml
    apiVersion: v1
       kind: Pod
       metadata:
         name: dapi-test-pod
       spec:
         containers:
           - name: test-container
             image: registry.k8s.io/busybox
             command: [ "/bin/sh", "-c", "env" ]
             env:
               # 定义环境变量
               - name: SPECIAL_LEVEL_KEY
                 valueFrom:
                   configMapKeyRef:
                     # ConfigMap 包含你要赋给 SPECIAL_LEVEL_KEY 的值
                     name: special-config
                     # 指定与取值相关的键名
                     key: special.how
         restartPolicy: Never
       
    ```
    
    创建 Pod:
    
    ```shell
    kubectl create -f https://kubernetes.io/examples/pods/pod-single-configmap-env-variable.yaml
    ```
    
    现在，Pod 的输出包含环境变量 `SPECIAL_LEVEL_KEY=very`。
    

#### 使用来自多个 ConfigMap 的数据定义容器环境变量[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#define-container-environment-variables-with-data-from-multiple-configmaps)

与前面的示例一样，首先创建 ConfigMap。 这是你将使用的清单：

[`configmap/configmaps.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/configmap/configmaps.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy configmap/configmaps.yaml to clipboard")

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  special.how: very
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: env-config
  namespace: default
data:
  log_level: INFO
```

- 创建 ConfigMap:
    
    ```shell
    kubectl create -f https://kubernetes.io/examples/configmap/configmaps.yaml
    ```
    

- 在 Pod 规约中定义环境变量。
    
    [`pods/pod-multiple-configmap-env-variable.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-multiple-configmap-env-variable.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-multiple-configmap-env-variable.yaml to clipboard")
    
    ```yaml
    apiVersion: v1
      kind: Pod
      metadata:
        name: dapi-test-pod
      spec:
        containers:
          - name: test-container
            image: registry.k8s.io/busybox
            command: [ "/bin/sh", "-c", "env" ]
            env:
              - name: SPECIAL_LEVEL_KEY
                valueFrom:
                  configMapKeyRef:
                    name: special-config
                    key: special.how
              - name: LOG_LEVEL
                valueFrom:
                  configMapKeyRef:
                    name: env-config
                    key: log_level
        restartPolicy: Never
      
    ```
    
    创建 Pod:
    
    ```shell
    kubectl create -f https://kubernetes.io/examples/pods/pod-multiple-configmap-env-variable.yaml
    ```
    
    现在，Pod 的输出包含环境变量 `SPECIAL_LEVEL_KEY=very` 和 `LOG_LEVEL=INFO`。
    
    一旦你乐意继续前进，删除该 Pod：
    
    ```shell
    kubectl delete pod dapi-test-pod --now
    ```
    

### 将 ConfigMap 中的所有键值对配置为容器环境变量[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#configure-all-key-value-pairs-in-a-configmap-as-container-environment-variables)

- 创建一个包含多个键值对的 ConfigMap。
    
    [`configmap/configmap-multikeys.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/configmap/configmap-multikeys.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy configmap/configmap-multikeys.yaml to clipboard")
    
    ```yaml
    apiVersion: v1
      kind: ConfigMap
      metadata:
        name: special-config
        namespace: default
      data:
        SPECIAL_LEVEL: very
        SPECIAL_TYPE: charm
      
    ```
    
    创建 ConfigMap:
    
    ```shell
    kubectl create -f https://kubernetes.io/examples/configmap/configmap-multikeys.yaml
    ```
    

- 使用 `envFrom` 将所有 ConfigMap 的数据定义为容器环境变量，ConfigMap 中的键成为 Pod 中的环境变量名称。
    
    [`pods/pod-configmap-envFrom.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-configmap-envFrom.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-configmap-envFrom.yaml to clipboard")
    
    ```yaml
    apiVersion: v1
      kind: Pod
      metadata:
        name: dapi-test-pod
      spec:
        containers:
          - name: test-container
            image: registry.k8s.io/busybox
            command: [ "/bin/sh", "-c", "env" ]
            envFrom:
            - configMapRef:
                name: special-config
        restartPolicy: Never
      
    ```
    
    创建 Pod:
    
    ```shell
    kubectl create -f https://kubernetes.io/examples/pods/pod-configmap-envFrom.yaml
    ```
    
    现在，Pod 的输出包含环境变量 `SPECIAL_LEVEL=very` 和 `SPECIAL_TYPE=charm`。
    
    一旦你乐意继续前进，删除该 Pod：
    
    ```shell
    kubectl delete pod dapi-test-pod --now
    ```
    

### 在 Pod 命令中使用 ConfigMap 定义的环境变量[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#use-configmap-defined-environment-variables-in-pod-commands)

你可以使用 `$(VAR_NAME)` Kubernetes 替换语法在容器的 `command` 和 `args` 属性中使用 ConfigMap 定义的环境变量。

例如，以下 Pod 清单：

[`pods/pod-configmap-env-var-valueFrom.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-configmap-env-var-valueFrom.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-configmap-env-var-valueFrom.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox
      command: [ "/bin/echo", "$(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
      env:
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_LEVEL
        - name: SPECIAL_TYPE_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_TYPE
  restartPolicy: Never
```

通过运行下面命令创建该 Pod：

```shell
kubectl create -f https://kubernetes.io/examples/pods/pod-configmap-env-var-valueFrom.yaml
```

此 Pod 在 `test-container` 容器中产生以下输出:

```
very charm
```

一旦你乐意继续前进，删除该 Pod：

```shell
kubectl delete pod dapi-test-pod --now
```

### 将 ConfigMap 数据添加到一个卷中[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#add-configmap-data-to-a-volume)

如基于文件创建 [ConfigMap](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-files) 中所述，当你使用 `--from-file` 创建 ConfigMap 时，文件名成为存储在 ConfigMap 的 `data` 部分中的键， 文件内容成为键对应的值。

本节中的示例引用了一个名为 `special-config` 的 ConfigMap：

[`configmap/configmap-multikeys.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/configmap/configmap-multikeys.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy configmap/configmap-multikeys.yaml to clipboard")

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  SPECIAL_LEVEL: very
  SPECIAL_TYPE: charm
```

创建 ConfigMap:

```shell
kubectl create -f https://kubernetes.io/examples/configmap/configmap-multikeys.yaml
```

#### 使用存储在 ConfigMap 中的数据填充卷[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#populate-a-volume-with-data-stored-in-a-configmap)

在 Pod 规约的 `volumes` 部分下添加 ConfigMap 名称。 这会将 ConfigMap 数据添加到 `volumeMounts.mountPath` 所指定的目录 （在本例中为 `/etc/config`）。 `command` 部分列出了名称与 ConfigMap 中的键匹配的目录文件。

[`pods/pod-configmap-volume.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-configmap-volume.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-configmap-volume.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox
      command: [ "/bin/sh", "-c", "ls /etc/config/" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        # 提供包含要添加到容器中的文件的 ConfigMap 的名称
        name: special-config
  restartPolicy: Never
```

创建 Pod:

```shell
kubectl create -f https://kubernetes.io/examples/pods/pod-configmap-volume.yaml
```

Pod 运行时，命令 `ls /etc/config/` 产生下面的输出：

```
SPECIAL_LEVEL
SPECIAL_TYPE
```

文本数据会展现为 UTF-8 字符编码的文件。如果使用其他字符编码， 可以使用 `binaryData`（详情参阅 [ConfigMap 对象](https://kubernetes.io/zh-cn/docs/concepts/configuration/configmap/#configmap-object)）。

**说明：**

如果该容器镜像的 `/etc/config` 目录中有一些文件，卷挂载将使该镜像中的这些文件无法访问。

一旦你乐意继续前进，删除该 Pod：

```shell
kubectl delete pod dapi-test-pod --now
```

#### 将 ConfigMap 数据添加到卷中的特定路径[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#add-configmap-data-to-a-specific-path-in-the-volume)

使用 `path` 字段为特定的 ConfigMap 项目指定预期的文件路径。 在这里，ConfigMap 中键 `SPECIAL_LEVEL` 的内容将挂载在 `config-volume` 卷中 `/etc/config/keys` 文件中。

[`pods/pod-configmap-volume-specific-key.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/pod-configmap-volume-specific-key.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/pod-configmap-volume-specific-key.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox
      command: [ "/bin/sh","-c","cat /etc/config/keys" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: special-config
        items:
        - key: SPECIAL_LEVEL
          path: keys
  restartPolicy: Never
```

创建 Pod：

```shell
kubectl create -f https://kubernetes.io/examples/pods/pod-configmap-volume-specific-key.yaml
```

当 Pod 运行时，命令 `cat /etc/config/keys` 产生以下输出：

```
very
```

**注意：**

如前，`/etc/config/` 目录中所有先前的文件都将被删除。

删除该 Pod:

```shell
kubectl delete pod dapi-test-pod --now
```

#### 映射键到指定路径并设置文件访问权限[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#project-keys-to-specific-paths-and-file-permissions)

你可以将指定键名投射到特定目录，也可以逐个文件地设定访问权限。 [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/#using-secrets-as-files-from-a-pod) 指南中为这一语法提供了解释。

#### 可选引用[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#optional-references)

ConfigMap 引用可以被标记为**可选**。 如果 ConfigMap 不存在，则挂载的卷将为空。 如果 ConfigMap 存在，但引用的键不存在，则挂载点下的路径将不存在。 有关更多信息，请参阅[可选 ConfigMap](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#optional-configmaps) 细节。

#### 挂载的 ConfigMap 会被自动更新[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#mounted-configMaps-are-updated-automatically)

当已挂载的 ConfigMap 被更新时，所投射的内容最终也会被更新。 这适用于 Pod 启动后可选引用的 ConfigMap 重新出现的情况。

Kubelet 在每次定期同步时都会检查所挂载的 ConfigMap 是否是最新的。 然而，它使用其基于 TTL 机制的本地缓存来获取 ConfigMap 的当前值。 因此，从 ConfigMap 更新到新键映射到 Pod 的总延迟可能与 kubelet 同步周期（默认为1分钟）+ kubelet 中 ConfigMap 缓存的 TTL（默认为1分钟）一样长。 你可以通过更新 Pod 的一个注解来触发立即刷新。

**说明：**

使用 ConfigMap 作为 [subPath](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#using-subpath) 卷的容器将不会收到 ConfigMap 更新。

### 了解 ConfigMap 和 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#understanding-configmaps-and-pods)

ConfigMap API 资源将配置数据存储为键值对。 数据可以在 Pod 中使用，也可以用来提供系统组件（如控制器）的配置。 ConfigMap 与 [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/) 类似， 但是提供的是一种处理不含敏感信息的字符串的方法。 用户和系统组件都可以在 ConfigMap 中存储配置数据。

**说明：**

ConfigMap 应该引用属性文件，而不是替换它们。可以将 ConfigMap 理解为类似于 Linux `/etc` 目录及其内容的东西。例如，如果你基于 ConfigMap 创建 [Kubernetes 卷](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/)，则 ConfigMap 中的每个数据项都由该数据卷中的某个独立的文件表示。

ConfigMap 的 `data` 字段包含配置数据。如下例所示，它可以简单 （如用 `--from-literal` 的单个属性定义）或复杂 （如用 `--from-file` 的配置文件或 JSON blob 定义）。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: 2016-02-18T19:14:38Z
  name: example-config
  namespace: default
data:
  # 使用 --from-literal 定义的简单属性
  example.property.1: hello
  example.property.2: world
  # 使用 --from-file 定义复杂属性的例子
  example.property.file: |-
    property.1=value-1
    property.2=value-2
    property.3=value-3    
```

当 `kubectl` 从非 ASCII 或 UTF-8 编码的输入创建 ConfigMap 时， 该工具将这些输入放入 ConfigMap 的 `binaryData` 字段，而不是 `data` 字段。 文本和二进制数据源都可以组合在一个 ConfigMap 中。

如果你想查看 ConfigMap 中的 `binaryData` 键（及其值）， 可以运行 `kubectl get configmap -o jsonpath='{.binaryData}' <name>`。

Pod 可以从使用 `data` 或 `binaryData` 的 ConfigMap 中加载数据。

#### 可选的 ConfigMap[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#optional-configmaps)

你可以在 Pod 规约中将对 ConfigMap 的引用标记为 **可选（optional）**。 如果 ConfigMap 不存在，那么它在 Pod 中为其提供数据的配置（例如：环境变量、挂载的卷）将为空。 如果 ConfigMap 存在，但引用的键不存在，那么数据也是空的。

例如，以下 Pod 规约将 ConfigMap 中的环境变量标记为可选：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: gcr.io/google_containers/busybox
      command: ["/bin/sh", "-c", "env"]
      env:
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              name: a-config
              key: akey
              optional: true # 将环境变量标记为可选
  restartPolicy: Never
```

当你运行这个 Pod 并且名称为 `a-config` 的 ConfigMap 不存在时，输出空值。 当你运行这个 Pod 并且名称为 `a-config` 的 ConfigMap 存在， 但是在 ConfigMap 中没有名称为 `akey` 的键时，控制台输出也会为空。 如果你确实在名为 `a-config` 的 ConfigMap 中为 `akey` 设置了键值， 那么这个 Pod 会打印该值，然后终止。

你也可以在 Pod 规约中将 ConfigMap 提供的卷和文件标记为可选。 此时 Kubernetes 将总是为卷创建挂载路径，即使引用的 ConfigMap 或键不存在。 例如，以下 Pod 规约将所引用得 ConfigMap 的卷标记为可选：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: gcr.io/google_containers/busybox
      command: ["/bin/sh", "-c", "ls /etc/config"]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: no-config
        optional: true # 将引用的 ConfigMap 的卷标记为可选
  restartPolicy: Never
```

#### 挂载的 ConfigMap 将被自动更新[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#mounted-configmaps-are-updated-automatically)

当某个已被挂载的 ConfigMap 被更新，所投射的内容最终也会被更新。 对于 Pod 已经启动之后所引用的、可选的 ConfigMap 才出现的情形， 这一动态更新现象也是适用的。

kubelet 在每次周期性同步时都会检查已挂载的 ConfigMap 是否是最新的。 但是，它使用其本地的基于 TTL 的缓存来获取 ConfigMap 的当前值。 因此，从更新 ConfigMap 到将新键映射到 Pod 的总延迟可能等于 kubelet 同步周期 （默认 1 分钟） + ConfigMap 在 kubelet 中缓存的 TTL（默认 1 分钟）。

**说明：**

使用 ConfigMap 作为 [subPath](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#using-subpath) 的数据卷将不会收到 ConfigMap 更新。

#### 限制[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#restrictions)

- 在 Pod 规约中引用某个 `ConfigMap` 之前，必须先创建这个对象， 或者在 Pod 规约中将 ConfigMap 标记为 `optional`（请参阅[可选的 ConfigMaps](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#optional-configmaps)）。 如果所引用的 ConfigMap 不存在，并且没有将应用标记为 `optional` 则 Pod 将无法启动。 同样，引用 ConfigMap 中不存在的主键也会令 Pod 无法启动，除非你将 Configmap 标记为 `optional`。

- 如果你使用 `envFrom` 来基于 ConfigMap 定义环境变量，那么无效的键将被忽略。 Pod 可以被启动，但无效名称将被记录在事件日志中（`InvalidVariableNames`）。 日志消息列出了每个被跳过的键。例如:
    
    ```shell
    kubectl get events
    ```
    
    输出与此类似:
    
    ```
    LASTSEEN FIRSTSEEN COUNT NAME          KIND  SUBOBJECT  TYPE      REASON                            SOURCE                MESSAGE
    0s       0s        1     dapi-test-pod Pod              Warning   InvalidEnvironmentVariableNames   {kubelet, 127.0.0.1}  Keys [1badkey, 2alsobad] from the EnvFrom configMap default/myconfig were skipped since they are considered invalid environment variable names.
    ```
    

- ConfigMap 位于确定的[名字空间](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/namespaces/)中。 每个 ConfigMap 只能被同一名字空间中的 Pod 引用.

- 你不能将 ConfigMap 用于[静态 Pod](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/)， 因为 Kubernetes 不支持这种用法。

### 清理现场[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#%E6%B8%85%E7%90%86%E7%8E%B0%E5%9C%BA)

删除你创建那些的 ConfigMap 和 Pod：

```bash
kubectl delete configmaps/game-config configmaps/game-config-2 configmaps/game-config-3 \
               configmaps/game-config-env-file
kubectl delete pod dapi-test-pod --now

# 你可能已经删除了下一组内容
kubectl delete configmaps/special-config configmaps/env-config
kubectl delete configmap -l 'game-config in (config-4,config-5)’
```

如果你创建了一个目录 `configure-pod-container` 并且不再需要它，你也应该删除这个目录， 或者将该目录移动到回收站/删除文件的位置。

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- 浏览[使用 ConfigMap 配置 Redis](https://kubernetes.io/zh-cn/docs/tutorials/configuration/configure-redis-using-configmap/) 真实示例。
## [在 Pod 中的容器之间共享进程命名空间](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/share-process-namespace/)
此页面展示如何为 Pod 配置进程命名空间共享。 当启用进程命名空间共享时，容器中的进程对同一 Pod 中的所有其他容器都是可见的。

你可以使用此功能来配置协作容器，比如日志处理 sidecar 容器， 或者对那些不包含诸如 shell 等调试实用工具的镜像进行故障排查。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/share-process-namespace/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 配置 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/share-process-namespace/#%E9%85%8D%E7%BD%AE-pod)

使用 Pod `.spec` 中的 `shareProcessNamespace` 字段可以启用进程命名空间共享。例如：

[`pods/share-process-namespace.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/share-process-namespace.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/share-process-namespace.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  shareProcessNamespace: true
  containers:
  - name: nginx
    image: nginx
  - name: shell
    image: busybox:1.28
    securityContext:
      capabilities:
        add:
        - SYS_PTRACE
    stdin: true
    tty: true
```

1. 在集群中创建 `nginx` Pod：
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/share-process-namespace.yaml
    ```
    

2. 获取容器 `shell`，执行 `ps`：
    
    ```shell
    kubectl attach -it nginx -c shell
    ```
    
    如果没有看到命令提示符，请按 enter 回车键。在容器 shell 中：
    
    ```shell
    # 在 “shell” 容器中运行以下命令
    ps ax
    ```
    
    输出类似于：
    
    ```none
    PID   USER     TIME  COMMAND
        1 root      0:00 /pause
        8 root      0:00 nginx: master process nginx -g daemon off;
       14 101       0:00 nginx: worker process
       15 root      0:00 sh
       21 root      0:00 ps ax
    ```
    

你可以在其他容器中对进程发出信号。例如，发送 `SIGHUP` 到 `nginx` 以重启工作进程。 此操作需要 `SYS_PTRACE` 权能。

```shell
# 在 “shell” 容器中运行以下命令
kill -HUP 8   # 如有必要，更改 “8” 以匹配 nginx 领导进程的 PID
ps ax
```

输出类似于：

```none
PID   USER     TIME  COMMAND
    1 root      0:00 /pause
    8 root      0:00 nginx: master process nginx -g daemon off;
   15 root      0:00 sh
   22 101       0:00 nginx: worker process
   23 root      0:00 ps ax
```

甚至可以使用 `/proc/$pid/root` 链接访问另一个容器的文件系统。

```shell
# 在 “shell” 容器中运行以下命令
# 如有必要，更改 “8” 为 Nginx 进程的 PID
head /proc/8/root/etc/nginx/nginx.conf
```

输出类似于：

```none
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
```

### 理解进程命名空间共享[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/share-process-namespace/#%E7%90%86%E8%A7%A3%E8%BF%9B%E7%A8%8B%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4%E5%85%B1%E4%BA%AB)

Pod 共享许多资源，因此它们共享进程命名空间是很有意义的。 不过，有些容器可能希望与其他容器隔离，因此了解这些差异很重要:

1. **容器进程不再具有 PID 1。** 在没有 PID 1 的情况下，一些容器拒绝启动 （例如，使用 `systemd` 的容器)，或者拒绝执行 `kill -HUP 1` 之类的命令来通知容器进程。 在具有共享进程命名空间的 Pod 中，`kill -HUP 1` 将通知 Pod 沙箱（在上面的例子中是 `/pause`）。

2. **进程对 Pod 中的其他容器可见。** 这包括 `/proc` 中可见的所有信息， 例如作为参数或环境变量传递的密码。这些仅受常规 Unix 权限的保护。

3. **容器文件系统通过 `/proc/$pid/root` 链接对 Pod 中的其他容器可见。** 这使调试更加容易， 但也意味着文件系统安全性只受文件系统权限的保护。
## [为 Pod 配置用户名字空间](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/user-namespaces/)
**特性状态：** `Kubernetes v1.25 [alpha]`

本页展示如何为无状态 Pod 配置用户名字空间。可以将容器内的用户与主机上的用户隔离开来。

在容器中以 root 用户运行的进程可以以不同的（非 root）用户在宿主机上运行；换句话说， 进程在用户名字空间内部拥有执行操作的全部特权，但在用户名字空间外部并没有执行操作的特权。

你可以使用这个特性来减少有害的容器对同一宿主机上其他容器的影响。 [有些安全脆弱性问题](https://github.com/kubernetes/enhancements/tree/217d790720c5aef09b8bd4d6ca96284a0affe6c2/keps/sig-node/127-user-namespaces#motivation)被评为 **HIGH** or **CRITICAL**，但当用户名字空间被启用时， 它们是无法被利用的。相信用户名字空间也能减轻一些未来的漏洞的影响。

在不使用用户名字空间的情况下，对于以 root 用户运行的容器而言，发生容器逃逸时， 容器将拥有在宿主机上的 root 特权。如果容器被赋予了某些权限，则这些权限在宿主机上同样有效。 当使用用户名字空间时这些都不可能发生。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/user-namespaces/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你的 Kubernetes 服务器版本必须不低于版本 v1.25. 要获知版本信息，请输入 `kubectl version`.

🛇 本条目指向第三方项目或产品，而该项目（产品）不是 Kubernetes 的一部分。[更多信息](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/user-namespaces/#third-party-content-disclaimer)

- 节点上的操作系统必须为 Linux
- 你需要在宿主机上执行命令
- 你需要能够通过 exec 操作进入 Pod
- 特性 `UserNamespacesStatelessPodsSupport` 需要被启用。

此外, 需要[容器运行时](https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes)提供相应的支持， 才能将此特性与 Kubernetes 无状态 Pod 一起使用：

- CRI-O: v1.25 支持用户名字空间。

请注意 **如果你的容器运行时环境不支持用户名字空间，字段 `pod.spec` 将被忽略， 并且系统会在没有用户名字空间的环境中创建 Pod。**

### 运行一个使用用户名字空间的 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/user-namespaces/#create-pod)

为一个无状态的 Pod 启用用户名字空间需要设置 `.spec` 的 `hostUsers` 字段 为 `false`. 例如:

[`pods/user-namespaces-stateless.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/pods/user-namespaces-stateless.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy pods/user-namespaces-stateless.yaml to clipboard")

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: userns
spec:
  hostUsers: false
  containers:
  - name: shell
    command: ["sleep", "infinity"]
    image: debian
```

1. 在你的集群上创建 Pod：
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/user-namespaces-stateless.yaml
    ```
    

2. 挂接到容器上并执行 `readlink /proc/self/ns/user`：
    
    ```shell
    kubectl attach -it userns bash
    ```
    

执行命令的输出类似于：

```none
readlink /proc/self/ns/user
user:[4026531837]
cat /proc/self/uid_map
0          0 4294967295
```

然后，在主机中打开一个 Shell 并运行相同的命令。

输出一定是不同的。这意味着主机和 Pod 使用不同的用户名字空间。当未启用用户名字空间时， 宿主机和 Pod 使用相同的用户名字空间。

如果你在用户名字空间中运行 kubelet，则需要将在 Pod 中运行命令的输出与在主机中运行的输出进行比较：

```none
readlink /proc/$pid/ns/user
user:[4026534732]
```

使用 kubelet 的进程号代替 `$pid`
## [创建静态 Pod](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/)
**静态 Pod** 在指定的节点上由 kubelet 守护进程直接管理，不需要 [API 服务器](https://kubernetes.io/zh-cn/docs/concepts/overview/components/#kube-apiserver)监管。 与由控制面管理的 Pod（例如，[Deployment](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/)） 不同；kubelet 监视每个静态 Pod（在它失败之后重新启动）。

静态 Pod 始终都会绑定到特定节点的 [Kubelet](https://kubernetes.io/docs/reference/generated/kubelet) 上。

kubelet 会尝试通过 Kubernetes API 服务器为每个静态 Pod 自动创建一个[镜像 Pod](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-mirror-pod)。 这意味着节点上运行的静态 Pod 对 API 服务来说是可见的，但是不能通过 API 服务器来控制。 Pod 名称将把以连字符开头的节点主机名作为后缀。

**说明：**

如果你在运行一个 Kubernetes 集群，并且在每个节点上都运行一个静态 Pod， 就可能需要考虑使用 [DaemonSet](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/daemonset/) 替代这种方式。

**说明：**

静态 Pod 的 `spec` 不能引用其他 API 对象 （如：[ServiceAccount](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-service-account/)、 [ConfigMap](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/)、 [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/) 等）。

**说明：**

静态 Pod 不支持[临时容器](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/ephemeral-containers/)。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

本文假定你在使用 [Docker](https://docs.docker.com/engine/) 来运行 Pod， 并且你的节点是运行着 Fedora 操作系统。 其它发行版或者 Kubernetes 部署版本上操作方式可能不一样。

### 创建静态 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#static-pod-creation)

可以通过[文件系统上的配置文件](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#configuration-files)或者 [Web 网络上的配置文件](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#pods-created-via-http)来配置静态 Pod。

#### 文件系统上的静态 Pod 声明文件[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#configuration-files)

声明文件是标准的 Pod 定义文件，以 JSON 或者 YAML 格式存储在指定目录。路径设置在 [Kubelet 配置文件](https://kubernetes.io/zh-cn/docs/reference/config-api/kubelet-config.v1beta1/)的 `staticPodPath: <目录>` 字段，kubelet 会定期的扫描这个文件夹下的 YAML/JSON 文件来创建/删除静态 Pod。 注意 kubelet 扫描目录的时候会忽略以点开头的文件。

例如：下面是如何以静态 Pod 的方式启动一个简单 web 服务：

1. 选择一个要运行静态 Pod 的节点。在这个例子中选择 `my-node1`。
    
    ```shell
    ssh my-node1
    ```
    

2. 选择一个目录，比如在 `/etc/kubernetes/manifests` 目录来保存 Web 服务 Pod 的定义文件，例如 `/etc/kubernetes/manifests/static-web.yaml`：
    
    ```shell
    # 在 kubelet 运行的节点上执行以下命令
    mkdir -p /etc/kubernetes/manifests/
    cat <<EOF >/etc/kubernetes/manifests/static-web.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: static-web
      labels:
        role: myrole
    spec:
      containers:
        - name: web
          image: nginx
          ports:
            - name: web
              containerPort: 80
              protocol: TCP
    EOF
    ```
    

3. 配置这个节点上的 kubelet，使用这个参数执行 `--pod-manifest-path=/etc/kubelet.d/`。 在 Fedora 上编辑 `/etc/kubernetes/kubelet` 以包含下面这行：
    
    ```
    KUBELET_ARGS="--cluster-dns=10.254.0.10 --cluster-domain=kube.local --pod-manifest-path=/etc/kubernetes/manifests/"
    ```
    
    或者在 [Kubelet 配置文件](https://kubernetes.io/zh-cn/docs/reference/config-api/kubelet-config.v1beta1/)中添加 `staticPodPath: <目录>`字段。
    

4. 重启 kubelet。在 Fedora 上，你将使用下面的命令：
    
    ```shell
    # 在 kubelet 运行的节点上执行以下命令
    systemctl restart kubelet
    ```
    

#### Web 网上的静态 Pod 声明文件[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#pods-created-via-http)

Kubelet 根据 `--manifest-url=<URL>` 参数的配置定期的下载指定文件，并且转换成 JSON/YAML 格式的 Pod 定义文件。 与[文件系统上的清单文件](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#configuration-files)使用方式类似，kubelet 调度获取清单文件。 如果静态 Pod 的清单文件有改变，kubelet 会应用这些改变。

按照下面的方式来：

1. 创建一个 YAML 文件，并保存在 Web 服务器上，这样你就可以将该文件的 URL 传递给 kubelet。
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: static-web
      labels:
        role: myrole
    spec:
      containers:
        - name: web
          image: nginx
          ports:
            - name: web
              containerPort: 80
              protocol: TCP
    ```
    

2. 通过在选择的节点上使用 `--manifest-url=<manifest-url>` 配置运行 kubelet。 在 Fedora 添加下面这行到 `/etc/kubernetes/kubelet`：
    
    ```
    KUBELET_ARGS="--cluster-dns=10.254.0.10 --cluster-domain=kube.local --manifest-url=<manifest-url>"
    ```
    

3. 重启 kubelet。在 Fedora 上，你将运行如下命令：
    
    ```shell
    # 在 kubelet 运行的节点上执行以下命令
    systemctl restart kubelet
    ```
    

### 观察静态 Pod 的行为[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#behavior-of-static-pods)

当 kubelet 启动时，会自动启动所有定义的静态 Pod。 当定义了一个静态 Pod 并重新启动 kubelet 时，新的静态 Pod 就应该已经在运行了。

可以在节点上运行下面的命令来查看正在运行的容器（包括静态 Pod）：

```shell
# 在 kubelet 运行的节点上执行以下命令
crictl ps
```

输出可能会像这样：

```console
CONTAINER       IMAGE                                 CREATED           STATE      NAME    ATTEMPT    POD ID
129fd7d382018   docker.io/library/nginx@sha256:...    11 minutes ago    Running    web     0          34533c6729106
```

**说明：**

`crictl` 会输出镜像 URI 和 SHA-256 校验和。`NAME` 看起来像： `docker.io/library/nginx@sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31`。

可以在 API 服务上看到镜像 Pod：

```shell
kubectl get pods
```

```
NAME         READY   STATUS    RESTARTS        AGE
static-web   1/1     Running   0               2m
```

**说明：**

要确保 kubelet 在 API 服务上有创建镜像 Pod 的权限。如果没有，创建请求会被 API 服务拒绝。

静态 Pod 上的[标签](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/labels/)被传播到镜像 Pod。 你可以通过[选择算符](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/labels/)使用这些标签。

如果你用 `kubectl` 从 API 服务上删除镜像 Pod，kubelet **不会**移除静态 Pod：

```shell
kubectl delete pod static-web
```

```
pod "static-web" deleted
```

可以看到 Pod 还在运行：

```shell
kubectl get pods
```

```
NAME         READY   STATUS    RESTARTS   AGE
static-web   1/1     Running   0          4s
```

回到 kubelet 运行所在的节点上，你可以手动停止容器。 可以看到过了一段时间后 kubelet 会发现容器停止了并且会自动重启 Pod：

```shell
# 在 kubelet 运行的节点上执行以下命令
# 把 ID 换为你的容器的 ID
crictl stop 129fd7d382018
sleep 20
crictl ps
```

```console
CONTAINER       IMAGE                                 CREATED           STATE      NAME    ATTEMPT    POD ID
89db4553e1eeb   docker.io/library/nginx@sha256:...    19 seconds ago    Running    web     1          34533c6729106
```

一旦你找到合适的容器，你就可以使用 `crictl` 获取该容器的日志。

```shell
# 在容器运行所在的节点上执行以下命令
crictl logs <container_id>
```

```console
10.240.0.48 - - [16/Nov/2022:12:45:49 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
10.240.0.48 - - [16/Nov/2022:12:45:50 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
10.240.0.48 - - [16/Nove/2022:12:45:51 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.47.0" "-"
```

若要找到如何使用 `crictl` 进行调试的更多信息， 请访问[使用 crictl 对 Kubernetes 节点进行调试](https://kubernetes.io/zh-cn/docs/tasks/debug/debug-cluster/crictl/)。

### 动态增加和删除静态 Pod[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#dynamic-addition-and-removal-of-static-pods)

运行中的 kubelet 会定期扫描配置的目录（比如例子中的 `/etc/kubernetes/manifests` 目录）中的变化， 并且根据文件中出现/消失的 Pod 来添加/删除 Pod。

```shell
# 这里假定你在用主机文件系统上的静态 Pod 配置文件
# 在容器运行所在的节点上执行以下命令
mv /etc/kubernetes/manifests/static-web.yaml /tmp
sleep 20
crictl ps
# 可以看到没有 nginx 容器在运行
mv /tmp/static-web.yaml  /etc/kubernetes/manifests/
sleep 20
crictl ps
```

```console
CONTAINER       IMAGE                                 CREATED           STATE      NAME    ATTEMPT    POD ID
f427638871c35   docker.io/library/nginx@sha256:...    19 seconds ago    Running    web     1          34533c6729106
```

### 接下来[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/static-pod/#%E6%8E%A5%E4%B8%8B%E6%9D%A5)

- [为控制面组件生成静态 Pod 清单](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/implementation-details/#generate-static-pod-manifests-for-control-plane-components)
- [为本地 etcd 生成静态 Pod 清单](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/implementation-details/#generate-static-pod-manifest-for-local-etcd)
- [使用 `crictl` 对 Kubernetes 节点进行调试](https://kubernetes.io/docs/tasks/debug/debug-cluster/crictl/)
- 更多细节请参阅 [`crictl`](https://github.com/kubernetes-sigs/cri-tools)
- [从 `docker` CLI 命令映射到 `crictl`](https://kubernetes.io/zh-cn/docs/reference/tools/map-crictl-dockercli/)
- [将 etcd 实例设置为由 kubelet 管理的静态 Pod](https://kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)
## [将 Docker Compose 文件转换为 Kubernetes 资源](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/)
Kompose 是什么？它是一个转换工具，可将 compose （即 Docker Compose）所组装的所有内容转换成容器编排器（Kubernetes 或 OpenShift）可识别的形式。

更多信息请参考 Kompose 官网 [http://kompose.io](http://kompose.io/)。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

要获知版本信息，请输入 `kubectl version`.

### 安装 Kompose[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#install-kompose)

我们有很多种方式安装 Kompose。首选方式是从最新的 GitHub 发布页面下载二进制文件。

- [GitHub 下载](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#install-ways-0)
- [基于源代码构建](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#install-ways-1)
- [CentOS 包](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#install-ways-2)
- [Fedora 包](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#install-ways-3)
- [Homebrew (macOS)](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#install-ways-4)

Kompose 通过 GitHub 发布，发布周期为三星期。 你可以在 [GitHub 发布页面](https://github.com/kubernetes/kompose/releases)上看到所有当前版本。

```shell
# Linux
curl -L https://github.com/kubernetes/kompose/releases/download/v1.26.0/kompose-linux-amd64 -o kompose

# macOS
curl -L https://github.com/kubernetes/kompose/releases/download/v1.26.0/kompose-darwin-amd64 -o kompose

# Windows
curl -L https://github.com/kubernetes/kompose/releases/download/v1.26.0/kompose-windows-amd64.exe -o kompose.exe

chmod +x kompose
sudo mv ./kompose /usr/local/bin/kompose
```

或者，你可以下载 [tar 包](https://github.com/kubernetes/kompose/releases)。

用 `go get` 命令从主分支拉取最新的开发变更的方法安装 Kompose。

```shell
go get -u github.com/kubernetes/kompose
```

Kompose 位于 [EPEL](https://fedoraproject.org/wiki/EPEL) CentOS 代码仓库。 如果你还没有安装并启用 [EPEL](https://fedoraproject.org/wiki/EPEL) 代码仓库， 请运行命令 `sudo yum install epel-release`。

如果你的系统中已经启用了 [EPEL](https://fedoraproject.org/wiki/EPEL)， 你就可以像安装其他软件包一样安装 Kompose。

```bash
sudo yum -y install kompose
```

Kompose 位于 Fedora 24、25 和 26 的代码仓库。你可以像安装其他软件包一样安装 Kompose。

```bash
sudo dnf -y install kompose
```

在 macOS 上你可以通过 [Homebrew](https://brew.sh/) 安装 Kompose 的最新版本：

```bash
brew install kompose
```

### 使用 Kompose[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#use-kompose)

只需几步，我们就把你从 Docker Compose 带到 Kubernetes。 你只需要一个现有的 `docker-compose.yml` 文件。

1. 进入 `docker-compose.yml` 文件所在的目录。如果没有，请使用下面这个进行测试。
    
    ```yaml
    version: "2"
    
    services:
    
      redis-master:
        image: registry.k8s.io/redis:e2e
        ports:
          - "6379"
    
      redis-slave:
        image: gcr.io/google_samples/gb-redisslave:v3
        ports:
          - "6379"
        environment:
          - GET_HOSTS_FROM=dns
    
      frontend:
        image: gcr.io/google-samples/gb-frontend:v4
        ports:
          - "80:80"
        environment:
          - GET_HOSTS_FROM=dns
        labels:
          kompose.service.type: LoadBalancer
    ```
    

2. 要将 `docker-compose.yml` 转换为 `kubectl` 可用的文件，请运行 `kompose convert` 命令进行转换，然后运行 `kubectl apply -f <output file>` 进行创建。
    
    ```bash
    kompose convert                           
    ```
    
    输出类似于：
    
    ```none
    INFO Kubernetes file "frontend-tcp-service.yaml" created 
    INFO Kubernetes file "redis-master-service.yaml" created 
    INFO Kubernetes file "redis-slave-service.yaml" created 
    INFO Kubernetes file "frontend-deployment.yaml" created 
    INFO Kubernetes file "redis-master-deployment.yaml" created 
    INFO Kubernetes file "redis-slave-deployment.yaml" created
    ```
    
    ```bash
    kubectl apply -f frontend-tcp-service.yaml,redis-master-service.yaml,redis-slave-service.yaml,frontend-deployment.yaml,redis-master-deployment.yaml,redis-slave-deployment.yaml
    ```
    
    输出类似于：
    
    ```none
    service/frontend-tcp created
    service/redis-master created
    service/redis-slave created
    deployment.apps/frontend created
    deployment.apps/redis-master created
    deployment.apps/redis-slave created
    ```
    
    你部署的应用在 Kubernetes 中运行起来了。
    

3. 访问你的应用。
    
    如果你在开发过程中使用 `minikube`，请执行：
    
    ```bash
    minikube service frontend
    ```
    
    否则，我们要查看一下你的服务使用了什么 IP！
    
    ```sh
    kubectl describe svc frontend
    ```
    
    ```none
    Name:                     frontend-tcp
    Namespace:                default
    Labels:                   io.kompose.service=frontend-tcp
    Annotations:              kompose.cmd: kompose convert
                              kompose.service.type: LoadBalancer
                              kompose.version: 1.26.0 (40646f47)
    Selector:                 io.kompose.service=frontend
    Type:                     LoadBalancer
    IP Family Policy:         SingleStack
    IP Families:              IPv4
    IP:                       10.43.67.174
    IPs:                      10.43.67.174
    Port:                     80  80/TCP
    TargetPort:               80/TCP
    NodePort:                 80  31254/TCP
    Endpoints:                10.42.0.25:80
    Session Affinity:         None
    External Traffic Policy:  Cluster
    Events:
      Type    Reason                Age   From                Message
      ----    ------                ----  ----                -------
      Normal  EnsuringLoadBalancer  62s   service-controller  Ensuring load balancer
      Normal  AppliedDaemonSet      62s   service-controller  Applied LoadBalancer DaemonSet kube-system/svclb-frontend-tcp-9362d276
    ```
    
    如果你使用的是云驱动，你的 IP 将在 `LoadBalancer Ingress` 字段给出。
    
    ```sh
    curl http://192.0.2.89
    ```
    

4. 清理。
    
    你完成示例应用 Deployment 的测试之后，只需在 Shell 中运行以下命令，就能删除用过的资源。
    
    ```sh
    kubectl delete -f frontend-tcp-service.yaml,redis-master-service.yaml,redis-slave-service.yaml,frontend-deployment.yaml,redis-master-deployment.yaml,redis-slave-deployment.yaml
    ```
    

### 用户指南[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#user-guide)

- CLI
    
    - [`kompose convert`](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#kompose-convert)
- 文档
    
    - [其他转换方式](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#alternative-conversions)
    - [标签](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#labels)
    - [重启](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#restart)
    - [Docker Compose 版本](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#docker-compose-versions)

Kompose 支持两种驱动：OpenShift 和 Kubernetes。 你可以通过全局选项 `--provider` 选择驱动。如果没有指定， 会将 Kubernetes 作为默认驱动。

### `kompose convert`[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#kompose-convert)

Kompose 支持将 V1、V2 和 V3 版本的 Docker Compose 文件转换为 Kubernetes 和 OpenShift 资源对象。

#### Kubernetes `kompose convert` 示例[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#kubernetes-kompose-convert-example)

```shell
kompose --file docker-voting.yml convert
```

```none
WARN Unsupported key networks - ignoring
WARN Unsupported key build - ignoring
INFO Kubernetes file "worker-svc.yaml" created
INFO Kubernetes file "db-svc.yaml" created
INFO Kubernetes file "redis-svc.yaml" created
INFO Kubernetes file "result-svc.yaml" created
INFO Kubernetes file "vote-svc.yaml" created
INFO Kubernetes file "redis-deployment.yaml" created
INFO Kubernetes file "result-deployment.yaml" created
INFO Kubernetes file "vote-deployment.yaml" created
INFO Kubernetes file "worker-deployment.yaml" created
INFO Kubernetes file "db-deployment.yaml" created
```

```shell
ls
```

```none
db-deployment.yaml  docker-compose.yml         docker-gitlab.yml  redis-deployment.yaml  result-deployment.yaml  vote-deployment.yaml  worker-deployment.yaml
db-svc.yaml         docker-voting.yml          redis-svc.yaml     result-svc.yaml        vote-svc.yaml           worker-svc.yaml
```

你也可以同时提供多个 docker-compose 文件进行转换：

```shell
kompose -f docker-compose.yml -f docker-guestbook.yml convert
```

```none
INFO Kubernetes file "frontend-service.yaml" created         
INFO Kubernetes file "mlbparks-service.yaml" created         
INFO Kubernetes file "mongodb-service.yaml" created          
INFO Kubernetes file "redis-master-service.yaml" created     
INFO Kubernetes file "redis-slave-service.yaml" created      
INFO Kubernetes file "frontend-deployment.yaml" created      
INFO Kubernetes file "mlbparks-deployment.yaml" created      
INFO Kubernetes file "mongodb-deployment.yaml" created       
INFO Kubernetes file "mongodb-claim0-persistentvolumeclaim.yaml" created
INFO Kubernetes file "redis-master-deployment.yaml" created  
INFO Kubernetes file "redis-slave-deployment.yaml" created   
```

```shell
ls
```

```none
mlbparks-deployment.yaml  mongodb-service.yaml                       redis-slave-service.jsonmlbparks-service.yaml  
frontend-deployment.yaml  mongodb-claim0-persistentvolumeclaim.yaml  redis-master-service.yaml
frontend-service.yaml     mongodb-deployment.yaml                    redis-slave-deployment.yaml
redis-master-deployment.yaml
```

当提供多个 docker-compose 文件时，配置将会合并。任何通用的配置都将被后续文件覆盖。

#### OpenShift `kompose convert` 示例[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#openshift-kompose-convert-example)

```sh
kompose --provider openshift --file docker-voting.yml convert
```

```none
WARN [worker] Service cannot be created because of missing port.
INFO OpenShift file "vote-service.yaml" created             
INFO OpenShift file "db-service.yaml" created               
INFO OpenShift file "redis-service.yaml" created            
INFO OpenShift file "result-service.yaml" created           
INFO OpenShift file "vote-deploymentconfig.yaml" created    
INFO OpenShift file "vote-imagestream.yaml" created         
INFO OpenShift file "worker-deploymentconfig.yaml" created  
INFO OpenShift file "worker-imagestream.yaml" created       
INFO OpenShift file "db-deploymentconfig.yaml" created      
INFO OpenShift file "db-imagestream.yaml" created           
INFO OpenShift file "redis-deploymentconfig.yaml" created   
INFO OpenShift file "redis-imagestream.yaml" created        
INFO OpenShift file "result-deploymentconfig.yaml" created  
INFO OpenShift file "result-imagestream.yaml" created  
```

kompose 还支持为服务中的构建指令创建 buildconfig。 默认情况下，它使用当前 git 分支的 remote 仓库作为源仓库，使用当前分支作为构建的源分支。 你可以分别使用 `--build-repo` 和 `--build-branch` 选项指定不同的源仓库和分支。

```sh
kompose --provider openshift --file buildconfig/docker-compose.yml convert
```

```none
WARN [foo] Service cannot be created because of missing port.
INFO OpenShift Buildconfig using git@github.com:rtnpro/kompose.git::master as source.
INFO OpenShift file "foo-deploymentconfig.yaml" created     
INFO OpenShift file "foo-imagestream.yaml" created          
INFO OpenShift file "foo-buildconfig.yaml" created
```

**说明：**

如果使用 `oc create -f` 手动推送 OpenShift 工件，则需要确保在构建配置工件之前推送 imagestream 工件，以解决 OpenShift 的这个问题： [https://github.com/openshift/origin/issues/4518](https://github.com/openshift/origin/issues/4518)。

### 其他转换方式[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#alternative-conversions)

默认的 `kompose` 转换会生成 yaml 格式的 Kubernetes [Deployment](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/) 和 [Service](https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/) 对象。 你可以选择通过 `-j` 参数生成 json 格式的对象。 你也可以替换生成 [Replication Controllers](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/replicationcontroller/) 对象、 [DaemonSet](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/daemonset/) 或 [Helm](https://github.com/helm/helm) Chart。

```sh
kompose convert -j
```

```none
INFO Kubernetes file "redis-svc.json" created
INFO Kubernetes file "web-svc.json" created
INFO Kubernetes file "redis-deployment.json" created
INFO Kubernetes file "web-deployment.json" created
```

`*-deployment.json` 文件中包含 Deployment 对象。

```sh
kompose convert --replication-controller
```

```none
INFO Kubernetes file "redis-svc.yaml" created
INFO Kubernetes file "web-svc.yaml" created
INFO Kubernetes file "redis-replicationcontroller.yaml" created
INFO Kubernetes file "web-replicationcontroller.yaml" created
```

`*-replicationcontroller.yaml` 文件包含 Replication Controller 对象。 如果你想指定副本数（默认为 1），可以使用 `--replicas` 参数： `kompose convert --replication-controller --replicas 3`

```shell
kompose convert --daemon-set
```

```none
INFO Kubernetes file "redis-svc.yaml" created
INFO Kubernetes file "web-svc.yaml" created
INFO Kubernetes file "redis-daemonset.yaml" created
INFO Kubernetes file "web-daemonset.yaml" created
```

`*-daemonset.yaml` 文件包含 DaemonSet 对象。

如果你想生成 [Helm](https://github.com/kubernetes/helm) 可用的 Chart， 只需简单的执行下面的命令：

```shell
kompose convert -c
```

```none
INFO Kubernetes file "web-svc.yaml" created
INFO Kubernetes file "redis-svc.yaml" created
INFO Kubernetes file "web-deployment.yaml" created
INFO Kubernetes file "redis-deployment.yaml" created
chart created in "./docker-compose/"
```

```shell
tree docker-compose/
```

```none
docker-compose
├── Chart.yaml
├── README.md
└── templates
    ├── redis-deployment.yaml
    ├── redis-svc.yaml
    ├── web-deployment.yaml
    └── web-svc.yaml
```

这个 Chart 结构旨在为构建 Helm Chart 提供框架。

### 标签[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#labels)

`kompose` 支持 `docker-compose.yml` 文件中用于 Kompose 的标签， 以便在转换时明确定义 Service 的行为。

- `kompose.service.type` 定义要创建的 Service 类型。例如：
    
    ```yaml
    version: "2"
    services:
      nginx:
        image: nginx
        dockerfile: foobar
        build: ./foobar
        cap_add:
          - ALL
        container_name: foobar
        labels:
          kompose.service.type: nodeport
    ```
    

- `kompose.service.expose` 定义是否允许从集群外部访问 Service。 如果该值被设置为 "true"，提供程序将自动设置端点， 对于任何其他值，该值将被设置为主机名。 如果在 Service 中定义了多个端口，则选择第一个端口作为公开端口。
    
    - 如果使用 Kubernetes 驱动，会有一个 Ingress 资源被创建，并且假定已经配置了相应的 Ingress 控制器。
    - 如果使用 OpenShift 驱动，则会有一个 route 被创建。
    
    例如：
    
    ```yaml
    version: "2"
    services:
      web:
        image: tuna/docker-counter23
        ports:
         - "5000:5000"
        links:
         - redis
        labels:
          kompose.service.expose: "counter.example.com"
      redis:
        image: redis:3.0
        ports:
         - "6379"
    ```
    

当前支持的选项有:

| 键 | 值 |
| --- | --- |
| kompose.service.type | nodeport / clusterip / loadbalancer |
| kompose.service.expose | true / hostname |

**说明：**

`kompose.service.type` 标签应该只用 `ports` 来定义，否则 `kompose` 会失败。

### 重启[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#restart)

如果你想创建没有控制器的普通 Pod，可以使用 docker-compose 的 `restart` 结构来指定这一行为。请参考下表了解 `restart` 的不同参数。

| `docker-compose` `restart` | 创建的对象 | Pod `restartPolicy` |
| --- | --- | --- |
| `""` | 控制器对象 | `Always` |
| `always` | 控制器对象 | `Always` |
| `on-failure` | Pod | `OnFailure` |
| `no` | Pod | `Never` |

**说明：**

控制器对象可以是 `deployment` 或 `replicationcontroller`。

例如，`pival` Service 将在这里变成 Pod。这个容器计算 `pi` 的取值。

```yaml
version: '2'

services:
  pival:
    image: perl
    command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
    restart: "on-failure"
```

#### 关于 Deployment Config 的提醒[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#warning-about-deployment-configurations)

如果 Docker Compose 文件中为服务声明了卷，Deployment（Kubernetes）或 DeploymentConfig（OpenShift）策略会从 “RollingUpdate”（默认）变为 “Recreate”。 这样做的目的是为了避免服务的多个实例同时访问卷。

如果 Docker Compose 文件中的服务名包含 `_`（例如 `web_service`）， 那么将会被替换为 `-`，服务也相应的会重命名（例如 `web-service`）。 Kompose 这样做的原因是 “Kubernetes” 不允许对象名称中包含 `_`。

请注意，更改服务名称可能会破坏一些 `docker-compose` 文件。

### Docker Compose 版本[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/translate-compose-kubernetes/#docker-compose-versions)

Kompose 支持的 Docker Compose 版本包括：1、2 和 3。 对 2.1 和 3.2 版本的支持还有限，因为它们还在实验阶段。

所有三个版本的兼容性列表， 请查看我们的[转换文档](https://github.com/kubernetes/kompose/blob/master/docs/conversion.md)， 文档中列出了所有不兼容的 Docker Compose 关键字。
## [通过配置内置准入控制器实施 Pod 安全标准](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-admission-controller/)
Kubernetes 提供一种内置的[准入控制器](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/admission-controllers/#podsecurity) 用来强制实施 [Pod 安全性标准](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards)。 你可以配置此准入控制器来设置集群范围的默认值和[豁免选项](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/#exemptions)。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-admission-controller/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

Pod 安全性准入（Pod Security Admission）在 Kubernetes v1.22 作为 Alpha 特性发布， 在 Kubernetes v1.23 中作为 Beta 特性默认可用。从 1.25 版本起， 此特性进阶至正式发布（Generally Available）。

要获知版本信息，请输入 `kubectl version`.

如果未运行 Kubernetes 1.26， 你可以切换到与当前运行的 Kubernetes 版本所对应的文档。

### 配置准入控制器[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-admission-controller/#configure-the-admission-controller)

**说明：**

`pod-security.admission.config.k8s.io/v1` 配置需要 v1.25+。 对于 v1.23 和 v1.24，使用 [v1beta1](https://v1-24.docs.kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-admission-controller/)。 对于 v1.22，使用 [v1alpha1](https://v1-22.docs.kubernetes.io/docs/tasks/configure-pod-container/enforce-standards-admission-controller/)。

```yaml
apiVersion: apiserver.config.k8s.io/v1 # 查阅兼容性说明
kind: AdmissionConfiguration
plugins:
- name: PodSecurity
  configuration:
    apiVersion: pod-security.admission.config.k8s.io/v1
    kind: PodSecurityConfiguration
    # 当未设置 mode 标签时会应用的默认设置
    #
    # level 标签必须是以下取值之一：
    # - "privileged" (默认)
    # - "baseline"
    # - "restricted"
    #
    # version 标签必须是如下取值之一：
    # - "latest" (默认) 
    # - 诸如 "v1.26" 这类版本号
    defaults:
      enforce: "privileged"
      enforce-version: "latest"
      audit: "privileged"
      audit-version: "latest"
      warn: "privileged"
      warn-version: "latest"
    exemptions:
      # 要豁免的已认证用户名列表
      usernames: []
      # 要豁免的运行时类名称列表
      runtimeClasses: []
      # 要豁免的名字空间列表
      namespaces: []
```

**说明：**

上面的清单需要通过 `--admission-control-config-file` 指定给 kube-apiserver。
## [使用名字空间标签来实施 Pod 安全性标准](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/)
名字空间可以打上标签以强制执行 [Pod 安全性标准](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards)。 [特权（privileged）](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/#privileged)、 [基线（baseline）](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/#baseline)和 [受限（restricted）](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/#restricted) 这三种策略涵盖了广泛安全范围，并由 [Pod 安全](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/)[准入控制器](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/admission-controllers/)实现。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

Pod 安全性准入（Pod Security Admission）在 Kubernetes v1.23 中作为 Beta 特性默认可用。 从 1.25 版本起，此特性进阶至正式发布（Generally Available）。

要获知版本信息，请输入 `kubectl version`.

### 通过名字空间标签来要求实施 `baseline` Pod 容器标准[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/#%E9%80%9A%E8%BF%87%E5%90%8D%E5%AD%97%E7%A9%BA%E9%97%B4%E6%A0%87%E7%AD%BE%E6%9D%A5%E8%A6%81%E6%B1%82%E5%AE%9E%E6%96%BD-baseline-pod-%E5%AE%B9%E5%99%A8%E6%A0%87%E5%87%86)

下面的清单定义了一个 `my-baseline-namespace` 名字空间，其中

- **阻止**任何不满足 `baseline` 策略要求的 Pod；
- 针对任何无法满足 `restricted` 策略要求的、已创建的 Pod 为用户生成警告信息， 并添加审计注解；
- 将 `baseline` 和 `restricted` 策略的版本锁定到 v1.26。

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-baseline-namespace
  labels:
    pod-security.kubernetes.io/enforce: baseline
    pod-security.kubernetes.io/enforce-version: v1.26

    # 我们将这些标签设置为我们所 _期望_ 的 `enforce` 级别
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/audit-version: v1.26
    pod-security.kubernetes.io/warn: restricted
    pod-security.kubernetes.io/warn-version: v1.26
```

### 使用 `kubectl label` 为现有名字空间添加标签[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/#%E4%BD%BF%E7%94%A8-kubectl-label-%E4%B8%BA%E7%8E%B0%E6%9C%89%E5%90%8D%E5%AD%97%E7%A9%BA%E9%97%B4%E6%B7%BB%E5%8A%A0%E6%A0%87%E7%AD%BE)

**说明：**

在添加或变更 `enforce` 策略（或版本）标签时，准入插件会测试名字空间中的每个 Pod 以检查其是否满足新的策略。不符合策略的情况会被以警告的形式返回给用户。

在刚开始为名字空间评估安全性策略变更时，使用 `--dry-run` 标志是很有用的。 Pod 安全性标准会在 **dry run（试运行）** 模式下运行，在这种模式下会生成新策略如何处理现有 Pod 的信息， 但不会真正更新策略。

```shell
kubectl label --dry-run=server --overwrite ns --all \
    pod-security.kubernetes.io/enforce=baseline
```

#### 应用到所有名字空间[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/#%E5%BA%94%E7%94%A8%E5%88%B0%E6%89%80%E6%9C%89%E5%90%8D%E5%AD%97%E7%A9%BA%E9%97%B4)

如果你是刚刚开始使用 Pod 安全性标准，一种比较合适的初始步骤是针对所有名字空间为类似 `baseline` 这种比较严格的安全级别配置审计注解。

```shell
kubectl label --overwrite ns --all \
  pod-security.kubernetes.io/audit=baseline \
  pod-security.kubernetes.io/warn=baseline
```

注意，这里没有设置 enforce 级别，因而没有被显式评估的名字空间可以被识别出来。 你可以使用下面的命令列举那些没有显式设置 enforce 级别的名字空间：

```shell
kubectl get namespaces --selector='!pod-security.kubernetes.io/enforce'
```

#### 应用到单个名字空间[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/#%E5%BA%94%E7%94%A8%E5%88%B0%E5%8D%95%E4%B8%AA%E5%90%8D%E5%AD%97%E7%A9%BA%E9%97%B4)

你也可以更新特定的名字空间。下面的命令将 `enforce=restricted` 策略应用到 `my-existing-namespace` 名字空间，将 restricted 策略的版本锁定到 v1.26。

```shell
kubectl label --overwrite ns my-existing-namespace \
  pod-security.kubernetes.io/enforce=restricted \
  pod-security.kubernetes.io/enforce-version=v1.26
```
## [从 PodSecurityPolicy 迁移到内置的 PodSecurity 准入控制器](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/)
本页面描述从 PodSecurityPolicy 迁移到内置的 PodSecurity 准入控制器的过程。 这一迁移过程可以通过综合使用试运行、`audit` 和 `warn` 模式等来实现， 尽管在使用了变更式 PSP 时会变得有些困难。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你的 Kubernetes 服务器版本必须不低于版本 v1.22. 要获知版本信息，请输入 `kubectl version`.

如果你目前运行的 Kubernetes 版本不是 1.26， 你可能要切换本页面以查阅你实际所运行的 Kubernetes 版本文档。

本页面假定你已经熟悉 [Pod 安全性准入](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/)的基本概念。

### 方法概览[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#overall-approach)

你可以采取多种策略来完成从 PodSecurityPolicy 到 Pod 安全性准入 （Pod Security Admission）的迁移。 下面是一种可能的迁移路径，其目标是尽可能降低生产环境不可用的风险， 以及安全性仍然不足的风险。

0. 确定 Pod 安全性准入是否对于你的使用场景而言比较合适。
1. 审查名字空间访问权限。
2. 简化、标准化 PodSecurityPolicy。
3. 更新名字空间：
    1. 确定合适的 Pod 安全性级别；
    2. 验证该 Pod 安全性级别可工作；
    3. 实施该 Pod 安全性级别；
    4. 绕过 PodSecurityPolicy。
4. 审阅名字空间创建过程。
5. 禁用 PodSecurityPolicy。

### 0\. 确定是否 Pod 安全性准入适合你[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#is-psa-right-for-you)

Pod 安全性准入被设计用来直接满足最常见的安全性需求，并提供一组可用于多个集群的安全性级别。 不过，这一机制比 PodSecurityPolicy 的灵活度要低。 值得注意的是，PodSecurityPolicy 所支持的以下特性是 Pod 安全性准入所不支持的：

- **设置默认的安全性约束** - Pod 安全性准入是一个非变更性质的准入控制器， 这就意味着它不会在对 Pod 进行合法性检查之前更改其配置。如果你之前依赖于 PSP 的这方面能力， 你或者需要更改你的负载以满足 Pod 安全性约束，或者需要使用一个 [变更性质的准入 Webhook](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/extensible-admission-controllers/) 来执行相应的变更。进一步的细节可参见后文的[简化和标准化 PodSecurityPolicy](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#simplify-psps)。

- **对策略定义的细粒度控制** - Pod 安全性准入仅支持 [三种标准级别](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/)。 如果你需要对特定的约束施加更多的控制，你就需要使用一个 [验证性质的准入 Webhook](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/extensible-admission-controllers/) 以实施这列策略。

- **粒度小于名字空间的策略** - PodSecurityPolicy 允许你为不同的服务账户或用户绑定不同策略， 即使这些服务账户或用户隶属于同一个名字空间。这一方法有很多缺陷，不建议使用。 不过如果你的确需要这种功能，你就需要使用第三方的 Webhook。 唯一的例外是当你只需要完全针对某用户或者 [RuntimeClasses](https://kubernetes.io/zh-cn/docs/concepts/containers/runtime-class/) 赋予豁免规则时， Pod 安全性准入的确也为豁免规则暴露一些 [静态配置](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/#exemptions)。

即便 Pod 安全性准入无法满足你的所有需求，该机制也是设计用作其他策略实施机制的 **补充**，因此可以和其他准入 Webhook 一起运行，进而提供一种有用的兜底机制。

### 1\. 审查名字空间访问权限[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#review-namespace-permissions)

Pod 安全性准入是通过[名字空间上的标签](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-admission/#pod-security-admission-labels-for-namespaces) 来控制的。这也就是说，任何能够更新（或通过 patch 部分更新或创建） 名字空间的人都可以更改该名字空间的 Pod 安全性级别，而这可能会被利用来绕过约束性更强的策略。 在继续执行迁移操作之前，请确保只有被信任的、有特权的用户具有这类名字空间访问权限。 不建议将这类强大的访问权限授予不应获得权限提升的用户，不过如果你必须这样做， 你需要使用一个[准入 Webhook](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/extensible-admission-controllers/) 来针对为 Namespace 对象设置 Pod 安全性级别设置额外的约束。

### 2\. 简化、标准化 PodSecurityPolicy[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#simplify-psps)

在本节中，你会削减变更性质的 PodSecurityPolicy，去掉 Pod 安全性标准范畴之外的选项。 针对要修改的、已存在的 PodSecurityPolicy，你应该将这里所建议的更改写入到其离线副本中。 所克隆的 PSP 应该与原来的副本名字不同，并且按字母序要排到原副本之前 （例如，可以向 PSP 名字前加一个 `0`）。 先不要在 Kubernetes 中创建新的策略 - 这类操作会在后文的[推出更新的策略](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#psp-update-rollout)部分讨论。

#### 2.a. 去掉纯粹变更性质的字段[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#eliminating-mutaging-fields)

如果某个 PodSecurityPolicy 能够变更字段，你可能会在关掉 PodSecurityPolicy 时发现有些 Pod 无法满足 Pod 安全性级别。为避免这类状况， 你应该在执行切换操作之前去掉所有 PSP 的变更操作。 不幸的是，PSP 没有对变更性和验证性字段做清晰的区分，所以这一迁移操作也不够简单直接。

你可以先去掉那些纯粹变更性质的字段，留下验证策略中的其他内容。 这些字段（也列举于[将 PodSecurityPolicy 映射到 Pod 安全性标准](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/psp-to-pod-security-standards/)参考中） 包括：

- `.spec.defaultAllowPrivilegeEscalation`
- `.spec.runtimeClass.defaultRuntimeClassName`
- `.metadata.annotations['seccomp.security.alpha.kubernetes.io/defaultProfileName']`
- `.metadata.annotations['apparmor.security.beta.kubernetes.io/defaultProfileName']`
- `.spec.defaultAddCapabilities` - 尽管理论上是一个混合了变更性与验证性功能的字段， 这里的设置应该被合并到 `.spec.allowedCapabilities` 中，后者会执行相同的验证操作， 但不会执行任何变更动作。

**注意：**

删除这些字段可能导致负载缺少所需的配置信息，进而导致一些问题。 参见后文[退出更新的策略](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#psp-update-rollout)以获得如何安全地将这些变更上线的建议。

#### 2.b. 去掉 Pod 安全性标准未涉及的选项[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#eliminate-non-standard-options)

PodSecurityPolicy 中有一些字段未被 Pod 安全性准入机制覆盖。如果你必须使用这些选项， 你需要在 Pod 安全性准入之外部署[准入 Webhook](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/extensible-admission-controllers/) 以补充这一能力，而这类操作不在本指南范围。

首先，你可以去掉 Pod 安全性标准所未覆盖的那些验证性字段。这些字段 （也列举于[将 PodSecurityPolicy 映射到 Pod 安全性标准](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/psp-to-pod-security-standards/)参考中， 标记为“无意见”）有：

- `.spec.allowedHostPaths`
- `.spec.allowedFlexVolumes`
- `.spec.allowedCSIDrivers`
- `.spec.forbiddenSysctls`
- `.spec.runtimeClass`

你也可以去掉以下字段，这些字段与 POSIX/UNIX 用户组控制有关。

**注意：**

如果这些字段中存在使用 `MustRunAs` 策略的情况，则意味着对应字段是变更性质的。 去掉相应的字段可能导致负载无法设置所需的用户组，进而带来一些问题。 关于如何安全地将这类变更上线的相关建议，请参阅后文的[推出更新的策略](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#psp-update-rollout)部分。

- `.spec.runAsGroup`
- `.spec.supplementalGroups`
- `.spec.fsGroup`

剩下的变更性字段是为了适当支持 Pod 安全性标准所需要的，因而需要逐个处理：

- `.spec.requiredDropCapabilities` - 需要此字段来为 Restricted 配置去掉 `ALL` 设置。
- `.spec.seLinux` - （仅针对带有 `MustRunAs` 规则的变更性设置）需要此字段来满足 Baseline 和 Restricted 配置所需要的 SELinux 需求。
- `.spec.runAsUser` - （仅针对带有 `RunAsAny` 规则的非变更性设置）需要此字段来为 Restricted 配置保证 `RunAsNonRoot`。
- `.spec.allowPrivilegeEscalation` - （如果设置为 `false` 则为变更性设置） 需要此字段来支持 Restricted 配置。

#### 2.c. 推出更新的 PSP[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#psp-update-rollout)

接下来，你可以将更新后的策略推出到你的集群上。在继续操作时，你要非常小心， 因为去掉变更性质的选项可能导致有些工作负载缺少必需的配置。

针对更新后的每个 PodSecurityPolicy：

1. 识别运行于原 PSP 之下的 Pod。可以通过 `kubernetes.io/psp` 注解来完成。 例如，使用 kubectl：
    
    ```shell
    PSP_NAME="original" # 设置你要检查的 PSP 的名称
    kubectl get pods --all-namespaces -o jsonpath="{range .items[?(@.metadata.annotations.kubernetes\.io\/psp=='$PSP_NAME')]}{.metadata.namespace} {.metadata.name}{'\n'}{end}"
    ```
    

2. 比较运行中的 Pod 与原来的 Pod 规约，确定 PodSecurityPolicy 是否更改过这些 Pod。 对于通过[工作负载资源](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/)所创建的 Pod， 你可以比较 Pod 和控制器资源中的 PodTemplate。如果发现任何变更，则原来的 Pod 或者 PodTemplate 需要被更新以加上所希望的配置。要审查的字段包括：
    
    - `.metadata.annotations['container.apparmor.security.beta.kubernetes.io/*']` （将 `*` 替换为每个容器的名称）
    - `.spec.runtimeClassName`
    - `.spec.securityContext.fsGroup`
    - `.spec.securityContext.seccompProfile`
    - `.spec.securityContext.seLinuxOptions`
    - `.spec.securityContext.supplementalGroups`
    
    - 对于容器，在 `.spec.containers[*]` 和 `.spec.initContainers[*]` 之下，检查下面字段：
    - `.securityContext.allowPrivilegeEscalation`
    - `.securityContext.capabilities.add`
    - `.securityContext.capabilities.drop`
    - `.securityContext.readOnlyRootFilesystem`
    - `.securityContext.runAsGroup`
    - `.securityContext.runAsNonRoot`
    - `.securityContext.runAsUser`
    - `.securityContext.seccompProfile`
    - `.securityContext.seLinuxOptions`

3. 创建新的 PodSecurityPolicy。如果存在 Role 或 ClusterRole 对象为用户授权了在所有 PSP 上使用 `use` 动词的权限，则所使用的的会是新创建的 PSP 而不是其变更性的副本。
4. 更新你的鉴权配置，为访问新的 PSP 授权。在 RBAC 机制下，这意味着需要更新所有为原 PSP 授予 `use` 访问权限的 Role 或 ClusterRole 对象，使之也对更新后的 PSP 授权。

5. 验证：经过一段时间后，重新执行步骤 1 中所给的命令，查看是否有 Pod 仍在使用原来的 PSP。 注意，在新的策略被推出到集群之后，Pod 需要被重新创建才可以执行全面验证。
6. （可选）一旦你已经验证原来的 PSP 不再被使用，你就可以删除这些 PSP。

### 3\. 更新名字空间[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#update-namespace)

下面的步骤需要在集群中的所有名字空间上执行。所列步骤中的命令使用变量 `$NAMESPACE` 来引用所更新的名字空间。

#### 3.a. 识别合适的 Pod 安全级别[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#identify-appropriate-level)

首先请回顾 [Pod 安全性标准](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-standards/)内容， 并了解三个安全级别。

为你的名字空间选择 Pod 安全性级别有几种方法：

1. **根据名字空间的安全性需求来确定** - 如果你熟悉某名字空间的预期访问级别， 你可以根据这类需求来选择合适的安全级别，就像大家在为新集群确定安全级别一样。

2. **根据现有的 PodSecurityPolicy 来确定** - 基于[将 PodSecurityPolicy 映射到 Pod 安全性标准](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/psp-to-pod-security-standards/) 参考资料，你可以将各个 PSP 映射到某个 Pod 安全性标准级别。如果你的 PSP 不是基于 Pod 安全性标准的，你可能或者需要选择一个至少与该 PSP 一样宽松的级别， 或者选择一个至少与其一样严格的级别。使用下面的命令你可以查看被 Pod 使用的 PSP 有哪些：
    
    ```sh
    kubectl get pods -n $NAMESPACE -o jsonpath="{.items[*].metadata.annotations.kubernetes\.io\/psp}" | tr " " "\n" | sort -u
    ```
    

3. **根据现有 Pod 来确定** - 使用[检查 Pod 安全性级别](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#verify-pss-level)小节所述策略， 你可以测试 Baseline 和 Restricted 级别，检查它们是否对于现有负载而言足够宽松， 并选择二者之间特权级较低的合法级别。

**注意：**

上面的第二和第三种方案是基于 _现有_ Pod 的，因此可能错失那些当前未处于运行状态的 Pod，例如 CronJobs、缩容到零的负载，或者其他尚未全面铺开的负载。

#### 3.b. 检查 Pod 安全性级别[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#verify-pss-level)

一旦你已经为名字空间选择了 Pod 安全性级别（或者你正在尝试多个不同级别）， 先进行测试是个不错的主意（如果使用 Privileged 级别，则可略过此步骤）。 Pod 安全性包含若干工具可用来测试和安全地推出安全性配置。

首先，你可以试运行新策略，这个过程可以针对所应用的策略评估当前在名字空间中运行的 Pod，但不会令新策略马上生效：

```sh
# $LEVEL 是要试运行的级别，可以是 "baseline" 或 "restricted"
kubectl label --dry-run=server --overwrite ns $NAMESPACE pod-security.kubernetes.io/enforce=$LEVEL
```

此命令会针对在所提议的级别下不再合法的所有 **现存** Pod 返回警告信息。

第二种办法在抓取当前未运行的负载方面表现的更好：audit 模式。 运行于 audit 模式（而非 enforcing 模式）下时，违反策略级别的 Pod 会被记录到审计日志中， 经过一段时间后可以在日志中查看到，但这些 Pod 不会被拒绝。 warning 模式的工作方式与此类似，不过会立即向用户返回告警信息。 你可以使用下面的命令为名字空间设置 audit 模式的级别：

```sh
kubectl label --overwrite ns $NAMESPACE pod-security.kubernetes.io/audit=$LEVEL
```

当以上两种方法输出意料之外的违例状况时，你就需要或者更新发生违例的负载以满足策略需求， 或者放宽名字空间上的 Pod 安全性级别。

#### 3.c. 实施 Pod 安全性级别[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#enforce-pod-security-level)

当你对可以安全地在名字空间上实施的级别比较满意时，你可以更新名字空间来实施所期望的级别：

```sh
kubectl label --overwrite ns $NAMESPACE pod-security.kubernetes.io/enforce=$LEVEL
```

#### 3.d. 绕过 PodSecurityPolicy[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#bypass-psp)

最后，你可以通过将 [完全特权的 PSP](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/policy/privileged-psp.yaml) 绑定到某名字空间中所有服务账户上，在名字空间层面绕过所有 PodSecurityPolicy。

```sh
# 下面集群范围的命令只需要执行一次
kubectl apply -f privileged-psp.yaml
kubectl create clusterrole privileged-psp --verb use --resource podsecuritypolicies.policy --resource-name privileged

# 逐个名字空间地禁用
kubectl create -n $NAMESPACE rolebinding disable-psp --clusterrole privileged-psp --group system:serviceaccounts:$NAMESPACE
```

由于特权 PSP 是非变更性的，PSP 准入控制器总是优选非变更性的 PSP， 上面的操作会确保对应名字空间中的所有 Pod 不再会被 PodSecurityPolicy 所更改或限制。

按上述操作逐个名字空间地禁用 PodSecurityPolicy 这种做法的好处是， 如果出现问题，你可以很方便地通过删除 RoleBinding 来回滚所作的更改。 你所要做的只是确保之前存在的 PodSecurityPolicy 还在。

```sh
# 撤销 PodSecurityPolicy 的禁用
kubectl delete -n $NAMESPACE rolebinding disable-psp
```

### 4\. 审阅名字空间创建过程[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#review-namespace-creation-process)

现在，现有的名字空间都已被更新，强制实施 Pod 安全性准入， 你应该确保你用来管控新名字空间创建的流程与/或策略也被更新，这样合适的 Pod 安全性配置会被应用到新的名字空间上。

你也可以静态配置 Pod 安全性准入控制器，为尚未打标签的名字空间设置默认的 enforce、audit 与/或 warn 级别。 详细信息可参阅[配置准入控制器](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/enforce-standards-admission-controller/#configure-the-admission-controller)页面。

### 5\. 禁用 PodSecurityPolicy[](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/migrate-from-psp/#disable-psp)

最后，你已为禁用 PodSecurityPolicy 做好准备。要禁用 PodSecurityPolicy， 你需要更改 API 服务器上的准入配置： [我如何关闭某个准入控制器？](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/admission-controllers/#how-do-i-turn-off-an-admission-controller)

如果需要验证 PodSecurityPolicy 准入控制器不再被启用，你可以通过扮演某个无法访问任何 PodSecurityPolicy 的用户来执行测试（参见 [PodSecurityPolicy 示例](https://kubernetes.io/zh-cn/docs/concepts/security/pod-security-policy/#example)）， 或者通过检查 API 服务器的日志来进行验证。在启动期间，API 服务器会输出日志行，列举所挂载的准入控制器插件。

```
I0218 00:59:44.903329      13 plugins.go:158] Loaded 16 mutating admission controller(s) successfully in the following order: NamespaceLifecycle,LimitRanger,ServiceAccount,NodeRestriction,TaintNodesByCondition,Priority,DefaultTolerationSeconds,ExtendedResourceToleration,PersistentVolumeLabel,DefaultStorageClass,StorageObjectInUseProtection,RuntimeClass,DefaultIngressClass,MutatingAdmissionWebhook.
I0218 00:59:44.903350      13 plugins.go:161] Loaded 14 validating admission controller(s) successfully in the following order: LimitRanger,ServiceAccount,PodSecurity,Priority,PersistentVolumeClaimResize,RuntimeClass,CertificateApproval,CertificateSigning,CertificateSubjectRestriction,DenyServiceExternalIPs,ValidatingAdmissionWebhook,ResourceQuota.
```

你应该会看到 `PodSecurity`（在 validating admission controllers 列表中）， 并且两个列表中都不应该包含 `PodSecurityPolicy`。

一旦你确定 PSP 准入控制器已被禁用（并且这种状况已经持续了一段时间， 这样你才会比较确定不需要回滚），你就可以放心地删除你的 PodSecurityPolicy 以及所关联的所有 Role、ClusterRole、RoleBinding、ClusterRoleBinding 等对象 （仅需要确保他们不再授予其他不相关的访问权限）。







