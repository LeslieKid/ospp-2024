# Remote Compaction Service

## 1. Introduction

**[Tracking issue for compaction offload](https://github.com/apache/horaedb/issues/1480)**

We found in production that the speed of sst compaction is unable to keep up with the speed of sst generation, leading to poor query performance. However we are unable give more resource to compaction to solve the problem because query/write is more important than compaction in the same node.
It is really hard to do a trade-off about resource allocation among query, write and compaction in lsm model. We want to compact the generated small ssts as fast as possible, but we can't tolerate its influence to query/write. And finally I think offload the compaction to the seperated nodes may be the key for it.

**The following is global architecture.**

![ArchDesign](/home/leslie/Project/contributor/ospp-2024/compaction/imgs/ArchDesign.png)

## 2. Design Overview

This part is about **implementing the special node (compaction server) supporting remote compaction service**, which shoulder the most workload of compact operation.

### 2.1 Compaction Procedure

The first step of compaction is *triggering compaciton*. It includes automatic triggers and manual trigger.

The second step is *selecting files for compaction*.

The third step is *reading data*. The selected files are read into memory.

The forth step is *merging data (merge sort) & garbage collection (GC)*. This part contains the main logic and most CPU workload of compaction operation. The data is merged and sorted by key. Duplicate keys are resolved according to the database's rules, e.g. keeping lastest version. And then tombstones (markers for deleted keys) and expired data are removed.

The fifth step is *writing compacted data*. The merged and cleaned data is written to new files. These new files are typically larger and fewer in number compared to the original fragmented files.

The last step is *updating metadata*. The database's metadata (store in manifest) is updated to reflect the new file organization. This involves updating indexes and pointers to the new files. And the old, now obsolete, files are marked for deletion or immediately deleted to free up space.

### 2.2 Data Flow

![CompactionServer](/home/leslie/Project/contributor/ospp-2024/compaction/imgs/CompactionServer.png)

*Q: Should we offload the second step (selecting files for compaction) to the compaction server?*

*Q: How to add priority queue into the compaction server?*

### 2.3 Data Structure



