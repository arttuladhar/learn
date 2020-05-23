---
title: 06 - Databases
---

### SQL - RDS

RDS is a Database as a Service (DBaaS) product. It can be used to provision a fully functional database without the admin overhead traditionally associated with DB platforms. It can perform at scale, be made publicly accessible, and can be configured for demanding availability and durability scenarios.

RDS supports a number of database engines:

* MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server
* Aurora: An in-house developed engine with substantial features and performance enhancements.

RDS can be deployed in single AZ or Multi-AZ mode (for resilience) and supports the following instance types:

* General purpose (DB.M4 and DB.M5)
* Memory optimized (DB.R4 and DB.R5, and DB.X1e and DB.X1 for Oracle)

Types of storage are supported:

* General Purpose SSD (gp2)
* Provisioned IOPS SSD

RDS instances are charged based on:

* Instance size
* Provisioned storage (Not Used)
* IOPS if using io1
* Data transfer out
* Any backups/snapshots beyond the 100% that is free with each DB instance.

RDS supports encryption with the following limits/restrictions/conditions:

* Encryption can be configured when creating DB instances.
* Encryption can be added by taking a sanpshot, making an encrypted snapshot, and creating a new encrypted instance from that encrypted snapshot.
* Encryption cannot be removed.

Network access to an RDS instance is controlled by a security group (SG) associated with the RDS instance.

### RDS Backups and Restore

RDS is capable of a number of different types of backups. Automated backsups to S3 occur daily and can be retained from 0 to **35 days**. (0 means disabled). Manual snapshot are taken manually and exist until deleted, and point-in-time log-based backups are also stored on S3.

![RDS Backups](/images/AWS_Certified_Solutions_Architect/RDS_Backups.jpg)

When you restore a snapshot, it will create a brand new instance of RDS with new CNAME.

### RDS Resilency

#### RDS Multi-AZ

* RDS can be provisioned in single or Multi-AZ mode.
* Multi-AZ provisions a primary instance and a standby instance in a different AZ of the same region
* Only the primary can be accessed using the instance CNAME
* There is no performance benefit, but it provides a better (Recovery Time Objective) RTO than restoring a snapshot.

![RDS-MultiAZ](/images/AWS_Certified_Solutions_Architect/RDS-MultiAZ.jpg)

With MultiAZ you donot have access to secondary AZ. For RDS only point of contact to the database is via CNAME. For the maintenance, changes happens first on the secondary and then later promoted to primary.

#### RDS Read Replica

RDS Read Replicas are read-only copies of an RDS instance that can be created in the same region or a different region from the primary instance.

Read Replicas can be addressed independently (each having their own DNS name) and used for read workloads, allowing you to scale reads. Five Read Replicas can be created from a RDS instance, allowing a 5x increase in reads.

{{% notice info %}}
First step on creating RDS Read Replica is to enable automatic backup on the master instance.
{{% /notice %}}

**Why Read Replicas**

* Global Resilence
* Independently Scale Out Reads (Horizontal Scalling)
  
---

### SQL - Aurora

Aurora is a database engine developed by AWS that is compatible with MySQL, PostgreSQL and associated tools.

* Aurora uses a base configuration of a cluster - shared storage
* A cluster contains a single primary instance and zero of more replicas.
* All instances (primary and replicas) use the **same shared storage** - the cluster volumes.
* Cluster volume scales automatically, only bills for consumed data, and it's constantly backed up to S3.

![Aurora Cluster Architecture](/images/AWS_Certified_Solutions_Architect/Aurora_Cluster_Architecture.jpg)

* Automatic Backup
* Snapshot Process
* Backtrack - There is an outage, whenever we do a backtrack. Maximum window of 72 Hours.
* Parallel Queries

#### Aurora Serverless

Aurora Serverless is based on the same database engine as Aurora, but instead of provisioning certain resouce allocation. Aurora Serverless handles this as a server. You simply specify a minimum and maximum number of Aurora capacity units (ACUs).

Abstracts away the capacity planning for database. You simply pay for the usage of the database. You start with specifying minimum and capacity unit with ACUs.

Usage - Unknown usage, Peak Usage at certain times, Shutdown database when not in use, good use case for Dev or Test databases.

Aurora Serverless exists in single Availability Zone.

Data API allows you to query database using traditional apis.

### NoSQL

#### DynamoDB

DynamoDB is a NoSQL database service. It's a global service, partioned regionally and allows the creation of tables.

### In-Memory Caching

DynamoDB Accelerator (DAX)
