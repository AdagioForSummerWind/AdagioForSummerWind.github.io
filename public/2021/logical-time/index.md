# DistributedSystem_HongweiDu_04

# Logical Time
Motivation of logical clocks:
- Cannot synchronize physical clocks perfectly in distributed systems. [Lamport 1978]
- Main function of computer clocks – order events
    - If two processes don’t interact, there is no need to sync clocks. 
    - This observation leads to “causality”

Causality（因果性）:
- Order events with happened-before  (->) relation
    - a > b 
        - a could have affected the outcome of b
    - a || b
        - a and b take place in different processes that don’t exchange data
        - Their relative ordering does not matter (they are concurrent)

Definition of happened-before:
- Definition of “” relationship:
    - If a and b take place in the same process
        - a comes before b, then a -> b 
    - If a and b take place in the different processes
        - a is a “send” and b is the corresponding “receive”, then a -> b 
    - Transitive: if a -> b and b -> c, then a -> c 
- Partial ordering – unordered events are concurrent
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145000.png)

Logical Clocks:
- A logical clock is a monotonically increasing software counter. It need not relate to a physical clock.
    - Corrections to a clock must be made by adding, not subtracting
- Rule for assigning “time” values to events
    - if a -> b then clock(a) < clock(b)

Event counting example:
- Three processes: P0, P1, P2, events a, b, c, …
- A local event counter in each process.
- Processes occasionally communicate with each other, where inconsistency occurs, …
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145216.png)

Lamport’s algorithm, 1978:
- Each process Pi has a logical clock Li. Clock synchronization algorithm:
    - Li is initialized to 0;
    - Update Li:
        - LC1: Li  is incremented by 1 for each new event happened in Pi 
        - LC2: when Pi sends message m, it attaches t =  Li to m
        - LC3: when Pj receives (m,t) it sets Lj := max{Lj, t} , and then applies LC1 to increment Lj for event receive(m)
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145316.png)

Problem: Identical timestamps:
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145349.png)
- Concurrent events (e.g., a, g) may have the same timestamp

Make timestamps unique:
- Append the process ID (or system ID) to the clock value after the decimal point:
    - e.g. if  P1, P2 both have L1 = L2 = 40, make L1 = 40.1, L2 = 40.2
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145434.png)

Problem: Detecting causal relations:
- If a -> b, then L(a) < L(b), however:
- If L(a) < L(b), we cannot conclude that a -> b 
- It is not very useful in distributed systems.
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145540.png)
- Solution: use **vector clocks**

Vector of Timestamps:
- Suppose there are a group of people and each needs to keep track of events happened to others. 
- Requirement: Given two events, you need to tell whether they are sequential or concurrent.
- Solution: you need to have a vector of timestamps, one element for each member.
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145641.png)

Vector clocks （向量时钟）:
- Each process Pi keeps a clock Vi which is a vector of N integers
    - Initialization: for 1 ≤ i ≤ N and 1 ≤ k ≤ N, Vi[k] := 0
    - Update Vi : 
        - VC1: when there is a new event in Pi, it sets Vi[i] := Vi[i] +1
        - VC2: when Pi sends a message m out, it attaches t = Vi to m
        - VC3: when Pj receives (m,t), for 1 ≤ k ≤ N, it sets Vj[k] := max{Vj[k], t[k]}, then applies VC1 to increment Vj[j] for event receive(m,t)
- Note: Vi[j] is a timestamp indicating that Pi knows all events that happened in Pj upto this time.
- example:
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145829.png)
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145851.png)
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145915.png)
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210145953.png)
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210150014.png)
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210150037.png)
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210150059.png)

Detecting “->” or “||” events by time vectors:
- Define
    - V = V’ iff V[i] = V’[i] for i = 1, …, N
    - V ≤ V’ iff V[i] ≤ V’[i] for i = 1, …, N
    - V < V’ iff V ≤ V’ and V ≠ V’ 
- V(e) = timestamp vector of an event e
- For any two events a and b, 
    - a -> b iff V(a) < V(b), a ≠ b
    - a || b iff neither V(a) ≤ V(b) nor V(b) ≤ V(a)
- an example:
    - ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210150247.png)

Summary on vector timestamps:
- No need to synchronize physical clocks
- Able to order causal events
- Able to identify concurrent events (but cannot order them) 

An Application of Timestamp Vectors: causally-ordered multicast:
- Multicast: a sender sends a message to a group of receivers. Every message must be received by all group members.
- Causally ordered multicast: if m1 ->  m2, m1 must be received before m2 by all receivers.
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210150506.png)

Algorithm of Causally-Ordered Multicast:
- Each group member keeps a timestamp vector of n components (n group members), all initialized to 0.
    - When Pi multicasts a message m, it increments i-th component of its time vector Vi and attaches Vi to m.
    - When Pj with Vj receives (m, Vi) from Pi:
        - if (Vj [k] >= Vi[k] for all k, k≠ i), then
		    - Vj [i] := Vi [i];  // Vi [i] is always greater than Vj [i]
		    - Vj [j] := Vj [j] + 1;
            - Deliver m;
	- otherwise 
		- Delay m until “if” is met. 
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210150745.png)

Causal-Order Preserved:
- If m1 -> m2, m1 is received by (delivered to) all recipients before m2.
- If m1 || m2, m1 and m2 can be received in arbitrary order by recipients.
- Total ordered multicast（全序组播）: for case of m1 || m2, m1 and m2 must be received in the same order by all recipients (i.e., either all m1 before m2, or all m2 before m1).
- ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211210150851.png)

Question:
- Suppose a group of process P0 -P5 and timestamp vector is used to represent logical times. P0 sends out a message m1 with timestamp vector T0 = {5,7,2,3,4,8}:
- a) what is the event number of P0?  
- b) what is the event number of the last event that P0 knows about P4?
- c) suppose P4 sends a message m2 with vector T4 = {5,7,3,3,6,8}. What is relationship between m1 and m2?













