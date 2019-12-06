# Reading--Eraser

Q1: According to the lockset algorithm, when does eraser signal a data race? Why is this condition chosen?

A1: When the intersection of the candidate set and held_locks is empty. Because we need to guarantee each shared variable is only associated with one lock.



Q2: Under what conditions does Eraser report a false positive? What conditions does it produce false negatives?

A2: 
False positive: for the first and simplest version of Eraser, Initialization and Read-Sharing will report a false positive.
False negative: when the thread use the non-standard lock to lock the shared data, it will report a false negative.



Q3: Typically, instrumenting a program changes the intra-thread timing (the paper calls it interleaving). This can cause bugs to disappear when you start trying to find them. What aspect of the Eraser design mitigates this problem?

A3: All shared variables will be monitored by Eraser.  That will migrate the problem.



Q4: Please raise at least one question of your own for the discussion.

A4: I think Eraser’s holding informations are not enough. It can’t distinguish between global and local locks.