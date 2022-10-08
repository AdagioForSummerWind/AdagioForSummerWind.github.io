# Linux_base_02


# shell
Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

Shell 脚本（shell script），是一种为 shell 编写的脚本程序。

业界所说的 shell 通常都是指 shell 脚本，但读者朋友要知道，shell 和 shell script 是两个不同的概念。

由于习惯的原因，简洁起见，本文出现的 "shell编程" 都是指 shell 脚本编程，不是指开发 shell 自身。

本教程关注的是 Bash，也就是 Bourne Again Shell，由于易用和免费，Bash 在日常工作中被广泛使用。同时，Bash 也是大多数Linux 系统默认的 Shell。

在一般情况下，人们并不区分 Bourne Shell 和 Bourne Again Shell，所以，像 #!/bin/sh，它同样也可以改为 #!/bin/bash。#! 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。

## 运行
1、作为可执行程序

将上面的代码保存为 test.sh，并 cd 到相应目录：
```
chmod +x ./test.sh  #使脚本具有执行权限
./test.sh  #执行脚本
```
**注意**，一定要写成 ./test.sh，而不是 test.sh，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

2、作为解释器参数

这种运行方式是，直接运行解释器，其参数就是 shell 脚本的文件名，如：
```
/bin/sh test.sh
/bin/php test.php
```
这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。

## shell变量
```sh
jefo="zqz"
readonly jefo
# jefo是只读变量，不可删除
unset jefo
str="Hello, I know you are \"$jefo\"! \n"
# 双引号拼接下面两种都行，但单引号拼接必须加一对单引号
str="Hello, I know you are $jefo ! \n"
str="Hello, I know you are "$jefo" ! \n"
# 获取字符串长度
string="abcd"
echo ${#string}   # 输出 4
echo ${#string[0]}   # 输出 4
# 提取字符串
string="jefofrank is a great boy"
echo ${string:1:4} # 输出 efof


```
**注意**，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样。同时，变量名的命名须遵循如下规则：

- 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 中间不能有空格，可以使用下划线 _。
- 不能使用标点符号。
- 不能使用bash里的关键字（可用help命令查看保留关键字）。

使用一个定义过的变量，只要在变量名前面加美元符号即可。变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界。

使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。

使用 unset 命令可以删除变量

运行shell时，会同时存在三种变量：

1) 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
2) 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
3) shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行


### 字符串
字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没啥其它类型好用了），字符串可以用单引号，也可以用双引号，也可以不用引号。

单引号字符串的限制：
- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。

双引号的优点：
- 双引号里可以有变量
- 双引号里可以出现转义字符

### shell数组
bash支持一维数组（不支持多维数组），并且没有限定数组的大小。类似于 C 语言，数组元素的下标由 0 开始编号。
定义:
```sh
array_name=(value0 value1 value2 value3)
array_name1=(
value0
value1
value2
value3
)
# 取值
${数组名[下标]}
# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}
# 取得数组单个元素的长度
lengthn=${#array_name[n]}
```
## shell传递参数

```sh
#!/bin/bash

echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
echo "参数个数为：$#";
echo "传递的参数作为一个字符串显示：$*";
```

```
$ chmod +x test.sh 
$ ./test.sh 1 2 3
Shell 传递参数实例！
执行的文件名：./test.sh
第一个参数为：1
第二个参数为：2
第三个参数为：3
……
```
$* 与 $@ 区别：

相同点：都是引用所有参数。
不同点：只有在双引号中体现出来。假设在脚本运行时写了三个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）。
```sh
#!/bin/bash

echo "-- \$* 演示 ---"
for i in "$*"; do
    echo $i
done

echo "-- \$@ 演示 ---"
for i in "$@"; do
    echo $i
done
```
```
$ chmod +x test.sh 
$ ./test.sh 1 2 3
-- $* 演示 ---
1 2 3
-- $@ 演示 ---
1
2
3
```

## shell 运算符
原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

expr 是一款表达式计算工具，使用它能完成表达式的求值操作。
```sh
#!/bin/bash

val=`expr 2 + 2`
echo "两数之和为 : $val"

a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a 等于 b"
fi
if [ $a != $b ]
then
   echo "a 不等于 b"
fi
```
两点**注意**：
- 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
- 完整的表达式要被 ` ` 包含

乘号(*)前边必须加反斜杠(\)才能实现乘法运算；
if...then...fi 是条件语句

在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\" 。
### 关系运算符
```sh
#!/bin/bash

a=10
b=20

if [ $a -eq $b ]
then
   echo "$a -eq $b : a 等于 b"
else
   echo "$a -eq $b: a 不等于 b"
fi
if [ $a -ne $b ]
then
   echo "$a -ne $b: a 不等于 b"
else
   echo "$a -ne $b : a 等于 b"
fi
if [ $a -gt $b ]
then
   echo "$a -gt $b: a 大于 b"#greater then
else
   echo "$a -gt $b: a 不大于 b"
fi
if [ $a -lt $b ]
then
   echo "$a -lt $b: a 小于 b"
else
   echo "$a -lt $b: a 不小于 b"
fi
if [ $a -ge $b ]
then
   echo "$a -ge $b: a 大于或等于 b"
else
   echo "$a -ge $b: a 小于 b"
fi
if [ $a -le $b ]
then
   echo "$a -le $b: a 小于或等于 b"
else
   echo "$a -le $b: a 大于 b"
fi
```
### 布尔运算符
```sh
#!/bin/bash

a=10
b=20

if [ $a != $b ]
then
   echo "$a != $b : a 不等于 b"
else
   echo "$a == $b: a 等于 b"
fi
if [ $a -lt 100 -a $b -gt 15 ]
then
   echo "$a 小于 100 且 $b 大于 15 : 返回 true"
else
   echo "$a 小于 100 且 $b 大于 15 : 返回 false"
fi
if [ $a -lt 100 -o $b -gt 100 ]
then
   echo "$a 小于 100 或 $b 大于 100 : 返回 true"
else
   echo "$a 小于 100 或 $b 大于 100 : 返回 false"
fi
if [ $a -lt 5 -o $b -gt 100 ]
then
   echo "$a 小于 5 或 $b 大于 100 : 返回 true"
else
   echo "$a 小于 5 或 $b 大于 100 : 返回 false"
fi
```

逻辑运算符就是&&和||

### 字符串运算符
```sh
#!/bin/bash

a="abc"
b="efg"

if [ $a = $b ]
then
   echo "$a = $b : a 等于 b"
else
   echo "$a = $b: a 不等于 b"
fi
if [ $a != $b ]
then
   echo "$a != $b : a 不等于 b"
else
   echo "$a != $b: a 等于 b"
fi
if [ -z $a ]
then
   echo "-z $a : 字符串长度为 0"
else
   echo "-z $a : 字符串长度不为 0"
fi
if [ -n "$a" ]
then
   echo "-n $a : 字符串长度不为 0"
else
   echo "-n $a : 字符串长度为 0"
fi
# **注意**字符串长度为零和字符串为空的区别
if [ $a ]
then
   echo "$a : 字符串不为空"
else
   echo "$a : 字符串为空"
fi
```

### 文件测试运算符
```sh
#!/bin/bash

file="/var/www/runoob/test.sh"
if [ -r $file ]
then
   echo "文件可读"
else
   echo "文件不可读"
fi
if [ -w $file ]
then
   echo "文件可写"
else
   echo "文件不可写"
fi
if [ -x $file ]
then
   echo "文件可执行"
else
   echo "文件不可执行"
fi
if [ -f $file ]
then
   echo "文件为普通文件"
else
   echo "文件为特殊文件"
fi
if [ -d $file ]
then
   echo "文件是个目录"
else
   echo "文件不是个目录"
fi
if [ -s $file ]
then
   echo "文件不为空"
else
   echo "文件为空"
fi
if [ -e $file ]
then
   echo "文件存在"
else
   echo "文件不存在"
fi
```

## echo
可以使用echo实现更复杂的输出格式控制

1. 显示普通字符串:
`echo "It is a test"`
这里的双引号完全可以省略，以下命令与上面实例效果一致：
`echo It is a test`

2. 显示转义字符
`echo "\"It is a test\""`
结果将是:
`"It is a test"`
同样，双引号也可以省略

3. 显示变量
read 命令从标准输入中读取一行,并把输入行的每个字段的值指定给 shell 变量
`#!/bin/sh`
`read name` 
`echo "$name It is a test"`
以上代码保存为 test.sh，name 接收标准输入的变量，结果将是:
`[root@www ~]# sh test.sh`
`OK                     #标准输入`
`OK It is a test        #输出`

4. 显示换行
`echo -e "OK! \n" # -e 开启转义`
`echo "It is a test"`
输出结果：
`OK!`
` `
`It is a test`
5. 显示不换行
`#!/bin/sh`
`echo -e "OK! \c" # -e 开启转义 \c 不换行`
`echo "It is a test"`
输出结果：
`OK! It is a test`

6. 显示结果定向至文件
`echo "It is a test" > myfile`

7. 原样输出字符串，不进行转义或取变量(用单引号)
`echo '$name\"'`
输出结果：
`$name\"`

8. 显示命令执行结果
```sh
echo `date`
```
**注意**： 这里使用的是反引号 `, 而不是单引号 '。

结果将显示当前日期
```
Thu Jun 24 10:08:46 CST 2022
```

## printf命令
`printf  format-string  [arguments...]`

printf 命令模仿 C 程序库（library）里的 printf() 程序。

printf 由 POSIX 标准所定义，因此使用 printf 的脚本比使用 echo 移植性好。

printf 使用引用文本或空格分隔的参数，外面可以在 printf 中使用格式化字符串，还可以制定字符串的宽度、左右对齐方式等。默认的 printf 不会像 echo 自动添加换行符，我们可以手动添加 \n。

```sh
#!/bin/bash
 
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876

# format-string为双引号
printf "%d %s\n" 1 "abc"

# 单引号与双引号效果一样
printf '%d %s\n' 1 "abc"

# 没有引号也可以输出
printf %s abcdef

# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出，format-string 被重用
printf %s abc def

printf "%s\n" abc def

printf "%s %s %s\n" a b c d e f g h i j

# 如果没有 arguments，那么 %s 用NULL代替，%d 用 0 代替
printf "%s and %d \n"
```
```
姓名     性别   体重kg
郭靖     男      66.12
杨过     男      48.65
郭芙     女      47.99
……
```
%s %c %d %f 都是格式替代符，％s 输出一个字符串，％d 整型输出，％c 输出一个字符，％f 输出实数，以小数形式输出。

%-10s 指一个宽度为 10 个字符（- 表示左对齐，没有则表示右对齐），任何字符都会被显示在 10 个字符宽的字符内，如果不足则自动以空格填充，超过也会将内容全部显示出来。

%-4.2f 指格式化为小数，其中 .2 指保留2位小数。

## test
Shell中的 test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。

数值：如前面的-eq  -ne等

字符串：=  !=  -z  -n

文件：
……见前文

## 流程控制
和 Java、PHP 等语言不一样，sh 的流程控制不可为空
### if
if 语句语法格式：
```sh
if condition
then
    command1 
    command2
    ...
    commandN 
fi
```
写成一行（适用于终端命令提示符）：
```sh
if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi
```
末尾的 fi 就是 if 倒过来拼写，后面还会遇到类似的。

```sh
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

iam项目里的是这种格式：
```sh
if [[ "$*" =~ iam::iamctl:: ]];then
  eval $*
fi
```
### for
for循环一般格式为：
```sh
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
# eg
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done

for str in This is a string
do
    echo $str
done
# This
# is
# a
# string
```
写成一行：
```sh
for var in item1 item2 ... itemN; do command1; command2… done;
```
in列表是可选的，如果不用它，for循环使用命令行的位置参数。

### while
while 循环用于不断执行一系列命令，也用于从输入文件中读取数据。其语法格式为：
```sh
while condition
do
    command
done
# eg
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done

# 读取键盘信息
echo '按下 <CTRL-D> 退出'
echo -n '输入你最喜欢的网站名: '
while read FILM
do
    echo "是的！$FILM 是一个好网站"
done
```

### until
until 循环与 while 循环在处理方式上刚好相反。

一般 while 循环优于 until 循环，但在某些时候—也只是极少数情况下，until 循环更加有用。

until 语法格式:
```sh
until condition
do
    command
done
```

### case
`case ... esac`
case ... esac 为多选择语句，与其他语言中的 switch ... case 语句类似，是一种多分支选择结构，每个 case 分支用右圆括号开始，用两个分号 ;; 表示 break，即执行结束，跳出整个 case ... esac 语句，esac（就是 case 反过来）作为结束标记。

可以用 case 语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。

case ... esac 语法格式如下：
```sh
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2)
    command1
    command2
    ...
    commandN
    ;;
esac
# eg
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```
case 工作方式如上所示，取值后面必须为单词 in，每一模式必须以右括号结束。取值可以为变量或常数，匹配发现取值符合某一模式后，其间所有命令开始执行直至 ;;。

取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。

### 跳出循环
break   continue

break 命令允许跳出所有循环（终止执行后面的所有循环）。

continue 命令与 break 命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。

## 函数
shell中函数的定义格式如下：
```sh
[ function ] funname [()]

{

    action;

    [return int;]

}
# eg
funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $? !"
```
说明：

1、可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
2、参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255)

函数返回值在调用该函数后通过 $? 来获得。

**注意**：所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。

在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数...**注意**，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

## shell输入输出重定向
command > file	将输出重定向到 file。
command < file	将输入重定向到 file。
command >> file	将输出以追加的方式重定向到 file。
n > file	将文件描述符为 n 的文件重定向到 file。
n >> file	将文件描述符为 n 的文件以追加的方式重定向到 file。
n >& m	将输出文件 m 和 n 合并。
n <& m	将输入文件 m 和 n 合并。
<< tag	将开始标记 tag 和结束标记 tag 之间的内容作为输入。

**需要**注意**的是文件描述符 0 通常是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。**

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。
默认情况下，command > file 将 stdout 重定向到 file，command < file 将stdin 重定向到 file。

如果希望 stderr 重定向到 file，可以这样写：
```
$ command 2>file
```

如果希望将 stdout 和 stderr 合并后重定向到 file，可以这样写：
```
$ command > file 2>&1
```
或者
```
$ command >> file 2>&1
```
如果希望对 stdin 和 stdout 都重定向，可以这样写：
```
$ command < file1 >file2
```
command 命令将 stdin 重定向到 file1，将 stdout 重定向到 file2。

### here document
Here Document 是 Shell 中的一种特殊的重定向方式，用来将输入重定向到一个交互式 Shell 脚本或程序。

它的基本的形式如下：
```
command << delimiter
    document
delimiter
```
它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command。

**注意**：

结尾的delimiter 一定要顶格写，前面不能有任何字符，后面也不能有任何字符，包括空格和 tab 缩进。
开始的delimiter前后的空格会被忽略掉。
```
[going@dev workspace]$ wc -l <<EOF
> qq
> qq
> ww
> EOF
3
```

### /dev/null文件
如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null：
```
$ command > /dev/null
```
/dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到"禁止输出"的效果。

如果希望屏蔽 stdout 和 stderr，可以这样写：
```
$ command > /dev/null 2>&1
```
**注意**：0 是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。

这里的 2 和 > 之间不可以有空格，2> 是一体的时候才表示错误输出。

## 管道
可以将两个或者多个命令（程序或者进程）连接到一起，把一个命令的输出作为下一个命令的输入，以这种方式连接的两个或者多个命令就形成了管道（pipe）。

内存缓冲区。

```
command1 | command2
```
这里需要**注意**，command1 必须有正确输出，而 command2 必须可以处理 command2 的输出结果；而且 command2 只能处理 command1 的正确输出结果，不能处理 command1 的错误信息。

和重定向的区别：简单地说，重定向操作符>将命令与文件连接起来，用文件来接收命令的输出；而管道符|将命令与命令连接起来，用第二个命令来接收第一个命令的输出。

## 文件包含
和其他语言一样，Shell 也可以包含外部脚本。这样可以很方便的封装一些公用的代码作为一个独立的文件。

Shell 文件包含的语法格式如下：
```
. filename   # **注意**点号(.)和文件名中间有一空格
```
或
```
source filename
```


