
### IP

- IP
    - AB类地址网络ID太少不够用，C类地址网络ID太多，影响查询效率
    - CIDR任意切分更加灵活，缓解了地址不足的问题（LPM最长网络ID匹配）--子网掩码
    - ISP(电信\移动\联通):网络运营商,只负责分发给子组织,因此路由表并不会太大
    - DHCP: 动态分配IP
    - MTU: 在路由处拆分,终端重组,会导致过多fragmentation,一个丢失,全部无效
    - ICMP: 网络巡检员,为后续发包提供信息
        - ping
        - 判断能否到达
        - 流量控制
        - 超时

- IPv6
    - 没有checksum(耗资源,别人也做)和fragmentation,增加了QoS的功能,无状态,自动配置
    - 放弃MTU,慢慢增大包,遇到包太大的反馈,就减小包

- NAT
    - IP没有那么紧缺,并不是每个设备都需要直接暴露自己的IP,经常和防火墙共同存在
    - 中间翻译,外部访问内部需要配置映射

- Routing
    - Centralized: 在一个中心节点具备全图信息,分发结果
    - Link-state: 每个节点分别收集自己需要的全图信息
        - Flooding
        - LSDB
        - OSPF
        - 计算量大，早晚能收敛到达
        - 错误广播比较快
    - Distance-vector: 每个节点只与邻居交换信息, Bellman-Ford算法
        - good news travels fast
        - bad news travels slow
        - 解决环路问题:
            - Split Horizon: 不把信息发给接收过此信息的路由
            - Poison Reverse: 对split horizon的加强, 重置为inf
        - 收敛时间不确定
        - 错误广播比较慢
        - RIP
    - Routing Hierarchy

### UDP & TCP

- UDP
    - RPC
- TCP两头的工作
    - 存在Server和Client的概念，三次握手建立连接
    - 流量控制
    - 拥塞控制
        - 拥塞标志：
            - 1. 超时
            - 2. 丢包
    - AIMD
    - 计时器管理，Long-tail问题
    - T/TCP
- QoS中间路由器的工作
    - 目标：尽可能快地填满buffer，并且避免溢出
    - Key参数：吞吐、延迟、丢包率
    - RED(Random Early Discard)：
        - 发现潜在拥塞、假定大家遵循TCP、避免瞬时burst
        - 低于min thresh：丢包率为0
        - 介于min和max之间：丢包率线性增加
        - 高于max thresh：丢包率突增为1
    - Router的任务：
        - Rate limiting
        - Fair Queueing
    - Real-time
        - Soft-real-time：允许丢一些
        - Hard-real-time：不允许丢包，确定时延内必须到

### Crypto-Https