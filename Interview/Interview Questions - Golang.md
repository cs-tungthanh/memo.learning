
### 1. Concurrency
**Q1:** What is a race condition in Go? How can you detect and prevent it?
**Q2:** Explain the difference between `sync.Mutex` and `sync.RWMutex`. When would you use each?
**Q3:** How does Go’s `select` statement work with channels? Give an example.
**Q4:** What is the purpose of the `context` package in Go? How would you use it for cancellation? how to cancel goroutine?
```
func Foo(ctx context.Context, ch <-chan int) {
	// or we can use stop channel to receive signal from outside
	for {
		select {
		case <-ctx.Done():
			fmt.Println("complete")
			return
		case v, ok := <-ch:
			if !ok {
				fmt.Println("cancelled")
			}
			fmt.Println("receive", v)
		}
	}
}
```
### 2. Interfaces and Types
**Q5:** How does Go implement interface satisfaction? Is it explicit or implicit?
**Q6:** What is an empty interface (`interface{}`)? What are common use cases for it?
**Q7:** Can you explain type assertions and type switches? When are they useful?
### 3. Error Handling
**Q8:** What is the idiomatic way to handle errors in Go?
Can you show how to wrap errors for more context?
**Q9:** What is the purpose of the `errors.Is` and `errors.As` functions in Go 1.13+?
### 4. Testing and Tooling
**Q10:** How do you write a unit test in Go?
How do you run tests and what does the `go test -cover` command do?
**Q11:** What is a table-driven test? Why is it useful in Go?
### 5. Go Idioms and Practices
**Q12:** What is a zero value in Go? How does it help simplify code?
**Q13:** Explain the difference between `new()` and `make()` in Go.
**Q14:** What is a goroutine leak? How can you detect and avoid them?
### 6. Miscellaneous
**Q15:** How can you embed one struct into another in Go?
What are the benefits and limitations?
**Q16:** What is shadowing in Go? Give an example.
**Q17:** Describe the `defer` statement. When is it commonly used?