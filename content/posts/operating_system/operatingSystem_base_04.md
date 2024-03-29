---
title: "OperatingSystem_base_04"
date: 2021-11-30T18:26:50+08:00
lastmod: 2021-11-30
tags: [operating system]
categories: [School courses]
slug: file management
draft: true
---
> 王道考研操作系统笔记

# 文件管理
## 文件系统基础
### 文件的概念
#### 文件的概念
数据处理是计算机的主要功能之一， 与数据处理相关的数据管理和数据保存是必不可少的，甚至是较为重要的环节。在计算机中，大量的数据和信息是通过文件存储和管理的。在用户进行的输入、输出中，以文件为基本单位。文件系统负责管理文件，并为用户提供对文件进行存取、共享及保护的方法。文件是具有文件名的一组相关元素的集合，在文件系统中是一个最大的数据单位，它描述了一个对象集，每个文件都有一个文件名，用户通过文件名来访问文件。在此对文件的组成结构进行自底向上的介绍。
- 数据项。数据项是文件系统中最低级的数据组织形式，可分为以下两种类型。基本数据项:用于描述一个对象的某种属性的一个值，如姓名、日期或证件号等，是数据中可命名的最小逻辑数据单位，即原子数据。组合数据项:由多个基本数据项组成。
- 记录。 记录是一组相关的数据项的集合，用于描述一个对象在某方面的属性，如一个考生报名记录包括考生姓名、出生日期、报考学校代号、身份证号等-系列域。
- 文件。 文件是指由创建者所定义的一组相关信息的集合，逻辑上可分为有结构文件和无结构文件两种。在有结构文件中，文件由一组相似记录组成，如报考某学校的所有考生的报考信息记录，又称记录式文件;而无结构文件则被看成是一个字符流，如一个二进制文件或字符文件，又称流式文件。文件表示的范围很广，系统或用户可以将具有一定功能的程序或数据集合命名为-个文
件。例如，一个命名的源程序、目标程序、一批数据以及系统程序都可以看作文件。在有的操作系统中，设备也被看作一种特殊的文件。这样，系统可以对设备和文件实施统一管理，既简化了系统设计，又方便了用户。
#### 文件的属性
文件有一定的属性，根据系统的不同而有所不同，但通常都包括如下属性。
- 名称。 文件名唯一，以容易读取的形式保存。
- 标识符。 系统内文件的唯一标签， 通常为数字，对用户来说是透明的。
- 文件类型。 被支持不同类型的文件系统所使用。
- 文件位置。指向文件的指针。
- 文件的大小、 建立时间、用户标识等。
#### 文件的分类
为了便于管理和控制文件，通常将文件分为若干类型。文件分类方法有很多，这里介绍常见的几种。
- (1)按照用途分类可以将文件分为如下3类。
    - 系统文件。 由系统软件构成的文件。大多数系统文件只允许用户调用执行，而不允许用户去读或修改。
    - 库文件。 由系统提供给用户使用的各种标准过程、函数和应用程序文件。这类文件允许用户调用执行，但同样不允许用户修改。
    - 用户文件。 用户委托文件系统保存的文件，如源程序、目标程序、原始数据等。这类文件只能由文件所有者或所有者授权用户使用(就算是用户文件，也不可任意访问，这点切记，只有授权用户或文件所有者才可以访问)。
- (2)按保护级别分类可以将文件分为以下4类。
    - 只读文件。 只读文件允许所有者或授权用户对文件进行读操作，但不允许写(**注意**:仍然是授权用户或所有者才可以读，不是任意用户都可以读，下 面的情况相同)。
    - 读写文件。该文件允许所有者或授权用户对文件进行读写，但禁止未核准用户读写。
    - 执行文件。 该文件允许核准用户调用执行，但不允许对文件进行读写(要明确读、写、执行是不同的操作，不要误认为读和执行相同)。不保护文件。不保护文件是指不加任何访问限制的文件。
- (3)按信息流向分类可以将文件分为以下3类。
    - 输入文件。如对于读卡机或键盘上的文件，只能进行读入，所以这类文件为输入文件。
    - 输出文件。如对于打印机上的文件，只能进行写出，因此这类文件为输出文件。
    - 输入/输出文件。如对于磁盘、磁带上的文件，既可以读又可以写，所以这类文件是输入/输出文件。
- (4)按数据形式分类
    - 源文件。 由源程序和数据构成的文件。通常，由终端或输入设备输入的源程序和数据所形成的文件都属于源文件。源文件一般由ASCII码或者汉字组成。
    - 目标文件。 源文件经过编译以后，但尚未链接的目标代码形成的文件。目标文件属于二进制文件。
    - 可执行文件。编译后的目标代码经链接程序链接后形成的可以运行的文件。
#### 文件的操作
- (1)基本的文件操作
    - 创建文件。创建新文件时，系统先要为其分配必要的外存空间，并在目录中建立一个目录项。
    - 删除文件。删除文件时应先删除文件的目录项，使之成为空项，然后回收文件所占的存储空间。
    - 读文件。系统将文件名和文件内存目标地址给文件调用程序，同时查找目录，根据文件的外存地址设置一个读指针，当进行读操作时更新读指针。
    - 写文件。 系统将文件名和文件内存地址传递给文件调用程序，同时查找目录，根据外存地址设置写指针，当进行写操作时更新写指针。
    - 截断文件。当文件内容不再需要或者需要全部更新时，可以将文件删除重新创建;或者保持文件所有属性不变，删除文件内容，即将其长度设为0并释放其空间。
    - 设置文件的读/写位置。通过设置文件的读/写位置，可以使每次对文件操作时不必从文件始端开始，而可以从某个特定位置开始。
- (2)文件的打开和关闭操作
    - 打开文件。 系统将文件的属性从外存复制到内存，并设定一个编号(或索引)返回给用户。以后当用户要对该文件进行操作时，只需利用编号(或索引号)向系统提出请求即可。这样避免了系统对文件的再次检索，既节约了检索开销，又提高了对文件的操作速度。每个打开文件都有如下关联信息:
        - ①文件指针。系统跟踪上次读写位置作为当前文件位置指针,这种指针对打开文件的某个进程来说是唯一的， 因此，必须与磁盘文件属性分开保存。
        - ②文件打开计数。文件关闭时，操作系统必须重用其打开文件表(包含所有打开文件信息的表)条目，否则表内空间会不够用。因为多个进程可能打开同一个文件，所以系统在删除打开文件条日之前，必须等待最后一个进程关闭文件。该计数器跟踪打开和关闭的数量，当该计数为0时，系统关闭文件，删除该条目。
        - ③文件磁盘位置。绝大多数文件操作都要求系统修改文件数据。该信息保存在内存中,以免为每个操作都从磁盘中读取。
        - ④访问权限。每个进程打开文件都需要有一个访问模式(创建、只读、读写、添加等)。该信息保存在进程的打开文件表中，以便操作系统能允许或拒绝之后的IO请求。
    - 关闭文件。 系统将打开的文件的编号(或索引号)删除，并销毁其文件控制块。若文件被修改，则需要将修改保存到外存。
### 文件的逻辑结构：顺序文件、索引文件、索引顺序文件。
- 文件的逻辑结构和物理结构
    - 文件的逻辑结构是从用户观点来看所观察到的文件的组织形式，是用户可以直接处理的数据及其结构，因其独立于文件的物理特性，故又称为文件组织;而从计算机的角度出发，文件在外存上的存放组织形式称为文件的物理结构。
    - 文件的逻辑结构与存储设备的特性无关，而物理结构与存储设备特性的关系很大。
    - 从逻辑结构上看，文件可以分为两种形式: 一种是有结构的记录式文件;另一种是无结构的流式文件。而记录式文件的逻辑结构通常有顺序、索引和索引顺序。
    - 从物理结构，上看，文件的组织形式有连续分配、链接分配和索引分配。

通常，有结构的文件由若千个记录组成，因此称为记录式文件。记录是一些相关数据项的集合，而数据项是数据组织中可以命名的最小逻辑单位，例如，每个职工情况记录由姓名、性别、出生年月、工资等数据项组成。一个单位的职工情况记录就组成了一个文件。总之, 数据项组成记录，记录组成文件。记录式文件又可以分为等长记录文件和变长记录文件。等长记录文件中的所有记录的长度相等，变长记录文件中的各记录长度可以不相等。无结构文件是由若干个字符组成，可以看作一个字符流，称为流式文件。可以将流式文件看作记录式文件的特例。在UNIX系统中，所有文件都被视为流式文件，系统不对文件进行格式处理。

文件存储设备通常划分为大小相等的物理块，物理块是分配及传输信息的基本单位。物理块的大小与设备有关，但与逻辑记录的大小无关，因此一个物理块中可以存放若千个逻辑记录，一个逻辑记录也可以存放在若干个物理块中。为了有效地利用外存设备和便于系统管理，一般也把文件信息划分为与物理存储块大小相等的逻辑块。记录式文件(即有结构文件)的逻辑结构通常分为顺序、索引和索引顺序结构。
- 顺序文件
    - 顺序结构又称为连续结构，是一种最简单的文件结构，其将一个逻辑文件的信息连续存放。以顺序结构存放的文件称为顺序文件或连续文件。
    - 按照记录是否定长，顺序文件分为定长记录顺序文件和变长记录顺序文件。按照文件中记录是否按照关键字排序，顺序文件又分为串结构和顺序结构:串结构中各记录之间的顺序与关键字无关，而顺序结构中所有记录按照关键字顺序排序。
    - 顺序文件的主要优点是顺序存取时速度较快;若文件为定长记录文件，还可以根据文件起始地址及记录长度进行随机访问。但因为文件存储要求连续的存储空间，所以会产生碎片,同时也不利于文件的动态扩充。
- 索引文件
    - 索引结构为一个逻辑文件的信息建立一个索引表。 索引表中的表目存放文件记录的长度和所在逻辑文件的起始位置，因此逻辑文件中不再保存记录的长度信息。索引表本身是一个定长文件，每个逻辑块可以是变长的，索引表和逻辑文件两者构成了索引文件。
    - 索引文件的优点是可以进行随机访问，也易于进行文件的增删。但索引表的使用增加了存储空间的开销，另外，索引表的查找策略对文件系统的效率影响很大。
- 索引顺序文件
    - 索引顺序文件是顺序文件和索引文件两种形式的结合。索引顺序文件将顺序文件中的所有记录分为若千个组，为顺序文件建立一张索 引表，并为每组中的第一个记录在索引表建立一个索引项，其中含有该记录的关键字和指向该记录的指针。索引表中包含关键字和指针两个数据项，索引表中索引项按照关键字顺序排列。索引顺序文件的逻辑文件(主文件)是一个顺序文件，每个分组内部的关键字不必有序排列，但是组与组之间的关键字是有序排列的。
    - 索引顺序文件大大提高了顺序存取的速度，但是，仍然需要配置一个索引表， 增加了存储开销。
- 直接文件和散列(Hash)文件
    - 建立关键字和相应记录物理地址之间的对应关系，这样就可以直接通过关键字的值找到记录的物理地址，也就是说，关键字的值决定了记录的物理地址，这种结构的文件称为直接文件。这种映射结构不同于顺序文件或索引文件，没有顺序的特性。
    - 散列文件是一种典型的直接文件。通过散列函数对关键字进行转换，转换结果直接决定记录的物理地址。散列文件有很高的存取速度，但是会因不同关键字的散列函数值相同而引起冲突。
### 目录结构：文件控制块和索引结点、单级目录结构和两级目录结构、树形目录结构、图形目录结构。
- 文件目录
    - 计算机系统中的文件种类繁多、数量庞大，为了有效管理这些文件，以方便用户查找所需文件，应对它们进行适当的组织。
    - 文件的组织可以通过目录来实现。文件说明的集合称为文件目录。目录最基本的功能就是通过文件名存取文件。一般来说，目录应具有如下几个功能。
        - 实现 "按名存取”。用户只需提供文件名，就可以对文件进行操作。这既是目录管理最基本的功能，也是文件系统向用户提供的最基本的服务。提高检索速度。这就需要在设计文件系统时合理地设计目录结构。对于大型文件系统来说，这是一个很重要的设计目标。
        - 允许文件同名。 为了便于用户按照自己的习惯来命名和使用文件，文件系统应该允许对不同的文件使用相同的名称。这时，文件系统可以通过不同工作目录来对此加以区别。
        - 允许文件共享。在多用户系统中，应该允许多个用户共享一个文件，这样就可以节省文件的存储空间，也可以方便用户共享文件资源。当然，还需要采用相应的安全措施，以保证不同权限的用户只能取得相应的文件操作权限，防止越权行为。
    - 通常，文件目录也作为一个文件来处理，称为目录文件。由于文件系统中一般有很多文件，文件目录也很大，因此文件目录并不放在主存中，而是放在外存中。
- 文件控制块和索引结点
    - (1)文件控制块
        - 从文件管理的角度看，文件由文件控制块(File Control Block, FCB) 和文件体两部分组成。文件体即文件本身，而文件控制块(又称为文件说明)则是保存文件属性信息的数据结构，它包含的具体内容因操作系统而异，但至少应包含以下信息。
            - 文件名。 该信息用于标识一个文件的符号名。每个文件必须具有唯一的名字，这样用户可以按照文件名进行文件操作。
            - 文件的结构。 该信息用于说明文件的逻辑结构是记录式文件还是流式文件，若为记录式文件还需进一步说明记录是否定长、记录长度及个数;说明文件的物理结构是顺序文件、索引顺序文件还是索引文件。
            - 文件的物理位置。 该信息用于指示文件在外存上的存储位置，包括存放文件的设备名，文件在外存的存储地址以及文件长度等。文件物理地址的形式取决于物理结构，如对于连续文件应给出文件第一块的物理地址及所占块数，对于索引顺序文件只需给出第一块的物理地址，而索引文件则应给出索引表地址。
            - 存取控制信息。 该信息用于指示文件的存取权限，包括文件拥有者(也称为文件主)的存储权限以及文件主同组用户的权限和其他一般用户的权限。
            - 管理信息。 该信息包括文件建立的日期及时间、上次存取文件的日期和时间以及当前文件使用状态的信息。
    - (2)索引结点
        - 在检索目录文件的过程中，只用到了文件名，仅当找到匹配目录项时，才需要从该目录项中读出该文件的物理地址。也就是说，在检索目录时，文件的其他描述信息是不会被用到的，因而也不需要调入内存。因此，有些系统采用了文件名与文件描述信息分开的方法，将文件描述信息单独形成一个索引结点，简称i结点。文件目录中的每个目录项仅由文件名和指向该文件i结点的指针构成。
        - 一个文件控制块的大小为64B,盘块大小是1KB，则在每个盘块中可以存放16个文件控制块。而在采用索引结点的系统中一个目录项仅占16B,其中14B是文件名，2B是i结点指针。这样，在1KB的盘块中就可以存放64个目录项，可以使文件查找时平均启动磁盘的次数减少到原来的1/4，大大节省了系统开销。存放在磁盘上的索引结点称为磁盘索引结点。每个文件都有唯一的磁盘索引结点， 其主要包括以下内容。
            - 文件主标识符。 拥有该文件的个人或小组的标识符。
            - 文件类型。 包括普通文件、目录文件或特别文件。
            - 文件存取权限。 各类用户对该文件的存取权限。
            - 文件物理地址。每个索引结点以直接或间接方式给出数据文件所在盘块的编号。
            - 文件长度。 以字节为单位的文件长度。
            - 文件链接计数。 表明在本文件系统中所有指向该文件的文件名的指针数。
            - 文件存取时间。本文件最近被存取的时间、最近被修改的时间以及索引结点最近被修改的时间。
        - 当文件被打开时，磁盘索引结点被复制到内存的索引结点中，以便使用。存放在内存中的索引结点称为内存索引结点，其增加了以下内容。
            - 索引结点编号。用于标识内存索引结点。
            - 状态。指示i结点是否上锁或被修改。
            - 访问计数。正在访问该文件的进程数。
            - 逻辑设备号。文件所属文件系统的逻辑设备号。
            - 链接指针。 设置分别指向空闲链表和散列队列的指针。
- 单级目录结构
    - 单级目录结构(或称为一级目录结构)是最简单的目录结构。在整个文件系统中，单级目录结构只建立一张目录表，每个文件占据其中的一个表目，如图4-1所示。
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130185824.png)
    - 当建立一个新文件时，首先应确定该文件名在目录中是否唯一，若与已有文件名没有冲突，则从目录表中找出一个空表目，将新文件的相关信息填入其中。在删除文件时，系统先从目录表中找到该文件的目录项，从中找到该文件的物理地址，对文件占用的存储空间进行回收，然后再清除它所占用的目录项。当对文件进行访问时，系统先根据文件名去查找目录表以确定该文件是否存在，若文件存在，则找出文件的物理地址，进而完成对文件的操作。单级目录结构的优点是易于实现，管理简单，但是存在以下缺点。
        - 不允许文件重名 (这个很显然)。单级目录下的文件，不允许和另一个文件有相同的名字。但对于多用户系统来说，这又是很难避免的。即使是单用户环境，当文件数量很大时,也很难弄清到底有哪些文件，这就导致文件系统极难管理。
        - 文件查找速度慢。 对稍具规模的文件系统来说，由于其拥有大量的目录项，因此查找一个指定的目录项可能花费较长的时间。
- 二级目录结构
    - 二级目录结构将文件目录分成主文件目录和用户文件目录。系统为每个用户建立一个单独的用户文件目录(User File Directory, UFD)， 其中的表项登记了该用户建立的所有文件及其说明信息。主文件目录(Master File Directory, MFD)则记录系统中各个用户文件目录的情况，每个用户占一个表目，表目中包括用户名及相应用户目录所在的存储位置等。这样就形成了二级目录结构。当用户要访问一个文件时，系统先根据用户名在主文件目录中查找该用户的文件目录，然后根据文件名在其用户文件目录中找出相应的目录项，从中得到该文件的物理地址，进而完成对文件的访问。
    - 当用户要建立一个文件时，若为新用户，即主文件目录表中无此用户的相应登记项，则系统为其在主目录中分配一个表目，并为其分配存放用户文件目录的存储空间，同时在用户文件目录中为新文件分配一个表目，然后在表目中填入有关信息。文件删除时，只需在用户文件目录中删除该文件的目录项。若删除后该用户目录表为空，则表明该用户已脱离了系统，从而可以将主文件目录表中该用户的对应项删除。
    - 二级目录结构可以解决文件重名问题，并可以获得较高的查找速度，但二级目录结构缺乏灵活性，特别是当用户需要在某些任务.上进行合作和访问其他文件时会产生很多问题。
- 树形目录结构
    - 为了便于系统和用户更灵活、方便地组织管理和使用各类文件，将二级目录的层次关系加以推广，便形成了多级目录结构，又称为树形目录结构。
    - 在树形目录结构中，第一级目录称为根目录(树根)，目录树中的非叶子结点均为目录文件(又称为子目录)，叶子结点为文件。系统会为每个文件赋予唯一的标识符(内部标识符)，该标识符对用户是透明的。
    - 树形目录结构中引入了以下概念。
        - 路径名。 在树形目录结构中，往往使用路径名来唯一标识文件。 文件的路径名是一个字符串，该字符串由从根目录出发到所找文件的通路上的所有目录名与数据文件名用分隔符“\”连接而成。从根目录出发的路径称为绝对路径，从当前目录开始直到文件为止的路径称为相对路径。
        - 当前目录。当树形目录的层次较多时，如果每次都要使用完整的路径名来查找文件,会使用户感到不便，系统本身也需要花费很多时间进行目录搜索。为此应采取有效措施解决这一问题。考虑到一个进程在-段时间内所访问的文件通常具有局部性，因此可在这段时间内指定某个目录作为当前目录(或称为工作目录)。进程对各文件的访问都是相对于当前目录进行的，此时文件使用的路径名为相对路径。系统允许文件路径往上走，并用“.”表示给定目录(文件)的父目录。
    - 树形目录结构可以很方便地对文件进行分类，层次结构清晰，也能够更有效地进行文件的管理和保护。但是在树形目录中查找一个文件，需要按照路径名逐级访问中间结点，增加了磁盘访问次数，进而影响了查询速度。
- 图形目录结构
    - 树形目录结构便于实现文件分类，但是不便于实现文件共享，为此，在树形目录结构的基础上增加了一些指向同一结点的有向边，使整个目录成为一个有向无环图。这就是图形目录结构，引入这种结构的目的是实现文件共享。当某用户要求删除一个共享结点时，系统不能将其简单地删除，否则会导致其他用户访问时找不到结点。为此，可以为每个共享结点设置一个共享计数器，每当增加对该结点的共享链时，计数器加1;每当有用户提出删除该结点时，计数器减1。仅当共享计数器为0时，.才真正删除该结点，否则仅删除提出刪除请求用户的共享链。图形目录结构方便实现文件的共享，但使系统的管理变得复杂。
### 文件共享
实现文件共享是文件系统的重要功能。文件共享是指不同的用户可以使用同一个文件。文件共享可以节省大量的外存空间和主存空间，减少输入/输出操作，为用户间的合作提供便利条件。文件共享并不意味着用户可以不加限制地随意使用文件，那样文件的安全性和保密性将无法保证。也就是说，文件共享应该是有条件的，是要加以控制的。因此，文件共享要解决两个问题: 一是如何实现文件共享;二是对各类需要共享的用户进行存取控制。
- 共享动机
    - 一来多用户操作系统中不同的用户间需要共享一些文件来共同完成任务; 二来网络上不同的计算机之间需要进行通信，需要远程文件系统的共享功能的支持。
- 基于索引结点的共享方式(硬链接)
    - 传统树形目录文件的共享是由不同用户通过将各自文件的FCB设置成相同的物理地址来实现的，即不同的目录项指向同样的几个物理块。而当其中一个目录项进行了添加物理块的操作(在文件中增加了新的内容)后，另一个目录项中却并没有增加，所以新增的物理块不能被两个目录项所共享。
    - 那么索引结点如何实现文件共享呢?
    - 先前已经介绍过了，索引结点是把FCB中的文件描述信息单独构成一个数据结构，也就是说，物理块的信息在索引结点中。此时，目录项中只有文件名和指向索引结点的指针，两个不同的目录项只需要指向相同的索引结点即可实现共享，即一个共享文件只有一个索引结点。如果不同文件名的目录项需要共享该文件，只需目录项中的指针都指向该索引结点即可。
    - 在索引结点中再增加一个计数值来统计指向该索引结点的目录项的个数，这样一来就需要在删除该文件时可以先判断计数值，只有计数值为1时才删除该索引结点，若计数值大于1,则把计数值减1即可。这种方法能够实现文件的异名共享，但当文件被多个用户共享时，文件拥有者不能删除文件。
- 利用符号链实现文件共享(软链接)
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130190836.png)
    - 如图4-6所示，该方法是创建一个称为链接的新目录项。例如，为了使得用户B能共享用户C的一个文件，可以由系统为用户B建立一个指向该文件的新目录项，并放在用户B的目录下，在新目录项中包含了被共享文件的路径名，可以是绝对路径或者相对路径。当需要访问一个文件时，就搜索目录表，如果目录项标记为链接，那么就可以获取真正文件(或目录)的名称，再搜索目录。链接可以通过使用目录项格式(或通过特殊类型)而加以标记，其实际上是具有名称的间接指针。在遍历目录树时，系统忽略这些链接以维护系统的无环结构。在利用符号链方式实现文件共享时，只有文件拥有者才拥有指向其索引结点的指针;而共享该文件的其他用户只有该文件的路径名，并不拥有指向其索引结点的指针。这样就不会发生在文件拥有者删除共享文件后留下悬空指针的情况。当文件拥有者把一个共享文件删除后，其他用户试图通过符号链去访问一个已被删除的共享文件时，会因系统找不到该文件而使访问失败，于是再将符号链删除，此时不会产生任何影响。符号链方式有一个很大的优点，就是它能够用于链接(通过计算机网络)世界上任何地方的计算机中的文件，此时只需提供该文件所在机器的网络地址以及该机器中的文件路径即可。
    - 这种方法解决了基于索引结点共享方法中文件拥有者不能删除共享文件的问题，但是当其他用户要访问共享文件时，需要逐层查找目录，开销较大。
### 文件保护：访问类型、访问控制。
文件保护用来防止文件受到物理破坏和非法访问。
- 访问类型
    - 对文件的保护可以从限制对文件的访问类型出发，可以加以控制的访问类型有读、写、执行、添加、删除、列表清单(列出文件名和文件属性)等。此外，还可以对文件的重命名、复制、编辑等加以控制。
- 访问控制
    - 访问控制就是对不同的用户访问同一个文件采取不同的访问类型。根据用户的权限不同，可以把用户划分为拥有者、工作组用户、其他用户等。然后对不同的用户组采取不同的访问类型，以防文件被非法访问。
    - 访问控制通常有4种方法:访问控制矩阵、访问控制表、用户权限表以及口令与密码。
    - 访问控制矩阵、访问控制表和用户权限表这3种方法比较类似，它们都是采用某种数据结构记录每个用户或用户组对于每个文件的操作权限，在访问文件时通过检查这些数据结构来看用户是否具有相应的权限来对文件进行保护。而口令与密码是另外一种访问控制方法。
    - 口令指用户在建立一个文件时提供一个口令，系统为其建立FCB时附上相应口令，用户请求访问时必须提供相应口令。这种方法的开销较小，但是口令直接存储在系统内部，不够安全。密码指用户对文件进行加密，文件被访问时需要使用密钥。这种方法的保密性强，节省存储空间，但编码和译码要花费一定时间。
## 文件系统实现
### 文件系统层次结构
文件系统是指操作系统中与文件管理有关的软件和数据的集合。从系统角度看，文件系统是对文件的存储空间进行组织和分配，负责文件的存储并对存入文件进行保护和检索的系统。具体来说，它负责为用户建立、撤销、读写、修改和复制文件。从用户角度看，文件系统主要实现了按名存取。也就是说，当用户要求系统保存一个已命名文件时，文件系统根据一定的格式将用户的文件存放到文件存储器中适当的地方;当用户要求使用文件时，系统根据用户所给的文件名能够从文件存储器中找到所要的文件。如图4-7所示，文件系统一种合理的层次结构可分为用户接口、文件目录系统、存取控制验证、逻辑文件系统与文件信息缓冲区和物理文件系统。
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130191305.png)
- 用户接口。 操作系统通常使用图形桌面作为一个接口，当然还有Windows下的黑黑的cmd以及Linux, Mac. 上好用的命令窗口，都是用户接口。这个用户是广义的概念，不仅仅指代程序员。比如查看文件F的内容，那么通过接口操作，向操作系统发出命令，这就是第一层，最抽象的也是最顶层的面对用户的接口，连接的是现实世界与虚拟世界。
- 文件目录系统。 操作系统得到命令后要干的事情是查找目录，得到文件F的索引信息，这个索引信息可以通过FCB，也可以通过索引结点。前面讨论过抽出文件名得到的i结点，按名存取时找到i结点指针。一个文件有一个FCB或一个i结点(索引结点)，这便是在第二层:文件目录系统做的事情。
- 存取控制验证。 找到FCB后，不是所有人都有资格见到F文件，还需要考察你的资质。好像你前面费了一番心思到了一个景区，想去看看，得验证你是不是有票，票即资格。FCB.上有你是不是可以访问此文件的权限信息，这便是存取控制验证。现在假定的任务是读,也即取，写入是存。存取这个名称也是可以再掰开体会的。
- 逻辑文件系统与文件信息缓冲区。这个也很好理解。确定你可以进入后，开始真正地帮你找具体的物理地址。我们应该建立起这样一种概念:操作系统通常先是管逻辑地址，再去根据相应的策略得到物理地址。这个部分的功能是:获得相应文件的逻辑地址，而具体的物理地址需要在物理文件系统中获取。
- 物理文件系统。 这是底层的实现，分为两部分内容:辅存的分配管理和设备的管理。在UNIX下，设备也是文件。具体的以后再展开。

### 目录实现
- 线性表
    - 最为简单的目录实现方法是使用存储文件名和数据块指针的线性表(数组、链表等)。创建新文件时，必须首先搜索目录表以确定没有同名的文件存在，接着在目录表后增加一个目录项。若要删除文件，根据给定的文件名搜索目录表，接着释放分配给它的空间。采用链表结构可以减少删除文件的时间，其优点在于实现简单，不过由于线性表需要采用顺序方法查找特定的项，故运行比较费时。
- 散列表
    - 散列表根据文件名得到一个值，并返回一个指向线性表中元素的指针。这种方法大大缩短了查找目录的时间，插入和删除也比较简单，不过需要一些措 施来避免冲突(两个不同名文件的散列函数值相同)。这种方法的特点是散列表长度固定以及散列函数对表长的依赖性。
### 文件实现
文件的实现主要是指文件在存储器上的实现，即文件物理结构的实现，包括外存分配方式与文件存储空间的管理。
- 外存分配方式
    - 文件的物理结构是指一个文件在外存上的存储组织形式，与外存分配方式有关。外存分配方式指的是如何为文件分配磁盘块。采用不同的分配方式将形成不同的文件物理结构。
    - 一般来说，外存的分配采用两种方式:静态分配和动态分配。静态分配是在文件建立时一次性分配所需的全部空间;而动态分配则是根据动态增长的文件长度进行分配，甚至可以一次分配一个物理块。在分配区域大小上，也可以采用不同方法。可以为文件分配一个完整的区域以装下整个文件，这就是文件的连续分配。但文件存储空间的分配通常以块或簇(几个连续物理块称为簇，一般是固定大小) 为单位。常用的外存分配方法有连续分配、链接分配和索引分配。
    - (1)连续分配
        - 连续分配是最简单的磁盘空间分配策略，该方法要求为文件分配连续的磁盘区域，如图4-8所示。在这种分配算法中，用户必须在分配前说明待创建文件所需的存储空间大小，然后系统查找空闲区的管理表格，查看是否有足够大的空闲区供其使用。如果有，就给文件分配所需的存储空间;如果没有，该文件就不能建立，用户进程必须等待。
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130191803.png)
        - 采用连续分配方式时，可把逻辑文件中的记录顺序地存储到相邻的物理盘块中，这样所形成的文件结构称为顺序文件结构，此时的物理文件称为顺序文件。这种分配方式保证了逻辑文件中的记录顺序与存储器中文件占用盘块的顺序一致。
        - 连续分配的优点是查找速度比其他方法快(只需要起始块号和文件大小)，目录中关于文件物理存储位置的信息也比较简单。其主要缺点是容易产生碎片，需要定期进行存储空间的紧缩。很显然，这种分配方法不适合文件随时间动态增长和减少的情况，也不适合用户事先不知道文件大小的情况。
    - (2)链接分配
        - 对于文件长度需要动态增减以及用户事先不知道文件大小的情况，往往采用链接分配。这种分配策略有以下两种实现方案。
        - 隐式链接。 该实现方案用于链接物理块的指针隐式地放在每个物理块中，目录项中有指向索引顺序文件的第一块盘块和最后-块盘块的指针，此外每个盘块中都含有指向下一盘块的指针，如图4-9所示。若要访问某一个盘块，需要从第一个盘块开始一个个盘块都读出指针来，所以存在随机访问效率低的问题;由于其中任何一个盘块的指针错误都会导致后面的盘块的位置丢失，因此这种实现方案可靠性较差。
        - 显式链接。 该实现方案用于链接物理块的指针显式存放在内存的一张链接表中，每个磁盘设置一张链接表， 如图4-10所示。这个表又称为文件分配表(File Allocation Table,FAT)，MS-DOS、Windows和OS/2等操作系统都用了FAT。由于还是链接方式，因此在FAT中找一个记录的对应物理块地址时还是需要一个个找下去，不能随机查找。但是与隐式链接相比，该方案是在内存中而非在磁盘中查找，所以能节省不少时间。
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130191958.png)
        - 链接分配的优点是简单(只需起始位置)，文件创建与增长容易实现。其缺点是不能随机访问盘块，链接指针会占用一些存储空间，而且存在可靠性问题。
    - (3)索引分配
        - 链接分配方式虽然解决了连续分配方式中存在的问题，但又出现了新的问题。首先，当要求随机访问文件中的一个记录时，需要按照链接指针依次进行查找，这样查找十分缓慢。其次，链接指针要占用一定数量的磁盘空间。为了解决这些问题，引入了索引分配方式。在索引分配方式中，系统为每个文件分配一个索引块，索引块中存放索引表，索引表中的每个表项对应分配给该文件的一个物理块，如图4-11所示。
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130192111.png)
        - 索引分配方式不仅支持直接访问，而且不会产生外部碎片，文件长度受限制的问题也得到了解决。其缺点是由于索引块的分配，增加了系统存储空间的开销。对于索引分配方式，索引块的大小选择是一个很重要的问题。为了节约磁盘空间，希望索引块越小越好，但索引块太小则无法支持大文件，所以要采用一些技术来解决这个问题。另外，存取文件需要两次访问外存一首 先读取索引块的内容,其次再访问具体的磁盘块，因而降低了文件的存取速度。
        - 为了更有效地使用索引表，避免访问索引文件时两次访问外存，可以在访问文件时先将索引表调入内存中，这样，文件的存取就只需要访问一次外存 了。当文件很大时，文件的索引表会很大。如果索引表的大小超过了一个物理块，可以将索引表本身作为一个文件，再为其建立一个“索引表”，这个“索引表”作为文件索引的索引，从而构成了二级索引。第一级索引表的表目指向第二级索引，第二级索引表的表目指向文件信息所在的物理块号。依次类推，可逐级建立索引，进而构成多级索引。
        - 索引分配支持直接访问，而且没有外部碎片，但是索引块本身会占用空间。
        - 1)单级索引分配。单级索引分配方法就是将每个文件所对应的盘块号集中放在一起，为每个文件分配一个索引块(表),再把分配给该文件的所有盘块号都记录在该索引块中，因而该索引块就是一个包含多个盘块号的数组。
        - 图4-12为test 文件分配的盘块，依次是9、16、 1、10。建立一个索引块，其盘块号为19，则在目录文件中该表项的块序号为19， 并在19号盘块中建立其分配盘块号的索引。图4-12中表示test文件分配的第1个盘块号为9,第2个盘块号为16,依次类推(-1表示结束)。
        - 2)两级索引分配。当文件较大，一个索引块放不下文件的块序列时，可以对索引块再建立索引，这样构成二级索引，如图4-13所示，test 文件的目录项的索引地址为主索引的块号，主索引中的各块号是第二级索引的块号，第二级索引中的块号才构成文件的块号序列。
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130192333.png)
        - 若盘块的大小为4KB，每个表项大小为4B，采用单级索引时允许的最大文件长度为Nx4KB= 4MB (其中N=4KB/4B=1K);而采用两级索引时所允许的最大文件长度为NxNx4KB=4GB，可见采用多级索引时可以大大提高文件的最大长度。这种思维可以推广到三级以及三级以上的索引分配，统称为多级索引分配。
        - 3)混合索引分配。所谓混合索引分配，是指将多种索引分配方式相结合而形成的一种分配方式。例如，系统既采用了直接地址，又采用了单级索引分配方式或两级索引分配方式，甚至多级索引分配方式。图4-14所示的是一种混合索引分配方式，假设每个盘块大小为4KB,描述盘块的盘块号需要4B。
        - 直接地址。 为了提高文件的检索速度，在索引结点中可设置10个直接地址项。这里每项中存放的是该文件所在盘块的盘块号，当文件不大于40KB时，便可以直接从索引结点中读出该文件的全部盘块号( 10x4KB- 40KB)。
        - 一次间接地址。对于较大的文件，索引结点提供了一次间接地址，其实质就是一级索引分配方式。在一次间接地址中可以存放1K个盘块号，因此允许文件长达4MB (1Kx4KB- 4MB)。若既采用直接地址，又采用一次间接地址，允许文件长达4MB+40KB。
        - 二次间接地址。当文件很大时，系统应采用二级间接地址。该方式实质上是两级索引分配方式，此时系统是在二次间接地址块中记入所有一次间接地址块的盘号。在采用二级间接地址方式时，文件最大长度可达到4GB (1Kx1K*4KB=4GB)。 如果同时采用直接地址、一次间接地址和二次间接地址，允许文件长达4GB+4MB+40KB.
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130192529.png)
        - 这种思想可以推广到三级间接地址等。当采用三级间接地址时，所允许的最大文件长度为4TB+4GB+4MB+40KB。
- 文件存储空间管理
    - 为了实现空闲存储空间的管理，系统应该记录空闲存储空间的情况，以便实施存储空间的分配。下面介绍几种常用的空闲存储空间管理方法。
    - (1)空闲文件表法
        - 文件存储设备上的一个连续空闲区可以看作一个空闲文件(又称为空白文件或自由文件)。空闲文件表方法为所有空闲文件单独建立一个目录,每个空闲文件在这个目录中占一个表目。表目的内容包括第一个空闲块号、物理块号和空闲块数目，见表4-1。
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130192731.png)
        - 当某用户请求分配存储空间时，系统依次扫描空闲文件目录，直到找到一个满足要求的空闲文件为止。当用户撤销一个文件时，系统回收该文件所占用的空间。这时也需要顺序扫描空闲文件目录，寻找一个空表目，并将释放空间的第一个物理块 号及它所占的块数填到这个表目中。
        - 这种空闲文件目录方法类似于内存动态分区的管理。当请求的块数正好等于某个目录表目中的空闲块数时，就把这些块全部分配给该文件并把该表目标记为空。若该项中的块数多于请求的块数，则把多余的块号留在表中，并修改该表目中的各项。同样，在释放过程中, 若被释放的物理块号与某一目录项中的物理块号相邻，则还要进行空闲文件的合并。
        - 仅当文件存储空间中只有少量空闲文件时，这种方法才有较好的效果。若存储空间中有大量的小空闲文件，则空闲文件目录将变得很大，其效率将大为降低。这种管理方法仅适用于连续文件。
    - (2)空闲块链表法;
        - 空闲块链表法是将文件存储设备上的所有空闲块链接在-起， 形成一条空闲块链，并设置一个头指针指向空闲块链的第一个物理块。 当用户建立文件时，就按需要从链首依次取下几个空闲块分配给文件。当撤销文件时，回收其存储空间，并将回收的空闲块依次链入空闲块链表中。
        - 也可以将链表中的空闲盘块改为空闲盘区(每个空闲盘区包含若干个连续的空闲盘块)，这样的链称为空闲盘区链。其中，在每个盘区上除了含有用于指示下一个空闲盘区的指针外，还应含有能指明本盘区大小的信息。分配盘区的方法与内存的动态分区分配类似，通常采用首次适应算法。在回收盘区时，同样也要将回收区与相邻接的空闲盘区合并。
    - (3)位示图法
        - 位示图法是为文件存储器建立一张位示图(尽管称其为图，其实就是一连串的二进制位)，以反映整个存储空间的分配情况。在位示图中，每一个二进制位都对应一个物理块，若某位为1,表示对应的物理块已分配;若为0，表示对应的物理块空闲。当请求分配存储空间时，系统顺序扫描位示图并按需要从中找出一组值为 0的二进制位，再经过简单的换算就可以得到相应的盘块号，然后将这些位变为1。当回收存储空间时，只需要将位示图的相应位清0。
        - 位示图的大小由磁盘空间的大小(物理块总数)确定，因为位示图仅用一个二进制位代表一个物理块，所以它通常比较小，可以保存在主存中，这就使得存储空间的分配与回收较快。但这种方法实现时，需要进行位示图中二进制所在位置与盘块号之间的转换。
    - (4)成组链接法(UNIX的文件存储空间管理方法)
        - 成组链接法适用于大型文件系统。该方法将一个文件的所有空闲块按每组100块分成若干组，把每一组的盘块数目和该组的所有盘块号记入到前一组的第一个盘块中，第一组的盘块数目和第一组的所有盘块号记入到超级块中，如图4-16所示。这样每组的第一个盘块就链接成了一个链表，而组内的多个盘块形成了堆栈。每组的第一块是存放 下一组的块号的堆栈，堆栈是临界资源，每次只能允许一个进程访问，所以系统设置了一把锁来对其互斥地访问。
        - 1)分配空闲盘块的方法。当系统要为文件分配空闲盘块时，先查找第一组的盘块数，若不止一块，则将超级块中的空闲盘块数减1，将栈顶的盘块分配出去。若第一组只剩下一块(是放置下一组的盘块数和盘块号的那个块,不是空闲块)且栈顶的盘块号不是结束标记0(说明这一组不是最后一组)，则先将该块的内容读到超级块中(下一组成了第一组，所以下一组的盘块数和盘块号需要放到超级块中)，然后再将该块分配出去(该块中的信息不再有用，这一块成了空闲块);若栈顶的盘块号是结束标记0,则表示磁盘已无空闲盘块，分配不成功。
        - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211130193359.png)
        - 2)空闲盘块回收的方法。当系统回收空闲块时，若第一组不满 100块，则只要在超级块的空闲盘块的栈顶放入该空闲盘块的块号，并将其中的空闲盘块数加1即可;若第一组已经有100块了，则先将第一组中的盘块数和盘块号写入到该空闲盘块中,然后将“盘块数=1及栈顶块号=该空闲盘块块号”写入到超级块中(该空闲盘块成了新的第一组， 原本的第一组成了第二组)。成组链接法占用的空间小，而且超级块不大，可以放在内存中，这样使得大多数分配和.回收空闲盘块的工作在内存中进行，提高了效率。
## 磁盘组织与管理
### 磁盘的结构
- 磁盘的物理结构
    - 磁盘是典型的直接存取设备，这种设备允许文件系统直接存取磁盘上的任意物理块。磁盘机一般由若干磁 盘片组成，可沿一个固定方向高速旋转。每个盘面对应一个磁头，磁臂可以沿着半径方向移动。磁盘上的一系列同心圆称为磁道，磁道沿径向又分成大小相等的多个扇区，盘片上与盘片中心有一定距离的所有磁道组成了一个柱面，如图4-17所示。因此，磁盘上的每个物理块可以用柱面号、磁头号和扇区号表示。
- 磁盘结构中的信息
    - 磁盘结构中的常用信息如下。
    - 引导控制块。 通常为分区的第一块,若该分区没有操作系统，则为空。
    - 分区控制块。 其中包括分区的详细信息，如分区的块数、块的大小、空闲块的数目和指针等。
    - 目录结构。采用目录文件组织。
    - 文件控制块。其中包括文件的信息，如文件名、拥有者、文件大小和数据块位置等。
- 磁盘的访问时间Ta
    - 磁盘的访问时间T。表示为访问时间=寻道时间+旋转延迟+传输时间
    - (1)寻道时间Ts
        - 磁盘接收到读指令后，磁头从当前位置移动到目标磁道位置，所需时间为寻道时间Ts。该时间是启动磁臂的时间s与磁头移动n条磁道所花费时间的总和，m为每移动一个磁道所需时间，即Ts = m * n + s
        - 式中，m为常数，与磁盘驱动器的速度有关，通常题目会给出。若题目没有给出磁臂的启动时间，则忽略不计(为了简化计算，总是被忽略)。
    - (2)旋转延迟T,
        - 旋转磁盘、定位数据所在的扇区所需的时间为旋转延迟Tr。设磁盘的旋转速度为r,则T-(1/r)/2=1/(2r)
        - 这里T,的物理意义就是磁盘旋转半周的时间，通常题目的问法是“每个磁道读取1个随机分布的扇区”，正因为这个“随机”，所以取旋转长度的平均期望值，也就是半周，所以平均旋转延迟就是磁盘旋转半周的时间。
    - (3)传输时间Tt
        - 从磁盘上读取数据的时间为传输时间T。传输时间取决于每次读写的字节数b和磁盘的旋转速度，即T=b/(rN)式中，r为转速; N为一个磁道上的字节数。
### 磁盘调度算法
磁盘是可以被多个进程共享的设备。当有多个进程都请求访问磁盘时，应采用一种适当的调度算法，以使各进程对磁盘的平均访问时间(主要是寻道时间)最短。下面介绍几种磁盘调度算法。
- 先来先服务(FCFS) 算法
    - FCFS算法是一种最简单的磁盘调度算法。该算法按进程请求访问磁盘的先后次序进行调度。该算法的特点是合理、简单，但未对寻道进行优化。
- 最短寻道时间优先(SSTF) 算法
    - SSTF算法选择与当前磁头所在磁道距离最近的请求作为下一次服务的对象。该算法的寻道性能比FCFS算法好，但不能保证平均寻道时间最短，并且可能会使某些进程的请求总被其他进程的请求抢占而长期得不到服务(这种现象称为“饥饿”)。
- 扫描算法(SCAN)或电梯调度算法
    - SCAN算法在磁头当前移动方向上选择与当前磁头所在磁道距离最近的请求作为下一次服务的对象。由于这种算法中磁头移动的规律颇似电梯的运行，故也称为电梯调度算法。SCAN算法具有较好的寻道性能，又避免了“饥饿”现象，但其对两端磁道请求比较不公平(通常两端请求都是最后得到服务)。
- 循环扫描(C-SCAN)算法
    - C-SCAN算法是对SCAN算法的改良，它规定磁头单向移动，例如，自里向外移动，当磁头移到最外磁道时立即返回到最里磁道，如此循环进行扫描。该算法消除了对两端磁道请求的不公平。
### 磁盘的管理
- 磁盘格式化
    - 一个新的磁盘只是一个含有磁性记录材料的空白盘。在磁盘能存储数据前，它必须分成扇区以便磁盘控制器能进行读和写操作，这个过程称为低级格式化。低级格式化为磁盘的每个扇区采用独特的数据结构。每个扇区的数据结构通常由头部、数据区域(通常为512B)和尾部组成。头部和尾部包含了一些磁盘控制器所使用的信息。为了使用磁盘存储文件，操作系统还需要将自己的数据结构记录在磁盘上。
    - 1)将磁盘分为由一个或多个柱面组成的分区(就是常见的C盘、D盘等分区)。
    - 2)对物理分区进行逻辑格式化(创建文件系统)，操作系统将初始的文件系统数据结构存储到磁盘上，这些数据结构包括空闲和已经分配的空间以及一个初始为空的目录。
- 引导块
    - 计算机启动时需要运行一个初始化程序(自举程序)，它初始化CPU、寄存器、设备控制器和内存等，接着启动操作系统。为此，该自举程序应找到磁盘上的操作系统内核，装入内存，并转到初始地址，从而开始操作系统的运行。自举程序通常保存在ROM中,为了避免改变自举代码需要改变ROM硬件的问题，只在ROM中保留很小的自举装入程序，而将功能完整的自举程序保存在磁盘的启动块上，启动块位于磁盘的固定位。拥有启动分区的磁盘称为启动磁盘或系统磁盘。
- 坏扇区
    - 由于硬件有移动部件且容错能力差，因此容易导致一个或多个扇区损坏。根据所使用的磁盘和控制器，对这些块有多种处理方式。对于简单磁盘，如电子集成驱动器(IDE)， 坏扇区可手工处理，如MS-DOS的Format命令执行逻辑格式化时会扫描磁盘检查坏扇区。坏扇区在FAT. 上会标明，因此程序不会使用。
    - 对于复杂的磁盘，如小型计算机系统接口(SCSD), 其控制器维护一个磁盘坏块链表。该链表在出厂前进行低级格式化时就初始化了，并在磁盘的整个使用过程中不断更新。低级格式化将一些块保留作为备用，对操作系统透明。控制器可以用备用块来逻辑地替代坏块，这种方案称为扇区备用。

## 习题
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214221209.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214221401.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214221842.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214221903.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214222238.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214223738.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214224054.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214224126.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214224306.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214224433.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214224626.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214225114.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214225129.png)

磁盘的物理格式化和逻辑格式化：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214225249.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214225311.png)

*
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214225945.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214230013.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214230351.png)

位示图是磁盘空闲管理中的一种方式，其做法是为文件存储器建立--张位示图(尽管称其为图，其实就是一连串的二进制位)，以反映整个存储空间的分配情况。

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214230744.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214230802.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211214230831.png)
