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

**M: machine** - 1 OS thread
**P: processor:** manage scheduling and own run queues - `runtime.GOMAXPROCS()` ~ CPU cores
**G: goroutine** - A lightweight task (your `go func()` ) - that is scheduled to run on a processor

P = 2 -> means can have 2 tasks run parallel

Go uses an **M:N scheduler**, meaning:
- **M** = number of OS threads
- **N** = number of Goroutine
Each **P manages one M** and **runs many Gs** using a local queue.
that's how Go can handle million Goroutine on a few OS threads
If P runs out of work, it **steals G** from another P - to keep CPU busy efficiently 

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

## Why Go don't map Goroutine directly to M
if G -> M it will become 1 goroutine = 1 OS thread 
with P it can manage and schedule N goroutine
Each P has 
- a local queue 
- minimal locking 
