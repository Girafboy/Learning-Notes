# 第4章 处理器体系结构
> ### Y86-64指令集
- ISA(Instruction-Set Architecture): 一个处理器支持的指令和指令的字节级编码
- 程序员可见的状态
  - 15 registers: %rax, %rcx, %rdx, %rbx, %rsp, %rbp, %rsi, %rdi, %r8-%r14
  - CC(Condition codes): ZF(是否为0), SF(负数), OF(无符号溢出)
    - OPq指令会设置CC
  - PC(Program counter)
  - memory
  - Stat: 表明程序正常运行，或是出现某种异常
    value|name|含义
    :-:|:-:|-
    1|AOK|正常操作
    2|HLT|执行halt指令
    3|ADR|遇到非法地址
    4|INS|遇到非法指令
    出现异常时，处理器会调用exception handler
- Y86-64指令及编码
  - 详见Computer Systems-A programmer's perspective P357-P359
- CISC v.s. RISC
    CISC|RISC
    -|-
    指令种类多|指令种类少，但编译出来指令多
    实现细节不可见|实现细节可见
    --|流水化作业
    编码长度可变|编码固定长度
    有条件码|无条件码
    栈用来存取过程参数和返回地址|寄存器被用来存取过程参数和返回地址
- directive伪指令
  - .data: .byte .word .long .quad
  - .pos
  - .align
- convension
  - pushq压入%rsp的原始值
  - popq取出%rsp栈中的值
> ### HCL(Hardware Control Language)
- HCL表达硬件设计的控制部分，已经开发有将HCL直接翻译成Verilog的工具，将这个代码与Verilog结合起来就能产生HDL，再据此就可以合成微处理器
- Logic Gates: AND&& OR|| NOT!
- Combinational Circuits and HCL Boolean Expressions
  `bool eq = (a && b) || (!a && !b)`
  `bool mux = (s && a) || (!s && b)`
- Word-Level Combinational Circuits and HCL Integer Expressions
  `bool Eq = (A == B)`
  ```
  word Mux = [
      s: A;
      1: B;
  ];
  word Min3 = [
      A <= B && A <= C : A;
      B <= A && B <= C : B;
      1                : C;
  ];
  ```
- Set Membership
  `bool s = code in { 1, 3 }`
- Memory and Clocking
  - register file
  - random access memory
> ### Sequential Y86-64
- 指令六个阶段
  - fetch: icode:ifun [rA:rB] [valC] valP
  - decode: [valA] [valB] (%rsp)
  - execute: (只有OPq设CC)
    - valE <- valB/0 + valA/valC/8/-8
    - Set CC / Cnd <- Cond(CC, ifun)
  - memory: <valM, valA, valE, valP>
  - write back: <valE, valM>
  - PC update: <valP, valC, valM>
- SEQ硬件结构
  - 详见Computer Systems-A programmer's perspective P397-P400
- SEQ时序
  - 组合逻辑: Instruction memory可以视作组合逻辑
  - 存储器设备
    - clocked register(PC, CC)
    - RAM(register file, instruction memory, data memory)
  - 原则： No reading back
- SEQ Stage 实现
  - Fetch Stage
    `bool imem_error = ...`
    `bool instr_valid = icode in {...}`
    `bool need_regids = icode in { IRRMOVQ, IOPQ, IPUSHQ, IPOPQ, IIRMOVQ, IRMMOVQ, IMRMOVQ };`
    `bool need_valC = icode in { IIRMOVQ, IRMMOVQ, IMRMOVQ, IJXX, ICALL}`
  - Decode and Write-Back Stages
  - Execute Stage
  - Memory Stage
  - PC Update Stage
> ### Pipeline