---
title: Jenkins Shared Library
layout: post
categories:
- devops
---

Jenkins Shared library is the concept of having a common pipeline code in the version control system that can be used by any number of pipeline just by referencing it. In fact, multiple teams can use the same library for their pipelines. Pipeline has support for creating "Shared Libraries" which can be defined in external source control repositories and loaded into existing Pipelines.

> A shared library is a collection of independent Groovy scripts which you pull into your Jenkinsfile at runtime.

### Getting Started with Shared Library

A Shared Library is defined with a name, a source code retrieval method such as by SCM, and optionally a default version. The name should be a short identifier as it will be used in scripts.

#### Directory structure

```
jenkins-shared-library
|____vars
|____src
|____resources
```

### Resources

* https://devopscube.com/create-jenkins-shared-library/
* https://tomd.xyz/articles/jenkins-shared-library/
* https://dev.to/kuperadrian/how-to-setup-a-unit-testable-jenkins-shared-pipeline-library-2e62
* https://tomd.xyz/articles/jenkins-shared-library/