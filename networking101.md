# Key Networking Principles for System Design

## Protocol Layers and Their Functions

Networking operates as a layered system, each layer building on the one below:

- **Network Layer (Layer 3)**:

  - IP addresses (public vs. private)
  - Public addresses for internet-facing services
  - Private addresses for internal services

- **Transport Layer (Layer 4)**:

  - TCP: Reliable, ordered delivery with connection state
  - UDP: Fast but unreliable delivery, good for real-time applications
  - TCP is the default choice unless you specifically need UDP's lower latency

- **Application Layer (Layer 7)**:
  - HTTP/REST: Default for most APIs, widely supported
  - GraphQL: Flexible data fetching for changing frontend requirements
  - gRPC: High-performance services with efficient serialization
  - SSE: Server-to-client push notifications over HTTP
  - WebSockets: Bidirectional real-time communication
  - WebRTC: Peer-to-peer connections, primarily for audio/video

## Connection State and Performance

Understanding connection state is critical:

- TCP connections have setup/teardown overhead (three-way handshake)
- Connection establishment adds latency
- Maintaining connections requires server resources
- Stateful connections (like WebSockets) complicate scaling and deployment
- WebSockets require special handling for load balancing and failover

## Load Balancing Approaches

Two main approaches to load balancing:

- **Client-side Load Balancing**:

  - Client directly connects to servers
  - Good for internal services with few clients
  - Requires client awareness of all servers
  - Examples: DNS round-robin, gRPC client-side balancing

- **Dedicated Load Balancers**:
  - Acts as intermediary between clients and servers
  - Types: Layer 4 (TCP level) vs. Layer 7 (HTTP level)
  - Layer 4: Better for connection-oriented protocols (WebSockets)
  - Layer 7: Offers more routing options, HTTP features
  - Performs health checks to detect server failures

## Handling Global Scale and Latency

Geography impacts network performance:

- **Regionalization**: Group data and services by geographical regions
- **Collocation**: Keep related services physically close to minimize latency
- **Data Locality**: Store data close to where it's most frequently accessed
- **Content Delivery Networks (CDNs)**: Cache content at edge locations
- The speed of light creates minimum latency between regions (~80ms trans-Atlantic)

## Fault Tolerance

Network failures require specific handling:

- **Timeouts**: Always set reasonable timeouts to prevent indefinite waiting
- **Retries**: Automatically retry failed requests
- **Exponential Backoff**: Increase delay between retries to prevent overwhelming servers
- **Jitter**: Add randomness to retry intervals to prevent synchronized retries
- **Circuit Breakers**: Temporarily stop requests when a service is failing
- **Cascading Failures**: Understand how failures can propagate through dependent services
