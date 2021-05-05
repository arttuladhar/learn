---
title: Spark
---

## Overview

* Apache Spark is an open source data analysis framework that uses clusters of computers to achieve high data processing speed
* An alternative to Hadoop that doesn’t require shared data be stored in intermediate stores
* Provides development APIs for Scala, Java, Python, and R
* Can work with a diverse set of data sources, including HDFS, Cassandra, Hbase, and S3

---

## How does it work ?

1. The driver program (main programming issuing commands) controls a SparkContext object 
2. SparkContext connects to cluster managers which then allocate resources across nodes

---

## RDDs - Resilient Distributed Datasets

* RDDs - immutable, partitioned collection of records that can be operated on with in-memory computations
* Supported Operations
  * Transformation - Produce a new RDD from existing RDDs
    * Action - Produce a non-RDD values from RDDs

### RDD Operations

Transformation
* map
* flatMap
* filter
* distinct
* groupByKey
* reduceByKey
* sortBy
* union
* intersection

Actions
* reduce
* collect
* count
* first
* take
* takeSample

## Spark Components

* Spark Core
* Spark SQL
* Spark Streaming
* Mlib (Machine Learning)
* GraphX (graph)