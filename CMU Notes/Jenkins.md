- Jenkins Architecture
	- Uses Distributed architecture to support entire CI/CD process
		- Has a Master Node (Jenkins server), which takes care of everything
	- For advanced deployments, it uses a combination of Master node and other worker nodes (Jenkins slaves), which is used for parallel processing
	- Each node has multiple executors which performs the jobs.
	- Each of the nodes has a agent which simply manages the executors and the node, and they are the ones which talk to the master node
- Installation and other misc
	- Firstly locate the JENKINS_HOME as it contains the entire fs for all scripts



- Project
	- How do you enable the permissions requrired for jenkins (Check how to do it with docker as well)
	- Setting path for jenkins
	- How does the jenkins handle the workspace directory