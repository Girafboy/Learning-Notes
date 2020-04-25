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
        - FIFO Ordering
        - Causal Ordering：所有人收到是满足因果关系的，但不一定全序
- 时序关系：