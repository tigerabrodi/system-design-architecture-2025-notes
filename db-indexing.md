# Database Indexing Overview

## The Problem Indexes Solve

Without indexes, databases have to scan through pages of data sequentially to find what you're looking for. This is inefficient:

- Data is organized in pages (8KB chunks)
- Each page holds ~100 rows
- A database with 100 million rows would have ~1 million pages
- Sequential scanning could take seconds (too long for users)

## B-tree Indexes

B-trees are the most common and versatile index type:

- Tree structure where each node contains sorted values
- Each node has pointers to child nodes or data pages
- Great for both exact matches and range queries
- Supports sorting operations

For example, if searching for users with age=51:

1. Load the root node
2. Follow pointers based on value comparisons (51 > 50, 51 < 55)
3. Quickly arrive at the page containing all users aged 51

For a range query like age > 51, the B-tree helps identify all the relevant pages.

## Hash Indexes

Hash indexes use a hashmap structure:

- Hash the search key (like an email address)
- Follow the pointer directly to the page with the data
- O(1) lookup time

Despite being theoretically faster for exact matches, hash indexes are rarely used in production databases because:

- B-trees are almost as fast for exact matches
- Hash indexes can't handle range queries or sorting
- They're mainly used in in-memory databases like Redis

## Geospatial Indexes

For two-dimensional data like latitude/longitude, B-trees aren't efficient. There are three main geospatial indexing approaches:

1. **Geohashing**:

   - Recursively divides the world into quadrants and assigns codes
   - Converts 2D coordinates into 1D strings
   - Nearby locations share similar prefixes
   - Popular in modern databases, including Redis
   - Can build a B-tree on these hashes

2. **Quad Trees**:

   - Recursively divides space into four quadrants
   - Only subdivides areas with high density
   - Less commonly used in production now

3. **R-trees**:
   - Evolution of quad trees with more dynamic clustering
   - Allows overlapping regions
   - Used in production systems like PostGIS

## Inverted Indexes

For text searching (especially when the search term can appear anywhere in the text), B-trees aren't effective. Instead, use inverted indexes:

- Maps each word/token to all documents containing it
- Essential for full-text search
- Used in Elasticsearch, PostgreSQL full-text search, etc.

## Secondary Indexes in DynamoDB

Referring back to your earlier confusion about DynamoDB's secondary indexes:

- **Global Secondary Index (GSI)**: Allows querying on attributes other than the primary partition key. Creates a completely new distribution of your data using a different partition key.

- **Local Secondary Index (LSI)**: Keeps the same partition key but uses a different sort key. Lets you organize data within a partition in multiple ways.

## When to Use Each Index Type

1. Full table scan if:

   - You don't need efficient access
   - You have very few rows

2. B-tree for:

   - Most query patterns
   - Exact matches and ranges
   - Sorting needs

3. Inverted index for:

   - Text search (words anywhere in text)

4. Geospatial index for:

   - Location data
   - Proximity searches

5. Hash index rarely, but possibly for:
   - In-memory exact matches

In system design interviews, understanding which index fits your access pattern is much more important than implementation details.
