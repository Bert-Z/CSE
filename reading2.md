# FFS : Fast File System

### What is the problem this paper tried to solve? (Hint: please state the problem using data)

The main problem was that the free list quickly became scrambled as files were created and  removed. This forced a seek before every  block access.

Although the old file systems provided transfer rates of up to 175  kilobytes per second when they were first created, this rate deteriorated to 30  kilobytes per second after a few weeks of moderate use because of this randomization of data block placement. 



### What is the root cause of the problem? In one sentence.

The allocation of the data to blocks is random after a long time moderate use.



### For each optimization, how does the author get the idea? (Hint: just imagine you’re the author. Given the problem, what experiment would you do to get more info?)

Firstly, as the old block size (512 bytes) is too small for allocating large files, we need to enlarge the block size to 4096 bytes, which will help for allocating a large file in less blocks. However, larger the block is, more space wasting caused by a large amount of small files. Thereby, author seperated the blocks into more fragment to increase the utilization rate.

Secondly, inorder to adapt to different file systems and improve the efficiency of seeking, parameterizing the processor capabilities and mass storage charateristics is essential.

Thirdly, as we all known, it is much more efficient to allocate files in one directory or one large file in the nearest block, but not randomly. We are supposed to optimize the layout policies and groups, so that the author put forward the cylinder groups and divide the layout policies into two distinct parts.



### For each optimization, does it always work? In which case it cannot work?

Optimizing Storage Utilization:

A finer grain origanization will cause more overhead. File system need more spaces to storage the block and fragment metadatas, which will be calculated as wasted resources.

File system parameterization:  

If a file system is parameterized to lay out blocks with a rotational separation of 2 milliseconds, and the disk pack is then moved to a system that  has a processor requiring 4 milliseconds to schedule a disk operation, the  throughput will drop precipitiously because of lost disk revolutions on nearly every block. 

Layout policies:

It will also cause many wasted storage resources to allocate the global and local policy metadata.



### Do you have some better ideas to implement a faster FS?

1) IO performance is also an important part to faster FS. Caching the files before writing on the disk and using writeback policies to optimize the allocation.

2) Writing some small files after inode.



