###  What problem does this paper want to solve? In one sentence. 

Instead of using *flush* guarantee order, the paper introduces a new interface to provide *async durability notification*, which is the order of operations guaranteed when writing, and there is no need to refresh the cache.



###  What is Asynchronous Durability Notification(ADN)? Why is it useful for implementing optimistic crash consistency? 

The actual write order and persistence have no effect, only the crash makes sense, so the current disk interface actually limits IO performance. If the disk is no longer concerned with the read and write order, the performance is optimized under the common case.

After canceling the order, in order to maintain consistency and durability, the author suggests that the disk add a minimal extension, ADN, to notify the superior that the specific write request has been completed and persisted.

When writing a request, ADN is used for asynchronous notification;

When a persistent write such as FUA is provided, a synchronization notification is provided, which is issued when the persistence is completed. Tagged Queuing (intermediately inserted command called TCQ) also allows limited requests to be processed at specified points in time. (SCSI supports the above TCQ and FUA, but many drivers do not support it, TCQ information is difficult to check, the original link expires as if).

Although FUA and TQ are sufficient for optimistic consistency, the authors believes that decoupling request validation and persistence can achieve better IO concurrency.



###  Why does this paper propose two sync-related calls (osync and dsync)? Please give some examples where osync or dsync is suitable, respectively. 

To understand the coupling of durability and consistency, the author has made a certain interface design, called osync and dsync, where o means order and d means durability. Osync may be implemented by ADN, followed by the former write and other persistence notifications.



eg.

 We now define and compare the guarantees given by osync() and dsync(). Assume the user makes a series of writes W1,W2,...,Wn. If no osync() or dsync() calls are made, there is no guarantee as to file-system state after a crash: any or all of the updates may be lost, and updates may be applied out of order, i.e., W2 may be applied without W1.

Now consider when every write is followed by dsync(), i.e., W1,d1,W2,d2,...,Wn,dn. If a crash happens after di , the file system will recover to a state with W1,W2,...,Wi applied. 

If every write was followed by osync(), i.e., W1,o1,W2,o2,...,Wn,on, and a crash happens after oi , the file system will recover to a state with W1,W2,...,Wi−k applied, where the last k writes had not been made durable before the crash. We term this eventual durability. Thus osync() provides prefix semantics , ensuring that users always see a consistent version of the file system, though the data may be stale. Prior research indicates that this is useful in many domains . 



