---
title: Terraform Modules
---
Module is a container for multiple resources that are going to be used together.

Main goal of module is logical grouping of resources to it's cohesive unit that can be reused and shared across different systems. Modules can also be shared across multiple teams or via public registry such as GitHub or Terraform Cloud registry.

### Using Terraform Modules

```
# Download the image
module "image" {
  source = "./image"
  image_name  = "${var.image_name}"
}

# Start the container
module "container" {
  source             = "./container"
  image              = "${module.image.image_out}"
  container_name     = "${var.container_name}"
  int_port           = "${var.int_port}"
  ext_port           = "${var.ext_port}"
}
```
