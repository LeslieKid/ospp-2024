# Compaction Server Implementation

## 1. 已基本实现

### 1.1 Compaction rpc service implementation

- [x] 实现`CompactionServiceImpl`，提供CompactionServer上的rpc service，关键函数实现为`execute_compaction_task()`
- [ ] 对`FilePurgeQueue`的处理：不通过protobuf（从HoraeDB传到CS），直接在CS上完成File Purge？

### 1.2 Introduce `CompactionCluster`

`CompactionCluster`实现了在cluster mode下CompactionServer提供的功能：

- [ ] Consider add `Builder` for `CompactionCluster`

- [x] `compact()`：处理HoraeDB传来的remote compaction task
- [ ] `start()/stop()...`：CompactionServer和HoraeMeta之间的交互（e.g. heartbeat）

CS代码struct层级如图（有点怪...）![CSCodeArch](/home/leslie/Project/contributor/ospp-2024/compaction/imgs/CSCodeArch.png)

### 1.3 `CompactionClient` implementation

`CompactionClient`用于帮助HoraeDB完成Compaction Offload

- [ ] 借助CompactionClient为`Cluster`实现`compact()`

## 2. 待实现

### 2.1 区分Horaedb和CS的部署

- [x] `ClusterDeployment`类型增加，提供CS的部署
- [ ] **关键**：`setup.rs`文件中`compaction_server_build()`的实现，*考虑相似函数的rename*
- [ ] 实现CS的`config`以及`horaedb-server.rs`中`config`初始化 --> `parse_toml_from_path()`
- [x] Introduce `CompactionClusterConfig`
- [x] `config.cluster_deployment`的pattern match

### 2.2 如何引入及判断是否引入`CompactionClient`



