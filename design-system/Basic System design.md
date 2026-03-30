---
tags:
  - system
  - Gateway
  - LB
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
## What is Microservice?
- a set of small services with **independent domain**
- a service have their own lifecycle: develop, deploy, scale independent
- have their own data (isolated database) to prevent coupling
- communicate across network (HTTP, gRPC, message event,...)
- **Domain is the most important that define its boundary**
	- mindset domain driven
	- clear in boundary context
	- each server has a clear responsibility (Order, Payment,...)
	- prevent sharing db
- Microservices is a result of correcting split boundary between domains
## Bad practice in Micro
- Don’t split your application into Microservices before you know the **boundaries**.
	- -> Distributed monolith
- **Aggregates**
	- Keep the data that needs to be strongly consistent within the same aggregate. 
	- Keep a **repository** for the **aggregate**.
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

# Diff between API Gateway and LB
It works for diff purposes
## Gateway: **HOW**  - front door
- API Gateway is optimized for **API Control**
- handle first layer to check: Auth, Cache, Transform, Rate limiting, Logging, Monitoring
- Versioning /v1, /v2
- It's application level -> How request is handled before going to server
- If we don't have Gateway, each server must handle above actions for each one
## LB: choose **WHICH** server should be used
- LB is optimized for **traffic control and health check at scale**
- health check
- choose target with: Least usage, round robin, weighted server,...
- Failover: when a server is died, it stops sending traffic to this server
- LB is a specialized form of **Reverse Proxy**



Monolith - everything in one codebase doing all of work, when any part fails the whole thing crashes

Microservices solve scaling problem not coding problem


# Notification for 100 million users at 8:00
## 1. Jilter (Traffic Spreading): never send 100 million requests at 8:00
- I would not send all notifications at exactly 8:00. Instead, I would distribute them over a time window using batching, so the system load is smoothed out.
- we can prioritize active user first....
## 2. Cache Warming 
- setup a cronjob to pre-handle and save all info that needed for that notification in Redis with TTL 24h
- I would precompute notification payloads and store them in Redis so that when sending happens, the system only needs to read from cache instead of hitting the database.
## 3. Circuit breaker
- after a few time retry let's show system is busy try later

To send notifications to 100 million users at 8:00, I would avoid sending all requests at the same time. Instead, I would distribute the traffic over a time window using jitter.
I would precompute the notification content and store it in Redis to reduce database load during peak time.
Then, I would use a message queue like Kafka to decouple the system. A cron job would enqueue all notification tasks, and worker services would consume them at a controlled rate with rate limiting.
I would also implement retries with exponential backoff and use a circuit breaker to handle failures from external services like push providers.
Finally, I would consider user segmentation or timezone-based delivery to improve user experience.