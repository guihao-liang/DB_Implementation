# 3/8/2016
---
# CH8
## Keywords
> familiar with: external storage, buffer manager, page I/O,
 file organization, heap files, indexes, data entries, __search keys__, clustered file, primary index, index organization, hash-based and tree-based indexes.


> unfamiliar: __file organization and common operations, performance tuning, workload, composite search keys, use of clustering__

* external storage  
 1. persistent
 2. external storage devices
 3. read and write unit with multiple of pages.


* buffer manager
 1. data is read into memory for processing, and written to disk for persistent storage by a layer called buffer manager.
 2. the space on the disk is managed by the disk space manager.


* file
 1. a file of records is an important abstraction.
 2. c collection of disk pages.


* index
 1. a data structure that organizes data records on disk to optimize certain kind of retrieval operations.
 2. retrieve records that satisfy search conditions on the search key fields of the index.
 3. organization: <k, \*>, <k,rid>, <k, rid-list>
 4. special file organization.
 5. the search key for an index can contain several fields; such keys are called __composite search key or concatenated keys__.


* __clustered & unclustered__
 1. when the file is organized so that the ordering of data records is same as or close to the ordering of data entries in some index.
 2. clustered file has to reserve the space for further insertion. Occupation of certain page can be 2/3.


* index & primary & secondary
 1. __two data entries have same value on the search key field associated with the index.__
 2. unique index is that search key field contains candidate key.
 3. primary index: index on set of fields containing primary key. # search key field + primary key. The rest is secondary key.


* hash-based indexing
 1. hashing -> buckets
 2. gracefully on the insert and delete.
