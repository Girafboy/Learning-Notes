# Introduction

- 传统计算机通过Interrupt对CPU资源分时复用
- 分布式系统要解决的问题：
    1. How to communicate
    2. How to store and manage data
    3. How to compute
    4. How to cooperate
- 从指令流和数据流角度分类：（S-Single, M-Multi, I-Instruction, D-Data）
    - SISD：传统计算机
    - SIMD：GPU
    - MISD：没用
    - MIMD：并行与分布式系统
        - 共享内存的：
        - 不共享内存的：
- 为什么有分布式？
    - 主频不能无限增加，面临能耗、散热等问题
    - 多核带来了并行计算，是一种微小的分布式系统
    - 互联网中大部分机器是分布且连接的，分布式已经成为常态
- 分布式系统是多个自主的计算机协同交流完成一个共同的目标
- 分布式的形式
    - 单机多处理器：
        1. 多个CPU对上层透明，使用单个OS
        2. 使用信号量通信
        3. 共享内存
    - 网络OS：
        1. 普通PC
        2. 通过网络互联
    - 分布式OS：
        1. 失败了
    - 中间件：
        1. 建立在OS以上，Application以下的软件
        2. 是一种抽象，解决了底层通信等问题
        - JDBC、ODBC、RPC、RMI
- 分布式的话题
    - Reliability
        - Availability： Reliability & Maintainability
        - Data Durability
    - Performance
    - Scalability
        - Distributable vs. Centralized algorithm
    - Failure happens all the time
- 计算服务模型
    - Centralized Model
    - Client-Server Model：当前4-tier
    - P2P Model
    - Cloud Computing Model

# RPC & Messaging

- Socket：是一个双向通信的，基于TCP/IP的CS模式
- RPC：远程过程调用，一种中间件，屏蔽了网络通信的细节，像普通函数一样调用
    - 单机下联合编译，分布式情况下无法确定这个函数是否存在
    - Stub：模拟了另一方的存在，提供接口但不实现功能，需要提供消息传递的功能
    - RPC位于第5/6层：Session（连接保持）/ Presentation（数据表示）
    - 挑战：
        1. 参数传递
            - Pass by value：直接copy
            - Pass by reference：把复杂数据结构变成特有结构，发送，Unmarshal，组织本地引用
        2. 数据表示：使用中间格式传输（JSON、XML）
            - Byte ordering
            - Size of integers...
            - Floating point
            - Character sets
            - Alignment requirements
        3. Where to bind：中心化（中心DB，Naming Service），分布式（各自维护）
        4. When Things Go Wrong：透明性被打破，语义不再是一次，可选至少一次（Idempotent）/至多一次
        5. 性能：慢
        6. 安全
    - 使用RPC编程：
        - 使用separate compiler来生成stubs
        - Client：初始化、传输类型、本地Server
        - Server：通常不需要修改
- Message Middleware
    - 同步/异步
    - 短暂/持久
    - Socket
    - MOM
    - Protocols
        - XMPP：
        - STOMP：
        - AMQP:
    - 用处:
        1. 异步处理
        2. 流量削峰
        3. 解耦
        4. 用作buffer,顺序保证,临时存储,流数据处理等
    - 通讯模式
        - Point to Point
        - Pub/Sub
    - 产品: ActiveMQ,RabbitMQ,RocketMQ,kafka

# Failure & Fault-tolerant
- 在分布式场景下错误随时会发生
- Availability
- Reliability
- Safely
- Maintainability
- 复制:高可用性,高性能,低延迟
    - 主从关系,一致性问题,错误处理,原子性
    - Primary-Backup:主设备干活,从设备就等着,心跳检查
    - Mastar-Slave:master作为协调者,不干活(mastar挂了会有单点故障)
- 恢复:
    - Backward recovery:回滚(checkpoint)
    - Forward recovery:不太容易
    - Logging
- 分布式事务
    - Two-phase Commit：都做或都不做
        - Phase 1：Voting，对于Coordinator，只要有人没回，就abort
        - Phase 2：Committing，对于Participate，只要没收到消息，就等着或者问问别人
    - 3PC：预提交阶段超时自动提交，如果错误提交可以回滚。解决了超时等待的问题，但是没有解决一致性问题。引入了多次提交，很少用。

# Time and Clock

- 逻辑时钟：是一个软件的单调递增的计数器，和物理时间没有关系
    - Lamport's Logical Time：
        - Receive event = max{Local event, Send event}
    - Vector Clocks：
        - 继承其他进程的向量值，自己的向量值+1，与本地比较可以检出问题
    - Message Delivery：
        - Total Ordering：所有人收到的顺序是一样的，但不一定满足因果关系
        - Causal Ordering：所有人收到是满足因果关系的，但不一定全序
        - FIFO Ordering：别人收到某个人的消息序，一定是按照它发送的顺序
- 物理时钟
    - 石英钟：32768Hz，每月差2秒
    - 原子钟：每六百万年差1秒
    - UTC：统一协调时间，原子钟，传播到各地
    - CMOS：计算机内的时钟，每秒产生60/100次中断
- 时钟同步
    - 直接设置收到的时间可能导致时光倒流=>慢的调快，快的调慢
    - 网络延时
        - Cristian's Algorithm：假设Server是准的，别人跟它同步，$T_{server}+(T_1-T_0)/2$
        - Berkeley Algorithm：只要几个人的时间一致就好了，没必要和UTC一致，选出一个master接收所有的时间求平均
    - NTP:多层

# Consistency:对访问的一种承诺

- 通过降低对一致性的约束,以获得更好的性能,可靠性等
- 一致性模型没有好坏之分,只有适不适合
- Data-Centric Consistency Model
    - Strict consistency:绝对时间意义上的顺序保证,不可能的
    - Sequential consistency:所有进程看到的顺序相同
    - Causal consistency:满足因果关系
    - FIFO consistency:别人看到和某个人的顺序一致
    - Relaxed consistency:都是使用同步变量来放松了一致性要求
        - weak:某个时间节点一致(同步后),不是都一致
        - release:类似加锁的方式
            - eager=push,release的时候通知大家
            - lazy=pull,acquire的时候问别人
        - entry:加锁粒度减小到变量
- Eventual Consistency:不保证信息立刻传播到全网,但最终会一致

# Large Scale

- 4 layer：Browser WebServer APPServer DBServer
- Cluster: 集群
- CDN：内容分发网络

- 解决大问题：1.分布（分发给很多人）2.并行（大家一起做）
    - 巨型机
    - 数据中心（Cluster）
- Large-Scale Cluster 的原因
    - 需求在增长，越来越多的用户、基础架构、操作经验
    - 大数据中心更经济
    - 网络越来越快
    - 软件栈的商品化、标准化

- 问题
    - 硬件不可靠，出错频繁
    - master单点故障
    - master成为瓶颈（内存、通信）
    - 硬盘访问成为瓶颈
    - 
    - 软件：分布式文件系统（扩展性、容错），数据库，编程，运行时环境
    - 维护：系统级别、应用级别、资源消耗

- 可能的方案
    - 高可靠的服务器是不可能的
    - 加强监控和管理
    - master多配置一些资源，多个master，应用级别的内存管理（限制配额）
    - 大规模文件系统，多点存储，备份slave
    - 管理粒度tradeoff
    - 规模调整，监控哪些，基于历史数据的调整（机器学习、数据挖掘）
    - 直接关掉

# Map/Reduce

- Google三大法宝：GFS，MapReduce，BigTable

- 解决的问题：
    1. 资源管理
    2. 任务监控
    3. 处理中间结果
    4. 处理错误
    5. 协调节点和任务
- Hadoop：对上提供简单的编程模型，对下有效管理cluster。可扩展，检测和处理错误
- HDFS+MapReduce+Zookeeper+Hbase
- HDFS+YARN（资源管理）+MapReduce/Tez/Spark+Zookeeper+Hbase
- GFS-HDFS
- BigTable-Hbase
- MapReduce-Hadoop MapReduce

- MapReduce：函数式编程（LISP，ML，Erlang）
    - Map：在list中每个上做同样的事
    - Reduce：结合所有的结果

- Bandwidth优化
    - 利用本地化
    - Combiner函数：先做一点Reduce
- Skew问题
    - 先Map完的先Reduce
- 依赖问题
    - 排序

# DFS

- 分布式文件系统，区别主要在于容错（系统可用性、数据完整性）
- 访问远程文件：
    - FTP：文件传输协议，显式的远程访问
    - NFS：网络文件系统，基于RPC，具有一定程度的透明性，仍无法解决大文件存储
- GFS
    - 应用特点：
        1. 错误普遍存在
        2. 文件非常巨大
        3. 需要不断追加新的数据
        4. 读远多于写
        5. 应用与文件系统API共同设计可以增加整个系统的灵活性和可用性
    - 是一个为Google专门设计的可扩展分布式文件系统
        - 大文件
        - 大规模cluster（容错、可用）
        - 对Google应用友好
    - Single Master：最小化负载，固定chunk大小，预测下一个可能访问的块一起返回
    - Chunk Size：64M，减少master元数据量和交互次数，Tradeoff
    - Metadata：驻留在内存，master操作快，扫描高效（垃圾回收、融合、负载均衡），
    - 系统交互：master临时选一个Primary Replica负责协调三个备份的一致性，原子性at-least-once追加，Snapshot（COW，只复制metadata）
    - 命名空间：映射路径到metadata，按顺序拿锁（避免死锁、允许并发）
    - 容错：
        - monitor->detect->recover
        - 高可用：
            1. 消除单点故障
            2. 可靠的交叉
            3. 检错
        - 数据完整性
    - Master Replica：
        - Operation Log
            - 逻辑时钟跟踪
            - 恢复master
            - 持久化存储和远端存储
            - 周期性checkpoint
            - 先写log再做更新
        - 进程死了：立即重启
        - 机器死了：在其他地方重启，恢复
        - Shadow Master：只读访问，不是mirror，稍微差一点
    - Disk Failure：
        - Checksum
        - 日志
        - 返回结果前验证
        - 空闲时检查
    - 错误发现：心跳检查

# Distributed Database

- 分布式文件系统仍然很基础，数据和应用耦合太紧，仍需要分布式数据库提供数据的存储、访问和控制
- 传统数据库
    - 关系型数据库，驻留在磁盘
    - ACID
    - 事务
    - 高延迟
- NoSQL
    - key-value：Redis
    - 基于列：Cassand，HBase
    - 基于Document：MongoDB
    - 基于图：Neo4J，InfoGrid
- CAP
- BASE
- BigTable:
    - （行，列，时间戳）到内容的映射表
    - 数据访问极其简单，没有关系型操作
    - 原子更新只在行级别支持
    - 每行可以有任意列
    - 每列可以是任意类型
    - 切分成多个tablet：每个tablet是一个或多个SSTable文件，SSTable是<key-value>map，tablet太大后切分成两个新tablet
    - 一个master server仅与多个tablet server交流
    - Chubby锁服务持有metadata，并处理master选举

- Data Partition
    - 本地化、高性能和高吞吐，与具体场景相关
    - 切分原则：
        - Range partitioning：按范围切分
        - List partitioning：按意义分类切分
        - Hash partitioning：按哈希切分
        - Composite partitioning：以上三种的组合
    - 切分方法：
        - Viertical partitioning
        - Horizontal partitioning
        - Hybird partitioning
    - 数据库存储
        - 面向行：所有数据的同一个字段在一起
        - 面向列：一个数据的所有字段在一起

- Cassandra
    - Facebook，Digg，Twitter
    - Consistent hashing partition
    - 问题：分布不均匀：虚拟节点
    - Partitioners：Murmur3，Random，ByteOrdered

# Zookeeper&Paxos

- Zookeeper：文件系统+通知机制
    - 配置管理：通知监控
    - Cluster管理：活着没有，运行状态，master选举
    - 分布式锁服务：独占锁、顺序锁
    - 队列管理：同步队列、FIFO队列
    - 命名服务：名字唯一性（惯例：符号分割的复合名字），选AP
    - 不适用于可拓展的写

- Paxos
    - 唯一已知的解决一致性的算法
    - Phase1
        - 
    - Phase2

# 图计算

- 图的应用场景：规模巨大
    - 产品推荐
    - PageRank
- Table View：Hadoop，Spark
- Graph View：Pregel，GraphLab
- 困难
    - 图依赖在MapReduce中的表达
    - 迭代计算在MapReduce中很难
- Pregel
    - 由BSP模型启发，以节点为中心的编程
    - SuperStep = Compute + Communicate + Synchronous
    - 每个节点收消息，执行预定义的函数，修改值，发消息给邻居（下一个SuperStep）
    - 图切分：按边切分，会产生ghost节点
    - BSP模型的问题：
        - 每个阶段的每个点都要检查邻居有没有变化；改进为一个节点变化后通知邻居节点
        - 性能受到最慢的机器限制，要等所有人做完一个SuperStep
- GraphLab
    - 基于图的表达
        - 边和点都存在
        - 图切分：按边切分，和pregel一样
    - 用户执行代码
        - 一个节点的scope是他的相邻节点和边
        - 只需要对节点的scope进行编程
        - 采用异步的通知方式，对pregel进行了改进
    - 一致性模型
        - Full consistency model：整个scope可以并行
        - Edge consistency model：相邻的边，不包括相邻节点，可以并行
        - Vertex consistency model：只对节点本身可以并行
    - 实现一致性
        - 图着色
            - 相邻节点颜色不一样-边一致性
        - 分布式锁
            - 点一致性：每个节点加写锁
            - 边一致性：中心节点加写锁，邻居加读锁
            - 全一致性：给整个scope一个写锁
    - 可序列化
- Natural Graph：符合幂律分布，长尾效应，切边不均匀，最好切点
- PowerGraph：GAS模型，由切边变成切点

# Deep Learning
- 数据科学：从收集数据、描述、发现、预测到建议
- 激活函数的目的是引入非线性