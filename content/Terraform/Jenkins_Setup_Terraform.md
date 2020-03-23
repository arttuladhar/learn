---
title: Setting up Jenkins using Terraform
---

## Building a Custom Jenkins Image

Create `Dockerfile` with contents:
```
FROM jenkins/jenkins:lts
USER root
RUN apt-get update -y && apt-get -y install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
RUN curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey
RUN add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
RUN apt-get update -y
RUN apt-get install -y docker-ce docker-ce-cli containerd.io
RUN curl -O https://releases.hashicorp.com/terraform/0.11.13/terraform_0.11.13_linux_amd64.zip && unzip terraform_0.11.13_linux_amd64.zip -d /usr/local/bin/
USER ${user}
```

Build the Image
```
docker build -t jenkins:terraform .
```

List the Docker Images:
```
docker image ls
```

## Setting up Jenkins

Create main module for Terraform, `main.tf`

```
# Jenkins Volume
resource "docker_volume" "jenkins_volume" {
  name = "jenkins_data"
}

# Start the Jenkins Container
resource "docker_container" "jenkins_container" {
  name  = "jenkins"
  image = "jenkins:terraform"
  ports {
    internal = "8080"
    external = "8080"
  }

  volumes {
    volume_name    = "${docker_volume.jenkins_volume.name}"
    container_path = "/var/jenkins_home"
  }

  volumes {
    host_path      = "/var/run/docker.sock"
    container_path = "/var/run/docker.sock"
  }
}
```

Initialize Terraform
```
terraform init
```

Plan the deployment
```
terraform plan -out=tfplan
```

Deploy Jenkins
```
terraform apply tfplan
```

Get the Admin password
```
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Login using Public IP with Port 8080
