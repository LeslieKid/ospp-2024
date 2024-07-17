# Tracking issue for protobuf of compaction offload

## Describe This Problem

I am working on implementing compaction offloading for [HoraeDB](https://github.com/apache/horaedb). And I found that it's necessary to extend the current protocol buffers with additional data structures and services required for compaction offloading.

## Proposal

To satisfy the requirement for compaction offloading, we need:

- [ ] Remote compaction service provided by compaction node for horaedb node
- [ ] Server status service provided by compaction node for horaemeta node
- [ ] Compaction node distribution service provided by horaemeta node for horaedb node