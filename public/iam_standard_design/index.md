# iam_standard_design

## 4 | 规范设计（上）：项目开发杂乱无章，如何规范？（开源规范、文档规范和版本规范）
规范问题：
**代码风格不一**：代码仓库中有多种代码风格，读 / 改他人的代码都是一件痛苦的事情，整个代码库也会看起来很乱。**目录杂乱无章**：相同的功能被放在不同的目录，或者一个目录你根本不知道它要完成什么功能，新开发的代码你也不知道放在哪个目录或文件。这些都会严重降低代码的可维护性。**接口不统一**：对外提供的 API 接口不统一，例如修改用户接口为/v1/users/colin，但是修改密钥接口为/v1/secret?name=secret0，难以理解和记忆。**错误码不规范**：错误码会直接暴露给用户，主要用于展示错误类型，以定位错误问题。错误码不规范会导致难以辨别错误类型，或者同类错误拥有不同错误码，增加理解难度。

在设计阶段、编码之前，我们需要一个好的规范来约束开发者。

### 需要制定规范的地方
非编码类规范，主要包括开源规范、文档规范、版本规范、Commit 规范和发布规范。编码类规范，则主要包括目录规范、代码规范、接口规范、日志规范和错误码规范。

![img](https://static001.geekbang.org/resource/image/db/4e/dbcb06d2c406446418778d07d917604e.png?wh=1981*1091)

### 开源规范

为什么一定要知道开源规范呢？原因主要有两方面：一是，开源项目在代码质量、代码规范、文档等方面，要比非开源项目要求更高，在项目开发中按照开源项目的要求来规范自己的项目，可以更好地驱动项目质量的提高；二是，一些大公司为了不重复造轮子，会要求公司团队能够将自己的项目开源，所以提前按开源标准来驱动 Go 项目开发，也会为我们日后代码开源省去不少麻烦

#### 开源规范详细列表

- 项目结构：一个开源项目应该有一个合理、专业的、符合语言特色的项目结构。
- 严格遵循代码规范：开源的代码，面向的人群是所有的开发者，一个不规范的代码，可读性差，不利于其他开发者阅读和贡献代码。
- 代码质量：开源的代码，一定要保证代码的质量，一个低质量的代码，不仅隐藏了很多性能和功能缺陷，而且还会影响开源项目的品牌，进而影响开源效果。
- 单元测试覆盖率：一个开源的 Go 项目，要保证整个项目的单元测试覆盖率，这样一方面可以保证代码的质量，另一方面可以使开源项目更专业，也能让你更加安心的发布版本。
- 版本发布规范：开源项目要遵循既定的版本规范，整个项目的更新迭代，要有版本号，目前用的比较多的是语义化的版本规范。
- 向下兼容：代码要做到向下兼容，这样可以尽可能减少发布变更的影响，遵循语义化的版本规范，可以在一定程度上保证代码的向下兼容能力。

- 详细的文档说明：要保证代码能够被其他开发者很容易的阅读和贡献代码，所以不仅要保证文档的质量和数量，还要确保有某些需要的文档：
  - LICENSE（如果是开源项目，LICENSE 是必选的）：软件协议，声明该开源项目遵循什么软件协议。
  - README.md：README 文件，放在项目的根目录下，包含项目的描述、依赖项、安装方法、使用方法、贡献方法、作者和遵循的软件协议等。
  - CHANGELOG：目录，用来存放版本的变更历史，方便其他开发者了解新版本或旧版本的变更内容。 
  - Makefile：对于一个复杂的项目，通常也会包含一个 Makefile 文件，用来对项目进行构建、测试、安装等操作。
  - CONTRIBUTING.md：用来说明如何给本项目贡献代码，包含贡献流程和流程中每个环节的详细操作。
  - docs：目录，用来存放本项目所有文档，例如：安装文档、使用文档、开发文档等。一些重要的文档，可以链接到项目根目录的 README.md 文档中。这些文档要确保开发者能够轻松的理解、部署和使用该项目。
  - examples：存放一些示例代码。
- 安全：开源的代码，要保证整个代码库和提交记录中，不能出现类似内部 IP、内部域名、密码、密钥这类信息。
- 完善的 examples：完善的 examples，可以帮助用户快速学习和使用开源项目。
- 好的 Commit Message 记录：开源项目在 commit 时，要遵循一定的规范，这样其他开发者才能够快速浏览和理解变更历史，减小学习成本，本项目遵循 Angular commit message 规范。
- 发布可用的版本：要确保每一次发布都经过充分的测试，每一个发布的版本都是可用的。
- 持续的更新：一个好的开源项目，应该能够持续的更新功能，修复 Bug。对于一些已经结项、不维护的开源项目，需要及时的对项目进行归档，并在项目描述中加以说明。
- 及时的处理 pull request、issue、评论等：当项目被别的开发者提交 pull request、issue、评论时，要及时的处理，一方面可以确保项目不断被更新，另一方面也可以激发其他开发者贡献代码的积极性。
- 建立讨论小组：如果条件允许，最好和贡献者建立讨论小组，每周或每月组织讨论，共同维护。
- 做好推广：如果有条件，可以宣传运营开源项目，让更多的人知道，更多的人用，更多的人贡献代码。例如：在掘金、简书等平台发表文章，创建 QQ、微信交流群等。
- Git 工作流：选择合适的 Git 工作流，并遵循 GIt 工作流使用规范，例如 Gitflow 工作流。


#### 开源协议


经常使用的 6 种开源协议

GPL： General Public License，开源项目最常用的许可证，衍生代码的分发需开源并且也要遵守此协议。该协议也有很多变种，不同变种要求会略微不同。
MPL： MPL 协议允许免费重发布、免费修改，但要求修改后的代码版权归软件的发起者，这种授权维护了商业软件的利益，它要求基于这种软件的修改无偿贡献版权给该软件。
LGPL： Lesser General Public Licence，是 GPL 的一个为主要为类库使用设计的开源协议。LGPL 允许商业软件通过类库引用的方式使用 LGPL 类库而不需要开源商业软件的代码。但是如果修改 LGPL 协议的代码或者衍生，则所有修改的代码，涉及修改部分的额外代码和衍生的代码都必须采用 LGPL 协议。
Apache： Apache 协议是 Apache 软件基金会发布的一个自由软件许可证，Apache 2.0 协议除了为用户提供版权许可之外，还有专利许可，非常适合涉及专利内容的项目。
BSD： BSD（Berkeley Software Distribution，伯克利软件发行版）。BSD 协议在软件分发方面，除需要包含一份版权提示和免责声明之外，没有任何限制，该协议还禁止用开源代码的作者/机构名字和原来产品的名字做市场推广。
MIT： 协议的主要内容为：该软件及其相关文档对所有人免费，可以任意处置，包括使用，复制，修改，合并，发表，分发，再授权，或者销售。唯一的限制是，软件中必须包含上述版权和许可提示。MIT 协议是所有开源许可中最宽松的一个，除了必须包含许可声明外，再无任何限制。![img](https://static001.geekbang.org/resource/image/61/00/61b4d5da6c8327b738e9657c6c144000.png?wh=6503*2466)

在上图中，右边的协议比左边的协议宽松，在选择时，你可以根据菱形框中的选择项从上到下进行选择。为了使你能够毫无负担地使用 IAM 项目提供的源码，我选择了最宽松的 MIT 协议。另外，因为 Apache 是对商业应用友好的协议，使用者也可以在需要的时候修改代码来满足需要，并作为开源或商业产品发布 / 销售，所以大型公司的开源项目通常会采用 Apache 2.0 开源协议。

### 文档规范
工作中我发现，很多开发者非常注重代码产出，但不注重文档产出。他们觉得，即使没有软件文档也没太大关系，不影响软件交付。我要说的是，这种看法是错误的！因为文档属于软件交付的一个重要组成部分，没有文档的项目很难理解、部署和使用。因此，编写文档是一个必不可少的开发工作。那么一个项目需要编写哪些文档，又该如何编写呢？我认为项目中最需要的 3 类文档是 README 文档、项目文档和 API 接口文档。下面，我们一一来说它们的编写规范。

README 规范
README 文档是项目的门面，它是开发者学习项目时第一个阅读的文档，会放在项目的根目录下。因为它主要是用来介绍项目的功能、安装、部署和使用的，所以它是可以规范化的。下面，我们直接通过一个 README 模板，来看一下 README 规范中的内容：

```
# 项目名称

<!-- 写一段简短的话描述项目 -->

## 功能特性

<!-- 描述该项目的核心功能点 -->

## 软件架构(可选)

<!-- 可以描述下项目的架构 -->

## 快速开始

### 依赖检查

<!-- 描述该项目的依赖，比如依赖的包、工具或者其他任何依赖项 -->

### 构建

<!-- 描述如何构建该项目 -->

### 运行

<!-- 描述如何运行该项目 -->

## 使用指南

<!-- 描述如何使用该项目 -->

## 如何贡献

<!-- 告诉其他开发者如果给该项目贡献源码 -->

## 社区(可选)

<!-- 如果有需要可以介绍一些社区相关的内容 -->

## 关于作者

<!-- 这里写上项目作者 -->

## 谁在用(可选)

<!-- 可以列出使用本项目的其他有影响力的项目，算是给项目打个广告吧 -->

## 许可证

<!-- 这里链接上该项目的开源许可证 -->
```

[例子](https://raw.githubusercontent.com/marmotedu/iam/master/README.md)

项目文档规范
项目文档包括一切需要文档化的内容，它们通常集中放在 /docs 目录下。当我们在创建团队的项目文档时，通常会预先规划并创建好一些目录，用来存放不同的文档。因此，在开始 Go 项目开发之前，我们也要制定一个软件文档规范。好的文档规范有 2 个优点：易读和可以快速定位文档。不同项目有不同的文档需求，在制定文档规范时，你可以考虑包含两类文档。开发文档：用来说明项目的开发流程，比如如何搭建开发环境、构建二进制文件、测试、部署等。用户文档：软件的使用文档，对象一般是软件的使用者，内容可根据需要添加。比如，可以包括 API 文档、SDK 文档、安装文档、功能介绍文档、最佳实践、操作指南、常见问题等。为了方便全球开发者和用户使用，开发文档和用户文档，可以预先规划好英文和中文 2 个版本。为了加深你的理解，这里我们来看下实战项目的文档目录结构：

```
docs
├── devel                            # 开发文档，可以提前规划好，英文版文档和中文版文档
│   ├── en-US/                       # 英文版文档，可以根据需要组织文件结构
│   └── zh-CN                        # 中文版文档，可以根据需要组织文件结构
│       └── development.md           # 开发手册，可以说明如何编译、构建、运行项目
├── guide                            # 用户文档
│   ├── en-US/                       # 英文版文档，可以根据需要组织文件结构
│   └── zh-CN                        # 中文版文档，可以根据需要组织文件结构
│       ├── api/                     # API文档
│       ├── best-practice            # 最佳实践，存放一些比较重要的实践文章
│       │   └── authorization.md
│       ├── faq                      # 常见问题
│       │   ├── iam-apiserver
│       │   └── installation
│       ├── installation             # 安装文档
│       │   └── installation.md
│       ├── introduction/            # 产品介绍文档
│       ├── operation-guide          # 操作指南，里面可以根据RESTful资源再划分为更细的子目录，用来存放系统核心/全部功能的操作手册
│       │   ├── policy.md
│       │   ├── secret.md
│       │   └── user.md
│       ├── quickstart               # 快速入门
│       │   └── quickstart.md
│       ├── README.md                # 用户文档入口文件
│       └── sdk                      # SDK文档
│           └── golang.md
└── images                           # 图片存放目录
    └── 部署架构v1.png
```
API 接口文档规范
接口文档又称为 API 文档，一般由后台开发人员编写，用来描述组件提供的 API 接口，以及如何调用这些 API 接口。在项目初期，接口文档可以解耦前后端，让前后端并行开发：前端只需要按照接口文档实现调用逻辑，后端只需要按照接口文档提供功能。当前后端都开发完成之后，我们就可以直接进行联调，提高开发效率。在项目后期，接口文档可以提供给使用者，不仅可以降低组件的使用门槛，还能够减少沟通成本。显然，一个有固定格式、结构清晰、内容完善的接口文档，就非常重要了。那么我们该如何编写接口文档，它又有什么规范呢？接口文档有四种编写方式，包括编写 Word 格式文档、借助工具编写、通过注释生成和编写 Markdown 格式文档。具体的实现方式见下表：

![img](https://static001.geekbang.org/resource/image/91/63/912fe30a70df0866d168ef1c0a50cb63.jpeg?wh=1920*1080)

其中，通过注释生成和编写 Markdown 格式文档这 2 种方式用得最多。在这个专栏，我采用编写 Markdown 格式文档的方式，原因如下：相比通过注释生成的方式，编写 Markdown 格式的接口文档，能表达更丰富的内容和格式，不需要在代码中添加大量注释。相比 Word 格式的文档，Markdown 格式文档占用的空间更小，能够跟随代码仓库一起发布，方便 API 文档的分发和查找。相比在线 API 文档编写工具，Markdown 格式的文档免去了第三方平台依赖和网络的限制。

API 接口文档又要遵循哪些规范呢？其实，一个规范的 API 接口文档，通常需要包含一个完整的 API 接口介绍文档、API 接口变更历史文档、通用说明、数据结构说明、错误码描述和 API 接口使用文档。API 接口使用文档中需要包含接口描述、请求方法、请求参数、输出参数和请求示例。当然，根据不同的项目需求，API 接口文档会有不同的格式和内容。我以这门课的实战项目采用的 API 接口文档规范为例，和你解释下。

接口文档拆分为以下几个 Markdown 文件，并存放在目录 docs/guide/zh-CN/api 中：
- README.md ：API 接口介绍文档，会分类介绍 IAM 支持的 API 接口，并会存放相关 API 接口文档的链接，方便开发者查看。
- CHANGELOG.md ：API 接口文档变更历史，方便进行历史回溯，也可以使调用者决定是否进行功能更新和版本更新。
- generic.md ：用来说明通用的请求参数、返回参数、认证方法和请求方法等。
- struct.md ：用来列出接口文档中使用的数据结构。这些数据结构可能被多个 API 接口使用，会在 user.md、secret.md、policy.md 文件中被引用。
- user.md 、 secret.md 、 policy.md ：API 接口文档，相同 REST 资源的接口会存放在一个文件中，以 REST 资源名命名文档名。
- error_code.md ：错误码描述，通过程序自动生成。

这里我拿 user.md 接口文档为例，和你解释下接口文档是如何写的。user.md 文件记录了用户相关的接口，每个接口按顺序排列，包含如下 5 部分。

接口描述：描述接口实现了什么功能。请求方法：接口的请求方法，格式为 HTTP 方法 请求路径，例如 POST /v1/users。在 通用说明中的请求方法部分，会说明接口的请求协议和请求地址。输入参数：接口的输入字段，它又分为 Header 参数、Query 参数、Body 参数、Path 参数。每个字段通过：参数名称、必选、类型 和 描述 4 个属性来描述。如果参数有限制或者默认值，可以在描述部分注明。输出参数：接口的返回字段，每个字段通过 参数名称、类型 和 描述 3 个属性来描述。请求示例：一个真实的 API 接口请求和返回示例。

[更详细的API接口文档](https://github.com/marmotedu/iam/tree/master/docs/guide/zh-CN/api)


### 版本规范
在做 Go 项目开发时，我**建议**你把所有组件都加入版本机制。原因主要有两个：一是通过版本号，我们可以很明确地知道组件是哪个版本，从而定位到该组件的功能和代码，方便我们定位问题。二是发布组件时携带版本号，可以让使用者知道目前的项目进度，以及使用版本和上一个版本的功能差别等。目前业界主流的版本规范是语义化版本规范，也是 IAM 系统采用的版本规范。那什么是语义化版本规范呢？

语义化版本规范（SemVer，Semantic Versioning）是 GitHub 起草的一个具有指导意义的、统一的版本号表示规范。它规定了版本号的表示、增加和比较方式，以及不同版本号代表的含义。在这套规范下，版本号及其更新方式包含了相邻版本间的底层代码和修改内容的信息。语义化版本格式为：主版本号.次版本号.修订号（X.Y.Z），其中 X、Y 和 Z 为非负的整数，且禁止在数字前方补零。

版本号可按以下规则递增：主版本号（MAJOR）：当做了不兼容的 API 修改。次版本号（MINOR）：当做了向下兼容的功能性新增及修改。这里有个不成文的约定需要你**注意**，偶数为稳定版本，奇数为开发版本。修订号（PATCH）：当做了向下兼容的问题修正。

你可能还看过这么一种版本号：v1.2.3-alpha.1+001。这其实是把先行版本号（Pre-release）和版本编译元数据，作为延伸加到了主版本号.次版本号.修订号的后面，格式为 X.Y.Z[-先行版本号][+版本编译元数据]

先行版本号意味着，该版本不稳定，可能存在兼容性问题，格式为：X.Y.Z-[一连串以句点分隔的标识符] ，比如下面这几个例子：

```
1.0.0-alpha
1.0.0-alpha.1
1.0.0-0.3.7
1.0.0-x.7.z.92
```
编译版本号，一般是编译器在编译过程中自动生成的，我们只定义其格式，并不进行人为控制。下面是一些编译版本号的示例：

```
1.0.0-alpha+001
1.0.0+20130313144700
1.0.0-beta+exp.sha.5114f85
```
先行版本号和编译版本号只能是字母、数字，且不可以有空格。

语义化版本控制规范
语义化版本控制规范比较多，这里我给你介绍几个比较重要的。如果你需要了解更详细的规范，可以参考 [这个链接](https://semver.org/lang/zh-CN/) 的内容。

标记版本号的软件发行后，禁止改变该版本软件的内容，任何修改都必须以新版本发行。主版本号为零（0.y.z）的软件处于开发初始阶段，一切都可能随时被改变，这样的公共 API 不应该被视为稳定版。1.0.0 的版本号被界定为第一个稳定版本，之后的所有版本号更新都基于该版本进行修改。修订号 Z（x.y.Z | x > 0）必须在只做了向下兼容的修正时才递增，这里的修正其实就是 Bug 修复。次版本号 Y（x.Y.z | x > 0）必须在有向下兼容的新功能出现时递增，在任何公共 API 的功能被标记为弃用时也必须递增，当有改进时也可以递增。其中可以包括修订级别的改变。每当次版本号递增时，修订号必须归零。主版本号 X（X.y.z | X > 0）必须在有任何不兼容的修改被加入公共 API 时递增。其中可以包括次版本号及修订级别的改变。每当主版本号递增时，次版本号和修订号必须归零。

如何确定版本号？说了这么多，我们到底该如何确定版本号呢？这里我给你总结了这么几个经验：第一，在实际开发的时候，我**建议**你使用 0.1.0 作为第一个开发版本号，并在后续的每次发行时递增次版本号。第二，当我们的版本是一个稳定的版本，并且第一次对外发布时，版本号可以定为 1.0.0。

第三，当我们严格按照 Angular commit message 规范提交代码时，版本号可以这么来确定：fix 类型的 commit 可以将修订号 +1。feat 类型的 commit 可以将次版本号 +1。带有 BREAKING CHANGE 的 commit 可以将主版本号 +1。

### 总结
一个项目的规范设计主要包括编码类和非编码类这两类规范。今天我们一起学习了开源规范、文档规范和版本规范，现在我们回顾一下重点内容吧。新开发的项目最好按照开源标准来规范，以驱动其成为一个高质量的项目。开发之前，最好提前规范好文档目录，并选择一种合适的方式来编写 API 文档。在这门课的实战项目中，我采用的是 Markdown 格式，也**推荐**你使用这种方式。项目要遵循版本规范，目前业界主流的版本规范是语义化版本规范，也是我**推荐**的版本规范。


## 5 | 规范设计（下）：commit 信息风格迥异、难以阅读，如何规范？
在 Go 项目开发时，一个好的 Commit Message 至关重要：
- 可以使自己或者其他开发人员能够清晰地知道每个 commit 的变更内容，方便快速浏览变更历史，比如可以直接略过文档类型或者格式化类型的代码变更。
- 可以基于这些 Commit Message 进行过滤查找，比如只查找某个版本新增的功能：git log --oneline --grep "^feat|^fix|^perf"。
- 可以基于规范化的 Commit Message 生成 Change Log。
- 可以依据某些类型的 Commit Message 触发构建或者发布流程，比如当 type 类型为 feat、fix 时我们才触发 CI 流程。
- 确定语义化版本的版本号。比如 fix 类型可以映射为 PATCH 版本，feat 类型可以映射为 MINOR 版本。带有 BREAKING CHANGE 的 commit，可以映射为 MAJOR 版本。在这门课里，我就是通过这种方式来自动生成版本号。

接下来，我们来看下如何规范 Commit Message。另外，除了 Commit Message 之外，我还会介绍跟 Commit 相关的 3 个重点，以及如何通过自动化流程来保证 Commit Message 的规范化。



### Commit Message 的规范有哪些？

毫无疑问，我们可以根据需要自己来制定 Commit Message 规范，但是我更**建议**你采用开源社区中比较成熟的规范。一方面，可以避免重复造轮子，提高工作效率。另一方面，这些规范是经过大量开发者验证的，是科学、合理的。目前，社区有多种 Commit Message 的规范，例如 jQuery、Angular 等。我将这些规范及其格式绘制成下面一张图片，供你参考：

![img](https://static001.geekbang.org/resource/image/16/48/1699f5c1933cfe72803dfb038152fc48.png?wh=973*172)

在这些规范中，**Angular 规范在功能上能够满足开发者 commit 需求，在格式上清晰易读，目前也是用得最多的**。Angular 规范其实是一种语义化的提交规范（Semantic Commit Messages），所谓语义化的提交规范包含以下内容：
- Commit Message 是语义化的：Commit Message 都会被归为一个有意义的类型，用来说明本次 commit 的类型。
- Commit Message 是规范化的：Commit Message 遵循预先定义好的规范，比如 Commit Message 格式固定、都属于某个类型，这些规范不仅可被开发者识别也可以被工具识别。

为了方便你理解 Angular 规范，我们直接看一个遵循 Angular 规范的 commit 历史记录，见下图：

![img](https://static001.geekbang.org/resource/image/e2/fe/e227e4976406daaa039438feb5affefe.png?wh=825*420)

再来看一个完整的符合 Angular 规范的 Commit Message，如下图所示：


![img](https://static001.geekbang.org/resource/image/da/cb/da69572c5605556b8144eb4ee281c4cb.png?wh=1877*406)

通过上面 2 张图，我们可以看到符合 Angular Commit Message 规范的 commit 都是有一定格式，有一定语义的。

那我们该怎么写出符合 Angular 规范的 Commit Message 呢？在 Angular 规范中，Commit Message 包含三个部分，分别是 Header、Body 和 Footer，格式如下：

```
<type>[optional scope]: <description>
// 空行
[optional body]
// 空行
[optional footer(s)]
```
其中，Header 是必需的，Body 和 Footer 可以省略。在以上规范中，必须用括号 () 括起来， [] 后必须紧跟冒号 ，冒号后必须紧跟空格，2 个空行也是必需的。

在实际开发中，为了使 Commit Message 在 GitHub 或者其他 Git 工具上更加易读，我们往往会限制每行 message 的长度。根据需要，可以限制为 50/72/100 个字符，这里我将长度限制在 72 个字符以内（也有一些开发者会将长度限制为 100，你可根据需要自行选择）。以下是一个符合 Angular 规范的 Commit Message：

```
fix($compile): couple of unit tests for IE9
# Please enter the Commit Message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
# ...

Older IEs serialize html uppercased, but IE9 does not...
Would be better to expect case insensitive, unfortunately jasmine does
not allow to user regexps for throw expectations.

Closes #392
Breaks foo.bar api, foo.baz should be used instead
```

接下来，我们详细看看 Angular 规范中 Commit Message 的三个部分。

#### Header
Header 部分只有一行，包括三个字段：type（必选）、scope（可选）和 subject（必选）。我们先来说 type，它用来说明 commit 的类型。为了方便记忆，我把这些类型做了归纳，它们主要可以归为 Development 和 Production 共两类。它们的含义是：
- Development：这类修改一般是项目管理类的变更，不会影响最终用户和生产环境的代码，比如 CI 流程、构建方式等的修改。遇到这类修改，通常也意味着可以免测发布。
- Production：这类修改会影响最终的用户和生产环境的代码。所以对于这种改动，我们一定要慎重，并在提交前做好充分的测试。

我在这里列出了 Angular 规范中的常见 type 和它们所属的类别，你在提交 Commit Message 的时候，一定要**注意**区分它的类别。举个例子，我们在做 Code Review 时，如果遇到 Production 类型的代码，一定要认真 Review，因为这种类型，会影响到现网用户的使用和现网应用的功能。

![img](https://static001.geekbang.org/resource/image/89/27/89c618a7415c0c38b09d86d7f882a427.png?wh=726*511)

有这么多 type，我们该如何确定一个 commit 所属的 type 呢？这里我们可以通过下面这张图来确定。

![img](https://static001.geekbang.org/resource/image/35/a7/3509bd169ce285f59fbcfa6ebea75aa7.png?wh=2513*1078)


如果我们变更了应用代码，比如某个 Go 函数代码，那这次修改属于代码类。在代码类中，有 4 种具有明确变更意图的类型：feat、fix、perf 和 style；如果我们的代码变更不属于这 4 类，那就全都归为 refactor 类，也就是优化代码。如果我们变更了非应用代码，例如更改了文档，那它属于非代码类。在非代码类中，有 3 种具有明确变更意图的类型：test、ci、docs；如果我们的非代码变更不属于这 3 类，那就全部归入到 chore 类。

Angular 的 Commit Message 规范提供了大部分的 type，在实际开发中，我们可以使用部分 type，或者扩展添加我们自己的 type。但无论选择哪种方式，我们一定要保证一个项目中的 type 类型一致。

接下来，我们说说 Header 的第二个字段 scope。

scope 是用来说明 commit 的影响范围的，它必须是名词。显然，不同项目会有不同的 scope。在项目初期，我们可以设置一些粒度比较大的 scope，比如可以按组件名或者功能来设置 scope；后续，如果项目有变动或者有新功能，我们可以再用追加的方式添加新的 scope。我们这门课采用的 scope，主要是根据组件名和功能来设置的。例如，支持 apiserver、authzserver、user 这些 scope。

这里想强调的是，scope 不适合设置太具体的值。太具体的话，一方面会导致项目有太多的 scope，难以维护。另一方面，开发者也难以确定 commit 属于哪个具体的 scope，导致错放 scope，反而会使 scope 失去了分类的意义。当然了，在指定 scope 时，也需要遵循我们预先规划的 scope，所以我们要将 scope 文档化，放在类似 devel 这类文档中。这一点你可以参考下 IAM 项目的 scope 文档： [IAM commit message scope](https://github.com/marmotedu/iam/blob/master/docs/devel/zh-CN/scope.md) 。

最后，我们再说说 subject。subject 是 commit 的简短描述，必须以动词开头、使用现在时。比如，我们可以用 change，却不能用 changed 或 changes，而且这个动词的第一个字母必须是小写。通过这个动词，我们可以明确地知道 commit 所执行的操作。此外我们还要**注意**，subject 的结尾不能加英文句号。

#### Body
Header 对 commit 做了高度概括，可以方便我们查看 Commit Message。那我们如何知道具体做了哪些变更呢？答案就是，可以通过 Body 部分，它是对本次 commit 的更详细描述，是可选的。Body 部分可以分成多行，而且格式也比较自由。不过，和 Header 里的一样，它也要以动词开头，使用现在时。此外，它还必须要包括修改的动机，以及和跟上一版本相比的改动点。我在下面给出了一个范例，你可以看看：

```
The body is mandatory for all commits except for those of scope "docs". When the body is required it must be at least 20 characters long.
```
#### Footer
Footer 部分不是必选的，可以根据需要来选择，主要用来说明本次 commit 导致的后果。在实际应用中，Footer 通常用来说明不兼容的改动和关闭的 Issue 列表，格式如下：

```
BREAKING CHANGE: <breaking change summary>
// 空行
<breaking change description + migration instructions>
// 空行
// 空行
Fixes #<issue number>
```
接下来，我给你详细说明下这两种情况：
- 不兼容的改动：如果当前代码跟上一个版本不兼容，需要在 Footer 部分，以 BREAKING CHANG: 开头，后面跟上不兼容改动的摘要。Footer 的其他部分需要说明变动的描述、变动的理由和迁移方法，例如：

```
BREAKING CHANGE: isolate scope bindings definition has changed and
    the inject option for the directive controller injection was removed.

    To migrate the code follow the example below:

    Before:

    scope: {
      myAttr: 'attribute',
    }

    After:

    scope: {
      myAttr: '@',
    }
    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```
- 关闭的 Issue 列表：关闭的 Bug 需要在 Footer 部分新建一行，并以 Closes 开头列出，例如：Closes #123。如果关闭了多个 Issue，可以这样列出：Closes #123, #432, #886。例如:

```
Change pause version value to a constant for image
    
    Closes #1137
```
#### Revert Commit
除了 Header、Body 和 Footer 这 3 个部分，Commit Message 还有一种特殊情况：如果当前 commit 还原了先前的 commit，则应以 revert: 开头，后跟还原的 commit 的 Header。而且，在 Body 中必须写成 This reverts commit ，其中 hash 是要还原的 commit 的 SHA 标识。例如：

```
revert: feat(iam-apiserver): add 'Host' option

This reverts commit 079360c7cfc830ea8a6e13f4c8b8114febc9b48a.
```
为了更好地遵循 Angular 规范，****建议**你在提交代码时养成不用 git commit -m，即不用 -m 选项的习惯，而是直接用 git commit 或者 git commit -a 进入交互界面编辑 Commit Message。这样可以更好地格式化 Commit Message**。但是除了 Commit Message 规范之外，在代码提交时，我们还需要关注 3 个重点内容：提交频率、合并提交和 Commit Message 修改。

#### Commit 相关的 3 个重要内容
我们先来看下提交频率。

提交频率
在实际项目开发中，如果是个人项目，随意 commit 可能影响不大，但如果是多人开发的项目，随意 commit 不仅会让 Commit Message 变得难以理解，还会让其他研发同事觉得你不专业。因此，我们要规定 commit 的提交频率。那到底什么时候进行 commit 最好呢？

我认为主要可以分成两种情况。一种情况是，只要我对项目进行了修改，一通过测试就立即 commit。比如修复完一个 bug、开发完一个小功能，或者开发完一个完整的功能，测试通过后就提交。另一种情况是，我们规定一个时间，定期提交。这里我**建议**代码下班前固定提交一次，并且要确保本地未提交的代码，延期不超过 1 天。这样，如果本地代码丢失，可以尽可能减少丢失的代码量。

按照上面 2 种方式提交代码，你可能会觉得代码 commit 比较多，看起来比较随意。或者说，我们想等开发完一个完整的功能之后，放在一个 commit 中一起提交。这时候，我们可以在最后合并代码或者提交 Pull Request 前，执行 git rebase -i 合并之前的所有 commit。那么如何合并 commit 呢？接下来，我来详细说说。

合并提交
合并提交，就是将多个 commit 合并为一个 commit 提交。这里，我**建议**你把新的 commit 合并到主干时，只保留 2~3 个 commit 记录。那具体怎么做呢？在 Git 中，我们主要使用 git rebase 命令来合并。git rebase 也是我们日后开发需要经常使用的一个命令，所以我们一定要掌握好它的使用方法。

git rebase 命令介绍git rebase 的最大作用是它可以重写历史。我们通常会通过 git rebase -i 使用 git rebase 命令，-i 参数表示交互（interactive），该命令会进入到一个交互界面中，其实就是 Vim 编辑器。在该界面中，我们可以对里面的 commit 做一些操作，交互界面如图所示：

![img](https://static001.geekbang.org/resource/image/c6/ac/c63a8682c03862802e5eacf1641b86ac.png?wh=1345*866)

这个交互界面会首先列出给定之前（不包括，越下面越新）的所有 commit，每个 commit 前面有一个操作命令，默认是 pick。我们可以选择不同的 commit，并修改 commit 前面的命令，来对该 commit 执行不同的变更操作。

git rebase 支持的变更操作如下：

![img](https://static001.geekbang.org/resource/image/5f/f2/5f5a79a5d2bde029d4de9d98026ef3f2.png?wh=629*393)

在上面的 7 个命令中，squash 和 fixup 可以用来合并 commit。例如用 squash 来合并，我们只需要把要合并的 commit 前面的动词，改成 squash（或者 s）即可。你可以看看下面的示例：

```
pick 07c5abd Introduce OpenPGP and teach basic usage
s de9b1eb Fix PostChecker::Post#urls
s 3e7ee36 Hey kids, stop all the highlighting
pick fa20af3 git interactive rebase, squash, amend
```

rebase 后，第 2 行和第 3 行的 commit 都会合并到第 1 行的 commit。这个时候，我们提交的信息会同时包含这三个 commit 的提交信息：

```
# This is a combination of 3 commits.
# The first commit's message is:
Introduce OpenPGP and teach basic usage

# This is the 2ndCommit Message:
Fix PostChecker::Post#urls

# This is the 3rdCommit Message:
Hey kids, stop all the highlighting
```

如果我们将第 3 行的 squash 命令改成 fixup 命令：

```
pick 07c5abd Introduce OpenPGP and teach basic usage
s de9b1eb Fix PostChecker::Post#urls
f 3e7ee36 Hey kids, stop all the highlighting
pick fa20af3 git interactive rebase, squash, amend
```
rebase 后，还是会生成两个 commit，第 2 行和第 3 行的 commit，都合并到第 1 行的 commit。但是，新的提交信息里面，第 3 行 commit 的提交信息会被注释掉：

```
# This is a combination of 3 commits.
# The first commit's message is:
Introduce OpenPGP and teach basic usage

# This is the 2ndCommit Message:
Fix PostChecker::Post#urls

# This is the 3rdCommit Message:
# Hey kids, stop all the highlighting
```
除此之外，我们在使用 git rebase 进行操作的时候，还需要**注意**以下几点：删除某个 commit 行，则该 commit 会丢失掉。删除所有的 commit 行，则 rebase 会被终止掉。可以对 commits 进行排序，git 会从上到下进行合并。

**为了加深你的理解，我给你完整演示一遍合并提交。**
合并提交操作示例
假设我们需要研发一个新的模块：user，用来在平台里进行用户的注册、登录、注销等操作，当模块完成开发和测试后，需要合并到主干分支，具体步骤如下。

首先，我们新建一个分支。我们需要先基于 master 分支新建并切换到 feature 分支：

```
$ git checkout -b feature/user
Switched to a new branch 'feature/user'
```
这是我们的所有 commit 历史：

```
$ git log --oneline
7157e9e docs(docs): append test line 'update3' to README.md
5a26aa2 docs(docs): append test line 'update2' to README.md
55892fa docs(docs): append test line 'update1' to README.md
89651d4 docs(doc): add README.md
```
接着，我们在 feature/user分支进行功能的开发和测试，并遵循规范提交 commit，功能开发并测试完成后，Git 仓库的 commit 记录如下：

```
$ git log --oneline
4ee51d6 docs(user): update user/README.md
176ba5d docs(user): update user/README.md
5e829f8 docs(user): add README.md for user
f40929f feat(user): add delete user function
fc70a21 feat(user): add create user function
7157e9e docs(docs): append test line 'update3' to README.md
5a26aa2 docs(docs): append test line 'update2' to README.md
55892fa docs(docs): append test line 'update1' to README.md
89651d4 docs(doc): add README.md
```
可以看到我们提交了 5 个 commit。接下来，我们需要将 feature/user分支的改动合并到 master 分支，但是 5 个 commit 太多了，我们想将这些 commit 合并后再提交到 master 分支。接着，我们合并所有 commit。在上一步中，我们知道 fc70a21是 feature/user分支的第一个 commit ID，其父 commit ID 是 7157e9e，我们需要将7157e9e之前的所有分支 进行合并，这时我们可以执行：

```
$ git rebase -i 7157e9e
```

执行命令后，我们会进入到一个交互界面，在该界面中，我们可以将需要合并的 4 个 commit，都执行 squash 操作，如下图所示：

![img](https://static001.geekbang.org/resource/image/6e/4e/6e41e61c27ca2a46e55e8801c47cd04e.png?wh=1097*320)
修改完成后执行:wq 保存，会跳转到一个新的交互页面，在该页面，我们可以编辑 Commit Message，编辑后的内容如下图所示：
![img](https://static001.geekbang.org/resource/image/73/87/73a884bac481236969ba2a219a2e9187.png?wh=1184*399)

#开头的行是 git 的注释，我们可以忽略掉，在 rebase 后，这些行将会消失掉。修改完成后执行:wq 保存，就完成了合并提交操作。除此之外，这里有 2 个点需要我们**注意**：
- git rebase -i 这里的一定要是需要合并 commit 中最旧 commit 的父 commit ID。
- 我们希望将 feature/user 分支的 5 个 commit 合并到一个 commit，在 git rebase 时，需要保证其中最新的一个 commit 是 pick 状态，这样我们才可以将其他 4 个 commit 合并进去。

然后，我们用如下命令来检查 commits 是否成功合并。可以看到，我们成功将 5 个 commit 合并成为了一个 commit：d6b17e0。

```
$ git log --oneline
d6b17e0 feat(user): add user module with all function implements
7157e9e docs(docs): append test line 'update3' to README.md
5a26aa2 docs(docs): append test line 'update2' to README.md
55892fa docs(docs): append test line 'update1' to README.md
89651d4 docs(doc): add README.md
```

最后，我们就可以将 feature 分支 feature/user 的改动合并到主干分支，从而完成新功能的开发。

```
$ git checkout master
$ git merge feature/user
$ git log --oneline
d6b17e0 feat(user): add user module with all function implements
7157e9e docs(docs): append test line 'update3' to README.md
5a26aa2 docs(docs): append test line 'update2' to README.md
55892fa docs(docs): append test line 'update1' to README.md
89651d4 docs(doc): add README.md
```
这里给你一个小提示，如果你有太多的 commit 需要合并，那么可以试试这种方式：先撤销过去的 commit，然后再建一个新的。

```
$ git reset HEAD~3
$ git add .
$ git commit -am "feat(user): add user resource"
```
需要说明一点：除了 commit 实在太多的时候，一般情况下我不**建议**用这种方法，有点粗暴，而且之前提交的 Commit Message 都要重新整理一遍。


#### 修改 Commit Message
即使我们有了 Commit Message 规范，但仍然可能会遇到提交的 Commit Message 不符合规范的情况，这个时候就需要我们能够修改之前某次 commit 的 Commit Message。

具体来说，我们有两种修改方法，分别对应两种不同情况：git commit --amend：修改最近一次 commit 的 message；git rebase -i：修改某次 commit 的 message。

接下来，我们分别来说这两种方法。

**git commit --amend：修改最近一次 commit 的 message**有时候，我们刚提交完一个 commit，但是发现 commit 的描述不符合规范或者需要纠正，这时候，我们可以通过 git commit --amend 命令来修改刚刚提交 commit 的 Commit Message。具体修改步骤如下：

查看当前分支的日志记录。

```
$ git log –oneline
418bd4 docs(docs): append test line 'update$i' to README.md
89651d4 docs(doc): add README.md
```
可以看到，最近一次的 Commit Message 是 docs(docs): append test line 'update$i' to README.md，其中 update$i 正常应该是 update1。

更新最近一次提交的 Commit Message
在当前 Git 仓库下执行命令：git commit --amend，后会进入一个交互界面，在交互界面中，修改最近一次的 Commit Message，如下图所示：

![img](https://static001.geekbang.org/resource/image/9e/a3/9ea39c153fccdbfd951a990e131ddea3.png?wh=1362*466)

修改完成后执行:wq 保存，退出编辑器之后，会在命令行显示，该 commit 的 message 的更新结果如下：

```
[master 55892fa] docs(docs): append test line 'update1' to README.md
 Date: Fri Sep 18 13:40:42 2020 +0800
 1 file changed, 1 insertion(+)
```
查看最近一次的 Commit Message 是否被更新

```
$ git log --oneline
55892fa docs(docs): append test line 'update1' to README.md
89651d4 docs(doc): add README.md
```
可以看到最近一次 commit 的 message 成功被修改为期望的内容。

**git rebase -i：修改某次 commit 的 message**如果我们想修改的 Commit Message 不是最近一次的 Commit Message，可以通过 git rebase -i <父 commit ID>命令来修改。这个命令在实际开发中使用频率比较高，我们一定要掌握。具体来说，使用它主要分为 4 步。

查看当前分支的日志记录。

```
$ git log --oneline
1d6289f docs(docs): append test line 'update3' to README.md
a38f808 docs(docs): append test line 'update$i' to README.md
55892fa docs(docs): append test line 'update1' to README.md
89651d4 docs(doc): add README.md
```
可以看到倒数第 3 次提交的 Commit Message 是：docs(docs): append test line 'update$i' to README.md，其中 update$i 正常应该是 update2。

修改倒数第 3 次提交 commit 的 message。在 Git 仓库下直接执行命令 git rebase -i 55892fa，然后会进入一个交互界面。在交互界面中，修改最近一次的 Commit Message。这里我们使用 reword 或者 r，保留倒数第 3 次的变更信息，但是修改其 message，如下图所示：

![img](https://static001.geekbang.org/resource/image/9e/b9/9e8344df0dd8f66f63a307b5a6487fb9.png?wh=1319*273)

修改完成后执行:wq 保存，还会跳转到一个新的交互页面，如下图所示：


![img](https://static001.geekbang.org/resource/image/0e/24/0ef693d6fc195ebd179b7992e0776f24.png?wh=1392*359)

修改完成后执行:wq 保存，退出编辑器之后，会在命令行显示该 commit 的 message 的更新结果：

```
[detached HEAD 5a26aa2] docs(docs): append test line 'update2' to README.md
 Date: Fri Sep 18 13:45:54 2020 +0800
 1 file changed, 1 insertion(+)
Successfully rebased and updated refs/heads/master.
```
Successfully rebased and updated refs/heads/master.说明 rebase 成功，其实这里完成了两个步骤：更新 message，更新该 commit 的 HEAD 指针。**注意**：这里一定要传入想要变更 Commit Message 的父 commit ID：git rebase -i <父 commit ID>。

查看倒数第 3 次 commit 的 message 是否被更新。

```
$ git log --oneline
7157e9e docs(docs): append test line 'update3' to README.md
5a26aa2 docs(docs): append test line 'update2' to README.md
55892fa docs(docs): append test line 'update1' to README.md
89651d4 docs(doc): add README.md
```
可以看到，倒数第 3 次 commit 的 message 成功被修改为期望的内容。这里有两点需要你**注意**：
- Commit Message 是 commit 数据结构中的一个属性，如果 Commit Message 有变更，则 commit ID 一定会变，git commit --amend 只会变更最近一次的 commit ID，但是 git rebase -i 会变更父 commit ID 之后所有提交的 commit ID。
- 如果当前分支有未 commit 的代码，需要先执行 git stash 将工作状态进行暂存，当修改完成后再执行 git stash pop 恢复之前的工作状态。

#### Commit Message 规范自动化
其实，到这里我们也就意识到了一点：Commit Message 规范如果靠文档去约束，就会严重依赖开发者的代码素养，并不能真正保证提交的 commit 是符合规范的。那么，有没有一种方式可以确保我们提交的 Commit Message 一定是符合规范的呢？有的，我们可以通过一些工具，来自动化地生成和检查 Commit Message 是否符合规范。另外，既然 Commit Message 是规范的，那么我们能不能利用这些规范来实现一些更酷的功能呢？答案是有的，我将可以围绕着 Commit Message 实现的一些自动化功能绘制成了下面一张图。

![img](https://static001.geekbang.org/resource/image/87/be/87cd05c48ac90ec93c379b568a6006be.png?wh=2141*1219)

这些自动化功能可以分为以下 2 类：Commit Message 生成和检查功能：生成符合 Angular 规范的 Commit Message、Commit Message 提交前检查、历史 Commit Message 检查。基于 Commit Message 自动生成 CHANGELOG 和 SemVer 的工具。

我们可以通过下面这 5 个工具自动的完成上面的功能：commitizen-go：使你进入交互模式，并根据提示生成 Commit Message，然后提交。commit-msg：githooks，在 commit-msg 中，指定检查的规则，commit-msg 是个脚本，可以根据需要自己写脚本实现。这门课的 commit-msg 调用了 go-gitlint 来进行检查。go-gitlint：检查历史提交的 Commit Message 是否符合 Angular 规范，可以将该工具添加在 CI 流程中，确保 Commit Message 都是符合规范的。gsemver：语义化版本自动生成工具。git-chglog：根据 Commit Message 生成 CHANGELOG。

这些工具你先有个印象就好了，在后面的课程内容中，我会带你通过实际使用来熟悉它们的用法。

### 总结
今天我向你介绍了 Commit Message 规范，主要讲了业界使用最多的 Angular 规范。Angular 规范中，Commit Message 包含三个部分：Header、Body 和 Footer。Header 对 commit 做了高度概括，Body 部分是对本次 commit 的更详细描述，Footer 部分主要用来说明本次 commit 导致的后果。格式如下：
```
<type>[optional scope]: <description>
// 空行
[optional body]
// 空行
[optional footer(s)]
```
另外，我们也需要控制 commit 的提交频率，比如可以在开发完一个功能、修复完一个 bug、下班前提交 commit。最后，我们也需要掌握一些常见的提交操作，例如通过 git rebase -i 来合并提交 commit，通过 git commit --amend 或 git rebase -i 来修改 commit message。
## 6 | 目录结构设计：如何组织一个可维护、可扩展的代码目录？
那具体怎么组织一个好的代码目录呢？在今天这一讲，我会从 2 个维度来解答这个问题。首先，我会介绍组织目录的一些基本原则，这些原则可以指导你去组织一个好的代码目录。然后，我会向你介绍一些具体的、优秀的目录结构。你可以通过学习它们，提炼总结出你自己的目录结构设计方法，或者你也可以直接用它们作为你的目录结构规范，也就是说结构即规范。

一个好的目录结构至少要满足以下几个要求。
- 命名清晰：目录命名要清晰、简洁，不要太长，也不要太短，目录名要能清晰地表达出该目录实现的功能，并且目录名最好用单数。一方面是因为单数足以说明这个目录的功能，另一方面可以统一规范，避免单复混用的情况。
- 功能明确：一个目录所要实现的功能应该是明确的、并且在整个项目目录中具有很高的辨识度。也就是说，当需要新增一个功能时，我们能够非常清楚地知道把这个功能放在哪个目录下。
- 全面性：目录结构应该尽可能全面地包含研发过程中需要的功能，例如文档、脚本、源码管理、API 实现、工具、第三方包、测试、编译产物等。
- 可预测性：项目规模一定是从小到大的，所以一个好的目录结构应该能够在项目变大时，仍然保持之前的目录结构。
- 可扩展性：每个目录下存放了同类的功能，在项目变大时，这些目录应该可以存放更多同类功能。举个例子，有如下目录结构：

```
$ ls internal/
app  pkg  README.md
```
internal 目录用来实现内部代码，app 和 pkg 目录下的所有文件都属于内部代码。如果 internal 目录不管项目大小，永远只有 2 个文件 app 和 pkg，那么就说明 internal 目录是不可扩展的。相反，如果 internal 目录下直接存放每个组件的源码目录（一个项目可以由一个或多个组件组成），当项目变大、组件增多时，可以将新增加的组件代码存放到 internal 目录，这时 internal 目录就是可扩展的。例如：

```
$ ls internal/
apiserver  authzserver  iamctl  pkg  pump  watcher
```
刚才我讲了目录结构的总体规范，现在来看 2 个具体的、可以作为目录规范的目录结构。通常，根据功能，我们可以将目录结构分为**结构化目录结构和平铺式目录结构两种**。结构化目录结构主要用在 Go 应用中，相对来说比较复杂；而平铺式目录结构主要用在 Go 包中，相对来说比较简单。


### 平铺式目录结构
一个 Go 项目可以是一个应用，也可以是一个代码框架 / 库，当项目是代码框架 / 库时，比较适合采用平铺式目录结构。平铺方式就是在项目的根目录下存放项目的代码，整个目录结构看起来更像是一层的，这种方式在很多框架 / 库中存在，使用这种方式的好处是引用路径长度明显减少，比如 github.com/marmotedu/log/pkg/options，可缩短为 github.com/marmotedu/log/options。例如 log 包 github.com/golang/glog 就是平铺式的，目录如下：

```
$ ls glog/
glog_file.go  glog.go  glog_test.go  LICENSE  README
```
### 结构化目录结构
当前 Go 社区比较**推荐**的结构化目录结构是 project-layout 。虽然它并不是官方和社区的规范，但因为组织方式比较合理，被很多 Go 开发人员接受。所以，我们可以把它当作是一个事实上的规范。

首先，我们来看下在开发一个 Go 项目时，通常应该包含的功能。这些功能内容比较多，我放在了 GitHub 的 Go 项目通常包含的功能 里，我们设计的目录结构应该能够包含这些功能。我结合 project-layout，以及上面列出的 Go 项目常见功能，总结出了一套 Go 的代码结构组织方式，也就是 IAM 项目使用的目录结构。这种方式保留了 project-layout 优势的同时，还加入了一些我个人的理解，希望为你提供一个拿来即用的目录结构规范。接下来，我们一起看看这门课的实战项目所采用的 Go 目录结构。因为实战项目目录比较多，这里只列出了一些重要的目录和文件，你可以快速浏览以加深理解。

```
├── api
│   ├── openapi
│   └── swagger
├── build
│   ├── ci
│   ├── docker
│   │   ├── iam-apiserver
│   │   ├── iam-authz-server
│   │   └── iam-pump
│   ├── package
├── CHANGELOG
├── cmd
│   ├── iam-apiserver
│   │   └── apiserver.go
│   ├── iam-authz-server
│   │   └── authzserver.go
│   ├── iamctl
│   │   └── iamctl.go
│   └── iam-pump
│       └── pump.go
├── configs
├── CONTRIBUTING.md
├── deployments
├── docs
│   ├── devel
│   │   ├── en-US
│   │   └── zh-CN
│   ├── guide
│   │   ├── en-US
│   │   └── zh-CN
│   ├── images
│   └── README.md
├── examples
├── githooks
├── go.mod
├── go.sum
├── init
├── internal
│   ├── apiserver
│   │   ├── api
│   │   │   └── v1
│   │   │       └── user
│   │   ├── apiserver.go
│   │   ├── options
│   │   ├── service
│   │   ├── store
│   │   │   ├── mysql
│   │   │   ├── fake
│   │   └── testing
│   ├── authzserver
│   │   ├── api
│   │   │   └── v1
│   │   │       └── authorize
│   │   ├── options
│   │   ├── store
│   │   └── testing
│   ├── iamctl
│   │   ├── cmd
│   │   │   ├── completion
│   │   │   ├── user
│   │   └── util
│   ├── pkg
│   │   ├── code
│   │   ├── options
│   │   ├── server
│   │   ├── util
│   │   └── validation
├── LICENSE
├── Makefile
├── _output
│   ├── platforms
│   │   └── linux
│   │       └── amd64
├── pkg
│   ├── util
│   │   └── genutil
├── README.md
├── scripts
│   ├── lib
│   ├── make-rules
├── test
│   ├── testdata
├── third_party
│   └── forked
└── tools
```
看到这一长串目录是不是有些晕？没关系，这里我们一起给这个大目录分下类，然后再具体看看每一类目录的作用，你就清楚了。在我看来，一个 Go 项目包含 3 大部分：Go 应用 、项目管理和文档。所以，我们的项目目录也可以分为这 3 大类。同时，Go 应用又贯穿开发阶段、测试阶段和部署阶段，相应的应用类的目录，又可以按开发流程分为更小的子类。当然了，这些是我**建议**的目录，Go 项目目录中还有一些不**建议**的目录。所以整体来看，我们的目录结构可以按下图所示的方式来分类：

![img](https://static001.geekbang.org/resource/image/94/a3/94e521c6eb884096ea107fc4c36f30a3.png?wh=2248x1832)

接下来你就先专心跟着我走一遍每个目录、每个文件的作用，等你下次组织代码目录的时候，可以再回过头来看看，那时你一定会理解得更深刻。

### Go 应用 ：主要存放前后端代码
首先，我们来说说开发阶段所涉及到的目录。我们开发的代码包含前端代码和后端代码，可以分别存放在前端目录和后端目录中。



/web前端代码存放目录，主要用来存放 Web 静态资源，服务端模板和单页应用（SPAs）。/cmd**一个项目有很多组件，可以把组件 main 函数所在的文件夹统一放在/cmd 目录下**，例如：

```
$ ls cmd/
gendocs  geniamdocs  genman  genswaggertypedocs  genyaml  iam-apiserver  iam-authz-server  iamctl  iam-pump

$ ls cmd/iam-apiserver/
apiserver.go
```
每个组件的目录名应该跟你期望的可执行文件名是一致的。这里要保证 /cmd/<组件名> 目录下不要存放太多的代码，**如果你认为代码可以导入并在其他项目中使用，那么它应该位于 /pkg 目录中**。**如果代码不是可重用的，或者你不希望其他人重用它，请将该代码放到 /internal 目录中。**

**/internal存放私有应用和库代码**。如果一些代码，你不希望在其他应用和库中被导入，可以将这部分代码放在/internal 目录下。在引入其它项目 internal 下的包时，Go 语言会在编译时报错：

```
An import of a path containing the element “internal” is disallowed
if the importing code is outside the tree rooted at the parent of the
"internal" directory.
```
可以通过 Go 语言本身的机制来约束其他项目 import 项目内部的包。/internal 目录**建议**包含如下目录：
- /internal/apiserver：该目录中存放真实的应用代码。这些应用的共享代码存放在/internal/pkg 目录下。
- /internal/pkg：**存放项目内可共享，项目外不共享的包。这些包提供了比较基础、通用的功能，例如工具、错误码、用户验证等功能。**

**我的**建议**是，一开始将所有的共享代码存放在 /internal/pkg 目录下，当该共享代码做好了对外开发的准备后，再转存到/pkg目录下**。下面，我详细介绍下 IAM 项目的 internal目录 ，来加深你对 internal 的理解，目录结构如下：

```
├── apiserver
│   ├── api
│   │   └── v1
│   │       └── user
│   ├── options
│   ├── config
│   ├── service
│   │   └── user.go
│   ├── store
│   │   ├── mysql
│   │   │   └── user.go
│   │   ├── fake
│   └── testing
├── authzserver
│   ├── api
│   │   └── v1
│   ├── options
│   ├── store
│   └── testing
├── iamctl
│   ├── cmd
│   │   ├── cmd.go
│   │   ├── info
└── pkg
    ├── code
    ├── middleware
    ├── options
    └── validation
```
/internal 目录大概分为 3 类子目录：
- /internal/pkg：**内部共享包**存放的目录。
- /internal/authzserver、/internal/apiserver、/internal/pump、/internal/iamctl：应用目录，里面包含**应用程序的实现代码**。
- /internal/iamctl：对于一些大型项目，可能还会需要一个**客户端工具**。

在每个应用程序内部，也会有一些目录结构，这些目录结构主要**根据功能来划分**：
- /internal/apiserver/api/v1：HTTP API 接口的具体实现，主要用来做 HTTP 请求的解包、参数校验、业务逻辑处理、返回。**注意**这里的业务逻辑处理应该是轻量级的，如果业务逻辑比较复杂，代码量比较多，**建议**放到 /internal/apiserver/service 目录下。该源码文件主要用来串流程。
- /internal/apiserver/options：应用的 command flag。
- /internal/apiserver/config：根据命令行参数创建应用配置。
- /internal/apiserver/service：存放应用复杂业务处理代码。
- /internal/apiserver/store/mysql：一个应用可能要持久化的存储一些数据，这里主要存放跟数据库交互的代码，比如 Create、Update、Delete、Get、List 等。

/internal/pkg 目录存放项目内可共享的包，通常可以包含如下目录：
- /internal/pkg/code：项目业务 Code 码。
- /internal/pkg/validation：一些通用的验证函数。
- /internal/pkg/middleware：HTTP 处理链。



/pkg 

/pkg 目录是 Go 语言项目中非常常见的目录，我们几乎能够在所有知名的开源项目（非框架）中找到它的身影，例如 Kubernetes、Prometheus、Moby、Knative 等。该目录中存放可以被外部应用使用的代码库，其他项目可以直接通过 import 导入这里的代码。所以，我们在将代码库放入该目录时一定要慎重。

/vendor项目依赖，可通过 go mod vendor 创建。需要**注意**的是，**如果是一个 Go 库，不要提交 vendor 依赖包。**

/third_party外部帮助工具，分支代码或其他第三方应用（例如 Swagger UI）。**比如我们 fork 了一个第三方 go 包，并做了一些小的改动，我们可以放在目录 /third_party/forked 下**。一方面可以很清楚的知道该包是 fork 第三方的，另一方面又能够方便地和 upstream 同步。

### Go 应用：主要存放测试相关的文件和代码
接着，我们再来看下测试阶段相关的目录，它可以存放测试相关的文件。

**/test用于存放其他外部测试应用和测试数据**。/test 目录的构建方式比较灵活：对于大的项目，有一个数据子目录是有意义的。例如，如果需要 Go 忽略该目录中的内容，可以使用 /test/data 或 /test/testdata 目录。需要**注意**的是，Go 也会忽略以“.”或 “_” 开头的目录或文件。这样在命名测试数据目录方面，可以具有更大的灵活性。

### Go 应用：存放跟应用部署相关的文件

接着，我们再来看下与部署阶段相关的目录，这些目录可以存放部署相关的文件。

/configs这个目录用来配置文件模板或默认配置。例如，可以在这里存放 confd 或 consul-template 模板文件。这里有一点要**注意**，配置中不能携带敏感信息，这些敏感信息，我们可以用占位符来替代，例如：

```
apiVersion: v1    
user:    
  username: ${CONFIG_USER_USERNAME} # iam 用户名    
  password: ${CONFIG_USER_PASSWORD} # iam 密码
```

/deployments用来存放 Iaas、PaaS 系统和容器编排部署配置和模板（Docker-Compose，Kubernetes/Helm，Mesos，Terraform，Bosh）。在一些项目，特别是用 Kubernetes 部署的项目中，这个目录可能命名为 deploy。为什么要将这类跟 Kubernetes 相关的目录放到目录结构中呢？主要是**因为当前软件部署基本都在朝着容器化的部署方式去演进**。

/init存放初始化系统（systemd，upstart，sysv）和进程管理配置文件（runit，supervisord）。比如 sysemd 的 unit 文件。这类文件，在非容器化部署的项目中会用到。

### 项目管理：存放用来管理 Go 项目的各类文件
在做项目开发时，还有些目录用来存放项目管理相关的文件，这里我们一起来看下。

/Makefile虽然 Makefile 是一个很老的项目管理工具，但它仍然是最优秀的项目管理工具。所以，一个 Go 项目在其根目录下应该有一个 Makefile 工具，用来对项目进行管理，**Makefile 通常用来执行静态代码检查、单元测试、编译**等功能。其他常见功能：
- 静态代码检查(lint)：**推荐**用 golangci-lint。
- 单元测试(test)：运行 go test ./...。
- 编译(build)：编译源码，支持不同的平台，不同的 CPU 架构。
- 镜像打包和发布(image/image.push)：现在的系统比较**推荐**用 Docker/Kubernetes 进行部署，所以一般也要有镜像构建功能。
- 清理（clean）:清理临时文件或者编译后的产物。
- 代码生成（gen）：比如要编译生成 protobuf pb.go 文件。
- 部署（deploy，可选）：一键部署功能，方便测试。
- 发布（release）：发布功能，比如：发布到 Docker Hub、github 等。
- 帮助（help）:告诉 Makefile 有哪些功能，如何执行这些功能。
- 版权声明（add-copyright）：如果是开源项目，可能需要在每个文件中添加版权头，这可以通过 Makefile 来添加。
- API 文档（swagger）：如果使用 swagger 来生成 API 文档，这可以通过 Makefile 来生成。

我还有一条**建议**：直接执行 make 时，执行如下各项 format -> lint -> test -> build，如果是有代码生成的操作，还可能需要首先生成代码 gen -> format -> lint -> test -> build。**在实际开发中，我们可以将一些重复性的工作自动化，并添加到 Makefile 文件中统一管理**。

/scripts该目录主要用来存放脚本文件，实现**构建、安装、分析**等不同功能。不同项目，里面可能存放不同的文件，但通常可以考虑包含以下 3 个目录：
- /scripts/make-rules：用来存放 makefile 文件，实现 /Makefile 文件中的各个功能。Makefile 有很多功能，为了保持它的简洁，我**建议**你将各个功能的具体实现放在/scripts/make-rules 文件夹下。
- /scripts/lib：shell 库，用来存放 shell 脚本。一个大型项目中有很多自动化任务，比如发布、更新文档、生成代码等，所以要写很多 shell 脚本，这些 shell 脚本会有一些通用功能，可以抽象成库，存放在/scripts/lib 目录下，比如 logging.sh，util.sh 等。
- /scripts/install：如果项目支持自动化部署，可以将自动化部署脚本放在此目录下。如果部署脚本简单，也可以直接放在 /scripts 目录下。

另外，shell 脚本中的函数名，**建议**采用语义化的命名方式，例如 iam::log::info 这种语义化的命名方式，可以使调用者轻松的辨别出函数的功能类别，便于函数的管理和引用。在 Kubernetes 的脚本中，就大量采用了这种命名方式。

/build这里存放安装包和持续集成相关的文件。这个目录下有 3 个大概率会使用到的目录，在设计目录结构时可以考虑进去。
- /build/package：存放容器（Docker）、系统（deb, rpm, pkg）的包配置和脚本。
- /build/ci：存放 CI（travis，circle，drone）的配置文件和脚本。
- /build/docker：存放子项目各个组件的 Dockerfile 文件。

/tools存放这个项目的支持工具。这些工具可导入来自 /pkg 和 /internal 目录的代码。

/githooksGit 钩子。比如，我们可以将 commit-msg 存放在该目录。

/assets项目使用的其他资源 (图片、CSS、JavaScript 等)。

/website如果你不使用 GitHub 页面，那么可以在这里放置项目网站相关的数据。

### 文档：主要存放项目的各类文档
一个项目，也包含一些文档，这些文档有很多类别，也需要一些目录来存放这些文档，这里我们也一起来看下。

/README.md项目的 README 文件一般包含了项目的介绍、功能、快速安装和使用指引、详细的文档链接以及开发指引等。有时候 README 文档会比较长，为了能够快速定位到所需内容，需要添加 markdown toc 索引，可以借助工具 tocenize 来完成索引的添加。这里还有个**建议**，前面我们也介绍过 README 是可以规范化的，所以这个 README 文档，可以通过脚本或工具来自动生成。

/docs存放设计文档、开发文档和用户文档等（除了 godoc 生成的文档）。**推荐**存放以下几个子目录：
- /docs/devel/{en-US,zh-CN}：存放开发文档、hack 文档等。
- /docs/guide/{en-US,zh-CN}: 存放用户手册，安装、quickstart、产品文档等，分为中文文档和英文文档。
- /docs/images：存放图片文件。

/CONTRIBUTING.md如果是一个开源就绪的项目，最好还要有一个 CONTRIBUTING.md 文件，用来说明如何贡献代码，如何开源协同等等。CONTRIBUTING.md 不仅能够规范协同流程，还能降低第三方开发者贡献代码的难度。

/api    /api 目录中存放的是当前项目对外提供的各种不同类型的 API 接口定义文件，其中可能包含类似 /api/protobuf-spec、/api/thrift-spec、/api/http-spec、openapi、swagger 的目录，这些目录包含了当前项目对外提供和依赖的所有 API 文件。例如，如下是 IAM 项目的 /api 目录：

```
├── openapi/
│   └── README.md
└── swagger/
    ├── docs/
    ├── README.md
    └── swagger.yaml
```
二级目录的主要作用，就是在一个项目同时提供了多种不同的访问方式时，可以分类存放。用这种方式可以避免潜在的冲突，也能让项目结构更加清晰。

/LICENSE版权文件可以是私有的，也可以是开源的。常用的开源协议有：Apache 2.0、MIT、BSD、GPL、Mozilla、LGPL。有时候，公有云产品为了打造品牌影响力，会对外发布一个本产品的开源版本，所以在项目规划初期最好就能规划下未来产品的走向，选择合适的 LICENSE。为了声明版权，你可能会需要将 LICENSE 头添加到源码文件或者其他文件中，这部分工作可以通过工具实现自动化，**推荐**工具： **addlicense** 。当代码中引用了其它开源代码时，需要在 LICENSE 中说明对其它源码的引用，这就需要知道代码引用了哪些源码，以及这些源码的开源协议，可以借助工具来进行检查，**推荐**工具： **glice** 。至于如何说明对其它源码的引用，大家可以参考下 IAM 项目的 LICENSE 文件。

/CHANGELOG当项目有更新时，为了方便了解当前版本的更新内容或者历史更新内容，需要将更新记录存放到 CHANGELOG 目录。编写 CHANGELOG 是一个复杂、繁琐的工作，我们可以**结合 Angular 规范 和 git-chglog 来自动生成 CHANGELOG。**

/examples存放应用程序或者公共包的示例代码。这些示例代码可以降低使用者的上手门槛。

### 不**建议**的目录
除了上面这些我们**建议**的目录，在 Go 项目中，还有一些目录是不**建议**包含的，这些目录不符合 Go 的设计哲学。

/src/一些开发语言，例如 Java 项目中会有 src 目录。在 Java 项目中， src 目录是一种常见的模式，但在 Go 项目中，不**建议**使用 src 目录。其中一个重要的原因是：在默认情况下，Go 语言的项目都会被放置到$GOPATH/src 目录下。这个目录中存放着所有代码，如果我们在自己的项目中使用/src 目录，这个包的导入路径中就会出现两个 src，例如：

```
$GOPATH/src/github.com/marmotedu/project/src/main.go
```
这样的目录结构看起来非常怪。

xxs/在 Go 项目中，要避免使用带复数的目录或者包。**建议**统一使用单数。

### 一些**建议**
上面介绍的目录结构包含很多目录，但一个小型项目用不到这么多目录。对于小型项目，可以考虑先包含 cmd、pkg、internal 3 个目录，其他目录后面按需创建，例如：

```
$ tree --noreport -L 2 tms
tms
├── cmd
├── internal
├── pkg
└── README.md
```
另外，在设计目录结构时，一些空目录无法提交到 Git 仓库中，但我们又想将这个空目录上传到 Git 仓库中，以保留目录结构。这时候，可以在空目录下加一个 .keep 文件，例如：

```
$ ls -A build/ci/ 
.keep
```
## 7 | 工作流设计：如何设计合理的多人开发模式？
一个企业级项目是由多人合作完成的，不同开发者在本地开发完代码之后，可能提交到同一个代码仓库，同一个开发者也可能同时开发几个功能特性。这种多人合作开发、多功能并行开发的特性如果处理不好，就会带来诸如丢失代码、合错代码、代码冲突等问题。所以，在编码之前，我们需要设计一个合理的开发模式。又因为目前开发者基本都是基于 Git 进行开发的，所以本节课，我会教你怎么基于 Git 设计出一个合理的开发模式。

那么如何设计工作流呢？你可以根据需要，自己设计工作流，也可以采用业界沉淀下来的、设计好的、受欢迎的工作流。一方面，这些工作流经过长时间的实践，被证明是合理的；另一方面，采用一种被大家熟知且业界通用的工作流，会减少团队内部磨合的时间。在这一讲中，我会为你介绍 4 种受欢迎的工作流，你可以选择其中一种作为你的工作流设计。

在使用 Git 开发时，有 4 种常用的工作流，也叫开发模式，按演进顺序分为集中式工作流、功能分支工作流、Git Flow 工作流和 Forking 工作流。接下来，我会按演进顺序分别介绍这 4 种工作流。

### 集中式工作流
我们先来看看集中式工作流，它是最简单的一种开发方式。集中式工作流的工作模式如下图所示：![img](https://static001.geekbang.org/resource/image/31/eb/3174a9e1373ed2d6d14471164dcb13eb.png?wh=2100*1144)


A、B、C 为 3 位开发者，每位开发者都在本地有一份远程仓库的拷贝：本地仓库。A、B、C 在本地的 master 分支开发完代码之后，将修改后的代码 commit 到远程仓库，如果有冲突就先解决本地的冲突再提交。在进行了一段时间的开发之后，远程仓库 master 分支的日志可能如下图所示：

![img](https://static001.geekbang.org/resource/image/fb/c7/fbcc75ba5b91223f6bf243f0bc08bac7.png?wh=2922*391)

集中式工作流是最简单的开发模式，但它的缺点也很明显：不同开发人员的提交日志混杂在一起，难以定位问题。如果同时开发多个功能，不同功能同时往 master 分支合并，代码之间也会相互影响，从而产生代码冲突。

和其他工作流相比，集中式工作流程的代码管理较混乱，容易出问题，因此适合用在团队人数少、开发不频繁、不需要同时维护多个版本的小项目中。当我们想要并行开发多个功能时，这种工作流就不适用了，这时候怎么办呢？我们接下来看功能分支工作流。

### 功能分支工作流
功能分支工作流基于集中式工作流演进而来。在开发新功能时，基于 master 分支新建一个功能分支，在功能分支上进行开发，而不是直接在本地的 master 分支开发，开发完成之后合并到 master 分支，如下图所示：

![img](https://static001.geekbang.org/resource/image/1c/0b/1c0b08a1c9032c87c35b85de6ca6820b.png?wh=3809*834)
相较于集中式工作流，这种工作流让不同功能在不同的分支进行开发，只在最后一步合并到 master 分支，不仅可以避免不同功能之间的相互影响，还可以使提交历史看起来更加简洁。还有，在合并到 master 分支时，需要提交 PR（pull request），而不是直接将代码 merge 到 master 分支。PR 流程不仅可以把分支代码提供给团队其他开发人员进行 CR（Code Review），还可以在 PR 页面讨论代码。通过 CR ，我们可以确保合并到 master 的代码是健壮的；通过 PR 页面的讨论，可以使开发者充分参与到代码的讨论中，有助于提高代码的质量，并且提供了一个代码变更的历史回顾途径。

那么，功能分支工作流具体的开发流程是什么呢？我们一起来看下。

基于 master 分支新建一个功能分支，功能分支可以取一些有意义的名字，便于理解，例如 feature/rate-limiting。

```
$ git checkout -b feature/rate-limiting
```

在功能分支上进行代码开发，开发完成后 commit 到功能分支。

```
$ git add limit.go
$ git commit -m "add rate limiting"
```
将本地功能分支代码 push 到远程仓库。

```
$ git push origin feature/rate-limiting
```
在远程仓库上创建 PR（例如：GitHub）。进入 GitHub 平台上的项目主页，点击 Compare & pull request 提交 PR，如下图所示。

![img](https://static001.geekbang.org/resource/image/db/ac/dbcd25542515788c7f4f2f592d0029ac.png?wh=1700*552)
点击 Compare & pull request 后会进入 PR 页面，在该页面中可以根据需要填写评论，最后点击 Create pull request 提交 PR。

代码管理员收到 PR 后，可以 CR 代码，CR 通过后，再点击 Merge pull request 将 PR 合并到 master，如下图所示。


![img](https://static001.geekbang.org/resource/image/48/c6/48aaa3a94108de765cb07bd34d899fc6.png?wh=1673*1107)


图中的“Merge pull request” 提供了 3 种 merge 方法：
- Create a merge commit：GitHub 的底层操作是 git merge --no-ff。feature 分支上所有的 commit 都会加到 master 分支上，并且会生成一个 merge commit。这种方式可以让我们清晰地知道是谁做了提交，做了哪些提交，回溯历史的时候也会更加方便。
- Squash and merge：GitHub 的底层操作是 git merge --squash。Squash and merge 会使该 pull request 上的所有 commit 都合并成一个 commit ，然后加到 master 分支上，但原来的 commit 历史会丢失。如果开发人员在 feature 分支上提交的 commit 非常随意，没有规范，那么我们可以选择这种方法来丢弃无意义的 commit。但是在大型项目中，每个开发人员都应该是遵循 commit 规范的，因此我不**建议**你在团队开发中使用 Squash and merge。
- Rebase and merge：GitHub 的底层操作是 git rebase。这种方式会将 pull request 上的所有提交历史按照原有顺序依次添加到 master 分支的头部（HEAD）。因为 git rebase 有风险，在你不完全熟悉 Git 工作流时，我不**建议** merge 时选择这个。

通过分析每个方法的优缺点，在实际的项目开发中，我比较**推荐**你使用 Create a merge commit 方式。

从刚才讲完的具体开发流程中，我们可以感受到，功能分支工作流上手比较简单，不仅能使你并行开发多个功能，还可以添加 code review，从而保障代码质量。当然它也有缺点，就是无法给分支分配明确的目的，不利于团队配合。它适合用在开发团队相对固定、规模较小的项目中。接下来我们要讲的 Git Flow 工作流以功能分支工作流为基础，较好地解决了上述问题。

### Git Flow 工作流
Git Flow 工作流是一个非常成熟的方案，也是非开源项目中最常用到的工作流。它定义了一个围绕项目发布的严格分支模型，通过为代码开发、发布和维护分配独立的分支来让项目的迭代流程更加顺畅，比较适合大型的项目或者迭代速度快的项目。接下来，我会通过介绍 Git Flow 的 5 种分支和工作流程，来给你讲解 GIt Flow 是如何工作的。

Git Flow 的 5 种分支
Git Flow 中定义了 5 种分支，分别是 master、develop、feature、release 和 hotfix。其中，master 和 develop 为常驻分支，其他为非常驻分支，不同的研发阶段会用到不同的分支。这 5 种分支的详细介绍见下表：

![img](https://static001.geekbang.org/resource/image/fa/d9/fa611f83053afd77cf3ddf83561ba1d9.png?wh=942*568)

#### Git Flow 开发流程
这里我们用一个实际的例子来演示下 Git Flow 的开发流程。场景如下：

a. 当前版本为：0.9.0。b. 需要新开发一个功能，使程序执行时向标准输出输出“hello world”字符串。c. 在开发阶段，线上代码有 Bug 需要紧急修复。

假设我们的 Git 项目名为 gitflow-demo，项目目录下有 2 个文件，分别是 README.md 和 main.go，内容如下。

```
package main

import "fmt"

func main() {
  fmt.Println("callmainfunction")
}
```
具体的开发流程有 12 步，你可以跟着以下步骤操作练习。创
```bash
# 建一个常驻的分支：develop。
$ git checkout -b develop master
# 基于 develop 分支，新建一个功能分支：feature/print-hello-world。
$ git checkout -b feature/print-hello-world develop
```
feature/print-hello-world 分支中，在 main.go 文件中添加一行代码fmt.Println("Hello")，添加后的代码如下。

```
package main

import "fmt"

func main() {
  fmt.Println("callmainfunction")
  fmt.Println("Hello")
}
```
紧急修复 Bug。我们正处在新功能的开发中（只完成了 fmt.Println("Hello")而非 fmt.Println("Hello World")）突然线上代码发现了一个 Bug，我们要立即停止手上的工作，修复线上的 Bug，步骤如下。

```
$ git stash # 1. 开发工作只完成了一半，还不想提交，可以临时保存修改至堆栈区
$ git checkout -b hotfix/print-error master # 2. 从 master 建立 hotfix 分支
$ vi main.go # 3. 修复 bug，callmainfunction -> call main function
$ git commit -a -m 'fix print message error bug' # 4. 提交修复
$ git checkout develop # 5. 切换到 develop 分支
$ git merge --no-ff hotfix/print-error # 6. 把 hotfix 分支合并到 develop 分支
$ git checkout master # 7. 切换到 master 分支
$ git merge --no-ff hotfix/print-error # 8. 把 hotfix 分支合并到 master
$ git tag -a v0.9.1 -m "fix log bug" # 9. master 分支打 tag
$ go build -v . # 10. 编译代码，并将编译好的二进制更新到生产环境
$ git branch -d hotfix/print-error # 11. 修复好后，删除 hotfix/xxx 分支
$ git checkout feature/print-hello-world # 12. 切换到开发分支下
$ git merge --no-ff develop # 13. 因为 develop 有更新，这里最好同步更新下
$ git stash pop # 14. 恢复到修复前的工作状态
```
继续开发。在 main.go 中加入 fmt.Println("Hello World")。

提交代码到 feature/print-hello-world 分支。

```
$ git commit -a -m "print 'hello world'"
```
在 feature/print-hello-world 分支上做 code review。首先，我们需要将 feature/print-hello-world push 到代码托管平台，例如 GitHub 上。

```
$ git push origin feature/print-hello-world
```
然后，我们在 GitHub 上，基于 feature/print-hello-world 创建 pull request，如下图所示。

![img](https://static001.geekbang.org/resource/image/ac/ea/ac70d5ab86887e47f78c48d1df42f2ea.png?wh=1637*729)

创建完 pull request 之后，我们就可以指定 Reviewers 进行 code review，如下图所示。


![img](https://static001.geekbang.org/resource/image/bc/50/bc5168fe73abc257ba35342764647250.png?wh=2420*813)

code review 通过后，由代码仓库 matainer 将功能分支合并到 develop 分支。


```
$ git checkout develop
$ git merge --no-ff feature/print-hello-world
```
基于 develop 分支，创建 release 分支，测试代码。


```
$ git checkout -b release/1.0.0 develop
$ go build -v . # 构建后，部署二进制文件，并测试
```
测试失败，因为我们要求打印“hello world”，但打印的是“Hello World”，修复的时候，我们直接在 release/1.0.0 分支修改代码，修改完成后，提交并编译部署。

```
$ git commit -a -m "fix bug"
$ go build -v .
```

测试通过后，将功能分支合并到 master 分支和 develop 分支。

```
$ git checkout develop
$ git merge --no-ff release/1.0.0
$ git checkout master
$ git merge --no-ff release/1.0.0
$ git tag -a v1.0.0 -m "add print hello world" # master 分支打 tag
```
删除 feature/print-hello-world 分支，也可以选择性删除 release/1.0.0 分支。

```
$ git branch -d feature/print-hello-world
```
亲自操作一遍之后，你应该会更了解这种模式的优缺点。它的缺点，就是你刚才已经体会到的，它有一定的上手难度。不过 Git Flow 工作流还是有很多优点的：Git Flow 工作流的每个分支分工明确，这可以最大程度减少它们之间的相互影响。因为可以创建多个分支，所以也可以并行开发多个功能。另外，和功能分支工作流一样，它也可以添加 code review，保障代码质量。

**因此，Git Flow 工作流比较适合开发团队相对固定，规模较大的项目。**


### Forking 工作流
上面讲的 Git Flow 是非开源项目中最常用的，而在开源项目中，最常用到的是 Forking 工作流，例如 Kubernetes、Docker 等项目用的就是这种工作流。这里，我们先来了解下 fork 操作。fork 操作是在个人远程仓库新建一份目标远程仓库的副本，比如在 GitHub 上操作时，在项目的主页点击 fork 按钮（页面右上角），即可拷贝该目标远程仓库。Forking 工作流的流程如下图所示。

![img](https://static001.geekbang.org/resource/image/63/ea/63419f767c61c9580861b59445b90fea.png?wh=2213*1019)


假设开发者 A 拥有一个远程仓库，如果开发者 B 也想参与 A 项目的开发，B 可以 fork 一份 A 的远程仓库到自己的 GitHub 账号下。后续 B 可以在自己的项目进行开发，开发完成后，B 可以给 A 提交一个 PR。这时候 A 会收到通知，得知有新的 PR 被提交，A 会去查看 PR 并 code review。如果有问题，A 会直接在 PR 页面提交评论，B 看到评论后会做进一步的修改。最后 A 通过 B 的 PR 请求，将代码合并进了 A 的仓库。这样就完成了 A 代码仓库新特性的开发。如果有其他开发者想给 A 贡献代码，也会执行相同的操作。

GitHub 中的 Forking 工作流详细步骤共有 6 步（假设目标仓库为 gitflow-demo），你可以跟着以下步骤操作练习。

Fork 远程仓库到自己的账号下。访问https://github.com/marmotedu/gitflow-demo ，点击 fork 按钮。fork 后的仓库地址为：https://github.com/colin404fork/gitflow-demo 。

2) 克隆 fork 的仓库到本地。

```
$ git clone https://github.com/colin404fork/gitflow-demo
$ cd gitflow-demo
$ git remote add upstream https://github.com/marmotedu/gitflow-demo
$ git remote set-url --push upstream no_push # Never push to upstream master
$ git remote -v # Confirm that your remotes make sense
origin  https://github.com/colin404fork/gitflow-demo (fetch)
origin  https://github.com/colin404fork/gitflow-demo (push)
upstream  https://github.com/marmotedu/gitflow-demo (fetch)
upstream  https://github.com/marmotedu/gitflow-demo (push)
```
创建功能分支。首先，要同步本地仓库的 master 分支为最新的状态（跟 upstream master 分支一致）。

```
$ git fetch upstream
$ git checkout master
$ git rebase upstream/master
```
然后，创建功能分支。

```
$ git checkout -b feature/add-function
```

提交 commit。在 feature/add-function 分支上开发代码，开发完代码后，提交 commit。

```
$ git fetch upstream # commit 前需要再次同步 feature 跟 upstream/master
$ git rebase upstream/master
$ git add <file>
$ git status
$ git commit
```
分支开发完成后，可能会有一堆 commit，但是合并到主干时，我们往往希望只有一个（或最多两三个）commit，这可以使功能修改都放在一个或几个 commit 中，便于后面的阅读和维护。这个时候，我们可以用 git rebase 来合并和修改我们的 commit，操作如下：

```
$ git rebase -i origin/master
```
第 5 讲已经介绍过了git rebase -i 的使用方法 ，如果你有疑问可以再去看看，这里不再说明。还有另外一种合并 commit 的简便方法，就是先撤销过去 5 个 commit，然后再建一个新的：

```
$ git reset HEAD~5
$ git add .
$ git commit -am "Here's the bug fix that closes #28"
$ git push --force
```
squash 和 fixup 命令，还可以当作命令行参数使用，自动合并 commit。

```
$ git commit --fixup
$ git rebase -i --autosquash
```
push 功能分支到个人远程仓库。在完成了开发，并 commit 后，需要将功能分支 push 到个人远程代码仓库，代码如下：

```
$ git push -f origin feature/add-function
```
在个人远程仓库页面创建 pull request。提交到远程仓库以后，我们就可以创建 pull request，然后请求 reviewers 进行代码 review，确认后合并到 master。这里要**注意**，创建 pull request 时，base 通常选择目标远程仓库的 master 分支。

我们已经讲完了 Forking 工作流的具体步骤，你觉得它有什么优缺点呢？

结合操作特点，我们来看看它的优点：Forking 工作流中，项目远程仓库和开发者远程仓库完全独立，开发者通过提交 Pull Request 的方式给远程仓库贡献代码，项目维护者选择性地接受任何开发者的提交，通过这种方式，可以避免授予开发者项目远程仓库的权限，从而提高项目远程仓库的安全性，这也使得任意开发者都可以参与项目的开发。但 Forking 工作流也有局限性，就是对于职能分工明确且不对外开源的项目优势不大。

Forking 工作流比较适用于以下三种场景：（1）开源项目中；（2）开发者有衍生出自己的衍生版的需求；（3）开发者不固定，可能是任意一个能访问到项目的开发者。

### 回顾
这一讲中，我基于 Git 向你介绍了 4 种开发模式，现在跟我回顾一下吧。

集中式工作流：开发者直接在本地 master 分支开发代码，开发完成后 push 到远端仓库 master 分支。功能分支工作流：开发者基于 master 分支创建一个新分支，在新分支进行开发，开发完成后合并到远端仓库 master 分支。Git Flow 工作流：Git Flow 工作流为不同的分支分配一个明确的角色，并定义分支之间什么时候、如何进行交互，比较适合大型项目的开发。Forking 工作流：开发者先 fork 项目到个人仓库，在个人仓库完成开发后，提交 pull request 到目标远程仓库，远程仓库 review 后，合并 pull request 到 master 分支。

集中式工作流是最早的 Git 工作流，功能分支工作流以集中式工作流为基础，Git Flow 工作流又是以功能分支工作流为基础，Forking 工作流在 Git Flow 工作流基础上，解耦了个人远端仓库和项目远端仓库。

每种开发模式各有优缺点，适用于不同的场景，我总结在下表中：

![img](https://static001.geekbang.org/resource/image/55/07/5503ce60f7c2ae5d7628222a4d87cc07.png?wh=820*505)

总的来说，在选择工作流时，我的**推荐**如下：非开源项目采用 Git Flow 工作流。开源项目采用 Forking 工作流。因为这门课的实战项目对于项目开发者来说是一个偏大型的非开源项目，所以采用了 Git Flow 工作流。

## 8 | 研发流程设计（上）：如何设计 Go 项目的开发流程？
一个不合理的研发流程会带来很多问题，例如：**代码管理混乱**。合并代码时出现合错、合丢、代码冲突等问题。**研发效率低**。编译、测试、静态代码检查等全靠手动操作，效率低下。甚至，因为没有标准的流程，一些开发者会漏掉测试、静态代码检查等环节。**发布效率低**。发布周期长，以及发布不规范造成的现网问题频发。

项目研发流程会因为团队、项目、需求等的不同而不同，很难概括出一个方法论让你去设计研发流程。

在这一讲中，我会介绍一种业界已经设计好的、相对标准的研发流程，来告诉你怎么设计研发流程。通过学习它，你不仅能够了解到项目研发的通用流程，而且还可以基于这个流程来优化、定制，满足你自己的流程需求。

### 在设计研发流程时，需要关注哪些点？
在看具体的研发流程之前，我们需要先思考一个问题：你觉得，一个好的流程应该是什么样子的？虽然我们刚才说了，不同团队、项目、需求的研发流程不会一成不变，但为了最大限度地提高研发效能，这些不同的流程都会遵循下面这几个原则。
- 发布效率高：研发流程应该能提高发布效率，减少发布时间和人工介入的工作量。
- 发布质量高：研发流程应该能够提高发布质量，确保发布出去的代码是经过充分测试的，并且完全避免人为因素造成的故障。
- 迭代速度快：整个研发流程要能支持快速迭代，产品迭代速度越快，意味着产品的竞争力越强，在互联网时代越能把握先机。
- 明确性：整个研发流程中角色的职责、使用的工具、方法和流程都应该是明确的，这可以增强流程的可执行性。
- 流程合理：研发流程最终是供产品、开发、测试、运维等人员使用的，所以整个流程设计不能是反人类的，要能够被各类参与人员接受并执行。
- 柔性扩展：研发流程应该是柔性且可扩展的，能够灵活变通，并适应各类场景。
- 输入输出：研发流程中的每个阶段都应该有明确的输入和输出，这些输入和输出标志着上一个阶段的完成，下一个阶段的开始。

明确了这些关注点，我们就有了设计、优化研发流程的抓手了。接下来，我们就可以一起去学习一套业界相对标准的研发流程了。在学习的过程中，你也能更好地理解我对各个流程的一些经验和**建议**了。

### 业界相对标准的研发流程，长啥样？
一个项目从立项到结项，中间会经历很多阶段。业界相对标准的划分，是把研发流程分为六个阶段，分别是需求阶段、设计阶段、开发阶段、测试阶段、发布阶段、运营阶段。其中，开发人员需要参与的阶段有 4 个：设计阶段、开发阶段、测试阶段和发布阶段。下图就是业界相对比较标准的流程：

![img](https://static001.geekbang.org/resource/image/ab/3b/ab6ac57696c0e90cf82624f78a82333b.png?wh=7309*2306)


每个阶段结束时，都需要有一个最终的产出物，可以是文档、代码或者部署组件等。这个产出物既是当前阶段的结束里程碑，又是下一阶段的输入。所以说，各个阶段不是割裂的，而是密切联系的整体。每个阶段又细分为很多步骤，这些步骤是需要不同的参与者去完成的工作任务。在完成任务的过程中，可能需要经过多轮的讨论、修改，最终形成定稿。

这里有个点我们一定要**注意**：研发流程也是一种规范，很难靠开发者的自觉性去遵守。为了让项目参与人员尽可能地遵守规范，需要借助一些工具、系统来对他们进行强约束。所以，在我们设计完整个研发流程之后，需要认真思考下，有哪些地方可以实现自动化，有哪些地方可以靠工具、系统来保障规范的执行。这些自动化工具会在第 16 讲 中详细介绍。接下来，咱们就具体看看研发的各个阶段，以及每个阶段的具体内容。

### 需求阶段
需求阶段是将一个抽象的产品思路具化成一个可实施产品的阶段。在这个阶段，产品人员会讨论产品思路、调研市场需求，并对需求进行分析，整理出一个比较完善的需求文档。最后，产品人员会组织相关人员对需求进行评审，如果评审通过，就会进入设计阶段。

需求阶段，一般不需要研发人员参与。但这里，我还是**建议**你积极参与产品需求的讨论。虽然我们是研发，但我们的视野和对团队的贡献，可以不仅仅局限在研发领域。这里有个点需要提醒你，如果你们团队有测试人员，这个阶段也需要拉测试人员旁听下。因为了解产品设计，对测试阶段测试用例的编写和功能测试等都很有帮助。需求阶段的产出物是一个通过评审的详细的需求文档。

### 设计阶段
设计阶段，是整个产品研发过程中非常重要的阶段，包括的内容也比较多，你可以看一下这张表：

![img](https://static001.geekbang.org/resource/image/81/1b/81296e3f9f0f90e77dd771ee4f61b71b.png?wh=642*458)

这里的每一个设计项都应该经过反复的讨论、打磨，最终在团队内达成共识。这样可以确保设计是合理的，并减少返工的概率。**这里想提醒你的是，技术方案和实现都要经过认真讨论，并获得一致通过，否则后面因为技术方案设计不当，需要返工，你要承担大部分责任。**对于后端开发人员，在设计技术方案之前，要做好充足的调研。一个技术方案，不仅要调研业界优秀的实现，还要了解友商相同技术的实现。只有这样，才可以确保我们的技术用最佳的方式实现。

除此之外，在这个阶段一些设计项可以并行，以缩短设计阶段的耗时。例如，产品设计和技术设计可以并行展开。另外，如果你们团队有测试人员，研发阶段最好也拉上测试人员旁听下，有利于后面的测试。该阶段的产出物是一系列的设计文档，这些文档会指导后面的整个研发流程。

### 开发阶段
开发阶段，从它的名字你就知道了，这是开发人员的主战场，同时它可能也是持续时间最长的阶段。在这一阶段，开发人员根据技术设计文档，编码实现产品需求。开发阶段是整个项目的核心阶段，包含很多工作内容，而且每一个 Go 项目具体的步骤是不同的。我把开发阶段的常见步骤总结在了下图中，帮助你对它进行整体把握。

![img](https://static001.geekbang.org/resource/image/13/57/137a2a20067d1472c9f00c6387a30857.png?wh=2225*2031)

让我们来详细看下这张图里呈现的步骤。开发阶段又可以分为“开发”和“构建”两部分，我们先来看开发。首先，我们需要制定一个所有研发人员共同遵循的 Git 工作流规范。最常使用的是 Git Flow 工作流或者 Forking 工作流。

为了提高开发效率，越来越多的开发者采用生成代码的方式来生成一部分代码，所以在真正编译之前可能还需要先生成代码，比如生成.pb.go 文件、API 文档、测试用例、错误码等。**我的**建议**是，在项目开发中，你要思考怎么尽可能自动生成代码**。这样不仅能提高研发效率，还能减少错误。

对于一个开源项目，我们可能还需要检查新增的文件是否有版权信息。此外，根据项目不同，开发阶段还可能有其它不同的步骤。在流程的最后，通常会进行静态代码检查、单元测试和编译。编译之后，我们就可以启动服务，并进行自测了。自测之后，我们可以遵循 Git Flow 工作流，将开发分支 push 到代码托管平台进行 code review。code review 通过之后，我们就可以将代码 merge 到 develop 分支上。

接下来进入构建阶段。这一阶段最好借助 CI/CD 平台实现自动化，提高构建效率。合并到 develop 分支的代码同样需要进行代码扫描、单元测试，并编译打包。最后，我们需要进行归档，也就是将编译后的二进制文件或 Docker 镜像上传到制品库或镜像仓库。

我刚刚带着你完整走了一遍开发阶段的常见步骤。可以看到，整个开发阶段步骤很多，而且都是高频的操作。那怎么提高效率呢？这里我**推荐**你两种方法：
- 将开发阶段的步骤通过 Makefile 实现集中管理；
- 将构建阶段的步骤通过 CI/CD 平台实现自动化。

**你还需要特别**注意**这一点：在最终合并代码到 master 之前，要确保代码是经过充分测试的。**这就要求我们一定要借助代码管理平台提供的 Webhook 能力，在代码提交时触发 CI/CD 作业，对代码进行扫描、测试，最终编译打包，并以整个作业的成功执行作为合并代码的先决条件。

开发阶段的产出物是满足需求的源代码、开发文档，以及编译后的归档文件。

### 测试阶段
测试阶段由测试工程师（也叫质量工程师）负责，这个阶段的主要流程是：测试工程师根据需求文档创建测试计划、编写测试用例，并拉研发同学一起评审测试计划和用例。评审通过后，测试工程师就会根据测试计划和测试用例对服务进行测试。

**为了提高整个研发效率，测试计划的创建和测试用例的编写可以跟开发阶段并行**。研发人员在交付给测试时，要提供自测报告、自测用例和安装部署文档。**这里我要强调的是：在测试阶段，为了不阻塞测试，确保项目按时发布，研发人员应该优先解决测试同学的 Bug，至少是阻塞类的 Bug。为了减少不必要的沟通和排障，安装部署文档要尽可能详尽和准确。**

另外，你也可以及时跟进测试，了解测试同学当前遇到的卡点。因为实际工作中，一些测试同学在遇到卡点时，不善于或者不会及时地跟你同步卡点，往往研发 1 分钟就可以解决的问题，可能要花测试同学几个小时或者更久的时间去解决。

当然，测试用例几乎不可能涵盖整个变更分支，所以对于一些难测，隐藏的测试，需要研发人员自己加强测试。最后，一个大特性测试完，请测试同学吃个饭吧，大家唠唠家常，联络联络感情，下次合作会更顺畅。测试阶段的产出物是满足产品需求、达到发布条件的源代码，以及编译后的归档文件。

### 发布阶段
发布阶段主要是将软件部署上线，为了保证发布的效率和质量，我们需要遵循一定的发布流程，如下图所示：

![img](https://static001.geekbang.org/resource/image/37/88/37yy6e0896daa8f97883631624996388.png?wh=2288*1938)

发布阶段按照时间线排序又分为代码发布、发布审批和服务发布 3 个子阶段。接下来，我详细给你介绍下这 3 个子阶段。我们先来看一下代码发布。




首先，开发人员首先需要将经过测试后的代码合并到主干，通常是 master 分支，并生成版本号，然后给最新的 commit 打上版本标签。之后，可以将代码 push 到代码托管平台，并触发 CI 流程，CI 流程一般会执行代码扫描、单元测试、编译，最后将构建产物发布到制品库。CI 流程中，我们可以根据需要添加任意功能。



接着，进入到发布审批阶段。首先需要申请资源，**资源申请周期可能会比较久，所以申请得越早越好，甚至资源申请可以在测试阶段发起**。在资源申请阶段，可以申请诸如服务器、MySQL、Redis、Kafka 之类资源。资源申请通常是开发人员向运维人员提需求，由运维人员根据需求，在指定的时间前准备好各类资源。如果是物理机通常申请周期会比较久，但当前越来越多的项目选择容器化部署，这可以极大地缩短资源的申请周期。如果在像腾讯云弹性容器这类 Serverless 容器平台上部署业务，甚至可以秒申请资源。所以这里，我也**建议**优先采用容器化部署。




发布之前需要创建发布计划，里面需要详细描述本次的变更详情，例如变更范围、发布方案、测试结果、验证和回滚方案等。这里需要你**注意**，**在创建发布计划时，一定要全面梳理这次变更的影响点**。例如，是否有不兼容的变更，是否需要变更配置，是否需要变更数据库等。任何一个遗漏，都可能造成现网故障，影响产品声誉和用户使用。



接下来，需要创建发布单，在发布单中可以附上发布计划，并根据团队需求填写其它发布内容，发布计划需要跟相关参与者对齐流程、明确职责。发布单最终提交给审批人（通常是技术 leader）对本次发布进行审批，审批通过后，才可以进行部署。


最后，就可以进入到服务发布阶段，将服务发布到现网。在正式部署的时候，应用需要先部署到预发环境。在预发环境，产品人员、测试人员和研发人员会分别对产品进行验证。其中，产品人员主要验证产品功能的体验是否流畅，开发和测试人员主要验证产品是否有 Bug。预发环境验证通过，产品才能正式发布到现网。



这里，我强烈**建议**，**编写一些自动化的测试用例，在服务发布到现网之后，对现网服务做一次比较充分的回归测试。**通过这个自动化测试，可以以最小的代价，最快速地验证现网功能，从而保障发布质量。



另外，我们还要**注意**，**现网可能有多个地域，每个地域发布完成之后都要进行现网验证**。发布阶段的产出物是正式上线的软件。


### 运营阶段
研发流程的最后一个阶段是运营阶段，该阶段主要分为产品运营和运维两个部分。
- 产品运营：通过一系列的运营活动，比如线下的技术沙龙、线上的免费公开课、提高关键词排名或者输出一些技术推广文章等方式，来推高整个产品的知名度，提高产品的用户数量，并提高月活和日活。
- 运维：由运维工程师负责，核心目标是确保系统稳定的运行，如果系统异常，能够及时发现并修复问题。长期目标是通过技术手段或者流程来完善整个系统架构，减少人力投入、提高运维效率，并提高系统的健壮性和恢复能力。



从上面可以看到，运维属于技术类，运营属于产品类，这二者不要搞混。为了加深你的理解和记忆，我将这些内容，总结在了下面一张图中。![img](https://static001.geekbang.org/resource/image/e0/b5/e0a4d8ed5f3a6a8a4bc4035e261881b5.png?wh=2519*1431)



在运营阶段，研发人员的主要职责就是协助运维解决现网 Bug，优化部署架构。当然，研发人员可能也需要配合运营人员开发一些运营接口，供运营人员使用。

到这里，业界相对标准的这套研发流程，我们就学完了。在学习过程中，你肯定也发现了，整个研发流程会涉及很多角色，不同角色参与不同的阶段，负责不同的任务。这里我再给你额外扩展一个点，就是这些核心角色和分工是啥样的。这些扩展内容，我放在了一张图和一张表里。这些角色和分工比较好理解，也不需要你背下来，只要先有一个大概的印象就可以了。

![img](https://static001.geekbang.org/resource/image/d1/da/d1797845f4105476c99ecc22cc7562da.png?wh=3088*1325)

具体分工如下表所示。



![img](https://static001.geekbang.org/resource/image/40/d5/40a1e20b153bb3ba1005cea4aefe62d5.png?wh=754*815)

### 总结
在开发 Go 项目时，掌握项目的研发流程很重要。掌握研发流程，会让项目研发对我们更加白盒，并且有利于我们制定详细的工作任务。那么如何设计项目研发流程呢？你可以根据需要自行设计。自行设计时有些点是一定要关注的，例如我们的流程需要支持高的发布效率和发布质量，支持快速迭代，流程是合理、可扩展的，等等。如果你不想自己设计，也可以。在这一讲中，我介绍了一套相对通用、标准的研发流程，如果合适可以直接拿来作为自己设计的研发流程。这套研发流程包含 6 个阶段：需求阶段、设计阶段、开发阶段、测试阶段、发布阶段和运营阶段。这里我将这些流程和每个流程的核心点总结在下面一张图中。

![img](https://static001.geekbang.org/resource/image/dd/0f/ddb314275ba1bab28413221bc56ac80f.png?wh=3225*1256)

## 9 | 研发流程设计（下）：如何管理应用的生命周期？

应用的生命周期管理，怎么理解呢？**其实，就是指采用一些好的工具或方法在应用的整个生命周期中对应用进行管理，以提高应用的研发效率和质量。**

这一讲我们就一起学习下，业界在不同时期沉淀下来的优秀管理手段，以及我对这些管理手段的经验和**建议**，帮助你选到一个最合适的。

### 应用生命周期管理技术有哪些？
那么，有哪些应用生命周期管理技术呢？在这里我先整体介绍一下，你先有个大致的印象，一会我们再一个个细讲。我们可以**从两个维度来理解应用生命周期管理技术。**

第一个维度是演进维度。应用生命周期，最开始主要是通过研发模式来管理的，按时间线先后出现了瀑布模式、迭代模式、敏捷模式。接着，为了解决研发模式中的一些痛点出现了另一种管理技术，也就是 CI/CD 技术。随着 CI/CD 技术的成熟，又催生了另一种更高级的管理技术 DevOps。


第二个维度是管理技术的类别。应用生命周期管理技术可以分为两类：
- 研发模式，用来确保整个研发流程是高效的。DevOps，主要通过协调各个部门之间的合作，来提高软件的发布效率和质量。
- DevOps 中又包含了很多种技术，主要包括 CI/CD 和多种 Ops，例如 AIOps、ChatOps、GitOps、NoOps 等。其中，CI/CD 技术提高了软件的发布效率和质量，而 Ops 技术则提高了软件的运维和运营效率。


尽管这些应用生命周期管理技术有很多不同，但是它们彼此支持、相互联系。研发模式专注于开发过程，DevOps 技术里的 CI/CD 专注于流程，Ops 则专注于实战。为了帮助你理解，我总结出了下面这张图供你参考。

![img](https://static001.geekbang.org/resource/image/9a/a3/9a290c28b0c238dd69e24dcc9f5c7ea3.png?wh=5241*2222)

这两个维度涉及的管理技术虽然不少，但一共就是那几类。所以，**为了能够逻辑清晰地给你讲解明白这些技术，我会从演进维度来展开，也就是按照这样的顺序：研发模式（瀑布模式 -> 迭代模式 -> 敏捷模式） -> CI/CD -> DevOps。**

接下来，我们就详细说说这些应用生命周期的管理方法，先来看专注于开发过程的研发模式部分。

### 研发模式
研发模式主要有三种，演进顺序为瀑布模式 -> 迭代模式 -> 敏捷模式，现在我们逐一看下。

#### 瀑布模式
在早期阶段，软件研发普遍采用的是瀑布模式，像我们熟知的 RHEL、Fedora 等系统就是采用瀑布模式。**瀑布模式按照预先规划好的研发阶段来推进研发进度**。比如，按照需求阶段、设计阶段、开发阶段、测试阶段、发布阶段、运营阶段的顺序串行执行开发任务。每个阶段完美完成之后，才会进入到下一阶段，阶段之间通过文档进行交付。整个过程如下图所示。

![img](https://static001.geekbang.org/resource/image/7c/89/7ccc702a02cf24e2295cc50a506e6289.png?wh=2466*1272)


瀑布模式最大的优点是简单。它严格按照研发阶段来推进研发进度，流程清晰，适合按项目交付的应用。

但它的缺点也很明显，最突出的就是这两个：**只有在项目研发的最后阶段才会交付给客户**。**交付后，如果客户发现问题，变更就会非常困难，代价很大**。**研发周期比较长**，很难适应互联网时代对产品快速迭代的诉求。

为了解决这两个问题，迭代式研发模式诞生了。

#### 迭代模式
迭代模式，是一种与瀑布式模式完全相反的开发过程：**研发任务被切分为一系列轮次，每一个轮次都是一个迭代，每一次迭代都是一个从设计到实现的完整过程**。它**不要求每一个阶段的任务都做到最完美，而是先把主要功能搭建起来，然后再通过客户的反馈信息不断完善**。迭代开发可以帮助产品改进和把控进度，它的灵活性极大地提升了适应需求变化的能力，克服了高风险、难变更、复用性低的特点。但是，**迭代模式的问题在于比较专注于开发过程，很少从项目管理的视角去加速和优化项目开发过程。**接下来要讲的敏捷模式，就弥补了这个缺点。


#### 敏捷模式
敏捷模式把一个大的需求分成多个、可分阶段完成的小迭代，每个迭代交付的都是一个可使用的软件。在开发过程中，软件要一直处于可使用状态。敏捷模式中具有代表性的开发模式，是 Scrum 开发模型。Scrum 开发模型网上有很多介绍，你可以去看看。在敏捷模式中，我们**会把一个大的需求拆分成很多小的迭代**，这意味着开发过程中会有很多个开发、构建、测试、发布和部署的流程。这种高频度的操作会给研发、运维和测试人员带来很大的工作量，降低了工作效率。为了解决这个问题，CI/CD 技术诞生了。


### CI/CD：自动化构建和部署应用
CI/CD 技术通过自动化的手段，来快速执行代码检查、测试、构建、部署等任务，从而提高研发效率，解决敏捷模式带来的弊端。

CI/CD 包含了 3 个核心概念。CI：Continuous Integration，持续集成。CD：Continuous Delivery，持续交付。CD：Continuous Deployment，持续部署。


CI 容易理解，但两个 CD 很多开发者区分不开。这里，我来详细说说这 3 个核心概念。

首先是持续集成。它的含义为：**频繁地（一天多次）将开发者的代码合并到主干上**。它的流程为：**在开发人员完成代码开发，并 push 到 Git 仓库后，CI 工具可以立即对代码进行扫描、（单元）测试和构建，并将结果反馈给开发者**。持续集成通过后，会将代码合并到主干。CI 流程可以使应用软件的问题在开发阶段就暴露出来，这会让开发人员交付代码时更有信心。因为 CI 流程内容比较多，而且执行比较频繁，所以 CI 流程需要有自动化工具来支撑。


其次是持续交付，它指的是一种能够使软件在较短的循环中可靠发布的软件方法。**持续交付在持续集成的基础上，将构建后的产物自动部署在目标环境中**。这里的目标环境，可以是测试环境、预发环境或者现网环境。通常来说，持续部署可以自动地将服务部署到测试环境或者预发环境。因为部署到现网环境存在一定的风险，所以如果部署到现网环境，需要手工操作。手工操作的好处是，可以使相关人员评估发布风险，确保发布的正确性。



最后是持续部署，持续部署在持续交付的基础上，将经过充分测试的代码自动部署到生产环境，整个流程不再需要相关人员的审核。**持续部署强调的是自动化部署**，是交付的最高阶段。我们可以借助下面这张图，来了解持续集成、持续交付、持续部署的关系。


![img](https://static001.geekbang.org/resource/image/96/d0/963b9983543de3d66379567ba491d7d0.png?wh=6709*2328)
持续集成、持续交付和持续部署强调的是持续性，也就是能够支持频繁的集成、交付和部署，这离不开自动化工具的支持，离开了这些工具，CI/CD 就不再具有可实施性。持续集成的核心点在代码，持续交付的核心点在可交付的产物，持续部署的核心点在自动部署。

### DevOps：研发运维一体化
CI/CD 技术的成熟，加速了 DevOps 这种应用生命周期管理技术的成熟和落地。DevOps（Development 和 Operations 的组合）是一组过程、方法与系统的统称，用于促进开发（应用程序 / 软件工程）、技术运营和质量保障（QA）部门之间的沟通、协作与整合。这 3 个部门的相互协作，可以提高软件质量、快速发布软件。如下图所示：




![img](https://static001.geekbang.org/resource/image/c8/79/c81a361fb98500cec8c866f465f14679.png?wh=1800*1650)

要实现 DevOps，需要一些工具或者流程的支持，CI/CD 可以很好地支持 DevOps 这种软件开发模式，如果没有 CI/CD 自动化的工具和流程，DevOps 就是没有意义的，CI/CD 使得 DevOps 变得可行。


听到这里是不是有些晕？你可能想问，DevOps 跟 CI/CD 到底是啥区别呢？其实，这也是困扰很多开发者的问题。这里，我们可以这么理解：DevOps ！= CI/CD。DevOps 是一组过程、方法和系统的统称，而 CI/CD 只是一种软件构建和发布的技术。


DevOps 技术之前一直有，但是落地不好，因为没有一个好的工具来实现 DevOps 的理念。但是随着容器、CI/CD 技术的诞生和成熟，DevOps 变得更加容易落地。也就是说，这几年越来越多的人采用 DevOps 手段来提高研发效能。

随着技术的发展，目前已经诞生了很多 Ops 手段，来实现运维和运营的高度自动化。下面，我们就来看看 DevOps 中的四个 Ops 手段：**AIOps、ChatOps、GitOps、NoOps。**


#### AIOps：智能运维
在 2016 年，Gartner 提出利用 AI 技术的新一代 IT 运维，即 AIOps（智能运维）。通过 AI 手段，来智能化地运维 IT 系统。AIOps 通过搜集海量的运维数据，并利用机器学习算法，智能地定位并修复故障。


也就是说，AIOps 在自动化的基础上，增加了智能化，从而进一步推动了 IT 运维自动化，减少了人力成本。随着 IT 基础设施规模和复杂度的倍数增长，企业应用规模、数量的指数级增长，传统的人工 / 自动化运维，已经无法胜任愈加沉重的运维工作，而 AIOps 提供了一个解决方案。在腾讯、阿里等大厂很多团队已经在尝试和使用 AIOps，并享受到了 AIOps 带来的红利。例如，故障告警更加灵敏、准确，一些常见的故障，可以自动修复，无须运维人员介入等。

#### ChatOps：聊着天就把事情给办了
随着企业微信、钉钉等企业内通讯工具的兴起，最近几年出现了一个新的概念 ChatOps。简单来说，ChatOps 就是在一个聊天工具中，发送一条命令给 ChatBot 机器人，然后 ChatBot 会执行预定义的操作。这些操作可以是执行某个工具、调用某个接口等，并返回执行结果。


这种新型智能工作方式的优势是什么呢？它可以利用 ChatBot 机器人让团队成员和各项辅助工具连接在一起，以沟通驱动的方式完成工作。ChatOps 可以解决人与人、人与工具、工具与工具之间的信息孤岛，从而提高协作体验和工作效率。ChatOps 的工作流程如下图所示（网图）：


![img](https://static001.geekbang.org/resource/image/29/6e/292372572f1fa8cae9a44891bd233a6e.png?wh=1080*489)

开发 / 运维 / 测试人员通过 @聊天窗口中的机器人 Bot 来触发任务，机器人后端会通过 API 接口调用等方式对接不同的系统，完成不同的任务，例如持续集成、测试、发布等工作。机器人可以是我们自己研发的，也可以是开源的。目前，业界有很多流行的机器人可供选择，常用的有 Hubot、Lita、Errbot、StackStorm 等。使用 ChatOps 可以带来以下几点好处。

- 友好、便捷：所有的操作均在同一个聊天界面中，通过 @机器人以聊天的方式发送命令，免去了打开不同系统，执行不同操作的繁琐操作，方式更加友好和便捷。
- 信息透明：在同一个聊天界面中的所有同事都能够看到其他同事发送的命令，以及命令执行的结果，可以消除沟通壁垒，工作历史有迹可循，团队合作更加顺畅。
- 移动友好：可以在移动端向机器人发送命令、执行任务，让移动办公变为可能。
- DevOps 文化打造：通过与机器人对话，可以降低项目开发中，各参与人员的理解和使用成本，从而使 DevOps 更容易落地和推广。


#### GitOps： 一种实现云原生的持续交付模型
GitOps 是一种持续交付的方式。它的核心思想是将应用系统的声明性基础架构（YAML）和应用程序存放在 Git 版本库中。将 Git 作为交付流水线的核心，每个开发人员都可以提交拉取请求（Pull Request），并使用 Gi​​t 来加速和简化 Kubernetes 的应用程序部署和运维任务。通过 Git 这样的工具，开发人员可以将精力聚焦在功能开发，而不是软件运维上，以此提高软件的开发效率和迭代速度。


使用 GitOps 可以带来很多优点，其中最核心的是：当使用 Git 变更代码时，GitOps 可以自动将这些变更应用到程序的基础架构上。因为整个流程都是自动化的，所以部署时间更短；又因为 Git 代码是可追溯的，所以我们部署的应用也能够稳定且可重现地回滚。我们可以从概念和流程上来理解 GitOps，它有 3 个关键概念。

- 声明性容器编排：通过 Kubernetes YAML 格式的资源定义文件，来定义如何部署应用。
- 不可变基础设施：基础设施中的每个组件都可以自动的部署，组件在部署完成后，不能发生变更。如果需要变更，则需要重新部署一个新的组件。例如，Kubernetes 中的 Pod 就是一个不可变基础设施。
- 连续同步：不断地查看 Git 存储库，将任何状态更改反映到 Kubernetes 集群中。


![img](https://static001.geekbang.org/resource/image/2f/8d/2f1b427674e7da60668b2af42cf7338d.png?wh=3981*1244)

GitOps 的工作流程如下：**首先，开发人员开发完代码后推送到 Git 仓库，触发 CI 流程，CI 流程通过编译构建出 Docker 镜像，并将镜像 push 到 Docker 镜像仓库中。Push 动作会触发一个 push 事件，通过 webhook 的形式通知到 Config Updater 服务，Config Updater 服务会从 webhook 请求中获取最新 push 的镜像名，并更新 Git 仓库中的 Kubernetes YAML 文件。**

**然后，GitOps 的 Deploy Operator 服务，检测到 YAML 文件的变动，会重新从 Git 仓库中提取变更的文件，并将镜像部署到 Kubernetes 集群中。Config Updater 和 Deploy Operator 两个组件需要开发人员设计开发。**


#### NoOps：无运维
NoOps 即无运维，完全自动化的运维。在 NoOps 中不再需要开发人员、运营运维人员的协同，把微服务、低代码、无服务全都结合了起来，开发者在软件生命周期中只需要聚焦业务开发即可，所有的维护都交由云厂商来完成。


毫无疑问，NoOps 是运维的终极形态，在我看来它像 DevOps 一样，更多的是一种理念，需要很多的技术和手段来支撑。当前整个运维技术的发展，也是朝着 NoOps 的方向去演进的，例如 GitOps、AIOps 可以使我们尽可能减少运维，Serverless 技术甚至可以使我们免运维。相信未来 NoOps 会像现在的 Serverless 一样，成为一种流行的、可落地的理念。


### 如何选择合适的应用生命周期管理技术？
好了，到这里我们就把主要的应用生命周期管理技术，学得差不多了。那在实际开发中，如何选择适合自己的呢？在我看来，你可以从这么几个方面考虑。



首先，根据团队、项目选择一个合适的研发模式。如果项目比较大，需求变更频繁、要求快速迭代，**建议**选择敏捷开发模式。敏捷开发模式，也是很多大公司选择的研发模式，在互联网时代很受欢迎。接着，要建立自己的 CI/CD 流程。任何变更代码在合并到 master 分支时，一定要通过 CI/CD 的流程的验证。我**建议**，你在 CI/CD 流程中设置质量红线，确保合并代码的质量。接着，除了建立 CI/CD 系统，我还**建议**将 ChatOps 带入工作中，尽可能地将可以自动化的工作实现自动化，并通过 ChatOps 来触发自动化流程。随着企业微信、钉钉等企业聊天软件成熟和发展，ChatOps 变得流行和完善。最后，GitOps、AIOps 可以将部署和运维自动化做到极致，在团队有人力的情况下，值得探索。


到这里你可能会问了，大厂是如何管理应用生命周期的？大厂普遍采用敏捷开发的模式，来适应互联网对应用快速迭代的诉求。例如，腾讯的TAPD、Coding的 Scrum 敏捷管理就是一个敏捷开发平台。CI/CD 强制落地，ChatOps 已经广泛使用，AIOps 也有很多落地案例，GitOps 目前还在探索阶段，NoOps 还处在理论阶段。


### 总结
这一讲，我从技术演进的维度介绍了应用生命周期管理技术，这些技术可以提高应用的研发效率和质量。应用生命周期管理最开始是通过研发模式来管理的。在研发模式中，我按时间线分别介绍了瀑布模式、迭代模式和敏捷模式，其中的敏捷模式适应了互联网时代对应用快速迭代的诉求，所以用得越来越多。在敏捷模式中，我们需要频繁构建和发布我们的应用，这就给开发人员带来了额外的工作量，为了解决这个问题，出现了 CI/CD 技术。CI/CD 可以将代码的检查、测试、构建和部署等工作自动化，不仅提高了研发效率，还从一定程度上保障了代码的质量。另外，CI/CD 技术使得 DevOps 变得可行，当前越来越多的团队采用 DevOps 来管理应用的生命周期。

另外，这一讲中我也介绍了几个大家容易搞混的概念。
- 持续交付和持续部署。二者都是持续地部署应用，但是持续部署整个过程是自动化的，而持续交付中，应用在发布到现网前需要人工审批是否允许发布。
- - CI/CD 和 DevOps。DevOps 是一组过程、方法与系统的统称，其中也包含了 CI/CD 技术。而 CI/CD 是一种自动化的技术，DevOps 理念的落地需要 CI/CD 技术的支持。

最后，关于如何管理应用的生命周期，我给出了一些**建议**：研发模式**建议**选择敏捷模式，因为它更能胜任互联网时代快速迭代的诉求。DevOps 则要优先确保落地 CI/CD 技术，接着尝试落地 ChatOps 技术，如果有条件可以积极探索 AIOps 和 GitOps。

## 10 | 设计方法：怎么写出优雅的 Go 项目？

Go 语言简单易学，对于大部分开发者来说，编写可运行的代码并不是一件难事，**但如果想真正成为 Go 编程高手，你需要花很多精力去研究 Go 的编程哲学。**


在我的 Go 开发生涯中，我见过各种各样的代码问题，例如：**代码不规范，难以阅读；函数共享性差，代码重复率高；不是面向接口编程，代码扩展性差，代码不可测；代码质量低下。**究其原因，是因为这些代码的开发者很少花时间去认真研究如何开发一个优雅的 Go 项目，更多时间是埋头在需求开发中。


其实，我们之前所学的各种规范设计，也都是为了写出一个优雅的 Go 项目。在这一讲，我又补充了一些内容，从而形成了一套“写出优雅 Go 项目”的方法论。这一讲内容比较多，但很重要。


### 如何写出优雅的 Go 项目？
那么，如何写出一个优雅的 Go 项目呢？在回答这个问题之前，我们先来看另外两个问题：为什么是 Go 项目，而不是 Go 应用？一个优雅的 Go 项目具有哪些特点？


先来看第一个问题。Go 项目是一个偏工程化的概念，不仅包含了 Go 应用，还包含了项目管理和项目文档：


![img](https://static001.geekbang.org/resource/image/24/aa/24ba3548f8574a747b51e291224097aa.png?wh=2175x834)
这就来到了第二个问题，一个优雅的 Go 项目，不仅要求我们的 Go 应用是优雅的，还要确保我们的项目管理和文档也是优雅的。这样，我们根据前面几讲学到的 Go 设计规范，很容易就能总结出一个优雅的 Go 应用需要具备的特点：

符合 Go 编码规范和最佳实践；**易阅读、易理解，易维护；易测试、易扩展；代码质量高。**


解决了这两个问题，让我们回到这一讲的核心问题：如何写出优雅的 Go 项目？写出一个优雅的 Go 项目，在我看来，就是用“最佳实践”的方式去实现 Go 项目中的 Go 应用、项目管理和项目文档。具体来说，就是编写高质量的 Go 应用、高效管理项目、编写高质量的项目文档。

为了协助你理解，我将这些逻辑绘制成了下面一张图。

![img](https://static001.geekbang.org/resource/image/77/03/77d541223576135df4c3d511abbfe603.png?wh=3131x1488)

接下来，我们就看看如何根据前面几讲学习的 Go 项目设计规范，实现一个优雅的 Go 项目。我们先从编写高质量的 Go 应用看起。


### 编写高质量的 Go 应用
基于我的研发经验，要编写一个高质量的 Go 应用，其实可以归纳为 5 个方面：**代码结构、代码规范、代码质量、编程哲学和软件设计**方法，见下图。


![img](https://static001.geekbang.org/resource/image/23/69/2392d94feb95d3d64d765abe7d6e5e69.png?wh=3544x766)
接下来，我们详细说说这些内容。


#### 代码结构
为什么先说代码结构呢？因为组织合理的代码结构是一个项目的门面。我们可以通过两个手段来组织代码结构。

第一个手段是，组织一个好的目录结构。关于如何组合一个好的目录结构，你可以回顾 06 讲 的内容。第二个手段是，选择一个好的模块拆分方法。做好模块拆分，可以使项目内模块职责分明，做到低耦合高内聚。

那么 Go 项目开发中，如何拆分模块呢？目前业界有两种拆分方法，分别是**按层拆分和按功能拆分。**


首先，我们看下按层拆分，最典型的是 MVC 架构中的模块拆分方式。在 MVC 架构中，我们将服务中的不同组件按访问顺序，拆分成了 Model、View 和 Controller 三层。

![img](https://static001.geekbang.org/resource/image/ed/46/ed0c3dfyy52ac82539cb602eec9f0146.png?wh=497x625)

每层完成不同的功能：View（视图）是提供给用户的操作界面，用来处理数据的显示。Controller（控制器），负责根据用户从 View 层输入的指令，选取 Model 层中的数据，然后对其进行相应的操作，产生最终结果。Model（模型），是应用程序中用于处理数据逻辑的部分。

我们看一个典型的按层拆分的目录结构：

```
$ tree --noreport -L 2 layers
layers
├── controllers
│   ├── billing
│   ├── order
│   └── user
├── models
│   ├── billing.go
│   ├── order.go
│   └── user.go
└── views
    └── layouts
```
在 Go 项目中，按层拆分会带来很多问题。最大的问题是**循环引用**：相同功能可能在不同层被使用到，而这些功能又分散在不同的层中，很容易造成循环引用。所以，**你只要大概知道按层拆分是什么意思就够了，在 Go 项目中我**建议**你使用的是按功能拆分的方法，这也是 Go 项目中最常见的拆分方法。**
那什么是按功能拆分呢？我给你看一个例子你就明白了。比如，一个订单系统，我们可以根据不同功能将其拆分成用户（user）、订单（order）和计费（billing）3 个模块，每一个模块提供独立的功能，功能更单一：

![img](https://static001.geekbang.org/resource/image/0d/a5/0d65eb1363bf8055e209bc24d1d99ca5.png?wh=625x572)
下面是该订单系统的代码目录结构：

```
$ tree pkg
$ tree --noreport -L 2 pkg
pkg
├── billing
├── order
│   └── order.go
└── user
```
相较于按层拆分，按功能拆分模块带来的好处也很好理解：不同模块，功能单一，可以实现高内聚低耦合的设计哲学。因为所有的功能只需要实现一次，引用逻辑清晰，会大大减少出现循环引用的概率。


所以，有很多优秀的 Go 项目采用的都是按功能拆分的模块拆分方式，例如 Kubernetes、Docker、Helm、Prometheus 等。除了组织合理的代码结构这种方式外，编写高质量 Go 应用的另外一个行之有效的方法，是遵循 Go 语言代码规范来编写代码。在我看来，这也是最容易出效果的方式。


#### 代码规范
那我们要遵循哪些代码规范来编写 Go 应用呢？在我看来，其实就两类：**编码规范和最佳实践。**

首先，我们的代码要符合 Go 编码规范，这是最容易实现的途径。Go 社区有很多这类规范可供参考，其中，比较受欢迎的是Uber Go 语言编码规范。阅读这些规范确实有用，也确实花时间、花精力。所以，我在参考了已有的很多规范后，结合自己写 Go 代码的经验，特地为你整理了一篇 Go 编码规范作为加餐，也就是“特别放送 | 给你一份清晰、可直接套用的 Go 编码规范”。

有了可以参考的编码规范之后，我们需要扩展到团队、部门甚至公司层面。只有大家一起参与、遵守，规范才会变得有意义。其实，我们都清楚，要开发者靠自觉来遵守所有的编码规范，不是一件容易的事儿。这时候，我们可以使用静态代码检查工具，来约束开发者的行为。


有了静态代码检查工具后，不仅可以确保开发者写出的每一行代码都是符合 Go 编码规范的，还可以将静态代码检查集成到 CI/CD 流程中。这样，在代码提交后自动地检查代码，就保证了只有符合编码规范的代码，才会被合入主干。

Go 语言的静态代码检查工具有很多，目前用的最多的是golangci-lint，这也是我极力**推荐**你使用的一个工具。关于这个工具的使用，我会在第 15 讲和你详细介绍。


除了遵循编码规范，要想成为 Go 编程高手，你还得学习并遵循一些最佳实践。“最佳实践”是社区经过多年探索沉淀下来的、符合 Go 语言特色的经验和共识，它可以帮助你开发出一个高质量的代码。这里我给你**推荐**几篇介绍 Go 语言最佳实践的文章，供你参考：


Effective Go：高效 Go 编程，由 Golang 官方编写，里面包含了编写 Go 代码的一些**建议**，也可以理解为最佳实践。Go Code Review Comments：Golang 官方编写的 Go 最佳实践，作为 Effective Go 的补充。Style guideline for Go packages：包含了如何组织 Go 包、如何命名 Go 包、如何写 Go 包文档的一些**建议**。


#### 代码质量
有了组织合理的代码结构、符合 Go 语言代码规范的 Go 应用代码之后，我们还需要通过一些手段来确保我们开发出的是一个高质量的代码，这可以通过单元测试和 Code Review 来实现。


单元测试非常重要。我们开发完一段代码后，第一个执行的测试就是单元测试。它可以保证我们的代码是符合预期的，一些异常变动能够被及时感知到。进行单元测试，不仅需要编写单元测试用例，还需要我们确保代码是可测试的，以及具有一个高的单元测试覆盖率。


接下来，我就来介绍下如何编写一个可测试的代码。如果我们要对函数 A 进行测试，并且 A 中的所有代码均能够在单元测试环境下按预期被执行，那么函数 A 的代码块就是可测试的。我们来看下一般的单元测试环境有什么特点：

可能无法连接数据库。可能无法访问第三方服务。

如果函数 A 依赖数据库连接、第三方服务，那么在单元测试环境下执行单元测试就会失败，函数就没法测试，函数是不可测的。解决方法也很简单：将依赖的数据库、第三方服务等抽象成接口，在被测代码中调用接口的方法，在测试时传入 mock 类型，从而将数据库、第三方服务等依赖从具体的被测函数中解耦出去。如下图所示：


![img](https://static001.geekbang.org/resource/image/0c/97/0cef423ec1a4f06f6f4715bd0b9f4497.png?wh=1500x438)

为了提高代码的可测性，降低单元测试的复杂度，对 function 和 mock 的要求是：要尽可能减少 function 中的依赖，让 function 只依赖必要的模块。编写一个功能单一、职责分明的函数，会有利于减少依赖。依赖模块应该是易 Mock 的。


为了协助你理解，我们先来看一段不可测试的代码：


```go
package post

import "google.golang.org/grpc"

type Post struct {
  Name    string
  Address string
}

func ListPosts(client *grpc.ClientConn) ([]*Post, error) {
  return client.ListPosts()
}
```
这段代码中的 ListPosts 函数是不可测试的。因为 ListPosts 函数中调用了client.ListPosts()方法，该方法依赖于一个 gRPC 连接。而我们在做单元测试时，可能因为没有配置 gRPC 服务的地址、网络隔离等原因，导致没法建立 gRPC 连接，从而导致 ListPosts 函数执行失败。下面，我们把这段代码改成可测试的，如下：


```go
package main

type Post struct {
  Name    string
  Address string
}

type Service interface {
  ListPosts() ([]*Post, error)
}

func ListPosts(svc Service) ([]*Post, error) {
  return svc.ListPosts()
}
```
上面代码中，ListPosts 函数入参为 Service 接口类型，只要我们传入一个实现了 Service 接口类型的实例，ListPosts 函数即可成功运行。因此，我们可以在单元测试中可以实现一个不依赖任何第三方服务的 fake 实例，并传给 ListPosts。上述可测代码的单元测试代码如下：


```go
package main

import "testing"

type fakeService struct {
}

func NewFakeService() Service {
  return &fakeService{}
}

func (s *fakeService) ListPosts() ([]*Post, error) {
  posts := make([]*Post, 0)
  posts = append(posts, &Post{
    Name:    "colin",
    Address: "Shenzhen",
  })
  posts = append(posts, &Post{
    Name:    "alex",
    Address: "Beijing",
  })
  return posts, nil
}

func TestListPosts(t *testing.T) {
  fake := NewFakeService()
  if _, err := ListPosts(fake); err != nil {
    t.Fatal("list posts failed")
  }
}
```

当我们的代码可测之后，就可以借助一些工具来 Mock 需要的接口了。常用的 Mock 工具，有这么几个：
- golang/mock，是官方提供的 Mock 框架。它实现了基于 interface 的 Mock 功能，能够与 Golang 内置的 testing 包做很好的集成，是最常用的 Mock 工具。golang/mock 提供了 mockgen 工具用来生成 interface 对应的 Mock 源文件。
- sqlmock，可以用来模拟数据库连接。数据库是项目中比较常见的依赖，在遇到数据库依赖时都可以用它。
- httpmock，可以用来 Mock HTTP 请求。
- bouk/monkey，猴子补丁，能够通过替换函数指针的方式来修改任意函数的实现。如果 golang/mock、sqlmock 和 httpmock 这几种方法都不能满足我们的需求，我们可以尝试通过猴子补丁的方式来 Mock 依赖。可以这么说，猴子补丁提供了单元测试 Mock 依赖的最终解决方案。


接下来，我们再一起看看如何提高我们的单元测试覆盖率。当我们编写了可测试的代码之后，接下来就需要编写足够的测试用例，用来提高项目的单元测试覆盖率。这里我有以下两个**建议**供你参考：


使用 gotests 工具自动生成单元测试代码，减少编写单元测试用例的工作量，将你从重复的劳动中解放出来。定期检查单元测试覆盖率。你可以通过以下方法来检查：


```
$ go test -race -cover  -coverprofile=./coverage.out -timeout=10m -short -v ./...
$ go tool cover -func ./coverage.out
```
执行结果如下：


![img](https://static001.geekbang.org/resource/image/d2/89/d2fae0aca602c7c33466411e39c49489.png?wh=2162x412)

在提高项目的单元测试覆盖率时，我们可以先提高单元测试覆盖率低的函数，之后再检查项目的单元测试覆盖率；如果项目的单元测试覆盖率仍然低于期望的值，可以再次提高单元测试覆盖率低的函数的覆盖率，然后再检查。以此循环，最终将项目的单元测试覆盖率优化到预期的值为止。这里要**注意**，对于一些可能经常会变动的函数单元测试，覆盖率要达到 100%。


说完了单元测试，我们再看看如何通过 Code Review 来保证代码质量。Code Review 可以提高代码质量、交叉排查缺陷，并且促进团队内知识共享，是保障代码质量非常有效的手段。在我们的项目开发中，一定要建立一套持久可行的 Code Review 机制。



但在我的研发生涯中，发现很多团队没有建立有效的 Code Review 机制。这些团队都认可 Code Review 机制带来的好处，但是因为流程难以遵守，慢慢地 Code Review 就变成了形式主义，最终不了了之。其实，建立 Code Review 机制很简单，主要有 3 点：


首先，确保我们使用的代码托管平台有 Code Review 的功能。比如，GitHub、GitLab 这类代码托管平台都具备这种能力。接着，建立一套 Code Review 规范，规定如何进行 Code Review。最后，也是最重要的，每次代码变更，相关开发人员都要去落实 Code Review 机制，并形成习惯，直到最后形成团队文化。


**到这里我们可以小结一下：组织一个合理的代码结构、编写符合 Go 代码规范的代码、保证代码质量，在我看来都是编写高质量 Go 代码的外功。那内功是什么呢？就是编程哲学和软件设计方法。**


#### 编程哲学
那编程哲学是什么意思呢？在我看来，编程哲学，其实就是要编写符合 Go 语言设计哲学的代码。Go 语言有很多设计哲学，对代码质量影响比较大的，我认为有两个：面向接口编程和面向“对象”编程。


我们先来看下面向接口编程。Go 接口是一组方法的集合。任何类型，只要实现了该接口中的方法集，那么就属于这个类型，也称为实现了该接口。接口的作用，其实就是**为不同层级的模块提供一个定义好的中间层**。**这样，上游不再需要依赖下游的具体实现，充分地对上下游进行了解耦**。很多流行的 Go 设计模式，就是通过面向接口编程的思想来实现的。


我们看一个面向接口编程的例子。下面这段代码定义了一个Bird接口，Canary 和 Crow 类型均实现了Bird接口。

```go
package main

import "fmt"

// 定义了一个鸟类
type Bird interface {
  Fly()
  Type() string
}

// 鸟类：金丝雀
type Canary struct {
  Name string
}

func (c *Canary) Fly() {
  fmt.Printf("我是%s，用黄色的翅膀飞\n", c.Name)
}
func (c *Canary) Type() string {
  return c.Name
}

// 鸟类：乌鸦
type Crow struct {
  Name string
}

func (c *Crow) Fly() {
  fmt.Printf("我是%s，我用黑色的翅膀飞\n", c.Name)
}

func (c *Crow) Type() string {
  return c.Name
}

// 让鸟类飞一下
func LetItFly(bird Bird) {
  fmt.Printf("Let %s Fly!\n", bird.Type())
  bird.Fly()
}

func main() {
  LetItFly(&Canary{"金丝雀"})
  LetItFly(&Crow{"乌鸦"})
}
```

这段代码中，因为 Crow 和 Canary 都实现了 Bird 接口声明的 Fly、Type 方法，所以可以说 Crow、Canary 实现了 Bird 接口，属于 Bird 类型。在函数调用时，可以传入 Bird 类型，并在函数内部调用 Bird 接口提供的方法，以此来解耦 Bird 的具体实现。好了，我们总结下使用接口的好处吧：



代码扩展性更强了。例如，同样的 Bird，可以有不同的实现。在开发中用的更多的是，将数据库的 CURD 操作抽象成接口，从而可以实现同一份代码对接不同数据库的目的。可以解耦上下游的实现。例如，LetItFly 不用关注 Bird 是如何 Fly 的，只需要调用 Bird 提供的方法即可。提高了代码的可测性。因为接口可以解耦上下游实现，我们在单元测试需要依赖第三方系统 / 数据库的代码时，可以利用接口将具体实现解耦，实现 fake 类型。代码更健壮、更稳定了。例如，如果要更改 Fly 的方式，只需要更改相关类型的 Fly 方法即可，完全影响不到 LetItFly 函数。


所以，**我**建议**你，在 Go 项目开发中，一定要多思考，那些可能有多种实现的地方，要考虑使用接口。**


接下来，我们再来看下面向“对象”编程。面向对象编程（OOP）有很多优点，例如可以使我们的代码变得易维护、易扩展，并能提高开发效率等，所以一个高质量的 Go 应用在需要时，也应该采用面向对象的方法去编程。那什么叫“在需要时”呢？就是我们在开发代码时，**如果一个功能可以通过接近于日常生活和自然的思考方式来实现，这时候就应该考虑使用面向对象的编程方法。**

**Go 语言不支持面向对象编程，但是却可以通过一些语言级的特性来实现类似的效果**。面向对象编程中，有几个核心特性：**类、实例、抽象，封装、继承、多态、构造函数、析构函数、方法重载、this 指针**。在 Go 中可以通过以下几个方式来实现类似的效果：


类、抽象、封装通过结构体来实现。实例通过结构体变量来实现。继承通过组合来实现。这里解释下什么叫组合：一个结构体嵌到另一个结构体，称作组合。例如一个结构体包含了一个匿名结构体，就说这个结构体组合了该匿名结构体。多态通过接口来实现。


至于构造函数、析构函数、方法重载和 this 指针等，Go 为了保持语言的简洁性去掉了这些特性。Go 中面向对象编程方法，见下图：


![img](https://static001.geekbang.org/resource/image/27/b8/27c84757b1f4626e84535d994ca70eb8.png?wh=1872x606)

我们通过一个示例，来具体看下 Go 是如何实现面向对象编程中的类、抽象、封装、继承和多态的。代码如下：


```go
package main

import "fmt"

// 基类：Bird
type Bird struct {
  Type string
}

// 鸟的类别
func (bird *Bird) Class() string {
  return bird.Type
}

// 定义了一个鸟类
type Birds interface {
  Name() string
  Class() string
}

// 鸟类：金丝雀
type Canary struct {
  Bird
  name string
}

func (c *Canary) Name() string {
  return c.name
}

// 鸟类：乌鸦
type Crow struct {
  Bird
  name string
}

func (c *Crow) Name() string {
  return c.name
}

func NewCrow(name string) *Crow {
  return &Crow{
    Bird: Bird{
      Type: "Crow",
    },
    name: name,
  }
}

func NewCanary(name string) *Canary {
  return &Canary{
    Bird: Bird{
      Type: "Canary",
    },
    name: name,
  }
}

func BirdInfo(birds Birds) {
  fmt.Printf("I'm %s, I belong to %s bird class!\n", birds.Name(), birds.Class())
}

func main() {
    canary := NewCanary("CanaryA")
    crow := NewCrow("CrowA")
  BirdInfo(canary)
  BirdInfo(crow)
}
```

将上述代码保存在 oop.go 文件中，执行以下代码输出如下：

```go
$ go run oop.go
I'm CanaryA, I belong to Canary bird class!
I'm CrowA, I belong to Crow bird class!
```
在上面的例子中，分别通过 Canary 和 Crow 结构体定义了金丝雀和乌鸦两种类别的鸟，其中分别封装了 name 属性和 Name 方法。也就是说通过结构体实现了类，该类抽象了鸟类，并封装了该鸟类的属性和方法。

在 Canary 和 Crow 结构体中，都有一个 Bird 匿名字段，Bird 字段为 Canary 和 Crow 类的父类，Canary 和 Crow 继承了 Bird 类的 Class 属性和方法。也就是说通过匿名字段实现了继承。


在 main 函数中，通过 NewCanary 创建了 Canary 鸟类实例，并将其传给 BirdInfo 函数。也就是说通过结构体变量实现实例。


在 BirdInfo 函数中，将 Birds 接口类型作为参数传入，并在函数中调用了 birds.Name，birds.Class 方法，这两个方法会根据 birds 类别的不同而返回不同的名字和类别，也就是说通过接口实现了多态。


### 软件设计方法
接下来，我们继续学习编写高质量 Go 代码的第二项内功，也就是让编写的代码遵循一些业界沉淀下来的，优秀的软件设计方法。

优秀的软件设计方法有很多，其中有两类方法对我们代码质量的提升特别有帮助，分别是**设计模式（Design pattern）和 SOLID 原则**。在我看来，设计模式可以理解为业界针对一些特定的场景总结出来的最佳实现方式。它的特点是**解决的场景比较具体**，实施起来会比较简单；而 SOLID 原则**更侧重设计原则**，需要我们彻底理解，并在编写代码时多思考和落地。


关于设计模式和 SOLID 原则，我是这么安排的：在第 11 讲，我会带你学习 Go 项目常用的设计模式；至于 SOLID 原则，网上已经有很多高质量的文章了，所以我会简单告诉你这个原则是啥，然后给你**推荐**一篇介绍文章。


我们先了解下有哪些设计模式。在软件领域，沉淀了一些比较优秀的设计模式，其中最受欢迎的是 GOF 设计模式。GOF 设计模式中包含了 3 大类（创建型模式、结构型模式、行为型模式），共 25 种经典的、可以解决常见软件设计问题的设计方案。这 25 种设计方案同样也适用于 Go 语言开发的项目。


这里，我将这 25 种设计模式总结成了一张图，你可以先看看，有个大概的印象，对于一些在 Go 项目开发中常用的设计模式，我会在第 11 讲详细介绍。


![img](https://static001.geekbang.org/resource/image/14/9c/1440f4bbcda682c8f5e7a599c8c51f9c.png?wh=2963x1613)
如果说设计模式解决的是具体的场景，那么 SOLID 原则就是我们设计应用代码时的指导方针。SOLID 原则，是由罗伯特·C·马丁在 21 世纪早期引入的，包括了面向对象编程和面向对象设计的五个基本原则：


![img](https://static001.geekbang.org/resource/image/19/3b/19b697bbbe31450d6cc8f222491d3e3b.png?wh=1462x879)


遵循 SOLID 原则可以确保我们设计的代码是易维护、易扩展、易阅读的。SOLID 原则同样也适用于 Go 程序设计。如果你需要更详细地了解 SOLID 原则，可以参考下SOLID 原则介绍这篇文章。



到这里，我们就学完了“编写高质量的 Go 应用”这部分内容。接下来，我们再来学习下如何高效管理 Go 项目，以及如何编写高质量的项目文档。这里面的大部分内容，之前我们都有学习过，因为它们是“如何写出优雅的 Go 项目”的重要组成部分，所以，这里我仍然会简单介绍下它们。


### 高效管理项目
一个优雅的 Go 项目，还需要具备高效的项目管理特性。那么如何高效管理我们的项目呢？

不同团队、不同项目会采用不同的方法来管理项目，在我看来比较重要的有 3 点，分别是制定一个高效的开发流程、使用 Makefile 管理项目和将项目管理自动化。我们可以通过自动生成代码、借助工具、对接 CI/CD 系统等方法来将项目管理自动化。具体见下图：


![img](https://static001.geekbang.org/resource/image/61/6d/61e022c1b25dab2b0fefb407fc1c776d.png?wh=2356x766)

#### 高效的开发流程
高效管理项目的第一步，就是要有一个高效的开发流程，这可以提高开发效率、减少软件维护成本。你可以回想一下设计开发流程的知识，如果印象比较模糊了，一定要回去复习下 08 讲的内容，因为这部分很重要 。


#### 使用 Makefile 管理项目
为了更好地管理项目，除了一个高效的开发流程之外，使用 Makefile 也很重要。Makefile 可以将项目管理的工作通过 Makefile 依赖的方式实现自动化，除了可以提高管理效率之外，还能够减少人为操作带来的失误，并统一操作方式，使项目更加规范。


IAM 项目的所有操作均是通过 Makefile 来完成的，具体 Makefile 完成了如下操作：


```
 build              Build source code for host platform.
  build.multiarch    Build source code for multiple platforms. See option PLATFORMS.
  image              Build docker images for host arch.
  image.multiarch    Build docker images for multiple platforms. See option PLATFORMS.
  push               Build docker images for host arch and push images to registry.
  push.multiarch     Build docker images for multiple platforms and push images to registry.
  deploy             Deploy updated components to development env.
  clean              Remove all files that are created by building.
  lint               Check syntax and styling of go sources.
  test               Run unit test.
  cover              Run unit test and get test coverage.
  release            Release iam
  format             Gofmt (reformat) package sources (exclude vendor dir if existed).
  verify-copyright   Verify the boilerplate headers for all files.
  add-copyright      Ensures source code files have copyright license headers.
  gen                Generate all necessary files, such as error code files.
  ca                 Generate CA files for all iam components.
  install            Install iam system with all its components.
  swagger            Generate swagger document.
  serve-swagger      Serve swagger spec and docs.
  dependencies       Install necessary dependencies.
  tools              install dependent tools.
  check-updates      Check outdated dependencies of the go projects.
  help               Show this help info.
```
#### 自动生成代码
低代码的理念现在越来越流行。虽然低代码有很多缺点，但确实有很多优点，例如：自动化生成代码，减少工作量，提高工作效率。代码有既定的生成规则，相比人工编写代码，准确性更高、更规范。

目前来看，自动生成代码现在已经成为趋势，比如 Kubernetes 项目有很多代码都是自动生成的。我认为，想写出一个优雅的 Go 项目，你也应该认真思考哪些地方的代码可以自动生成。在这门课的 IAM 项目中，就有大量的代码是自动生成的，我放在这里供你参考：

错误码、错误码说明文档。自动生成缺失的 doc.go 文件。利用 gotests 工具，自动生成单元测试用例。使用 Swagger 工具，自动生成 Swagger 文档。使用 Mock 工具，自动生成接口的 Mock 实例。



#### 善于借助工具
在开发 Go 项目的过程中，我们也要善于借助工具，来帮助我们完成一部分工作。利用工具可以带来很多好处：


解放双手，提高工作效率。利用工具的确定性，可以确保执行结果的一致性。例如，使用 golangci-lint 对代码进行检查，可以确保不同开发者开发的代码至少都遵循 golangci-lint 的代码检查规范。有利于实现自动化，可以将工具集成到 CI/CD 流程中，触发流水线自动执行。



那么，Go 项目中，有哪些工具可以为我们所用呢？这里，我给你整理了一些有用的工具：


![img](https://static001.geekbang.org/resource/image/90/80/90ca527c2863fe642f9ab3d5b90fe980.png?wh=1645x1477)
所有这些工具都可以通过下面的方式安装。


```
$ cd $IAM_ROOT
$ make tools.install
```
IAM 项目使用了上面这些工具的绝大部分，用来尽可能提高整个项目的自动化程度，提高项目维护效率。


#### 对接 CI/CD
代码在合并入主干时，应该有一套 CI/CD 流程来自动化地对代码进行检查、编译、单元测试等，只有通过后的代码才可以并入主干。通过 CI/CD 流程来保证代码的质量。当前比较流行的 CI/CD 工具有 Jenkins、GitLab、Argo、Github Actions、JenkinsX 等。在第 51 讲 和 第 52 讲中，我会详细介绍 CI/CD 的原理和实战。



### 编写高质量的项目文档
最后，一个优雅的项目，还应该有完善的文档。例如 README.md、安装文档、开发文档、使用文档、API 接口文档、设计文档等等。这些内容在第 04 讲的文档规范部分有详细介绍，你可以去复习下。

### 总结
使用 Go 语言做项目开发，核心目的其实就是开发一个优雅的 Go 项目。那么如何开发一个优雅的 Go 项目呢？Go 项目包含三大内容，即 Go 应用、项目管理、项目文档，因此开发一个优雅的 Go 项目，其实就是编写高质量的 Go 应用、高效管理项目和编写高质量的项目文档。针对每一项，我都给出了一些实现方式，这些方式详见下图：

![img](https://static001.geekbang.org/resource/image/b0/cc/b051da025c897996473df44693ea4ecc.png?wh=3619x2006)


## 11 | 设计模式：Go常用设计模式概述
在软件开发中，经常会遇到各种各样的编码场景，这些场景往往重复发生，因此具有典型性。针对这些典型场景，我们可以自己编码解决，也可以采取更为省时省力的方式：直接采用设计模式。

设计模式是啥呢？简单来说，就是将软件开发中需要重复性解决的编码场景，按最佳实践的方式抽象成一个模型，模型描述的解决方法就是设计模式。使用设计模式，可以使代码更易于理解，保证代码的重用性和可靠性。


在软件领域，GoF（四人帮，全拼 Gang of Four）首次系统化提出了 3 大类、共 25 种可复用的经典设计方案，来解决常见的软件设计问题，为可复用软件设计奠定了一定的理论基础。从总体上说，这些设计模式可以分为创建型模式、结构型模式、行为型模式 3 大类，用来完成不同的场景。这一讲，我会介绍几个在 Go 项目开发中比较常用的设计模式，帮助你用更加简单快捷的方法应对不同的编码场景。其中，简单工厂模式、抽象工厂模式和工厂方法模式都属于工厂模式，我会把它们放在一起讲解。


![img](https://static001.geekbang.org/resource/image/98/20/98fb0ecb8ba65bc83f25bb2504e51d20.png?wh=3142x1613)

### 创建型模式
首先来看创建型模式（Creational Patterns），它提供了一种在创建对象的同时隐藏创建逻辑的方式，而不是使用 new 运算符直接实例化对象。这种类型的设计模式里，单例模式和工厂模式（具体包括简单工厂模式、抽象工厂模式和工厂方法模式三种）在 Go 项目开发中比较常用。我们先来看单例模式。


#### 单例模式
单例模式（Singleton Pattern），是最简单的一个模式。在 Go 中，单例模式指的是全局只有一个实例，并且它负责创建自己的对象。单例模式不仅有利于减少内存开支，还有减少系统性能开销、防止多个实例产生冲突等优点。因为单例模式保证了实例的全局唯一性，而且只被初始化一次，所以比较适合全局共享一个实例，且只需要被初始化一次的场景，例如数据库实例、全局配置、全局任务池等。单例模式又分为饿汉方式和懒汉方式。饿汉方式指全局的单例实例在包被加载时创建，而懒汉方式指全局的单例实例在第一次被使用时创建。你可以看到，这种命名方式非常形象地体现了它们不同的特点。


接下来，我就来分别介绍下这两种方式。先来看饿汉方式。下面是一个饿汉方式的单例模式代码：


```go
package singleton

type singleton struct {
}

var ins *singleton = &singleton{}

func GetInsOr() *singleton {
    return ins
}
```

你需要**注意**，**因为实例是在包被导入时初始化的，所以如果初始化耗时，会导致程序加载时间比较长。懒汉方式是开源项目中使用最多的，但它的缺点是非并发安全，在实际使用时需要加锁**。以下是懒汉方式不加锁的一个实现：


```go
package singleton

type singleton struct {
}

var ins *singleton

func GetInsOr() *singleton {
    if ins == nil {
        ins = &singleton{}
    }
    
    return ins
}
```
可以看到，在创建 ins 时，如果 ins==nil，就会再创建一个 ins 实例，这时候单例就会有多个实例。为了解决懒汉方式非并发安全的问题，需要对实例进行加锁，**下面是带检查锁的一个实现**：


```go
import "sync"

type singleton struct {
}

var ins *singleton
var mu sync.Mutex

func GetIns() *singleton {
  if ins == nil {
    mu.Lock()
    if ins == nil {
      ins = &singleton{}
    }
    mu.Unlock()
  }
  return ins
}
```
上述代码只有在创建时才会加锁，既提高了代码效率，又保证了并发安全。除了饿汉方式和懒汉方式，在 Go 开发中，还有一种更优雅的实现方式，我**建议**你采用这种方式，代码如下：



```go
package singleton

import (
    "sync"
)

type singleton struct {
}

var ins *singleton
var once sync.Once

func GetInsOr() *singleton {
    once.Do(func() {
        ins = &singleton{}
    })
    return ins
}
```

使用once.Do可以确保 ins 实例全局只被创建一次，once.Do 函数还可以确保当同时有多个创建动作时，只有一个创建动作在被执行。另外，IAM 应用中大量使用了单例模式，如果你想了解更多单例模式的使用方式，可以直接查看 IAM 项目代码。IAM 中单例模式有 GetStoreInsOr、GetEtcdFactoryOr、GetMySQLFactoryOr、GetCacheInsOr等。



#### 工厂模式
工厂模式（Factory Pattern）是面向对象编程中的常用模式。在 Go 项目开发中，你可以通过使用多种不同的工厂模式，来使代码更简洁明了。Go 中的结构体，可以理解为面向对象编程中的类，例如 Person 结构体（类）实现了 Greet 方法。


```go
type Person struct {
  Name string
  Age int
}

func (p Person) Greet() {
  fmt.Printf("Hi! My name is %s", p.Name)
}
```

有了 Person“类”，就可以创建 Person 实例。我们可以通过简单工厂模式、抽象工厂模式、工厂方法模式这三种方式，来创建一个 Person 实例。这三种工厂模式中，简单工厂模式是最常用、最简单的。它就是一个接受一些参数，然后返回 Person 实例的函数：


```go
type Person struct {
  Name string
  Age int
}

func (p Person) Greet() {
  fmt.Printf("Hi! My name is %s", p.Name)
}

func NewPerson(name string, age int) *Person {
  return &Person{
    Name: name,
    Age: age,
  }
}
```
**和p：=＆Person {}这种创建实例的方式相比，简单工厂模式可以确保我们创建的实例具有需要的参数，进而保证实例的方法可以按预期执行**。例如，通过NewPerson创建 Person 实例时，可以确保实例的 name 和 age 属性被设置。再来看**抽象工厂模式，它和简单工厂模式的唯一区别，就是它返回的是接口而不是结构体**。通过返回接口，**可以在你不公开内部实现的情况下，让调用者使用你提供的各种功能**，例如：


```go
type Person interface {
  Greet()
}

type person struct {
  name string
  age int
}

func (p person) Greet() {
  fmt.Printf("Hi! My name is %s", p.name)
}

// Here, NewPerson returns an interface, and not the person struct itself
func NewPerson(name string, age int) Person {
  return person{
    name: name,
    age: age,
  }
}
```

上面这个代码，定义了一个不可导出的结构体person，在通过 NewPerson 创建实例的时候返回的是接口，而不是结构体。通过返回接口，我们还可以实现多个工厂函数，来返回不同的接口实现，例如：


```go
// We define a Doer interface, that has the method signature
// of the `http.Client` structs `Do` method
type Doer interface {
  Do(req *http.Request) (*http.Response, error)
}

// This gives us a regular HTTP client from the `net/http` package
func NewHTTPClient() Doer {
  return &http.Client{}
}

type mockHTTPClient struct{}

func (*mockHTTPClient) Do(req *http.Request) (*http.Response, error) {
  // The `NewRecorder` method of the httptest package gives us
  // a new mock request generator
  res := httptest.NewRecorder()

  // calling the `Result` method gives us
  // the default empty *http.Response object
  return res.Result(), nil
}

// This gives us a mock HTTP client, which returns
// an empty response for any request sent to it
func NewMockHTTPClient() Doer {
  return &mockHTTPClient{}
}
```

NewHTTPClient和NewMockHTTPClient都返回了同一个接口类型 Doer，这使得二者可以互换使用。当你想测试一段调用了 Doer 接口 Do 方法的代码时，这一点特别有用。因为你可以使用一个 Mock 的 HTTP 客户端，从而避免了调用真实外部接口可能带来的失败。来看个例子，假设我们想测试下面这段代码：


```go
func QueryUser(doer Doer) error {
  req, err := http.NewRequest("Get", "http://iam.api.marmotedu.com:8080/v1/secrets", nil)
  if err != nil {
    return err
  }

  _, err := doer.Do(req)
  if err != nil {
    return err
  }

  return nil
}
```

其测试用例为：


```go
func TestQueryUser(t *testing.T) {
  doer := NewMockHTTPClient()
  if err := QueryUser(doer); err != nil {
    t.Errorf("QueryUser failed, err: %v", err)
  }
}
```

另外，在使用简单工厂模式和抽象工厂模式返回实例对象时，都可以返回指针。例如，简单工厂模式可以这样返回实例对象：


```go
return &Person{
  Name: name,
  Age: age
}
```
抽象工厂模式可以这样返回实例对象：



```go
return &person{
  name: name,
  age: age
}
```
在实际开发中，我**建议**返回非指针的实例，因为我们主要是想通过创建实例，调用其提供的方法，而不是对实例做更改。如果需要对实例做更改，可以实现SetXXX的方法。通过返回非指针的实例，可以确保实例的属性，避免属性被意外 / 任意修改。

**在简单工厂模式中，依赖于唯一的工厂对象，如果我们需要实例化一个产品，就要向工厂中传入一个参数，获取对应的对象；如果要增加一种产品，就要在工厂中修改创建产品的函数。这会导致耦合性过高，这时我们就可以使用工厂方法模式。**



在工厂方法模式中，依赖工厂函数，我们可以通过实现工厂函数来创建多种工厂，将对象创建从由一个对象负责所有具体类的实例化，变成由一群子类来负责对具体类的实例化，从而将过程解耦。下面是工厂方法模式的一个代码实现：



```go
type Person struct {
  name string
  age int
}

func NewPersonFactory(age int) func(name string) Person {
  return func(name string) Person {
    return Person{
      name: name,
      age: age,
    }
  }
}
```

然后，我们可以使用此功能来创建具有默认年龄的工厂：


```go
newBaby := NewPersonFactory(1)
baby := newBaby("john")

newTeenager := NewPersonFactory(16)
teen := newTeenager("jill")
```


### 结构型模式
我已经向你介绍了单例模式、工厂模式这两种创建型模式，接下来我们来看结构型模式（Structural Patterns），它的特点是**关注类和对象的组合**。这一类型里，我想详细讲讲策略模式和模板模式。


#### 策略模式
策略模式（Strategy Pattern）定义一组算法，将每个算法都封装起来，并且使它们之间可以互换。


在什么时候，我们需要用到策略模式呢？在项目开发中，我们经常要根据不同的场景，采取不同的措施，也就是不同的策略。比如，假设我们需要对 a、b 这两个整数进行计算，根据条件的不同，需要执行不同的计算方式。**我们可以把所有的操作都封装在同一个函数中，然后通过 if ... else ... 的形式来调用不同的计算方式**，这种方式称之为**硬编码**。


在实际应用中，随着功能和体验的不断增长，我们需要经常添加 / 修改策略，这样就需要不断修改已有代码，不仅会让这个函数越来越难维护，还可能因为修改带来一些 bug。所以为了解耦，需要使用策略模式，定义一些独立的类来封装不同的算法，每一个类封装一个具体的算法（即策略）。下面是一个实现策略模式的代码：


```go
package strategy

// 策略模式

// 定义一个策略类
type IStrategy interface {
  do(int, int) int
}

// 策略实现：加
type add struct{}

func (*add) do(a, b int) int {
  return a + b
}

// 策略实现：减
type reduce struct{}

func (*reduce) do(a, b int) int {
  return a - b
}

// 具体策略的执行者
type Operator struct {
  strategy IStrategy
}

// 设置策略
func (operator *Operator) setStrategy(strategy IStrategy) {
  operator.strategy = strategy
}

// 调用策略中的方法
func (operator *Operator) calculate(a, b int) int {
  return operator.strategy.do(a, b)
}
```
在上述代码中，我们定义了策略接口 IStrategy，还定义了 add 和 reduce 两种策略。最后定义了一个策略执行者，可以设置不同的策略，并执行，例如：



```go
func TestStrategy(t *testing.T) {
  operator := Operator{}

  operator.setStrategy(&add{})
  result := operator.calculate(1, 2)
  fmt.Println("add:", result)

  operator.setStrategy(&reduce{})
  result = operator.calculate(2, 1)
  fmt.Println("reduce:", result)
}
```
可以看到，我们可以随意更换策略，而不影响 Operator 的所有实现。



#### 模版模式
模版模式 (Template Pattern) 定义一个操作中算法的骨架，而将一些步骤延迟到子类中。这种方法让子类在不改变一个算法结构的情况下，就能重新定义该算法的某些特定步骤。


简单来说，模板模式就是将一个类中能够公共使用的方法放置在抽象类中实现，将不能公共使用的方法作为抽象方法，强制子类去实现，这样就做到了将一个类作为一个模板，让开发者去填充需要填充的地方。以下是模板模式的一个实现：



```go
package template

import "fmt"

type Cooker interface {
  fire()
  cooke()
  outfire()
}

// 类似于一个抽象类
type CookMenu struct {
}

func (CookMenu) fire() {
  fmt.Println("开火")
}

// 做菜，交给具体的子类实现
func (CookMenu) cooke() {
}

func (CookMenu) outfire() {
  fmt.Println("关火")
}

// 封装具体步骤
func doCook(cook Cooker) {
  cook.fire()
  cook.cooke()
  cook.outfire()
}

type XiHongShi struct {
  CookMenu
}

func (*XiHongShi) cooke() {
  fmt.Println("做西红柿")
}

type ChaoJiDan struct {
  CookMenu
}

func (ChaoJiDan) cooke() {
  fmt.Println("做炒鸡蛋")
}
```
这里来看下测试用例：


```go
func TestTemplate(t *testing.T) {
  // 做西红柿
  xihongshi := &XiHongShi{}
  doCook(xihongshi)

  fmt.Println("\n=====> 做另外一道菜")
  // 做炒鸡蛋
  chaojidan := &ChaoJiDan{}
  doCook(chaojidan)

}

```

### 行为型模式
然后，让我们来看最后一个类别，行为型模式（Behavioral Patterns），它的特点是关注对象之间的通信。这一类别的设计模式中，我们会讲到代理模式和选项模式。



#### 代理模式
代理模式 (Proxy Pattern)，可以为另一个对象提供一个替身或者占位符，以控制对这个对象的访问。以下代码是一个代理模式的实现：


```go
package proxy

import "fmt"

type Seller interface {
  sell(name string)
}

// 火车站
type Station struct {
  stock int //库存
}

func (station *Station) sell(name string) {
  if station.stock > 0 {
    station.stock--
    fmt.Printf("代理点中：%s买了一张票,剩余：%d \n", name, station.stock)
  } else {
    fmt.Println("票已售空")
  }

}

// 火车代理点
type StationProxy struct {
  station *Station // 持有一个火车站对象
}

func (proxy *StationProxy) sell(name string) {
  if proxy.station.stock > 0 {
    proxy.station.stock--
    fmt.Printf("代理点中：%s买了一张票,剩余：%d \n", name, proxy.station.stock)
  } else {
    fmt.Println("票已售空")
  }
}
```
上述代码中，StationProxy 代理了 Station，代理类中持有被代理类对象，并且和被代理类对象实现了同一接口。


#### 选项模式
选项模式（Options Pattern）也是 Go 项目开发中经常使用到的模式，例如，grpc/grpc-go 的NewServer函数，uber-go/zap 包的New函数都用到了选项模式。使用选项模式，我们可以创建一个带有默认值的 struct 变量，并选择性地修改其中一些参数的值。


在 Python 语言中，创建一个对象时，可以给参数设置默认值，这样在不传入任何参数时，可以返回携带默认值的对象，并在需要时修改对象的属性。这种特性可以大大简化开发者创建一个对象的成本，尤其是在对象拥有众多属性时。


而在 Go 语言中，因为不支持给参数设置默认值，为了既能够创建带默认值的实例，又能够创建自定义参数的实例，不少开发者会通过以下两种方法来实现：

第一种方法，我们要分别开发两个用来创建实例的函数，一个可以创建带默认值的实例，一个可以定制化创建实例。



```go
package options

import (
  "time"
)

const (
  defaultTimeout = 10
  defaultCaching = false
)

type Connection struct {
  addr    string
  cache   bool
  timeout time.Duration
}

// NewConnect creates a connection.
func NewConnect(addr string) (*Connection, error) {
  return &Connection{
    addr:    addr,
    cache:   defaultCaching,
    timeout: defaultTimeout,
  }, nil
}

// NewConnectWithOptions creates a connection with options.
func NewConnectWithOptions(addr string, cache bool, timeout time.Duration) (*Connection, error) {
  return &Connection{
    addr:    addr,
    cache:   cache,
    timeout: timeout,
  }, nil
}
```
使用这种方式，创建同一个 Connection 实例，却要实现两个不同的函数，实现方式很不优雅。另外一种方法相对优雅些。我们需要创建一个带默认值的选项，并用该选项创建实例：


```go
package options

import (
  "time"
)

const (
  defaultTimeout = 10
  defaultCaching = false
)

type Connection struct {
  addr    string
  cache   bool
  timeout time.Duration
}

type ConnectionOptions struct {
  Caching bool
  Timeout time.Duration
}

func NewDefaultOptions() *ConnectionOptions {
  return &ConnectionOptions{
    Caching: defaultCaching,
    Timeout: defaultTimeout,
  }
}

// NewConnect creates a connection with options.
func NewConnect(addr string, opts *ConnectionOptions) (*Connection, error) {
  return &Connection{
    addr:    addr,
    cache:   opts.Caching,
    timeout: opts.Timeout,
  }, nil
}

```
使用这种方式，虽然只需要实现一个函数来创建实例，但是也有缺点：为了创建 Connection 实例，每次我们都要创建 ConnectionOptions，操作起来比较麻烦。那么有没有更优雅的解决方法呢？答案当然是有的，就是使用选项模式来创建实例。以下代码通过选项模式实现上述功能：



```go
package options

import (
  "time"
)

type Connection struct {
  addr    string
  cache   bool
  timeout time.Duration
}

const (
  defaultTimeout = 10
  defaultCaching = false
)

type options struct {
  timeout time.Duration
  caching bool
}

// Option overrides behavior of Connect.
type Option interface {
  apply(*options)
}

type optionFunc func(*options)

func (f optionFunc) apply(o *options) {
  f(o)
}

func WithTimeout(t time.Duration) Option {
  return optionFunc(func(o *options) {
    o.timeout = t
  })
}

func WithCaching(cache bool) Option {
  return optionFunc(func(o *options) {
    o.caching = cache
  })
}

// Connect creates a connection.
func NewConnect(addr string, opts ...Option) (*Connection, error) {
  options := options{
    timeout: defaultTimeout,
    caching: defaultCaching,
  }

  for _, o := range opts {
    o.apply(&options)
  }

  return &Connection{
    addr:    addr,
    cache:   options.caching,
    timeout: options.timeout,
  }, nil
}
```
在上面的代码中，首先我们定义了options结构体，它携带了 timeout、caching 两个属性。接下来，我们通过NewConnect创建了一个连接，NewConnect函数中先创建了一个带有默认值的options结构体变量，并通过调用


```go
for _, o := range opts {
    o.apply(&options)
}
```
来修改所创建的options结构体变量。


需要修改的属性，是在NewConnect时，通过 Option 类型的选项参数传递进来的。可以通过WithXXX函数来创建 Option 类型的选项参数：WithTimeout、WithCaching。Option 类型的选项参数需要实现apply(*options)函数，结合 WithTimeout、WithCaching 函数的返回值和 optionFunc 的 apply 方法实现，可以知道o.apply(&options)其实就是把 WithTimeout、WithCaching 传入的参数赋值给 options 结构体变量，以此动态地设置 options 结构体变量的属性。


这里还有一个好处：我们可以在 apply 函数中自定义赋值逻辑，例如o.timeout = 100 * t。通过这种方式，我们会有更大的灵活性来设置结构体的属性。选项模式有很多优点，例如：支持传递多个参数，并且在参数发生变化时保持兼容性；支持任意顺序传递参数；支持默认值；方便扩展；通过 WithXXX 的函数命名，可以使参数意义更加明确，等等。


不过，为了实现选项模式，我们增加了很多代码，所以在开发中，要根据实际场景选择是否使用选项模式。选项模式通常适用于以下场景：


结构体参数很多，创建结构体时，我们期望创建一个携带默认值的结构体变量，并选择性修改其中一些参数的值。结构体参数经常变动，变动时我们又不想修改创建实例的函数。例如：结构体新增一个 retry 参数，但是又不想在 NewConnect 入参列表中添加retry int这样的参数声明。


如果结构体参数比较少，可以慎重考虑要不要采用选项模式。


### 总结
设计模式，是业界沉淀下来的针对特定场景的最佳解决方案。在软件领域，GoF 首次系统化提出了 3 大类设计模式：创建型模式、结构型模式、行为型模式。这一讲，我介绍了 Go 项目开发中 6 种常用的设计模式。每种设计模式解决某一类场景，我给你总结成了一张表格，你可以根据自己的需要进行选择。


![img](https://static001.geekbang.org/resource/image/1e/01/1e32f9d8318c8968b50e9ea7e89bbe01.png?wh=1455x1015)










