
 ### References
- https://eli.thegreenplace.net/2020/implementing-raft-part-0-introduction/
- https://raft.github.io/
- https://arorashu.github.io/posts/raft.html


### Nuances in understanding the Algorithm

#### Why does leader commit the logs only from the current term not previous terms
	- 
So if a candidate wanted to become a leader, and wants to make sure that if contains all the committed entries from atleast the majority of the peers, why does it compare if it has the most up-to-date log than the peer in the requestvoterpc but not if it has the same committed log entries