---
title: Understanding List, Get and Put Permissions in Amazon S3
date: 2024-10-07T16:25:52+05:30
description: Learn how to manage S3 permissions for listing, getting, and putting files, and see an example IAM policy for read-only access to an S3 bucket and its contents.
slug: s3-list-get-put-permissions
categories:
  - AWS
tags:
  - AWS
---
Amazon S3 is a widely used storage service where permissions are controlled through AWS Identity and Access Management (IAM) policies. These policies specify what actions users or services can perform on S3 buckets and their contents.

In S3, permissions are granted using ARNs (Amazon Resource Names) that identify specific resources. There are two key types of ARNs when dealing with S3 permissions:

* **`arn:aws:s3:::mybucket` :** This refers to the bucket.
* **`arn:aws:s3:::mybucket/*` :** This refers to all the objects (files) within the bucket.

AWS developers have implemented a clear separation between permissions that apply to the bucket and permissions that apply to the objects (files) inside the bucket. This distinction is important for managing access to both the list of files and the content itself.

## S3 Permissions and Resource ARNs

### **`arn:aws:s3:::mybucket`**

This ARN is used when you want to allow actions related to the bucket itself, such as listing the files. If you grant the `s3:ListBucket` permission on this ARN, the user will be able to view the names of the files stored in the bucket but will not be able to interact with the file contents.

### **`arn:aws:s3:::mybucket/*`**

This ARN refers to the files inside the bucket. Permissions like `s3:GetObject`, `s3:PutObject`, and `s3:DeleteObject` apply to this ARN. These permissions control actions such as downloading, uploading, or deleting the actual content of the files.

## Why the Distinction Matters

The distinction between `arn:aws:s3:::mybucket` and `arn:aws:s3:::mybucket/*` ensures that access to the bucket and its contents is managed separately. This allows for more granular control over who can list the files and who can interact with the file data.

For example:

* If you want a user to **see the list of files** without being able to download or modify them, you would grant the s3:ListBucket permission on `arn:aws:s3:::mybucket`.
* If you want a user to **download or upload files**, you would apply permissions like s3:GetObject or s3:PutObject on `arn:aws:s3:::mybucket/*`.

## Example IAM Policy for Read-Only Access

Below is an example of an IAM policy that provides **read-only access** to the bucket mybucket and its contents. This policy allows the user to list the files in the bucket and download them, but they cannot upload, delete, or modify any files:

```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Action": [
            "s3:ListBucket"
         ],
         "Resource": [
            "arn:aws:s3:::mybucket"
         ]
      },
      {
         "Effect": "Allow",
         "Action": [
            "s3:GetObject"
         ],
         "Resource": [
            "arn:aws:s3:::mybucket/*"
         ]
      }
   ]
}
```

**Explanation of the Policy:**

1. The first block allows the `s3:ListBucket` action on `arn:aws:s3:::mybucket`, which gives the user permission to view the list of file names in the bucket.
2. The second block allows the s3:GetObject action on `arn:aws:s3:::mybucket/*`, giving the user permission to download (read) the actual file contents.

With this policy, the user has read-only access. They can see what files are in the bucket and download them, but they cannot upload new files, delete existing ones, or modify the files.

## Conclusion

In Amazon S3, there is a clear distinction between actions that apply to the bucket (such as listing files) and actions that apply to the files (such as downloading, uploading, or deleting). By separating permissions between `arn:aws:s3:::mybucket` and `arn:aws:s3:::mybucket/*`, AWS provides a way to precisely control access. The example IAM policy demonstrates how to give read-only access, allowing a user to list and download files without making any changes.