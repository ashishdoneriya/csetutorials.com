---
title: Everything About Docker Storage Drivers
seoTitle: 
date: 2025-03-18T04:26:25+05:30
description: Docker storage drivers manage how containers store, modify, and access data using layered filesystems. This guide explains their types, workings, and impact.
slug: docker-storage-drivers-explained
categories:
  - Technology
tags:
  - docker
---
When you run a container in Docker, it needs to read and write files just like any other application. But since containers are isolated environments, they can't just write files anywhere on your system. This is where Docker storage drivers come in. They manage how files are stored, shared, and accessed within Docker containers. Different storage drivers have different ways of handling this, affecting speed, efficiency, and compatibility.

## What is a Docker Storage Driver?

A Docker storage driver is a component that decides how data inside a container is stored and managed. It allows containers to create and modify files, ensuring that storage is handled efficiently. Without a storage driver, a container wouldn’t be able to store any data persistently.

Storage drivers are used when you start a container and determine how files are managed between different layers of the container’s filesystem. This is important because containers are designed to be lightweight and share as much data as possible to avoid duplication.

## The Role of a Base Image

A **Docker base image** is the starting point of a container. It provides the initial filesystem, libraries, and dependencies required to run an application. Every Docker image is built on top of a base image, which is usually a lightweight Linux distribution like Ubuntu, Alpine, or Debian.

When a container runs, it does not modify the base image directly. Instead, Docker uses **layers** to manage changes efficiently. The storage driver controls how these layers are created, read, and written.

Here’s how it works:

1. The base image forms the **bottom-most layer**. It remains unchanged and is shared across multiple containers to save disk space.
2. When a container starts, a **read-write layer** is added on top of the base image. Any changes inside the container happen in this layer.
3. When the container is deleted, the read-write layer is also removed, but the base image remains intact.
4. Storage drivers manage how these layers interact, ensuring performance and efficiency.

## Where is a Docker Storage Driver Used?

Storage drivers come into play in multiple scenarios:

* When a container needs to store temporary files while running.
* When multiple containers need to share the same files efficiently.
* When Docker builds container images and needs to layer file changes.
* When you restart a container and want it to keep its data from a previous session.
* When optimizing storage space by sharing common layers between different containers.

## Types of Docker Storage Drivers

Docker supports multiple storage drivers, each suited for different use cases. The most commonly used ones are:

1. **OverlayFS (Overlay2 and Overlay)** – Default and most efficient for modern Linux systems.
2. **AUFS (Advanced Multi-Layered Unification File System)** – Used in older versions of Docker.
3. **Device Mapper** – Works at the block storage level instead of the filesystem level.
4. **Btrfs (B-Tree File System)** – An advanced filesystem supporting snapshots.
5. **ZFS (Zettabyte File System)** – A high-performance storage system often used in enterprise settings.
6. **VFS (Virtual File System)** – A simple but inefficient driver mostly used for testing.

Each driver has its own advantages and trade-offs, which we’ll cover next.

## OverlayFS (Overlay and Overlay2)

OverlayFS is the most commonly used storage driver in modern Linux systems. It works by stacking multiple layers of files on top of each other, making it efficient for Docker images.

### How OverlayFS Works

When you create a container, OverlayFS keeps the **base image** in a read-only layer. Any changes made inside the container are stored in a separate writable layer. This means:

* The original base image remains unchanged.
* Containers can share common files without duplicating them.
* Changes made by one container don’t affect others.

### Overlay vs. Overlay2

* **Overlay**: Uses a simpler approach but has issues with inode limits.
* **Overlay2**: The improved version that allows more layers and better performance.

**Pros:** Fast, efficient, and ideal for most Docker workloads.

**Cons:** Requires newer Linux kernels and may have issues with SELinux.

## AUFS (Advanced Multi-Layered Unification File System)

AUFS was one of the earliest storage drivers used by Docker but is now deprecated.

### How AUFS Works

AUFS allows multiple layers to be merged into a single view. It consists of:

* **Base Image Layers**: These remain read-only and are shared across multiple containers.
* **Writable Container Layer**: This is where the container writes data. Any modifications happen here without altering the base image.

Instead of duplicating files, AUFS references the same underlying data for efficiency.

**Pros:** Efficient storage usage and reduces file duplication.

**Cons:** Not included in the main Linux kernel and has been replaced by Overlay2.

## Device Mapper

Device Mapper is different from OverlayFS and AUFS because it works at the block storage level instead of the file system level. It creates thin-provisioned volumes for each container.

### How Device Mapper Works

Instead of layering files, it treats the storage as virtual disk space. This approach makes it more suitable for large-scale environments that need strong storage isolation.

**Pros:** Good for block-level storage and offers strong isolation.

**Cons:** Slower than Overlay2 and requires more configuration.

## Btrfs (B-Tree File System)

Btrfs is a modern file system that provides advanced storage features such as snapshots and deduplication.

### How Btrfs Works

Btrfs operates on a Copy-on-Write (COW) basis, meaning:

* When you modify a file, Btrfs does not overwrite the original. Instead, it creates a new copy of just the modified block while keeping the unchanged parts of the file the same.
* If you create a new file (e.g., `touch test1.txt`), it will simply be added to the writable layer without copying existing data.
* If you then modify `test1.txt`, only the changed parts of the file are copied, not the entire file.
* If you create another file (e.g., `touch test2.txt`), it will be stored separately and does not trigger a copy of previous files.

This means that Btrfs is efficient in managing storage by minimizing unnecessary duplication while ensuring data integrity.

**Pros:** Advanced storage features, good for large-scale applications.

**Cons:** Requires a dedicated Btrfs filesystem and is not widely used with Docker.

## ZFS (Zettabyte File System)

ZFS is another advanced file system known for reliability and performance.

### How ZFS Works

Like Btrfs, ZFS supports snapshots, compression, and deduplication. It’s often used in enterprise environments where data integrity is crucial.

**Pros:** High reliability and efficient storage management.

**Cons:** Requires specific setup and is not included in the Linux kernel by default.

## VFS (Virtual File System)

VFS is a basic storage driver mostly used for debugging and testing.

### How VFS Works

It simply copies files between containers and the host system without using layers.

**Pros:** Simple and works everywhere.

**Cons:** Poor performance and high disk space usage.

## Conclusion

Docker storage drivers control how data is stored and shared within containers. The right driver depends on your specific needs. For most users, **Overlay2** is the best option due to its efficiency and performance. However, advanced file systems like **ZFS and Btrfs** offer additional benefits for enterprise workloads. Understanding these drivers helps in optimizing storage, improving performance, and ensuring data persistence in containerized environments.