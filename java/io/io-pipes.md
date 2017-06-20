# Java IO: Pipes

Pipes in Java IO provides the ability for two threads running in the same JVM to communicate. Therefore pipes can also be sources or destinations of data.



## Creating Pipes via Java IO

Creating a pipe using Java IO is done via the [`PipedOutputStream`](http://tutorials.jenkov.com/java-io/pipedoutputstream.html) and [`PipedInputStream`](http://tutorials.jenkov.com/java-io/pipedinputstream.html) classes. A `PipedInputStream` should be connected to a `PipedOutputStream`. The data written to the `PipedOutputStream` by one thread can be read from the connected `PipedInputStream` by another thread.



## Java IO Pipe Example

Here is a simple example of how to connect a `PipedInputStream` to a `PipedOutputStream`:

```java
import java.io.IOException;
import java.io.PipedInputStream;
import java.io.PipedOutputStream;

public class PipeExample {

    public static void main(String[] args) throws IOException {

        final PipedOutputStream output = new PipedOutputStream();
        final PipedInputStream  input  = new PipedInputStream(output);


        Thread thread1 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    output.write("Hello world, pipe!".getBytes());
                } catch (IOException e) {
                }
            }
        });


        Thread thread2 = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    int data = input.read();
                    while(data != -1){
                        System.out.print((char) data);
                        data = input.read();
                    }
                } catch (IOException e) {
                }
            }
        });

        thread1.start();
        thread2.start();

    }
}
```

You can also connect the two pipe streams using their `connect()` methods. Both    `PipedInputStream` and `PipedOutputStream` has a `connect()`    method that can connect one to the other.



## Pipes and Threads

Remember, when using the two connected pipe streams, pass one stream to one thread, and the other stream to another thread. The `read()` and `write()` calls on    the streams are blocking, meaning if you try to use the same thread to both read and write,    this may result in the thread deadlocking itself.

## Pipe Alternatives

There are many other ways than pipes that threads can communicate within the same JVM. In fact, threads more    often exchange complete objects rather than raw byte data. But - if you need to exchange raw byte data between    threads, Java IO's pipes are a possibility.



# Practice

```java
/**
 * Created by WS on 2017/6/20.
 */
public class PipeTest {

    @Test
    public void test() throws IOException, InterruptedException {
        PipedOutputStream outputStream = new PipedOutputStream();
        PipedInputStream inputStream = new PipedInputStream(outputStream);

        Thread output = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    outputStream.write("Hello world,Pipe!".getBytes());
                    outputStream.close();//must
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });

        Thread input = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    int data = inputStream.read();
                    while (data != -1){
                        System.out.print((char) data);
                        data = inputStream.read();
                    }
                    inputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });

        output.start();
        input.start();

        output.join();
        input.join();
    }
}
```

# References

[Java IO: Pipes](http://tutorials.jenkov.com/java-io/pipes.html)

[Write end dead exception using PipedInputStream java](https://stackoverflow.com/questions/5453525/write-end-dead-exception-using-pipedinputstream-java)

---

created at 2017-06-20 18:44