# DynamoDB Explained

DynamoDB is a NoSQL database service from Amazon that's designed for high-scale applications. Let's break down how it works and clarify the confusion around secondary indexes.

## Data Model Basics

- **Tables**: Collections of related data (like in SQL)
- **Items**: Individual records within tables (like rows)
- **Attributes**: Data fields within items (like columns)

DynamoDB is schema-less, meaning items in the same table can have different attributes. This makes it flexible but requires your application to handle potentially missing data.

## Primary Keys

In DynamoDB, your primary key can be:

1. **Simple Primary Key**: Just a partition key
2. **Composite Primary Key**: A partition key plus a sort key

The **partition key** determines which physical server stores your data. DynamoDB hashes this value to decide where data is stored. This is crucial for distributing data across multiple servers.

The **sort key** (optional) allows you to organize data within a partition. It creates a sorted index, making range queries efficient.

## Secondary Indexes Explained

Now for your main area of confusion - secondary indexes. These exist because your primary key design determines how you can efficiently query data, but you often need multiple efficient access patterns.

### Global Secondary Index (GSI)

A GSI lets you query using a completely different partition key than your table's primary key. It's "global" because it spans all partitions of your base table.

**Example**:

- Your main table uses `chatID` as the partition key (to group all messages in a chat)
- But you also need to find all messages from a specific user
- Solution: Create a GSI with `userID` as the partition key

It's like creating an alternative version of your table optimized for a different access pattern. You can choose which attributes to "project" (copy) to this index.

### Local Secondary Index (LSI)

An LSI keeps the same partition key as your main table but uses a different sort key. It's "local" because it's scoped to a single partition.

**Example**:

- Your main table uses `chatID` (partition key) and `messageID` (sort key)
- But you also need to find messages with the most attachments
- Solution: Create an LSI with `chatID` as partition key and `attachmentCount` as sort key

This lets you sort or filter items within a partition by a different attribute.

## Main Difference Between GSI and LSI

- **GSI**: Different partition key = completely different data distribution
- **LSI**: Same partition key but different sort key = different ordering within each partition

The key insight is that a GSI creates a whole new way to distribute your data, while an LSI just adds a new way to sort or filter within your existing partitions.

## When To Use Each

- Use a **GSI** when you need to query by a totally different attribute (finding data across partitions)
- Use an **LSI** when you need to sort or filter within existing partitions in a different way
