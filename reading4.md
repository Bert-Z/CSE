###  Use your own word to describe “end-to-end” argument. 

End-to-end reliable communication can only be guaranteed by the application layer at both ends of the communication, while middleware can only improve efficiency, and can not guarantee the reliability of communication.



###  Give at lease three cases that are suitable to use this principle. 

1. End-to-end caretaking: there are still many threats when we trying to trust tcp or other low-level data transfer. Thus the "end-to-end check and retry" is much more reliable, which can prove to make careful file transfer.
2. Delivery guarantees :  The reason is that knowing for sure that the message was delivered to the target host is not very important. What the application wants to know is whether or not the target host acted on the message; all manner of disaster might have struck after message delivery but before completion of the action requested by the message. The acknowledgement that is really desired is an end-to-end one, which can be originated only by the target application – "I did it", or "I didn't."  
3. Duplicate message suppression :    A property of some communication network designs is that a message or a part of a message may be delivered twice, typically as a result of time-out-triggered failure detection and retry mechanisms operating within the network.  These application level duplications look like different messages to the communication system, so it cannot suppress them; suppression must be accomplished by the application itself with knowledge of how to detect its own duplicates. 



###  Give at lease three cases that are NOT suitable to use this principle. 

1. Speech message system :   in which the voice packets are stored in a file for later listening by the recipient, the arguments suddenly change their nature.  Short delays in delivery of packets to the storage medium are not particularly disruptive so there is no longer any objection to low-level reliability measures that might introduce delay in order to achieve reliability.   
2. The bottom layer is inherently reliable, such as some high-precision equipment. They already guarantee reliability in the underlying circuitry and do not need to manage these at the top.
3. Anonymous communication : In some cases, users may prefer to be anonymous, for example, when viewing a Web site, anonymous political speeches and whistleblowers want to keep their privacy. At least in the United States, the privilege of anonymous public political speech is a protected right. In this regard, speakers will seek assurance that their anonymity cannot be penetrated, both at the time and afterwards. 



###  [Discussion] Consider the design of the file system based on inode. Which part(s) of the design do you think can be removed? In another word, which part(s) of the design is(are) not flexible enough and should be implemented by the user? (Hint: don’t be limited by the FS API.) 

The error recovery function of the file system is backed up by logs, which affects the performance of the system to a certain extent, and is not necessary for the file system.



###  [Discussion] The same question, for the OS. 

Microkernel : Memory only implements the abstraction of the smallest set, while other redundant modules are provided by third parties, running in user mode. This can greatly reduce the bugs in the kernel, and can also limit the module's errors to that module. Its errors will not affect the kernel and improve fault tolerance. And because some of the modules that were originally running in kernel mode are now running in user mode, context switching is reduced and, to some extent, speed is improved.