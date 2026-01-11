---
tags:
  - B-Tree
---

### Glossary
- d: the minimum degree of the tree

# B-Tree
**B-Tree** is self-balancing tree data that maintains sorted data and allow for efficient insert, delete, search operation.
All those operations can be performed in **O(log N)** time.
### Features
- All leaves are at the same level -> **balanced**
- All internal nodes (except for root) 
	- have the number of children ranging from **d -> 2d**
	- have at least 2 children
- A non-leaf node with **k children** contain **k-1 keys**.
	- This means if a node has three children (k=3), it will hold two keys (k-1) that segment the data into three parts corresponding to each child node.
	- Example: Node: key: [10, 20, 30] -> 4 children
		- [<10] - [10, 20] - [20, 30] - [>30]

# B+ Tree
- B+ Tree is a variant of B-Tree
- It is wisely used in disk-based storage systems.
### Features
- Data Pointers are stored only in leaf nodes.
	- The internal nodes only contain keys and pointers to other nodes.
- All leaf nodes are linked together in linked list.
- In B+ tree, every key appears twice: once in internal node + once in leaf node
- Lookup requires a path from root -> a leaf
	- All data access operations take a consistent time.
 ![](B+Tree.png)

# Compare B Tree and B+ Tree
## Overview lookup process
- Both the data and the B+ tree index are stored on disk. When we query, the database loads only the relevant index blocks into memory, searches sequentially along the path, and once it finds the record reference, it fetches the actual record from disk. Each B+ tree node is stored in a disk block, so we never need to read the entire index
## Which B+ tree over B tree?
- In B+ Tree, only **leaf node** store **the data**, internal nodes only store **keys**
- -> Internal nodes have **more spaces** to store **more keys** => **Reduce disk read** operation
- Make Tree **shallower**
- All **leaf nodes** are **linked together** -> range query **will be much faster**
## Under the hood
- every read operation in OS is read by page
	- 1 page can be 4KB, 8KB,...
- so DB does NOT read individual nodes -> they read a whole page -> one page can contain multiple nodes and keys
- One disk read a page -> loads MANY keys at once. -> One I/O operation = lots of useful data to find

