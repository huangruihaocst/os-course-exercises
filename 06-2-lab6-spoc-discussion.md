# 06-2-lab6-spoc-discussion

## 视频相关思考题

### 16.1 总体介绍

1. 进程控制块中与调度相关的字段有哪些？在什么情况下会对其进行修改？

> volatile bool need_resched; // bool value: need to be rescheduled to release CPU?

> uint32_t wait_state; // waiting state

> list_entry_t run_link; // the entry linked in run queue

> int time_slice; // time slice for occupying the CPU

> skew_heap_entry_t lab6_run_pool; // FOR LAB6 ONLY: the entry in the run pool

> uint32_t lab6_stride; // FOR LAB6 ONLY: the current stride of the process

> uint32_t lab6_priority; // FOR LAB6 ONLY: the priority of process, set by lab6_set_priority(uint32_t)

1. ucore的就绪队列数据结构在哪定义？在哪进行修改？

> kern/schedule/sched.c

> static struct run_queue __rq;

1. ucore的等待队列数据结构在哪定义？在哪进行修改？

> kern/schedule/sched.c

> static list_entry_t timer_list;

1. 尝试跟踪ucore中的调度过程。

> 中断响应、线程的中断现场保存、中断处理、调度触发、当前线程入队、选取下一个运行线程、下一个运行线程出队、线程切换、新线程的中断现场恢复、新线程的继续执行

### 16.2 调度算法支撑框架

1. 调度算法支撑框架中的各个函数指针的功能是啥？会被谁在何种情况下调用？

> 初始化、触发、选取、出队、入队、切换

1. 调度函数schedule()的调用函数分析，了解进程调度的原因。请分析ucore中所有可能的调度位置，并说明可能的调用原因。

> do_exit

> do_wait

> cpu_idle

> lock

> init_main

> trap

### 16.3 时间片轮转调度算法

1. 时间片轮转调度算法是如何基于调度算法支撑框架实现的？

> kern/schedule/default_sched.c

> struct sched_class default_sched_class

> 还需要分析并确定调度算法的实现会依赖哪些内核数据和对哪些内核数据有影响；

1. 时钟中断如何调用RR_proc_tick()的？

> 时钟中断时会检查时间片的计数，到达零（时间片用完）时，设置可调度标志（need_resched）。

### 16.4 stride调度算法

1. stride调度算法的思路？

> 步长值stride

> 步进值pass

> 以步长为优先级的动态优先级调度算法；每次执行一个时间片，时间片用完时，优先级增加量为“步进”值。

1. stride算法的特征是什么？

> 动态优先级调度算法

> 确定的调度顺序

> 线程的执行时间与步进值的倒数成正比

1. stride调度算法是如何避免stride溢出问题的？

> 利用无符号数的有符号比较，从而避免步长值修改时的溢出处理；

1. 无符号数的有符号比较会产生什么效果？

> [无符号数的有符号比较会产生什么效果？](https://piazza.com/class/i5j09fnsl7k5x0?cid=357)

1. 什么是斜堆(skew heap)？斜堆在stride算法的实现中有什么用？

> 参考文档：[Skew heap](https://en.wikipedia.org/wiki/Skew_heap) [斜堆](http://baike.baidu.com/link?url=BYMgWi8gT5sZE2sG0ndX1CoYZVhe5NJig5s9-u1gO7ldVIxRwLzUpL9pvqN5qEOk_8nGUuJ7VSZNU8pGSicUnK)

> 斜堆的堆顶是优先级最小的节点；斜堆的合并时间开销为O(logN)，删除最小节点操作和插入操作都可以转换成合并操作，开销很小；