# Chapter 3 Storage and Retrieval

## Data Structures That Power Your Database
**Index** is a general idea that keep some additional metadata on the side, which acts as a signpost and helps you to locate the data you want. An index is an additional structure that is derived from the primary data. Any kind of index usually slows down writes, because the index also needs to be updated every time data is written.

This is an important trade-off in storage systems: well-chosen indexes speed up read queries, but every index slows down writes.

### Hash Indexes

### SSTables and LSM-Trees

### B-Trees

### Comparing B-Trees and LSM-Trees

### Other Indexing Structures


## Transaction Processing or Analytics

### Data Warehousing


### Stars and Snowflakes: Schemas for Analytics

## Column-Oriented Storage

### Column Compression

### Sort Order in Column Storage

### Writing to Column-Oriented Storage

### Aggregation: Data Cubes and Materialized Views
