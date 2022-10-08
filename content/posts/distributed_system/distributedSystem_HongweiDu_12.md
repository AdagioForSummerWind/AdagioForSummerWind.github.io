---
title: "DistributedSystem_HongweiDu_12"
date: 2022-01-01T15:21:45+08:00
lastmod: 2022-01-01
tags: [distributed system]
categories: [School courses]
slug: Transaction Processing Systems
draft: true
---
> 2021哈工大深圳堵宏伟分布式系统课程笔记

# Transaction Processing Systems事务处理系统
File operations and Transaction operations：
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101220659.png)

Definitions of Atomic Transactions ：
- Atomic Transaction: a sequence of data access operations that are atomic in the sense of:
    - All-or-Nothing
    - Serializability
- The ACID properties of a transaction in RM-ODP:
    - Atomicity
    - Consistency
    - Isolation
    - Durability

What makes Database inconsistent?：
- Concurrent accesses of transactions
- Failures of servers
- Two well-known problems caused by concurrency:
- **Lost update problem**
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101220857.png)

Inconsistent Retrieval Problem：
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101220928.png)

Problem Caused by Server Failures：
- Transaction: transfer(A, B, 100)
- bal = A.Read();
- A.Write(bal - 100);
- "bad" <-server crash point
- bal = B.Read();
- B.Write(bal + 100); 

Transactional File Operations:
- Transactions provide a programming environment: 
    - Concurrency transparency
    - Failure transparency 
- Transactional file service operations:
```
TID = OpenTrans ()
(Commit, Abort) = CloseTrans (TID)
AbortTrans (TID)
TWrite(TID, FID, i, Data)
TRead(TID, FID, i, buf)
FID = TCreate(TID, filename, type)
TDelete(TID, FID)
 ......
```
An Example of Using Transactions :
- Transaction: transfer (A, B, 100)
```
tid = OpenTrans();
    Tread(tid, A, bal)
    Twrite(tid, accountA, bal-100)
    Tread(tid, accountB, bal)
    Twrite(tid, accountB, bal+100)
CloseTrans(tid);
```
How to achieve atomicity of transactions?:
- Failure Recovery (guarantee nothing-or-all) 
    - Intention list approach
    - Shadow version approach
- Concurrency Control (guarantee serializability)
    - 2-Phase Locking
    - Timestamp Ordering
    - Optimistic Method

Failure Recovery of Transactions :
- An execution of a transaction has Two Phases: 
    1. Tentative phase, the execution transaction body
    2. Commit phase, making tentative values permanent 
- Making transactions failure recoverable:
    - Keep the tentative values of data on disk that can survive failures
    - Restore data items at the restart from a failure (recovery operation should be idempotent)
    - Make the commit phase repeatable

Intention List Approach (failure recovery) :
- Example of intention list:
```
tid = OpenTrans;
    TWrite(tid, fd1, len1, data1);
    TWrite(tid, fd2, len2, data2);
Close(tid)
```
- Intention List: 
```
tid, status, 
{"Twrite, fd, pos1, len1, data1", 
"Twrite, fd, pos2, len2, data2"}
```
Implementation of Intention List:
- Transaction operations by using intention list:
    - Twrite writes data to the intention list.	
    - Tread reads data from the intention list if it is present.
    - CloseTrans performs operations in the intention list onto database files (or the recovery file).
- Recovery manager: a program (part of the server) which is called when the server restarts from a failure.
- Recovery file: a file used by the recovery manager to restore the database to a consistency state. Each entry of the file, for one transaction, contains the information:
    - Tid
    - transaction status
    - intention list

Example of recovery:
- Transactions T and U:
- T: transfer (A, B, $20)
- U: transfer (C, B, $22)
- Recovery: remove tentative data created by U (p5 & p6) and commit the data updated by T (p1 & p2) to database.
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101221702.png)

Shadow Version Approach (failure recovery) :
- When a transaction modifies a file or data item, it creates a shadow (tentative) version of the file. 
- The subsequent Twrite/Tread are performed on the shadow version.
- At closeTrans, it detects version conflict with other concurrent transactions (done by concurrency control).
- If no conflict, the shadow version is merged with other concurrent versions already committed; otherwise it is aborted.
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101221737.png)

Implementation of Shadow Version Approach:
- A copy of the file index block is created at the first Twrite of a transaction.
- Each Twrite operation creates shadow pages. The index entries of modified pages are made pointing to the shadow pages.
- The original file index block is replaced by the new one at the commit of the transaction (it’s an idempotent operation).

Serializability (Serially Equivalent) :
- Two transactions may conflict with each other when they access the same data item(s).
- Only write operations may cause inconsistency.

- Def. Serializability:  T  U (T completes before U starts):
    1. read operations in U must read the data written by T
    2. write operations in U must overwrite the data done by T
    3. T cannot see any effect of U

Absolute Sequential Execution:
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101222016.png)

Serial Equivalence:
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101222049.png)

Non-serial Equivalence :
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101222127.png)

Non-serial Equivalence of Lost Update and Inconsistent Retrievals:
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101222249.png)

Serializability of Transactions :
- Conflicting operations: Two operations access the same data item and one of them is write.
- Conflicting transactions: Two transactions that have conflicting operations in them. 

- Serializability of transactionsT1 and T2:
- Serializability of Transactions: 
    - The execution of a set of transactions T1 T2, ..., Tm, is equivalent to the execution of them in a serial order, i.e., Ti1  Ti2 ->  , ...,  Tim
- Note: Serializability is the minimum requirement for database consistency in general.

2-Phase Locking :
- First phase: obtaining locks.
- Second phase: releasing locks.
- When a transaction begins to release a lock, it cannot apply for locks any more.
- Why two phases of locking?
```
    Server operations for T		Server operations for U	
    write(a); 				
	read(b);
				               	read(b); 
				               	write(b); 	
	write(b); 	
```
Simple locking won’t work (2-Phase Locking):
- First phase: obtaining locks.
- Second phase: releasing locks.
- When a transaction begins to release a lock, it cannot apply for locks any more.

- Think about by using simple locking:
```
Server operations for T		Server operations for U	
lock(a);write(a); unlock(a)				
lock(b); read(b); unlock(b)
				               lock(b); read(b); unlock(b)
				               lock(b);write(b); unlock(b)	
lock(b);write(b); unlock(b)	
```
2-Phase Locking :
- First phase: obtaining locks.
- Second phase: releasing locks.
- When a transaction begins to release a lock, it cannot apply for locks any more.

- Use two phases of locking:
```
Server operations for T		Server operations for U	
lock(a);write(a); 				
lock(b); read(b); 
				               lock(b) – wait!!!  read(b); 
				               …….		write(b); 	
lock(b);write(b); unlock(a, b)	
```
- Note: U cannot obtain lock on b until T completes.

Serializability of 2-Phase Locking:
- Serializability: All transactions are serialized in the order of the time they obtain locks on data items. 
- Lock compatibility:![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101222824.png)

Implementation of Locking:
- Lock manager: a module of a server program. It maintains a table of locks for the data items of the server.Each entry in the table of locks has:
    - transaction ID
    - data-item ID
    - lock type
    - a condition variable (a queue for clients waiting unlock)
- Lock manager provides two operations:
    - lock(trans, dataItem, lockType)
    - unlock(trans), signal the condition variable
- The lock and unlock operations must be atomic 

Deadlock in 2-Phase Locking:
- Deadlock may occur in 2-Phase locking
- Deadlock prevention and handling:
    - Lock all data items a transaction accesses at the start of the transaction. 
    - Set timeout for waiting a lock.

Discussions on 2-Phase Locking:
- Advantages of locking: 
    1. No abort and restart.
    2. Simple for understanding and implementation.
- Disadvantages: 
    1. Pessimistic. 
    2. Poor efficiency (cost of locking and clients waiting for locks).
    3. Deadlock.

Timestamp Ordering :
- Serializability of Timestamp Ordering: transactions are serialized in the order of their start time (openTrans).
- Important Timestamps:
    1. Each transaction is assigned an unique timestamp T (also used as TID) at the open. 
    2. Each data item has a write-timestamp (wt) and a read-timestamp (rt).

Timestamp Ordering (Cont’d) :
- Read/Write Operations: the transaction’s timestamp T is compared with the rt and wt of the data to decide if the operation can proceed.
- Read Rule:
    - transaction T can read a data item only if the data item was last written by an earlier transaction.
    - if a read of T is accepted, T becomes a tentative rt of the data (if T > rt).
- Write Rule:
    - transaction T can write the data only if the data was last read and written by earlier transactions.
    - when a write of T is accepted, a tentative version of data is the created with timestamp T; 
- Commitment:
    - when T commits, its tentative version of data and tentative rt become permanent; 
    - if T aborts, all tentative data and rts created by T are removed.

Rules for Write:
- W1: T >= rt and T > wt, a tentative value is created. 
- W2: T < rt (including tentative rt) or T < wt, abort.
-                        T1 < T2 < T3 < T4
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101223354.png)

Rules for Read:
- R1: T >= wt  
    - if there is a tentative value made by itself, read this tentative value; otherwise:
    - if there are tentative values whose timestamps are earlier than T, wait for the tentative values committed; otherwise: 
    - read data immediately.
- R2: T < wt,  abort. 
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101223512.png)

Commitment of Transactions:
- The commit of a tentative value (including a tentative rt of a data object) has to wait for the commit of tentative values of earlier transactions. 
- A transaction waits for the earlier transactions only, which avoids deadlocks. 
- Note: all transactions are serialized in the order of the time when they start (openTrans).

Multiversion Timestamp Ordering ;
- The server keeps old committed versions (a history of versions) as well as tentative versions in a list of versions of the data.
- A read that arrives too late need not be rejected. It returns the data whose version has the largest wt that is less than the transaction. A read still need to wait for a tentative version to commit/abort.
- There is no conflict between write operations, bcs each transaction writes on its own version of the data. But a write will be rejected if the data was read by a later transaction.
- A commit does not need to wait for earlier transactions (bcs multi-versions can co-exist) if it does not read data from earlier versions.
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101223611.png)

Multiversion Timestamp Ordering: late read / write:
- In multiversion timestamp ordering, a read operation can always proceed (provided the old version is still kept)
- But a write operation arrived too late can still be rejected. 
- Example: after T3 read & write…. (cont’d)..
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101223652.png)

Multiversion Timestamp Ordering: late read/write (cont’d):
- Ater T5 reads the data and committed, if T4-write arrives, it’ll be aborted; otherwise you have conflict of T4 & T5.
- Rule: a write operation arrived too late can still be rejected.
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101223725.png)

Discussions on Timestamp Ordering:
- Advantages: 
    - No deadlocks.
- Disadvantage: 
    - Extra storage for timestamps.
    - Possible abort and restart.
    - A long transaction may block others.
```
Timestamp ordering method is pessmistic (over-strong):
T				U			
t1=openTrans
				t2 = openTrans
                                    	read(t2, b)
				write(t2 ,b)
				closeTrans(t2)
read(t1, b) 	 <- T will abort (unnecessarily)
```
Optimistic Method (Optimistic Timestamp Ordering) :
- Observation: the possibility of conflicts of two transactions is low. Transactions are allowed to progress as if there were no conflict at all. 
- A transaction has three phases: tentative, validation and commit. 
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101223902.png)
- Tentative phase: read and write are returned immediately (write is to a tentative version). Read set (RS) and Write set (WS) of the trans are recorded.
- Validation phase: detect the conflicts with other concurrent transactions and decide commit/abort as the result.
- Commit phase: commit tentative values made by the transaction.
Note: transactions are serialized in the order of their close time.

Validation Condition :
- When validating Ti, system checks each Tj which is concurrent with Ti (i.e. Ti starts in between the start and completion of Tj).
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101224000.png)

Implementation Details :
- Three important time points of a transaction: 
- start_t, finish_t and commit_t.
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101224053.png)
- validation_set: a set of transactions being in validation. 
- Note: start_t is the time of “openTrans”, finish_t is the time of “closeTrans”.

Validation, Write, and Commit of Transaction Ti；
1. When a Ti enters validation, record time finish_t,  make a copy of validation_set and add Ti into validation_set.
    - transactions whose commit_t is in between start_t and finish_t of Ti are concurrent in tentative phase but serial in validation phase with Ti.
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101224154.png)
    - transactions in validation_set are concurrent in validation phase with Ti.
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101224215.png)
2. If Ti passes validation, it enters write-phase. 
3. Ti's commit_t is recorded and Ti is removed from validation_set.

How long we need to keep information of Ti：
- Information of committed transactions should be kept for other transactions validation: 
    - the information of a transaction can be removed when its commit_t < start_t of any uncommitted transaction in the system.
    - ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220101224304.png)

Discussions on Optimistic Method：
- Serializability
    - Transactions are serialized in the order of the time when they are closed. 
- Starvation problem
    - a (long) transaction may be aborted again and again.
- Advantage 
    - optimistic (high concurrency).
    - no deadlock.
    - a transaction will not be blocked by others.
- Disadvantage:
    - complicate.
    - starvation.
    - abort and restart.

Conclusion of Concurrency Control Methods：
- Two phase locking: 
    - Lock data items before access. 
    - Serialized in the order of obtaining locks. 
    - No abort and restart.

- Timestamp ordering:
    - Check timestamps of data items before access.
    - Serialized in the order of start time. 
    - Abort during execution.

- Optimistic method:
    - Validate at the close.
    - Serialized in the order of close time. 
    - Abort at validation.

