# Thread Pools

Thread Pools are useful when you need to limit the number of threads running in your application at the same time. There is a performance overhead associated with starting a new thread, and each thread is also allocated some memory for its stack etc.

Instead of starting a new thread for every task to execute concurrently, the task can be passed to a thread pool. As soon as the pool has any idle threads the task is assigned to one of them and executed. Internally the tasks are inserted into a [Blocking Queue](http://tutorials.jenkov.com/java-concurrency/blocking-queues.html) which the threads in the pool are dequeuing from. When a new task is inserted into the queue one of the idle threads will dequeue it successfully and execute it. The rest of the idle threads in the pool will be blocked waiting to dequeue tasks.

```java
public class ThreadPool {

    private BlockingQueue taskQueue = null;
    private List<PoolThread> threads = new ArrayList<PoolThread>();
    private boolean isStopped = false;

    public ThreadPool(int noOfThreads, int maxNoOfTasks){
        taskQueue = new BlockingQueue(maxNoOfTasks);

        for(int i=0; i<noOfThreads; i++){
            threads.add(new PoolThread(taskQueue));
        }
        for(PoolThread thread : threads){
            thread.start();
        }
    }

    public synchronized void  execute(Runnable task) throws Exception{
        if(this.isStopped) throw
            new IllegalStateException("ThreadPool is stopped");

        this.taskQueue.enqueue(task);
    }

    public synchronized void stop(){
        this.isStopped = true;
        for(PoolThread thread : threads){
           thread.doStop();
        }
    }

}
```

```java
public class PoolThread extends Thread {

    private BlockingQueue taskQueue = null;
    private boolean       isStopped = false;

    public PoolThread(BlockingQueue queue){
        taskQueue = queue;
    }

    public void run(){
        while(!isStopped()){
            try{
                Runnable runnable = (Runnable) taskQueue.dequeue();
                runnable.run();
            } catch(Exception e){
                //log or otherwise report exception,
                //but keep pool thread alive.
            }
        }
    }

    public synchronized void doStop(){
        isStopped = true;
        this.interrupt(); //break pool thread out of dequeue() call.
    }

    public synchronized boolean isStopped(){
        return isStopped;
    }
}
```



# ExecutorService

## Task Delegation

![A thread delegating a task to an ExecutorService for asynchronous execution.](http://tutorials.jenkov.com/images/java-concurrency-utils/executor-service.png)

Once the thread has delegated the task to the `ExecutorService`, the thread continues its own execution independent of the execution of that task.

## ExecutorService Usage

There are a few different ways to delegate tasks for execution to an `ExecutorService`:

- execute(Runnable)
- submit(Runnable)
- submit(Callable)
- invokeAny(...)
- invokeAll(...)

### execute(Runnable)

The `execute(Runnable)` method takes a `java.lang.Runnable` object, and executes it asynchronously. Here is an example of executing a `Runnable` with an `ExecutorService`:

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();

executorService.execute(new Runnable() {
    public void run() {
        System.out.println("Asynchronous task");
    }
});

executorService.shutdown();
```

There is no way of obtaining the result of the executed `Runnable`, if necessary. You will have to use a `Callable` for that.



### submit(Runnable)

The `submit(Runnable)` method also takes a `Runnable` implementation, but returns a `Future` object. This `Future` object can be used to check if the `Runnable` as finished executing.

```java
Future future = executorService.submit(new Runnable() {
    public void run() {
        System.out.println("Asynchronous task");
    }
});

future.get();  //returns null if the task has finished correctly.
```



### submit(Callable)

The `submit(Callable)` method is similar to the `submit(Runnable)` method except for the type of parameter it takes. The `Callable` instance is very similar to a `Runnable`    except that its `call()` method can return a result. The `Runnable.run()` method cannot return a result.

The `Callable`'s result can be obtained via the `Future` object returned by the  `submit(Callable)` method. 

```java
Future future = executorService.submit(new Callable(){
    public Object call() throws Exception {
        System.out.println("Asynchronous Callable");
        return "Callable Result";
    }
});

System.out.println("future.get() = " + future.get());
```

The above code example will output this:

```
Asynchronous Callable
future.get() = Callable Result
```



### invokeAny()

The `invokeAny()` method takes a collection of `Callable` objects, or subinterfaces    of `Callable`. Invoking this method does not return a `Future`, but returns the    result of one of the `Callable` objects. You have no guarantee about which of the `Callable`'s    results you get. Just one of the ones that finish.

If one of the tasks complete (or throws an exception), the rest of the `Callable`'s are cancelled.

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();

Set<Callable<String>> callables = new HashSet<Callable<String>>();

callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 1";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 2";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 3";
    }
});

String result = executorService.invokeAny(callables);

System.out.println("result = " + result);

executorService.shutdown();
```

This code example will print out the object returned by one of the `Callable`'s in the  given collection. I have tried running it a few times, and the result changes. Sometimes it is "Task 1", sometimes "Task 2" etc.



### invokeAll()

The `invokeAll()` method invokes all of the `Callable` objects you pass to    it in the collection passed as parameter. The `invokeAll()` returns a list of `Future`    objects via which you can obtain the results of the executions of each `Callable`.

Keep in mind that a task might finish due to an exception, so it may not have "succeeded". There is no way on a `Future` to tell the difference.



```java
ExecutorService executorService = Executors.newSingleThreadExecutor();

Set<Callable<String>> callables = new HashSet<Callable<String>>();

callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 1";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 2";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 3";
    }
});

List<Future<String>> futures = executorService.invokeAll(callables);

for(Future<String> future : futures){
    System.out.println("future.get = " + future.get());
}

executorService.shutdown();
```



## ExecutorService Shutdown

When you are done using the `ExecutorService` you should shut it down, so the threads do not keep running.

For instance, if your application is started via a `main()` method and your main thread exits your application, the application will keep running if you have an active `ExexutorService` in your application. The active threads inside this `ExecutorService`  prevents the JVM from shutting down.

To terminate the threads inside the `ExecutorService` you call its `shutdown()` method.  The `ExecutorService` will not shut down immediately, but it will no longer accept new tasks, and once all threads have finished current tasks, the `ExecutorService` shuts down. All tasks submitted    to the `ExecutorService` before `shutdown()` is called, are executed.

If you want to shut down the `ExecutorService` immediately, you can call the `shutdownNow()`  method. This will attempt to stop all executing tasks right away, and skips all submitted but non-processed tasks.There are no guarantees given about the executing tasks. Perhaps they stop, perhaps the execute until the end. It is a best effort attempt.



# References

[Thread Pools](http://tutorials.jenkov.com/java-concurrency/thread-pools.html)

[ExecutorService](http://tutorials.jenkov.com/java-util-concurrent/executorservice.html)

---

created at 2017-06-02 20:34