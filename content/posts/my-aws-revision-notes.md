---
title: My AWS Revision Notes
date: 2024-10-12T10:42:27+05:30
slug: my-aws-revision-notes
categories:
  - AWS
---
**Outpost Use Cases:**

• Used for compliance or very low latency, such as in regions like Jammu & Kashmir.

**AWS Availability Zones (AZ) & Local Zones:**

• **AZ (Availability Zone):** Provides high availability within a region.

• **Local Zone:** Limited services like database and EC2, close to users (e.g., Bengaluru), used to reduce latency. Example use case: Netflix. Local Zones offer single-digit millisecond latency but cost slightly more.

• More information: [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/).

**Edge Locations & POP (Point of Presence):**

• **Edge Location:** A specialized location used for caching to reduce latency.

• **Regional Edge Caches:** Larger caches for less frequently accessed content. More details can be found [here](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/HowCloudFrontWorks.html).

• **CloudFront Origin Shield:** Adds an extra caching layer to reduce load on the origin server.

**IAM & Roles:**

• **Principals:** Entities (user/service) that have permissions in AWS.

• **IAM Role vs. Permissions vs. Policy:**

• **Assuming Role:** Temporarily assign permissions.

• **Switch Role:** Switch between different IAM roles.

• **STS (Security Token Service):** Issues temporary credentials.

• **IAM Policies:** Identity-based or resource-based policies.

• **Bucket, VPC, Endpoint Policies:** Different policies for specific resources.

• **Service Control Policy (SCP):** Enforces specific policies at an organization level.

• **IAM Instance Profile:** Used to attach roles to EC2 instances.

More info on IAM policies can be found [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html).

**Signed URLs & Cookies:**

• **Signed URL:** Grants temporary access to a single URL.

• **Signed Cookie:** Grants temporary access to multiple URLs (commonly used in OTT platforms).

**EC2 & Storage:**

• **ENI (Elastic Network Interface):** Bound by AZ, used for managing traffic between EC2 instances.

• **Instance Store vs. EBS Volume:**

• **EBS (Elastic Block Store):** Persistent storage across stops and terminations.

• **Instance Store:** Ephemeral storage tied to the lifecycle of an instance, used for temporary data.

• **Elastic IP:** Use for continuity of public IP addresses.

• **EC2 Hibernate Behavior:** Allows the instance to retain its state, especially with EBS, but not with ephemeral instance store volumes.

**Placement Groups:**

• **Cluster:** Places EC2 instances close together on the same rack for low latency.

• **Partition:** Spreads EC2 instances across partitions (separate racks) for fault tolerance.

• **Spread:** Spreads instances across racks to reduce the risk of simultaneous failures.

**Spot Instances & AMI Version Control:**

• **Spot Instances:** Cost-effective for non-critical workloads with potential interruptions.

• **AMI Version Control:** Keep track of updates and versions in case of rollback or updates to the image.

**S3 & Storage Gateways:**

• **S3 Access Points:** Helps manage access for multiple users by assigning different folders to users in an S3 bucket.

• **Storage Gateway Types:** Use cases for hybrid cloud storage like File Gateway, Tape Gateway, and Volume Gateway.

• **DataSync:** Automates data transfer between on-premises storage and AWS. Learn more [here](https://docs.aws.amazon.com/storagegateway/).

• **AWS Snow Family:** Offline data transfer device family, including:

• **Snowcone:** 8 TB storage, lightweight.

• **Snowball Edge:** Multiple variants (Storage Optimized: 80 TB, Compute Optimized: 42 TB).

**RDS & DynamoDB:**

• **Aurora Cloning:** Enables quick data replication.

• **DynamoDB Indexes:**

• **Local Secondary Index (LSI):** Allows multiple sort keys, max 5.

• **Global Secondary Index (GSI):** Allows different partition keys than the main table, max 20.

• **Global Tables:** Used across multiple regions.

• **DynamoDB Streams:** Captures all CRUD operations.

• **TTL (Time-to-Live):** Enables automatic data expiration.

• **RDS Proxy:** Manages database connections efficiently for serverless applications.

• **Secrets Manager:** A service for securely storing secrets like database credentials.

**AWS Monitoring & Security:**

• **CloudWatch:** Used for monitoring resources, creating alarms based on thresholds.

• **CloudTrail:** Tracks all API activity and data events.

• **GuardDuty:** Continuous threat monitoring and protection, detects suspicious activities like cryptocurrency mining.

• **WAF (Web Application Firewall):** Protects applications from SQL injections and XSS.

• **Lambda for WAF Authentication:** Can be used to handle authentication in integration with WAF.

• **Firewall Manager:** Manages security policies across AWS accounts.

**Load Balancers & Auto Scaling:**

• **Types of Load Balancers:**

• **Application Load Balancer (ALB):** Best for HTTP traffic (e.g., web browsers).

• **Network Load Balancer (NLB):** Best for low latency, high-throughput use cases like gaming.

• **Gateway Load Balancer:** For integrating third-party services like intrusion detection.

• **Auto Scaling Policies:**

• **Target Tracking:** Scales based on specific metrics (e.g., 60% CPU utilization).

• **Simple Scaling:** Triggered by an alarm breach.

• **Desired Capacity:** Total number of instances to maintain at any time.

**Amazon Kinesis:**

• **Kinesis Data Streams:** For collecting and processing real-time streaming data.

• **Kinesis Data Firehose:** Streams data to destinations like S3 or Redshift with minimal setup.

• **Managed Service for Flink:** Used for real-time stream processing.

• **Kinesis Video Streams:** For streaming video data in real-time for analytics.

**Backup & Recovery Strategies:**

• **Pilot Light:** Minimal setup in standby mode to reduce RPO and RTO.

• **Warm Standby:** Some backup servers are running, ensuring quick failover.

• **Multi-Site:** Two production setups running simultaneously for full redundancy.

• **RTO (Recovery Time Objective):** The time within which systems must be restored after failure.

• **RPO (Recovery Point Objective):** The maximum acceptable data loss measured in time.

**EventBridge:**

• **EventBridge:** Allows integration with third-party services and automation of events like snapshots or spot instance termination.

**AWS Direct Connect vs. Site-to-Site VPN:**

• **Direct Connect:** High bandwidth, low latency, and more secure with a dedicated connection.

• **Site-to-Site VPN:** Encrypted tunnel over the public internet, lower bandwidth, and higher latency.

• **Association vs. Propagation:** Used in VPN configuration for route distribution.

**GuardDuty Data Sources:**

• **Supported Sources:**

• **VPC Flow Logs**

• **CloudTrail Events**

• **API Gateway Logs**

• **DNS Logs**

• GuardDuty uses these sources to detect anomalies and threats.

**Amazon Macie:**

• **Macie:** Scans S3 buckets for sensitive data, helping with compliance and data security.

**Amazon Inspector:**

• **Inspector:** Security assessment for EC2, ECR, checks for vulnerabilities and compliance, such as CVEs.

**Miscellaneous Services:**

• **Global Accelerator:** Improves global application performance by reducing latency, working at layer 4 with Anycast IP.

• **AWS Glue:** An ETL (Extract, Transform, Load) tool for data processing.

• **Step Functions:** Used for building workflows as state machines.

**Instance Metadata:**

• **Instance Metadata Service:** Provides details about the EC2 instance itself, such as its ID, type, and region.

• Can be accessed through IP: 169.254.169.254.

• More info [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-IMDS-new-instances.html).