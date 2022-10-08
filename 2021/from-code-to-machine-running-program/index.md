# OperatingSystem_advanced_02

```
极客时间操作系统学习笔记
```
# 程序运行过程 实现一个简单内核
## 程序编译过程
使用 GCC 相关的工具链。那么使用命令：gcc HelloWorld.c -o HelloWorld 或者 gcc ./HelloWorld.c -o ./HelloWorld ，就可以编译这段代码。其实，GCC 只是完成编译工作的驱动程序，它会根据编译流程分别调用预处理程序（->HelloWorld.i）、编译程序（->HelloWorld.s）、汇编程序（->HelloWorld.o）、链接程序(将HelloWorld.o与其他的库进行链接形成可执行文件)来完成具体工作。
- gcc HelloWorld.c -E -o  HelloWorld.i 预处理：加入头文件，替换宏。
- gcc HelloWorld.c -S -c -o HelloWorld.s 编译：包含预处理，将 C 程序转换成汇编程序。
- gcc HelloWorld.c -c -o HelloWorld.o 汇编：包含预处理和编译，将汇编程序转换成可链接的二进制程序。
- gcc HelloWorld.c -o HelloWorld 链接：包含以上所有操作，将可链接的二进制程序和其它别的库链接在一起，形成可执行的程序文件。
## 程序装载执行
- 图灵机：
    - 一个抽象的模型：有一条无限长的纸带，纸带上有无限个小格子，小格子中写有相关的信息，纸带上有一个读头，读头能根据纸带小格子里的信息做相关的操作并能来回移动。
- 根据冯诺依曼体系结构构成的计算机，必须具有如下功能：
    - 把程序和数据装入到计算机中；
    - 必须具有长期记住程序、数据的中间结果及最终运算结果；
    - 完成各种算术、逻辑运算和数据传送等数据加工处理；
    - 根据需要控制程序走向，并能根据指令控制机器的各部件协调操作；
    - 能够按照要求将处理的数据结果显示给用户。

- 为了完成上述的功能，计算机必须具备五大基本组成部件：
    - 装载数据和程序的输入设备；
    - 记住程序和数据的存储器；
    - 完成数据加工处理的运算器；
    - 控制程序执行的控制器；
    - 显示处理结果的输出设备。

反汇编指令objdump细节可[参考](https://zhuanlan.zhihu.com/p/335550245)

现代电子计算机正是通过内存中的信息（指令和数据）做出相应的操作，并通过内存地址的变化，达到程序读取数据，控制程序流程（顺序、跳转对应该图灵机的读头来回移动）的功能。和图灵机的核心思想相比，没有根本性的变化。只要配合一些 I/O 设备，让用户输入并显示计算结果给用户，就是一台现代意义的电子计算机。
## HELLO OS
### PC 机的引导流程
写操作系统要用汇编和 C 语言，尽管这个 Hello OS 很小，但也要用到两种编程语言。其实，现有的商业操作系统都是用这两种语言开发出来的。
- hello os的引导流程：
    - PC机加电
    - PC机BIOS固件
    - 加载可引导设备中的**GRUB**(GrandUnified Boot Loader引导内核加载程序)
    - GRUB引导
    - 加载硬盘分区的hello os 文件
    - hello os

- **PC机BIOS固件**：
    - PC 机 BIOS 固件是固化在 PC 机主板上的 ROM 芯片中的，掉电也能保存，PC 机上电后的第一条指令就是 BIOS 固件中的，它负责**检测和初始化 CPU、内存及主板平台，然后加载引导设备（大概率是硬盘）中的第一个扇区数据（这个扇区一共512字节，前446字节内容存放grub（bootloader）的关键引导程序，接着64字节放置硬盘分区表DPT（Disk Partition Table），一共可以有四个主分区，占64个字节，这也是为什么主分区最多只有四个的原因，最后2个字节是固定的标志0x55AA。）到 0x7c00 地址开始的内存空间，再接着跳转到 0x7c00 处执行指令**，在我们这里的情况下就是 GRUB 引导程序。
    - 当BIOS把引导程序加载到内存后就把控制权交给grub，而后grub的剩余代码将完成其它代码的加载和搬移以及文件系统初始化查找等工作，最终加载内核映像文件，从而把控制权交给真正的内核运行。（[参考](https://blog.csdn.net/rosetta/article/details/8687556)）
### Hello OS 引导汇编代码
为什么不直接开始用c写操作系统？
- C 作为通用的高级语言，不能直接操作特定的硬件，而且 C 语言的函数调用、函数传参，都需要用栈。
- 栈简单来说就是一块内存空间，其中数据满足后进先出的特性，它由 CPU 特定的栈寄存器指向，所以我们要先用汇编代码处理好这些 C 语言的工作环境。

1. 用汇编定义的 GRUB 的多引导协议头，其实就是一定格式的数据，我们的 Hello OS 是用 GRUB 引导的，当然要遵循 GRUB 的多引导协议标准，让 GRUB 能识别我们的 Hello OS。之所以有两个引导头，是为了兼容 GRUB1 和 GRUB2。
2. 关掉中断，设定 CPU 的工作模式。
3. 初始化cpu的寄存器和c语言的运行环境，最后调用c语言的mian函数
4. GDT_START 开始的，是 CPU 工作模式所需要的数据。
### HELLO OS的主函数
main函数是用 C 语言写的在（main.c）中，最终它们分别由 **nasm**(NASM是一个为可移植性与模块化而设计的一个80x86的汇编器。它支持相当多的目标文件格式，包括Linux和''NetBSD/FreeBSD'',''a.out'',''ELF'',''COFF'',微软16位的''OBJ''和''Win32''。它还可以输出纯二进制文件。它的语法设计得相当的简洁易懂，和Intel语法相似但更简单。它持''Pentium'',''P6'',''MMX'',''3DNow!'', ''SSE'' and ''SSE2''指令集。[参考](https://www.cnblogs.com/wufu/articles/5077359.html)) 和 GCC 编译成可链接模块，由 **LD 链接器**链接在一起，形成可执行的程序文件。

main.c
```c
#include "vgastr.h"
void main()
{
  printf("Hello OS!");
  return;
} 
```
vgastr.h：控制计算机屏幕VGABIOS固件程序显示特定字符（自己实现）
### 控制计算机屏幕
计算机屏幕显示往往是显卡的输出，显卡有很多形式：集成在主板的叫集显，做在 CPU 芯片内的叫核显，独立存在通过 PCIE 接口（PCIe是PCI Express的简写，是新一代的总线接口，是由20多家业界主导公司共同起草并完成的新技术规范，采用点对点的串行连接，可以将数据传输率提高到一个很高的频率，以此提供更高的带宽。[参考](new.qq.com/omn/20200701/20200701A0I3C000.html)）连接的叫独显，性能依次上升，价格也是。
- 独显
    - 3D 图形显示往往要涉及顶点处理、多边形的生成和变换、纹理、着色、打光、栅格化等。而这些任务的计算量超级大，所以独显往往有自己的 RAM、多达几百个运算核心的处理器。因此独显不仅仅是可以显示图像，而且可以执行大规模并行计算，比如“挖矿”。

要在屏幕上显示字符，就要编程操作显卡。

无论PC上是什么显卡，它们都支持一种叫 VESA 的标准，这种标准下有两种工作模式：字符模式和图形模式。显卡们为了兼容这种标准，不得不自己提供一种叫 VGABIOS 的固件程序。

显卡的字符模式的工作细节:
- 它把屏幕分成 24 行，每行 80 个字符，把这（24*80）个位置映射到以 0xb8000 地址开始的内存中，每两个字节对应一个字符，其中一个字节是字符的 ASCII 码，另一个字节为字符的颜色值。

注：C 语言字符串是以 0 结尾的，其字符编码通常是 utf8，而 utf8 编码对 ASCII 字符是兼容的，即英文字符的 ASCII 编码和 utf8 编码是相等的

在vgastr.h里实现：

```c
void _strwrite(char* string)
{
  char* p_strdst = (char*)(0xb8000);//指向显存的开始地址
  while (*string)                   //将字符串里每个字符依次定入到 0xb8000 地址开始的显存中。
  {
    *p_strdst = *string++;
    p_strdst += 2;                  //为了跳过字符的颜色信息的空间。
  }
  return;
}

void printf(char* fmt, ...)
{
  _strwrite(fmt);
  return;
}
```
### 编译和安装 Hello OS
不像HELLO OS,一个成熟的商业操作系统多达几万个代码模块文件，几千万行的代码量。需要一个工具来控制这个巨大的编译过程----make
#### make工具
在软件开发中，make 是一个工具程序，它读取一个叫“makefile”(makefile是一种用于编译的脚本语言)的文件，也是一种文本文件，这个文件中写好了构建软件的规则，它能根据这些规则自动化构建软件。

makefile 文件中规则是这样的：首先有一个或者多个构建目标称为“target”；目标后面紧跟着用于构建该目标所需要的文件，目标下面是构建该目标所需要的命令及参数。与此同时，它也检查文件的依赖关系，如果需要的话，它会调用一些外部软件来完成任务。

第一次构建目标后，下一次执行 make 时，它会根据该目标所依赖的文件是否更新决定是否编译该目标，如果所依赖的文件没有更新且该目标又存在，那么它便不会构建该目标。这种特性非常有利于编译程序源代码。

任何一个 Linux 发行版中都默认自带这个 make 程序，所以不需要额外的安装工作，我们直接使用即可。

一个有关makefile的例子：
```makefile
CC = gcc #定义一个宏CC 等于gcc
CFLAGS = -c #定义一个宏 CFLAGS 等于-c
OBJS_FILE = file.o file1.o file2.o file3.o file4.o #定义一个宏
.PHONY : all everything #定义两个伪目标all、everything
all:everything #伪目标all依赖于伪目标everything
everything :$(OBJS_FILE) #伪目标everything依赖于OBJS_FILE，而OBJS_FILE是宏会被
#替换成file.o file1.o file2.o file3.o file4.o
%.o : %.c
   $(CC) $(CFLAGS) -o $@ $<
```
- makefile 中可以定义宏，方法是在一个字符串后跟一个“=”或者“:=”符号，引用宏时要用“$(宏名)”，宏最终会在宏出现的地方替换成相应的字符串，例如：$(CC) 会被替换成 gcc，$( OBJS_FILE) 会被替换成 file.o file1.o file2.o file3.o file4.o。
- .PHONY 在 makefile 中表示定义伪目标。所谓伪目标，就是它不代表一个真正的文件名，在执行 make 时可以指定这个目标来执行其所在规则定义的命令。但是伪目标可以依赖于另一个伪目标或者文件，例如：all 依赖于 everything，everything 最终依赖于 file.c file1.c file2.c file3.c file4.c。
- 通用规则：“%.o : %.c”。其中的“%”表示通配符，表示所有以“.o”结尾的文件依赖于所有以“.c”结尾的文件。
- 针对这些依赖关系，分别会执行：$(CC) $(CFLAGS) -o $@ $< 命令，当然最终会转换为：gcc –c –o xxxx.o xxxx.c，这里的“xxxx”表示一个具体的文件名。
### 编译
1. makefile进行make后产生三个文件：entry.asm(汇编程序文件) main.c vgastr.c
2. entry.asm经nasm汇编器汇编为entry.o(二进制文件)
3. main.c和vgastr.c经gcc编译为main.o和vgastr.o
4. 三个二进制文件经LD链接为HELLO OS.elf(Executable Linkable Format，二进制可执行文件的一种形式) 
5. HELLO OS.elf经**objcopy**命令格式转化为 HELLO OS.bin(objcopy 把一种目标文件中的内容复制到另一种类型的目标文件中)
### 安装HELLO OS
前面得到了 Hello OS.bin 文件，但是我们还要让 GRUB 能够找到它，才能在计算机启动时加载它，即---安装。

GRUB 在启动时会加载一个 **grub.cfg** 的文本文件，根据其中的内容执行相应的操作，其中一部分内容就是启动项。GRUB 首先会显示启动项到屏幕，然后让我们选择启动项，最后 GRUB 根据启动项对应的信息，加载 OS 文件到内存。

HELLO OS启动项：
```conf
menuentry 'HelloOS' {
     insmod part_msdos #GRUB加载分区模块识别分区
     insmod ext2 #GRUB加载ext文件系统模块识别ext文件系统
     set root='hd0,msdos4' #注意boot目录挂载的分区，这是我机器上的情况，命令df /boot/可查看
     multiboot2 /boot/HelloOS.bin #GRUB以multiboot2协议加载HelloOS.bin
     boot #GRUB启动HelloOS.bin
}
```
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211202195453.png)
df /boot/后也可能是：
```
文件系统          1K-块    已用     可用      已用% 挂载点
/dev/sda4      48752308 8087584 38158536   18%    /
```
其中的“**sda4**”就是硬盘的第四个分区（硬件分区选择 MBR），但是 GRUB 的 **menuentry** 中不能写 sda4，而是要写“**hd0,msdos4**”，这是 GRUB 的命名方式，hd0 表示第一块硬盘，结合起来就是第一块硬盘的第四个分区。

**MBR**与**GPT**（[参考](https://baijiahao.baidu.com/s?id=1660593088582666423&wfr=spider&for=pc)）:
- **MBR**，它的全称是主引导记录(Master Boot Record)，它最早是在1983年的IBM PC DOS 2.0中提出的，这种模式下会在系统驱动器的一个特殊的位置建立一个用于保存操作系统BootLoader和驱动器逻辑分区的扇区。它只能最大支持2TB的磁盘，超过之后就无法识别，而在Windows下也只能创建最多4个分区。
- GPT，他是指的GUID分区表，也就是GUID意为全局唯一标识符，会生成一个唯一的识别码来进行引导创建，在功能上则没有MBR那么多的限制，支持2TB以上的磁盘，并且在Windows下支持最高128个分区的建立。
- 如果你的电脑硬件比较老旧的话，或者安装的系统版本也比较老旧的话，那就为了兼容性更好，选择MBR，而反之的话，就选择标准更新更加没有限制的GPT。

把上面启动项的代码插入到你的 Linux 机器上的 /boot/grub/grub.cfg 文件末尾，然后把 Hello OS.bin 文件复制到 /boot/ 目录下，一定注意这里是追加不是覆盖。最后重启计算机，你就可以看到 Hello OS 的启动选项了。选择 Hello OS，按下 Enter 键（或者重启按 ESC 键），这样就可以成功启动我们自己的 Hello OS 了。
### HELLO OS回顾
- 首先，我们了解了从按下 PC 机电源开关开始，PC 机的引导过程。它从 CPU 上电，到加载 BIOS 固件，再由 BIOS 固件对计算机进行自检和默认的初始化，并加载 GRUB 引导程序，最后由 GRUB 加载具体的操作系统。
- 其次，用汇编语言和 C 语言实现我们的 Hello OS。
    - 第一步，用汇编程序初始化 CPU 的寄存器、设置 CPU 的工作模式和栈，最重要的是加入了 GRUB 引导协议头；
    - 第二步，切换到 C 语言，用 C 语言写好了主函数和控制显卡输出的函数，其间还了解了显卡的一些工作细节。
- 最后，就是编译和安装 Hello OS 了。我们用了 make 工具编译整个代码，其实 make 会根据一些规则调用具体的 nasm、gcc、ld 等编译器，然后形成 Hello OS.bin 文件，你把这个文件写复制到 boot 分区，写好 GRUB 启动项，这样就好了。
