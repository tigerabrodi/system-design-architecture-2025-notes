# Recommendation System Architecture

## Three-Stage Architecture

Modern recommendation systems typically use a three-stage architecture to make seemingly impossible problems tractable:

1. **Candidate Generation**: Create a smaller pool of potentially relevant items
2. **Ranking**: Score these candidates using more sophisticated models
3. **Re-ranking**: Apply business rules and constraints to the final results

This architecture solves the fundamental scale problem - trying to rank billions or trillions of items would be computationally prohibitive.

## Candidate Generation

The core scaling technique is to use multiple candidate generators to create a manageable subset of items:

- **Simple heuristic generators**:
  - Top videos on the platform
  - Videos from channels the user subscribes to
  - Recently popular content
- **Embedding-based generators**:
  - "Videos similar to ones you liked"
  - "Videos watched by similar users"

Generators produce maybe thousands of items from billions, creating a feasible set for further processing.

## Vector Databases for Similarity Search

For similarity-based recommendations, the infrastructure uses:

1. **Embeddings**: Neural networks convert items into numerical vectors
2. **Vector databases**: Specialized databases like Pinecone or FAISS store these vectors
3. **Approximate Nearest Neighbor algorithms**: Find similar items quickly

These databases use algorithms like Hierarchical Navigable Small Worlds (HNSW) to make what would be an O(n) operation into something much faster.

## Re-ranking for Business Logic

Instead of making the core ranking system handle every special case, re-ranking:

- Boosts new content for exploration
- Filters out blocked content
- Applies diversity constraints
- Manages sensitive content exposure

This pattern of "handle the core 99% case and adjust the edge cases later" is common in many large-scale systems.

## Infrastructure Implications

From an infrastructure perspective, this architecture means:

- You need a service for each candidate generator
- Vector databases require specialized storage and indexing
- The ranking system can use more compute-intensive models since it processes fewer items
- Re-ranking is relatively lightweight but must be highly available

This multi-stage approach demonstrates a key system design principle: sometimes the most elegant solution isn't to solve the whole problem at once, but to break it into more manageable pieces and develop specialized solutions for each.
