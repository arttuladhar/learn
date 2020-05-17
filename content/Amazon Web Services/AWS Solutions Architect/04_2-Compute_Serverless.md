---
title: 04 (B) - Compute Services - Serverless
---

- [Serverless Compute](#serverless-compute)
  - [Lambda Essentials](#lambda-essentials)
  - [API Gateway](#api-gateway)
  - [Step Functions](#step-functions)
- [Container Based Compute](#container-based-compute)
  - [ECS](#ecs)

## Serverless Compute

![Serverless](/images/AWS_Certified_Solutions_Architect/serverless.png)

Serverless architecture consists of two main principles, including **BaaS** (Backend as a Service), which means using third party services where possible rather than running your own. Example include Auth0 or Cognito for authentication and Firebase or DynamoDb for data storage. Servless architecture uses event driven architecture using **FaaS** (Function as a Service) products to provide application logic. These functions are only actively invoked when they are needed.

### Lambda Essentials

Lambda is a **FaaS** product. Function are code, which run in a runtime. Functions are invoked by events, perform actions for **upto 15 minutes** and terminate. Functions are also stateless - each run is clean.

![Serverless Compute Lambda](/images/AWS_Certified_Solutions_Architect/Serverless_Compute_Lambda.jpg)

### API Gateway

API Gateway is a managed API endpoint service. It can be used to create, publish, monitor and secure APIs **"As a Service"**. API Gateway can use other AWS services for compute (FaaS/IaaS) as well as to store and recall data.

{{% notice note %}}
Access to AWS Service is provided by the function's execution role. This role is assumed by Lambda, and temporary security credentials are available to the function via STS.
{{% /notice %}}

![Api Gateway](/images/AWS_Certified_Solutions_Architect/ApiGateway.jpg)

{{% notice info %}}
Pricing is based on the number of API calls, the data transferred and any caching required to improve performance.
{{% /notice %}}

{{% notice note %}}
API Gateway can access some AWS services directly using proxy mode.
{{% /notice %}}

### Step Functions

Step Functions is a **serverless visual workflow** servcie that provides state machines. A state machine can orchestrate other AWS services with simple logic, branching, and parallel execution, and it maintains a state. Workflow steps are known as **states**, and they can perform work via **tasks**. Step Functions allows for long-running serverless workflows. A state machine can be defined by using Amazon State Language (ASL).

Without Step Functions, Lambda functions could only run for 15 minutes. Lambda functions are stateless. State machines maintain state and allow longer running processess.

## Container Based Compute

### ECS 
ECS is a managed container engine. It allows **Docker containers** to be deployed and managed within AWS environments. ECS can use infrastructure clsutes based on EC2 or **Fargate** where AWS manages the backing infrastructure.

![Api Gateway](/images/AWS_Certified_Solutions_Architect/Containers_ECS.jpg)

With EC2 launch type utilizes your own EC2 instances. AWS Fargate is a managed service, so tasks are auto placed.

**Cluster** - A logical collection of ECS resources - either ECS EC2 instances or a logical representation of managed Fargate infrastructure

**Task Definition** - Defines your application. Similar to Dockerfile but for running containers in ECS. Task definition can contain multiple containers.

**Container Definition** - Inside a task definition, a container definition defines the individual containers a task uses. It controls the CPU and memory each container has, in addition to port mappings for the container

**Task** - A single running copy of any containers defined by a task definition. One working copy of an application

**Services** - Allows task definitions to be scaled by adding additional tasks. Define minimum and maximum values.

**Registry** - Storage for container images. (eg. ECS Container Registry or Dockerhub). Used to download image to create containers.