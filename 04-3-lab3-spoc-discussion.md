# 04-3-lab3-spoc-discussion

计44 黄锐皓 2014011385

# lab3 SPOC思考题

### 10.1 实验目标：虚存管理

1. 缺页和页访问非法的返回地址有什么不同？

   硬件设置、软件可修改。

2. 虚拟内存管理中是否用到了段机制？

   是。

3. ucore如何知道页访问异常的地址？

   CPU会把产生异常的线性地址存储在cr2中。

### 10.2 回顾历史和了解当下

1. 中断处理例程的段表在GDT还是LDT？

   GDT。

2. 物理内存管理的数据结构在哪？

   list_entry_t。

3. 页表项的结构？

   物理页帧号、标志位。

4. 页表项的修改代码？

   缺页异常处理代码。

5. 如何设置一个虚拟地址到物理地址的映射关系？

   修改页表项内容。

6. 为了建立虚拟内存管理，需要在哪个数据结构中表示“合法”虚拟内存？

   页表项中的有效位。

### 10.3 处理流程、关键数据结构和功能

1. swap_init()做了些什么？

   对换分区的初始化。

2. vmm_init()做了些什么？

   逻辑地址空间初始化。

3. vma_struct数据结构的功能？

   逻辑地址空间的合法区域的维护数据结构。

4. mmap_list是什么列表？

   用于维护连续逻辑地址空间映射的数据结构。

5. 外存中的页面后备如何找到？

   swap_in_page。

6. vma_struct和mm_struct的关系是什么？

   合法的连续虚拟地址区域、整个进程的地址空间。

7. 画数据结构图，描述进程的虚拟地址空间、页表项、物理页面和后备页面的关系；

### 10.4 页访问异常

1. 页面不在内存和页面访问非法的处理中有什么区别？对应的代码区别在哪？

   pgfault_handler。

2. find_vma()做了些什么？

   查找合法逻辑地址所在vma数据结构。

3. swapfs_read()做了些什么？

   读取在外存中的页面内容。

4. 缺页时的页面创建代码在哪？

   page_insert。

5. struct rb_tree数据结构的原理是什么？在虚拟管理中如何用它的？

   红黑树，用于加快查找速度。

6. 页目录项和页表项的dirty bit是何时，由谁置1的？

   硬件。

7. 页目录项和页表项的access bit是何时，由谁置1的？

   硬件。	

### 10.5 页换入换出机制

1. 虚拟页与磁盘后备页面的对应有关系？

   PTE中存在位为零时，采用swap_entry_t的结构来表示与外存后备页面的对应关系。

2. 如果在开始加载可执行文件时，如何改？

3. check_swap()做了些什么检查？

   替换算法能否正确完成。

4. swap_entry_t数据结构做什么用的？放在什么地方？

   表示虚拟页与磁盘中后备页面的对应关系，在PTE中。

5. 空闲物理页面的组织数据结构是什么？

   Page。

6. 置换算法的接口数据结构？

   swap_manager。