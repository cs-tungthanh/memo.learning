---
tags:
  - Golang
  - GoScheduler
  - MPG
---
Go a statically typed compiled language, which means that variables have a specific data type.  

Though being statically typed, Go’s type system is still very flexible and surprisingly, very much a like Typescript’s one, which is a structural type system.  
  
Structural type system, in plain terms, can be explained as duck typing—“If it walks like a duck and it quacks like a duck, then it must be a duck”.  
In a duck-typed system, an object's suitability for a particular operation is determined by whether it can perform that operation, rather than by its explicit type or class.  
  
Working with structural type system gives the developers all the flexibilities but without trading-off type-safety.
![](../assets/Go-Mapping-Type-EdgeCase.png)

# Interface in Go
[Nil comparisons and Go interface | Redowan's Reflections](https://rednafi.com/go/nil_interface_comparison/?ref=dailydev)
- An interface in Go has 3 components:
	- a static type
	- a dynamic type
	- a dynamic value
```go
var n any // The static type of n is any (interface{}) 
n = 1     // Upon assignment, the dynamic type becomes int 
          // And the dynamic value becomes 1

var b *bytes.Buffer    // b is a nil pointer of type *bytes.Buffer
var r io.Reader = b    // The static type of r is io.Reader.
                       // The dynamic type of r is *bytes.Buffer.
                       // The dynamic value of r is nil.

// Output: b is nil
// Output: r is not nil (r holds type information (*bytes.Buffer))

if underlying, ok := r.(*bytes.Buffer); ok && underlying == nil { 
	fmt.Println("r holds a nil pointer") 
} else { 
	fmt.Println("r does not hold a nil pointer") 
}
// Output: -> r holds a nul pointer
```


# Go scheduler - MPG
> **Key rule**: an M must have a P to run a G.


**G: goroutine** - A lightweight task (your `go func()` ) - that is scheduled to run on a processor
**M: machine** - 1 OS thread
**P: is logical schedule context:** manage scheduling and own run queues - `runtime.GOMAXPROCS()` ~ CPU cores

G  →  sits in P's local queue (waiting to run)
P  →  holds the queue, must attach to an M to actually execute
M  →  the real OS thread doing the actual CPU work

P = 2 -> means can have 2 tasks run parallel

Go uses an **M:N scheduler**, meaning:
- **M** = number of OS threads
- **N** = number of Goroutine
Each **P manages one M thread** and **runs many Gs** using a local queue.
that's how Go can handle million Goroutine on a few OS threads

Go runs fast because it runs **millions of goroutines on a few OS threads** using its own scheduler in user space avoiding the cost of creating and context-switching real OS threads.
- OS Threads are expensive ~1 MB of stack mem and context switching requires the OS to get involved -> which is slow
- Goroutine is tiny only start with 2KB of stack mem can be shrink later, so we can run million of goroutine without running over the memory
- Go use G-M-P model which implement M-N scheduling -> map million goroutines onto just a few OS thread
	- G is goroutine that is on P's queue and wait to run
	- M is a real OS Thread
	- P is logical scheduler context which hold a local queue and be assigned with one thread (M) to execute goroutine
	- If P runs out of work, it **steals G** from another P - to keep CPU busy efficiently 

|             | OS Thread | Goroutine  |
| :---------- | :-------- | :--------- |
| Stack       | 1~8MB     | ~2KB       |
| create cost | expensive | cheap      |
| managed by  | OS        | Go runtime |
| ctx switch  | slow      | fast       |

## Goroutine
- start with a small stack (~2KB)
- auto-growth like slice when exceeding its allocated stack (by double the stack size)
- auto-shrink: the runtime may shrink the stack down
- 1 OS Thread can hold million Goroutine - but at a time only execute 1 Goroutine

For **Golang**:
- create a goroutine G
- Put G onto P's local queue
- an M (OS thread) picks up that P
- then M exec G on a CPU core
- 1G -> 1P -> 1M -> CPU
- **GOMAXPROCS** = number of **P**

1 OS thread
↳ runs G1
↳ switches to G2
↳ switches to G3
↳ ...
-> **very fast ctx switching controlled by Go Runtime not OS**
One thread can schedule and execute a million Goroutines over time


# How channel wait and awake Goroutine
> Do you know about sudoq, why dont use directly Goroutine 
> How select work under the hood (phase shuffe, phase on blocked)?
> 
example: when 1 Goroutine is blocked and waiting signal from one or more channel
```go
Goroutine G --> wait chan 1
			--> wait chan 2
			--> wait chan 1

go func() {
	select {
		case v := <-ch1:
		case v := <-ch2:
		case v := <-ch3:
	}
	// select will be blocked when there's no case is ready
	// but if it has default it will not be blocked and run default
}()
```

In a channel it will store 2 list (LINKED LIST)
- **buffer** -> mem to store data for buffered channel 
- **recv** list -> list of goroutine waiting to receive
- **send** list -> list of goroutine waiting to send
In list, each node is a **sudog** - struct of a node in linked list
- a sudog is a wrapper of **a Goroutine and its context** (which channel is waiting, ....)

Question: why don't we store Goroutine directly on a list
**=> list is linked list** - one node cannot stay on 2 lists -> need to wrap it into sudog
1 Goroutine can join to multiple channel to wait -> that need to use multiple **sudog**

```go
type sudog struct {
	*g -> current goroutine
	ch -> which channel is waiting for this goroutine
	next *sudog
	prev *sudog
	......
}
```

so when we write c := <-ch
it will create a sudog and append to list on channel
when ch receive data it pick the first goroutine (sudog) -> and wake that goroutine

when we register a goroutine to wait on a channel or to send a value to channel -> it will create a sudog to wrap this goroutine and push to the list inside channel
in channel it will keep 2 list: receiveList and senderList, these are linked list of sudog
so when a sender send a data it will pop sudoq from receiveList and awake this Goroutine to execute

## How select work under the hood

and how select work is also related to sudoq
when select run will register this goroutine on a list channel 
- for **each channel** will create **a sudoq**
- enqueue sudoq into channel's send/recev queue
- when any one case become ready the G is woken up and other sudoq will be dequeued and released
```
switch
case 0 
case 1
case 2
```
## 🟢 Phase A — _Before blocking_ (shuffle happens here)
When `select` starts:
1. Shuffle cases for example the order to check case: [2 1 0] (rather than always check 0 1 2)
2. Scan channels (non-blocking)
If multiple channels are already ready:  
👉 **shuffle decides which one wins**
## 🔴 Phase B — _After blocking_ (NO shuffle here)
If nothing is ready:
1. Register `sudog` on all channels
2. Goroutine sleeps
Later:
- `G1` sends to `ch1`
- `G2` sends to `ch2`
👉 Only **ONE** wake-up will succeed
At this point:   👉 **NO shuffle anymore**

sudog (n) -> g (1)
**inside g there is a field** -> **selectDone** atomic -> use to race when **multiple sudoq** want to awake **a goroutine**, but only one can success
