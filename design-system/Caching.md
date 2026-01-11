---
tags: Cache
---
> Technique to speed up performance in a system
> 	- reduce **latency** in a system, 
> 	- reduce DB hits

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