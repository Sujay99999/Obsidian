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

### Issues with MultiThreading
- Mullithreading can cause multiple issues such as Race conditions, Deadlocks, Congestion, etc and lot of concurrency related issues

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
	- 