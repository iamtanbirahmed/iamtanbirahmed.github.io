---
title: 'Building NaiveDFS: A High-Availability Distributed File System from Scratch'
date: 2026-03-15
permalink: /posts/2026/03/naivedfs-distributed-file-system/
tags:
  - Distributed Systems
  - Java
  - Spring Boot
  - gRPC
  - System Design
---

![NaiveDFS Logo](/images/naivedfs-logo.png){: .align-center width="150px"}

Distributed systems are the backbone of modern cloud computing. Understanding how they work often requires peeling back layers of abstraction. To deepen my understanding of large-scale storage systems like HDFS (Hadoop Distributed File System), I built **NaiveDFS**—a high-availability, distributed file system written in Java and Spring Boot.

NaiveDFS isn't just a simple file storage project; it's an exploration of consensus, replication, and metadata management in a distributed environment.

## What is NaiveDFS?

NaiveDFS is a distributed file system designed to handle large files by breaking them down into smaller chunks and replicating them across multiple storage nodes. It features a central **Master Node** for metadata management, multiple **Data Nodes** for physical storage, a **Control Plane** for API coordination, and a modern **Web Console** for ease of use.

![NaiveDFS Dashboard](/images/naivedfs-screenshot.png){: .align-center}

---

## The Architecture

Building a file system requires a clear separation of concerns. NaiveDFS is divided into four main components:

### 1. The Master Node: The Central Brain
The Master Node is the metadata store. It doesn't store the actual file data but knows exactly where every piece of it resides.
- **Metadata Management:** Maintains an in-memory index of all files and their logical blocks.
- **Replication Strategy:** Decides which Data Nodes should hold physical replicas of each chunk.
- **Heartbeats & Health Monitoring:** Regularly monitors Data Nodes to ensure they are alive and healthy.
- **Durability (WAL):** Implements a Write-Ahead Log (WAL) to ensure that the metadata index can be reconstructed if the Master node restarts.

### 2. Data Nodes: The Storage Workers
Data Nodes are the heavy lifters. They store physical file chunks (128MB by default) on their local disks.
- **Single Leader Replication:** When a chunk is written, it’s sent to a "Leader" Data Node, which then streams it to "Follower" nodes for replication, ensuring fault tolerance.
- **Block Reporting:** Periodically tells the Master which blocks they are currently storing.

### 3. Control Plane: The Gateway
The Control Plane acts as the entry point for clients. It provides RESTful APIs for uploading and downloading files.
- **Coordination:** It communicates with the Master to get block locations and then streams data directly to the Data Nodes using high-performance gRPC.
- **Chunk Slicing:** Automatically splits large files into manageable chunks before distribution.

### 4. Web Frontend: The Interface
A modern dashboard built with **Next.js** and **React**. It provides a glassmorphism-themed UI where users can drag and drop files, view the cluster status, and manage their storage effortlessly.

---

## Technical Deep Dive: The Write Flow

How does a file actually get stored in NaiveDFS?

1. **Upload Request:** A user uploads a file via the Web UI or API.
2. **Metadata Allocation:** The Control Plane asks the Master where the chunks should be stored.
3. **Block Placement:** The Master allocates block IDs and selects a set of Data Nodes (Leader + Followers).
4. **gRPC Streaming:** The Control Plane streams the file data directly to the **Leader Data Node**.
5. **Replication:** The Leader node simultaneously writes the data to its local disk and streams it to its assigned **Follower Data Nodes**.
6. **Acknowledgment:** Once the data is safely replicated, an acknowledgment is sent back to the client.

---

## Why gRPC?

For internal communication, performance is critical. Instead of traditional REST, NaiveDFS uses **gRPC** and **Protocol Buffers**. This allows for:
- **Low Latency:** Binary serialization is much faster than JSON.
- **Strong Typing:** Defined service contracts ensure consistency across the Master, Data Nodes, and Control Plane.
- **Streaming Support:** Perfectly suited for streaming large chunks of file data between nodes.

## Key Features & Goals

- **High Availability:** Replication ensures that data remains accessible even if a Data Node fails.
- **Scalability:** New Data Nodes can be added to the cluster to increase total storage capacity.
- **Fault Tolerance:** The system automatically detects node failures and can re-replicate blocks as needed.
- **Containerized:** The entire cluster is orchestrated with **Docker Compose**, making it easy to spin up a full multi-node environment in seconds.

## Conclusion

NaiveDFS was a challenging but rewarding project to build. It forced me to think about edge cases like network partitions, node failures, and data consistency. While it's "naive" in name, the principles it implements—metadata separation, single-leader replication, and decoupled control planes—are the same ones that power the most robust distributed systems in the world.

You can check out the full source code and documentation on my GitHub: [iamtanbirahmed/NaiveDFS](https://github.com/iamtanbirahmed/NaiveDFS)

---

*Interested in distributed systems? Let's connect on [GitHub](https://github.com/iamtanbirahmed) or [Twitter](https://twitter.com/iamtanbirsagar)!*
