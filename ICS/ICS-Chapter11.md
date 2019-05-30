# 第11章 网络编程
> C/S 编程模式(Client/Server)
  - 基本操作：Transaction（事务）
  - 基本流程：
    1. Client发起Transaction，发送request
    2. Server收到request，解释并操作资源
    3. Server发送response给Client，等待下一个request
    4. Client收到response并处理
  - Client&Server是进程，而不是machine或host
> Network
  - LAN(Local Area Network)：局域网，其中最流行的是Ethernet(以太网)
    - Ethernet segment(以太网段)：包括wires(双绞线)和hub(集线器)，hub完全同步所有端口
    - Ethernet adapter(以太网适配器)：全球唯一48bit地址
    - Bridge Ethernet(桥接以太网)：多个Ethernet segment用wire和bridge(网桥，会分配算法)连接起来形成
  - WAN(Wide-Area Network，广域网)：高速的点到点电话连接
  - internet(interconnected network)：多个不兼容的LAN&WAN用router(路由器)连接起来形成
  - Protocol(协议)：解决了不兼容局域网间传送数据的问题
    1. HostA 上的client调用system call，把数据从virtual address复制到kernel buffer
    2. HostA 上的protocol　software在数据前附加PH和FH1形成frame(PH<互联网络包头>寻址到Host B，FH1<LAN1的帧头>寻址到router)，然后传送frame到LAN1 adapter
    3. LAN1 adapter把frame复制到network
    4. Router's LAN1 adapter读取frame传给protocol software
    5. Router从PH提取出目标网络地址，把FH1换成FH2，把新的frame传给adapter
    6. Router's LAN2 adapter把frame复制到network
    7. HostB's adapter读取frame并传给protocol software
    8. HostB's protocol software剥落PH和FH2，最后把数据通过system call复制到virtual address
> The Global IP Internet
  - IP地址
    - 地址结构(历史遗留问题)
    ```
    struct in_addr {
        uint32_t s_addr;/*Address in network byte order(big-endian)*/
    }
    ```
    - big-endian/little-endian转换
    ```
    #include <arpa/inet.h>

    //返回网络字节顺序值
    uint32_t htonl(uint32_t hostlong); 
    uint16_t htons(uint16_t hostshort);

    //返回主机字节顺序值
    uint32_t ntohl(uint32_t netlong);
    uint16_t ntohs(uint16_t netshort);
    ```
    - IP address/dotted-decimal string转换
    ```
    #include <arpa/inet.h>

    int inet_pton(AF_INET, const char *src, void *dst);

    const char *inet_ntop(AF_INET, const void *src, char *dst, socklen_t size);
    ```