### What is MapReduce
- MapReduce is a typical programming model which is generally used for processing large datasets
- Users specify a Map function that processes the given input key-value pairs into intermediate key-value pairs and Reduce function basically merges all of the intermediate values of the same intermediate key.
- The Map and Reduce functions are deterministic in nature which allows us to produce the same results when run multiple times.

### Flow of MapReduce Program
- Step 1: The given dataset by the user is initially split into multiple HDFS/GFS blocks of size 128MB (User can control this), and these blocks of data are fed to the Map tasks.
- Step 2: Each of the nodes which are assigned to perform the Map tasks processes one or many HDFS blocks encapsulated in what is called a **split**. Each map task is always responsible for processing only one split. Thus, the number of splits dictates the number of map tasks in a MapReduce job, which in return, dictates the overall map parallelism.
- Step 3: Once the map phase is completed, these tasks store the partitions on **Local Disks (Not on HDFS)** and then hashes them based on either the key or other (Eg: Hash(key)modR), which allows the partitions to be split across the Reduce task nodes.
- Step 4: Each reduce task collects (shuffles) its corresponding partitions from local disks, merges and sorts them, runs a user-defined reduce function, and stores the final result in HDFS. Thus, the reduce phase is usually broken into shuffle, merge and sort, and reduce stages.

### Typical Cluster Configuration
- The Compute and storage are generally present together unlike the HPC operations, where we have the Network as the conventional ethernet switches.
- The MapReduce jobs typically use "Synchronous or Asynchronous" communication model to implement the "Incremental Data progression" that happens across Map, Combiner and Reduce tasks.
![[Pasted image 20251124221210.png]]
- The master is denoted as JobTracker and each slave is called TaskTracker. As shown, the JobTracker and the TaskTrackers communicate over the network using a heartbeat mechanism.


### Fault Tolerance in MapReduce
- Fault tolerance in MapReduce is deemed necessary because of the fact that this is being run on the commodity machines of the datacenters, where there is a much more chance of the node being failed.
- MapReduce applies two mechanisms to achieve Fault tolerance i.e., "Data redundancy" and "Task Resiliency".
- Data Redundancy
	- Data redundancy is applied at the storage layer. Specifically, HDFS reliability retains HDFS blocks by maintaining multiple replicas per block (by default, three replicas) at physically separate machines.
	- Clearly, this enables MapReduce to tolerate corrupted blocks and any nodes easily. If a block is lost due to hardware or software failure, another replica at a different node can always be located and read in a way totally transparent to HDFS users. HDFS computes checksums using a cycle redundancy check (CRC-32) for all data written to it, and, by default, verifies checksums when reading data from it.
- Task Resiliency
	- Worker Failure
		- The master node typically pings the worker node similar to a heartbeat mechanism, and if the worker fails, and the job is re-executed.
		- Completed Map tasks are re-executed on failure because of the reason that the results are stored on local disks, where as the Reduce tasks are not re-executed.
	- Master Failure
		- Since MapReduce works very similarly to the "Checkpointing" style, we can enable the Master to resume the work from the last checkpoint.
	- Stragglers
		- A machine that takes extra amount of time to process the task is called a straggler, where the slowness is calculated using "Per Task score"
		- For Map tasks, the score is calculated using "the amount of work done compared to how much work is soon a function of the input HDFS block read so far".
		- For Reduce tasks, it checks the progress of all the 3 phases i.e., shuffles (merges), sorts, and reduces and accounting for one-third of a task's progress score
		- When Master detects a slow task, it runs a corresponding backup (speculative) task simultaneously. Hadoop allows one speculative task per original slow task, and the two compete. Whichever version completes first is committed, and the other is killed.

