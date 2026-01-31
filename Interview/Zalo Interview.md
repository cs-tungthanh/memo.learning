
ZaloPay round 1 (1 senior + 1 leader merchant team)
Culture question
- In grooming meeting, PO gives you a requirement, what will you do?
  next, so how you estimate that requirement for delivery on time (e.g. estimate about hour,…)?
- How you read old documents. When you received a feature on an old service, how you know where is it?
- If you and your teammate are in progress delivering project and he hate you, what u do?
- What do you expect in here?: working environment, career path, title, culture?

Technical question
- What’s your featured work you was proud of?
- How your system authentication & authorization?
- How JWT authorization work, which hash function be used for signature? Detail how hash function work like MD5, RSA, HS256, RSA, RS256,tE…
- How many design pattern you know? Ask to explain some pattern.
- Ask about your CV, e.g. push notifications by event-driven architecture, some questions such as:
  - You said about event-driven architecture, how you apply it, how you migrate from your monolith to micro-service?
  - Describe about your featured work you proud of, what you do?
  - Why your company duplicate data about order (because it serve search use case) —> what u can do to optimize that duplicated data?
  - In case consumer process fail message, is event lost and are there way to re-processing from offset fail, how to do it?
  - Your service process event successfully, sent to 3rd partner, but data is wrong. You know the offset need to reprocess again, what you doing and how you do?
- Is there case when you index your column but your query still slow, why? (Suppose your query is simple get where your column index)
- In distributed cache, how you overcame it without using Redis & Database
- Why u said gRPC faster than REST? In case u said the transmission by binary data, also in REST we can encode and sent request along with encoded data then server will decode later, is it a factor that make gRPC faster? Or what difference with REST?
- You said u have standardized about your Partner API, what you do? What convention or best practices apply for RESTFUL
  Advanced question
- How dead-letter queue work, how delay queue schedule delayed event in RabbitMQ & Kafka?
- In Kafka, when consumer read from partition, have it duplicate processing (read from primary & replica partitions, what replica partitions do?)
- When message reach your Kafka cluster, how to know it go into which cluster, is it across load balancer, if so how load balancer distribute message to your cluster.
- What happened when inserting data to ES?
- How you can optimize a large document at ES?
- When using an SQL database, what are the crucial metrics to monitor to ensure performance, reliability, and scalability of the system?. Similarity at Elasticsearch
- In Kafka cluster, what happened when one cluster has been downed?
- I said about MQTT cluster and webSocket server for handle communication, how WebSocket server can grow and scale when too much opened connection from client

Zalopay round 2 (Head of engineering)
- **Why you migrate to micro-services, what it differ and benefits than monolith?**
	- isolated deployment
	- can be written in any language 
	- scale to multiple team to handle for each service instead of all teams touch to the same codebase -> reduce conflicts
	- independent scaling each service can be have a specific scaling strategy: CPU bound, GPU, IO,....
	- Disadvantage: this is a tradeoff between **simplicity** and **organizational scalability.**
		- increase latency because we have changed from local func call to communicate across network like REST, gRPC,...
		- complexity increase: because it goes through multiple layers
		- easy to go to anti-pattern because setting the wrong boundary and make it worse more
- **How can you split monolith to micro-services, what factor you consider?**
	- the most important I think is **boundary** for each service, is this service isolated enough to prevent monolith but communicate with network -> split based on **business domain** instead of technical components
	- **data ownership:** each service must own its own db, no other service can modify this data
	- **dependency coupling**
	- **team structure:** service boundary should also align with team boundaries, so each team can fully own their services
- Follow up, you said micro-services could scale efficiently but monolith could do the same, are u look at Oracle DB why it can handle high-traffic with monolith architecture?
	- monolith can scale very well when system is well designed and backed by the powerful infra like Oracle
	- the main diff is not about tech scalability, but it's organizational scalability. micro help multiple team can work independently while the monolith request stronger coordination between diff teams when system grow too big
	- when system grow too big it will waste resource like an app that requires a lot of RAM but another services only need GPU but we still need to upgrade both
- You said migrate to Golang for high-performance, why ? What’s the most optimized & high-performance you use at Golang and what it differ than Python?
- You said Golang is strict type than Python. What is typing at Golang, I found all features at Golang that have the same with Python
  - Goroutine is the same with thread pool async at python
	  - actually it is not the same Goroutine is designed with MPG model with multiplexing and each goroutine representing a lightweight thread and is managed by go runtime that why's we can run thousand of goroutine with multiplexing on a smaller number of OS Thread
	  - rather than with Python thread is exactly OS Thread and is managed by OS scheduler the context switch is large it may take more time than goroutine that managed by Go Runtime
- Why memory model at Golang is optimize than python
- Except for work, are you learn or read any else, follow up, what’s type of blogs you reading?
  Problem solving questions:
- I have a 10 trillion, how you subtract it one by one until reach 0. How you solve it as soon as possible?
- Follow up, the same problem when subtract that number when it’s stored at Database.
