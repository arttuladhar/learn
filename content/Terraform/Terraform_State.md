---
title: Terraform State
---

Create a S3 Bucket in AWS that we will be using to store the Remote State.

Set the Environment Variables:
```
export AWS_ACCESS_KEY_ID="[ACCESS_KEY]"
export AWS_SECRET_ACCESS_KEY="[SECRET_KEY]]"
export AWS_DEFAULT_REGION="us-east-1"
```

Add the Remote Backend Configuration
```
terraform {
  backend "s3" {
    key    = "terraform-aws/terraform.tfstate"
  }
}
```

Initialize Terraform
```
terraform init -backend-config "bucket=[BUCKET_NAME]"
```
