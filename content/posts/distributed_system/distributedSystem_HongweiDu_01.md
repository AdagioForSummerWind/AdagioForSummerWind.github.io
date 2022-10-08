---
title: "DistributedSystem_HongweiDu_01"
date: 2021-12-03T14:35:31+08:00
lastmod: 2021-12-03
tags: [distributed system]
categories: [School courses]
slug: DistributedSystem HongweiDu intro
draft: true
---
> 2021哈工大深圳堵宏伟分布式系统课程笔记

# Intr
HongweiDu(堵宏伟)
## Topics
- Introduction
- Distributed Systems models
- Distributed Time and Clock Synchronization
- Socket Communication
- Remote Method Invocation(RMI)
- Group Communication
- Mutual exclusion & election algorithms
- Replication
- ……
## Motivation
- Resource sharing
    - Computers connected by the network and share resources.
    - Hardware sharing, software sharing,  data sharing, service sharing.media stream sharing.

- Collaborative computing
    - Parallel computing, distributed computing
## Definition
A distributed system is defined as one in which components at networked computers communicate and coordinate their actions only by passing messages.
## Features
The distributed system features:
- Concurrency
    - Multi-process and multi-threads concurrently execute, share resources.
- No global clock
    - Program coordination depend on message passing.
- Independent failure
    - Some processes failure,  can not be known by other processes.
## Challenges
### Heterogeneity
- Middleware
    - Apply to  a software layer that provides a program abstraction as well as masking the heterogeneity of the underlying layers (networks, hardware, operating systems and programming languages). 
    - Example: Java RMI

- Mobile code
    - Program code that can transferred from one computer to another and run at the destination.  Example: Java Applet.The Java virtual machine (JVM) provide a way of making code executable on a variety of host computers.
### Openness
- Computer System Openness
    - Determines whether the system can be extended and reimplemented in various ways.  For example: UNIX.
- Distributed System Openness
    - The degree to which new resource-sharing services can be added and be made available for use by a variety of client programs.
    - RFC (‘Request For Comments’)
### Security
- Confidentiality(机密性）
    - Protection against disclosure to unauthorized individuals.
- Integrity （完整性）
    - Protection against alteration or corruption.
    - e.g.  Checksum (校验和）
- Availability（可用性）
    - Protection against interference with the means to access the resources.
### Scalability(可拓展性)
- Controlling the cost of physical resources
    - As the demand for a resource grows, it should be possible to extend the system, at reasonable cost, to meet it.
- Controlling the performance loss
    - Consider the management of a set of data whose size is proportional to the number of users or resources in the system.
- Preventing software resources running out
    - IPv4, IPv6…..
- Avoiding performance bottlenecks
    - In general, algorithms should be decentralized to avoid having performance bottlenecks. 
### Failure handling
- Detecting failures 
    - Some failure can be detected.
- Masking failures
    - Some failure that have been detected can be hidden or made less severe.
- Tolerating failures
    - Most of the services in the Internet do exhibit failures.
- Recovering from failures
    - Recovery involves the design of software so that the state of permanent data can be recovered or ‘roll back’ after a server has crashed.
- Redundancy
    - Services can be made to tolerate failures by the use of redundant components.
### Concurrency
- Consistent
    - Multi-thread concurrent access the sharing resource.
- Performance
### Transparency
- Access transparency: enables local and remote resources to be accessed using identical operations.
- Location transparency: enables resources to be accessed without knowledge of their physical or network location (for example, which building or IP address).
- Concurrency transparency: enables several processes to operate concurrently using shared resources without interference between them.
- Replication transparency: enables multiple instances of resources to be used to increase reliability and performance without knowledge of the replicas by users or application programmers.
- Failure transparency: enables the concealment of faults, allowing users and application programs to complete their tasks despite the failure of hardware or software components.
- Mobility transparency: allows the movement of resources and clients within a system without affecting the operation of users or programs.
- Performance transparency: allows the system to be reconfigured to improve performance as loads vary.
- Scaling transparency: allows the system and applications to expand in scale without change to the system structure or the application algorithms.
### Quality of Service
- Reliability
- Security
- Performance
- Adaptability
## Conclusion
- Distributed system is everywhere.
- The motivation of constructing a distributed system is resource sharing and collaborative computing
- Distributed system features.
    - Concurrency
    - No global clock
    - Independent failure
- Distributed system challenges.
    - Heterogeneity
    - Openness
    - Security
    - Scalability
    - Failure handling
    - Concurrency
    - Transparency
    - Quality of Service









