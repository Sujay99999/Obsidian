- How do we actually send/ forward the packets from the source to destination
	- How do we identify nodes? (Addressing Problem)
	- How do the messages get routed from one node to another (Routing Problem)
- Addressing
	- There are two major types of addressing
		- Flat Addressing
			- Unique identifier
			- The way the identifier is designed may not be used for routing
			- Used in MAC addresses within the LAN
			- > 281 trillion unique addresses, where this 48bit address is boiled into the equipment
		- Hierarchical Addressing
			- Has a structure such a way it can be used for routing, like phone numbers
			- IP addresses which are used in WAN (32 bit identifier)
- Single Line Ethernet vs Switched Ethernet
	- Single line is the old fashioned one where all the devices are connected across, which is not scalable
	- Switched Ethernet uses Switches as a major component which has a lot of ports, where the data comes at a particular port, and redirected into some port
		- The control processor within the switch basically routes the data based on the "Forwarding Table", which is a mapping of "MAC addresses to Port numbers"
		- And again this faces the problem of Flat addressing, which makes it not scalable
- IP addresses
	- Its a 32 bit address
	- It has 2 components, where first 23 bits are (Network component), and the 9 bits are (Host Component)
	- The IP addresses are assigned using a protocol called DHCP
	- IP Address design
		- Iteration 1: 8 bits for Network and 24 bits for hosts, which doesnt make sense
		- Iteration 2: Class Based assignments
			- First Bits help to identify the Class
			- Lot of Underutilization of address blocks
		- Iteration 3:
			- Usage of CIDR
				- There is a flexible division between the network and host addresses
				- Network prefix can be any size
				- So, when we are having flexible address spaces, we should explicitly put the network size i.e., `/<number of network bits>`
				- The Network prefix can also be represented as 32 bit mask, where all the network bits are 1, and host bits are 0
- Routing
	- So, the next problem is to understand how the packets are routed, where we use the routers, which forwards the packets based on the network prefix it holds to the next hop which is another router
	- So, the forwarding node reduces the forwarding table size by aggregating the table entries it has by using the "Common prefixes" picked up from the destination address
	- The IP address allocation is also done in a hierarchical order where ICANN > ARIN > ISPs > small institutions
	- Routers basically use "Longest Matching Prefix" algorithm, as the longer the match, the closer it is to the host
	- The forwarding tables are populated based on different metrics
		- LANs generally use Broadcasting, learning
		- WANs uses BGP (Border Gateway Protocol)

- Transport Layer helps us to support multiple applications using "Process Multiplexing" using ports
	- TCP
		- It is a protocol which is reliable, In-order and two-way byte stream service
		- It has Flow control, Error control, Congestion Control
		- In Order delivery
			- Uses the concept of "Stop and Wait" where it sequences the packets and expects the receiver to send out the ACKs.
			- It also uses the idea of "Retransmission after timeout" to recover from Packet error, and the timer has to be more conservative as possible
				- Estimated based on the RTT (Round Trip Time)
			- We can improve this throughput with "Sliding Window", where the sliding window size is determined based on the buffer size
			- Packet drops are tricky as the protocol creates space for dropped packets to be added in the next batch transmission
		- Congestion Control
			- We use a congestion control algorithm called CCA, which is a conservative way of maintaining the size




- Go Language
	- Generally, we expect the code to be linearly run, where if a piece of code is blocking the execution, the compiler simply waits for it to complete the execution. Goroutines solve this problem by making them run parallely
	- GoRoutines
		- We can fork a piece of code to run using the keyword "go" infront of the function call, which allows the original code execution to continue
		- Since if we spawn off a function asynchronously using goroutines, then we might have a case where the main function completes execution before completing the async functions which can terminate the program
		- Go language follows a concurrency model called "Fork and Join model", where when the main program spwans off a new child process, it expects us to also make sure to join it back to the main process when the execution is completed.
		- The main function is also a "Goroutine"
	- Channels
		- Channels help us to implement this "join operation", where the child processes can read the data which is shared across the goroutines.
		- In this example,
		  ```
		  func main(){
			  myChannel := make(chan string)
			  
			  go func(){
				  myChannel <- "data"
			  }()
			  msg := <- myChannel
			  fmt.Println(msg)
		  }
		  ```
		  - As we can see the `msg := <- myChannel` line acts as the code that is blocking the main goroutine, as it waits the channel to either have some data in it or get closed, which only happens, when the `func` anonymous goroutine gets completed


- Exam NOtes
	- mut ex vs others - know stuff
	- concurrency