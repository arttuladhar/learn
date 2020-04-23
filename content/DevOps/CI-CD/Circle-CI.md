---
title: Circle CI
tags: ["devops"]
---

- [Concepts](#concepts)
- [Workflows](#workflows)
- [Jobs](#jobs)
- [Steps](#steps)
- [Image](#image)
- [Example](#example)

## Concepts

## Workflows

Workflows define a list of jobs and their run order. It is possible to run jobs concurrently, sequentially, on a schedule, or with a manual gate using an approval job.

## Jobs

Jobs are a collection of Steps. All of the steps in the job are executed in a single unit which consumes a CircleCI container from your plan while it’s running. Each job must declare an executor that is either docker, machine, windows or macos. machine includes a default image if not specified, for docker you must specify an image to use for the primary container, for macos you must specify an Xcode version, and for windows you must use the Windows orb.

## Steps

Steps are actions that need to be taken to perform your job. Steps are usually a collection of executable commands. For example, the checkout step checks out the source code for a job over SSH. Then, the run step executes the make test command using a non-login shell by default.

## Image

An image is a packaged system that has the instructions for creating a running container. The Primary Container is defined by the first image listed in .circleci/config.yml file. This is where commands are executed for jobs using the Docker or machine executor. The Docker executor spins up a container with a Docker image. The machine executor spins up a complete Ubuntu virtual machine image. See Choosing an Executor Type document for a comparison table and considerations.

## Example

```yml
version: 2

jobs:
  one:
    docker:
      - image: circleci/ruby:2.4.1
    steps:
      - checkout
      - run: echo "Hello Ruby"
      - run: mkdir -p my_workspace
      - run: echo "Trying out workspaces" > my_workspace/echo-output
      - persist_to_workspace:
            root: my_workspace
            paths:
                - echo-output
      - run: sleep 25
  two:
    docker:
      - image: circleci/ruby:2.4.1
    steps:
      - checkout
      - run: echo "A more familiar Hi"
      - attach_workspace:
          at: my_workspace
      - run: |
          if [[ $(cat my_workspace/echo-output) == "Trying out workspaces" ]]; then
            echo "It worked!";
          else
            echo "Nope!"; exit 1
          fi
      - run: sleep 15
    

workflows:
  version: 2
  one_two_wf:
    jobs:
      - one
      - two:
          requires:
            - one
```