## WebSockets: Why They're Essential

WebSockets are used here because messaging apps like WhatsApp require:

1. **Bidirectional communication**: Unlike HTTP where clients request and servers respond, WebSockets allow both sides to initiate communication at any time.

2. **Real-time messaging**: The low-latency requirement (<500ms) demands a persistent connection rather than polling or frequent HTTP requests.

3. **Connection efficiency**: Maintaining a single persistent connection is more efficient than establishing new connections for each message exchange.

4. **Push notifications**: Servers need to push messages to clients immediately when they arrive, without clients having to ask.

## Redis Pub/Sub: Core Traits and Purpose

Redis Pub/Sub is used as a message router between chat servers because:

1. **Lightweight channels**: Unlike Kafka (which has overhead per topic), Redis channels have minimal overhead, allowing millions of channels.

2. **Low latency**: Redis provides single-digit millisecond performance, essential for real-time chat.

3. **At-most-once delivery**: It's used for the "fast path" delivery, with another system (the Inbox table) handling durability requirements.

4. **Connection scaling**: It reduces the connection complexity from N² (all chat servers connecting to each other) to N+S (where N is the number of chat servers and S is the Redis nodes).

5. **Stateless design**: Redis Pub/Sub doesn't store messages, it just forwards them, making the architecture simpler.

## DynamoDB: Why It's Selected

DynamoDB is chosen because:

1. **Key-value performance**: It excels at fast lookups by primary keys, perfect for chat and user data.

2. **Scalability**: It can scale horizontally without manual sharding.

3. **Composite keys**: Support for composite keys allows efficient querying of relationships (like all participants in a chat).

4. **Global Secondary Indexes (GSIs)**: These allow efficient queries on non-primary attributes (like finding all chats for a user).

5. **High throughput**: It can handle the massive read/write requirements of billions of messages.

## Blob Storage for Media

Blob storage is used for media attachments because:

1. **Specialized purpose**: Unlike general databases, blob storage is designed specifically for large binary objects.

2. **Pre-signed URLs**: This pattern allows direct client uploading/downloading without routing through application servers.

3. **TTL support**: Media can be automatically expired after 30 days.

4. **Offloading bandwidth**: It removes the heavy lifting of media transfer from the core messaging infrastructure.
