# 05-3-lab4-spoc-discussion.md

计44 黄锐皓 2014011385

## 视频相关思考题

### 13.1 总体介绍

1. 为什么讲基本原理时先讲进程后讲线程，而在做实验时先做线程后做进程？

   线程控制块TCB是由proc_struct这样一个数据结构维护。

2. ucore的线程控制块数据结构是什么？

   在ucore中只有一个PCB数据结构，进程和线程都使用这一个数据结构。

### 13.2 关键数据结构

1. 分析proc_struct数据结构，说明每个字段的用途，是线程控制块或进程控制块的，会在哪些函数中修改。

   寄存器状态、堆栈、当前指令指针等信息是线程控制块的；mm, vma等内存管理字段是进程控制块的。

2. 如何知道ucore的两个线程同在一个进程？

   比较两个线程的cr3和mm_struct，因为同一个进程共享一块内存空间和页表。

3. context和trapframe分别在什么时候用到？

   trapframe在返回中断即执行iret时用于恢复现场，context在switch_to的ret时用到，返回到要切换的进程上下文context（一组寄存器的值）。

4. 用户态或内核态下的中断处理有什么区别？在trapframe中有什么体现？

   用户态下中断处理涉及特权级切换(从用户态到内核态),内核态不涉及。

5. 分析trapframe数据结构，说明每个字段的用途，是由硬件或软件保存的，在内核态中断响应时是否会保存。

   tf_eip, tf_cs等由硬件保存；tf_esp, tf_ss等在用户态响应时由硬件保存；通用寄存器由软件保存。( lab4/kern/trap/trapentry.S )

### 13.3 执行流程

1. kernel_thread创建的内核线程执行的第一条指令是什么？它是如何过渡到内核线程对应的函数的？

   ```
   tf.tf_eip = (uint32_t) kernel_thread_entry;
   /kern-ucore/arch/i386/init/entry.S
   /kern/process/entry.S
   ```

2. 内核线程的堆栈初始化在哪？

   setup_stack

   tf和context中的esp

3. fork()父子进程的返回值是不同的。这在源代码中的体现中哪？

4. 内核线程initproc的第一次执行流程是什么样的？能跟踪出来吗？

5. 分析线程切换流程，找到内核堆栈、页表、寄存器切换的代码位置。

6. 分析C语言中调用汇编函数switch_to()的参数传递位置。

7. 分析内核线程idleproc的创建流程，说明线程切换后执行的第一条是什么。

   ```C
   proc->context.eip = forkret;
   tf.tf_eip = (uint32_t) kernel_thread_entry;
   ```


8. 分析内核线程initproc的创建流程，说明线程切换后执行的第一条是什么。