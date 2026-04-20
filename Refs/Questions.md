1. **What happen when you enter a website link in browser?**
	- DNS: resolve domain -> IP Address
		- read browser cache 
		- query to DNS server...
			- tell me where I can find .com
			- get ip to that server
	- establish TCP conn: 3 way handshake, TLS/SSL handshake
	- Send HTTP request
		- BE handles and response (go through with APT Gateway, LB, server, redis, db)
	- Browser renders content with response
- **Python and Golang**
	- Python is bytecode interpreted 
		- .py -> bytecode -> Python VM run it
			- dynamic type -> the type can be computed at runtime, it can be changed at runtime -> means inefficient resource allocation
			- each value is object -> heap object 
	- Go is compiled directly to binary - dont need runtime to compile code -> faster
		- strictly with static type
		- all types has been determined at compilation time so it know exactly how many resource need to allocate

# Basic Notes:
- 1 byte = 8 bits
- **Character**:
    - ASCII is a 7-bit code -> 128 characters.
    - Character sets used today in the US are generally 8-bit sets-> 256 different characters.
    - Unicode is an encoding for mandarin, Arabic, etc languages... - typically 2 bytes per character.
- **Integers** are typically stored with either 4 or 8 bytes
### Why JSON?
- human-readable - lightweight format - simple - the data can only be text
- **simple for machine to parse and create**
- It can be accessed by multiple platforms.
- JSON is a language-independent data format. This means that a JSON document can be used with any programming language.
- JSON is best for simplicity and fast data exchange - multiple language support
### Why XML
- **XML is best for structured documents and formal rules is designed for strict validation, metadata, format contracts.**
- JSON _can_ handle structured data, but it is not _designed_ for strict document rules and formal contracts like XML.
	- json has no build-in schema like XML 
	- no required fields - manage at app layer
	- no type enforcement
## Why concurrency?
- Optimize processes and increase throughput using the same resource
> **Concurrency**: deal with a lot of things at once
> **Parallelism**: doing a lot of thing at once


The Tree is a data structure that is consisting of **nodes** (called vertices or points) and **edges** (lines)

B-Tree is a generalization of a self-balancing binary search tree
- in which each node can hold more than one search key and have more than 2 children.
- Every node has at most m children
- Every non-leaf node (except the root) has a least `m/2` children.
- All leaf nodes appear at the same level and carry information.


# Common
- JWT là gì - dùng để làm gì - ứng dụng
- How is the sorted set in Redis implemented?
- Why do we need a TCP handshake?
	- ensure both sides are reachable
	- TCP is reliable -> make sure no packet loss, ordering, duplicates
	- stateful, bi-direction communication with sync sequence number
- Restful vs GraphQL vs gRPC
    - Why using JSON
    - List some status responses
- what is a cooperatively / preemptively thread
- Can you tell me anything that you know about Docker?
- Stack and heap
    - The stack is fast. The heap is slow.
- Draw a sequence diagram for the login feature
- Asymmetric and symmetric algorithms
- Designing e-commerce with login, viewing products, updating the product with 1000req/sec
- Kiểm tra 1 nums có thể chia 3 mảng con có tổng ===
- Cho 1 mảng có các rule B→C, C→A Tìm từ mach với rule đó → BCA
- HTTP vs HTTPS + SSL
- TCP vs UDP
    - Why do we need three ways handshake - purpose
    - Compare
    - When to use
- Difference between RUN, CMD, and ENTRYPOINT in Dockerfile.
- mutex, semaphore
- - What did you learned at current company?

```go
 type TreeNode struct {
      Val int // why not be a pointer
      Next *TreeNode // why is a pointer
 }
```

# Database
- Database scaling → sharding - replica
    - when - how
    - master-master vs master-slave
- MVCC: Multi-version concurrency control
- SQL Basic
    - HAVING - WHERE: difference
    - biễu diễn JOIN: bằng lời và bằng 2 vòng tròn giao nhau
    - Function, procedure, trigger…
    - SQL Planing
- mongo có table ko → ko có table + ko cần define scheme trc
    - when to use
    - **Why is it fast than mysql**
    - document based?
- How to design DB → make sure data consistency, data redundancy, and satisfy ACID.
    - consider CAP
        - NoSQL offer available better
        - SQL offers good data consistency.
- Transaction - ACID, locking
    - Cho ví dụ deadlock in transaction → cách giải quyết:
        - ví dụ transaction T1 transfer money from user 1 → user 2
        - tại thời điểm đó T2 transfer money from user 2 → user 1 → gây deadlock
        - —> giải quyết sort theo user id để update vào db
    - Locking types
- Indexing
    - Clustered index + Non-clustered index
    - Composite index: the order indexing.
    - Benefits - drawbacks
- Optimistic với Pessimistic lock: when we will acquire the lock: benefits + drawbacks
- Why do we need to use a pointer (pass-by-reference) instead of pass by value
    - → we want to modify the pointer
    - → the struct is very large and a deep copy is expensive
- How to optimize query
	- partition
	- index
	- denomarlize 

- Design transaction: [Database Transactions in Go with Layered Architecture | Three Dots Labs blog](https://threedots.tech/post/database-transactions-in-go/?ref=dailydev)
	- [go-web-app-antipatterns/04-transactions/05-tx-provider/main.go at master · ThreeDotsLabs/go-web-app-antipatterns (github.com)](https://github.com/ThreeDotsLabs/go-web-app-antipatterns/blob/master/04-transactions/05-tx-provider/main.go)
- 

# Golang
- Why you choose Golang
	- Concurrency
	- Ease of deployment
	- Performance
- Go thì hỏi Goroutine, Select.
- Goroutine là gì, benefits, drawback
- why go is fast, concurrency modeling in go
	- statically typed -> type of each var is known at compiled time
	- lightweight Goroutines : lightweight processes called Goroutines
			- channels - powerful mechanism to communicate and synchronize between goroutines
			- **No Threading Overhead:** Unlike traditional threads, goroutines don't require heavy OS involvement for creation and management.
	- Garbage collection: automatically manage memory allocation and deallocation.
- Go: set n > number of process → what happens + how to properly set n value
- (Slice and Map act as references)
- map in Golang quản lý bộ nhớ s
	- -> map is always allocated on heap
	- Map trong Go luôn allocate trên heap, biến map thực chất chỉ là một pointer đến struct `hmap`. Dữ liệu được lưu trong các bucket, mỗi bucket chứa tối đa 8 key-value. Khi load factor vượt 6.5, Go tự động double số bucket và evacuate dần dần để tránh pause. Map không bao giờ tự shrink — muốn giải phóng memory phải tạo map mới.
- Why do we want to use concurrency
- interface{} vs []interface{}

```go
func foo1(v interface{}) {}
func foo2(v []interface{}) {}

foo1([]int{1,2}) -> valid
foo2([]int{1,2}) -> invalid
```

- Embedded interface
    - → It gives the ability to borrow behavior from multiple classes (similar to multiple inheritances)

# Algorithms + Data structure
- What is B-Tree
    - The difference with Hash Table
- Heap - priority queue
    - build heap
    - when to use
- Consistent hashing
- Leaky bucket algorithms
- Bloom filter

# OOP
1. Ý tưởng oop như thế nào, cho 1 ví dụ khi mình dùng oop. Thế nào là lập trình đối tượng? Cho biết các tính chất đặc thù của lập trình hướng đối tượng?
2. 4 tính chất của oop
    - Khi nào cần kế thừa, định nghĩa interface để làm gì,

Class is a template of object - nothing data

Object is instance of class - have full data

The interface is an abstract class - can’t create an instance

1. what is the difference between a compiler and an interpreter?
2. data type: list and set
    1. So sánh độ phức tạp: insert, delete…
    2. Ứng dụng khi nào (coi mấy ứng dụng của set thường được dùng khá nhiều)
3. sort algorithms: merge, quick, heap, buble
    - heap sort dùng khi nào -> Trả lời : vào priority queue, để làm định thời cpu, các giải thuật định thời cpu, vào thuật toán đồ thị như Dijkstra, prim,.m (heap hỏi nhiều)
4. coi DSF, BSF kỹ
    - pause condition

- Coi cách tính complexity: time + space: lật sách coi các tính độ phức tạp quick sort đồ lại.
- Phân biệt ArrayList , Linkedlist và Vector?
- Sự khác nhau giữa ArrayList – Array, Linkedlist – Arraylist, Set – List, Override – Overload?
- Khái niệm tham trị và tham chiếu? - nên kiếm ví dụ khi nào cần dùng cái nào
- Coi lại bộ nhớ stack và bộ nhớ heap (sài đệ quy thì sài bộ nhớ nào - trong C thì loại biến nào nằm trên heap)

### Đệ quy:

- Khi nào thì nên dùng dệ quy khi nào dùng vòng lặp
- Bất lợi dùng đệ quy là gì
- Viết hàm tính giai thưa bằng 2 cách nhận xét cách nào tốt hơn

Coi lại mấy ứng dụng của stack

- vd: co the dung stack de reverse link list

Cho 2 cái link list được sort sẵn head_list_1 -> 1 -> 2 ->7 -> 12 head_list_2 -> 3 ->7 -> 11 -> 14

1. Merge 2 list lại đúng thứ tự tăng dần
2. Đảo ngược list - chỗ này kiếm thêm hình như có TH đặc biệt
3. nhận xét các độ phức tạp: insert, delete, traverser.
4. Tìm số ở giữa link list

- Tìm max
- Tìm số lớn nhất thứ 2

## Dynamic Programming - hard problem

> Characteristics of DP: (top-down memoization and bottom-up tabulation)
> 1. Overlapping Subproblem
> 2. Optimal Substructure Property

NodeJs handles requests using an Event loop inside NodeJs environment.

- the event loop is an event-listener that function inside the NodeJS env and it is always ready to listen, process, and output for an event
- asynchronous means all function in JS that are processed without blocking any other request
    - > They will be processed in the background
        
- synchronous say that the functions were executed in the sequence it was defined

# What happens when you type an URL into a web browser

[What happens when you type a URL into your browser? — The big picture (with Cloud) | by Christopher Le | Jun, 2023 | Medium](https://medium.com/@locvicvn1234/what-happens-when-you-type-a-url-into-your-browser-the-with-aws-ec2-8d53dca6decc)


# Docker
**Container**: a standard way to package your application code, configuration and dependencies into a single resource
- solve isolation problem
- conflict dependencies, configuration

**Pod**: a group of containers deployed together on the same host


## Program - Process - Thread
1. The program contains a set of instructions.
2. The program is loaded into memory. It becomes one or more running processes.
3. When a process starts, it is assigned memory and resources. A process can have one or more threads. For example, in the Microsoft Word app, a thread might be responsible for spelling checking and the other thread for inserting text into the doc.
Processes are usually independent, while threads exist as subsets of a process.
🔹 Each process has its own memory space. Threads that belong to the same process share the same memory.
🔹 A process is a heavyweight operation. It takes more time to create and terminate.
🔹 Context switching is more expensive between processes.
🔹 Inter-thread communication is faster for threads.



[Implementing Caching Strategies: Techniques for High-Performance Web Apps - DEV Community](https://dev.to/nayanraj-adhikary/implementing-caching-strategies-techniques-for-high-performance-web-apps-3dm7?ref=dailydev)

# Difference between Message Broker
There're 2 types of message brokers:
- In-memory: RabbitMQ,...
	- Usecase: maximum **Throughput**, the **order** is **NOT** matter
	- Example: 
		- Youtube, I upload a video so that would be encrypted so it doesn't matter if my video and other videos can be processed first or not
- Log-based: Kafka, AWS Kinesis, SQS...
	- Usecase: the order must be guarantee, ability to replay,...
	- Example: 
		- we want to replicate from DB to search index (OpenSearch,...)
		- we want to compute the average of sensor metrics coming every 20 messages.

## System Design
- [Design a URL Shortener - System Design Interview (algomaster.io)](https://blog.algomaster.io/p/design-a-url-shortener?ref=dailydev)
- [System Design Interview: Design Twitter (X) | by Hayk Simonyan | Aug, 2024 | Level Up Coding (gitconnected.com)](https://levelup.gitconnected.com/system-design-interview-design-twitter-x-695cd800de51)


1. **What's diff between RESTful and HTTP APIs?**
- **HTTP APIs**: refers to API using HTTP protocol
	- HTTP API can be as simple as calling a URL endpoint and receiving a response in any format (like JSON, XML, HTML).
- **RESTful**: is not a protocol that refers to the standard (architectural style) - a set of constrains/principles for building/designing APIs which usually uses HTTP
	- Stateless (must be), Cacheable
	- Client-Server: clear separation boundary -> server don't need to store client state
	- Uniform interface: **resources** are identified via URLs, **operations** are defined by HTTP method


---
1. Về design  
- Bên cty trước đây thì hệ thống backend được thiết kế thế nào?  
- Có những giải pháp, protocal nào cho phần kết nối? Tại sao mình lại lựa chọn Redis, MQTT mà không phải những cái khác.  
- Em có biết về Cache không? Khi nào mình sử dụng Cache? Trước database có đặt cache, có những vấn đề gì có thể xảy ra với kiến trúc mà em đưa ra?  
- Em có biết về Queue không? Khi nào mình sử dụng Queue? Mình sử dụng những queue nào? Quotas của nó là bao nhiêu? Khi đầy Queue mình sẽ xử lý như thế nào?  
- Với 1 hệ thống đang có sẵn, bên mình có sẵn Google Ads rồi, được nhúng vào trong những thẻ `<script>` chẳng hạn. Bên cty có nhu cầu sử dụng những thông tin từ Google Ads. Hãy thiết kế 1 hệ thống lưu trữ những thông tin mà Google Ads xuống Database.  
- Em có sử dụng Micro-service không?  
- Kiến trúc CQRS là gì? em ứng dụng như thế nào?

1. Về Database  
- Index là gì? Partition là gì? Sharding là gì?  
- Giữa SQL và NoSQL thì những tiêu chí lựa chọn là gì? nếu nói về nhanh thì với 1tr record thì cái nào đọc/ghi nhanh hơn?  
	- SQL -> data have complex relationships - fixed schema (less change) - need follow ACID
	- NoSQL -> flexible schema (unstructured data), easy to scale-out (due to data don't have complex relation, easy to shard data into multiple machines,...)
	- Scale-out means adding more servers to handle more traffic. In databases, **sharding** is one way to scale out: we split the data across multiple nodes so each node manages part of the dataset.
	- Unstructured data means data doesn't follow a fixed schema: logs, json, document, images,... where each record can have diff fields, format.
	- I think it depends on how we store and use in some way, 1 millions rows usually is not large
		- Read Perf
			- **NoSQL** often uses hash-based key-value storage, providing O(1) lookups when accessing by primary key - **SQL** databases like PostgreSQL use B+ Tree indexes, which provide O(log n) lookups - still very fast, but slightly more overhead for simple key access
			- Complex Query
				- SQL offer better for complex join, range query.
				- NoSQL we need to denormalize data or doing join logic at application level that can involve lot of query to db.
		- Write Perf 
			- NoSQL will faster due to not check constrains (relationship to other tables like SQL)
			- SQL must enforce ACID properties, validate relationship, **maintain indexes** adding write overhead
- Trong SQL có các clause: SELECT, FROM, WHERE, JOIN, GROUP BY, HAVING, ORDER BY, LIMIT. Hãy sắp xếp thứ tự thực hiện.  
	- FROM -> JOIN ->WHERE-> GROUP -> HAVING ->  SELECT -> ORDER -> LIMIT
- Có các kiểu JOIN nào?  
	- left/right/ full(outer)/inner
- Cho 1 câu SQL (...), đánh giá câu lệnh query nào, liệu có thể cải thiện nó hơn được hay không?  
- Có 1 bài toán là bên mình có ứng dụng chat, sơ sơ thì nó có 2 bảng Room và Message. 2 bảng có thiết kế: Room(room_id, message_id) và Message(msg_id, msg_content, sender, create_at). Anh muốn 1 report lấy những message mới nhất của tất cả các room. Hãy viết câu query là điều đó.
	- 1 room n message - 1 message > n room?
	- select * from room r left 
	- join message m on r.message_id = m.msg_id
	- order by m.create_at desc 
- Hỏi sâu về concurrency

1. Về vận hành  
- Deploy code như thế nào? Áp dụng CI/CD, mô tả toàn bộ quá trình mà em đã thực hiện.  
- khi hệ thống prod đang chạy như vậy, em không được tắt nó (No Downtime) thì thực hiện migration data như thế nào?
1. Web  
- RestAPI là gì? có bao giờ sử dụng graphAPI, gRPC chưa? điểm khác nhau là gì? tại sao mình sử dụng RestAPI mà không phải cái khác?  
- Kể tên các lỗi bảo mật mà mình dễ gặp phải và cách xử lý.  
- SQL injection / XSS là gì? mô tả chi tiết và cách khắc phục  
- Vấn đề n+1 là gì? mô tả chi tiết và cách khắc phục  
- JWT là gì? các thành phần của nó? cách nó hoạt động như thế nào? Tại sao lại dùng JWT mà không dùng Basic Auth? Use case sử dụng của Basic Auth là gì?  
- ở phần login, bạn A thực hiện login bằng 1 thiết bị A thành công. Sau đó, trong lúc bạn A đang sử dụng dịch vụ thì bạn B thực hiện login ở thiết bị B. Làm sao để "đá" bạn A khi bạn B login thành công?

[System Design: The complete course](https://kps.hashnode.dev/system-design-the-complete-course)


### why is redis so fast?
- Data is stored in RAM
	- RAM connects directly to CPU
- Data structure in Redis is simple (key-value)
	- compared with SQL: B-tree, ...
- Single-thread
	- no happen ctx switching
	- dont need to ensure consistency

## Scaling Writes
### Bottlenecks
- Disk I/O limit
- Lock contention: conflict between concurrent write - multiple write on a same row
- Network: consume network bandwidth
### Scaling method
- Vertical scaling, DB optimize (choose db type,...)
- Sharding / Partitioning
- Handle async with background job with Queue/Load Shedding
	- to absorb burst traffic -> decouple writes using queues
- Batching
	- 
#### Method 1: Vertical scaling, DB optimization
- Increase RAM, faster SSD, better CPU
- DB-level optimization
	- **OLTP (Postgres, MySQL)** 
		- -> ideal for transactional workload where **consistency and atomic** are crucial
		- **reducing index** overhead on write heavy table
		- using batch insert
	- Use **LSM-based DB** (Cassandra, RockDB,...)
		- these are designed for **high write-throughput**
		- Instead of writing directly to disk, they buffer write in memory (Memtable) and flush them sequentially to disk as sorted run (SSTables) - data in SSTable is immutable cannot be modified - it has compaction stage to merge data
		- => **minimize randome I/O** 
	- Use **Log-structured system** (Kafka, Clickhouse)
		- optimize for **append-only workload** - write are sequential and compaction happen async
		- Kafka is famous for event-streaming where million message are appended to logs

#### Method 2: Sharding, Partitioning
- Sharding -> distribute data across diff servers
	- define **where** data is split - **physical** - **horizontal**
- Partitioning -> distribute data by logical group (it can be in the same server)
	- define **how** data is split - **logical** - **vertical** + **horizontal**
	- method: **range** (>2026,...), **list** (by country,...), **hash**
- Disadvantage: **high complexity**
	- hot shard -> make unbalanced traffic
	- cross query is diff, and costly
	- operational monitoring is overhead: monitor, backup,....
- How do you handle re-sharding?
	- → Use **consistent hashing** or **dynamic shard splitting**.

