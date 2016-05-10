# 3/21/2016

## Prolog
Recovery manager of a DBMS is responsible for ensuring two important properties of transactions: Atomicity and durability. It ensures atomicity by undoing the actions of transactions that do not commit and durability by making sure that all actions of of committed transactions survive system crashes.

## Failure types
* power outage
* disk failure:
 1. severe -> backups (eg. tapes).  
 2. light -> RAID (Redundant Disks of Inexpensive disks)
* Hardware failure: RAM goes down
* Natural disaster: off site replication.

## Assumption of later lecture
* Everything in the RAM gets lost.
* storage works fine.

## Where problem comes from
* no-force: data should be in disk, but remains dirty in RAM.
* stealing: data of uncommitted transactions being evicted to disk due the buffer management policy.

## Log
* A file records stored in stable storage, which is assumed to survive crashes.
* recovery info is sequentially appended to log file for each transaction.
* write ahead log: log must be written to disk prior to any change to the disk.
* **log tail**: the recent portion of the log and is kept in main memory. It's periodically forced to stable storage or when a transaction decides to commit.
*  **LSN**: unique id for each log record,log sequential number.
* **pageLSN**: every page in the database contains the LSN of the most recent log record that describes a change to this page.
* __linked list__ is used to maintain transactions: prevLSN, transID, and type.

## what is commit?
* transactions commit in the log are committed transactions.
* After `end` is recorded in log for a committed transaction, the transaction is finished.

## ARIES (Algorithms for Recovery and Isolation Exploiting Semantics)
* Assume transactions are under strict 2PL.
* 3 phases: Analysis, Redo and Undo.
* designed for no-force and stealing.

* __check points__ stores the status of the database at certain time, including all info in RAM, like   
__transaction table__: all active transactions, __lastLSN__, which is the LSN of the most recent log record for this transaction.   
__dirty page table__: __recLSN__, which is the __first__ log record that caused the page to become dirty.

* write ahead: **old and new value**   
Any change to a database is first recorded in log.

* Log the changes during undo. **Note**, you don't have to undo an undo. Take some time to think about.

#### CLR (compensation log record)   

__CLR__ is written just right before the change recorded in an update log record is undone. Avoid undo again when restart again. Also, tell the ARIES the undo process so far. 

__linked list__ structure: undoNextLSN: read form prevLSN field of LSN undone.   

CLR will be never undone again when repeating restarts occurs.
__If the action that CLR describes is not written to disk, CLR will be redone in redo phase__.

## Analysis phase
1. determine the point at which to start redo.
2. determine dirty page by restore dirty page table at checkpoint.
3. identify which transactions are active by restore transaction table at checkpoint.
4. Scans the log forward to update these tables.
5. `end` log for a transaction to determine whether it should be removed from the transaction table.


## Redo: recover dirty page.
**important**: page in RAM has not beed propagated to disk.
1. starts from the smallest recLSN of all pages in dirty page table.
2. not to redo conditions:
 1. affected page is not in dirty table. // all changes of this page have been written to the disk.
 2. recLSN > LSN
 3. pageLSN (retrieved from disk) >= LSN. // checked last becase it requires read page from disk.

## Undo: clear losers.
* loser transactions: active at crash time.
* choose the largest lastLSN of loser transaction set.
* abort is a single transaction to be undone instead of a set of transactions.
