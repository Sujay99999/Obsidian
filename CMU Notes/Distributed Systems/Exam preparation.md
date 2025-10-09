Midsem 1
- Homeworks
- Lecture slides
- Previous year question papers
- Logistics
	- intro


- Distributed File System [Part 1 and Part 2]
	- AFS is more about scaling as much as possible and NFS is more about the reliability
	- We can different filesystems designed based on different workload philosophies
	- AFS
		- Design goal: Scale to support tens os thousands of users
		- Strategy: Aggressive caching, whole file operations
		- Consistency approach: Server basically sends callbacks to notify the clients of the changes (Server is Stateful)
		- Caches the entire files as units
		- Wasteful for small edits to large files + Losing local changes when caches are overwritten
		- Better for consistency
	- NFS
		- Design goal: Simple, ROBUST file system
		- Strategy: Stateless server + Minimal caching at client side (Server is Stateless)
		- Consistency approach: Client polls the server to validate the cache
		- Caches only 4KB chunks independently
		- Efficient for Large files, having partial access to the files
		- Can lead to mixed versions of the file
		- Bad for consistency
	- Four Fundamental Design Dimensions
		- Performance
			- Understand that Network latency is 3x slower than Disk reads
		- Consistency
			- Make sure that multiple clients must have same coherent data
		- Naming
			- Make sure that we have a global scheme of locating the files
		- Security
			- Maintain basic data integrity, authentication, and authorization
	- How one decision of a dimension impacts others
		- More performance optimizations -> Less consistency
		- Better Naming schema -> Security boundary issues
		- Better security mechanisms -> Less performance
	- Why simple approach of DFS wont work
		- Idea: All the clients forward all the operations to the server using RPC approach
		- Benefit: No coherence issues at all
		- Downsides: The network latency + crowding of clients creates huge bottleneck
		- How to improve: Implement Caching
	- Caching (Client-side)
		- Store the copies of the frequently accessed data
		- Trying to transform the network operations to local operations
		- First access is slow, but the subsequent accesses are much faster
		- Reduces congestion at server level
		- Questions to ask
			- Update visibility - when should new writes changes are visible to others
			- Cache invalidation - when does the client understand that its cache is stale
	- Consistency spectrum
		- Each of the applications can have different types of consistency such as Strong consistency or weaker consistency
		- Strong consistency - Less performance - Such as Simple server solution + RPC
		- Weaker consistency - More performance - No guarantees at all
		- So, we have to design the application with the weakest consistency model that it can tolerate
	- Caching strategies
		- Broadcasting Invalidations
			- When file is modified, the server broadcasts the invalidates the caches for each client. When client receives this message, discards its cache, and when that particular client performs the operation, it gets the entire data and caches again
			- Advantages - Simple to implement
			- Disadvantages - Explosion of messages, leading to unscalability
		- Check on Use
			- When client is about to do any operation, it checks with the server if the stored cache is valid
			- Advantages - Simple to implement, reduces the RPC bandwidth
			- Disadvantages 
				- Every read triggers the same round trip
				- Server is bombarded with the validation requests
		- Time based Check on Use (NFS v2)
			- Client when caches the data for the first time, stores the timestamp, and when the client reads the data again within the 60sec limit, it doesnt validate with server, else restart the process
			- Advantages - Server load is reduced
			- Disadvantages - Strict consistency is lost, expecting clients can tolerate a minute long inconsistency window
		- Callbacks (AFS)
			- When client caches a file, the server stores its entry in a list. Server callbacks the client when the file is changed. Client trusts the client until callbacks are received
			- Advantages - Good performance gain
			- Disadvantages
				- Server must gracefully recover from failures
		- Leases
			- A lease grants a client the right to cache the data for particular time period. The server promises not to allow modifications without notification during the lease period
			- Read lease
				- Multiple clients can hold this lease
				- Guarantees that no writes will be done in this period such as 30sec
			- Write lease
				- Only one client can get this
				- Any modification, is instantly updated at the server
				- Must write through before the lease expires
			- Advantages - the max inconsistency == lease duration + better server recovery
			- Implementation requirements
				- Need clock sync between all of them
				- Grace periods for latency issues
	- Consistency Semantics


- Time Sync
	- Keeping the track of the time is hard
	- Different Time standards
		- UT1 - Universal time - Based on the astronomical observations - greenwich mean time
		- TAI - Temps atomique international - based on cycles of radiation from Cesium- Diverged from UT1 due to slowing of earth
		- UTC - TAI + leap seconds to be within 0.9s of UT1 - Currently 30 leap secs
	- In a perfect network, we expect that there is propagation delay of d secs, then the synchronization error is at most d/2 (sender is at t time, then receiver is at t+d/2)
	- But you always have unreliable network
	- Cristian Time Sync
		- We basically have a time source S, which has access to the UTC clock, and different processes/ nodes talk to this S to set their time
		- Process p requests time in m(r) and receives time value t in m(t) from S
		- p sets its clock to t + RTT/2
		- Accuracy ± (RTT/2 - min) [TODO if we need more details]
		- Problems - S might fail; Only reasonable to use if RTTs are short; Probabilistic nature
	- Berkeley Algorithm
		- An algorithm for internal synchronization of a group of computers
		- A master node/ time deamon polls to collect clock values from the others (workers)
		- The master node uses RTT to estimate the workers’ clock values
		- It takes an average (eliminating any outliers)
		- It sends the required adjustment to the workers (better than sending the time which depends on the RTT)
		- Benefit - We can reelect masters if we lose one; Outliers are discarded; Eventual correctness in time
	- NTP
		- There is need to sync clocks across internet; Its for broader networks, where as the Cristian and Berkeley are LAN devices
		- Uses hierarchy of servers, where primary servers are connected to UTC, secondary servers are connected to primary, our devices are connected to secondary
		- Syncing between times of these servers is similar to Cristian algo
		- All msgs uses UDP
	- Lamport Clocks
		- We need to only capture the “happens before” relationship between events
		- Almost can be understood as causality - a has caused b
		- The expression a → b is read “event a happens before event b” and means that all processes agree that first event a occurs, then afterward, event b occurs.
		- The “happened before” relation is transitive -  if a->b and b->c, then a->c
		- So, if we have two events are NOT causal, then they are concurrent i.e., a || e
		- Each process pi has a logical clock, L(i) starts with 0, which can be used to apply logical timestamps to events [Check to remove this]
			- Rule 1: L(i) is incremented by 1 before each event at process p(i)
			- Rule 2:
				- when process p(i) sends message m, it piggybacks(adding 1) t = L(i)
				- when p(j) receives (m,t) it sets L(j) := max(L(j), t) and applies Rule 1 before time stamping the event receive (m)
		- If e -> f, then L(e) < L(f); If L(e) < L(f), then its NOT e < f, can be e || f 
		- Disadvantage - They dont capture causality completely
	- Total Order lamport clocks
		- Many systems require a total-ordering of events, not a partial-ordering
		- Use Lamport’s algorithm, but break ties using the process ID
		- Only used to BREAK ties between concurrent events, not capture casuality
	- Vector clocks
		- Covers overcomings of lamport clocks of not capturing causality
		- V(e) < V(f), then e -> f


- Mutual exclusion algorithms
	- There are two different types of scaling - up(vertical) or out(horizontal)
	- In single systems, we have locks to make sure that we dont have inconsistency
		- Mutex requirements
			- Correctness/ Safety - At most only one process can use CS
			- Fairness - No process will hold onto lock forever; Any process that makes a request must be granted lock
	- Distributed mutex
		- Requirements - low msg overhead; no bottlenecks; tolerate unreliable network; 
		- Assumptions - Number of processes are fixed at n; NO process fails
	- Centralized algorithm
		- Coordinator gets a REQUEST from the clients, where he sends back OK. If another client asks, then there is no reply, but put onto queue
		- When first client is done, sends RELEASE to C, then it sends OK to top most element of queue
		- Correctness - Obviously safe; Fairness depends on the queueing policy
		- Performance - We send 3 total msgs in one cycle (REQUEST, OK, RELEASE)
		- Issues - Bottleneck due to C; C can be single point of failure i.e., client cant say whether C is dead or its queued
	- Bully Leader Algorithm
		- Stage 1: Process P notices that leader has failed
		- Stage 2: Election Algorithm, e.g., Bully Algorithm
			- P sends an ELECTION message to all processes with higher numbers and expects some replies OK from higher processes
			- If no one responds, P wins the election and becomes coordinator by broadcasting the COORDINATOR msg.
			- If one of the higher-ups answers, it takes over. P’s job is done.
		- Correctness - Not completely fair
	- Ring Algorithm
		- Clients who detected that C is failed, sends the election msg - process IDs in a array to the next node
		- Eventually they receive back the same array back, and check the highest ID guy
	- Decentralized algorithm
		- Opposite extreme to centralized algorithm
		- Assume that there are n coordinators
			- Get a majority vote from m (messages) > n/2 coordinators
			- Reply immediately with GRANT or DENY
		- If it doesnt get majority - retry later
		- Used when you the case multiple servers join/ drop frequently 
		- Correctness - Majority ensures safety; Fairness depends on random chance
		- Performance - 2m + m [TODO] msgs per attempt to get majority; Unbounded number of msgs per cycle
		- Issues - Potential Starvation; Backoff and retry problem; Forgets vote on reboot
	- Totally ordered multicast
		- This says that if we had a total order, we could rely on it to grant access to a shared resource
		- The ordering in the priority queue based on the timestamp makes sure that the msgs are in order
		- NOTE: Acks are also multicasted to all other processes
	- Lamport Mutual exclusion
		- In this, we send the Ack only to the requestor, and once a process recieves all the acks, then it can access the CS
		- When node i wants to enter C.S., it sends time-stamped request to all other nodes (including itself)
			- Wait for replies from all other nodes.
			- If own request is at the head of its queue and all replies have been received, enter C.S.
			- Upon exiting C.S., remove its request from the queue and send a release message to every process.
		- Correctness - Yes, it has the fairness
		- Performance - 3*(n-1) i.e., process i sends n-1 req, n-1 reply, n-1 release msgs
		- Issues - only performance issues
	- Ricart Agarwala Exclusion
		- It uses better optimization than Lamport mutex algo
		- Same concept as Lamport, but
		- On the receiving processes end, 3 possible cases:
			1. Receiver uninterested in resource = If the receiver is not accessing the resource and does not want to access it, it sends back an OK message to the sender.
			2. Receiver already has access to resource. If the receiver already has access to the resource, it simply does not reply. Instead, it queues the request.
			3. Receiver wants access to resource, but doesn’t have it yet. If the receiver
				wants to access the resource as well but has not yet done so, it compares the timestamp of the incoming message with the one contained in the message that it has sent everyone. The lowest one wins. If the incoming message has a lower timestamp, the receiver sends back an OK message. If its own message has a lower timestamp, the receiver queues the incoming request and sends nothing.
		- Correctness - Yes
		- Performance - Each cycle has 2*(n-1) msgs
		- Advantages - Deadlock free, Starvation free
	- Token ring
		- Ideal when we want to minimize the msg overhead as we simply use the token only to pass the control
		- Can be harmful, as the token is passed unncessarily to everyone irrespective whether they want it ot
		- Correctness - will be unfair in large systems due to delay
		- Performance - Number of messages are 1 to infinity per cycle
		- Issues - Token is lost


- Remote Procedure Call (RPC)
	- RPCs allow nodes to call functions that execute on other nodes using convenient syntax
	- It's a programming model for distributed computation, making it more transparent especially "Access level transparency"
		- To the client, it's going to look just like calling a function
		- To the server, it's going to look just like implementing a function that gets called
	- Low transparency - More complexity; looks like distributed systems; Less abstractions (EC2)
	- High transparency - Less complexity; looks like monolith; More abstractions (Google photos)
	- The key difference from a local function call is what happens when things fail
	- Complete transparency is not possible
	- Distributed and local function call performance different
	- RPC falls under "Middleware services"
	- RPC in Go
		- Provides access to the exported methods of an object across a network or other I/O connection.
		- A server registers an object (like rpc.register), making it visible as a service with the name of the type of the object by exporting
		- After registration, exported methods of the object will be accessible remotely.
		- Multiple objects(services) of same type is not possible
	- Stubs - Code that handles de/serializing RPC arguments
	- Stubs generally use IDL - interface definition language - protobufs
	- If the arguments in the service changes, we have to make change both server and client (but if we use some rpc framework, we can have single point of change due to IDL)
	- RPC workflow
	    1. client procedure calls the client stub in the normal way
		2. client stub builds a message and calls the local OS
		3. client OS sends the message to the remote OS
		4. the remote OS gives the message to the server stub
		5. server stub unpacks the parameters and calls the server
		6. server does the work and returns the result to the stub
		7. server stub packs the result in a message and calls its local OS
		8. server’s OS sends the message to the client’s OS
		9. client’s OS gives the message to the client stub
		10. client stub unpacks the result and returns it to the client
	- Challenges in RPC, when implemented in distributed systems
		- Memory Access - Cant use pointers, need to use Pass by Value
		- Failures of systems - RPC must handle these failures as if LPC
			- Communication failures - happens due to unreliable network
			- Machine failures - server or client fails
			- We need to make the partial failures transparent to client
				- We can treat these partial failures treated as complete failures and reboot from scratch
				- But this idea is not practical
			- So, instead of treating as LPC, we can tolerate failures using semantics i.e., we tell the client about the failure (applying them on client's application layer)
	- Semantics of RPC
		- At least once - For idempotent operations - Just keep retrying on client side until you get a response.
			- If the client receives a response message to a request:
			    - then the request was exectued at least one time (perhaps more than once)
			- If the client does not receive a response message:
			    - then the request _might_ have been executed (or it might not, or it might have been exectued more than once)
		- At most once - Server might get same request twice, Must re-send previous reply and not process request
			- If the client receives a response message to a request:
			    - then the request was exectued exactly once
			- If the client does not receive a response message:
			    - then the request _might_ have been executed (or it might not)
		- Exactly once - Impossible in practice - A message is guaranteed to be sent and received exactly-once.
			- If the client receives a response message to a request:
			    - then the request was exectued exactly once
			- If the client does not receive a response message:
			    - it blocks forever retransmitting the request and waiting until it receives a response



- Two phase commit
	- Assumptions
		- Ignoring failures, as concurrency is main concern
		- How we deal with failures - We use some checkpointing so that there is recovery
	- Challenges with composite operations
		- Lack of atomicity
		- Fault tolerance
	- Solved using transactions, and they fully execute or abort
	- ACID
		- Atomicity: Each transaction completes in its entirely, or is aborted. If aborted, should not have effect on the shared global state.
		- Consistency: Each transaction preserves a set of invariants about global state. (Nature of invariants is system dependent). Example: in a bank system, law of conservation of money 
		- Isolation: serializability -> each transaction executes as if it were the only one with the ability to read/write shared global state - So, cant have any kind of interleaving operations
		- Durability: Once a transaction has been completed, or “committed”, its effects will persist, even in the presence of failures.
	- One way to implement perfect txn on a single machine is using "Global ordering of locks", which guarantees strong Isolation. 
	- Best practice of this global ordering of locks is "two phase locking"
		- Phase 1: Acquire locks, no locks are released
		- Phase 2: Release locks, no locks are acquired
	- Deadlocks can occur at 2PL, but can be prevented using wait-for-graphs + timeouts
	- 2PC
		- In case of distributed systems, 2PC is a better way to follow Atomicity and Consistency, where all the servers reach the same decision at the end.
		- Only single decision is reached by all nodes at the end
		- Assumptions - Reliable, in order communication; Asynchronous network; Fail-recover with stable storage
		- Phase 1A: Coordinator sends "PREPARE" to participants
		- Phase 1B: Participants log vote to disk and responds with vote "VoteCommit" or "VoteAbort" - must persist to disk before sending message; 
		- Phase 2A: Coordinator checks votes. If all VoteCommit: write to disk and send DoCommit; else write to disk and send DoAbort - Commit point: after the coordinator has written Commit to stable storage
		- Phase 2B: Participants receive the final decision, write Commit/Abort to disk, and send Ack back to coordinator, coordinator logs END and ends the transaction
	- 2PC correctness - Atomicity is ensured
	- Liveness is maintained in 2PC using timeouts i.e., not blocking everyone
	- Failure scenarios
		- Participant fails with no logged decision
			- During execution: Coordinator sees timeout at participant, aborts and informs everyone
			- Upon recovery: participant does not see any logged decision, aborts transaction
		- Participant fails with logged VoteAbort
			- During execution: Coordinator sees VoteAbort, aborts (or) Coordinator sees timeout at participant, aborts and informs everyone
			- Upon recovery: participant unilaterally aborts after seeing local “VoteAbort”
		- Participant fails with logged VoteCommit
			- During execution: Coordinator sees VoteCommit, checks other decision
			- Upon recovery: participant asks the coordinator
		- Coordinator fails with logged COMMIT and END
			- Participants have already logged the final decision and sent Ack
		- Coordinator fails before sending out decision, all participants have logged VoteCommit
			- Participants cannot proceed until coordinator comes back up
			- Coordinator sends decision to participants once up
	- 2PC is BLOCKING protocol, where Paxos is non-blocking protocol
	-  2PC generally uses concept of 2PL, ensuring isolation as well with atomicity and consistency

- Fault tolerance
	- Backward recovery
		- Return the incorrect state to some previous correct state (via a checkpoint), then continue executing.; Retransmission; Easier to implement; Expensive
	- Forward recovery
		- bring the system into a correct new state, from which it can then continue to execute.; Erasure coding (reconstruct from received packets); Harder to implement; Recovery could be faster
	- Checkpointing (Backward recovery)
		- Assumption: Stable, persistant storage
		- Consistent snapshot: For any recorded event e somewhere in the snapshot, any events that happened before e should also be recorded in the snapshot
		- Independent checkpointing records only local state; Easier to implement; Difficult to recover consistently; Prone to cascaded rollbacks
		- Coordinated checkpointing 
			- Assumptions: Global state changes when event happens; Channels are FIFO
	- Chandy Lamport Algorithm
		- Does not require “stopping” the application when recording snapshot
		- Multiple processes can initiate snapshots at the same time
		- Step 1: Snapshot initiation by Process P
			- P records its own state
			- P sends marker to all other processes on OUTGOING channels
			- P starts recording on its INCOMING channels i.e., < recording >
		- Step 2: Snapshot propagation, when process P(i) receives marker message
			- If receiving the marker for the first time (Sending a marker message is counted as receiving)
				- Records its own state
				- Mark that INCOMING channel as empty
				- Propagate marker to all other processes on OUTGOING channels
				- Start recording incoming messages on other channels
			- Else
				- Stop recording on that incoming channel
		- Step 3: Snapshot termination
			- All processes have received a marker in their incoming channels
	- Tradeoffs of using frequent checkpoints
		- Pros: Less rollbacks
		- Cons: Overhead during normal operation (Significant I/O traffic)
	- Logging
		- Complements checkpointing
		- WAL - Write ahead logging - Log operations to storage before operating
		- Recovery: Go through the logs and replay them
	- If frequent checkpointing is slow, then have fine grained logs
	- If logs are growing unbounded, then use periodic checkpointing
	- Checkpointing used to record coarse-grained system snapshots
	- Logging used to record fine-grained system operations
	- Recovery: rollback to most recent snapshot, then continue recovering using log records
	- Consistency types
		- Strict: System has a global clock; Writes instantly to every replica; Read always returns from latest write
		- Sequential: Weaker than strict; Lead to global order
		- Causal: All nodes see potentially causally related writes in the same order, but writes on different replicas might be concurrent
	- SMR
		- replicas should be in sync with each other
		- Any operation should be propagated to all replicas
		- Replicas reach consensus through consistent log ordering
	- Consensus is impossible due to 2 assumptions: Deterministic algo; We need to fully achieve consensus (Safety+Liveness) but possible by compromising
	- Paxos
		- Correctness(safety): yes, we get that as we get a single value to be chosen, and also guarantee that the value is proposed by some node
		- Liveness - Not guaranteed, sacrificed in favor of correctness
		- Fault tolerance - if < f servers are failed, still we can reach consensus
		- 2f+1 nodes, f+1 quorum size
		- At least one acceptor common to distinct quorums
		- An acceptor accepts only a single proposal
		- All proposals are globally ordered using a unique numbering
		- Once chosen, the Proposers must block unknown future proposals through a “lock”
	- Paxos workflow
		- Proposers
			1) 1 Choose new proposal number n
			2) 2 Broadcast Prepare(n) to all servers
			3) 4 When responses received from majority:
				1) If any acceptedValues returned, replace value with acceptedValue for highest acceptedProposal
			 4) 5. Broadcast Accept(n,value) to all servers
			 5) 7. When responses received from majority:
				 1) If majority Accept-OK(), value is definitely chosen
				 2) Could retry if no majority accept
		 - Acceptors
			 - 3. Respond to Prepare(n):
				 - If n > minProposal then minProposal = n
					 - Prepare-OK(acceptedProposal, acceptedValue)
				 - else Prepare-REJECT()
			 - 6. Respond to Accept(n,value):
				 - If n ≥ minProposal then acceptedProposal = minProposal = n and acceptedValue = value
					 - Accept-OK()
				 - else Accept-REJECT()![[Screenshot 2025-10-09 at 7.26.41 AM.png]]
	 - Dueling can occur leading to blocking
	 - Adversary can spam request with higher numbers


- Go style concurrency
	- Reasons that 2 processes interact with each other in first place
		- Processes may need to cooperate to carry out some task - leading to Condition Synchronization
		- Processes may compete for exclusive use of services or resources - leading to Mutual exclusion
	- Critical Section: piece of code accessing a shared resource, usually variables or data structures
	- Race Condition: Multiple threads of execution enter CS at the same time, update shared resource, leading to undesirable outcome
	- Indeterminate Program: One or more Race Conditions, output of program depending on ordering, non deterministic
	- Mutual Exclusion
		- guarantee that only a single thread/process enters a critical section, avoiding races.
		- Desired properties of mutex
			- (Correctness): single process in CS at one time
			- Progress (Efficiency): Processes don’t wait for available resources, or no spin-locks => no wasted resources
			- Bounded Waiting (Fairness): No process waits for ever for a resource, i.e. a notion of fairness
	- Semaphores
		- A semaphore is an integer variable with two atomic operations
		- **P(x)** (proberen = "to test"): `while (x == 0) wait; x--`
		- **V(x)** (verhogen = "to increment"): `x++`
	- Condition variables provide efficient waiting (better than spin-locks):
		- **Wait()**: Atomically releases mutex & suspends. When resumed, re-acquires mutex
		- **Signal()**: Wakes up at least one waiting thread (NO-OP if none waiting)
		- **Broadcast()**: Wakes up ALL waiting threads



![[Screenshot 2025-10-09 at 8.36.53 AM.png]]




- Networking
	- **Circuit Switching vs Packet Switching**

| Feature             | Circuit Switching                     | Packet Switching                            |
| ------------------- | ------------------------------------- | ------------------------------------------- |
| Resource Allocation | Dedicated circuit for each connection | Shared links via _statistical multiplexing_ |
| Reliability         | High, predictable                     | Variable delay, congestion possible         |
| Scalability         | Poor (per-flow state)                 | Excellent                                   |
| Example             | Telephone system                      | Internet (TCP/IP)                           |

- **Packet switching advantages**: bursty traffic efficiency, robustness, dynamic routing.
- **Circuit switching advantages**: consistent QoS, low jitter (good for voice)
- **Statistical Multiplexing**
	- Many flows share the same link over time.
	- Uses **FIFO queues**; bursty traffic smoothed out across flows.
	- Queueing → delay, potential congestion (key for TCP).
- Multiplexing/Demultiplexing
	- Each layer can support multiple protocols (e.g., IP → TCP/UDP).
	- Headers include **demux fields** (e.g., protocol number in IP).
- End-to-End Argument
	- **Principle**: Implement functionality at endpoints unless it can’t be efficiently done there.
	- Example: Reliability
	    - Network-level retransmission = wasteful if app must verify data anyway.
	    - End-host-level reliability (TCP) preferred.   
	- **Exceptions**: Hop-by-hop reliability may help for _high-loss links_ (e.g., satellite).
- Addressing Schemes

|Type|Description|Pros|Cons|
|---|---|---|---|
|Flat|e.g., MAC address (48-bit, unique per NIC)|Plug & play|Not scalable (huge tables)|
|Hierarchical|e.g., IP address (prefix + host)|Scalable, aggregatable|Complex management|
- IPv4 Basics
- 32-bit address = Network prefix + Host ID
- Dotted decimal: e.g., `12.34.158.69`
- Assigned dynamically (DHCP

Classful Addressing (Historic)

| Class | Leading Bits | Network Bits | Host Bits | Range                     | Notes                   |     |
| ----- | ------------ | ------------ | --------- | ------------------------- | ----------------------- | --- |
| A     | 0            | 8            | 24        | 1.0.0.0 – 126.0.0.0       | For very large networks |     |
| B     | 10           | 16           | 16        | 128.0.0.0 – 191.255.0.0   | Medium                  |     |
| C     | 110          | 24           | 8         | 192.0.0.0 – 223.255.255.0 | Small                   |     |
- Routers combine entries going to same network.
- Reduces forwarding table size:
- TCP Performance Example

|Variable|Symbol|Example|
|---|---|---|
|Packet size|P|1500 bytes|
|RTT|T|100 ms|
|Window|W|3 packets|
|Throughput|(W × P × 8) / T|360 Kbps|


