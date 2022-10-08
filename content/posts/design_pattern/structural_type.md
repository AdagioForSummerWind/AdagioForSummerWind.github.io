---
title: "Structural_type"
date: 2022-01-22T09:21:00+08:00
lastmod: 2022-01-22
tags: [design pattern]
categories: [Coding]
slug: DM structural type
draft: false
---
结构型设计模式
常用的有：代理模式、桥接模式、装饰者模式、适配器模式。不常用的有：门面模式、组合模式、享元模式。



代理模式：代理模式在不改变原始类接口的条件下，为原始类定义一个代理类，主要目的是控制访问，而非加强功能，这是它跟装饰器模式最大的不同。
桥接模式：桥接模式的目的是将接口部分和实现部分分离，从而让它们可以较为容易、也相对独立地加以改变。
装饰器模式：装饰者模式在不改变原始类接口的情况下，对原始类功能进行增强，并且支持多个装饰器的嵌套使用。
适配器模式：适配器模式是一种事后的补救策略。适配器提供跟原始类不同的接口，而代理模式、装饰器模式提供的都是跟原始类相同的接口。
## adapter
适配器模式的英文翻译是 Adapter Design Pattern。顾名思义，这个模式就是用来做适配的，它将不兼容的接口转换为可兼容的接口，让原本由于接口不兼容而不能一起工作的类可以一起工作。对于这个模式，有一个经常被拿来解释它的例子，就是 USB 转接头充当适配器，把两种不兼容的接口，通过转接变得可以一起工作。

适配器模式用于转换一种接口适配另一种接口。

实际使用中Adaptee一般为接口，并且使用工厂函数生成实例。

在Adapter中匿名组合Adaptee接口，所以Adapter类也拥有SpecificRequest实例方法，又因为Go语言中非入侵式接口特征，其实Adapter也适配Adaptee接口。

两种实现方式，类适配器和对象适配器
其中，类适配器使用继承关系来实现，对象适配器使用组合关系来实现。

一般来说，适配器模式可以看作一种“补偿模式”，用来补救设计上的缺陷。应用这种模式算是“无奈之举”。如果在设计初期，我们就能协调规避接口不兼容的问题，那这种模式就没有应用的机会了。

1. 封装有缺陷的接口设计 假设我们依赖的外部系统在接口设计方面有缺陷（比如包含大量静态方法），引入之后会影响到我们自身代码的可测试性。为了隔离设计上的缺陷，我们希望对外部系统提供的接口进行二次封装，抽象出更好的接口设计，这个时候就可以使用适配器模式了。

2. 统一多个类的接口设计 某个功能的实现依赖多个外部系统（或者说类）。通过适配器模式，将它们的接口适配为统一的接口定义，然后我们就可以使用多态的特性来复用代码逻辑。

3. 替换依赖的外部系统 当我们把项目中依赖的一个外部系统替换为另一个外部系统的时候，利用适配器模式，可以减少对代码的改动。


4. 兼容老版本接口 在做版本升级的时候，对于一些要废弃的接口，我们不直接将其删除，而是暂时保留，并且标注为 deprecated，并将内部实现逻辑委托为新的接口实现。这样做的好处是，让使用它的项目有个过渡期，而不是强制进行代码修改。这也可以粗略地看作适配器模式的一个应用场景。


5. 适配不同格式的数据 前面我们讲到，适配器模式主要用于接口的适配，实际上，它还可以用在不同格式的数据之间的适配。比如，把从不同征信系统拉取的不同格式的征信数据，统一为相同的格式，以方便存储和使用。再比如，Java 中的 Arrays.asList() 也可以看作一种数据适配器，将数组类型的数据转化为集合容器类型。






```go
package adapter

//Target 是适配的目标接口
type Target interface {
	Request() string
}

//Adaptee 是被适配的目标接口
type Adaptee interface {
	SpecificRequest() string
}

//NewAdaptee 是被适配接口的工厂函数
func NewAdaptee() Adaptee {
	return &adapteeImpl{}
}

//AdapteeImpl 是被适配的目标类
type adapteeImpl struct{}

//SpecificRequest 是目标类的一个方法
func (*adapteeImpl) SpecificRequest() string {
	return "adaptee method"
}

//NewAdapter 是Adapter的工厂函数
func NewAdapter(adaptee Adaptee) Target {
	return &adapter{
		Adaptee: adaptee,
	}
}

//Adapter 是转换Adaptee为Target接口的适配器
type adapter struct {
	Adaptee
}

//Request 实现Target接口
func (a *adapter) Request() string {
	return a.SpecificRequest()
}
```
```go
package adapter

import "testing"

var expect = "adaptee method"

func TestAdapter(t *testing.T) {
	adaptee := NewAdaptee()
	target := NewAdapter(adaptee)
	res := target.Request()
	if res != expect {
		t.Fatalf("expect: %s, actual: %s", expect, res)
	}
}
```
## proxy
代理模式用于延迟处理操作或者在进行实际操作前后进行其它处理。

代理模式的常见用法有
- 虚代理
- COW代理
- 远程代理
- 保护代理
- Cache 代理
- 防火墙代理
- 同步代理
- 智能指引
- 等。。。

```go
package proxy

type Subject interface {
	Do() string
}

type RealSubject struct{}

func (RealSubject) Do() string {
	return "real"
}

type Proxy struct {
	real RealSubject
}

func (p Proxy) Do() string {
	var res string

	// 在调用真实对象之前的工作，检查缓存，判断权限，实例化真实对象等。。
	res += "pre:"

	// 调用真实对象
	res += p.real.Do()

	// 调用之后的操作，如缓存结果，对结果进行处理等。。
	res += ":after"

	return res
}
```
```go
package proxy

import "testing"

func TestProxy(t *testing.T) {
	var sub Subject
	sub = &Proxy{}

	res := sub.Do()

	if res != "pre:real:after" {
		t.Fail()
	}
}
```
### 代理在RPC、缓存、监控等场景中的应用

#### 代理模式的原理解析
代理模式（Proxy Design Pattern）的原理和代码实现都不难掌握。它在不改变原始类（或叫被代理类）代码的情况下，通过引入代理类来给原始类附加功能。


#### 动态代理的原理解析

所谓动态代理（Dynamic Proxy），就是我们不事先为每个原始类编写代理类，而是在运行的时候，动态地创建原始类对应的代理类，然后在系统中用代理类替换掉原始类。


#### 代理模式的应用场景
代理模式的应用场景非常多，我这里列举一些比较常见的用法，希望你能举一反三地应用在你的项目开发中。

1. 业务系统的非功能性需求开发代理模式最常用的一个应用场景就是，在业务系统中开发一些非功能性需求，比如：监控、统计、鉴权、限流、事务、幂等、日志。我们将这些附加功能与业务功能解耦，放到代理类中统一处理，让程序员只需要关注业务方面的开发。实际上，前面举的搜集接口请求信息的例子，就是这个应用场景的一个典型例子。如果你熟悉 Java 语言和 Spring 开发框架，这部分工作都是可以在 Spring AOP 切面中完成的。前面我们也提到，Spring AOP 底层的实现原理就是基于动态代理。


2. 代理模式在 RPC、缓存中的应用实际上，RPC 框架也可以看作一种代理模式，GoF 的《设计模式》一书中把它称作远程代理。通过远程代理，将网络通信、数据编解码等细节隐藏起来。客户端在使用 RPC 服务的时候，就像使用本地函数一样，无需了解跟服务器交互的细节。除此之外，RPC 服务的开发者也只需要开发业务逻辑，就像开发本地使用的函数一样，不需要关注跟客户端的交互细节。关于远程代理的代码示例，我自己实现了一个简单的 RPC 框架 Demo，放到了 GitHub 中，你可以点击这里的[链接](https://github.com/wangzheng0822/codedesign/tree/master/com/xzg/cd/rpc)查看。

我们再来看代理模式在缓存中的应用。假设我们要开发一个接口请求的缓存功能，对于某些接口请求，如果入参相同，在设定的过期时间内，直接返回缓存结果，而不用重新进行逻辑处理。比如，针对获取用户个人信息的需求，我们可以开发两个接口，一个支持缓存，一个支持实时查询。对于需要实时数据的需求，我们让其调用实时查询接口，对于不需要实时数据的需求，我们让其调用支持缓存的接口。那如何来实现接口请求的缓存功能呢？最简单的实现方法就是刚刚我们讲到的，给每个需要支持缓存的查询需求都开发两个不同的接口，一个支持缓存，一个支持实时查询。但是，这样做显然增加了开发成本，而且会让代码看起来非常臃肿（接口个数成倍增加），也不方便缓存接口的集中管理（增加、删除缓存接口）、集中配置（比如配置每个接口缓存过期时间）。针对这些问题，代理模式就能派上用场了，确切地说，应该是动态代理。如果是基于 Spring 框架来开发的话，那就可以在 AOP 切面中完成接口缓存的功能。在应用启动的时候，我们从配置文件中加载需要支持缓存的接口，以及相应的缓存策略（比如过期时间）等。当请求到来的时候，我们在 AOP 切面中拦截请求，如果请求中带有支持缓存的字段（比如 http://…?..&cached=true），我们便从缓存（内存缓存或者 Redis 缓存等）中获取数据直接返回。




## decorator
装饰模式使用对象组合的方式动态改变或增加对象行为。

Go语言借助于匿名组合和非入侵式接口可以很方便实现装饰模式。

使用匿名组合，在装饰器中不必显式定义转调原对象方法。

装饰器模式主要解决继承关系过于复杂的问题，通过组合来替代继承。它主要的作用是给原始类添加增强功能。这也是判断是否该用装饰器模式的一个重要的依据。除此之外，装饰器模式还有一个特点，那就是可以对原始类嵌套使用多个装饰器。为了满足这个应用场景，在设计的时候，装饰器类需要跟原始类继承相同的抽象类或者接口。
```go
package decorator

type Component interface {
	Calc() int
}

type ConcreteComponent struct{}

func (*ConcreteComponent) Calc() int {
	return 0
}

type MulDecorator struct {
	Component
	num int
}

func WarpMulDecorator(c Component, num int) Component {
	return &MulDecorator{
		Component: c,
		num:       num,
	}
}

func (d *MulDecorator) Calc() int {
	return d.Component.Calc() * d.num
}

type AddDecorator struct {
	Component
	num int
}

func WarpAddDecorator(c Component, num int) Component {
	return &AddDecorator{
		Component: c,
		num:       num,
	}
}

func (d *AddDecorator) Calc() int {
	return d.Component.Calc() + d.num
}
```
```go
package decorator

import "fmt"

func ExampleDecorator() {
	var c Component = &ConcreteComponent{}
	c = WarpAddDecorator(c, 10)
	c = WarpMulDecorator(c, 8)
	res := c.Calc()

	fmt.Printf("res %d\n", res)
	// Output:
	// res 80
}
```
## bridge
桥接模式分离抽象部分和实现部分。使得两部分独立扩展。

桥接模式类似于策略模式，区别在于策略模式封装一系列算法使得算法可以互相替换。

策略模式使抽象部分和实现部分分离，可以独立变化。

桥接模式有两种理解方式。第一种理解方式是“将抽象和实现解耦，让它们能独立开发”。这种理解方式比较特别，应用场景也不多。另一种理解方式更加简单，类似“组合优于继承”设计原则，这种理解方式更加通用，应用场景比较多。不管是哪种理解方式，它们的代码结构都是相同的，都是一种类之间的组合关系。
```go
package bridge

import "fmt"

type AbstractMessage interface {
	SendMessage(text, to string)
}

type MessageImplementer interface {
	Send(text, to string)
}

type MessageSMS struct{}

func ViaSMS() MessageImplementer {
	return &MessageSMS{}
}

func (*MessageSMS) Send(text, to string) {
	fmt.Printf("send %s to %s via SMS", text, to)
}

type MessageEmail struct{}

func ViaEmail() MessageImplementer {
	return &MessageEmail{}
}

func (*MessageEmail) Send(text, to string) {
	fmt.Printf("send %s to %s via Email", text, to)
}

type CommonMessage struct {
	method MessageImplementer
}

func NewCommonMessage(method MessageImplementer) *CommonMessage {
	return &CommonMessage{
		method: method,
	}
}

func (m *CommonMessage) SendMessage(text, to string) {
	m.method.Send(text, to)
}

type UrgencyMessage struct {
	method MessageImplementer
}

func NewUrgencyMessage(method MessageImplementer) *UrgencyMessage {
	return &UrgencyMessage{
		method: method,
	}
}

func (m *UrgencyMessage) SendMessage(text, to string) {
	m.method.Send(fmt.Sprintf("[Urgency] %s", text), to)
}
```
```go
package bridge

func ExampleCommonSMS() {
	m := NewCommonMessage(ViaSMS())
	m.SendMessage("have a drink?", "bob")
	// Output:
	// send have a drink? to bob via SMS
}

func ExampleCommonEmail() {
	m := NewCommonMessage(ViaEmail())
	m.SendMessage("have a drink?", "bob")
	// Output:
	// send have a drink? to bob via Email
}

func ExampleUrgencySMS() {
	m := NewUrgencyMessage(ViaSMS())
	m.SendMessage("have a drink?", "bob")
	// Output:
	// send [Urgency] have a drink? to bob via SMS
}

func ExampleUrgencyEmail() {
	m := NewUrgencyMessage(ViaEmail())
	m.SendMessage("have a drink?", "bob")
	// Output:
	// send [Urgency] have a drink? to bob via Email
}
```
### 如何实现支持不同类型和渠道的消息推送系统？
相对于代理模式来说，桥接模式在实际的项目中并没有那么常用，你只需要简单了解

#### 桥接模式的原理解析
桥接模式，也叫作桥梁模式，英文是 Bridge Design Pattern。这个模式可以说是 23 种设计模式中最难理解的模式之一了。我查阅了比较多的书籍和资料之后发现，对于这个模式有两种不同的理解方式。当然，这其中“最纯正”的理解方式，当属 GoF 的《设计模式》一书中对桥接模式的定义。毕竟，这 23 种经典的设计模式，最初就是由这本书总结出来的。在 GoF 的《设计模式》一书中，桥接模式是这么定义的：“Decouple an abstraction from its implementation so that the two can vary independently。”翻译成中文就是：“将抽象和实现解耦，让它们可以独立变化。”关于桥接模式，很多书籍、资料中，还有另外一种理解方式：“一个类存在两个（或多个）独立变化的维度，我们通过组合的方式，让这两个（或多个）维度可以独立进行扩展。”通过组合关系来替代继承关系，避免继承层次的指数级爆炸。这种理解方式非常类似于，我们之前讲过的“组合优于继承”设计原则，所以，这里我就不多解释了。我们重点看下 GoF 的理解方式。GoF 给出的定义非常的简短，单凭这一句话，估计没几个人能看懂是什么意思。所以，我们通过 JDBC 驱动的例子来解释一下。JDBC 驱动是桥接模式的经典应用。我们先来看一下，如何利用 JDBC 驱动来查询数据库。具体的代码如下所示：

```
Class.forName("com.mysql.jdbc.Driver");//加载及注册JDBC驱动程序
String url = "jdbc:mysql://localhost:3306/sample_db?user=root&password=your_password";
Connection con = DriverManager.getConnection(url);
Statement stmt = con.createStatement()；
String query = "select * from test";
ResultSet rs=stmt.executeQuery(query);
while(rs.next()) {
  rs.getString(1);
  rs.getInt(2);
}
```
如果我们想要把 MySQL 数据库换成 Oracle 数据库，只需要把第一行代码中的 com.mysql.jdbc.Driver 换成 oracle.jdbc.driver.OracleDriver 就可以了。当然，也有更灵活的实现方式，我们可以把需要加载的 Driver 类写到配置文件中，当程序启动的时候，自动从配置文件中加载，这样在切换数据库的时候，我们都不需要修改代码，只需要修改配置文件就可以了。不管是改代码还是改配置，在项目中，从一个数据库切换到另一种数据库，都只需要改动很少的代码，或者完全不需要改动代码，那如此优雅的数据库切换是如何实现的呢？源码之下无秘密。要弄清楚这个问题，我们先从 com.mysql.jdbc.Driver 这个类的代码看起。我摘抄了部分相关代码，放到了这里，你可以看一下。


```
java
package com.mysql.jdbc;
import java.sql.SQLException;

public class Driver extends NonRegisteringDriver implements java.sql.Driver {
  static {
    try {
      java.sql.DriverManager.registerDriver(new Driver());
    } catch (SQLException E) {
      throw new RuntimeException("Can't register driver!");
    }
  }

  /**
   * Construct a new driver and register it with DriverManager
   * @throws SQLException if a database error occurs.
   */
  public Driver() throws SQLException {
    // Required for Class.forName().newInstance()
  }
}
```
结合 com.mysql.jdbc.Driver 的代码实现，我们可以发现，当执行 Class.forName(“com.mysql.jdbc.Driver”) 这条语句的时候，实际上是做了两件事情。第一件事情是要求 JVM 查找并加载指定的 Driver 类，第二件事情是执行该类的静态代码，也就是将 MySQL Driver 注册到 DriverManager 类中。现在，我们再来看一下，DriverManager 类是干什么用的。具体的代码如下所示。当我们把具体的 Driver 实现类（比如，com.mysql.jdbc.Driver）注册到 DriverManager 之后，后续所有对 JDBC 接口的调用，都会委派到对具体的 Driver 实现类来执行。而 Driver 实现类都实现了相同的接口（java.sql.Driver ），这也是可以灵活切换 Driver 的原因。


```
java
public class DriverManager {
  private final static CopyOnWriteArrayList<DriverInfo> registeredDrivers = new CopyOnWriteArrayList<DriverInfo>();

  //...
  static {
    loadInitialDrivers();
    println("JDBC DriverManager initialized");
  }
  //...

  public static synchronized void registerDriver(java.sql.Driver driver) throws SQLException {
    if (driver != null) {
      registeredDrivers.addIfAbsent(new DriverInfo(driver));
    } else {
      throw new NullPointerException();
    }
  }

  public static Connection getConnection(String url, String user, String password) throws SQLException {
    java.util.Properties info = new java.util.Properties();
    if (user != null) {
      info.put("user", user);
    }
    if (password != null) {
      info.put("password", password);
    }
    return (getConnection(url, info, Reflection.getCallerClass()));
  }
  //...
}
```
桥接模式的定义是“将抽象和实现解耦，让它们可以独立变化”。那弄懂定义中“抽象”和“实现”两个概念，就是理解桥接模式的关键。那在 JDBC 这个例子中，什么是“抽象”？什么是“实现”呢？实际上，JDBC 本身就相当于“抽象”。注意，这里所说的“抽象”，指的并非“抽象类”或“接口”，而是跟具体的数据库无关的、被抽象出来的一套“类库”。具体的 Driver（比如，com.mysql.jdbc.Driver）就相当于“实现”。注意，这里所说的“实现”，也并非指“接口的实现类”，而是跟具体数据库相关的一套“类库”。JDBC 和 Driver 独立开发，通过对象之间的组合关系，组装在一起。JDBC 的所有逻辑操作，最终都委托给 Driver 来执行。我画了一张图帮助你理解，你可以结合着我刚才的讲解一块看。

![img](https://static001.geekbang.org/resource/image/81/40/812234b0717043a67c2d62ea8e783b40.jpg?wh=2136*1221)


#### 桥接模式的应用举例
一个 API 接口监控告警的例子：根据不同的告警规则，触发不同类型的告警。告警支持多种通知渠道，包括：邮件、短信、微信、自动语音电话。通知的紧急程度有多种类型，包括：SEVERE（严重）、URGENCY（紧急）、NORMAL（普通）、TRIVIAL（无关紧要）。不同的紧急程度对应不同的通知渠道。比如，SERVE（严重）级别的消息会通过“自动语音电话”告知相关人员。在当时的代码实现中，关于发送告警信息那部分代码，我们只给出了粗略的设计，现在我们来一块实现一下。我们先来看最简单、最直接的一种实现方式。代码如下所示：

```
java
public enum NotificationEmergencyLevel {
  SEVERE, URGENCY, NORMAL, TRIVIAL
}

public class Notification {
  private List<String> emailAddresses;
  private List<String> telephones;
  private List<String> wechatIds;

  public Notification() {}

  public void setEmailAddress(List<String> emailAddress) {
    this.emailAddresses = emailAddress;
  }

  public void setTelephones(List<String> telephones) {
    this.telephones = telephones;
  }

  public void setWechatIds(List<String> wechatIds) {
    this.wechatIds = wechatIds;
  }

  public void notify(NotificationEmergencyLevel level, String message) {
    if (level.equals(NotificationEmergencyLevel.SEVERE)) {
      //...自动语音电话
    } else if (level.equals(NotificationEmergencyLevel.URGENCY)) {
      //...发微信
    } else if (level.equals(NotificationEmergencyLevel.NORMAL)) {
      //...发邮件
    } else if (level.equals(NotificationEmergencyLevel.TRIVIAL)) {
      //...发邮件
    }
  }
}

//在API监控告警的例子中，我们如下方式来使用Notification类：
public class ErrorAlertHandler extends AlertHandler {
  public ErrorAlertHandler(AlertRule rule, Notification notification){
    super(rule, notification);
  }


  @Override
  public void check(ApiStatInfo apiStatInfo) {
    if (apiStatInfo.getErrorCount() > rule.getMatchedRule(apiStatInfo.getApi()).getMaxErrorCount()) {
      notification.notify(NotificationEmergencyLevel.SEVERE, "...");
    }
  }
}
```
Notification 类的代码实现有一个最明显的问题，那就是有很多 if-else 分支逻辑。实际上，如果每个分支中的代码都不复杂，后期也没有无限膨胀的可能（增加更多 if-else 分支判断），那这样的设计问题并不大，没必要非得一定要摒弃 if-else 分支逻辑。不过，Notification 的代码显然不符合这个条件。因为每个 if-else 分支中的代码逻辑都比较复杂，发送通知的所有逻辑都扎堆在 Notification 类中。我们知道，类的代码越多，就越难读懂，越难修改，维护的成本也就越高。很多设计模式都是试图将庞大的类拆分成更细小的类，然后再通过某种更合理的结构组装在一起。针对 Notification 的代码，我们将不同渠道的发送逻辑剥离出来，形成独立的消息发送类（MsgSender 相关类）。其中，Notification 类相当于抽象，MsgSender 类相当于实现，两者可以独立开发，通过组合关系（也就是桥梁）任意组合在一起。所谓任意组合的意思就是，不同紧急程度的消息和发送渠道之间的对应关系，不是在代码中固定写死的，我们可以动态地去指定（比如，通过读取配置来获取对应关系）。按照这个设计思路，我们对代码进行重构。重构之后的代码如下所示：
```
java
public interface MsgSender {
  void send(String message);
}

public class TelephoneMsgSender implements MsgSender {
  private List<String> telephones;

  public TelephoneMsgSender(List<String> telephones) {
    this.telephones = telephones;
  }

  @Override
  public void send(String message) {
    //...
  }

}

public class EmailMsgSender implements MsgSender {
  // 与TelephoneMsgSender代码结构类似，所以省略...
}

public class WechatMsgSender implements MsgSender {
  // 与TelephoneMsgSender代码结构类似，所以省略...
}

public abstract class Notification {
  protected MsgSender msgSender;

  public Notification(MsgSender msgSender) {
    this.msgSender = msgSender;
  }

  public abstract void notify(String message);
}

public class SevereNotification extends Notification {
  public SevereNotification(MsgSender msgSender) {
    super(msgSender);
  }

  @Override
  public void notify(String message) {
    msgSender.send(message);
  }
}

public class UrgencyNotification extends Notification {
  // 与SevereNotification代码结构类似，所以省略...
}
public class NormalNotification extends Notification {
  // 与SevereNotification代码结构类似，所以省略...
}
public class TrivialNotification extends Notification {
  // 与SevereNotification代码结构类似，所以省略...
}
```
#### 总结
总体上来讲，桥接模式的原理比较难理解，但代码实现相对简单。对于这个模式有两种不同的理解方式。在 GoF 的《设计模式》一书中，桥接模式被定义为：“将抽象和实现解耦，让它们可以独立变化。”在其他资料和书籍中，还有另外一种更加简单的理解方式：“一个类存在两个（或多个）独立变化的维度，我们通过组合的方式，让这两个（或多个）维度可以独立进行扩展。”对于第一种 GoF 的理解方式，弄懂定义中“抽象”和“实现”两个概念，是理解它的关键。定义中的“抽象”，指的并非“抽象类”或“接口”，而是被抽象出来的一套“类库”，它只包含骨架代码，真正的业务逻辑需要委派给定义中的“实现”来完成。而定义中的“实现”，也并非“接口的实现类”，而是一套独立的“类库”。“抽象”和“实现”独立开发，通过对象之间的组合关系，组装在一起。对于第二种理解方式，它非常类似我们之前讲过的“组合优于继承”设计原则，通过组合关系来替代继承关系，避免继承层次的指数级爆炸。

## facade
门面模式，一个很典型的例子，Socket其实就是应用层与TCP/IP协议族通信的中间软件抽象层，就是一个门面模式，把复杂的TCP/IP协议族隐藏在Socket后面，对用户来说，只需要调用Socket规定的相关函数，让Socket去组织符合制定的协议数据然后进行通信。

API 为facade 模块的外观接口，大部分代码使用此接口简化对facade类的访问。

facade模块同时暴露了a和b 两个Module 的NewXXX和interface，其它代码如果需要使用细节功能时可以直接调用。
```go
package facade

import "fmt"

func NewAPI() API {
	return &apiImpl{
		a: NewAModuleAPI(),
		b: NewBModuleAPI(),
	}
}

//API is facade interface of facade package
type API interface {
	Test() string
}

//facade implement
type apiImpl struct {
	a AModuleAPI
	b BModuleAPI
}

func (a *apiImpl) Test() string {
	aRet := a.a.TestA()
	bRet := a.b.TestB()
	return fmt.Sprintf("%s\n%s", aRet, bRet)
}

//NewAModuleAPI return new AModuleAPI
func NewAModuleAPI() AModuleAPI {
	return &aModuleImpl{}
}

//AModuleAPI ...
type AModuleAPI interface {
	TestA() string
}

type aModuleImpl struct{}

func (*aModuleImpl) TestA() string {
	return "A module running"
}

//NewBModuleAPI return new BModuleAPI
func NewBModuleAPI() BModuleAPI {
	return &bModuleImpl{}
}

//BModuleAPI ...
type BModuleAPI interface {
	TestB() string
}

type bModuleImpl struct{}

func (*bModuleImpl) TestB() string {
	return "B module running"
}
```
```go
package facade

import "testing"

var expect = "A module running\nB module running"

// TestFacadeAPI ...
func TestFacadeAPI(t *testing.T) {
	api := NewAPI()
	ret := api.Test()
	if ret != expect {
		t.Fatalf("expect %s, return %s", expect, ret)
	}
}
```
### 如何设计合理的接口粒度以兼顾接口的易用性和通用性？
门面模式原理和实现都特别简单，应用场景也比较明确，主要在接口设计方面使用。

如果你平时的工作涉及接口开发，不知道你有没有遇到关于接口粒度的问题呢？

为了保证接口的可复用性（或者叫通用性），我们需要将接口尽量设计得细粒度一点，职责单一一点。但是，如果接口的粒度过小，在接口的使用者开发一个业务功能时，就会导致需要调用 n 多细粒度的接口才能完成。调用者肯定会抱怨接口不好用。

相反，如果接口粒度设计得太大，一个接口返回 n 多数据，要做 n 多事情，就会导致接口不够通用、可复用性不好。接口不可复用，那针对不同的调用者的业务需求，我们就需要开发不同的接口来满足，这就会导致系统的接口无限膨胀。


#### 门面模式的原理与实现
门面模式，也叫外观模式，英文全称是 Facade Design Pattern。在 GoF 的《设计模式》一书中，门面模式是这样定义的：

Provide a unified interface to a set of interfaces in a subsystem. Facade Pattern defines a higher-level interface that makes the subsystem easier to use.翻译成中文就是：门面模式为子系统提供一组统一的接口，定义一组高层接口让子系统更易用。



#### 门面模式的应用场景举例

1. 解决易用性问题门面模式可以用来封装系统的底层实现，隐藏系统的复杂性，提供一组更加简单易用、更高层的接口。比如，**Linux 系统调用函数就可以看作一种“门面”。**它是 Linux 操作系统暴露给开发者的一组“特殊”的编程接口，它封装了底层更基础的 Linux 内核调用。再比如，Linux 的 Shell 命令，实际上也可以看作一种门面模式的应用。它继续封装系统调用，提供更加友好、简单的命令，让我们可以直接通过执行命令来跟操作系统交互。我们前面也多次讲过，设计原则、思想、模式很多都是相通的，是同一个道理不同角度的表述。实际上，从隐藏实现复杂性，提供更易用接口这个意图来看，门面模式有点类似之前讲到的迪米特法则（最少知识原则）和接口隔离原则：两个有交互的系统，只暴露有限的必要的接口。除此之外，门面模式还有点类似之前提到封装、抽象的设计思想，提供更抽象的接口，封装底层实现细节。

2. 解决性能问题关于利用门面模式解决性能问题这一点，刚刚我们已经讲过了。我们通过将多个接口调用替换为一个门面接口调用，减少网络通信成本，提高 App 客户端的响应速度。所以，关于这点，我就不再举例说明了。我们来讨论一下这样一个问题：从代码实现的角度来看，该如何组织门面接口和非门面接口？如果门面接口不多，我们完全可以将它跟非门面接口放到一块，也不需要特殊标记，当作普通接口来用即可。如果门面接口很多，我们可以在已有的接口之上，再重新抽象出一层，专门放置门面接口，从类、包的命名上跟原来的接口层做区分。如果门面接口特别多，并且很多都是跨多个子系统的，我们可以将门面接口放到一个新的子系统中。

3. 解决分布式事务问题关于利用门面模式来解决分布式事务问题，我们通过一个例子来解释一下。在一个金融系统中，有两个业务领域模型，用户和钱包。这两个业务领域模型都对外暴露了一系列接口，比如用户的增删改查接口、钱包的增删改查接口。假设有这样一个业务场景：在用户注册的时候，我们不仅会创建用户（在数据库 User 表中），还会给用户创建一个钱包（在数据库的 Wallet 表中）。对于这样一个简单的业务需求，我们可以通过依次调用用户的创建接口和钱包的创建接口来完成。但是，用户注册需要支持事务，也就是说，创建用户和钱包的两个操作，要么都成功，要么都失败，不能一个成功、一个失败。要支持两个接口调用在一个事务中执行，是比较难实现的，这涉及分布式事务问题。虽然我们可以通过引入分布式事务框架或者事后补偿的机制来解决，但代码实现都比较复杂。而最简单的解决方案是，利用数据库事务或者 Spring 框架提供的事务（如果是 Java 语言的话），在一个事务中，执行创建用户和创建钱包这两个 SQL 操作。这就要求两个 SQL 操作要在一个接口中完成，所以，我们可以借鉴门面模式的思想，再设计一个包裹这两个操作的新接口，让新接口在一个事务中执行两个 SQL 操作。



## composite
k8s的Job对象有应用。

组合模式统一对象和对象集，使得使用相同接口使用对象和对象集。

组合模式常用于树状结构，用于统一叶子节点和树节点的访问，并且可以用于应用某一操作到所有子节点。
```go
package composite

import "fmt"

type Component interface {
	Parent() Component
	SetParent(Component)
	Name() string
	SetName(string)
	AddChild(Component)
	Print(string)
}

const (
	LeafNode = iota
	CompositeNode
)

func NewComponent(kind int, name string) Component {
	var c Component
	switch kind {
	case LeafNode:
		c = NewLeaf()
	case CompositeNode:
		c = NewComposite()
	}

	c.SetName(name)
	return c
}

type component struct {
	parent Component
	name   string
}

func (c *component) Parent() Component {
	return c.parent
}

func (c *component) SetParent(parent Component) {
	c.parent = parent
}

func (c *component) Name() string {
	return c.name
}

func (c *component) SetName(name string) {
	c.name = name
}

func (c *component) AddChild(Component) {}

func (c *component) Print(string) {}

type Leaf struct {
	component
}

func NewLeaf() *Leaf {
	return &Leaf{}
}

func (c *Leaf) Print(pre string) {
	fmt.Printf("%s-%s\n", pre, c.Name())
}

type Composite struct {
	component
	childs []Component
}

func NewComposite() *Composite {
	return &Composite{
		childs: make([]Component, 0),
	}
}

func (c *Composite) AddChild(child Component) {
	child.SetParent(c)
	c.childs = append(c.childs, child)
}

func (c *Composite) Print(pre string) {
	fmt.Printf("%s+%s\n", pre, c.Name())
	pre += " "
	for _, comp := range c.childs {
		comp.Print(pre)
	}
}
```
```go
package composite

func ExampleComposite() {
	root := NewComponent(CompositeNode, "root")
	c1 := NewComponent(CompositeNode, "c1")
	c2 := NewComponent(CompositeNode, "c2")
	c3 := NewComponent(CompositeNode, "c3")

	l1 := NewComponent(LeafNode, "l1")
	l2 := NewComponent(LeafNode, "l2")
	l3 := NewComponent(LeafNode, "l3")

	root.AddChild(c1)
	root.AddChild(c2)
	c1.AddChild(c3)
	c1.AddChild(l1)
	c2.AddChild(l2)
	c2.AddChild(l3)

	root.Print("")
	// Output:
	// +root
	//  +c1
	//   +c3
	//   -l1
	//  +c2
	//   -l2
	//   -l3
}
```
### 如何设计实现支持递归遍历的文件系统目录树结构？

组合模式跟我们之前讲的面向对象设计中的“组合关系（通过组合来组装两个类）”，完全是两码事。这里讲的“组合模式”，主要是用来处理树形结构数据。这里的“数据”，你可以简单理解为一组对象集合，待会我们会详细讲解。正因为其应用场景的特殊性，数据必须能表示成树形结构，这也导致了这种模式在实际的项目开发中并不那么常用。但是，一旦数据满足树形结构，应用这种模式就能发挥很大的作用，能让代码变得非常简洁。


#### 组合模式的原理与实现
在 GoF 的《设计模式》一书中，组合模式是这样定义的：Compose objects into tree structure to represent part-whole hierarchies.Composite lets client treat individual objects and compositions of objects uniformly.翻译成中文就是：将一组对象组织（Compose）成树形结构，以表示一种“部分 - 整体”的层次结构。组合让客户端（在很多设计模式书籍中，“客户端”代指代码的使用者。）可以统一单个对象和组合对象的处理逻辑。

“将一组对象（文件和目录）组织成树形结构，以表示一种‘部分 - 整体’的层次结构（目录与子目录的嵌套结构）。组合模式让客户端可以统一单个对象（文件）和组合对象（目录）的处理逻辑（递归遍历）。”


实际上，刚才讲的这种组合模式的设计思路，与其说是一种设计模式，倒不如说是对业务场景的一种数据结构和算法的抽象。其中，数据可以表示成树这种数据结构，业务需求可以通过在树上的递归遍历算法来实现。


#### 组合模式的应用场景举例

在实际的项目中，遇到类似的可以表示成树形结构的业务场景，你只要“照葫芦画瓢”去设计就可以了。


假设我们在开发一个 OA 系统（办公自动化系统）。公司的组织结构包含部门和员工两种数据类型。其中，部门又可以包含子部门和员工。

我们希望在内存中构建整个公司的人员架构图（部门、子部门、员工的隶属关系），并且提供接口计算出部门的薪资成本（隶属于这个部门的所有员工的薪资和）。部门包含子部门和员工，这是一种嵌套结构，可以表示成树这种数据结构。计算每个部门的薪资开支这样一个需求，也可以通过在树上的遍历算法来实现。所以，从这个角度来看，这个应用场景可以使用组合模式来设计和实现。

```
java
public abstract class HumanResource {
  protected long id;
  protected double salary;

  public HumanResource(long id) {
    this.id = id;
  }

  public long getId() {
    return id;
  }

  public abstract double calculateSalary();
}

public class Employee extends HumanResource {
  public Employee(long id, double salary) {
    super(id);
    this.salary = salary;
  }

  @Override
  public double calculateSalary() {
    return salary;
  }
}

public class Department extends HumanResource {
  private List<HumanResource> subNodes = new ArrayList<>();

  public Department(long id) {
    super(id);
  }

  @Override
  public double calculateSalary() {
    double totalSalary = 0;
    for (HumanResource hr : subNodes) {
      totalSalary += hr.calculateSalary();
    }
    this.salary = totalSalary;
    return totalSalary;
  }

  public void addSubNode(HumanResource hr) {
    subNodes.add(hr);
  }
}

// 构建组织架构的代码
public class Demo {
  private static final long ORGANIZATION_ROOT_ID = 1001;
  private DepartmentRepo departmentRepo; // 依赖注入
  private EmployeeRepo employeeRepo; // 依赖注入

  public void buildOrganization() {
    Department rootDepartment = new Department(ORGANIZATION_ROOT_ID);
    buildOrganization(rootDepartment);
  }

  private void buildOrganization(Department department) {
    List<Long> subDepartmentIds = departmentRepo.getSubDepartmentIds(department.getId());
    for (Long subDepartmentId : subDepartmentIds) {
      Department subDepartment = new Department(subDepartmentId);
      department.addSubNode(subDepartment);
      buildOrganization(subDepartment);
    }
    List<Long> employeeIds = employeeRepo.getDepartmentEmployeeIds(department.getId());
    for (Long employeeId : employeeIds) {
      double salary = employeeRepo.getEmployeeSalary(employeeId);
      department.addSubNode(new Employee(employeeId, salary));
    }
  }
}
```
组合模式的设计思路，与其说是一种设计模式，倒不如说是对业务场景的一种数据结构和算法的抽象。其中，数据可以表示成树这种数据结构，业务需求可以通过在树上的递归遍历算法来实现。组合模式，将一组对象组织成树形结构，将单个对象和组合对象都看做树中的节点，以统一处理逻辑，并且它利用树形结构的特点，递归地处理每个子树，依次简化代码实现。使用组合模式的前提在于，你的业务场景必须能够表示成树形结构。所以，组合模式的应用场景也比较局限，它并不是一种很常用的设计模式。




## flyweight
享元模式从对象中剥离出不发生改变且多个实例需要的重复数据，独立出一个享元，使多个对象共享，从而节省内存以及减少对象数量。
```go
package flyweight

import "fmt"

type ImageFlyweightFactory struct {
	maps map[string]*ImageFlyweight
}

var imageFactory *ImageFlyweightFactory

func GetImageFlyweightFactory() *ImageFlyweightFactory {
	if imageFactory == nil {
		imageFactory = &ImageFlyweightFactory{
			maps: make(map[string]*ImageFlyweight),
		}
	}
	return imageFactory
}

func (f *ImageFlyweightFactory) Get(filename string) *ImageFlyweight {
	image := f.maps[filename]
	if image == nil {
		image = NewImageFlyweight(filename)
		f.maps[filename] = image
	}

	return image
}

type ImageFlyweight struct {
	data string
}

func NewImageFlyweight(filename string) *ImageFlyweight {
	// Load image file
	data := fmt.Sprintf("image data %s", filename)
	return &ImageFlyweight{
		data: data,
	}
}

func (i *ImageFlyweight) Data() string {
	return i.data
}

type ImageViewer struct {
	*ImageFlyweight
}

func NewImageViewer(filename string) *ImageViewer {
	image := GetImageFlyweightFactory().Get(filename)
	return &ImageViewer{
		ImageFlyweight: image,
	}
}

func (i *ImageViewer) Display() {
	fmt.Printf("Display: %s\n", i.Data())
}
```
```go
package flyweight

import "testing"

func ExampleFlyweight() {
	viewer := NewImageViewer("image1.png")
	viewer.Display()
	// Output:
	// Display: image data image1.png
}

func TestFlyweight(t *testing.T) {
	viewer1 := NewImageViewer("image1.png")
	viewer2 := NewImageViewer("image1.png")

	if viewer1.ImageFlyweight != viewer2.ImageFlyweight {
		t.Fail()
	}
}
```
### 如何利用享元模式优化文本编辑器的内存占用？
跟其他所有的设计模式类似，享元模式的原理和实现也非常简单。今天，我会通过棋牌游戏和文本编辑器两个实际的例子来讲解。除此之外，我还会讲到它跟单例、缓存、对象池的区别和联系


#### 享元模式原理与实现
所谓“享元”，顾名思义就是被共享的单元。享元模式的意图是复用对象，节省内存，**前提是享元对象是不可变对象。**

具体来讲，当一个系统中存在大量重复对象的时候，如果这些重复的对象是不可变对象，我们就可以利用享元模式将对象设计成享元，在内存中只保留一份实例，供多处代码引用。这样可以减少内存中对象的数量，起到节省内存的目的。实际上，不仅仅相同对象可以设计成享元，对于相似对象，我们也可以将这些对象中相同的部分（字段）提取出来，设计成享元，让这些大量相似对象引用这些享元。

这里我稍微解释一下，定义中的“不可变对象”指的是，一旦通过构造函数初始化完成之后，它的状态（对象的成员变量或者属性）就不会再被修改了。所以，不可变对象不能暴露任何 set() 等修改内部状态的方法。之所以要求享元是不可变对象，那是因为它会被多处代码共享使用，避免一处代码对享元进行了修改，影响到其他使用它的代码。

#### 享元模式 vs 单例、缓存、对象池
在上面的讲解中，我们多次提到“共享”“缓存”“复用”这些字眼，那它跟单例、缓存、对象池这些概念有什么区别呢？我们来简单对比一下。

我们先来看享元模式跟单例的区别。在单例模式中，一个类只能创建一个对象，而在享元模式中，一个类可以创建多个对象，每个对象被多处代码引用共享。实际上，享元模式有点类似于之前讲到的单例的变体：多例。我们前面也多次提到，区别两种设计模式，不能光看代码实现，而是要看设计意图，也就是要解决的问题。尽管从代码实现上来看，享元模式和多例有很多相似之处，但从设计意图上来看，它们是完全不同的。应用享元模式是为了对象复用，节省内存，而应用多例模式是为了限制对象的个数。

我们再来看享元模式跟缓存的区别。在享元模式的实现中，我们通过工厂类来“缓存”已经创建好的对象。这里的“缓存”实际上是“存储”的意思，跟我们平时所说的“数据库缓存”“CPU 缓存”“MemCache 缓存”是两回事。我们平时所讲的缓存，主要是为了提高访问效率，而非复用。

最后我们来看享元模式跟对象池的区别。对象池、连接池（比如数据库连接池）、线程池等也是为了复用，那它们跟享元模式有什么区别呢？你可能对连接池、线程池比较熟悉，对对象池比较陌生，所以，这里我简单解释一下对象池。像 C++ 这样的编程语言，内存的管理是由程序员负责的。为了避免频繁地进行对象创建和释放导致内存碎片，我们可以预先申请一片连续的内存空间，也就是这里说的对象池。每次创建对象时，我们从对象池中直接取出一个空闲对象来使用，对象使用完成之后，再放回到对象池中以供后续复用，而非直接释放掉。虽然对象池、连接池、线程池、享元模式都是为了复用，但是，如果我们再细致地抠一抠“复用”这个字眼的话，对象池、连接池、线程池等池化技术中的“复用”和享元模式中的“复用”实际上是不同的概念。池化技术中的“复用”可以理解为“重复使用”，主要目的是节省时间（比如从数据库池中取一个连接，不需要重新创建）。在任意时刻，每一个对象、连接、线程，并不会被多处使用，而是被一个使用者独占，当使用完成之后，放回到池中，再由其他使用者重复利用。享元模式中的“复用”可以理解为“共享使用”，在整个生命周期中，都是被所有使用者共享的，主要目的是节省空间。


#### 总结
1. 享元模式的原理所谓“享元”，顾名思义就是被共享的单元。享元模式的意图是复用对象，节省内存，前提是享元对象是不可变对象。具体来讲，当一个系统中存在大量重复对象的时候，我们就可以利用享元模式，将对象设计成享元，在内存中只保留一份实例，供多处代码引用，这样可以减少内存中对象的数量，以起到节省内存的目的。实际上，不仅仅相同对象可以设计成享元，对于相似对象，我们也可以将这些对象中相同的部分（字段），提取出来设计成享元，让这些大量相似对象引用这些享元。

2. 享元模式的实现享元模式的代码实现非常简单，主要是通过工厂模式，在工厂类中，通过一个 Map 或者 List 来缓存已经创建好的享元对象，以达到复用的目的。


3. 享元模式 VS 单例、缓存、对象池我们前面也多次提到，区别两种设计模式，不能光看代码实现，而是要看设计意图，也就是要解决的问题。这里的区别也不例外。我们可以用简单几句话来概括一下它们之间的区别。应用单例模式是为了保证对象全局唯一。应用享元模式是为了实现对象复用，节省内存。缓存是为了提高访问效率，而非复用。池化技术中的“复用”理解为“重复使用”，主要是为了节省时间。



























































[参考](https://github.com/senghoo/golang-design-pattern)