---
title: 'AWS Auto Scaling Group: EC2 and ELB Health Checks Explained'
date: 2024-10-06T13:40:02+05:30
description: Learn how AWS Auto Scaling Groups use EC2 and ELB health checks to maintain instance health, ensure high availability, and manage multiple load balancers.
slug: aws-asg-ec2-elb-health-checks
categories:
  - AWS
tags:
  - AWS
---
When managing your application’s infrastructure on AWS, **Elastic Load Balancers (ELBs)** and **Auto Scaling Groups (ASGs)** work together to ensure high availability and scalability. Both components perform **health checks** to determine the status of your **EC2 instances**, but they operate differently, and understanding how they interact is essential for smooth operation.

In this article, we will explore how **ELB health checks** and **ASG health checks** work, their differences, and why it’s important to configure them correctly.

## The Role of Health Checks

Health checks in AWS are used to assess whether an **EC2 instance** is functioning properly and can handle traffic or workloads. These checks are used by both **Auto Scaling Groups** and **Elastic Load Balancers**, but they differ in terms of **what they monitor** and **how they act on the results**.

### EC2 Health Checks

By default, when you create an Auto Scaling Group (ASG), the health check type is set to **EC2**. This means the ASG relies on **EC2 status checks** to monitor instance health. EC2 health checks examine the status of the virtual hardware running the instance, network connectivity, and basic instance availability.

If an EC2 instance fails these checks (such as when it has a network or hardware failure), it is marked as **unhealthy**, and the ASG may terminate the instance and replace it with a new one.

### ELB Health Checks

**Elastic Load Balancers (ELB)**, whether it’s an **Application Load Balancer (ALB)** or a **Network Load Balancer (NLB)**, perform **application-level health checks**. These checks assess whether the instance is **responding correctly to traffic**. For example, an ALB might check whether an HTTP server is returning a successful status code (such as 200 OK) when pinged.

If the **ELB health check** fails (for instance, if the application is not responding or is misconfigured), the **ELB will mark the instance as unhealthy** and stop sending traffic to it. However, by default, this doesn’t mean the instance will be automatically terminated by the ASG, this depends on how you configure the ASG’s health check type.

## Auto Scaling Group Health Checks and ELB Interaction

When managing instances within an ASG, you need to configure which health check type the ASG will rely on to decide whether an instance should be terminated and replaced. You have the following options:

### 1. EC2 Health Check Only

By default, ASG uses only **EC2 health checks**. In this configuration, ASG does not pay attention to the **ELB’s health check results**. If an instance passes the EC2 health check but fails the ELB health check, the ASG won’t terminate the instance. This can lead to a scenario where an instance is failing to handle traffic correctly (marked **OutOfService** by the ELB) but is not replaced by the ASG because it is still healthy according to EC2 status checks.

### 2. ELB Health Check

If you configure the ASG’s health check type to **ELB**, the ASG will now monitor the health checks reported by the ELB. If an instance fails the ELB health check, the ASG will consider the instance unhealthy, terminate it, and launch a replacement. This is useful because ELB health checks are more application-centric and can catch issues that EC2 checks do not.

### 3. EC2 and ELB Health Checks

You can configure the ASG to use both **EC2 and ELB health checks**. In this case, an instance is only considered healthy if it passes both checks. If it fails either the EC2 health check or the ELB health check, the ASG will terminate the instance and replace it. This setup provides the best coverage, as it accounts for both infrastructure-level issues (EC2) and application-level issues (ELB).

## Why EC2 Health Check Status May Differ from ELB Health Check Status

It’s important to understand why an **instance may be marked as healthy by EC2** but **unhealthy by the ELB**. These two systems check for different kinds of issues:

1. **EC2 Health Checks**: These checks are primarily concerned with the infrastructure that the instance runs on. For example, EC2 checks might look at whether the instance is running, if it has network connectivity, or if it’s suffering from underlying hardware problems. If the instance passes these checks, it is considered healthy by EC2.
2. **ELB Health Checks**: ELB health checks, on the other hand, assess **application-level** functionality. They look at whether the instance is correctly handling traffic. For example, the health check could involve sending an HTTP request to the instance and checking the response. If the instance fails to respond, or if the response is an error (e.g., 500 Internal Server Error), the ELB will mark the instance as unhealthy.

## Common Reasons for Mismatches

* An instance might be **up and running (healthy in EC2)**, but if the **application** running on the instance is misconfigured, unresponsive, or failing, the ELB will mark the instance as **unhealthy**.
* EC2 checks are focused on **system-level issues** like CPU, memory, and disk, while ELB checks focus on whether the instance is **correctly handling requests**, such as web or API traffic.
* **Example**: An EC2 instance might be running without any hardware issues (EC2 marks it as healthy), but if the web server software (like Apache or NGINX) is down or misconfigured, the ELB health check will fail, marking it unhealthy for traffic.

## Using Multiple ELBs with One ASG

If you have multiple ELBs (such as an **Application Load Balancer** for HTTP traffic and a **Network Load Balancer** for TCP traffic), you can configure your ASG to register instances with multiple **target groups** (each associated with a different ELB).

However, the **Auto Scaling Group does not automatically pick up the health checks from all attached ELBs** unless you configure it to do so. When using multiple ELBs, you must ensure that:

* The ASG is explicitly configured to use **ELB health checks**.
* The ASG is attached to **multiple target groups**, each associated with the respective ELBs.
* If an instance fails the health check in **any one of the target groups**, the ASG will mark the instance as unhealthy and replace it, regardless of whether it passes the health check in other target groups.

This setup is useful when you have different load balancers managing different types of traffic, but you still want the ASG to manage instance health across all traffic types.

## Summary

In AWS, **health checks** are a critical part of ensuring the availability and reliability of your instances. While **EC2 health checks** monitor the infrastructure, **ELB health checks** focus on the application layer. It’s essential to configure the Auto Scaling Group (ASG) to use **ELB health checks** when you’re routing traffic through load balancers to ensure that instances that fail to serve traffic correctly are terminated and replaced.

Moreover, when working with **multiple ELBs**, ensure that your ASG is configured to use health checks from all associated load balancers to cover all potential failure points across different traffic types. By understanding these distinctions and configuring health checks appropriately, you can ensure that your infrastructure responds quickly to both system-level and application-level failures.