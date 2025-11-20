![[Screenshot 2025-11-19 at 8.19.02 PM.png]]

- ### High Performance Computing
	- One of the major differences between the HPC and the normal computing is that HPC doesnt have their own Disk storage, instead it is separate from the compute nodes which typically large amounts of RAM
	- They have this notion of Global storage layer, where all the nodes read and write to that layer
	- They also have really fast networking layer
	- #### HPC Programming Model
		- The applications written for HPC are very Machine-Dependent i.e., we write the programs which are specific for the hardware it is executing on.
		- Even the software packages between the applications and the hardware are either very low or very thin
		- Uses "Message Passing Model"
			- The processes communicate and synchronize via the exchange of messages
		- The processes are long lived
		- The processes explicitly exploit spatial locality
		- Makes sure that the resources are highly utilized
		- All the data required during computation is held in the memory
		- One of the biggest disadvantage is that HPC are generally very intolerant of any variability i.e., they are tightly coupled processes, which essentially says that failure of one process prevents the other processes from progressing
		- Fault tolerance in HPC is typically achieved using simple Checkpoint and recovery mechanisms
![[Screenshot 2025-11-19 at 6.56.43 PM.png]]


		- Message Passing Interface (MPI)
			- This a standardized communication protocol for the HPC for programming the parallel computers
			- Uses virtual topology
				- Which helps one process to find the number of processes, and other details [ADD MORE]
			- MPI has a set of standard set of group communication methods
				- For Sending
					- Barrier Method
						- The synchronization mechanism for these processes is called "Barrier Method"
						- They help to create the global synchronization points
					- Broadcast Method
						- Method using which an individual process sends data to multiple other processes at same time
					- Scatter Method
				- For Receiving
					- Gather method
					- All-to-All Method

- ### [[Actor Model]]
- [[MapReduce]]