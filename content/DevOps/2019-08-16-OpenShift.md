---
title: Openshift
layout: post
categories:
- devops
---
- [Architecture](#architecture)
- [Containers and Image](#containers-and-image)
  - [Container Registries](#container-registries)
- [Pods and Services](#pods-and-services)
  - [Pods](#pods)
  - [Services](#services)
  - [Labels](#labels)
- [Builds and Image Streams](#builds-and-image-streams)
  - [Builds](#builds)
  - [Image Stream](#image-stream)
  - [Image stream tag](#image-stream-tag)
  - [Image stream image](#image-stream-image)
  - [Image stream trigger](#image-stream-trigger)
  - [Templates](#templates)
- [References](#references)
- [Cheatsheet](#cheatsheet)

## Architecture

OpenShift is a layered system designed to expose underlying Docker-formatted container image and Kubernetes concepts as acurately as possible, with a focus on easy composition of applications by a developer.

OpenShift Container Platform (OCP) has a microservices based architecture of smaller, decoupled units that work together. It runs on top of Kubernetes cluster, with data about the objects stored in etcd, a realible key-value store.

* REST APIs, which expose each of the core objects.
* Controllers, which read those APIs, apply changes to other objects, and report status or write back to the object.

![Openshift Architecture]({{ site.baseurl }}/img/2019-08-16-OpenShift/openshift-architecture.png)

## Containers and Image

The basic units of OpenShift Container Platform applications are called **Containers**. Linux container technologies are lightweight mechanism for isolating running processess so that they are limited to interacting with only their designated resources.

Containers in OpenShift Container Platform are based on Docker formatted container **Images**. An image is a binary that includes all of the requirements for running a single container, as well as metadata describring its needs and capabilities.

### Container Registries

A container registry is a service for storing and retreving Docker formatted container images. A registry contains a collection of one or more image repositories. Each image repository contains one of more tagged images. Docker provides its own registry, the Docker Hub, and you can also use private or third-party registries. Red Hat provides a registry at registry.access.redhat.com for subscribers. OpenShift Container Platform can also supply its own internal registry for managing custom container images.

## Pods and Services

### Pods

OpenShift Enterprise leverages the Kubernetes concept of a pod, which is one or more containers deployed together on one host, and the smallest compute unit that can be defined, deployed, and managed.

Pods are the rough equivalent of a machine instance (physical or virtual) to a container. Each pod is allocated its own internal IP address, therefore owning its entire port space, and containers within pods can share their local storage and networking.

```bash
# Get All Pods within a Namespace
oc get pods

# Get Pod information in YAML
oc get pod hello-node-1-nz525 -o yaml

# Get Pods Details using Label
oc describe pods -l app=hello-node
```

### Services

A Kubernetes service serves as an internal load balancer. It identifies a set of replicated pods in order to proxy the connections it receives to them. Backing pods can be added to or removed from a service arbitrarily while the service remains consistently available, enabling anything that depends on the service to refer to it at a consistent internal address.

Services are assigned an IP address and port pair that, when accessed, proxy to an appropriate backing pod. A service uses a label selector to find all the containers running that provide a certain network service on a certain port.

### Labels

Labels are used to organize, group, or select API objects. For example, pods are "tagged" with labels, and then services use label selectors to identify the pods they proxy to. This makes it possible for services to reference groups of pods, even treating pods with potentially different containers as related entities.

Most objects can include labels in their metadata. So labels can be used to group arbitrarily-related objects; for example, all of the pods, services, replication controllers, and deployment configurations of a particular application can be grouped.

```yaml
labels:
  key1: value1
  key2: value2
```

## Builds and Image Streams

### Builds

A build is the process of transforming input parameters into a resulting object. Most often, the process is used to transform input parameters or source code into a runnable image. A **BuildConfig** object is the definition of the entire build process.

OpenShift Container Platform leverages Kubernetes by creating Docker-formatted containers from build images and pushing them to a container registry.

The OpenShift build system provides extensible support for build strategies that are based on selectable types specified in the build API. There are three build strategies available:

* Docker build
* Source-to-Image (S2I) build
* Custom build

By default, Docker builds and S2I builds are supported.

### Image Stream

An OpenShift Container Platform object that contains pointers to any number of Docker-formatted container images identified by tags. You can think of an image stream as equivalent to a Docker repository.

### Image stream tag

A named pointer to an image in an image stream. An image stream tag is similar to a Docker image tag. See Image Stream Tag below.

### Image stream image

An image that allows you to retrieve a specific Docker image from a particular image stream where it is tagged. An image stream image is an API resource object that pulls together some metadata about a particular image SHA identifier. See Image Stream Images below.

### Image stream trigger

A trigger that causes a specific action when an image stream tag changes. For example, importing can cause the value of the tag to change, which causes a trigger to fire when there are Deployments, Builds, or other resources listening for those. See Image Stream Triggers below.

```bash
# Get OpenShift from Project Openshift
oc get is -n openshift


oc describe is jenkins -n openshift

# Get Image Stream Definition in YAML
oc get is jenkins -o yaml
```

### Templates

A template describes a set of objects that can be parameterized and processed to produce a list of objects for creation by OpenShift. A template can be processed to create anything you have permission to create within a project, for example services, build configurations, and deployment configurations. A template may also define a set of labels to apply to every object defined in the template.

You can create a list of objects from a template using the CLI or, if a template has been uploaded to your project or the global template library, using the web console.

```
# Get All Templates from a project
oc get templates -n openshift

# Create Template from YAML file
oc create -f <filename>
```

## References

* [Core Concepts - Builds and Image Streams](https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/builds_and_image_streams.html)

* [CLI Reference - Basic CLI Operation](https://docs.openshift.com/enterprise/3.1/cli_reference/basic_cli_operations.html#delete)

* [Core Concepts - Architecture](https://docs.okd.io/latest/architecture/core_concepts/index.html)

* [Core Concepts - Builds and Image Streams](https://docs.openshift.com/container-platform/3.7/architecture/core_concepts/builds_and_image_streams.html)

## Cheatsheet

<script src="https://gist.github.com/arttuladhar/23ac69ac6706d7ee2e0597f8dec3211b.js"></script>