---
tags:
  - OS
  - Process
  - Thread
  - Goroutine
  - closure
---
### **When you using distributed cache to prevent duplication, do u know Idempotence, are there other ways? What case Distributed cache with Redis could make duplication?**
-> yes I know idempotence
- when a same request is exec multiple time (due to retries, timeout, restart...) the final state should remain in valid state - example pay 2 twices but only take money once
- I often use redis SETNX with idempotence key; if key is exist it means we already processed that request so we can ignore the request or return the cache request
- but I usually set it with ttl so if the ttl expired before the time request come it will process again this request
- Other ways like: 
	- using SQS **deduplication**
	- using **Inbox** pattern
- What case Distributed cache with Redis could make duplication?
	- -> TTL expires before request come

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

1 Process -> **n** OS Thread (main thread, other threads)
- each process have its own heap memory that can shared between threads
- each Thread have 1 stack -> execute on 1 CPU core at a time
MPG: 1 Machine (OS Thread) -> 1 Processor -> 1 Goroutine
- in Go app: we will have **n OS Threads** to run not only 1 that why we say run million goroutine on a few threads

### How thread work when start app:
A process create main thread, each thread share the same:
- Code segment
- Heap: shared memory for allocated objects
- File descriptors: threads share access to opened file or sockets
However, each thread have its own:
- Stack
- Thread control block (TCB): a small data managed by OS contains: thread ID, stack pointer, program counter, register and other CPU information
Different between asynchronous & parallel execution?
- Parallel is suitable for executing CPU-bound task that can run on multiple CPU cores.
- Asynchronous works well for I/O task that is heavily rely on waiting like network call,…
### **Additional Threads May Be Created**
Depending on the language and execution model:
- **Go:**
    - Uses **a single OS thread per core** by default.
    - Has its own **scheduler** that maps Goroutines to OS threads.
    - Creates **worker threads** for garbage collection.
- **NodeJS:**
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
   - First I need create jira ticket, list draft ideas (pain point, idea to solve, background, estimate determine scope affect)
   - Raise to team or higher level stakeholders, first let check the priority of this task will it have enough priority to do now, if the task is large we can break it down into multi milestone, what output we expect for each milestone
   - If the task is just an idea we will need request a time to pick task and make a proposal to this task the output we will know more detail, actionable steps, output expected...) sometime we need consensus from another team, or leader to make sure the product go with the right direction so we need to request a meeting to discuss it directly
   - to prove my solution is correct and that is right to apply we need to debate with ourself first, whether is it reasonate to do that way
   - but not always get consensus or agreement from team member that may came from lot of factors
	   - if solution is pointing out many mistakes we need to fix that - normal case
		   - too big -> need break into achievable milestones
		   - wrong -> need to understand core problem and propose again
	   - but sometime it is the right direction for my team and not the right direction to another team
		   - we need discuss more and determine the scope both of us accepted to go
		   - but sometime if they just reject due to more work I think the only way to consult with higher level who can solve that issue

1. At project …. (Specific is push notification you’ve mentioned), what’s your contribution and your colleagues? How you propose your solution and persuade your team to execute it? (Hint: not only propose or present idea, talk about pros&cons and why need it, what problem it solving, need a long-term roadmap & strategy to deliver it successfully)? Present clearly about your role & contribution?
2. What you often do and ask at the planning & pre-planning section?
	1. core problem
	2. definition of done - ETA deadline - may need to split into smaller milestone
	3. identify constrains maybe with other team, any blocks
	4. which part is unclear, do we need a deeper meeting to solve it?
3. Give me some type of questions you will ask at the planning? What’re the typical questions u ask to ensure understand PO’s requirements correctly?
   - You said need to identify whether there’s dependency. Can you more clarify it, what type of dependency you need to check?
1. What you will do before & after planning?
	1. after planning: reduce ambiguity, keep executable actions 
2. When you work with partner team or cross-team at your squad, have u encountered any conflicts, describe it & how u solve them?
3. When you work at your squad, do you encounter any conflict with another member and how you solve it?
4. Have you standardized your Partner API, how you do it with seamless partner integration, how long it takes your time to do it, how long it successfully release to production.
5. While your development is fast but it release late, what blocks you? You said your QC team is full of resources and you tried to push them several times, why you’re not push at the beginning of quarter?
6. Why QC prioritize their task and not prioritize your task?.
7. t if u receive a requirement from PO need a lot of effort & you can’t fulfill it at expected due date, it can’t be rescheduled, you can’t re-distribute task to your team member because they already have their own high-priority task, what you will do?
11.13. er releasing into production, what you do next? How you monitor & detect anomaly errors, when you revert build or hot fix, what type of error you consider to hotfix instead of revert?
    - What percentage p% you used to monitor dashboard?

### Stack and heap?
   - Stack is dedicated to a goroutine (a task), but heap is shared across threads within the same process
   - NodeJS/Python run on a single process
   - **Process → threads (M) → goroutines (G) → each G has its own stack**
   - 1 process can have multiple threads, and all those threads share the same **heap** 
### Are thread & Goroutine stack size statically or dynamically, how it could dynamically resize?
- OS Thread stack are fixed size when create -> if exceed  -> stack overflow
	- Goroutine Stack can resize start from 2KB
- Goroutine use Go runtime, implement M:N scheduling model: multiple Goroutine are multiplexed into fewer OS threads
- Goroutine stacks are divided into segments, go routine will allocate more larger segments and link it to existing stacks, make it can dynamically resizing. The runtime copy current stack’s components to the new block, update pointers and resume execution
- Thread life cycle: new, runnable, running, waiting, terminated
### Stack and heap memory? When data is put into stack or heap?
- Stack serve for local var, func params, func calls, determined lifetime objects
	- data is private to a call stack
	- a stack belongs to a goroutine not be shared
- Heap is used for undetermined lifetime objects (dynamic) that may outlive a function call
	- share across goroutine/threads
- Stack will be faster and auto free
	- faster because allocation and deallocation is just pointer movements while heap need garbage collector
- Heap won’t be reserved at process start, i allocate as needed and private to each Process
### How context switch works to distribute threads workload
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
### How garbage collector works, how it can keep reference count for variable
- Go GC uses mark-sweep technique
	- GC works on heap memory, it marks all reachable objects starting from stacks or globals then sweep unreachable ones (reclaim unreachable value)
	- GC only reclaim unreachable var, it mean when you declare var but haven’t used yet, it still alive. When it goes out of scope, or no referencing in it (out of function scope, stack frame,…) it will be reclaimed.
- Stack: 
	- local var, func params
	- pointer to heap object
	- auto free when func returns
- Heap
	- object with dynamic lifetime
	- **shared between goroutine**
	- lifetime is not deterministic
	-> **Heap is clean by Garbage collector (GC)**

```
func makeUser() *User {
    u := User{Name: "Alice"}
    return &u
}
=> `u` must live after makeUser returns -> u will on heap -> dynamic lifetime
-> cannot determine when we can reclaim u
```
### What is closure and how it works?
- A closure happens when a func captures var from its scope, even after the outer func return, the inner func can still access and modify those vars
- Example:
```
  func outerFunc() func() int {
	  c := 0
	  return func() int {
		c++
		return c
	  }
  }
  add := outerFunc()
  add() //=> 1
  add() //=> 2
  ```
- Benefits:
	- useful for encapsulation states -> allow to keep private datas and void global vars
