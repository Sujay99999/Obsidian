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
	- Resources for VM
		- CPU -  Most of the times, the VM will be dedicated to an single CPU, but instead the VMs are only able to schedule CPU cycles of the host using the hypervisor
		- RAM - Similar concept as CPU, but the hypervisor can add additional RAM if required without reboot
		- NIC - Hypervisors provide virtual network for VM, by having individual virtual NIC for the VMs, and these are connected by the Virtual switches in the hypervisor
	- In x8086 architectures, the protection and security is provided in 4 different levels, on which the processor commands are run, known as rings
	- Ring 0 is the most privilged one, where the OS kernel works, Ring 1 and 2 are where the device drivers commands are executed, and Ring 3 is for the applications.
	- **Hypervisor runs on Ring 0**
	- Packaging and distributing VMs
		- VMs are generally provisioned using the Clone files i.e., we can easily clone an entire VM into a bunch of files
		- We can also build templates for VMs
		- We can also use OVF - Open Virtualization format which helps to bundle the VMs into a set of files which then can be converted into vendor-neutral format i.e., Xen based VMs once created can be exported into a OVF files, which then can be run on Hyper-V environment
- VM vs Containers