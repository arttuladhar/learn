---
title: 01 - HCL Basics
---
- [Terraform CLI Commands](#terraform-cli-commands)
- [Terraform Syntax](#terraform-syntax)
- [Resources](#resources)
- [Console and Outputs](#console-and-outputs)
- [Variables](#variables)
    - [Passing Variable](#passing-variable)
- [DataSources](#datasources)
- [Terraform Workspaces](#terraform-workspaces)
- [NullResources and Local-exec](#nullresources-and-local-exec)

## Terraform CLI Commands

Command | Description |
---------|----------|
`init` | Initializes a new or existing Terraform configuration
`validate` | Validates the Terraform files
`plan`   | Generates ans shows an execution plan
`apply` | Builds or Change Infrastructure
`output` | Reads an output from a state file
`show`   | Inspects Terraform State or Plan
`providers` | Print a tree of the providers using the configuration
`destory` | Destorys Terraform-managed infrastructure

## Terraform Syntax

* Single line comments start with `#`
* Multi line comments are wrapped with `/*` and `*/`
* Values are assigned with the syntax `key=value`
* Strings are double quoted
* Strings can interpolate other values using the syntax `${}`

## Resources

Resosurces are the Objects manged by Terraform such as VM or S3 Buckets. Declaring a Resource tells Terraform that it should CREATE and MANAGE the resource described. If the resource already exists it must be imported into Terraform's State.

**Syntax**
```
resource <Resource_Type> <Resource_Name> {
    // Meta Parameters
}
```

**Example**
```
# Example
# Download the latest Ghost image
resource "docker_image" "image_id" {
  name = "ghost:latest"
}

# Start the Container
resource "docker_container" "container_id" {
  name  = "ghost_blog"
  image = "${docker_image.image_id.latest}"
  ports {
    internal = "2368"
    external = "80"
  }
}
```

## Console and Outputs
Outputs are printed by the CLI after `apply`. These can reveal calculated values.

```
#Output the IP Address of the Container
output "ip_address" {
  value       = "${docker_container.container_id.ip_address}"
  description = "The IP for the container."
}

#Output the Name of the Container
output "container_name" {
  value       = "${docker_container.container_id.name}"
  description = "The name of the container."
}
```

## Variables

Input variables serve as parameters for a Terraform file. A variable block configures a single input variable for a Terraform module. Each block declares a single variable.

**Syntax**
```
variable [NAME] {
  [OPTION] = "[VALUE]"
}
```

Variables can be used using interpolation syntax `"${var.name}"`

**Example**
```
variable "image_name" {
  description = "Image for container."
  default     = "ghost:latest"
}

resource "docker_image" "image_id" {
  name = "${var.image_name}"
}
```

#### Passing Variable
Variables can be specified on the command line with `-var bucket_name=my-bucket` or in file using `terraform.tfvars` 
```
terraform apply -var 'foo=bar'
terraform apply -var 'container_name=ghost_blog' -var 'ext_port=8080'
```

We can set the variabl using Environment Variables
```
export TF_VAR_container_name=ghost_blog
export TF_VAR_ext_port=8080
```

## DataSources

Data sources allow data to be fetched or computed for use elsewhere in Terraform configuration. Use of data sources allows a Terraform configuration to make use of information defined outside of Terraform, or defined by another separate Terraform configuration.

Each provider may offer data sources alongside its set of resource types.

## Terraform Workspaces
Each Terraform configuration has an associated backend that defines how operations are executed and where persistent data such as Terraform State are stored. Terraform supports multiple workspace to isolate different terraform plans and their respective state files.

```
# Creates Dev Workspace
terraform workspace new dev

# List Workspaces
terraform workspace list

# Switch to default Workspace
terraform workspace select default
```

## NullResources and Local-exec

```
# Download the latest Ghost Image
resource "docker_image" "image_id" {
  name = "${lookup(var.image_name, var.env)}"
}

# Start the Container
resource "docker_container" "container_id" {
  name  = "${lookup(var.container_name, var.env)}"
  image = "${docker_image.image_id.latest}"
  ports {
    internal = "${var.int_port}"
    external = "${lookup(var.ext_port, var.env)}"
  }
}

# Using Null Resource and Local Exec to Write Container Name and Container Address to TextFile
resource "null_resource" "null_id" {
  provisioner "local-exec" {
    command = "echo ${docker_container.container_id.name}:${docker_container.container_id.ip_address} >> container.txt"
  }
}
```