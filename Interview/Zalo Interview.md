
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
- Why you migrate to micro-services, what it differ and benefits than monolith?
- How can you split monolith to micro-services, what factor you consider?
- Follow up, you said micro-services could scale efficiently but monolith could do the same, are u look at Oracle DB why it can handle high-traffic with monolith architecture?
  - You said monolith hard to horizontal scale? Is it true? He said: he will duplicate to multiple pods and only allow specific API traffics, it the same with micro-service?
- What did you learned at Ahamove?
- You said migrate to Golang for high-performance, why ? What’s the most optimized & high-performance you use at Golang and what it differ than Python?
- You said Golang is strict type than Python. What is typing at Golang, I found all features at Golang that have the same with Python
  - Goroutine is the same with thread pool async at python
- Why memory model at Golang is optimize than python
- Except for work, are you learn or read any else, follow up, what’s type of blogs you reading?
  Problem solving questions:
- I have a 10 trillion, how you subtract it one by one until reach 0. How you solve it as soon as possible?
- Follow up, the same problem when subtract that number when it’s stored at Database.
