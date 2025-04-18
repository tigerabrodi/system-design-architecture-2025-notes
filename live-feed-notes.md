# Key Technologies and Patterns in Facebook Live Comments System Design

## Server-Sent Events (SSE) vs WebSockets

SSE works well when:

- Communication is primarily one-way (server to client)
- You need a lightweight connection protocol
- You have read-heavy applications with infrequent writes

WebSockets are better when:

- You need bidirectional communication
- The application has a more balanced read/write ratio (like chat apps)
- Lower latency is critical for both directions

## Pagination Approaches

**Offset Pagination:**

- Simple implementation: `GET /resource?offset=100&limit=20`
- Becomes inefficient for large datasets
- Not stable with concurrent updates (can miss or duplicate items)

**Cursor Pagination:**

- Uses a unique identifier as reference point: `GET /resource?cursor=item_123&limit=20`
- More efficient for large datasets
- Stable with concurrent updates
- Works well with NoSQL databases like DynamoDB

## Pub/Sub Patterns

The design shows several approaches to publish-subscribe architecture:

**Simple Pub/Sub:**

- All servers subscribe to all topics
- Simple but inefficient at scale
- Every server processes every message

**Partitioned Pub/Sub:**

- Messages are partitioned by topic (videoId)
- Servers only subscribe to relevant topics
- Reduces processing overhead

**Intelligent Routing:**

- Uses Layer 7 load balancing with consistent hashing
- Routes related clients to the same server
- Minimizes cross-server message distribution

## Distributed System Coordination

**ZooKeeper/etcd:**

- Centralized service for maintaining configuration information
- Helps with service discovery and load distribution
- Manages dynamic mappings (which server handles which videoId)
- Allows coordination between distributed components

## Scaling Patterns

**Separation of Concerns:**

- Split write traffic (comment creation) from read traffic (comment viewing)
- Enables independent scaling of components

**Co-location Strategy:**

- Group related clients on the same server (viewers of same video)
- Reduces cross-server communication
- Improves efficiency of message distribution

**Dispatcher Service Pattern:**

- Alternative to pub/sub for direct routing
- Centralized service that knows where to send messages
- Maintains dynamic mapping of resources to servers

---

# Pub/Sub Inefficiencies at Scale

When all servers subscribe to all topics in a pub/sub system, it becomes inefficient at scale for several reasons:

1. **Processing Overhead**: Every server receives and must process every message, even if none of its connected clients need that message. If you have 1,000 servers and 1,000 topics, each server spends 99.9% of its processing power on messages it will ultimately discard.

2. **Network Bandwidth**: Messages are sent to every server, consuming network bandwidth unnecessarily. If a message is 1KB and you have 1,000 servers, a single message publication consumes 1MB of network bandwidth when only 1KB was actually needed.

3. **Memory Consumption**: Each server must maintain subscription state for every topic, which consumes memory that could be used for other purposes.

# Intelligent Routing in Detail

Intelligent routing becomes necessary when you need to optimize which servers handle which clients. Here's how it works in more detail:

1. **Layer 7 Load Balancer Configuration**: The load balancer examines the content of the request (like the videoId in HTTP headers or URL path) rather than just IP/port information.

2. **Consistent Hashing Implementation**:

   - The videoId is hashed to a value in a circular hash space
   - Servers are also mapped to points in this hash space
   - A client is routed to the server that appears next in the clockwise direction from its hash position
   - This ensures that when servers are added or removed, only a minimal fraction of clients need to be reassigned

3. **Practical Example**:
   - A viewer requests to watch video "abc123"
   - The load balancer applies a hash function: hash("abc123") = 745
   - It finds the server responsible for the hash range containing 745
   - All viewers of video "abc123" will be directed to the same server
   - If that server gets overloaded, the hash space can be split and some videos redistributed

# Centralized Dispatch vs. Pub/Sub

A centralized dispatch service can be better than pub/sub in certain scenarios:

1. **Reduced Message Duplication**: In pub/sub, a message is sent to all subscribers, even if many don't need it. A dispatcher sends each message exactly where it needs to go.

2. **Dynamic Adaptability**: A dispatcher service can have real-time awareness of the system state (which server is handling which clients) and make intelligent routing decisions. It can quickly adapt to changing conditions like server additions/removals or traffic spikes.

3. **Lower Infrastructure Requirements**: You don't need to maintain a separate pub/sub infrastructure with topics/channels. The dispatcher can use direct point-to-point communication.

4. **More Control Over Message Distribution**: The dispatcher can implement custom logic for message prioritization, batching, or filtering that might be harder to implement with a general-purpose pub/sub system.

The primary disadvantages are that the dispatcher becomes a potential bottleneck and single point of failure, so it needs to be highly available and scalable itself.
