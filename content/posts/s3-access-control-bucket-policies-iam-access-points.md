---
title: 'Mastering S3 Access Control: Bucket Policies, IAM Policies, and S3 Access Points Explained'
seoTitle: 'AWS S3 Access Control: Bucket Policies, IAM Policies, and S3 Access Points'
date: 2024-09-24T03:36:57+05:30
description: Understand AWS S3 Access Control, Principal vs Condition Elements, Bucket Policies vs. IAM Policies and access points with Practical Examples
slug: s3-access-control-bucket-policies-iam-access-points
categories:
  - AWS
tags:
  - AWS
  - S3
---
When working with **Amazon S3**, securing your data is a top priority. One powerful way to manage who can access your S3 bucket and under what conditions is by using **bucket policies**. These policies consist of different elements, two of which are the **Principal element** and the **Condition element**.

In this article, I will explain what the Principal and Condition elements are, how they work, and how you can use them to control access to your S3 bucket. I will also provide examples and real-world use cases to make these concepts easy to understand. Additionally, I will explain how bucket policies interact with **IAM policies** and when you might need to modify both. Lastly, I will introduce the concept of **S3 Access Points**, which are an additional way to control access to S3 buckets.

## What is the Principal Element?

The **Principal element** in an AWS policy specifies **who or what is allowed** to access a resource, like an S3 bucket. It answers the question, **“Who is allowed to access this resource?”** The Principal can refer to an AWS user, role, or service like CloudFront or Lambda.

To make it simpler, think of it like this: if you have a building and want to control who can enter, the Principal would represent the people or services you allow in.

### Example of a Principal Element in an S3 Bucket Policy:

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity"
  },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*"
}
```

In this example:

*   The **Principal** is **CloudFront’s Origin Access Identity (OAI)**, which means that **only CloudFront** is allowed to access the objects in the bucket.
*   The **Effect** is set to "Allow," which means that the specified action (getting objects from the S3 bucket) is permitted.
*   The **Action** specifies that CloudFront is allowed to read the objects in the bucket (via `s3:GetObject`).

This method is useful when you want to **restrict public access** to your S3 bucket and only allow CloudFront to retrieve the data. This ensures that users can access the bucket through CloudFront, but they cannot access it directly via S3 URLs.

## What is the Condition Element?

The **Condition element** adds another layer of security by specifying the **circumstances** under which access is granted. It answers the question, **“Under what conditions can they access the resource?”**

Conditions allow you to set specific rules such as:

*   **Source IP**: Only allow access from a particular IP address or range.
*   **AWS Service**: Only allow access if the request is made through a particular AWS service (like CloudFront or Lambda).
*   **Time of Day**: You can specify time-based conditions, allowing access only during certain hours.

### Example of a Condition Element in an S3 Bucket Policy:

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "*"
  },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*",
  "Condition": {
    "StringEquals": {
      "aws:Referer": "mycloudfrontdistributionid"
    }
  }
}
```

In this example:

*   The **Principal** is `"*"` (meaning **anyone** can request access).
*   However, the **Condition** restricts access so that only requests coming from the specific CloudFront distribution (with the ID `mycloudfrontdistributionid`) are allowed.
*   This ensures that even though anyone can technically send a request, access is granted only when the request passes through the correct CloudFront distribution.

The Condition element allows you to define very specific rules for when and how access is granted, making it ideal for fine-tuning access control in scenarios like using CloudFront with S3.

## The Difference Between Principal and Condition Elements

To summarize the difference between the two:

*   The **Principal** explains **who** can access the resource. It names the users, roles, or services that are allowed.
*   The **Condition** explains **how** or **when** access is allowed. It sets the rules that must be followed for the Principal to gain access.

### Real-World Example:

Imagine you run a **library**. You can define who is allowed to borrow books (just like the **Principal** in AWS policies). However, you might also want to define conditions, such as **only during library hours** or **only with a valid membership card**. In AWS, the Principal represents who has access, and the Condition sets the rules for how and when access is granted.

## Difference Between Bucket Policy and IAM Policy for S3 Access

Many people often get confused between **S3 bucket policies** and **IAM policies**. While both can control access to S3, they are applied in different ways and serve different purposes. Here’s how they differ:

### 1. S3 Bucket Policy

*   **Attached to the S3 bucket itself**: A **bucket policy** is applied **directly to the S3 bucket** and controls access to the bucket and its contents.
*   **Defines who can access the bucket**: The Principal in a bucket policy specifies which AWS accounts, services, or users can access the bucket.
*   **Best for cross-account access**: Bucket policies are often used when multiple AWS accounts or services (such as CloudFront) need access to the bucket. For example, if another AWS account needs to access your bucket, you’d specify that in the bucket policy.

### Example of a Bucket Policy:

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::123456789012:role/SomeOtherAccountRole"
  },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*"
}
```

This allows a role from another AWS account (account ID 123456789012) to access objects in your bucket.

### 2. IAM Policy

*   **Attached to users or roles**: An **IAM policy** is attached to an **individual user, group, or role** and defines what resources they can access, including S3 buckets.
*   **Controls what the user can do**: If an IAM user has access to multiple AWS services, an IAM policy will define which services and resources (such as S3 buckets) they are allowed to interact with.
*   **Best for controlling individual user access**: You typically use IAM policies to control access for **users or roles** within the same AWS account. For example, you might have an IAM policy that gives a specific user access to certain S3 buckets or speific path while denying access to others.

### Example of an IAM Policy for S3:

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*"
}
```

This IAM policy allows the user to retrieve objects from the bucket `mybucket`.

## Key Differences Between S3 Bucket Policy and IAM Policy:

### Scope

*   **Bucket Policy**: Controls access to the S3 bucket for **any AWS account or service** (including cross-account access).
*   **IAM Policy**: Controls access for **specific users or roles** within the same AWS account.

### Attachment

*   **Bucket Policy**: Attached directly to the **S3 bucket**.
*   **IAM Policy**: Attached to **IAM users, groups, or roles**.

### Use Case

*   **Bucket Policy**: Best for cross-account access or when you want to control access at the bucket level.
*   **IAM Policy**: Best for controlling individual user access to S3 (and other AWS services).

## How IAM Policy and S3 Bucket Policy Work Together

Now, let’s say you want to allow a specific user access to an S3 bucket. You’ve created an **IAM policy** for this user and attached it to them. You might wonder whether this policy alone is sufficient or whether you need to modify the **S3 bucket policy** as well.

### Scenario 1: No Restrictive Bucket Policy

*   If there is **no bucket policy** on the S3 bucket or if the bucket policy is **not restrictive**, then the **IAM policy alone** is sufficient to grant access.
*   In this case, the user can access the S3 bucket according to the permissions defined in their IAM policy.

### Scenario 2: Restrictive Bucket Policy

*   If the S3 bucket has an **existing restrictive bucket policy** (for example, a policy that blocks access to everyone except specific AWS accounts or services), the IAM policy **alone is not enough**.
*   You will need to **edit the bucket policy** to specifically allow that user or their IAM role access to the bucket.

### Example of a Bucket Policy that Blocks Access:

```json
{
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*",
  "Condition": {
    "StringNotEquals": {
      "aws:PrincipalArn": "arn:aws:iam::123456789012:role/SomeRole"
    }
  }
}
```

In this case, even if the user’s IAM policy allows access, the **bucket policy blocks them** because it denies access to anyone who doesn’t match the `SomeRole` in the condition.

In such a case, you need to update the **bucket policy** to allow access for the specific user, even if their IAM policy permits it.

## Best Practice

If you want to simplify access management, it’s usually best to rely primarily on **IAM policies** for granting access to users. However, if you need more granular or cross-account control, use **S3 bucket policies** to enforce access restrictions at the bucket level.

## AWS CloudFront and S3 Bucket Policies: A Use Case

Let’s say you have a website serving static content from an S3 bucket, and you use **Amazon CloudFront** as your CDN to distribute that content globally. You want to ensure that users can only access the S3 bucket **through CloudFront**, not directly via S3 URLs.

Here’s how you can configure an **S3 bucket policy** to enforce this:

1.  **Principal**: You would use the **CloudFront OAI** as the principal in the bucket policy to ensure that only CloudFront can access the bucket.
2.  **Condition**: You can add a **Condition element** to ensure that only the correct CloudFront distribution accesses the bucket.

### Example Policy:

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity"
  },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*",
  "Condition": {
    "StringEquals": {
      "aws:Referer": "mycloudfrontdistributionid"
    }
  }
}
```

This ensures that:

*   **Principal**: Only the **CloudFront OAI** can access the bucket.
*   **Condition**: Access is only granted if the request comes through the specified CloudFront distribution.

This prevents users from directly accessing the bucket through S3 URLs, ensuring all access goes through CloudFront.

## Introducing S3 Access Points: Simplifying Access Control

**S3 Access Points** are another way to control access to your S3 bucket. They provide more flexibility by allowing you to create different **entry points** into a bucket, each with its own **policy** and **permissions**. This is useful if you have different applications, users, or teams accessing the same bucket but needing **different levels of access**.

### Key Features of S3 Access Points:

1.  **Multiple Access Points per Bucket**: You can create several access points for a single S3 bucket, each with its own access controls. This allows you to manage access more easily without having to rely solely on bucket policies.
2.  **Fine-Grained Control**: Each access point can have its own **policy**, enabling you to grant or restrict access to different users, groups, or applications without modifying the bucket policy.
3.  **VPC-Only Access**: You can configure an access point to allow access only from within a **Virtual Private Cloud (VPC)**, adding an extra layer of security by ensuring that data can only be accessed from within your AWS network.

### Example Use Case for S3 Access Points:

Imagine a scenario where you have two teams, **Team A** and **Team B**, accessing the same S3 bucket, but they need different access controls:

*   **Team A** needs full access to upload and download files.
*   **Team B** only needs read-only access.

Instead of creating complex bucket policies to accommodate both teams, you can create **two S3 Access Points**:

*   One for **Team A** with full permissions.
*   One for **Team B** with read-only permissions.

Each access point has its own **policy** tailored to the needs of each team, simplifying access management while keeping the bucket secure.

### Example of an S3 Access Point Policy:

```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::111122223333:user/TeamAUser"
  },
  "Action": [
    "s3:PutObject",
    "s3:GetObject"
  ],
  "Resource": "arn:aws:s3:accesspoint:us-west-2:111122223333:exampleap/*"
}
```

With S3 Access Points, you can manage multiple access patterns to the same bucket without changing the underlying bucket policy. This can be helpful in scenarios involving multiple applications, teams, or compliance requirements.

## Conclusion

Understanding the differences between the **Principal** and **Condition** elements in S3 bucket policies helps you control who can access your S3 resources and under what circumstances. Additionally, it's important to know the distinction between **S3 bucket policies** and **IAM policies**. While both control access to S3, **bucket policies** are typically used for cross-account or service-level access, while **IAM policies** are best for controlling individual user access within the same account.

When granting access to an S3 bucket, always check if the bucket policy is **restrictive**. If so, you’ll need to modify both the **IAM policy** and the **bucket policy** to ensure that the user has access. If the bucket policy is not restrictive, the IAM policy should be enough to grant the user the required permissions.

Lastly, **S3 Access Points** provide an additional method to simplify access control by allowing you to create multiple entry points to a bucket, each with its own permissions, making access management more flexible and scalable.