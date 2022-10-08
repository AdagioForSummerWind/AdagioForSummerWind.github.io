# DistributedSystem_HongweiDu_05

> 2021哈工大深圳堵宏伟分布式系统课程笔记

# Mutual  Exclusion  & Election  Algorithms(互斥算法 & 选举算法)
## Mutual Exclusion
Process Synchronization:
- Techniques to coordinate execution among processes
    - One process may have to wait for another
    - Shared resource (e.g. critical section) may require exclusive access

Requirements of Mutual Exclusion Algorithms:
- A mutual exclusion algorithm should satisfy the following properties:
    - Safety Property: The safety property states that at any instant, only one process can execute the critical section. 
    - Liveness Property: This property states the absence of deadlock and starvation.
    - Fairness: Fairness in the context of mutual exclusion means that each process gets a fair chance to execute the critical section.

Centralized Systems:
- Achieve Mutual exclusion via:
    - Test & set in hardware
    - Semaphores
    - Messages(inter-process)
    - Condition variables

Distributed Mutual Exclusion:
- Assume there is agreement on how a resource is identified
    - Pass identifier with requests
    - e.g., lock(“printer”), lock(“table:employees”),lock(“table:employees;row:15”)
- …and every process can identify itself uniquely
- Goal:
    - Create an algorithm to allow a process to obtain exclusive access to a resource that is available on the network

Centralized algorithm（集中式算法）:
- Mimic single processor system
- One process elected as coordinator
1. Request resource
2. Wait for response
3. Receive  grant
4. Access resource
5. Release  resource
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210151450.png)
- If another process claimed resource:
    - Coordinator does not reply until release
    - Maintain queue
        - Service requests in FIFO order
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210151558.png)
- Benefits
    - Fair: All requests processed in order
    - Easy to implement, understand, verify
    - Processes do not need to know group members – just the coordinator
- Problems
    - Process cannot distinguish being blocked from a dead coordinator – single point of failure
    - Centralized server can be a bottleneck

Token Ring algorithm（令牌环算法）:
- Assume known group of processes
    - Some ordering can be imposed on group (unique process IDs)
    - Construct logical ring in software
    - Process communicates with its neighbor
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210151837.png)
- Initialization
    - Process 0 creates a token for resource R
- Token circulates around ring
    - From Pi to P(i+1)mod N
- When process acquires token
    - Checks to see if it needs to enter critical section
    - If no, send ring to neighbor
    - If yes, access resource
        - Hold token until done
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210151948.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210152022.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210152053.png)
- ……
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210152140.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210152022.png)

Token Ring algorithm summary:
- Only one process at a time has token
    - Mutual exclusion guaranteed
- Order well-defined (but not necessarily first-come, first-served)
    - Starvation cannot occur
    - Lack of FCFS ordering may be undesirable sometimes
- Problems
    - Token loss (e.g., process died)
        - It will have to be regenerated
        - Detecting loss may be a problem(is the token lost or in just use by someone?)
    - Process loss: what if you can't talk to your neighbor?

Ricart & Agrawala algorithm:
- Distributed algorithm using reliable multicast and logical clocks
- Process wants to enter critical section:
    - Compose message containing:
        - Identifier (machine ID, process ID)
        - Name of resource
        - Timestamp (totally-ordered Lamport）
    - Send request to all processes in group
    - Wait until everyone gives permission
    - Enter critical section / use resource
- When process receives request:
    - If receiver not interested:
        - Send OK to sender
    - If receiver is in critical section
        - Do not reply; add request to queue
    - If receiver just sent a request as well:
        - Compare timestamps: received & sent messages
        - Earliest wins
        - If receiver is loser, send OK
        - If receiver is winner, do not reply, queue
    - When done with critical section
        - Send OK to all  queued  requests

Example for Ricart & Agrawala algorithm:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210152548.png)

Ricart & Agrawala algorithm:
- N points of failure
- A lot of messaging traffic
- Demonstrates that a fully distributed algorithm is possible

Lamport’s Mutual Exclusion:
- Each process maintains request queue
    - Contains mutual  exclusion  requests
- Requesting critical section 
    - Process Pi sends request(i, Ti) to all nodes(Ti:Lamport time)
    - Places request on its own queue
    - When a process Pj receives a request, it returns a timestamped ack
- Entering critical section  (accessing resource):
    - Pi received a message (ack or release) from every other process with a timestamp larger than Ti
    - Pi’s request has the earliest timestamp in its queue
- Difference from Ricart-Agrawala:
    - Everyone responds (acks) … always - no hold-back
    - Process decides to go based on whether its request is the earliest in its queue
- Releasing critical section 
    - Remove request from its own queue
    - Send a timestamped release message
    - When a process receives a release message
        - Removes request for that process from its queue
        - This may cause its own entry have the earliest timestamp in the queue, enabling it to access the critical section

## Election  Algorithms
Elections：
- Need one process to act as coordinator
- Processes have no distinguishing characteristics
- Each process has a unique ID to identify itself

Bully algorithm （霸道选举算法)：
- Select process with largest ID as coordinator
- When process P detects dead coordinator:
    - Send election message to all processes with higher IDs.
- If nobody responds, P wins and takes over.
- If any process responds, P’s job is done.
    - Optional: Let all nodes with lower IDs know an election is taking place.
- If process receives an election message 
    - Send OK message back
    - Hold election (unless it is already holding one)
- A process announces victory by sending all processes a
message telling them that it is the new coordinator
- If a dead process recovers, it holds an election to find the
coordinator.
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153245.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153309.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153332.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153355.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153422.png)

Ring algorithm （环选举算法）：
- Ring arrangement of processes
- If any process detects failure of coordinator
    - Construct election message with process ID and send to next process
    - If successor is down, skip over
    - Repeat until a running process is located
- Upon receiving an election message
    - Process forwards the message, adding its process ID to the body
- Eventually message returns to originator
    - Process sees its ID on list
    - Circulates (or multicasts) a coordinator message announcing coordinator
- E.g. lowest numbered process
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153539.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153618.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153713.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153736.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153756.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153819.png)
- P2 receives the election message that it initiated
- P2 now picks a leader (e.g., lowest or highest ID)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153914.png)
- P2 announces the new coordinator to the group
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210153958.png)

Chang & Roberts Ring Algorithm：
- Optimize the ring
    - Message always contains one process ID
    - Avoid multiple circulating elections
    - If a process sends a message, it marks its state as a participant
- Upon receiving an election message:
    - If PID(message) > PID(process)
    - forward the message – higher ID will always win over a lower one
    - If PID(message) < PID(process)
    - replace PID in message with PID(process)
    - forward the new message – we have a higher ID number; use it
    - If PID(message) < PID(process) AND process is participant
    - discard the message – we're already circulating our ID
    - If PID(message) == PID(process)
    - the process is now the leader – message circulated: announce winner

Network Partitioning: Split Brain （脑裂）：
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210154220.png)













