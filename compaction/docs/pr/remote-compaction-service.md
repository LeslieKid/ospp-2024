# (pr) feat: add compaction server supporting remote compaction service

## Implementation

### 1. feat: enable the deployment of compaction server with compaction rpc service.

通过引入在configuration中引入`ClusterType`，将原本的HoraeDB in cluster mode区分为`HoraeDB`和`CompactionServer`，用户能够通过`server.toml`作用于`ClusterType`值：

- `horaedb::Config` --> `ClusterDeployment` --> `WithMeta(ClusterConfig)` --> `ClusterType::CompactionServer`

`ClusterType`将在以下场景中起作用：

1. 在`WithMeta`部署场景下，对HoraeDB和CompactionServer的区分，此处二者的区别主要在于`RpcServices`，HoraeDB特有提供meta service，CompactionServer特有提供compaction service（未来实现可以拆得更细一些让cs更“轻”？）。该部分代码逻辑关系如下：
   - `build_with_meta()` --> `server::Builder::build()` --> `RpcServices` --> `grpc::Builder::build()` -->  `ClusterType::CompactionServer` --> **`CompactionServiceServer`**
2. 在cluster mode下，HoraeDB / CompactionServer通过用meta client来传递`ClusterType`信息来让HoraeMeta区分二者。此处只需要在meta client中的`NodeMetaInfo`中加入`ClusterType`即可，该信息会通过heartbeat传输给HoraeMeta进行处理。*（ps：这部分代码放在另一个pr中）*

在上述场景1中，有关`CompactionServiceServer`的实现涉及到了remote compaction操作的核心，其中的关键是`CompactionService::execute_compaction_task()`。*（ps：`FilePurgeQueue`的类型转化可能需要修改...）*

*todo list :*

- [ ] *feat: enable to deploy the compaction server.*
  - [ ] *完善[HoraeDB Cluster Deployment Doc](https://horaedb.apache.org/cluster_deployment/with_meta.html#run-horaedb-cluster-with-horaemeta)*
  - [ ] *在HoraeDB中添加`docs/example-cluster-3.toml`来展示如何部署compaction server*

### 2. feat: introduce compaction client for HoraeDB to access remote compaction node.

在`Cluster`特征中加上`compact()`，该函数逻辑为：

- （待实现）向HoraeMeta申请cs结点
- 通过client实现compaction offload

- [ ] *todo: client实现部分也涉及type conversation，计划在实现remote compaction runner时完成*

