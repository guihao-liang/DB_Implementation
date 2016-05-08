# 3/8/2016
# CH9-CH11
---

Assumption: the records can fit int one page.

---
## CH9
### DISk
---
* Magnetic DISk
 1. in units called block
 2. cylinder, platter, sector (abstracted as page in file system)
        > disk access 10 ms, main memory 60 nanoseconds.

 3. seek time is the time taken to move the disk heads to the track on which a desired block is located.
 4. rotational delay is the waiting time for the desired block to rotate under the disk head.
 5. transfer time is the time to actually read and write the data in the block once the head is positioned, that is, the time for the disk to rotate over the block.
---
* __RAID & Dara Striping__  
 1. Redundancy Arrays of Independent Disks
 2. improve the reliability and concurrent access.
 3.
 __data striping__: data is segmented into equal-size partitions distributed over multiple disks.  
 __striping unit__: the size of the partition.  
 __round robin__:if the disk contains of D disks, then the partition i is written onto disk i mod D.  
 4. RAID0: non-redundancy, in order to improve the bandwidth.  
 5. RAID1: mirroring, reliability.
 6. RAID2-3: parity.
 ---
* Disk space manager
 1. __Abstrction__: support the __page__ as the a unit of data.
 2. the size of the page is chosen to be the size of a disk block and pages are stored as disk blocks so that reading or writing a page can be done in on disk I/O
 3. keeping track of the free space on the disk.
 4. OS file system or build from scratch. portability. self-containded.
---
* Buffer Manager
 1. the main memory pages in the buffer pool is called __frame__.
 2. __pin_count__: the number of current users of the frame (number of times current requested but not released)
 3. __dirty__: frame has been modified.
 4. replacement policies.
---
* Record formats:
 1. given record type is stored in the system catalog, which avoids repeatedly storage of same information.
---
* __B+ Tree__
 0. usually 1-2 pages to retrieve data.
 1. order of the tree. d<= keys <= 2d
 2. bulk-loading
 3. overflow pages on leaf page to deal with duplicates (like the linked list way, also apply for the hash table).
---
* __Hash based indexing__
 1. extensible hashing and linear probing with overflow buckets.
 2. just a way to distribute the data more evenly. 
