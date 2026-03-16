---
title: "NaiveDFS: Distributed File System"
excerpt: "A high-availability, distributed file system written in Java/Spring Boot inspired by Hadoop HDFS.<br/><img src='/images/naivedfs-screenshot.png'>"
collection: portfolio
---

NaiveDFS is a high-availability, distributed file system designed to handle large files by breaking them down into smaller chunks and replicating them across multiple storage nodes. 

Key features include:
*   **Master Node** for metadata management and replication strategy.
*   **Data Nodes** for physical storage and single-leader replication.
*   **gRPC** for high-performance internal communication.
*   **Next.js Dashboard** for managing the cluster.

[Read the detailed blog post](/posts/2026/03/naivedfs-distributed-file-system/)

[View on GitHub](https://github.com/iamtanbirahmed/NaiveDFS)
