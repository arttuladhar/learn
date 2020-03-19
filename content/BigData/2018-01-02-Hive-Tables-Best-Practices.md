---
title: Hive Tables [Best Practices]
layout: post
tags: ["hive", "bigdata"]
---

## Hive Table Storage Formats

Avoid using TEXT format, Sequence file format or complex storage format such as JSON. Ideally, RCFile (Row Columnar File) or Parquet files are best suited. If you are building data warehouse on Hive, for better performance use Parquet file format.

```sql
CREATE TABLE IF NOT EXISTS test_table (
  col1 int,
  col2 string )
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS PARQUET;
```

## Compression Techniques

Try to split compression algorithms provided by Hadoop & Hive like Snappy. Also you should avoid Gzip because it is not splittable and is CPU intensive.

```sql
CREATE TABLE IF NOT EXISTS test_table (
col1 int,
col2 string )
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS PARQUET
TBLPROPERTIES ("parquet.compress"="SNAPPY") ";
```

```
set hive.exec.compress.output=true;
set hive.exec.compress.intermediate=true;
```

## Partition Tables

Partition will significantly reduce the table scan time. You should partition large Hive tables if you are collecting time series data. Hive Partition will store data in subdirectories like year/month/day. When you query the data, instead of scanning entire table, Hive will go to particular subdirectory and get you required data.

## Bucketing Tables

Hadoop Hive bucket concept is dividing Hive partition into number of equal clusters or buckets. Hive Buckets distribute the data load into user defined set of clusters. Hive distribute on hash code of key mentioned in query. Bucketing is useful when it is difficult to create partition on a column as it would be having huge variety of data in that column on which we want to run queries.