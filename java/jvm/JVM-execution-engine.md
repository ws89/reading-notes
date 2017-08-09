# Execution Engine

The bytecode which is assigned to the **Runtime Data Area** will be executed by the Execution Engine. The Execution Engine reads the bytecode and executes it piece by piece.

- **Interpreter** – The interpreter interprets the bytecode faster, but executes slowly. The disadvantage of the interpreter is that when one method is called multiple times, every time a new interpretation is required.
- **JIT Compiler** – The JIT Compiler neutralizes the disadvantage of the interpreter. The Execution Engine will be using the help of the interpreter in converting byte code, but when it finds repeated code it uses the JIT compiler, which compiles the entire bytecode and changes it to native code. This native code will be used directly for repeated method calls, which improve the performance of the system.   
  - **Intermediate Code generator** – Produces intermediate code
  - **Code Optimizer** – Responsible for optimizing the intermediate code generated above
  - **Target Code Generator** – Responsible for Generating Machine Code or Native Code
  - **Profiler** – A special component, responsible for finding hotspots, i.e. whether the method is called multiple times or not.




# Java Native Interface (JNI)

**Java Native Interface (JNI)**: **JNI** will be interacting with the **Native Method Libraries** and provides the Native Libraries required for the Execution Engine.

**Native Method Libraries**:It is a collection of the Native Libraries which is required for the Execution Engine.



# References

[The JVM Architecture Explained](https://dzone.com/articles/jvm-architecture-explained)

---

created at 2017-08-09 22:12