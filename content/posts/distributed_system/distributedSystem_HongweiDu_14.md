---
title: "DistributedSystem_HongweiDu_14"
date: 2022-01-01T15:21:52+08:00
lastmod: 2022-01-02
tags: [distributed system]
categories: [School courses]
slug: DS_Summary
draft: true
---
> 2021哈工大深圳堵宏伟分布式系统课程笔记

# topics
Understanding lecture notes and tutorial questions.
## 1.Introduction
- A distributed system is defined as one in which components at networked computers communicate and coordinate their actions only by passing messages.
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

## 2.System Model
- Difficulties for and threats to distributed systems
    - Widely varying mode of use
    - Wide range of system environments
    - Internal threats
    - External threats
- Physical Model
    - Three generations of distributed systems and the emergence of ultra-large-scale (ULS) distributed systems.
- Architectural Model 
    - An architectural model of a distributed system simplifies and abstracts the functions of the individual components of a distributed system
    - Entities that are communicating in the distributed system 
        - 进程、线程、传感器节点或者说对象、组件、网页服务
    - Types of communication paradigms:
        - Interprocess communication
        - Remote invocation
        - Indirect communication
            - Group communication
                - Broadcast (message sent to everyone)
                - Multicast (message sent to specific group)
                - Used for:
                    - Replication of services
                    - Replication of data
                    - Service discovery
                    - Event notification
            - Publish-subscribe-systems
                - Communication through propagation of events
                - Generally associated with publish/subscribe systems
                - Sender process publishes events
                - Receiver process subscribes to events and receives only the ones it is interested in
            - Message queues
    - Architectural styles: 
        - client-server：it scales poorly 
            - Decomposed a typical client-server application into three logical parts
                - the interface part.
                - the application logic part, and.
                - the data part.
            - Thin client implementation
                - Provides a minimal user interface layer, and leave everything else to the server.
            - Fat client implementation
                - Include all of the user interface and application logic in the client.
                - Rely only on the server to store and provide access to data.
            - Implementations in between will split up the interface or application logic parts over the clients and server in different ways.
        - peer-to-peer
            - Is composed of a large number of peer processes running on separate computers.
            - All processes have client and server roles.
            - Patterns of communication between them depends entirely on application requirements.
            - Storage, processing and communication loads for accessing objects are distributed across computers and network links.
            - Each object is replicated in several computers to further distribute the load and to provide resilience in the event of disconnection of individual computers.
            - Need to place and retrieve individual computers is more complex then in client-server architecture.
                - placement one:Servers partition a set of objects in which the service is based and distribute them between themselves.
                - placement two:Server maintain replicated copies of them on several hosts.
    - Vertical distribution (Multi-Tier) and horizontal distribution of c/s systems in Architectural patterns
        - Middleware:Is a layer of software whose purpose is to mask heterogeneity and to provide a convenient programming model to application programmers.
        - Vertical Distribution (Multi-Tier):
            - An extension of the client-server architecture.
            - Distributes the traditional server functionality over multiple servers.
        - Horizontal Distribution:
            - Involves replicating a server’s functionality over multiple computers.
            - Typical example: replicated Web server
                - Each server machine contains a complete copy of all hosted Web pages.
                - Client requests are passed on to the servers in a round robin fashion.
            - Is used to improve scalability (by reducing the load on individual servers) and reliability (by providing redundancy)
- Fundamental Model
    - Characteristics of synchronous distributed systems and asynchronous distributed system(Interaction model)
        - Two variants of the interaction model:
        - Synchronous distributed systems
            - The following bounds are defined:
                - The time to execute each step of a process has known lower and upper bounds
                - Each message transmitted over a channel is received within a known bounded time.
                - Each process has a local clock whose drift rate from real time has known bound.
        - Asynchronous distributed system:
            - There are no bounds on:
                - Process execution speed
                - Message transmission delays
                - Clock drift rate
    - Failure model
    - Security model
## 3.Physical Clock Synchronization
Adjustment changes slope of system time: Linear compensating function （线性补偿函数）
### 3.1Cristian’s algorithm: 
synchronize clocks with a UTC server 
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220102082432.png)
### 3.2Berkeley Algorithm: 
synchronize a set of clocks as close as possible
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220102082505.png)
## 4.NTP Symmetric mode
- 如何计算偏移量、准确值、延迟、传输时间。
- used to synchronize between the time servers (peer-peer)
- There can be a non-negligible delay between the arrival of one message and the dispatch of the next
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220102150951.png)
- Delay = total transmission time of the two messages
$$d_i = (T_i – T_{i-3} ) – (T_{i-1}– T_{i-2})$$
- Offset of clock A relative to clock B:  
    - Offset of clock A: $o_i =((T_{i-2} – T_{i-3} ) + ( T_{i-1} – T_i))/2$
    - Accuracy bound: $d_i /2$

## 5.Logical Clock Synchronization:
- Event ordering: happened before (->), concurrent (||)
- Lamport’s algorithm: single timestamp clock synchronization & features 
    - If a -> b then V(a) < V(b)
    - The reverse is not always true
- Timestamp vector synchronization and features
    - a -> b iff V(a) < V(b)
    - a || b iff neither V(a) ≤ V(b) nor V(b) ≤ V(a)
- Application of timestamp vectors: causal ordered multicast

## 6.Interprocess Communication (IPC)
- Socket operations and Socket types
    - Socket/bind
    - listen, accept, connect
    - Read/write functionalities
    - close/shutdown
- Programming by using sockets

## 7.Remote Invocation (RPC)
- Interface Definition of a client-server system
- Client program development
- Server program development
- Client-server interactions in RPC 

## 8.Group Communication
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220102083158.png)

## 9.Mutual Exclusion & Election Algorithm
- Mutual Exclusion 
    - Centralized Algorithm
    - Token Ring Algorithm
    - Ricart & Agrawala algorithm
    - Distributed Mutual Exclusion
- Election 
    - Bully Algorithm
    - Ring election Algorithm
    - Robert & Chang Algorithm

## 10.Data Centric Consistency Models
- consistency---description
- Strict---**Absolute time ordering** of all shared accesses matters.
- Linearizability---All processes must see all shared accesses in the **same order**. Accesses are furthermore ordered according to a (nonunigue) global timestamp
- Sequential---All processes see all shared accesses in the **same order**. Accesses are not ordered in time
- Causal---All processes see **causally-related** shared accesses in the same order.
- FIFO---All processes see writes from each other **in the order they were used**. Writes from different processes may not always be seen in that order
- Consistency models **not using synchronization** operations.

- Weak---Shared data can be counted on to be consistent **only after a synchronization** is done
- Release---Shared data are made consistent when a **critical region is exited**
- Entry---Shared data **pertaining to a critical region** are made consistent when a **critical region is entered.**
- Models **with synchronization** operations.

## 11.Client Centric Consistency
1. 4 rules of client centric consistency.
2. What is to be propagated?
3. How is it to be propagated?
4. Consistency Protocols
    - Primary-based protocols 
    - Replicated write protocols
    - Cache-coherence protocols

## 12.Internetworking
- CIDR - Classless InterDomain Routing
- NAT - Network Address Translation
- DHCP - Dynamic Host Configuration Protocol
- ARP - Address Resolution Problem
- OSPF - Interior Gateway Routing Protocol 
- BGP - Exterior Gateway Routing Protocol

## 13.Name Services（本有题目，被删了）
- DNS Name Servers
- Recursive query & Iterative query
- DNS Records
- IP-address to Name: Reverse Mapping

## 14.Unix File System
- Internal File Structure : file attributes and data index
- Unix Directory File Directory structure and name resolution
- Superblock
- Internal Structure for File Accesses
- Mount and Unmount 

## 15.Distributed File System
- Distributed File System Components 
- Differences between DFS and stand-alone FS 
- Google FS and NFS

## 16.Transaction Processing System（一定会考）
- Definitions of Atomic Transactions
- RM-ODP properties
- How to achieve atomicity of transactions?
    - Failure Recovery (guarantee nothing-or-all) 
        - Intention list approach
        - Shadow version approach
    - Concurrency Control (guarantee serializability)
        - 2-Phase Locking
        - Timestamp Ordering
        - Optimistic Method

## 17.Web Searching
- Search Engines Architecture
    - Spider
    - Indexer
    - Database
    - Search Engine
- Web Data Mining: Digraph
- FAN - WebPage Ranking
- Inverted File: indexing for search

