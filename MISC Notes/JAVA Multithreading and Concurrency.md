### What is MultiTasking
- Its the ability of the CPU and OS together to switch the execution i.e., CPU cycles from One application to another using the concept of Context Switching, which gives the illusion that the CPU has been handling multiple applications at the same time.
- Within multitasking, we are explicitly not talking about threads or cores of the CPU, its the ability to move from one application to another.

### What is MultiThreading (With Single vs Multi Core CPUs)
- Basically, thread is considered as the smallest unit for process of execution, which essentially means that one CPU contains multiple threads and the CPU basically context switches between the threads to complete the workload
- Each CPU has fixed number of threads based on the capability
- There can be multiple threads assigned to one application, and it is not necessary that an application is assigned threads from single CPU, it can be from multiple CPUs
- This concept of breaking down the workload of multiple applications across the threads, which are then executed against multiple CPUs is called MultiThreading

### Why MultiThreading
- Single big reason to introduce MultiTasking and MultiThreading in general is to **Reduce the CPU cycle wastage**. In most of the cases, when CPU executes the task, it often reads data from disk or Sends some network request, which can be time consuming, and if we expect the CPU is wait idle during the time, then we end up wasting the CPU cycles.
- Instead, using Multithreading, we can breaakdown the tasks of the multiple applications across multiple threads and these CPUs can be allocated the tasks in such as way that they dont sit idle. This helps is improving the efficiency of CPU as well as the IO capabilities
- - So, in overall Multithreading allows to have higher throuput, the reason being that creating thread is a lightweight task than compared to creating another process

### Issues with MultiThreading
- Mullithreading can cause multiple issues such as Race conditions, Deadlocks, Congestion, etc and lot of concurrency related issues

#### Definitions
- Program
	- A program is basically some set of instructions along with some data (may be on disk or cloud), which is executed by the OS
	- Simple example is "Executing a python script"
	- When we run a program, it essentially means that we are asking the OS's kernel to basically a process (which is an environment), in which this program runs
- Process
	- A process is "Program in execution"
	- A process basically is a execution environment which contains all the data that is required to execute the set of instructions such as System data, user data, CPU based data, etc
	- A program can contain multiple processes, but each process is mapped to one program
	- Any 2 processes dont share any resources at all
- Threads
	- It is smallest unit of execution
	- It is responsible for the executing the instructions serially
	- We can have multiple threads sharing the same resources or owning a global shared data, and this is where we have to be careful

--- 
### Java Threads
- Java threads can be assumed as mini CPUs, and they are maintained by JVM + OS together
- When we write a Java application, it is actually executed by a main thread which is created for this sole purpose by JVM. Within the java application, we can create multiple threads, which has the capability of executing the code as well
- Java threads are basically java objects, they are instances of `java.lang.Thread` or its subclasess

#### Creating Java Threads
-  We can create java threads using 2 ways i.e., either using the Runnable interface or Thread class
- Creating Java thread using Thread class
	-  ```
	   Thread thread = new Thread();
	   thread.start();
		```
	- In this example, the Main thread executing the java applcaition, baically executes the application, and finds this piece of code and creates a thread. But this new thread created and started, has nothing to do, hence it exits immediately, along side with the Main thread as well.
	- ```
	  public class MyThread extends Thread {

    public void run(){
       System.out.println("MyThread running");
    }
  }
  
    MyThread myThread = new MyThread();
  myTread.start();
	  ```
	- In this example, we explicitly provide some task to the new thread created using the `run()` method. 
	- The `start()` call will return as soon as the thread is started. It will not wait until the `run()` method is done. The `run()` method will execute as if executed by a different CPU. When the `run()` method executes it will print out the text "MyThread running". {{Check this}}
	- We can also create anonymous class instead of creating new class
- Creating Java thread using Runnable interface
	- Another way to specifiy to the created thread on what to execute is using the Java runnable interface. 
	- Runnable interface has only one method called `run()`, which has to be overridden the the class implemeting it
	- We can use this Runnable interface in different ways i.e, 
		- Creating a new class implmenting the runnable interface
		- Creating an anonymous class that implements the runnable interface
		- Creating a Lmabda expression that implements the runnable interface
	- Creating a new class implmenting the runnable interface
	-  ```
	public class MyRunnable implements Runnable {

    public void run(){
       System.out.println("MyRunnable running");
	    }
	  }
	```
	- Creating an anonymous class that implements the runnable interface
	- ```
	  Runnable myRunnable =
    new Runnable(){
        public void run(){
            System.out.println("Runnable running");
        }
    }
	  ```
	- Creating a Lmabda expression that implements the runnable interface
	- ```
	  Runnable runnable =
        () -> { System.out.println("Lambda Runnable running"); };
	  ```
	- Starting a Thread using this Runnable interface
	- ```
	  Runnable runnable = new MyRunnable(); // or an anonymous class, or lambda...

Thread thread = new Thread(runnable);
thread.start();
	  ```
- Using Thread class directly vs Runnable interface
	- Its preferred to use the `Runnable` interface than compared to the Thread class, as its more compatible with the Thread pool queueing process.
- Pausing a thread
	- We can pause a thread using the static method of thread class called `Thread.sleep()`, which takes milliseconds as parameter.
- Stopping a thread
	- Java threads and the Runnable interface dont explicitly provide the `stop()` methods, which can be used without any issues. Thread class does provide `stop()` method, but its deprecated and when used, it can cause issues such as unpredictability, especially when the current thread and other threads are performing some operations on data, and the current thread callss`stop()`, leaving them in a unkwnown state.
	- So, it is the responsibility of the user to create the logic of stopping of threads if he needs it explicitly i.e., A thread always compeltes the execution when the `run()` method exits from completing the execution.
	- ```
	  public class MyRunnable implements Runnable {

    private boolean doStop = false;

    public synchronized void doStop() {
        this.doStop = true;
    }

    private synchronized boolean keepRunning() {
        return this.doStop == false;
    }

    @Override
    public void run() {
        while(keepRunning()) {
            // keep doing what this thread should do.
            System.out.println("Running");

            try {
                Thread.sleep(3L * 1000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
    }
}

	  ```
  - ```
    public class MyRunnableMain {

    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();

        Thread thread1 = new Thread(myRunnable);

        thread1.start();

        try {
            Thread.sleep(10L * 1000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        myRunnable.doStop();
    }
}
	  ```
	  - In this example, we can see that the Main thread starts the execution, then creates a new thread called `thread1` using the custom class implementing the Runnable interface.
	  - At this moment, the `run()` starts executing using the `thread1`, where it keeps printing at some intervals.
	  - But in parallel, the Main thread continues the execution and finds the peice of code to sleep for 10 sec using static method `Thread.sleep()`.
	  - Once the timer is completed, the Main thread then explictly toggles the boolean which is shared constant in this program, and this acts as a signal. This works because the methods within the class use `synchrnoized` keyword, which explicitly allows only one thread to access the state of the variable.
	  - So, in `thread1` pov, the moment it checks for the boolean and finds it as false, then it returns, completing the execution
	  - But the important pointer is that the Main thread is completing the execution before the `thread1` exit, but the entire program is completed only when the `thread1` is completing the execution {{Doubt: why is the behavior like this, we are specifying the thread1 as a daemon thread, right?; Oh okay got it, the JVM runs until all threads complete execution}}
	  - This is because he JVM runs until all threads complete execution.
  - Daemon threads
	  - When you create a thread and mark it as `thread.setDaemon(true)`, then you are explicitly telling the JVM that the program can be completed once the Main thread + Other Non-Daemon threads compltetes their execution.
	  - But this also implies that the daemon threads are also stopped which can lead to unexpected behavior
  - `join()` method
	  - We can use this method to explicitly ensure that one thread waits for the execution of another thread using this method
	  - So if a thread calls explicitly `thread.join()`, then the Main thread waits until this thread completes the execution and then only exits the program.

#### Concurrency vs Parallelism
- 


### Single Threading
- Single Threading concurrency model is a concept where "A single thread is created and **is responsible for executing more than one task**", rather than spinning up multiple threads for different tasks.
- Simply put, this is a trade off model than compared to Multi threading concurrency model, where it is expected to have shared state or consistency issues like Deadlocks, starvations, etc.
- But in Single threaded model, one single thread executes different tasks by switching between them.
- NodeJS uses Single threaded Server design, which resonates with Event driven architecture as well, where it basically has "Event loop", which is like a main single thread, looking for events or tasks to execute
- Advantages of Single Threading architecture
	- Provides **Full thread visibility** allowing the developer to provide scope to design task switching between tasks
	- There is no concept of "Shared state", hence removing issues such as Deadlock, Race conditions etc.
- Disadvantages
	- A single thread can utilize only one CPU for executing the instructions, hence not fully utilizing the multi core/ CPU architecture
	- Any kind of blocking operations have to be handled by background threads. NodeJS provides inbuilt support for this.
	- If we wanted to scale Single threaded architecture, we need to use "Same threaded architecture"

#### Same threaded Architecture
- Same threaded architecture is basically an upgrade of "Single threaded concurrency model", utilizing the fact that machine has multiple cores instead of one.
- But this is different from "Multithreaded Architecture", because the threads here dont share any data unlike the multithreading.
- So, it can thought of "One machine handling multiple single threaded processes".
- But, in the case where these process have to communicate, they have to implement some kind of messaging model using either sockets, etc
- This is a very good model for implementing microservice architecture within single machine, as each of the process is separate from each other, but needs to communicate
#### 