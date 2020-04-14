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