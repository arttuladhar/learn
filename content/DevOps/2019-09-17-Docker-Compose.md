---
title: Docker Compose
layout: post
categories:
- devops
---

Docker Compose is used to run multiple containers as a single service.

```yaml
# docker-compose.yml
version: '2'

services:
  web:
    build: .
    # build from Dockerfile
    context: ./Path
    dockerfile: Dockerfile
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    image: redis
```

## Command Line

```bash

# Start Service
docker-compose start

# Stop Service
docker-compose stop

# Pause Service
docker-compose pause

# UnPause Service
docker-compose unpause

# List containers
docker-compose ps

# Create and start containers
docker-compose up

# Stop and remove containers, networks, images, and volumes
docker-compose down
```