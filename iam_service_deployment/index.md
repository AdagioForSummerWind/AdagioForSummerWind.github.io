# iam_service_deployment

# 服务部署
## 40 | 软件部署实战（上）：部署方案及负载均衡、高可用组件介绍

接下来，我们就进入到这门课的最后一个模块，服务部署部分的学习。在这一模块中，我会带着你一步一步地部署一个生产级可用的 IAM 应用。在 03 讲 中，我们快速在单机上部署了 IAM 系统，但这样的系统缺少高可用、弹性扩容等能力，是很脆弱的，遇到流量波峰、发布变更很容易出问题。在系统真正上线前，我们需要重新调整部署架构，来保证我们的系统具有负载均衡、高可用、弹性伸缩等核心运维能力。考虑到你手中的系统资源有限，这一模块会尽量简单地展示如何部署一个相对高可用的 IAM 系统。按照我讲的部署方法，基本上可以上线一个中小型的系统。

在这一模块中，我会介绍两种部署方式。第一种是传统的部署方式，基于物理机 / 虚拟机来部署，容灾、弹性伸缩能力要部署人员自己实现。第二种是容器化部署方式，基于 Docker、Kubernetes 来部署，容灾、弹性伸缩等能力，可以借助 Kubernetes 自带的能力来实现。接下来的三讲，我们先来看下传统的部署方式，也就是如何基于虚拟机来部署 IAM 应用。今天我主要讲跟 IAM 部署相关的两个组件，Nginx + Keepalived 的相关功能。

接下来的三讲，我们先来看下传统的部署方式，也就是如何基于虚拟机来部署 IAM 应用。今天我主要讲跟 IAM 部署相关的两个组件，Nginx + Keepalived 的相关功能。

### 部署方案
先来整体看下我们的部署方案。这里，我采用 Nginx + Keepalived 来部署一个高可用的架构，同时将组件都部署在内网，来保证服务的安全和性能。部署需要两台物理机 / 虚拟机，组件之间通过内网访问。所需的服务器如下表所示：

![img](https://static001.geekbang.org/resource/image/e0/1d/e0a3323831768fbe7f45085ca4a53a1d.jpg?wh=1920x719)

两台服务器均为腾讯云 CVM，VIP（Virtual IP，虚拟 IP）为10.0.4.99。部署架构如下图所示：

![img](https://static001.geekbang.org/resource/image/fc/9d/fc5331a780d45a7de6223d6ff3c86f9d.jpg?wh=1920x1197)


这里我来具体介绍下图中的部署架构。部署采用的这两台 CVM 服务器，一主一备，它们共享同一个 VIP。同一时刻，VIP 只在一台主设备上生效，当主服务器出现故障时，备用服务器会自动接管 VIP，继续提供服务。

主服务器上部署了iam-apiserver、iam-authz-server、iam-pump和数据库mongodb、redis、mysql。备服务器部署了iam-apiserver、iam-authz-server和iam-pump。备服务器中的组件通过内网10.0.4.20访问主服务器中的数据库组件。

主备服务器同时安装了 Keepalived 和 Nginx，通过 Nginx 的反向代理功能和负载均衡功能，实现后端服务iam-apiserver和iam-authz-server的高可用，通过 Keepalived 实现 Nginx 的高可用。

我们通过给虚拟 IP 绑定腾讯云弹性公网 IP，从而使客户端可以通过外网 IP 访问内网的 Nginx 服务器（443端口），如果想通过域名访问内网，还可以申请域名指向该弹性公网 IP。

通过以上部署方案，我们可以实现一个具有较高可用性的 IAM 系统，它主要具备下面这几个能力。

- 高性能：可以通过 Nginx 的负载均衡功能，水平扩容 IAM 服务，从而实现高性能。
- 具备容灾能力：通过 Nginx 实现 IAM 服务的高可用，通过 Keepalived 实现 Nginx 的高可用，从而实现核心组件的高可用。
- 具备水平扩容能力：通过 Nginx 的负载均衡功能，实现 IAM 服务的水平扩容。
- 高安全性：将所有组件部署在内网，客户端只能通过VIP:443端口访问 Nginx 服务，并且通过开启 TLS 认证和 JWT 认证，保障服务有一个比较高的安全性。因为是腾讯云 CVM，所以也可以借助腾讯云的能力再次提高服务器的安全性，比如安全组、DDoS 防护、主机安全防护、云监控、云防火墙等。

这里说明下，为了简化 IAM 应用的安装配置过程，方便你上手实操，有些能力，例如数据库高可用、进程监控和告警、自动伸缩等能力的构建方式，这里没有涉及到。这些能力的构建方式，你可以在日后的工作中慢慢学习和掌握。

接下来，我们看下这个部署方案中用到的两个核心组件，Nginx 和 Keepalived。我会介绍下它们的安装和配置方法，为你下一讲的学习做准备。

### Nginx 安装和配置
#### Nginx 功能简介

这里先简单介绍下 Nginx。Nginx 是一个轻量级、高性能、开源的 HTTP 服务器和反向代理服务器。IAM 系统使用了 Nginx 反向代理和负载均衡的功能，下面我就来分别介绍下。

为什么需要反向代理呢？在实际的生产环境中，服务部署的网络（内网）跟外部网络（外网）通常是不通的，这就需要一台既能够访问内网又能够访问外网的服务器来做中转，这种服务器就是反向代理服务器。Nginx 作为反向代理服务器，简单的配置如下：

```
server {
    listen      80;
    server_name  iam.marmotedu.com;
    client_max_body_size 1024M;

    location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass  http://127.0.0.1:8080/;
        client_max_body_size 100m;
    }
}
```

Nginx 的反向代理功能，能够根据不同的配置规则转发到不同的后端服务器上。假如我们在 IP 为x.x.x.x的服务器上，用上面说的 Nginx 配置启动 Nginx，当我们访问http://x.x.x.x:80/时，会将请求转发到http://127.0.0.1:8080/。listen      80指定了 Nginx 服务器的监听端口，proxy_pass  http://127.0.0.1:8080/则指定了转发路径。


Nginx 另一个常用的功能是七层负载均衡。所谓的负载均衡，就是指当 Nginx 收到一个 HTTP 请求后，会根据负载策略将请求转发到不同的后端服务器上。比如 iam-apiserver 部署在两台服务器 A 和 B 上，当请求到达 Nginx 后，Nginx 会根据 A 和 B 服务器上的负载情况，将请求转发到负载较小的那台服务器上。

这里要求 iam-apiserver 是无状态的服务。Nginx 有多种负载均衡策略，可以满足不同场景下的负载均衡需求。

#### Nginx 安装步骤
接下来，我就来介绍下如何安装和配置 Nginx。我们分别在10.0.4.20和10.0.4.21服务器上执行如下步骤，安装 Nginx。


在 CentOS 8.x 系统上，我们可以使用 yum 命令来安装，具体安装过程可以分为下面 4 个步骤。第一步，安装 Nginx：

```
$ sudo yum -y install nginx
```
第二步，确认 Nginx 安装成功：

```
$ nginx -v
nginx version: nginx/1.14.1
```
第三步，启动 Nginx，并设置开机启动：


```
$ sudo systemctl start nginx
$ sudo systemctl enable nginx
```
Nginx 默认监听80端口，启动 Nginx 前要确保80端口没有被占用。当然，你也可以通过修改 Nginx 配置文件/etc/nginx/nginx.conf修改 Nginx 监听端口。

第四步，查看 Nginx 启动状态：

```
$ systemctl status nginx
```
输出中有active (running)字符串，说明成功启动。如果 Nginx 启动失败，你可以查看/var/log/nginx/error.log日志文件，定位错误原因。

### Keepalived 安装和配置
Nginx 自带负载均衡功能，并且当 Nginx 后端某个服务器故障后，Nginx 会自动剔除该服务器，将请求转发到可用的服务器，通过这种方式实现后端 API 服务的高可用。但是 Nginx 是单点的，如果 Nginx 挂了，后端的所有服务器就都不能访问，所以在实际生产环境中，也需要对 Nginx 做高可用。

业界最普遍采用的方法是通过 Keepalived 对前端 Nginx 实现高可用。Keepalived + Nginx 的高可用方案具有服务功能强大、维护简单等特点。接下来，我们来看下如何安装和配置 Keepalived。

#### Keepalived 安装步骤
我们分别在10.0.4.20和10.0.4.21服务器上执行下面 5 个步骤，安装 Keepalived。第一步，下载 Keepalived 的最新版本（这门课安装了当前的最新版本 2.1.5）：

```
$ wget https://www.keepalived.org/software/keepalived-2.1.5.tar.gz
```
第二步，安装 Keepalived：

```
$ sudo yum -y install openssl-devel # keepalived依赖OpenSSL，先安装依赖
$ tar -xvzf keepalived-2.1.5.tar.gz
$ cd keepalived-2.1.5
$ ./configure --prefix=/usr/local/keepalived
$ make
$ sudo make install
```

第三步，配置 Keepalived：


```
$ sudo mkdir /etc/keepalived # 安装后，默认没有创建/etc/keepalived目录
$ sudo cp /usr/local/keepalived/etc/keepalived/keepalived.conf  /etc/keepalived/keepalived.conf
$ sudo cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/keepalived
```
Keepalived 的 systemd uint 配置，默认使用了/usr/local/keepalived/etc/sysconfig/keepalived作为其EnvironmentFile，我们还需要把它修改为/etc/sysconfig/keepalived文件。编辑/lib/systemd/system/keepalived.service文件，设置EnvironmentFile，值如下：

```
EnvironmentFile=-/etc/sysconfig/keepalived
```
第四步，启动 Keepalived，并设置开机启动：

```
$ sudo systemctl start keepalived
$ sudo systemctl enable keepalived
```

这里要注意，Keepalived 启动时不会校验配置文件是否正确，所以我们要小心修改配置，防止出现意想不到的问题。第五步，查看 Keepalived 的启动状态：


```
$ systemctl status keepalived
```
输出中有active (running)字符串，说明成功启动。Keepalived 的日志保存在/var/log/messages中，你有需要的话可以查看。

#### Keepalived 配置文件解析
Keepalived 的默认配置文件为/etc/keepalived/keepalived.conf，下面是一个 Keepalived 配置：

```conf
# 全局定义，定义全局的配置选项
global_defs {
# 指定keepalived在发生切换操作时发送email，发送给哪些email
# 建议在keepalived_notify.sh中发送邮件
  notification_email {
    acassen@firewall.loc
  }
  notification_email_from Alexandre.Cassen@firewall.loc # 发送email时邮件源地址
    smtp_server 192.168.200.1 # 发送email时smtp服务器地址
    smtp_connect_timeout 30 # 连接smtp的超时时间
    router_id VM-4-21-centos # 机器标识，通常可以设置为hostname
    vrrp_skip_check_adv_addr # 如果接收到的报文和上一个报文来自同一个路由器，则不执行检查。默认是跳过检查
    vrrp_garp_interval 0 # 单位秒，在一个网卡上每组gratuitous arp消息之间的延迟时间，默认为0
    vrrp_gna_interval 0 # 单位秒，在一个网卡上每组na消息之间的延迟时间，默认为0
}
# 检测脚本配置
vrrp_script checkhaproxy
{
  script "/etc/keepalived/check_nginx.sh" # 检测脚本路径
    interval 5 # 检测时间间隔（秒）
    weight 0 # 根据该权重改变priority，当值为0时，不改变实例的优先级
}
# VRRP实例配置
vrrp_instance VI_1 {
  state BACKUP  # 设置初始状态为'备份'
    interface eth0 # 设置绑定VIP的网卡，例如eth0
    virtual_router_id 51  # 配置集群VRID，互为主备的VRID需要是相同的值
    nopreempt               # 设置非抢占模式，只能设置在state为backup的节点上
    priority 50 # 设置优先级，值范围0～254，值越大优先级越高，最高的为master
    advert_int 1 # 组播信息发送时间间隔，两个节点必须设置一样，默认为1秒
# 验证信息，两个节点必须一致
    authentication {
      auth_type PASS # 认证方式，可以是PASS或AH两种认证方式
        auth_pass 1111 # 认证密码
    }
  unicast_src_ip 10.0.4.21  # 设置本机内网IP地址
    unicast_peer {
      10.0.4.20             # 对端设备的IP地址
    }
# VIP，当state为master时添加，当state为backup时删除
  virtual_ipaddress {
    10.0.4.99 # 设置高可用虚拟VIP，如果是腾讯云的CVM，需要填写控制台申请到的HAVIP地址。
  }
  notify_master "/etc/keepalived/keepalived_notify.sh MASTER" # 当切换到master状态时执行脚本
    notify_backup "/etc/keepalived/keepalived_notify.sh BACKUP" # 当切换到backup状态时执行脚本
    notify_fault "/etc/keepalived/keepalived_notify.sh FAULT" # 当切换到fault状态时执行脚本
    notify_stop "/etc/keepalived/keepalived_notify.sh STOP" # 当切换到stop状态时执行脚本
    garp_master_delay 1    # 设置当切为主状态后多久更新ARP缓存
    garp_master_refresh 5   # 设置主节点发送ARP报文的时间间隔
    # 跟踪接口，里面任意一块网卡出现问题，都会进入故障(FAULT)状态
    track_interface {
      eth0
    }
  # 要执行的检查脚本
  track_script {
    checkhaproxy
  }
}
```
这里解析下配置文件，大致分为下面 4 个部分。
- global_defs：全局定义，定义全局的配置选项。
- vrrp_script checkhaproxy：检测脚本配置。
- vrrp_instance VI_1：VRRP 实例配置。
- virtual_server：LVS 配置。如果没有配置 LVS+Keepalived，就不用设置这个选项。这门课中，我们使用 Nginx 代替 LVS，所以无需配置virtual_server（配置示例中不再展示）。

只有在网络故障或者自身出问题时，Keepalived 才会进行 VIP 切换。但实际生产环境中，我们往往使用 Keepalived 来监控其他进程，当业务进程出故障时切换 VIP，从而保障业务进程的高可用。

为了让 Keepalived 感知到 Nginx 的运行状况，我们需要指定vrrp_script脚本，vrrp_script脚本可以根据退出码，判断 Nginx 进程是否正常，0正常，非0不正常。当不正常时，Keepalived 会进行 VIP 切换。为了实现业务进程的监控，我们需要设置vrrp_script和track_script：

```
vrrp_script checkhaproxy
{
    script "/etc/keepalived/check_nginx.sh"
    interval 3
    weight -20
}

vrrp_instance test
{
    ...
    track_script
    {
        checkhaproxy
    }
    ...
}
```
这里，我介绍下上面配置中的一些配置项。
- script：指定脚本路径。
- interval：表示 Keepalived 执行脚本的时间间隔（秒）。
- weight：检测权重，可以改变priority的值。例如，-20表示检测失败时，优先级-20，成功时不变。20表示检测成功时，优先级+20，失败时不变。

### 总结
今天我主要讲了跟 IAM 部署相关的两个组件，Nginx + Keepalived 的相关功能。我们可以基于物理机 / 虚拟机来部署 IAM 应用，在部署 IAM 应用时，需要确保整个应用具备高可用和弹性扩缩容能力。你可以通过 Nginx 的反向代理功能和负载均衡功能实现后端服务 iam-apiserver 和 iam-authz-server 的高可用，通过 Keepalived 实现 Nginx 的高可用，通过 Nginx + Keepalived 组合，来实现 IAM 应用的高可用和弹性伸缩能力。

## 41 | 软件部署实战（中）：IAM 系统生产环境部署实战


上一讲，我介绍了 IAM 部署用到的两个核心组件，Nginx 和 Keepalived。那么这一讲，我们就来看下，如何使用 Nginx 和 Keepalived 来部署一个高可用的 IAM 应用。下一讲，我再介绍下 IAM 应用安全和弹性伸缩能力的构建方式。这一讲，我们会通过下面四个步骤来部署 IAM 应用：

1. 在服务器上部署 IAM 应用中的服务。
2. 配置 Nginx，实现反向代理功能。通过反向代理，我们可以通过 Nginx 来访问部署在内网的 IAM 服务。
3. 配置 Nginx，实现负载均衡功能。通过负载均衡，我们可以实现服务的水平扩缩容，使 IAM 应用具备高可用能力。
4. 配置 Keepalived，实现 Nginx 的高可用。通过 Nginx + Keepalived 的组合，可以实现整个应用架构的高可用。

### 部署 IAM 应用
部署一个高可用的 IAM 应用，需要至少两个节点。所以，我们按照先后顺序，分别在10.0.4.20和10.0.4.21服务器上部署 IAM 应用。

#### 在10.0.4.20服务器上部署 IAM 应用
首先，我来介绍下如何在10.0.4.20服务器上部署 IAM 应用。我们要在这个服务器上部署如下组件：

- iam-apiserver
- iam-authz-server
- iam-pump
- MariaDB
- Redis
- MongoDB


这些组件的部署方式，03 讲 有介绍，这里就不再说明。此外，我们还需要设置 MariaDB，给来自于10.0.4.21服务器的数据库连接授权，授权命令如下：

```
$ mysql -hlocalhost -P3306 -uroot -proot # 先以root用户登陆数据库
MariaDB [(none)]> grant all on iam.* TO iam@10.0.4.21 identified by 'iam1234';
Query OK, 0 rows affected (0.000 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.000 sec)
```

#### 在10.0.4.21服务器上部署 IAM 应用
然后，在10.0.4.21服务器上安装好 iam-apiserver、iam-authz-server 和 iam-pump。这些组件通过10.0.4.20 IP 地址，连接10.0.4.20服务器上的 MariaDB、Redis 和 MongoDB。


### 配置 Nginx 作为反向代理
假定要访问的 API Server 和 IAM Authorization Server 的域名分别为iam.api.marmotedu.com和iam.authz.marmotedu.com，我们需要分别为 iam-apiserver 和 iam-authz-server 配置 Nginx 反向代理。整个配置过程可以分为 5 步（在10.0.4.20服务器上操作）。
#### 第一步，配置 iam-apiserver。
新建 Nginx 配置文件/etc/nginx/conf.d/iam-apiserver.conf，内容如下：



```conf
server {
    listen       80;
    server_name  iam.api.marmotedu.com;
    root         /usr/share/nginx/html;
    location / {
      proxy_set_header X-Forwarded-Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_pass  http://127.0.0.1:8080/;
      client_max_body_size 5m;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}
```

有几点你在配置时需要注意，这里说明下。
- server_name需要为iam.api.marmotedu.com，我们通过iam.api.marmotedu.com访问 iam-apiserver。
- iam-apiserver 默认启动的端口为8080。
- 由于 Nginx 默认允许客户端请求的最大单文件字节数为1MB，实际生产环境中可能太小，所以这里将此限制改为 5MB（client_max_body_size 5m）。如果需要上传图片之类的，可能需要设置成更大的值，比如50m。
- server_name 用来说明访问 Nginx 服务器的域名，例如curl -H 'Host: iam.api.marmotedu.com' http://x.x.x.x:80/healthz，x.x.x.x为 Nginx 服务器的 IP 地址。
- proxy_pass 表示反向代理的路径。因为这里是本机的 iam-apiserver 服务，所以 IP 为127.0.0.1。端口要和 API 服务端口一致，为8080。

最后还要提醒下，因为 Nginx 配置选项比较多，跟实际需求和环境有关，所以这里的配置是基础的、未经优化的配置，在实际生产环境中需要你再做调节。


#### 第二步，配置 iam-authz-server。
新建 Nginx 配置文件/etc/nginx/conf.d/iam-authz-server.conf，内容如下：


```conf
server {
    listen       80;
    server_name  iam.authz.marmotedu.com;
    root         /usr/share/nginx/html;
    location / {
      proxy_set_header X-Forwarded-Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_pass  http://127.0.0.1:9090/;
      client_max_body_size 5m;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}
```
下面是一些配置说明。
- server_name 需要为iam.authz.marmotedu.com，我们通过iam.authz.marmotedu.com访问 iam-authz-server。
- iam-authz-server 默认启动的端口为9090。
- 其他配置跟/etc/nginx/conf.d/iam-apiserver.conf一致。

#### 第三步，配置完 Nginx 后，重启 Nginx：

```
$ sudo systemctl restart nginx
```

#### 第四步，在 /etc/hosts 中追加下面两行：


```
127.0.0.1 iam.api.marmotedu.com
127.0.0.1 iam.authz.marmotedu.com
```

#### 第五步，发送 HTTP 请求：

```
$ curl http://iam.api.marmotedu.com/healthz
{"status":"ok"}
$ curl http://iam.authz.marmotedu.com/healthz
{"status":"ok"}
```
我们分别请求 iam-apiserver 和 iam-authz-server 的健康检查接口，输出了{"status":"ok"}，说明我们可以成功通过代理访问后端的 API 服务。

在用 curl 请求http://iam.api.marmotedu.com/healthz后，后端的请求流程实际上是这样的：
1. 因为在/etc/hosts中配置了127.0.0.1 iam.api.marmotedu.com，所以请求http://iam.api.marmotedu.com/healthz实际上是请求本机的 Nginx 端口（127.0.0.1:80）。
2. Nginx 在收到请求后，会解析请求，得到请求域名为iam.api.marmotedu.com。根据请求域名去匹配 Nginx 的 server 配置，匹配到server_name  iam.api.marmotedu.com;配置。
3. 匹配到 server 后，把请求转发到该 server 的proxy_pass路径。
4. 等待 API 服务器返回结果，并返回客户端。

### 配置 Nginx 作为负载均衡
这门课采用 Nginx 轮询的负载均衡策略转发请求。负载均衡需要至少两台服务器，所以会分别在10.0.4.20和10.0.4.21服务器上执行相同的操作。下面我分别来介绍下如何配置这两台服务器，并验证配置是否成功。

#### 10.0.4.20服务器配置
登陆10.0.4.20服务器，在/etc/nginx/nginx.conf中添加 upstream 配置，配置过程可以分为 3 步。

第一步，在/etc/nginx/nginx.conf中添加 upstream：

```
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    upstream iam.api.marmotedu.com {
        server 127.0.0.1:8080
        server 10.0.4.21:8080
    }
    upstream iam.authz.marmotedu.com {
        server 127.0.0.1:9090
        server 10.0.4.21:9090
    }
}
```

配置说明：
- upstream 是配置在/etc/nginx/nginx.conf文件中的http{ … }部分的。
- 因为我们要分别为 iam-apiserver 和 iam-authz-server 配置负载均衡，所以我们创建了两个 upstream，分别是iam.api.marmotedu.com和iam.authz.marmotedu.com。为了便于识别，upstream 名称和域名最好保持一致。
- 在 upstream 中，我们需要分别添加所有的 iam-apiserver 和 iam-authz-server 的后端（ip:port），本机的后端为了访问更快，可以使用127.0.0.1:<port>，其他机器的后端，需要使用<内网>:port，例如10.0.4.21:8080、10.0.4.21:9090。

第二步，修改 proxy_pass。修改/etc/nginx/conf.d/iam-apiserver.conf文件，将proxy_pass修改为：

```
proxy_pass http://iam.api.marmotedu.com/;
```
修改/etc/nginx/conf.d/iam-authz-server.conf文件，将proxy_pass修改为：

```
proxy_pass http://iam.authz.marmotedu.com/;
```

当 Nginx 转发到http://iam.api.marmotedu.com/域名时，会从iam.api.marmotedu.com upstream 配置的后端列表中，根据负载均衡策略选取一个后端，并将请求转发过去。转发http://iam.authz.marmotedu.com/域名的逻辑也一样。


第三步，配置完 Nginx 后，重启 Nginx：

```
$ sudo systemctl restart nginx
```

最终配置好的配置文件，你可以参考下面这些（保存在configs/ha/10.0.4.20目录下）：
- nginx.conf：configs/ha/10.0.4.20/nginx.conf。
- iam-apiserver.conf：configs/ha/10.0.4.20/iam-apiserver.conf。
- iam-authz-server.conf：configs/ha/10.0.4.20/iam-authz-server.conf。

#### 10.0.4.21服务器配置
登陆10.0.4.21服务器，在/etc/nginx/nginx.conf中添加 upstream 配置。配置过程可以分为下面 4 步。

第一步，在/etc/nginx/nginx.conf中添加 upstream：

```
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    upstream iam.api.marmotedu.com {
        server 127.0.0.1:8080
        server 10.0.4.20:8080
    }
    upstream iam.authz.marmotedu.com {
        server 127.0.0.1:9090
        server 10.0.4.20:9090
    }
}
```
upstream 中，需要配置10.0.4.20服务器上的 iam-apiserver 和 iam-authz-server 的后端，例如10.0.4.20:8080、10.0.4.20:9090。

第二步，创建/etc/nginx/conf.d/iam-apiserver.conf文件（iam-apiserver 的反向代理 + 负载均衡配置），内容如下：

```
server {
    listen       80;
    server_name  iam.api.marmotedu.com;
    root         /usr/share/nginx/html;
    location / {
      proxy_set_header X-Forwarded-Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_pass  http://iam.api.marmotedu.com/;
      client_max_body_size 5m;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}
```
第三步，创建/etc/nginx/conf.d/iam-authz-server文件（iam-authz-server 的反向代理 + 负载均衡配置），内容如下：

```
server {
    listen       80;
    server_name  iam.authz.marmotedu.com;
    root         /usr/share/nginx/html;
    location / {
      proxy_set_header X-Forwarded-Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_pass  http://iam.authz.marmotedu.com/;
      client_max_body_size 5m;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}

```
第四步，配置完 Nginx 后，重启 Nginx：

```
$ sudo systemctl restart nginx
```

最终配置好的配置文件，你可以参考下面这些（保存在configs/ha/10.0.4.21目录下）：
- nginx.conf：configs/ha/10.0.4.21/nginx.conf。
- iam-apiserver.conf：configs/ha/10.0.4.21/iam-apiserver.conf。
- iam-authz-server.conf：configs/ha/10.0.4.21/iam-authz-server.conf。

#### 测试负载均衡
上面，我们配置了 Nginx 负载均衡器，这里我们还需要测试下是否配置成功。

第一步，执行测试脚本（test/nginx/loadbalance.sh）：

```
#!/usr/bin/env bash

for domain in iam.api.marmotedu.com iam.authz.marmotedu.com
do
  for n in $(seq 1 1 10)
  do
    echo $domain
    nohup curl http://${domain}/healthz &>/dev/null &
  done
done
```
第二步，分别查看 iam-apiserver 和 iam-authz-server 的日志。这里我展示下 iam-apiserver 的日志（iam-authz-server 的日志你可自行查看）。
10.0.4.20服务器的 iam-apiserver 日志如下图所示：

![img](https://static001.geekbang.org/resource/image/58/26/58d072c92552fa3068e3ef3acd0ed726.png?wh=1920x498)

10.0.4.21服务器的 iam-apiserver 日志如下图所示：

![img](https://static001.geekbang.org/resource/image/19/85/199066c65ff60007f80f3c2dyy11c785.png?wh=1920x482)

通过上面两张图，你可以看到10.0.4.20和10.0.4.21各收到5个/healthz请求，说明负载均衡配置成功。


### 配置 Keepalived
在 40 讲，我们分别在10.0.4.20和10.0.4.21服务器上安装了 Keepalived。这里，我来介绍下如何配置 Keepalived，实现 Nginx 的高可用。为了避免故障恢复时，VIP 切换造成的服务延时，这一讲采用 Keepalived 的非抢占模式。

配置 Keepalived 的流程比较复杂，分为创建腾讯云 HAVIP、主服务器配置、备服务器配置、测试 Keepalived、VIP 绑定公网 IP 和测试公网访问六大步，每一步中都有很多小步骤，下面我们来一步步地看下。

#### 第一步：创建腾讯云 HAVIP
公有云厂商的普通内网 IP，出于安全考虑（如避免 ARP 欺骗等），不支持主机通过 ARP 宣告 IP 。如果用户直接在keepalived.conf文件中指定一个普通内网 IP 为 virtual IP，当 Keepalived 将 virtual IP 从 MASTER 机器切换到 BACKUP 机器时，将无法更新 IP 和 MAC 地址的映射，而需要调 API 来进行 IP 切换。所以，这里的 VIP 需要申请腾讯云的 HAVIP。

申请的流程可以分为下面 4 步：
- 登录私有网络控制台。
- 在左侧导航栏中，选择【IP 与网卡】>【高可用虚拟 IP】。
- 在 HAVIP 管理页面，选择所在地域，单击【申请】。
- 在弹出的【申请高可用虚拟 IP】对话框中输入名称，选择 HAVIP 所在的私有网络和子网等信息，单击【确定】即可。

这里选择的私有网络和子网，需要和10.0.4.20、10.0.4.21相同。HAVIP 的 IP 地址可以自动分配，也可以手动填写，这里我们手动填写为 10.0.4.99。申请页面如下图所示：

![img](https://static001.geekbang.org/resource/image/a4/11/a49d6e7e080d658392dbb144a1560811.png?wh=827x1016)

#### 第二步：主服务器配置
进行主服务器配置，可以分为两步。

首先，修改 Keepalived 配置文件。登陆服务器10.0.4.20，编辑/etc/keepalived/keepalived.conf，修改配置，修改后配置内容如下（参考：configs/ha/10.0.4.20/keepalived.conf）：

```conf
# 全局定义，定义全局的配置选项
global_defs {
# 指定keepalived在发生切换操作时发送email，发送给哪些email
# 建议在keepalived_notify.sh中发送邮件
  notification_email {
    acassen@firewall.loc
  }
  notification_email_from Alexandre.Cassen@firewall.loc # 发送email时邮件源地址
    smtp_server 192.168.200.1 # 发送email时smtp服务器地址
    smtp_connect_timeout 30 # 连接smtp的超时时间
    router_id VM-4-20-centos # 机器标识，通常可以设置为hostname
    vrrp_skip_check_adv_addr # 如果接收到的报文和上一个报文来自同一个路由器，则不执行检查。默认是跳过检查
    vrrp_garp_interval 0 # 单位秒，在一个网卡上每组gratuitous arp消息之间的延迟时间，默认为0
    vrrp_gna_interval 0 # 单位秒，在一个网卡上每组na消息之间的延迟时间，默认为0
}
# 检测脚本配置
vrrp_script checkhaproxy
{
  script "/etc/keepalived/check_nginx.sh" # 检测脚本路径
    interval 5 # 检测时间间隔（秒）
    weight 0 # 根据该权重改变priority，当值为0时，不改变实例的优先级
}
# VRRP实例配置
vrrp_instance VI_1 {
  state BACKUP  # 设置初始状态为'备份'
    interface eth0 # 设置绑定VIP的网卡，例如eth0
    virtual_router_id 51  # 配置集群VRID，互为主备的VRID需要是相同的值
    nopreempt               # 设置非抢占模式，只能设置在state为backup的节点上
    priority 100 # 设置优先级，值范围0～254，值越大优先级越高，最高的为master
    advert_int 1 # 组播信息发送时间间隔，两个节点必须设置一样，默认为1秒
# 验证信息，两个节点必须一致
    authentication {
      auth_type PASS # 认证方式，可以是PASS或AH两种认证方式
        auth_pass 1111 # 认证密码
    }
  unicast_src_ip 10.0.4.20  # 设置本机内网IP地址
    unicast_peer {
      10.0.4.21             # 对端设备的IP地址
    }
# VIP，当state为master时添加，当state为backup时删除
  virtual_ipaddress {
    10.0.4.99 # 设置高可用虚拟VIP，如果是腾讯云的CVM，需要填写控制台申请到的HAVIP地址。
  }
  notify_master "/etc/keepalived/keepalived_notify.sh MASTER" # 当切换到master状态时执行脚本
    notify_backup "/etc/keepalived/keepalived_notify.sh BACKUP" # 当切换到backup状态时执行脚本
    notify_fault "/etc/keepalived/keepalived_notify.sh FAULT" # 当切换到fault状态时执行脚本
    notify_stop "/etc/keepalived/keepalived_notify.sh STOP" # 当切换到stop状态时执行脚本
    garp_master_delay 1    # 设置当切为主状态后多久更新ARP缓存
    garp_master_refresh 5   # 设置主节点发送ARP报文的时间间隔
    # 跟踪接口，里面任意一块网卡出现问题，都会进入故障(FAULT)状态
    track_interface {
      eth0
    }
  # 要执行的检查脚本
  track_script {
    checkhaproxy
  }
}
```
这里有几个注意事项：
- 确保已经配置了 garp 相关参数。因为 Keepalived 依赖 ARP 报文更新 IP 信息，如果缺少这些参数，会导致某些场景下主设备不发送 ARP，进而导致通信异常。garp 相关参数配置如下：
```
garp_master_delay 1
garp_master_refresh 5
```
- 确定没有采用 strict 模式，即需要删除 vrrp_strict 配置。
- 配置中的/etc/keepalived/check_nginx.sh和/etc/keepalived/keepalived_notify.sh脚本文件，可分别拷贝自scripts/check_nginx.sh和scripts/keepalived_notify.sh。

然后，重启 Keepalived：


```
$ sudo systemctl restart keepalived
```
#### 第三步：备服务器配置
进行备服务器配置也分为两步。

**首先，修改 Keepalived 配置文件**。登陆服务器10.0.4.21，编辑/etc/keepalived/keepalived.conf，修改配置，修改后配置内容如下（参考：configs/ha/10.0.4.21/keepalived.conf）：

```conf
# 全局定义，定义全局的配置选项
global_defs {
# 指定keepalived在发生切换操作时发送email，发送给哪些email
# 建议在keepalived_notify.sh中发送邮件
  notification_email {
    acassen@firewall.loc
  }
  notification_email_from Alexandre.Cassen@firewall.loc # 发送email时邮件源地址
    smtp_server 192.168.200.1 # 发送email时smtp服务器地址
    smtp_connect_timeout 30 # 连接smtp的超时时间
    router_id VM-4-21-centos # 机器标识，通常可以设置为hostname
    vrrp_skip_check_adv_addr # 如果接收到的报文和上一个报文来自同一个路由器，则不执行检查。默认是跳过检查
    vrrp_garp_interval 0 # 单位秒，在一个网卡上每组gratuitous arp消息之间的延迟时间，默认为0
    vrrp_gna_interval 0 # 单位秒，在一个网卡上每组na消息之间的延迟时间，默认为0
}
# 检测脚本配置
vrrp_script checkhaproxy
{
  script "/etc/keepalived/check_nginx.sh" # 检测脚本路径
    interval 5 # 检测时间间隔（秒）
    weight 0 # 根据该权重改变priority，当值为0时，不改变实例的优先级
}
# VRRP实例配置
vrrp_instance VI_1 {
  state BACKUP  # 设置初始状态为'备份'
    interface eth0 # 设置绑定VIP的网卡，例如eth0
    virtual_router_id 51  # 配置集群VRID，互为主备的VRID需要是相同的值
    nopreempt               # 设置非抢占模式，只能设置在state为backup的节点上
    priority 50 # 设置优先级，值范围0～254，值越大优先级越高，最高的为master
    advert_int 1 # 组播信息发送时间间隔，两个节点必须设置一样，默认为1秒
# 验证信息，两个节点必须一致
    authentication {
      auth_type PASS # 认证方式，可以是PASS或AH两种认证方式
        auth_pass 1111 # 认证密码
    }
  unicast_src_ip 10.0.4.21  # 设置本机内网IP地址
    unicast_peer {
      10.0.4.20             # 对端设备的IP地址
    }
# VIP，当state为master时添加，当state为backup时删除
  virtual_ipaddress {
    10.0.4.99 # 设置高可用虚拟VIP，如果是腾讯云的CVM，需要填写控制台申请到的HAVIP地址。
  }
  notify_master "/etc/keepalived/keepalived_notify.sh MASTER" # 当切换到master状态时执行脚本
    notify_backup "/etc/keepalived/keepalived_notify.sh BACKUP" # 当切换到backup状态时执行脚本
    notify_fault "/etc/keepalived/keepalived_notify.sh FAULT" # 当切换到fault状态时执行脚本
    notify_stop "/etc/keepalived/keepalived_notify.sh STOP" # 当切换到stop状态时执行脚本
    garp_master_delay 1    # 设置当切为主状态后多久更新ARP缓存
    garp_master_refresh 5   # 设置主节点发送ARP报文的时间间隔
    # 跟踪接口，里面任意一块网卡出现问题，都会进入故障(FAULT)状态
    track_interface {
      eth0
    }
  # 要执行的检查脚本
  track_script {
    checkhaproxy
  }
}
```
然后，重启 Keepalived：


```
$ sudo systemctl restart keepalived
```

#### 第四步：测试 Keepalived
上面的配置中，10.0.4.20的优先级更高，所以正常情况下10.0.4.20将被选择为主节点，如下图所示：

![img](https://static001.geekbang.org/resource/image/54/79/54968f40707b779e2ab70d3ab5a53479.png?wh=1920x500)

接下来，我们分别模拟一些故障场景，来看下配置是否生效。

场景 1：Keepalived 故障
在10.0.4.20服务器上执行sudo systemctl stop keepalived模拟 Keepalived 故障，查看 VIP，如下图所示：

![img](https://static001.geekbang.org/resource/image/2a/ee/2a57e958bd9fce3b9c842c1cf09c48ee.png?wh=1920x544)
可以看到，VIP 从10.0.4.20服务器上，漂移到了10.0.4.21服务器上。查看/var/log/keepalived.log，可以看到10.0.4.20服务器新增如下一行日志：

```
[2020-10-14 14:01:51] notify_stop
```
10.0.4.21服务器新增如下日志：

```
[2020-10-14 14:01:52] notify_master
```

场景 2：Nginx 故障
在10.0.4.20和10.0.4.21服务器上分别执行sudo systemctl restart keepalived，让 VIP 漂移到10.0.4.20服务器上。

在10.0.4.20服务器上，执行 sudo systemctl stop nginx 模拟 Nginx 故障，查看 VIP，如下图所示：

![img](https://static001.geekbang.org/resource/image/2a/ee/2a57e958bd9fce3b9c842c1cf09c48ee.png?wh=1920x544)

可以看到，VIP 从10.0.4.20服务器上，漂移到了10.0.4.21服务器上。查看/var/log/keepalived.log，可以看到10.0.4.20服务器新增如下一行日志：

```
[2020-10-14 14:02:34] notify_fault
```

10.0.4.21 服务器新增如下日志：

```
[2020-10-14 14:02:35] notify_master

```
场景 3：Nginx 恢复
基于场景 2，在10.0.4.20服务器上执行sudo systemctl start nginx恢复 Nginx，查看 VIP，如下图所示：

![img](https://static001.geekbang.org/resource/image/2a/ee/2a57e958bd9fce3b9c842c1cf09c48ee.png?wh=1920x544)

可以看到，VIP 仍然在10.0.4.21服务器上，没有被10.0.4.20抢占。查看/var/log/keepalived.log，可以看到10.0.4.20服务器新增如下一行日志：

```
[2020-10-14 14:03:44] notify_backup
```

10.0.4.21服务器没有新增日志。

#### 第五步：VIP 绑定公网 IP
到这里，我们已经成功配置了 Keepalived + Nginx 的高可用方案。但是，我们的 VIP 是内网，还不能通过外网访问。这时候，我们需要将 VIP 绑定一个外网 IP，供外网访问。在腾讯云上，可通过绑定弹性公网 IP 来实现外网访问，需要先申请公网 IP，然后将 VIP 绑定弹性公网 IP。下面我来讲讲具体步骤。

申请公网 IP：

登录私有网络控制台。在左侧导航栏中，选择【IP 与网卡】>【弹性公网 IP】。在弹性公网 IP 管理页面，选择所在地域，单击【申请】。

将 VIP 绑定弹性公网 IP：

登录私有网络控制台。在左侧导航栏中，选择【IP 与网卡】>【高可用虚拟】。单击需要绑定的 HAVIP 所在行的【绑定】。在弹出界面中，选择需要绑定的公网 IP 即可，如下图所示：

![img](https://static001.geekbang.org/resource/image/83/62/83bc9f4595325e9d339e7c3269aa3462.png?wh=1388x666)
绑定的弹性公网 IP 是106.52.252.139。

这里提示下，腾讯云平台中，如果 HAVIP 没有绑定实例，绑定 HAVIP 的 EIP 会处于闲置状态，按¥0.2/小时 收取闲置费用。所以，你需要正确配置高可用应用，确保绑定成功。

#### 第六步：测试公网访问
最后，你可以通过执行如下命令来测试：

```
$ curl -H"Host: iam.api.marmotedu.com" http://106.52.252.139/healthz -H"iam.api.marmotedu.com"
{"status":"ok"}
```
可以看到，我们可以成功通过公网访问后端的高可用服务。到这里，我们成功部署了一个可用性很高的 IAM 应用。

### 总结
今天，我主要讲了如何使用 Nginx 和 Keepalived，来部署一个高可用的 IAM 应用。


为了部署一个高可用的 IAM 应用，我们至少需要两台服务器，并且部署相同的服务 iam-apiserver、iam-authz-server、iam-pump。而且，选择其中一台服务器部署数据库服务：MariaDB、Redis、MongoDB。为了安全和性能，iam-apiserver、iam-authz-server、iam-pump 服务都是通过内网来访问数据库服务的。这一讲，我还介绍了如何配置 Nginx 来实现负载均衡，如何配置 Keepalived 来实现 Nginx 的高可用。

## 42 | 软件部署实战（下）：IAM系统安全加固、水平扩缩容实战
这一讲和前面两讲，都是介绍如何基于物理机 / 虚拟机来部署 IAM 的。在前面两讲，我们了解了如何部署一个高可用的 IAM 应用，今天就再来看看 IAM 应用安全和弹性伸缩能力的构建方式。在这一讲中，我会带你加固 IAM 应用的安全性，并介绍如何具体执行扩缩容步骤。接下来，我们先来看下如何加固 IAM 应用的安全性。


### IAM 应用安全性加固
iam-apiserver、iam-authz-server、MariaDB、Redis 和 MongoDB 这些服务，都提供了绑定监听网卡的功能。我们可以将这些服务绑定到内网网卡上，从而只接收来自于内网的请求，通过这种方式，可以加固我们的系统。

我们也可以通过 iptables 来实现类似的功能，通过将安全问题统一收敛到 iptables 规则，可以使我们更容易地维护安全类设置。这门课通过 iptables 来加固系统，使系统变得更加安全。下面，我先来对 iptables 工具进行一些简单的介绍。


### iptables 简介
iptables 是 Linux 下最优秀的防火墙工具，也是 Linux 内核中 netfilter 网络子系统用户态的工具。

netfilter 提供了一系列的接口，在一个到达本机的数据包，或者经本机转发的数据包流程中添加了一些可供用户操作的点，这些点被称为 HOOK 点。通过在 HOOK 点注册数据包处理函数，可以实现数据包转发、数据包过滤、地址转换等功能。

用户通过 iptables 工具定义各种规则，这些规则通过 iptables 传给内核中的 netfilter。最终，netfilter 会根据规则对网络包进行过滤。Linux 系统一般会默认安装 iptables 软件。防火墙根据 iptables 里的规则，对收到的网络数据包进行处理。

iptables 里的数据组织结构分为表、链、规则。
- 表（tables）: 表可以提供特定的功能，每个表里包含多个链。iptables 里面一共有 5 个表，分别是 filter、nat、mangle、raw、security。这些表，分别用来实现包过滤、网络地址转换、包重构、数据追踪处理和 SELinux 标记设置。
- 链（chains）: 链是数据包传播的路径，每一条链中可以有一个或多个规则。当一个数据包到达一个链时，iptables 会从链中第一条规则开始，检查该数据包是否满足规则所定义的条件。如果满足，就会根据该条规则所定义的方法，处理该数据包。否则，就继续检查下一条规则。如果该数据包不符合链中任一条规则，iptables 就会根据该链预先定义的默认策略来处理数据包。
- 规则（rules）：规则存储在内核空间的信息包过滤表中，用来描述“如果数据包满足所描述的条件，就按照要求处理这个数据包，如果不满足，就判断下一条规则”。

其中，iptables 中表和链的种类及其功能，如下表所示：

![img](https://static001.geekbang.org/resource/image/11/0f/112df7eb9a1258dd61e3bd0e0b6b210f.png?wh=2248x1941)

上面的表格中，五张表的处理是有顺序的。当数据包到达某一条链时，会按照 RAW、MANGLE、NAT、FILTER、SECURITY 的顺序进行处理。到这里，我介绍了关于 iptables 的一些基础知识，但这还远远不够。要想使用 iptables 来加固你的系统，你还需要掌握 iptables 工具的使用方法。接下来，我先来介绍下 iptables 是如何处理网络数据包的。


### 网络数据包处理流程
网络数据包的处理流程如下图所示：

![img](https://static001.geekbang.org/resource/image/9e/bb/9ece7f3001c022790f1fd1a0yy1246bb.jpg?wh=2248x1414)

具体可以分为两个步骤。

第一步，当数据包进入网卡后，它首先进入 PREROUTING 链，根据目的 IP 判断是否转发出去。第二步分为两种情况：如果数据包目的地是本机，它会到达 INPUT 链。到达后，任何进程都会收到它。本机上的程序可以发送数据包，这些数据包会经过 OUTPUT 链，然后经 POSTROUTING 链输出；如果数据包是要转发出去，并且内核允许转发，那么数据包会经过 FORWARD 链，最后从 POSTROUTING 链输出。

### iptables 工具使用方式介绍
iptables 的功能强大，所以使用方法也非常多样。这里，我来介绍下 iptables 工具的使用方式，并给出一些使用示例。


**命令格式**
iptables 的语法格式为：


```
iptables [-t 表名] 命令选项 [链名] [条件匹配] [-j 目标动作或跳转]
```

下面是一个 iptables 的使用示例：

```
iptables -t nat -I PREROUTING -p tcp --dport 8080 -j DNAT --to 10.0.4.88
```

这里对上面涉及到的一些参数进行说明。
- 表名 / 链名：指定 iptables 命令所操作的表 / 链。
- 命令选项：指定处理 iptables 规则的方式，例如插入、增加、删除、查看等。
- 条件匹配：指定对符合条件的数据包进行处理。
- 目标动作或跳转：防火墙处理数据包的方式。

iptables 的命令选项又分为管理控制选项和通用选项。管理控制选项如下：

![img](https://static001.geekbang.org/resource/image/6d/b2/6d37f77b4cee31eea694cc588ayy3cb2.png?wh=2248x2323)

通用选项如下：

![img](https://static001.geekbang.org/resource/image/0b/ae/0b38f3ba2d722ccf3274a0ae0a5f79ae.png?wh=2248x1498)

处理数据包的方式（目标动作或跳转）有多种，具体如下表所示：

![img](https://static001.geekbang.org/resource/image/f7/dc/f796fc7905c88cf0461f4464cec8cddc.png?wh=2248x1625)

上面，我介绍了 iptables 工具的使用方式。因为内容有点多，你可能仍然不知道如何使用 iptables 工具。没关系，接下来你可以结合我举的一些例子来看下。


**命令示例**
下面的命令示例，默认使用了 FILTER 表，也即规则存放在 FILTER 表中，相当于每一条 iptables 命令都添加了-t filter 参数。


拒绝进入防火墙的所有 ICMP 协议数据包：

```
$ iptables -I INPUT -p icmp -j REJECT
```
允许防火墙转发除 ICMP 协议以外的所有数据包：

```
$ iptables -A FORWARD -p ! icmp -j ACCEPT
```
拒绝转发来自 192.168.1.10 主机的数据，允许转发来自 192.168.0.0/24 网段的数据：

```
$ iptables -A FORWARD -s 192.168.1.11 -j REJECT
$ iptables -A FORWARD -s 192.168.0.0/24 -j ACCEPT
```
丢弃从外网接口（eth1）进入防火墙本机的源地址为私网地址的数据包：

```
$ iptables -A INPUT -i eth1 -s 192.168.0.0/16 -j DROP
$ iptables -A INPUT -i eth1 -s 172.16.0.0/12 -j DROP
$ iptables -A INPUT -i eth1 -s 10.0.0.0/8 -j DROP
```
只允许管理员从 202.13.0.0/16 网段使用 SSH 远程登录防火墙主机：


```
$ iptables -A INPUT -p tcp --dport 22 -s 202.13.0.0/16 -j ACCEPT
$ iptables -A INPUT -p tcp --dport 22 -j DROP
```
允许本机开放从 TCP 端口 20-1024 提供的应用服务：

```
$ iptables -A INPUT -p tcp --dport 20:1024 -j ACCEPT
$ iptables -A OUTPUT -p tcp --sport 20:1024 -j ACCEPT
```
允许转发来自 192.168.0.0/24 局域网段的 DNS 解析请求数据包：

```
$ iptables -A FORWARD -s 192.168.0.0/24 -p udp --dport 53 -j ACCEPT
$ iptables -A FORWARD -d 192.168.0.0/24 -p udp --sport 53 -j ACCEPT
```

禁止其他主机 ping 防火墙主机，但是允许从防火墙上 ping 其他主机：


```
$ iptables -I INPUT -p icmp --icmp-type Echo-Request -j DROP
$ iptables -I INPUT -p icmp --icmp-type Echo-Reply -j ACCEPT
$ iptables -I INPUT -p icmp --icmp-type destination-Unreachable -j ACCEPT
```

禁止转发来自 MAC 地址为 00：0C：29：27：55：3F 的数据包和主机的数据包：

```
$ iptables -A FORWARD -m mac --mac-source 00:0c:29:27:55:3F -j DROP
```

对外开放 TCP 端口 20、21、25、110，以及被动模式 FTP 端口 1250-1280：

```
$ iptables -A INPUT -p tcp -m multiport --dport 20,21,25,110,1250:1280 -j ACCEPT
```
禁止转发源 IP 地址为 192.168.1.20-192.168.1.99 的 TCP 数据包：

```
$ iptables -A FORWARD -p tcp -m iprange --src-range 192.168.1.20-192.168.1.99 -j DROP
```

禁止转发与正常 TCP 连接无关的非 syn 请求数据包：

```
$ iptables -A FORWARD -m state --state NEW -p tcp ! --syn -j DROP
```
拒绝访问防火墙的新数据包，但允许响应连接或与已有连接相关的数据包：

```
$ iptables -A INPUT -p tcp -m state --state NEW -j DROP
$ iptables -A INPUT -p tcp -m state --state ESTABLISHED,RELATED -j ACCEPT
```
只开放本机的 web 服务（80）、FTP(20、21、20450-20480)，放行外部主机发往服务器其他端口的应答数据包，将其他入站数据包都进行丢弃处理：

```
$ iptables -I INPUT -p tcp -m multiport --dport 20,21,80 -j ACCEPT
$ iptables -I INPUT -p tcp --dport 20450:20480 -j ACCEPT
$ iptables -I INPUT -p tcp -m state --state ESTABLISHED -j ACCEPT
$ iptables -P INPUT DROP
```
到这里，我们已经了解了 iptables 的功能，下面来看看如何使用 iptables 来加固 IAM 应用。我把它分成内网不安全和内网安全两种情况。

### IAM 安全加固（内网不安全）
在设置 iptables 规则之前，我们需要先梳理系统的访问关系，然后根据这些访问关系设置 iptables 规则。访问关系如下图所示：

![img](https://static001.geekbang.org/resource/image/9a/8d/9a9b8d4283410dc842505f258128d78d.jpg?wh=2248x1386)

你可以看到，IAM 系统服务互访关系分为下面这 4 种：
- 允许公网客户端访问 Nginx 的 80 和 443 端口。
- Keepalived 服务之间能够互发 VRRP 协议包。
- Nginx 访问各节点上 iam-apiserver、iam-authz-server 和 iam-pump 组件开启的 HTTP/HTTPS/GRPC 服务。
- iam 服务可以从各节点访问 Redis、MariaDB、MongoDB 数据库。

这里，我们假定 IAM 系统部署在一个非常大的内网中，该内网部署了很多其他团队的服务，有很多其他团队的研发、测试等人员在内网中执行各种操作。也就是说，我们处在一个不安全的内网中。这时候，如果要加固我们的系统，最安全的方式是屏蔽掉未知的来源 IP。
内网不安全的情况下，加固系统可以分为 3 大步骤，每个步骤中又有一些小步骤。另外，需要新增节点或者删除节点时，也需要进行一些变更操作。下面我们来具体看下。
#### 第一步，设置防火墙规则。
基于上面说到的几种互访关系，我们可以在各个节点上设置 iptables 规则来加固系统。我将这些规则设置编写成了 go 工具，用来自动生成设置这些规则的 shell 脚本。

具体设置的过程可以分为 5 步。


进入 iam 项目源码根目录。

配置 accesss.yaml（工具根据此配置，自动生成 iptables 设置脚本），内容如下（位于configs/access.yaml文件）：

```yaml
# 允许登录SSH节点的来源IP，可以是固定IP(例如10.0.4.2)，也可以是个网段，0.0.0.0/0代表不限制来源IP
ssh-source: 10.0.4.0/24

# IAM应用节点列表（来源IP）
hosts:
  - 10.0.4.20
  - 10.0.4.21

# 来源IP可以访问的应用端口列表（iam-apiserver, iam-authz-server, iam-pump对外暴露的的端口）
ports:
  - 8080
  - 8443
  - 9090
  - 9443
  - 7070

# 来源IP可以访问的数据库端口列表（Redis, MariaDB, MongoDB）
dbports:
  - 3306
  - 6379
  - 27017
```
上面的配置中，我们指定了允许登陆机器的子网、Nginx 需要访问的端口列表和各节点需要访问的数据库端口列表。

生成 iptables 初始化脚本：

```
$ go run tools/geniptables/main.go -c access.yaml -t app -a -o firewall.sh
$ ls firewall.sh
firewall.sh

```
你可以打开 firewall.sh 文件，查看该脚本设置的规则。

将 firewall.sh 脚本拷贝到 10.0.4.20 和 10.0.4.21 节点执行：

```
$ scp firewall.sh root@10.0.4.20:/tmp/
$ scp firewall.sh root@10.0.4.21:/tmp/
```
登陆 10.0.4.20 和 10.0.4.21 机器，执行/tmp/firewall.sh。

在 10.0.4.20（数据库节点）节点上，设置 iptables 规则，以允许各节点访问：因为数据库节点也位于 10.0.4.20 节点，所以只需要添加新的 rule，并将iptables -A INPUT -j DROP规则放到最后执行即可。


```
$ go run tools/geniptables/main.go -c access.yaml -t db -o addrules.sh
```
然后，将 addrules.sh 脚本拷贝到 10.0.4.20 节点执行。注意，因为 iptables 是按顺序进行规则过滤的，所以需要将iptables -A INPUT -j DROP规则放在新设置规则的后面，否则执行不到新设置的规则。你可以在设置完 iptables 规则之后，执行下面的命令来将 DROP 放到最后：

```
iptables -A INPUT -j LOG --log-level 7 --log-prefix "Default Deny"
iptables -A INPUT -j DROP
```
生成的 addrules.sh 脚本加入以上设置。

#### 第二步，设置重启自动加载 iptables 规则。
前面我们在各个节点设置了 iptables 规则，但是这些规则在系统重启后会丢失。为了使系统重启后自动重新设置这些规则，我们需要将当前的 iptables 规则保存起来，让系统重启时自动加载。需要进行下面两个步骤。

保存现有的规则：
```
$ sudo iptables-save > /etc/sysconfig/iptables
```
添加下面的命令行到 /etc/rc.d/rc.local 文件中：

```
$ iptables-restore < /etc/sysconfig/iptables

```
#### 第三步，自动化。
在上面的步骤中，我们自动生成了 iptables 规则，并手动登陆到节点进行设置。你肯定也发现了，整个流程手动操作过多，容易出错，效率还低。你可以参考设置过程，将这些设置工作自动化，比如编写脚本，一键刷新所有节点的 iptables 规则。

另外，我们再来看下在新增节点和删除节点两种场景下，如何设置 iptables 规则。



**场景 1：新增节点**
如果我们要扩容一个节点，也需要在新节点设置防火墙规则，并在数据库节点设置防火墙规则允许来自新节点的访问。假如我们新增一个 10.0.4.22 节点，这里要设置防火墙规则，需要下面的 4 个步骤。

编辑 access.yaml，在 hosts 列表下新增 10.0.4.22 节点 IP。编辑后内容如下：

```
# 允许登录SSH节点的来源IP，可以是固定IP(例如10.0.4.2)，也可以是个网段，0.0.0.0/0代表不限制来源IP
ssh-source: 10.0.4.0/24

# IAM应用节点列表（来源IP）
hosts:
  - 10.0.4.20
  - 10.0.4.21
  - 10.0.4.22

# 来源IP可以访问的应用端口列表（iam-apiserver, iam-authz-server, iam-pump对外暴露的的端口）
ports:
  - 8080
  - 8443
  - 9090
  - 9443
  - 7070

# 来源IP可以访问的数据库端口列表（Redis, MariaDB, MongoDB）
dbports:
  - 3306
  - 6379
  - 27017
```

在 10.0.4.22 节点设置 iptables 规则：

```
$ go run tools/geniptables/main.go -c access.yaml -t app -a -o firewall.sh
```
将 firewall.sh 脚本拷贝到 10.0.4.22 节点，并执行。

在已有节点新增规则，允许来自 10.0.4.22 的 Nginx 服务的访问：

```
$ go run tools/geniptables/main.go -c access.yaml -t app 10.0.4.22 -o addrules.sh
```
将 addrules.sh 脚本拷贝到存量节点，并执行。

在数据库节点新增 iptables 规则，以允许来自新节点的访问：

```
$ go run tools/geniptables/main.go -c access.yaml -t db 10.0.4.22 -o addrules.sh
```
将 addrules.sh 脚本拷贝到 10.0.4.20 节点执行即可。

**场景 2：删除节点。**
如果我们要删除一个节点，需要在保留的节点和数据库节点中，将该节点的访问权限删除。假如我们要删除 10.0.4.22 节点，设置防火墙规则需要下面 3 个步骤。

在保留节点删除 10.0.4.22 节点访问权限：

```
$ go run tools/geniptables/main.go -c access.yaml -t app --delete 10.0.4.22 -o delete.sh
```
将 delete.sh 脚本拷贝到 10.0.4.20 节点执行即可。

将下线的节点从 access.yaml 文件中的 hosts 部分删除。



### IAM 安全加固（内网安全）
这里，我们来看第二种情况：假定我们系统部署在一个安全的内网环境中，这时候加固系统就会变得异常简单，只需要允许来源 IP 为内网 IP 的客户端访问我们提供的各类端口即可。在我们设置完 iptables 规则之后，后续再新增或者删除节点，就不需要再做变更了。具体可以分为 5 个步骤。

具体可以分为 5 个步骤。

第一步，进入 iam 项目源码根目录。


第二步，配置 accesss.yaml（工具根据此配置，自动生成 iptables 设置脚本），内容如下（configs/access.yaml文件）：


```yaml
# 允许登录SSH节点的来源IP，可以是固定IP(例如10.0.4.2)，也可以是个网段，0.0.0.0/0代表不限制来源IP
ssh-source: 10.0.4.0/24

# 来源IP可以访问的应用端口列表（iam-apiserver, iam-authz-server, iam-pump对外暴露的的端口）
ports:
  - 8080
  - 8443
  - 9090
  - 9443
  - 7070

# 来源IP可以访问的数据库端口列表（Redis, MariaDB, MongoDB）
dbports:
  - 3306
  - 6379
  - 27017
```
上面配置中，我们仅仅指定了 IAM 服务端口和数据库端口。

第三步，生成 iptables 初始化脚本：

```
$ go run tools/geniptables/main.go -c access.yaml -t app --cidr=10.0.4.0/24 -a -o firewall.sh
$ ls firewall.sh
firewall.sh
```
第四步，将 firewall.sh 脚本拷贝到 10.0.4.20 和 10.0.4.21 节点执行：

```
$ scp firewall.sh root@10.0.4.20:/tmp/
$ scp firewall.sh root@10.0.4.21:/tmp/
```
登陆 10.0.4.20 和 10.0.4.21 机器执行 /tmp/firewall.sh 。

第五步，在 10.0.4.20（数据库节点）节点上，设置 iptables 规则，以允许各节点访问。

因为数据库节点也位于 10.0.4.20 节点，所以只需要添加新的 rule，并将 iptables -A INPUT -j DROP 规则放到最后执行即可。


```
$ go run tools/geniptables/main.go -c access.yaml -t db --cidr=10.0.4.0/24 -o addrules.sh
```

然后，将 addrules.sh 脚本拷贝到 10.0.4.20 节点执行。如果要增加节点，你只需要重新执行第三步，生成 firewall.sh 脚本，并将 firewall.sh 脚本拷贝到新节点上执行即可。删除节点，则不需要做任何操作。

接下来，我们再来看下**如何对 IAM 应用进行弹性伸缩操作。**


### 弹性伸缩
弹性伸缩包括扩容和缩容。扩容是指当业务量越来越大时，能够很容易地增加计算节点，来分散工作负载，从而实现计算等能力的扩展。缩容是指当业务量变小时，能够很容易地减少计算节点，从而减小成本。

在系统上线初期，通常业务量不会很大，但是随着产品的迭代，用户量的增多，系统承载的请求量会越来越多，系统承载的压力也会越来越大。这时，就需要我们的系统架构有能力进行水平扩容，以满足业务需求，同时避免因为系统负载过高造成系统雪崩。一些电商系统，在双 11 这类促销活动之前会提前扩容计算节点，以应对即将到来的流量高峰。但是活动过后，流量会逐渐下降，这时就需要我们的系统有能力进行缩容，以减少计算节点，从而节省成本。

一个可伸缩的系统架构，是我们在进行系统设计时必须要保证的。如果系统不具有伸缩性，那么当我们后期需要扩缩容时，就需要对代码进行大改，不仅会增加额外的工作量，还会拖累产品的迭代速度。而且你想想，改完之后还要测试，发布之后，还可能因为代码变更引入 Bug。总之，不具伸缩性的系统架构可以说是后患无穷。

IAM 系统在设计之初就考虑到了系统的伸缩能力，我们可以很容易地对系统进行扩缩容。下面，我来分别介绍下如何对系统进行扩容和缩容。

#### 系统扩容
系统扩容的步骤很简单，你只需要进行下面这 5 步：

根据需要申请计算节点，如无特殊需求，计算节点的配置、操作系统等要跟已有的节点保持一致。在新的节点上部署 iam-apiserver、iam-authz-server、iam-pump，部署方式跟部署其他节点一样。在新节点部署 Nginx，并将新节点的 IP 加入到已有所有节点的 Nginx upstream 配置中，重启 Nginx。在新节点部署 Keepalived，并将新节点的 IP 加入到已有所有节点的 unicast_peer 配置中，重启 Keepalived。修改 iptables 规则，并刷新所有机器的 iptables。

#### 系统缩容
系统缩容是系统扩容的逆向操作，也是 5 个步骤：

根据需要，确定要删除的节点。关闭待删除节点的 iam-apiserver、iam-authz-server、iam-pump 服务。从所有保留节点的 Nginx upstream 配置中，删除待删除节点的 IP 地址, 重启 Nginx。从所有保留节点的 Keepalived unicast_peer 配置中，删除待删除节点的 IP 地址, 重启 Keepalived。修改 iptables 规则，并刷新所有保留机器的 iptables。

### 总结
安全对于应用软件来说至关重要，在部署应用时，也一定要评估应用的安全性，并采取一定的措施来保证安全性。

在进行软件部署时，保证应用安全性最简单有效的方式是使用 iptables 规则来加固系统。实现思路也很简单，就是使用 iptables 规则，只允许特定来源的 IP 访问特定的端口。在业务正式上线之后，可能会遇到业务高峰期或低峰期。业务高峰期，可能需要添加机器，提高系统的吞吐量，可以在新机器上安装需要扩容的服务组件，并安装和配置好 Nginx 和 Keepalived，之后将该服务器添加到 Nginx 的 upstream 中。在业务低峰期时，可以将服务器从 Nginx 的 upstream 列表中移除，并关停 IAM 应用的服务。

## 43｜技术演进（上）：虚拟化技术演进之路
在前面的三讲中，我介绍了传统应用的部署方式。但是，随着软件架构进入云原生时代，我们越来越多地使用云原生架构来构建和部署我们的应用。为了给你演示如何使用云原生化的方式来部署 IAM 应用，接下来我会介绍如何基于 Kubernetes 来部署 IAM 应用。

在 Kubernetes 集群中部署 IAM 应用，会涉及到一些重要的云原生技术，例如 Docker、Kubernetes、微服务等。另外，云原生架构中还包含了很多其他的技术。为了让你提前了解后面部署需要的相关技术，同时比较通透地了解当前最火热的云原生架构，这一讲我就采用技术演进的思路，来详细讲解下云原生技术栈的演进中的虚拟化技术演进部分。

因为这一讲涉及的技术栈很多，所以我会把重点放在演进过程上，不会详细介绍每种技术的具体实现原理和使用方法。如果你感兴趣，可以自行学习，也可以参考我为你整理的这个资料：[awesome-books](https://github.com/marmotedu/awesome-books#%E4%BA%91%E8%AE%A1%E7%AE%97)。


在讲这个演进过程之前，我们先来看下这个问题：我们为什么使用云？

### 我们为什么使用云？
使用云的原因其实很简单，我们只是想在云上部署一个能够对外稳定输出业务能力的服务，这个服务以应用的形态部署在云上。为了启动一个应用，我们还需要申请系统资源。此外，我们还需要确保应用能够快速迭代和发布，出故障后能够快速恢复等，这就需要我们对应用进行生命周期管理。

应用、系统资源、应用生命周期管理这 3 个维度就构成了我们对云的所有诉求，如下图所示：

![img](https://static001.geekbang.org/resource/image/yy/eb/yyea295d642681444a004d55e8d73eeb.png?wh=1920x1010)

接下来的两讲，我就围绕着这 3 个维度，来给你详细介绍下每个维度的技术演进。这一讲，我会先介绍下系统资源维度的技术演进。在 44 讲，我会再介绍下应用维度和应用生命周期管理维度的技术演进。当前有 3 种系统资源形态，分别是物理机、虚拟机和容器，这 3 种系统资源形态都是围绕着虚拟化来演进的。所以，介绍系统资源技术的演进，其实就是介绍虚拟化技术的演进。接下来，我们就来看下虚拟化技术是如何演进的。


### 虚拟化技术的演进
虚拟化这个概念，其实在 20 世纪 60 年代就已经出现了。但因为技术、场景等限制，虚拟化技术曾沉寂过一段时间，直到 21 世纪虚拟机出现，虚拟化技术又迎来了一波爆发期，并逐渐走向成熟。

那么，什么是虚拟化技术呢？简单来讲，就是把计算机上的硬件、系统资源划分为逻辑组的技术，由此生成的仅仅是一个逻辑角度的视图。通过虚拟化技术，我们可以在一台计算机上运行多个虚拟机进程，进而发挥计算机硬件的最大利用率。

虚拟化分为很多种，例如操作系统虚拟化、存储虚拟化、网络虚拟化、桌面虚拟化等。其中，最重要的是操作系统虚拟化，支撑操作系统虚拟化的是底层 CPU、内存、存储、网络等的虚拟化，这些资源我们统称为计算资源。

因为计算资源的虚拟化在虚拟化领域占主导地位，所以很多时候我们说虚拟化技术演进，其实就是在说计算资源技术的演进。在我看来，虚拟化技术的演进过程如下：物理机阶段 -> 虚拟机阶段 -> 容器阶段（Docker + Kubernetes） -> Serverless 阶段。

#### 物理机阶段
上面我提到虚拟化技术包含很多方面，但是整个虚拟化技术是围绕着 CPU 虚拟化技术来演进的。这是因为，内存虚拟化、I/O 虚拟化的正确实现，都依赖于对内存、I/O 中一些敏感指令的正确处理，这就涉及到 CPU 虚拟化，所以 CPU 虚拟化是虚拟化技术的核心。因此，这一讲我会围绕着 CPU 虚拟化的演进，来讲解虚拟化技术的演进。这里，我先来介绍一下物理机阶段 CPU 的相关知识。

CPU 是由一系列指令集构成的，这些指令集主要分为两种，分别是特权指令集和非特权指令集。特权指令集是指那些可以改变系统状态的指令集，非特权指令集是指那些不会影响系统状态的指令集。我举个例子你就明白了：写内存是特权指令集，因为它可以改变系统的状态；读内存是非特权指令集，因为它不会影响系统的状态。

因为非特权指令集可能会影响整个系统，所以芯片厂商在 x86 架构上又设计了一种新模式，保护模式，这个模式可以避免非特权指令集非法访问系统资源。

保护模式是通过 Ring 来实现的。在 x86 架构上，一共有 4 个 Ring，不同的 Ring 有不同的权限级别：Ring 0 有最高的权限，可以操作所有的系统资源，Ring 3 的权限级别最低。Kernel 运行在 Ring 0 上，Application 运行在 Ring 3 上。Ring 3 的 Application 如果想请求系统资源，需要通过 system call 调用 Ring 0 的内核功能，来申请系统资源。

这种方式有个好处：可以避免 Applicaiton 直接请求系统资源，影响系统稳定性。通过具有更高权限级的 Kernel 统一调度、统一分配资源，可以使整个系统更高效，更安全。

x86 架构的 Ring 和调用关系如下图所示：

![img](https://static001.geekbang.org/resource/image/7e/8d/7e2868cd62baae3154bc4e8957e9b48d.png?wh=1920x708)

在物理机阶段，对外提供物理资源，这种资源提供方式面临很多问题，例如成本高，维护麻烦、需要建机房、安装制冷设备、服务器不方便创建、销毁等等。所以在云时代，和物理机相比，我们用得更多的是虚拟机。下面我们就来看虚拟机阶段。
#### 虚拟机阶段
这里，在讲虚拟化技术之前，我想先介绍下 x86 的虚拟化漏洞，CPU 虚拟化技术的演进也主要是围绕着解决这个漏洞来演进的。
#### 虚拟化漏洞
一个虚拟化环境分为三个部分，分别是硬件、虚拟机监控器（又叫 VMM，Virtual Machine Manager），还有虚拟机。

你可以把虚拟机看作物理机的一种高效隔离的复制，它具有三个特性：同质、高效、资源受控。这三个特点决定了不是所有体系都可以虚拟化，比如目前我们用得最多的 x86 架构，就不是一个可虚拟化的架构，我们称之为虚拟化漏洞。

在虚拟化技术产生后，诞生了一个新的概念：敏感指令。敏感指令是指可以操作特权资源的指令，比如修改虚拟机运行模式、物理机状态，读写敏感寄存器 / 内存等。显然，所有的特权指令都是敏感指令，但不是所有的敏感指令都是特权指令。特权指令和敏感指令的关系，可以简单地用这张图来表示：

![img](https://static001.geekbang.org/resource/image/30/6a/30da714fc8341600f558817789a9096a.png?wh=1920x1942)

在一个可虚拟化的架构中，所有的敏感指令应该都是特权指令。x86 架构中有些敏感指令不是特权指令，最简单的例子是企图访问或修改虚拟机模式的指令。所以，x86 架构是有虚拟化漏洞的。

#### Hypervisor 技术的演进
为了解决 x86 架构的虚拟化漏洞，衍生出了一系列的虚拟化技术，这些虚拟化技术中最核心的是 Hypervisor 技术。所以接下来，我就介绍下 Hypervisor 技术的演进。

Hypervisor，也称为虚拟机监控器 VMM，可用于创建和运行虚拟机 （VM）。它是一种中间软件层，运行在基础物理服务器和操作系统之间，可允许多个操作系统和应用共享硬件。通过让 Hypervisor 以虚拟化的方式共享系统资源（如内存、CPU 资源），一台主机计算机可以支持多台客户机虚拟机。

Hypervisor、物理机和虚拟机的关系如下图：

![img](https://static001.geekbang.org/resource/image/2d/7f/2d471ca14d50b80ffcd421c8719cf17f.png?wh=1920x1080)

按时间顺序，Hypervisor 技术的发展依次经历了下面 3 个阶段：
- 软件辅助的完全虚拟化（Software-assisted full virtualization）：该虚拟化技术在 1999 年出现，里面又包含了解释执行（如 Bochs）、扫描与修补（如 VirtualBox）、二进制代码翻译（如 Vmware、Qemu）三种技术。
- 半虚拟化（Para-virtualization）：该虚拟化技术在 2003 年出现，也叫类虚拟化技术，典型的 Hypervisor 代表是 Xen。
- 硬件辅助的完全虚拟化（Hardware-assistant full virtualization ）：该虚拟化技术在 2006 年出现，典型的 Hypervisor 代表是 KVM。当前普遍使用的主流虚拟化技术，就是以 KVM 为代表的硬件辅助的完全虚拟化。

下面，我就来简单介绍下这三个阶段。

先来看第一个阶段，**软件辅助的完全虚拟化**，它又分为解释执行、扫描与修补、二进制代码翻译三个演进阶段。


解释执行
简单地说，解释执行的过程就是取一条指令，模拟出这条指令的执行效果，再取下一条指令。这种技术因为思路比较简单，所以容易实现，复杂度低。执行时，编译好的二进制代码是不会被载入到物理 CPU 直接运行的，而是由解释器逐条解码，再调入对应的函数来模拟指令的功能。解释过程如下图所示：

![img](https://static001.geekbang.org/resource/image/9d/c7/9d6c29e788e62ec16115d02a7f0807c7.png?wh=1920x1180)

因为每一条指令都要模拟，所以就解决了虚拟化漏洞，同时也可以模拟出一个异构的 CPU 结构，比如在 x86 架构上模拟出一个 ARM 架构的虚拟机。也正是因为每一条指令都需要模拟，不区别对待，导致这种技术的性能很低。

扫描与修补
由于解释执行性能损失很大，再加上虚拟机中模拟的虚拟 CPU 和物理 CPU 的体系结构相同（同质），这样大多数指令可以直接在物理 CPU 上运行。因此，CPU 虚拟化过程中，可以采用更优化的模拟技术来弥补虚拟化漏洞。

扫描与修补技术就是通过这种方式，让大多数指令直接在物理 CPU 上运行，而把操作系统中的敏感指令替换为跳转指令，或者会陷入到 VMM 中去的指令。这样，VMM 一旦运行到敏感指令，控制流就会进入 VMM 中，由 VMM 代为模拟执行。过程如下图所示：

![img](https://static001.geekbang.org/resource/image/f0/4e/f0fdb92c7c1fedc3bbedc90d411d314e.png?wh=1920x1460)

使用这种方式，因为大部分指令不需要模拟，可以直接在 CPU 上运行，所以性能损失相对较小，实现起来比较简单。

二进制代码翻译
这个算是软件辅助的完全虚拟化的主流方式了，早期的 VMware 用的就是这个技术。二进制代码翻译会在 VMM 中开辟一段缓存，将翻译好的代码放在缓存中。在执行到某条指令的时候，直接从内存中找到这条指令对应的翻译后的指令，然后在 CPU 上执行。

在性能上，二进制代码翻译跟扫描与修补技术各有长短，但是实现方式最为复杂。它的过程如下图所示：

![img](https://static001.geekbang.org/resource/image/ba/1a/ba3962c283c567d6f20c8d780930d41a.jpg?wh=1920x1698)

看到这里，你可能会对模拟和翻译这两个概念有疑惑，我在这里解释下模拟和翻译的区别：模拟是将 A 动作模拟成 B 动作，而翻译是将 A 指令翻译成 B 指令，二者是有本质不同的。


**然后，我们来看 Hypervisor 技术发展的第二个阶段，Para-virtualization。**

软件辅助的完全虚拟化对 x86 的指令做了翻译或者模拟，在性能上，多多少少都会有些损失，而这些性能损失在一些生产级的场景是不可接受的。所以，在 2003 年出现了 Para-virtualization 技术，也叫半虚拟化 / 类虚拟化。和之前的虚拟化技术相比，Para-virtualization 在性能上有了大幅度的提升，甚至接近于原生的物理机。

Para-virtualization 的大概原理是这样的：Hypervisor 运行在 Ring 0 中，修改客户机操作系统内核，将其中的敏感指令换成 hypercall。hypercall 是一个可以直接跟 VMM 通信的函数，这样就绕过了虚拟化的漏洞（相当于所有敏感指令都被 VMM 捕获了），同时不存在模拟和翻译的过程，所以性能是最高的。这个过程如下图所示：

![img](https://static001.geekbang.org/resource/image/6e/77/6ea013e621c58fb78d444449fb55f977.png?wh=1920x902)
因为要修改操作系统，所以不能模拟一些闭源的操作系统，比如 Windows 系列。另外，修改客户机操作系统内核还是有些开发和维护工作量的。所以，随着硬件辅助完全虚拟化技术的成熟，Para-virtualization 也逐渐被替换掉了。

**然后，我们来看 Hypervisor 技术发展的第三个阶段，硬件辅助的完全虚拟化。**
在 2006 年，Intel 和 AMD 分别在硬件层面支持了虚拟化，比如 Intel 的 VT-X 技术和 AMD 的 SVM。它们的核心思想都是引入新运行模式，可以理解为增加了一个新的 CPU Ring -1，权限比 Ring 0 还高，使 VMM 运行在 Ring -1 下，客户机内核运行在 Ring 0 下。

通常情况下，客户机的核心指令可以直接下达到计算机系统硬件执行，不需要经过 VMM。当客户机执行到敏感指令的时候，CPU 会从硬件层面截获这部分敏感指令，并切换到 VMM，让 VMM 来处理这部分敏感指令，从而绕开虚拟化漏洞。具体如下图所示：

![img](https://static001.geekbang.org/resource/image/e6/91/e60585ea58107b48af2815a3fd552591.png?wh=1920x929)

因为 CPU 是从硬件层面支持虚拟化的，性能要比软件模拟更高，同时硬件虚拟化可以不用去修改操作系统。所以，即使是现在，硬件辅助的完全虚拟化也是主流的虚拟化方式。


接下来我们来看虚拟化技术演进的第三阶段，容器阶段。

#### 容器阶段
2005 年，诞生了一种新的虚拟化技术，容器技术。容器是一种轻量级的虚拟化技术，能够在单一主机上提供多个隔离的操作系统环境，通过一系列的命名空间隔离进程，每个容器都有唯一的可写文件系统和资源配额。

#### 容器引擎 Docker
容器技术的的代表项目就是 Docker，Docker 是 Docker 公司在 2013 年推出的容器项目，因为轻量、易用的特点，迅速得到了大规模的使用。Docker 的大规模应用使得系统资源的形态由虚拟机阶段进入到了容器阶段。

基于 Docker 容器化技术，开发者可以打包他们的应用以及依赖和配置到一个可移植的容器中，然后发布到任何流行的 Linux/Windows 机器上。开发者无需关注底层系统、环境依赖，这使得容器成为部署单个微服务的最理想的工具。

Docker 通过 Linux Namespace 技术来进行资源隔离，通过 Cgroup 技术来进行资源分配，具有更高的资源利用率。Docker 跟宿主机共用一个内核，不需要模拟整个操作系统，所以具有更快的启动时间。在 Docker 镜像中，已经打包了所有的依赖和配置，这样就可以在不同环境有一个一致的运行环境，所以能够支持更快速的迁移。另外，Docker 的这些特性也促进了 DevOps 技术的发展。

我这里拿 Docker 和虚拟机来做个对比，让你感受下 Docker 的强大。二者的架构对比如下图所示：

![img](https://static001.geekbang.org/resource/image/7e/98/7e24d5667f4d41724c9cc0ab6fee5398.png?wh=1920x822)


可以看到，Container 相比于虚拟机，不用模拟出一个完整的操作系统，非常轻量。因此，和虚拟机相比，容器具有下面这些优势：

![img](https://static001.geekbang.org/resource/image/65/5c/65049ffd61b12de9fcd5ccf8f684385c.png?wh=1920x1209)

从这张表格里你可以看到，在启动时间、硬盘占用量、性能、系统支持量、资源使用率、环境配置这些方面，Docker 和虚拟机相比具有巨大的优势。这些优势，使得 Docker 成为比虚拟机更流行的应用部署媒介。

也许这时你想问了：Docker 就这么好，一点缺点都没有吗？显然不是的，Docker 也有自己的**局限性**。

我们先来看一下生产环境的 Docker 容器是什么样的：一个生产环境的容器数量可能极其庞大，关系错综复杂，并且生产环境的应用可能天生就是集群化的，具备高可用、负载均衡等能力。**Docker 更多是用来解决单个服务的部署问题，无法解决生产环境中的这些问题。并且，不同节点间的 Docker 容器无法相互通信。**

**不过，这些问题都可以通过容器编排技术来解决**。业界目前也有很多优秀的容器编排技术，比较受欢迎的有 Kubernetes、Mesos、Docker Swarm、Rancher 等。这两年，随着 Kubernetes 的发展壮大，Kubernetes 已经成为容器编排的事实标准。

#### 容器编排技术 Kubernetes
因为我们后面会基于 Kubernetes 来部署 IAM 应用，所以这里我会详细介绍下 Kubernetes 服务编排技术。

Kubernetes 是 Google 开源的一个容器编排技术（编排也可以简单理解为调度、管理），用于容器化应用的自动化部署、扩展和管理。它的前身是 Google 内部的 Borg 项目。Kubernetes 的主要特性有网络通信、服务发现与负载均衡、滚动更新 & 回滚、自愈、安全配置管理、资源管理、自动伸缩、监控、服务健康检查等。

Kubernetes 通过这些特性，解决了生产环境中 Docker 存在的问题。Kubernetes 和 Docker 相辅相成，Kubernetes 的成功也使 Docker 有了更大规模的使用，最终使得 Docker 成为比虚拟机更流行的计算资源提供方式。

接下来，我围绕着下面这张架构图来介绍 K8S（Kubernetes）的基本概念：

![img](https://static001.geekbang.org/resource/image/ee/7c/ee25460fdbc4b257440dd4f6b109237c.jpg?wh=1920x1149)

Kubernetes 采用的是 Master-Worker 架构模式。其中，Master 节点是 Kubernetes 最重要的节点，里面部署了 Kubernetes 的核心组件，这些核心组件共同构成了 Kubernetes 的 Control Plane（控制面板）。而 Worker，也就是图中的 Node Cluster，就是节点集群。其中，每一个 Node 就是具体的计算资源，它既可以是一台物理服务器，也可以是虚拟机。

我们先来介绍下 Master 节点上的组件。
- Kube API Server：提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制。
- Kube Scheduler：负责资源的调度，按照预定的调度策略将 Pod 调度到相应的机器上。
- Kube Controller Manager：负责维护集群的状态，比如故障检测、自动扩展、滚动更新等。
- Cloud Controller Manager：这个组件是在 Kubernetes 1.6 版本加入的与基础云提供商交互的控制器。
- Etcd：分布式的 K-V 存储，独立于 Kubernetes 的开源组件。主要存储关键的元数据，支持水平扩容保障元数据的高可用性。基于 Raft 算法实现强一致性，独特的 watch 机制是 Kubernetes 设计的关键。


介绍完了 Master，再看看每一个 Kubernetes Node 需要有哪些组件。

- Kubelet：负责维持容器的生命周期，同时也负责 volume（CVI）和网络（CNI）的管理。
- kube-proxy：kube-proxy 是集群中每个节点上运行的网络代理，维护节点上的网络规则，它允许从集群的内部或外部网络与 Pod 进行网络通信，并负责为 Service 提供集群内部的服务发现和负载均衡。
- Container Runtime：负责镜像管理以及 Pod 和容器的真正运行（CRI），默认的容器运行时为 Docker。

上面那张架构图里的 Service、Deployment、Pod 等，都不算是组件，而是属于 Kubernetes 资源对象，我们稍后再做介绍。这里我先简单介绍下架构图的 UI dashboard 和 kubectl。

- UI dashboard 是 Kubernetes 官方提供的 web 控制面板，可以对集群进行各种控制，直接与 API Server 进行交互，其实就是 API Server 暴露出来的可视化接口。在这里可以直观地创建 Kubernetes 对象、查看 Pod 运行状态等。UI dashboard 界面如下图所示：

![img](https://static001.geekbang.org/resource/image/5c/49/5c6acce4e90048e63426b9e896be8b49.png?wh=1920x1148)

- kubectl 是 Kubernetes 的客户端工具，提供了非常多的命令、子命令、命令行选项，支持开发或运维人员在命令行快速操作 Kubernetes 集群，例如对各类 Kubernetes 资源进行增删改查操作，给资源打标签，等等。下面是执行kubectl describe service iam-pump命令获取iam-pump详细信息的命令行截图：

![img](https://static001.geekbang.org/resource/image/60/38/6046e3586eefce16b923aebbf0de2538.png?wh=1269x721)

Kubernetes 有多种多样的 Objects，如果要查看所有 Objects 的 Kind，可以使用命令kubectl api-resources。我们通过这些 Objects 来完成 Kubernetes 各类资源的创建、删除等操作。因为我们这一讲的核心目的是介绍云原生技术的演进，所以不会详细介绍 Kubernetes 资源对象的使用方式。你如果感兴趣，可以查看Kubernetes 官方文档。

我这里简单介绍一下 Kubernetes 对象的一些基本信息，以及在架构图中出现的 Deployment、Pod、Service 三种对象。

下面是一个典型的 Kubernetes 对象 YAML 描述文件：


```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  # ...
```
在这个描述文件中，apiVersion 和 kind 共同决定了当前 YAML 配置文件应该由谁来处理，前者表示描述文件使用的 API 组，后者表示一个 API 组中的一个资源类型。这里的 v1 和 Pod 表示的就是核心 API 组 api/v1 中的 Pod 类型对象。

metadata 则是一些关于该对象的元数据，其中主要有name、namespace、labels、annotations。其中， name 需要在 namespace 下唯一，成为这个对象的唯一标识。label和annotations分别是这个对象的一些标签和一些注解，前者用于筛选，后者主要用来标注提示性的信息。

**接下来，我再介绍下 Pod、Deployment、Service 这 3 种对象。**

Pod
Pod 是 Kubernetes 中运行的最小的、最简单的计算单元，我觉得 Pod 也是 Kubernetes 最核心的对象。Pod 中可以指定运行多个 Containers，可以挂载 volume 来实现部署有状态的服务，这些都在spec中被指定。对于任意类型的对象，spec都是用来描述开发人员或运维人员对这个对象所期望的状态的，对于不同类型的对象，spec有不同的子属性。下面是一个 Pod 示例，我们在 YAML 描述文件里指定了期望 Pod 运行的 Docker 镜像和命令：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  labels:
    app: busybox
spec:
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: busybox
  restartPolicy: Always
```
Deployment
一般来说，我们不会直接部署 Pod，而是部署一个 Deployment 或者 StatefulSet 之类的 Kubernetes 对象。Deployment 一般是无状态服务；StatefulSet 一般是有状态服务，会使用 volume 来持久化数据。下面是一个部署两个 Pod 的示例：

```yaml
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
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
```

Service
Service 是 Kubernetes 中另一个常见的对象，它的作用是作为一组 Pod 的负载均衡器，利用 selector 将 Service 和 Pod 关联起来。下面这个示例里，使用的是run: my-nginx这个 label。这个 Service 绑定的就是上面那个 Deployment 部署的 nginx 服务器：

```yaml
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

```


最后我还想介绍下基于 Kubernetes 的容器云平台。各大公有云厂商，都有基于 Kubernetes 的容器管理平台，目前国内容器服务平台做得比较好的有腾讯云容器服务 TKE、阿里云容器服务 ACK。

TKE 基于原生 Kubernetes ，提供以容器为核心的解决方案，解决用户开发、测试及运维过程的环境问题，帮助用户降低成本、提高效率。腾讯云容器服务 TKE 完全兼容原生 Kubernetes API，并扩展了腾讯云的云硬盘、负载均衡等 Kubernetes 插件，同时以腾讯云私有网络为基础，实现了高可靠、高性能的网络方案。


#### Serverless 阶段
容器阶段之后，虚拟化技术的演进方向是什么呢？我们接着来看下 Serverless 阶段。

在 2014 年的时候，AWS 推出了 Lambda 服务，这是一个 Serverless 服务。从此，Serverless 越来越引人注意，成为了这几年最受关注的技术。我先介绍下什么是 Serverless。Serverless 直译过来就是无服务器，无服务器并不代表 Serverless 真的不需要服务器，只不过服务器的管理，以及资源的分配部分对用户不可见，而是由平台开发商维护。Serverless 不是具体的一个编程框架、类库或者工具，它是一种软件系统架构思想和方法。它的核心思想是：用户无需关注支撑应用服务运行的底层资源，比如 CPU、内存和数据库等，只需要关注自己的业务开发就行了。

Serverless 具有很多特点，核心特点主要有下面这几个。
- 无穷弹性计算能力：根据请求，自动水平扩容实例，拥有近乎无限的扩容能力。
- “零”运维：不需要申请和运维服务器。极致的伸缩能力：能够根据 CPU、内存、请求量等指标敏感地弹性伸缩，并支持缩容到 0。
- 按量计费：真正按使用量去计费。


在我看来，Serverless 有 3 种技术形态，分别是云函数、Serverless 容器、BaaS（Backend as a Service），如下图：

![img](https://static001.geekbang.org/resource/image/cb/19/cba931763a846ab8008e5600cd6e5419.jpg?wh=1920x641)

这 3 种 Serverless 技术形态中，Serverless 容器是核心，云函数和 BaaS 起辅助作用。Serverless 容器可以承载业务的核心架构，云函数则可以很好地适配触发器场景，BaaS 则可以满足我们对各种其他 Serverless 组件的需求，例如 Serverless 数据库、Serverless 存储等。

这 3 种技术形态，各大公用云厂商都早已有相应的产品，其中比较优秀的产品是腾讯云推出的 Serverless 产品，SCF、EKS 和 TDSQL-C。下面我分别介绍下。

- EKS：弹性容器服务（Elastic Kubernetes Service）是腾讯云容器服务推出的无需用户购买节点即可部署工作负载的服务模式。EKS 完全兼容原生 Kubernetes，支持使用原生方式购买及管理资源，按照容器真实使用的资源量计费。
- SCF：云函数（Serverless Cloud Function）是腾讯云为企业和开发者们提供的无服务器执行环境，帮助你在无需购买和管理服务器的情况下运行代码。你只需使用平台支持的语言编写核心代码，并设置代码运行的条件，就能在腾讯云基础设施上弹性、安全地运行代码。
- TDSQL-C：云原生数据库（Cloud Native Database TDSQL-C）是腾讯云自研的新一代高性能高可用的企业级分布式云数据库，具有高吞吐量、高可靠性等优点。

我们开始的时候提到，应用、系统资源、应用生命周期管理这 3 个维度构成了我们对云的所有诉求。那么到这里，系统资源维度的技术演进我就介绍完了。下一讲，我会介绍应用维度和应用生命周期管理维度的技术演进。


### 总结
这一讲，我主要通过虚拟化技术的演进，介绍了系统资源维度的技术演进。


虚拟化技术的演进流程为：物理机阶段 -> 虚拟机阶段 -> 容器阶段 -> Serverless 阶段。其中，物理机到虚拟机阶段的演进技术，主要是为了解决 x86 架构的虚拟化漏洞。要虚拟 CPU、内存和 I/O，就需要捕获其中的敏感指令，防止这些敏感指令修改系统状态，影响系统的稳定性。x86 架构有些敏感指令不是特权指令，导致这些指令可以从客户机中直接在物理 CPU 上执行，从而可能会影响系统状态。所以，我们说 x86 架构是有虚拟化漏洞的。

在虚拟机阶段，又诞生了 3 种不同的虚拟化技术，分别是软件辅助的完全虚拟化、半虚拟化和硬件辅助的完全虚拟化。因为硬件辅助的完全虚拟化技术不需要修改客户机内核，并且有着接近物理机的性能，所以成为当前的虚拟化主流技术，并以 KVM 为事实技术标准。

因为容器技术比虚拟机更加轻量，再加上 Docker、Kubernetes 项目的诞生，使得大规模使用容器技术变得可行，所以这几年系统资源的提供形态已经由虚拟机转变成了容器。

系统资源的最终形态，我认为会是 Serverless。Serverless 技术中，又分为 3 种技术形态：云函数、Serverless 容器和 BaaS。在业务架构 Serverless 化的过程中，整个部署架构会以 Serverless 容器为主，云函数为辅。


## 44｜技术演进（下）：软件架构和应用生命周期技术演进之路

应用、系统资源、应用生命周期管理这 3 个维度，构成了我们对云的所有诉求。上一讲，我从系统资源维度，介绍了虚拟化技术的演进之路。这一讲，我会介绍下应用维度和应用生命周期管理维度的技术演进。应用软件架构是用来构建应用的，不同的软件架构，构建应用的方式、效率，以及所构建应用的可维护度、性能都是不同的。随着技术的不断更新迭代，应用软件架构也在不断往前演进。这一讲我们就来看看，应用软件架构都有哪些，这些软件架构都有什么特点，以及它们之间是如何演进的。

至于应用生命周期管理维度，我在 09 讲 中已经介绍了应用生命周期管理技术的演进，这一讲也会再补充一些核心的技术，比如日志、监控告警、调用链等。接下来，我们就先来看下软件架构的演进之路。

### 软件架构的演进
软件架构技术演进如下图所示：

![img](https://static001.geekbang.org/resource/image/f1/cd/f17c948f69e84efef776ee367a145dcd.jpg?wh=2248x618)

最开始，我们使用单体架构来构建应用，后面逐渐演进为 SOA 架构。不管是单体架构，还是 SOA 架构，都很难满足互联网时代应用快速迭代的诉求。所以，在互联网时代，应用软件架构又演进成了微服务架构。当前我们正处在微服务架构阶段，也有很多团队的业务正在尝试使用 Service Mesh 替代微服务架构中的一些功能。

随着 Serverless 云函数的诞生，也诞生了一种新的软件架构，FaaS 架构。这里我先简单介绍下它，后面再详细讲。FaaS 架构因为限制多、使用场景局限，目前还仅仅适用于云函数这种系统资源形态，我个人认为它不会成为未来主流的软件架构。还要说明下，业界目前并没有 FaaS 软件架构这个说法，大家说到 FaaS，一般指的都是云函数这种技术形态。这里为了方便描述，我们先这样表达。

接下来，我仍然以技术演进的思路，来介绍下这些软件架构。首先来看下最早的单体架构。

#### 单体架构
在最早的时候，我们用的软件架构是单体架构。在单体架构中，我们会将应用程序的所有功能都存放在一个代码仓库中，并且发布时，也是发布整个代码仓库的代码和功能。在单体架构中，应用软件一般会包含四层，分别是表示层、业务逻辑层、数据访问层、数据库，如下图所示：

![img](https://static001.geekbang.org/resource/image/cd/a9/cde65byyc639286dbb27ac6a6abd8da9.jpg?wh=2248x3110)

这里简单介绍下每层的功能。

- 表示层：用于直接和用户交互，通常是网页、UI 界面。
- 业务逻辑层：用来进行业务逻辑处理。使用表示层传来的参数，进行业务逻辑处理，并将结果返回给表示层。
- 数据访问层：用来操作数据库，通常包括数据的 CURD 操作。例如，从数据库中查询用户信息，或者往数据库增加一条用户记录。
- 数据库：存储数据的物理介质。我们通过数据访问层来访问数据库中的数据。

单体架构的优点是应用开发简单，技术单一，测试、部署相对简单明了。因此它比较适合用户访问量较小的应用服务端。但它的缺陷也是非常明显的。随着业务的发展，项目越来越大，单体架构会带来开发效率低、发布周期长、维护困难、稳定性差、扩展性差等问题。另外，单体架构的技术栈也不易扩展，只能在原有的基础上，不断地进行局部优化。

#### SOA 架构
为了解决单体架构在业务代码变大时带来的各种问题，SOA 架构出现了。

SOA 架构是面向服务的软件架构，它的核心理念是：基于 SOA 的架构思想，将重复共用的功能抽取为组件，以服务的方式给各系统提供服务，服务之间通过 ESB 企业服务总线进行通信。如下图所示：

![img](https://static001.geekbang.org/resource/image/73/5c/73c40dab7430b1b5d320944b1d13515c.jpg?wh=2248x2489)


SOA 架构中，主要有两个角色，分别是服务提供者和服务消费者。服务消费者可以通过发送消息来调用购买商品、申请售后的服务，这些消息由 ESB 总线转换后，发送给对应的服务，实现 SOA 服务之间的交互通信。SOA 架构主要适用于大型软件服务企业对外提供服务的场景，至于一般业务场景就并不适用了。这是因为，SOA 服务的定义、注册和调用都需要繁琐的编码或者配置来实现，并且 ESB 总线也容易导致系统的单点风险，并拖累整体性能。


#### 微服务架构
在互联网时代，越来越多的企业推出了面向普通大众的网站和应用。这些企业没有能力，也没有必要构建和维护 ESB 企业服务总线。于是，基于 SOA 架构，又演进出了微服务架构。

微服务架构由 Matrin Fowler 在 2014 年提出，它的理念是将业务系统彻底地组件化和服务化，形成多个可以独立开发、部署和维护的服务或应用的集合。微服务之间采用 RESTful 等轻量的传输协议，来应对更快的需求变更和更短的开发迭代周期。如下图所示：

![img](https://static001.geekbang.org/resource/image/a2/8f/a2d303433462c384322a4342ff10fe8f.jpg?wh=2248x1984)

微服务架构提出得比较早，但在这几年才逐渐流行起来。这是什么原因呢？一方面，微服务架构基于自身的特点，确实能够解决其他软件架构中存在的一些问题；另一方面，Docker + Kubernetes 等云原生技术这几年也发展了起来，能够很好地支撑微服务的部署和生命周期管理。

总体来说，微服务架构有下面这几个特点：
- 微服务遵循单一原则，每个微服务负责一个独立的上下文边界；
- 微服务架构提供的服务之间采用 RESTful 等轻量协议传输，比 ESB 更轻量；
- 每个服务都有自己独立的业务开发活动和周期；
- 微服务一般使用容器技术独立部署，运行在自己的独立进程中，合理分配其所需的系统资源。这样，开发者就可以更加方便地制定每个服务的优化方案，提高系统可维护性。


微服务架构有很多优点，但也存在着问题。因为一个应用被拆分成一个个的微服务，随着微服务的增多，就会引入一些问题，比如微服务过多导致服务部署复杂。微服务的分布式特点也带来了一些复杂度，比如需要提供服务发现能力、调用链难以追踪、测试困难，等等。服务之间相互依赖，有可能形成复杂的依赖链路，往往单个服务异常，其他服务都会受到影响，出现服务雪崩效应。

目前业界针对这些问题也有一些标准的解决方案，比如，可以通过 Kubernetes、Helm 和 CI/CD 技术，解决微服务部署复杂的问题。至于微服务的分布式特点所带来的复杂性，可以通过一些微服务开发框架来解决。一些业界比较知名的微服务开发框架，比如 Spring Cloud 和 Dubbo，已经很好地解决了上面的问题。另外，云原生相关的技术也可以解决微服务调用链跟踪复杂、故障排障困难等问题。

另外，在我的日常开发中，经常会有开发者把 SOA 架构和微服务架构给搞混，所以我在这里再来介绍下二者的相同点和不同点。


微服务架构是 SOA 架构设计思想的另一种实现方式，这是二者相同的地方。至于区别，主要有三个。理解了下面这三点，以后你在开发中就很容易区分它们了。

- SOA 中的服务，其实只能属于某个应用的服务之一，微服务中的服务则是一个独立的服务，可以被多个应用共用。
- SOA 强调尽可能多地共享，而微服务强调尽可能少地共享。
- SOA 架构中，服务之间通过 ESB 来通信，而微服务中，服务之间通过轻量化机制，比如 RESTful 来实现通信。

#### Service Mesh
在讲微服务的时候，我提到微服务架构的一些问题可以通过一些微服务开发框架来解决，比如 Spring Cloud 和 Dubbo。但这里也有个问题：这些框架通常是侵入式的，比如语言只能限制在 Java，并且开发的时候要按框架的指定方式来开发。这个理念跟微服务的独立技术栈也是相反的。

2017 年底 Service Mesh（服务网格）的出现解决了这个问题，它是一种非侵入式技术，可以提供服务之间的网络调用、限流、熔断和服务监控等功能。Service Mesh 类似于 TCP/IP 协议，无需应用层感知，开发者只需要开发应用程序即可。所以，Service Mesh 是致力于解决服务间通讯的基础设施层，它具有下面这几个特点：


- 应用程序间通讯的中间层。
- 轻量级网络代理。
- 非侵入式，应用程序无感知。
- 可以将服务治理功能，例如重试、超时、监控、链路追踪、服务发现等功能，以及服务本身解耦。

Service Mesh 目前的发展比较火热，社区有很多优秀的 Service Mesh 开源项目，例如 Istio 、Linkerd 等。当前最受欢迎的开源项目是 Istio。

Istio 是一个完全开源的服务网格，作为透明的一层接入到现有的分布式应用程序里，提供服务治理等功能。它也是一个平台，拥有可以集成任何日志、遥测和策略系统的 API 接口。

Istio 的大概实现原理是：每个服务都会被注入一个 Sidecar（边车）组件，服务之间通信是先通过 Sidecar，然后 Sidecar 再将流量转发给另一个服务。因为所有流量都经过一个 Sidecar，所以可以通过 Sidecar 实现很多功能，比如认证、限流、调用链等。同时还有一个控制面，控制面通过配置 Sidecar 来实现各种服务治理功能。


目前 Istio 的最新版本是 1.8，1.8 版本的 Istio 架构图如下：

![img](https://static001.geekbang.org/resource/image/1b/96/1b47b53095c5baa73609b90834ec3996.jpg?wh=2248x1579)


从图中你可以看到，Istio 主要包含两大平面。一个是数据平面（Data plane），由 Envoy Proxy 充当的 Sidecar 组成。另一个是控制平面（Control plane），主要由三大核心组件 Pilot、Citadel、Galley 组成。下面，我来分别介绍下这三大核心组件的功能。

- Pilot：主要用来管理部署在 Istio 服务网格中的 Envoy 代理实例，为它们提供服务发现、流量管理以及弹性功能，比如 A/B 测试、金丝雀发布、超时、重试、熔断等。
- Citadel：Istio 的核心安全组件，负责服务的密钥和数字证书管理，用于提供自动生成、分发、轮换及撤销密钥和数据证书的功能。
- Galley：负责向 Istio 的其他组件提供支撑功能，可以理解为 Istio 的配置中心，它用于校验进入网络配置信息的格式内容正确性，并将这些配置信息提供给 Pilot。


#### FaaS 架构
这几年，以云函数为代表的 Serverless 技术异常火爆。伴随着 Serverless 技术的发展，一个新的软件开发模式也诞生了，这就是 FaaS 架构。FaaS 架构提供了一种比微服务更加服务碎片化的软件架构模式。简单来说，FaaS 架构就是把之前一个完整的业务拆分成一个个 Function 来部署，通过事件来触发底层 Function 的执行。

Function 里可能会调用第三方组件，比如数据库、消息队列服务等，这些第三方组件在 Serverless 架构中，统称为 BaaS（Backend as a Serivce）。BaaS 把这些后端的服务能力抽象成 API 让用户调用，用户不需要关注这些后端组件的高可用、扩缩容等运维层面的点，只需要去使用就可以了。

下面是 FaaS 架构的示意图：

![img](https://static001.geekbang.org/resource/image/23/43/2388ed22bf86c5f73b06f89ded610b43.jpg?wh=2248x775)


从这张图里你可以看到，用户通过浏览器、手机、小程序等客户端请求触发器服务，例如 API 网关、COS 对象存储、CLS 日志等。这些触发器服务在收到来自用户的请求之后，会触发它们所绑定的云函数，云函数会根据请求量等数据，实时启动多个并发实例。在触发云函数时，也会传递参数给云函数，并在云函数中使用这些参数，进行一些业务逻辑处理。例如，调用第三方的服务，将处理结果保存在后端数据库中。

在我看来，FaaS 架构未来不会成为主流，更多的是存在于云函数的场景中。我这么说是因为，如果将应用拆分成一个个 Function，这些 Function 的部署、维护，以及之间的通信会是一个巨大的挑战，从目前来看，还不存在解决这种挑战的技术和条件。另外，FaaS 架构也不适合承载一些较重的业务逻辑，比如还没法大规模迁移企业的应用系统。

### 应用生命周期管理技术：监控告警、日志、调用链
在这门课的 09 讲 中，我已经详细介绍了应用生命周期管理技术的演进。这里我们可以再回顾一下：应用生命周期，最开始主要是通过研发模式来管理的，按时间线先后出现了瀑布模式、迭代模式、敏捷模式。接着，为了解决研发模式中的一些痛点，出现了另一种管理技术，也就是 CI/CD 技术。随着 CI/CD 技术的成熟，又催生了另一种更高级的管理技术 DevOps。

其他的细节内容，如果有遗忘，你可以返回 09 讲 再复习一下，这里就不再重复介绍了。接下来，对于应用生命周期管理技术，我会补充一些之前没有讲到的重要技术，包括下面这三个：
- 监控告警组件，Prometheus；
- 统一日志管理框架，EFK；
- 调用链跟踪组件，Jaeger。

需要说明的是，这些技术之间不存在演进关系，而是平级的，共同作为应用生命周期管理技术的补充。


#### 监控告警组件：Prometheus
对于应用来说，监控告警功能是必不可少的一项功能，能够让开发者或运维人员及时感知到程序异常，并及时修复。另外，监控也能够收集一些有用的数据，供后面的运营分析使用。云原生技术栈中，也有很多开源的优秀监控告警项目，例如 Zabbix、Prometheus 等，其中最受欢迎的是Prometheus。

Prometheus 是一款开源的、自带时序数据库的监控告警系统。目前，Prometheus 已经成为 Kubernetes 集群中监控告警系统的标配。它具有下面这几个特点：

- 强大的多维度数据模型；
- 在多维度上灵活地查询语言；
- 不依赖分布式存储，单主节点工作；
- 通过基于 HTTP 的 pull 方式，采集时序数据；
- 可以通过 Push Gateway 进行时序列数据推送；
- 可以通过服务发现或者静态配置，去获取要采集的目标服务器；
- 多种可视化图表及仪表盘支持 (Grafana)。

Prometheus 的架构如下图所示：

![img](https://static001.geekbang.org/resource/image/04/04/0428cf195e1c7e8c2fd024c87bc3a904.jpg?wh=2248x1416)

从上图可以看出，Prometheus 的主要模块包括 Prometheus Server、Exporters、Pushgateway、Alertmanager 以及 Grafana 图形界面。这些模块，有些是可选的，有些是必选的，大部分组件使用 Golang 编写。下面我来分别介绍下。

- Prometheus Server（必选）：Prometheus 的核心服务，会定期从 Jobs/exporters 或者 Pushgateway 中拉取监控数据，并将时间序列（time-series）数据保存 TSDB 中，TSDB 是一个时间序列数据库。
- Client Library（必选）: Prometheus 的客户端，应用程序使用 Client Library，可以很方便地生成 metrics，并暴露一个 API 接口，供 Prometheus server 从中拉取（pull）metrics 数据。
- Pushgateway（可选）: 接收短期的 Jobs（Short-lived）推送（push）过来的 metrics 数据并缓存，供 Prometheus server 定期来 pull 这些监控数据。
- Exporters（可选）: 以 agent 的形式运行在需要采集监控数据的应用服务器上，收集应用程序监控数据，并提供 API 接口，供 Prometheus server 来 pull metrics 数据。
- Alertmanager（可选）: Prometheus 的告警组件，接收来自于 Prometheus server 的 alerts，将这些 alerts 去重、分组，并往配置的接收目的地发送告警。
- Grafana（可选）：Grafana 是一款跨平台、开源的可视化数据展示工具，可以用来统计和展示 Prometheus 监控数据，并带有告警功能，采用 Go 语言开发。

Prometheus 大致的工作流程是：

1. Prometheus Server 定期从配置好的 jobs 或者 Exporters 中拉 metrics，或者接收来自 Pushgateway 的 metrics，再或者从其他的 Prometheus Server 中拉 metrics。
2. Prometheus Server 在本地存储收集到的 metrics，并运行已经定义好的 alert.rules，记录新的时间序列，或者向 Alertmanager 推送警报。
3. Alertmanager 根据配置文件，对接收到的警报进行处理，发出告警。
4. Grafana 在图形界面中，可视化地展示采集数据。

Prometheus 会将所有采集到的样本数据以时间序列的方式保存在内存数据库中，并且定时保存到硬盘上。time-series 是按照时间戳和值的序列顺序存放的。每条 time-series 通过指标名称 (metrics name) 和一组标签集 (labelset) 命名，如下所示：

```
<--------------- metric ---------------------><-timestamp -><-value->
http_request_total{status="200", method="GET"}@1434417560938 => 94355
http_request_total{status="200", method="GET"}@1434417561287 => 94334

http_request_total{status="404", method="GET"}@1434417560938 => 38473
http_request_total{status="404", method="GET"}@1434417561287 => 38544

http_request_total{status="200", method="POST"}@1434417560938 => 4748
http_request_total{status="200", method="POST"}@1434417561287 => 4785
```

在 time-series 中的每一个点，我们称为一个样本（sample）。样本由下面三个部分组成。
- 指标 (metric)：metric name 和描述当前样本特征的 labelsets。
- 时间戳 (timestamp)：一个精确到毫秒的时间戳。
- 样本值 (value)： 一个 folat64 的浮点型数据，表示当前样本的值。


#### 统一日志管理框架：EFK
我们通过监控告警服务感知到程序异常，这时候需要开发者或者运维人员介入排障。排障最有效的手段，是查看日志。所以，对于一个应用来说，一个优秀的日志系统也是必不可少的功能。

在一个大型的分布式系统中，有很多组件，这些组件分别部署在不同的服务器上。如果系统出故障，需要查看日志排障。这时候，你可能需要登陆不同的服务器，查看不同组件的日志，这个过程是非常繁琐、低效的，也会导致排障时间变长。故障时间越久，意味着给客户带来的损失越大。

所以，在一个大型系统中，传统的日志查看手段已经满足不了我们的需求了。这时候，我们需要有一个针对分布式系统的日志解决方案。当前，业界有不少成熟的分布式日志解决方案，其中使用最多的是 EFK 日志解决方案。甚至可以说，EFK 已经成为分布式日志解决方案的事实标准。


EFK 中包含三个开源的软件，分别是 Elasticsearch、FlieBeat、Kibana。下面，我来介绍下这三个开源软件：
- Elasticsearch：简称 ES，是一个实时的、分布式的搜索引擎，通常用来索引和搜索大规模的日志数据，并支持全文、结构化的搜索。
- FlieBeat：轻量的数据采集组件，以 agent 的方式运行在需要采集日志的服务器上。FlieBeat 采集指定的文件，并上报给 ES。如果采集日志量大，也可以上报给 Kafka，再由其他组件消费 Kafka 中的日志并转储到 ES 中。
- Kibana：用于展示 ES 中存储的日志数据，支持通过图表进行高级数据分析及展示。

EFK 的架构图如下：

![img](https://static001.geekbang.org/resource/image/64/10/64c523c18ee7d86382db3aea06bf8b10.jpg?wh=2248x669)

通过 Filebeat 采集所在服务器上各服务组件的日志，并上传到 Kafka 中。Logstash 消费 Kafka 中的日志，过滤后上报给 Elasticsearch 进行存储。最后，通过 Kibana 可视化平台来检索这些日志。Kibana 是通过调用 Elasticsearch 提供的 API 接口，来检索日志数据的。

当 Filebeat 的日志生产速度和 Logstash 的日志消费速度不匹配时，中间的 Kafka 服务，会起到削峰填谷的作用。

#### 调用链跟踪组件：Jaeger
在云原生架构中，应用普遍采用微服务。一个应用包含多个微服务，微服务之间会相互调用，这会给排障带来很大的挑战。比如，当我们通过前端访问应用报错时，我们根本不知道具体哪个服务、哪个步骤出问题了。所以这时候，应用就需要有分布式链路追踪能力。目前，业界也有多种分布式链路追踪系统，但用得最多的是Jaeger。


Jaeger 是 Uber 推出的一款开源分布式追踪系统，兼容 OpenTracing API。这里我们先来介绍两个概念：
- OpenTracing：它是一套开源的调用链追踪标准，通过提供厂商无关、平台无关的 API，来支持开发人员方便地添加 / 更换追踪系统的实现。
- 分布式追踪系统：用于记录请求范围内的信息，是我们排查系统问题和系统性能的利器。分布式追踪系统种类繁多，但核心步骤都有三个，分别是代码埋点、数据存储和查询展示。

Jaeger 架构图如下：

![img](https://static001.geekbang.org/resource/image/09/01/09dc7ae3eb2a32381b3940e8f8743901.png?wh=2239x1202)


Jaeger 中有 7 个关键组件，下面我来具体介绍下。
- instrument：将应用程序与 jaeger-client 装载起来，从而使应用程序可以上报调用链数据到 Jaeger。
- jaeger-client：Jaeger 的客户端 SDK，负责收集并发送应用程序的调用链数据到 jaeger-agent。
- jaeger-agent：接收并汇聚 Span 数据，并将这些数据上报给 jaeger-collector。
- jaeger-collector：从 jaeger-agent 收集 traces 信息，并通过处理管道处理这些信息，最后写入后端存储。jaeger-collector 是无状态的组件，可以根据需要水平扩缩容。
- Data Store：Jaeger 的后端存储组件。目前，支持 cassandra、elasticsearch。
- jaeger-ui：jaeger 的前端界面，用于展示调用链等信息。
- jaeger-query：用于从存储中检索 trace，并提供给 jaeger-ui。

下面，我通过一个 Jaeger 官方提供的All in One 教程来让你更好地理解 Jaeger。具体可以分成两个操作步骤。

第一步，使用jaeger-all-in-one安装 Jaeger 服务：

```
$ wget https://github.com/jaegertracing/jaeger/releases/download/v1.25.0/jaeger-1.25.0-linux-amd64.tar.gz
$ tar -xvzf jaeger-1.25.0-linux-amd64.tar.gz
$ mv jaeger-1.25.0-linux-amd64/* $HOME/bin
$ jaeger-all-in-one --collector.zipkin.host-port=:9411
```

第二步，启动一个HotROD示例应用，产生调用链：

```
$ example-hotrod all # 第 1) 我们已经安装了 example-hotrod 命令
```
访问http://$IP:16686/search可以查找调用链（IP 是 Jaeger 部署的服务器 IP 地址），如下图所示：

![img](https://static001.geekbang.org/resource/image/68/17/682b4c1ca9c9fd180f913aa12deb7817.jpg?wh=2249x1011)

查询到调用链列表后，可以点击任意一个调用链，查看其详细的调用过程，如下图所示：

![img](https://static001.geekbang.org/resource/image/b5/ff/b5afaefacfa0f87ecfb13774532ef8ff.jpg?wh=2249x1274)

具体如何使用 Jaeger 来记录调用链，你可以参考[Jaeger](https://github.com/jaegertracing/jaeger/tree/main/examples/hotrod) 官方给出的hotrod示例。


### 总结
最后，我们通过下面这张图，来对整个云技术的演进之路做个整体性的回顾：

通过这张图你可以看到，每种技术并不是孤立存在的，而是相互促进的。在物理机阶段，我们用的是瀑布开发模式和单体架构；在虚拟机阶段，用得比较多的是敏捷开发模式和 SOA 架构；在容器这个阶段，则使用 CI/CD 的开发模式和微服务架构。

在 Serverless 阶段，软件架构仍然采用微服务，不过在一些触发器场景，也可能会编写一些 FaaS 架构的函数，部署在类似腾讯云云函数这样的 FaaS 平台上；底层系统资源主要使用 Serverless 容器，并配合 Kubernetes 资源编排技术。在一些触发器场景中，也可能会使用云函数。应用程序中的第三方服务（BaaS），也都是越来越 Serverless 化的服务。应用生命周期管理技术也会演进为 CI/CD/CO 这种模式，其中 CI/CD 更加智能化，自动化程度更高。

这张图里，阴影部分是我们当前所处的阶段：容器技术得到了大规模普及，业界也在积极探索 Serverless 技术，并取得了卓有成效的结果。


## 45｜基于Kubernetes的云原生架构设计

前面两讲，我们一起看了云技术的演进之路。软件架构已经进入了云原生时代，云原生架构是当下最流行的软件部署架构。那么这一讲，我就和你聊聊什么是云原生，以及如何设计一种基于 Kubernetes 的云原生部署架构。

### 云原生简介

云原生包含的概念很多，对于一个应用开发者来说，主要关注点是如何开发应用，以及如何部署应用。所以，这里我在介绍云原生架构的时候，会主要介绍应用层的云原生架构设计和系统资源层的云原生架构设计。

在设计云原生架构时，应用生命周期管理层的云原生技术，我们主要侧重在使用层面，所以这里我就不详细介绍应用生命周期管理层的云原生架构了。后面的云原生架构鸟瞰图中会提到它，你可以看看。另外，在介绍云原生时，也总是绕不开云原生计算基金会。接下来，我们就先来简单了解下 CNCF 基金会。

### CNCF（云原生计算基金会）简介
CNCF（Cloud Native Computing Foundation，云原生计算基金会），2015 年由谷歌牵头成立，目前已有一百多个企业与机构作为成员，包括亚马逊、微软、思科、红帽等巨头。CNCF 致力于培育和维护一个厂商中立的开源社区生态，用以推广云原生技术。

CNCF 目前托管了非常多的开源项目，其中有很多我们耳熟能详的项目，例如 Kubernetes、Prometheus、Envoy、Istio、etcd 等。更多的项目，你可以参考 CNCF 公布的Cloud Native Landscape，它给出了云原生生态的参考体系，如下图所示：

![img](https://static001.geekbang.org/resource/image/5e/32/5ef7aa9e514ac27b6474165c745dfe32.jpg?wh=2248x1282)


### 什么是云原生？
CNCF 官方在 2018 年发布了云原生 v1.0，并给出了定义：

“云原生技术有利于各组织在公有云、私有云和混合云等新型动态环境中，构建和运行可弹性扩展的应用。云原生的代表技术包括容器、服务网格、微服务、不可变基础设施和声明式 API。 这些技术能够构建容错性好、易于管理和便于观察的松耦合系统。结合可靠的自动化手段，云原生技术使工程师能够轻松地对系统作出频繁和可预测的重大变更。”

简单点说，云原生（Cloud Native）是一种构建和运行应用程序的方法，是一套技术体系和方法论。云原生中包含了 3 个概念，分别是技术体系、方法论和云原生应用。整个云原生技术栈是围绕着 Kubernetes 来构建的，具体包括了以下核心技术栈：

![img](https://static001.geekbang.org/resource/image/8f/66/8fc9dc16d99e7f2949813fe109986266.jpg?wh=2248x911)

这里来介绍下这些核心技术栈的基本内容。
- 容器：Kubernetes 的底层计算引擎，提供容器化的计算资源。
- 微服务：一种软件架构思想，用来构建云原生应用。
- 服务网格：建立在 Kubernetes 之上，作为服务间通信的底座，提供强大的服务治理功能。
- 声明式 API ：一种新的软件开发模式，通过描述期望的应用状态，来使系统更加健壮。
- 不可变基础设施：一种新的软件部署模式，应用实例一旦被创建，便只能重建不能更新，是现代运维的基础。

在 43 讲 和 44 讲 中，我介绍了容器、服务网格和微服务，这里再补充介绍下不可变基础设施和声明式 API。

不可变基础设施（Immutable Infrastructure）的构想，是由 Chad Fowler 于 2013 年提出的。具体来说就是：一个应用程序的实例，一旦被创建，就会进入只读的状态，后面如果想变更这个应用程序的实例，只能重新创建一个新的实例。通过这种模式，可以确保应用程序实例的一致性，这使得落地 DevOps 更加容易，并可以有效减少运维人员管理配置的负担。

声明式 API 是指我们通过工具描述期望的应用状态，并由工具保障应用一直处在我们期望的状态。

Kubernetes 的 API 设计，就是一种典型的声明式 API。例如，我们在创建 Deployment 时，在 Kubernetes YAML 文件中声明应用的副本数为2，即设置replicas: 2，Deployment Controller 就会确保应用的副本数一直为2。也就是说，如果当前副本数大于2，Deployment Controller 会删除多余的副本；如果当前副本数小于2，会创建新的副本。

声明式设计是一种设计理念，同时也是一种工作模式，它使得你的系统更加健壮。分布式系统环境可能会出现各种不确定的故障，面对这些组件故障，如果使用声明式 API ，你只需要查看对应组件的 API 服务器状态，再确定需要执行的操作即可。


### 什么是云原生应用？
上面，我介绍了什么是云原生，接下来再介绍下什么是云原生应用。

整体来看，云原生应用是指生而为云的应用，应用程序从设计之初就考虑到了云的环境，可以在云上以最佳姿势运行，充分利用和发挥云平台提供的各种能力。具体来看，云原生应用具有以下三大特点：
- 从应用生命周期管理维度来看，使用 DevOps 和 CI/CD 的方式，进行开发和交付。
- 从应用维度来看，以微服务原则进行划分设计。
- 从系统资源维度来看，采用 Docker + Kubernetes 的方式来部署。

看完上面的介绍，你应该已经对云原生和云原生应用有了一定的理解，接下来我就介绍一种云原生架构实现。因为云原生内容很多，所以这里的介绍只是起到抛砖引玉的作用，让你对云原生架构有初步的理解。至于在具体业务中如何设计云原生架构，你还需要根据业务、团队和技术栈等因素综合考虑。

### 云原生架构包含很多内容，如何学习？
云原生架构中包含了很多概念、技术，那么我们到底如何学习呢？在前面的两讲中，我分别从系统资源层、应用层、应用生命周期管理层介绍了云技术。这 3 个层次基本上构成了整个云计算的技术栈。

今天，我仍然会从这三个层次入手，来对整个云原生架构设计进行相对完整的介绍。每个层次涉及到的技术很多，这一讲我只介绍每一层的核心技术，通过这些核心技术来看每一层的构建方法。

另外，因为应用生命周期管理层涉及到的技术栈非常多，所以今天不会详细讲解每种生命周期管理技术的实现原理，但会介绍它们提供的能力。

除了功能层面的架构设计之外，我们还要考虑部署层面的架构设计。对于云原生架构的部署，通常我们需要关注以下两点：
- 容灾能力：容灾能力是指应用程序遇到故障时的恢复能力。在互联网时代，对应用的容灾能力有比较高的要求。理想情况是系统在出现故障时，能够无缝切换到另外一个可用的实例上，继续提供服务，并做到用户无感知。但在实际开发中，无缝切换在技术上比较难以实现，所以也可以退而求其次，允许系统在一定时间内不可用。通常这个时间需要控制在秒级，例如 5s。容灾能力可以通过负载均衡、健康检查来实现。
- 扩缩容能力：扩缩容能力指的是系统能够根据需要扩缩容，可以手动扩缩容，也可以自动扩缩容。互联网时代对扩缩容能力的要求也比较高，需要实现自动扩缩容。我们可以基于一些自定义指标，例如 CPU 使用率、内存使用率等来自动扩缩容。扩容也意味着能够承载更多的请求，提高系统的吞吐量；缩容，意味着能够节省成本。扩缩容能力的实现，需要借助于负载均衡和监控告警能力。

容灾能力和扩缩容能力都属于高可用能力。也就是说，在部署层面，需要我们的架构具备高可用能力。

接下来，我就重点介绍下系统资源层和应用层的云原生架构设计，并简单介绍下应用生命周期管理层的核心功能构建。在介绍完架构设计之后，我还会介绍下这些层面的高可用架构设计。


### 系统资源层的云原生架构设计
先来看系统资源层面的云原生架构设计。对于一个系统来说，系统资源的架构是需要优先考虑的。在云原生架构中，当前的业界标准是通过 Docker 提供系统资源（例如 CPU、内存等），通过 Kubernetes 来编排 Docker 容器。Docker 和 Kubernetes 的架构，我在43 讲中介绍过，这里我主要介绍下系统资源层面的高可用架构设计。

基于 Docker+Kubernetes 的方案，高可用架构是通过 Kubernetes 高可用架构来实现的。要实现整个 Kubernetes 集群的高可用，我们需要分别实现以下两类高可用：
- Kubernetes 集群的高可用。
- Kubernetes 集群中所部署应用的高可用。

我们来分别看下这两个高可用方案。

#### Kubernetes 集群高可用方案设计
通过43 讲的学习，我们知道 Kubernetes 由 kube-apiserver、kube-controller-manager、kube-scheduler、cloud-controller-manager、etcd、kubelet、kube-proxy、container runtime 8 大核心组件组成。

其中，kube-apiserver、kube-controller-manager、kube-scheduler、cloud-controller-manager、etcd 通常部署在 master 节点，kubelet、kube-proxy、container runtime 部署在 Node 节点上。实现 Kubernetes 集群的高可用，需要分别实现这 8 大核心组件的高可用。

Kubernetes 集群的高可用架构图如下：

![img](https://static001.geekbang.org/resource/image/76/3f/7667b9f5a88e61487fce2374400d823f.jpg?wh=2248x1466)

上面图片展示的方案中，所有管理节点都部署了 kube-apiserver、kube-controller-manager、kube-scheduler、etcd 等组件。kube-apiserver 均与本地的 etcd 进行通信，etcd 在三个节点间同步数据；而 kube-controller-manager、kube-scheduler 和 cloud-controller-manager，也只与本地的 kube-apiserver 进行通信，或者通过负载均衡访问。

一个 Kubernetes 集群中有多个 Node 节点，当一个 Node 节点故障时，Kubernetes 的调度组件 kube-controller-manager 会将 Pod 调度到其他节点上，并将故障节点的 Pod 在其他可用节点上重建。也就是说，只要集群中有两个以上的节点，当其中一个 Node 故障时，整个集群仍然能够正常提供服务。换句话说，集群的 kubelet、kube-proxy、container runtime 组件可以是单点的，不用实现这些组件的高可用。

接下来，我们来看下 Master 节点各组件是如何实现高可用的。**先来说下 kube-apiserver 组件的高可用方案设计**。因为 kube-apiserver 是一个无状态的服务，所以可以通过部署多个 kube-apiserver 实例，其上挂载负载均衡的方式来实现。其他所有需要访问 kube-apiserver 的组件，都通过负载均衡来访问，以此实现 kube-apiserver 的高可用。

kube-controller-manager、cloud-controller-manager 和 kube-scheduler 因为是有状态的服务，所以它们的高可用能力不能通过负载均衡来实现。kube-controller-manager/kube-scheduler/cloud-controller-manager 通过–leader-elect=true 参数开启分布式锁机制，来进行 leader election。

你可以创建多个 kube-controller-manager/kube-scheduler/cloud-controller-manager 实例，同一时刻只有一个实例能够获取到锁，成为 leader，提供服务。如果当前 leader 故障，其他实例感知到 leader 故障之后会自动抢锁，成为 leader 继续提供服务。通过这种方式，我们实现了 kube-controller-manager/kube-scheduler/cloud-controller-manager 组件的高可用。

当 kube-apiserver、kube-controller-manager、kube-scheduler、cloud-controller-manager 故障时，我们期望这些组件能够自动恢复，这时候可以将这些组件以 Static Pod 的方式进行部署，这样当 Pod 故障时，上述实例就能够自动被拉起。


etcd 的高可用方案有下面这 3 种思路：
- 使用独立的 etcd 集群，独立的 etcd 集群自带高可用能力。
- 在每个 Master 节点上，使用 Static Pod 来部署 etcd，多个节点上的 etcd 实例数据相互同步。每个 kube-apiserver 只与本 Master 节点的 etcd 通信。
- 使用 CoreOS 提出的 self-hosted 方案，将 etcd 集群部署在 kubernetes 集群中，通过 kubernetes 集群自身的容灾能力来实现 etcd 的高可用。

这三种思路，需要你根据实际需要进行选择，在实际生产环境中，第二种思路用得最多。到这里，我们就实现了整个 Kubernetes 集群的高可用。接下来，我们来看下 Kubernetes 集群中，应用的高可用是如何实现的。


#### Kubernetes 应用的高可用
Kubernetes 自带了应用高可用能力。在 Kubernetes 中，应用以 Pod 的形式运行。你可以通过 Deployment/StatefulSet 来创建应用，并在 Deployment/StatefulSet 中指定多副本和 Pod 的健康检查方式。当 Pod 健康检查失败时，Deployment/StatefulSet 的控制器（ReplicaSet）会自动销毁故障 Pod，并创建一个新的 Pod，替换故障的 Pod。

你可能会问：当 Pod 故障时，怎么才能避免请求被调度到已故障的 Pod 上，造成请求失败？这里我也详细介绍下。

在 Kubernetes 中，我们可以通过 Kubernetes Service 或者负载均衡来访问这些 Pod。当通过负载均衡来访问 Pod 时，负载均衡后端的 RS（Real Server）实例其实就是 Pod。我们创建了多个 Pod，负载均衡可以自动根据 Pod 的健康状况来进行负载。


接下来，我们主要看下这个问题：**当通过 Kubernetes Service 访问 Pod 时，如何实现高可用？**

高可用原理如下图所示：

![img](https://static001.geekbang.org/resource/image/65/cd/65dab9a3f07d5afed29aa616db14bdcd.jpg?wh=2248x1266)


在 Kubernetes 中，我们可以给每个 Pod 打上标签（Label），标签是一个 key-value 对，例如label: app=Nginx。当我们访问 Service 时，Service 会根据它配置的 Label Selector，匹配具有相同 Label 的 Pod，并将这些 Pod 的 endpoint 地址作为其后端 RS。

举个例子，你可以看看上面的图片：Service 的 Label Selector 是 Labelsapp=Nginx，这样就会选择我们创建的具有label: app=Nginx的 3 个 Pod 实例。这时候，Service 会根据其负载均衡策略，选取一个 Pod 将请求流量转发过去。当其中一个 Pod 故障时，Kubernetes 会自动将故障 Pod 的 endpoint 从 Service 后端对应的 RS 列表中剔除。

由 Deployment 创建的 ReplicaSet，这时候也会发现有一个 Pod 故障，健康的 Pod 实例数变为2，这时候跟其期望的值3不匹配，就会自动创建一个新的健康 Pod，替换掉故障的 Pod。因为新 Pod 满足 Service 的 Label Selector，所以新 Pod 的 endpoint 会被 Kubernetes 自动添加到 Service 对应的 endpoint 列表中。

通过上面这些操作，Service 后端的 RS 中，故障的 Pod IP 被新的、健康的 Pod IP 所替换，通过 Service 访问的后端 Pod 就都是健康的。这样，就通过 Service 实现了应用的高可用。

从上面的原理分析中，我们也可以发现，Service 本质上是一个负载均衡器。

Kubernetes 还提供了滚动更新（RollingUpdate）机制，来确保在发布时服务正常可用。这个机制的大致原理是：在更新时，先创建一个 Pod，再销毁一个 Pod，依次循环，直到所有的 Pod 都更新完成。在更新时，我们还可以控制每次更新的 Pod 数，以及最小可用的 Pod 数。

接下来，我们再来看下应用层的云原生架构设计和高可用设计。

### 应用层的云原生架构设计
在云原生架构中，我们采用微服务架构来构建应用。所以，这里我主要围绕着微服务架构的构建方式来介绍。先和你谈谈我对微服务的理解。

从本质上来说，微服务是一个轻量级的 Web 服务，只不过在微服务场景下，我们通常考虑的不是单个微服务，而是更多地考虑由多个微服务组成的应用。也就是说，一个应用由多个微服务组成，多个微服务就带来了一些单个 Web 服务不会面临的问题，例如部署复杂、排障困难、服务依赖复杂、通信链路长，等等。在微服务场景下，除了编写单个微服务（轻量级的 Web 服务）之外，我们更多是要专注于解决应用微服务化所带来的挑战。所以，在我看来，微服务架构设计包括两个重要内容：


单个微服务的构建方式；解决应用微服务化带来的挑战。


#### 微服务实现
我们可以通过两种方式来构建微服务：
- 采用 Gin、Echo 等轻量级 Web 框架。
- 采用微服务框架，例如 go-chassis、go-micro、go-kit等。

如果要解决应用微服务化带来的挑战，我们需要采用多种技术和手段，每种技术和手段会解决一个或一部分挑战。

综上，在我看来，微服务本质上是一个轻量级的 Web 服务，但又包含一系列的技术和手段，用来解决应用微服务化带来的挑战。微服务的技术栈如下图所示：

![img](https://static001.geekbang.org/resource/image/7a/65/7a860825662bb93f93c51c00efbdeb65.jpg?wh=2248x1158)


不同的技术栈可以由不同的方式来实现，并解决不同的问题：
- 监控告警、日志、CI/CD、分布式调度，可以由 Kubernetes 平台提供的能力来实现。
- 服务网关、权限验证、负载均衡、限流 / 熔断 / 降级，可以由网关来实现，例如 Tyk 网关。
- 进程间通信、REST/RPC 序列化，可以借助 Web 框架来实现，例如 Gin、Go Chassis、gRPC、Sprint Cloud。
- 分布式追踪可以由 Jaeger 来实现。
- 统一配置管理可以由 Apollo 配置中心来实现。
- 消息队列可以由 NSQ、Kafka、RabbitMQ 来实现。

上面的服务注册 / 服务发现，有 3 种实现方式：
- 通过 Kubernetes Service 来进行服务注册 / 服务发现，Kubernetes 自带服务注册 / 服务发现功能。使用此方式，我们不需要额外的开发。
- 通过服务中心来实现服务注册 / 服务发现功能。采用这种方式，需要我们开发并部署服务中心，服务中心通常可以使用 etcd/consul/mgmet 来实现，使用 etcd 的较多。
- 通过网关，来进行服务注册 / 服务发现。这种情况下，可以将服务信息直接上报给网关服务，也可以将服务信息上报到一个服务中心，例如 etcd 中，再由网关从服务中心中获取。

这里要注意，原生的 Kubernetes 集群是不支持监控告警、日志、CI/CD 等功能的。我们在使用 Kubernetes 集群时，通常会使用一个基于 Kubernetes 开发而来的 Kubernetes 平台，例如腾讯云容器服务 TKE。

在 Kubernetes 平台中，通常会基于一些优秀的开源项目，进行二次开发，来实现平台的监控告警、日志、CI/CD 等功能。

- 监控告警：基于 Prometheus 来实现。
- 日志：基于 EFK 日志解决方案来实现。
- CI/CD：可以自己研发，也可以基于优秀的开源项目来实现，例如 drone。

#### 微服务架构设计
上面我介绍了如何实现微服务，这里我再来具体讲讲，上面提到的各个组件 / 功能是如何有机组合在一起，共同构建一个微服务应用的。下面是微服务的架构图：

![img](https://static001.geekbang.org/resource/image/02/yy/020fa7eccc352d03b78b57a1dbfc75yy.jpg?wh=2248x2168)

在上图中，我们将微服务应用层部署在 Kubernetes 集群中，在 Kubernetes 集群之上，可以构建微服务需要的其他功能，例如监控告警、CI/CD、日志、调用链等。这些功能共同完成应用的生命周期管理。

我们在微服务的最上面挂载负载均衡。客户端，例如移动端应用、Web 应用、API 调用等，都通过负载均衡来访问微服务。

微服务在启动时会将自己的 endpoint 信息（通常是ip:port格式）上报到服务中心。微服务也会定时上报自己的心跳到服务中心。在服务中心中，我们可以监控微服务的状态，剔除不健康的微服务，获取微服务之间的访问数据，等等。如果要通过网关调用微服务，或者需要使用网关做负载均衡，那我们还需要网关从服务中心中获取微服务的 endpoint 信息。


#### 微服务高可用架构设计
我们再来看下如何设计微服务应用的高可用能力。

我们可以把所有微服务组件以 Deployment/StatefulSet 的形式部署在 Kubernetes 集群中，副本数至少设置为两个，更新方式为滚动更新，设置服务的监控检查，并通过 Kubernetes Service 或者负载均衡的方式访问服务。这样，我们就可以不用做任何改造，直接使用 Kubernetes 自有的容灾能力，实现微服务架构的高可用。

### 云原生架构鸟瞰图
上面，我介绍了系统资源层和应用层的云原生架构设计，但还不能构成整个云原生架构设计。这里，我通过一张云原生架构鸟瞰图，来整体介绍下云原生架构的设计方案。

![img](https://static001.geekbang.org/resource/image/05/b5/05c7cb8fd6c524242229ab9ea6c9b1b5.jpg?wh=2248x1470)

上图的云原生架构分为 4 层，除了前面提到的系统资源层、应用层、应用生命周期管理层之外，又加了统一接入层。接下来，我来介绍下这些层在云原生架构中的作用。

在最下面的系统资源层，我们除了提供传统的计算资源（物理机、虚拟机）之外，还可以提供容器化的计算资源和高可用的存储服务。其中，容器化的计算资源是基于传统的物理机 / 虚拟机来构建的。

在云原生架构中，我们更应该使用容器化的计算资源，通过 Docker 容器技术来隔离并对外提供计算资源，通过 Kubernetes 来编排容器。Docker + Kubernetes 的组合使用，可以构建出一个非常优秀的系统资源层。这个系统资源层，自带了资源管理、容器调度、自动伸缩、网络通信、服务发现、健康检查等企业应用需要的核心能力。

在云原生时代，这些系统资源除了具有容器化、轻量化的特点之外，还越来越倾向于朝着 Serverless 化的方向去构建：系统资源免申请、免运维，按需计费，具备极致的弹性伸缩能力，并能够缩容到 0。Serverless 化的系统资源，可以使开发者只聚焦在应用层的应用功能开发上，而不用再把时间浪费在系统层的运维工作上。

在系统资源层之上，就可以构建我们的应用层了。云原生架构中，应用的构建方式，基本上都是采用的微服务架构。开发一个微服务应用，我们可以使用微服务框架，也可以不使用。二者的区别是，微服务框架替我们完成了服务治理相关功能，让我们不需要再开发这些功能。

在我看来，这一点有利有弊。好处当然是节省了开发工作量。至于坏处，主要有两方面：一方面，在实现方式和实现思路上，微服务框架所集成的服务治理功能并不一定是最适合我们的方案。另一方面，使用微服务框架还意味着我们的应用会跟微服务框架耦合，不能自由选择服务治理技术和方式。所以，在实际开发中，你应该根据需要，自行选择微服务的构建方式。

一般来说，一个微服务框架中，至少集成了这些服务治理功能：配置中心、调用链追踪、日志系统、监控、服务注册 / 服务发现。

再往上，我们就实现了统一接入层。统一接入层中包含了负载均衡和网关两个组件，其中负载均衡作为服务的唯一入口，供 API、Web 浏览器、手机终端等客户端访问。通过负载均衡，可以使我们的应用在故障时，能够自动切换实例，在负载过高时能够水平扩容。负载均衡下面还对接了网关，网关提供了一些通用能力，例如安全策略、路由策略、流量策略、统计分析、API 管理等能力。

最后，我们还可以构建一系列的应用生命周期管理技术，例如服务编排、配置管理、日志、存储、审计、监控告警、消息队列、分布式链路追踪。这些技术中，一些可以基于 Kubernetes，集成在我们的 Kubernetes 平台中，另一些则可以单独构建，供所有产品接入。

### 公有云版云原生架构
上面我们提到，云原生架构涉及到很多的技术栈。如果公司有能力，可以选择自己开发；如果觉得人力不够、成本太高，也可以使用公有云厂商已经开发好的云原生基础设施。使用云厂商的云原生基础设施，好处很明显：这些基础设施专业、稳定、免开发、免运维。

为了补全云原生架构设计版图，这里我也介绍一个公用云版的云原生架构设计。那么，公有云厂商会提供哪些云原生基础设施呢？这里我介绍下腾讯云提供的云原生解决方案。解决方案全景如下图所示：

![img](https://static001.geekbang.org/resource/image/44/20/4473c11e51fa0976d91202fc7c836020.jpg?wh=2249x1068)

可以看到，**腾讯云提供了全栈的云原生能力**。腾讯云基于底层的云原生能力，提供了一系列的云原生解决方案。这些解决方案，是已经设计好的云原生架构构建方案，可以帮助企业快速落地云原生架构，例如混合云解决方案、AI 解决方案、IoT 解决方案等。


那么，腾讯云底层提供了哪些云原生能力呢？我们一起来看下。在应用层，通过 TSF 微服务平台，我们可以实现微服务的构建，以及微服务的服务治理能力。另外，还提供了更多的应用构建架构，例如：

- Serverless Framework，可以构建 Serverless 应用。
- CloudBase，云原生一体化应用开发平台，可以快速构建小程序、Web、移动应用。
- …

在系统资源层，腾讯云提供了多种计算资源提供形态。例如：通过 TKE，可以创建原生的 Kubernetes 集群；通过 EKS，可以创建 Serverless 化的 Kubernetes 集群；通过TKE-Edge，可以创建能够纳管边缘节点的 Kubernetes 集群。此外，还提供了开源容器服务平台TKEStack，TKEStack 是一个非常优秀的容器云平台，在代码质量、稳定性、平台功能等方面，都在开源的容器云平台中处于龙头地位，也欢迎你 Star。

在应用生命周期管理这一层，提供了云原生的 etcd、Prometheus 服务。此外，还提供了 CLS 日志系统，供你保存并查询应用日志；提供了云监控，供你监控自己的应用程序；提供了容器镜像服务（TCR），用来保存 Docker 镜像；提供了 CODING DevOps 平台，用来支持应用的 CI/CD；提供了调用链跟踪服务（TDW），用来展示微服务的调用链。

在统一接入层，腾讯云提供了功能强大的 API 网关。此外，还提供了多种 Serverless 化的中间件服务，例如消息队列 TDMQ、云原生数据库 TDSQL 等。所有这些云原生基础设施，都有共同的特点，就是免部署、免运维。换句话说，在腾讯云，你可以只专注于使用编程语言编写你的业务逻辑，其他的一切都交给腾讯云来搞定。


### 总结
云原生架构设计，包含了系统资源层、应用层、统一接入层和应用生命周期管理层 4 层。

在系统资源层，可以采用 Docker + Kubernetes 的方式来提供计算资源。我们所有的应用和应用生命周期管理相关的服务，都可以部署在 Kubernetes 集群中，利用 Kubernetes 集群的能力实现服务发现 / 服务注册、弹性伸缩、资源调度等核心能力。

在应用层，可以采用微服务架构，来构建我们的应用。具体构建时，我们可以根据需要，采用类似 Gin 这种轻量级的 Web 框架来构建应用，然后再实现旁路的服务治理功能；也可以采用集成了很多服务治理功能的微服务框架，例如 go-chassis、go-micro 等。

因为我们采用了微服务架构，为了能够将微服务的一些功能，例如：认证授权、限流等功能最大化的复用，我们又提供了统一接入层。可以通过 API 网关、负载均衡、服务网格等技术来构建统一接入层。

在应用生命周期管理这一层，我们可以实现一些云原生的管理平台，例如 DevOps、监控告警、日志、配置中心等，并使我们的应用以云原生化的方式接入这些平台，使用这些平台提供的能力。

最后，我还介绍了腾讯云的云原生基础设施。通过腾讯云提供的云原生能力，你可以专注于使用编程语言编写你的业务逻辑，其他的各种云原生能力，都可以交给云厂商来帮你实现。

## 46 | 如何制作Docker镜像？
要落地云原生架构，其中的一个核心点是通过容器来部署我们的应用。如果要使用容器来部署应用，那么制作应用的 Docker 镜像就是我们绕不开的关键一步。今天，我就来详细介绍下如何制作 Docker 镜像。

在这一讲中，我会先讲解下 Docker 镜像的构建原理和方式，然后介绍 Dockerfile 的指令，以及如何编写 Dockerfile 文件。最后，介绍下编写 Dockerfile 文件时要遵循的一些最佳实践。

### Docker 镜像的构建原理和方式
首先，我们来看下 Docker 镜像构建的原理和方式。我们可以用多种方式来构建一个 Docker 镜像，最常用的有两种：
- 通过docker commit命令，基于一个已存在的容器构建出镜像。
- 编写 Dockerfile 文件，并使用docker build命令来构建镜像。


上面这两种方法中，镜像构建的底层原理是相同的，**都是通过下面 3 个步骤来构建镜像：**
- 基于原镜像，启动一个 Docker 容器。
- 在容器中进行一些操作，例如执行命令、安装文件等。由这些操作产生的文件变更都会被记录在容器的存储层中。
- 将容器存储层的变更 commit 到新的镜像层中，并添加到原镜像上。

下面，我们来具体讲解这两种构建 Docker 镜像的方式。

#### 通过docker commit命令构建镜像
我们可以通过docker commit来构建一个镜像，命令的格式为docker commit [选项] [<仓库名>[:<标签>]]。

下图中，我们通过 4 个步骤构建了 Docker 镜像ccr.ccs.tencentyun.com/marmotedu/iam-apiserver-amd64:test：

![img](https://static001.geekbang.org/resource/image/23/11/23619b513ff043792c7374acf7781d11.png?wh=1920x344)


具体步骤如下：
- 执行docker ps获取需要构建镜像的容器 ID 48d1dbb89a7f。
- 执行docker pause 48d1dbb89a7f暂停48d1dbb89a7f容器的运行。
- 执行docker commit 48d1dbb89a7f ccr.ccs.tencentyun.com/marmotedu/iam-apiserver-amd64:test，基于容器 ID 48d1dbb89a7f构建 Docker 镜像。
- 执行docker images ccr.ccs.tencentyun.com/marmotedu/iam-apiserver-amd64:test，查看镜像是否成功构建。

这种镜像构建方式通常用在下面两个场景中：
- 构建临时的测试镜像；
- 容器被入侵后，使用docker commit，基于被入侵的容器构建镜像，从而保留现场，方便以后追溯。


除了这两种场景，我不建议你使用docker commit来构建生产现网环境的镜像。我这么说的主要原因有两个：
- 使用docker commit构建的镜像包含了编译构建、安装软件，以及程序运行产生的大量无用文件，这会导致镜像体积很大，非常臃肿。
- 使用docker commit构建的镜像会丢失掉所有对该镜像的操作历史，无法还原镜像的构建过程，不利于镜像的维护。

下面，我们再来看看如何使用Dockerfile来构建镜像。

#### 通过Dockerfile来构建镜像
在实际开发中，使用Dockerfile来构建是最常用，也最标准的镜像构建方法。Dockerfile是 Docker 用来构建镜像的文本文件，里面包含了一系列用来构建镜像的指令。

docker build命令会读取Dockerfile的内容，并将Dockerfile的内容发送给 Docker 引擎，最终 Docker 引擎会解析Dockerfile中的每一条指令，构建出需要的镜像。

docker build的命令格式为docker build [OPTIONS] PATH | URL | -。PATH、URL、-指出了构建镜像的上下文（context），context 中包含了构建镜像需要的Dockerfile文件和其他文件。默认情况下，Docker 构建引擎会查找 context 中名为Dockerfile的文件，但你可以通过-f, --file选项，手动指定Dockerfile文件。例如：


```
 $ docker build -f Dockerfile -t ccr.ccs.tencentyun.com/marmotedu/iam-apiserver-amd64:test .
```
使用 Dockerfile 构建镜像，本质上也是通过镜像创建容器，并在容器中执行相应的指令，然后停止容器，提交存储层的文件变更。和用docker commit构建镜像的方式相比，它有**三个好处：**

- Dockerfile 包含了镜像制作的完整操作流程，其他开发者可以通过 Dockerfile 了解并复现制作过程。
- **Dockerfile 中的每一条指令都会创建新的镜像层，这些镜像可以被 Docker Daemnon 缓存。再次制作镜像时，Docker 会尽量复用缓存的镜像层（using cache），而不是重新逐层构建，这样可以节省时间和磁盘空间。**
- Dockerfile 的操作流程可以通过docker image history [镜像名称]查询，方便开发者查看变更记录。

这里，我们通过一个示例，来详细介绍下通过Dockerfile构建镜像的流程。

首先，我们需要编写一个Dockerfile文件。下面是 iam-apiserver 的Dockerfile文件内容：

```dockerfile
FROM centos:centos8
LABEL maintainer="<colin404@foxmail.com>"

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo "Asia/Shanghai" > /etc/timezone

WORKDIR /opt/iam
COPY iam-apiserver /opt/iam/bin/

ENTRYPOINT ["/opt/iam/bin/iam-apiserver"]
```
这里选择centos:centos8作为基础镜像，是因为centos:centos8镜像中包含了基本的排障工具，例如vi、cat、curl、mkdir、cp等工具。

接着，执行docker build命令来构建镜像：

```
$ docker build -f Dockerfile -t ccr.ccs.tencentyun.com/marmotedu/iam-apiserver-amd64:test .
```
执行docker build后的构建流程为：

第一步，docker build会将 context 中的文件打包传给 Docker daemon。如果 context 中有.dockerignore文件，则会从上传列表中删除满足.dockerignore规则的文件。这里有个例外，如果.dockerignore文件中有.dockerignore或者Dockerfile，docker build命令在排除文件时会忽略掉这两个文件。如果指定了镜像的 tag，还会对 repository 和 tag 进行验证。

第二步，docker build命令向 Docker server 发送 HTTP 请求，请求 Docker server 构建镜像，请求中包含了需要的 context 信息。

第三步，Docker server 接收到构建请求之后，会执行以下流程来构建镜像：
1. 创建一个临时目录，并将 context 中的文件解压到该目录下。
2. 读取并解析 Dockerfile，遍历其中的指令，根据命令类型分发到不同的模块去执行。
3. **Docker 构建引擎为每一条指令创建一个临时容器**，在临时容器中执行指令，然后 commit 容器，生成一个新的镜像层。
4. 最后，**将所有指令构建出的镜像层合并**，形成 build 的最后结果。**最后一次 commit 生成的镜像 ID 就是最终的镜像 ID**。

为了提高构建效率，docker build默认会缓存已有的镜像层。如果构建镜像时发现某个镜像层已经被缓存，就会直接使用该缓存镜像，而不用重新构建。如果不希望使用缓存的镜像，可以在执行docker build命令时，指定--no-cache=true参数。

Docker 匹配缓存镜像的规则为：遍历缓存中的基础镜像及其子镜像，**检查这些镜像的构建指令是否和当前指令完全一致**，如果不一样，则说明缓存不匹配。对于ADD、COPY指令，还会根据文件的校验和（checksum）来判断添加到镜像中的文件是否相同，如果不相同，则说明缓存不匹配。

这里要**注意**，缓存匹配检查不会检查容器中的文件。比如，当使用RUN apt-get -y update命令更新了容器中的文件时，缓存策略并不会检查这些文件，来判断缓存是否匹配。

最后，我们可以通过docker history命令来查看镜像的构建历史，如下图所示：

![img](https://static001.geekbang.org/resource/image/f3/06/f32a0e2f90a36995a561747519897806.png?wh=1854x345)


#### 其他制作镜像方式
上面介绍的是两种最常用的镜像构建方式，还有一些其他的镜像创建方式，这里我简单介绍两种。

通过docker save和docker load命令构建

docker save用来将镜像保存为一个 tar 文件，docker load用来将 tar 格式的镜像文件加载到当前机器上，例如：

```
# 在 A 机器上执行，并将 nginx-v1.0.0.tar.gz 复制到 B 机器
$ docker save nginx | gzip > nginx-v1.0.0.tar.gz

# 在 B 机器上执行
$ docker load -i nginx-v1.0.0.tar.gz
```

通过上面的命令，我们就在机器 B 上创建了nginx镜像。


通过docker export和docker import命令构建

我们先通过docker export 保存镜像，再通过docker import 加载镜像，具体命令如下：

```
# 在 A 机器上执行，并将 nginx-v1.0.0.tar.gz 复制到 B 机器
$ docker export nginx > nginx-v1.0.0.tar.gz

# 在 B 机器上执行
$ docker import - nginx:v1.0.0 nginx-v1.0.0.tar.gz
```
通过docker export导出的镜像和通过docker save保存的镜像相比，会丢失掉所有的镜像构建历史。在实际生产环境中，我不建议你通过docker save和docker export这两种方式来创建镜像。我比较推荐的方式是：在 A 机器上将镜像 push 到镜像仓库，在 B 机器上从镜像仓库 pull 该镜像。


### Dockerfile 指令介绍
上面，我介绍了一些与 Docker 镜像构建有关的基础知识。在实际生产环境中，我们标准的做法是通过 Dockerfile 来构建镜像，这就要求你会编写 Dockerfile 文件。接下来，我就详细介绍下如何编写 Dockerfile 文件。

Dockerfile 指令的基本格式如下：


```
# Comment
INSTRUCTION arguments
```
INSTRUCTION是指令，不区分大小写，但我的建议是指令都大写，这样可以与参数进行区分。Dockerfile 中，以 # 开头的行是注释，而在其他位置出现的 # 会被当成参数，例如：

```
# Comment
RUN echo 'hello world # dockerfile'
```

一个 Dockerfile 文件中包含了多条指令，这些指令可以分为 5 类。
- 定义基础镜像的指令：**FROM**；
- 定义镜像维护者的指令：**MAINTAINER**（可选）；
- 定义镜像构建过程的指令：**COPY**、ADD、**RUN**、USER、**WORKDIR**、ARG、**ENV**、VOLUME、**ONBUILD**；
- 定义容器启动时执行命令的指令：**CMD**、**ENTRYPOINT**；
- 其他指令：EXPOSE、HEALTHCHECK、STOPSIGNAL。

其中，加粗的指令是编写 Dockerfile 时经常用到的指令，需要你重点了解下。我把这些常用 Dockerfile 指令的介绍放在了 GitHub 上，你可以看看这个[Dockerfile 指令详解](https://github.com/marmotedu/geekbang-go/blob/master/Dockerfile%E6%8C%87%E4%BB%A4%E8%AF%A6%E8%A7%A3.md)。

下面是一个 Dockerfile 示例：

```
# 第一行必须指定构建该镜像所基于的容器镜像
FROM centos:centos8

# 维护者信息
MAINTAINER Lingfei Kong <colin404@foxmail.com>

# 镜像的操作指令
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo "Asia/Shanghai" > /etc/timezone
WORKDIR /opt/iam
COPY iam-apiserver /opt/iam/bin/

# 容器启动时执行指令
ENTRYPOINT ["/opt/iam/bin/iam-apiserver"]
```

Docker 会顺序解释并执行 Dockerfile 中的指令，并且第一条指令必须是FROM，FROM 用来指定构建镜像的基础镜像。接下来，一般会指定镜像维护者的信息。后面是镜像操作的指令，最后会通过CMD或者ENTRYPOINT来指定容器启动的命令和参数。

### Dockerfile指令详解

Dockerfile中包含了大量的指令，这些指令完成的功能，使用的格式都不同。这里，我详细介绍下这些指令。

1) `FROM`

格式：`FROM <image>`或`FROM <image>:<tag>`

`FROM`指令的功能是为后面的指令提供基础镜像，因此，Dockerfile必须以`FROM`指令作为第一条非注释指令。我们可以根据需要，选择任何有效的镜像作为基础镜像。

可以在一个Dockerfile文件中，使用多个 `FROM` 指令，来构建多个镜像。每个镜像构建完成之后，Docker会打印出该镜像的ID。如果 `FROM` 指令中没有指定镜像的tag，则默认tag是 `latest` 。如果镜像不存在，则报错退出。

2) `MAINTAINER`

格式：`MAINTAINER <name> <Email>`

用来指定维护该Dockerfile的作者信息。

3) `ENV`

`ENV`指令有两种格式：

* `ENV <key> <value>`
* `ENV <key1>=<value1> <key2>=<value2>`

`ENV`指令用来在镜像创建出来的容器中声明环境变量，被声明的环境变量可被后面的特定指令，例如：`ENV`、`ADD`、`COPY`、`WORKDIR`、`EXPOSE`、`VOLUME`、`USER`使用。其他指令引用格式为：`$variableName`或`${variableName}`。可以使用斜杠 `\` 来转义环境变量，例如：`\$test`或者`\${test}`，这样二者将会被分别转换为`$test`和`${test}`字符串，而不是环境变量所保存的值。这里要注意，`ONBUILD`指令不支持环境替换。

4) `WORKDIR`

格式：`WORKDIR <工作目录路径>`

该指令用于指定当前的工作目录，使用该命令后，接下来每一层的工作目录都会切换到指定的目录（除非重新使用 `WORKDIR` 切换）。

`WORKDIR` 的路径始终使用绝对路径。这可以保证指令的准确和可靠。 同时，使用 `WORKDIR` 来替代`RUN cd ... && do-something` 这样难以维护的指令。

5) `COPY`

格式：`COPY <src> <dest>`

`COPY` 指令复制本机上的 `<src>` 文件或目录到镜像的 `<dest>` 路径下。若`<dest>`或`<src>`以反斜杠`/`结尾则说明其指向的是目录，否则指向文件。

`<src>`可以是多个文件或目录，但必须是上下文根目录中的相对路径。不能使用形如 `COPY ../filename /filename`这样的指令。此外，`<src>`支持使用通配符指向所有匹配通配符的文件或目录，例如，`COPY iam* /opt/`表示添加所有以 `iam` 开头的文件到目录`/opt/`中。

`<dest>`可以是文件或目录，但必须是镜像中的绝对路径或者相对于`WORKDIR`的相对路径。 若`<dest>`是一个文件，则`<src>`的内容会被复制到`<dest>`中；否则`<src>`指向的文件或目录中的内容会被复制到`<dest>`目录中。当`<src>`指定多个源时，`<dest>`必须是目录。如果`<dest>`在镜像中不存在，则会被自动创建。

6) `ADD`

格式：`ADD <src> <dest>`

`ADD`与`COPY`指令具有相似的功能，都支持复制本地文件到镜像的功能。但`ADD`指令还支持其他功能。在 `ADD` 指令中，`<src>`可以是一个网络文件的下载地址， 比如 `ADD http://example.com/iamctl.yaml /`会在镜像中创建文件`/iamctl.yaml`。

`<src>`还可以是一个压缩归档文件，该文件在复制到容器时会被解压提取，例如`ADD iam.tar.xz /`。但是若URL中的文件为归档文件，则不会被解压提取。

在编写Dockerfile时，推荐使用`COPY`，因为`COPY`只支持本地文件，它比 `ADD` 更加透明。

7) `RUN`

`RUN`指令有两种格式：

* `RUN <command>` (shell格式)
* `RUN ["executable", "param1", "param2"]` (exec格式，推荐使用)

`RUN`指令会在前一条命令创建出的镜像的基础上创建一个容器，并在容器中运行命令，在命令结束运行后提交容器为新镜像，新镜像被Dockerfile中的下一条指令使用。

当使用shell格式时，命令通过`/bin/sh -c`运行。当使用exec格式时，命令是直接运行的，即不通过shell来运行命令。这里要注意，exec格式中的参数会以 `JSON` 数组的格式被Docker解析，所以参数必须使用双引号而不是单引号。

因为exec格式不会在shell中执行，所以环境变量不会被替换。比如，执行`RUN ["echo", "$USER"]`指令时，`$USER`不会做变量替换。如果希望运行shell程序，指令可以写成 `RUN ["/bin/bash", "-c", "echo", "$USER"]`。

8) `CMD`

`CMD`指令有3种格式:

* `CMD <command>` (shell格式)
* `CMD ["executable", "param1", "param2"]` (exec格式，推荐使用)
* `CMD ["param1", "param2"]` (为`ENTRYPOINT`指令提供参数)

`CMD`指令提供容器运行时的默认命令或参数，一个Dockerfile中可以有多条`CMD`指令，但只有最后一条`CMD`指令有效。`CMD ["param1", "param2"]`格式用来跟`ENTRYPOINT`指令配合使用，`CMD`指令中的参数会添加到`ENTRYPOINT`指令中。当使用shell和exec格式时，命令在容器中的运行方式与`RUN`指令相同。如果在执行`docker run` 时指定了命令行参数，则会覆盖`CMD`指令中的命令。

9) `ENTRYPOINT`

`ENTRYPOINT`指令有两种格式：

* `ENTRYPOINT <command>` (shell格式)
* `ENTRYPOINT ["executable", "param1", "param2"]` (exec格式，推荐格式)

`ENTRYPOINT`指令和`CMD`指令类似，都可以让容器在每次启动时执行指定的命令，但二者又有不同。`ENTRYPOINT`只能是命令 ，而`CMD`可以是参数，也可以是指令。另外，`docker run`命令行参数可以覆盖`CMD`，但不能覆盖`ENTRYPOINT`。

当使用Shell格式时，`ENTRYPOINT`指令会忽略任何`CMD`指令和`docker run`命令的参数，并且会运行在`bin/sh -c`中。

推荐使用exec格式。使用exec格式时，`docker run`传入的命令行参数会覆盖`CMD`指令的内容并且追加到`ENTRYPOINT`指令的参数中。

一个Dockerfile中可以有多条`ENTRYPOINT`指令，但只有最后一条`ENTRYPOINT`指令有效。

10) `ONBUILD`

格式：`ONBUILD [INSTRUCTION]`

`ONBUILD` 指令后面跟的是其它指令，例如  `RUN` ,  `COPY` 等。这些指令，在当前镜像构建时不会被执行，当以当前镜像为基础镜像，构建下一级镜像时才会被执行。

这里要注意，使用包含`ONBUILD`指令的Dockerfile，构建的镜像应该打上特殊的标签，比如：`python:3.0-onbuild`。另外，在`ONBUILD`指令中使用`ADD`或`COPY`指令时要特别注意。假如新的构建过程的上下文中缺失了被添加的资源，则新的构建过程会失败。我们可以通过给`ONBUILD`镜像添加特殊标签，来提示编写Dockerfile的开发人员要特别注意。

11) `VOLUME`

`VOLUME`指令有两种格式：

* `VOLUMN ["<路径1>", "路径2"...]`
* `VOLUMN <路径>`

为了防止容器内的重要数据因为容器重启而丢失，且避免容器不断变大，应该将容器内的某些目录挂载到宿主机上。在Dockerfile中，我们可以通过 `VOLUME` 指令事先将某些目录挂载为匿名卷，这样在执行`docker run`时，如果没有指定 `-v` 选项，则默认会将`VOLUMN`指定的目录挂载为匿名卷 。

12) `EXPOSE`

格式：`EXPOSE <端口1> [<端口2> ...]`

`EXPOSE`指定了该镜像生成容器时提供服务的端口（默认对外不暴露端口），可以配合`docker run -P`使用，也可以配合`docker run --net=host`使用。

13) `LABEL`

格式：`LABEL <key>=<value> <key>=<value> <key>=<value> ...`

`LABEL` 指令用来给镜像添加一些元数据（metadata）。

14) `USER`

格式：`USER <用户名>[:<用户组>]`

`USER`指令设定一个用户或者用户ID，在执行`RUN`、`CMD`、`ENTRYPOINT`等指令时指定以那个用户得身份去执行。如果容器中的服务不需要以特权身份来运行，则可以使用 `USER` 指令，切换到非root用户，以此来增加安全性。

15) `ARG`

格式：`ARG <参数名>[=<默认值>]`

与`ENV`作用一致，但作用域不相同。通过`ARG`指定的环境变量仅在`docker build`的过程中有效，构建好的镜像内不存在此变量。虽然构建好的镜像内不存在此变量，但是使用`docker history`可以查看到。所以，敏感数据不建议使用`ARG`。

可以在`docker build`时用`--build-arg <参数名>=<值>`来覆盖`ARG`指定的参数值 。

16) `HEALTHCHECK`

`HEALTHCHECK`指令有两种格式：

* `HEALTHCHECK [选项] CMD <命令>`：设置检查容器健康状况的命令
* `HEALTHCHECK NONE`：用来屏蔽掉已有的健康检查指令。

定时检测容器进程是否健康，可以设置检查的时间间隔、超时时间、重试次数、以及用于判断是否健康的指令。其中，`CMD`指定的`<命令>`的返回值决定了该次健康检查是否成功：`0` 成功；`1` 失败；`2` 保留。

`HEALTHCHECK`支持下列指令：

* --interval=<时间间隔>：设置健康检查的时间间隔，默认是 30s。
* --timeout=<超时时长>：设置单次健康检查的超时时间，默认是 30s。
* --retries=<重试次数>：如果健康检查失败，重试多少次，默认是 3 次。如果连续 3 次健康检查都失败，容器的 STATUS 就会显示 unhealthy。

例如：

```dockerfile
FROM nginx
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/list*
HEALTHCHECK --interval=5s --timeout=3s\
    CMD curl -fs http://localhost/ || exit 1
```

上面的Dockerfile中，使用`curl -fs http://localhost/ || exit 1`作为健康检查命令 ，每`5`秒检查一次容器是否健康，如果健康检查命令超过`3`秒没响应就视为检查失败。

17) `STOPSIGNAL`

格式：`STOPSIGNAL signal`

`STOPSIGNAL`指令指定了容器退出时，发送给容器的系统调用信号。

### Dockerfile 最佳实践
上面我介绍了 Dockerfile 的指令，但在编写 Dockerfile 时，只知道这些指令是不够的，还不能编写一个合格的 Dockerfile。我们还需要遵循一些编写 Dockerfile 的最佳实践。这里，我总结了一份编写 Dockerfile 的最佳实践清单，你可以参考。

1. 建议所有的 Dockerfile 指令大写，这样做可以很好地跟在镜像内执行的指令区分开来。
2. 在选择基础镜像时，尽量选择官方的镜像，并在满足要求的情况下，尽量选择体积小的镜像。**目前，Linux 镜像大小有以下关系：busybox < debian < centos < ubuntu。**最好确保同一个项目中使用一个统一的基础镜像。如无特殊需求，可以选择使用debian:jessie或者alpine。
3. 在构建镜像时，删除不需要的文件，只安装需要的文件，保持镜像干净、轻量。
4. 使用更少的层，把相关的内容放到一个层，并使用换行符进行分割。这样可以进一步减小镜像的体积，也方便查看镜像历史。
5. 不要在 Dockerfile 中修改文件的权限。因为如果修改文件的权限，Docker 在构建时会重新复制一份，这会导致镜像体积越来越大。
6. 给镜像打上标签，标签可以帮助你理解镜像的功能，例如：docker build -t="nginx:3.0-onbuild"。
7. FROM指令应该包含 tag，例如使用FROM debian:jessie，而不是FROM debian。
8. 充分利用缓存。Docker 构建引擎会顺序执行 Dockerfile 中的指令，而且一旦缓存失效，后续命令将不能使用缓存。为了有效地利用缓存，需要尽量将所有的 Dockerfile 文件中相同的部分都放在前面，而将不同的部分放在后面。
9. 优先使用COPY而非ADD指令。和ADD相比，COPY 功能简单，而且也够用。ADD可变的行为会导致该指令的行为不清晰，不利于后期维护和理解。
10. 推荐将CMD和ENTRYPOINT指令结合使用，使用 execl 格式的ENTRYPOINT指令设置固定的默认命令和参数，然后使用CMD指令设置可变的参数。
11. 尽量使用 Dockerfile 共享镜像。通过共享 Dockerfile，可以使开发者明确知道 Docker 镜像的构建过程，并且可以将 Dockerfile 文件加入版本控制，跟踪起来。
12. 使用.dockerignore忽略构建镜像时非必需的文件。忽略无用的文件，可以提高构建速度。
13. 使用多阶段构建。多阶段构建可以大幅减小最终镜像的体积。例如，COPY指令中可能包含一些安装包，安装完成之后这些内容就废弃掉。下面是一个简单的多阶段构建示例：

```dockerfile
FROM golang:1.11-alpine AS build

# 安装依赖包
RUN go get github.com/golang/mock/mockgen

# 复制源码并执行build，此处当文件有变化会产生新的一层镜像层
COPY . /go/src/iam/
RUN go build -o /bin/iam

# 缩小到一层镜像
FROM busybox
COPY --from=build /bin/iam /bin/iam
ENTRYPOINT ["/bin/iam"]
CMD ["--help"]
```
### 总结
如果你想使用 Docker 容器来部署应用，那么就需要制作 Docker 镜像。今天，我介绍了如何制作 Docker 镜像。


你可以使用这两种方式来构建 Docker 镜像：
- 通过 docker commit 命令，基于一个已存在的容器构建出镜像。
- 通过编写 Dockerfile 文件，并使用 docker build 命令来构建镜像。


这两种方法中，镜像构建的底层原理是相同的：
- 基于原镜像启动一个 Docker 容器。
- 在容器中进行一些操作，例如执行命令、安装文件等，由这些操作产生的文件变更都会被记录在容器的存储层中。
- 将容器存储层的变更 commit 到新的镜像层中，并添加到原镜像上。

此外，我们还可以使用 docker save / docker load 和 docker export / docker import 来复制 Docker 镜像。在实际生产环境中，我们标准的做法是通过 Dockerfile 来构建镜像。使用 Dockerfile 构建镜像，就需要你编写 Dockerfile 文件。Dockerfile 支持多个指令，这些指令可以分为 5 类，对指令的具体介绍你可以再返回复习一遍。

另外，我们在构建 Docker 镜像时，也要遵循一些最佳实践，具体你可以参考我给你总结的最佳实践清单。

## 47 | 如何编写Kubernetes资源定义文件？

在接下来的 48 讲，我会介绍如何基于腾讯云 EKS 来部署 IAM 应用。EKS 其实是一个标准的 Kubernetes 集群，在 Kubernetes 集群中部署应用，需要编写 Kubernetes 资源的 YAML（Yet Another Markup Language）定义文件，例如 Service、Deployment、ConfigMap、Secret、StatefulSet 等。

这些 YAML 定义文件里面有很多配置项需要我们去配置，其中一些也比较难理解。为了你在学习下一讲时更轻松，这一讲我们先学习下如何编写 Kubernetes YAML 文件。

### 为什么选择 YAML 格式来定义 Kubernetes 资源？
首先解释一下，我们为什么使用 YAML 格式来定义 Kubernetes 的各类资源呢？这是因为 YAML 格式和其他格式（例如 XML、JSON 等）相比，不仅能够支持丰富的数据，而且结构清晰、层次分明、表达性极强、易于维护，非常适合拿来供开发者配置和管理 Kubernetes 资源。

其实 Kubernetes 支持 YAML 和 JSON 两种格式，JSON 格式通常用来作为接口之间消息传递的数据格式，YAML 格式则用于资源的配置和管理。YAML 和 JSON 这两种格式是可以相互转换的，你可以通过在线工具json2yaml，来自动转换 YAML 和 JSON 数据格式。

例如，下面是一个 YAML 文件中的内容：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: iam-apiserver
spec:
  clusterIP: 192.168.0.231
  externalTrafficPolicy: Cluster
  ports:
  - name: https
    nodePort: 30443
    port: 8443
    protocol: TCP
    targetPort: 8443
  selector:
    app: iam-apiserver
  sessionAffinity: None
  type: NodePort
```
它对应的 JSON 格式的文件内容为：
```json
{
  "apiVersion": "v1",
  "kind": "Service",
  "metadata": {
    "name": "iam-apiserver"
  },
  "spec": {
    "clusterIP": "192.168.0.231",
    "externalTrafficPolicy": "Cluster",
    "ports": [
      {
        "name": "https",
        "nodePort": 30443,
        "port": 8443,
        "protocol": "TCP",
        "targetPort": 8443
      }
    ],
    "selector": {
      "app": "iam-apiserver"
    },
    "sessionAffinity": "None",
    "type": "NodePort"
  }
}
```
我就是通过json2yaml在线工具，来转换 YAML 和 JSON 的，如下图所示：

![img](https://static001.geekbang.org/resource/image/0f/02/0ffac271b296d1cc407941cfc3139702.png?wh=1920x780)

在编写 Kubernetes 资源定义文件的过程中，如果因为 YAML 格式文件中的配置项缩进太深，导致不容易判断配置项的层级，那么，你就可以将其转换成 JSON 格式，通过 JSON 格式来判断配置型的层级。

如果想学习更多关于 YAML 的知识，你可以参考YAML 1.2 (3rd Edition)。这里，可以先看看我整理的 YAML 基本语法：

- 属性和值都是大小写敏感的。
- 使用缩进表示层级关系。
- 禁止使用 Tab 键缩进，只允许使用空格，建议两个空格作为一个层级的缩进。元素左对齐，就说明对齐的两个元素属于同一个级别。
- 使用 # 进行注释，直到行尾。
- key: value格式的定义中，冒号后要有一个空格。
- 短横线表示列表项，使用一个短横线加一个空格；多个项使用同样的缩进级别作为同一列表。
- 使用 --- 表示一个新的 YAML 文件开始。

现在你知道了，Kubernetes 支持 YAML 和 JSON 两种格式，它们是可以相互转换的。但鉴于 YAML 格式的各项优点，我建议你使用 YAML 格式来定义 Kubernetes 的各类资源。

### Kubernetes 资源定义概述
Kubernetes 中有很多内置的资源，常用的资源有 Deployment、StatefulSet、ConfigMap、Service、Secret、Nodes、Pods、Events、Jobs、DaemonSets 等。除此之外，Kubernetes 还有其他一些资源。如果你觉得 Kubernetes 内置的资源满足不了需求，还可以自定义资源。

Kubernetes 的资源清单可以通过执行以下命令来查看：


```
$ kubectl api-resources
NAME                              SHORTNAMES   APIVERSION                        NAMESPACED   KIND
bindings                                       v1                                true         Binding
componentstatuses                 cs           v1                                false        ComponentStatus
configmaps                        cm           v1                                true         ConfigMap
endpoints                         ep           v1                                true         Endpoints
events                            ev           v1                                true         Event
```

上述输出中，各列的含义如下。
- NAME：资源名称。
- SHORTNAMES：资源名称简写。
- APIVERSION：资源的 API 版本，也称为 group。
- NAMESPACED：资源是否具有 Namespace 属性。
- KIND：资源类别。

这些资源有一些共同的配置，也有一些特有的配置。这里，我们先来看下这些资源共同的配置。下面这些配置是 Kubernetes 各类资源都具备的：


```yaml
---
apiVersion: <string> # string类型，指定group的名称，默认为core。可以使用 `kubectl api-versions` 命令，来获取当前kubernetes版本支持的所有group。
kind: <string> # string类型，资源类别。
metadata: <Object> # 资源的元数据。
  name: <string> # string类型，资源名称。
  namespace: <string> # string类型，资源所属的命名空间。
  lables: < map[string]string> # map类型，资源的标签。
  annotations: < map[string]string> # map类型，资源的标注。
  selfLink: <string> # 资源的 REST API路径，格式为：/api/<group>/namespaces/<namespace>/<type>/<name>。例如：/api/v1/namespaces/default/services/iam-apiserver
spec: <Object> # 定义用户期望的资源状态（disired state）。
status: <Object> # 资源当前的状态，以只读的方式显示资源的最近状态。这个字段由kubernetes维护，用户无法定义。
```
你可以通过kubectl explain <object>命令来查看 Object 资源对象介绍，并通过kubectl explain <object1>.<object2>来查看<object1>的子对象<object2>的资源介绍，例如：

```
$ kubectl explain service
$ kubectl explain service.spec
$ kubectl explain service.spec.ports
```

Kubernetes 资源定义 YAML 文件，支持以下数据类型：
- string，表示字符串类型。
- object，表示一个对象，需要嵌套多层字段。
- map[string]string，表示由 key:value 组成的映射。
- [ ]string，表示字串列表。
- []object，表示对象列表。
- boolean，表示布尔类型。
- integer，表示整型。

### 常用的 Kubernetes 资源定义
上面说了，Kubernetes 中有很多资源，其中 Pod、Deployment、Service、ConfigMap 这 4 类是比较常用的资源，我来一个个介绍下。


#### Pod 资源定义
下面是一个 Pod 的 YAML 定义：

```yaml
apiVersion: v1   # 必须 版本号， 常用v1  apps/v1
kind: Pod   # 必须
metadata:  # 必须，元数据
  name: string  # 必须，名称
  namespace: string # 必须，命名空间，默认上default,生产环境为了安全性建议新建命名空间分类存放
  labels:   # 非必须，标签，列表值
    - name: string
  annotations:  # 非必须，注解，列表值
    - name: string
spec:  # 必须，容器的详细定义
  containers:  #必须，容器列表，
    - name: string　　　#必须，容器1的名称
      image: string    #必须，容器1所用的镜像
      imagePullPolicy: [Always|Never|IfNotPresent]  #非必须，镜像拉取策略，默认是Always
      command: [string]  # 非必须 列表值，如果不指定，则是一镜像打包时使用的启动命令
      args:　[string] # 非必须，启动参数
      workingDir: string # 非必须，容器内的工作目录
      volumeMounts: # 非必须，挂载到容器内的存储卷配置
        - name: string  # 非必须，存储卷名字，需与【@1】处定义的名字一致
          readOnly: boolean #非必须，定义读写模式，默认是读写
      ports: # 非必须，需要暴露的端口
        - name: string  # 非必须 端口名称
          containerPort: int  # 非必须 端口号
          hostPort: int # 非必须 宿主机需要监听的端口号，设置此值时，同一台宿主机不能存在同一端口号的pod， 建议不要设置此值
          proctocol: [tcp|udp]  # 非必须 端口使用的协议，默认是tcp
      env: # 非必须 环境变量
        - name: string # 非必须 ，环境变量名称
          value: string  # 非必须，环境变量键值对
      resources:  # 非必须，资源限制
        limits:  # 非必须，限制的容器使用资源的最大值，超过此值容器会推出
          cpu: string # 非必须，cpu资源，单位是core，从0.1开始
          memory: string 内存限制，单位为MiB,GiB
        requests:  # 非必须，启动时分配的资源
          cpu: string 
          memory: string
      livenessProbe:   # 非必须，容器健康检查的探针探测方式
        exec: # 探测命令
          command: [string] # 探测命令或者脚本
        httpGet: # httpGet方式
          path: string  # 探测路径，例如 http://ip:port/path
          port: number  
          host: string  
          scheme: string
          httpHeaders:
            - name: string
              value: string
          tcpSocket:  # tcpSocket方式，检查端口是否存在
            port: number
          initialDelaySeconds: 0 #容器启动完成多少秒后的再进行首次探测，单位为s
          timeoutSeconds: 0  #探测响应超时的时间,默认是1s,如果失败，则认为容器不健康，会重启该容器
          periodSeconds: 0  # 探测间隔时间，默认是10s
          successThreshold: 0  # 
          failureThreshold: 0
        securityContext:
          privileged: false
        restartPolicy: [Always|Never|OnFailure]  # 容器重启的策略，
        nodeSelector: object  # 指定运行的宿主机
        imagePullSecrets:  # 容器下载时使用的Secrets名称，需要与valumes.secret中定义的一致
          - name: string
        hostNetwork: false
        volumes: ## 挂载的共享存储卷类型
          - name: string  # 非必须，【@1】
          emptyDir: {}
          hostPath:
            path: string
          secret:  # 类型为secret的存储卷，使用内部的secret内的items值作为环境变量
            secrectName: string
            items:
              - key: string
                path: string
            configMap:  ## 类型为configMap的存储卷
              name: string
              items:
                - key: string
                  path: string

```

Pod 是 Kubernetes 中最重要的资源，我们可以通过 Pod YAML 定义来创建一个 Pod，也可以通过 DaemonSet、Deployment、ReplicaSet、StatefulSet、Job、CronJob 来创建 Pod。

#### Deployment 资源定义
Deployment 资源定义 YAML 文件如下：


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels: # 设定资源的标签
    app: iam-apiserver
  name: iam-apiserver
  namespace: default
spec:
  progressDeadlineSeconds: 10 # 指定多少时间内不能完成滚动升级就视为失败，滚动升级自动取消
  replicas: 1 # 声明副本数，建议 >= 2
  revisionHistoryLimit: 5 # 设置保留的历史版本个数，默认是10
  selector: # 选择器
    matchLabels: # 匹配标签
      app: iam-apiserver # 标签格式为key: value对
  strategy: # 指定部署策略
    rollingUpdate:
      maxSurge: 1 # 最大额外可以存在的副本数，可以为百分比，也可以为整数
      maxUnavailable: 1 # 表示在更新过程中能够进入不可用状态的 Pod 的最大值，可以为百分比，也可以为整数
    type: RollingUpdate # 更新策略，包括：重建(Recreate)、RollingUpdate(滚动更新)
  template: # 指定Pod创建模板。注意：以下定义为Pod的资源定义
    metadata: # 指定Pod的元数据
      labels: # 指定Pod的标签
        app: iam-apiserver
    spec:
      affinity:
        podAntiAffinity: # Pod反亲和性，尽量避免同一个应用调度到相同Node
          preferredDuringSchedulingIgnoredDuringExecution: # 软需求
          - podAffinityTerm:
              labelSelector:
                matchExpressions: # 有多个选项，只有同时满足这些条件的节点才能运行 Pod
                - key: app
                  operator: In # 设定标签键与一组值的关系，In、NotIn、Exists、DoesNotExist
                  values:
                  - iam-apiserver
              topologyKey: kubernetes.io/hostname
            weight: 100 # weight 字段值的范围是1-100。
      containers:
      - command: # 指定运行命令
        - /opt/iam/bin/iam-apiserver # 运行参数
        - --config=/etc/iam/iam-apiserver.yaml
        image: ccr.ccs.tencentyun.com/lkccc/iam-apiserver-amd64:v1.0.6 # 镜像名，遵守镜像命名规范
        imagePullPolicy: Always # 镜像拉取策略。IfNotPresent：优先使用本地镜像；Never：使用本地镜像，本地镜像不存在，则报错；Always：默认值，每次都重新拉取镜像
        # lifecycle: # kubernetes支持postStart和preStop事件。当一个容器启动后，Kubernetes将立即发送postStart事件；在容器被终结之前，Kubernetes将发送一个preStop事件
        name: iam-apiserver # 容器名称，与应用名称保持一致
        ports: # 端口设置
        - containerPort: 8443 # 容器暴露的端口
          name: secure # 端口名称
          protocol: TCP # 协议，TCP和UDP
        livenessProbe: # 存活检查，检查容器是否正常，不正常则重启实例
          httpGet: # HTTP请求检查方法
            path: /healthz # 请求路径
            port: 8080 # 检查端口
            scheme: HTTP # 检查协议
          initialDelaySeconds: 5 # 启动延时，容器延时启动健康检查的时间
          periodSeconds: 10 # 间隔时间，进行健康检查的时间间隔
          successThreshold: 1 # 健康阈值，表示后端容器从失败到成功的连续健康检查成功次数
          failureThreshold: 1 # 不健康阈值，表示后端容器从成功到失败的连续健康检查成功次数
          timeoutSeconds: 3 # 响应超时，每次健康检查响应的最大超时时间
        readinessProbe: # 就绪检查，检查容器是否就绪，不就绪则停止转发流量到当前实例
          httpGet: # HTTP请求检查方法
            path: /healthz # 请求路径
            port: 8080 # 检查端口
            scheme: HTTP # 检查协议
          initialDelaySeconds: 5 # 启动延时，容器延时启动健康检查的时间
          periodSeconds: 10 # 间隔时间，进行健康检查的时间间隔
          successThreshold: 1 # 健康阈值，表示后端容器从失败到成功的连续健康检查成功次数
          failureThreshold: 1 # 不健康阈值，表示后端容器从成功到失败的连续健康检查成功次数
          timeoutSeconds: 3 # 响应超时，每次健康检查响应的最大超时时间
        startupProbe: # 启动探针，可以知道应用程序容器什么时候启动了
          failureThreshold: 10
          httpGet:
            path: /healthz
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 5
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 3
        resources: # 资源管理
          limits: # limits用于设置容器使用资源的最大上限,避免异常情况下节点资源消耗过多
            cpu: "1" # 设置cpu limit，1核心 = 1000m
            memory: 1Gi # 设置memory limit，1G = 1024Mi
          requests: # requests用于预分配资源,当集群中的节点没有request所要求的资源数量时,容器会创建失败
            cpu: 250m # 设置cpu request
            memory: 500Mi # 设置memory request
        terminationMessagePath: /dev/termination-log # 容器终止时消息保存路径
        terminationMessagePolicy: File # 仅从终止消息文件中检索终止消息
        volumeMounts: # 挂载日志卷
        - mountPath: /etc/iam/iam-apiserver.yaml # 容器内挂载镜像路径
          name: iam # 引用的卷名称
          subPath: iam-apiserver.yaml # 指定所引用的卷内的子路径，而不是其根路径。
        - mountPath: /etc/iam/cert
          name: iam-cert
      dnsPolicy: ClusterFirst
      restartPolicy: Always # 重启策略，Always、OnFailure、Never
      schedulerName: default-scheduler # 指定调度器的名字
      imagePullSecrets: # 在Pod中设置ImagePullSecrets只有提供自己密钥的Pod才能访问私有仓库
        - name: ccr-registry # 镜像仓库的Secrets需要在集群中手动创建
      securityContext: {} # 指定安全上下文
      terminationGracePeriodSeconds: 5 # 优雅关闭时间，这个时间内优雅关闭未结束，k8s 强制 kill
      volumes: # 配置数据卷，类型详见https://kubernetes.io/zh/docs/concepts/storage/volumes
      - configMap: # configMap 类型的数据卷
          defaultMode: 420 #权限设置0~0777，默认0664
          items:
          - key: iam-apiserver.yaml
            path: iam-apiserver.yaml
          name: iam # configmap名称
        name: iam # 设置卷名称，与volumeMounts名称对应
      - configMap:
          defaultMode: 420
          name: iam-cert
        name: iam-cert
```

在部署时，你可以根据需要来配置相应的字段，常见的需要配置的字段为：labels、name、namespace、replicas、command、imagePullPolicy、container.name、livenessProbe、readinessProbe、resources、volumeMounts、volumes、imagePullSecrets等。

另外，在部署应用时，经常需要提供配置文件，供容器内的进程加载使用。最常用的方法是挂载 ConfigMap 到应用容器中。那么，如何挂载 ConfigMap 到容器中呢？

引用 ConfigMap 对象时，你可以在 volume 中通过它的名称来引用。你可以自定义 ConfigMap 中特定条目所要使用的路径。下面的配置就显示了如何将名为 log-config 的 ConfigMap 挂载到名为 configmap-pod 的 Pod 中：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
    - name: test
      image: busybox
      volumeMounts:
        - name: config-vol
          mountPath: /etc/config
  volumes:
    - name: config-vol
      configMap:
        name: log-config
        items:
          - key: log_level
            path: log_level

```

log-config ConfigMap 以卷的形式挂载，并且存储在 log_level 条目中的所有内容都被挂载到 Pod 的/etc/config/log_level 路径下。 请注意，这个路径来源于卷的 mountPath 和 log_level 键对应的path。这里需要注意，在使用 ConfigMap 之前，你首先要创建它。接下来，我们来看下 ConfigMap 定义。


#### ConfigMap 资源定义
下面是一个 ConfigMap YAML 示例：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: test-config4
data: # 存储配置内容
  db.host: 172.168.10.1 # 存储格式为key: value
  db.port: 3306
```

可以看到，ConfigMap 的 YAML 定义相对简单些。假设我们将上述 YAML 文件保存在了iam-configmap.yaml文件中，我们可以执行以下命令，来创建 ConfigMap：

```
$ kubectl create -f iam-configmap.yaml
```
除此之外，kubectl 命令行工具还提供了 3 种创建 ConfigMap 的方式。我来分别介绍下。

1）通过--from-literal参数创建
创建命令如下：

```
$ kubectl create configmap iam-configmap --from-literal=db.host=172.168.10.1 --from-literal=db.port='3306'
```
2）通过--from-file=<文件>参数创建

创建命令如下：

```
$ echo -n 172.168.10.1 > ./db.host
$ echo -n 3306 > ./db.port
$ kubectl create cm iam-configmap --from-file=./db.host --from-file=./db.port
```
--from-file的值也可以是一个目录。当值是目录时，目录中的文件名为 key，目录的内容为 value。

3）通过--from-env-file参数创建

创建命令如下：

```
$ cat << EOF > env.txt
db.host=172.168.10.1
db.port=3306
EOF
$ kubectl create cm iam-configmap --from-env-file=env.txt
```

#### Service 资源定义
Service 是 Kubernetes 另一个核心资源。通过创建 Service，可以为一组具有相同功能的容器应用提供一个统一的入口地址，并且将请求负载到后端的各个容器上。Service 资源定义 YAML 文件如下：

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: iam-apiserver
  name: iam-apiserver
  namespace: default
spec:
  clusterIP: 192.168.0.231 # 虚拟服务地址
  externalTrafficPolicy: Cluster # 表示此服务是否希望将外部流量路由到节点本地或集群范围的端点
  ports: # service需要暴露的端口列表
  - name: https #端口名称
    nodePort: 30443 # 当type = NodePort时，指定映射到物理机的端口号
    port: 8443 # 服务监听的端口号
    protocol: TCP # 端口协议，支持TCP和UDP，默认TCP
    targetPort: 8443 # 需要转发到后端Pod的端口号
  selector: # label selector配置，将选择具有label标签的Pod作为其后端RS
    app: iam-apiserver
  sessionAffinity: None # 是否支持session
  type: NodePort # service的类型，指定service的访问方式，默认为clusterIp
```
上面，我介绍了常用的 Kubernetes YAML 的内容。我们在部署应用的时候，是需要手动编写这些文件的。接下来，我就讲解一些在编写过程中常用的编写技巧。

### YAML 文件编写技巧
这里我主要介绍三个技巧。


1）使用在线的工具来自动生成模板 YAML 文件。

YAML 文件很复杂，完全从 0 开始编写一个 YAML 定义文件，工作量大、容易出错，也没必要。我比较推荐的方式是，使用一些工具来自动生成所需的 YAML。

这里我推荐使用k8syaml工具。k8syaml是一个在线的 YAML 生成工具，当前能够生成 Deployment、StatefulSet、DaemonSet 类型的 YAML 文件。k8syaml具有默认值，并且有对各字段详细的说明，可以供我们填参时参考。


2）使用kubectl run命令获取 YAML 模板：

```
$ kubectl run --dry-run=client --image=nginx nginx -o yaml > my-nginx.yaml
$ cat my-nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
然后，我们可以基于这个模板，来修改配置，形成最终的 YAML 文件。


3）导出集群中已有的资源描述。

有时候，如果我们想创建一个 Kubernetes 资源，并且发现该资源跟集群中已经创建的资源描述相近或者一致的时候，可以选择导出集群中已经创建资源的 YAML 描述，并基于导出的 YAML 文件进行修改，获得所需的 YAML。例如：

```
$ kubectl get deployment iam-apiserver -o yaml > iam-authz-server.yaml
```
接着，修改iam-authz-server.yaml。通常，我们需要删除 Kubernetes 自动添加的字段，例如kubectl.kubernetes.io/last-applied-configuration、deployment.kubernetes.io/revision、creationTimestamp、generation、resourceVersion、selfLink、uid、status。

这些技巧可以帮助我们更好地编写和使用 Kubernetes YAML。


### 使用 Kubernetes YAML 时的一些推荐工具
接下来，我再介绍一些比较流行的工具，你可以根据自己的需要进行选择。

#### kubeval
kubeval可以用来验证 Kubernetes YAML 是否符合 Kubernetes API 模式。

安装方法如下：

```
$ wget https://github.com/instrumenta/kubeval/releases/latest/download/kubeval-linux-amd64.tar.gz
$ tar xf kubeval-linux-amd64.tar.gz
$ mv kubeval $HOME/bin
```

安装完成后，我们对 Kubernetes YAML 文件进行验证：

```
$ kubeval deployments/iam.invalid.yaml
ERR  - iam/templates/iam-configmap.yaml: Duplicate 'ConfigMap' resource 'iam' in namespace ''
```
根据提示，查看iam.yaml，发现在iam.yaml文件中，我们定义了两个同名的iam ConfigMap：

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: iam
data:
  {}
---
# Source: iam/templates/iam-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: iam
data:
  iam-: ""
  iam-apiserver.yaml: |
    ...
```
可以看到，使用kubeval之类的工具，能让我们在部署的早期，不用访问集群就能发现 YAML 文件的错误。


#### kube-score
kube-score能够对 Kubernetes YAML 进行分析，并根据内置的检查对其评分，这些检查是根据安全建议和最佳实践而选择的，例如：
- 以非 Root 用户启动容器。
- 为 Pods 设置健康检查。
- 定义资源请求和限制。

你可以按照这个方法安装：

```
$ go get github.com/zegl/kube-score/cmd/kube-score
```
然后，我们对 Kubernetes YAML 进行评分：

```
$ kube-score score -o ci deployments/iam.invalid.yaml
[OK] iam-apiserver apps/v1/Deployment
[OK] iam-apiserver apps/v1/Deployment
[OK] iam-apiserver apps/v1/Deployment
[OK] iam-apiserver apps/v1/Deployment
[CRITICAL] iam-apiserver apps/v1/Deployment: The pod does not have a matching NetworkPolicy
[CRITICAL] iam-apiserver apps/v1/Deployment: Container has the same readiness and liveness probe
[CRITICAL] iam-apiserver apps/v1/Deployment: (iam-apiserver) The pod has a container with a writable root filesystem
[CRITICAL] iam-apiserver apps/v1/Deployment: (iam-apiserver) The container is running with a low user ID
[CRITICAL] iam-apiserver apps/v1/Deployment: (iam-apiserver) The container running with a low group ID
[OK] iam-apiserver apps/v1/Deployment
...
```
检查的结果有OK、SKIPPED、WARNING和CRITICAL。CRITICAL是需要你修复的；WARNING是需要你关注的；SKIPPED是因为某些原因略过的检查；OK是验证通过的。如果你想查看详细的错误原因和解决方案，可以使用-o human选项，例如：

```
$ kube-score score -o human deployments/iam.invalid.yaml
```

上述命令会检查 YAML 资源定义文件，如果有不合规的地方会报告级别、类别以及错误详情，如下图所示：

![img](https://static001.geekbang.org/resource/image/04/f6/0498529693c6d15c9d9d45cbyy866cf6.png?wh=1920x827)


当然，除了 kubeval、kube-score 这两个工具，业界还有其他一些 Kubernetes 检查工具，例如config-lint、copper、conftest、polaris等。

这些工具，我推荐你这么来选择：首先，使用 kubeval 工具做最基本的 YAML 文件验证。验证通过之后，我们就可以进行更多的测试。如果你没有特别复杂的 YAML 验证要求，只需要用到一些最常见的检查策略，这时候可以使用 kube-score。如果你有复杂的验证要求，并且希望能够自定义验证策略，则可以考虑使用 copper。当然，polaris、config-lint、copper也值得你去尝试下。

### 总结
今天，我主要讲了如何编写 Kubernetes YAML 文件。

YAML 格式具有丰富的数据表达能力、清晰的结构和层次，因此被用于 Kubernetes 资源的定义文件中。如果你要把应用部署在 Kubernetes 集群中，就要创建多个关联的 K8s 资源，如果要创建 K8s 资源，目前比较多的方式还是编写 YAML 格式的定义文件。

这一讲我介绍了 K8s 中最常用的四种资源（Pod、Deployment、Service、ConfigMap）的 YAML 定义的写法，你可以常来温习。

另外，在编写 YAML 文件时，也有一些技巧。比如，可以通过在线工具k8syaml来自动生成初版的 YAML 文件，再基于此 YAML 文件进行二次修改，从而形成终版。最后，我还给你分享了编写和使用 Kubernetes YAML 时，社区提供的多种工具。比如，kubeval 可以校验 YAML，kube-score 可以给 YAML 文件打分。了解了如何编写 Kubernetes YAML 文件，下一讲的学习相信你会进行得更顺利。


## 48 | 基于腾讯云 EKS 的容器化部署实战


在 45 讲中，我介绍了一种基于 Kubernetes 的云原生架构设计方案。在云原生架构中，我们是通过 Docker + Kubernetes 来部署云原生应用的。那么这一讲，我就手把手教你如何在 Kubernetes 集群中部署好 IAM 应用。因为步骤比较多，所以希望你能跟着我完成每一个操作步骤。相信在实操的过程中，你也会学到更多的知识。

### 准备工作
在部署 IAM 应用之前，我们需要做以下准备工作：开通腾讯云容器服务镜像仓库。安装并配置 Docker。准备一个 Kubernetes 集群。

### 开通腾讯云容器服务镜像仓库
在 Kubernetes 集群中部署 IAM 应用，需要从镜像仓库下载指定的 IAM 镜像，所以首先需要有一个镜像仓库来托管 IAM 的镜像。我们可以选择将 IAM 镜像托管到DockerHub上，这也是 docker 运行时默认获取镜像的地址。

但因为 DockerHub 服务部署在国外，国内访问速度很慢。所以，我建议将 IAM 镜像托管在国内的镜像仓库中。这里我们可以选择腾讯云提供的镜像仓库服务，访问地址为[容器镜像服务个人版](https://cloud.tencent.com/login?s_url=https%3A%2F%2Fconsole.cloud.tencent.com%2Ftke2%2Fregistry)。

如果你已经有腾讯云的镜像仓库，可以忽略腾讯云镜像仓库开通步骤。

在开通腾讯云镜像仓库之前，你需要注册腾讯云账号，并完成实名认证。开通腾讯云镜像仓库的具体步骤如下：

#### 第一步，开通个人版镜像仓库。
登录容器服务控制台，选择左侧导航栏中的【镜像仓库】>【个人版】。根据以下提示，填写相关信息，并单击【开通】进行初始化。如下图所示：

![img](https://static001.geekbang.org/resource/image/1d/8c/1d4bdfe89bc049d224e7002c23a0ea8c.png?wh=1920x569)

用户名：默认是当前用户的账号 ID，是你登录到腾讯云 Docker 镜像仓库的身份，可在 账号信息 页面获取。密码：是你登录到腾讯云 Docker 镜像仓库的凭证。

这里需要你记录用户名及密码，用于推送及拉取镜像。假如我们开通的镜像仓库，用户名为10000099xxxx，密码为iam59!z$。

这里要注意，10000099xxxx要替换成你镜像仓库的用户名。

#### 第二步，登录到腾讯云 Registry（镜像仓库）。
在我们开通完 Registry，就可以登录 Registry 了。可以通过以下命令来登录腾讯云 Registry：

```
$ docker login --username=[username] ccr.ccs.tencentyun.com
```

这里的 username 是腾讯云账号 ID，开通时已注册，可在 账号信息 页面获取。docker 命令会在后面安装。



#### 第三步，新建镜像仓库命名空间。
如果想使用镜像仓库，那么你首先需要创建一个用来创建镜像的命名空间。上一步，我们开通了镜像仓库，就可以在“命名空间”页签新建命名空间了，如下图所示：

![img](https://static001.geekbang.org/resource/image/1e/5a/1e79852fdc5ee1a094bd42efdf21015a.png?wh=1920x522)

上图中，我们创建了一个叫marmotedu的命名空间。这里，镜像仓库服务、命名空间、镜像仓库、标签这几个概念你可能弄不清楚。接下来，我详细介绍下四者的关系，关系如下图所示：

![img](https://static001.geekbang.org/resource/image/ab/3b/abe8358d3ea2851bc32c80fd9519c63b.jpg?wh=2248x1581)

先来看下我们使用镜像仓库的格式：<镜像仓库服务地址>/<命名空间>/<镜像仓库>:<标签>，例如ccr.ccs.tencentyun.com/marmotedu/iam-apiserver-amd64:v1.1.0。


如果想使用一个 Docker 镜像，我们首先需要开通一个镜像仓库服务（Registry），镜像仓库服务都会对外提供一个固定的地址供你访问。在 Registry 中，我们（User）可以创建一个或多个命名空间（Namespace），命名空间也可以简单理解为镜像仓库逻辑上的一个分组。接下来，就可以在 Namespace 中创建一个或多个镜像仓库，例如iam-apiserver-amd64、iam-authz-server-amd64、iam-pump-amd64等。针对每一个镜像仓库，又可以创建多个标签（Tag），例如v1.0.1、v1.0.2等。

<镜像仓库>:<标签>又称为镜像。镜像又分为私有镜像和公有镜像，公有镜像可供所有能访问 Registry 的用户下载使用，私有镜像只提供给通过授权的用户使用。


### 安装 Docker
开通完镜像仓库之后，我们还需要安装 Docker，用来构建和测试 Docker 镜像。下面我来讲解下具体的安装步骤。

#### 第一步，安装 Docker 前置条件检查。
需要确保 CentOS 系统启用了centos-extras yum 源，默认情况下已经启用，检查方式如下：

```
$ cat /etc/yum.repos.d/CentOS-Extras.repo
# Qcloud-Extras.repo

[extras]
name=Qcloud-$releasever - Extras
baseurl=http://mirrors.tencentyun.com/centos/$releasever/extras/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-Qcloud-8
```
如果/etc/yum.repos.d/CentOS-Extras.repo文件存在，且文件中extras部分的enabled配置项值为1，说明已经启用了centos-extras yum 源。如果/etc/yum.repos.d/CentOS-Extras.repo 文件不存在，或者enabled 不为 1，则需要创建/etc/yum.repos.d/CentOS-Extras.repo 文件，并将上述内容复制进去。

#### 第二步，安装 docker。
Docker 官方文档 Install Docker Engine on CentOS提供了 3 种安装方法:
通过 Yum 源安装。通过 RPM 包安装。通过脚本安装。

这里，我们选择最简单的安装方式：通过 Yum 源安装。它具体又分为下面 3 个步骤。

```bash
# 安装 docker。
sudo yum install -y yum-utils # 1. 安装 `yum-utils` 包，该包提供了 `yum-config-manager` 工具
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo # 2. 安装 `docker-ce.repo` yum 源
sudo yum-config-manager --enable docker-ce-nightly docker-ce-test # 3. 启用 `nightly` 和 `test` yum 源
sudo yum install -y docker-ce docker-ce-cli containerd.io # 4. 安装最新版本的 docker 引擎和 containerd

# 启动 docker。
$ sudo systemctl start docker

# docker 的配置文件是 /etc/docker/daemon.json ，这个配置文件默认是没有的，需要我们手动创建：
$ sudo tee /etc/docker/daemon.json << EOF
{
  "bip": "172.16.0.1/24",
  "registry-mirrors": [],
  "graph": "/data/lib/docker"
}
EOF
```

这里，我来解释下常用的配置参数。
- registry-mirrors：仓库地址，可以根据需要修改为指定的地址。
- graph：镜像、容器的存储路径，默认是 /var/lib/docker。如果你的 / 目录存储空间满足不了需求，需要设置 graph 为更大的目录。
- bip：指定容器的 IP 网段。

配置完成后，需要重启 Docker：

```
$ sudo systemctl restart docker

```
测试 Docker 是否安装成功。

```
$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b8dfde127a29: Pull complete
Digest: sha256:0fe98d7debd9049c50b597ef1f85b7c1e8cc81f59c8d623fcb2250e8bec85b38
Status: Downloaded newer image for hello-world:latest
...
Hello from Docker!
This message shows that your installation appears to be working correctly.
....
```
docker run hello-world命令会下载hello-world镜像，并启动容器，打印安装成功提示信息后退出。这里注意，如果你通过 Yum 源安装失败，可以尝试 Docker 官方文档 [Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/)提供的其他方式安装。


#### 第三步，安装后配置。
安装成功后，我们还需要做一些其他配置。主要有两个，**一个是配置 docker，使其可通过non-root用户使用，另一个是配置 docker 开机启动。**

使用non-root用户操作 Docker
我们在 Linux 系统上操作，为了安全，需要以普通用户的身份登录系统并执行操作。所以，我们需要配置 docker，使它可以被non-root用户使用。具体配置方法如下：

```
$ sudo groupadd docker # 1. 创建`docker`用户组
$ sudo usermod -aG docker $USER # 2. 将当前用户添加到`docker`用户组下
$ newgrp docker # 3. 重新加载组成员身份
$ docker run hello-world # 4. 确认能够以普通用户使用docker
```

如果在执行 sudo groupadd docker 时报 groupadd: group 'docker' already exists 错误，说明 docker 组已经存在了，可以忽略这个报错。如果你在将用户添加到 docker 组之前，使用 sudo 运行过 docker 命令，你可能会看到以下错误：

```
WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
```
这个错误，我们可以通过删除~/.docker/目录来解决，或者通过以下命令更改~/.docker/目录的所有者和权限：

```
$ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
$ sudo chmod g+rwx "$HOME/.docker" -R
```
配置 docker 开机启动
大部分 Linux 发行版（RHEL、CentOS、Fedora、Debian、Ubuntu 16.04 及更高版本）使用 systemd 来管理服务，包括指定开启时启动的服务。在 Debian 和 Ubuntu 上，Docker 默认配置为开机启动。

在其他系统，我们需要手动配置 Docker 开机启动，配置方式如下（分别需要配置 docker 和 containerd 服务）：要在引导时为其他发行版自动启动 Docker 和 Containerd，你可以使用以下命令：

```
$ sudo systemctl enable docker.service # 设置 docker 开机启动
$ sudo systemctl enable containerd.service # 设置 containerd 开机启动
```
如果要禁止docker、containerd开启启动，可以用这个命令：

```
$ sudo systemctl disable docker.service # 禁止 docker 开机启动
$ sudo systemctl disable containerd.service # 禁止 containerd 开机启动
```
### 安装docker-compose
```
sudo yum install epel-release
sudo yum install python3-pip 
sudo pip3 install --upgrade pip 
sudo pip3 install docker-compose  
#这里会报错：ModuleNotFoundError: No module named 'setuptools_rust'
#解决方法：pip3 install -U pip setuptools
 
 
docker-compose --version
```
### 准备一个 Kubernetes 集群
安装完 Docker 之后，我们还需要一个 Kubernetes 集群，来调度 docker 容器。安装 Kubernetes 集群极其复杂，这里选择一种最简单的方式来准备一个 Kubernetes 集群：购买一个腾讯云弹性集群（EKS）。

如果你想自己搭建 Kubernetes 集群，这里建议你购买 3 台腾讯云 CVM 机器，并参照[follow-me-install-kubernetes-cluster](https://github.com/opsnull/follow-me-install-kubernetes-cluster)教程来一步步搭建 Kubernetes 集群，CVM 机器建议的最小配置如下：

![img](https://static001.geekbang.org/resource/image/88/0b/885d2431e7f2d9bcd02b32d33yye930b.jpg?wh=1920x876)


#### EKS 简介
我先简单介绍一下 EKS 是什么。EKS（Elastic Kubernetes Service）即腾讯云弹性容器服务，是腾讯云容器服务推出的无须用户购买节点即可部署工作负载的服务模式。它完全兼容原生的 Kubernetes，支持使用原生方式创建及管理资源，按照容器真实的资源使用量计费。弹性容器服务 EKS 还扩展支持腾讯云的存储及网络等产品，同时确保用户容器的安全隔离，开箱即用。

#### EKS 费用
那它是如何收费呢？EKS 是全托管的 Serverless Kubernetes 服务，不会收取托管的 Master、etcd 等资源的费用。弹性集群内运行的工作负载采用后付费的按量计费模式，费用根据实际配置的资源量按使用时间计算。也就是说：**Kubernetes 集群本身是免费的，只有运行工作负载，消耗节点资源时收费。**


EKS 有 3 种计费模式：预留券、按量计费、竞价模式，这里我建议选择按量计费。按量计费，支持按秒计费，按小时结算，随时购买随时释放，从专栏学习的角度来说，费用是最低的。EKS 会根据工作负载申请的 CPU、内存数值以及工作负载的运行时间来核算费用，具体定价，你可以参考：[定价|弹性容器服务。](https://buy.cloud.tencent.com/price/eks)

这里我通过例子来说明一下费用问题，IAM 应用会部署 4 个 Deployment，每个 Deployment 一个副本：

- iam-apiserver：IAM REST API 服务，提供用户、密钥、策略资源的 CURD 功能的 API 接口。
- iam-authz-server：IAM 资源授权服务，对外提供资源授权接口。
- iam-pump：IAM 数据清洗服务，从 Redis 中获取授权日志，处理后保存在 MongoDB 中。
- iamctl：IAM 应用的测试服务，登陆 iamctl Pod 可以执行 iamctl 命令和 smoke 测试脚本，完成对 IAM 应用的运维和测试。

上述 4 个 Deployment 中的 Pod 配置均为 0.25 核、512Mi 内存。

这里，我们根据 EKS 的费用计算公式 费用 = 相关计费项配置 × 资源单位时间价格 × 运行时间 计算 IAM 部署一天的费用：

```
总费用 = (4 x 1) x (0.25 x 0.12 + 0.5 x 0.05) x 24 = 4.8 元
```
也就是按最低配置部署 IAM 应用，运行一天的费用是 4.8 元（一瓶水的钱，就能学到如何将 IAM 应用部署在 Kubernetes 平台上，很值！）。你可能想这个计算公式里每个数值都代表什么呢？我来解释一下，其中：

(4 x 1)：Kubernetes Pod 总个数（一共是 4 个 Deployment，每个 Pod 1 个副本）。0.25 x 0.12：连续运行 1 小时的 CPU 配置费用。0.5 x 0.05：连续运行 1 小时的内存配置费用。24：24 小时，也即一天。

这里需要注意，为了帮助你节省费用，上述配置都是最低配置。在实际生产环境中，建议的配置如下：

![img](https://static001.geekbang.org/resource/image/1d/35/1d20c7eeb9dba8f53194969b0da5e835.jpg?wh=1920x718)

因为 iam-pump 组件是有状态的，并且目前没有实现抢占机制，所以副本数需要设置为 1。另外，Intel 按量计费的配置费用见下图：

![img](https://static001.geekbang.org/resource/image/64/59/64a5f89f4cbea95e50691455a06b1e59.png?wh=1372x226)

在这里有个很重要的事情提醒你：学完本节课，销毁这些 Deployment，避免被继续扣费。建议腾讯云账户余额不要超过 50 元。

#### 申请 EKS 集群
了解了 EKS 以及费用相关的问题，接下来我们看看如何申请 EKS 集群。你可以通过以下 5 步来申请 EKS 集群。在正式申请前，请先确保腾讯云账户有大于 10 元的账户余额，否则在创建和使用 EKS 集群的过程中可能会因为费用不足而报错。

创建腾讯云弹性集群
具体步骤如下：首先，登录容器服务控制台，选择左侧导航栏中的【弹性集群】。然后，在页面上方选择需创建弹性集群的地域，并单击【新建】。在“创建弹性集群”页面，根据以下提示设置集群信息。如下图所示：

![img](https://static001.geekbang.org/resource/image/37/8c/3740a98a6140b9c85517bdd27f30268c.png?wh=1920x950)

页面中各选择项的意思，我来给你解释一下：

- 集群名称：创建的弹性集群名称，不超过 60 个字符。
- Kubernetes 版本：弹性集群支持 1.12 以上的多个 Kubernetes 版本选择，建议选择最新的版本。
- 所在地域：建议你根据所在地理位置选择靠近的地域，可降低访问延迟，提高下载速度。
- 集群网络：已创建的弹性集群 VPC 网络，你可以选择私有网络中的子网用于弹性集群的容器网络，详情请见 私有网络（VPC） 。
- 容器网络：为集群内容器分配在容器网络地址范围内的 IP 地址。弹性集群的 Pod 会直接占用 VPC 子网 IP，请尽量选择 IP 数量充足且与其他产品使用无冲突的子网。
- Service CIDR：集群的 ClusterIP Service 默认分配在所选 VPC 子网中，请尽量选择 IP 数量充足且与其他产品使用无冲突的子网。
- 集群描述：创建集群的相关信息，该信息将显示在“集群信息”页面。

设置完成后，单击【完成】即可开始创建，可在“弹性集群”列表页面查看集群的创建进度。等待弹性集群创建完成，创建完成后的弹性集群页面如下图所示：

![img](https://static001.geekbang.org/resource/image/1c/3e/1c533956986531f0245b27864f17dc3e.png?wh=1920x376)

我们创建的弹性集群 ID 为 cls-dc6sdos4。

开启外网访问
如果想访问 EKS 集群，需要先开启 EKS 的外网访问能力，开启方法如下：登录容器服务控制台 -> 选择左侧导航栏中的【弹性集群】 -> 进入 cls-dc6sdos4 集群的详情页中 -> 选择【基本信息】 -> 点击【外网访问】按钮。如下图所示：

![img](https://static001.geekbang.org/resource/image/23/c6/235679797980de87432a0d4b05dd83c6.png?wh=1920x764)

这里要注意，开启外网访问时，为了安全，需要设置允许访问 kube-apiserver 的 IP 段。为了避免不必要的错误，外网访问地址我们设置为0.0.0.0/0  。如下图所示：

![img](https://static001.geekbang.org/resource/image/ea/21/ea627942e5a432401e5959ee90c06221.png?wh=920x441)

注意，只有测试时才可这么设置为 0.0.0.0/0 ，如果是生产环境，建议严格限制可以访问 kube-apiserver 的来源 IP。

安装 kubectl 命令行工具
如果要访问 EKS（标准的 Kubernetes 集群），比较高效的方式是通过 Kubernetes 提供的命令行工具 kubectl 来访问。所以，还需要安装 kubectl 工具。

安装方式如下：
```
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
$ mkdir -p $HOME/bin
$ mv kubectl $HOME/bin
$ chmod +x $HOME/bin/kubectl
```
具体可参考安装和设置 kubectl。你可以通过以下命令来配置 kubectl 的 bash 自动补全：

```
$ kubectl completion bash > $HOME/.kube-completion.bash
$ echo 'source $HOME/.kube-completion.bash' >> ~/.bashrc
$ bash
```

下载并安装 kubeconfig
安装完 kubectl 工具之后，需要配置 kubectl 所读取的配置文件。这里注意，在上一步，我们开启了外网访问，开启后 EKS 会生成一个 kubeconfig 配置（ kubeconfig 即为 kubectl 的配置文件）。我们可以从页面下载并安装。在弹性集群的基本信息页面，点击【复制】按钮，复制 kubeconfig 文件内容，如下图所示：

![img](https://static001.geekbang.org/resource/image/bc/bf/bc314604334dd56b7337a905c9b6bfbf.png?wh=1775x731)

复制后，将粘贴板的内容保存在$HOME/.kube/config文件中。需要先执行mkdir -p $HOME/.kube创建.kube目录，再将粘贴版中的内容写到 config 文件中。你可以通过以下命令，来测试 kubectl 工具是否成功安装和配置：

```
$ kubectl get nodes
NAME                    STATUS   ROLES    AGE    VERSION
eklet-subnet-lowt256k   Ready    <none>   2d1h   v2.5.21
```
如果输出了 Kubernetes 的 eklet 节点，并且节点状态为 Ready，说明 Kubernetes 集群运行正常，并且 kubectl 安装和配置正确。

EKS 集群开通集群内服务访问外网能力
因为 IAM 应用中的数据库：MariaDB、Redis、MongoDB 可能需要通过外网访问，所以还需要开通 EKS 中 Pod 访问外网的能力。EKS 支持通过配置 NAT 网关 和 路由表 来实现集群内服务访问外网。具体开启步骤，需要你查看腾讯云官方文档：通过 NAT 网关访问外网。

在开通过程中有以下两点需要你注意：在创建指向 NAT 网关的路由表步骤中，目的端要选择：0.0.0.0/0。在关联子网至路由表步骤中，只关联创建 EKS 集群时选择的子网。

如果你的数据库需要通过外网访问，这里一定要确保 EKS 集群成功开通集群内服务访问外网能力，否则部署 IAM 应用时会因为访问不了数据库而失败。

### 安装 IAM 应用
上面，我们开通了镜像仓库、安装了 Docker 引擎、安装和配置了 Kubernetes 集群，那么接下来，我们就来看下如何将 IAM 应用部署到 Kubernetes 集群中。

假设 IAM 项目仓库根目录路径为 $IAM_ROOT，具体安装步骤如下：

配置scripts/install/environment.sh
scripts/install/environment.sh文件中包含了各类自定义配置。你可能需要配置跟数据库相关的配置（当然，也可以都使用默认值）：
- MariaDB 配置：environment.sh 文件中以MARIADB_开头的变量。
- Redis 配置：environment.sh 文件中以REDIS_开头的变量。
- MongoDB 配置：environment.sh 文件中以MONGO_开头的变量。

其他配置，使用默认值。

创建 IAM 应用的配置文件
```
$ cd ${IAM_ROOT}
$ make gen.defaultconfigs # 生成iam-apiserver、iam-authz-server、iam-pump、iamctl组件的默认配置文件
$ make gen.ca # 生成 CA 证书
```
上述命令会将 IAM 的配置文件存放在这个${IAM_ROOT}/_output/configs/目录下。

创建 IAM 命名空间
我们将 IAM 应用涉及到的各类资源，都创建在iam命名空间中。将 IAM 资源创建在独立的命名空间中，不仅方便维护，还可以有效避免影响其他 Kubernetes 资源。

```
$ kubectl create namespace iam
```
将 IAM 各服务的配置文件，以 ConfigMap 资源的形式保存在 Kubernetes 集群中

```
$ kubectl -n iam create configmap iam --from-file=${IAM_ROOT}/_output/configs/
$ kubectl -n iam get configmap iam
NAME   DATA   AGE
iam    4      13s
```
执行kubectl -n iam get configmap iam命令，可以成功获取创建的iam configmap。如果你觉得每次执行kubectl命令都要指定-n iam选项很繁琐，你可以使用以下命令，将kubectl上下文环境中的命名空间指定为iam。设置后，执行kubectl命令，默认在iam命名空间下执行：

```
$ kubectl config set-context `kubectl config current-context` --namespace=iam
```

将 IAM 各服务使用的证书文件，以 ConfigMap 资源的形式创建在 Kubernetes 集群中


```
$ kubectl -n iam create configmap iam-cert --from-file=${IAM_ROOT}/_output/cert
$ kubectl -n iam get configmap iam-cert
NAME       DATA   AGE
iam-cert   14     12s
```
执行kubectl -n iam get configmap iam-cert命令，可以成功获取创建的iam-cert configmap。

创建镜像仓库访问密钥
在准备阶段，我们开通了腾讯云镜像仓库服务（访问地址为 ccr.ccs.tencentyun.com），并创建了用户10000099xxxx，其密码为iam59!z$。接下来，我们就可以创建 docker-registry secret。Kubernetes 在下载 Docker 镜像时，需要 docker-registry secret 来进行认证。创建命令如下：

```
$ kubectl -n iam create secret docker-registry ccr-registry --docker-server=ccr.ccs.tencentyun.com --docker-username=10000099xxxx --docker-password='iam59!z$'
```

创建 Docker 镜像，并 Push 到镜像仓库
将镜像 Push 到 CCR 镜像仓库，需要确保你已经登录到腾讯云 CCR 镜像仓库，如果没登录，可以执行以下命令来登录：

```
$ docker login --username=[username] ccr.ccs.tencentyun.com
```
执行 make push 命令构建镜像，并将镜像 Push 到 CCR 镜像仓库：

```
$ make push REGISTRY_PREFIX=ccr.ccs.tencentyun.com/marmotedu VERSION=v1.1.0
```
上述命令，会构建 iam-apiserver-amd64、iam-authz-server-amd64、iam-pump-amd64、iamctl-amd64 四个镜像，并将这些镜像 Push 到腾讯云镜像仓库的marmotedu命名空间下。构建的镜像如下：

```
$ docker images|grep marmotedu
ccr.ccs.tencentyun.com/marmotedu/iam-pump-amd64           v1.1.0   e078d340e3fb        10 seconds ago      244MB
ccr.ccs.tencentyun.com/marmotedu/iam-apiserver-amd64      v1.1.0   5e90b67cc949        2 minutes ago       239MB
ccr.ccs.tencentyun.com/marmotedu/iam-authz-server-amd64   v1.1.0   6796b02be68c        2 minutes ago       238MB
ccr.ccs.tencentyun.com/marmotedu/iamctl-amd64             v1.1.0   320a77d525e3        2 minutes ago       235MB
```

修改 ${IAM_ROOT}/deployments/iam.yaml 配置
这里请你注意，如果在上一个步骤中，你构建的镜像 tag 不是 v1.1.0 ，那么你需要修改 ${IAM_ROOT}/deployments/iam.yaml 文件，并将 iam-apiserver-amd64、 iam-authz-server-amd64、 iam-pump-amd64、iamctl-amd64 镜像的 tag 修改成你构建镜像时指定的 tag。


部署 IAM 应用

```
$ kubectl -n iam apply -f ${IAM_ROOT}/deployments/iam.yaml
```

执行上述命令，会在iam命令空间下，创建一系列 Kubernetes 资源，可以使用以下命令，来获取这些资源的状态：

```
$ kubectl -n iam get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/iam-apiserver-d8dc48596-wkhpl       1/1     Running   0          94m
pod/iam-authz-server-6bc899c747-fbpbk   1/1     Running   0          94m
pod/iam-pump-7dcbfd4f59-2w9vk           1/1     Running   0          94m
pod/iamctl-6fc46b8ccb-gs62l             1/1     Running   1          98m

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/iam-apiserver      ClusterIP   192.168.0.174   <none>        8443/TCP,8080/TCP,8081/TCP   101m
service/iam-authz-server   ClusterIP   192.168.0.76    <none>        9443/TCP,9090/TCP            101m
service/iam-pump           ClusterIP   192.168.0.155   <none>        7070/TCP                     101m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/iam-apiserver      1/1     1            1           101m
deployment.apps/iam-authz-server   1/1     1            1           101m
deployment.apps/iam-pump           1/1     1            1           101m
deployment.apps/iamctl             1/1     1            1           101m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/iam-apiserver-d8dc48596       1         1         1       101m
replicaset.apps/iam-authz-server-6bc899c747   1         1         1       101m
replicaset.apps/iam-pump-7dcbfd4f59           1         1         1       101m
replicaset.apps/iamctl-6fc46b8ccb             1         1         1       101m
```
我们看到pod/iam-apiserver-d8dc48596-wkhpl、pod/iam-authz-server-6bc899c747-fbpbk、pod/iam-pump-7dcbfd4f59-2w9vk、pod/iamctl-6fc46b8ccb-gs62l 4 个 Pod 都处在Running状态，说明服务都成功启动。

### 测试 IAM 应用
我们在iam命令空间下创建了一个测试 Deployment iamctl。你可以登陆iamctl Deployment 所创建出来的 Pod，执行一些运维操作和冒烟测试。登陆命令如下：

```
$ kubectl -n iam exec -it `kubectl -n iam get pods -l app=iamctl | awk '/iamctl/{print $1}'` -- bash
```
登陆到iamctl-xxxxxxxxxx-xxxxx Pod 中后，就可以执行运维操作和冒烟测试了。

运维操作在 iamctl 容器中，你可以使用 iamctl 工具提供的各类功能，iamctl 以子命令的方式对外提供功能。命令执行效果见下图：

![img](https://static001.geekbang.org/resource/image/44/7e/4460dfd5cd7f7fae5cbb0c64e605367e.png?wh=1920x433)
冒烟测试

```
# cd /opt/iam/scripts/install
# ./test.sh iam::test::smoke
```
如果./test.sh iam::test::smoke命令，打印的输出中，最后一行为congratulations, smoke test passed!字符串，说明 IAM 应用安装成功。如下图所示：

![img](https://static001.geekbang.org/resource/image/b9/2e/b9688e6b0609571a06401da412b63d2e.png?wh=1920x603)


### 销毁 EKS 集群及其资源
好了，到这里，你已经成功在 EKS 集群中部署了 IAM 应用，EKS 的使命也就完成了。接下来，为避免账户被持续扣费，需要删除 EKS 内的资源和集群。

删除 EKS 内创建的 IAM 资源

```
$ kubectl delete namespace iam
```
因为删除 Namespace 会删除 Namespace 下的所有资源，所以上述命令执行时间会久点。

删除 EKS 集群
首先，登录容器服务控制台，选择左侧导航栏中的【弹性集群】。然后，选择创建的 EKS 集群：cls-dc6sdos4，点击最右侧的【删除】按钮，删除 EKS 集群。如下图所示：

![img](https://static001.geekbang.org/resource/image/05/95/05db9d9aca0a2a06e0a9a9faf37c4695.png?wh=1920x455)


### 总结
云原生架构设计中，需要将 IAM 应用部署到 Kubernetes 集群中。所以，首先需要你准备一个 Kubernetes 集群。你可以自己购买腾讯云 CVM 机器搭建 Kubernetes 集群，但这种方式费用高、操作复杂。所以，我建议你直接申请一个 EKS 集群来部署 IAM 应用。EKS 集群是一个标准的 Kubernetes 集群，可以快速申请，并免运维。EKS 集群只收取实际的资源使用费用。在专栏学习过程中，部署 IAM 应用期间产生的资源使用费用其实是很低的，所以推荐使用这种方式来部署 IAM 应用。

有了 Kubernetes 集群，就可以直接通过以下命令来部署整个 IAM 应用：

```
$ kubectl -n iam apply -f ${IAM_ROOT}/deployments/iam.yaml
```
应用部署起来之后，我们可以登陆到iamctl-xxxxxxxxxx-xxxxxPod，并执行以下命令来测试整个 IAM 应用是否被成功部署：

```
# cd /opt/iam/scripts/install
# ./test.sh iam::test::smoke
```

## 49 | 服务编排（上）：Helm服务编排基础知识

我们将应用部署在 Kubernetes 时，可能需要创建多个服务。我就见过一个包含了 40 多个微服务的超大型应用，每个服务又包含了多个 Kubernetes 资源，比如 Service、Deployment、StatefulSet、ConfigMap 等。相同的应用又要部署在不同的环境中，例如测试环境、预发环境、现网环境等，也就是说应用的配置也不同。

对于一个大型的应用，如果基于 YAML 文件一个一个地部署 Kubernetes 资源，是非常繁琐、低效的，而且这些 YAML 文件维护起来极其复杂，还容易出错。那么，有没有一种更加高效的方式？比如，像 Docker 镜像一样，将应用需要的 Kubernetes 资源文件全部打包在一起，通过这个包来整体部署和管理应用，从而降低应用部署和维护的复杂度。

答案是有。我们可以通过 Helm Chart 包来管理这些 Kubernetes 文件，并通过helm命令，基于 Chart 包来创建和管理应用。接下来，我就来介绍下 Helm 的基础知识，并给你演示下如何基于 Helm 部署 IAM 应用。

### Helm 基础知识介绍
Helm 目前是 Kubernetes 服务编排事实上的标准。Helm 提供了多种功能来支持 Kubernetes 的服务编排，例如 helm 命令行工具、Chart 包、Chart 仓库等。下面，我就来详细介绍下。

#### Helm 是什么？
Helm 是 Kubernetes 的包管理器，类似于 Python 的 pip ，centos 的 yum 。Helm 主要用来管理 Chart 包。Helm Chart 包中包含一系列 YAML 格式的 Kubernetes 资源定义文件，以及这些资源的配置，可以通过 Helm Chart 包来整体维护这些资源。

Helm 也提供了一个helm命令行工具，该工具可以基于 Chart 包一键创建应用，在创建应用时，可以自定义 Chart 配置。应用发布者可以通过 Helm 打包应用、管理应用依赖关系、管理应用版本，并发布应用到软件仓库；对于使用者来说，使用 Helm 后不需要编写复杂的应用部署文件，可以非常方便地在 Kubernetes 上查找、安装、升级、回滚、卸载应用程序。

Helm 最新的版本是 v3，Helm3 以 Helm2 的核心功能为基础，对 Chart repo、发行版管理、安全性和 library Charts 进行了改进。和 Helm2 比起来，Helm3 最明显的变化是删除了 Tiller（Helm2 是一种 Client-Server 结构，客户端称为 Helm，服务器称为 Tiller）。Helm3 还新增了一些功能，并废弃或重构了 Helm2 的部分功能，与 Helm2 不再兼容。此外，Helm3 还引入了一些新的实验功能，包括 OCI 支持。

Helm3 架构图如下：

![img](https://static001.geekbang.org/resource/image/9b/59/9bb9e2d495d8fbe5eab4d02d407c8059.jpg?wh=1920x1083)

上面的架构图中，核心是 Helm Client（helm命令）和 Helm Chart 包。helm命令可以从Chart Repository中下载 Helm Chart 包，读取kubeconfig文件，并构建 kube-apiserver REST API 接口的 HTTP 请求。通过调用 Kubernetes 提供的 REST API 接口，将 Chart 包中包含的所有以 YAML 格式定义的 Kubernetes 资源，在 Kubernetes 集群中创建。

这些资源以 Release 的形式存在于 Kubernetes 集群中，每个 Release 又包含多个 Kubernetes 资源，例如 Deployment、Pod、Service 等。

#### Helm 中的三大基本概念
要学习和使用 Helm，一定要了解 Helm 中的三大基本概念，Helm 的所有操作基本都是围绕着这些概念来进行的。下面我来介绍下 Helm 的三大基本概念。

- Chart： 代表一个 Helm 包。它包含了在 Kubernetes 集群中运行应用程序、工具或服务所需的所有 YAML 格式的资源定义文件。
- Repository（仓库）： 它是用来存放和共享 Helm Chart 的地方，类似于存放源码的 GitHub 的 Repository，以及存放镜像的 Docker 的 Repository。
- Release：它是运行在 Kubernetes 集群中的 Chart 的实例。一个 Chart 通常可以在同一个集群中安装多次。每一次安装都会创建一个新的 Release。

#### 我们为什么要使用 Helm？
现在你对 Helm 已经有了一定了解，这里我再来详细介绍下为什么要使用 Helm。先来看下传统的应用部署模式：

![img](https://static001.geekbang.org/resource/image/c1/c4/c1f54e347b5db7850b57815abed99ec4.jpg?wh=1920x966)
我们有测试环境、预发环境、现网环境三个环境，每个环境中部署一个应用 A，应用 A 中包含了多个服务，每个服务又包含了自己的配置，不同服务之间的配置有些是共享的，例如配置A。每个服务由一个复杂的 Kubernetes YAML 格式的文件来定义并创建，可以看到如果靠传统的方式，去维护这些 YAML 格式文件，并在不同环境下使用不同的配置去创建应用，是一件非常复杂的工作，并且后期 YAML 文件和 Kubernetes 集群中部署应用的维护都很复杂。随着微服务规模越来越大，会面临以下挑战：

微服务化服务数量急剧增多，给服务管理带来了极大的挑战。服务数量急剧增多，增加了管理难度，对运维部署是一种挑战。服务数量的增多，对服务配置管理也提出了更高的要求。随着服务数量增加，服务依赖关系也变得更加复杂，服务依赖关系的管理难度增大。在环境信息管理方面，在新环境快速部署一个复杂应用变得更加困难。

所以，我们需要一种更好的方式，来维护和管理这些 YAML 文件和 Kubernetes 中部署的应用。Helm 可以帮我们解决上面这些问题。接下来，我们来看下 Helm 是如何解决这些问题的。

在 Helm 中，可以理解为主要包含两类文件：模板文件和配置文件。模板文件通常有多个，配置文件通常有一个。Helm 的模板文件基于text/template模板文件，提供了更加强大的模板渲染能力。Helm 可以将配置文件中的值渲染进模板文件中，最终生成一个可以部署的 Kubernetes YAML 格式的资源定义文件，如下图所示：

![img](https://static001.geekbang.org/resource/image/ff/86/ffcc4eaf4071e19e2e0d317b1c536486.png?wh=1920x936)

上图中，我们将以下配置渲染进了模板中，生成了 Kubernetes YAML 文件：

```
replicas: 2
tag: latest
common:
    username: colin
    password: iam1234
```
所以在 Helm 中，部署一个应用可以简化为Chart模板（多个服务） + Chart配置 -> 应用，如下图所示：

![img](https://static001.geekbang.org/resource/image/bb/6b/bb87b8562102525d4c2yy0b0314ac46b.jpg?wh=1920x995)
Chart 模板一个应用只用编写一次，可以重复使用。在部署时，可以指定不同的配置，从而将应用部署在不同的环境中，或者在同一环境中部署不同配置的应用。

### Helm 基本操作实战
上面，我介绍了 Helm 的一些基础知识，这里我们再来学习下如何使用 Helm 对应用进行生命周期管理。

#### 前置条件
在开始之前，你需要确保你有一个可以使用的 Kubernetes 集群。目前最方便快捷、最经济的方式是申请一个腾讯云 EKS 集群。至于如何申请和访问，你可以参考 48 讲 “准备一个 Kubernetes 集群”部分的教程。这里再提醒下，**用完集群后，记得删除集群资源，免得被持续扣费。**


#### 安装 Helm
Helm 提供了多种安装方式，在能连通外网的情况下，可以通过脚本来安装，安装命令如下：

```
$ mkdir -p $HOME/bin
$ wget https://get.helm.sh/helm-v3.6.3-linux-amd64.tar.gz
$ tar -xvzf helm-v3.6.3-linux-amd64.tar.gz
$ mv linux-amd64/helm $HOME/bin
$ chmod +x $HOME/bin/helm
$ helm version
version.BuildInfo{Version:"v3.6.3", GitCommit:"d506314abfb5d21419df8c7e7e68012379db2354", GitTreeState:"clean", GoVersion:"go1.16.5"}
```
如果执行helm version可以成功打印出 helm 命令的版本号，说明 Helm 安装成功。Helm 各版本安装包地址见 [Helm Releases](https://github.com/helm/helm/releases)。

安装完helm命令后，可以安装helm命令的自动补全脚本。假如你用的 shell 是bash，安装方法如下：

```
$ helm completion bash > $HOME/.helm-completion.bash
$ echo 'source $HOME/.helm-completion.bash' >> ~/.bashrc
$ bash
```
执行 helm comp< TAB >，就会自动补全为helm completion。


### Helm 快速入门
你可以通过以下六个步骤，来快速创建一个 Chart 应用。

#### 第一步，初始化一个 Helm Chart 仓库。
安装完 Helm 之后，就可以使用 helm 命令添加一个 Chart 仓库。类似于用来托管 Docker 镜像的 DockerHub、用来托管代码的 GitHub，Chart 包也有一个托管平台，当前比较流行的 Chart 包托管平台是Artifact Hub。

Artifact Hub 上有很多 Chart 仓库，我们可以添加需要的 Chart 仓库，这里我们添加 BitNami 提供的 Chart 仓库：

```
$ helm repo add bitnami https://charts.bitnami.com/bitnami # 添加 Chart Repository
$ helm repo list # 查看添加的 Repository 列表
```
添加完成后，我们可以通过helm search命令，来查询需要的 Chart 包。helm search支持两种不同的查询方式，这里我来介绍下。
- helm search repo<keyword>：从你使用 helm repo add 添加到本地 Helm 客户端中的仓库里查找。该命令基于本地数据进行搜索，无需连接外网。
- helm search hub<keyword>：从 Artifact Hub 中查找并列出 Helm Charts。 Artifact Hub 中存放了大量的仓库。

Helm 搜索使用模糊字符串匹配算法，所以你可以只输入名字的一部分。下面是一个helm search的示例：

```
$ helm search repo bitnami
NAME                                          CHART VERSION  APP VERSION    DESCRIPTION
bitnami/bitnami-common                        0.0.9          0.0.9          DEPRECATED Chart with custom templates used in ...
bitnami/airflow                               10.2.8         2.1.2          Apache Airflow is a platform to programmaticall...
bitnami/apache                                8.6.1          2.4.48         Chart for Apache HTTP Server
bitnami/argo-cd                               1.0.2          2.0.5          Declarative, GitOps continuous delivery tool fo...
bitnami/aspnet-core                           1.3.14         3.1.18         ASP.NET Core is an open-source framework create...
bitnami/cassandra                             8.0.2          4.0.0          Apache Cassandra is a free and open-source dist...
bitnami/cert-manager                          0.1.15         1.5.1          Cert Manager is a Kubernetes add-on to automate...
# ... and many more
```
第二步，安装一个示例 Chart。

查询到自己需要的 Helm Chart 后，就可以通过helm install命令来安装一个 Chart。helm install支持从多种源进行安装：

- Chart 的 Repository。
- 本地的 Chart Archive，例如helm install foo foo-1.0.0.tgz。
- 一个未打包的 Chart 路径，例如helm install foo path/to/foo。
- 一个完整的 URL，例如helm install foo https://example.com/charts/foo-1.0.0.tgz。

这里，我们选择通过bitnami/mysql Chart 包来安装一个 MySQL 应用。你可以执行 helm show chart bitnami/mysql 命令，来简单了解这个 Chart 的基本信息。 或者，你也可以执行 helm show all bitnami/mysql，获取关于该 Chart 的所有信息。

接下来，就可以使用helm install命令来安装这个 Chart 包了。安装命令如下：

```
$ helm repo update              # Make sure we get the latest list of charts
$ helm install bitnami/mysql --generate-name
NAME: mysql-1629528555
LAST DEPLOYED: Sat Aug 21 14:49:19 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES: ...
```
在上面的例子中，我们通过安装bitnami/mysql这个 Chart，创建了一个mysql-1629528555 Release。--generate-name参数告诉 Helm 自动为这个 Release 命名。在安装过程中，Helm 客户端会打印一些有用的信息，包括哪些资源已经被创建，Release 当前的状态，以及你是否还需要执行额外的配置步骤。例如，从上述例子的输出中，你可以获取到数据库的 Root 密码、登陆方式、更新方式等信息。

安装完之后，你可以使用 helm status 来追踪 Release 的状态。每当你执行 helm install 的时候，都会创建一个新的发布版本。所以一个 Chart 在同一个集群里面可以被安装多次，每一个都可以被独立地管理和升级。

helm install命令会将 templates 渲染成最终的 Kubernetes 能够识别的 YAML 格式，然后安装到 Kubernetes 集群中。helm install 功能非常强大，想了解更多功能，你可以参考这个指南：[使用 Helm](https://helm.sh/zh/docs/intro/using_helm/)。

#### 第三步，安装前自定义 Chart。
上一步中的安装方式只会使用 Chart 的默认配置选项，很多时候我们需要自定义 Chart 来指定我们想要的配置。使用 helm show values 可以查看 Chart 中的可配置选项：

```
$ helm show values bitnami/mysql # 为了方便展示，我删除了 `helm show values`输出中的`#`注释
# ... and many more
architecture: standalone
auth:
  rootPassword: ""
  database: my_database
  username: ""
  password: ""
  replicationUser: replicator
  replicationPassword: ""
  existingSecret: ""
  forcePassword: false
  usePasswordFiles: false
  customPasswordFiles: {}
initdbScripts: {}
# ... and many more
```

然后，你可以使用 YAML 格式的文件，覆盖上述任意配置项，并在安装过程中使用该文件。

```
$ echo '{auth.database: iam, auth.username: iam, auth.password: iam59!z$}' > values.yaml
$ helm install bitnami/mysql -f values.yaml --generate-name
```

上述命令将为 MySQL 创建一个名称为 iam 的默认用户，密码为iam59!z$，并且授予该用户访问新建的 iam 数据库的权限。Chart 中的其他默认配置保持不变。安装过程中，有两种传递配置数据的方式。

-f, --values：使用 YAML 文件覆盖配置。可以指定多次，优先使用最右边的文件。--set：通过命令行的方式对指定配置项进行覆盖。

如果同时使用两种方式，则 --set 中的值会被合并到 --values 中，但是 --set 中的值优先级更高。在--set中覆盖的内容会被保存在 ConfigMap 中。你可以通过 helm get values <release-name> 来查看指定 Release 中 --set 设置的值，也可以通过运行 helm upgrade 并指定 --reset-values 字段，来清除 --set中设置的值。

**这里我讲解下--set的格式和限制**。--set 选项使用0或多个key-value 对。最简单的用法类似于--set name=value，等价于下面这个 YAML 格式：

```yaml
name: value
```
多个值之间使用逗号分割，因此--set a=b,c=d 的 YAML 表示是：

```yaml
a: b
c: d
```
--set还支持更复杂的表达式。例如，--set outer.inner=value 被转换成了：

```yaml
outer:
  inner: value
```
列表使用花括号{}来表示。例如，--set name={a, b, c} 被转换成了：


```yaml
name:
  - a
  - b
  - c
```
从 2.5.0 版本开始，我们可以使用数组下标的语法来访问列表中的元素了。例如 --set servers[0].port=80 就变成了：

```yaml
servers:
  - port: 80

```
多个值也可以通过这种方式来设置。--set servers[0] [0].host=``marmotedu 变成了：


```yaml
servers:
  - port: 80
    host: marmotedu
```
如果需要在 --set 中使用特殊字符，你可以使用反斜线来进行转义，比如--set name=value1\,value2 就变成了：

```yaml
name: "value1,value2"
```
如果是深层嵌套的数据结构，可能很难用--set 来表达，更多内容你可以参考 Values 文件。

#### 第四步，查看当前集群安装了哪些 Release。
通过helm list可以查看当前集群、当前 Namespace 下安装的 Release 列表：

```
$ helm list
NAME              NAMESPACE  REVISION  UPDATED                                  STATUS    CHART        APP VERSION
mysql-1629528555  default    1         2021-08-21 14:49:19.101935218 +0800 CST  deployed  mysql-8.8.4  8.0.26
mysql-1629529348  default    1         2021-08-21 15:02:32.079969128 +0800 CST  deployed  mysql-8.8.4  8.0.26
```
可以看到，我们创建了两个 Release，这些 Release 位于default命名空间中。上述命令，也列出了 Release 的更新时间、状态、Chart 的版本等。

#### 第五步，升级 Release，并且在失败时恢复。
部署完应用之后，后续还可能升级应用，可以通过helm upgrade命令来升级应用。升级操作会基于已有的 Release，根据提供的信息进行升级。Helm 在更新时，只会变更有更改的内容。

例如，这里我们升级mysql-1629528555，变更它的 Root 密码：

```
$ helm upgrade mysql-1629528555 bitnami/mysql --set auth.rootPassword='iam59!z$'
```
在上面的例子中，mysql-1629528555 这个 Release 使用相同的 Chart 进行升级，但使用了一个新的rootPassword配置。我们可以使用 helm get values 命令，来看看配置值是否真的生效了：

```
$ helm get values mysql-1629528555
USER-SUPPLIED VALUES:
auth:
  rootPassword: iam59!z$

```
可以看到rootPassword 的新值已经被部署到集群中了。假如发布失败，我们也很容易通过 helm rollback [RELEASE] [REVISION] 命令，回滚到之前的发布版本。

```
$ helm rollback mysql-1629528555 1
```
上面这条命令将我们的mysql-1629528555回滚到了它最初的版本。Release 版本其实是一个增量修订（revision）。 每当发生了一次安装、升级或回滚操作，revision 的值就会加1。第一次 revision 的值永远是1。我们可以使用 helm history [RELEASE] 命令来查看一个特定 Release 的修订版本号：

```
$ helm history mysql-1629528555
REVISION  UPDATED                   STATUS      CHART        APP VERSION  DESCRIPTION
1         Sat Aug 21 14:49:19 2021  superseded  mysql-8.8.4  8.0.26       Install complete
2         Sat Aug 21 15:14:45 2021  deployed    mysql-8.8.4  8.0.26       Upgrade complete
```
你还可以指定一些其他的选项，来自定义 Helm 在安装、升级、回滚期间的行为。这里，我介绍一些常用的参数，供你参考。
- --timeout：一个 Go duration 类型的值，用来表示等待 Kubernetes 命令完成的超时时间，默认值为 5m0s。
- --no-hooks：不运行当前命令的钩子。
- --wait：表示必须要等到所有的 Pods 都处于 ready 状态、PVC 都被绑定、Deployments 处在 ready 状态的 Pods 个数达到最小值（Desired 减去 maxUnavailable），才会标记该 Release 为成功。最长等待时间由 --timeout 值指定。如果达到超时时间，Release 将被标记为 FAILED。


这里需要注意，当 Deployment 的 replicas 被设置为 1，但其滚动升级策略中的maxUnavailable 没有被设置为0时，--wait 将返回就绪，因为已经满足了最小 ready Pod 数。

#### 第六步，卸载 Release。
你可以使用helm uninstall命令卸载一个 Release：


```
$ helm  uninstall mysql-1629528555
```
上述命令会从 Kubernetes 卸载 mysql-1629528555， 它将删除和该版本关联的所有资源（Service、Deployment、Pod、ConfigMap 等），包括该 Release 的所有版本历史。如果你在执行 helm uninstall 的时候提供--keep-history 选项， Helm 将会保存版本历史。 你可以通过helm status命令查看该版本的信息：


```
$ helm status mysql-1629528555
Status: UNINSTALLED
...
```
因为 --keep-history 选项会让 Helm 跟踪你的版本（即使你卸载了它们），所以你可以审计集群历史，甚至使用 helm rollback 回滚版本。

### Helm 命令
上面我介绍了 Helm 的一些命令的用法，如果你想查看 Helm 提供的所有命令，可以执行helm help。或者，你也可以执行helm <subcommand> -h来查看某个子命令的用法，例如：
```
$ helm get -h

This command consists of multiple subcommands which can be used to
get extended information about the release, including:

- The values used to generate the release
- The generated manifest file
- The notes provided by the chart of the release
- The hooks associated with the release

Usage:
  helm get [command]
# ... and many more
```
我整理了一份命令列表，供你参考：

![img](https://static001.geekbang.org/resource/image/c4/bb/c4fa82cf7bf7fc5c98314419b1e0febb.png?wh=1920x4212)

上面这些命令中，有些提供了子命令和命令行参数，具体你可以执行helm <subcommand> -h来查看。

### 总结
今天，我介绍了 Helm 的基础知识，并给你演示了如何基于 Helm 部署 IAM 应用。当一个应用包含了很多微服务时，手动在 Kubernetes 集群中部署、升级、回滚这些微服务是一件非常复杂的工作。这时候，我们就需要一个服务编排方案来编排这些服务，从而提高服务部署和维护的效率。


目前业界提供了多种服务编排方案，其中最流行的是 Helm，Helm 已经成为一个事实上的 Kubernetes 服务编排标准。在 Helm 中，有 Chart、Repository 和 Release 三大基本概念。Chart 代表一个 Helm 包，里面包含了运行 Kubernetes 应用需要的所有资源定义 YAML 文件；Repository 是 Chart 仓库，用来存放和共享 Helm Chart；Release 是运行在 Kubernetes 集群中的 Chart 的实例。

我们可以通过   helm install [NAME] [CHART] [flags] 来安装一个 Chart 包；通过 helm upgrade [RELEASE] [CHART] [flags] 来更新一个 Helm Release；通过 helm uninstall RELEASE_NAME [...] [flags] 来卸载一个 Helm Release。另外，helm 命令行工具还提供了其他的功能，你可以再回顾一遍。

## 50 | 服务编排（下）：基于Helm的服务编排部署实战

上一讲，我介绍了 Helm 的基础知识，并带着你部署了一个简单的应用。掌握 Helm 的基础知识之后，今天我们就来实战下，一起通过 Helm 部署一个 IAM 应用。通过 Helm 部署 IAM 应用，首先需要制作 IAM Chart 包，然后通过 Chart 包来一键部署 IAM 应用。在实际开发中，我们需要将应用部署在不同的环境中，所以我也会给你演示下如何在多环境中部署 IAM 应用。

### 制作 IAM Chart 包
在部署 IAM 应用之前，我们首先需要制作一个 IAM Chart 包。我们假设 IAM 项目源码根目录为${IAM_ROOT}，进入 ${IAM_ROOT}/deployments目录，在该目录下创建 Chart 包。具体创建流程分为四个步骤，下面我来详细介绍下。

第一步，创建一个模板 Chart。Chart 是一个组织在文件目录中的集合，目录名称就是 Chart 名称（没有版本信息）。你可以看看这个 [Chart 开发指南](https://helm.sh/zh/docs/topics/charts/) ，它介绍了如何开发你自己的 Chart。不过，这里你也可以使用 helm create 命令来快速创建一个模板 Chart，并基于该 Chart 进行修改，得到你自己的 Chart。创建命令如下：

```
$ helm create iam
```
helm create iam会在当前目录下生成一个iam目录，里面存放的就是 Chart 文件。Chart 目录结构及文件如下：

```
$ tree -FC iam/
├── charts/                            # [可选]: 该目录中放置当前Chart依赖的其他Chart
├── Chart.yaml                         # YAML文件，用于描述Chart的基本信息，包括名称版本等
├── templates/                         # [可选]: 部署文件模版目录，模版使用的值来自values.yaml和由Tiller提供的值
│   ├── deployment.yaml                # Kubernetes Deployment object
│   ├── _helpers.tpl                   # 用于修改Kubernetes objcet配置的模板
│   ├── hpa.yaml                       # Kubernetes HPA object
│   ├── ingress.yaml                   # Kubernetes Ingress object
│   ├── NOTES.txt                      # [可选]: 放置Chart的使用指南
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests/                         # 定义了一些测试资源
│       └── test-connection.yaml
└── values.yaml                        # Chart的默认配置文件

```
上面的目录中，有两个比较重要的文件：Chart.yaml 文件templates 目录

下面我来详细介绍下这两个文件。我们先来看 **Chart.yaml 文件**。Chart.yaml 用来描述 Chart 的基本信息，包括名称、版本等，内容如下：

```yaml
apiVersion: Chart API 版本 （必需）
name: Chart名称 （必需）
version: 语义化版本（必需）
kubeVersion: 兼容Kubernetes版本的语义化版本（可选）
description: 对这个项目的一句话描述（可选）
type: Chart类型 （可选）
keywords:
  - 关于项目的一组关键字（可选）
home: 项目home页面的URL （可选）
sources:
  - 项目源码的URL列表（可选）
dependencies: # chart 必要条件列表 （可选）
  - name: Chart名称 (nginx)
    version: Chart版本 ("1.2.3")
    repository: （可选）仓库URL ("https://example.com/charts") 或别名 ("@repo-name")
    condition: （可选） 解析为布尔值的YAML路径，用于启用/禁用Chart(e.g. subchart1.enabled )
    tags: # （可选）
      - 用于一次启用/禁用 一组Chart的tag
    import-values: # （可选）
      - ImportValue 保存源值到导入父键的映射。每项可以是字符串或者一对子/父列表项
    alias: （可选） Chart中使用的别名。当你要多次添加相同的Chart时会很有用
maintainers: # （可选）
  - name: 维护者名字 （每个维护者都需要）
    email: 维护者邮箱 （每个维护者可选）
    url: 维护者URL （每个维护者可选）
icon: 用作icon的SVG或PNG图片URL （可选）
appVersion: 包含的应用版本（可选）。不需要是语义化，建议使用引号
deprecated: 不被推荐的Chart（可选，布尔值）
annotations:
  example: 按名称输入的批注列表 （可选）.
```
我们再来看下**templates 目录这个文件**。templates 目录中包含了应用中各个 Kubernetes 资源的 YAML 格式资源定义模板，例如：

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: {{ .Values.pump.name }}
  name: {{ .Values.pump.name }}
spec:
  ports:
  - name: http
    protocol: TCP
    {{- toYaml .Values.pump.service.http| nindent 4 }}
  selector:
    app: {{ .Values.pump.name }}
  sessionAffinity: None
  type: {{ .Values.serviceType }}
```

{{ .Values.pump.name }}会被deployments/iam/values.yaml文件中pump.name的值替换。上面的模版语法扩展了 Go text/template包的语法：

```yaml
# 这种方式定义的模版，会去除test模版尾部所有的空行
{{- define "test"}}
模版内容
{{- end}}

# 去除test模版头部的第一个空行
{{- template "test" }}
```
下面是用于 YAML 文件前置空格的语法：

```yaml
# 这种方式定义的模版，会去除test模版头部和尾部所有的空行
{{- define "test" -}}
模版内容
{{- end -}}

# 可以在test模版每一行的头部增加4个空格，用于YAML文件的对齐
{{ include "test" | indent 4}}
```
最后，这里有三点需要你注意：
- Chart 名称必须是小写字母和数字，单词之间可以使用横杠-分隔，Chart 名称中不能用大写字母，也不能用下划线，.号也不行。
- 尽可能使用SemVer 2来表示版本号。
- YAML 文件应该按照双空格的形式缩进 (一定不要使用 tab 键)。

第二步，编辑 iam 目录下的 Chart 文件。我们可以基于helm create生成的模板 Chart 来构建自己的 Chart 包。这里我们添加了创建 iam-apiserver、iam-authz-server、iam-pump、iamctl 服务需要的 YAML 格式的 Kubernetes 资源文件模板：
```
$ ls -1 iam/templates/*.yaml
iam/templates/hpa.yaml                                   # Kubernetes HPA模板文件
iam/templates/iam-apiserver-deployment.yaml              # iam-apiserver服务deployment模板文件
iam/templates/iam-apiserver-service.yaml                 # iam-apiserver服务service模板文件
iam/templates/iam-authz-server-deployment.yaml           # iam-authz-server服务deployment模板文件
iam/templates/iam-authz-server-service.yaml              # iam-authz-server服务service模板文件
iam/templates/iamctl-deployment.yaml                     # iamctl服务deployment模板文件
iam/templates/iam-pump-deployment.yaml                   # iam-pump服务deployment模板文件
iam/templates/iam-pump-service.yaml                      # iam-pump服务service模板文件

```

模板的具体内容，你可以查看deployments/iam/templates/。在编辑 Chart 时，我们可以通过 helm lint 验证格式是否正确，例如：

```
$ helm lint iam
==> Linting iam

1 chart(s) linted, 0 chart(s) failed
```

0 chart(s) failed 说明当前 Iam Chart 包是通过校验的。


第三步，修改 Chart 的配置文件，添加自定义配置。我们可以编辑deployments/iam/values.yaml文件，定制自己的配置。具体配置你可以参考deployments/iam/values.yaml。在修改 values.yaml 文件时，你可以参考下面这些最佳实践。


- 变量名称以小写字母开头，单词按驼峰区分，例如chickenNoodleSoup。
- 给所有字符串类型的值加上引号。
- 为了避免整数转换问题，将整型存储为字符串更好，并用 {{ int $value }} 在模板中将字符串转回整型。
- values.yaml中定义的每个属性都应该文档化。文档字符串应该以它要描述的属性开头，并至少给出一句描述。例如：

```yaml
# serverHost is the host name for the webserver
serverHost: example
# serverPort is the HTTP listener port for the webserver
serverPort: 9191
```
这里需要注意，所有的 Helm 内置变量都以大写字母开头，以便与用户定义的 value 进行区分，例如.Release.Name、.Capabilities.KubeVersion。为了安全，values.yaml 中只配置 Kubernetes 资源相关的配置项，例如 Deployment 副本数、Service 端口等。至于 iam-apiserver、iam-authz-server、iam-pump、iamctl 组件的配置文件，我们创建单独的 ConfigMap，并在 Deployment 中引用。

第四步，打包 Chart，并上传到 Chart 仓库中。这是一个可选步骤，可以根据你的实际需要来选择。如果想了解具体操作，你可以查看 [Helm chart 仓库](https://helm.sh/zh/docs/topics/chart_repository)获取更多信息。

最后，IAM 应用的 Chart 包见[deployments/iam](https://github.com/marmotedu/iam/tree/v1.1.0/deployments/iam)。

### IAM Chart 部署
上面，我们制作了 IAM 应用的 Chart 包，接下来我们就使用这个 Chart 包来一键创建 IAM 应用。IAM Chart 部署一共分为 10 个步骤，你可以跟着我一步步操作下。

第一步，配置scripts/install/environment.sh。scripts/install/environment.sh文件中包含了各类自定义配置，你主要配置下面这些跟数据库相关的就可以，其他配置使用默认值。

MariaDB 配置：environment.sh 文件中以MARIADB_开头的变量。Redis 配置：environment.sh 文件中以REDIS_开头的变量。MongoDB 配置：environment.sh 文件中以MONGO_开头的变量。

第二步，创建 IAM 应用的配置文件。

```
$ cd ${IAM_ROOT}
$ make gen.defaultconfigs # 生成iam-apiserver、iam-authz-server、iam-pump、iamctl组件的默认配置文件
$ make gen.ca # 生成 CA 证书
```
上面的命令会将 IAM 的配置文件存放在目录${IAM_ROOT}/_output/configs/下。

第三步，创建 iam 命名空间。我们将 IAM 应用涉及到的各类资源都创建在iam命名空间中。将 IAM 资源创建在独立的命名空间中，不仅方便维护，还可以有效避免影响其他 Kubernetes 资源。

```
$ kubectl create namespace iam
```

第四步，将 IAM 各服务的配置文件，以 ConfigMap 资源的形式保存在 Kubernetes 集群中。

```
$ kubectl -n iam create configmap iam --from-file=${IAM_ROOT}/_output/configs/
$ kubectl -n iam get configmap iam
NAME   DATA   AGE
iam    4      13s
```
第五步，将 IAM 各服务使用的证书文件，以 ConfigMap 资源的形式保存在 Kubernetes 集群中。

```
$ kubectl -n iam create configmap iam-cert --from-file=${IAM_ROOT}/_output/cert
$ kubectl -n iam get configmap iam-cert
NAME       DATA   AGE
iam-cert   14     12s
```

第六步，创建镜像仓库访问密钥。在准备阶段，我们开通了腾讯云镜像仓库服务，并创建了用户10000099``xxxx，密码为iam59!z$。

接下来，我们就可以创建 docker-registry secret 了。Kubernetes 在下载 Docker 镜像时，需要 docker-registry secret 来进行认证。创建命令如下：

```
$ kubectl -n iam create secret docker-registry ccr-registry --docker-server=ccr.ccs.tencentyun.com --docker-username=10000099xxxx --docker-password='iam59!z$'
```
第七步，创建 Docker 镜像，并 Push 到镜像仓库。


```
$ make push REGISTRY_PREFIX=ccr.ccs.tencentyun.com/marmotedu VERSION=v1.1.0
```
第八步，安装 IAM Chart 包。在49 讲里，我介绍了 4 种安装 Chart 包的方法。这里，我们通过未打包的 IAM Chart 路径来安装，安装方法如下：

```
$ cd ${IAM_ROOT}
$ helm -n iam install iam deployments/iam
NAME: iam
LAST DEPLOYED: Sat Aug 21 17:46:56 2021
NAMESPACE: iam
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
执行 helm install 后，Kubernetes 会自动部署应用，等到 IAM 应用的 Pod 都处在 Running 状态时，说明 IAM 应用已经成功安装：

```
$ kubectl -n iam get pods|grep iam
iam-apiserver-cb4ff955-hs827        1/1     Running   0          66s
iam-authz-server-7fccc7db8d-chwnn   1/1     Running   0          66s
iam-pump-78b57b4464-rrlbf           1/1     Running   0          66s
iamctl-59fdc4995-xrzhn              1/1     Running   0          66s
```
第九步，测试 IAM 应用。我们通过helm install在iam命令空间下创建了一个测试 Deployment iamctl。你可以登陆iamctl Deployment 所创建出来的 Pod，执行一些运维操作和冒烟测试。登陆命令如下：

```
$ kubectl -n iam exec -it `kubectl -n iam get pods -l app=iamctl | awk '/iamctl/{print $1}'` -- bash
```

登陆到iamctl-xxxxxxxxxx-xxxxx Pod 中后，你就可以执行运维操作和冒烟测试了。先来看运维操作。iamctl 工具以子命令的方式对外提供功能，你可以使用它提供的各类功能，如下图所示：

![img](https://static001.geekbang.org/resource/image/69/y2/693f608aa571cbfd6e06c8cfdb242yy2.png?wh=1920x337)

再来看冒烟测试：

```
# cd /opt/iam/scripts/install
# ./test.sh iam::test::smoke
```

如果./test.sh iam::test::smoke命令打印的输出中，最后一行为congratulations, smoke test passed!字符串，就说明 IAM 应用安装成功。如下图所示：

![img](https://static001.geekbang.org/resource/image/9d/8c/9dcc557952b3586f7b37b065bf2bd58c.png?wh=1920x314)

第十步，销毁 EKS 集群的资源。

```
$ kubectl delete namespace iam
```
你可以根据需要选择是否删除 EKS 集群，如果不需要了就可以选择删除。


### IAM 应用多环境部署
在实际的项目开发中，我们需要将 IAM 应用部署到不同的环境中，不同环境的配置文件是不同的，那么 IAM 项目是如何进行多环境部署的呢？

IAM 项目在configs目录下创建了多个 Helm values 文件（格式为values-{envName}-env.yaml）：
- values-test-env.yaml，测试环境 Helm values 文件。
- values-pre-env.yaml，预发环境 Helm values 文件。
- values-prod-env.yaml，生产环境 Helm values 文件。

在部署 IAM 应用时，我们在命令行指定-f参数，例如：

```
$ helm -n iam install -f configs/values-test-env.yaml iam deployments/iam # 安装到测试环境。
```
### 总结
这一讲，我们通过 helm create iam 创建了一个模板 Chart，并基于这个模板 Chart 包进行了二次开发，最终创建了 IAM 应用的 Helm Chart 包：[deployments/iam](https://github.com/marmotedu/iam/tree/v1.1.0/deployments/iam)。有了 Helm Chart 包，我们就可以通过 helm -n iam install iam deployments/iam 命令来一键部署好整个 IAM 应用。当 IAM 应用中的所有 Pod 都处在 Running 状态后，说明 IAM 应用被成功部署。

最后，我们可以登录 iamctl 容器，执行 test.sh iam::test::smoke 命令，来对 IAM 应用进行冒烟测试。


## 51 | 基于 GitHub Actions 的 CI 实战

在 Go 项目开发中，我们要频繁地**执行静态代码检查、测试、编译、构建等操作**。如果每一步我们都手动执行，效率低不说，还容易出错。所以，我们通常借助 CI 系统来自动化执行这些操作。

当前业界有很多优秀的 CI 系统可供选择，例如 CircleCI、TravisCI、Jenkins、CODING、GitHub Actions 等。这些系统在设计上大同小异，为了减少你的学习成本，我选择了相对来说容易实践的 GitHub Actions，来给你展示如何通过 CI 来让工作自动化。这一讲，我会先介绍下 GitHub Actions 及其用法，再向你展示一个 CI 示例，最后给你演示下 IAM 是如何构建 CI 任务的。


### GitHub Actions 的基本用法
GitHub Actions 是 GitHub 为托管在 github.com 站点的项目提供的持续集成服务，于 2018 年 10 月推出。GitHub Actions 具有以下功能特性：

- 提供原子的 actions 配置和组合 actions 的 workflow 配置两种能力。
- 全局配置基于YAML 配置，兼容主流 CI/CD 工具配置。
- Actions/Workflows 基于事件触发，包括 Event restrictions、Webhook events、Scheduled events、External events。
- 提供可供运行的托管容器服务，包括 Docker、VM，可运行 Linux、macOS、Windows 主流系统。
- 提供主流语言的支持，包括 Node.js、Python、Java、Ruby、PHP、Go、Rust、.NET。
- 提供实时日志流程，方便调试。
- 提供平台内置的 Actions与第三方提供的 Actions，开箱即用。

### GitHub Actions 的基本概念
在构建持续集成任务时，我们会在任务中心完成各种操作，比如克隆代码、编译代码、运行单元测试、构建和发布镜像等。GitHub 把这些操作称为 Actions。

Actions 在很多项目中是可以共享的，GitHub 允许开发者将这些可共享的 Actions 上传到GitHub 的官方 Actions 市场，开发者在 Actions 市场中可以搜索到他人提交的 Actions。另外，还有一个 awesome actions 的仓库，里面也有不少的 Action 可供开发者使用。如果你需要某个 Action，不必自己写复杂的脚本，直接引用他人写好的 Action 即可。整个持续集成过程，就变成了一个 Actions 的组合。

Action 其实是一个独立的脚本，可以将 Action 存放在 GitHub 代码仓库中，通过<userName>/<repoName>的语法引用 Action。例如，actions/checkout@v2表示https://github.com/actions/checkout这个仓库，tag 是 v2。actions/checkout@v2也代表一个 Action，作用是安装 Go 编译环境。GitHub 官方的 Actions 都放在 github.com/actions 里面。


GitHub Actions 有一些自己的术语，下面我来介绍下。
- workflow（工作流程）：一个  .yml  文件对应一个 workflow，也就是一次持续集成。一个 GitHub 仓库可以包含多个 workflow，只要是在  .github/workflow  目录下的  .yml  文件都会被 GitHub 执行。
- job（任务）：一个 workflow 由一个或多个 job 构成，每个 job 代表一个持续集成任务。
- step（步骤）：每个 job 由多个 step 构成，一步步完成。
- action（动作）：每个 step 可以依次执行一个或多个命令（action）。
- on：一个 workflow 的触发条件，决定了当前的 workflow 在什么时候被执行。

#### workflow 文件介绍
GitHub Actions 配置文件存放在代码仓库的.github/workflows目录下，文件后缀为.yml，支持创建多个文件，文件名可以任意取，比如iam.yml。GitHub 只要发现.github/workflows目录里面有.yml文件，就会自动运行该文件，如果运行过程中存在问题，会以邮件的形式通知到你。

workflow 文件的配置字段非常多，如果你想详细了解，可以查看[官方文档](https://docs.github.com/cn/actions/reference/workflow-syntax-for-github-actions)。这里，我来介绍一些基本的配置字段。

name
name字段是 workflow 的名称。如果省略该字段，默认为当前 workflow 的文件名。

```
name: GitHub Actions Demo
```
on
on字段指定触发 workflow 的条件，通常是某些事件。

```
on: push
```

上面的配置意思是，push事件触发 workflow。on字段也可以是事件的数组，例如:

```
on: [push, pull_request]
```
上面的配置意思是，push事件或pull_request事件都可以触发 workflow。想了解完整的事件列表，你可以查看官方文档。除了代码库事件，GitHub Actions 也支持外部事件触发，或者定时运行。

on.< push|pull_request>.< tags|branches>
指定触发事件时，我们可以限定分支或标签。

```
on:
  push:
    branches:
      - master
```
上面的配置指定，只有master分支发生push事件时，才会触发 workflow。

jobs.< job_id>.name
workflow 文件的主体是jobs字段，表示要执行的一项或多项任务。jobs字段里面，需要写出每一项任务的job_id，具体名称自定义。job_id里面的name字段是任务的说明。

```
jobs:
  my_first_job:
    name: My first job
  my_second_job:
    name: My second job
```
上面的代码中，jobs字段包含两项任务，job_id分别是my_first_job和my_second_job。

jobs.< job_id>.needs
needs字段指定当前任务的依赖关系，即运行顺序。

```
jobs:
  job1:
  job2:
    needs: job1
  job3:
    needs: [job1, job2]
```

上面的代码中，job1必须先于job2完成，而job3等待job1和job2完成后才能运行。因此，这个 workflow 的运行顺序为：job1、job2、job3。

jobs.< job_id>.runs-on
runs-on字段指定运行所需要的虚拟机环境，它是必填字段。目前可用的虚拟机如下：
- ubuntu-latest、ubuntu-18.04 或 ubuntu-16.04。
- windows-latest、windows-2019 或 windows-2016。
- macOS-latest 或 macOS-10.14。

下面的配置指定虚拟机环境为ubuntu-18.04。

```
runs-on: ubuntu-18.04
```

jobs..steps
steps字段指定每个 Job 的运行步骤，可以包含一个或多个步骤。每个步骤都可以指定下面三个字段。

jobs.< job_id>.steps.name：步骤名称。jobs.< job_id>.steps.run：该步骤运行的命令或者 action。jobs.< job_id>.steps.env：该步骤所需的环境变量。

下面是一个完整的 workflow 文件的范例：

```
name: Greeting from Mona
on: push

jobs:
  my-job:
    name: My Job
    runs-on: ubuntu-latest
    steps:
    - name: Print a greeting
      env:
        MY_VAR: Hello! My name is
        FIRST_NAME: Lingfei
        LAST_NAME: Kong
      run: |
        echo $MY_VAR $FIRST_NAME $LAST_NAME.
```

上面的代码中，steps字段只包括一个步骤。该步骤先注入三个环境变量，然后执行一条 Bash 命令。

uses
uses 可以引用别人已经创建的 actions，就是上面说的 actions 市场中的 actions。引用格式为userName/repoName@verison，例如uses: actions/setup-go@v1。

with
with 指定 actions 的输入参数。每个输入参数都是一个键 / 值对。输入参数被设置为环境变量，该变量的前缀为 INPUT_，并转换为大写。

这里举个例子：我们定义 hello_world 操作所定义的三个输入参数（first_name、middle_name 和 last_name），这些输入变量将被 hello-world 操作作为 INPUT_FIRST_NAME、INPUT_MIDDLE_NAME 和 INPUT_LAST_NAME 环境变量使用。

```
jobs:
  my_first_job:
    steps:
      - name: My first step
        uses: actions/hello_world@master
        with:
          first_name: Lingfei
          middle_name: Go
          last_name: Kong
```
run
run指定执行的命令。可以有多个命令，例如：

id
id是 step 的唯一标识。

### GitHub Actions 的进阶用法
上面，我介绍了 GitHub Actions 的一些基本知识，这里我再介绍下 GitHub Actions 的进阶用法。
#### 为工作流加一个 Badge
在 action 的面板中，点击Create status badge就可以复制 Badge 的 Markdown 内容到 README.md 中。之后，我们就可以直接在 README.md 中看到当前的构建结果：

![img](https://static001.geekbang.org/resource/image/45/af/453a97b0776281873dee5671c53347af.png?wh=1280x765)

#### 使用构建矩阵
如果我们想在多个系统或者多个语言版本上测试构建，就需要设置构建矩阵。例如，我们想在多个操作系统、多个 Go 版本下跑测试，可以使用如下 workflow 配置：

```
name: Go Test

on: [push, pull_request]

jobs:

  helloci-build:
    name: Test with go ${{ matrix.go_version }} on ${{ matrix.os }}
    runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        go_version: [1.15, 1.16]
        os: [ubuntu-latest, macOS-latest]

    steps:

      - name: Set up Go ${{ matrix.go_version }}
        uses: actions/setup-go@v2
        with:
          go-version: ${{ matrix.go_version }}
        id: go
```

上面的 workflow 配置，通过strategy.matrix配置了该工作流程运行的环境矩阵（格式为go_version.os）：ubuntu-latest.1.15、ubuntu-latest.1.16、macOS-latest.1.15、macOS-latest.1.16。也就是说，会在 4 台不同配置的服务器上执行该 workflow。

#### 使用 Secrets
在构建过程中，我们可能需要用到ssh或者token等敏感数据，而我们不希望这些数据直接暴露在仓库中，此时就可以使用secrets。

我们在对应项目中选择Settings-> Secrets，就可以创建secret，如下图所示：

![img](https://static001.geekbang.org/resource/image/c0/d3/c00b11a1709838c1a205ace7976768d3.png?wh=1920x1046)

配置文件中的使用方法如下：

```
name: Go Test
on: [push, pull_request]
jobs:
  helloci-build:
    name: Test with go
    runs-on: [ubuntu-latest]
    environment:
      name: helloci
    steps:
      - name: use secrets
        env:
          super_secret: ${{ secrets.YourSecrets }}
```

secret name 不区分大小写，所以如果新建 secret 的名字是 name，使用时用 secrets.name 或者 secrets.Name 都是可以的。而且，就算此时直接使用 echo 打印 secret , 控制台也只会打印出*来保护 secret。

这里要注意，你的 secret 是属于某一个环境变量的，所以要指明环境的名字：environment.name。上面的 workflow 配置中的secrets.YourSecrets属于helloci环境。
#### 使用 Artifact 保存构建产物
在构建过程中，我们可能需要输出一些构建产物，比如日志文件、测试结果等。这些产物可以使用 Github Actions Artifact 来存储。你可以使用action/upload-artifact 和 download-artifact 进行构建参数的相关操作。

这里我以输出 Jest 测试报告为例来演示下如何保存 Artifact 产物。Jest 测试后的测试产物是 coverage：

```
steps:
      - run: npm ci
      - run: npm test

      - name: Collect Test Coverage File
        uses: actions/upload-artifact@v1.0.0
        with:
          name: coverage-output
          path: coverage
```
执行成功后，我们就能在对应 action 面板看到生成的 Artifact：

![img](https://static001.geekbang.org/resource/image/4c/66/4c4a8d6aec12a5dd1cdc80d238472566.png?wh=1280x208)

### GitHub Actions 实战
上面，我介绍了 GitHub Actions 的用法，接下来我们就来实战下，看下使用 GitHub Actions 的 6 个具体步骤。

第一步，创建一个测试仓库。登陆GitHub 官网，点击 New repository 创建，如下图所示：

![img](https://static001.geekbang.org/resource/image/6d/a0/6d76d02f0418671a32f5346fccf616a0.png?wh=1920x810)

这里，我们创建了一个叫helloci的测试项目。
第二步，将新的仓库 clone 下来，并添加一些文件：


```
$ git clone https://github.com/marmotedu/helloci
```
你可以克隆marmotedu/helloci，并将里面的文件拷贝到你创建的项目仓库中。
第三步，创建 GitHub Actions workflow 配置目录：

```
$ mkdir -p .github/workflows                     
```

第四步，创建 GitHub Actions workflow 配置。在.github/workflows目录下新建helloci.yml文件，内容如下：

```
name: Go Test

on: [push, pull_request]

jobs:

  helloci-build:
    name: Test with go ${{ matrix.go_version }} on ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    environment:
      name: helloci

    strategy:
      matrix:
        go_version: [1.16]
        os: [ubuntu-latest]

    steps:

      - name: Set up Go ${{ matrix.go_version }}
        uses: actions/setup-go@v2
        with:
          go-version: ${{ matrix.go_version }}
        id: go

      - name: Check out code into the Go module directory
        uses: actions/checkout@v2

      - name: Tidy
        run: |
          go mod tidy

      - name: Build
        run: |
          go build -v -o helloci .

      - name: Collect main.go file
        uses: actions/upload-artifact@v1.0.0
        with:
          name: main-output
          path: main.go

      - name: Publish to Registry
        uses: elgohr/Publish-Docker-GitHub-Action@master
        with:
          name: ccr.ccs.tencentyun.com/marmotedu/helloci:beta  # docker image 的名字
          username: ${{ secrets.DOCKER_USERNAME}} # 用户名
          password: ${{ secrets.DOCKER_PASSWORD }} # 密码
          registry: ccr.ccs.tencentyun.com # 腾讯云Registry
          dockerfile: Dockerfile # 指定 Dockerfile 的位置
          tag_names: true # 是否将 release 的 tag 作为 docker image 的 tag
```

上面的 workflow 文件定义了当 GitHub 仓库有push、pull_request事件发生时，会触发 GitHub Actions 工作流程，流程中定义了一个任务（Job）helloci-build，Job 中包含了多个步骤（Step），每个步骤又包含一些动作（Action）。

上面的 workflow 配置会按顺序执行下面的 6 个步骤。
1. 准备一个 Go 编译环境。
2. 从marmotedu/helloci下载源码。
3. 添加或删除缺失的依赖包。
4. 编译 Go 源码。
5. 上传构建产物。
6. 构建镜像，并将镜像 push 到ccr.ccs.tencentyun.com/marmotedu/helloci:beta。

第五步，在 push 代码之前，我们需要先创建DOCKER_USERNAME和DOCKER_PASSWORD secret。其中，DOCKER_USERNAME保存腾讯云镜像服务（CCR）的用户名，DOCKER_PASSWORD保存 CCR 的密码。我们将这两个 secret 保存在helloci Environments 中，如下图所示：

![img](https://static001.geekbang.org/resource/image/c0/d3/c00b11a1709838c1a205ace7976768d3.png?wh=1920x1046)


第六步，将项目 push 到 GitHub，触发 workflow 工作流：

```
$ git add .
$ git push origin master
```

打开我们的仓库 Actions 标签页，可以发现 GitHub Actions workflow 正在执行：

![img](https://static001.geekbang.org/resource/image/1a/8a/1afb7860d68635c5e3eaba4ff8da208a.png?wh=1920x691)

等 workflow 执行完，点击 Go Test 进入构建详情页面，在详情页面能够看到我们的构建历史：

![img](https://static001.geekbang.org/resource/image/a4/95/a4b83a122379db4f2fe9538afdfb5a95.png?wh=1920x701)

然后，选择其中一个构建记录，查看其运行详情（具体可参考chore: update step name Go Test #10）：

![img](https://static001.geekbang.org/resource/image/48/4f/481f64aabccf30ed61d0a7c85ab30d4f.png?wh=1920x1084)

你可以看到，Go Test工作流程执行了 6 个 Job，每个 Job 执行了下面这些自定义 Step：
1. Set up Go 1.16。
2. Check out code into the Go module directory。
3. Tidy。
4. Build。
5. Collect main.go file。
6. Publish to Registry。

其他步骤是 GitHub Actions 自己添加的步骤：Setup Job、Post Check out code into the Go module directory、Complete job。点击每一个步骤，你都能看到它们的详细输出。

### IAM GitHub Actions 实战
接下来，我们再来看下 IAM 项目的 GitHub Actions 实战。假设 IAM 项目根目录为 ${IAM_ROOT}，它的 workflow 配置文件为：

接下来，我们再来看下 IAM 项目的 GitHub Actions 实战。假设 IAM 项目根目录为 ${IAM_ROOT}，它的 workflow 配置文件为：

```
$ cat ${IAM_ROOT}/.github/workflows/iamci.yaml
name: IamCI

on:
  push:
    branchs:
    - '*'
  pull_request:
    types: [opened, reopened]

jobs:

  iamci:
    name: Test with go ${{ matrix.go_version }} on ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    environment:
      name: iamci

    strategy:
      matrix:
        go_version: [1.16]
        os: [ubuntu-latest]

    steps:

      - name: Set up Go ${{ matrix.go_version }}
        uses: actions/setup-go@v2
        with:
          go-version: ${{ matrix.go_version }}
        id: go

      - name: Check out code into the Go module directory
        uses: actions/checkout@v2

      - name: Run go modules Tidy
        run: |
          make tidy

      - name: Generate all necessary files, such as error code files
        run: |
          make gen

      - name: Check syntax and styling of go sources
        run: |
          make lint

      - name: Run unit test and get test coverage
        run: |
          make cover

      - name: Build source code for host platform
        run: |
          make build

      - name: Collect Test Coverage File
        uses: actions/upload-artifact@v1.0.0
        with:
          name: main-output
          path: _output/coverage.out

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to DockerHub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build docker images for host arch and push images to registry
        run: |
          make push
```

上面的 workflow 依次执行了以下步骤：
1. 设置 Go 编译环境。
2. 下载 IAM 项目源码。
3. 添加 / 删除不需要的 Go 包。
4. 生成所有的代码文件。
5. 对 IAM 源码进行静态代码检查。
6. 运行单元测试用例，并计算单元测试覆盖率是否达标。
7. 编译代码。收集构建产物_output/coverage.out。
8. 配置 Docker 构建环境。
9. 登陆 DockerHub。
10. 构建 Docker 镜像，并 push 到 DockerHub。

IamCI workflow 运行历史如下图所示：

![img](https://static001.geekbang.org/resource/image/2b/b0/2b542f9101be0c3a83576fb99bf882b0.png?wh=1920x844)

IamCI workflow 的其中一次工作流程运行结果如下图所示：

![img](https://static001.geekbang.org/resource/image/e9/6a/e9ebf13fdb6e4f41a1b00406e646ec6a.png?wh=1920x887)


### 总结
在 Go 项目开发中，我们需要通过 CI 任务来将需要频繁操作的任务自动化，这不仅可以提高开发效率，还能减少手动操作带来的失误。这一讲，我选择了最易实践的 GitHub Actions，来给你演示如何构建 CI 任务。GitHub Actions 支持通过 push 事件来触发 CI 流程。一个 CI 流程其实就是一个 workflow，workflow 中包含多个任务，这些任务是可以并行执行的。一个任务又包含多个步骤，每一步又由多个动作组成。动作（Action）其实是一个命令 / 脚本，用来完成我们指定的任务，如编译等。因为 GitHub Actions 内容比较多，这一讲只介绍了一些核心的知识，更详细的 GitHub Actions 教程，你可以参考 [官方中文文档](https://docs.github.com/cn/actions)。


