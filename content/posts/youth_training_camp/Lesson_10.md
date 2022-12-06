---
title: "Lesson_10"
date: 2022-05-16T11:22:38+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
---
- [引言](#引言)

## 引言
为什么要学习 Git ?

Git 在开源社区，协同工作中非常重要

为什么要安排这个课程 ？

许多新同学，对 Git 完全不了解，或者是只知道基本命令，不了解正确的使用姿势和最佳实践

Git 是什么
版本控制
本地版本控制

依托于本地磁盘进行版本控制

集中式版本控制

存在一个统一的远端服务器，用于版本控制，本地不存储版本控制

分布式版本控制

每个库都拥有所有的版本控制信息，远端服务器用于不同库之间进行版本信息同步

发展历史
最初版由 Liunx 创始人 Linus Torvalds 花两周时间开发而成，主要是为了用于 Linux 项目的维护
衍生平台

Gitlab github.com/



Github gitlab.com/gitlab-org



Gerrit android-review.googlesource.com/q/status:op…

除此之外，还有 bitbucket, Coding, 码云 等一系列平台
Git 命令基本使用方式和原理
Git 配置

Git Config

Git 配置，分成本地，用户，系统基本的配置

Git Remote

Git Remote 配置，分成 SSH 和 HTTP 两种协议实现，不同协议有不同的免密配置方式
代码提交

Git Add

将代码从工作区提交到暂存区

Git Commit

将暂存区代码提交到 Git 存储
Git 存储基本概念


Ref

Tag 仓库标签
Branch 仓库分支






Object

Blob 存储文件内容信息
Tree 存储目录树信息
Commit 存储提交信息
Tag 存储附注标签信息



代码同步

Git Clone

将代码从远端拉取到本地

Git Fetch & Git Pull

将远端仓库代码同步到本地仓库

Git Push

将本地代码同步到远端仓库
Git 研发流程
集中式工作流

Gerrit 平台

围绕主分支进行开发
分支管理工作流

Git Flow



Github Flow



Gitlab Flow

如何合并代码

Three Way Merge



Fast Forward Merge

选择合适的工作流

少量多次提交



保证 CR / CI 等流程通过后才可合入



尽量保证主分支整洁

