# NestJS Architecture

Look at the **"Node.js Runtime"** as the Virtual Machine and **"NestJS"** as the Application Framework (similar to ASP.NET Core or Spring Boot) that provides the high-level orchestration.

Here is the architectural stack of a NestJS application from the OS up to the code.

---

### 1. The OS & Node.js Process (The "Virtual Machine" Layer)

When you run `node main.js`, the OS creates a single process.

* **The "VM":** Node.js is the environment. It loads the **V8 Engine** (equivalent to the JIT compiler) and **Libuv** (the "Platform" layer).
* **Threads:** Unlike the JVM, Node.js is famously "single-threaded" for your code execution, but the OS actually sees several threads:
* **The Main Loop (Event Loop):** Where your NestJS code runs.
* **The Worker Pool (4-12 threads):** Handles heavy I/O (File System, DNS, Crypto) and Garbage Collection (GC).
* **The V8 Inspector/Helper threads:** For debugging and optimization.



### 2. The NestJS "AppDomain" (The Startup/Container Layer)

Once Node.js is alive, NestJS begins its **Bootstrap** phase. This is the equivalent of loading the framework assemblies and setting up the IoC container.

* **The Root Module (Entry Point):** `NestFactory.create(AppModule)` acts as the entry point.
* **The Scanner:** Nest scans the metadata (Decorators) and builds a **Dependency Graph**. This graph is the framework's internal "Map" of every class, service, and controller.
* **The IoC Container (The Registry):** Nest creates a central repository. It instantiates your **Singletons** (the default scope). Once the container is "primed," the application is ready to accept traffic.

---

### 3. The Execution Loop (The Request Layer)

In the CLR, a request might spawn or use a ThreadPool thread. In NestJS, a request enters the **Event Loop**.

* **The Adapter:** Nest sits on top of an HTTP Adapter (Express or Fastify). The OS socket receives data, Node.js parses it, and hands it to the NestJS **Middleware**.
* **The Context:** For every request, Nest creates an `ExecutionContext`. If you have **Request-Scoped** providers, Nest creates a "Transient" sub-container just for that specific request, which is cleaned up immediately after the response is sent.
* **The Pipeline:** The request flows through the "Onion" of Guards, Interceptors, Pipes, and finally the Controller.

---

### 4. Comparison Table: CLR/JVM vs. NestJS

| Feature | CLR / JVM | Node.js + NestJS |
| --- | --- | --- |
| **Host Process** | `dotnet.exe` / `java.exe` | `node.exe` |
| **Execution Engine** | JIT Compiler (within VM) | V8 Engine (within Node) |
| **Isolation Unit** | AppDomain / ClassLoader | **NestJS Module** (Logic isolation) |
| **Threading Model** | Multi-threaded (Thread-per-request) | **Single-threaded Event Loop** |
| **IoC Lifecycle** | Managed by Framework (e.g., Unity, Spring) | Managed by **NestContainer** |
| **Memory Cleanup** | JVM/CLR Garbage Collector | V8 Garbage Collector |

---

### 5. The "Shutdown" (The Graceful Exit)

In the CLR, the AppDomain unloads. In NestJS:

1. **Signal Capture:** Nest captures `SIGTERM` from the OS.
2. **Hook Execution:** It iterates through the IoC container and calls `onModuleDestroy()` and `onApplicationShutdown()` on every provider.
3. **Connection Drain:** It tells the HTTP Adapter to stop accepting new connections and finish pending ones.
4. **Process Death:** Once the Event Loop is empty and all hooks are done, the Node process exits.

---

### The Big Difference

The most significant departure from the CLR model is the **Thread Management**. While the CLR relies on OS threads to scale, NestJS relies on **Non-blocking I/O**. The IoC container is designed to be extremely lightweight because it usually only handles one "main" thread of execution, making the dependency resolution very fast compared to the overhead of thread-safe containers in Java or C#.

Since we've mapped the "Infrastructure," would you like to see the **Metadata Storage** (Reflect-metadata)? That is the "Glue" that allows the IoC container to actually see your classes and their dependencies.