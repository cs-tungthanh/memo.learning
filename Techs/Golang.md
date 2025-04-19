---
tags:
  - Golang
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

