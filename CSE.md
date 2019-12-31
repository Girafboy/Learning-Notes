# Transaction：保证了atomicity和isolation
> ### CAP Theory
- 以下三点不可同时满足：
    1. Consistency：所有节点同时看到同样的数据
    2. Availability：每个请求均响应
    3. Partition Tolerance：可以容忍信息丢失和部分系统故障
- 两种选择：
    1. AP：保证用户体验，不一致没关系，e.g.买书
    2. CP：保证数据一致，拒绝用户访问，e.g.火车票、金融
> ### All-or-nothing：要么都做，要么不做
- Commit Point：在此之前的操作都能回滚，在此之后的操作都能恢复
- Shadow Copy：写数据备份，并原子地替换旧数据，即对当前数据的更改只写一次。依赖于底层硬盘小数据块的All-or-nothing。
    1. Rename 之前保持了旧文件，之后持有了新文件，只需保证Rename操作的原子性
    2. *Rename Solustion1：* 更新inode =》 增加new_refcount =》 减少old_refcount =》 删除临时文件 =》 减少new_refcount
        *Problem：* No commit point！两个名字指向一个inode，但refcount=1，不知道删除哪一个
    3. *Rename Solustion2：* 先增加refcount，再更新inode
        *Correctness：* commit point 是更新inode，如果发现refcount多了，减少计数即可
    4. Recovery after crash：如果发生在commitpoint之前，new_refcount多了；如果之后，old_refcount多了。此外，都需要删除临时文件。
    5. Pros：适合于单个文件
    6. Cons：不能拓展到多个文件或者目录；任何小改动需要复制整个文件；一次只能做一个操作；仅适合于单机单磁盘
- Logging：
    1. Log-only Approach：
       - Begin：分配一个Transaction ID
       - Write Variable：写Log
       - Read Variable：读Log
       - Commit：写commit point
       - Abort：可以写abort记录，也可以不写

        *Problem：* 写性能好，顺序写，新旧值都写，两倍开销；读性能差，每次读扫描log；瞬间恢复，什么都不需做。
    2. Cell Storage，提供快速的读，使用Write-ahead-log Protocol（WAL），log一定是最先写的。当log和install过程中发生crash，log作为权威可以恢复cell storage
        *Problem：* 写还OK，log&install做了两遍；读很快，直接查cell storage；恢复需要扫描整个log。
    3. Optimization 1：在install时使用cache，加速了读，变成一次写
        *Problem：* 原子性问题，在flush之前，cache比cell storage新
        *Solution：* 恢复过程中除了undo以外，还要增加redo操作
    4. Optimization 2：增加checkpoint，截短log
        - 停止接收事务
        - 等待所有事务commit/abort，并且写入log
        - flush log to disk
        - 写CKPT，再次flush
        - 恢复接收事务
      
        *Problem：* 在checkpoint过程中必须停止系统
        *Solution：* Non-quiescent checkpointing，《START CKPT（T1……TK）》，Do other transaction，《END CKPT》。
    5. Optimization 3：External Synchronous I/O，因为sync过程很慢，所以直到看的时候再flush
> ### Before-or-after：看起来好像是先后发生一样
- Race Condition：包含共享状态的，依赖时序调度的间歇性bug，难以确保一定不会发生
- Serializability
    - Final-state serializability：最终写入的状态等价于某种串行执行
    - View serializability：最终写入的状态和中间读的状态都等价于某种串行执行。NP-hard
    - Conflict serializability：一个事务写，另一个事务写或读同一个变量，就会有冲突。这个冲突的顺序等价于某种串行执行。易于用2PL生成
        - Acyclic Conflict Graph <=> Conflict Serializable
  
    *Relationship：* Final-state > View > Conflict
- 2PL（Two-phase Locking）-Pessimistic
    - Global Lock：每次只能运行一个事务
        *Problem：* 太慢了
    - Simple Locking：一次性为所有共享变量拿锁，commit/abort之后放锁
        *Problem：* 不知道有多少潜在的共享变量
    - 每个变量一把锁，操作变量前为它拿锁，一旦放锁不能再拿锁
    
    *Optimization：* Read-write Locks，多人同时读，一人写，写的人可能会无限等下去
    *Problem：* 可能发生死锁
    *Solution：* 1.对锁全局排序，2.直接abort某个事务（optimistic！）
- OCC（Optimistic Concurrency Control）-Optimistic
    1. 并发独立执行，持有私有的read set和write set
    2. 验证read set中是否有内容被其他事务修改
    3. 如果验证失败abort，如果验证成功commit
    
    *Advantages：* 高吞吐量和可伸缩性，假设低冲突（可接受0.1-0.3 aborts/transaction）
    *Problem：* 会导致事务错误地被abort

> ### Lock
- Memory Consistency Model：
    1. Strict Consistency：读总是返回最近一次写的值
    2. Sequential Consistency：多处理器读到的顺序是一致的
        Cache Coherence：单处理器读到的顺序是合理的
    3. Processor Consistency：不同处理器看到的写的顺序可能是不一致的（传输延迟）
    
    Strict>Sequential>Processor
- Atomic Instructions
    1. Test-and-set
    2. Compare-and-swap
    3. Load-linked and Store-conditional
    4. Fetch-and-add
> ### Distributed Transaction：
- 2-phase Commit：-CAP在保证了A的同时保证了C
    - 在不可靠的网络环境中实现了事务的原子性
    - 先准备，收到回复准备好了，再commit
    - Commit Point：收到所有的回复准备好了，在此之前可以都abort，在此之后可以重新commit

# Consistency
> ### Consistency across multiple machines
- Optimistic Replication：允许不一致，发现了再修复
    *Problem:* 当两台机器上的文件不一致时，怎么知道哪个是最新的？
    - Time：使用mtime时间戳最大的那个文件
        *Problem:* 只有一个文件被修改时是可行的，如果两个文件都被修改就会丢掉一份数据
        *Solution：* 每台机器跟踪一个最后reconcile时间，只要从此开始有文件被修改了，就需要更新。需要merge，conflict的话询问用户。
        *Problem：* 当一个文件的修改已经覆盖了其他文件的所有更改，此时可以直接用新版本覆盖，但只维护一个时间戳并不能识别这种情况
    - 时间是不可靠的
        - Time Measuring：由电池供电的RTC（Real-Time Clock chip）有固定频率的晶体振荡器计数来计算时间间隔，但晶振频率受到年限、温度等影响，需要网络校准时间
        - Clock Synchronizing：NTP（网络时间协议）需要考虑网络延迟，简单起见认为是RTT/2；出现误差时简单修改时间可能造成时间倒流或时间线混乱，因此采用加快/减慢本地时钟的方式，逐渐修正误差。
        - Improving Time Precision：借助每次网络校正时间的反馈修正自己的频率，例如使用PLL。
    - Vector Timestamps：每台机器都为文件记录了各机器下的修改时间，如果机器A认为的各机器版本都晚于B，那么A就可以覆盖B。
        *Pros：* 不需要和其他机器同步时钟，每台机器可以独立记录版本
- Pessimistic Replication：必须保证一致，当不一致会造成很严重后果时使用这种
    - Tradeoff：强一致性意味着低可用性
    - 看起来就好像只copy了一次
    - Quorum：定义了$Q_r+Q_w>N_{replicas}$，可以确保读到的人中至少会覆盖到正确的写过的人
    *Problem：* 用户请求到达Server的顺序可能是不一样的，怎么保证一致性呢？
    - RSM（Replicated status machine）
        - General Idea：每个Server都以相同的初始状态开始，按相同顺序收到同样的输入，并确保所有操作是确定性的，从而达到相同的终态。
        - Primary/Backup Model：主Server向Coordinator确认之前，确保先发给backup，然后选一个主从都认可的操作顺序，并确定非定值。
            *Problem：* 当有多个Coordinator使用不同的主Server时，就会发生不一致
        - View Server：维护一张主从设备表，告诉Server是Primary/Backup并监听其是否存活，Coordinator联系View Server谁是Primary，然后再发送请求
            - Rules：
                1. Server在知道自己是Primary之前拒绝一切请求
                2. Primary必须等待Backup接受自己的请求
                3. Primary必须拒绝之前primary发的请求
                4. 在新View下的Primary在上一View中必须已经是Primary/Backup
            - commit point：从Server接收到新的View
    - PAXOS：CAP-在保证P的同时保证了A
        - commit point：大多数人同意接受一个值
        1. 告诉proposer（leader）一个event
        2. leader用见过最大的数来要求accepters接受自己的提议
        3. 当大多数人同意接受一个提议后，event被发给learner
        4. learner执行event
> ### Consistency across decentralized machines
- Centralized Infrastructure：中心节点宕机；高维护费用；不适用于无中介情景；缺少聚合客户的能力
- P2P（Peer-to-peer）
    - BT（BitTorrent）：
        - Tracker：跟踪每个peer有什么
        - Seeder：持有完整文件
        - Peer：当持有完整文件时就变成了Seeder
        - 第一个文件随机下载（尽快拿到一个），剩下的越稀有的优先（有利于传播），最后一个并行（避免等待）
        
        *Problem：*Tracker仍然是中心化的，无法增大规模
    - DHT（Distributed hash table）：提供了数据存查的抽象接口
        - 每个节点只知道部分的hash table
        - 在O(logn)次hops到达目标节点（Finger Table）

        *Problem：* 部分节点宕机可能导致查找失败
        *Solution：* 每个节点存储一个successor list，所有后继同时宕机的概率很小
- BitCoin&BlockChain：
    - Public Key作为用户标识，别人用它给你转账；Private Key作为交易签发的密码，你用它来花钱
    - Block Chain使用自己的Hash值进行链接，存储上一个Block的Hash，并借助它生成自己的Hash给下一个Block用
    - 信任大多数人是honest的时候，可以保证honest chain总是增长最快的；攻击者修改某笔交易，除非重做从此开始的所有ProofofWork（工作量证明）并且超越原有的链的长度
    - 一笔交易广播全网=》每个节点都记录一下=》解出PoW的节点组装Block并通知全网=》每个节点验证其中的交易后接受Block，加到区块链的末尾
    - 一个有能力控制绝大多数计算资源的人没有破坏区块链的动机
    - 如果有人值得信赖，那么区块链就不再需要，它毕竟又慢又难用
# Performance
> ### Metrics
- Capacity：内存设备block数；处理器cycle数；Server同时请求数；网络宽带
- Latency：$Latency_{A+B} ≥ Latency_A + Latency_B$
- Throughput：$Thoughput_{A+B} ≤ min(Throughput_A, Throughput_B)$
- Utilization：CPU利用率；磁盘分配率；网络忙碌时间比例
- 串行执行，throughput和latency成反比；并行执行，两者没有直接联系
- throughput受限于资源，一种方案是concurrency；而latency更难提升，一种方案是fast path（以cache为代表），另外overlapping
- 排队论中利用率高的时候延迟就差，使工作能力和负载相匹配
> ### 基本思想
- Batching
- Caching
- Concurrency
- Parallelism (scheduling)
> ### Bottleneck
- Batching：分摊处理开销；调度请求以获得更好的性能
- Dallying：拖延发现本来需要写的不需要写了；cache写吸收；数据库打包commit
- Speculation：猜测然后提前执行（要求能undo），分支预测；prefetching block
- I/O Bottleneck Case：Latency of 4-KB Data Accessing(寻道8ms，7200转 每转8.33ms，IDE bus 66MB/s)
    1. 不优化。$seektime_{avg}+rotationlatency_{avg}+transmission=8+4.17+(4/(66*1024))*1000ms=8+4.17+0.06ms=12.23ms$
        $reading 4 KB + 1 millisecond of computation + writing 4 KB = 12.23+1+12.23ms=25.46ms$ 
    2. 每次读预取整个track。$seektime_{avg}+1 rotational delay=8+8.33ms=16.33ms$
        一次读可以有$1.5MB/4KB=384$次
        $read 1536KB+384*(1 millisecond of computation + writing 4 KB)=16.33+384*(1+12.23)ms=16.33+5080.32ms=5096.65ms$
        平摊到每次访问是13.27ms
    3. dallying & batching一次性写。$(16.33+384+16.33)/384ms=1.09ms$
    4. 在上一次计算的时候预取，在下一次计算的同时写回，优化到1ms
> ### Scheduling
- Difficulties：Lack of information；Lack of mechanism to enforce policies；Getting mechanism right 
- Example：Web服务器因为把大多数时间用来处理中断和丢弃请求而发生Live lock
- 理想的Scheduler：低时延；高吞吐；低overhead；公平，不会有人饿死；规模线性达到最大容量
- Measuring Request’s Response：
    - Turn-around time：请求到达-完成的时间
    - Response time：请求到达-开始回应的时间
    - Waiting time：请求到达-开始处理的时间
- FCFS（First Come First Server）：会出现Convoy Effect，护卫队现象
- Shortest-Job-First：可能会有人饿死
- Round-Robin：
- 现代处理器使用Priority，相同优先级下Round-Robin
- Real-Time Scheduling：Earliest Deadline First (EDF)最早ddl的先做，做不完的就放弃
- Disk Scheduling：First-come first-serve、Shortest-Seek-First、Elevator Algorithm
> ### Cache Policy
- 与访问数据、引入策略、淘汰策略和容量有关
- 淘汰策略：
    - FIFO：会发生Belady's anomaly，指容量增大，miss反而增多，性能下降了
    - OPT：选择移除最晚才将被用到的
    - LRU：工程上使用时钟策略近似
> ### 带来性能的同时引入了安全漏洞
- Cache Side Channel：通过内存访问的时间推断数据（利用cache）
- Meltdown：从用户态访问kernel时触发的异常发生前，CPU已经无序地超前执行指令拿到了kernel数据，再通过cache是否hit推断出拿出的数据是什么（利用了CPU out-of-order的优化和cache）

# Security
> ### Introduction
- Flush + Reload Attacks：利用cache计时攻击
- Square-and-Multiply Exponentiation in RSA：利用平方和乘法的执行顺序推断密钥
- 共享是危险的，预测执行是危险的
- Lots of personal info stolen
- Phishing attacks
- Botnet
- Stuxnet
- HOW TO SHOP FOR FREE ONLINE：验证中心只验证了客户消费，但没有验证消费给谁
- Buffer overflow attack (stack/heap)
ROP attack
Password attack
Phishing attack 
XSS attack 
SQL injection attack
Integer overflow attack 
Social engineering attack 
Side-channel attack
- Security is negative goal，错误太多且高度相关
- 如何构建安全的系统：Policy（谁能读，谁能写，保持活着） 和 threat model（可以相信什么）
- Guard Model：
  - Authentication：认证，证明我是我？
  - Authorization：授权，证明我有哪些权限？
  - 安全技术：加密（数学），隔离（编程）
  - Complete Mediation：必须是访问资源的唯一方式
> ### Authentication
- Principle of Least Privilege：最低权限原则，减少调用guard数量
- Security Jargon: Trusted is bad，Untrusted components are good，少信别人
- High-level policy is (ideally) concise and clear，try to line up security mechanisms with desired policies
- threat model should not assume users are perfect，用户不是完美的
- cost of security mechanism should be commensurate with value，安全成本与价值相匹配
- Case: Password
    - Timing Attack 时序攻击，一次一位猜密码 =》存密码的hash，密码来了先计算hash，再验证是不是和我的匹配
    - Rainbow table 彩虹表，通过大量常用密码的统计尝试破解hash =》salting，撒盐，计算hash（password|salt），同样的密码有了不同的hash，使构造查找表更为困难
    - Bootstrap Authentication 计算hash时由于理解歧义，对不同的用户名密码生成了相同的hash，例如“Ben”“22May2019”，“Ben2”“2May2019”
    - Phishing Attacks 模仿真正的网站要求用户输入密码
    - Tech 1: challenge-response scheme：Server给用户一个随机数R，用户计算hash（R|password），Server与自己算出的对比
    - Tech 2: use passwords to authenticate the server：用户要验证对面是不是一个真的Server，给Server一个随机数Q，Server计算hash（Q|password），以知道Server是不是真的知道我的密码
    - Tech 3: turn offline into online attack：原本攻击者无需与Server交互，现在要求攻击前必须先访问Server，比如上传图片
    - Tech 4: Specific password：密码专用，用户想一套方法生成密码，但每个站点的密码都不同
    - Tech 5: one-time passwords：用户对密码做n-1次hash，Server再做一次hash后与自己的存储比对，每个密码都只用一次
    - Tech 6: bind authentication and request authorization：不单独验证密码，直接把密码融合在请求中验证
    - Tech 7: FIDO: Replace the Password：用硬件/物理行为来代替密码，指纹等
> ### Secure Channel
- 简单加密算法：
    1. 攻击者可以截获A的消息，然后发给B =》 双方各自增加自增数字seq
    2. 攻击者可以截获A的消息，然后发回A =》 双方使用不同的加密算法！
- Diffie-Hellman Key Exchange：双方不必交流可对密码达成共识 =》 Man-in-the-middle attack
- RSA非对称加密：可靠性基础是大数因式分解的困难性，公钥签名，私钥验签；实际中，用RSA加密传输对称密钥，双方交换密码后时候DES等对称算法传输数据。
- CA
- TLS
- Taint Tracking：对敏感数据的去向全程跟踪，不允许泄露
- Bug from Input
    1. Chose a library (open-source, of course)
    2. 找到所有源文件
    3. 找到输入数据
    4. 跟踪输入数据：TaintCheck，检查不信任的数据，不信任的指令，危险使用
> ### ROP（Return-oriented programming）
- Stack Buffer Overflow
- Code Injection
- 预防：数据区标明不可执行
- Code Reuse Attack
- 预防：
    1. 隐藏二进制文件，不让攻击者找到目标代码；
    2. ASLR，地址空间布局随机化，更难找到目标；
    3. Canary，金丝雀，一个随机值是否被修改，检测stack overflow
> ### CFI（Control-Flow Integrity）
- 一条指令能够转移到的点是预定义的有限集合，如果跑出去就是不安全的
- 分支跳跃有direct和indirect branch，我们需要预防的indirect包含很少量的节点
- 实现中在每个可能跳转的label前加一个唯一bit pattern，运行时插入二进制代码检查目标指令的bit pattern是否匹配
*Problem：* 如果A->C,B->C&D，那么C和D将使用同一个tag，那么A->D也将被允许
*Solution1：* duplicate code or inline 或者 multiple tags
*Solution2：* shadow call stack，再维护一个栈，专门存放跳跃表
- 没有保护错误参数的系统调用；文件名替换；以及其它仅针对数据的攻击
> ### Minimizing TCB（Trusted Computing Base）
- Root of trust：首先我们必须要相信些什么，但我们尽可能少地去信赖
- MircoKernel vs MonolithicKernel
    1. 如果关键服务挂掉，系统都不可用
    2. 一些服务可以在多模块间共享
    3. 某些服务的性能是很关键的
    4. 独立系统可以轻松调试微内核系统
    5. 很难重新组织现有的内核程序
- TPM（Trusted Platform Module）：一种可信赖的芯片，用于存储私钥、BIOS开机密码、管理散列链等
# Isolation：让系统变得简单
> ### Virtual Machine
- Pros：单机运行多个系统；隔离和容错；易于部署备份复制迁移；安全
- CPU Virtualization
    *Challenge：* privilege instructions不能在user mode下执行，e.g. cli，change CR3，set IDT，etc.
    *Solution：* Trap&Emulate：遇到privilege instructions就trap，然后模拟运行
    *Problem：* 不是所有架构都是strictly virtualizable的，例如x86中有17条指令在user/kernel态表现不同
    *Solution：*
    1. Instruction Interpretation: 用软件模拟所有指令各阶段的执行，e.g. Bochs，太慢
    2. Binary translation: CPU前置过滤器，将17条特权指令翻译成其他函数调用，类似inline，e.g. VMware，Qemu
       *Problem：* 中断只能发生在Basic Block边界
    3. Para-virtualization: 直接修改OS的源代码，不要使用特殊指令，改成hypercall
    4. New hardware: root和non-root都支持ring0-3
- Memory Virtualization
    *Terminology：* GVA（Guest Virtual Address），GPT（Guest Page Table）， GPA（Guest Physical Address）， HPT（Host Page Table），HPA（Host Physical Address）
    *Challenge：* CR3只有一个，指向GPT并不能找到真正的地址
    *Solution：*
    1. Shadow Pages：把GPT和HPT映射为一个SPT，并模拟CR3指向SPT
        *Problem：* Guest修改自己的page table没有效果，因为CR3指向的是SPT
        *Solution：* 把GPT设为readonly，写时触发trap，更新SPT
        *Problem：* Guest App可以访问访问kernel，因为大家都是运行在user mode下的
        *Solution：* 拆分成两个SPT，在切换user/kernel mode的同时切换SPT
        *Problem：* 每个Guest App都持有一个SPT，浪费内存
    2. Direct Paging (Para-virtualization)：没有GPA，Guest OS直接管理它的HPA，使用hypercall通知VMM更新页表，CR3指向GPT
        *Pros：* 易于开发，性能好，guest可以通过batch减少trap
        *Cons：* Guest知道的太多了，虚拟化程度不够，例如rowhammer攻击
    3. Hardware Supported Memory Virtualization：Intel's EPT，AMD's NPT
        *Problem？* 两个CR3，页表操作次数从4+1次增加到16+4次
- I/O Virtualization：OS课上再讲
> ### Enclave and TEE