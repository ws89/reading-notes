# Java IO Overview

## Input and Output - Source and Destination

Java's IO package mostly concerns itself with the reading of raw data from a source and writing    of raw data to a destination. The most typical sources and destinations of data are these:

- Files
- Pipes
- Network Connections
- In-memory Buffers (e.g. arrays)
- System.in, System.out, System.error

​    

### Streams

[IO Streams](http://tutorials.jenkov.com/java-io/streams.html) are a core concept in Java IO. A stream is a conceptually endless flow of data. You can either read from a stream or write to a stream. A stream is connected to a data source or a data destination. Streams in Java IO can be either **byte based** (reading and writing bytes) or **character based** (reading and writing characters).



### The InputStream, OutputStream, Reader and Writer

A program that needs to read data from some source needs an [`InputStream`](http://tutorials.jenkov.com/java-io/inputstream.html) or a [Reader](http://tutorials.jenkov.com/java-io/reader.html). A program that needs to write data to some destination needs an    [`OutputStream`](http://tutorials.jenkov.com/java-io/outputstream.html) or a [`Writer`](http://tutorials.jenkov.com/java-io/writer.html).    



## Java IO Purposes and Features

Java IO contains many subclasses of the `InputStream`, `OutputStream`, `Reader`    and `Writer` classes. The reason is, that all of these subclasses are addressing various different purposes. That is why there are so many different classes. The purposes addressed are summarized below:

- File Access
- Network Access
- Internal Memory Buffer Access
- Inter-Thread Communication (Pipes)
- Buffering
- Filtering
- Parsing
- Reading and Writing Text (Readers / Writers) 
- Reading and Writing Primitive Data (long, int etc.)
- Reading and Writing Objects



## Java IO Class Overview Table

|                  | Byte Based                            |                                     | Character Based                   |                             |
| ---------------- | ------------------------------------- | ----------------------------------- | --------------------------------- | --------------------------- |
|                  | Input                                 | Output                              | Input                             | Output                      |
| Basic            | InputStream                           | OutputStream                        | Reader / InputStreamReader        | Writer / OutputStreamWriter |
| Arrays           | ByteArrayInputStream                  | ByteArrayOutputStream               | CharArrayReader                   | CharArrayWriter             |
| Files            | FileInputStream  / RandomAccessFile   | FileOutputStream / RandomAccessFile | FileReader                        | FileWriter                  |
| Pipes            | PipedInputStream                      | PipedOutputStream                   | PipedReader                       | PipedWriter                 |
| Buffering        | BufferedInputStream                   | BufferedOutputStream                | BufferedReader                    | BufferedWriter              |
| Filtering        | FilterInputStream                     | FilterOutputStream                  | FilterReader                      | FilterWriter                |
| Parsing          | PushbackInputStream / StreamTokenizer |                                     | PushbackReader / LineNumberReader |                             |
| Strings          |                                       |                                     | StringReader                      | StringWriter                |
| Data             | DataInputStream                       | DataOutputStream                    |                                   |                             |
| Data - Formatted |                                       | PrintStream                         |                                   | PrintWriter                 |
| Objects          | ObjectInputStream                     | ObjectOutputStream                  |                                   |                             |
| Utilities        | SequenceInputStream                   |                                     |                                   |                             |



# References

[Java IO Overview](http://tutorials.jenkov.com/java-io/overview.html)



---

created at 2017-06-20 17:35