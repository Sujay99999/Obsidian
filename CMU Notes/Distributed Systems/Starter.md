- How do we actually send/ forward the packets from the source to destination
	- How do we identify nodes? (Addressing Problem)
	- How do the messages get routed from one node to another (Routing Problem)
- Addressing
	- There are two major types of addressing
		- Flat Addressing
			- Unique identifier
			- The way the identifier is designed may not be used for routing
			- Used in MAC addresses within the LAN
			- > 281 trillion unique addresses, where this 48bit address is boiled into the equipment
		- Hierarchical Addressing
			- Has a structure such a way it can be used for routing, like phone numbers
			- IP addresses which are used in WAN (32 bit identifier)
- Single Line Ethernet vs Switched Ethernet
	- Single line is the old fashioned one where all the devices are connected across, which is not scalable
	- Switched Ethernet uses Switches as a major component which has a lot of ports, where the data comes at a particular port, and redirected into some port
		- The control processor within the switch basically routes the data based on the "Forwarding Table", which is a mapping of "MAC addresses to Port numbers"
		- And again this faces the problem of Flat addressing, which makes it not scalable
- IP addresses
	- Its a 32 bit address
	- It has 2 components, where first 23 bits are (Network component), and the 9 bits are (Host Component)
	- The IP addresses are assigned using a protocol called DHCP
	- IP Address design
		- Iteration 1: 8 bits for Network and 24 bits for hosts, which doesnt make sense
		- Iteration 2: Class Based assignments
			- First Bits help to identify the Class
			- Lot of Underutilization of address blocks
		- Iteration 3:
			- Usage of CIDR
				- There is a flexible division between the network and host addresses
				- Network prefix can be any size
				- So, when we are having flexible address spaces, we should explicitly put the network size i.e., `/<number of network bits>`
				- The Network prefix can also be represented as 32 bit mask, where all the network bits are 1, and host bits are 0
- Routing
	- So, the next problem is to understand how the packets are routed, where we use the routers, which forwards the packets based on the network prefix it holds to the next hop which is another router
	- So, the forwarding node reduces the forwarding table size by aggregating the table entries it has by using the "Common prefixes" picked up from the destination address
	- The IP address allocation is also done in a hierarchical order where ICANN > ARIN > ISPs > small institutions
	- Routers basically use "Longest Matching Prefix" algorithm, as the longer the match, the closer it is to the host
	- The forwarding tables are populated based on different metrics
		- LANs generally use Broadcasting, learning
		- WANs uses BGP (Border Gateway Protocol)

- Transport Layer helps us to support multiple applications using "Process Multiplexing" using ports
	- TCP
		- It is a protocol which is reliable, In-order and two-way byte stream service
		- It has Flow control, Error control, Congestion Control
		- In Order delivery
			- Uses the concept of "Stop and Wait" where it sequences the packets and expects the receiver to send out the ACKs.
			- It also uses the idea of "Retransmission after timeout" to recover from Packet error, and the timer has to be more conservative as possible
				- Estimated based on the RTT (Round Trip Time)
			- We can improve this throughput with "Sliding Window", where the sliding window size is determined based on the buffer size
			- Packet drops are tricky as the protocol creates space for dropped packets to be added in the next batch transmission
		- Congestion Control
			- We use a congestion control algorithm called CCA, which is a conservative way of maintaining the size



- Time/ Clock Synchronization
	- Coordination of the processes in much important in multiprocessor/ distributed systems than compared to uniprocessor/ centralized systems
- Physical clocks
	- The crystal basically oscillates at a frequency such that each of the oscillation decrements the counter by one, and when the counter reduces to zero, an interrupt is generated. Each interrupt is called "One Clock tick"
	- We have different clocks running at different oscillations, making them out of sync and this difference is known as "Clock Skew"
	- This clock drift which is the difference between actual time and the current time per unit of time is called "Clock drift rate"
	- Physical clocks basically raise 2 questions:
		- How can we keep the clocks within the software in sync with the accurate physical clock?
		- How do we synchronize the clocks with each other
	- How can we keep the clocks within the software in sync with the accurate physical clock?
		- The answer is "Clock Synchronization Algorithms"
			- The goal of the clock synchronization algorithm is to keep the deviation between the respective clocks of any two machines within a specified bound
			- Some of the algorithms are "Cristian's Time Sync" and "Berkeley Algorithm", which have their own shortcomings
			- NTP (Network Time Protocol)
				- The protocol uses "Hierarchy pattern", where it divides the servers into strata, where the server with the actual clock is called as "Stratum 1 server", and a server updates its clock only when it receives the updated time from a server of higher strata
				- Have to be built so that it is reliable and scalable
- Logical Clocks
	- Logical clocks works completely different as it says that "Distributed systems need not rely on the actual source of truth for their clocks, instead they have to agree on the order in which the events occur"
	- Lamport's Logical Clocks
		- If `a` and `b` are events in the same process, and `a` occurs before `b`, then `a->b` is true. The relation is called "happened-before" which is a transient relation
		- If `a` is a event of sending data from `process 1`, and `b` is the event of the receiving the data by `process 2`, then `a->b` is also true
		- if `a->b` and `b->c`, then `a->c` as the events are concurrent
		- So, lamport clock says that each process `p(i)` has a logical clock `l(i)`, then
			- `l(i)` is incremented by 1 before each event at `p(i)`
			- when `p(j)` receives the data from `p(i)` as `(data, t)`, then we synchronize the logical clock as `l(j) = Max(l(j), t)` ![[Screenshot 2025-09-16 at 8.32.02 PM.png]]
		- One of the short comings of lamport clocks is, if `e -> e'`, then `l(e) < l(e')` but if `l(e) < l(e')` , it doesn't imply `e -> e'`.
	- Vector clocks
		- Since we understood that Lamport clocks only provide partial causality, we require better mechanisms to obtain stronger casualty relation between 2 events
		- So, now instead of passing on the logical clock as the "Single counter of the respective process", we pass the "Casual history of all processes" at each transfer
		- The same piggybacking rules of lamport clocks apply as well![[Screenshot 2025-09-16 at 9.26.58 PM.png]]
	



- Mutual exclusion algorithms
	- Why do we need them
		- These algorithms help us to solve the "Critical problem of Coordinating access to shared resources with multiple processes or nodes"
		- In a single computer system, we use the concepts of semaphores which acts like a "Shared memory" across the entire computer, but in case of distributed systems, we dont have "Global Shared memory"
- Centralized algorithm
	- One process is elected as an "Coordinator" which handles the mutual exclusivity, where it allows only one process to access the resource
	- How it works
		- Step 1: `P1` wants to access the resource, it sends a request message to the coordinator
		- Step 2: Permission is granted to `P1`, and replies to `P1` with OK message but at the same time `P2` also asks for the resource
		- Step 3: Coordinator doesnt respond at all, but it also adds `P2` onto its internal queue
		- Step 4: When `P1` completes its work, it again sends a RELEASE message to coordinator
		- Step 5: The coordinator sees the first process on the queue and sends the OK message to it
	- Best parts
		- Fair to all the processes, doesnt let anyone starve
		- Easy implementation, as it only use 3 messages (Request, Ok, Release)
	- Shortcomings
		- Coordinator is single point of failure
		- Coordinator can become a performance bottleneck
- Bully Leader Election Algorithm
	- As we have seen that having a single coordinator can be a single point of failure, hence we require something on top of the Centralized algorithm to make sure that the coordinator is always up and running
	- We assume that each of the process can be identifiable with unique id `P`
	- The aim of the this algorithm is to locate the process with highest id and designate it as the leader
	- How it works
		- Step 1: Out of 7 processes from `P1 to P7`, we initially have `P7` as the leader and it crashes
		- Step 2: Randomly `P4` finds that `P7` is not running in one of the exchanges and sends out a `Election` message to higher processes i.e., `P5, P6 and P7` 
		- Step 3: `P5 and P6` being alive respond back to `P4` with OK
		- Step 4: Seeing this `P5 and P6` send `Election` message to higher processes immediately
		- Step 5: Since `P6` doesn't get a response from `P7`, it assumes as the new coordinator and sends out an `New Leader` message to all other processes
- Total Order Multicast using Lamport Clocks
	- Lamport and vector clocks deal with synchronization  between independent processes, where as when we scale this idea to distributed systems (especially for **replicated systems**), we get "Total Order Multicast"
	- This involves a "Multicast operation" by which **All messages are delivered in the same order to each receiver**.
		- Step 1: `Process A` i.e., sender starts by multicasting the messages to each other, where it sends the "Logical time" of the sender.
		- Step 2: `Process B` i.e., receiver gets the message and puts it onto local queue and sort the queue based on the timestamp
		- Step 3: Each of the receivers again multicasts the acknowledgement to all others
	- Eventually all the members with have the same copy if the local queue
	- A process can deliver the queued message to the application layer only when it has been acknowledged by all other members ensuring safe replication of actions across different systems
	- Total Order Multicast with vector clocks provide more guarantee on the causality but has unnecessary overhead of data


- Fault tolerance 
- only bkward recovery alogs
	- checkpointing - chandy lamport 
	- logging
- assunptions for bkd recovery algos
- checkpointing algo
	- que - how do we take a snapshot of a dist sys
	- what is the basic intuition we build
	- how do we apply the logic to dist sys now ?
	- what is consistent snapshot, and how is it connected with lamport implies 
	- token transistion example
		- independent checkpointing --  and how it leads to global checkpointing
		- How recovery is done in independent checkpointing
		- How coordinated checkpointing works - chandy lamport 
			- How snapshot is initiated, continued, and terminated
			- algo slide
- Chandy lamport coordinated checkpointing algorithm
	- What is tries to say
		- For any event that is recorded somewhere in the snapshot, any events that happened before that particular event is also recorded in that snapshot
- logging
	- WAL
	- how wal is implmented in dbs
	- come back to this example after completing the 2pc and how atomicity is actually maintained
	- Understand on how the checkpointing and logging complement each other
- can we use bkd recovery algos during failures
- distributed consistency vs acid consistency
- types of consistency (understand the semantics)
	- Strict consistency
	- sequential consistency
		- Check wdtm by global order cannot reorder the local order of operations
	- Casual consistency
		- Understand what exactly is casual writes vs concurrent writes
		- SMR
		- Understand why consensus is impossible, what is liveness an safety in FLR
- Paxos algo
	- What is it solving i.e., when a value is choosen
	- How a quorum is formed in first place





GoLang: [[GoLang with Multithreading concepts]]


- Exam NOtes
	- mut ex vs others - know stuff
	- concurrency