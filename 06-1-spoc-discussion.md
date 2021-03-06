# 06-1-spoc-discussion

计44 黄锐皓 2014011385

## 视频相关思考题

### 15.1 处理机调度概念

1. 处理机调度的功能是什么？

> 选线程：从就绪队列中挑选下一个占用CPU运行的线程

> 选CPU：从多个可用CPU中挑选就绪线程可使用的CPU资源

1. 在什么时候可以进行处理机调度？

> 调度时机的所有可能：

> 占用CPU运行的线程的主动放弃（退出、等待）

> 当前线程被抢先（时间片用完、高优先级线程就绪）

1. 当操作系统的处理机调度导致线程切换时，暂停线程的当前指令指针可能在什么位置？用户态代码或内核代码？给出理由。

> 系统调用返回时可能出现线程切换

### 15.2 调度准则

1. 处理机的使用模式有什么特征？

> CPU与I/O交替使用；

> 每次占用CPU执行指令的时间长度分布多数在10ms以内；

1. 处理机调度的目标是什么？

> CPU利用率（CPU使用率、吞吐率、周转时间、等待时间）

> 用户感受（周转时间、等待时间、响应时间、响应时间的方差）

> 公平性（CPU时间分配公平性）

1. 尝试在ucore上写一个外排序程序，然后分析它的执行时间分布统计（每次切换后开始执行时间和放弃CPU的时间、当前用户和内核栈信息）。
2. 在Linux上有一个应用程序time，可以统计应用程序的执行时间信息。请分析它是如何统计进程执行时间信息的。如可能，请在ucore上实现相同功能的应用程序。下面是可能的参考。
   - [Linux用户态程序计时方式详解](http://www.cnblogs.com/clover-toeic/p/3845210.html)
   - [Get Source Code for any Linux Command](http://www.thegeekstuff.com/2010/02/get-source-code-for-any-linux-command/)
   - [How does time command work](http://unix.stackexchange.com/questions/29800/how-does-time-command-work)
   - <https://github.com/illumos/illumos-gate/blob/master/usr/src/cmd/time/time.c>
3. 尝试获取一个操作系统的调度算法的性能统计数据（CPU使用率、进程执行）。

### 15.3 先来先服务、短进程优先和最高响应比优先调度算法

1. 尝试描述下列处理机调度算法的工作原理和算法优缺点。
   - 先来先服务算法（FCFC、FIFO）
   - 短进程优先算法（SPN、SJF）
   - 最高响应比优先算法（HRRN）

> 就绪队列的排队依据：到达时间、进程预期执行时间、按响应比R=(w+s)/s

> 算法特征：平均等待时间、等待时间方差、资源利用率

1. 为什么短进程优先算法的平均周转时间最优？

> 可以证明知进程优先算法的调度顺序的平均周转时间是最短的。

1. 如何调试你的调度算法？

> 监控调度算法的执行状态、调度结果、算法特征等信息。

### 15.4 时间片轮转、多级反馈队列、公平共享调度算法和ucore调度框架

1. 尝试描述下列处理机调度算法的工作原理和算法优缺点。
   - 时间片轮转算法（RR）
   - 多级反馈队列算法（MLFQ）
   - 公平共享调度算法（FSS）

> 让出CPU的条件：进程执行时间

> 就绪队列排队依据：多队列、多种排队依据、依据进程特征调整所在队列

> 算法特征：响应时间、平均周转时间、不同类型进程的调度差异

1. RR算法选择时间片长度的依据有哪些？

> 进程切换开销

> 进程当前操作占用的CPU时间

> 切换开销与响应时间的折中权衡

1. 请描述在MLFQ中，如何提升或降低进程的优先级？

> I/O操作后提升优先级

> 时间片用完后降低优先级

1. 尝试跟踪ucore中进程切换和调度算法的就绪进程选择过程。

> 中断响应、进程的中断现场保存、中断处理、调度判断、进程切换、新进程的中断现场恢复、新进程的继续执行

1. 为什么要引入调度框架？定义调度算法接口需要考虑哪些因素？

> 引入调度框架的目的：分离调度操作和调度策略，以支持多种调度算法；

> 定义调度算法接口的考虑因素：调度算法需要的调度操作类型、调度算法在各调度操作中的体现方式；

1. 通过观察FIFO、SJF和RR调度算法的[模拟程序](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep8-scheduler.py)运行结果及其[描述文档](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep8-scheduler.md)，理解其工作原理和算法特征。
2. 通过观察MLFQ调度算法的[模拟程序](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep9-mlfq.py)运行结果及其[描述文档](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep9-mlfq.md)，理解其工作原理和算法特征。
3. 通过观察彩票调度算法([Lottery scheduling](https://en.wikipedia.org/wiki/Lottery_scheduling))的[模拟程序](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep10-lottery.py)运行结果及其[描述文档](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep10-lottery.md)，理解其工作原理和算法特征。

### 15.5 实时调度和多处理器调度

1. 什么是实时操作系统？

> 实时操作系统是保证在一定时间限制内完成特定功能的操作系统。

> 参考： <http://baike.baidu.com/item/%E5%AE%9E%E6%97%B6%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F>

1. 什么是可调度性？

> 可调度表示一个实时操作系统能够满足任务时限要求

1. 尝试描述下列处理机调度算法的工作原理和算法优缺点。
   - 速率单调调度算法(RM, Rate Monotonic)
   - 最早截止时间优先算法 (EDF, Earliest Deadline First)

> 任务优先级定义：按周期大小排队、按截止时间先后排队

> [RM](https://en.wikipedia.org/wiki/Rate-monotonic_scheduling)的可调度条件：CPU利用率小于ln2时，是可调度的。

> [EDF](https://en.wikipedia.org/wiki/Earliest_deadline_first_scheduling)的可调度条件：CPU利用率小于100%。

1. 有兴趣的同学，请阅读下面论文，然后说明实时调度面临的主要困难是什么？
   - [Buttazzo, “Rate monotonic vs. EDF: Judgement Day”, EMSOFT 2003.](http://www.eecs.umich.edu/courses/eecs571/reading/rm-vs-edf.pdf)
   - [单调速率及其扩展算法的可调度性判定](http://www.jos.org.cn/ch/reader/create_pdf.aspx?file_no=20040602)
2. 多处理机调度中每个处理机一个就绪队列与整个系统一个就绪队列有什么不同？

> 区别：调度开销、负载均衡程度

### 15.6 优先级反置

1. 什么是优先级反置现象？

> 操作系统中出现高优先级进程长时间等待低优先级进程所占用资源的现象

1. 什么是优先级继承(Priority Inheritance)和优先级天花板协议(priority ceiling protocol)？它们的区别是什么？

> 优先级继承：占用资源的低优先级进程继承申请资源的高优先级进程的优先级

> 优先级天花板协议：占用资源进程的优先级和所有可能申请该资源的进程的最高优先级相同

> 区别：提升占用资源的低优先级进程的优先级的时机不同

1. 在单CPU情况下，基于以前的OS知识，能否设计一个更简单的方法（也许执行效率会低一些）解决优先级反置现象？
2. ppt中“优先级继承”页里的图示有误，你能指出来吗？