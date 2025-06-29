
# Question
### What if Redis Go down?
Redis primarily stores in in-memory which mean by default:
1. you have a single point-failure that can bring down critical services
2. you risk permanent data lost during crash or restarts
Answer: **Redis Sentinel** 
- Redis Sentinel is a distributed system that monitor Redis instances and automatically handles failover when you primary Redis fails.
- It's not a feature of redis but a separate set of processes that work alongside with Redis to provide HA
1. **Distributed Monitoring**: multiple Redis Sentinels processes run on diff machines continuously checking if you Redis primary or replicas are responding
2. **Consensus-based failover**: Sentinels use **Quorum** system - they vote on whether a primary instance has truly failed before taking action
3. **Automatic promotion**: when failure is confirmed, sentinels elect a leader that promotes the most up-to-date replica to become the new primary
4. **Client notification**: Sentinels acts as a configuration provider, telling clients where to find the current primary
Importantly, this doesn't solve every problem. For example, if you need strong consistency, Sentinel isn't right for you as there is a small window where writes can be lost.  
  
Another critical limitation: Sentinel only works if your entire dataset fits on a single node. If your data volume exceeds what a single Redis instance can handle, you'll need Redis Cluster instead. Redis Cluster provides both sharding (distributing your data across multiple nodes) and high availability, whereas Sentinel focuses solely on high availability for a single dataset.  
  
But in the majority of cases, introducing Sentinel to manage replication and monitoring is enough to all but guarantee that you've eliminated any single point of failure and achieved high availability in that part of your system.
![](../assets/redis-sentinel.png)