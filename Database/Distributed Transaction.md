---
tags:
  - ACID
  - Distributed
  - Outbox
  - Inbox
  - Transaction
  - 2PC
---
# Inbox/Outbox pattern - transaction for non db
> used when we want to make sure the DB Write + Pub Message (Queue,Broker...) in a transaction
> Outbox pattern **NEVER guarantees**: "Event sent **exactly once**"
> It guarantees: "Event sent **at least once** (and safely)"
### **Outbox** -> used in **producer**
-> make sure data change and event creation in atomic
-> give reliable event delivery
	cannot solve idempotence -> it must be solved at consumer level

-  we will have a Outbox Table that stores event that need to be published
- Instead of trying to **writeDB and PubMessage** we will change to **writeDB + WriteEvent to Outbox table** in a same local transaction -> so it will complete or fail together
- **Background Worker** will read from **Outbox Table** -> to perform send message and mark record as **send_success** (save by message-id)
- => we will never lose the message but the duplication of publishing message can happen due to worker crash it cannot mark success to table - but it's acceptable due to the Queue may have the same behavior with at least once
### **Inbox** -> used in **consumer**
Inbox Pattern is the **consumer-side** design that handles duplicates safely.
- idea is the same with using **redis** to store idempotence key but in a **persistent database**
	- using **redis SETNX** is just an **cache-based idempotence technique.**
- store processed key **for long-term**

| Dimension                     | Redis SETNX       | Inbox Pattern           |
| ----------------------------- | ----------------- | ----------------------- |
| Purpose                       | Dedup best-effort | Exactly-once processing |
| Storage                       | In-memory         | Database                |
| Durability                    | ❌ Weak            | ✅ Strong                |
| Handles duplicates            | ✅                 | ✅                       |
| Handles crash mid-process     | ❌                 | ✅                       |
| Replay after restart          | ❌                 | ✅                       |
| Observability                 | ❌                 | ✅                       |
| Auditing                      | ❌                 | ✅                       |
| Data safety                   | ❌                 | ✅                       |
| Suitable for finance / orders | ❌                 | ✅                       |
| Performance                   | 🚀 Very fast      | 🐢 Slower but safe      |

# 2 Phase commit (2PC) - strong consistence
> Use for Distributed transaction in Microservices - ensuring strong consistence
**2 phase:**
- **Prepare**
- **Commit**
We must have a **Coordinator** to request services to prepare or commit
Coordinator -> Service A : prepare 
	-> A response failed -> C send rollback to all services
	 -> all service responses success -> C sends commit to all services
```
POST /prepare
Body: { "transactionId": "tx-123", data: ... }
POST /commit
Body: { "transactionId": "tx-123" }
```

**Disadvantage**:
- **lock db**: all participants hold the locks on their data wait until request from Coordinator
	->  Deadlocks, timeouts, and low concurrency._
- **single of point failure** - when Coordinator die
- **Latency**