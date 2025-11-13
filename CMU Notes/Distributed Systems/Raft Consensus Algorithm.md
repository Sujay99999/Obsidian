
 ### References
- https://eli.thegreenplace.net/2020/implementing-raft-part-0-introduction/
- https://raft.github.io/
- https://arorashu.github.io/posts/raft.html


## Nuances in understanding the Algorithm

#### Definitions of Correctness


#### Why does leader commit the logs only from the current term not previous terms
- Here, i will be referring to the Figure 8 example from the paper. In the case c, the leader 1 replicates the logs onto the peers 2, 3 and himself. Let us assume he crashes before committing the log entry, then that particular log is said to "Present on majority of the servers but not committed yet".
- This implies a fact that "A log from a older term which is present on the majority of the peers, doesnt guarentee you that it has been committed and returned back to client".
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