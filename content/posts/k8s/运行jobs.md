# 运行 Jobs

使用并行处理运行 Jobs。

---

## [使用 CronJob 运行自动化任务](https://kubernetes.io/zh-cn/docs/tasks/job/automated-tasks-with-cron-jobs/)
本页演示如何使用 Kubernetes [CronJob](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/cron-jobs/) 对象运行自动化任务。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/job/automated-tasks-with-cron-jobs/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

- 你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：
    
    - [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
    - [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 创建 CronJob[](https://kubernetes.io/zh-cn/docs/tasks/job/automated-tasks-with-cron-jobs/#creating-a-cron-job)

CronJob 需要一个配置文件。 以下是针对一个 CronJob 的清单，该 CronJob 每分钟运行一个简单的演示任务：

[`application/job/cronjob.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/cronjob.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/cronjob.yaml to clipboard")

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

执行以下命令以运行此 CronJob 示例：

```shell
kubectl create -f https://k8s.io/examples/application/job/cronjob.yaml
```

输出类似于：

```
cronjob.batch/hello created
```

创建好 CronJob 后，使用下面的命令来获取其状态：

```shell
kubectl get cronjob hello
```

输出类似于：

```
NAME    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
hello   */1 * * * *   False     0        <none>          10s
```

就像你从命令返回结果看到的那样，CronJob 还没有调度或执行任何任务。大约需要一分钟任务才能创建好。

```shell
kubectl get jobs --watch
```

输出类似于：

```
NAME               COMPLETIONS   DURATION   AGE
hello-4111706356   0/1                      0s
hello-4111706356   0/1           0s         0s
hello-4111706356   1/1           5s         5s
```

现在你已经看到了一个运行中的任务被 “hello” CronJob 调度。 你可以停止监视这个任务，然后再次查看 CronJob 就能看到它调度任务：

```shell
kubectl get cronjob hello
```

输出类似于：

```
NAME    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
hello   */1 * * * *   False     0        50s             75s
```

你应该能看到 `hello` CronJob 在 `LAST SCHEDULE` 声明的时间点成功地调度了一次任务。 目前有 0 个活跃的任务，这意味着任务执行完毕或者执行失败。

现在，找到最后一次调度任务创建的 Pod 并查看一个 Pod 的标准输出。

**说明：** Job 名称与 Pod 名称不同。

```shell
# 在你的系统上将 "hello-4111706356" 替换为 Job 名称
pods=$(kubectl get pods --selector=job-name=hello-4111706356 --output=jsonpath={.items..metadata.name})
```

查看 Pod 日志：

```shell
kubectl logs $pods
```

输出类似于：

```
Fri Feb 22 11:02:09 UTC 2019
Hello from the Kubernetes cluster
```

### 删除 CronJob[](https://kubernetes.io/zh-cn/docs/tasks/job/automated-tasks-with-cron-jobs/#deleting-a-cronjob)

当你不再需要 CronJob 时，可以用 `kubectl delete cronjob <cronjob name>` 删掉它：

```shell
kubectl delete cronjob hello
```

删除 CronJob 会清除它创建的所有任务和 Pod，并阻止它创建额外的任务。 你可以查阅[垃圾收集](https://kubernetes.io/zh-cn/docs/concepts/architecture/garbage-collection/)。
## [使用工作队列进行粗粒度并行处理](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/)
本例中，我们会运行包含多个并行工作进程的 Kubernetes Job。

本例中，每个 Pod 一旦被创建，会立即从任务队列中取走一个工作单元并完成它，然后将工作单元从队列中删除后再退出。

下面是本次示例的主要步骤：

1. **启动一个消息队列服务**。 本例中，我们使用 RabbitMQ，你也可以用其他的消息队列服务。 在实际工作环境中，你可以创建一次消息队列服务然后在多个任务中重复使用。
    
2. **创建一个队列，放上消息数据**。 每个消息表示一个要执行的任务。本例中，每个消息是一个整数值。 我们将基于这个整数值执行很长的计算操作。
    
3. **启动一个在队列中执行这些任务的 Job**。 该 Job 启动多个 Pod。每个 Pod 从消息队列中取走一个任务，处理任务，然后退出。
    

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

要熟悉 Job 基本用法（非并行的），请参考 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/)。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

### 启动消息队列服务[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#starting-a-message-queue-service)

本例使用了 RabbitMQ，但你可以更改该示例，使用其他 AMQP 类型的消息服务。

在实际工作中，在集群中一次性部署某个消息队列服务，之后在很多 Job 中复用，包括需要长期运行的服务。

按下面的方法启动 RabbitMQ：

```shell
kubectl create -f https://raw.githubusercontent.com/kubernetes/kubernetes/release-1.3/examples/celery-rabbitmq/rabbitmq-service.yaml
```

```
service "rabbitmq-service" created
```

```shell
kubectl create -f https://raw.githubusercontent.com/kubernetes/kubernetes/release-1.3/examples/celery-rabbitmq/rabbitmq-controller.yaml
```

```
replicationcontroller "rabbitmq-controller" created
```

我们仅用到 [celery-rabbitmq 示例](https://github.com/kubernetes/kubernetes/tree/release-1.3/examples/celery-rabbitmq)中描述的部分功能。

### 测试消息队列服务[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#testing-the-message-queue-service)

现在，我们可以试着访问消息队列。我们将会创建一个临时的可交互的 Pod， 在它上面安装一些工具，然后用队列做实验。

首先创建一个临时的可交互的 Pod：

```shell
# 创建一个临时的可交互的 Pod
kubectl run -i --tty temp --image ubuntu:18.04
```

```
Waiting for pod default/temp-loe07 to be running, status is Pending, pod ready: false
... [ previous line repeats several times .. hit return when it stops ] ...
```

请注意你的 Pod 名称和命令提示符将会不同。

接下来安装 `amqp-tools`，这样我们就能用消息队列了。

```shell
# 安装一些工具
root@temp-loe07:/# apt-get update
.... [ lots of output ] ....
root@temp-loe07:/# apt-get install -y curl ca-certificates amqp-tools python dnsutils
.... [ lots of output ] ....
```

后续，我们将制作一个包含这些包的 Docker 镜像。

接着，我们将要验证可以发现 RabbitMQ 服务：

```
# 请注意 rabbitmq-service 拥有一个由 Kubernetes 提供的 DNS 名称：

root@temp-loe07:/# nslookup rabbitmq-service
Server:        10.0.0.10
Address:    10.0.0.10#53

Name:    rabbitmq-service.default.svc.cluster.local
Address: 10.0.147.152

# 你的 IP 地址会不同
```

如果 Kube-DNS 没有正确安装，上一步可能会出错。 你也可以在环境变量中找到服务 IP。

```
# env | grep RABBIT | grep HOST
RABBITMQ_SERVICE_SERVICE_HOST=10.0.147.152

# 你的 IP 地址会有所不同
```

接着我们将要确认可以创建队列，并能发布消息和消费消息。

```shell
# 下一行，rabbitmq-service 是访问 rabbitmq-service 的主机名。5672是 rabbitmq 的标准端口。

root@temp-loe07:/# export BROKER_URL=amqp://guest:guest@rabbitmq-service:5672

# 如果上一步中你不能解析 "rabbitmq-service"，可以用下面的命令替换：
# root@temp-loe07:/# BROKER_URL=amqp://guest:guest@$RABBITMQ_SERVICE_SERVICE_HOST:5672

# 现在创建队列：

root@temp-loe07:/# /usr/bin/amqp-declare-queue --url=$BROKER_URL -q foo -d foo

# 向它推送一条消息:

root@temp-loe07:/# /usr/bin/amqp-publish --url=$BROKER_URL -r foo -p -b Hello

# 然后取回它.

root@temp-loe07:/# /usr/bin/amqp-consume --url=$BROKER_URL -q foo -c 1 cat && echo
Hello
root@temp-loe07:/#
```

最后一个命令中，`amqp-consume` 工具从队列中取走了一个消息，并把该消息传递给了随机命令的标准输出。 在这种情况下，`cat` 会打印它从标准输入中读取的字符，echo 会添加回车符以便示例可读。

### 为队列增加任务[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#filling-the-queue-with-tasks)

现在让我们给队列增加一些任务。在我们的示例中，任务是多个待打印的字符串。

实践中，消息的内容可以是：

- 待处理的文件名
- 程序额外的参数
- 数据库表的关键字范围
- 模拟任务的配置参数
- 待渲染的场景的帧序列号

本例中，如果有大量的数据需要被 Job 的所有 Pod 读取，典型的做法是把它们放在一个共享文件系统中， 如 NFS（Network File System 网络文件系统），并以只读的方式挂载到所有 Pod，或者 Pod 中的程序从类似 HDFS （Hadoop Distributed File System 分布式文件系统）的集群文件系统中读取。

例如，我们创建队列并使用 amqp 命令行工具向队列中填充消息。实践中，你可以写个程序来利用 amqp 客户端库来填充这些队列。

```shell
/usr/bin/amqp-declare-queue --url=$BROKER_URL -q job1  -d job1
for f in apple banana cherry date fig grape lemon melon
do
  /usr/bin/amqp-publish --url=$BROKER_URL -r job1 -p -b $f
done
```

这样，我们给队列中填充了 8 个消息。

### 创建镜像[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#create-an-image)

现在我们可以创建一个做为 Job 来运行的镜像。

我们将用 `amqp-consume` 实用程序从队列中读取消息并运行实际的程序。 这里给出一个非常简单的示例程序：

[`application/job/rabbitmq/worker.py`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/rabbitmq/worker.py) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/rabbitmq/worker.py to clipboard")

```python
#!/usr/bin/env python

# Just prints standard out and sleeps for 10 seconds.
import sys
import time
print("Processing " + sys.stdin.readlines()[0])
time.sleep(10)
```

赋予脚本执行权限:

```shell
chmod +x worker.py
```

现在，编译镜像。如果你在用源代码树，那么切换到目录 `examples/job/work-queue-1`。 否则的话，创建一个临时目录，切换到这个目录。下载 [Dockerfile](https://kubernetes.io/examples/application/job/rabbitmq/Dockerfile) 和 [worker.py](https://kubernetes.io/examples/application/job/rabbitmq/worker.py)。 无论哪种情况，都可以用下面的命令编译镜像：

```shell
docker build -t job-wq-1 .
```

对于 [Docker Hub](https://hub.docker.com/), 给你的应用镜像打上标签， 标签为你的用户名，然后用下面的命令推送到 Hub。用你的 Hub 用户名替换 `<username>`。

```shell
docker tag job-wq-1 <username>/job-wq-1
docker push <username>/job-wq-1
```

如果你在用[谷歌容器仓库](https://cloud.google.com/tools/container-registry/)， 用你的项目 ID 作为标签打到你的应用镜像上，然后推送到 GCR。 用你的项目 ID 替换 `<project>`。

```shell
docker tag job-wq-1 gcr.io/<project>/job-wq-1
gcloud docker -- push gcr.io/<project>/job-wq-1
```

### 定义 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#defining-a-job)

这里给出一个 Job 定义 YAML 文件。你将需要拷贝一份 Job 并编辑该镜像以匹配你使用的名称，保存为 `./job.yaml`。

[`application/job/rabbitmq/job.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/rabbitmq/job.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/rabbitmq/job.yaml to clipboard")

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: job-wq-1
spec:
  completions: 8
  parallelism: 2
  template:
    metadata:
      name: job-wq-1
    spec:
      containers:
      - name: c
        image: gcr.io/<project>/job-wq-1
        env:
        - name: BROKER_URL
          value: amqp://guest:guest@rabbitmq-service:5672
        - name: QUEUE
          value: job1
      restartPolicy: OnFailure
```

本例中，每个 Pod 使用队列中的一个消息然后退出。 这样，Job 的完成计数就代表了完成的工作项的数量。 本例中我们设置 `.spec.completions: 8`，因为我们放了 8 项内容在队列中。

### 运行 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#running-the-job)

现在我们运行 Job：

```shell
kubectl apply -f ./job.yaml
```

你可以等待 Job 在某个超时时间后成功：

```shell
# 状况名称的检查不区分大小写
kubectl wait --for=condition=complete --timeout=300s job/job-wq-1
```

接下来查看 Job：

```shell
kubectl describe jobs/job-wq-1
```

```
Name:             job-wq-1
Namespace:        default
Selector:         controller-uid=41d75705-92df-11e7-b85e-fa163ee3c11f
Labels:           controller-uid=41d75705-92df-11e7-b85e-fa163ee3c11f
                  job-name=job-wq-1
Annotations:      <none>
Parallelism:      2
Completions:      8
Start Time:       Wed, 06 Sep 2017 16:42:02 +0800
Pods Statuses:    0 Running / 8 Succeeded / 0 Failed
Pod Template:
  Labels:       controller-uid=41d75705-92df-11e7-b85e-fa163ee3c11f
                job-name=job-wq-1
  Containers:
   c:
    Image:      gcr.io/causal-jigsaw-637/job-wq-1
    Port:
    Environment:
      BROKER_URL:       amqp://guest:guest@rabbitmq-service:5672
      QUEUE:            job1
    Mounts:             <none>
  Volumes:              <none>
Events:
  FirstSeen  LastSeen   Count    From    SubobjectPath    Type      Reason              Message
  ─────────  ────────   ─────    ────    ─────────────    ──────    ──────              ───────
  27s        27s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-hcobb
  27s        27s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-weytj
  27s        27s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-qaam5
  27s        27s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-b67sr
  26s        26s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-xe5hj
  15s        15s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-w2zqe
  14s        14s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-d6ppa
  14s        14s        1        {job }                   Normal    SuccessfulCreate    Created pod: job-wq-1-p17e0
```

该 Job 的所有 Pod 都已成功。耶！

### 替代方案[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#alternatives)

本文所讲述的处理方法的好处是你不需要修改你的 "worker" 程序使其知道工作队列的存在。

本文所描述的方法需要你运行一个消息队列服务。如果不方便运行消息队列服务， 你也许会考虑另外一种[任务模式](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/#job-patterns)。

本文所述的方法为每个工作项创建了一个 Pod。 如果你的工作项仅需数秒钟，为每个工作项创建 Pod 会增加很多的常规消耗。 可以考虑另外的方案请参考[示例](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/)， 这种方案可以实现每个 Pod 执行多个工作项。

示例中，我们使用 `amqp-consume` 从消息队列读取消息并执行我们真正的程序。 这样的好处是你不需要修改你的程序使其知道队列的存在。 要了解怎样使用客户端库和工作队列通信， 请参考[不同的示例](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/)。

### 友情提醒[](https://kubernetes.io/zh-cn/docs/tasks/job/coarse-parallel-processing-work-queue/#caveats)

如果设置的完成数量小于队列中的消息数量，会导致一部分消息项不会被执行。

如果设置的完成数量大于队列中的消息数量，当队列中所有的消息都处理完成后， Job 也会显示为未完成。Job 将创建 Pod 并阻塞等待消息输入。

当发生下面两种情况时，即使队列中所有的消息都处理完了，Job 也不会显示为完成状态：

- 在 amqp-consume 命令拿到消息和容器成功退出之间的时间段内，执行杀死容器操作；
- 在 kubelet 向 api-server 传回 Pod 成功运行之前，发生节点崩溃。

## [带 Pod 间通信的 Job](https://kubernetes.io/zh-cn/docs/tasks/job/job-with-pod-to-pod-communication/)
在此例中，你将以[索引完成模式](https://kubernetes.io/blog/2021/04/19/introducing-indexed-jobs/)运行一个 Job， 并通过配置使得该 Job 所创建的各 Pod 之间可以使用 Pod 主机名而不是 Pod IP 地址进行通信。

某 Job 内的 Pod 之间可能需要通信。每个 Pod 中运行的用户工作负载可以查询 Kubernetes API 服务器以获知其他 Pod 的 IP，但使用 Kubernetes 内置的 DNS 解析会更加简单。

索引完成模式下的 Job 自动将 Pod 的主机名设置为 `${jobName}-${completionIndex}` 的格式。 你可以使用此格式确定性地构建 Pod 主机名并启用 Pod 通信，无需创建到 Kubernetes 控制平面的客户端连接来通过 API 请求获取 Pod 主机名/IP。

此配置可用于需要 Pod 联网但不想依赖 Kubernetes API 服务器网络连接的使用场景。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/job/job-with-pod-to-pod-communication/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你应该已熟悉了 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/) 的基本用法。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你的 Kubernetes 服务器版本必须不低于版本 v1.21. 要获知版本信息，请输入 `kubectl version`.

**说明：**

如果你正在使用 MiniKube 或类似的工具， 你可能需要采取[额外的步骤](https://minikube.sigs.k8s.io/docs/handbook/addons/ingress-dns/)来确保你拥有 DNS。

### 启动带 Pod 间通信的 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/job-with-pod-to-pod-communication/#starting-a-job-with-pod-to-pod-communication)

要在某 Job 中启用使用 Pod 主机名的 Pod 间通信，你必须执行以下操作：

1. 对于 Job 所创建的那些 Pod， 使用一个有效的标签选择算符创建[无头服务](https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/#headless-services)。 该无头服务必须位于与该 Job 相同的名字空间内。 实现这一目的的一种简单的方式是使用 `job-name: <任务名称>` 作为选择算符， 因为 `job-name` 标签将由 Kubernetes 自动添加。 此配置将触发 DNS 系统为运行 Job 的 Pod 创建其主机名的记录。
    
2. 通过将以下值包括到你的 Job 模板规约中，针对该 Job 的 Pod，将无头服务配置为其子域服务：
    
    ```yaml
    subdomain: <无头服务的名称>
    ```
    

#### 示例[](https://kubernetes.io/zh-cn/docs/tasks/job/job-with-pod-to-pod-communication/#example)

以下是启用通过 Pod 主机名来完成 Pod 间通信的 Job 示例。 只有在使用主机名成功 ping 通所有 Pod 之后，此 Job 才会结束。

**说明：**

在以下示例中的每个 Pod 中执行的 Bash 脚本中，如果需要从名字空间外到达 Pod， Pod 主机名也可以带有该名字空间作为前缀。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: headless-svc
spec:
  clusterIP: None # clusterIP 必须为 None 以创建无头服务
  selector:
    job-name: example-job # 必须与 Job 名称匹配
---
apiVersion: batch/v1
kind: Job
metadata:
  name: example-job
spec:
  completions: 3
  parallelism: 3
  completionMode: Indexed
  template:
    spec:
      subdomain: headless-svc # 必须与 Service 名称匹配
      restartPolicy: Never
      containers:
      - name: example-workload
        image: bash:latest
        command:
        - bash
        - -c
        - |
          for i in 0 1 2
          do
            gotStatus="-1"
            wantStatus="0"             
            while [ $gotStatus -ne $wantStatus ]
            do                                       
              ping -c 1 example-job-${i}.headless-svc > /dev/null 2>&1
              gotStatus=$?                
              if [ $gotStatus -ne $wantStatus ]; then
                echo "Failed to ping pod example-job-${i}.headless-svc, retrying in 1 second..."
                sleep 1
              fi
            done                                                         
            echo "Successfully pinged pod: example-job-${i}.headless-svc"
          done          
```

应用上述示例之后，使用 `<Pod 主机名>.<无头服务名>` 通过网络到达彼此。 你应看到类似以下的输出：

```shell
kubectl logs example-job-0-qws42
```

```
Failed to ping pod example-job-0.headless-svc, retrying in 1 second...
Successfully pinged pod: example-job-0.headless-svc
Successfully pinged pod: example-job-1.headless-svc
Successfully pinged pod: example-job-2.headless-svc
```

**说明：**

谨记此例中使用的 `<Pod 主机名>.<无头服务名称>` 名称格式不适用于设置为 `None` 或 `Default` 的 DNS 策略。 你可以在[此处](https://kubernetes.io/zh-cn/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy)了解有关 Pod DNS 策略的更多信息。
## [使用工作队列进行精细的并行处理](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/)
在这个例子中，我们会运行一个 Kubernetes Job，其中的 Pod 会运行多个并行工作进程。

在这个例子中，当每个 Pod 被创建时，它会从一个任务队列中获取一个工作单元，处理它，然后重复，直到到达队列的尾部。

下面是这个示例的步骤概述：

1. **启动存储服务用于保存工作队列。** 在这个例子中，我们使用 Redis 来存储工作项。 在上一个例子中，我们使用了 RabbitMQ。 在这个例子中，由于 AMQP 不能为客户端提供一个良好的方法来检测一个有限长度的工作队列是否为空， 我们使用了 Redis 和一个自定义的工作队列客户端库。 在实践中，你可能会设置一个类似于 Redis 的存储库，并将其同时用于多项任务或其他事务的工作队列。

2. **创建一个队列，然后向其中填充消息。** 每个消息表示一个将要被处理的工作任务。 在这个例子中，消息是一个我们将用于进行长度计算的整数。

3. **启动一个 Job 对队列中的任务进行处理**。这个 Job 启动了若干个 Pod。 每个 Pod 从消息队列中取出一个工作任务，处理它，然后重复，直到到达队列的尾部。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

熟悉基本的、非并行的 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/)。

### 启动 Redis[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#%E5%90%AF%E5%8A%A8-redis)

对于这个例子，为了简单起见，我们将启动一个单实例的 Redis。 了解如何部署一个可伸缩、高可用的 Redis 例子，请查看 [Redis 示例](https://github.com/kubernetes/examples/tree/master/guestbook)

你也可以直接下载如下文件：

- [`redis-pod.yaml`](https://kubernetes.io/examples/application/job/redis/redis-pod.yaml)
- [`redis-service.yaml`](https://kubernetes.io/examples/application/job/redis/redis-service.yaml)
- [`Dockerfile`](https://kubernetes.io/examples/application/job/redis/Dockerfile)
- [`job.yaml`](https://kubernetes.io/examples/application/job/redis/job.yaml)
- [`rediswq.py`](https://kubernetes.io/examples/application/job/redis/rediswq.py)
- [`worker.py`](https://kubernetes.io/examples/application/job/redis/worker.py)

### 使用任务填充队列[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#%E4%BD%BF%E7%94%A8%E4%BB%BB%E5%8A%A1%E5%A1%AB%E5%85%85%E9%98%9F%E5%88%97)

现在，让我们往队列里添加一些 “任务”。在这个例子中，我们的任务是一些将被打印出来的字符串。

启动一个临时的可交互的 Pod 用于运行 Redis 命令行界面。

```shell
kubectl run -i --tty temp --image redis --command "/bin/sh"
```

输出类似于：

```
Waiting for pod default/redis2-c7h78 to be running, status is Pending, pod ready: false
Hit enter for command prompt
```

现在按回车键，启动 Redis 命令行界面，然后创建一个存在若干个工作项的列表。

```shell
# redis-cli -h redis
redis:6379> rpush job2 "apple"
(integer) 1
redis:6379> rpush job2 "banana"
(integer) 2
redis:6379> rpush job2 "cherry"
(integer) 3
redis:6379> rpush job2 "date"
(integer) 4
redis:6379> rpush job2 "fig"
(integer) 5
redis:6379> rpush job2 "grape"
(integer) 6
redis:6379> rpush job2 "lemon"
(integer) 7
redis:6379> rpush job2 "melon"
(integer) 8
redis:6379> rpush job2 "orange"
(integer) 9
redis:6379> lrange job2 0 -1
1) "apple"
2) "banana"
3) "cherry"
4) "date"
5) "fig"
6) "grape"
7) "lemon"
8) "melon"
9) "orange"
```

因此，这个键为 `job2` 的列表就是我们的工作队列。

注意：如果你还没有正确地配置 Kube DNS，你可能需要将上面的第一步改为 `redis-cli -h $REDIS_SERVICE_HOST`。

### 创建镜像[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#%E5%88%9B%E5%BB%BA%E9%95%9C%E5%83%8F)

现在我们已经准备好创建一个我们要运行的镜像。

我们会使用一个带有 Redis 客户端的 Python 工作程序从消息队列中读出消息。

这里提供了一个简单的 Redis 工作队列客户端库，名为 rediswq.py ([下载](https://kubernetes.io/examples/application/job/redis/rediswq.py))。

Job 中每个 Pod 内的 “工作程序” 使用工作队列客户端库获取工作。具体如下：

[`application/job/redis/worker.py`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/redis/worker.py) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/redis/worker.py to clipboard")

```python
#!/usr/bin/env python

import time
import rediswq

host="redis"
# 如果你未在运行 Kube-DNS，请取消下面两行的注释
# import os
# host = os.getenv("REDIS_SERVICE_HOST")

q = rediswq.RedisWQ(name="job2", host=host)
print("Worker with sessionID: " +  q.sessionID())
print("Initial queue state: empty=" + str(q.empty()))
while not q.empty():
  item = q.lease(lease_secs=10, block=True, timeout=2)
  if item is not None:
    itemstr = item.decode("utf-8")
    print("Working on " + itemstr)
    time.sleep(10) # 将你的实际工作放在此处来取代 sleep
    q.complete(item)
  else:
    print("Waiting for work")
print("Queue empty, exiting")
```

你也可以下载 [`worker.py`](https://kubernetes.io/examples/application/job/redis/worker.py)、 [`rediswq.py`](https://kubernetes.io/examples/application/job/redis/rediswq.py) 和 [`Dockerfile`](https://kubernetes.io/examples/application/job/redis/Dockerfile) 文件。然后构建镜像：

```shell
docker build -t job-wq-2 .
```

#### Push 镜像[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#push-%E9%95%9C%E5%83%8F)

对于 [Docker Hub](https://hub.docker.com/)，请先用你的用户名给镜像打上标签， 然后使用下面的命令 push 你的镜像到仓库。请将 `<username>` 替换为你自己的 Hub 用户名。

```shell
docker tag job-wq-2 <username>/job-wq-2
docker push <username>/job-wq-2
```

你需要将镜像 push 到一个公共仓库或者 [配置集群访问你的私有仓库](https://kubernetes.io/zh-cn/docs/concepts/containers/images/)。

如果你使用的是 [Google Container Registry](https://cloud.google.com/tools/container-registry/)， 请先用你的 project ID 给你的镜像打上标签，然后 push 到 GCR 。请将 `<project>` 替换为你自己的 project ID。

```shell
docker tag job-wq-2 gcr.io/<project>/job-wq-2
gcloud docker -- push gcr.io/<project>/job-wq-2
```

### 定义一个 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#%E5%AE%9A%E4%B9%89%E4%B8%80%E4%B8%AA-job)

这是 Job 定义：

[`application/job/redis/job.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/redis/job.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/redis/job.yaml to clipboard")

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: job-wq-2
spec:
  parallelism: 2
  template:
    metadata:
      name: job-wq-2
    spec:
      containers:
      - name: c
        image: gcr.io/myproject/job-wq-2
      restartPolicy: OnFailure
```

请确保将 Job 模板中的 `gcr.io/myproject` 更改为你自己的路径。

在这个例子中，每个 Pod 处理了队列中的多个项目，直到队列中没有项目时便退出。 因为是由工作程序自行检测工作队列是否为空，并且 Job 控制器不知道工作队列的存在， 这依赖于工作程序在完成工作时发出信号。 工作程序以成功退出的形式发出信号表示工作队列已经为空。 所以，只要有任意一个工作程序成功退出，控制器就知道工作已经完成了，所有的 Pod 将很快会退出。 因此，我们将 Job 的完成计数（Completion Count）设置为 1。 尽管如此，Job 控制器还是会等待其它 Pod 完成。

### 运行 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#%E8%BF%90%E8%A1%8C-job)

现在运行这个 Job：

```shell
kubectl apply -f ./job.yaml
```

稍等片刻，然后检查这个 Job。

```shell
kubectl describe jobs/job-wq-2
```

```
Name:             job-wq-2
Namespace:        default
Selector:         controller-uid=b1c7e4e3-92e1-11e7-b85e-fa163ee3c11f
Labels:           controller-uid=b1c7e4e3-92e1-11e7-b85e-fa163ee3c11f
                  job-name=job-wq-2
Annotations:      <none>
Parallelism:      2
Completions:      <unset>
Start Time:       Mon, 11 Jan 2016 17:07:59 -0800
Pods Statuses:    1 Running / 0 Succeeded / 0 Failed
Pod Template:
  Labels:       controller-uid=b1c7e4e3-92e1-11e7-b85e-fa163ee3c11f
                job-name=job-wq-2
  Containers:
   c:
    Image:              gcr.io/exampleproject/job-wq-2
    Port:
    Environment:        <none>
    Mounts:             <none>
  Volumes:              <none>
Events:
  FirstSeen    LastSeen    Count    From            SubobjectPath    Type        Reason            Message
  ---------    --------    -----    ----            -------------    --------    ------            -------
  33s          33s         1        {job-controller }                Normal      SuccessfulCreate  Created pod: job-wq-2-lglf8
```

你可以等待 Job 成功，等待时长有超时限制：

```shell
# 状况名称的检查不区分大小写
kubectl wait --for=condition=complete --timeout=300s job/job-wq-2
```

```shell
kubectl logs pods/job-wq-2-7r7b2
```

```
Worker with sessionID: bbd72d0a-9e5c-4dd6-abf6-416cc267991f
Initial queue state: empty=False
Working on banana
Working on date
Working on lemon
```

你可以看到，其中的一个 Pod 处理了若干个工作单元。

### 替代方案[](https://kubernetes.io/zh-cn/docs/tasks/job/fine-parallel-processing-work-queue/#%E6%9B%BF%E4%BB%A3%E6%96%B9%E6%A1%88)

如果你不方便运行一个队列服务或者修改你的容器用于运行一个工作队列，你可以考虑其它的 [Job 模式](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/#job-patterns)。

如果你有持续的后台处理业务，那么可以考虑使用 `ReplicaSet` 来运行你的后台业务， 和运行一个类似 [https://github.com/resque/resque](https://github.com/resque/resque) 的后台处理库。
## [使用索引作业完成静态工作分配下的并行处理](https://kubernetes.io/zh-cn/docs/tasks/job/indexed-parallel-processing-static/)
**特性状态：** `Kubernetes v1.24 [stable]`

在此示例中，你将运行一个使用多个并行工作进程的 Kubernetes Job。 每个 worker 都是在自己的 Pod 中运行的不同容器。 Pod 具有控制平面自动设置的**索引编号（index number）**， 这些编号使得每个 Pod 能识别出要处理整个任务的哪个部分。

Pod 索引在[注解](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/annotations/) `batch.kubernetes.io/job-completion-index` 中呈现，具体表示为一个十进制值字符串。 为了让容器化的任务进程获得此索引，你可以使用 [downward API](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/downward-api/) 机制发布注解的值。为方便起见， 控制平面自动设置 Downward API 以在 `JOB_COMPLETION_INDEX` 环境变量中公开索引。

以下是此示例中步骤的概述：

1. **定义使用带索引完成信息的 Job 清单**。 Downward API 使你可以将 Pod 索引注解作为环境变量或文件传递给容器。
2. **根据该清单启动一个带索引（`Indexed`）的 Job**。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/job/indexed-parallel-processing-static/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你应该已经熟悉 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/) 的基本的、非并行的用法。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你的 Kubernetes 服务器版本必须不低于版本 v1.21. 要获知版本信息，请输入 `kubectl version`.

### 选择一种方法[](https://kubernetes.io/zh-cn/docs/tasks/job/indexed-parallel-processing-static/#choose-an-approach)

要从工作程序访问工作项，你有几个选项：

1. 读取 `JOB_COMPLETION_INDEX` 环境变量。Job [控制器](https://kubernetes.io/zh-cn/docs/concepts/architecture/controller/) 自动将此变量链接到包含完成索引的注解。
2. 读取包含完整索引的文件。
3. 假设你无法修改程序，你可以使用脚本包装它， 该脚本使用上述任意方法读取索引并将其转换为程序可以用作输入的内容。

对于此示例，假设你选择了选项 3 并且想要运行 [rev](https://man7.org/linux/man-pages/man1/rev.1.html) 实用程序。 这个程序接受一个文件作为参数并按逆序打印其内容。

```shell
rev data.txt
```

你将使用 [`busybox`](https://hub.docker.com/_/busybox) 容器镜像中的 `rev` 工具。

由于这只是一个例子，每个 Pod 只做一小部分工作（反转一个短字符串）。 例如，在实际工作负载中，你可能会创建一个表示基于场景数据制作 60 秒视频任务的 Job 。 此视频渲染 Job 中的每个工作项都将渲染该视频剪辑的特定帧。 索引完成意味着 Job 中的每个 Pod 都知道通过从剪辑开始计算帧数，来确定渲染和发布哪一帧。

### 定义索引作业[](https://kubernetes.io/zh-cn/docs/tasks/job/indexed-parallel-processing-static/#define-an-indexed-job)

这是一个使用 `Indexed` 完成模式的示例 Job 清单：

[`application/job/indexed-job.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/indexed-job.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/indexed-job.yaml to clipboard")

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: 'indexed-job'
spec:
  completions: 5
  parallelism: 3
  completionMode: Indexed
  template:
    spec:
      restartPolicy: Never
      initContainers:
      - name: 'input'
        image: 'docker.io/library/bash'
        command:
        - "bash"
        - "-c"
        - |
          items=(foo bar baz qux xyz)
          echo ${items[$JOB_COMPLETION_INDEX]} > /input/data.txt          
        volumeMounts:
        - mountPath: /input
          name: input
      containers:
      - name: 'worker'
        image: 'docker.io/library/busybox'
        command:
        - "rev"
        - "/input/data.txt"
        volumeMounts:
        - mountPath: /input
          name: input
      volumes:
      - name: input
        emptyDir: {}
```

在上面的示例中，你使用 Job 控制器为所有容器设置的内置 `JOB_COMPLETION_INDEX` 环境变量。 [Init 容器](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/init-containers/) 将索引映射到一个静态值，并将其写入一个文件，该文件通过 [emptyDir 卷](https://kubernetes.io/zh-cn/docs/concepts/storage/volumes/#emptydir) 与运行 worker 的容器共享。或者，你可以 [通过 Downward API 定义自己的环境变量](https://kubernetes.io/zh-cn/docs/tasks/inject-data-application/environment-variable-expose-pod-information/) 将索引发布到容器。你还可以选择从 [包含 ConfigMap 的环境变量或文件](https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-pod-configmap/) 加载值列表。

或者也可以直接 [使用 Downward API 将注解值作为卷文件传递](https://kubernetes.io/zh-cn/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/#store-pod-fields)， 如下例所示：

[`application/job/indexed-job-vol.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/indexed-job-vol.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/indexed-job-vol.yaml to clipboard")

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: 'indexed-job'
spec:
  completions: 5
  parallelism: 3
  completionMode: Indexed
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: 'worker'
        image: 'docker.io/library/busybox'
        command:
        - "rev"
        - "/input/data.txt"
        volumeMounts:
        - mountPath: /input
          name: input
      volumes:
      - name: input
        downwardAPI:
          items:
          - path: "data.txt"
            fieldRef:
              fieldPath: metadata.annotations['batch.kubernetes.io/job-completion-index']
```

### 执行 Job {running-the-job}[](https://kubernetes.io/zh-cn/docs/tasks/job/indexed-parallel-processing-static/#%E6%89%A7%E8%A1%8C-job-running-the-job)

现在执行 Job：

```shell
# 使用第一种方法（依赖于 $JOB_COMPLETION_INDEX）
kubectl apply -f https://kubernetes.io/examples/application/job/indexed-job.yaml
```

当你创建此 Job 时，控制平面会创建一系列 Pod，你指定的每个索引都会运行一个 Pod。 `.spec.parallelism` 的值决定了一次可以运行多少个 Pod， 而 `.spec.completions` 决定了 Job 总共创建了多少个 Pod。

因为 `.spec.parallelism` 小于 `.spec.completions`， 所以控制平面在启动更多 Pod 之前，将等待第一批的某些 Pod 完成。

你可以等待 Job 成功，等待时间可以设置超时限制：

```shell
# 状况名称的检查不区分大小写
kubectl wait --for=condition=complete --timeout=300s job/indexed-job
```

现在，描述 Job 并检查它是否成功。

```shell
kubectl describe jobs/indexed-job
```

输出类似于：

```
Name:              indexed-job
Namespace:         default
Selector:          controller-uid=bf865e04-0b67-483b-9a90-74cfc4c3e756
Labels:            controller-uid=bf865e04-0b67-483b-9a90-74cfc4c3e756
                   job-name=indexed-job
Annotations:       <none>
Parallelism:       3
Completions:       5
Start Time:        Thu, 11 Mar 2021 15:47:34 +0000
Pods Statuses:     2 Running / 3 Succeeded / 0 Failed
Completed Indexes: 0-2
Pod Template:
  Labels:  controller-uid=bf865e04-0b67-483b-9a90-74cfc4c3e756
           job-name=indexed-job
  Init Containers:
   input:
    Image:      docker.io/library/bash
    Port:       <none>
    Host Port:  <none>
    Command:
      bash
      -c
      items=(foo bar baz qux xyz)
      echo ${items[$JOB_COMPLETION_INDEX]} > /input/data.txt

    Environment:  <none>
    Mounts:
      /input from input (rw)
  Containers:
   worker:
    Image:      docker.io/library/busybox
    Port:       <none>
    Host Port:  <none>
    Command:
      rev
      /input/data.txt
    Environment:  <none>
    Mounts:
      /input from input (rw)
  Volumes:
   input:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:  <unset>
Events:
  Type    Reason            Age   From            Message
  ----    ------            ----  ----            -------
  Normal  SuccessfulCreate  4s    job-controller  Created pod: indexed-job-njkjj
  Normal  SuccessfulCreate  4s    job-controller  Created pod: indexed-job-9kd4h
  Normal  SuccessfulCreate  4s    job-controller  Created pod: indexed-job-qjwsz
  Normal  SuccessfulCreate  1s    job-controller  Created pod: indexed-job-fdhq5
  Normal  SuccessfulCreate  1s    job-controller  Created pod: indexed-job-ncslj
```

在此示例中，你使用每个索引的自定义值运行 Job。 你可以检查其中一个 Pod 的输出：

```shell
kubectl logs indexed-job-fdhq5 # 更改它以匹配来自该 Job 的 Pod 的名称
```

输出类似于：

```
xuq
```
## [使用展开的方式进行并行处理](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/)
本任务展示基于一个公共的模板运行多个[Jobs](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/)。 你可以用这种方法来并行执行批处理任务。

在本任务示例中，只有三个工作条目：_apple_、_banana_ 和 _cherry_。 示例任务处理每个条目时打印一个字符串之后结束。

参考[在真实负载中使用 Job](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#using-jobs-in-real-workloads)了解更适用于真实使用场景的模式。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你应先熟悉基本的、非并行的 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/) 的用法。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

任务中的基本模板示例要求安装命令行工具 `sed`。 要使用较高级的模板示例，你需要安装 [Python](https://www.python.org/)， 并且要安装 Jinja2 模板库。

一旦 Python 已经安装好，你可以运行下面的命令安装 Jinja2：

```shell
pip install --user jinja2
```

### 基于模板创建 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#create-jobs-based-on-a-template)

首先，将以下作业模板下载到名为 `job-tmpl.yaml` 的文件中。

[`application/job/job-tmpl.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/application/job/job-tmpl.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy application/job/job-tmpl.yaml to clipboard")

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: process-item-$ITEM
  labels:
    jobgroup: jobexample
spec:
  template:
    metadata:
      name: jobexample
      labels:
        jobgroup: jobexample
    spec:
      containers:
      - name: c
        image: busybox:1.28
        command: ["sh", "-c", "echo Processing item $ITEM && sleep 5"]
      restartPolicy: Never
```

```shell
 # 使用 curl 下载 job-tmpl.yaml
curl -L -s -O https://k8s.io/examples/application/job/job-tmpl.yaml
```

你所下载的文件不是一个合法的 Kubernetes [清单](https://kubernetes.io/zh-cn/docs/reference/glossary/?all=true#term-manifest)。 这里的模板只是 Job 对象的 yaml 表示，其中包含一些占位符，在使用它之前需要被填充。 `$ITEM` 语法对 Kubernetes 没有意义。

#### 基于模板创建清单[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#%E5%9F%BA%E4%BA%8E%E6%A8%A1%E6%9D%BF%E5%88%9B%E5%BB%BA%E6%B8%85%E5%8D%95)

下面的 Shell 代码片段使用 `sed` 将字符串 `$ITEM` 替换为循环变量，并将结果 写入到一个名为 `jobs` 的临时目录。

```shell
# 展开模板文件到多个文件中，每个文件对应一个要处理的条目
mkdir ./jobs
for i in apple banana cherry
do
  cat job-tmpl.yaml | sed "s/\$ITEM/$i/" > ./jobs/job-$i.yaml
done
```

检查上述脚本的输出：

```shell
ls jobs/
```

输出类似于：

```
job-apple.yaml
job-banana.yaml
job-cherry.yaml
```

你可以使用任何一种模板语言（例如：Jinja2、ERB），或者编写一个程序来 生成 Job 清单。

#### 基于清单创建 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#%E5%9F%BA%E4%BA%8E%E6%B8%85%E5%8D%95%E5%88%9B%E5%BB%BA-job)

接下来用一个 kubectl 命令创建所有的 Job：

```shell
kubectl create -f ./jobs
```

输出类似于：

```
job.batch/process-item-apple created
job.batch/process-item-banana created
job.batch/process-item-cherry created
```

现在检查 Job：

```shell
kubectl get jobs -l jobgroup=jobexample
```

输出类似于：

```
NAME                  COMPLETIONS   DURATION   AGE
process-item-apple    1/1           14s        22s
process-item-banana   1/1           12s        21s
process-item-cherry   1/1           12s        20s
```

使用 kubectl 的 `-l` 选项可以仅选择属于当前 Job 组的对象 （系统中可能存在其他不相关的 Job）。

你可以使用相同的 [标签选择算符](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/labels/) 来过滤 Pods：

```shell
kubectl get pods -l jobgroup=jobexample
```

输出类似于：

```
NAME                        READY     STATUS      RESTARTS   AGE
process-item-apple-kixwv    0/1       Completed   0          4m
process-item-banana-wrsf7   0/1       Completed   0          4m
process-item-cherry-dnfu9   0/1       Completed   0          4m
```

我们可以用下面的命令查看所有 Job 的输出：

```shell
kubectl logs -f -l jobgroup=jobexample
```

输出类似于：

```
Processing item apple
Processing item banana
Processing item cherry
```

#### 清理[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#cleanup-1)

```shell
# 删除所创建的 Job
# 集群会自动清理 Job 对应的 Pod
kubectl delete job -l jobgroup=jobexample
```

### 使用高级模板参数[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#%E4%BD%BF%E7%94%A8%E9%AB%98%E7%BA%A7%E6%A8%A1%E6%9D%BF%E5%8F%82%E6%95%B0)

在[第一个例子](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#create-jobs-based-on-a-template)中，模板的每个示例都有一个参数 而该参数也用在 Job 名称中。不过，对象 [名称](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/names/#names) 被限制只能使用某些字符。

这里的略微复杂的例子使用 [Jinja 模板语言](https://palletsprojects.com/p/jinja/) 来生成清单，并基于清单来生成对象，每个 Job 都有多个参数。

在本任务中，你将会使用一个一行的 Python 脚本，将模板转换为一组清单文件。

首先，复制下面的 Job 对象模板到一个名为 `job.yaml.jinja2` 的文件。

```liquid
{% set params = [{ "name": "apple", "url": "http://dbpedia.org/resource/Apple", },
                  { "name": "banana", "url": "http://dbpedia.org/resource/Banana", },
                  { "name": "cherry", "url": "http://dbpedia.org/resource/Cherry" }]
%}
{% for p in params %}
{% set name = p["name"] %}
{% set url = p["url"] %}
---
apiVersion: batch/v1
kind: Job
metadata:
  name: jobexample-{{ name }}
  labels:
    jobgroup: jobexample
spec:
  template:
    metadata:
      name: jobexample
      labels:
        jobgroup: jobexample
    spec:
      containers:
      - name: c
        image: busybox:1.28
        command: ["sh", "-c", "echo Processing URL {{ url }} && sleep 5"]
      restartPolicy: Never
{% endfor %}
```

上面的模板使用 python 字典列表（第 1-4 行）定义每个作业对象的参数。 然后使用 for 循环为每组参数（剩余行）生成一个作业 yaml 对象。 我们利用了多个 YAML 文档（这里的 Kubernetes 清单）可以用 `---` 分隔符连接的事实。 我们可以将输出直接传递给 kubectl 来创建对象。

接下来我们用单行的 Python 程序将模板展开。

```shell
alias render_template='python -c "from jinja2 import Template; import sys; print(Template(sys.stdin.read()).render());"'
```

使用 `render_template` 将参数和模板转换成一个 YAML 文件，其中包含 Kubernetes 资源清单：

```shell
# 此命令需要之前定义的别名
cat job.yaml.jinja2 | render_template > jobs.yaml
```

你可以查看 `jobs.yaml` 以验证 `render_template` 脚本是否正常工作。

当你对输出结果比较满意时，可以用管道将其输出发送给 kubectl，如下所示：

```shell
cat job.yaml.jinja2 | render_template | kubectl apply -f -
```

Kubernetes 接收清单文件并执行你所创建的 Job。

#### 清理[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#cleanup-2)

```shell
# 删除所创建的 Job
# 集群会自动清理 Job 对应的 Pod
kubectl delete job -l jobgroup=jobexample
```

### 在真实负载中使用 Job[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#using-jobs-in-real-workloads)

在真实的负载中，每个 Job 都会执行一些重要的计算，例如渲染电影的一帧， 或者处理数据库中的若干行。这时，`$ITEM` 参数将指定帧号或行范围。

在此任务中，你运行一个命令通过取回 Pod 的日志来收集其输出。 在真实应用场景中，Job 的每个 Pod 都会在结束之前将其输出写入到某持久性存储中。 你可以为每个 Job 指定 PersistentVolume 卷，或者使用其他外部存储服务。 例如，如果你在渲染视频帧，你可能会使用 HTTP 协议将渲染完的帧数据 用 'PUT' 请求发送到某 URL，每个帧使用不同的 URl。

### Job 和 Pod 上的标签[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#job-%E5%92%8C-pod-%E4%B8%8A%E7%9A%84%E6%A0%87%E7%AD%BE)

你创建了 Job 之后，Kubernetes 自动为 Job 的 Pod 添加 [标签](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/labels/)，以便能够将一个 Job 的 Pod 与另一个 Job 的 Pod 区分开来。

在本例中，每个 Job 及其 Pod 模板有一个标签: `jobgroup=jobexample`。

Kubernetes 自身对标签名 `jobgroup` 没有什么要求。 为创建自同一模板的所有 Job 使用同一标签使得我们可以方便地同时操作组中的所有作业。 在[第一个例子](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#create-jobs-based-on-a-template)中，你使用模板来创建了若干 Job。 模板确保每个 Pod 都能够获得相同的标签，这样你可以用一条命令检查这些模板化 Job 所生成的全部 Pod。

**说明：** 标签键 `jobgroup` 没什么特殊的，也不是保留字。 你可以选择你自己的标签方案。 如果愿意，有一些[建议的标签](https://kubernetes.io/zh-cn/docs/concepts/overview/working-with-objects/common-labels/#labels) 可供使用。

### 替代方案[](https://kubernetes.io/zh-cn/docs/tasks/job/parallel-processing-expansion/#%E6%9B%BF%E4%BB%A3%E6%96%B9%E6%A1%88)

如果你有计划创建大量 Job 对象，你可能会发现：

- 即使使用标签，管理这么多 Job 对象也很麻烦。
- 如果你一次性创建很多 Job，很可能会给 Kubernetes 控制面带来很大压力。 一种替代方案是，Kubernetes API 可能对请求施加速率限制，通过 429 返回 状态值临时拒绝你的请求。
- 你可能会受到 Job 相关的[资源配额](https://kubernetes.io/zh-cn/docs/concepts/policy/resource-quotas/) 限制：如果你在一个批量请求中触发了太多的任务，API 服务器会永久性地拒绝你的某些请求。

还有一些其他[作业模式](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/#job-patterns) 可供选择，这些模式都能用来处理大量任务而又不会创建过多的 Job 对象。

你也可以考虑编写自己的[控制器](https://kubernetes.io/zh-cn/docs/concepts/architecture/controller/) 来自动管理 Job 对象。
## [使用 Pod 失效策略处理可重试和不可重试的 Pod 失效](https://kubernetes.io/zh-cn/docs/tasks/job/pod-failure-policy/)
**特性状态：** `Kubernetes v1.26 [beta]`

本文向你展示如何结合默认的 [Pod 回退失效策略](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job#pod-backoff-failure-policy)来使用 [Pod 失效策略](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job#pod-failure-policy)， 以改善 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/) 内处理容器级别或 Pod 级别的失效。

Pod 失效策略的定义可以帮助你：

- 避免不必要的 Pod 重试，以更好地利用计算资源。
- 避免由于 Pod 干扰（例如[抢占](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/pod-priority-preemption/#preemption)、 [API 发起的驱逐](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/api-eviction/)或基于[污点](https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/taint-and-toleration/)的驱逐） 而造成的 Job 失败。

### 准备开始[](https://kubernetes.io/zh-cn/docs/tasks/job/pod-failure-policy/#%E5%87%86%E5%A4%87%E5%BC%80%E5%A7%8B)

你应该已熟悉了 [Job](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job/) 的基本用法。

你必须拥有一个 Kubernetes 的集群，同时你的 Kubernetes 集群必须带有 kubectl 命令行工具。 建议在至少有两个节点的集群上运行本教程，且这些节点不作为控制平面主机。 如果你还没有集群，你可以通过 [Minikube](https://minikube.sigs.k8s.io/docs/tutorials/multi_node/) 构建一个你自己的集群，或者你可以使用下面任意一个 Kubernetes 工具构建：

- [Killercoda](https://killercoda.com/playgrounds/scenario/kubernetes)
- [玩转 Kubernetes](http://labs.play-with-k8s.com/)

你的 Kubernetes 服务器版本必须不低于版本 v1.25. 要获知版本信息，请输入 `kubectl version`.

### 使用 Pod 失效策略以避免不必要的 Pod 重试[](https://kubernetes.io/zh-cn/docs/tasks/job/pod-failure-policy/#using-pod-failure-policy-to-avoid-unecessary-pod-retries)

借用以下示例，你可以学习在 Pod 失效表明有一个不可重试的软件漏洞时如何使用 Pod 失效策略来避免不必要的 Pod 重启。

首先，基于配置创建一个 Job：

[`/controllers/job-pod-failure-policy-failjob.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples//controllers/job-pod-failure-policy-failjob.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy /controllers/job-pod-failure-policy-failjob.yaml to clipboard")

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: job-pod-failure-policy-failjob
spec:
  completions: 8
  parallelism: 2
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: main
        image: docker.io/library/bash:5
        command: ["bash"]
        args:
        - -c
        - echo "Hello world! I'm going to exit with 42 to simulate a software bug." && sleep 30 && exit 42
  backoffLimit: 6
  podFailurePolicy:
    rules:
    - action: FailJob
      onExitCodes:
        containerName: main
        operator: In
        values: [42]
```

运行以下命令：

```sh
kubectl create -f job-pod-failure-policy-failjob.yaml
```

大约 30 秒后，整个 Job 应被终止。通过运行以下命令来查看 Job 的状态：

```sh
kubectl get jobs -l job-name=job-pod-failure-policy-failjob -o yaml
```

在 Job 状态中，看到一个任务状况为 `Failed`，其 `reason` 字段等于 `PodFailurePolicy`。 此外，`message` 字段包含有关 Job 终止更详细的信息，例如： `Container main for pod default/job-pod-failure-policy-failjob-8ckj8 failed with exit code 42 matching FailJob rule at index 0`。

为了比较，如果 Pod 失效策略被禁用，将会让 Pod 重试 6 次，用时至少 2 分钟。

#### 清理[](https://kubernetes.io/zh-cn/docs/tasks/job/pod-failure-policy/#%E6%B8%85%E7%90%86)

删除你创建的 Job：

```sh
kubectl delete jobs/job-pod-failure-policy-failjob
```

集群自动清理这些 Pod。

### 使用 Pod 失效策略来忽略 Pod 干扰[](https://kubernetes.io/zh-cn/docs/tasks/job/pod-failure-policy/#using-pod-failure-policy-to-ignore-pod-disruptions)

通过以下示例，你可以学习如何使用 Pod 失效策略将 Pod 重试计数器朝着 `.spec.backoffLimit` 限制递增来忽略 Pod 干扰。

**注意：**

这个示例的时机比较重要，因此你可能需要在执行之前阅读这些步骤。 为了触发 Pod 干扰，重要的是在 Pod 在其上运行时（自 Pod 调度后的 90 秒内）腾空节点。

1. 基于配置创建 Job：
    
    [`/controllers/job-pod-failure-policy-ignore.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples//controllers/job-pod-failure-policy-ignore.yaml) ![](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg "Copy /controllers/job-pod-failure-policy-ignore.yaml to clipboard")
    
    ```yaml
    apiVersion: batch/v1
       kind: Job
       metadata:
         name: job-pod-failure-policy-ignore
       spec:
         completions: 4
         parallelism: 2
         template:
           spec:
             restartPolicy: Never
             containers:
             - name: main
               image: docker.io/library/bash:5
               command: ["bash"]
               args:
               - -c
               - echo "Hello world! I'm going to exit with 0 (success)." && sleep 90 && exit 0
         backoffLimit: 0
         podFailurePolicy:
           rules:
           - action: Ignore
             onPodConditions:
             - type: DisruptionTarget
       
    ```
    
    运行以下命令：
    
    ```sh
    kubectl create -f job-pod-failure-policy-ignore.yaml
    ```
    

2. 运行以下这条命令检查将 Pod 调度到的 `nodeName`：
    
    ```sh
    nodeName=$(kubectl get pods -l job-name=job-pod-failure-policy-ignore -o jsonpath='{.items[0].spec.nodeName}')
    ```
    

3. 腾空该节点以便在 Pod 完成任务之前将其驱逐（90 秒内）：
    
    ```sh
    kubectl drain nodes/$nodeName --ignore-daemonsets --grace-period=0
    ```
    

4. 查看 `.status.failed` 以检查针对 Job 的计数器未递增：
    
    ```sh
    kubectl get jobs -l job-name=job-pod-failure-policy-ignore -o yaml
    ```
    

5. 解除节点的保护：
    
    ```sh
    kubectl uncordon nodes/$nodeName
    ```
    

Job 恢复并成功完成。

为了比较，如果 Pod 失效策略被禁用，Pod 干扰将使得整个 Job 终止（随着 `.spec.backoffLimit` 设置为 0）。

#### 清理[](https://kubernetes.io/zh-cn/docs/tasks/job/pod-failure-policy/#%E6%B8%85%E7%90%86-1)

删除你创建的 Job：

```sh
kubectl delete jobs/job-pod-failure-policy-ignore
```

集群自动清理 Pod。

### 替代方案[](https://kubernetes.io/zh-cn/docs/tasks/job/pod-failure-policy/#alternatives)

通过指定 Job 的 `.spec.backoffLimit` 字段，你可以完全依赖 [Pod 回退失效策略](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/job#pod-backoff-failure-policy)。 然而在许多情况下，难题在于如何找到一个平衡，为 `.spec.backoffLimit` 设置一个较小的值以避免不必要的 Pod 重试， 同时这个值又足以确保 Job 不会因 Pod 干扰而终止。