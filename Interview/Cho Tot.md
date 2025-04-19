Chotot round 1 (1 senior)
- When you using distributed cache to prevent duplication, do u know Idempotence, are there other ways? What case Distributed cache with Redis could make duplication?
## What happened when start an application, how many type of memory allocated behind it?
 1. **OS Loads executable program from disk to memory**
	 - The OS reads the program from disk and creates a **process**, and assign it a **main thread**
	 - Initializes a **Process Control Block (PCB)** with PID, open files, CPU state, etc.
	- Memory Allocation
		 - Code, text segment -> store compiled instructions
		 - **Heap** → Allocated for dynamic memory (GC-managed in Go & NodeJs).
			 - In Go, Heap is managed automatically by Go runtime (cannot specify when a var allocated in heap)
		 - **Stack** → Stores function calls & local variables.
	- For compiled languages (like **Go**), the OS loads the executable into memory.
	- For interpreted languages (**Node.js / TS**), the runtime (V8) is loaded.
 2. **Runtime setup**
	 - If the language has a runtime like Go or NodeJS, it initializes
		 - GC, runtime scheduling,...
		 - Concurrency handling (Goroutine, event loop)
		 - Thread management
 3. **Begin execution at the entry point** 
	 - Go: main func 
	 - NodeJS: executes script line-by-line (nodeJS),...
		 - V8 starts interpreting JavaScript **line by line** after initializing the event loop.
4. **App Exit & Clean up**
	- when application finishes, the OS **releases memory**, closes open files, and cleans up resources.
	- If the program exits normally, it returns an **exit code of 0**, otherwise, an error code is returned.

  How thread work when start app:
    - A process create main thread, each thread share the same:
      - Code segment
      - Heap: shared memory for allocated objects
      - File descriptors: threads share access to opened file or sockets
    - However, each thread have its own:
      - Stack
      - Thread control block (TCB): a small data managed by OS contains: thread ID, stack pointer, program counter, register and other CPU information
    - Different between asynchronous & parallel execution?
      - Parallel is suitable for executing CPU-bound task that can run on multiple CPU cores.
      - Asynchronous works well for I/O task that is heavily rely on waiting like network call,…
### **Additional Threads May Be Created**
Depending on the language and execution model:
- **Go:**
    - Uses **a single OS thread per core** by default.
    - Has its own **scheduler** that maps Goroutines to OS threads.
    - Creates **worker threads** for garbage collection.
- **NodeJs:**
    - Uses a **single-threaded event loop**.
    - **Offloads blocking tasks** (I/O, file system, crypto, etc.) to a **thread pool (libuv, using worker threads)**.
- **Other Languages (C, Java, Python, etc.)**
    - Java and C create **native OS threads** for each concurrent task.
    - Python’s **GIL (Global Interpreter Lock)** limits execution to **one thread at a time**.

# OS questions:
  - Thread and Goroutine comparison? Why Goroutine cheaper than thread ? How thread works?
  - Concurrent & parallel? What happened when multiple threads concurrently run on core cpu, describe detail the process? How threads could be context switch, how underlying mechanism work?
  - Are thread & Goroutine stack size statically or dynamically, how it could dynamically resize?
  - Are thread tied or managed by OS ? (Hint: thread’s tied to application)
  - How multiple processes can communicate with each other?
  - How application can manage threads (hint: main thread for manage main function and fork multiple child threads to process)
  - Stack and heap memory? When data is put into stack or heap?
  - How garbage collector works, how it can keep reference count for variable?
- At your callback service, how many authentication and authorization you know, describe it?
- How you design your service that can serve various authentication, what’s your design to support & easy adopt with partner authentication config.
- Do you know mTLS, how TLS work at https, how session key in TLS created, is at client or server side. If you said it created in client, how it be secured?
- Different between gRPC & REST, why gRPC faster than rest? Follow up, OSI model, which grpc & rest rely on
- What is closure and how it works?
- Explain what happen when execute a query SQL (hint: query will be parse into DSL,…)
- How underlying mutex lock works?
  - In 32bit architecture machine, how many bytes a struct of 3 var unint8 allocated? (Hint: memory padding allocation)
  - How context works, how it can be used to cancel operations like DB, network call, operation code logic cancel,…, how to implement context —> Hint: use channel to receive signal
  - What you do when improve your code?
  - Slice and array golfing. Follow up, array & linked list data structure, search & 6tykiol space complexity, how can improve Linkedllist search (hint: build tree for faster search)
  - How Go implement channel?
  - How you refactor and improve your code, do you know how profiler Golang work, how it run to capture metrics?
  - How go generic works?
- Solution question:
  - You have a large logs system file stored at S3 and you need to process & generate report from it in the tomorrow, what’’s your design? (Hint: ETL process & batching data processing to worker)

# Chotot round 2 (1 Tech manager + 1 tech lead)
Interview about project development

1. Introduce yourself with your latest experience, talk about 1 projects you joined and 1 project you found the most proud of, why u think It’s the most proud of, 3 points you think the most valuable for you?
2. Do you persuade your team to raise an idea or innovation? How you convince them to prove your idea and how you schedule your planning do execute, release it?
   - When raise an idea, are you point out any drawbacks of your implementation?
3. At project …. (Specific is push notification you’ve mentioned), what’s your contribution and your colleagues? How you propose your solution and persuade your team to execute it? (Hint: not only propose or present idea, talk about pros&cons and why need it, what problem it solving, need a long-term roadmap & strategy to deliver it successfully)? Present clearly about your role & contribution?
4. What you often do and ask at the planning & pre-planning section?
5. Give me some type of questions you will ask at the planning? What’re the typical questions u ask to ensure understand PO’s requirements correctly?
   - You said need to identify whether there’s dependency. Can you more clarify it, what type of dependency you need to check?
6. What you will do before & after planning?
7. When you work with partner team or cross-team at your squad, have u encountered any conflicts, describe it & how u solve them?
8. When you work at your squad, do you encounter any conflict with another member and how you solve it?
9. Have you standardized your Partner API, how you do it with seamless partner integration, how long it takes your time to do it, how long it successfully release to production.
   10. While your development is fast but it release late, what blocks you? You said your QC team is full of resources and you tried to push them several times, why you’re not push at the beginning of quarter?
   11. Why QC prioritize their task and not prioritize your task?
10.12. t if u receive a requirement from PO need a lot of effort & you can’t fulfill it at expected due date, it can’t be rescheduled, you can’t re-distribute task to your team member because they already have their own high-priority task, what you will do?
11.13. er releasing into production, what you do next? How you monitor & detect anomaly errors, when you revert build or hot fix, what type of error you consider to hotfix instead of revert?
    - What percentage p% you used to monitor dashboard?

Chotot round 1 answers:
1. Stack and heap?
   - Stack is dedicated to application, but heap is shared across processes 
1. Are thread & Goroutine stack size statically or dynamically, how it could dynamically resize?
- Threads are managed by OS but it’s tied to an application and isolate with other procesesses.
- Threads are mapped directly to CPU & use kernel-scheduling (1:1 threading model), it can block entire process if synchronization mechanism aren’t use properly
- Thread stack is fixed size, it’s a contiguous block of memory within the process’s virtual address space. During context switch, the CPU registers, including the stack pointer are saved and stored by the OS schedulerqjuhh
- Goroutine use Go runtime, implement M:N scheduling model: multiple Goroutine are multiplexed into fewer OS threads
- Goroutine stacks are divided into segments, go routine will allocate more larger segments and link it to existing stacks, make it can dynamically resizing. The runtime copy current stack’s components to the new block, update pointers and resume execution
- Thread life cycle: new, runable, running, waiting, terminated

4. Stack and heap memory? When data is put into stack or heap?
   - Heap won’t be reserved at process start, i allocate as needed and private to each Process
   - Process heap is isolated because of:
     - OS ensure that each process have its own virtual address space
     - Virtual memory map the process’s address space into physical memory —> make it like dedicated memory
     - Virtual address space is entirely separated so other processes cannot access to it.
   - How processes could communicate?
     - Inter-process communication (IPC) are provided by OS: pipe, message queue, socket, rpm because of OS provide strict process separation, not easily access shared resources like thread.
     - E.g. in python, when we communicate via IPC, Python interpreter serialize & deserialize them behind the back before sending over IPC link, this implemented by pickle module.
5. How context switch works to distribute threads workload

- Context switch step by step:
  - Trigger switch, CPU issue a interrupt signal (part of OS kernel) and save current thread state, including:
    - Program counter (PC): the address of the next instruction to execute
    - Stack pointer: location of thread’s stack in memory (thread stack is tied on specific app)
    - General-purpose registers: data currently being operated on.
  - When thread is running, its state resides in CPU but at Pause state (during context switch) its state is saved into TCB
  - Select other threads
  - Restore thread state and resume execution
- Challenges:
  - Overhead: involve saving/restore states and running the scheduler and interrupt from OS kernel
  - Synchronization issue: threads access shared resources may encounter race conditions

6. How you design your service that can serve various authentication, what’s your design to support & easy adopt with partner authentication config.

- Several authentication methods:
  - Basic authen: support send username & password to partner
  - Digest authen:
  - Bearer token: is a type of token that allows the bearer (whoever possesses it) to access resources.
    - Request to authentication partner server to retrieve token and then use token to callback partner service.
    - OAuth2.0:
      - Oauth is a protocol framework that specifies how token like Bearer are issued, managed & used.
      - request Auth server with client info and scope, ask client consent their permission
      - Type of OAuth 2.0, once issue successfully, the access token is typically Bearer token:
        - Authorization code flow: use for apps with FE and BE
        - Client credentials flow: use for machine to machine communication (this is callback svc support)
        - Password grant flow
        - Implicit flow: used for Single page app
  - JWT: sign payload as JWT and send to partner.
  - API key: Use partner api_key and partner verify the key
  - Shared secret HMAC: use shared secret to sign the payload and send the signature in the request headers, the partner validate the signature

7. Challenges at Partner API & Warehouse management system?

- Partner API
  - High latency API call during high-traffic period:
    - Implement both asynchronous processing, divide several logic make it background running and mechanism for alerting and monitoring.
    - Leverage event-driven to handle event and notify callback later, use retry mechanism for retry failed processing.
  - High traffic make our processing duplicate, use idempotent to process event without data corruption.
- Warehouse management system
  - Optimize API, query database to enhance latency during peak event, reducing spam hit to database
  - Operation often mistake in operation, develop bot to faster resolve issues, reduce manual workload retry support and monitor error case.

8. How garbage collector works, how it can keep reference count for variable
   - GC work on heap memory, there are some algorithms:
     - Reference counting
       - Cons: can’t free variables that are cycle references. But in python have use additional mechanism to detect cycle
     - Mark-and-sweep:
       - Starting from root references and recursively mark all reachable objects. Then traverse and reclaim all unreachable value.
       - In managed runtime, it keeps tracking of root (such as global or local var) to other references. How root are tracked:
         - Stack pointer: runtime can identify local var in active stack frames (during a func call) as roots
         - Global reference table: can keep a table of global var or static var as a root
         - Thread-local storage: runtime will keep track the root specific to each thread
     - Copying collection
   - Useful notes:
     - GC only reclaim unreachable var, it mean when you declare var but haven’t used yet, it still alive. When it goes out of scope, or no referencing in it (out of function scope, stack frame,…) it will be reclaimed.
9. What is closure and how it works?

- In Go, it’ essentially an anonymous function that capture variables from its surrounding scope, its var still alive although the function return. The idea behind it are:
  - Closure capture variables by references, not by value
  - Variable records references to it and GC won’t reclaim its allocated memory.
  - When that function call again, the variables is differ and isolate with old call.