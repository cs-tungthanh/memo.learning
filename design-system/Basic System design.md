---
tags: system
---

## Reference
[System Design Interview Question Handbook – Concepts You Should Know](https://www.freecodecamp.org/news/systems-design-for-interviews/?fbclid=IwAR1pdn4DQu-dbUCEjTvvs9ijDw0w2FBdi6aSyZaYMCKxKIZqEWLlkGXxMVk_aem_AfR2FF60Dd4tT8pf56m1HVevFqXemLXfU4Ae4Nv0JVkycE2AcEt7dapPjGT7Op0OurE#section-3-system-availability)

## Guidelines
1. Network: IP, TCP, HTTP
	- [[Protocol- IP, TCP, HTTP]]
2. Storage, Latency and Throughput
3. Availability
4. Caching
	- [[Caching]]
5. Proxies
6. Load balancing
7. Consistent hashing
8. Databases
9. Polling, streaming, sockets
10. Endpoint protection 
11. Message & pub-sub
12. Smaller essential.

- **Database**: Store data so that they or another apps can find it again later
- **Cache**: Remember the result of an expensive operation to speed up reads
- **Index**: Allow users to search data by keywords or filters it in various way
- **Stream processing**: Send a message to another process to be handled asynchronously
- **Batch processing**: periodically crunch a large amount of accumulated data.


Benefits - Key point of Microservice:
- Continuous development
- Isolation of concerns
- Service level scalability
- Parallel development
## Bad practice in Micro
- Don’t split your application into Microservices before you know the **boundaries**.
	- -> Distributed monolith
- **Aggregates**
	- Keep the data that needs to be strongly consistent within the same aggregate. 
	- Keep a **repository** for the **aggregate**.
	- 

For example: we have User and Discount 
- It's common to think of the database tables as your entities. This way, you end up with one repository per table and the app logic orchestrating them. But it has no practical value
- Data should be transactionally consistent should also be coherent and consistent.
- The idea of aggregate: it's a set of data should be consistent
	- that's why we keep a repository per aggregate
- We think User and Discount as separate concerns (entities, structs, table,...) and it makes sense as conceptually they're diff things . The user is used for identity and authentication. Placing order with discount is just one of many thing the user can do. But we want to keep user and discount consistent. So we should consider it as an Root Aggregate (User)


"high-performance" can vary by domain and isn't just about speed. Features of High-Performance Applications include:
- High Throughput
- Data Intensive
- Real Time
- Low Latency
- Event Driven
- Stream Processing



## Non-functional System
### 1. **Availability**
**Availability** measures the percentage of time a system accepts requests and responds to clients.
### 2. **Reliability**
-> Refers to both **time-to-repair** and **time-to-failure**
**Reliability** measures how well a system performs its intended operations (functional requirements). We use averages for that (Mean Time to Failure, Mean Time to Repair, etc.
### 3. **Scalability**
**Scalability** is the ability of a system to handle an increasing amount of workload without compromising performance
- Vertical - scale up
- Horizontal - scale out
The workload can be of different types, including the following:
- **Request workload**: This is the number of requests served by the system.
- **Data/storage workload**: This is the amount of data stored by the system.
### 4. **Maintainability**
-> Refer to **time-to-repair**
 Maintainability, M, is the probability that the service will restore its functions within a specified time of fault occurrence. M measures how conveniently and swiftly the service regains its normal operating conditions.
### 5. **Fault tolerance**
Fault tolerance refers to a system’s ability to execute persistently even if one or more of its components fail

**Techniques:**
- Replication
- Checkpointing