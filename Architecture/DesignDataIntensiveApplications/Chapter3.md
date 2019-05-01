# Chapter 3 Storage and Retrieval

## Data Structures That Power Your Database
**Index** is a general idea that keep some additional metadata on the side, which acts as a signpost and helps you to locate the data you want. An index is an additional structure that is derived from the primary data. Any kind of index usually slows down writes, because the index also needs to be updated every time data is written.

This is an important trade-off in storage systems: well-chosen indexes speed up read queries, but every index slows down writes.

### Hash Indexes
Make indexes using HashMap for that appended log storage.
Talk about the file format, deleting records, crash recovery, partially written records, concurrency control.

The idea is quite intuitive.

### SSTables and LSM-Trees
In the native log based storage, the pairs appear in the order that the same key were written, and values later in the log take precedence over values for the same key. Now, a simple change to the format of segment files: we require the sequence of key-value pairs is sorted by key.
We call this format Sorted String Table, or SSTable for short. We require that each key only appear once within each merged segment file.

1. Merging segments is simple and efficient. The approach is like MergeSort algorithm.
2. In order to find a particular key in file, you only need a sparse index that points to some of the values in segments. And search in a range. (Read book for detail)
3. Since read requests need to scan over several key-value pairs in the requested range, it's possible to group those records into a block and compress it before writing it to disk. Each entry of the sparse in-memory index then points at the start of a compressed block. Bersides saving disk space, compression also reduces the I/O bandwidth use.

**How to construct and maintain SSTables**
As the data in the segments are sorted, how to ensure that? The incoming writes can occur in any order.
Maintaining a sorted structure on disk is possible, but maintaining it in memory is much easier. There are plenty of well-known tree data structures that you can use, such as red-black trees or AVL trees. With these data strucutures, you can insert keys in any order and read then back in sorted order.

* When a write comes in, add it to an in-memory balanced tree. This in-memory tree is sometimes called a `memtable`.
* When the memtable gets bigger than threshold, maybe megabytes-- write it out to disk as an SSTable file. The new SSTable becomes the most recent segment of the database. While the SSTable is being written out to disk, write can continue to a new memtable instance.
* In order to serve a read request, first try to find the key in the memtable, then in most recent on-disk segment, then in the next-older segment.
* From time to time, run a merging and compaction process in the background to combine segment files and do discard overwritten or deleted values.

The scheme works well. It only suffers: if the database crashes, the most recent writes(which are in memtable but not yet written out to disk) are lost. In order to avoid that, we can keep a separate log on disk which every write is immediately appended, just like the previous section. That log is not in sorted order, but that doesn't matter. When database recovers from crash, the log can be loaded then. Every time the memtable is written to an SSTable, the corresponding log can be discarded.

**Making an LSM-tree out of SSTables**
Originally, this indexing structure was described by Patric O'Neil et al. under the name Log-Structured Merge-Tree(or LSM Tree). Storage engines that are based on this principle of merging and compacting sorted files are often called LSM storage engine.  

Lucence, an indexing engine for full-text search used by Elasticsearch and Solr, uses a similar method for storing its term dictionary. In Lucene, this mapping from term to document list is kept in SSTable-like sorted files, which are merged in the background as needed.

**Performance optimizations**
The disadvantage of LSM-tree algorithm is that it can be slow when looking up keys that do not exist in the database, you have to check the memtable and all the segments. In order to optimize this kind of access, storage engine often use additional Bloom filters. A Bloom filter is a memory-efficient data strucutre fro approximating the contents of a set.

### B-Trees
The most widely used indexing structure is B-tree. Like SSTables, B-Tree keep key-value pairs sorted by key, which allows efficient key-value lookups and range queries.

B-trees break the database down into fixed-size blocks or pages., traditionally 4 KB in size, and read or write one page at a time. This design corresponds more closely to the underlying hardware, as disks are also arranged in fixed-size blocks.

Each page can be identified using an address or location, which allows one page to refer to another-similar to a pointer, but on disk instead of in memory. We can use these page references to construct a tree of pages. See the figure in the book, it's quite clear.

One page is designated as the root of the B-tree; whenever you want to look up a key in the index, you start here. The page contains several keys and references to child pages. Each child is responsible for a continuous range of keys, and the keys between the references indicate where the boundaries between those ranges lie.

Eventually we get down to a page containing individual keys(a leaf page), which either contains the value for each key inline or contains reference to the pages where the values can be found.

The number of references to child pages in one page of the B-tree is called the **branching factor**. In practice, the branching factor depends on the amount of space required to store the page references and the range boundaries, but typically it is several hundred.

If you want to update the value for an existing key in a B-tree, you search for the leaf page containing that key, change the value in that page, and write the page back to disk(any references to that page remain valid). If you want to add a new key, you need to find the page whose range encompasses the new key and add it to that page. If there isn't enough free space in the page to accommodate the new key, it is split into two half-full pages, and the parent page is updated to account for the new subdivision of key ranges.  This algorithm ensures that the tree remains balanced: a B-tree with n keys always has a depth of O(log n). Most databases can fit into a B-tree that is three or four levels deep, so you don't need to follow many page references to find the page you are looking for. (A four-level tree of 4 KB pages with a branching factor of 500 can store up to 256TB)

** Making B-trees reliable **
* This section describles the write operation in a real disk, how it works.
* Use WAL(write ahead log) to protect database crash.
* Use **latches** (lightweight locks) in multi-thread mode

** B-tree optimizations **
* Instead of overwriting pages and maintaining a WAL for creash recovery, some databases (like LMDB) use a copy-on-write schema. A modified page is written to a different location, and a new version of parent pages in the tree is created, pointing at the new location. This approach is also useful for concurrency control.
* We can save space in pages by not storing the entire key, but abbreviating it. Especially in pages on the interior of the key, keys only need to provide enough information to act as boundaries between key ranges. Packing more keys into a page allows the tree to have a higher branching factor, and thus fewer levels.
* In general, pages can be positioned anywhere on disk; there is nothing requiring pages with nearby key ranges to be nearby on disk. If a query needs to scan over a large part of the key range in sorted order, that page-by-page layout can be inefficient, because a dksk seek may be required for every page that is read. Many B-tree implementations therefore try to lay out the tree so that leaf pages appear in sequential order on disk. Howeer, it's difficult to maintain that order as the tree grows. By contrast, since LSM-tree rewrite large segments of the storage in one go during merging, it's easier for them to keep sequential keys close to each other on disk.
* Additional pointers have been added to the tree. like reference to its sibling pages, which allows scanning keys in order without jumping back to parent pages.
* B-tree variants such as fractal trees borrow some log-structured idea to reduce disk seeks.

### Comparing B-Trees and LSM-Trees
LSM-trees are typically faster for writes, whereas B-trees are thought to be faster for reads. Reads are slower on LSM-trees because they have to check several different data structures and SSTables at different stages of compaction.

** Advantages of LSM-trees **
* Log-structured indexes also rewrite data multiple times due to repeated compaction and merging of SSTables. This effect one write to database resulting in muliple writes to the disk over the course of the databases's lifetime. This is known as write amplification. It is of particular concern on SSD, which can only overwrite blocks a limited number of times before wearing out.
* LSM-trees are typically able to sustain higher write throughput than B-trees, partly because they sometimes have lower write amplification and partly because they sequentially write compact SSTable files rather than having to overwrite several pages in the tree.
* LSM-tree can be compressed better, and often produce smaller files on disk than B-trees. B-tree storage engines leave some disk space unused due to fragmentation: when a page is split or when a row can not fi into an existing page, some space in a page remains unused. LSM-trees are not page-oriented and periodically rewrite SSTables to remove fragmentation, they have lower storage overhead, especially when using leveled compaction.  

** Downside of LSM-trees **
* The compaction process can sometimes interfere with the performance of ongoing reads and writes. Disks have limited resources. The impact on throughput and average response time is usually small, but at high percentiles the response time can be quite high, B-tree can bemore predictable.
* Another issue with compaction arises at high write throughput: the disk's finite write bandwidth need to be shared between the initial write and compaction threads running in background. The bigger the database gets, the more disk bandwidth is required for compaction.
* If the throughput is high and compaction is not configured carefully, it can happen that compaction cannot keep up with the rate of incoming writes.
* An advantage of B-tree is that each key exists in exactly one place in the index, whereas a log-structured storage engine may have multiple copies. Thisi makes B-tree atrtractive in databases that want to offer strong transactional semantics: locks.
* B-tree have good performance ofr may workloads.


### Other Indexing Structures
A secondary index can be constructed from a key-value index. But the key may not unique. This can be solved either by making each value in the index a list of matching row identifiers or by making each key unique by appending a row identifier to it.

** Storing values within the index **
The value of a key in database is either be the actual row or be a reference to the row stored elsewhere. The latter case, the place where rows are stored is known as a heap file, and it stores data in no paricular order. The heap file is common because it avoids duplicating data.

In some situations, the extra hop from the index to the heap file is too much of a performance penalty for reads, so it can be desirable to store the indexed row directly within an index. This is known as a clustered index. In MySQL's InnoDB storage engine, the primary key of a table is alwasys a clustered index, and secondary indexed refger to the primary key.

A compromise between a clustered(storing all row data within the index) index and a non-clustered index(storing only references to the data within the index) is known as a covering index or index with included columns, which stores some of a table's columns within the index. But this can cause duplicated data and add overhead on writes.  c c

** Multi-column Indexes **
The most common type of multi-column index is called a concatenated index, which simply combines several fields into one key by appending one column to another.
> This book doesn't discuss too much on how to implement multi-column index

```sql
select * from restaurants where latitude > 51 and latitude < 51
                           and  longitude > -0.1 and longitude < 0.1
```
A standard B-tree or LSM-tree index is not able to answer that kind of query efficiently. One option is to translate a two-dimensional location into a single number using a space-filling curve, and then use a regular B-Tree index. More commonly, specialized spatial indexes such as R-tree are used.


## Transaction Processing or Analytics
**Transaction** processing just means allowing clients to make low-latency reads and writes, as opposed to batch processing jobs, which only run periodically.
**OLTP**: Online Transaction Processing
**OLAP**: Online Analytic Processing


Property | OLTP | OLAP
-- | -- | --
Main read pattern | Small number of records per query, fetched by key | Aggregate over large number of records
Main write pattern | Random-access, low-latency writes from user input | Build import(ETL) or event stream
Primarily used by | End user/customer, via web application | Internal analyst, for decision support
What data represents | Latest state of data | History of events that happen over time
Dataset size | Gigabytes to terabytes | Terabytes to petabytes


### Data Warehousing
Run the analytics on a separate database instead. This is called data warehouse.

These OLTP systems are usually expected to be highly available and to process transactions with low latency, since they are often critical to the operation of the business. Database administrators therefore closely guard their OLTP databases. They are usually reluctant to let business analysts run ad hoc analytic queries on an OLTP database, since those queries are often expensive, scanning large parts of the dataset, which can harm the performance of concurrently executing transactions.

On the surface, a data warehouse and a relational OLTP database look similar, because they both have a SQL query interface. However, the internals of the systems can look quite different, because they are optimized for very different query patterns. Many database vendors now focus on supporting either transaction processing or analytics workloads, but not both.

### Stars and Snowflakes: Schemas for Analytics
Many data warehouses are used in a fairly formulaic style, known as a **star schema**(also known as dimensional modeling)

## Column-Oriented Storage
If you have trillions of rows and petabytes of data in your fact tables, storing and quering them efficiently becomes a challenging problem. Although fact table are often over 100 columsn wide, a typical query only access 4 or 5 of them at one time.

The idea behind column-oriented storage is simple: don't store all the values from one row together, but store all the values from each column together instead. If column is stored ins separate file, a query only needs to read and parse those columns that are used in that query, which can save a lot of work.

The column-oriented storage layout relies on each column file containing the rows in the same order. Thus, if you need to reassemble an entire row, you can take the 23rd entry from each of the individual column files and put them together to form the 23rd row of the table.

### Column Compression
We can further reduce the demands on disk throughput by compressing data. And column-oriented storage often leads itself very well to compression.

The most widely used technique is **bitmap encoding**
For detailed algorithm, please google it. It's not very clear in the book.

### Sort Order in Column Storage

### Writing to Column-Oriented Storage
Column-oriented storage, compression and sorting all help to make those read queries faster. However, they have the downside of making writes more difficult.

An update-in-place approach, like B-tree, is not possible with compressed columns. If you want to insert a row in the middle of a sorted table, you would most likely have to rewrite all the column files. As rows are identified by their position within a column, the insertion has to update all columns consistently.

We can use LSM-trees(Log-Structured Merge) tree idea. All writes first go to an in-memory store, where they are added to a sorted structure and prepared for writing to disk. It doesn't matter whether the in-memory store is row-oriented or column-oriented. When enough writes have accumulated, they are merged with the column files on disk and written to new files in bulk.

Queries need to examine both the column data on disk and the recent writes in memory, and combine the two.

### Aggregation: Data Cubes and Materialized Views
