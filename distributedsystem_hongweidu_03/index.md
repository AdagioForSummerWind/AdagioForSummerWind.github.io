# DistributedSystem_HongweiDu_03

> 2021哈工大深圳堵宏伟分布式系统课程笔记

# Physical Time
Why Timestamps in Systems?
- Precise performance measurements
- Guarantee “up-to-date” or recentness of data
- Temporal ordering of events produced by concurrent processes
- Synchronization between senders and receivers of messages
- Coordination of joint activities
- Serialization of concurrent accesses to shared objects
- ……

Physical time:
- Solar time 
    - 1 sec = 1 day / 86400
    - Problem: days are of different lengths (due to tidal friction, etc.) 
    - mean solar second: averaged over many days  
- Greenwich Mean Time (GMT)
    - The mean solar time at Royal Observatory in Greenwich, London
    - Greenwich located at longitude 0, the line that divides east and west

Coordinated Universal Time (UTC):
- International atomic time (TAI) 国际原子时间
    - 1 sec = time for Cesium-133 atom to make 9,192,631,770  state transitions.
    - TAI time is simply the number of Cesium-133 transitions since midnight on Jan 1, 1958.
    - Accuracy: better than 1 second in six million years
    - Problem: Atomic clocks do not keep in step with solar time
- Coordinated Universal Time (UTC) 通用协调时间
    - Based on the atomic time (TAI) and introduced from 1 Jan 1972
    - A leap second is occasionally inserted or deleted to keep in step with solar time when the difference btw a solar-day and a TAI-day is over 800ms

Computer Clocks:
- CMOS clock circuit driven by a quartz oscillator
    - battery backup to continue measuring time when power is off
- The circuit has a counter（计数器） and a register（寄存器）. The counter decrements by 1 for each oscillation; an interrupt（中断） is generated when it reaches 0 and the number in the register is loaded to the counter. Then, it repeats…
- OS catches interrupt signals to maintain a computer clock
    - e.g., 60 or 100 interrupts per second
    - Programmable Interrupt Controller (PIC)
    - Interrupt service routine increments the “clock” by 1 for each interrupt
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210142038.png)

Clock drift and clock skew:
- Clock Drift 时钟漂移
    - Clocks tick at different rates
        - Ordinary quartz clocks drift by ~ 1sec in 11-12 days. (10-6 secs/sec).
        - High precision quartz clocks drift rate is ~ 10-7 or 10-8 secs/sec
    - Create ever-widening gap in perceived time
- Clock Skew (offset)  时钟偏移
    - Difference between two clocks at one point in time
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210142249.png)

Perfect clock:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210142339.png)

Drift with a slow computer clock:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210142418.png)

Drift with a fast computer clock:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210142452.png)

Dealing with drift:
- No good to set a clock backward
    - Illusion of time moving backwards can confuse message ordering and software development environments
- Go for gradual clock correction
    - If fast: Make clock run slower until it synchronizes
    - If slow: Make clock run faster until it synchronizes

Linear compensating function:
- OS can do this: Change the frequency of clock interrupts
    - e.g.: if the system generates an interrupt every 17 ms but clock is too slow: generates an interrupt at (e.g.) 15 ms
- Adjustment changes slope of system time: Linear compensating function （线性补偿函数）
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210142708.png) 

Resynchronization:
- After synchronization period is reached
    - Resynchronize periodically, or
    - The skew is beyond a threshold
- Keep track of adjustments and apply continuously
    - UNIX adjtime system call:
        - int adjtime(struct  timeval  *delta,  struct  timeval  *old-delta)
    - adjusts the system's  notion  of  the current time, advancing or retarding it, by the amount of time specified in the struct timeval pointed to by delta. “old-delta”, output parameter, returns time left uncorrected since last call of “adjtime”

Getting UTC from Top Sources:
- Attach GPS receiver to each computer
    - ± 1 ms of UTC
- Attach WWV (http://tf.nist.gov) radio receiver
    - Obtain time broadcasts from Boulder or DC
    - ± 3 ms of UTC (depending on distance)
- Attach GOES receiver (Geostationary Operational Environmental Satellites, http://www.goes.noaa.gov/)
    - ± 0.1 ms of UTC
- Not practical for every machine
    - Cost, size, convenience, environment

Getting UTC for Client Computers:
- Synchronize clock of a client to a time server that
    - with a more accurate clock, or
    - connected to UTC time source
- Also called external clock synchronization

Synchronizing Clocks by using RPC:
- Simplest synchronization technique
    - Make an RPC to obtain time from the server
    - Set the local clock to the server time
    - ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143116.png)
- Does not count network or processing latency

Cristian’s algorithm:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143221.png)

Cristian’s algorithm: example:
- Send request at 5:08:15.100 (T0)
- Receive response at 5:08:15.900 (T1)
    - Response contains 5:09:25.300 (Tserver)
- Round-trip time is T1 − T0
	- 5:08:15.900 - 5:08:15.100 = 800 ms
- Best guess: timestamp was generated 400 ms ago
- Set the local time to Tserver + round-trip-time/2
	- 5:09:25.300 + 400 = 5:09.25.700
- Accuracy: ± round-trip-time/2
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143355.png)

Cristian’s algorithm: error bound:
- Tmin: Minimum message travel time
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143435.png)

Problems with Cristian’s algorithm:
- Server might fail
- Subject to malicious interference

Berkeley Algorithm:
- Proposed by Gusella & Zatti, 1989 and implemented in BSD version of UNIX
- Aim: synchronize clocks of a group of machines as close as possible (also called internal synchronization)
- Assumes no machine has an accurate time source (i.e., no differentiation of client and server)
- Obtains average from participating computers
- Synchronizes all clocks to average
- One machine is elected (or designated) as the master; others are slaves:
    - Master polls all slaves periodically, asking for their time
        - Cristian’s algorithm can be used to obtain more accurate clock values from other machines by counting network latency
    - When results are collected, compute the average
        - Including master’s time
    - Send each slave the offset that its clock need be adjusted
        - Avoids problems with network delays by sending “offset” instead of “timestamp”
- Algorithm has provisions for ignoring readings from clocks whose skew is too large
    - Compute a fault-tolerant average
- Any slave can take over the master if master fails

Berkeley Algorithm: example:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143658.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143727.png)
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143759.png)

Network Time Protocol (NTP):
- NTP is the most commonly used Internet time protocol and the one provides best accuracy (RFC 1305, http://tf.nist.gov/service/its.htm ). 
- Computers often include NTP software in OS. The client software periodically gets updates from one or more servers (average them).
- Time servers listen to NTP requests on port 123, and reply a UDP/IP data packet in NTP format, which is a 64-bit timestamp in UTC seconds since Jan 1, 1900 with a resolution of 200 pico-s.
- Many NTP client software for PC only gets time from a single server (no averaging). The client is called SNTP (Simple Network Time Protocol, RFC 2030), a simple version of NTP.

NTP synchronization subnet:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210143913.png)
- 1st stratum: machines connected directly to accurate time source
- 2nd stratum: machines synchronized from 1st stratum machines
-  …

NTP goals:
- Enable clients across Internet to be accurately synchronized to UTC despite message delays
    - Use statistical techniques to filter data and improve quality of results
- Provide reliable service
    - Survive lengthy losses of connectivity
    - Redundant paths
    - Redundant servers
- Enable clients to synchronize frequently
    - Adjustment of clocks by using offset (for symmetric mode)
- Provide protection against interference
    - Authenticate source of data

NTP Synchronization Modes:
- Multicast (for quick LANs, low accuracy)
    - server periodically multicasts its time to its clients in the subnet
- Remote Procedure Call (medium accuracy)
    - server responds to client requests with its actual timestamp
    - like Cristian’s algorithm 
- Symmetric mode (high accuracy)
    - used to synchronize between the time servers (peer-peer)
- All messages delivered unreliably with UDP

Symmetric mode:
- The delay between the arrival of a request (at server B) and the dispatch of the reply is NOT negligible:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210144218.png)

Symmetric mode (another expression):
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210144256.png)

Symmetric NTP example:
- ![](https://raw.githubusercontent.com/QizhengZou/Image_hosting_rep/main/20211210144452.png)

Improving accuracy:
- Data filtering from a single source
    - Retain the multiple most recent pairs < oi, di >
    - Filter dispersion: choose oj corresponding to the smallest dj
- Peer-selection: synchronize with lower stratum servers
    - lower stratum numbers, lower synchronization dispersion
- The stratum of a server is dynamically changing, depending on which server it synchronize with

Simple Network Time Protocol (SNTP) RFC 2030:
- Targeted for machines that have no need of full NTP implementation, particularly for machines at the end of synchronization subnet (client nodes)
- SNTP operate in one of the following modes:
    - Unicast mode, the client sends a request to a designated server
    - Multicast mode, the server periodically broadcast/multicast its time to the subnet and does not serve any requests from clients
    - Anycast mode, the client broadcast/multicast a request to the local subnet and takes the first response for time synchronization





















