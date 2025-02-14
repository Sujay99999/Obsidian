AWS access key
sujay_root  --> AKIAZ7SALAXCFR5UQ2OU - 4BTPO01LE/BX/twx3imPddaUT7osNpY77ifTK+ZT

AWS tags
project - getting-started-with-cloud-computing

Burstable res
SAN-style storage systems - EBS
	attach ebs to insatnce - make sure to format the ebs instance and mount a file system around it
Sysbench for Disk IO operations
why to create seperate tfvars instead of the variables.tf file only
Why terraform must store the state
Horizontal scalin can be performed by partioning the problem domain into smaller shards and making individual resource responsible for a single partition(shard)


Questions for reddy
	can we have 4 ppl in a team
	can we mock the submitters that we have in the major project
	Shall i team up with the 2mscs folks
	

AWS
	EC2
		Instance types
		Pricing 
			On-Demand
			Reserved
			Spot pricing - Bid on the unused res

Misc questions to checkout
  
Is the following statement true or false?

"In the case of AWS burstable read/write bandwidth for EBS volume, NO GP2 volume can accumulate more than 5.4 million I/O credits."



Peak load calc = 4200/500 = 8.4, taking an upper round off as 9: 9x5x4 =180
Greedy Load calc = Servers required at each 15 mins are = [2,2,3,4,7,7,3,2,4,2,2,2,9,8,9,8]. Hence the billing is = (74x5)/4 = 92.5. dividing by 4 because of 15 mins
Lowest Cost, remains same as Greedy cost as its calculated when the graph is known




Project 2
	- docker credhelper and using it against gcp cli ==> gcpcloud auth configure-docker
	-  what is num-nodes in cluster create vs replicaset nodes

Project 2 learnings
	- docker cmds override the application.prop i.e., SERVER_PORT in dockerfile has overridden the application.properties of the sprin project which is server.port
	- Dockerfile must be built from the right folder
	- Wh
