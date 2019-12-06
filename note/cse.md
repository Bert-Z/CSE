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



## 第九节

#### RPC: Failure Handling

- at least once

- at most once

- exactly once

  

#### Why RPC and C/S?

- programmers make mistakes
- mistakes propagate easily
- enforce modularity



#### What does RPC do,exactly?Can we use other ways?

- Be more friendly for programmers.Don't need to learn many things like socket , http and so on.



#### GFS

flatter namespace在本地的缺点：文件太大（可能会是G级别的），在本地的话占用资源较多，master是只有这个简单的逻辑，所以不影响。

flatter namespace在master的缺点：ls(只需要跟master通信即可)会比较慢，搜table，需要每个都查找一遍；rename directory会比较慢，也需要查找所有的directory。解决方案：存成树的结构。



#### GFS Cluster

Read & Write

Write 不需要sync的原因：三备份（三台server机器），所以不需要写磁盘，同时崩掉的概率很低。

client会cache chunk server的data，不是数据，而是存在哪个chunk server，可以不用去master



磁盘顺序写和随机写速度相差100倍的量级，随机写太慢了。 



#### Content Distribution

content distribution network(CDN)

domain name service(DNS)





## 第十节

#### DNS



### Network



#### Link Layer

physical transmission using shared clock

physical transmission without shared clock





## 第十一节

#### Manchestor Code

0->01      1->10



Isochronous(等时的) - TDM



#### Error Handling

error detection code : eg: **checksum** at the end

what to do then (depend on the real condition)

- correction code
- ask sender to resend 



#### checksum

**Hamming distance** : longer the distance is, safer the code is.



### Network Layer

IP: Best-effort network

duplicate package



#### Routing

routing table (IP-NIC(port) table)

control-plane vs data-plane



send : network_send->network_handle->link_send

receive : link_receive->network_handle



#### NAT

NAT router : bridge the private network

mapping between private IP and port



































## Multiple transaction

### Two-phase Commit

phase-1: preparation/voting

phase-2: commitment



3-N protocal

​                                                                                                                                                                                                                                                                                                                                                                                        







sharding: use mutiple machines to replace one machines -> MTTF lower









### View Server

each contains the view number , the primary server and the backup server

will alert which one is primary server.

each server will sent heart beats to the view server.



Some failures

before S2 knows it's primary, it'll **reject** any requests from clients





### Paxos

