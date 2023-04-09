---
title: "Linux_commands"
date: 2022-12-04T12:42:31+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
- [sed](#sed)
  - [选定行的范围：,（逗号）](#选定行的范围逗号)
  - [多点编辑：e命令](#多点编辑e命令)
  - [从文件读入：r命令](#从文件读入r命令)
  - [写入文件：w命令](#写入文件w命令)
  - [追加（行下）：a\\命令](#追加行下a命令)
  - [插入（行上）：i\\命令](#插入行上i命令)
  - [下一个：n命令](#下一个n命令)
  - [变形：y命令](#变形y命令)
  - [退出：q命令](#退出q命令)
  - [保持和获取：h命令和G命令](#保持和获取h命令和g命令)
  - [保持和互换：h命令和x命令](#保持和互换h命令和x命令)
  - [脚本scriptfile](#脚本scriptfile)
  - [打印奇数行或偶数行](#打印奇数行或偶数行)
  - [打印匹配字符串的下一行](#打印匹配字符串的下一行)
- [cat](#cat)
  - [注意](#注意)
- [ps](#ps)
- [top](#top)
- [awk](#awk)
  - [awk模式和操作](#awk模式和操作)
    - [模式](#模式)
    - [操作](#操作)
  - [awk脚本基本结构](#awk脚本基本结构)
    - [awk的工作原理](#awk的工作原理)
  - [awk内置变量（预定义变量）](#awk内置变量预定义变量)
  - [将外部变量值传递给awk](#将外部变量值传递给awk)
  - [查找进程pid](#查找进程pid)
  - [awk运算与判断](#awk运算与判断)
    - [算术运算符](#算术运算符)
    - [赋值运算符](#赋值运算符)
    - [逻辑运算符](#逻辑运算符)
    - [正则运算符](#正则运算符)
    - [关系运算符](#关系运算符)
    - [其它运算符](#其它运算符)
    - [运算级优先级表](#运算级优先级表)
  - [awk高级输入输出](#awk高级输入输出)
    - [读取下一条记录](#读取下一条记录)
    - [简单地读取一条记录](#简单地读取一条记录)
    - [关闭文件](#关闭文件)
    - [输出到一个文件](#输出到一个文件)
  - [设置字段定界符](#设置字段定界符)
  - [流程控制语句](#流程控制语句)
    - [条件判断语句](#条件判断语句)
    - [循环语句](#循环语句)
    - [# while语句](#-while语句)
    - [# for循环](#-for循环)
    - [# do循环](#-do循环)
    - [其他语句](#其他语句)
  - [数组应用](#数组应用)
    - [数组的定义](#数组的定义)
    - [数组相关函数](#数组相关函数)
    - [二维、多维数组使用](#二维多维数组使用)
  - [内置函数](#内置函数)
    - [算术函数](#算术函数)
    - [字符串函数](#字符串函数)
    - [一般函数](#一般函数)
    - [时间函数](#时间函数)
- [xargs](#xargs)
    - [打印出执行的命令](#打印出执行的命令)
    - [使用 -p 选项确认执行的命令](#使用--p-选项确认执行的命令)
    - [执行多个命令](#执行多个命令)
    - [其他应用](#其他应用)
  - [子 Shell（Subshells）](#子-shellsubshells)
- [kill](#kill)
  - [内建命令](#内建命令)
    - [概要](#概要)
    - [主要用途](#主要用途)
    - [选项](#选项)
    - [参数](#参数)
    - [返回值](#返回值)
    - [例子](#例子)
    - [注意](#注意-1)
  - [外部命令](#外部命令)
    - [概要](#概要-1)
    - [主要用途](#主要用途-1)
    - [选项](#选项-1)
    - [参数](#参数-1)
    - [例子](#例子-1)
    - [返回值](#返回值-1)
    - [注意](#注意-2)
- [scp](#scp)
  - [语法](#语法)
  - [选项](#选项-2)
  - [参数](#参数-2)
  - [实例](#实例)
- [curl](#curl)
  - [补充说明](#补充说明)
    - [语法](#语法-1)
    - [选项](#选项-3)
    - [实例](#实例-1)
    - [要求返回是压缩的状态](#要求返回是压缩的状态)
- [wget](#wget)
  - [补充说明](#补充说明-1)
    - [语法](#语法-2)
    - [选项](#选项-4)
    - [参数](#参数-3)
    - [实例](#实例-2)
- [mv](#mv)
  - [补充说明](#补充说明-2)
  - [语法](#语法-3)
  - [选项](#选项-5)
  - [参数](#参数-4)
  - [实例](#实例-3)



> https://www.bookstack.cn/read/jaywcjlove-linux-command-1.9.1/README.md


## sed
强大的流式文本编辑器

处理时，把当前处理的行存储在临时缓冲区（`pattern space`）中，然后用`sed`命令处理缓冲区中的内容，完成后将缓冲区的内容送往屏幕，接着处理下一行，指导文件末尾。

主要用于：自动编辑一个或多个文件，简化对文件的反复操作，编写转换程序等等

命令格式：
```
sed [options] 'command' file(s)
sed [options] -f scriptfile file(s)
```

options:
```
1 -e<script>或--expression=<script>：以选项中的指定的script来处理输入的文本文件；
2 -f<script文件>或--file=<script文件>：以选项中指定的script文件来处理输入的文本文件；
3 -h或--help：显示帮助；
4 -n或--quiet或——silent：仅显示script处理后的结果；
5 -V或--version：显示版本信息。
6 -i： edit file(s) in place 就地编辑文件
```

```
sed '表达式' | sed '表达式'
等价于：
sed '表达式; 表达式'
```
sed表达式可以使用单引号来引用，但是如果表达式内部包含**变量字符串**，就需要使用双引号。
```
test=hello
echo hello WORLD | sed "s/$test/HELLO"
HELLO WORLD
```

commands:
```
(/(:或|) 定界符，若出现在样式内部需要进行转义\/)
a\ # 在当前行下面插入文本。
i\ # 在当前行上面插入文本。
c\ # 把选定的行改为新的文本。
d # 删除，删除选择的行。
    sed '/^$/d' file 删除空白行
    sed '2d' file 删除第二行
    sed '2,$d' file 删除第二行到末尾所有行
    sed '$d' file 删除最后一行
    sed '/^test/'d file 删除以test开头的行
D # 删除模板块的第一行。
s # 替换指定字符
    sed 's/book/books/' file
h # 拷贝模板块的内容到内存中的缓冲区。
H # 追加模板块的内容到内存中的缓冲区。
g # 获得内存缓冲区的内容，并替代当前模板块中的文本。
G # 获得内存缓冲区的内容，并追加到当前模板块文本的后面。
l # 列表不能打印字符的清单。
n # 读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。
N # 追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。
p # 打印模板块的行。
    sed -n ‘s/test/TEST/p’ file 只打印那些发生替换的行
P # (大写) 打印模板块的第一行。
q # 退出Sed。
b lable # 分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。
r file # 从file中读行。
t label # if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
T label # 错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
w file # 写并追加模板块到file末尾。  
W file # 写并追加模板块的第一行到file末尾。  
! # 表示后面的命令对所有没有被选定的行发生作用。  
= # 打印当前行号码。  
# # 把注释扩展到下一个换行符以前。
```

sed 替换标记：
```
g # 表示行内全面替换。  
    sed -i 's/book/books/g' file
    当需要从第N处匹配开始替换时，可以使用 /Ng：
    echo sksksksksksk | sed 's/sk/SK/2g'
    skSKSKSKSKSK
p # 表示打印行。  
w # 表示把行写入一个文件。  
x # 表示互换模板块中的文本和缓冲区中的文本。  
y # 表示把一个字符翻译为另外的字符（但是不用于正则表达式）
\1 # 子串匹配标记
& # 已匹配字符串标记
    echo this is a test line | sed 's/\w\+/[&]/g'
    [this] [is] [a] [test] [line]
    \w\+ 表示匹配一个单词，&表示之前匹配到的单词
    sed 's/^192.168.0.1/&localhost/' file
    192.168.0.1localhost
``` 

sed 元字符集：
```
^ # 匹配行开始，如：/^sed/匹配所有以sed开头的行。
$ # 匹配行结束，如：/sed$/匹配所有以sed结尾的行。
. # 匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。
* # 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。
[] # 匹配一个指定范围内的字符，如/[sS]ed/匹配sed和Sed。  
[^] # 匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。

\(..\) # 匹配子串，保存匹配的字符，如
    s/\(love\)able/\1rs，loveable被替换成lovers。
    echo this is digit 7 in a number | sed 's/digit \([0-9]\)/\1/'
    this is 7 in a number  第一个digit被删除了

& # 保存搜索字符用来替换其他字符，如s/love/ **&** /，love这成 **love** 。

\< # 匹配单词的开始，如:/\<love/匹配包含以love开头的单词的行。

\> # 匹配单词的结束，如/love\>/匹配包含以love结尾的单词的行。
x\{m\} # 重复字符x，m次，如：/0\{5\}/匹配包含5个0的行。
x\{m,\} # 重复字符x，至少m次，如：/0\{5,\}/匹配至少有5个0的行。
x\{m,n\} # 重复字符x，至少m次，不多于n次，如：/0\{5,10\}/匹配5~10个0的行。
```

### 选定行的范围：,（逗号）

所有在模板test和check所确定的范围内的行都被打印：
```
1. `sed -n '/test/,/check/p' file`
```
打印从第5行开始到第一个包含以test开始的行之间的所有行：
```
1. `sed -n '5,/^test/p' file`
```
对于模板test和west之间的行（包括test、west），每行的末尾用字符串aaa bbb替换：
```
1. `sed '/test/,/west/s/$/aaa bbb/' file`
```
### 多点编辑：e命令
\-e选项允许在同一行里执行多条命令：
```
1. `sed -e '1,5d' -e 's/test/check/' file`
```
上面sed表达式的第一条命令删除1至5行，第二条命令用check替换test。命令的执行顺序对结果有影响。如果两个命令都是替换命令，那么第一个替换命令将影响第二个替换命令的结果。

和 -e 等价的命令是 —expression：
```
1. `sed --expression='s/test/check/' --expression='/love/d' file`
```
### 从文件读入：r命令

file里的内容被读进来，显示在与test匹配的行后面，如果匹配多行，则file的内容将显示在所有匹配行的下面：
```
1. `sed '/test/r file' filename`
```
### 写入文件：w命令

在example中所有包含test的行都被写入file里：
```
1. `sed -n '/test/w file' example`
```
### 追加（行下）：a\\命令

将 this is a test line 追加到 以test 开头的行后面：
```
1. `sed '/^test/a\this is a test line' file`
```
在 test.conf 文件第2行之后插入 this is a test line：
```
1. `sed -i '2a\this is a test line' test.conf`
```
### 插入（行上）：i\\命令

将 this is a test line 追加到以test开头的行前面：
```
1. `sed '/^test/i\this is a test line' file`
```
在test.conf文件第5行之前插入this is a test line：
```
1. `sed -i '5i\this is a test line' test.conf`
```
### 下一个：n命令

如果test被匹配，则移动到匹配行的下一行，替换这一行的aa，变为bb，并打印该行，然后继续：
```
1. `sed '/test/{ n; s/aa/bb/; }' file`
```
### 变形：y命令

把1~10行内所有abcde转变为大写，注意，正则表达式元字符不能使用这个命令：
```
1. `sed '1,10y/abcde/ABCDE/' file`
```
### 退出：q命令

打印完第10行后，退出sed
```
1. `sed '10q' file`
```
### 保持和获取：h命令和G命令

在sed处理文件的时候，每一行都被保存在一个叫模式空间的临时缓冲区中，除非行被删除或者输出被取消，否则所有被处理的行都将 打印在屏幕上。接着模式空间被清空，并存入新的一行等待处理。
```
1. `sed -e '/test/h' -e '$G' file`
```
在这个例子里，匹配test的行被找到后，将存入模式空间，h命令将其复制并存入一个称为保持缓存区的特殊缓冲区内。第二条语句的意思是，当到达最后一行后，G命令取出保持缓冲区的行，然后把它放回模式空间中，且追加到现在已经存在于模式空间中的行的末尾。在这个例子中就是追加到最后一行。简单来说，任何包含test的行都被复制并追加到该文件的末尾。

### 保持和互换：h命令和x命令

互换模式空间和保持缓冲区的内容。也就是把包含test与check的行互换：
```
1. `sed -e '/test/h' -e '/check/x' file`
```
### 脚本scriptfile

sed脚本是一个sed的命令清单，启动Sed时以-f选项引导脚本文件名。Sed对于脚本中输入的命令非常挑剔，在命令的末尾不能有任何空白或文本，如果在一行中有多个命令，要用分号分隔。以#开头的行为注释行，且不能跨行。
```
1. `sed [options] -f scriptfile file(s)`
```
### 打印奇数行或偶数行

方法1：
```
1. `sed -n 'p;n' test.txt #奇数行`
2. `sed -n 'n;p' test.txt #偶数行`
```
方法2：
```
1. `sed -n '1~2p' test.txt #奇数行`
2. `sed -n '2~2p' test.txt #偶数行`
```
### 打印匹配字符串的下一行
```
1. `grep -A 1 SCC URFILE`
2. `sed -n '/SCC/{n;p}' URFILE`
3. `awk '/SCC/{getline; print}' URFILE`
```



## cat
[cat tail head tee](https://www.cnblogs.com/smallfa/p/5156924.html)
连接多个文件并打印到标准输出。

```
cat [OPTION]... [FILE]...
```
主要用途：

- 显示文件内容，如果没有文件或文件为`-`则读取标准输入。
- 将多个文件的内容进行连接并打印到标准输出。
- 显示文件内容中的不可见字符（控制字符、换行符、制表符等）。

参数：

FILE（可选）：要处理的文件，可以为一或多个。

选项：
```
长选项与短选项等价
-A, --show-all           等价于"-vET"组合选项。
-b, --number-nonblank    只对非空行编号，从1开始编号，覆盖"-n"选项。
-e                       等价于"-vE"组合选项。
-E, --show-ends          在每行的结尾显示'$'字符。
-n, --number             对所有行编号，从1开始编号。
-s, --squeeze-blank      压缩连续的空行到一行。
-t                       等价于"-vT"组合选项。
-T, --show-tabs          使用"^I"表示TAB（制表符）。
-u                       POSIX兼容性选项，无意义。
-v, --show-nonprinting   使用"^"和"M-"符号显示控制字符，除了LFD（line feed，即换行符'\n'）和TAB（制表符）。
--help                   显示帮助信息并退出。
--version                显示版本信息并退出。
```


示例：
```
# 合并显示多个文件
cat ./1.log ./2.log ./3.log
# 显示文件中的非打印字符、tab、换行符
cat -A test.log
# 压缩文件的空行
cat -s test.log
# 显示文件并在所有行开头附加行号
cat -n test.log
# 显示文件并在所有非空行开头附加行号
cat -b test.log
# 将标准输入的内容和文件内容一并显示
echo '######' |cat - test.log
```
### 注意

1. 该命令是`GNU coreutils`包中的命令，相关的帮助信息请查看`man -s 1 cat`或`info coreutils 'cat invocation'`。
2. 当使用`cat`命令查看**体积较大的文件**时，文本在屏幕上迅速闪过（滚屏），用户往往看不清所显示的内容，为了控制滚屏，可以按`Ctrl+s`键停止滚屏；按`Ctrl+q`键恢复滚屏；按`Ctrl+c`（中断）键可以终止该命令的执行，返回Shell提示符状态。
3. 建议您查看**体积较大的文件**时使用`less`、`more`命令或`emacs`、`vi`等文本编辑器。


## ps

查看系统进程状态

ps命令 用于报告当前系统的进程状态。可以搭配kill指令随时中断、删除不必要的程序。ps命令是最基本同时也是非常强大的进程查看命令，使用该命令可以确定**有哪些进程正在运行和运行的状态、进程是否结束、进程有没有僵死、哪些进程占用了过多的资源等等**，总之大部分信息都是可以通过执行该命令得到的。

```
ps(选项)

-a：显示所有终端机下执行的程序，除了阶段作业领导者之外。
a：显示现行终端机下的所有程序，包括其他用户的程序。
-A：显示所有程序。
-c：显示CLS和PRI栏位。
c：列出程序时，显示每个程序真正的指令名称，而不包含路径，选项或常驻服务的标示。
-C<指令名称>：指定执行指令的名称，并列出该指令的程序的状况。
-d：显示所有程序，但不包括阶段作业领导者的程序。
-e：此选项的效果和指定"A"选项相同。
e：列出程序时，显示每个程序所使用的环境变量。
-f：显示UID,PPIP,C与STIME栏位。
f：用ASCII字符显示树状结构，表达程序间的相互关系。
-g<群组名称>：此选项的效果和指定"-G"选项相同，当亦能使用阶段作业领导者的名称来指定。
g：显示现行终端机下的所有程序，包括群组领导者的程序。
-G<群组识别码>：列出属于该群组的程序的状况，也可使用群组名称来指定。
h：不显示标题列。
-H：显示树状结构，表示程序间的相互关系。
-j或j：采用工作控制的格式显示程序状况。
-l或l：采用详细的格式来显示程序状况。
L：列出栏位的相关信息。
-m或m：显示所有的执行绪。
n：以数字来表示USER和WCHAN栏位。
-N：显示所有的程序，除了执行ps指令终端机下的程序之外。
-O<格式>：预加载默认列
-o,o  --format <format> 用户自定义显示字段
-p<程序识别码>：指定程序识别码，并列出该程序的状况。
p<程序识别码>：此选项的效果和指定"-p"选项相同，只在列表格式方面稍有差异。
r：只列出现行终端机正在执行中的程序。
-s<阶段作业>：指定阶段作业的程序识别码，并列出隶属该阶段作业的程序的状况。
s：采用程序信号的格式显示程序状况。
S：列出程序时，包括已中断的子程序资料。
-t<终端机编号>：指定终端机编号，并列出属于该终端机的程序的状况。
t<终端机编号>：此选项的效果和指定"-t"选项相同，只在列表格式方面稍有差异。
-T：显示现行终端机下的所有程序。
-u<用户识别码>：此选项的效果和指定"-U"选项相同。
u：以用户为主的格式来显示程序状况。
-U<用户识别码>：列出属于该用户的程序的状况，也可使用用户名称来指定。
U<用户名称>：列出属于该用户的程序的状况。
v：采用虚拟内存的格式显示程序状况。
-V或V：显示版本信息。
-w或w：采用宽阔的格式来显示程序状况。　
x：显示所有程序，不以终端机来区分。
X：采用旧式的Linux i386登陆格式显示程序状况。
-y：配合选项"-l"使用时，不显示F(flag)栏位，并以RSS栏位取代ADDR栏位　。
-<程序识别码>：此选项的效果和指定"p"选项相同。
--cols<每列字符数>：设置每列的最大字符数。
--columns<每列字符数>：此选项的效果和指定"--cols"选项相同。
--cumulative：此选项的效果和指定"S"选项相同。
--deselect：此选项的效果和指定"-N"选项相同。
--forest：此选项的效果和指定"f"选项相同。
--headers：重复显示标题列。
--help：在线帮助。
--info：显示排错信息。
--lines<显示列数>：设置显示画面的列数。
--no-headers：此选项的效果和指定"h"选项相同，只在列表格式方面稍有差异。
--group<群组名称>：此选项的效果和指定"-G"选项相同。
--Group<群组识别码>：此选项的效果和指定"-G"选项相同。
--pid<程序识别码>：此选项的效果和指定"-p"选项相同。
--rows<显示列数>：此选项的效果和指定"--lines"选项相同。
--sid<阶段作业>：此选项的效果和指定"-s"选项相同。
--tty<终端机编号>：此选项的效果和指定"-t"选项相同。
--user<用户名称>：此选项的效果和指定"-U"选项相同。
--User<用户识别码>：此选项的效果和指定"-U"选项相同。
--version：此选项的效果和指定"-V"选项相同。
--widty<每列字符数>：此选项的效果和指定"-cols"选项相同。
```


示例：
```
ps axo pid,comm,pcpu # （自定义显示字段）查看进程的PID、名称以及CPU 占用率
ps aux | sort -rnk 4 # （显示详细字段）按内存资源的使用量对进程进行排序
ps aux | sort -nk 3  # 按 CPU 资源的使用量对进程进行排序
ps -A # 显示所有进程信息
ps -u root # 显示指定用户信息
ps -efL # 查看线程数
ps -e -o "%C : %p :%z : %a"|sort -k5 -nr # 查看进程并按内存使用大小排列
ps -ef # 显示所有进程信息，连同命令行
ps -ef | grep ssh # ps 与grep 常用组合用法，查找特定进程
ps -C nginx # 通过名字或命令搜索进程
ps aux --sort=-pcpu,+pmem # CPU或者内存进行排序,-降序，+升序
ps -f --forest -C nginx # 用树的风格显示进程的层次关系
ps -o pid,uname,comm -C nginx # 显示一个父进程的子进程
ps -e -o pid,uname=USERNAME,pcpu=CPU_USAGE,pmem,comm # 重定义标签
ps -e -o pid,comm,etime # 显示进程运行的时间
ps -aux | grep named # 查看named进程详细信息
ps -o command -p 91730 | sed -n 2p # 通过进程id获取服务名称
```



## top
显示或管理执行中的程序。


top命令 可以实时动态地查看系统的整体运行情况，是一个综合了多方信息监测系统性能和运行信息的实用工具。通过top命令所提供的互动式界面，用热键可以管理。


```
top(选项)

-b：以批处理模式操作；
-c：显示完整的治命令；
-d：屏幕刷新间隔时间；
-I：忽略失效过程；
-s：保密模式；
-S：累积模式；
-i<时间>：设置间隔时间；
-u<用户名>：指定用户名；
-p<进程号>：指定进程；
-n<次数>：循环显示的次数。
```

示例：
```
top - 09:44:56 up 16 days, 21:23,  1 user,  load average: 9.59, 4.75, 1.92
Tasks: 145 total,   2 running, 143 sleeping,   0 stopped,   0 zombie
Cpu(s): 99.8%us,  0.1%sy,  0.0%ni,  0.2%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:   4147888k total,  2493092k used,  1654796k free,   158188k buffers
Swap:  5144568k total,       56k used,  5144512k free,  2013180k cached
```


## awk
文本和数据进行处理的编程语言

awk 是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自标准输入(stdin)、一个或多个文件，或其它命令的输出。它支持用户自定义函数和动态正则表达式等先进功能，是linux/unix下的一个强大编程工具。**它在命令行中使用，但更多是作为脚本来使用**。awk有很多内建的功能，比如数组、函数等，这是它和C语言的相同之处，灵活性是awk最大的优势。

```
awk [options] 'script' var=value file(s)
awk [options] -f scriptfile var=value file(s)
```
**常用命令选项**

- **\-F fs** fs指定输入分隔符，fs可以是字符串或正则表达式，如-F:，默认的分隔符是连续的空格或制表符
- **\-v var=value** 赋值一个用户定义变量，将外部变量传递给awk
- **\-f scripfile** 从脚本文件中读取awk命令
- **\-m\[fr\] val** 对val值设置内在限制，-mf选项限制分配给val的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。

### awk模式和操作

awk脚本是由模式和操作组成的。

#### 模式

模式可以是以下任意一个：

- /正则表达式/：使用通配符的扩展集。
- 关系表达式：使用运算符进行操作，可以是字符串或数字的比较测试。
- 模式匹配表达式：用运算符`~`（匹配）和`!~`（不匹配）。
- BEGIN语句块、pattern语句块、END语句块：参见awk的工作原理

#### 操作

操作由一个或多个命令、函数、表达式组成，之间由换行符或分号隔开，并位于大括号内，主要部分是：

- 变量或数组赋值
- 输出命令
- 内置函数
- 控制流语句

### awk脚本基本结构

```
1. `awk 'BEGIN{ print "start" } pattern{ commands } END{ print "end" }' file`
```
一个awk脚本通常由：BEGIN语句块、能够使用模式匹配的通用语句块、END语句块3部分组成，这三个部分是可选的。任意一个部分都可以不出现在脚本中，脚本通常是被 **单引号** 中，例如：

```
1. `awk 'BEGIN{ i=0 } { i++ } END{ print i }' filename`
```
#### awk的工作原理

```
1. `awk 'BEGIN{ commands } pattern{ commands } END{ commands }'`
```
- 第一步：执行`BEGIN{ commands }`语句块中的语句；
- 第二步：从文件或标准输入(stdin)读取一行，然后执行`pattern{ commands }`语句块，它逐行扫描文件，从第一行到最后一行重复这个过程，直到文件全部被读取完毕。
- 第三步：当读至输入流末尾时，执行`END{ commands }`语句块。
    
    **BEGIN语句块** 在awk开始从输入流中读取行 **之前** 被执行，这是一个可选的语句块，比如变量初始化、打印输出表格的表头等语句通常可以写在BEGIN语句块中。
    
    **END语句块** 在awk从输入流中读取完所有的行 **之后** 即被执行，比如打印所有行的分析结果这类信息汇总都是在END语句块中完成，它也是一个可选语句块。
    
    **pattern语句块** 中的通用命令是最重要的部分，它也是可选的。如果没有提供pattern语句块，则默认执行`{ print }`，即打印每一个读取到的行，awk读取的每一行都会执行该语句块。
    
    **示例**
    

```
1. `echo -e "A line 1\nA line 2" | awk 'BEGIN{ print "Start" } { print } END{ print "End" }'`
2. `Start`
3. `A line 1`
4. `A line 2`
5. `End`
```
当使用不带参数的`print`时，它就打印当前行，当`print`的参数是以逗号进行分隔时，打印时则以空格作为定界符。在awk的print语句块中双引号是被当作拼接符使用，例如：

```
1. `echo | awk '{ var1="v1"; var2="v2"; var3="v3"; print var1,var2,var3; }'` 
2. `v1 v2 v3`
```
双引号拼接使用：

```
1. `echo | awk '{ var1="v1"; var2="v2"; var3="v3"; print var1"="var2"="var3; }'`
2. `v1=v2=v3`
```
{ }类似一个循环体，会对文件中的每一行进行迭代，通常变量初始化语句（如：i=0）以及打印文件头部的语句放入BEGIN语句块中，将打印的结果等语句放在END语句块中。

### awk内置变量（预定义变量）

说明：\[A\]\[N\]\[P\]\[G\]表示第一个支持变量的工具，\[A\]=awk、\[N\]=nawk、\[P\]=POSIXawk、\[G\]=gawk

```
1.  `**$n** 当前记录的第n个字段，比如n为1表示第一个字段，n为2表示第二个字段。` 
2.  `**$0** 这个变量包含执行过程中当前行的文本内容。`
3. `[N] **ARGC** 命令行参数的数目。`
4. `[G] **ARGIND** 命令行中当前文件的位置（从0开始算）。`
5. `[N] **ARGV** 包含命令行参数的数组。`
6. `[G] **CONVFMT** 数字转换格式（默认值为%.6g）。`
7. `[P] **ENVIRON** 环境变量关联数组。`
8. `[N] **ERRNO** 最后一个系统错误的描述。`
9. `[G] **FIELDWIDTHS** 字段宽度列表（用空格键分隔）。`
10. `[A] **FILENAME** 当前输入文件的名。`
11. `[P] **FNR** 同NR，但相对于当前文件。`
12. `[A] **FS** 字段分隔符（默认是任何空格）。`
13. `[G] **IGNORECASE** 如果为真，则进行忽略大小写的匹配。`
14. `[A] **NF** 表示字段数，在执行过程中对应于当前的字段数。`
15. `[A] **NR** 表示记录数，在执行过程中对应于当前的行号。`
16. `[A] **OFMT** 数字的输出格式（默认值是%.6g）。`
17. `[A] **OFS** 输出字段分隔符（默认值是一个空格）。`
18. `[A] **ORS** 输出记录分隔符（默认值是一个换行符）。`
19. `[A] **RS** 记录分隔符（默认是一个换行符）。`
20. `[N] **RSTART** 由match函数所匹配的字符串的第一个位置。`
21. `[N] **RLENGTH** 由match函数所匹配的字符串的长度。`
22. `[N] **SUBSEP** 数组下标分隔符（默认值是34）。`
```
转义序列

```
1. `\\ \自身`
2. `\$ 转义$`
3. `\t 制表符`
4. `\b 退格符`
5. `\r 回车符`
6. `\n 换行符`
7. `\c 取消换行`
```
**示例**

```
1. `echo -e "line1 f2 f3\nline2 f4 f5\nline3 f6 f7" | awk '{print "Line No:"NR", No of fields:"NF, "$0="$0, "$1="$1, "$2="$2, "$3="$3}'` 
2. `Line No:1, No of fields:3 $0=line1 f2 f3 $1=line1 $2=f2 $3=f3`
3. `Line No:2, No of fields:3 $0=line2 f4 f5 $1=line2 $2=f4 $3=f5`
4. `Line No:3, No of fields:3 $0=line3 f6 f7 $1=line3 $2=f6 $3=f7`
```
使用`print $NF`可以打印出一行中的最后一个字段，使用`$(NF-1)`则是打印倒数第二个字段，其他以此类推：

```
1. `echo -e "line1 f2 f3\n line2 f4 f5" | awk '{print $NF}'`
2. `f3`
3. `f5`
```
```
1. `echo -e "line1 f2 f3\n line2 f4 f5" | awk '{print $(NF-1)}'`
2. `f2`
3. `f4`
```
打印每一行的第二和第三个字段：

```
1. `awk '{ print $2,$3 }' filename`
```
统计文件中的行数：

```
1. `awk 'END{ print NR }' filename`
```
以上命令只使用了END语句块，在读入每一行的时，awk会将NR更新为对应的行号，当到达最后一行NR的值就是最后一行的行号，所以END语句块中的NR就是文件的行数。

一个每一行中第一个字段值累加的例子：

```
1. `seq 5 | awk 'BEGIN{ sum=0; print "总和：" } { print $1"+"; sum+=$1 } END{ print "等于"; print sum }'` 
2. `总和：`
3. `1+`
4. `2+`
5. `3+`
6. `4+`
7. `5+`
8. `等于`
9. `15`
```
### 将外部变量值传递给awk

借助 **`-v`选项** ，可以将外部值（并非来自stdin）传递给awk：

```
1. `VAR=10000`
2. `echo | awk -v VARIABLE=$VAR '{ print VARIABLE }'`
```
另一种传递外部变量方法：

```
1. `var1="aaa"`
2. `var2="bbb"`
3. `echo | awk '{ print v1,v2 }' v1=$var1 v2=$var2`
```
当输入来自于文件时使用：

```
1. `awk '{ print v1,v2 }' v1=$var1 v2=$var2 filename`
```
以上方法中，变量之间用空格分隔作为awk的命令行参数跟随在BEGIN、{}和END语句块之后。

### 查找进程pid

```
1. `netstat -antup | grep 7770 | awk '{ print $NF NR}' | awk '{ print $1}'`
```
### awk运算与判断

作为一种程序设计语言所应具有的特点之一，awk支持多种运算，这些运算与C语言提供的基本相同。awk还提供了一系列内置的运算函数（如log、sqr、cos、sin等）和一些用于对字符串进行操作（运算）的函数（如length、substr等等）。这些函数的引用大大的提高了awk的运算功能。作为对条件转移指令的一部分，关系判断是每种程序设计语言都具备的功能，awk也不例外，awk中允许进行多种测试，作为样式匹配，还提供了模式匹配表达式~（匹配）和!~（不匹配）。作为对测试的一种扩充，awk也支持用逻辑运算符。

#### 算术运算符


例：

```
1. `awk 'BEGIN{a="b";print a++,++a;}'`
2. `0 2`
``` 
注意：所有用作算术运算符进行操作，操作数自动转为数值，所有非数值都变为0

#### 赋值运算符

例：

```
1. `a+=5; 等价于：a=a+5; 其它同类`
```
#### 逻辑运算符

例：

```
1. `awk 'BEGIN{a=1;b=2;print (a>5 && b<=2),(a>5 || b<=2);}'`
2. `0 1`
```
#### 正则运算符

~ !~ :匹配正则表达式和不匹配正则表达式

```
1. `^ 行首`
2. `$ 行尾`
3. `. 除了换行符以外的任意单个字符`
4. `* 前导字符的零个或多个`
5. `.* 所有字符`
6. `[] 字符组内的任一字符`
7. `[^]对字符组内的每个字符取反(不匹配字符组内的每个字符)`
8. `^[^] 非字符组内的字符开头的行`
9. `[a-z] 小写字母`
10. `[A-Z] 大写字母`
11. `[a-Z] 小写和大写字母`
12. `[0-9] 数字`
13. `\< 单词头单词一般以空格或特殊字符做分隔,连续的字符串被当做单词`
14. `\> 单词尾`
```
> 正则需要用 /正则/ 包围住

例：

```
1. `awk 'BEGIN{a="100testa";if(a ~ /^100*/){print "ok";}}'`
2. `ok`
```
#### 关系运算符

例：

```
1. `awk 'BEGIN{a=11;if(a >= 9){print "ok";}}'`
2. `ok`
```
注意：> < 可以作为字符串比较，也可以用作数值比较，关键看操作数如果是字符串就会转换为字符串比较。两个都为数字才转为数值比较。字符串比较：按照ASCII码顺序比较。

#### 其它运算符

```
$ :字段引用
空格 :字符串连接符
?: :C条件表达式
in :数组中是否存在某键值
```

例：

```
1. `awk 'BEGIN{a="b";print a=="b"?"ok":"err";}'`
2. `ok`
```

```
1. `awk 'BEGIN{a="b";arr[0]="b";arr[1]="c";print (a in arr);}'`
2. `0`
```

```
1. `awk 'BEGIN{a="b";arr[0]="b";arr["b"]="c";print (a in arr);}'`
2. `1`
```

#### 运算级优先级表

!级别越高越优先  
级别越高越优先

### awk高级输入输出

#### 读取下一条记录

awk中`next`语句使用：在循环逐行匹配，如果遇到next，就会跳过当前行，直接忽略下面语句。而进行下一行匹配。next语句一般用于多行合并：

```
1. `cat text.txt`
2. `a`
3. `b`
4. `c`
5. `d`
6. `e`

8. `awk 'NR%2==1{next}{print NR,$0;}' text.txt`
9. `2 b`
10. `4 d`
```
当记录行号除以2余1，就跳过当前行。下面的`print NR,$0`也不会执行。下一行开始，程序有开始判断`NR%2`值。这个时候记录行号是`：2` ，就会执行下面语句块：`'print NR,$0'`

分析发现需要将包含有“web”行进行跳过，然后需要将内容与下面行合并为一行：

```
1. `cat text.txt`
2. `web01[192.168.2.100]`
3. `httpd            ok`
4. `tomcat               ok`
5. `sendmail               ok`
6. `web02[192.168.2.101]`
7. `httpd            ok`
8. `postfix               ok`
9. `web03[192.168.2.102]`
10. `mysqld            ok`
11. `httpd               ok`
12. `0`
13. `awk '/^web/{T=$0;next;}{print T":"t,$0;}' text.txt`
14. `web01[192.168.2.100]: httpd            ok`
15. `web01[192.168.2.100]: tomcat               ok`
16. `web01[192.168.2.100]: sendmail               ok`
17. `web02[192.168.2.101]: httpd            ok`
18. `web02[192.168.2.101]: postfix               ok`
19. `web03[192.168.2.102]: mysqld            ok`
20. `web03[192.168.2.102]: httpd               ok`
```
#### 简单地读取一条记录

`awk getline`用法：输出重定向需用到`getline函数`。getline从标准输入、管道或者当前正在处理的文件之外的其他输入文件获得输入。它负责从输入获得下一行的内容，并给NF,NR和FNR等内建变量赋值。如果得到一条记录，getline函数返回1，如果到达文件的末尾就返回0，如果出现错误，例如打开文件失败，就返回-1。

getline语法：getline var，变量var包含了特定行的内容。

awk getline从整体上来说，用法说明：

- **当其左右无重定向符`|`或`<`时：** getline作用于当前文件，读入当前文件的第一行给其后跟的变量`var`或`$0`（无变量），应该注意到，由于awk在处理getline之前已经读入了一行，所以getline得到的返回结果是隔行的。
- **当其左右有重定向符`|`或`<`时：** getline则作用于定向输入文件，由于该文件是刚打开，并没有被awk读入一行，只是getline读入，那么getline返回的是该文件的第一行，而不是隔行。

**示例：**

执行linux的`date`命令，并通过管道输出给`getline`，然后再把输出赋值给自定义变量out，并打印它：

```
1. `awk 'BEGIN{ "date" | getline out; print out }' test`
```
执行shell的date命令，并通过管道输出给getline，然后getline从管道中读取并将输入赋值给out，split函数把变量out转化成数组mon，然后打印数组mon的第二个元素：

```
1. `awk 'BEGIN{ "date" | getline out; split(out,mon); print mon[2] }' test`
```
命令ls的输出传递给geline作为输入，循环使getline从ls的输出中读取一行，并把它打印到屏幕。这里没有输入文件，因为BEGIN块在打开输入文件前执行，所以可以忽略输入文件。

```
1. `awk 'BEGIN{ while( "ls" | getline) print }'`
```
#### 关闭文件

awk中允许在程序中关闭一个输入或输出文件，方法是使用awk的close语句。

```
1. `close("filename")`
```
filename可以是getline打开的文件，也可以是stdin，包含文件名的变量或者getline使用的确切命令。或一个输出文件，可以是stdout，包含文件名的变量或使用管道的确切命令。

#### 输出到一个文件

awk中允许用如下方式将结果输出到一个文件：

```
1. `echo | awk '{printf("hello word!n") > "datafile"}'`
2. `# 或`
3. `echo | awk '{printf("hello word!n") >> "datafile"}'`
```
### 设置字段定界符

默认的字段定界符是空格，可以使用`-F "定界符"` 明确指定一个定界符：

```
1. `awk -F: '{ print $NF }' /etc/passwd`
2. `# 或`
3. `awk 'BEGIN{ FS=":" } { print $NF }' /etc/passwd`
```
在`BEGIN语句块`中则可以用`OFS=“定界符”`设置输出字段的定界符。

### 流程控制语句

在linux awk的while、do-while和for语句中允许使用break,continue语句来控制流程走向，也允许使用exit这样的语句来退出。break中断当前正在执行的循环并跳到循环外执行下一条语句。if 是流程选择用法。awk中，流程控制语句，语法结构，与c语言类型。有了这些语句，其实很多shell程序都可以交给awk，而且性能是非常快的。下面是各个语句用法。

#### 条件判断语句

```
1. `if(表达式)`
2.  `语句1`
3. `else`
4.  `语句2`
```
格式中语句1可以是多个语句，为了方便判断和阅读，最好将多个语句用{}括起来。awk分枝结构允许嵌套，其格式为：

```
1. `if(表达式)`
2.  `{语句1}`
3. `else if(表达式)`
4.  `{语句2}`
5. `else`
6.  `{语句3}`
```
示例：

```
1. `awk 'BEGIN{`
2. `test=100;`
3. `if(test>90){`
4.  `print "very good";`
5.  `}`
6.  `else if(test>60){`
7.  `print "good";`
8.  `}`
9.  `else{`
10.  `print "no pass";`
11.  `}`
12. `}'`

14. `very good`
```
每条命令语句后面可以用`;` **分号** 结尾。

#### 循环语句

#### \# while语句

```
1. `while(表达式)`
2.  `{语句}`
```
示例：

```
1. `awk 'BEGIN{`
2. `test=100;`
3. `total=0;`
4. `while(i<=test){`
5.  `total+=i;`
6.  `i++;`
7. `}`
8. `print total;`
9. `}'`
10. `5050`
```
#### \# for循环

for循环有两种格式：

格式1：

```
1. `for(变量 in 数组)`
2.  `{语句}`
```
示例：

```
1. `awk 'BEGIN{`
2. `for(k in ENVIRON){`
3.  `print k"="ENVIRON[k];`
4. `}`

6. `}'`
7. `TERM=linux`
8. `G_BROKEN_FILENAMES=1`
9. `SHLVL=1`
10. `pwd=/root/text`
11. `...`
12. `logname=root`
13. `HOME=/root`
14. `SSH_CLIENT=192.168.1.21 53087 22`
```
注：ENVIRON是awk常量，是子典型数组。

格式2：

```
1. `for(变量;条件;表达式)`
2.  `{语句}`
```
示例：

```
1. `awk 'BEGIN{`
2. `total=0;`
3. `for(i=0;i<=100;i++){`
4.  `total+=i;`
5. `}`
6. `print total;`
7. `}'`
8. `5050`
```
#### \# do循环

```
1. `do`
2. `{语句} while(条件)`
```
例子：

```
1. `awk 'BEGIN{` 
2. `total=0;`
3. `i=0;`
4. `do {total+=i;i++;} while(i<=100)`
5.  `print total;`
6. `}'`
7. `5050`
```
#### 其他语句

- **break** 当 break 语句用于 while 或 for 语句时，导致退出程序循环。
- **continue** 当 continue 语句用于 while 或 for 语句时，使程序循环移动到下一个迭代。
- **next** 能能够导致读入下一个输入行，并返回到脚本的顶部。这可以避免对当前输入行执行其他的操作过程。
- **exit** 语句使主输入循环退出并将控制转移到END,如果END存在的话。如果没有定义END规则，或在END中应用exit语句，则终止脚本的执行。

### 数组应用

数组是awk的灵魂，处理文本中最不能少的就是它的数组处理。因为数组索引（下标）可以是数字和字符串在awk中数组叫做关联数组(associative arrays)。awk 中的数组不必提前声明，也不必声明大小。数组元素用0或空字符串来初始化，这根据上下文而定。

#### 数组的定义

数字做数组索引（下标）：

```
1. `Array[1]="sun"`
2. `Array[2]="kai"`
```
字符串做数组索引（下标）：

```
1. `Array["first"]="www"`
2. `Array"[last"]="name"`
3. `Array["birth"]="1987"`
```
使用中`print Array[1]`会打印出sun；使用`print Array[2]`会打印出kai；使用`print["birth"]`会得到1987。

**读取数组的值**

```
1. `{ for(item in array) {print array[item]}; } #输出的顺序是随机的`
2. `{ for(i=1;i<=len;i++) {print array[i]}; } #Len是数组的长度`
```
#### 数组相关函数

**得到数组长度：**

```
1. `awk 'BEGIN{info="it is a test";lens=split(info,tA," ");print length(tA),lens;}'`
2. `4 4`
```
length返回字符串以及数组长度，split进行分割字符串为数组，也会返回分割得到数组长度。

```
1. `awk 'BEGIN{info="it is a test";split(info,tA," ");print asort(tA);}'`
2. `4`
```
asort对数组进行排序，返回数组长度。

**输出数组内容（无序，有序输出）：**

```
1. `awk 'BEGIN{info="it is a test";split(info,tA," ");for(k in tA){print k,tA[k];}}'`
2. `4 test`
3. `1 it`
4. `2 is`
5. `3 a`
```
`for…in`输出，因为数组是关联数组，默认是无序的。所以通过`for…in`得到是无序的数组。如果需要得到有序数组，需要通过下标获得。

```
1. `awk 'BEGIN{info="it is a test";tlen=split(info,tA," ");for(k=1;k<=tlen;k++){print k,tA[k];}}'`
2. `1 it`
3. `2 is`
4. `3 a`
5. `4 test`
```
注意：数组下标是从1开始，与C数组不一样。

**判断键值存在以及删除键值：**

```
1. `# 错误的判断方法：`
2. `awk 'BEGIN{tB["a"]="a1";tB["b"]="b1";if(tB["c"]!="1"){print "no found";};for(k in tB){print k,tB[k];}}'` 
3. `no found`
4. `a a1`
5. `b b1`
6. `c`
```
以上出现奇怪问题，`tB[“c”]`没有定义，但是循环时候，发现已经存在该键值，它的值为空，这里需要注意，awk数组是关联数组，只要通过数组引用它的key，就会自动创建改序列。

```
1. `# 正确判断方法：`
2. `awk 'BEGIN{tB["a"]="a1";tB["b"]="b1";if( "c" in tB){print "ok";};for(k in tB){print k,tB[k];}}'` 
3. `a a1`
4. `b b1`
```
`if(key in array)`通过这种方法判断数组中是否包含`key`键值。

```
1. `#删除键值：`
2. `awk 'BEGIN{tB["a"]="a1";tB["b"]="b1";delete tB["a"];for(k in tB){print k,tB[k];}}'` 
3. `b b1`
```
`delete array[key]`可以删除，对应数组`key`的，序列值。

#### 二维、多维数组使用

awk的多维数组在本质上是一维数组，更确切一点，awk在存储上并不支持多维数组。awk提供了逻辑上模拟二维数组的访问方式。例如，`array[2,4]=1`这样的访问是允许的。awk使用一个特殊的字符串`SUBSEP(�34)`作为分割字段，在上面的例子中，关联数组array存储的键值实际上是2�344。

类似一维数组的成员测试，多维数组可以使用`if ( (i,j) in array)`这样的语法，但是下标必须放置在圆括号中。类似一维数组的循环访问，多维数组使用`for ( item in array )`这样的语法遍历数组。与一维数组不同的是，多维数组必须使用`split()`函数来访问单独的下标分量。

```
1. `awk 'BEGIN{`
2. `for(i=1;i<=9;i++){`
3.  `for(j=1;j<=9;j++){`
4.  `tarr[i,j]=i*j; print i,"*",j,"=",tarr[i,j];`
5.  `}`
6. `}`
7. `}'`
8. `1 * 1 = 1`
9. `1 * 2 = 2`
10. `1 * 3 = 3`
11. `1 * 4 = 4`
12. `1 * 5 = 5`
13. `1 * 6 = 6` 
14. `...`
15. `9 * 6 = 54`
16. `9 * 7 = 63`
17. `9 * 8 = 72`
18. `9 * 9 = 81`
```
可以通过`array[k,k2]`引用获得数组内容。

另一种方法：

```
1. `awk 'BEGIN{`
2. `for(i=1;i<=9;i++){`
3.  `for(j=1;j<=9;j++){`
4.  `tarr[i,j]=i*j;`
5.  `}`
6. `}`
7. `for(m in tarr){`
8.  `split(m,tarr2,SUBSEP); print tarr2[1],"*",tarr2[2],"=",tarr[m];`
9. `}`
10. `}'`
```
### 内置函数

awk内置函数，主要分以下3种类似：算数函数、字符串函数、其它一般函数、时间函数。

#### 算术函数

```
atan2( y, x ):返回 y/x 的反正切。

cos( x ):返回 x 的余弦；x 是弧度。

sin( x ):返回 x 的正弦；x 是弧度。

exp( x ):返回 x 幂函数。

log( x ):返回 x 的自然对数。

sqrt( x ):返回 x 平方根。

int( x ):返回 x 的截断至整数的值。

rand( ):返回任意数字 n，其中 0 <= n < 1。

srand( \[expr\] ):将 rand 函数的种子值设置为 Expr 参数的值，或如果省略 Expr 参数则使用某天的时间。返回先前的种子值。
```
举例说明：

```
1. `awk 'BEGIN{OFMT="%.3f";fs=sin(1);fe=exp(10);fl=log(10);fi=int(3.1415);print fs,fe,fl,fi;}'`
2. `0.841 22026.466 2.303 3`
```
OFMT 设置输出数据格式是保留3位小数。

获得随机数：

```
1. `awk 'BEGIN{srand();fr=int(100*rand());print fr;}'`
2. `78`
3. `awk 'BEGIN{srand();fr=int(100*rand());print fr;}'`
4. `31`
5. `awk 'BEGIN{srand();fr=int(100*rand());print fr;}'`
6. `41`
```
#### 字符串函数

gsub( Ere, Repl, \[ In \] )

除了正则表达式所有具体值被替代这点，它和 sub 函数完全一样地执行。

sub( Ere, Repl, \[ In \] )

用 Repl 参数指定的字符串替换 In 参数指定的字符串中的由 Ere 参数指定的扩展正则表达式的第一个具体值。sub 函数返回替换的数量。出现在 Repl 参数指定的字符串中的 &（和符号）由 In 参数指定的与 Ere 参数的指定的扩展正则表达式匹配的字符串替换。如果未指定 In 参数，缺省值是整个记录（$0 记录变量）。

index( String1, String2 )

在由 String1 参数指定的字符串（其中有出现 String2 指定的参数）中，返回位置，从 1 开始编号。如果 String2 参数不在 String1 参数中出现，则返回 0（零）。

length \[(String)\]

返回 String 参数指定的字符串的长度（字符形式）。如果未给出 String 参数，则返回整个记录的长度（$0 记录变量）。

blength \[(String)\]

返回 String 参数指定的字符串的长度（以字节为单位）。如果未给出 String 参数，则返回整个记录的长度（$0 记录变量）。

substr( String, M, \[ N \] )

返回具有 N 参数指定的字符数量子串。子串从 String 参数指定的字符串取得，其字符以 M 参数指定的位置开始。M 参数指定为将 String 参数中的第一个字符作为编号 1。如果未指定 N 参数，则子串的长度将是 M 参数指定的位置到 String 参数的末尾 的长度。

match( String, Ere )

在 String 参数指定的字符串（Ere 参数指定的扩展正则表达式出现在其中）中返回位置（字符形式），从 1 开始编号，或如果 Ere 参数不出现，则返回 0（零）。RSTART 特殊变量设置为返回值。RLENGTH 特殊变量设置为匹配的字符串的长度，或如果未找到任何匹配，则设置为 -1（负一）。

split( String, A, \[Ere\] )

将 String 参数指定的参数分割为数组元素 A\[1\], A\[2\], . . ., A\[n\]，并返回 n 变量的值。此分隔可以通过 Ere 参数指定的扩展正则表达式进行，或用当前字段分隔符（FS 特殊变量）来进行（如果没有给出 Ere 参数）。除非上下文指明特定的元素还应具有一个数字值，否则 A 数组中的元素用字符串值来创建。

tolower( String )

返回 String 参数指定的字符串，字符串中每个大写字符将更改为小写。大写和小写的映射由当前语言环境的 LC\_CTYPE 范畴定义。

toupper( String )

返回 String 参数指定的字符串，字符串中每个小写字符将更改为大写。大写和小写的映射由当前语言环境的 LC\_CTYPE 范畴定义。

sprintf(Format, Expr, Expr, . . . )

根据 Format 参数指定的 printf 子例程格式字符串来格式化 Expr 参数指定的表达式并返回最后生成的字符串。

注：Ere都可以是正则表达式。

**gsub,sub使用**

```
1. `awk 'BEGIN{info="this is a test2010test!";gsub(/[0-9]+/,"!",info);print info}'`
2. `this is a test!test!`
```
在 info中查找满足正则表达式，`/[0-9]+/` 用`””`替换，并且替换后的值，赋值给info 未给info值，默认是`$0`

**查找字符串（index使用）**

```
1. `awk 'BEGIN{info="this is a test2010test!";print index(info,"test")?"ok":"no found";}'`
2. `ok`
```
未找到，返回0

**正则表达式匹配查找(match使用）**

```
1. `awk 'BEGIN{info="this is a test2010test!";print match(info,/[0-9]+/)?"ok":"no found";}'`
2. `ok`
```
**截取字符串(substr使用）**

```
1. `[wangsl@centos5 ~]$ awk 'BEGIN{info="this is a test2010test!";print substr(info,4,10);}'`
2. `s is a tes`
```
从第 4个 字符开始，截取10个长度字符串

**字符串分割（split使用）**

```
1. `awk 'BEGIN{info="this is a test";split(info,tA," ");print length(tA);for(k in tA){print k,tA[k];}}'`
2. `4`
3. `4 test`
4. `1 this`
5. `2 is`
6. `3 a`
```
分割info，动态创建数组tA，这里比较有意思，`awk for …in`循环，是一个无序的循环。 并不是从数组下标1…n ，因此使用时候需要注意。

**格式化字符串输出（sprintf使用）**

格式化字符串格式：

其中格式化字符串包括两部分内容：一部分是正常字符，这些字符将按原样输出; 另一部分是格式化规定字符，以`"%"`开始，后跟一个或几个规定字符,用来确定输出内容格式。


%d:十进制有符号整数

%u:十进制无符号整数

%f:浮点数

%s:字符串

%c:单个字符

%p:指针的值

%e:指数形式的浮点数

%x:%X 无符号以十六进制表示的整数

%o:无符号以八进制表示的整数

%g:自动选择合适的表示法

```
1. `awk 'BEGIN{n1=124.113;n2=-1.224;n3=1.2345; printf("%.2f,%.2u,%.2g,%X,%on",n1,n2,n3,n1,n1);}'`
2. `124.11,18446744073709551615,1.2,7C,174`
```
#### 一般函数

close( Expression )

用同一个带字符串值的 Expression 参数来关闭由 print 或 printf 语句打开的或调用 getline 函数打开的文件或管道。如果文件或管道成功关闭，则返回 0；其它情况下返回非零值。如果打算写一个文件，并稍后在同一个程序中读取文件，则 close 语句是必需的。

system(command )

执行 Command 参数指定的命令，并返回退出状态。等同于 system 子例程。

Expression \`\\

\` getline \[ Variable \]

从来自 Expression 参数指定的命令的输出中通过管道传送的流中读取一个输入记录，并将该记录的值指定给 Variable 参数指定的变量。如果当前未打开将 Expression 参数的值作为其命令名称的流，则创建流。创建的流等同于调用 popen 子例程，此时 Command 参数取 Expression 参数的值且 Mode 参数设置为一个是 r 的值。只要流保留打开且 Expression 参数求得同一个字符串，则对 getline 函数的每次后续调用读取另一个记录。如果未指定 Variable 参数，则 $0 记录变量和 NF 特殊变量设置为从流读取的记录。

getline \[ Variable \] < Expression

从 Expression 参数指定的文件读取输入的下一个记录，并将 Variable 参数指定的变量设置为该记录的值。只要流保留打开且 Expression 参数对同一个字符串求值，则对 getline 函数的每次后续调用读取另一个记录。如果未指定 Variable 参数，则 $0 记录变量和 NF 特殊变量设置为从流读取的记录。

getline \[ Variable \]

将 Variable 参数指定的变量设置为从当前输入文件读取的下一个输入记录。如果未指定 Variable 参数，则 $0 记录变量设置为该记录的值，还将设置 NF、NR 和 FNR 特殊变量。

**打开外部文件（close用法）**

```
1. `awk 'BEGIN{while("cat /etc/passwd"|getline){print $0;};close("/etc/passwd");}'`
2. `root:x:0:0:root:/root:/bin/bash`
3. `bin:x:1:1:bin:/bin:/sbin/nologin`
4. `daemon:x:2:2:daemon:/sbin:/sbin/nologin`
```
**逐行读取外部文件(getline使用方法）**

```
1. `awk 'BEGIN{while(getline < "/etc/passwd"){print $0;};close("/etc/passwd");}'`
2. `root:x:0:0:root:/root:/bin/bash`
3. `bin:x:1:1:bin:/bin:/sbin/nologin`
4. `daemon:x:2:2:daemon:/sbin:/sbin/nologin`
```
```
1. `awk 'BEGIN{print "Enter your name:";getline name;print name;}'`
2. `Enter your name:`
3. `chengmo`
4. `chengmo`
```
**调用外部应用程序(system使用方法）**

```
1. `awk 'BEGIN{b=system("ls -al");print b;}'`
2. `total 42092`
3. `drwxr-xr-x 14 chengmo chengmo 4096 09-30 17:47 .`
4. `drwxr-xr-x 95 root   root 4096 10-08 14:01 ..`
```
b返回值，是执行结果。

#### 时间函数


mktime( YYYY MM dd HH MM ss\[ DST\])

生成时间格式

strftime(\[format \[, timestamp\]\])

格式化时间输出，将时间戳转为时间字符串具体格式，见下表。

systime()

得到时间戳，返回从1970年1月1日开始到当前时间(不计闰年)的整秒数

**建指定时间(mktime使用）**

```
1. `awk 'BEGIN{tstamp=mktime("2001 01 01 12 12 12");print strftime("%c",tstamp);}'`
2. `2001年01月01日 星期一 12时12分12秒`
```
```
1. `awk 'BEGIN{tstamp1=mktime("2001 01 01 12 12 12");tstamp2=mktime("2001 02 01 0 0 0");print tstamp2-tstamp1;}'`
2. `2634468`
```
求2个时间段中间时间差，介绍了strftime使用方法

```
1. `awk 'BEGIN{tstamp1=mktime("2001 01 01 12 12 12");tstamp2=systime();print tstamp2-tstamp1;}'` 
2. `308201392`
```
**strftime日期和时间格式说明符**
```
%a
星期几的缩写(Sun)

%A
星期几的完整写法(Sunday)

%b
月名的缩写(Oct)

%B
月名的完整写法(October)

%c
本地日期和时间

%d
十进制日期

%D
日期 08/20/99

%e
日期，如果只有一位会补上一个空格

%H
用十进制表示24小时格式的小时

%I
用十进制表示12小时格式的小时

%j
从1月1日起一年中的第几天

%m
十进制表示的月份

%M
十进制表示的分钟

%p
12小时表示法(AM/PM)

%S
十进制表示的秒

%U
十进制表示的一年中的第几个星期(星期天作为一个星期的开始)

%w
十进制表示的星期几(星期天是0)

%W
十进制表示的一年中的第几个星期(星期一作为一个星期的开始)

%x
重新设置本地日期(08/20/99)

%X
重新设置本地时间(12:00:00)

%y
两位数字表示的年(99)

%Y
当前月份

%%
百分号(%)
```


## xargs
给其他命令传递参数的一个过滤器

xargs 命令 是给其他命令传递参数的一个过滤器，也是组合多个命令的一个工具。它擅长**将标准输入数据转换成命令行参数**，xargs 能够处理管道或者 stdin 并将其转换成特定命令的命令参数。**xargs 也可以将单行或多行文本输入转换为其他格式，例如多行变单行，单行变多行**。**xargs 的默认命令是 echo，空格是默认定界符。这意味着通过管道传递给 xargs 的输入将会包含换行和空白，不过通过 xargs 的处理，换行和空白将被空格取代**。xargs 是构建单行命令的重要组件之一。

xargs 用作**替换工具，读取输入数据重新格式化后输出**。

定义一个测试文件，内有多行文本数据：
```
cat test.txt
a b c d e f g
h i j k l m n
o p q
r s t
u v w x y z
```

多行输入单行输出：

```
cat test.txt | xargs
a b c d e f g h i j k l m n o p q r s t u v w x y z`
```

-n 选项 多行输出：
```
cat test.txt | xargs -n3
a b c
d e f
g h i
j k l
m n o
p q r
s t u
v w x
y z
```

-d 选项 可以自定义一个定界符：

```
echo "nameXnameXnameXname" | xargs -dX
name name name name
```

结合 -n 选项 使用：

```
echo "nameXnameXnameXname" | xargs -dX -n2
name name
name name
```
**读取 stdin，将格式化后的参数传递给命令**

假设一个命令为 sk.sh 和一个保存参数的文件 arg.txt：

```
#!/bin/bash
#sk.sh 命令内容，打印出所有参数。
echo $*
```
arg.txt 文件内容：
```
cat arg.txt
aaa
bbb
ccc
```

xargs 的一个 **选项 -I** ，使用 -I 指定一个替换字符串{}，这个字符串在 xargs 扩展时会被替换掉，当 -I 与 xargs 结合使用，每一个参数命令都会被执行一次：

```
cat arg.txt
aaa
bbb
ccc
```
复制所有图片文件到 /data/images 目录下：

```
`ls *.jpg | xargs -n1 -I{} cp {} /data/images`
```

**xargs 结合 find 使用**

用 rm 删除太多的文件时候，可能得到一个错误信息：`/bin/rm Argument list too long`. 用 `xargs` 去避免这个问题：
```
`find . -type f -name "*.log" -print0 | xargs -0 rm -f`
```
xargs -0 将 `\0` 作为定界符。

统计一个源代码目录中所有 php 文件的行数：
```
1. `find . -type f -name "*.php" -print0 | xargs -0 wc -l`
```
查找所有的 jpg 文件，并且压缩它们：
```
1. `find . -type f -name "*.jpg" -print | xargs tar -czvf images.tar.gz`
```
#### 打印出执行的命令

结合 `-t` 选项可以打印出 `xargs` 执行的命令
```
1. `ls | xargs -t -I{} echo {}`
```
会输出当前目录下的文件列表和执行的 echo 命令

#### 使用 -p 选项确认执行的命令

`-p` 选项会在执行每一个命令时弹出确认，当你需要非常准确的确认每一次操作时可以使用 `-p` 参数，比如，查找当前目录下 `.log` 文件，每一次删除都需要确认：
```
1. `find . -maxdepth 1 -name "*.log" | xargs -p -I{} rm {}`
```
#### 执行多个命令

使用 `-I` 选项可以让 `xargs` 执行多个命令
```
1. `cat foo.txt`
2. `one`
3. `two`
4. `three`

6. `cat foo.txt | xargs -I % sh -c 'echo %; mkdir %'`
7. `one`
8. `two`
9. `three`

11. `ls`
12. `one two three`
```
#### 其他应用

**xargs 其他应用**

假如你有一个文件包含了很多你希望下载的 URL，你能够使用 xargs 下载所有链接：
```
1. `cat url-list.txt | xargs wget -c`
```
### 子 Shell（Subshells）

运行一个 shell 脚本时会启动另一个命令解释器.，就好像你的命令是在命令行提示下被解释的一样，类似于批处理文件里的一系列命令。每个 shell 脚本有效地运行在父 shell(parent shell) 的一个子进程里。这个父 shell 是指在一个控制终端或在一个 xterm 窗口中给你命令指示符的进程。
```
1. `cmd1 | ( cmd2; cmd3; cmd4 ) | cmd5`
```
如果 cmd2 是 cd /，那么就会改变子 Shell 的工作目录，这种改变只是局限于子 shell 内部，cmd5 则完全不知道工作目录发生的变化。子 shell 是嵌在圆括号 () 内部的命令序列，子 Shell 内部定义的变量为局部变量。

子 shell 可用于为一组命令设定临时的环境变量：
```
1. `COMMAND1`
2. `COMMAND2`
3. `COMMAND3`
4. `(`
5.  `IFS=:`
6.  `PATH=/bin`
7.  `unset TERMINFO`
8.  `set -C`
9.  `shift 5`
10.  `COMMAND4`
11.  `COMMAND5`
12.  `exit 3 # 只是从子 shell 退出。`
13. `)`
14. `# 父 shell 不受影响，变量值没有更改。`
15. `COMMAND6`
16. `COMMAND7`
```

## kill
发送信号到进程。

### 内建命令

#### 概要
```

1. `kill [-s sigspec | -n signum | -sigspec] pid | jobspec ...`
2. `kill -l [sigspec]`
```
#### 主要用途

- 发送信号到作业或进程（可以为多个）。
- 列出信号。

#### 选项
```

1. `-s sig 信号名称。`
2. `-n sig 信号名称对应的数字。`
3. `-l 列出信号名称。如果在该选项后提供了数字那么假设它是信号名称对应的数字。`
4. `-L 等价于-l选项。`
```
#### 参数

pid：进程ID

jobspec：作业标识符

#### 返回值

返回状态为成功除非给出了非法选项、执行出现错误。

#### 例子
```
[user2@pc] kill -l 9
KILL
# 列出所有信号名称：
[user2@pc] kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL
 5) SIGTRAP      6) SIGABRT      7) SIGBUS       8) SIGFPE
 9) SIGKILL     10) SIGUSR1     11) SIGSEGV     12) SIGUSR2
13) SIGPIPE     14) SIGALRM     15) SIGTERM     16) SIGSTKFLT
17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU
25) SIGXFSZ     26) SIGVTALRM   27) SIGPROF     28) SIGWINCH
29) SIGIO       30) SIGPWR      31) SIGSYS      34) SIGRTMIN
35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3  38) SIGRTMIN+4
39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12
47) SIGRTMIN+13 48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14
51) SIGRTMAX-13 52) SIGRTMAX-12 53) SIGRTMAX-11 54) SIGRTMAX-10
55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7  58) SIGRTMAX-6
59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
# 下面是常用的信号。
# 只有第9种信号(SIGKILL)才可以无条件终止进程，其他信号进程都有权利忽略。
HUP     1    终端挂断
INT     2    中断（同 Ctrl + C）
QUIT    3    退出（同 Ctrl + \）
KILL    9    强制终止
TERM   15    终止
CONT   18    继续（与STOP相反，fg/bg命令）
STOP   19    暂停（同 Ctrl + Z）
```
```
# 以下发送KILL信号的形式等价。当然还有更多的等价形式，在此不一一列举了。
[user2@pc] kill -s SIGKILL PID
[user2@pc] kill -s KILL PID
[user2@pc] kill -n 9 PID
[user2@pc] kill -9 PID
[user2@pc] sleep 90 &
[1] 178420
# 终止作业标识符为1的作业。
[user2@pc] kill -9 %1
[user2@pc] jobs -l
[1]+ 178420 KILLED                  ssh 192.168.1.4
[user2@pc] sleep 90 &
[1] 181357
# 发送停止信号。
[user2@pc] kill -s STOP 181357
[user2@pc] jobs -l
[1]+ 181537 Stopped (signal)        sleep 90
# 发送继续信号。
[user2@pc] kill -s CONT 181357
[user2@pc] jobs -l
[1]+ 181537 Running                 sleep 90 &
```
#### 注意

1. `bash`的作业控制命令包括`bg fg kill wait disown suspend`。
2. 该命令是bash内建命令，相关的帮助信息请查看`help`命令。

### 外部命令

#### 概要
```

1. `kill [-signal|-s signal|-p] [-q value] [-a] [--] pid|name...`
2. `kill -l [number] | -L`
```
#### 主要用途

- 发送信号到进程（可以为多个）。
    
- 列出信号。
    

#### 选项
```

1. `-s, --signal signal 要发送的信号，可能是信号名称或信号对应的数字。`
2. `-l, --list [number] 打印信号名称或转换给定数字到信号名称。信号名称可参考文件（/usr/include/linux/signal.h）。`
3. `-L, --table 和'-l'选项类似，但是输出信号名称以及信号对应的数字。`
4. `-a, --all 不要限制“命令名到pid”的转换为具有与当前进程相同的UID的进程。`
5. `-p, --pid 打印目标进程的PID而不发送信号。`
6. `--verbose 打印信号以及接收信号的PID。`
7. `-q, --queue value 使用sigqueue(3)而不是kill(2)。参数value是信号对应的数字。`
8.  `如果接收进程已为此信号安装了处理程序将SA_SIGINFO标记为sigaction(2)，则可以获取`
9.  `该数据通过siginfo_t结构的si_sigval字段。`
10. `--help 显示帮助信息并退出。`
11. `--version 显示版本信息并退出。`
```
#### 参数

接收信号的进程列表可以是PID以及name的混合组成。

PID：每一个PID可以是以下四种情况之一：
```
n
当n大于0时，PID为n的进程接收信号。

0
当前进程组中的所有进程均接收信号。

\-1
PID大于1的所有进程均接收信号。

\-n
当n大于1时，进程组n中的所有进程接收信号。当给出了一个参数的形式为“-n”，想要让它表示一个进程组，那么必须首先指定一个信号，或参数前必须有一个“—”选项，否则它将被视为发送的信号。
```
name：使用此名称调用的所有进程将接收信号。

#### 例子
```

1. `> sleep 20 &`

3. `# 列出对应的PID。`
4. `> kill -p sleep`
5. `23021`
```
#### 返回值

- 0 成功。
- 1 失败。
- 64 部分成功（当指定了多个进程时）。

#### 注意

1. 该命令是`GNU coreutils`包中的命令，相关的帮助信息请查看`man -s 1 kill`或`info coreutils 'kill invocation'`。
2. 启动或关闭内建命令请查看`enable`命令，关于同名优先级的问题请查看`builtin`命令的例子部分的相关讨论。
3. 与`kill`命令类似的有`xkill`，`pkill`,`killall`等，用于不同的目的和场景。











## scp 

加密的方式在本地主机和远程主机之间复制文件

补充说明

**scp命令** 用于在Linux下进行远程拷贝文件的命令，和它类似的命令有cp，不过cp只是在本机进行拷贝不能跨服务器，而且scp传输是加密的。可能会稍微影响一下速度。当你服务器硬盘变为只读read only system时，用scp可以帮你把文件移出来。另外，scp还非常不占资源，不会提高多少系统负荷，在这一点上，rsync就远远不及它了。虽然 rsync比scp会快一点，但当小文件众多的情况下，rsync会导致硬盘I/O非常高，而scp基本不影响系统正常使用。

### 语法

 

1. `scp(选项)(参数)`

### 选项

 

1. `-1：使用ssh协议版本1；`
2. `-2：使用ssh协议版本2；`
3. `-4：使用ipv4；`
4. `-6：使用ipv6；`
5. `-B：以批处理模式运行；`
6. `-C：使用压缩；`
7. `-F：指定ssh配置文件；`
8. `-i：identity_file 从指定文件中读取传输时使用的密钥文件（例如亚马逊云pem），此参数直接传递给ssh；`
9. `-l：指定宽带限制；`
10. `-o：指定使用的ssh选项；`
11. `-P：指定远程主机的端口号；`
12. `-p：保留文件的最后修改时间，最后访问时间和权限模式；`
13. `-q：不显示复制进度；`
14. `-r：以递归方式复制。`

### 参数

- 源文件：指定要复制的源文件。
- 目标文件：目标文件。格式为`user@host：filename`（文件名为目标文件的名称）。

### 实例

从远程复制到本地的scp命令与上面的命令雷同，只要将从本地复制到远程的命令后面2个参数互换顺序就行了。

**从远程机器复制文件到本地目录**

 

1. `scp root@10.10.10.10:/opt/soft/nginx-0.5.38.tar.gz /opt/soft/`

从10.10.10.10机器上的`/opt/soft/`的目录中下载nginx-0.5.38.tar.gz 文件到本地`/opt/soft/`目录中。

**从亚马逊云复制OpenVPN到本地目录**

 

1. `scp -i amazon.pem ubuntu@10.10.10.10:/usr/local/openvpn_as/etc/exe/openvpn-connect-2.1.3.110.dmg openvpn-connect-2.1.3.110.dmg`

从10.10.10.10机器上下载openvpn安装文件到本地当前目录来。

**从远程机器复制到本地**

 

1. `scp -r root@10.10.10.10:/opt/soft/mongodb /opt/soft/`

从10.10.10.10机器上的`/opt/soft/`中下载mongodb目录到本地的`/opt/soft/`目录来。

```
sudo scp root@110.41.4.161:/etc/nginx/conf.d/default.conf /etc/nginx/conf.d
```

**上传本地文件到远程机器指定目录**

 

1. `scp /opt/soft/nginx-0.5.38.tar.gz root@10.10.10.10:/opt/soft/scptest`
2. `# 指定端口 2222`
3. `scp -rp -P 2222 /opt/soft/nginx-0.5.38.tar.gz root@10.10.10.10:/opt/soft/scptest`

复制本地`/opt/soft/`目录下的文件nginx-0.5.38.tar.gz到远程机器10.10.10.10的`opt/soft/scptest`目录。

**上传本地目录到远程机器指定目录**

 

1. `scp -r /opt/soft/mongodb root@10.10.10.10:/opt/soft/scptest`

上传本地目录`/opt/soft/mongodb`到远程机器10.10.10.10上`/opt/soft/scptest`的目录中去。


## curl
利用URL规则在命令行下工作的文件传输工具

### 补充说明

**curl命令** 是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载，所以是综合传输工具，但按传统，习惯称curl为下载工具。作为一款强力工具，curl支持包括HTTP、HTTPS、ftp等众多协议，还支持POST、cookies、认证、从指定偏移处下载部分文件、用户代理字符串、限速、文件大小、进度条等特征。做网页处理流程和数据检索自动化，curl可以祝一臂之力。

#### 语法

 

1. `curl(选项)(参数)`

#### 选项

 

1. `-a/--append # 上传文件时，附加到目标文件` 
2. `-A/--user-agent # 设置用户代理发送给服务器` 
3. `-anyauth # 可以使用“任何”身份验证方法` 
4. `-b/--cookie # cookie字符串或文件读取位置` 
5.  `--basic # 使用HTTP基本验证` 
6. `-B/--use-ascii # 使用ASCII /文本传输` 
7. `-c/--cookie-jar # 操作结束后把cookie写入到这个文件中` 
8. `-C/--continue-at # 断点续传` 
9. `-d/--data # HTTP POST方式传送数据` 
10.  `--data-ascii # 以ascii的方式post数据` 
11.  `--data-binary # 以二进制的方式post数据` 
12.  `--negotiate # 使用HTTP身份验证` 
13.  `--digest # 使用数字身份验证` 
14.  `--disable-eprt # 禁止使用EPRT或LPRT` 
15.  `--disable-epsv # 禁止使用EPSV` 
16. `-D/--dump-header # 把header信息写入到该文件中` 
17.  `--egd-file # 为随机数据(SSL)设置EGD socket路径` 
18.  `--tcp-nodelay # 使用TCP\_NODELAY选项` 
19. `-e/--referer # 来源网址` 
20. `-E/--cert # 客户端证书文件和密码 (SSL)`
21.  `--cert-type # 证书文件类型 (DER/PEM/ENG) (SSL)`
22.  `--key # 私钥文件名 (SSL)`
23.  `--key-type # 私钥文件类型 (DER/PEM/ENG) (SSL)`
24.  `--pass # 私钥密码 (SSL)`
25.  `--engine # 加密引擎使用 (SSL). "--engine list" for list` 
26.  `--cacert # CA证书 (SSL)`
27.  `--capath # CA目录 (made using c\_rehash) to verify peer against (SSL)`
28.  `--ciphers # SSL密码` 
29.  `--compressed # 要求返回是压缩的形势 (using deflate or gzip)`
30.  `--connect-timeout # 设置最大请求时间` 
31.  `--create-dirs # 建立本地目录的目录层次结构` 
32.  `--crlf # 上传是把LF转变成CRLF` 
33. `-f/--fail # 连接失败时不显示http错误` 
34.  `--ftp-create-dirs # 如果远程目录不存在，创建远程目录` 
35.  `--ftp-method \[multicwd/nocwd/singlecwd] # 控制CWD的使用` 
36.  `--ftp-pasv # 使用 PASV/EPSV 代替端口` 
37.  `--ftp-skip-pasv-ip # 使用PASV的时候,忽略该IP地址` 
38.  `--ftp-ssl # 尝试用 SSL/TLS 来进行ftp数据传输` 
39.  `--ftp-ssl-reqd # 要求用 SSL/TLS 来进行ftp数据传输` 
40. `-F/--form # 模拟http表单提交数据` 
41.  `--form-string # 模拟http表单提交数据` 
42. `-g/--globoff # 禁用网址序列和范围使用{}和\[]` 
43. `-G/--get # 以get的方式来发送数据` 
44. `-H/--header # 自定义头信息传递给服务器` 
45.  `--ignore-content-length # 忽略的HTTP头信息的长度` 
46. `-i/--include # 输出时包括protocol头信息` 
47. `-I/--head # 只显示请求头信息` 
48. `-j/--junk-session-cookies # 读取文件进忽略session cookie` 
49.  `--interface # 使用指定网络接口/地址` 
50.  `--krb4 # 使用指定安全级别的krb4` 
51. `-k/--insecure # 允许不使用证书到SSL站点` 
52. `-K/--config # 指定的配置文件读取` 
53. `-l/--list-only # 列出ftp目录下的文件名称` 
54.  `--limit-rate # 设置传输速度` 
55.  `--local-port # 强制使用本地端口号` 
56. `-m/--max-time # 设置最大传输时间` 
57.  `--max-redirs # 设置最大读取的目录数` 
58.  `--max-filesize # 设置最大下载的文件总量` 
59. `-M/--manual # 显示全手动` 
60. `-n/--netrc # 从netrc文件中读取用户名和密码` 
61.  `--netrc-optional # 使用 .netrc 或者 URL来覆盖-n` 
62.  `--ntlm # 使用 HTTP NTLM 身份验证` 
63. `-N/--no-buffer # 禁用缓冲输出` 
64. `-o/--output # 把输出写到该文件中` 
65. `-O/--remote-name # 把输出写到该文件中，保留远程文件的文件名` 
66. `-p/--proxytunnel # 使用HTTP代理` 
67.  `--proxy-anyauth # 选择任一代理身份验证方法` 
68.  `--proxy-basic # 在代理上使用基本身份验证` 
69.  `--proxy-digest # 在代理上使用数字身份验证` 
70.  `--proxy-ntlm # 在代理上使用ntlm身份验证` 
71. `-P/--ftp-port # 使用端口地址，而不是使用PASV` 
72. `-q # 作为第一个参数，关闭 .curlrc` 
73. `-Q/--quote # 文件传输前，发送命令到服务器` 
74. `-r/--range # 检索来自HTTP/1.1或FTP服务器字节范围` 
75. `--range-file # 读取（SSL）的随机文件` 
76. `-R/--remote-time # 在本地生成文件时，保留远程文件时间` 
77.  `--retry # 传输出现问题时，重试的次数` 
78.  `--retry-delay # 传输出现问题时，设置重试间隔时间` 
79.  `--retry-max-time # 传输出现问题时，设置最大重试时间` 
80. `-s/--silent # 静默模式。不输出任何东西` 
81. `-S/--show-error # 显示错误` 
82.  `--socks4 # 用socks4代理给定主机和端口` 
83.  `--socks5 # 用socks5代理给定主机和端口` 
84.  `--stderr #` 
85. `-t/--telnet-option # Telnet选项设置` 
86.  `--trace # 对指定文件进行debug` 
87.  `--trace-ascii # Like --跟踪但没有hex输出` 
88.  `--trace-time # 跟踪/详细输出时，添加时间戳` 
89. `-T/--upload-file # 上传文件` 
90.  `--url # Spet URL to work with` 
91. `-u/--user # 设置服务器的用户和密码` 
92. `-U/--proxy-user # 设置代理用户名和密码` 
93. `-w/--write-out \[format] # 什么输出完成后` 
94. `-x/--proxy # 在给定的端口上使用HTTP代理` 
95. `-X/--request # 指定什么命令` 
96. `-y/--speed-time # 放弃限速所要的时间，默认为30` 
97. `-Y/--speed-limit # 停止传输速度的限制，速度时间`

#### 实例

**文件下载**

curl命令可以用来执行下载、发送各种HTTP请求，指定HTTP头部等操作。如果系统没有curl可以使用`yum install curl`安装，也可以下载安装。curl是将下载文件输出到stdout，将进度信息输出到stderr，不显示进度信息使用`--silent`选项。

 

1. `curl URL --silent`

这条命令是将下载文件输出到终端，所有下载的数据都被写入到stdout。

使用选项`-O`将下载的数据写入到文件，必须使用文件的绝对地址：

 

1. `curl http://example.com/text.iso --silent -O`

选项`-o`将下载数据写入到指定名称的文件中，并使用`--progress`显示进度条：

 

1. `curl http://example.com/test.iso -o filename.iso --progress`
2. `######################################### 100.0%`

**不输出错误和进度信息**

`-s` 参数将不输出错误和进度信息。

 

1. `curl -s https://www.example.com`
2. `# 上面命令一旦发生错误，不会显示错误信息。不发生错误的话，会正常显示运行结果。`

如果想让 curl 不产生任何输出，可以使用下面的命令。

 

1. `curl -s -o /dev/null https://google.com`

**断点续传**

curl能够从特定的文件偏移处继续下载，它可以通过指定一个便宜量来下载部分文件：

 

1. `curl URL/File -C 偏移量`

3. `#偏移量是以字节为单位的整数，如果让curl自动推断出正确的续传位置使用-C -：`
4. `curl -C -URL`

**使用curl设置参照页字符串**

参照页是位于HTTP头部中的一个字符串，用来表示用户是从哪个页面到达当前页面的，如果用户点击网页A中的某个连接，那么用户就会跳转到B网页，网页B头部的参照页字符串就包含网页A的URL。

使用`--referer`选项指定参照页字符串：

 

1. `curl --referer http://www.google.com http://wangchujiang.com`

**用curl设置用户代理字符串**

有些网站访问会提示只能使用IE浏览器来访问，这是因为这些网站设置了检查用户代理，可以使用curl把用户代理设置为IE，这样就可以访问了。使用`--user-agent`或者`-A`选项：

 

1. `curl URL --user-agent "Mozilla/5.0"`
2. `curl URL -A "Mozilla/5.0"`

其他HTTP头部信息也可以使用curl来发送，使用`-H`“头部信息” 传递多个头部信息，例如：

 

1. `curl -H "Host:wangchujiang.com" -H "accept-language:zh-cn" URL`

**curl的带宽控制和下载配额**

使用`--limit-rate`限制curl的下载速度：

 

1. `curl URL --limit-rate 50k`

命令中用k（千字节）和m（兆字节）指定下载速度限制。

使用`--max-filesize`指定可下载的最大文件大小：

 

1. `curl URL --max-filesize bytes`

如果文件大小超出限制，命令则返回一个非0退出码，如果命令正常则返回0。

 

1. `curl --limit-rate 200k https://example.com`
2. `# 上面命令将带宽限制在每秒 200K 字节。`

**用curl进行认证**

使用curl选项 -u 可以完成HTTP或者FTP的认证，可以指定密码，也可以不指定密码在后续操作中输入密码：

 

1. `curl -u user:pwd http://wangchujiang.com`
2. `curl -u user http://wangchujiang.com`

**只打印响应头部信息**

通过`-I`或者`-head`可以只打印出HTTP头部信息：

 

1. `[root@localhost text]# curl -I http://wangchujiang.com`
2. `HTTP/1.1 200 OK`
3. `Server: nginx/1.2.5`
4. `date: Mon, 10 Dec 2012 09:24:34 GMT`
5. `Content-Type: text/html; charset=UTF-8`
6. `Connection: keep-alive`
7. `Vary: Accept-Encoding`
8. `X-Pingback: http://wangchujiang.com/xmlrpc.php`

**get请求**

 

1. `curl "http://www.wangchujiang.com" # 如果这里的URL指向的是一个文件或者一幅图都可以直接下载到本地`
2. `curl -i "http://www.wangchujiang.com" # 显示全部信息`
3. `curl -l "http://www.wangchujiang.com" # 显示页面内容`
4. `curl -v "http://www.wangchujiang.com" # 显示get请求全过程解析`

**post请求**

 

1. `$ curl -d "param1=value1&param2=value2" "http://www.wangchujiang.com/login"`

3. `curl -d'login=emma＆password=123' -X POST https://wangchujiang.com/login`
4. `# 或者`
5. `$ curl -d 'login=emma' -d 'password=123' -X POST  https://wangchujiang.com/login`

`--data-urlencode` 参数等同于 `-d`，发送 `POST` 请求的数据体，区别在于会自动将发送的数据进行 `URL` 编码。

 

1. `curl --data-urlencode 'comment=hello world' https://wangchujiang.com/login`
2. `# 上面代码中，发送的数据hello world之间有一个空格，需要进行 URL 编码。`

**读取本地文本文件的数据，向服务器发送**

 

1. `curl -d '@data.txt' https://wangchujiang.com/upload`
2. `# 读取data.txt文件的内容，作为数据体向服务器发送。`

**json格式的post请求**

 

1. `curl -l -H "Content-type: application/json" -X POST -d '{"phone":"13521389587","password":"test"}' http://wangchujiang.com/apis/users.json`

**向服务器发送 Cookie**

使用`--cookie "COKKIES"`选项来指定cookie，多个cookie使用分号分隔：

 

1. `curl http://wangchujiang.com --cookie "user=root;pass=123456"`

将cookie另存为一个文件，使用`--cookie-jar`选项：

 

1. `curl URL --cookie-jar cookie_file`

`-b` 参数用来向服务器发送 Cookie。

 

1. `curl -b 'foo=bar' https://taobao.com`
2. `# 上面命令会生成一个标头Cookie: foo=bar，向服务器发送一个名为foo、值为bar的 Cookie。`

 

1. `curl -b 'foo1=bar' -b 'foo2=baz' https://taobao.com`
2. `# 上面命令发送两个 Cookie。`

4. ` ```shell`
5. `curl -b cookies.txt https://www.taobao.com`
6. `# 上面命令读取本地文件 cookies.txt，里面是服务器设置的 Cookie（参见-c参数），将其发送到服务器。`

**Cookie 写入一个文件**

 

1. `curl -c cookies.txt https://www.taobao.com`
2. `# 上面命令将服务器的 HTTP 回应所设置 Cookie 写入文本文件cookies.txt。`

**请求的来源**

`-e` 参数用来设置 `HTTP` 的标头 `Referer`，表示请求的来源。

 

1. `curl -e 'https://taobao.com?q=example' https://www.example.com`
2. `# 上面命令将Referer标头设为 https://taobao.com?q=example。`

`-H` 参数可以通过直接添加标头 `Referer`，达到同样效果。

 

1. `curl -H 'Referer: https://taobao.com?q=example' https://www.example.com`

**上传二进制文件**

`-F` 参数用来向服务器上传二进制文件。

 

1. `curl -F 'file=@photo.png' https://taobao.com/profile`
2. `# 上面命令会给 HTTP 请求加上标头 Content-Type: multipart/form-data ，然后将文件photo.png作为file字段上传。`

`-F` 参数可以指定 `MIME` 类型。

 

1. `curl -F 'file=@photo.png;type=image/png' https://taobao.com/profile`
2. `# 上面命令指定 MIME 类型为image/png，否则 curl 会把 MIME 类型设为 application/octet-stream。`

`-F` 参数也可以指定文件名。

 

1. `curl -F 'file=@photo.png;filename=me.png' https://taobao.com/profile`
2. `# 上面命令中，原始文件名为photo.png，但是服务器接收到的文件名为me.png。`

**设置请求头**

`-H` 参数添加 `HTTP` 请求的标头。

 

1. `curl -H 'Accept-Language: en-US' https://google.com`
2. `# 上面命令添加 HTTP 标头 Accept-Language: en-US。`

 

1. `curl -H 'Accept-Language: en-US' -H 'Secret-Message: xyzzy' https://google.com`
2. `# 上面命令添加两个 HTTP 标头。`

 

1. `curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login`
2. `# 上面命令添加 HTTP 请求的标头是 Content-Type: application/json，然后用 -d 参数发送 JSON 数据。`

**跳过 SSL 检测**

 

1. `curl -k https://www.example.com`
2. `# 上面命令不会检查服务器的 SSL 证书是否正确。`

**请求跟随服务器的重定向**

`-L` 参数会让 `HTTP` 请求跟随服务器的重定向。`curl` 默认不跟随重定向。

 

1. `curl -L -d 'tweet=hi' https://api.example.com/tweet`

**调试参数**

`-v` 参数输出通信的整个过程，用于调试。

 

1. `curl -v https://www.example.com`
2. `# --trace参数也可以用于调试，还会输出原始的二进制数据。`

 

1. `$ curl --trace - https://www.example.com`

**获取本机外网ip**

 

1. `curl ipecho.net/plain`

**使用 curl 测试网站加载速度**

命令有一个鲜为人知的选项，`-w`，该选项在请求结束之后打印本次请求的统计数据到标准输出。

首先，我们定义控制打印行为的格式化字符串。新建文本文件 `fmt.txt`，并填入下面的内容：

 

1. `\n`
2. `Response Time for: %{url_effective}\n\n`
3. `DNS Lookup Time:\t\t%{time_namelookup}s\n`
4. `Redirection Time:\t\t%{time_redirect}s\n`
5. `Connection Time:\t\t%{time_connect}s\n`
6. `App Connection Time:\t\t%{time_appconnect}s\n`
7. `Pre-transfer Time:\t\t%{time_pretransfer}s\n`
8. `Start-transfer Time:\t\t%{time_starttransfer}s\n\n`
9. `Total Time:\t\t\t%{time_total}s\n`

curl 提供了很多置换变量，可以在格式化字符串中通过 `%{var}` 的形式使用。完整的变量列表可以在 `curl` 的 `manpage` 中查看。简单介绍一下我们使用的这几个变量：

- `url_effective`: 执行完地址重定向之后的最终 URL；
- `time_namelookup`: 从请求开始至完成名称解析所花的时间，单位为秒，下同；
- `time_redirect`: 执行所有重定向所花的时间；
- `time_connect`: 从请求开始至建立 TCP 连接所花的时间；
- `time_appconnect`: 从请求开始至完成 SSL/SSH 握手所花的时间；
- `time_pretransfer`: 从请求开始至服务器准备传送文件所花的时间，包含了传送协商时间；
- `time_starttransfer`: 从请求开始至服务器准备传送第一个字节所花的时间；
- `time_total`: 完整耗时。

然后执行请求，通过 @filename 指定保存了格式化字符串的文件：

 

1. `$ curl -L -s -w @fmt.txt -o /dev/null http://www.google.com`

输出：

 

1. `Response Time for: http://www.google.co.jp/?gfe_rd=cr&dcr=0&ei=cjIaWpTkHeiQ8QfnxYzoBA`

3. `DNS Lookup Time: 0.000038s`
4. `Redirection Time: 0.207271s`
5. `Connection Time: 0.000039s`
6. `App Connection Time: 0.000039s`
7. `Pre-transfer Time: 0.000067s`
8. `Start-transfer Time: 0.260115s`

10. `Total Time: 0.467691s`

#### 要求返回是压缩的状态

 

1. `▶ curl --compressed -o- -L https://yarnpkg.com/install.sh | bash`
2.  `% Total % Received % Xferd Average Speed Time Time Time Current`
3.  `Dload Upload Total Spent Left Speed`
4. `100 54 100 54 0 0 42 0 0:00:01 0:00:01 --:--:-- 42`
5. `100 2341 100 2341 0 0 1202 0 0:00:01 0:00:01 --:--:-- 9289`
6. `Installing Yarn!`
7. `> Downloading tarball...`

9. `[1/2]: https://yarnpkg.com/latest.tar.gz --> /var/folders/j7/3xly5sk567s65ny5dnr__3b80000gn/T/yarn.tar.gz.XXXXXXXXXX.9hJsBsrA`
10.  `% Total % Received % Xferd Average Speed Time Time Time Current`
11.  `Dload Upload Total Spent Left Speed`
12. `100 57 100 57 0 0 72 0 --:--:-- --:--:-- --:--:-- 72`
13. `100 93 100 93 0 0 63 0 0:00:01 0:00:01 --:--:-- 63`
14. `100 643 100 643 0 0 248 0 0:00:02 0:00:02 --:--:-- 707`
15. `100 1215k 100 1215k 0 0 153k 0 0:00:07 0:00:07 --:--:-- 305k`

17. `[2/2]: https://yarnpkg.com/latest.tar.gz.asc --> /var/folders/j7/3xly5sk567s65ny5dnr__3b80000gn/T/yarn.tar.gz.XXXXXXXXXX.9hJsBsrA.asc`
18. `100 61 100 61 0 0 356 0 --:--:-- --:--:-- --:--:-- 356`
19. `100 97 100 97 0 0 325 0 --:--:-- --:--:-- --:--:-- 325`
20. `100 647 100 647 0 0 1283 0 --:--:-- --:--:-- --:--:-- 1283`
21. `100 832 100 832 0 0 1107 0 --:--:-- --:--:-- --:--:-- 812k`




## wget
Linux系统下载文件工具

### 补充说明

**wget命令** 用来从指定的URL下载文件。wget非常稳定，它在带宽很窄的情况下和不稳定网络中有很强的适应性，如果是由于网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载。这对从那些限定了链接时间的服务器上下载大文件非常有用。

wget支持HTTP，HTTPS和FTP协议，可以使用HTTP代理。所谓的自动下载是指，wget可以在用户退出系统的之后在后台执行。这意味这你可以登录系统，启动一个wget下载任务，然后退出系统，wget将在后台执行直到任务完成，相对于其它大部分浏览器在下载大量数据时需要用户一直的参与，这省去了极大的麻烦。

用于从网络上下载资源，没有指定目录，下载资源回默认为当前目录。wget虽然功能强大，但是使用起来还是比较简单：

1. **支持断点下传功能** 这一点，也是网络蚂蚁和FlashGet当年最大的卖点，现在，Wget也可以使用此功能，那些网络不是太好的用户可以放心了；
2. **同时支持FTP和HTTP下载方式** 尽管现在大部分软件可以使用HTTP方式下载，但是，有些时候，仍然需要使用FTP方式下载软件；
3. **支持代理服务器** 对安全强度很高的系统而言，一般不会将自己的系统直接暴露在互联网上，所以，支持代理是下载软件必须有的功能；
4. **设置方便简单** 可能，习惯图形界面的用户已经不是太习惯命令行了，但是，命令行在设置上其实有更多的优点，最少，鼠标可以少点很多次，也不要担心是否错点鼠标；
5. **程序小，完全免费** 程序小可以考虑不计，因为现在的硬盘实在太大了；完全免费就不得不考虑了，即使网络上有很多所谓的免费软件，但是，这些软件的广告却不是我们喜欢的。

#### 语法

 

1. `wget [参数] [URL地址]`

#### 选项

 

1. `启动参数：`

3. `-V, –-version 显示wget的版本后退出`
4. `-h, –-help 打印语法帮助`
5. `-b, –-background 启动后转入后台执行`
6. ``-e, –-execute=COMMAND 执行 `.wgetrc’格式的命令，wgetrc格式参见/etc/wgetrc或~/.wgetrc``

8. `记录和输入文件参数：`

10. `-o, –-output-file=FILE 把记录写到FILE文件中`
11. `-a, –-append-output=FILE 把记录追加到FILE文件中`
12. `-d, –-debug 打印调试输出`
13. `-q, –-quiet 安静模式(没有输出)`
14. `-v, –-verbose 冗长模式(这是缺省设置)`
15. `-nv, –-non-verbose 关掉冗长模式，但不是安静模式`
16. `-i, –-input-file=FILE 下载在FILE文件中出现的URLs`
17. `-F, –-force-html 把输入文件当作HTML格式文件对待`
18. `-B, –-base=URL 将URL作为在-F -i参数指定的文件中出现的相对链接的前缀`
19. `–-sslcertfile=FILE 可选客户端证书`
20. `–-sslcertkey=KEYFILE 可选客户端证书的KEYFILE`
21. `–-egd-file=FILE 指定EGD socket的文件名`

23. `下载参数：`

25. `–-bind-address=ADDRESS 指定本地使用地址(主机名或IP，当本地有多个IP或名字时使用)`
26. `-t, –-tries=NUMBER 设定最大尝试链接次数(0 表示无限制).`
27. `-O –-output-document=FILE 把文档写到FILE文件中`
28. `-nc, –-no-clobber 不要覆盖存在的文件或使用.#前缀`
29. `-c, –-continue 接着下载没下载完的文件`
30. `–progress=TYPE 设定进程条标记`
31. `-N, –-timestamping 不要重新下载文件除非比本地文件新`
32. `-S, –-server-response 打印服务器的回应`
33. `–-spider 不下载任何东西`
34. `-T, –-timeout=SECONDS 设定响应超时的秒数`
35. `-w, –-wait=SECONDS 两次尝试之间间隔SECONDS秒`
36. `–waitretry=SECONDS 在重新链接之间等待1…SECONDS秒`
37. `–random-wait 在下载之间等待0…2*WAIT秒`
38. `-Y, –-proxy=on/off 打开或关闭代理`
39. `-Q, –-quota=NUMBER 设置下载的容量限制`
40. `-–limit-rate=RATE 限定下载输率`

42. `目录参数：`

44. `-nd –-no-directories 不创建目录`
45. `-x, –-force-directories 强制创建目录`
46. `-nH, –-no-host-directories 不创建主机目录`
47. `-P, –-directory-prefix=PREFIX 将文件保存到目录 PREFIX/…`
48. `–cut-dirs=NUMBER 忽略 NUMBER层远程目录`

50. `HTTP 选项参数：`

52. `-–http-user=USER 设定HTTP用户名为 USER.`
53. `-–http-passwd=PASS 设定http密码为 PASS`
54. `-C, –-cache=on/off 允许/不允许服务器端的数据缓存 (一般情况下允许)`
55. `-E, –-html-extension 将所有text/html文档以.html扩展名保存`
56. ``-–ignore-length 忽略 `Content-Length’头域``
57. `-–header=STRING 在headers中插入字符串 STRING`
58. `-–proxy-user=USER 设定代理的用户名为 USER`
59. `-–proxy-passwd=PASS 设定代理的密码为 PASS`
60. ``-–referer=URL 在HTTP请求中包含 `Referer: URL’头``
61. `-s, –-save-headers 保存HTTP头到文件`
62. `-U, –-user-agent=AGENT 设定代理的名称为 AGENT而不是 Wget/VERSION`
63. `-–no-http-keep-alive 关闭 HTTP活动链接 (永远链接)`
64. `–-cookies=off 不使用 cookies`
65. `–-load-cookies=FILE 在开始会话前从文件 FILE中加载cookie`
66. `-–save-cookies=FILE 在会话结束后将 cookies保存到 FILE文件中`

68. `FTP 选项参数：`

70. ``-nr, -–dont-remove-listing 不移走 `.listing’文件``
71. `-g, -–glob=on/off 打开或关闭文件名的 globbing机制`
72. `-–passive-ftp 使用被动传输模式 (缺省值).`
73. `-–active-ftp 使用主动传输模式`
74. `-–retr-symlinks 在递归的时候，将链接指向文件(而不是目录)`

76. `递归下载参数：`

78. `-r, -–recursive 递归下载－－慎用!`
79. `-l, -–level=NUMBER 最大递归深度 (inf 或 0 代表无穷)`
80. `–-delete-after 在现在完毕后局部删除文件`
81. `-k, –-convert-links 转换非相对链接为相对链接`
82. `-K, –-backup-converted 在转换文件X之前，将之备份为 X.orig`
83. `-m, –-mirror 等价于 -r -N -l inf -nr`
84. `-p, –-page-requisites 下载显示HTML文件的所有图片`

86. `递归下载中的包含和不包含(accept/reject)：`

88. `-A, –-accept=LIST 分号分隔的被接受扩展名的列表`
89. `-R, –-reject=LIST 分号分隔的不被接受的扩展名的列表`
90. `-D, –-domains=LIST 分号分隔的被接受域的列表`
91. `–-exclude-domains=LIST 分号分隔的不被接受的域的列表`
92. `–-follow-ftp 跟踪HTML文档中的FTP链接`
93. `–-follow-tags=LIST 分号分隔的被跟踪的HTML标签的列表`
94. `-G, –-ignore-tags=LIST 分号分隔的被忽略的HTML标签的列表`
95. `-H, –-span-hosts 当递归时转到外部主机`
96. `-L, –-relative 仅仅跟踪相对链接`
97. `-I, –-include-directories=LIST 允许目录的列表`
98. `-X, –-exclude-directories=LIST 不被包含目录的列表`
99. `-np, –-no-parent 不要追溯到父目录`
100. `wget -S –-spider url 不下载只显示过程`

#### 参数

URL：下载指定的URL地址。

#### 实例

**使用wget下载单个文件**

 

1. `wget http://www.jsdig.com/testfile.zip`

以下的例子是从网络下载一个文件并保存在当前目录，在下载的过程中会显示进度条，包含（下载完成百分比，已经下载的字节，当前下载速度，剩余下载时间）。

**下载并以不同的文件名保存**

 

1. `wget -O wordpress.zip http://www.jsdig.com/download.aspx?id=1080`

wget默认会以最后一个符合`/`的后面的字符来命令，对于动态链接的下载通常文件名会不正确。

错误：下面的例子会下载一个文件并以名称`download.aspx?id=1080`保存:

 

1. `wget http://www.jsdig.com/download?id=1`

即使下载的文件是zip格式，它仍然以`download.php?id=1080`命名。

正确：为了解决这个问题，我们可以使用参数`-O`来指定一个文件名：

 

1. `wget -O wordpress.zip http://www.jsdig.com/download.aspx?id=1080`

**wget限速下载**

 

1. `wget --limit-rate=300k http://www.jsdig.com/testfile.zip`

当你执行wget的时候，它默认会占用全部可能的宽带下载。但是当你准备下载一个大文件，而你还需要下载其它文件时就有必要限速了。

**使用wget断点续传**

 

1. `wget -c http://www.jsdig.com/testfile.zip`

使用`wget -c`重新启动下载中断的文件，对于我们下载大文件时突然由于网络等原因中断非常有帮助，我们可以继续接着下载而不是重新下载一个文件。需要继续中断的下载时可以使用`-c`参数。

**使用wget后台下载**

 

1. `wget -b http://www.jsdig.com/testfile.zip`

3. `Continuing in background, pid 1840.`
4. ``Output will be written to `wget-log'.``

对于下载非常大的文件的时候，我们可以使用参数`-b`进行后台下载，你可以使用以下命令来察看下载进度：

 

1. `tail -f wget-log`

**伪装代理名称下载**

 

1. `wget --user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" http://www.jsdig.com/testfile.zip`

有些网站能通过根据判断代理名称不是浏览器而拒绝你的下载请求。不过你可以通过`--user-agent`参数伪装。

**测试下载链接**

当你打算进行定时下载，你应该在预定时间测试下载链接是否有效。我们可以增加`--spider`参数进行检查。

 

1. `wget --spider URL`

如果下载链接正确，将会显示:

 

1. `Spider mode enabled. Check if remote file exists.`
2. `HTTP request sent, awaiting response... 200 OK`
3. `Length: unspecified [text/html]`
4. `Remote file exists and could contain further links,`
5. `but recursion is disabled -- not retrieving.`

这保证了下载能在预定的时间进行，但当你给错了一个链接，将会显示如下错误:

 

1. `wget --spider url`
2. `Spider mode enabled. Check if remote file exists.`
3. `HTTP request sent, awaiting response... 404 Not Found`
4. `Remote file does not exist -- broken link!!!`

你可以在以下几种情况下使用`--spider`参数：

- 定时下载之前进行检查
- 间隔检测网站是否可用
- 检查网站页面的死链接

**增加重试次数**

 

1. `wget --tries=40 URL`

如果网络有问题或下载一个大文件也有可能失败。wget默认重试20次连接下载文件。如果需要，你可以使用`--tries`增加重试次数。

**下载多个文件**

 

1. `wget -i filelist.txt`

首先，保存一份下载链接文件：

 

1. `cat > filelist.txt`
2. `url1`
3. `url2`
4. `url3`
5. `url4`

接着使用这个文件和参数`-i`下载。

**镜像网站**

 

1. `wget --mirror -p --convert-links -P ./LOCAL URL`

下载整个网站到本地。

- `--miror`开户镜像下载。
- `-p`下载所有为了html页面显示正常的文件。
- `--convert-links`下载后，转换成本地的链接。
- `-P ./LOCAL`保存所有文件和目录到本地指定目录。

**过滤指定格式下载**

 

1. `wget --reject=gif ur`

下载一个网站，但你不希望下载图片，可以使用这条命令。

**把下载信息存入日志文件**

 

1. `wget -o download.log URL`

不希望下载信息直接显示在终端而是在一个日志文件，可以使用。

**限制总下载文件大小**

 

1. `wget -Q5m -i filelist.txt`

当你想要下载的文件超过5M而退出下载，你可以使用。注意：这个参数对单个文件下载不起作用，只能递归下载时才有效。

**下载指定格式文件**

 

1. `wget -r -A.pdf url`

可以在以下情况使用该功能：

- 下载一个网站的所有图片。
- 下载一个网站的所有视频。
- 下载一个网站的所有PDF文件。

**FTP下载**

 

1. `wget ftp-url`
2. `wget --ftp-user=USERNAME --ftp-password=PASSWORD url`

可以使用wget来完成ftp链接的下载。

使用wget匿名ftp下载：

 

1. `wget ftp-url`

使用wget用户名和密码认证的ftp下载：

 

1. `wget --ftp-user=USERNAME --ftp-password=PASSWORD url`


## mv 
用来对文件或目录重新命名

### 补充说明

**mv命令** 用来对文件或目录重新命名，或者将文件从一个目录移到另一个目录中。source表示源文件或目录，target表示目标文件或目录。如果将一个文件移到一个已经存在的目标文件中，则目标文件的内容将被覆盖。

mv命令可以用来将源文件移至一个目标文件中，或将一组文件移至一个目标目录中。源文件被移至目标文件有两种不同的结果：

1. 如果目标文件是到某一目录文件的路径，源文件会被移到此目录下，且文件名不变。
2. 如果目标文件不是目录文件，则源文件名（只能有一个）会变为此目标文件名，并覆盖己存在的同名文件。如果源文件和目标文件在同一个目录下，mv的作用就是改文件名。当目标文件是目录文件时，源文件或目录参数可以有多个，则所有的源文件都会被移至目标文件中。所有移到该目录下的文件都将保留以前的文件名。

注意事项：mv与cp的结果不同，mv好像文件“搬家”，文件个数并未增加。而cp对文件进行复制，文件个数增加了。

### 语法
1. `mv(选项)(参数)`

### 选项
1. `--backup=<备份模式>：若需覆盖文件，则覆盖前先行备份；`
2. `-b：当文件存在时，覆盖前，为其创建一个备份；`
3. `-f：若目标文件或目录与现有的文件或目录重复，则直接覆盖现有的文件或目录；`
4. `-i：交互式操作，覆盖前先行询问用户，如果源文件与目标文件或目标目录中的文件同名，则询问用户是否覆盖目标文件。用户输入”y”，表示将覆盖目标文件；输入”n”，表示取消对源文件的移动。这样可以避免误将文件覆盖。`
5. `--strip-trailing-slashes：删除源文件中的斜杠“/”；`
6. `-S<后缀>：为备份文件指定后缀，而不使用默认的后缀；`
7. `--target-directory=<目录>：指定源文件要移动到目标目录；`
8. `-u：当源文件比目标文件新或者目标文件不存在时，才执行移动操作。`

### 参数

- 源文件：源文件列表。
- 目标文件：如果“目标文件”是文件名则在移动文件的同时，将其改名为“目标文件”；如果“目标文件”是目录名则将源文件移动到“目标文件”下。

### 实例

将目录`/usr/men`中的所有文件移到当前目录（用`.`表示）中：
1. `mv /usr/men/* .`

移动文件
1. `mv file_1.txt /home/office/`

移动多个文件
1. `mv file_2.txt file_3.txt file_4.txt /home/office/`
2. `mv *.txt /home/office/`

移动目录
1. `mv directory_1/ /home/office/`

重命名文件或目录
1. `mv file_1.txt file_2.txt # 将文件file_1.txt改名为file_2.txt`

重命名目录
1. `mv directory_1/ directory_2/`

打印移动信息
1. `mv -v *.txt /home/office`

提示是否覆盖文件
1. `mv -i file_1.txt /home/office`

源文件比目标文件新时才执行更新
1. `mv -uv *.txt /home/office`

不要覆盖任何已存在的文件
1. `mv -vn *.txt /home/office`

复制时创建备份
1. `mv -bv *.txt /home/office`

无条件覆盖已经存在的文件
1. `mv -f *.txt /home/office`