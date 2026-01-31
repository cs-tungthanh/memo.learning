---
tags:
  - Hash
  - consistent-hashing
---
## Rehashing Problem
- Initially, we have 4 servers and a hash function like `serverIndex = f(x)%4`
- What happen when we add one more server or remove some
	- we will have a number of key that need to go to a diff server rather than the old one
	- example `key=1 -> server 1` but after adding server 5 `key=1 is remapped to server 3`
	- => this cause the a large amount of data to move, event though only one server is added
=> Consistent hashing solves that problem
	- to minimize the number of key that is remapped 

# Idea
- we map servers based on IP/key/ID,... on the ring
- Then, for a given key, we hash it, place it on the ring and move clockwise until we reach a server => that server is responsible for that key

## Problem about unbalancing
- Imagine we have 3 servers
- S1 ----------- S2 ---- S3
- As you can see S1 and S2 is far away  -> S1 own huge range -> too much data
- => imbalanced, one server can be overloaded while others are almost idle
- Instead of putting each server on the ring **once**, we put it many time **(called virtual nodes)**
- S1_1 - S1_2 - S1_3 -S2_1 - S2_2 - S2_3 - S3_1 - S3_2 - S3_3
- We changed from **"one big range per server"** ==> **"many small ranges per server"**
- => that makes rebalancing smoother and safer


Example when add a new server 4

![](./assets/consistent-hashsing-add.png)


![](./assets/consistent-hashing-virtual-node.png)