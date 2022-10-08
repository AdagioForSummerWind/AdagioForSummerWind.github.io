---
title: "Lesson_06"
date: 2022-05-16T11:22:02+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---

---
title: day6-上-实战项目-Go语言笔记服务
date: 2022-05-17 21:38:36
tags: 
- Go学习路线
- 青训营笔记
---



## 实战项目-Go语言笔记服务
### 一、课程背景与目标

在大家已经学完前5节Go原理与实践课程的基础上，通过项目实战帮助大家把前面学过的知识应用起来

课程目标
●将前面所学的知识应用到项目中
●熟悉项目的代码,可以将项目正常运行
●熟悉Kitex/Gorm的使用

## 二、课前了解
安装Docker/Postman/Git

**Kitex初体验**
通过阅读htts://www cloudwego io/zh/docs/kitex/getting-started/尝试运行Kitex 的示例代码
●kitex暂时没有针对Windows做支持，如果本地开发环境是Windows建议使用WSL 2

●安装Minikube 或Docker Desktop 安装教程
●可以使用Minikube 或者使用Docker Desktop启动Docker
●安装Postman
●安装Git安装教程


## gorm 
单个查询，查询不到的话会返回一个error，**注意**检查
```go
result := db.First(&user)
result.RowsAffected // returns found records count
result.Error        // returns error

// check error ErrRecordNotFound
errors.Is(result.Error, gorm.ErrRecordNotFound)
```
更新多列时：
Updates 方法支持 struct 和 map[string]interface{} 参数。当使用 struct 更新时，默认情况下，GORM 只会更新非零值的字段。如果想确保指定字段被更新，应该使用 Select 更新选定字段，或使用 map 来完成更新操作

```go
// 根据 `struct` 更新属性，只会更新非零值的字段
db.Model(&user).Updates(User{Name: "hello", Age: 18, Active: false})
// UPDATE users SET name='hello', age=18, updated_at = '2013-11-17 21:34:10' WHERE id = 111;这里Active为零值故不更新

// 根据 `map` 更新属性
db.Model(&user).Updates(map[string]interface{}{"name": "hello", "age": 18, "actived": false})
// UPDATE users SET name='hello', age=18, actived=false, updated_at='2013-11-17 21:34:10' WHERE id=111;
```
## Etcd和Opentracing
Etcd用来做服务发现和服务注册
Opentracing主要做链路追踪、可观测性相关的东西。（笔记服务有三个，要查看每个服务的调用耗时可以使用opentracing，gorm也是支持opentracing的，能看到某条sql执行具体耗时）

现在一般是从opentracing迁移到opentelemetry，但gorm要到5.6月才能支持，所以用前者
## 笔记服务调用关系

demouser和demonote会把IP、端口号、服务名称注册到etcd，API可以通过服务发现发现etcd里边有对应的笔记服务和用户服务，然后就可以通过IP和端口请求用户数据，即对demouser服务发起请求

实际开发并不建议API直接操作MySQL，API把聚合好的数据传递给前端

## 项目模块功能介绍

API主要提供HTTP接口

## 其他
HTTP框架都是注册路由、写handler就行了


IDL方便协作

cmd下业务代码

yaeger是配合opentracing做链路追踪的


gorm里model结构：deleteat就是软删的一个字段
```go
// Model a basic GoLang struct which includes the following fields: ID, CreatedAt, UpdatedAt, DeletedAt
// It may be embedded into your model or you may build your own model without it
//    type User struct {
//      gorm.Model
//    }
type Model struct {
	ID        uint `gorm:"primarykey"`
	CreatedAt time.Time
	UpdatedAt time.Time
	DeletedAt DeletedAt `gorm:"index"`
}

```















