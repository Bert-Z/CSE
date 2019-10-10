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



#### PIO(in/out) 与 MMIO(load/store)（Memory Mapped IO）



#### 硬件的寄存器和Memory的区别

- disk register里面的东西是可以被修改的，和Memory两者行为是不一样的。
- cpu先访问的是disk cache，但是cache register可能缓存之后会被修改，disk register是最新的数据，所以对于这部分要 noncacheable （页表上有这个bit）
- 两个地方要考虑：cpu（noncacheable）和编译器（volatile关键字）



​                                                               

## 第五节

#### MTTF & AVALIABILITY

​	MTTF: mean time to failure

​	MTTR: mean time to repair

​	MTBF: mean time between failure

​	MTBF=MTTF+MTTR

#### Redundancy

- Coding for incremental redundancy
  - Hamming distance
- Replication in bridge building
- Voting  
- Repair

#### Durable Storge

- RAID 1 : 两份一样的，并且有一个clerk，隔段时间check一下是否一样 
- RAID 4 : (Dedicated Parity Disk) 通过几个disk计算出一个disk 作为 parity
- RAID 5 : (Spread Out the Parity)

#### SYNC 

- sync 会直接让数据直接写到硬盘里的，不会写到block cache里



## 第七节

#### The BUS

MMU（将physical address转换为virtual address）在CPU里面，出了cpu用的就是physical address，所以bus上的地址是physical address。



#### Bus Transaction

Sync	--	a shared clock

Async	--	an acknowledge line : ready & acknowledge



#### physical address assigned

- Memory physical address bu **BIOS**
- Some devices : fixed for all time
- ...



#### logging / journaling (for crash consistence)



Workload: Creating and writing to a file



Full journal mode (data and meta data all write in journal area)	--	too slow

Ordered journal mode (only write metadata to journal area)	--	default data may be overwriten

Data Wirte (D)	->	Logging Metadata(Jm)	->	Logging Commit(Jc)	->	Checkpointing(M)



#### journaling with ordering

using FLUSH to guarantee the file writing order on disk platter

Data Wirte (D)	->	Logging Metadata(Jm)	->	Flush	->	Logging Commit(Jc)	->	Flush	->	Checkpointing(M)



#### journaling without ordering

Tech #1:	checksums





## 第八节

#### RPC

Message

- service id
- service parameter
- using marshal / unmarshal



Request & Reply



marshal & unmarshal



Client Framework

-  **need to handles timing out** 

Server Framework

会传绝对的地址（从哪里都到哪里）



#### NFS（stateless）



没有记任何的状态，所以需要一个Generation number来确保file system 的 reuse 问题



#### 为什么不放pathname

避免reuse的问题



#### rename after open 

没关系，因为已经拿到fd了，已经找到了对应关系，不需要pathname这一层了。



#### stateless on NFS server

- reply cache	（断电，所以还得做幂等）
- server fail between two same requests	--	做成幂等，通过绝对地址查找



#### delete after open 

通过generation number来避免读别的文件，（删除文件后如果是新的文件Generation number不一样）



vnode



