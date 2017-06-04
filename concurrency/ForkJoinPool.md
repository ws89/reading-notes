# Fork and Join Explained

The `ForkJoinPool` was added to Java in Java 7. The `ForkJoinPool` is similar to the [Java ExecutorService](http://tutorials.jenkov.com/java-util-concurrent/executorservice.html) but with one difference. The `ForkJoinPool`
makes it easy for tasks to split their work up into smaller tasks which are then submitted to the `ForkJoinPool` too. Tasks can keep splitting their work into smaller subtasks for as long as it makes to split up the task.

**The fork and join principle consists of two steps which are performed recursively. These two steps are the fork step and the join step.**



## Fork

A task that uses the fork and join principle can *fork* (split) itself into smaller subtasks which can be executed concurrently. This is illustrated in the diagram below:

![Splitting tasks into smaller tasks is referred to as forking tasks.](http://tutorials.jenkov.com/images/java-concurrency-utils/java-fork-and-join-1.png)

By splitting itself up into subtasks, each subtask can be executed in parallel by different CPUs, or different threads on the same CPU.

A task only splits itself up into subtasks if the work the task was given is large enough for this to make sense.There is an overhead to splitting up a task into subtasks, so for small amounts of work this overhead may be greater    than the speedup achieved by executing subtasks concurrently.

The limit for when it makes sense to fork a task into subtasks is also called a threshold. It is up to each task to decide on a sensible threshold. It depends very much on the kind of work being done.



## Join

When a task has split itself up into subtasks, the task waits until the subtasks have finished executing.

Once the subtasks have finished executing, the task may *join* (merge) all the results into one result.    This is illustrated in the diagram below:

![Merging the results of subtasks is referred to as joining results.](http://tutorials.jenkov.com/images/java-concurrency-utils/java-fork-and-join-2.png)

Of course, not all types of tasks may return a result. If the tasks do not return a result then a task just waits    for its subtasks to complete. No result merging takes place then.



# The ForkJoinPool

The `ForkJoinPool` is a special thread pool which is designed to work well with fork-and-join task splitting. The `ForkJoinPool` located in the `java.util.concurrent` package, so the full class name is `java.util.concurrent.ForkJoinPool`.



## Creating a ForkJoinPool

You create a `ForkJoinPool` using its constructor. As a parameter to the `ForkJoinPool`    constructor you pass the indicated level of parallelism you desire. The parallelism level indicates how many    threads or CPUs you want to work concurrently on on tasks passed to the `ForkJoinPool`.    Here is a `ForkJoinPool` creation example:

```java
ForkJoinPool forkJoinPool = new ForkJoinPool(4);
```

This example creates a `ForkJoinPool` with a parallelism level of 4.



## Submitting Tasks to the ForkJoinPool

You submit tasks to a `ForkJoinPool` similarly to how you submit tasks to an `ExecutorService`.  You can submit two types of tasks. A task that does not return any result (an "action"), and a task which does  return a result (a "task"). These two types of tasks are represented by the `RecursiveAction` and `RecursiveTask` classes.



## RecursiveAction

A `RecursiveAction` is a task which does not return any value. It just does some work, e.g. writing data to disk, and then exits.

A `RecursiveAction` may still need to break up its work into smaller chunks which can be executed by  independent threads or CPUs.

​    You implement a `RecursiveAction` by subclassing it. Here is a `RecursiveAction` example:

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.RecursiveAction;

public class MyRecursiveAction extends RecursiveAction {

    private long workLoad = 0;

    public MyRecursiveAction(long workLoad) {
        this.workLoad = workLoad;
    }

    @Override
    protected void compute() {

        //if work is above threshold, break tasks up into smaller tasks
        if(this.workLoad > 16) {
            System.out.println("Splitting workLoad : " + this.workLoad);

            List<MyRecursiveAction> subtasks =
                new ArrayList<MyRecursiveAction>();

            subtasks.addAll(createSubtasks());

            for(RecursiveAction subtask : subtasks){
                subtask.fork();
            }

        } else {
            System.out.println("Doing workLoad myself: " + this.workLoad);
        }
    }

    private List<MyRecursiveAction> createSubtasks() {
        List<MyRecursiveAction> subtasks =
            new ArrayList<MyRecursiveAction>();

        MyRecursiveAction subtask1 = new MyRecursiveAction(this.workLoad / 2);
        MyRecursiveAction subtask2 = new MyRecursiveAction(this.workLoad / 2);

        subtasks.add(subtask1);
        subtasks.add(subtask2);

        return subtasks;
    }

}
```

This example is very simplified. The `MyRecursiveAction` simply takes a fictive `workLoad`    as parameter to its constructor. If the `workLoad` is above a certain threshold, the work is split    into subtasks which are also scheduled for execution (via the `.fork()` method of the subtasks.    If the `workLoad` is below a certain threshold then    the work is carried out by the `MyRecursiveAction` itself.

You can schedule a `MyRecursiveAction` for execution like this:

```java
MyRecursiveAction myRecursiveAction = new MyRecursiveAction(24);

forkJoinPool.invoke(myRecursiveAction);
```



## RecursiveTask

A `RecursiveTask` is a task that returns a result. It may split its work up into smaller tasks, and merge the result of these smaller tasks into a collective result. The splitting and merging may take place on several levels. Here is a `RecursiveTask` example:

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.RecursiveTask;
    
    
public class MyRecursiveTask extends RecursiveTask<Long> {

    private long workLoad = 0;

    public MyRecursiveTask(long workLoad) {
        this.workLoad = workLoad;
    }

    protected Long compute() {

        //if work is above threshold, break tasks up into smaller tasks
        if(this.workLoad > 16) {
            System.out.println("Splitting workLoad : " + this.workLoad);

            List<MyRecursiveTask> subtasks =
                new ArrayList<MyRecursiveTask>();
            subtasks.addAll(createSubtasks());

            for(MyRecursiveTask subtask : subtasks){
                subtask.fork();
            }

            long result = 0;
            for(MyRecursiveTask subtask : subtasks) {
                result += subtask.join();
            }
            return result;

        } else {
            System.out.println("Doing workLoad myself: " + this.workLoad);
            return workLoad * 3;
        }
    }

    private List<MyRecursiveTask> createSubtasks() {
        List<MyRecursiveTask> subtasks =
        new ArrayList<MyRecursiveTask>();

        MyRecursiveTask subtask1 = new MyRecursiveTask(this.workLoad / 2);
        MyRecursiveTask subtask2 = new MyRecursiveTask(this.workLoad / 2);

        subtasks.add(subtask1);
        subtasks.add(subtask2);

        return subtasks;
    }
}
```

This example is similar to the `RecursiveAction` example except it returns a result. The class `MyRecursiveTask` extends `RecursiveTask<Long>` which means that the result returned from the task is a `Long` .

The `MyRecursiveTask` example also breaks the work down into subtasks, and schedules these subtasks    for execution using their `fork()` method.

Additionally, this example then receives the result returned by each subtask by calling the `join()`    method of each subtask. The subtask results are merged into a bigger result which is then returned. This kind    of joining / mergining of subtask results may occur recursively for several levels of recursion.

You can schedule a `RecursiveTask` like this:

```java
MyRecursiveTask myRecursiveTask = new MyRecursiveTask(128);

long mergedResult = forkJoinPool.invoke(myRecursiveTask);

System.out.println("mergedResult = " + mergedResult);    
```

Notice how you get the final result out from the `ForkJoinPool.invoke()` method call.



# References

[Java Fork and Join using ForkJoinPool](http://tutorials.jenkov.com/java-util-concurrent/java-fork-and-join-forkjoinpool.html)

---

created at 2017-06-04 17:37