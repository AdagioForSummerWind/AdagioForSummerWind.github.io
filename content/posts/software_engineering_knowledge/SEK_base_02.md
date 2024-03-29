---
title: "SEK_base_02"
date: 2021-11-25T15:47:55+08:00
lastmod: 2021-11-25
tags: [software engineering knowledge]
categories: [School courses]
slug: software engineering procedure and management
draft: true
---
> 笔记来自2021秋哈工大深圳软件工程授课教师张永兵

## 第二部分 软件项目开发过程与管理
### 2.1 软件项目开发过程
- 为什么要将软件开发过程划分为多个阶段？
    -  六七十年代的软件开发过程：程序员个人设计、个人操作、小作坊式的开发过程，开发的软件可靠差，难以维护，无法满足快速发展的软件需求，因而产生软件危机。
    -  如何解决软件危机？
        - 把软件开发过程划分为多个阶段使得每个阶段有明确的任务，通过问题的分解，将大规模、结构复杂的软件开发变的容易控制和管理。
- 软件开发的典型阶段：
    - 计划
    - 需求分析
    - 软件设计
    - 软件实现
    - 软件验证
    - 软件维护
    - 可以通过调整软件开发不同阶段的顺序使之适应不同的情况。
- 为什么要验证和确认？
    - 存在的问题：
        - 要求开发之前需求被充分理解
        - 与客户的交互只在开始(需求)和最后(发布)
        - 实际情况？用户的需求是模糊的
    - 优点：
        - 通过用户的反馈进行验证和确认
        - 开发的产品与客户的需求接近
- 典型软件过程模型
    - 瀑布模型
        - 瀑布模型的开发阶段严格按照线性方式进行，每一个阶段具有相关的里程碑和交付产品，且需要确认和验证
        - 基本思想
            - 将软件开发过程划分为分析、设计、编码、测试等阶段
            - 工作以线性方式进行，上一阶段的输出是下一阶段的输入
            - 每个阶段均有里程碑和提交物
        - **特点**
            - 需求最为重要，假设需求是稳定的
            - 以文档为中心，文档是连接各阶段的关键
        - 问题：
            - 文档的问题
                - 对于文档的评估需要各领域的专家，文档是否有效？
                - 当某一文档调整后的影响，不同文档间如何保持一致性？
                - 大量的文档就一定有效吗？
                - 把用户隔离在开发过程之外，不容易满足用户需求。
            - 对于大多数软件项目，客户看到软件前无法可靠地描述他们想要的是什么，而瀑布模型却要求客户明确需求，这就很难适应许多项目开始阶段必然存在的不确定性。
            - 客户必须要有耐心，因为只有在项目接近尾声时，他们才能得到可执行的程序。对于系统中存在的重大缺陷，如果在可执行程序评审之前没有发现，将可能造成惨重损失。
            - 瀑布模型在某些项目中容易导致“阻塞状态”。因为任务之间的依赖性，开发团队的一些成员要等待另一些成员工作完成才能进行下一步工作。
            - 平均水平的项目开发过程中会有25%，大型项目有40%的需求变化
            - 需求变更导致的返工占总返工量的80%
                - 瀑布方法需求中45%从未被使用，时间计划与实际相差4倍
        - **瀑布模型适用场合**：
            - 软件项目较小；
            - 需求在项目开始之前已经被全面的了解；
            - 需求在开发中不太可能发生重大改变；
            - 外部环境的不可控因素很少。
    - 增量过程模型
        - 增量模型
            - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211125164540.png)
            - **优点**：
                - 在时间要求较高的情况下交付产品：在各个阶段并不交付一个可运行的完整产品，而是交付满足客户需求的一个子集的可运行产品，**对客户起到“镇静剂”的作用**；
                - 人员分配灵活：如果找不到足够的开发人员，可采用增量模型，**早期的增量由少量人员实现，如果客户反响较好，则在下一个增量中投入更多的人力**；
                - 逐步增加产品功能可以使用户有较充裕的时间来学习和适应新产品，避免全新软件可能带来的冲击；
                - 因为具有较高优先权的模块被首先交付，而后面的增量也不断被集成进来，这使得**最重要的功能肯定接受了最多的测试**，从而使得项目总体性失败的风险比较低。
            - **缺点**：
                - 每个附加的增量并入现有的软件时，必须不破坏原来已构造好的东西。
                - 同时，加入新增量时应简单、方便:该类软件的体系结构应当是开放的；
            - **增量模型的适用情况**
                - 在开始开发时，需求很明确，且产品还可被适当地分解为一些独立的、可交付的软件。
                - 在开发中，期望尽快提交其中的一些增量产品。
        - 快速应用程序开发（RAD）
            - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211125164854.png)
            - **RAD模型的使用方法**
                - 侧重于短开发周期(一般为60~90天)的增量过程模型，通过基于已有资源的构建方法实现快速开发。
                - 本质：是瀑布模型的高速变体，并行运行瀑布模型。
            - 优点：
                - 提高软件交付速度
                - 充分利用企业已有资产进行项目开发
            - 缺点：
                - 需求充分理解，系统被合理的模块化；
                - 需要**大量的人力资源**来创建多个相对独立的RAD团队；
                - **要求管理水平高**，如果没有在短时间内为急速完成整个系统做好准备，RAD项目将会败；
                - 如果系统需求是**高性能**，并且需要通过调整构件接口的方式来提高性能，不能采用RAD型
                - **技术风险很高的情况**下(采用很多新技术、软件需与其他已有软件建立集成、等等)，不宜采用RAD。
    - 演化过程模型 
        - **快速原型开发模型** 
            - 原型快速分析:指在分析者和用户的紧密配合下,快速确定软件系统的基本要求。
            - 原型构造:在原型分析的基础上,根据基本需求规格说明,**忽略细节,只考虑主要特性，快速构造一个可运行的系统**。
            - 原型运行与评价:软件开发人员与用户频繁通信、发现问题、消除误解的重要阶段，目的是发现新需求并修改原有需求。
            - 原型修正:对原型系统，要根据修改意见进行修正。
            - 判定原型完成:如果原型经过修正或改进，获得了参与者的一致认可，那么原型开发的迭代过程可以结束
            - **优点**：
                - 快速开发出可以演示的系统，方便与客户沟通；
                - 采用迭代技术能够使开发者逐步弄清客户的需求；
            - 缺点：
                - 为了尽快完成原型，开发者没有考虑整体软件的质量和长期的可维护性，**系统结构通常较差**；
                - 用户可能混淆原型系统与最终系统，原型系统在完全满足用户需求之后可能会被直接交付给客户使用；
        - **螺旋模型**
            - 该模型由**Dr.Barry Boehm[Boehm 1988]**提出。它是在瀑布模型和演化模型的基础上，加入两者所忽略的风险分析所建立的一种软件开发模型。
            - 该模型将软件生存周期的活动分为四个可重复的阶段：**规划、风险分析、开发和评估**
            - 优点：结合了原型的迭代性质与瀑布模型的系统性和可控性，是一种风险驱动型的过程模型；
                - 采用循环的方式逐步加深系统定义和实现的深度，同时更好的理解、应对和降低风险；
                - 确定一系列里程碑，确保各方都得到可行的系统解决方案；
                - 始终保持可操作性，直到软件生命周期的结束；
                - 由风险驱动，支持现有软件的复用。
            - 缺点：
                - 适用于**大规模软件项目，特别是内部项目，周期长、成本高；**
                - 软件开发人员应该擅长寻找可能的风险，准确的分析风险，否则将会带来更大的风险；
                - 由于构建产品所需的周期数据不确定，给项目管理带来困难；
                - 演化速度不易把握，演化速度太快，项目陷入混乱；演化速度太慢，影响生产率；
                - **为追求软件的高质量而牺牲了开发速度、灵活性和可扩展性；**
### 2.2 软件项目开发管理
#### 软件项目管理概念及特征
- 项目(Project)：精心定义的一组活动，使用受约束的资源(资金、人、原料、能源、空间等)来满足预定义的目标。
- 项目管理(Project Management, PM)：有效的组织与管理各类资源(例如人)，以使项目能够在预定的范围、质量、时间和成本等约束条件下顺利交付(deliver)
    - 挑战1：在各类约束条件下交付项目；
    - 挑战2：通过优化资源的分配与集成来满足预先定义的目标；
- **软件管理的4P**:
    - 软件人员
    - 软件产品
    - 软件过程
    - 软件工具
#### 软件项目估算
- 项目估算是对项目的规模、工作量、时间和成本等进行预算和估计的过程。
- 软件项目估算的挑战是项目的复杂性和不确定性
    - 软件规模越大，复杂性越高，不确定性就越大
    - 需求的不确定性会对项目估算产生很大影响
    - 没有可靠的历史数据使项目估算缺少参照物
- 总结：**估算的风险取决于资源、成本及进度的定量估算中存在的不确定性。**
- 基本估算方法
    - **参数估算**：通过对大量的项目历史数据进行统计分析，使用项目特性参数建立经验估算模型，估算诸如成本、预算和持续时间等活动参数。
    - **代码行技术(LOC)**：从过去开发类似产品的经验和历史数据出发，估计出所开发软件的代码行数
    - **功能点方法**是依据软件信息域的基本特征和对软件复杂性的估计，估算出软件规模。这种方法适合于在软件开发初期进行估算，并以功能点为单位度量软件规模。
    - **结构性成本模型 COCOMO（COnstructive COst MOdel**是一种利用经验模型进行成本估算的方法。
    - **故事点**的基本做法：把一些常见“标准任务”给出一个“标准点数”，形成比较基线；估算时只要是同一类型任务，直接写故事点数而非天数。
    - **人工神经网络**是采用一种学习方法导出一种预测模型，首先建立神经网络，再使用一组历史项目数据（样本数据）训练网络，训练后的网络可以用于估算新项目的工作量。
#### 项目进度安排
- Step1：工作量分配
    - 40-20-40法则
    - 40%的工作量分配给前期的分析和设计
    - 20%的工作量分配给编码
    - 40%分配给测试
- Step 2：定义任务网络
    - 任务不是独立存在的，各任务在顺序上存在相互依赖关系。
    - 项目管理里通常采用“网络图(Network Diagram)”的形式对此进行描述。
- Step 3：时间分配
    - 在绘制出任务网络图之后，下一步需要为每一个任务分配具体的执行时间。
    - 两种具体的技术：
        - 程序评估及评审技术(PERT)
        - 关键路径方法(CPM)
    - 步骤：
        - Step 3.1：标出任务的最早开始(ES)与结束时间(EF)；
        - Step 3.2：标出任务的最迟开始(LS)与结束时间(LF)；
        - Step 3.3：计算关键路径(Critical Path)；
        - Step 3.4：确定任务的开始/结束时间。
        - Step 3.5：绘制最终的任务进度安排(甘特图, Gantt Diagram)
            - **甘特图**（Gantt Chart）是一种通用的显示进度方法，其横轴表示时间，纵轴表示活动，线条表示在整个期间上计划和实际的活动完成情况。
            ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20211126102900.png)
- Step 4：确认任务资源。确认每个任务的资源需求
- Step 5：确定责任。确定每个任务的负责人和参与人
- Step 6：明确结果。明确任务的输出结果（文档或部分软件）
- Step 7：确定里程碑(milestones)。确定任务与项目里程碑关联

#### 项目风险管理
- 风险识别：确定项目有哪些风险，包括运用专家判断法、头脑风暴法等分析项目风险产生的各种原因或者影响因素，以确定风险事件及其来源。
- 风险评估：比较风险的大小，确定风险的性质。通过对各种风险进行定性、定量的分析，包括发生概率、影响严重性等，确定出每种风险的大小和性质。
- 应对计划：制定风险响应的措施和实施步骤，按照风险的大小和性质，制定相应的措施去应对和响应风险，包括风险接受、风险转移等。
- 风险控制：监督、检查风险事件的发生情况以及风险措施的落实情况，通过对风险事件及其来源的控制和对风险计划落实情况的监督，确保风险措施有效。