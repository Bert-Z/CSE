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

