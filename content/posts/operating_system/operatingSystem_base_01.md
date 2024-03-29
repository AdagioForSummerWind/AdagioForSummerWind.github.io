---
title: "OperatingSystem_base_01"
date: 2021-11-10T13:14:26+08:00
lastmod: 2021-11-21
tags: [operating system]
categories: [School courses]
slug: Operating System Intro
draft: true
---
> 王道考研操作系统笔记
# 绪论
## 操作系统(Operating System, OS)的概念、特征、功能和提供的服务
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211121145727.png)

引入操作系统的目的是：
- 提供一个计算机用户与计算机硬件系统之间的接口，使计算机系统更易于使用;
- 有效地控制和管理计算机系统中的各种硬件和软件资源，使之得到更有效的利用;
- 合理地组织计算机系统的工作流程，以改善系统性能。

操作系统的概念：
- 用户角度：
    - 用户不一样，操作系统的设计目的也不一样，pc机重视用户使用方便、服务器重视资源利用率等等
- 系统角度（资源管理的角度）
    - 操作系统是计算机系统的资源管理程序
- 进程角度
    - 操作系统可以看作若干个进程以及进程调度的核心
- 虚拟机角度（机器扩充的角度）
    - 用户不再使用裸机，而是通过操作系统来控制和使用计算机，计算机被扩充为功能更强大、使用更方便的虚拟计算机

操作系统的特征：
- 并发性：
    - **注意**与并行性的区别：并发性是指两个或多个事件在同一时间间隔内发生，而并发性是指两个或多个事件在同一时刻发生
- 共享性（并发性与共享性是操作系统最基本的特征）：
    - 硬件、软件资源不再被某个程序独占。由不同资源性质得到两种资源共享方式：
        - 互斥共享
        - 同时访问（如磁盘、可重入代码）
            - 宏观上的“同时”，微观上可能是作业交替访问该资源，但访问顺序不影响访问结果。
            - 注：可重入代码是指一种允许多个进程同时访问的代码，要求必须保证资源的互不影响的使用，如io代码通常是不可重入的
- 虚拟性
    - 比如单cpu的分时使用在宏观上可虚拟为多个cpu在提供服务，此外还有虚拟存储器、虚拟设备等
- 异步性
    - 多道程序环境中，由于资源等因素的限制，程序的运行时间以及次序都是不确定的

操作系统的功能和提供的服务：   
如前所述，操作系统的职能是负责系统中软硬件资源的管理，合理地组织计算机的工作流程，并为用户提供一个良好的工作环境和友好的使用界面。下面来说明操作系统的五大基本功能:
- 处理器管理
    - 处理器管理的主要任务是对处理器的分配和运行实施有效的管理。**在多道程序环境下，处理器的分配和运行是以进程为基本单位的，因此对处理器的管理可归结为对进程的管理。**
    - 进程管理应实现下述主要功能:
        - 进程控制。
        - 负责进程的创建、撤销及状态转换。
        - 进程同步。对并发执行的进程进行协调。
        - 进程通信。负贵完成进程间的信息交换。
        - 进程调度。 按一定算法进行处理器分配。
- 存储器管理
    - 存储器管理的主要任务是对内存进行分配、保护和扩充。存储器管理应实现下述主要功能:
        - 内存分配。按一定的策略为每道程序分配内存。
        - 内存保护。保证各程序在自己的内存区域内运行而不相互干扰。
        - 内存扩充。为允许大型作业或多作业的运行，必须借助虚拟存储技术去获得增加内存的效果。
- 设备管理
    - 计算机外设的管理是操作系统中最庞杂、琐碎的部分。设备管理的主要任务是对计算机系统内的所有设备实施有效管理。设备管理应具有下述功能:
        - 设备分配。 根据一定的设备分配原则对设备进行分配。为了使设备与主机并行工作，还需采用缓冲技术和虚拟技术。
        - 设备传输控制。实现物理的输入/输出操作，即启动设备、中断处理、结束处理等。
        - 设备独立性。即用户程序中的设备与实际使用的物理设备无关。
- 文件管理
    - 操作系统中负责信息管理的部分称为文件系统，因此称为文件管理。文件管理的主要任务就是有效地支持文件的存储、检索和修改等操作，解决文件的共享、保密和保护问题。文件管理应实现下述功能:
        - 文件存储空间的管理。负责对文件存储空间进行管理，包括存储空间的分配与回收等功能。
        - 目录管理。目录是为方便文件管理而设置的数据结构，它能提供按名存取的功能。
        - 文件操作管理。实现文件的操作，负责完成数据的读写。
        - 文件保护。提供文件保护功能，防止文件遭到破坏。
- 用户接口
    - 为方便用户使用操作系统，操作系统还提供了用户接口。通常，操作系统以如下3种接口方式提供给用户使用。
        - 命令接口。提供一组命令供用户直接或间接控制自己的作业。主要有两种命令接口控制方式，即联机命令接口和脱机命令接口。
            - 联机命令接口又称交互式命令接口，适用于分时或实时操作系统，它由一组 键盘操作命令组成，用户通过控制台或终端输入操作命令，向系统提出各种服务要求，用户每输入完一条命令，控制权就转入操作系统的命令解释程序，然后由命令解释程序对输入的命令解释并执行，完成执行的功能。之后控制权又转回到控制台或终端，此时用户又可以输入下一条命令。
            - 脱机命令接口又称批处理命令接口，即适用于批处理系统，它由一组作业控制命令(或称作业控制语句)组成，脱机用户不能直接干预作业的运行，应事先用相应的作业控制命令写成一份作业操作说明书，连同作业一起提交给系统。当系统调度到该作业时，由系统中的命令解释程序对作业说明书上的命令或控制语句逐条解释执行从而间接地控制作业的运行。
        - 程序接口。 也称为系统调用，是程序级的接口，由系统提供一组系统调用命令供用户程序和其他系统程序调用。用户在程序中可以直接使用这组系统调用命令向操作系统提出各种服务要求，如使用外设、申请分配内存、磁盘文件的操作等。
        - 图形接口。近年来出现的图形接口(也称图形界面)是联机命令接口的图形化。由操作系统的功能可以知道操作系统提供哪些服务:操作系统提供了一个用以执行程序的环境，提供的服务有程序执行、I/O 操作、文件操作、资源分配与保护、错误检测与排除等。

## 操作系统的发展与分类

操作系统的形成与发展：
- 无操作系统阶段
    - 第一代计算机的主要元器件是电子管，人们采用手工操作方式操作计算机。但后来cpu速度越来越快，而手工的慢速便与其形成了“人机矛盾”。cpu与io设备的速度不匹配同样日益突出，为了缓和此矛盾，先后出现了通道技术和缓冲技术，但都未能很好地解决上述矛盾，直到后来引入脱机输入/输出技术，才获得了较为满意的效果。
    - 脱机输入/输出技术是为了解决CPU和I/O设备之间速度不匹配的矛盾而提出的，此技术减少了CPU的空闲等待时间，提高了IO速度。其输入/输出方式如图1-3 所示。
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211121155522.png)
    为解决低速输入设备与CPU速度不匹配的问题，可以将用户程序和数据在一台外围机(又称卫星机)的控制下，预先从低速输入设备(纸带机)输入到输入带上，当CPU需要这些程序和数据时，再直接从输入带高速输入到内存，从而大大加快输入速度，减少CPU等待输入的时间，这就是脱机输入技术。
    - 类似地，当程序运行完毕或告一段落，当CPU需要输出时，无须直接把计算结果送至低速输出设备(图1-3中为打印机)，而是高速地把结果送到输出带上，然后在外围机的控制下，把磁带上的计算结果由相应的输出设备输出，这就是脱机输出技术。若输入/输出操作在主机控制下进行，则称为联机输入/输出。采用脱机输入/输出技术后，低速I/O设备.上数据的输入/输出都在外围机的控制下进行，而CPU只与高速的输入带及输出带打交道，从而有效地减少了CPU等待慢速设备输入/输出的时间。
    - 详细说明本方法的目的在于使我们了解脱机输入/输出的模型，因为之后的缓冲区技术以及SPOOLing技术等，都是基于这种原理产生的。理解了这个模型，对之后学习类似技术有较大的帮助。
- 单道批处理系统
    - 单道批处理系统是最早出现的一种操作系统，严格地说，它只能算作是操作系统的前身而并非是现在人们所理解的操作系统。早期的计算机系统非常昂贵，为了能充分利用，应尽量使系统连续运行，以减少空闲时间。为此，通常是把一批作业以脱机输入方式输入到磁带上，并在系统中配置监督程序(管理作业的运行，负责装入和运行各种系统程序来完成作业的自动过渡)，在其控制下，先把磁带上的第一个作业传送到内存，并把运行的控制权交给第一个作业，当第一个作业处理完后又把控制权交还给监督程序，由监督程序再把第二个作业调入内存。计算机系统按这种方式对磁带上的作业自动地一个接一个进行处理，直至把磁带上的所有作业全部处理完毕，这样便形成了早期的批处理系统。
    - 图1-4给出了单道批处理系统工作示例。
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211121160103.png)
    - 从图1-4中可以看出，每当程序发出I/O请求时，CPU便处于等待I/O完成的状态，致使CPU空闲。
    - 单道批处理系统主要有如下特点:
        - 自动性。在顺利的情况下，在磁带上的一批作业能自动地依次运行，而无须人工干预
        - 顺序性。 磁带上的各道作业顺序地进入内存，各道作业的完成顺序与它们进入内存的顺序在正常情况下应完全相同，亦即先调入内存的作业先完成。
        - 单道性。内存中仅有一道程序运行， 即监督程序每次从磁带上只调入一道程序进入内存运行，当该程序完成或发生异常情况时，才换入其后继程序进入内存运行。
- 多道批处理系统
    - 为进一步提高CPU的利用率，引入了多道程序设计技术，由此而形成了多道批处理系统。多道程序设计技术是“将一个以上的作业存放在主存中，并且同时处于运行状态。这些作业共享处理器、外设以及其他资源”。现代计算机系统一般都基于多道程序设计技术。图1-5给出了多道程序工作示例。![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211121160413.png)
    - 在单处理器系统中，多道程序运行的特点如下:
        - 多道。计算机内存中同时存放多道相互独立的程序。
        - 宏观上并行。同时进入系统的几道程序都处于运行过程中，即它们先后开始了各自的运行，但都未运行完毕。
        - 微观上串行。实际上，内存中的多道程序轮流占用CPU,交替运行。
    - 多道程序设计技术能有效提高系统的吞吐量和改善资源利用率。但实现多道程序系统时,由于主存中总是同时存在几道作业，因此还需要妥善解决下述一系列问题。
        - 如何分配处理器，以使处理器既能满足各程序运行的需要又有较高的利用率;将处理器分配给某程序后，应何时收回等问题。
        - 如何为每道程序分配必要的内存空间，使它们各得其所又不致因相互重叠而失去信息;如何防止因某个程序出现异常情况而破坏其他程序。
        - 系统中可能有多种类型的I/O设备供多道程序共享，应如何分配这些I/O设备;如何做到既方便用户对设备的使用，又能提高设备的利用率。
        - 在计算机系统中， 通常都存放着大量的程序和数据，应如何组织它们才能便于用户使用并能保证数据的安全性和一致性。
        - 对于系统中的各种应用程序，它们有的属于计算型，有的属于IO型，有的作业既重要又紧迫，有的作业要求系统能及时响应，这时应如何组织这些作业。
- 操作系统的形成
    - 为了解决上述问题，应在多道程序系统中增设一组软件， 用这组软件对上述问题进行妥善有效的处理。此外，还应提供方便用户使用计算机的软件，这样便形成了操作系统。
    - 操作系统是一组控制和管理计算机硬件和软件资源，合理地组织计算机工作流程以及方便用户的程序的集合。

操作系统的分类：
- 操作系统有3种基本类型，即批处理操作系统、分时操作系统和实时操作系统。随着计算机体系结构的发展，许多种操作系统应运而生，比如嵌入式操作系统、个人计算机操作系统、网络操作系统和分布式操作系统。
- 批处理操作系统：
    - 单道批处理操作系统
    - 多道批处理操作系统（大大提高了资源利用率和作业吞吐量，但无交互性，提交作业便失去对作业的控制）
- 分时操作系统
    - 所谓分时技术，就是把处理器的运行时间分成很短的时间片，按时间片轮流把处理器分配给各联机作业使用。若某个作业在分配给它的时间片内不能完成其计算，则该作业暂时停止运行，把处理器让给另一个作业使用，等下一轮时再继续运行。由于计算机速度很快，作业运行轮转也很快，给每个用户的感觉都好像是自己独占一台计算机。
    - 在操作系统中采用分时技术就形成了分时操作系统。在分时操作系统中，一台计算机和许多终端设备连接，用户可以通过终端向系统发出命令，请求完成某项工作，而系统则分析从终端设备发来的命令，完成用户提出的要求，然后用户再根据系统提供的运行结果，向系统提出下一步请求，这样重复上述交互会话过程，直到用户完成预计的全部工作为止。实现分时操作系统有下述几种方法:
        - 简单分时操作系统。在简单分时操作系统中，内存只驻留一道作业，其他作业都在外存上。每当内存中的作业运行一个时间片后,便被调至外存(称为调出),再从外存上选一个作业装入内存(称为调入)并运行一个时间片，按此方法使所有作业都能在规定的时间内轮流运行一个时间片，这样，所有用户都能与自己的作业交互。
        - 具有 “前台”和“后台”的分时操作系统。为了改善系统性能，引入了“前台”和“后台”的概念。这里，把作业划分为“前台”和“后台”两类。“前台”存放按时间片调入/调出的作业流，其工作方式与简单分时操作系统相同;“后台”存放批处理作业。仅当“前台”正在调入/调出或无调入/调出作业流时，才运行“后台”的批处理作业，并给它分配更长的时间片。
        - 多道分时操作系统。在分时操作系统中引入多道程序设计技术后，内存中可以同时装入多道作业，系统把所有具备运行条件的作业排成一个队列，使它们依次轮流获得一个时间片运行。
    - 分时操作系统具有以下特征：
        - 多路性。 指一台计算机与若干台终端相连接，终端上的这些用户可以同时或基本同时使用计算机。
        - 交互性。 分时操作系统中用户的操作方式是联机方式，即用户通过终端采用人机会话的方式直接控制程序运行，同程序进行交互。
        - 独占性。由于分时操作系统采用时间片轮转的方法使一台计算机同时为许多终端用户服务(通常能在2~3s内响应用户请求)，因此客观效果是这些用户彼此之间都感觉不到别人也在使用这台计算机，好像自己独占计算机一样。
        - 及时性。 系统能够在较短时间内响应用户请求。
- 实时操作系统
    - 实时操作系统是操作系统的又一种类型。对外部输入的信息，实时操作系统能够在规定的时间内处理完毕并做出反应。“实时”的含义是指计算机对于外来信息能够以足够快的速度进行处理，并在被控制对象允许的时间范围内做出快速反应。实时操作系统对响应时间的要求比分时操作系统更高，一般要求秒级、 毫秒级甚至微秒级的响应时间。
    - 实时操作系统可以分成如下两类:
        - 实时控制系统。通常是指以计算机为中心的生产过程控制系统，又称为计算机控制系统。例如，钢铁冶炼和钢板轧制的自动控制，化工、炼油生产过程的自动控制等。在这类系统中，要求实时采集现场数据，并对它们进行及时处理，进而自动控制相应的执行机构，使某参数(如温度、压力、流量等)能按预定规律变化或保持不变，以达到保证产品质量、提高产量的目的。
        - 实时信息处理系统。在这类系统中，计算机及时接收从远程终端发来的服务请求，根据用户提出的问题对信息进行检索和处理，并在很短时间内对用户做出正确响应，如机票订购系统、情报检索系统等，都属于实时信息处理系统。实时操作系统的主要特点是提供及时响应和高可靠性。系统必须保证对实时信息的分析和处理的速度要快，而且系统本身要安全可靠，因为诸如生产过程的实时控制、航空订票等实时事务系统，信息处理的延误或丢失往往会带来不堪设想的后果。批处理操作系统、分时操作系统和实时操作系统是3种基本的操作系统。若一个操作系统兼有批处理、分时和实时系统或其中两者的功能，则称该操作系统为通用操作系统。
- 其他操作系统：
    - 嵌入式操作系统
    - 集群系统
    - 网络操作系统
    - 分布式操作系统（统一、共享、透明、自治）

## 操作系统的运行环境
### 核心态与用户态
为了避免操作系统及其关键数据(如PCB等)受到用户程序有意或无意的破坏，通常将处理器的执行状态分为两种:核心态与用户态。
- 核心态。核心态又称管态、系统态，是操作系统管理程序执行时机器所处的状态。它具有较高的特权，能执行包括特权指令的一切指令，能访问所有寄存器和存储区。
- 用户态。用户态又称目态，是用户程序执行时机器所处的状态，是具有较低特权的执行状态，它只能执行规定的指令，只能访问指定的寄存器和存储区。

划分核心态与用户态之后，这两类程序以及各自的存储空间被严格区分了，而且在CPU执行时有着完全不同的待遇。**用户态程序不能直接调用核心态程序，而是通过执行访问核心态的命令，引起中断，由中断系统转入操作系统内的相应程序**，例如，在系统调用时，将由用户态转换到核心态。

特权指令:只能由操作系统内核部分使用，不允许用户直接使用的指令，如I/O指令、设置中断屏蔽指令、清内存指令、存储保护指令和设置时钟指令。

操作系统中一些与硬件关联较紧密的模块(如时钟管理、中断处理、设备驱动等)以及运行频率较高的程序(如进程管理、存储器管理、设备管理等)构成了操作系统的内核。内核的指令操作工作在核心态，主要包括以下4个方面的内容：
- 时钟管理。时钟是计算机的各部件中最关键的设备，操作系统通过时钟管理，向用户提供标准的系统时间。另外通过时钟中断的管理，可以实现进程的切换，如时间片轮转调度。
- 中断机制。键盘或鼠标的输入、进程的管理和调度、系统功能的调用、设备驱动、文件访问等，无不依赖于中断机制。中断机制中，只有一小部分属于内核，负责保护和恢复中断现场的信息，转移控制权到相关的处理程序。这样可以减少终端的处理时间，提高系统的并行处理能力。
- 原语。原语是一些关闭中断的公用小程序，主要有以下特点。
    - 处于操作 系统最底层，是最接近硬件的部分。
    - 程序运行具有原子性，操作只能一气呵成。
    - 这些程序的运行时间较短， 调用频繁。
- 系统控制的数据结构及处理。操作系统中需要一些用 来登记状态信息的数据结构，如作业控制块、进程控制块、设备控制块、各类链表、消息队列、缓冲器、空闲登记区、内存分配表等。除此之外还应该定义对这些数据结构的一系列操作:进程管理、存储器管理、设备管理。

### 中断与异常
中断与异常是一对类似但又有区别的概念。
- 中断，也称外中断，是系统正常功能的一部分，例如，因进程调度使系统停止当前运行的进程转而执行其他进程，或者因缺少所需资源而中断当前操作等待资源到达，在系统处理完其他事情之后，会继续执行中断前的进程。
- 异常，也称内中断，是由错误引起的，如文件损坏、进程越界等。
- 通常异常会引起中断，而中断未必是由异常引起的。

### 系统调用
系统调用是操作系统提供的用户接口之一，是由操作系统实现的所有系统调用所构成的集合，即程序接口或应用编程接口(Application Programming Interface, API),是应用程序同系统之间的接口。

操作系统的主要功能是为应用程序的运行创建良好的环境。为了达到这个目的，内核提供了一系列具备预定功能的内核函数，通过一组称为系统调用(System Call) 的接口呈现给用户。系统调用把应用程序的请求传给内核，调用相应的内核函数完成所需的处理，并将处理结果返回给应用程序。如果没有系统调用和内核函数，用户将不能编写大型应用程序。操作系统提供的系统调用通常包括进程控制、文件系统控制(文件读写操作和文件系统操作)、系统控制、内存管理、网络管理、socket控制、用户管理以及进程间通信(信号、消息、管道、信号量和共享内存)。

操作系统执行系统调用的流程如图1-6 所示。用户需要执行系统调用时，首先准备并传递系统调用所需的参数，通过陷入(trap)指令进入操作系统的系统内核，此时将从用户态进入内核态;之后执行相应的系统调用函数，使用特定的系统内核功能;最后将处理结果返回给用户进程，此时将从内核态返回用户态。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211121163420.png)
## 操作系统的体系结构
操作系统的体系结构就是操作系统的组成结构。操作系统的体系结构主要包括模块组合结构、层次结构和微内核结构。
- 模块组合结构：
    - 模块组合结构是软件工程出现以前的早期操作系统以及目前一些小型操作系统的体系结构。操作系统是一个有多种功能的系统程序，可以看作一个整体模块，也可以看作若干个模块按一定的结构方式组成的。系统中的每一个模块都是根据它们要完成的功能来划分的，这些功能模块按照一定的结构方式组合起来，协同完成整个系统的功能。
    - 优点:结构紧密、接口简单直接、系统的效率相对较高。
    - 缺点:首先，这种结构的模块之间可以随意转接，各模块相互牵连，不容易把握好模块的独立性，导致系统结构不清晰。其次，这种结构的可扩展性较差。在更换一个模块或修改一个 模块时，要先弄清模块间的接口，如果要按当初设计的模块接口来设计新的模块，而当初设计的模块接口很可能是随意约定的，那么要做这项工作就存在一定难度。 最后，这种结构系统的可适应性差。随着系统规模的不断增大，采用这种结构构造的系统的复杂性会迅速增长，所以它只适用于系统小、模块少、使用环境比较稳定的系统。
- 层次结构：
    - 要弥补模块组合结构中模块间调用存在的不足之处，就必须改善模块间毫无规则的相互调用、相互依赖的关系，尤其要清除模块间的循环调用。层次结构的设计就是从这一点出发,力求使模块之间调用的无序性变为有序，减少了模块调用的无规则性。按层次结构来设计操作系统，就是将操作系统的所有功能模块按功能的调用次序排列成若干层，使得功能模块之间只存在单向调用和单向依赖。
    - 优点: 模块间的组织和依赖关系清晰明了，上层功能是建立在下层功能基础之，上的，系统的可读性、可适应性以及可靠性都得到了增强。此外，对某一层进行修改或替换时，最多只影响到邻近的两层，便于修改和扩充。
    - 缺点: 操作系统的各个功能模块应该放在哪一层， 如何有效地进行分层是必须要考虑的问题。
    - 为了增强其适应性,必须把与机器特点紧密相关的软件(如中断处理、输入/输出管理等)放在最底层;其次，要将最常用的操作方式放在最内层，而把随着这些操作方式改变的部分放在外层。另外，当前操作系统的设计都是基于进程的概念，通常要将为进程提供服务的系统调用模块放在系统的内层。
- 微内核结构：
    - 随着网络技术的普遍应用和发展，很有必要为用户提供一个符合处理分布式信息的分布式系统环境。因此，操作系统可以采用微内核结构。微内核的主要思想是:在操作系统内核中只留下一些最基本的功能，而将其他服务尽可能地从内核中分离出去，用若千个运行在用户态下的进程(即服务器进程)来实现，形成所谓的“客户/服务器”模式，即C/S模式。普通用户进程(即客户进程)可通过内核向服务器进程发送请求，以取得操作系统的服务。从微内核结构的主要思想可以看出，它非常适用于分布式系统。
    - 优点:首先，每个服务进程运行在独立的用户进程中，即便某个服务器失败或产生问题，也不会引起系统其他服务器和其他组成部分的崩溃，可靠性好;其次，系统具有很好的灵活性，只要接口规范，操作系统可以方便地增删服务功能;再次，便于维护，即修改服务器的代码不会影响系统其他部分;最后,这种结构的操作系统适合分布式处理的计算环境。
    - 缺点: 这种结构的操作系统效率不高，因为所有用户进程都要通过微内核相互通信，所以微内核本身就成了系统的"瓶颈”，尤其是通信频繁的系统。

## 习题
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214150931.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214151008.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214152157.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214152256.png)
多CPU一般用于分布式系统

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214152940.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214153019.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214153717.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214153739.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214144315.png)
## 答案
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214144515.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214144607.png)

