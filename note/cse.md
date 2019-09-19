## 第三节

#### FAT 和 inode



vnode : virtual inode (基于FAT上面再加一层抽象)

 

VFS and NFS



User 调 Kernel 的 FS ，因为要从User切到Kernel，调用时间更长（相比于app内）



fopen() -> libc -> open()

fopen() 更快一点，因为在libc那一层会做一层buffer



File Meta-data

- no atime (不会每一次读都需要去写磁盘，会先放在内存里，关掉文件之后再统一写磁盘)



open()返回的fd是当前这个process未使用的最小的fd，一般是fd3（fd0:std in ,  fd1:std out ,  fd2:std err）



file cursor 共享：

- 两个进程共同都作用似乎不大

- 两个进程同时写会有影响，eg：写log，不会存在一个进程把另一个进程覆盖掉的情况。

  

## 第四节

file_table 全局一个，fd_table 每个进程一个。

read before write （由于磁盘每个读写单位比较大）。



#### SYNC

​						SYNC																						 FLUSH

application ------------>file system ----->buffer cache -------> disk driver ----------- > disk cache ---->magnetic disk



#### A Canonical Device

SRAM 一般做cache，DRAM 一般做数据的cache（SRAM快于DRAM）

PIO（Program I/O）



#### interrupt 和 exception 的区别

- interrupt 可重复的，exception 不可重复
- exception 一般是报错（例如除零）
- interrupt 在流水线任何时候都可以发给硬件，而exception只能是在最后memory writeback的时候可以



#### Interrupt 和 Polling 是一对概念 

- livelock solution ：hybrid（混合使用）





#### DMA（直接内存访问） for Disk Device

- 一个插在总线上的设备，是一个能够读写内存的控制器



#### PIO 与 MMIO（Memory Mapped IO）



#### 硬件的寄存器和Memory的区别

- disk register里面的东西是可以被修改的，和Memory两者行为是不一样的。
- cpu先访问的是disk cache，但是cache register可能缓存之后会被修改，disk register是最新的数据，所以对于这部分要 noncacheable （页表上有这个bit）
- 两个地方要考虑：cpu（noncacheable）和编译器（volatile关键字）



​                                                               