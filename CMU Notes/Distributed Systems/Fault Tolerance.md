Vanilla Paxos Algorithm
- it guarantees safety, but not liveness
- benefits of the algorithm
	- Only a value that has been proposed can be choosen
	- Only a single value is choosen
	- Proceess will learn the value, only if it has been chosen before
- Assumptions
	- servers store the information at each step
	- msgs transfer can be long but not corrupted
- First requirement
	- An accpetor must accpet the first proposal it gets
	- Once a proposal with value v is chosen, it must accept higher numbered proposals only in future
- Steps
	- proposer to accepter - prepare(n)

Doubts
- Check if you have to learn about the byzantine quoroum
- Why it is (null, null) in the first prepare-ok
	- because it is prepare-ok(prev accepted proposal, prev accepted value)
- What happens in example 2
- What happens if you get accept before prepare for acceptor