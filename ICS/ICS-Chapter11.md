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
    
    //AF_INET(IPv4地址)、AF_INET6(IPv6地址)
    //src(dotted-decimal)转换为dst(IP地址，网络字节顺序)
    //成功返回1，src非法返回0，出错返回-1并设置errno
    int inet_pton(AF_INET, const char *src, void *dst);

    //src(IP地址，网络字节顺序)转换为dst(dotted-decimal,最多size个byte)
    //成功返回dst，出错返回NULL
    const char *inet_ntop(AF_INET, const void *src, char *dst, socklen_t size);
    ```
  - Domain name
    - 域名集合形成一个层次结构，可以表示成一棵树
      - 第一层：未命名根节点
      - first-level：由ICANN定义，例如com、edu、gov、org、net
      - second-level：由ICANN的各个授权代理按照先到先服务的基础分配的，例如cmu.edu
      - 一旦一个组织得到了一个二级域名，就可以在subdomain中创建任何新的域名了，例如cs.cmu.edu
    - 域名集合与IP地址集合之间的映射通过DNS(Domain Name System)
    - 域名和IP地址可以是一一映射、一多映射、多一映射、没有映射
  - 因特网连接
    - 点对点、全双工、可靠的
    - socket：address：port(16-bit)
      - 客户端port自动分配(ephemeral port临时端口)
      - 服务器port知名端口(参考 /etc/services)
        1. Web服务器 port：80 知名服务名：http
        2. 电子邮件服务器 port：25 知名服务名：smtp
    - socket pair：(cliaddr:cliport, servaddr:servport)
> Socket Interface
  - 地址结构：
    ```
    //历史遗留问题，本可以是一个标量类型
    struct in_addr{
        uint32_t s_addr; //in network byte orde (big-endian)
    }

    //以下两种数据结构强制转换，解决早期没有void*的困难
    struct sockaddr_in{
        uint16_t sin_family; //Protocol(always AF_INET)
        uint16_t sin_port; //in network byte order
        struct in_addr sin_addr; //in network byte order
        unsigned char sin_zero[8];
    }

    struct sockaddr{
        uint16_t sa_family;
        char as_data[14];
    }
    ```
  
  - 函数
    ```
    #include <sys/types.h>
    #include <sys/socket.h>

    /*
     *成功返回socket descriptor,出错返回-1
     *Usage: clientfd = Socket(AF_INET, SOCK_STREAM, 0);
     */
    int socket(int domain, int type, int protocol);

    /*
     *成功返回0，出错返回-1
     */
    int connect(int clientfd, const struct sockaddr *addr, socklen_t addrlen);

    int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);