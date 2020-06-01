---
title: 09 - Application, Analytics, and Operations
---

## Application Integration

### Simple Notification Service (SNS)

![Simple Notification Service](/images/AWS_Certified_Solutions_Architect/Simple-Notification-Service.jpg)

* SNS is a Publisher / Subscriber based fully managed Regional Service.
* It is a Public Service.
* SNS coordinates and manages the sending and delivery of messages. Messages sent to a **topic** are delivered to **subscribers**
* SNS is integrated with many AWS serviecs and can be used for certain event notifications (e.g. CloudFormation, Stack creation etc)
* Using SNS, CloudWatch can notifiy admins of important alerts
* SNS can be used for mobile push notifications

#### SNS Components

##### Topic
* An isolated configuration for SNS, including permissions
* Messages (**<= 256 KB**) are sent to a topic
* Subscribers to that topic receive messages

##### Subscribers
* Endpoint that receive message for topic
  * HTTP(S)
  * Email
  * SQS (Message can be added to one or more queues)
  * Mobile Push Notification
  * Lambda Functions
  * SMS

##### Publisher
* An entity that publishes/sends message to queues
  * Application
  * AWS services, including S3 (S3 events), CloudWatch, CloudFormation etc.

---

### Simple Queue Service (SQS)

![Simple Notification Service](/images/AWS_Certified_Solutions_Architect/Simple-Queue-Service.jpg?height=10px)

* Simple Queue Service (SQS) provides fully managed, highly available message queues for inter-process /service /service messaging.
* SQS is used mainly to create decoupled architectures.
* Message are added to a queue and retreived via polling.

#### Polling Types
* Short Polling: Available messages are returned ASAP - a short poll might return 0 messages. Causes increased number of API calls.
* Long polling - Waits for message for a given *WaitTimeSeconds* (More Efficient)


* Each SQS message can contain upto 256KB of data but can link data stored in S3 for any larger payloads.
* When a message is polled, it is hidden in the queue. It can be deleted when processing is completed - otherwise, after a VisibilityTimeout period, it will return to the queue.
* Lambda functions can be invoked based on message on a queue offering better scalling and faster response than Auto Scalling groups for any messages that can be processed quickly.

There are two types of queues: **Standard Queues** and **FIFO queues**.

* Standard queues are distributed and scalable to nearly unlimited message volume. The order is not guarantted, best-effort only, and messages are gurantted to be delivered at least once but sometimes more than once.
* FIFO queues ensure first-in,first-out delivery. Messages are delivered once only - duplicates do not occur. The throughput is limited to ~ 3,000 messages per second with batching or ~300 without by default.

```
aws sqs get-queue-attributes --queue-url https://URL --attribute-names All
aws sqs send-message --queue-url https://URL --message-body "INSERTMESSAGE"
aws sqs receive-message --queue-url https://URL
aws sqs delete-message --queue-url https://URL --receipt-handle "INSERTHANDLE"
aws sqs receive-message --wait-time-seconds 10 --max-number-of-messages 10 --queue-url https://URL
aws sqs --region us-east-1 receive-message --wait-time-seconds 10 --max-number-of-messages 10 --queue-url https://URL
aws sqs delete-message --queue-url https://URL --receipt-handle "INSERTHANDLE"
```

### Elastic Transcoder

Elastic Transcoder is an AWS service that allows you to convert media file from an input format to one or more output formats. It's delivered as a service, and you are billed a per-minute charge while using the service.

A pipeline is a queue for jobs. It stores source and destination settings, notification, security and other high settings. Jobs are processed in the order they are added as resources allow. Pipelines can send notifications as jobs progress through various sates. These might notify an administrator or initiate further even-driven processing.

## Analytics

### Athena

Amazon Athena is an interactive query servie that utilizes Schema-On-Read, allowing you to run ad-hoc SQL like queries on data from a range of sources. Results are returned in seconds, and you are billed only for the **compute time used** and any existing storage costs.

Athena can query many forms of structured, semi-structured, and unstructured data in S3.

{{% notice info %}}
Athena is used to query large dataset stored in S3 with in-frequent access pattern. You are charged for compute time only. You don't need to maintain separate dataset for Athena, it can directly access S3 bucket.
{{% /notice %}}

![Athena](/images/AWS_Certified_Solutions_Architect/Athena.jpg)


### Elastic MapReduce (EMR)

Amazon Elastic MapReduce (EMR) is a tool for large-scale parallel processing of big data and other large data workloads. It's based on the Apache Hadoop framework and is delivered as a managed cluster using EC2 instances. EMR is used for huge-scale log analysis, indexing, machine learning, financial analysis, simulations, bioinformatics and many other large-scale applications.

EMR cluster have zero or more core nodes, which are managed by the master node. They run tasks and manage data for HDFS. 

Data can be input from and output to S3. Intermediate data can be stored using HDFS in the cluster or EMRFS using S3.


### Kinesis and Firehose
### Redshift

## Logging and Monitoring

### CloudWatch
### CloudWatch Logs
### CloudTrail
### VPC Flow Logs

## Operations

### CloudWatch Events
### KMS

## Deployment
### Elastic Beanstalk
### OpsWorks


