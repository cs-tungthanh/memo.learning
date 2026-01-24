---
tags:
  - LSM
  - B-Tree
  - SSTable
  - Memtable
---
LSM: **Log-structured Merge**
LSM is usually used in No-SQL to support **FAST WRITE**

Ref: 
https://www.youtube.com/watch?v=I6jB0nM9SKU
https://vivekbansal.substack.com/p/what-is-lsm-tree
# Why not using B-Tree
B-Tree is perfect for balance read and write optimization O(log n)
But when data is grow, db is bottleneck because **Write amplification**, **need a lot random I/O**
- Amplification means: 1 operation need n operations to complete

Updating on B-Tree can be relatively expensive as it involved random I/O and might include updating multiple pages on disk
-> this limits how fast the B-Tree can ingest data

# What is LSM tree
LSM is optimized for **fast WRITE**
- because it don't take lot of random I/O with immutable SSTable
An LSM Tree consists of 2 components: 
- **Memtable** is on memory
- **SSTables** is on disk

**Memtable** is a data structure that hold data on memory **before it's flushed to disk** (SSTables)
- usually implemented by **Balanced binary search tree**
**SSTables** is a **Sorted String tables**
- contains a lot of number of <key, value> pairs **sorted by key**
- they are permanently persisted on the disk and immutable - cannot be modified
- Every flush creates a new SSTable.
	- Problem: too many tables are created -> slow read and waste time to read stale data
	- need to compaction data on SSTables

Flow:
- Incoming Write --update--> Memtable
- when a Memtable reaches to a certain size it is sorted and flushed to the disk **as an immutable SSTable**
	- update on an existing key don't override an SSTable (because it is immutable)
- **new SSTables are added to an existing level**
- when that level exceeds its size limit (threshold), **compaction pushes data to the next level**

![](./assets/LSM-flow.png)

![](LSM-compaction.png)
### B-Tree (RDS / MySQL / PostgreSQL)
- One main on-disk tree
- Updates happen **in place**
- Deletes physically remove or mark pages
- Random I/O is common
- Optimized for **reads + transactions**
 ```
Disk B-Tree
	|- page
	|- page
	|- ....
 ```
### LSM Tree (Cassandra / RocksDB / LevelDB)
- Writes go to:
    1. WAL
    2. MemTable (in memory)
- Disk data is **immutable**
	- for example when delete a key on Memtable - it don't actually remove this key on it, it's replace it with tombstones (means as deleted)
- Deletes are **tombstones**
- Cleanup happens during compaction
- Optimized for **high write throughput**
```
Memory
 └── MemTable (ordered)

Disk
 ├── SSTable (L0)
 ├── SSTable (L1)
 ├── SSTable (L2)
```

## Key insight
- LSM trees avoid in-place updates on disk  - using tombstones and compaction
- B-Trees update disk pages directly - update IN-PLACE


#  What is Random I/O
> **Random I/O occurs when a database must read or write multiple disk pages located at non-contiguous positions on disk.**

when we update a row in DB with B+ Tree
- the db traverse the tree and find leaf node at key = target
- it modifies that page **in place**
	- if the page is full -> it splits the page -> update parent pointers -> possibly cascade upward
	- so update a page can make a lot of updating the middle nodes due to the the page is split and make key change 
	- -> that is **write amplification**
- Each step may touch **different disk pages**. and these page are not next to each other
- -> that is random I/O

Each insert or update may touch multiple disk pages located in different positions, which leads to random disk access. This is expensive compared to the sequential writes used by LSM trees.

Why LSM is better for that because it works with immutable SSTables, only write data when Memtable reach threshold and is flushed to SSTables -> it is not happen random I/O
but the compaction in SSTables cause a lot of I/O because it merge SSTables
- Compaction causes a lot of I/O, but it is mostly sequential, not random.

## Why merge is mostly sequential I/O
This is the key connection to your earlier question.
During compaction:
- SSTables are read **from start to end**
- Merge is like merge-sort
- Output SSTable is written **sequentially**
So:
- High I/O volume ✅
- Random I/O ❌ (mostly)

# Perf about range query
LSM trees can support range queries efficiently because SSTables are sorted and written sequentially. After compaction, lower levels have non-overlapping key ranges, so range scans touch only a few files. However, range queries are less efficient in Level 0 due to overlapping SSTables and multiple versions.

|Aspect|B-Tree|LSM Tree|
|---|---|---|
|Data order|Sorted in one tree|Sorted in many files|
|Range scan|Very good|Good after compaction|
|Random reads|Good|Worse|
|Write-heavy workload|Worse|Much better|
## When LSM is a good choice for range queries
LSM works well when:
- write-heavy workload
- large sequential scans
- eventual consistency is acceptable
- compaction is well tuned
Examples:
- time-series databases
- log analytics
- metrics storage
