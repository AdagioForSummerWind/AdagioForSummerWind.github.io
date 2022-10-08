---
title: "Docker_base_01"
date: 2021-11-23T18:41:07+08:00
lastmod:
tags: [docker]
categories: [docker]
slug:
draft: true
---
# docker简介
Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

Docker的应用场景：
- Web 应用的自动化打包和发布。
- 自动化测试和持续集成、发布。
- 在服务型环境中部署和调整数据库或其他的后台应用。
- 从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境。

Docker 的优点：   
Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

- 快速，一致地交付您的应用程序
    - Docker 允许开发人员使用您提供的应用程序或服务的本地容器在标准化环境中工作，从而简化了开发的生命周期。容器非常适合持续集成和持续交付（CI / CD）工作流程，请考虑以下示例方案：
    - 您的开发人员在本地编写代码，并使用 Docker 容器与同事共享他们的工作。
    - 他们使用 Docker 将其应用程序推送到测试环境中，并执行自动或手动测试。
    - 当开发人员发现错误时，他们可以在开发环境中对其进行修复，然后将其重新部署到测试环境中，以进行测试和验证。
    - 测试完成后，将修补程序推送给生产环境，就像将更新的镜像推送到生产环境一样简单。
- 响应式部署和扩展
    - Docker 是基于容器的平台，允许高度可移植的工作负载。Docker 容器可以在开发人员的本机上，数据中心的物理或虚拟机上，云服务上或混合环境中运行。
    - Docker 的可移植性和轻量级的特性，还可以使您轻松地完成动态管理的工作负担，并根据业务需求指示，实时扩展或拆除应用程序和服务。
- 在同一硬件上运行更多工作负载
    - Docker 轻巧快速。它为基于虚拟机管理程序的虚拟机提供了可行、经济、高效的替代方案，因此您可以利用更多的计算能力来实现业务目标。Docker 非常适合于高密度环境以及中小型部署，而您可以用更少的资源做更多的事情。


hadoop实践：

https://www.runoob.com/w3cnote/hadoop-setup.html
https://www.runoob.com/w3cnote/hdfs-setup.html
passwd命令用不了安装：yum install -y passwd sudo
hadoop_single容器里hadoop用户密码就是hadoop
HDFS是hadoop最主要的守护进程
启动HDFS后用jps查看java进程：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211123211252.png)
因为我用的linux系统是centos8不带桌面环境，不然可以从http://172.17.0.2:9870/查看HDFS面板及详细信息