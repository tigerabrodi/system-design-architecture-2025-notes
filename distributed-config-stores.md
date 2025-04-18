# Etcd and ZooKeeper: Distributed Configuration Stores

Etcd and ZooKeeper are distributed configuration stores. They provide a reliable way to store critical information that multiple computers in a network need to share.

## Basic Function

These systems maintain a small amount of data (typically in kilobytes, not gigabytes) that needs to be:

- Consistent across all servers
- Highly available
- Reliable even when some servers fail

## Common Uses

They're typically used for:

1. Service discovery - helping servers find each other
2. Configuration management - storing settings all servers need
3. Leader election - deciding which server is in charge
4. Distributed locking - preventing conflicts when multiple servers work together

## How They Work

They use consensus algorithms (like Paxos or Raft) to ensure all servers agree on the data even during network problems or server failures.

Both systems store data in a structure similar to a file system, with paths and values.

## Differences Between Them

ZooKeeper:

- Older, created by Apache
- More mature and battle-tested
- Often used in Hadoop ecosystems

Etcd:

- Newer, written in Go
- Key component in Kubernetes
- Generally simpler to set up and use

## In the WhatsApp Design Example

In the WhatsApp design, they'd be used to track:

- Which chat servers are active
- How users are distributed across servers
- The health status of system components

They solve the problem of "who's responsible for what" in a system that's constantly changing as servers come online or go offline.
