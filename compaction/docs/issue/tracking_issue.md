# Tracking issue for compaction offloading 

## Describe This Problem

We found in production that the speed of sst compaction is unable to keep up with the speed of sst generation, leading to poor query performance. However we are unable give more resource to compaction to solve the problem because query/write is more important than compaction in the same node.

It is really hard to do a trade-off about resource allocation among query, write and compaction in lsm model. We want to compact the generated small ssts as fast as possible, but we can't tolerate its influence to query/write. And finally I think offload the compaction to the seperated nodes may be the key for it.

## Proposal

**The following is the architecture for compaction offloading.**

![Architecture.png](https://s2.loli.net/2024/07/17/ivH4E2MJq8pZbnQ.png)

To support compaction offloading, we need:

- **Compaction server supporting remote compaction service (Execute Plane)** 
  - [ ] Introduce runnable compaction server and expose the api  for HoraeDB node to ask for remote compaction service
- **HoraeMeta supports managing the compaction servers (Control Plane)**
  - [ ] Impl the ability to manage the compaction servers and collect their status (Monitor)
  - [ ] Impl scheduling algorithm for load balance and expose the api for HoraeDB node to get the proper remote compaction node (Scheduler)
- **HoraeDB node supports submitting the compaction task to remote**
  - [ ] Impl remote mode compactor based on the provided compaction service and HoraeMeta service above

## Additional Information

 This issue replaces issue #1480. Please close issue #1480 as it is outdated.

