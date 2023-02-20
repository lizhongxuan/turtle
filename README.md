# turtle
#### IO存储栈
![IO stack](./images/io_stack.png)
- 系统调用
- 虚拟文件系统VFS
- 文件系统
- 块设备
- 设备驱动

#### linux IO 存储栈分为7层
- VFS 虚拟文件层: 
   - 在各个具体的文件系统上建立一个抽象层，屏蔽不同文件系统的差异。
- PageCache 层:
   - 为了缓解内核与磁盘速度的巨大差异。
- 映射层 Mapping Layer:
   - 内核必须从块设备上读取数据，Mapping layer 要确定在物理设备上的位置。
- 通用块层: 
   - 通用块层处理来自系统其他组件发出的块设备请求。包含了块设备操作的一些通用函数和数据结构。
- IO 调度层： 
   - IO 调度层主要是为了减少磁盘IO 的次数，增大磁盘整体的吞吐量，队列中多个bio 进行排序和合并。
- 块设备驱动层: 
   - 每一类设备都有其驱动程序，负责设备的读写。
- 物理设备层: 
   - 物理设备层有 HDD,SSD，Nvme 等磁盘设备。



#### 网络存储
- Ceph
- Minio
- JuiceFS

#### CSI
- CSI接口
- TopoLVM

#### curve架构
![curve architecture](./images/curve_stack.jpeg)

#### curvefs架构
![curvefs architecture](./images/curvefs_architecture.png)
CurveFS由三个部分组成：
1. `curve-fuse` 是基于 fuse 的文件系统客户端。
   - 接收并处理 fuse 的请求，与fs-meta cluster 交互实现对元数据的增删改查，与 fs-data cluster 交互实现对数据的增删改查；
   - 提供元数据缓存和数据缓存以提高性能；
   - 用户可以通过客户端访问不同的文件系统实例。
2. `fs-meta cluster` 是 CurveFS 的元数据服务集群。
   - 它的架构类似 CurveBS，由 MDS 和 Metaserver 两个部分组成，高可扩、高可用；
   - mds 用于管理集群拓扑、集群调度、文件系统实例、文件元数据分片管理；基于 etcd 存储集群拓扑、用户和文件系统信息；基于 etcd 实现 mds 的高可用。
   - metaserver 用于存储文件的元数据( inode 和 dentry )，通过 multi-raft 实现高可用和高可靠。每个 raft 复制组管理多组元数据分片。
3. `fs-data cluster` 是 CurveFS 的数据服务集群。
   - 当前支持S3标准接口的对象存储以及 CurveBS；
   - 用户可以根据性能场景灵活配置；
   - 也因为支持多种存储集群，CurveFS 的元数据使用独立的集群统一管理。
