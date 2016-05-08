# 3/8/2016
# CH12-CH15
---
## CH13 EXTERNAL SORTING
* MERGE SORTING
 1. first run to individually on given memory size.
 2. merge pass by pass by factor F. B is the buffer size for sorting.
 3. formula: 2|R|(log(|R|/B, F) + 1, roughly O(Nlog(N))
 4. minimizing the initial run: replacement sort, which can write out runs in approximately 2B
 5. double buffering: keep CPU busy.
 6. blocked I/O: reduce the number of the I/O request.
---
## CH14 EVALUATION RELATIONAL OPERATORS
* SELECTION
 1. with index or not.
 2. hash based index 1-2; B+ index: 2-4

* PROJECTION to DISTINCT
 0. read the only desired attributes.
 1. SORTING.
 2. HASHING h1 for partition, h2 for hash table to discard.
 3. discard duplicate.
 4. the sorting-based approach is superior to hashing if we have many duplicates or if the distribution of hash values is very non-uniform.

* JOIN
 1. blocked nested join: |R|\*|S|/(M-1) + |S|
 2. index nested join: works well only on condition that the outer R is small.
 3. sort merge join. 3(R+S). duplicates can be treated as a partition. Join partition with partition with same value.
 4. hash join -> similar to distinct hash projection. 3(R+S).
 require B > sqrt(R) to fit into hash table.
 5. hash join is better than blocked, cause each block of R corresponds to corresponding block of S.
 6. if data is uniform, hash is better than sort. Otherwise, sort wins.

* UNION & DIFFERENCE
 1. Sort R and
         union: merge them, eliminate the duplicates.
         diff: entry in R do not appear in S, similar to sort join.   
 2. Hash R and S
         hashing partition
         build im-memory hash table on corresponding partition.
         union: write out the hash table.
         diff: not in table.

* AGGREGATION
 1. GROUP BY SUM MIN MAX AVG COUNT
         SORT: Mlog(M)
         HASHING: M
         INDEX:
         1. If have index on the group by search key, use that instead of sorting, depends on clustered index or not.
         2. If search key in the index contains all the attributed needed for aggregation query, can use the data set of the index instead of all the records.
