---
tags: Cache
---
Technique to speed up performance in a system
- reduce **latency** in a system
- reduce DB hits

# Questions
1. Do you know about Caching? When should we use caching? If we place caching in front of DB **what issues** can occur with this arch?
Issues:
- cache is **outdated** (inconsistency)-> need invalidation action
- sometime the **invalidation is complex**
- **Cold cache problem**: initially cache is empty -> if all requests flood at the same time all of it will hit directly to DB
- **Limited memory**: need to have a right eviction policies to prevent load all db into cache
- In distributed systems, keeping multiple caches consistent adds more complexity
### why is Redis so fast?
- Data is stored in RAM
	- RAM connects directly to CPU
- Data structure in Redis is simple (key-value)
	- compared with SQL: B-tree, ...
- Single-thread
	- no happen context switching
	- don't need to ensure consistency
### Data can be cached anywhere from Frontend -> Backend
1. Client apps: HTTP responses can be cached by the browser. We request data over HTTP for the first time; we request data again, and the client app tries to retrieve the data from the browser cache first. 
2. CDN: CDN caches static web resources. The clients can retrieve data from a CDN node nearby. 
3. Load Balancer: The load Balancer can cache resources as well. 
4. Messaging infra: Message brokers store messages on disk first, and then consumers retrieve them at their own pace. Depending on the retention policy, the data is cached in Kafka clusters for a period of time. 
5. Services: There are multiple layers of cache in a service. If the data is not cached in CPU cache, the service will try to retrieve the data from memory. Sometimes the service has a second-level cache to store data on disk. 
6. Distributed Cache: Distributed cache like Redis hold key-value pairs for multiple services in memory. It provides much better read/write performance than the database. 
7. Full-text Search: we sometimes need to use full-text searches like Elastic Search for document search or log search. A copy of data is indexed in the search engine as well. 
8. Database: Even in the database, we have different levels of caches: 
	• WAL(Write-ahead Log): data is written to WAL first before building the B tree index 
	• Bufferpool: A memory area allocated to cache query results 
	• Materialized View
	• Transaction log: record all the transactions and database updates 
	• Replication Log: used to record the replication state in a database cluster


## 1. Writing policies

### Write through Cache
- **Simultaneously updated to Cache and DB**
- write in Cache first and push it to DB
- For the system with **write-heavy** → this method should be avoided
**Disadvantage** 
- is that every write hit has to do two writes, one of which accesses slower main memory.
### Write back Cache
- The data is **first written to the Cache** - and **updated into the DB at a later time.**
**Advantages**
**Disadvantages**: inconsistency is inevitable in scenarios where a client reads stale data from the database.
### Write around Cache
- The data is **Directly** written/updated to the **Database** only without disturbing the Cache.
**Disadvantages**:
- Reduce performance because we remove the value in the cache. → increase latency

## 2. Evicting policies
- Least recently used (LRU)
- Most recently used (MRU)
- Least frequently used (LFU)
- Most frequently used (MFU)

**LRU**
- get O1: hash map
- set O1 → hash map
- → vì đây là cache
- → phải có order → array hạn chế vùng nhớ liên tiếp →  linked list
- LRU = double linked list
- why double → because single remove On because we don’t know prev

implement LRU in leetcode
- https://github.com/labuladong/fucking-algorithm/blob/english/interview/LRU_algorithm.md