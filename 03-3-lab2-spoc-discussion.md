# 03-3-lab2-spoc-discussion

计44 黄锐皓 2014011385

## 第七讲视频相关思考题

### 7.1 了解x86保护模式中的特权级

1. X86有几个特权级？

4个。

2. 不同特权级有什么区别？

一些特权指令只能在ring 0特权级执行，在不同特权级下，指令所能访问的数据空间是不同的，通常高特权级能够访问的数据空间更大。同一条指令在不同特权级下也会有不同的行为。

2. 请说明CPL、DPL和RPL在中断响应、函数调用和指令执行时的作用。

- CPL：当前进程优先级
- DPL：数据段需求的优先级
- RPL：进程当前请求的优先级
- 访问门时，低优先级进程请求高优先级服务；访问数据时，高优先级访问低优先级数据

4. 写一个示例程序，完成4个特权级间的函数调用和数据访问时特权级控制的作用。

### 7.2 了解特权级切换过程

1. 一条指令在执行时会有哪些可能的特权级判断？

当指令需要访问数据时，判断响应的数据段能否在当前特权级访问。跳转指令跳转到一个新的地址时，同样需要判断段是否能被访问。执行某些特权级指令时，要判断当前特权级是否有权限执行这些特权指令。当发生中断响应和系统调用时，访问对应的中断门页需要特权级判断。

2. 在什么情况下会出现特权级切换？

在中断、陷入、系统调用及返回时会有特权级切换。

3. int指令在ring0和ring3的执行行为有什么不同？

在ring3时执行会切换堆栈，且压栈的内容中多了ESP和SS。

4. 如何利用int和iret指令完成不同特权级的切换？

人工构造需要的栈结构，然后通过int和iret指令进行切换。

5. TSS和Task Register的作用是什么？

> [Task state segment](https://en.wikipedia.org/wiki/Task_state_segment)

> Reference: [Intel® 64 and IA-32 Architectures Software Developer Manuals](http://os.cs.tsinghua.edu.cn/oscourse/OS2017spring/lecture04?action=AttachFile&do=view&target=325462-sdm-vol-1-2abcd-3abcd.pdf) Page 2897/4684: 7.2.1 Task-State Segment (TSS)

### 7.3 了解段/页表

1. 一条指令执行时最多会出现多少次地址转换？

(1+页表级数)次。

2. 描述X86-32的MMU地址转换过程；

先通过段选择符确定段基址，再通过多级页偏移决定物理帧，最后加上偏移得到实际物理地址。

### 7.4 了解UCORE建立段/页表

1. 分析MMU的使能过程，尽可能详细地分析在执行进入保护械的代码“movl %eax, %cr0 ; ljmp $CODE_SEL, $0x0”时，CPU的状态和寄存器内容的变化。

打开cr0的保护模式位，进入保护模式，开启MMU。

2. 分析页表的建立过程；

- 将一页设置为页目录
- 将页目录clear
- 将0xC0000000-0xF8000000(va)映射到0x00000000-0x38000000
- 将0x00000000-0x00100000(va)映射到0x00000000-0x00100000(pa)
- 设置cr3和cr0的第31位
- 更新GDT
- 取消0x00000000-0x00100000的映射