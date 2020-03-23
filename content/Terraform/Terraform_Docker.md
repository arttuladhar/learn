---
title: Setting up Terraform With Docker
---

## Installing Docker on the Swarm Manager and Worker

```
# Update the Operating System
sudo yum update -y

# Uninstall Old Versions
sudo yum remove -y docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

# Install Docker CE
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

# Add Docker Repository
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum -y install docker-ce

# Start Docker and Enable
sudo systemctl start docker && sudo systemctl enable docker

# Add `cloud_user` to the `docker` group
sudo usermod -aG docker cloud_user

docker --version

# Configure Swarm Manager Node
docker swarm init --advertise-addr [PRIVATE_IP]

On the worker node, add the worker to the cluster:
docker swarm join --token [TOKEN] [PRIVATE_IP]:2377

# Verify Swarm cluster
docker node ls
```

## Installing Terraform

```
# Install Terraform 0.11.13 on the Swarm manager
sudo curl -O https://releases.hashicorp.com/terraform/0.11.13/terraform_0.11.13_linux_amd64.zip

sudo yum install -y unzip
sudo unzip terraform_0.11.13_linux_amd64.zip -d /usr/local/bin/

# Test the Terraform installation
terraform version
```




