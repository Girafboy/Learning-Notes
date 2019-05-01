### 第6章 存储器层次架构
> 存储技术：
  - DRAM和SRAM(断电丢失，Main Memory用的是DRAM)、ROM（断电不丢失，flash memory基于EEPROM，用于手机、SSD硬盘等）
  - 磁盘存储
    - 构造：platter->surface->track->sector->bytes
    - 时间计算：access time = seek time + rotational latency + transfer time  
               T/avg seek = 3~9ms  
               T/avg rotation = 1/RPM X 60s/1min (RPM- Revolution Per Minute)  
               T/avg transfer = 1/RPM X 60s/1min X 1/(average#sectors/track)  
    - DMA(Direct Memory Access)
  - SSD
  - 速度 SRAM > DRAM > Disk  
  - 价格 SRAM > DRAM > SSD > 旋转磁盘  
> 局部性（Locality）：
  - 时间局部性：重复引用相同变量的程序
  - 空间局部性：步长越小越好
  - 对于指令：循环体越小，局部性越好
### 第7章 链接
### 第8章 异常控制流
### 第9章 虚拟内存
### 第10章 系统级I/O
