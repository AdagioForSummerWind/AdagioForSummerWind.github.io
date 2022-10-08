---
title: "Linux_base_01"
date: 2021-11-08T15:34:58+08:00
lastmod: 2021-11-08
tags: [linux]
categories: [Advanced learning]
slug: linux
draft: true
---
## 1. Linux 背景知识
Linux 有两种含义：
* 一种是 Linus 编写的开源操作系统的内核
* 另一种是广义的操作系统

执行环境
* 云主机
* 虚拟机（较推荐）
* 无数据的 PC（不推荐多系统混跑）

linux版本：   
* 内核版本
    * 内核版本分为三个部分 
        * 主版本号、次版本号、末版本号
    * 次版本号是奇数为开发版，偶数为稳定版（但从2.6开始就不这样了）

* 发行版本（因为linux是开源的）5种
    * Redhat Enterprise Linux:RedHat公司发行，软件经过专业人员的测试，非常稳定，但是需要付费。
    * Fedora发行版本，也是RadHat发行，组建一个社区免费提供这个操作系统，软件较新，但未经过RedHat的专业测试，稳定性较差。
    * centos，是基于Enterprise Linux 的源代码经行编译的，把RedHad的商标和字样去掉了，故免费。
    * Debian和Ubuntu 不是字符桌面，有图形界面。
## 2. 系统操作
### 帮助命令man help info
* manual: man ls;man 章节号 man；man -a passwd
* help: 内部命令(命令解释器shell自带的命令 type cd查看cd命令类型)：help cd 外部命令：ls --help
* info：比help 更详细，可以作为help的补充，全英文。
### linux文件管理
一切皆文件
windows里有注册表，资源管理器等组件，控制linux自身的统统都是文件，linux文件管理非常重要
### 文件查看
tab可以进行目录补全
* pwd
* cd
    * cd - 回到上次目录
    * cd ..(/)回到上一级目录
    * cd /path/to/…. 绝对路径
    * cd ./path/to/… 相对路径（./可以省略）
* ls
    * -l长格式显示（显示大小单位为Mb时可用-lh）
    * -a显示隐藏文件
    * -r逆序显示
    * -t时间顺序显示
    * -R递归显示
    - -F把文件按照类型归类，主要区分目录文件、可执行文件、链接文件，并且在末尾加上 / 、*、@符号标识
### 目录文件的创建和删除
只列出了少量命令
* mkdir
    * mkdir a b c
    * mkdir /a/b /a目录已存在
    * mkdir -p /a/b/c/d 无需a b c等目录存在
    * mkdir a b c -p  忽略已存在a b c 的报错
* rmdir 删除空目录，目录下有空目录也不行，体现了“一切皆文件”
* rm
    * -r -f（/rf） 可以删除非空目录，不去进行提示删除目录，具有一定危险性 eg: rm -r -f / a 不小心在/a之间加空格会导致删除所有目录且不提示
    * -i 删除前逐一询问确认
    * -f 即使原档案属性设为唯读，亦直接删除，无需逐一确认
    * -r 将目录及以下之档案亦逐一删除
* cp 
    * -r 复制⽬录
    * -p 保留访问权限、修改时间
    * -a 等同于 -dpR（保留权限、属组、修改时间）
    * -v 显示复制过程
    - -i 会在复制文件的时候给提示 如果复制的目标文件存在 会给你提示是否要覆盖如果目标文件不存在那么就直接复制了
* mv
    * mv /a /b将a改名为b（可与移动同时进行），在linux底层操作即进行了一个移动
* 更多命令见[命令大全](https://www.runoob.com/linux/linux-command-manual.html)
### 通配符（通用的匹配符号）
是shell内建的符号，用于多个相似的文件进行操作  
* *匹配任何字符串   
* ？匹配单个字符   
* [abc]匹配abc任意一个字符  
* [a-z]匹配一个范围   
* [!xyz]或[^xyz] 不匹配   
### 文件操作
#### 文本内容查看
* cat ⽂本内容显示到终端
* head 查看⽂件开头（默认10行，-5可显示5行）
* tail 查看⽂件结尾（行数同head）(-f可进行跟踪)
    - eg:tail -10 /etc/passwd 查看psaawd文件中倒数十行
* 常⽤参数 -f ⽂件内容更新后，显示信息同步更新
* wc 统计⽂件内容信息（-l查看行数）
* more 分行显示 （空格继续显示）
* less

more和less:
less:由于more不能后退，就取more的反义词less加上后退功能
所以Linux里流传着这样一句话："less is more".
总结下more 和 less的区别:
* less可以按键盘上下方向键显示上下内容,more不能这样
* less不必读整个文件，加载速度会比more更快
* less退出后shell不会留下刚显示的内容,而more退出后会在shell上留下刚显示的内容
#### 打包压缩解压
* 最早的 Linux 备份介质是磁带，使⽤的命令是 tar
* 可以打包后的磁带⽂件进⾏压缩储存，压缩的命令是 gzip 和 bzip2
* 经常使⽤的扩展名是 .tar.gz .tar.bz2 .tgz
* tar 打包命令
    * c 打包(cf  .tar)
    * x 解包
    * f 指定操作类型为⽂件
    * 可以使⽤ gzip 和 bzip2 命令单独操作通常和 tar 命令配合操作
    * -z gzip 格式压缩和解压缩(czf  .tar.gz)
    * -j bzip2 格式压缩和解压缩(cjf  .tar.bz2)，压缩比例更高，执行时间更慢。    
#### vim的四种模式
vim是一个vi向上兼容的文本编辑器，vim写的时候实际上是先复制一个隐藏文件，写入其中，保存退出时再用隐藏文件替换目的文件。
##### 正常模式
正常模式下输入：
* i:进入插入模式光标位置不动
* I:进入插入模式光标移到行首
* a:进入插入模式光标移到下一位
* A:进入插入模式光标移到行末
* o:进入插入模式光标在原来那一行的下一行，另起一行
* O:进入插入模式光标在原来那一行的上一行，另起一行
* ::进入命令模式
* hjkl:正常模式下光标上下左右移动
* 复制剪切粘贴输入错误进行重做
* y:
    * yy:光标在某一行输入n(不输入时默认为1，作为从当前行开始要复制的行数) yy，粘贴用p
    * y$:复制当前字符到行末的字符
* d:剪切
    * dd
    * d$
* u:撤销
* ctrl+r:重做
* x:单个字符删除
* r:替换当前字符
* n(你要移动光标到哪一行)+G
* g到第一行
* G到最后一行
* ^到当前行的开头
* $到当前行的结尾

##### 命令模式
文件的保存、退出、查找、替换，在正常模式下输入：
* w /test.txt 将文本保存到新文件，w直接保存到原始文件
* wq保存并推出
* q!不保存退出
* ！执行linux命令，有时候在打开vim的同时需要临时执行一条命令，eg:!ifconfig(查看ip地址)
* /查找eg:/x+enter光标自动移到第一个x,按n可以移动到下一个x，按N可以移到上一个x
* s/old/new 在光标所在行，将第一个旧的字符替换成新的字符
* %s/old/new 所有行，将第一个旧的字符替换成新的字符
* %s/old/new/g 所有行，将所有旧的字符替换成新的字符、
* n,m+前三个命令是在[n,m]行进行替换，左闭右闭
* set nohlsearch去除高亮显示
* set nu:显示行数
* set nonu:不显示行数
修改vim配置在/etc/vimrc文件里操作   
比如在最后一行添加set nu则以后每次打开文件都会显示行号

##### 可视模式
用于对文件进行重复的大量操作可以一次性执行完成
* v      字符可视模式
* V      行可视模式
* ctrl+v 块可视模式（用的最多）
* 配合d和I可以进行块的便利操作
    * eg:进入块可视模式，用hjkl选择一个块，I可以在块头插入比如abc再按两次esc可见每行都被插入了abc；选中一个块，按d直接删除块

### 用户和用户组管理及密码管理
windows linux都是多用户操作系统。只是windows的用户一般只有一个。

多用户操作系统的目的是隔离
- 用户权限隔离
- 系统资源隔离
- root 用户与普通用户的区别

用户管理常用命令
- useradd 新建用户
    - id+用户名 可显示相关信息（uid gid 以及组号）
- userdel 删除用户
    - 一般会加-r选项，因为如果不加，用户的家目录就会被保留下来，防止误删数据，加-r后都会删除
    - 可查看/etc/passwd  /etc/shadow
- passwd 修改用户密码
- usermod 修改用户属性
    - 可接很多属性如-a -c -d -e -g……。经常使用的一个属性是-d（用户的新登陆目录，家目录）
    - 修改用户组 eg: usermod -g group1 user1
- chage 修改用户属性
    - change age的缩写，可更改用户密码过期信息

组管理命令
- groupadd 新建用户组
    - 新建用户加入组有两种方法：
        - 先新建用户再用usermod -g
        - 直接useradd -g group1 user1
- groupdel 删除用户组

### su和sudo
用户切换
- su 切换用户
    - su - USERNAME 使用 login shell 方式切换用户
        -  -的作用是在用户切换的同时将运行环境也变更为目标用户的运行环境
        - 不带 - 为不完全切换，只切换了身份，需要额外切换目录
- sudo 以其他用户身份执行命令
    - visudo 设置需要使用 sudo 的用户（组）
```bash
#30分钟后关闭主机，需要管理员权限
shutdown -h 30
#停止关闭主机命令
shutdown -c 
```
vi sudo后可手动添加某用户权限eg:(A B=C )=(user1 ALL=/sbin/shutdown -c)
- 字符终端时B可以是localhost
- C是命令，在命令模式输入!which shutdown可得到shutdown命令的位置
- user1就被临时赋予了使用shutdown -c的权限
### 用户和用户组的配置文件介绍
用户配置文件
- /root root 用户的家目录
- /home/USERNAME 普通用户默认家目录位置
- /etc/passwd 用户配置文件
- /etc/shadow 用户密码相关配置文件
- /etc/group 用户组配置文件

### 文件和目录权限的表示方法
文件类型：
- \- 普通⽂件
- d ⽬录⽂件
- b 块特殊⽂件（块设备）
- c 字符特殊⽂件（字符设备）
- l 符号链接（快捷方式）
- f 命名管道
- s 套接字⽂件


字符权限表示方法
- r 读
- w 写
- x 执行

数字权限的表示方法
- r = 4 
- w = 2
- x = 1

\- rw- r-x r- - 1 userame groupname mtime filename
- 第一列：文件类型加权限。
    - 第一个字符为文件类型，d表示目录，l表示软连接，-表示文件，c表示字符设备文件。后面的字符分为三组，所有者u，所属组g,其它人o. r表示可读，w表示可写，x表示可执行。

- 第2列是硬链接的引用次数。
- 第3列是文件的拥有者账户。只能有一个。
- 第4列是文件的拥有者账户所在组名。只能有一个。
- 第5列是文件所占有的字节数。
- 第6列是文件最后修改时间。
- 第7列是文件名。

- 创建文件有默认权限，根据 umask 值计算



目录权限：
- x 进入目录
- rx 显示目录内的文件名
- wx 修改目录内的文件名

### 文件权限的修改方法和数字表示方法
修改权限：
- chmod 修改文件、目录权限
    - u/g/o/a 前三个分别对应属主、属组、其他用户的权限，a代表所有的权限
    - chmod u+/-/=  rwx   file
    - 数字权限表示：chmod 446 file
    - 一个新建文件的默认权限是644，即666-0022（umask）
- chown 更改属主、属组
    - chown user /dir改属主
    - chown :group1 /dir改属组
    - 或者 chown user1:group1 /dir
- chgrp 可以单独更改属组，不常用
    - chgrp group1 /dir

**ctrl+r搜索之前出现的命令**



### 权限管理和文件的特殊权限
echo 123 > file
将123输出到file，>是输出重定向符号，会将file先清空。

****注意**：如果一个文件的属主权限和其属组的权限有冲突，以属主的权限为准。**

对于目录来说，x执行就是进入目录,rx是可以进入查看，wx是可以进入修改，只给r权限只能看到文件名。。

特殊权限：
- SUID 用于二进制可执行文件，执行命令时取得文件属主权限
    - 如/usr/bin/passwd，执行passwd自动获取到root身份，只有root能改/etc/shadow （/etc/shadow里边是用户密码）
    - chmod 4755 /test/b  755是目的文件属性
- SGID 用于目录，在该目录下创建新的文件和目录，权限自动更改为该目录的属组，**文件共享会用到**
- SBIT 用于目录，该目录下新建的文件和目录，仅 root 和自己可以删除，用sbit位进行标记
    - ![image-20220622095949728](D:\Blog\localImages\image-20220622095949728.png)
    - chmod 1777  /test  777是目的文件夹属性






## 3. 服务管理
### 网络管理

#### ⽹络状态查看 ：

1. net-tools （centos7以前）
	- ifconfig 
        - eth0 第⼀块⽹卡（⽹络接⼝）
          - 第⼀个⽹络接⼝可能叫做下⾯的名字
            - eno1 板载⽹卡
            - ens33 PCI-E⽹卡
            - enp0s3 ⽆法获取物理信息的 PCI-E ⽹卡
            - CentOS 7 使⽤了⼀致性⽹络设备命名，以上都不匹配则使⽤ eth0
        - **⽹络接⼝命名修改**
            - ⽹卡命名规则受 biosdevname 和 net.ifnames 两个参数影响
            - 编辑 /etc/default/grub ⽂件，在GRUB_COMMIT_LINUX项中增加 biosdevname=0 net.ifnames=0      （grub是系统刚开始启动的时候，引导内核需要一个工具，类似于启动菜单，这个启动菜单可以设置一些参数，传递到内核，真正启动时读取到的文件是/boot/grub2/grub.cfg）
            - 更新 grub 
            - # grub2-mkconfig -o /boot/grub2/grub.cfg
            - 重启后网卡生效
            - # reboot
        - ![image-20220622102300462](D:\Blog\localImages\image-20220622102300462.png)

	- route 
	- netstat 
2. iproute2（centos7、8）
	- ip 
	- ss

查看⽹卡物理连接情况
-  mii-tool eth0

当网卡通信需要连接其他网络地址范围的时候，需要配置一个网关路由，查看⽹关
- route -n
- 使⽤ -n 参数不解析主机名
```bash
[test@Centos8 workspace]$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.25.208.0    0.0.0.0         255.255.240.0   U     100    0        0 eth0
[test@Centos8 workspace]$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.25.223.253  0.0.0.0         UG    100    0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.25.208.0    0.0.0.0         255.255.240.0   U     100    0        0 eth0

```
#### ⽹络配置修改、路由命令 

- ifconfig <接⼝> <IP地址> [netmask ⼦⽹掩码 ]（云主机会立即生效）eg:ifconfig eth0 10.211.55.4
- ifup <接⼝> （启动）
- ifdown <接⼝>

添加⽹关（删除del）
- route add default gw <⽹关ip>
- route add -host <指定ip> gw <⽹关ip>
- route add -net <指定⽹段> netmask <⼦⽹掩码> gw <⽹关ip>

**ip命令**
- ip addr ls 
    - ifconfig
- ip link set dev eth0 up 
    - ifup eth0
- ip addr add 10.0.0.1/24 dev eth1 
    - ifconfig eth1 10.0.0.1 netmask 255.255.255.0
- ip route add 10.0.0/24 via 192.168.0.1 
    - route add -net 10.0.0.0 netmask 255.255.255.0 gw 192.168.0.


#### ⽹络故障排除 

从上到下依次排除：
- ping：检查到目标主机是否畅通
- traceroute： Ping有效，但网络依旧异常，可追踪服务器每一跳服务质量，显示数据包到主机间的路径 eg:traceroute -w 1（指1秒） www.baidu.com
- mtr：检查到目标主机中间是否有数据报丢失，更详细
- nslookup：查看域名对应的IP eg:nslookup www.baidu.com
- telnet：查看端口连接状态 eg:telnet www.baidu.com 80
- tcpdump：抓取分析数据包 eg:tcpdump -i any -n port 80(任意网卡、80端口数据包、以IP形式进行显示)（捕获主机的话用host eg: ~-i any host 10.0.0.1）（两者都要可用and eg: ~host 10.0.0.1 and port 80）（~ -w /filename保存到文件中）
- netstat：服务的监听地址范围 netstat -ntpl
    - n显示IP不显示域名
    - t 以TCP方式截取要显示的内容，非UDP（UDP是-p）
    - p 显示进程，除了显示端口之外，还要显示端口对应哪一个进程
    - l TCP的一个状态，叫做listen，监听服务
```bash
[test@Centos8 workspace]$ netstat -ntpl
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:5355            0.0.0.0:*               LISTEN      -      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -      
tcp6       0      0 :::5355                 :::*                    LISTEN      -      

```
- ss：服务的监听地址范围，参数和netstat基本相同


⽹络服务管理 :
⽹络服务管理程序分为两种，分别为SysV和systemd
- service network start|stop|restart|status
- chkconfig -list network
- chkconfig --level 2345 network off禁用2345network
- systemctl list-unit-files NetworkManager.service
- systemctl start|stop|restart NetworkManger
- systemctl enable|disable NetworkManger

常⽤⽹络配置⽂件:
- ifcfg-eth0
- /etc/hosts

/etc/sysconfig/network-scripts/下每个ifcfg开头的文件都对应着一个网络接口，可以配置这些网卡。

route -n可以查看网关



### 软件包管理器的使用及软件包的安装
包管理器是⽅便软件安装、卸载，解决软件依赖关系的重要⼯具
- CentOS、RedHat 使⽤ yum 包管理器，软件安装包格式为 rpm
- Debian、Ubuntu 使⽤ apt 包管理器，软件安装包格式为 deb

#### rpm命令
rpm 包格式
- vim-common-7.4.10-5.el7.x86_64.rpm 
 软件名称 软件版本 系统版本 平台

rpm 命令常⽤参数
- -q 查询软件包
- -i 安装软件包（要加软件包完整名称）
- -e 卸载软件包


rpm -qa | more查看所有安装的软件包
rpm -q vim-common查看某个软件包
#### yum包管理器也叫yum仓库
rpm 包的问题
- 需要⾃⼰解决依赖关系
- 软件包来源不可靠


 CentOS yum 源
- http://mirror.centos.org/centos/7/
 国内镜像
- https://opsx.alibaba.com/mirror

yum makecache切换镜像后更新缓存

常⽤选项
- install 安装软件包
- remove 卸载软件包
- list| grouplist 查看软件包
- update 升级软件包



#### 源代码编译安装
- ⼆进制安装


源代码编译安装
- wget https://openresty.org/download/openresty-1.15.8.1.tar.gz
- tar -zxf openresty-VERSION.tar.gz
- cd openresty-VERSION/ 
- ./configure --prefix=/usr/local/openresty指定安装目录
- make -j2 指定两个内核
- make install





### 内核升级
rpm 格式内核（不能获得最新稳定版内核）
- 查看内核版本
- uname –r
- 升级内核版本
- yum install kernel-3.10.0
- 升级已安装的其他软件包和补丁
- yum update

源代码编译安装内核：
- 安装依赖包
- yum install gcc gcc-c++ make ncurses-devel openssl-devel elfutils-libelf-devel
- 下载并解压缩内核
- https://www.kernel.org
- tar xvf linux-5.1.10.tar.xz -C /usr/src/kernels
- 配置内核编译参数
- cd /usr/src/kernels/linux-5.1.10/
- make menuconfig | allyesconfig | allnoconfig
- 使⽤当前系统内核配置
- cp /boot/config-kernelversion.platform /usr/src/kernels/
linux-5.1.10/.config

- 查看 CPU
- lscpu
- 编译
- make -j2 all
- 安装内核
- make modules_install
- make install


### grub配置文件介绍
启动引导软件grub
grub 配置⽂件
- /etc/default/grub（主要关注：grub_default和grub_cmdline_linux）
- /etc/grub.d/
- /boot/grub2/grub.cfg
- grub2-mkconfig -o /boot/grub2/grub.cfg

使⽤单⽤户进⼊系统（忘记 root 密码）
重设root密码~


### ps top查看进程
查看命令
- ps   （ps -e  , ps -e | more,ps -ef | more,ps -eLf可查看线程数）
- pstree
- top（top -p 18746）
结论：
- 进程也是树形结构
- 进程和权限有着密不可分的关系

### 进程的控制、关联、通信
调整优先级
- nice 范围从-20 到 19 ，值越⼩优先级越⾼，抢占资源就越多
- renice 重新设置优先级（已经运行的程序更改优先级 renice -n 15 19314）
进程的作业控制（前后台切换）
- jobs
- & 符号

ctrl+z暂停 ctrl+c终止


进程通信：（比如管道、socket）
- 信号是进程间通信⽅式之⼀，典型⽤法是：终端⽤户输⼊中断命令，通过信号机制停⽌⼀个程序的运⾏。
- 使⽤信号的常⽤快捷键和命令
- kill -l
    - SIGINT 通知前台进程组终⽌进程 ctrl + c 
    - SIGKILL ⽴即结束程序，不能被阻塞和处理 kill -9 pid


### 守护进程
如何使你关掉终端也不杀死进程？
- 使⽤ nohup 与 & 符号配合运⾏⼀个命令
- nohup 命令使进程忽略 hangup（挂起）信号
- 把输出追加到nohup.out文件中
- 关掉终端后，继续执行的进程a的父进程--也就是终端结束，进程a成了孤儿进程，会被1号进程收留

守护进程(daemon)和⼀般进程有什么差别呢？（开机自启，输出会打印到socket通信程序，socket通过套接字与系统日志进行通讯。daemon进程占用的目录是根目录）(/var/log系统日志)





### screen和系统日志
使⽤ screen 命令
- screen 进⼊ screen 环境
- ctrl+a d 退出 (detached) screen 环境
- screen -ls 查看 screen 的会话
- screen -r sessionid 恢复会话

常⻅的系统⽇志/var/log（系统日志）
- message（常规日志）
- dmesg（内核运行相关信息）
- cron（周期性、计划任务日志信息）
- secur（安全日志）



### 服务管理工具systemctl
服务（提供常⻅功能的守护进程）集中管理⼯具
- service
    - /etc/init.d/
- systemctl
    - systemctl 常⻅操作
        - systemctl start | stop | restart | reload | enable(随开机运行) | disable |status 服务名称
        - 软件包安装的服务单元 /usr/lib/systemd/system/


systemctl 的服务配置
- [Unit]
    - Requires = 新的依赖服务
    - After = 新的依赖服务
- [Service]
- [Install]
    - 安装到哪个默认启动级别 /lib/systemd/system
    - systemctl get-default | set-default




### SElinux
安全组件，安全但复杂，且会降低服务器的性能，生产环境下一般被关闭

- MAC（强制访问控制）与 DAC（⾃主访问控制）
- 查看 SELinux 的命令
    - getenforce
    - /usr/sbin/sestatus
    - ps -Z and ls -Z and id -Z
- 关闭 SELinux
    - setenforce 0
    -  vim /etc/selinux/sysconfig


### 内存和磁盘管理

#### 内存和磁盘使⽤率查看
- free
如果不设swap分区，也就是虚拟内存，那当内存不足时，linux内核会随机杀掉占用内存较大的进程。一旦发现swap被占用了，尽量增大内存。

- top

- fdisk -l
- df
- du
- du 与 ls 的区别




#### ext4 ⽂件系统
Linux ⽀持多种⽂件系统，常⻅的有
- ext4
- xfs
- NTFS（需安装额外软件）

ext4 ⽂件系统基本结构⽐较复杂
- 超级块
- 超级块副本
- i 节点(inode)
- 数据块(datablock)

ext4 ⽂件系统深⼊理解
- 执⾏ mkdir 、touch、 vi 等命令后的内部操作
- 符号链接与硬链接
- facl



#### 磁盘配额的使⽤
⽤户磁盘配额
- xfs⽂件系统的⽤户磁盘配额 quota
- mkfs.xfs /dev/sdb1
- mkdir /mnt/disk1
- mount -o uquota,gquota /dev/sdb1 /mnt/disk1
- chmod 1777 /mnt/disk1
- xfs_quota -x -c ‘report -ugibh’ /mnt/disk1 
- xfs_quota -x -c ‘limit -u isoft=5 ihard=10 user1’ /mnt/disk1
#### 磁盘的分区与挂载
常⽤命令
- fdisk
- mkfs
- parted
- mount
常⻅配置⽂件
- /etc/fstab

#### 交换分区（虚拟内存）的查看与创建
增加交换分区的⼤⼩
- mkswap
- swapon
使⽤⽂件制作交换分区
- dd if=/dev/zero bs=4M count=1024 of=/swapfile

#### 软件 RAID 的使⽤
RAID 与软件 RAID 技术
- RAID 的常⻅级别及含义
    - RAID 0 striping 条带⽅式，提⾼单盘吞吐率
    - RAID 1 mirroring 镜像⽅式，提⾼可靠性
    - RAID 5 有奇偶校验
    - RAID 10 是RAID 1 与 RAID 0 的结合
- 软件 RAID 的使⽤

#### 逻辑卷管理
- 逻辑卷和⽂件系统的关系
- 为 Linux 创建逻辑卷
- 动态扩容逻辑卷

#### 系统综合状态查看
- 使⽤ sar 命令查看系统综合状态
- 使⽤第三⽅命令查看⽹络流量
- yum install epel-release
- yum install iftop
- iftop -P




## 4. Shell
### 什么是shell
- Shell 是命令解释器，用于解释用户对操作系统的操作

- Shell 有很多

- cat /etc/shells

- CentOS 7 默认使用的 Shell 是 bash




### linux的启动过程
BIOS-MBR-BootLoader(grub)-kernel-init-系统初始化-shell

### shell脚本
- UNIX 的哲学：一条命令只做一件事

- 为了组合命令和多次执行，使用脚本文件来保存需要执行的命令

- 赋予该文件执行权限（chmod u+rx filename）

标准的 Shell 脚本要包含哪些元素
- Sha-Bang

- 命令

- “#”号开头的注释

- chmod u+rx filename 可执行权限

- 执行命令 

    - bash ./filename.sh. 

    - ./filename.sh

    - source ./filename.sh

    - . filename.sh

内建命令和外部命令的区别
- 内建命令不需要创建子进程

- 内建命令对当前 Shell 生效


### 管道
管道与管道符
- 管道和信号一样，也是进程通信的方式之一

- 匿名管道（管道符）是 Shell 编程经常用到的通信工具

- 管道符是“|”，将前一个命令执行的结果传递给后面的命令

    - ps | cat

    - echo 123 | ps

子进程与子 Shell
- 子进程是 Shell 程序，称作子 Shell

- 内部命令的结果不会传递给子 Shell


### 重定向
重定向符号
- 一个进程默认会打开标准输入、标准输出、错误输出三个文件描述符

- 输入重定向符号 “ <”

- 输出重定向符号 “>” “>>” “2>” “&>”
### 变量查看
变量的查看方法

- echo

- ${变量名} 在部分情况下可以省略为 $变量名

### 变量作用范围
- 变量的默认作用范围

- 变量的导出

- export

### 环境变量、预定义变量与位置变量
系统环境变量
- 环境变量：每个 Shell 打开都可以获得到的变量

- set 和 env 命令

- $?

- $!

- $$ $0

- $PATH

- $PS1

环境变量配置文件
- 配置文件

- /etc/profile

- /etc/bashrc

- ~/.bashrc

- ~/.bash_profile

### 环境变量配置文件
### 数组
### 转义和引用
特殊字符
- 特殊字符：一个字符不仅有字面意义，还有元意（meta-meaning）

- # 注释

- ; 分号

- \ 转义符号

- “和’ 引号

转义符号
- 单个字符前的转义符号

- \n \r \t 单个字母的转义

- \$ \” \\ 单个非字母的转义

引用
- 常用的引用符号

- “ 双引号

- ‘ 单引号

- ` 反引号

### 运算符
运算符
- 赋值运算符

- 算数运算符

- 数字常量

- 双圆括号

### 特殊字符大全
### test比较
### if判断的使用
#### if-else判断的使用
### 嵌套if的使用
### case分支
### for的基本使用
### c语言风格的for
### while循环和until循环
### 循环的嵌套和break、continue语句
### 使用循环处理位置参数
### 自定义参数
### 系统函数库介绍
### 脚本资源控制
### 信号
### 一次性计划任务
### 周期性计划任务
### 为脚本加锁
## 5. 文本操作
### 元字符介绍
### find演示
### sed和awk介绍
### sed替换命令讲解
#### 加强版
### sed的其他常用命令
### sed多行模式空间
### 什么是sed的保持空间
### awk
## 6. 服务
### 防火墙概述
### iptables
#### iptables规则的基本使用演示
#### iptables过滤规则的使用
#### iptables nat表的使用
### firewalld
### SSH
#### SSH介绍之Telent明文漏洞
#### SSH服务演示
### FTP服务器vsftpd介绍与软件包安装
### vsftpd配置文件介绍
#### vsftp虚拟用户
### sanba服务演示
### NFS服务
### Nginx基本配置文件
### 使用Nginx配置域名虚拟主机
### LNMP环境搭建
### DNS服务原理
### NAS演示

