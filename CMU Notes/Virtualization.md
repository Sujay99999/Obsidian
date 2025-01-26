Virtualization refers to the abstraction of some physical component into a logical object
It created the abaility to condense multiple physical servers into fewer servers that would run multiple virtual machines. The measure of consolidation is defined by Consolidation ratio i.e., 8:1 means 8 VMs are running on 1 physical server.
Virtual machines can be moved from one physical host to another without disruption -- check
Converged infrastructure means the way hardware vendors started to create servers that are optimal for hypervisors to work on, enabling them to be preconfigured

- Hypervisors
	- Hypervisor is the software that resides between hardware and the virtual machines.
	- These VMs are also called as guests
	- There are 2 types of Hypervisors
		- Type 1 hypervisor
			- Type 1 hypervisor is the one which runs directly on the server hardware without a operating system beneath it
			- Since, there is no intervening layer between the hypervisor and the hardwre, this implmentation of software is also known as "Bare Metal implementation"
			- Type 1 is considered much more efficient than the Type 2, as it can directly communicate with the hardware resources below it.
			- Type 1 is considered to be more secure than the Type 2
			- Examples are VMware ESX, Microsoft Hyper-V and Xen variants
		- Type 2 hypervisor
			- Its an application itself, which runs on the traditional operating system
			- Easy to deploy and manager than compared to type 1
			- For every request from the guest OS, it requires additional overhead, as it passes onto the main OS
			- Less reliable than compared to the type 1, as it has multiple points of failure
			- Examples are VMare fusion, parallels
	- Roles of a Hypervisor
		- Provide an environment similar to the physical system for the VM
		- Retain complete access to the system resources
		- Provide an environment with minimal performance cost
- Virtual Machines
	- The main files that are important to the VM are
		- Configuration file - Describes the resources required for the VM
		- Virtual Disk files
			