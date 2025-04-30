---
title: 'Understanding the Amazon S3 Glacier Restore Process: How It Works, Request Types, and Key Considerations'
seoTitle: How Amazon S3 Glacier restore process works
date: 2024-09-22T14:00:11+05:30
description: Learn how to restore data from Amazon S3 Glacier, the different retrieval options, and manage restored objects for temporary or permanent access in S3 Standard.
slug: s3-glacier-restore-process-retrieval-options
categories:
  - AWS
tags:
  - AWS
---
Amazon S3 Glacier and Glacier Deep Archive are cost-effective storage classes designed for **long-term archival** of data that is infrequently accessed. These storage classes provide durability and low-cost storage, making them ideal for regulatory, compliance, or backup purposes. However, when you need to retrieve data from **S3 Glacier** or **Glacier Deep Archive**, you must initiate a **restore process** to temporarily make the data accessible.

In this article, I’ll explain how the **restore process** works, the different **restore request types**, and discuss the **free tier** options that can impact the cost of Glacier restores. I will also explore cost considerations and the impact of the free tier on different restore types.

## The Restore Process in Glacier and Glacier Deep Archive

When an object is stored in **S3 Glacier** or **Glacier Deep Archive**, it is placed in **cold storage**, meaning it is not immediately accessible for retrieval. To interact with an object, you must submit a **restore request**. The restore process temporarily makes the object available in the **S3 Standard** storage class for a limited time. Importantly, the original object remains in Glacier storage, while the restored version is available temporarily.

### Key Steps of the Restore Process:

1.  **Initiate the Restore Request**: A restore request is made through the AWS Management Console, CLI, SDK, or API. During this request, you specify the **retrieval speed (restore request type)** and the **restore duration**.
2.  **Temporary Copy Availability**: Once the object is restored, a **temporary copy** is placed in the **S3 Standard** storage class under the same key (path and name) as the original object. This copy is available for the specified number of days (e.g., 7 days, 30 days).
3.  **Original Object in Glacier**: Even after the object is restored, the **original object remains in Glacier** or Glacier Deep Archive. The temporary version is deleted after the restore period expires.

## Restore Request Types

There are three types of **restore requests**, which vary based on the speed of retrieval and cost. Depending on how quickly you need to access your data, you can select from the following options:

### Expedited Restore

*   **Time**: Usually restores data in **1 to 5 minutes**.
*   **Cost**: This is the **most expensive** option and is suitable for situations where immediate access to archived data is necessary, such as in urgent cases or business-critical operations.
*   **Best For**: Small objects or emergency restores when data is needed immediately.

### Standard Restore

*   **Time**: Restores objects in **3 to 5 hours**.
*   **Cost**: This is the **most common** restore option and balances cost and retrieval time. It is typically used for regular access to archived data that isn’t immediately critical.
*   **Best For**: Routine restores where data is needed within a few hours, such as for scheduled processing or regular use cases.

### Bulk Restore

*   **Time**: Restores data in **5 to 12 hours**.
*   **Cost**: Bulk is the **least expensive** option and is ideal for restoring large volumes of data where immediate access is not required.
*   **Best For**: Large-scale data restoration for archival projects or when you are retrieving large datasets and can afford to wait for several hours.

## Choosing the Right Restore Request Type

*   **Expedited**: Best for urgent or small-scale data retrieval where speed is critical, but the cost is higher.
*   **Standard**: The most balanced option, offering moderate speed at a reasonable cost for typical retrieval needs.
*   **Bulk**: The most cost-effective, suitable for large datasets or infrequent access where time isn’t an issue.

## Understanding the Free Tier

The **AWS Free Tier** offers a **monthly allowance** of **10 GB of data retrievals** from S3 Glacier or Glacier Deep Archive. This free tier applies to **Standard** and **Bulk** retrievals, allowing you to restore smaller amounts of data each month without incurring costs. However, it does not cover **Expedited** restores, which are always chargeable.

### Key Points About the Free Tier

* **What’s Free**: You get **10 GB of data retrievals** for free from Glacier and Glacier Deep Archive each month when using **Standard** or **Bulk retrievals**.
* **What’s Not Free**: **Expedited** restores are **not covered**, and any data restored using this method will incur charges.
* **Exceeding the Free Tier**: If you restore more than 10 GB in a month, any additional data will be charged based on AWS pricing for Standard or Bulk restores.
* The **free tier** is particularly useful for retrieving smaller datasets or testing retrievals without incurring costs. For businesses with infrequent and small restore needs, this can significantly lower costs.

## Where Are Restored Objects Stored?

Once you initiate a restore request, the restored object is made temporarily available in the **same S3 bucket and under the same key (name)** as the original object. The key point is that the object’s location and name do not change during the restore process. For example, if the object is stored as `s3://my-bucket/videos/video1.mp4`, then after restoration, the object is temporarily accessible at the same path, `s3://my-bucket/videos/video1.mp4`, but now in the **S3 Standard** storage class. You can access the object, copy it, or download it during the restore period.

### Important Points:

*   The restored version appears under the same name and path, making it easy to interact with the data.
*   The **original object remains in Glacier** or Glacier Deep Archive, and is not moved out of cold storage during the restore.

## Temporary Nature of the Restored Copy

The restored object is a **temporary copy** made available for a specified period, which is typically set during the restore request. Common durations are **7 days**, **30 days**, or other custom periods. After the specified restore period, the restored version is automatically deleted.

*   **Expiration**: Once the restore period ends, the temporary copy in S3 Standard is deleted. The original object remains in Glacier or Glacier Deep Archive, and you can initiate another restore if needed in the future.
*   **Permanent Access**: If you require ongoing access to the restored data, you should copy it to another storage class (e.g., **S3 Intelligent-Tiering** or **S3 Standard**) before the restore period expires.

## Example Scenario

Let’s say you have a large video file stored in **S3 Glacier Deep Archive** with the key `s3://my-bucket/videos/video1.mp4` and you need to restore this file for editing purposes. Here’s how the process works:

1.  **Restore Request**: You initiate a restore request using the **Standard** retrieval option with a **7-day restore period**.
2.  **Temporary Availability**: After about 3-5 hours, the file `video1.mp4` is temporarily available in the **S3 Standard** storage class at the same location, `s3://my-bucket/videos/video1.mp4`.
3.  **Access the File**: You can now access, download, or copy the file. This restored version will remain accessible for 7 days.
4.  **Expiration**: After 7 days, the temporary copy is deleted, but the original video remains in **Glacier Deep Archive**.

If you need ongoing access to the video beyond the 7-day period, it’s best to copy it to another storage class, such as **S3 Standard** or **S3 Intelligent-Tiering**, before the temporary copy expires.

## Cost Considerations

The cost of restoring objects from Glacier or Glacier Deep Archive includes:

1.  **Restore Request Charges**: You are charged based on the **retrieval tier** you select (Expedited, Standard, or Bulk). Expedited is the most expensive, while Bulk is the cheapest.
2.  **Temporary Storage Costs**: While the object is temporarily restored in **S3 Standard**, you are charged for storing the restored copy based on the size of the object and the number of days it remains available.

These costs can vary significantly, especially if restoring large datasets. Carefully choose the retrieval tier and restore period to optimize costs.

## Managing Restored Objects

Once an object is restored, you have several options for managing it:

*   **Use During the Restore Period**: Access, download, or manipulate the restored file while it is temporarily available in the S3 Standard storage class.
*   **Copy to Another Storage Class**: If you need to keep the object accessible beyond the restore period, copy it to a permanent storage class (e.g., **S3 Standard**, **S3 Intelligent-Tiering**).
*   **Let It Expire**: If you only need temporary access, simply let the restored copy expire after the set period. The original object will still reside in Glacier or Glacier Deep Archive.

## Conclusion

The restore process for objects in **S3 Glacier** and **Glacier Deep Archive** temporarily moves data from cold storage into **S3 Standard**, enabling you to access or download the data. With various **restore request types** (Expedited, Standard, and Bulk), you can balance cost and retrieval speed based on your needs.

Understanding the restore process is crucial for managing costs and ensuring that data is available when needed. By selecting the appropriate retrieval tier and carefully planning how long you need the data restored, you can optimize your interaction with Glacier storage while controlling costs. If ongoing access is required, copying the restored object to a more suitable storage class like **S3 Intelligent-Tiering** before the expiration period is the best approach.