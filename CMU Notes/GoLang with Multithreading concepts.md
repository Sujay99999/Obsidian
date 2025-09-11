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
		  - Buffered and Unbuffered Channels
			  - Unbuffered Channels
				  - These channels when created, we either provide a capacity of 0 or omit the capacity altogether 
				  - This enables a synchronous communication between the sending and the receiving go-routines i.e., when a sender sends a data onto the channel, the sender goroutine gets blocked until a receiver uses the data from the channel, which unblocks the sender goroutine
			  - Buffered Channels
				  - These channels are created a initial capacity, and performs like a FIFO queue
				  - This enables a asynchronous communication between the sender and the receiver goroutines i.e., the sender simply adds the data onto the channel and is not blocked as expected only until the channel is full
	  - Select 
		  - Select is a simple switch case statement, but when combined with channels, it plays an interesting role, where it initially blocks the current goroutine and it checks for the case which has the channel populated the first, and performs that particular code
		    ```
		    func main(){
				  myChannel := make(chan string)
				  anotherChannel := make(chan string)
				  
				  go func(){
					  myChannel <- "data"
				  }()
				  go func(){
					  anotherChannel <- "data2"
				  }()
				  select{
					case msg1 := myChannel:
						fmt.Println(msg1)
					case msg2 := anotherChannel:
						fmt.Println(msg2)
				  }
			  }
		    ```
		- In this case, the select waits for either of the channels to get populated, and the one that is available first, gets its code executed
	- Concurrency Patterns
		- For Select loop