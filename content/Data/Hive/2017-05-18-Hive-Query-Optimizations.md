---
title: Hive Query Optimizations
layout: post
tags: ["hive", "bigdata"]
---

### Changing Engine for Hive Queries

In general, Tez provides increased performance over Map Reduce specially where JOINS are required resulting in intermediate output being written to disk.

```
set hive.execution.engine=tez
```

### Using Partitions

Partitions separate date in Hive tables by HDFS directories. If data is distributed based on partitions on certain fields, Using those fields to access data allows speeding up Hive queries. In other words, querying without partitions equates to full table scan.

### Splits and Number of Files

Considering splits and number of files created while doing joins helps to analyze query performance.

### Performance Considerations for using `JOINS`

Joins are important aspects of the SQL queries. Avoid using correlated queries and in-line tables. Create temporary tables and try to use inner join wherever possible. You can also use the Hive WITH clause instead of temporary tables

Also you should avoid any queries that leads to CARTESIAN JOINS.

### Avoid loading Too Many Small Files

Hadoop works well with large files and it applies to Hive as well. You should avoid ingestion process that produces large number of small files. When producing outside Hive, use Text file format. Once you have data into Hive tables, then you can convert that to ORC or Parquet file format.

### Choose Appropriate File Format for the Data

Choosing right file format will improve the Hive performance. Hive supports ORCfile, a new table storage format that sports fantastic speed improvements through techniques like predicate push-down, compression etc. ORC file increases esponse times for your HIVE queries.