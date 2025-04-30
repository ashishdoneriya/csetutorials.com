---
title: SSTable Architecture Summary
date: 2021-03-10T10:38:50+05:30
lastmod: 2022-05-03T09:22:32+05:30
description: Overview of Sorted String Table Architecture
slug: sstable-architecture
categories:
  - System Design
---
## Key Points

1. SSTable or Sorted String Table is used to store key values.
2. In SSTables, key-value pairs are written in sorted order in disk.
3. For faster access to value of a key, indexes are maintained for SSTable. Lets call it SSIndex.
4. We keep copy of SSIndex in RAM for faster access.
5. There is a special property of SSTable that is they are immutable. It means we cannot append or delete data to SSTable so as to maintain sorting order and read speed.
6. If we cannot modify the sstable then we have to overwrite the sstable if we want to modify the data. But if we do that then the data write speed would be very-very slow.
7. To counter this problem we maitain a table known as MemTable in RAM (as we know that writing data in RAM is very fast).
8. If we have to write data then we would write data in MemTable.
9. If we have to read data then first we would check memtable for that record. If record is not available then we would search into sstable.
10. If we have to delete data then we mark the record as deleted using a flag or something like that in memtable. This is because lets say if you completely delete the record from memtable and if a search query comes up for that record then in this case it would check both memtable and sstable. But if we mark that record as deleted in memtable then the user won't have to search sstable also.
11. Periodically we flush the memtable to disk.
12. There is drawback in this approach. In case of unexpected system crash or shutdown the data in memtable will be lost. Therefore for recovery purpose we write every data write or delete request in a log file.


## Why we maintain sorting order in SSTable ?

As I told in the above points that we keep the index of sstable in ram. What if there are billions of record. In this case RAM won't be able to hold all the keys.

To counter this problem what we would do is that we would not store all the keys in index. Lets say there are 10 keys from 1 to 10. In RAM index we will save 1, 5. So if the user requests to fetch value for key 8, then in index we check a key which is just less than 8 that is 5. Now from key 5 to the next key that is 10, we search our key (8).


**Sources :** 
* [https://medium.com/swlh/log-structured-merge-trees-9c8e2bea89e8](https://medium.com/swlh/log-structured-merge-trees-9c8e2bea89e8)
* [https://www.igvita.com/2012/02/06/sstable-and-log-structured-storage-leveldb/](https://www.igvita.com/2012/02/06/sstable-and-log-structured-storage-leveldb/)
* [https://dev.to/codingblocks/designing-data-intensive-applications-sstables-and-lsm-trees](https://dev.to/codingblocks/designing-data-intensive-applications-sstables-and-lsm-trees)

**Read More :**
* [https://blog.yugabyte.com/a-busy-developers-guide-to-database-storage-engines-the-basics/](https://blog.yugabyte.com/a-busy-developers-guide-to-database-storage-engines-the-basics/)
* Compaction strategies for SSTable.