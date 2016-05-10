# 3/23/2016

## Problem: latency in RAM instead of DISK
### memory hierarchy
* register -> L1 cache -> L2 cache -> L3 cache -> RAM (main memory)
* **cache line** from main to L3 is 64 bytes. Latency from memory is 200 cycles to bring data into CPU.    

> solution: pre-fetching.   
CPU brings objects like arrays in advance into cache and utilizes predictable access pattern to overlap the latency.

### locality
* temporal: we don't use replacement policy as buffer pool controlled by hardware because in cache, things goes so fast, we don't have time to compute.
* spacial: predictable data structure. When querying a node of B+ tree, we know we need to fetch 4k of the block data.

### Branching And Data Caching
* B+ Tree (Branching Factor)

| node    | key     | pointer   | BF    |
| :------------- | :------------- | :------------- | :------------- |
| 4K       | 4B       | 4B      | 512   |

If used the binary search to find the right key with cache line, at most 7 misses to find the right key. log2(512/2)

* 64 Bytes node (choose B+ tree node to match cache line)

7 keys and 8 pointers

| miss    | BF     |
| :------ | :----- |
| 1       | 8      |
| 2       | 64     |
| 3       | 512    |

Convert the 4k node B+ tree into above 3 level tree can reach same Branching Factor. Store 4k in disk and convert it in memory. Can fully utilize the cache line size.

* Increase the BF   

|2N keys | 1 pointer| -> 2N+1 BF. Assume the ptr is of same size of key.

Pointer points to the starting child node. All children will be stored continuously and compute the offset accordingly by their order.

Update: splitting node will globally affect other nodes.   
Space: Since the children nodes are continuously allocated, there will be more waste on space (2N + 1 children potential but less in reality).

* extension: 2 cache lines.

Two cache line can simultaneously probe the node.

16 keys | 15 keys | 1 pointer

| misses | BF   | Visible misses |
| :----- | :--- | :------------- |
| 2      | 32   | 1              |
| 4      | 1024 | 2              |
....

utilize the parallelism and overlap the latency of miss. Throughput is about how many real misses at once for a whole transaction.

**Throughput**: requests finished per second.

If you take up all cache lines, other request can't proceed, so the throughput will be low even though the response time for certain transaction is decreased.

### Instruction cache
* L1 instruction cache 32 KB: Why not big with tech improves?   
> Tradeoff between time and size.   
L1 is supposed to be really fast since you want to get data in 2 cycles. By adding bigger size, addressing may add extra overhead, and may take 3 or 4 cycles.

* interleaving vs batch.

pipeline join:
S join R -> join M -> join N -> result.
Instruction 1 -> instruction 2 -> instruction 3

interleaving may introduce new code to evict previous instructions out of cache. So the extra overhead of switching cache instruction is introduced. So a batch of record to be processed in a pipeline is better than interleaving on every single record.

* Instruction decode

[CPU pipelining](http://cs.stanford.edu/people/eroberts/courses/soco/projects/risc/pipelining/).

Instruction can be decoded into micro instructions so that CPU can pipeline these micro instructions to make processing faster. But **condition branching (if else)** will poison this scheme and leads to a CPU bubble. If the pipeline loads the code for true, while the real condition is false, the part of the pipeline should be aborted.

CPU pipeline: when branch misprediction at instruction 4.   
> |6|5|4|3|2|1| -> 4|X|X|X|3|2|1

# 3/28/2016

## with or without prediction: &&, &

* with
```c
for i to N {
        // Ci is short for Condition i
        if (C1 && C2 && C2)
                output[record++] = i;
}
```

| Condition | occur rate  |  misprediction  |
| :---------| :-----------| :-------------- |
| C1        |     0.5     | .5              |
| C2        |     0.7     | .5 \* .3        |
| C3        |     0.2     | .5 \* .7 \* .2  |

**Note**: for C3, CPU will predict ~C3, which has .8 occur rate.


* without

```
for i {
        c = C1 & C2 & C3;
        output[r+=c] = i;
}
```
Without prediction, the time should be constant independent with the selectivity of conditions. But no benefit is free, this method will write i to output every time.

Also, the answer of this method is not same as `&&` above. It's just a demonstration.
