# 4/4/2016

## Assumption
1. n nodes
2. Table R and S are evenly split among n nodes.
3. no pre-existing locality.

R|S   R|S   R|S ...

## Proposal 3 Hashing

For each node, hash on the join key to locally split table into n sets. hash1(key): 1-> n. Send portion i to node i for next step hash join. For each node, use hash2 to hash join on intermediate results.

It's similar to hash join.

For finer granularity, each node hash1(key): 1 -> n*k. In this case, hash2 can receive smaller data for further hash join. The benefit is the hash table may fit into cache in order to get faster procession.

## Proposal 4 global RAM with hashing.

This method is similar to proposal 3 but slightly different in choosing K. In this method, K is first computed to fully utilize the global RAM size (one over K of data can fully fit in to global RAM).

n*|RAM local| = |RAM global|
K = |S| / |RAM global|

1. locally partition the data into K pieces at each node.
2. for each part in turn, send partition from S to remote sites to build in-memory hash table.
3.


Advantage:
 1. pipeline (join)
 2. more efficiently use local RAM.
 3. network volume is similar to proposal 3.
