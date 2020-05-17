
---
title: Creating a Static Website Using Amazon S3
---

### Create S3 Bucket

1. Navigate to the S3 portion of the AWS Management Console.
2. Create a bucket, choosing a globally unique name.
3. Select US East (N. Virginia) region.
4. Click Next.
5. Leave options as defaults; click Next.
6. Under permissions, uncheck all four permissions restrictions.
7. Click Next.
8. Click Create bucket.
9. Select bucket name.
10. Click Upload.
11. Add files (use your own or those from the sample website).
12. Click Upload.

### Enable Static Website Hosting

1. Click the bucket name.
2. Navigate to Properties > Static website hosting.
3. Select Use this bucket to host a website.
4. For Index document, enter index.html.
5. For Error document, enter error.html.
6. Click Save.

### Apply Bucket Policy

Navigate to Permissions > Bucket policy.
Add the following JSON statement (replacing <my-bucket> with your bucket name):

```
{
 "Version":"2012-10-17",
 "Statement":[{
    "Sid":"PublicReadGetObject",
    "Effect":"Allow",
    "Principal": "*",
    "Action":["s3:GetObject"],
    "Resource":["arn:aws:s3:::<my-bucket>/*"]
 }]
}
```

Ensure the trailing /* is present so that the policy applies to all objects within the bucket.
