
 ### References
- https://eli.thegreenplace.net/2020/implementing-raft-part-0-introduction/
- https://raft.github.io/
- https://arorashu.github.io/posts/raft.html

## Definition of Correctness in Consensus Algorithms wrt Raft

#### Safety: 
Guaranteeing that only one value or decision is agreed upon. Conflicting decisions are prevented to maintain data consistency.

#### Liveness:
Ensures that the system can make progress and continue to agree on new values or decisions even in the absence of failures.

### How Raft guarantees Safety
- #### Election Safety
	- Property: At most one leader can be elected in a given term
	- How it Works:
		- Every Peer in the network, is allowed to vote only once in a given term
		- Any Candidate must obtain majority of the votes to become a leader, ensuring that no other peer can become a leader in that particular term 
- #### Leader Append Only
	- Property: Leader never overwrites or deletes entries in its log; it only appends new entries
	- How it Works:
		- A leader can never assume that any log from previous terms is already committed
		- When a leader undergoes network partition, but joins back it indirectly continues the process of committing the previous entries along with the current ones
- #### Log Matching
	- Property: If two logs contain an entry with the same index and term, then the logs are identical in all entries up through the given index
	- How It Works:
		- This is guaranteed by the fact that, any AppendEntry RPC performs a recursive check of finding the last common log while appending new entries from the leader, and as the recursion is completed, the leader and the follower have same logs
		- The above property can be broken down into another property as "If two entries from different peers, have the same log index and the term, then they store the same command", which is guaranteed by the simple fact that, the log entries is a array based structure, where we can't have multiple logs at the same index.
- #### Leader Completeness
	- Property: If a log entry is committed in a given term, then that entry will be present in the logs of the leaders for all higher numbered terms
	- How it Works:
		- This property is satisfied using multiple properties.
			- Leader Append Only - A leader never deletes/ overwrites it the entries in its log 
			- A leader only commits the entries from the current term, which indirectly commits the log entries from previous terms as well - Section 5.4.2
			- A entry when present on the majority on the peers is said to be committed only when the leader acknowledges it - Section 5.4.2 and 5.4.1
- #### State Machine Safety
	- Property: If a server has applied a log entry at a given index to its state machine, so no other server will ever apply a different log entry for the same index.
	- How it Works:
		- This property is satisfied using multiple properties.
			- Leader completeness - If a log entry is committed in a given term, then that entry will be present in the logs of the leaders for all higher numbered terms
			- Log Matching Property - If two logs contain an entry with the same index and term, then the logs are identical in all entries up through the given index

### How Raft guarantees Liveness
- #### Election Timeout Randomness
	- Property: All the election timeouts for the peers are always randomized
	- How it Works:
		- Ensures that there are less chances of split votes
- #### Timing Requirement
	- Property: Will be able to elect and maintain a steady leader as long as the system satisfies the Timing requirement i.e., `broadcastTime(RPC) << ElectionTimeout << Mean Time Between failures of the server (MTBF)`
	- How it Works:
		- The broadcast time should be an order of magnitude less than the election timeouts, so that the leaders can send the heartbeat messages preventing unnecessary elections
		- The election timeouts must be less than MTBF so that there can be a steady progress i.e., When the leader crashes, another peer can carry on the process, while the original leader is recovering. 


## Nuances in understanding the Algorithm

#### Why does leader commit the logs only from the current term not previous terms
- Here, i will be referring to the Figure 8 example from the paper. In the case c, the leader 1 replicates the logs onto the peers 2, 3 and himself. Let us assume he crashes before committing the log entry, then that particular log is said to "Present on majority of the servers but not committed yet".
- This implies a fact that "A log from a older term which is present on the majority of the peers, doesn't guarantee you that it has been committed and returned back to client".
- This makes more sense, when the network is unstable and might lead to these situations, i.e., If the leader i.e., Peer 1 comes back after disconnecting himself, this means that his current term would be greater than the term he had in his previous reign. 
- Upon seeing the log entry from the previous reign, he can't simply commit it as it violates the safety property.
- Another important fact to be noted is that "In terms of non-leader peer point of view, any log appended to its entries, is only confirmed to be committed by another round of AppendEntry RPC" i.e., Let us say Peer 2 (Non leader) recieved an AppendEntry RPC from Peer 2 (Non Leader) (Round 1), which enables the Peer 2 to append the entry, but not commit it. And during the next set of heartbeat, the Peer 2 gets to know that the Log entry it previously appended is now being committed.
- This tells us one fact that "It is possible for a Candidate to become Leader when it has higher term but missing entries". This actually is tied to the Section 5.4.1 i.e., A candidate must contact a majority of the cluster in order to be elected, which means that every committed entry must be present in at least one of those servers. If the candidate’s log is at least as up-to-date as any other log in that majority then it will hold all the committed entries.
- Up-to-Date Log verification 
```
def is_more_up_to_date(candidate_log, voter_log):
    if candidate_log.last_term != voter_log.last_term:
        return candidate_log.last_term > voter_log.last_term
    else:
        return candidate_log.length >= voter_log.length
```


#### So if a candidate wanted to become a leader, and wants to make sure that if contains all the committed entries from atleast the majority of the peers, why does it compare if it has the most up-to-date log than the peer in the requestvoterpc but not if it has the same committed log entries
- In the entire implementation of the protocol, there is no single source of truth who is always told to be available to check whether if a particular log entry is committed or not.
- Hence, any candidate who wants to become a leader always must make sure that its logs are up-to-date with atleast majority of the servers, which indirectly also confirms the fact that it has the previous committed entries