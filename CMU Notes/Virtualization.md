Virtualization refers to the abstraction of some physical component into a logical object
It created the abaility to condense multiple physical servers into fewer servers that would run multiple virtual machines. The measure of consolidation is defined by Consolidation ratio i.e., 8:1 means 8 VMs are running on 1 physical server.
Virtual machines can be moved from one physical host to another without disruption -- check
Converged infrastructure means the way hardware vendors started to create servers that are optimal for hypervisors to work on, enabling them to be preconfigured

hypervisors
	Hypervisor is the software that resides between hardware and the virtual machines.
	These VMs are also called as guests
	There are 2 types of Hypervisors
		Type 1 hypervisor
			- Type 1 hypervisor is the one which runs directly on the server hardware without a operating system beneath it
			- Since, there is no intervening layer between the hypervisor and the hardwre, this implmentation of software is also known as "Bare Metal implementation"
			- Type 1 is considered much more efficient than the Type 2, as it can directly communicate with the hardware resources below it.
			- 
			