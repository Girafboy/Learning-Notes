# 第6章 存储器层次架构
> 存储技术：
  - DRAM和SRAM(断电丢失，Main Memory用的是DRAM)、ROM（断电不丢失，flash memory基于EEPROM，用于手机、SSD硬盘等）
  - 磁盘存储
    - 构造：platter->surface->track->sector->bytes
    - 时间计算：
      - access time = seek time + rotational latency + transfer time  
      - T/avg seek = 3~9ms  
      - T/avg rotation = 1/RPM X 60s/1min (RPM- Revolution Per Minute)  
      - T/avg transfer = 1/RPM X 60s/1min X 1/(average#sectors/track)  
    - DMA(Direct Memory Access)
  - SSD
  - 速度 SRAM > DRAM > Disk  
  - 价格 SRAM > DRAM > SSD > 旋转磁盘  
> 局部性（Locality）：
  - 时间局部性：重复引用相同变量的程序
  - 空间局部性：步长越小越好
  - 对于指令：循环体越小，局部性越好
> 层次结构（Hierarchy）：
  - Regs -> L1 cache(SRAM) -> L2 cache(SRAM) -> L3 cache(SRAM) -> Main Memory(DRAM) -> Local disks -> Remote(distributed file systems, Web serves)
> Cache Memories：
  - General organization of cache (S, E, B, m).
  - C = S x E x B (S=2^s B=2^b t=m-(s+b)
  - cache: S set -> E line -> 1 valid bit & t tag bits & B bytes 
  - address: t bits Tag & s bits Set index & b bits Block offset
  - Type:
    - Direct-Mapped Caches(E=1)
    - Set Associative Caches
    - Fully Associative Caches(E=C/B)
  - write hit: write-through v.s. write-back
  - write miss: write-allocate v.s. not-write-allocate
  - i-cache d-cache / unified cache
  - Performance:
    - miss rate
    - hit rate
    - hit time
    - miss penalty
> Cache-Friendly Code
  - Example:  
    ```  
    for(i=0; i<M; i++)
      for(j=0; j<N; j++)
        sum += a[i][j];
    ``` 
    ```
    for(j=0; j<M; j++)
      for(i=0; i<N; i++)
        sum += a[i][j];
    ```
> Memory mountain
# 第7章 链接
> Compiler Driver
  - Source files:
    - *.c (ASCII source file)
  - Translators:
    - cpp(C preprocessor) -> *.i (ASCII intermediate file)
    - ccl(C compiler) -> *.s (ASCII assembly-language file)
    - as(assembler) -> *.o (relocatable object file)
  - Linker:
    - ld(linker program) -> *.elf (executable object file)
> Static Linking
  - Symbol resolution
  - Relocation
> Object File
  - Relocatable object file
    - Windows -> *.obj
    - Linux -> *.o
  - Executable object file
    - Windows -> *.exe
    - Linux -> /bin/bash
  - Shared object file 
    - Windows -> *.dll
    - Linux -> *.so
  - Format (COFF)
    - Windows -> Portable Executable(PE)
    - MacOS-X -> Mach-O
    - x86-64 Linux & Unix -> Executable and Linkable Format(ELF)
  - Relocatable Object Files
    - ELF header:
      1. 生成该文件的系统的字的大小和字节顺序
      2. ELF header的大小
      3. 目标文件的类型（relocatable， executable or shared）
      4. 机器类型（e.g. x86-64)
      5. section header table的文件偏移，以及其中entry的大小和数量
    - .text:(Ndx=1)
      - 已编译的机器代码
    - .rodata:
      - 只读数据
    - .data:(Ndx=3)
      - 已初始化的全局和静态变量
    - .bss:（Better save space）
      - 未初始化的全局和静态变量
      - 初始化为0的全局和静态变量
      - 占位符，不占据实际空间；运行时，内存分配，初始值为0
    - .symtab:
      - 符号表：定义和引用的函数和全局变量的信息
      - 类型：
        - 全局符号： 非静态函数和全局变量
        - 外部符号： 引用的非静态函数和全局变量
        - 局部符号： static属性的函数和全局变量（对外部模块不可见）
      - Entry:
        - name: String table offset
        - type: Function or Data
        - binding: Local or Global
        - value: Section offset or absolute address
        - section: <=> Ndx
          - pseudosection:（仅出现在可重定位目标文件）
            - ABS: 不该被重定位的符号
            - UNDEF： 未定义符号
            - COMMON： 未被分配位置的未初始化数据（未初始化的全局变量，其他是.bss），也即弱全局符号
      - Resolution:
        - Rules:（Linux链接器）
          1. 不允许多个同名的强符号
          2. 强符号和弱符号同名，选择强符号
          3. 多个弱符号同名，任意选择一个
        - static library:
          - Linux中archive
          - E-可重定位目标文件集合，U-未解析符号集合，D-已定义符号集合
          - *.a位置很重要
    - .rel.text：
      - .text节中的位置列表，链接时修改位置
      - 任何调用外部函数或者引用全局变量的指令
      - 可执行目标文件中通常省略
    - .rel.data:
      - 被引用或定义的所有全局变量的重定位信息
    - .debug:
      - 局部变量和类型定义、定义和引用的全局变量、原始C源文件
      - 以-g选项调用才生成
    - .line:
      - 源文件中行号与.text中机器指令的映射
      - 以-g选项调用才生成
    - .strtab:
      - .symtab和.debug中的符号表
      - section header中的section name
    - Section header table:
      - 描述了不同section的位置和大小
      - 每个section有固定大小的entry
# 第8章 异常控制流
# 第9章 虚拟内存
# 第10章 系统级I/O
