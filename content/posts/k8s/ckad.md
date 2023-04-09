---
title: "K8s_ckad"
date: 2022-07-14T10:38:08+08:00
lastmod: 2022-07-14
tags: [k8s]
categories: [k8s]
slug: k8s ckad
draft: true
---
## 00
https://www.cnblogs.com/redmoon/p/12050311.html

请你详细介绍CKAD证书
CKAD（Certified Kubernetes Application Developer）是Kubernetes官方认证考试，考察考生在Kubernetes环境下部署、管理和调试应用程序的能力。持有CKAD证书的人可以证明自己在Kubernetes领域具有一定的专业能力，是企业在招聘Kubernetes相关岗位时的重要参考依据。

CKAD考试的主要内容包括：

Kubernetes基础知识：包括Kubernetes的架构、对象模型、命名规则、标签和注释、网络和服务等基础知识。

应用程序部署：包括如何创建、配置和管理Pod、ReplicaSet、Deployment等对象，以及如何在Kubernetes中运行应用程序。

应用程序调试：包括如何诊断应用程序故障、查看容器日志、执行命令和调试应用程序等。

网络设置：包括如何配置Service、Ingress和Network Policy等网络设置，以及如何管理网络流量和安全。

存储管理：包括如何创建和配置PersistentVolume和PersistentVolumeClaim等存储资源，以及如何挂载存储卷和管理数据。

配置管理：包括如何使用ConfigMap和Secret对象管理应用程序的配置和敏感信息。

安全设置：包括如何使用RBAC和Pod Security Policy等安全设置，以及如何保护集群的安全。

CKAD考试采用远程监控的形式，考试时间为2小时，考生需要在考试中完成一系列的实际操作任务。考试通过后，考生将获得Kubernetes官方颁发的证书。考生需要在考试前购买考试券，并具备较好的Kubernetes实践经验和编程能力。

持有CKAD证书的人可以在企业中承担Kubernetes应用程序的设计、部署、管理和调试工作，并获得更好的薪资待遇和职业发展机会。

## 01
1、
请添加工程师的微信  shadowooom
后面的答疑排错，模拟环境激活，都是联系工程师。
请在添加时，备注订单号。

2、
ssh登录node01（11.0.1.112），使用candidate帐号做题，密码为123
在node01上可以免密切换到其他节点，如 ssh master01 或 ssh node02
candidate帐号也可以免密切换到root，如 sudo -i

3、
模拟环境已加密，需要激活后才能使用！
注意：每人仅可授权1个帐号（可供1台电脑使用）。

4、
效率最高 学习路线
<1> 先从百度网盘下载所有资料。
<2> 根据《Windows模拟环境激活方法.pdf》或《苹果MAC模拟环境激活方法.pdf》操作。
<3> 根据《CKAD 1.25 题库（配合模拟环境使用）.pdf》，以及在官网搜索关键字的方法，在【模拟环境】里实操练习。
	所有题目做完后，恢复“CKAD初始化”的快照，即可重复练习。
	使用模拟环境的时候，笔记本不能睡眠待机、断网，虚拟机不要关机、重启、挂起，这些都可能会导致认证失败（可能）。
	不用模拟环境的时候，回退关机状态的快照。
	如果你时间比较琐碎，比如做到第5题没时间继续做了，建议直接回退初始化快照。等下次有空了，则从初始化快照开机，接着继续做第6题。
<4> 参考《K8S 考试流程(报名、约考、注意).pdf》，报名、预约考试。
	考前可微信联系我，确认近期考试情况，以及是否变题等。

5、
注意：
<1> 每道题会提示你执行相应命令来切换k8s集群（kubectl config use-context xxx），如果没有切换集群，则会导致此题不得分。
<2> 有些题需要ssh到其他机器，或者sudo -i切换到root下，操作完成后，记得exit退回到初始的candidate@node-1或者candidate@node-1终端下。
<3> 考试前30分钟，点击“启动考试”。
<4> 新版考试平台，不能用自己的浏览器书签了。每道题给了几个链接可以参考，或者到K8S官网自己搜关键字。
<5> 对于国内考生，可以使用的证件为（任选其一） <1> 护照 <2> 身份证。只有身份证的，必须使用中文姓名约考。有护照的，可以使用中文姓名或姓名拼音约考。
<6> yaml文件里的空格是有严格要求的，不要多，不要少，否则创建时会报错。vi编辑文件  先输入 :set paste 转换一下格式  再按 i ，再粘贴。
<7> 考试时，左边是考题，右边是Ubuntu桌面版。左边的考题里的蓝色字体，左键点一下选中，就自动复制了，就可以在右边shell终端里粘贴了。

6、
其他：
默认有两次考试机会，如果运气不好，赶上换新题了，为了给第二次补考留后路，最好将题目和yaml文件拍下来。
可以使用手机，从侧面拍摄电脑屏幕，因为摄像头仅能照到你的的半身照。
然后我可以针对新题，给你再免费做套模拟环境和解题答案。
以及可以根据提供新题的内容价值，提供折扣或者红包50至200不等！



m4eemvxqdjo

## 必读

模拟环境是在 node01（ 11.0.1.112 ）上做题的。
帐号为 candidate
密码为 123
在此账号下，可以免密 ssh 登录 master01 和 node02（ ssh master01 或 ssh node02 ）
3 台虚拟机的 candidate 帐号也可以免密切换到 root（ sudo -i ）
考试时，是 candidate 或 student 用户，在 node-1 机器上做题的，也不是在 root 的，也不是在 master 的，要注意。
（请注意，文档里的一些截图还是使用的 student 帐号，没有更新。但这不影响的。只是因为在 v1.23 里用的是 student，截图是当时 v1.23 时候截的，但是在 v1.25
里用的帐号是 candidate 了。）
注意，官方的 CKAD 考试环境是有多套的，你考试时，不一定抽到哪套。不同考试环境里面部分题目的内容有很小的变化，但题干都是一样的。在下面的答案解
析中也有详细的描述。
注意阅读《K8S 考试流程(报名、约考、注意).pdf》，几乎所有同学都反馈新考试平台非常卡，70%反馈不用 V-P-N 没法做题，20%反馈用了 V-P-N 还是卡，甚至有
几个同学没有做完题。
所以日常要练习的很熟练，尽量熟练到 80%的题不参考 K8S 网页！！！
新考试平台，不允许外接第二个屏幕，不允许访问自己的浏览器书签。
新考试平台，更像是一个远程的 Ubuntu 桌面，在这个 Ubuntu 桌面里，你可以用终端做题，也可以用 Ubuntu 自带的火狐浏览器到官网自己查。
另外每道题目都会给你一个官方的参考链接，可以先看这个连接，如果找不到自己需要的信息，再从官网自己搜关键字。
注意考试时搜出来的结果，跟平常在官网搜出来结果排序不一样。还有就是官网网页的最右边，是可以点击选择中文/英文的。
最后，能不查官网就尽量不要查，因为真的很卡，很费时间。
每次还原初始化快照后，开机后等 5 分钟，ssh 登录 node01（11.0.1.112）检查一下所有的 pod，确保都为 Running，再开始练习。
CKAD 反馈可能为抽考，将从这 20 多道题中抽取 16 或 17 道题考试。
kubectl get pod -A #（CKAD 会有 2 个 Pod 不是 Running，可忽略）

kubectl 备忘单（kubectl Cheat Sheet）
https://kubernetes.io/zh-cn/docs/reference/kubectl/cheatsheet/
这套《题库》跟《真题》的题干是一样的，区别在于里面的一些 pod、deployment、namespace、ServiceAccount 等参数可能不同而已，因为在真实考试中，也
是会时常变换里面的这些变量参数的。注意理解这些变量的含义，而不要死记硬背答案。

### 1.CronJob-1
Quick Reference
Cluste/配置环境 k8s
Namespace default
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
1、**创建一个名为 ppi 并执行一个运行以下单一容器的 Pod 的 CronJob**：

- name: pi
 image: perl:5
  command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)"]
 配置 CronJob 为：
 ⚫ 每隔 5 分钟执行一次
 ⚫ 保留 2 个已完成的 Job
 ⚫ 保留 4 个失败的 Job
 ⚫ 永不重启 Pod
 ⚫ 在 8 秒后终止 Pod
 2、为测试目的，从 CronJob ppi 中手动创建并执行一个名为 ppi-test 的 Job。(Job 的完成或失败并不重要)

Tasks->Run Jobs->Running Automated Tasks with a CronJob
```
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
#### 答题
1. kubectl config use-context k8s
2. vi cronjob-1.yaml
```yaml
apiVersion: batch/v1
kind: CronJob
metedata:
  name: ppi
spec: 
  schedule: "*/5 * * * *"   #5min
  successfulJobsHistoryLimit: 2
  failedJobsHistoryLimit: 4
  # jobTemplate 是一个 CronJob 资源的字段，用于定义调度的 Job 的模板。而 template 则是一个 PodTemplateSpec 资源的字段，用于定义创建 Pod 的模板。
  jobTemplate: 
    spec:
      activeDeadlineSeconds: 8
      template:
        spec: 
          containers:
          - name: pi
            image: perl:5
            imagePullPolicy: IfNotPresent
            commmand: ["perl", "-Mbugnum=bpi", "-wle". "print bpi(2000)"]
          restartPolicy: Never


apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: ppi
spec:
  schedule: "*/5 * * * *"
  successfulJobsHistoryLimit: 2
  failedJobsHistoryLimit: 4
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: pi
            image: perl:5
            command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)"]
            lifecycle:
              postStart:
                exec:
                  command: ["/bin/sh", "-c", "sleep 8"]
              preStop:
                exec:
                  command: ["/bin/sh", "-c", "sleep 8"]
          restartPolicy: Never

```

3. kubectl apply -f cronjob-1.yaml
4. kubectl get cronjob

### 2.CronJob-2

Quick Reference
Cluste/配置环境 k8s
Namespace default
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Contex
**您需要创建一个将在预定时间运行的 Pod**。
Task
1、在清单文件 /ckad/CKAD00016/periodic.yaml 中定义此 Pod
2、在一个 busybox:stable 容器中运行命令 date ，该命令必须每分钟运行一次，并且必须在 10 秒内完成运行，或者被 Kubernetes 终止运行。
 注意：CronJob 名称和容器名称都必须为 hello
3、在上述清单文件中创建此资源，并验证此 Job 至少成功执行一次。
PS：最后一句话的意思是，如果执行的时间超过 10 秒，则应强制终止此 Job。

#### 答题
1. kubectl config use-context k8s
2. vi /ckad/CKAD00016/periodic.yaml
```yaml
......
metadata:
  name: hello 
spec: 
  schedule: "* * * * *"
  jobTemplate: 
    spec:
      activeDeadlineSeconds: 10
      template:
        spec:
          containers:
          - name: hello
            image: busybox:stable
            imagePullPolicy: IfNotPresent
            # -c选项是Linux和Unix系统上的一个常见命令行选项，表示将后面的字符串作为命令执行。
            command:
            - /bin/sh
            - -c
            - date
          restartPolicy: OnFailure
```
3. kubectl apply -f /ckad/CKAD00016/periodic.yaml
4. kubectl get cronjobs hello
5. kubectl get jobs

### 3.Dockerfile
Quick Reference
Task
一个 Dockerfile 已经存在于 /ckad/DF/Dockerfile
1、使用已存在的 Dockerfile，构建一个名为 centos 和标签为 8.2 的容器镜像。
您可以安装和使用您选择的工具。
2、使用您选择的工具，以 OCI 格式导出构建的容器镜像，并将其存储在 /ckad/DF/centos-8.2.tar

#### 答题
1. kubectl config use-context k8s
2. cd /ckad/DF
3. sudo docker build -t centos:8.2   # "-t"参数指定了镜像的名称和标签，利用当前目录下的dockerfile 文件构建镜像。
4. sudo docker images | grep centos | grep 8.2
5. sudo docker save centos:8.2 > /ckad/DF/centos-8.2.tar
6. ll /ckad/DF/centos-8.2.tar


### 4.限制cpu和内存
Quick Reference
Cluster/配置环境 k8s
Namespace pod-resources
Context
您需要创建一个请求一定量的 CPU 和内存的 Pod。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
在现有的 namespace pod-resources 中创建一个名为 nginx-resources 的 Pod。用 nginx:1.16 的镜像来指定一个容器.
为其容器指定 40m 的 CPU 和 50Mi 的内存的资源请求.

#### 答题
1. kubectl config use-context k8s
2. vi nginx-resources.yaml
```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: nginx-resources
  namespace: pod-resources
spec:
  containers:
  - iamge: nginx:1.16
    name: nginx-resources
    resources:
      requests:
        cpu: "40m"
        memory: "50Mi"
```
3. kubectl apply -f nginx-resources.yaml


### 5.限制cpu和内存2
Quick Reference
Cluster/配置环境 k8s
Namespace haddock
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
haddock namespace 中名为 nosql 的 Deployment 的 Pod 因其容器已用完资源而无法启动。
请更新 nosql Deployment，使 Pod：
⚫ 为其容器请求 15Mi 的内存
⚫ 将内存限制为 haddock namespace 设置的最大内存容量的一半。
您可以在 /ckad/chief-cardinal/nosql.yaml 找到 nosql Deployment 的配置清单。

#### 答题
1. kubectl config use-context k8s
2. kubectl describe ns haddock或者kubectl -n haddock get limitrange或者kubectl -n haddock get limitrange XXXXXXXXXXX -o yaml

方法一：在线修改
kubectl -n haddock edit deployments.apps nosql
添加：
```yaml
spec:
  containers:
  - image: nginx:1.16
    name: nginx
    terminationMessagePath: /dec/termination-log
    terminationMessagePolicy: File
    resources:
      requests:
        memory: "15Mi"
      limits:
        memory: "20Mi"
```
kubectl -n haddock desxribe deployment nosql

方法二：使用Yaml文件，重新创建

cp /ckad/chief-cardinal/nosql.yaml-bak

kubectl delete -f /ckad/chief-cardinal/nosql.yaml
或者
kubectl -n haddock delete deployments.apps nosql

vi /ckad/chief-cardinal/nosql.yaml

在containers的image/name同一层添加或修改：
```yaml
spec: 
  containers:
  - image: nginx:1.16
    name: nginx
    resources:
      requests:
        memory: "15Mi"
      limits:
        memory: "20Mi"
```
kubectl apply -f /ckad/chief-cardinal/nosql.yaml

kubectl -n haddock describe deployment nosql

### 6.运行旧版程序
Quick Reference
Cluster/配置环境 k8s
Namespace garfish
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Context
您需要在运行最新版本 Kubernetes 的 cluster 上部署一个为旧版本 Kubernetes 开发的应用程序。
Task
1、修复清单文件 /ckad/credible-mite/www.yaml 中的任何 API 弃用问题，以便可以将应用程序部署在 k8s cluster 上。
 注意：该应用程序是为 Kubernetes v1.15 开发的。
 k8s cluster 运行着 Kubernetes v1.25
2、请在 garfish namespace 中部署更新后的清单文件/ckad/credible-mite/www.yaml 中指定的应用程序。

#### 答题
kubectl explain deployment.spec
kubectl explain deployment.spec.selector


1. kubectl config use-context k8s
2. vi /ckad/credible-mite/www.yaml
```yaml
apiVersion: apps/vi
……
  replicas: 1
  seletor: 
    matchLabels:
      app: nginx
……
```
4. kubectl apply -f /ckad/credible-mite/www.yaml
5. kubectl -n garfish get all

### 7.金丝雀部署

Quick Reference
Cluster/配置环境 k8s
Namespace goshawk
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Contex
为了测试新的应用程序发布，您需要准备一个金丝雀部署。
Task
namespace goshawk 中名为 chipmunk-service 的 Service 指向名为 current-chipmunk-deployment 的 Deployment 创建的 5 个 Pod。
你可以在/ckad/goshawk/中找到 current-chipmunk-deployment 的清单文件。
1、在同一 namespace 中创建一个相同的 Deployment，名为 canary-chipmunk-deployment
2、修改 Deployment，以便：
⚫ 在 namespace goshawk 中运行的 Pod 的最大数量为 10 个
⚫ chipmunk.service 流量的 40%流向 Pod canary-chipmunk-deployment

#### 答题
kubectl scale -h

scale（缩放命令）：它是 kubectl 命令中的一部分，用于缩放 Kubernetes 中的部署或副本集等资源对象。可以使用该命令增加或减少 Pod 的数量。

1. kubectl config use-context k8s
2. cp /ckad/goshawk/current-chipmunk-deployment.yaml bal.yaml
3. vi /ckad/goshawk/current-chipmunk-deployment.yaml
```yaml
……
metadata:
  name: canary-chipmunk-deployment
  namespace: goshawk
spec:
  replicas: 1
  selector:
    matchLabels:
      app: canary-chipmunk-deployment
      run: dep-svc
    template:
      metadata:
        labels:
          app:canary-chipmunk-deployment
          run: dep-svc
……
```
4. kubectl apply -f /ckad/goshawk/current-chipmunk-deployment.yaml
5. kubectl scale deployment current-chipmunk-deployment --replicas=6 -n goshawk
6. kubectl scale deployment canary-chipmunk-deployment --replicas=4 -n goshawk
7. kubectl get pod -n goshawk

### 8.配置Container安全上下文

Quick Reference
Cluste/配置环境 k8s
Namespace quetzal
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
修改运行在 namespace quetzal 名为 broker-deployment 的现有 Deployment，使其容器
⚫ 以用户 30000 运行
⚫ 禁止特权提升。
您可以在/ckad/daring-moccasin/broker-deployment.yaml 找到 broker-deployment 的清单文件。（模拟环境无此文件，做题也不需要此文件）

#### 答题

1. kubectl config use-contexts k8s
2. kubectl edit deployment broker-deployment -n quetzal
在 containers:下的 image:和 name:下面添加：
```yaml
 spec:
  containers:
  - image: busybox
    imagePullPolicy: IfNotPresent
    name: sec-ctx-demo
    securityContext:
      runAsUser: 3000
      allowPrivilegeEscalation: false
```
### 9.创建 Deployment 并指定环境变量
Quick Reference
Cluster/配置环境 k8s
Namespace ckad00014
Context
您需要新建一个用于运行 NGINX 的 Deployment。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
在现有的 namespace ckad00014 中创建一个运行 6 个 Pod 副本，名为 api 的 Deployment。用 nginx:1.16 的镜像来指定一个容器。
将名为 NGINX_PORT 且值为 8000 的环境变量添加到容器中，然后公开端口 80

#### 答题
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
下面命令背过，如果不会，就使用 kubectl -h 来帮助。
kubectl create deployment api --image=nginx:1.16 --replicas=6 -n ckad00014 --dry-run=client -o yaml > api.yaml
vi api.yaml
在 containers:里的 image:和 name:下添加
```yaml
 env:
 - name: NGINX_PORT
 value: "8000"
 ports:
 - containerPort: 80
```
kubectl apply -f api.yaml


### 10、RBAC 授权
RBAC是Kubernetes中的一种授权机制，用于控制哪些用户可以访问Kubernetes中的资源，以及这些用户对这些资源的访问权限是什么。

Quick Reference
Cluster/配置环境 k8s
Namespace gorilla
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
在名为 honeybee-deployment 的 Deployment 和 namespace gorilla 中的一个 Pod 正在记录错误.
1 查看日志以识别错误消息。
找出错误，包括 User “system:serviceaccount:gorilla:default ”cannot list resource “serviceaccounts” […] in the namespace “gorilla”
2 更新 Deployment honeybee-deployment 以解决 Pod 日志中的错误。
您可以在/ckad/prompt-escargot/honeybee-deployment.yaml 中找到 honeybee-deployment 的清单文件

#### 答题

在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
1、通过 logs 打印错误日志
kubectl -n gorilla get pod
kubectl -n gorilla logs honeybee-deployment-***

考试时，无论是不能 list pods，还是不能 list deployments，或者不能 list serviceaccounts，做法都一样。

2、
（感谢网友 wxid_pc6olyf169ek**）
检查现有的 serviceaccount
kubectl -n gorilla get sa

查看 rolebinding 和 role 的绑定
kubectl -n gorilla get rolebinding

通过 rolebinding，查看 role 和 serviceaccount 的绑定
kubectl -n gorilla describe rolebinding

检查 role，寻找有 get 或 list 权限的 role
kubectl -n gorilla describe role

通过上面的一系列操作，检查发现，gorilla-role 符合题目的要求，它对应的 serviceaccount 是 gorilla-sa

3、设置 honeybee-deployment 的 serviceaccount
（考试时，其实就是跟题目里的 namespace 相同的那个 sa 是正确的。对应这道模拟题，就是 gorilla-sa 是正确的）
kubectl -n gorilla set serviceaccount deployments honeybee-deployment gorilla-sa
等 2 分钟，会自动生成一个新 pod，再次检查，不报错了
kubectl -n gorilla logs honeybee-deployment-***

### 11.ConfigMap
Quick Reference
Cluster/配置环境 k8s
Namespace default

Context
您需要创建一个 Configmap，并在一个 Pod 中使用此 Config Map。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s

Task
1 在 namespace default 中创建一个名为 some-config 并存储着以下键/值对的 Configmap：
key3: value4
2 在 namespace default 中创建一个名为 nginx-configmap 的 Pod。用 nginx:stable 的镜像来指定一个容器。
用存储在 Configmap some-config 中的数据来填充卷，并将其安装在路径/some/path

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
1、
kubectl create configmap some-config --from-literal=key3=value4
2、
vi nginx-configmap.yaml
#添加如下内容
```yaml
apiVersion: v1
kind: Pod
metadata:
 name: nginx-configmap
spec:
 containers:
 - name: nginx-configmap
 image: nginx:stable
 volumeMounts:
 - name: config
 mountPath: "/some/path"
 volumes:
 - name: config
 configMap:
  name: some-config
```
kubectl apply -f nginx-configmap.yaml
检查（考试时，不用检查）
kubectl exec -it nginx-configmap -- /bin/bash
cat /some/path/key3
exit

### 12.Secret
Quick Reference
Cluster/配置环境 k8s
Namespace default
Context
您需要创建一个 Secret，并在一个 Pod 中使用此 Secret。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
1、在 namespace default 中创建一个名为 another-secret 并包含以下单个键/值对的 Secret：
key1: valuel2
2、在 namespace default 中创建一个名为 nginx-secret 的 Pod。用 nginx:1.16 的镜像来指定一个容器。
添加一个名为 COOL_VARIABLE 的环境变量来使用 secret 键 key1 的值。


#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
1、
kubectl create secret generic another-secret --from-literal=key1=value1
2、
vi nginx-secret.yaml
添加如下内容
```yaml
apiVersion: v1
kind: Pod
metadata:
 name: nginx-secret
spec:
  containers:
  - image: nginx:1.16
    name: nginx-secret
    env:
    - name: COOL_VARIABLE
      valueFrom:
        secretKeyRef:
          name: another-secret
          key: key1
```
kubectl apply -f nginx-secret.yaml


### 13.Pod 健康检查 livenessProbe
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context dk8s
Task
由于 Liveness Probe 发生了问题，您无法访问一个应用程序。该应用程序可能在任何 namespace 中运行。
1 找出对应的 Pod 并将其名称和 namespace 写入文件 /ckad/CKAD00011/broken.txt 。使用以下格式：
<namespaceName>/<podName>
文件/ckad/CKAD00011/broken.txt 已存在
2 用 kubectl get events 来获取相关错误事件井将其写入文件 /ckad/CKAD00011/error.txt 。请使用输出格式 wide 。
文件/ckad/CKAD00011/error.txt 已存在。
3 修复故障的 Pod 的 Liveness Probe 问题。


#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context dk8s
livenessProbe 是存活探测
1、
检查集群 dk8s 下的所有命令空间里的 Pods，找出 Liveness probe 问题的 Pod。
kubectl get pods -A
对所有 namespace 下的 pod 逐一检查，考试中会有 5 个 pod 需要你检查。
kubectl describe pod probe-demo -n probe-ns |tail
echo probe-ns/probe-demo > /ckad/CKAD00011/broken.txt


2、
kubectl get events -n probe-ns -o wide |grep probe-demo > /ckad/CKAD00011/error.txt

3、
kubectl get pods probe-demo -n probe-ns -o yaml > probe.yaml
cp probe.yaml bak-probe.yaml
kubectl delete -f probe.yaml
vi probe.yaml

#修改 livenessProbe:字段下的 port 为 8443。
 port: 8443

重新创建此 Pod
kubectl apply -f probe.yaml
检查
kubectl describe pod probe-demo -n probe-ns |tail


### 14.Pod 健康检查 readinessProbe
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context dk8s
修改现有的 deployment probe-http ，增加 readinessProbe 探测器，规格如下：
使用 httpGet 进行探测
探测路径为 /healthz/return200
探测端口为 80
在执行第一次探测前应该等待 15 秒
执行探测的时间间隔为 20 秒

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context dk8s
readinessProbe 是就绪探测
编辑 deployment probe-http
kubectl edit deployment probe-http
在 image 或者 name 下面添加，注意空格对齐位置。
```yaml
 readinessProbe:
  httpGet:
    path: /healthz/return200
    port: 80
  initialDelaySeconds: 15
  periodSeconds: 20
```
检查
kubectl get pod

### 15.升级与回滚
Quick Reference
Cluster/配置环境 k8s
Namespace ckad00015
Context
您需要更新一个应用程序，然后执行该更新的回滚。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
1 更新 namespace ckad00015 中的 Deployment webapp 的比例缩放配置，将 maxSurge 设置为 10%，将 maxUnavailable 设置为 4。
2 更新 Deployment webapp 以让容器镜像 lfccncf/nginx 使用版本标签 1.13.7。
3 将 Deployment webapp 回滚至前一版本。

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
1、
kubectl edit deployment webapp -n ckad00015
按照题目要求，修改红框中的内容
maxSurge 和 maxUnavailable 可以是数字，也可以是百分比，请根据题目的要求写。


2、
先检查一下之前的 image 版本，为 lfccncf/nginx:1.12.2
kubectl -n ckad00015 get deployments webapp -o yaml|grep image

更新为 lfccncf/nginx:1.13.7，这里的 nginx=其中的 nginx 是 containers 的 name 字段。如上图划线处。
kubectl set image deployment webapp nginx=lfccncf/nginx:1.13.7 -n ckad00015

或者 kubectl set image deployment webapp nginx=lfccncf/nginx:1.13.7 -n ckad00015 --record
#注意，这里加不加--record 都行，加上，则下面第 3 步的 rollout history 描述里会是命令，不加，则下面第 3 步的 rollout history 描述里就是<none>
再次检查，image 为 1.13.7 了。
kubectl -n ckad00015 get deployments webapp -o yaml|grep image


3、
如果需要看历史，则可以使用下面这条命令
#kubectl -n ckad00015 rollout history deployment webapp
回滚
kubectl rollout undo deployment webapp -n ckad00015
或者 kubectl rollout undo deployment webapp -n ckad00015 --to-revision=1
#--to-revision 指定回滚的版本，其实，这里指不指定都行，一共就两个版本，1 为 lfccncf/nginx:1.12.2，而 2 为 lfccncf/nginx:1.13.7。
再检查，image 为 1.12.2 了。
kubectl -n ckad00015 get deployments webapp -o yaml|grep image

### 16.Deployment 使用 ServiceAcount
Quick Reference
Cluster/配置环境 k8s
Namespace frontend
Contest
您的应用程序需要使用特定的 ServiceAccount
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
更新在 namespace frontend 中的 Deployment，使其使用现有的 ServiceAccount app

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
方法 1：（推荐）
检查 deployment 的名字
kubectl -n frontend get deployment
开始更新
kubectl -n frontend set serviceaccount deployments frontend-deployment app
方法 2：
kubectl edit deployments frontend-deployment -n frontend
在有 containers:的那个 spec:下添加
 serviceAccountName: app

### 17.更新 Deployment 并暴露 Service
Quick Reference
Cluster/配置环境 k8s
Namespace ckad00017
Contest
您需要扩展一个现有的应用程序，并将其公开在基础设施内。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
1 首先，更新在 namespace ckad00017 中的 Deployment ckad00017-deployment ：
⚫ 以使其运行 5 个 Pod 的副本
⚫ 将以下标签添加到 Pod
tier: dmz
2 然后，在 namespace ckad00017 中创建一个名为 rover 的 NodePort Service 以在 TCP 端口 81 上公开 Deployment ckad00017-deployment

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
1、第一个 Task
方法 1：
以前貌似可以直接通过 edit 来修改，但是新版本的 K8S 不行，在模拟环境里 edit 也是报错的。
考试时，可以优先试一下 edit，如果不行，就使用下面的方法 2。
kubectl edit deployment ckad00017-deployment -n ckad00017
修改 replicas: 1 为 replicas: 5
在图片位置下，与原标签 app: ckad00017-deployment 对齐的位置，添加新标签
 tier: dmz
和
 tier: dmz


方法 2： （推荐）
kubectl get deployment ckad00017-deployment -n ckad00017 -o yaml > ckad00017.yaml
cp ckad00017.yaml bak-ckad00017.yaml
kubectl delete -f ckad00017.yaml
vi ckad00017.yaml
修改 replicas: 1 为 replicas: 5
在图片位置下，与原标签 app: ckad00017-deployment 对齐的位置，添加新标签
 tier: dmz
和
 tier: dmz
重新创建
kubectl apply -f ckad00017.yaml

检查
kubectl get pod -n ckad00017 --show-labels

2、第二个 Task
先确认 pod 容器的端口，就是下面命令--target-port=的端口。
kubectl -n ckad00017 get deployments ckad00017-deployment -o yaml|grep -i "containerPort:"
通过检查发现，容器端口为 81。
kubectl -n ckad00017 expose deployment ckad00017-deployment --name=rover --protocol=TCP --port=81 --target-port=81 --type=NodePort
命令太长记不住，就使用 kubectl -h 和 kubectl expose -h 来帮助。
注意：
--port=81 是暴露的 service 的端口为 81。
--target-port=81 是 Deployment ckad00017-deployment 内 Pod 容器使用的端口，这个需要检查 yaml 文件。如果检查完，发现容器端口为 containerPort: 80，
或者没有 containerPort，则也是用默认的 80。则暴露服务的命令需要写成。
kubectl -n ckad00017 expose deployment ckad00017-deployment --name=rover --protocol=TCP --port=81 --target-port=80 --type=NodePort
检查
kubectl -n ckad00017 get svc
curl 10.103.95.21:81

### 18.NetworkPolicy 网络策略
Quick Reference
Cluster/配置环境 nk8s
Namespace ckad00018
Contest
您需要让一个 Pod 只与其他两个 Pod，而不是和这两个以外的，进行通信。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context nk8s
Task
更新在 namespace ckad00018 中的 Pod ckad00018-newpod ，
使其使用一个只允许 此 Pod 与 Pod front 和 db 之间收发流量的 Networkpolicy。


#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context nk8s
虽然这道题不需要你写 NetworkPolicy，
但是考试中会有多个 NetworkPolicy 供你选择出正确的。
在所有的 NetworkPolicy 的 yaml 文件中导找，
找 ingress 和 egress 都有标签 podSelector 的，
那么这个 NetworkPolicy 就是正确的。
那么 podSelector 里面的标签就是要给 pod 打上的正确标签。
1、先检查所有的 networkpolicy 和 pod 标签
通过查看所有 networkpolicy 得知，
front 和 db 各有一个 networkpolicy，ckad00018-newpod 没有 networkpolicy。
所以查看 front 和 db 的 networkpolicy 标签，把可以跟他们互通的标签，设置给 ckad00018-newpod。
kubectl -n ckad00018 get networkpolicy
kubectl -n ckad00018 get networkpolicy access-front -o yaml

kubectl -n ckad00018 get networkpolicy access-db -o yaml

kubectl -n ckad00018 get pod --show-labels

2、给 ckad00018-newpod 打正确的标签
kubectl label pod -n ckad00018 ckad00018-newpod front-access=true
kubectl label pod -n ckad00018 ckad00018-newpod db-access=true
再次检查标签
kubectl -n ckad00018 get pod --show-labels

真实考试时的截图：（db 换成了 api）

### 19.19、Ingress 排错-1
Quick Reference
Cluster/配置环境 k8s
Namespace ingress-ckad
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
在 namespace ingress-ckad 下，有 deployment service ingress 三个资源已经部署好了，
但是他们的配置有问题，导致的 ingress 网络不通。
3 个资源的配置清单在目录/ckad/CKAD202206 中，请将其修改为正确的，并重新创建。
请注意，这道题的 deployment 是正确的，请不要修改 deployment。


#### 答题 
说明：
port 是 service 端口，即 k8s 中服务之间的访问端口
targetport 是 pod（也就是容器）的端口
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context nk8s
修改配置文件
vi /ckad/CKAD202206/ingress.yaml
需改配置文件，并重新创建 ingress
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: nginx-dm
 namespace: ingress-ckad
spec:
 replicas: 2
 selector:
 matchLabels:
 name: nginx-ing
 template:
 metadata:
 labels:
 name: nginx-ing ## 1 ## 记住 Deployment 的 labels 标签，因为题目说明了 Deployment 是正确的。
 spec:
 containers:
 - name: nginx
 image: vicuu/nginx:hello81
 imagePullPolicy: IfNotPresent
 ports:
 - containerPort: 81 ## 2 ## 记住 Deployment 的容器的端口号，因为题目说明了 Deployment 是正确的。
---
apiVersion: v1
kind: Service
metadata:
 name: nginx-ing-svc ## 3 ## 记住 Service 的 name 名字
 namespace: ingress-ckad
spec:
 ports:
 - port: 80 ## 5 ## 检查一下，确保 Service 的 port 和 Ingress 的 port:number 端口一致
 targetPort: 81 ## 2 ## 修改 Service 的 targetPort 端口与上面 Deployment 的 containerPort 端口号一致，即修改为 81。
 protocol: TCP
 selector:
 name: nginx-ing ## 1 ## 修改 Service 的 selector 为上面 Deployment 的 labels 标签，即修改为 name: nginx-ing，注意是 name 冒号空格 nginx-ing
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
 name: nginx-ingress-test
 namespace: ingress-ckad
 annotations:
 nginx.ingress.kubernetes.io/rewrite-target: /
spec:
 ingressClassName: nginx-example123
 rules:
 - http:
 paths:
 - path: /hello ## 4 ## 检查一下 Ingress 的 path，如果题目里有要求，则修改为题目要求的路径。
 pathType: Prefix
 backend:
 service:
 name: nginx-ing-svc ## 3 ## 修改 Ingress 的 service 为上面 Service 的 name 名字，即修改为 name: nginx-ing-svc
 port:
 number: 80 ## 5 ## 检查一下，确保 Ingress 的 port:number 和 Service 的 port 端口一致

```
kubectl apply -f /ckad/CKAD202206/ingress.yaml

检查：
kubectl -n ingress-ckad get pod,svc,ingress -o wide


### 20.Ingress 排错-2
Quick Reference
Cluster/配置环境 k8s
Namespace ingress-kk
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
在 namespace ingress-kk 下有一个 ingress ，但是它貌似不能被正常访问，
请排除出原因，并修复。
请注意，这道题的 deployment 是正确的，请不要修改 deployment。

#### 答题 
先检查这道题里的 ingress 和 deployment，以及 service，这三项哪里有问题。
kubectl -n ingress-kk get all

经过分析检查，会发现没有 service，所以需创建 service，而 service 需要的内容部分来自 ingress 和 deployment 里的字段。
查看 ingress，记下 service 的 name 和 port 的 number。
kubectl -n ingress-kk get ingress -o yaml

查看 deployment，记下 ports 的 containerPort
kubectl -n ingress-kk get deployments -o yaml

创建 service
vi kk-svc.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
 name: nginxsvc-kk #这里对应 ingress 里的 service: name: 
 namespace: ingress-kk
spec:
 ports:
 - port: 80 #port: 80 是 service 的端口，它对应的是 ingress 里的 port: number
  targetPort: 80 # targetPort: 80 是 deployment 里的容器的端口
  protocol: TCP
 selector:
  name: nginx-lab #这里对应 deployment 的 labels 标签
```
创建 service
kubectl apply -f kk-svc.yaml
检查
kubectl -n ingress-kk get pod,svc,ingress -o wide
curl <ingress 的 ip 地址>
返回提示：Hello World ^_^

### 21.Service、Configmap、Sidecar
Quick Reference
Cluster/配置环境 k8s
Namespace default
Context
位于一个 Pod 中的容器被硬编码使其连接到在特定的端口上的 Service。您需要添加一个 ambassador 容器来更新端口并使其可配置以供将来更新。
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context k8s
Task
1、更新在 namespace default 中的 Service nginxsvc 来暴露端口 9090。
2、在 namespace default 中创建一个名为 haproxy-config 并存储着的/ckad/ambassador/haproxy.cfg 的内容的 ConfigMap。
3、更新在 namespace default 中名为 poller 的 Pod：
首先，添加一个使用 haproxy:lts 镜像、暴露端口 80 并名为 ambassador-container 的 ambassador 容器（大使代理模式）。
最后，ConfigMap haproxy-config 要挂载到 ambassador 容器 ambassador-container 的/usr/local/etc/haproxy/目录。

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
1、
kubectl edit svc nginxsvc -n default
修改为 port: 9090
注意，不是修改 targetPort。

检查
kubectl get svc -n default

2、
kubectl create configmap haproxy-config --from-file=/ckad/ambassador/haproxy.cfg -n default
3、
kubectl -n default get pod poller -o yaml > poller.yaml
cp poller.yaml bak-poller.yaml
kubectl delete -f poller.yaml
vi poller.yaml
在 - image: vicuu/nginx:hello 这一小段的下面（详见下图），添加以下内容（详见下方截图）
……
 - image: haproxy:lts
 name: ambassador-container
  ports:
 - containerPort: 80
 volumeMounts:
 - name: config
 mountPath: "/usr/local/etc/haproxy/"
  readOnly: true
 ……
 在 volumes:下面
 - name: config
 configMap:
  name: haproxy-config
 ……
 另外注意下方图片浅蓝色的部分。如果考试时，已经给你挂载好了，就不要重复挂载了。否则会报错。
 如果没有挂载，则需要手动写上去。
 kubectl apply -f poller.yaml

检查
kubectl get pod poller -n default
kubectl get svc -n default
curl 10.98.61.231:9090

### 22、Deployment 修改镜像
Quick Reference
Cluster/配置环境 nk8s
Namespace default
您必须切换到正确的 Cluster/配置环境。不这样做可能导致零分。
[candidate@node-1] $ kubectl config use-context nk8s
Task
在 namespace default 中的一个 Deployment 由于指定了错误的容器镜像而失败。
找出此 Deployment 并修复问题。

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context nk8s
kubectl get pods -n default

通过查出来的 pod，去编辑它的 deployment。
kubectl edit deployment deploy-nk8s -n default
修改 image:为正确的镜像，比如改为 image: nginx

再次检查
kubectl get pods -n default

### 23、PV/PVC 的使用
Exhibit
Set configuration context:
[candidate@node-1] $ kubectl config use-context sk8s
Context
A project that you are working on has a requirement for persistent data to be available.
Task
To facilitate this, perform the following tasks:
* Create a file on node node02 at /opt/KDSP00101/data/index.html with the content Acct=Finance
* Create a PersistentVolume named task-pv-volume using hostPath and allocate 1Gi to it, specifying that the volume is at /opt/KDSP00101/data on the cluster's 
node. The configuration should specify the access mode of ReadWriteOnce . It should define the StorageClass name exam for the PersistentVolume , which will be 
used to bind PersistentVolumeClaim requests to this PersistenetVolume.
* Create a PefsissentVolumeClaim named task-pv-claim that requests a volume of at least 100Mi and specifies an access mode of ReadWriteOnce
* Create a pod that uses the PersistentVolmeClaim as a volume with a label app: my-storage-app mounting the resulting volume to a mountPath 
/usr/share/nginx/html inside the pod


中文问题：（按此做题）
1、在 node02 节 点 上 创 建 一 个 文 件 /opt/KDSP00101/data/index.html ， 内 容 为 WEPKEY=7789
2、使用 hostPath 创建一个名为 task-pv-volume 的 PersistentVolume ，并分配 2Gi 容量，指定该卷位于集群节点上的 /opt/KDSP00101/data ，访问模式
ReadWriteOnce 。它应该为 PersistentVolume 定义 StorageClass 名称为 keys ，它将被用来绑定 PersistentVolumeClaim 请求到这个 PersistenetVolume。
3、创建一个名为 task-pv-claim 的 PersistentVolumeClaim，请求容量 200Mi ，并指定访问模式 ReadWriteOnce
4、创建一个 pod，使用 PersistentVolmeClaim 作为一个卷，带有一个标签 app:my-storage-app ，将卷挂载到 pod 内的 /usr/share/nginx/html

#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context sk8s
从 node01 上 ssh 跳转到 node02 上
ssh node02
1、
echo WEPKEY=7789 > /opt/KDSP00101/data/index.html
2、
vi task-pv-volume.yaml
添加如下内容
apiVersion: v1
kind: PersistentVolume
metadata:
 name: task-pv-volume
spec:
 storageClassName: keys
 capacity:
 storage: 2Gi
 accessModes:
 - ReadWriteOnce
 hostPath:
  path: "/opt/KDSP00101/data"
 kubectl apply -f task-pv-volume.yaml
 3、
 vi task-pv-claim.yaml
 添加如下内容
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
  name: task-pv-claim
 spec:
  storageClassName: keys
  accessModes:
 - ReadWriteOnce
 resources:
  requests:
  storage: 200Mi
 kubectl apply -f task-pv-claim.yaml
 4、
 注意：
 因为 Kubernetes supports hostPath for development and testing on a single-node cluster.
 所以，测试环境里，需要指定 Pod 在 node02 上运行，即，与 hostPath 类型的 PV 在同一个节点上。
 考试环境，一般只有一个 node 节点，所以不需要指定。但是最好你考试时，kubectl get node 先检查一下，如果有多个 node 节点，则也需要指定 Pod 在创建 PV
 的那个节点上运行，即，使用 nodeSelector。
 vi task-pv-pod.yaml
 添加如下内容
 apiVersion: v1
 kind: Pod
 metadata:
  labels:
  app: my-storage-app
  name: task-pv-pod
 spec:
  containers:
 - name: nginx
 image: nginx
  volumeMounts:
 - mountPath: "/usr/share/nginx/html"
 name: task-pv-storage
  nodeSelector:
  kubernetes.io/hostname: node02 #使用 kubectl get node --show-labels 确定这个 node 的特有标签
  volumes:
 - name: task-pv-storage
 persistentVolumeClaim:
  claimName: task-pv-claim
 kubectl apply -f task-pv-pod.yaml
 测试
 kubectl get pod -o wide | grep task-pv-pod
 curl 10.244.2.35
 退回到 node01
 exit


### 24、Pod 多容器--边车
Exhibit
Set configuration context:
[candidate@node-1] $ kubectl config use-context k8s
Context
Given a container that writes a log file in format A and a container that converts log files from format A to format B, create a deployment that runs both containers 
such that the log files from the first container are converted by the second container, emitting logs in format B.
Task
* Create a deployment named deployment-xyz in the default namespace, that:
* Includes a primary lfccncf/busybox:1 container, named logger-dev
* includes a sidecar Ifccncf/fluentd:v0.12 container, named adapter-zen
* Mounts a shared volume /tmp/log on both containers, which does not persist when the pod is deleted
* Instructs the logger-dev
container to run the command
which should output logs to /tmp/log/input.log in plain text format, with example values:
* The adapter-zen sidecar container should read /tmp/log/input.log and output the data to /tmp/log/output.* in Fluentd JSON format. Note that no knowledge of 
Fluentd is required to complete this task: all you will need to achieve this is to create the ConfigMap from the spec file provided at /opt/KDMC00102/fluentd-configma p.yaml , and mount that ConfigMap to /fluentd/etc in the adapter-zen sidecar container
中文问题：（按此做题）
⚫ 在 default 命名空间创建一个 deployment 名为 deploymenb-web
⚫ 包含一个主容器 lfccncf/busybox:1 ，名称 logger-123
⚫ 包含一个边车容器 lfccncf/fluentd:v0.12 ，名称 adaptor-dev
⚫ 在两个容器上挂载一个共享卷 /ckad/log ,当 pod 删除，这个卷不会持久。
⚫ 在 logger-123 容器运行以下命令：
while true; do
echo “i luv cncf” >> /ckad/log/input.log;
sleep 10;
done
结果会文本输出到 /ckad/log/input.log ，格式示例如下：
i luv cncf
i luv cncf
i luv cncf
⚫ adaptor-dev 容器读取 /ckad/log/input.log ，并将数据输出到 /ckad/log/output.* 格式为 Fluentd JSON。
请 注 意 ： 完 成 此 任 务 不 需 要 了 解 Fluentd ， 完 成 此 任 务 所 需 要 的 知 识 从/ckad/KDMC00102/fluentd-configmap.yaml 提供规范文件中
创建 configmap ，并将该 configmap 挂载到边车容器 adapter-dev 中的 /fluentd/etc


#### 答题 
在 node01 使用 candidate 用户做题
考试时执行，切换集群。模拟环境中不需要执行。
kubectl config use-context k8s
cp /ckad/KDMC00102/fluentd-configmap.yaml bak-2.yaml
vi /ckad/KDMC00102/fluentd-configmap.yaml
修改核对以下内容：
注意，考试中，这个 fluentd-configmap.yaml 文件一般是正确的，一般不需要你载更改。模拟环境为了让你了解原理，所以故意写错了。
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
 name: fluentd-config
data:
 fluent.conf: |
 <source>
 @type tail
 format none
 path /ckad/log/input.log
 tag cncf #修改为 cncf 或其他包含在日志中的字段。因为从题中 echo “i luv cncf” >> /ckad/log/input.log 得知内容“i luv cncf”要写到/ckad/log/output，
所以 tag 可以过滤 cncf 字段，而得到内容“i luv cncf”。
 <parse>
 @type none
 </parse>
 </source>
 <match cncf> #修改为 cncf 或者* ，match 是匹配上面的 tag cncf，如果写为 cncf，则只匹配 tag cncf，如果写为*，则匹配上面所有的 tag。在这道题里，
也可以写为* 。
 @type file
 path /ckad/log/output #创建完成后，会自动生成/ckad/log/output.20220314.b5da2f3157860121f，其中 output.* 星号是随机字符。
 </match>
```
kubectl apply -f /ckad/KDMC00102/fluentd-configmap.yaml
vi sidecar.yaml
添加以下内容
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: deploymenb-web
 namespace: default
spec:
 replicas: 1
 selector:
 matchLabels:
 app: nginx
 template:
 metadata:
 labels:
 app: nginx
 spec:
 containers:
 - image: lfccncf/busybox:1
 name: logger-123
 args:
 - /bin/sh
 - -c
 - >
 while true;
 do
 echo "i luv cncf" >> /ckad/log/input.log;
 sleep 10;
 done
 volumeMounts:
 - name: log
 mountPath: /ckad/log
 - image: lfccncf/fluentd:v0.12
 name: adaptor-dev
 volumeMounts:
 - name: log
 mountPath: /ckad/log
 - name: config
 mountPath: "/fluentd/etc"
 volumes:
 - name: log
 emptyDir: {}
 - name: config
 configMap:
 name: fluentd-config
```
创建
kubectl apply -f sidecar.yaml
检查
kubectl get deploy,pod | grep deploymenb-web
检查（蓝色字体是 deployment 随机创建的 pod 名字）
kubectl exec deploymenb-web-857b985b8b-zmcfj -c adaptor-dev -- tail /ckad/log/input.log



## 附录

### 在k8s里，cronjob有什么用

在Kubernetes中，CronJob是一种**定时任务**类型，类似于Linux中的Cron。CronJob是在规定时间执行的任务，也就是大家俗称的排程。CronJob在Kubernetes中使用的是Cron格式，必须遵循Cron格式才能在正确的时间点执行任务。Cron格式的写法类似于 \* \* \* \* _，其中第五个星号代表的是星期（0~7），其中星期日可用0或7表示。如果今天每3小时要做一次任务，可以在小时的星号那边加个 /3 就代表每 3 小时要做一次任务，写法会像这样：_ \*/3 \* \* \* 。 [\[3\]](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-cronjob-168083aba87e)

CronJob在Kubernetes中也要运行，所以会产生相对应的Pod，因此也会吃到机器的资源。当不再需要CronJob时，可以用kubectl delete cronjob <cronjob name>命令将其删除。 [\[0\]](https://kubernetes.io/zh-cn/docs/tasks/job/automated-tasks-with-cron-jobs/)

CronJob可以完成大型计算和一些批处理任务，比如转码文件、获取部分文件和目录，机器学习中的训练任务等。在Kubernetes中，通过创建工作负载资源Job可完成这些任务。 [\[2\]](https://juejin.cn/post/7022537961333850119)

Kubernetes的CronJob写法必须要遵循Cron格式，这样才能在正确的时间点执行任务。Cron格式的写法类似于 \* \* \* \* _，其中第五个星号代表的是星期（0~7），其中星期日可用0或7表示。如果今天每3小时要做一次任务，可以在小时的星号那边加个 /3 就代表每 3 小时要做一次任务，写法会像这样：_ \*/3 \* \* \* 。 [\[4\]](https://blog.csdn.net/zhengzaifeidelushang/article/details/122780041)

CronJob的建立可以用kubectl create cronjob <cronjob name>命令，在建立完成后可以用kubectl get cronjob <cronjob name>命令查看建立好的CronJob。由于这些任务都要在Kubernetes中运行，所以每个任务都会是一个Pod，可以用kubectl get jobs命令查看Pod的建立状况。想知道目前CronJob有多少个任务可以用kubectl get jobs --watch的方式查看。 [\[3\]](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-cronjob-168083aba87e)


### k8s lifecycle字段是什么意思


在Kubernetes中，lifecycle字段是容器的生命周期事件，其中包括两个事件：postStart和preStop。postStart事件在容器启动后立即发送，而preStop事件在容器被终止之前发送。容器可以为每个事件指定一个处理程序。当容器启动后，Kubernetes将立即发送postStart事件。在容器被终止之前，Kubernetes将发送一个preStop事件。容器可以为每个事件指定一个处理程序。[\[0\]](https://kubernetes.io/zh/docs/concepts/workloads/pods/pod-lifecycle/)[\[1\]](https://kubernetes.io/zh/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)

Pod的Lifecycle字段中可以定义postStart和preStop事件的处理函数，Kubernetes支持不同类型的事件处理方式，如Exec、HTTP Get和TCP Socket等。在Lifecycle字段中，用户可以为每个事件定义一个处理程序，Kubernetes将在容器的生命周期事件发生时调用这些处理程序。[\[1\]](https://kubernetes.io/zh/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)[\[3\]](https://blog.csdn.net/yy8623977/article/details/124805042)

在Lifecycle字段中，可以定义容器的重启策略。Pod的spec中包含一个restartPolicy字段，其可能取值包括Always、OnFailure和Never。默认值是Always。restartPolicy适用于Pod中的所有容器。当Pod中的容器退出时，kubelet会按指数回退方式计算重启的延迟，其最长延迟为5分钟。一旦某容器执行了10分钟并且没有出现问题，kubelet对该容器的重启回退计时器执行重置操作。[\[0\]](https://kubernetes.io/zh/docs/concepts/workloads/pods/pod-lifecycle/)

在Lifecycle字段中，用户可以为每个事件定义一个处理程序，Kubernetes将在容器的生命周期事件发生时调用这些处理程序。例如，可以使用postStart事件在容器启动后执行一些初始化操作，如创建临时文件和目录，加载配置文件等。而preStop事件可用于在容器被终止之前执行一些清理操作，如保存数据、关闭连接等。[\[1\]](https://kubernetes.io/zh/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)

下面是一个在Lifecycle字段中定义postStart和preStop事件处理函数的示例：

`apiVersion: v1 kind: Pod metadata:  name: lifecycle-demo spec:  containers:  - name: lifecycle-demo-container
 image: nginx lifecycle:  postStart:  exec:  command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]  preStop:  exec:  command: ["/bin/sh","-c","nginx -s quit; while killall -0 nginx; do sleep 1; done"]` 

在上述配置文件中，postStart命令在容器的/usr/share目录下写入文件message。命令preStop负责优雅地终止nginx服务。当因为失效而导致容器终止时，这一处理方式很有用。[\[1\]](https://kubernetes.io/zh/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)



### k8s spec字段是什么意思


K8s中的spec字段是一个对象的规范属性，描述了对象的期望状态。下面是一些关于K8s中spec字段的信息：

- 在Kubernetes对象的.yaml文件中，你需要设置以下字段的值[\[1\]](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/):
    - apiVersion：你正在使用的Kubernetes API版本，用于创建此对象。
    - metadata：帮助唯一标识对象的数据，包括名称字符串、UID和可选的命名空间。
- 除了metadata字段外，每个Kubernetes对象都包含两个嵌套的对象字段来管理对象的配置：对象规范（object spec）和对象状态（object status）[\[1\]](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)。
    - 对于具有规范的对象，你需要在创建对象时设置它，提供你想要资源具有的特征的描述。
    - 状态描述对象的当前状态，由Kubernetes系统及其组件提供和更新。Kubernetes控制平面不断主动管理每个对象的实际状态，以匹配你提供的期望状态[\[1\]](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)。
    - 例如，在Kubernetes中，Deployment是一个可以代表在集群上运行的应用程序的对象。当你创建Deployment时，你可能会将Deployment规范设置为指定你要运行三个应用程序的副本。Kubernetes系统读取Deployment规范并启动三个你想要的应用程序实例 - 更新状态以匹配你的规范。如果这些实例中的任何一个失败了（状态更改），Kubernetes系统通过对规范和状态之间的差异进行纠正来响应该变化 - 在这种情况下，启动一个替换实例[\[1\]](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)。
- spec字段是资源对象的规范属性，描述了资源对象的期望状态[\[0\]](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/field-selectors/)[\[3\]](https://www.cnblogs.com/panwenbin-logs/p/9895953.html)。
- spec字段是Kubernetes资源对象的一个重要属性，由于不同的资源类型有不同的属性，因此不同的资源类型支持不同的字段选择器[\[0\]](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/field-selectors/)。
- metadata.name和metadata.namespace是所有资源类型都支持的字段[\[0\]](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/field-selectors/)。
- 你可以使用=、==和!=操作符在字段选择器中[\[0\]](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/field-selectors/)。
- 你可以使用多种资源类型的字段选择器[\[0\]](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/field-selectors/)。
- 下面是一些常见的spec字段及其含义[\[3\]](https://www.cnblogs.com/panwenbin-logs/p/9895953.html)：
    - spec.containers：一个容器数组，每个容器都需要定义一个镜像，以及如何运行该镜像。
    - spec.imagePullSecrets：一个数组，其中包含用于从私有Docker镜像仓库拉取镜像的凭证。
    - spec.restartPolicy：定义容器退出时如何重启容器的策略。
    - spec.volumes：定义Pod中的数据卷。




### k8s apiVersion有哪些，batch/v1又代表什么，请使用中文

###### Answer

Kubernetes中的apiVersion是指Kubernetes API中的版本号。每当Kubernetes发布更新时，就会创建一个新的apiVersion [\[0\]](https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html)。apiVersion是Kubernetes对象定义中必需的字段，用于指定该对象使用的API版本。Kubernetes的官方文档提供了少量关于apiVersion的指导，但是这个指南可以提供每个版本的详细解释和使用建议 [\[0\]](https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html)。

以下是一些常见的apiVersion及其含义 [\[0\]](https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html) [\[1\]](https://blog.csdn.net/xixihahalelehehe/article/details/112789211) [\[4\]](https://www.cnblogs.com/luoluo160717/p/15757266.html):

- v1：这是Kubernetes API的第一个稳定版本，包含许多核心对象。
- apps/v1：apps是Kubernetes中最常见的API组，其中许多核心对象都来自它和v1。它包括与在Kubernetes上运行应用程序相关的功能，例如Deployments、RollingUpdates和ReplicaSets。
- autoscaling/v1：此API版本允许根据不同的资源使用度量标准自动缩放Pod。此稳定版本仅包括对CPU缩放的支持，但是将来的alpha和beta版本将允许您根据内存使用情况和自定义度量标准进行缩放。
- batch/v1：batch API组包含与批处理和类似作业的任务相关的对象（而不是像应用程序一样的任务，如无限期地运行Web服务器）。此apiVersion是这些API对象的第一个稳定版本。
- batch/v1beta1：**Kubernetes中用于批处理对象的新功能的beta版本，特别是包括CronJobs，可让您在特定时间或周期性地运行作业。**
- certificates.k8s.io/v1beta1：此API版本添加了功能，用于验证集群中的网络证书以进行安全通信。您可以在官方文档中阅读更多相关信息。
- extensions/v1beta1：此API版本包括Kubernetes许多新的、常用的功能。Deployments、DaemonSets、ReplicaSets和Ingresses在此版本中都得到了重大改进。请注意，在Kubernetes 1.6中，一些对象从extensions移动到了特定的API组（例如apps）。当这些对象移出beta时，预计它们将位于特定的API组，例如apps/v1。extensions/v1beta1的使用正在变得不建议使用。尽可能使用特定的API组，具体取决于您的Kubernetes集群版本。
- policy/v1beta1：此apiVersion添加了设置Pod破坏预算和有关Pod安全的新规则的功能。
- rbac.authorization.k8s.io/v1：此apiVersion包括Kubernetes基于角色的访问控制的额外功能。这有助于您保护您的群集。

在Kubernetes中使用的apiVersion通常是由Kubernetes版本和API对象类型决定的。要查看Kubernetes中可用的所有API版本，可以使用以下命令：`kubectl api-versions` [\[3\]](https://stackoverflow.com/questions/52711326/kubernetes-how-to-know-latest-supported-api-version)。


### 金丝雀部署
金丝雀部署（Canary Deployment）是一种软件部署策略，其目的是在部署新版本的应用程序或服务时，最小化生产环境中潜在的故障和风险。它的名称来自于矿工在地下开采时用金丝雀测试空气质量的做法，以避免毒气中毒。

在金丝雀部署中，新版本的应用程序或服务首先部署到一小部分的生产环境，这部分被称为金丝雀环境。在这个环境中，新版本可以被一小部分用户或流量使用，以测试其可靠性和性能。

如果在金丝雀环境中没有出现任何问题，那么新版本就可以继续部署到整个生产环境中。否则，问题可以被识别和解决，避免影响整个生产环境。

使用金丝雀部署策略的另一个好处是可以逐步升级生产环境。在升级过程中，可以先将新版本的应用程序或服务部署到一小部分的生产环境中，然后逐步增加部署的规模，直到所有的生产环境都更新为新版本。

总之，金丝雀部署是一种非常有用的部署策略，可以帮助开发团队在部署新版本的应用程序或服务时最小化风险，并在出现问题时能够快速识别和解决。


### ServiceAccount
在Kubernetes中，ServiceAccount是给运行在Pod的程序使用的身份认证，Pod容器的进程需要访问API Server时用的就是ServiceAccount账户。Service Account不是给Kubernetes集群的用户使用的，而是给Pod里面的进程使用的，它为Pod提供必要的身份认证。当Pod内部的进程需要访问 Kubernetes API Server 时，就需要使用 ServiceAccount 账户.


### 授权机制
在Kubernetes中，有两种授权机制，分别是Role-Based Access Control（RBAC）和 Attribute-Based Access Control（ABAC）。其中，RBAC是官方推荐的授权机制。RBAC规定了三种抽象的Kubernetes对象，分别是Role、ClusterRole和RoleBinding。其中，Role和ClusterRole定义了一组访问权限，而RoleBinding和ClusterRoleBinding将Role或ClusterRole绑定到用户或用户组，从而授予相应的权限。因此，RoleBinding中的Role是指访问权限的集合，而Binding则是将Role与用户或用户组绑定起来，从而授权访问。