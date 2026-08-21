# System Design Learning Roadmap

A structured roadmap for learning system design from the lowest practical layer—data and application code—to distributed systems and large-scale architecture.

## Quick navigation

- [How to use this roadmap](#how-to-use-this-roadmap)
- [The seven layers](#the-seven-layers)
- [Layer 1: Data and storage](#layer-1-data-and-storage)
- [Layer 2: Application and code](#layer-2-application-and-code)
- [Layer 3: Communication and networking](#layer-3-communication-and-networking)
- [Layer 4: Messaging and integration](#layer-4-messaging-and-integration)
- [Layer 5: Server and infrastructure](#layer-5-server-and-infrastructure)
- [Layer 6: Distributed systems](#layer-6-distributed-systems)
- [Layer 7: System architecture and system design](#layer-7-system-architecture-and-system-design)
- [Cross-cutting concerns](#cross-cutting-concerns)
- [How the layers connect](#how-the-layers-connect)
- [Recommended learning order](#recommended-learning-order)
- [Practice projects](#practice-projects)
- [System design interview workflow](#system-design-interview-workflow)
- [Mastery checklist](#mastery-checklist)
- [Glossary](#glossary)

## How to use this roadmap

System design is not one isolated subject. It is the discipline of combining many lower-level engineering concepts to build a system that satisfies functional and non-functional requirements.

A useful mental model is:

```text
Level 7  System architecture and system design
            ↑
Level 6  Distributed systems
            ↑
Level 5  Server and infrastructure
            ↑
Level 4  Messaging and integration
            ↑
Level 3  Communication and networking
            ↑
Level 2  Application and code
            ↑
Level 1  Data and storage
```

Do not try to memorize complete architectures first. Learn the building blocks, understand their trade-offs, and then practice combining them.

For every technology or pattern, ask four questions:

1. What problem does it solve?
2. How does it work?
3. What does it cost or complicate?
4. When should I choose it instead of an alternative?

That reasoning is more important than memorizing product names.

## The seven layers

| Layer | Main question | Typical topics |
| --- | --- | --- |
| 1. Data and storage | How is data stored, queried, protected, and scaled? | SQL, NoSQL, indexes, transactions, replication, partitioning |
| 2. Application and code | How does one application process a request safely and efficiently? | application layers, concurrency, transactions, background jobs |
| 3. Communication and networking | How do clients and services communicate? | HTTP, REST, WebSocket, SSE, gRPC, DNS, TCP |
| 4. Messaging and integration | How do independent components communicate asynchronously? | queues, Kafka, RabbitMQ, pub/sub, retries, DLQ |
| 5. Server and infrastructure | Where does the application run and how is traffic delivered? | Linux, Nginx, containers, load balancers, CDN, Kubernetes |
| 6. Distributed systems | How do multiple machines behave as one reliable system? | consistency, availability, replication, consensus, resilience |
| 7. System architecture and system design | How should all components be combined for a real product? | requirements, capacity, APIs, data model, scaling, trade-offs |

## Layer 1: Data and storage

This layer answers the question: **where does the system's state live?**

Most system design decisions eventually affect data. A system can often survive a temporary application-server failure, but losing or corrupting persistent data can be catastrophic.

### 1.1 Relational databases

Learn:

- tables, rows, and columns
- primary keys and foreign keys
- relationships: one-to-one, one-to-many, many-to-many
- normalization and denormalization
- joins
- constraints
- views
- stored procedures and triggers at a conceptual level
- schema migrations

Typical technologies:

- PostgreSQL
- MySQL
- MariaDB
- SQL Server
- Oracle Database

Understand why relational databases are strong when the domain benefits from structured schemas, relationships, constraints, and transactions.

### 1.2 Indexes

Indexes are one of the most important database topics for backend engineers.

Learn:

- B-tree indexes
- hash indexes conceptually
- clustered vs non-clustered indexes
- composite indexes
- covering indexes
- index selectivity
- leftmost-prefix behavior
- read amplification vs write amplification
- why too many indexes slow writes
- query execution plans

You should be able to explain why this query may need an index:

```sql
SELECT id, customer_id, total_amount
FROM orders
WHERE customer_id = ?
ORDER BY created_at DESC
LIMIT 20;
```

and why an index such as `(customer_id, created_at)` may help depending on the database and query plan.

### 1.3 Transactions and ACID

Learn the ACID properties:

- **Atomicity** — all operations in a transaction succeed or all fail.
- **Consistency** — a transaction preserves defined data rules.
- **Isolation** — concurrent transactions should not incorrectly interfere with one another.
- **Durability** — committed data survives failures according to the database's durability guarantees.

Learn common isolation levels:

- Read Uncommitted
- Read Committed
- Repeatable Read
- Serializable

Understand anomalies such as:

- dirty reads
- non-repeatable reads
- phantom reads
- lost updates

### 1.4 Locking and concurrency control

Learn:

- optimistic locking
- pessimistic locking
- row locks
- table locks
- deadlocks
- MVCC
- version columns

Example use case: preventing two users from purchasing the last available item at the same time.

### 1.5 SQL vs NoSQL

Do not treat this as a competition. They solve overlapping but different problems.

Learn major NoSQL categories:

| Category | Model | Typical use cases |
| --- | --- | --- |
| Key-value | key → value | caching, sessions, counters |
| Document | JSON-like documents | flexible domain objects, content |
| Wide-column | partitioned sparse rows | very large distributed datasets |
| Graph | nodes and edges | relationship-heavy queries |
| Time-series | timestamp-oriented records | metrics, telemetry, IoT |

Typical technologies include Redis, MongoDB, Cassandra, DynamoDB, Neo4j, and specialized time-series databases.

The important skill is deciding based on access patterns, consistency requirements, scale, operational complexity, and data relationships.

### 1.6 Caching

Learn:

- local/in-process cache
- distributed cache
- browser/client cache
- CDN cache
- database/query cache concepts

Common patterns:

- cache-aside
- read-through
- write-through
- write-behind

Important problems:

- cache invalidation
- stale data
- cache stampede
- cache penetration
- hot keys
- eviction policies
- TTL selection

Typical technology:

- Redis

### 1.7 Replication

Replication creates copies of data on multiple nodes.

Learn:

- leader/follower replication
- multi-leader replication conceptually
- leaderless replication conceptually
- synchronous vs asynchronous replication
- read replicas
- replication lag
- failover

Replication can improve availability and read scalability, but it creates consistency and operational trade-offs.

### 1.8 Partitioning and sharding

Partitioning divides a dataset into smaller pieces.

Learn:

- horizontal partitioning
- vertical partitioning
- range-based sharding
- hash-based sharding
- directory-based sharding
- consistent hashing conceptually
- shard key selection
- hot partitions
- rebalancing
- cross-shard queries
- cross-shard transactions

A bad shard key can make a theoretically scalable system perform poorly.

### 1.9 Data modeling for access patterns

In system design, start from questions such as:

- What are the most common reads?
- What are the most common writes?
- Which queries need low latency?
- Which fields must be unique?
- Which data must be strongly consistent?
- Which data can be eventually consistent?
- What data grows fastest?

Design storage around actual access patterns, not only around entity diagrams.

### Layer 1 exit criteria

Before moving on, you should be able to explain:

- why an index can improve reads but hurt writes
- when a transaction is necessary
- SQL vs NoSQL trade-offs
- cache-aside and cache invalidation
- replication vs sharding
- how to choose a primary key and a shard key
- how data consistency requirements affect storage design

## Layer 2: Application and code

This layer answers: **how does a single application receive work, execute business logic, and access data?**

### 2.1 Application structure

A common backend structure is:

```text
Client
  ↓
Controller / API layer
  ↓
Service / business layer
  ↓
Repository / data-access layer
  ↓
Database
```

Learn the responsibilities of:

- controllers
- services
- repositories/DAOs
- domain models
- DTOs
- validators
- mappers

The exact architecture may differ, but separation of concerns remains important.

### 2.2 Dependency injection and inversion of control

For Java/Spring developers, understand:

- dependency injection
- inversion of control
- bean lifecycle at a useful level
- constructor injection
- interface-based abstractions

These are not distributed-system concepts, but they affect how maintainable services are built.

### 2.3 Request lifecycle

Understand what happens after an HTTP request reaches your backend:

```text
Socket accepted
    ↓
Web server / application server
    ↓
Filter / middleware
    ↓
Authentication / authorization
    ↓
Routing
    ↓
Controller
    ↓
Business logic
    ↓
Database / cache / external service
    ↓
Response serialization
    ↓
HTTP response
```

### 2.4 Processes, threads, and concurrency

Learn:

- process vs thread
- thread lifecycle
- race conditions
- critical sections
- mutex/lock concepts
- semaphores
- atomic operations
- thread pools
- context switching
- CPU-bound vs I/O-bound work

For Java, study:

- `Thread`
- `Runnable`
- `Callable`
- `ExecutorService`
- `Future`
- `CompletableFuture`
- concurrent collections
- synchronization primitives

### 2.5 Synchronous vs asynchronous execution

**Synchronous** usually means the caller waits for an operation to complete before continuing.

```text
Caller
  ↓
Service A
  ↓
Service B
  ↓
Result
  ↓
Caller continues
```

**Asynchronous** execution allows work to continue independently.

```text
Caller
  ↓
Service A
  ↓
Queue
  ↓
Worker
```

The caller may receive an acknowledgement before the worker completes the job.

Do not confuse these terms with blocking/non-blocking. They are related but describe different aspects of execution and coordination.

### 2.6 Blocking vs non-blocking I/O

Learn the difference between:

- blocking calls
- non-blocking calls
- event loops
- reactive/event-driven I/O

Understand why a server handling thousands of mostly waiting I/O operations may benefit from non-blocking approaches, while simple blocking code can remain the better engineering choice for many applications.

### 2.7 Application-level transactions

Learn:

- transaction boundaries
- connection pools
- database transaction propagation concepts
- rollback behavior
- why holding transactions open during slow network calls is dangerous

For Java/Spring, understand what `@Transactional` does conceptually and where transaction boundaries should live.

### 2.8 Background jobs

Typical background work includes:

- email sending
- image processing
- report generation
- data imports
- scheduled cleanup
- notification delivery

Learn when work belongs inside the request path and when it should be delegated to background processing.

### 2.9 Idempotency at application level

An operation is idempotent when repeating the same logical request does not create unintended additional effects.

Important use cases:

- payment requests
- order creation
- webhook processing
- message consumers
- retries

Common strategies:

- idempotency keys
- unique constraints
- processed-message tables
- compare-and-set/version checks

### Layer 2 exit criteria

You should be able to explain:

- the lifecycle of a backend request
- threads and thread pools
- sync vs async
- blocking vs non-blocking
- where transaction boundaries belong
- why idempotency matters
- when work should become a background job

## Layer 3: Communication and networking

This layer answers: **how do clients, servers, and services exchange data?**

### 3.1 Networking fundamentals

Learn enough networking to reason about latency and failure.

Topics:

- IP addresses
- ports
- TCP
- UDP conceptually
- connection establishment
- sockets
- DNS
- TLS
- HTTP
- proxies
- timeouts

You do not need to become a network engineer, but you should understand what happens between a client and server.

### 3.2 DNS

Learn the basic resolution flow:

```text
User enters domain
      ↓
DNS resolver
      ↓
Authoritative DNS
      ↓
IP address
      ↓
Client connects to server/load balancer
```

Understand:

- A/AAAA records
- CNAME conceptually
- TTL
- DNS caching
- why DNS changes are not always immediate

### 3.3 HTTP fundamentals

Learn:

- methods: GET, POST, PUT, PATCH, DELETE
- status codes
- headers
- request/response bodies
- cookies
- keep-alive
- HTTP/1.1 vs HTTP/2 vs HTTP/3 at a conceptual level
- TLS/HTTPS

### 3.4 REST

REST is an architectural style commonly used for request/response APIs over HTTP.

Study:

- resources
- resource identifiers
- HTTP semantics
- stateless requests
- idempotent methods
- pagination
- filtering
- sorting
- versioning
- error design

REST is usually a strong default for conventional CRUD and request/response APIs.

### 3.5 WebSocket

WebSocket creates a persistent, full-duplex connection between client and server.

Useful for:

- chat
- multiplayer games
- live collaboration
- trading dashboards
- presence systems
- real-time notifications where low-latency bidirectional communication matters

Trade-offs:

- connection state must be managed
- scaling requires connection-aware infrastructure
- load balancing becomes more involved
- reconnect and heartbeat behavior must be designed

### 3.6 REST vs WebSocket

| Question | REST/HTTP | WebSocket |
| --- | --- | --- |
| Communication | Request/response | Full duplex |
| Connection | Usually independent HTTP requests over reusable connections | Long-lived logical connection |
| Server push | Limited; often polling/SSE/webhooks | Native bidirectional messaging |
| CRUD APIs | Excellent fit | Usually unnecessary |
| Real-time chat | Possible but awkward with polling | Strong fit |
| Infrastructure complexity | Lower | Higher |
| Stateless scaling | Easier | More connection-aware |

The system-design question is not "Which is better?" It is "Which communication model matches the product requirements?"

### 3.7 Server-Sent Events

SSE provides a long-lived HTTP connection through which the server sends events to the client.

Useful when:

- communication is mainly server → client
- browser support is sufficient
- you want something simpler than bidirectional WebSocket communication

Examples:

- live feeds
- job progress updates
- notifications

### 3.8 Polling and long polling

**Polling:** client asks the server repeatedly for updates.

```text
Client → Any update?
Client ← No
Client → Any update?
Client ← No
Client → Any update?
Client ← Yes
```

**Long polling:** the server holds the request until data becomes available or a timeout is reached.

Understand their simplicity and their extra request/connection overhead.

### 3.9 gRPC

gRPC is commonly used for efficient service-to-service communication.

Learn conceptually:

- Protocol Buffers
- strongly defined contracts
- binary serialization
- unary calls
- streaming
- internal service communication

Compare REST and gRPC based on interoperability, browser/client requirements, operational tooling, contracts, and performance needs.

### 3.10 GraphQL

Learn:

- schema
- queries
- mutations
- resolvers
- client-selected fields
- over-fetching/under-fetching trade-offs
- N+1 query problems
- caching complexity

Use it when its flexibility solves a real client/API problem, not merely because it is fashionable.

### 3.11 Webhooks

Webhooks are HTTP callbacks triggered by events.

Important concerns:

- signature verification
- retries
- duplicate delivery
- idempotency
- ordering
- timeout handling

### 3.12 Timeouts and retries

Every remote call can fail or become slow.

Learn:

- connection timeout
- read/request timeout
- retry policies
- exponential backoff
- jitter
- retryable vs non-retryable failures
- retry storms

Retries without careful design can multiply load during an incident.

### Layer 3 exit criteria

You should be able to choose among:

- REST
- WebSocket
- SSE
- polling
- gRPC
- GraphQL
- webhooks

and explain the trade-offs of each choice.

## Layer 4: Messaging and integration

This layer answers: **how can components communicate without requiring the receiver to complete work immediately?**

### 4.1 Message queues

A queue separates producers from consumers.

```text
Producer
   ↓
 Queue
   ↓
Consumer
```

Benefits can include:

- asynchronous processing
- load smoothing
- failure isolation
- buffering
- independent scaling of producers and consumers

### 4.2 Producer and consumer

Learn:

- producer
- consumer
- queue
- topic
- partition
- offset
- acknowledgement
- consumer group

Exact meanings differ by product, so learn the concepts first and then the implementation details.

### 4.3 Kafka

Kafka is commonly used as a distributed event-streaming platform.

Learn:

- topics
- partitions
- offsets
- brokers
- producers
- consumers
- consumer groups
- retention
- partition ordering
- replication

Common use cases:

- event streaming
- event-driven integration
- activity pipelines
- log/event collection
- change propagation
- asynchronous workflows

### 4.4 RabbitMQ

RabbitMQ is a message broker commonly used for queue-based messaging.

Learn:

- exchanges
- queues
- bindings
- routing keys
- acknowledgements
- prefetch
- dead-letter exchanges/queues

It is often a good fit for task queues and routing-oriented messaging patterns.

### 4.5 Kafka vs RabbitMQ

Avoid reducing the comparison to "Kafka is faster" or "RabbitMQ is simpler."

Compare them by:

- retention model
- consumption model
- replay requirements
- ordering requirements
- routing needs
- throughput
- operational complexity
- event-stream vs task-queue semantics

### 4.6 Publish/subscribe

In pub/sub, one event may be consumed by multiple independent subscribers.

```text
              ┌→ Email service
Order event ──┼→ Analytics service
              └→ Inventory service
```

This reduces direct coupling but increases the need for observability, schema governance, idempotency, and failure handling.

### 4.7 Delivery semantics

Understand the practical meaning of:

- at-most-once
- at-least-once
- effectively-once processing

"Exactly once" should always be examined carefully because guarantees depend on the boundaries of the system and the technologies involved.

### 4.8 Ordering

Ask:

- Is global ordering required?
- Is per-user or per-order ordering enough?
- Can events be processed out of order?

Global ordering is expensive and often unnecessary.

### 4.9 Retry and dead-letter queues

Typical flow:

```text
Main queue
   ↓
Consumer fails
   ↓
Retry with backoff
   ↓
Fails repeatedly
   ↓
Dead-letter queue
```

A DLQ is not a complete solution. You still need monitoring, investigation, replay/recovery procedures, and idempotent processing.

### 4.10 Event-driven architecture

An event represents something that happened:

- `OrderCreated`
- `PaymentCompleted`
- `UserRegistered`

Producers publish facts; consumers react to them.

Learn the difference between:

- commands
- events
- queries

### 4.11 Outbox pattern

A classic problem:

1. application writes an order to the database
2. application publishes `OrderCreated`
3. the database commit succeeds but message publishing fails

Now the system state and event stream disagree.

The transactional outbox pattern stores the event in the same database transaction as the business change, then publishes it asynchronously.

### 4.12 Saga pattern

A distributed business transaction may span multiple services.

Example:

```text
Create order
   ↓
Reserve inventory
   ↓
Charge payment
   ↓
Create shipment
```

If shipping fails, the system may need compensating actions rather than one global ACID transaction.

Learn:

- choreography
- orchestration
- compensating transactions

### Layer 4 exit criteria

You should understand:

- queues and pub/sub
- Kafka and RabbitMQ at a conceptual level
- retries and DLQs
- duplicate messages
- ordering
- idempotent consumers
- outbox pattern
- saga pattern

## Layer 5: Server and infrastructure

This layer answers: **how does the application run, receive traffic, and scale operationally?**

### 5.1 Operating-system fundamentals

For backend engineers, learn useful Linux concepts:

- process management
- file permissions
- environment variables
- sockets and ports
- memory and CPU usage
- disk usage
- signals
- logs
- basic shell commands

### 5.2 Web server and reverse proxy

Typical request path:

```text
Internet
   ↓
Reverse proxy / load balancer
   ↓
Application server
```

Nginx can commonly provide:

- reverse proxying
- TLS termination
- static-file serving
- compression
- routing
- rate limiting
- caching in some architectures

### 5.3 Load balancing

Load balancing distributes traffic across multiple instances.

```text
             Load balancer
            /      |      \
         App 1   App 2   App 3
```

Learn:

- Layer 4 vs Layer 7 load balancing conceptually
- round robin
- least connections
- weighted routing
- health checks
- sticky sessions
- connection draining

### 5.4 Horizontal vs vertical scaling

**Vertical scaling:** make one machine larger.

**Horizontal scaling:** add more machines/instances.

Understand the advantages and limits of each approach.

### 5.5 Stateless application servers

Stateless services are easier to scale horizontally because any healthy instance can handle a request.

Move shared state such as sessions to appropriate external storage when necessary.

### 5.6 Containers

Learn Docker concepts:

- image
- container
- Dockerfile
- layers
- volumes
- networks
- environment variables
- container registry

Understand the difference between packaging an application and orchestrating many application instances.

### 5.7 Container orchestration

Learn Kubernetes conceptually after Docker:

- Pod
- Deployment
- Service
- Ingress/Gateway concepts
- ConfigMap
- Secret
- liveness/readiness probes
- autoscaling
- rolling deployments

You do not need deep Kubernetes expertise to learn system design, but you should understand why orchestration exists.

### 5.8 CDN

A CDN serves content from geographically distributed edge locations.

Useful for:

- static assets
- images
- videos
- downloads
- cacheable API/content responses in suitable cases

Benefits include lower latency and reduced origin load.

### 5.9 Object storage

Do not store large binary objects in the application server's local filesystem if multiple stateless instances must access them.

Learn object-storage concepts for:

- images
- videos
- backups
- documents
- large generated files

Typical technology example: Amazon S3-compatible object storage.

### 5.10 Deployment strategies

Learn:

- rolling deployment
- blue/green deployment
- canary deployment
- rollback
- feature flags

### 5.11 Autoscaling

Possible signals:

- CPU
- memory
- requests per second
- queue depth
- custom application metrics

Scaling based on the wrong metric can make incidents worse.

### Layer 5 exit criteria

You should be able to draw and explain:

```text
User
  ↓
DNS
  ↓
CDN / edge
  ↓
Load balancer
  ↓
Application instances
  ↓
Cache / database / messaging
```

and describe how an application can be deployed and horizontally scaled.

## Layer 6: Distributed systems

This layer answers: **what changes when one logical system spans multiple machines and failures are normal?**

Distributed systems are difficult because the network is not perfectly reliable, clocks are not perfectly synchronized, nodes fail independently, and messages can be delayed or duplicated.

### 6.1 Fundamental failure model

Assume that:

- requests can time out
- responses can be lost
- servers can crash
- dependencies can become slow
- network partitions can occur
- messages can be duplicated
- messages can arrive late or out of order

Design for these conditions instead of treating them as impossible edge cases.

### 6.2 Latency

Understand that remote communication is much slower and less predictable than in-process function calls.

Reducing unnecessary network hops often improves both latency and reliability.

### 6.3 Availability

Availability asks whether the system can continue serving acceptable requests when components fail.

Techniques include:

- redundancy
- replication
- failover
- health checks
- graceful degradation
- multi-zone deployment

### 6.4 Consistency

Consistency asks what different clients are allowed to observe after reads and writes.

Learn conceptually:

- strong consistency
- eventual consistency
- read-your-writes
- monotonic reads

Do not use "eventual consistency" as an excuse for undefined behavior. Specify what temporary inconsistency is acceptable.

### 6.5 CAP theorem

Understand CAP as a model for behavior during network partitions rather than as a simple permanent classification of databases.

The three properties are:

- consistency
- availability
- partition tolerance

When a partition occurs, a distributed system may face trade-offs between maintaining a particular consistency guarantee and continuing to serve all requests.

### 6.6 PACELC

PACELC extends the discussion:

- if there is a **Partition**, choose between **Availability** and **Consistency**
- **Else**, choose between **Latency** and **Consistency**

The useful lesson is that consistency trade-offs also exist during normal operation.

### 6.7 Replication and failover

Learn:

- leader election conceptually
- failover
- split brain
- replication lag
- quorum concepts
- read/write quorums conceptually

### 6.8 Consensus

Understand why distributed nodes sometimes need agreement.

Study at a conceptual level:

- consensus problem
- leader election
- quorum
- Raft
- Paxos at a high level

You usually do not need to implement these algorithms, but you should know what problem they solve.

### 6.9 Distributed locks

Distributed locks may be required when multiple processes coordinate access to a shared resource.

Learn the risks:

- lease expiry
- stale lock holders
- clock assumptions
- network partitions
- fencing tokens conceptually

Prefer designs that avoid distributed locks when simpler data constraints or partition ownership can solve the problem.

### 6.10 Idempotency in distributed systems

Because retries are unavoidable, repeated requests must often be safe.

Example payment flow:

```text
Client
   ↓ payment request + idempotency key
Payment API
   ↓
Check key
   ├─ already processed → return previous result
   └─ new → execute payment and persist result
```

### 6.11 Circuit breaker

A circuit breaker prevents repeated calls to a failing dependency.

Typical states:

- closed
- open
- half-open

Combine it carefully with timeouts, retries, and fallback behavior.

### 6.12 Bulkhead isolation

Separate resource pools so one failing dependency or traffic class cannot consume every thread, connection, or worker.

### 6.13 Rate limiting

Common algorithms:

- fixed window
- sliding window
- token bucket
- leaky bucket

Use cases:

- abuse prevention
- tenant fairness
- protecting expensive services
- API quotas

### 6.14 Backpressure

When producers generate work faster than consumers can process it, the system needs a strategy.

Possible approaches:

- reject requests
- slow producers
- buffer temporarily
- scale consumers
- prioritize work
- shed non-critical load

Unbounded queues usually postpone rather than solve overload.

### 6.15 Distributed transactions

Understand why traditional multi-resource ACID transactions become difficult across independently deployed services.

Study alternatives:

- local transactions
- sagas
- transactional outbox
- idempotent processing
- reconciliation jobs

### 6.16 Service discovery

In dynamic environments, instances need a way to locate services.

Learn conceptually:

- client-side discovery
- server-side discovery
- DNS/service registry approaches

### 6.17 Clock and ordering problems

Learn why wall-clock timestamps cannot always provide perfect event ordering across machines.

Concepts to recognize:

- clock skew
- logical clocks
- sequence numbers
- monotonic ordering within a partition or aggregate

### Layer 6 exit criteria

You should be able to explain:

- why distributed systems fail differently from single-process applications
- availability vs consistency trade-offs
- replication and failover
- idempotency
- retries, timeouts, and circuit breakers
- rate limiting and backpressure
- sagas and outbox
- why consensus and leader election exist

## Layer 7: System architecture and system design

This is the synthesis layer. You combine the previous six layers to design a complete system.

The central question becomes:

> Given these product requirements, scale targets, reliability requirements, and constraints, what architecture should we choose and why?

### 7.1 Functional requirements

Define what the product must do.

Example for a chat system:

- send a direct message
- create a group conversation
- receive messages in near real time
- display conversation history
- show message delivery/read state
- support multiple devices

Do not begin drawing infrastructure until the core requirements are clear.

### 7.2 Non-functional requirements

Define quality targets:

- latency
- throughput
- availability
- durability
- consistency
- scalability
- security
- compliance
- cost
- maintainability

Different requirements can produce very different architectures for the same feature set.

### 7.3 Capacity estimation

Estimate order of magnitude, not false precision.

Useful inputs:

- daily active users
- requests per second
- peak multiplier
- read/write ratio
- average payload size
- storage growth
- bandwidth
- retention period

Example:

```text
10,000,000 active users/day
× 20 messages/user/day
= 200,000,000 messages/day
```

Average writes per second:

```text
200,000,000 / 86,400 ≈ 2,315 messages/second
```

If peak traffic is 5× average, design for roughly 11,600 writes/second before adding safety margin and considering read traffic.

The purpose is to reveal which components may become bottlenecks.

### 7.4 API design

Define important interfaces.

Example:

```http
POST /conversations/{conversationId}/messages
GET  /conversations/{conversationId}/messages?cursor=...
```

For real-time delivery, the same system may also use WebSocket connections.

System design often uses multiple communication mechanisms for different requirements.

### 7.5 Data model

Identify core entities and access patterns.

Chat example:

```text
User
Conversation
ConversationMember
Message
MessageReceipt
```

Then ask:

- query messages by conversation?
- paginate by time or message ID?
- query unread counts?
- find all conversations for a user?
- retain messages forever?

### 7.6 High-level architecture

Start simple:

```text
Clients
   ↓
Load balancer
   ↓
Application service
   ↓
Database
```

Then introduce complexity only when a requirement demands it.

Possible evolution:

```text
                       ┌───────────────┐
Clients ──────────────→│ Load balancer │
                       └───────┬───────┘
                               ↓
                     ┌─────────────────┐
                     │ API / app tier  │
                     └───────┬─────────┘
                             │
                  ┌──────────┼──────────┐
                  ↓          ↓          ↓
               Redis      Database    Kafka
                                         ↓
                              ┌──────────┼──────────┐
                              ↓          ↓          ↓
                          Worker A   Worker B   Analytics
```

### 7.7 Choosing REST vs WebSocket

Example reasoning:

- use REST for account settings and message-history pagination
- use WebSocket for low-latency bidirectional message delivery

The correct system may use both.

### 7.8 Choosing synchronous vs asynchronous communication

Use synchronous calls when:

- the caller needs an immediate result
- the dependency is part of the request's critical path
- simple request/response semantics are valuable

Use asynchronous communication when:

- immediate completion is unnecessary
- work is expensive or slow
- buffering protects downstream systems
- producers and consumers should scale independently
- event fan-out is needed

Do not make every service call asynchronous. Complexity must earn its place.

### 7.9 Choosing cache placement

Possible caching locations:

```text
Browser cache
     ↓
CDN
     ↓
Reverse proxy cache
     ↓
Application cache
     ↓
Distributed cache
     ↓
Database
```

Each cache changes invalidation, consistency, and observability requirements.

### 7.10 Read-heavy systems

Possible techniques:

- caching
- CDN
- read replicas
- precomputation
- denormalization
- pagination
- search indexes

### 7.11 Write-heavy systems

Possible techniques:

- batching
- partitioning
- append-oriented storage
- asynchronous processing
- queue buffering
- efficient indexes
- avoiding unnecessary synchronous fan-out

### 7.12 Hotspot handling

Examples:

- celebrity account receives huge traffic
- one shard contains a popular tenant
- one cache key receives millions of requests

Possible strategies:

- caching
- replication
- partition splitting
- key salting in appropriate designs
- request coalescing
- CDN/edge caching
- special handling for extreme entities

### 7.13 Failure analysis

For every component, ask:

> What happens if this component becomes slow or unavailable?

Examples:

- database primary fails
- cache is unavailable
- queue fills up
- consumer crashes
- external payment provider times out
- one region becomes unreachable

A design is incomplete until failure behavior is discussed.

### 7.14 Bottleneck analysis

Potential bottlenecks:

- database CPU
- disk I/O
- connection pool
- hot partitions
- cache hotspots
- message broker partitions
- application CPU
- thread pools
- network bandwidth
- external APIs

### 7.15 Cost awareness

System design is not only about maximum scale.

Consider:

- compute cost
- storage cost
- data-transfer cost
- managed-service cost
- engineering/operational cost

The best architecture is often the simplest architecture that safely meets the requirements.

## Cross-cutting concerns

These concerns apply across all seven layers.

### Security

Learn:

- authentication vs authorization
- sessions
- JWT concepts and trade-offs
- OAuth 2.0 / OpenID Connect concepts
- TLS
- encryption at rest
- secrets management
- least privilege
- input validation
- common web-security risks
- rate limiting
- audit logging

Security should be part of the design, not added after the architecture is complete.

### Observability

The three common observability signals are:

- logs
- metrics
- traces

Learn:

- correlation/request IDs
- structured logging
- latency percentiles
- error rates
- throughput
- saturation
- distributed tracing
- dashboards
- alerting

A distributed architecture that cannot be observed is extremely difficult to operate.

### Reliability

Learn:

- SLI
- SLO
- SLA
- error budgets conceptually
- redundancy
- backups
- disaster recovery
- recovery time objective (RTO)
- recovery point objective (RPO)

### Testing

System-level testing may include:

- unit tests
- integration tests
- contract tests
- end-to-end tests
- load tests
- stress tests
- failure/chaos testing where justified

### Data governance and privacy

Depending on the product, consider:

- data retention
- deletion requirements
- auditability
- personally identifiable information
- regional data requirements
- access controls

## How the layers connect

Consider a user creating an order.

```text
Client
  │
  │ HTTPS / REST
  ▼
Load balancer
  ▼
Order API
  │
  ├────────→ Redis cache
  │
  ├────────→ SQL database
  │
  └────────→ Message broker
                  │
         ┌────────┼─────────┐
         ▼        ▼         ▼
      Payment  Inventory  Notification
```

This one diagram contains almost the entire roadmap:

| Component | Relevant layer |
| --- | --- |
| SQL database | Layer 1 — Data |
| Redis | Layer 1 — Data/caching |
| Order API code | Layer 2 — Application |
| HTTPS/REST | Layer 3 — Communication |
| Message broker | Layer 4 — Messaging |
| Load balancer/runtime | Layer 5 — Infrastructure |
| retries, consistency, failures | Layer 6 — Distributed systems |
| deciding how everything fits together | Layer 7 — System design |

That is why topics such as REST vs WebSocket or sync vs async are "system design topics" while also belonging to more specific lower-level domains.

## Recommended learning order

### Phase 0: Prerequisites

Know enough of:

- one backend programming language
- Git
- command line
- basic SQL
- basic HTTP
- basic Linux

For Java developers, a useful baseline is Java + Spring Boot + PostgreSQL/MySQL.

### Phase 1: Data fundamentals

Study:

1. relational modeling
2. SQL queries
3. indexes
4. transactions
5. isolation and locks
6. Redis/caching
7. replication
8. sharding
9. SQL vs NoSQL

Build: a CRUD application with a real relational database, indexes, transactions, and pagination.

### Phase 2: Application runtime

Study:

1. request lifecycle
2. application layering
3. threads
4. thread pools
5. concurrency
6. sync vs async
7. blocking vs non-blocking
8. background jobs
9. idempotency

Build: an API that performs background report generation or email delivery.

### Phase 3: Communication

Study:

1. TCP/IP basics
2. DNS
3. HTTP/HTTPS
4. REST
5. WebSocket
6. SSE
7. polling
8. gRPC
9. webhooks
10. timeouts/retries

Build: a notification or chat prototype using REST plus WebSocket/SSE.

### Phase 4: Messaging

Study:

1. message queues
2. producer/consumer
3. pub/sub
4. Kafka
5. RabbitMQ
6. retries
7. DLQ
8. ordering
9. idempotent consumers
10. outbox
11. saga

Build: an order system that publishes events and has separate notification/inventory consumers.

### Phase 5: Infrastructure

Study:

1. Linux process/network basics
2. Nginx/reverse proxy
3. load balancing
4. Docker
5. CDN
6. object storage
7. deployment strategies
8. Kubernetes concepts
9. autoscaling

Build: containerize the application and run multiple instances behind a reverse proxy/load balancer.

### Phase 6: Distributed systems

Study:

1. failure models
2. consistency
3. availability
4. CAP/PACELC
5. replication
6. consensus concepts
7. distributed locks
8. idempotency
9. resilience patterns
10. backpressure
11. distributed transactions

Build: deliberately inject dependency failures and design retries, timeouts, circuit breakers, and reconciliation.

### Phase 7: Full system design

Practice complete designs:

1. URL shortener
2. rate limiter
3. notification system
4. chat system
5. news feed
6. file-storage service
7. search/autocomplete system
8. ride-hailing system
9. video-streaming platform
10. e-commerce/order system
11. payment system

For each design, explain not only what you choose but why alternatives were rejected.

## Practice projects

### Project 1: URL shortener

Learn:

- API design
- ID generation
- database schema
- cache
- redirects
- read-heavy scaling
- expiration
- analytics asynchronously

### Project 2: Rate limiter

Learn:

- token bucket/sliding window
- Redis
- atomic operations
- distributed coordination
- per-user/per-IP limits
- failure behavior

### Project 3: Notification service

Learn:

- REST API
- asynchronous queue
- retries
- DLQ
- email/SMS/push channels
- templates
- user preferences
- idempotency

### Project 4: Chat system

Learn:

- REST + WebSocket
- persistent connections
- message storage
- ordering
- unread counts
- online presence
- fan-out
- connection routing
- multi-device delivery

### Project 5: E-commerce order system

Learn:

- transactions
- inventory consistency
- payments
- outbox
- saga
- idempotency
- events
- eventual consistency
- reconciliation

### Project 6: News feed

Learn:

- fan-out on write vs fan-out on read
- cache
- ranking
- hot users
- pagination
- precomputation
- eventual consistency

### Project 7: File-storage service

Learn:

- metadata vs binary storage
- object storage
- multipart uploads
- CDN
- signed URLs
- replication
- lifecycle/retention

## System design interview workflow

A disciplined design process is more valuable than immediately drawing many boxes.

### Step 1: Clarify requirements

Ask:

- Who uses the system?
- What are the critical features?
- Which features are out of scope?
- Is real-time behavior required?
- What consistency guarantees are required?

### Step 2: Define scale

Estimate:

- users
- requests per second
- read/write ratio
- data size
- bandwidth
- retention
- peak traffic

### Step 3: Define APIs

Write only the important interfaces.

### Step 4: Define data model

Focus on entities and access patterns.

### Step 5: Draw a simple high-level design

Start with:

```text
Client → API → Database
```

Then add components only when requirements justify them.

### Step 6: Identify bottlenecks

Ask what breaks first as load increases.

### Step 7: Scale selectively

Consider:

- caching
- replicas
- partitioning
- queues
- CDN
- additional application instances

Do not add all of them by default.

### Step 8: Discuss reliability

Cover:

- timeouts
- retries
- failover
- duplicate requests/messages
- dependency outages
- data recovery

### Step 9: Discuss trade-offs

Explicitly state decisions such as:

- SQL over NoSQL because strong relational transactions dominate the workload
- WebSocket for real-time delivery but REST for history retrieval
- asynchronous events for notifications because they do not belong in the order-creation critical path

### Step 10: Revisit requirements

Confirm that the final design satisfies the original functional and non-functional requirements.

## Mastery checklist

### Data and storage

- [ ] I can model relational data and explain normalization vs denormalization.
- [ ] I understand indexes and can interpret basic query-plan reasoning.
- [ ] I understand ACID and common isolation levels.
- [ ] I understand optimistic and pessimistic concurrency control.
- [ ] I can compare SQL and major NoSQL models.
- [ ] I understand cache-aside and common cache failure modes.
- [ ] I can explain replication vs sharding.
- [ ] I understand shard-key and hotspot problems.

### Application and code

- [ ] I can explain a backend request lifecycle.
- [ ] I understand processes, threads, and thread pools.
- [ ] I understand race conditions and synchronization.
- [ ] I can distinguish sync/async from blocking/non-blocking.
- [ ] I understand transaction boundaries.
- [ ] I can design idempotent application operations.
- [ ] I know when to use background jobs.

### Communication

- [ ] I understand TCP, ports, DNS, TLS, and HTTP at a practical level.
- [ ] I can design a REST API.
- [ ] I can explain REST vs WebSocket.
- [ ] I understand SSE, polling, gRPC, GraphQL, and webhooks conceptually.
- [ ] I use explicit timeouts for remote calls.
- [ ] I understand safe retry strategies.

### Messaging

- [ ] I understand queues, topics, producers, and consumers.
- [ ] I understand Kafka fundamentals.
- [ ] I understand RabbitMQ fundamentals.
- [ ] I can explain queue vs pub/sub use cases.
- [ ] I understand duplicate delivery and idempotent consumers.
- [ ] I understand ordering trade-offs.
- [ ] I understand retries and DLQs.
- [ ] I understand outbox and saga patterns.

### Infrastructure

- [ ] I understand reverse proxies and load balancers.
- [ ] I understand horizontal vs vertical scaling.
- [ ] I understand stateless service design.
- [ ] I can containerize an application.
- [ ] I understand CDN and object-storage use cases.
- [ ] I understand basic deployment strategies.
- [ ] I understand Kubernetes concepts at a high level.

### Distributed systems

- [ ] I expect partial failure and network timeouts.
- [ ] I understand consistency vs availability trade-offs.
- [ ] I understand CAP and PACELC at a practical level.
- [ ] I understand replication and failover issues.
- [ ] I understand why consensus exists.
- [ ] I understand distributed-lock risks.
- [ ] I understand idempotency across service boundaries.
- [ ] I understand circuit breakers, bulkheads, rate limiting, and backpressure.
- [ ] I understand distributed-transaction alternatives.

### Full system design

- [ ] I clarify functional requirements before choosing technology.
- [ ] I define non-functional requirements.
- [ ] I perform order-of-magnitude capacity estimates.
- [ ] I design APIs around real use cases.
- [ ] I model data around access patterns.
- [ ] I start with a simple architecture.
- [ ] I identify bottlenecks before adding scaling mechanisms.
- [ ] I discuss failure scenarios.
- [ ] I explain trade-offs instead of claiming one technology is always best.
- [ ] I consider cost, operations, security, and observability.

## Glossary

| Term | Short meaning |
| --- | --- |
| Availability | Ability to serve acceptable requests despite failures |
| Backpressure | Controlling producers when consumers cannot keep up |
| Cache | Faster storage holding reusable data to reduce latency/load |
| CDN | Distributed edge network for serving cacheable content closer to users |
| Consumer | Component that reads/processes messages or events |
| DLQ | Queue holding messages that repeatedly failed processing |
| Eventual consistency | Model where replicas may temporarily differ but converge later under defined assumptions |
| Idempotency | Property allowing repeated logical operations without unintended extra side effects |
| Load balancer | Component that distributes traffic across multiple servers |
| Message broker | Infrastructure that transports messages between producers and consumers |
| Partition | Division of a dataset or event stream into subsets |
| Producer | Component that creates/publishes messages or events |
| Pub/Sub | Messaging model where published events can reach multiple subscribers |
| Replication | Maintaining copies of data on multiple nodes |
| REST | HTTP-oriented architectural style centered on resources and stateless interactions |
| Sharding | Horizontal division of data across multiple storage nodes |
| SSE | Server-to-client event stream over HTTP |
| WebSocket | Persistent full-duplex communication protocol between client and server |

## Final mental model

System design can be reduced to a repeatable chain of reasoning:

```text
Requirements
    ↓
Traffic and scale
    ↓
API / communication model
    ↓
Application behavior
    ↓
Data model and storage
    ↓
Infrastructure
    ↓
Distributed-system guarantees
    ↓
Failure handling
    ↓
Security + observability + cost
    ↓
Trade-offs
```

When you understand the lower layers, "system design" stops looking like a collection of random diagrams. REST, WebSocket, Kafka, Redis, databases, load balancers, Docker, replication, and sharding become tools. The real skill is knowing which tool solves which problem, what new problems it introduces, and whether the system actually needs it.
