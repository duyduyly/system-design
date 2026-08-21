# System Design Learning Roadmap

A structured roadmap for learning system design from the lowest practical layer—data and application code—to distributed systems, production infrastructure, and large-scale architecture.

The goal is not to memorize architectures. The goal is to understand the building blocks, the trade-offs between them, and how to combine them deliberately.

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
- [Final mental model](#final-mental-model)

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

For every architecture decision, add three more:

5. What fails when this component fails?
6. How will I observe and recover from that failure?
7. What is the simplest design that still satisfies the requirements?

That reasoning is more important than memorizing product names.

## The seven layers

| Layer | Main question | Typical topics |
| --- | --- | --- |
| 1. Data and storage | How is data stored, queried, protected, searched, and scaled? | SQL, NoSQL, indexes, transactions, replication, sharding, search, IDs |
| 2. Application and code | How does one application process requests safely and remain maintainable? | application layers, concurrency, transactions, modularity, background jobs |
| 3. Communication and networking | How do clients and services communicate? | HTTP, REST, WebSocket, SSE, gRPC, DNS, TCP, API gateways |
| 4. Messaging and integration | How do independent components communicate asynchronously and propagate change? | queues, Kafka, RabbitMQ, pub/sub, CDC, CQRS, event schemas |
| 5. Server and infrastructure | Where does the application run and how is traffic delivered and deployed? | Linux, Nginx, containers, load balancers, CDN, Kubernetes, IaC, CI/CD |
| 6. Distributed systems | How do multiple machines behave as one reliable system? | consistency, availability, replication, consensus, resilience, multi-region |
| 7. System architecture and system design | How should all components be combined and evolved for a real product? | architecture styles, decomposition, capacity, multi-tenancy, scaling, trade-offs |

---

# Layer 1: Data and storage

This layer answers the question: **where does the system's state live, and how is that state queried and scaled?**

Most system design decisions eventually affect data. A system can often survive a temporary application-server failure, but losing or corrupting persistent data can be catastrophic.

## 1.1 Relational databases

Learn:

- tables, rows, and columns
- primary keys and foreign keys
- one-to-one, one-to-many, many-to-many relationships
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

## 1.2 Indexes

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

## 1.3 Transactions and ACID

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
- write skew conceptually

## 1.4 Locking and concurrency control

Learn:

- optimistic locking
- pessimistic locking
- row locks
- table locks
- deadlocks
- MVCC
- version columns
- compare-and-set concepts

Example use case: preventing two users from purchasing the last available item at the same time.

## 1.5 SQL vs NoSQL

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

Choose based on:

- access patterns
- consistency requirements
- scale
- data relationships
- query flexibility
- operational complexity
- cost

## 1.6 Caching

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
- cache warming
- request coalescing

Typical technology:

- Redis

## 1.7 Replication

Replication creates copies of data on multiple nodes.

Learn:

- leader/follower replication
- multi-leader replication conceptually
- leaderless replication conceptually
- synchronous vs asynchronous replication
- read replicas
- replication lag
- failover
- promotion of a replica

Replication can improve availability and read scalability, but it creates consistency and operational trade-offs.

## 1.8 Partitioning and sharding

Partitioning divides a dataset into smaller pieces.

Learn:

- horizontal partitioning
- vertical partitioning
- range-based sharding
- hash-based sharding
- directory-based sharding
- consistent hashing conceptually
- shard-key selection
- hot partitions
- rebalancing
- cross-shard queries
- cross-shard transactions

A bad shard key can make a theoretically scalable system perform poorly.

## 1.9 Data modeling for access patterns

In system design, start from questions such as:

- What are the most common reads?
- What are the most common writes?
- Which queries need low latency?
- Which fields must be unique?
- Which data must be strongly consistent?
- Which data can be eventually consistent?
- What data grows fastest?
- What data must be retained or deleted?

Design storage around actual access patterns, not only around entity diagrams.

## 1.10 Distributed ID generation

Large systems often need identifiers that can be generated without one central bottleneck.

Study:

- auto-increment database IDs
- sequences
- UUIDs
- random IDs
- time-ordered UUID-style identifiers conceptually
- Snowflake-style IDs
- timestamp + worker + sequence designs

Compare them by:

- uniqueness
- sortability
- index locality
- coordination requirements
- information leakage
- storage size

Questions to answer:

- Must IDs be sortable by creation time?
- Can the system tolerate a central sequence service?
- Will random IDs fragment a database index?
- Does exposing sequential IDs create a security or privacy concern?

## 1.11 Search and indexing systems

A transactional database is not always the right tool for full-text search.

Learn:

- inverted indexes
- tokenization
- analyzers
- ranking concepts
- filters vs full-text queries
- autocomplete
- prefix search
- typo tolerance conceptually
- search indexing pipelines
- index refresh delay

Typical technologies:

- Elasticsearch
- OpenSearch
- Solr

A common architecture is:

```text
Primary database
      ↓
CDC / outbox / event
      ↓
Search indexing worker
      ↓
Search index
```

Treat the database as the source of truth unless the product architecture explicitly says otherwise.

## 1.12 OLTP, OLAP, and analytical storage

Learn the difference between:

- **OLTP** — many small operational reads and writes
- **OLAP** — analytical queries over large datasets

Understand why production systems often separate the transactional path from analytics.

Concepts:

- data warehouse
- data lake conceptually
- ETL vs ELT
- column-oriented analytical storage
- batch ingestion
- streaming ingestion
- materialized views

Do not send expensive analytical workloads to the primary transactional database without understanding the impact.

### Layer 1 exit criteria

Before moving on, you should be able to explain:

- why an index can improve reads but hurt writes
- when a transaction is necessary
- SQL vs NoSQL trade-offs
- cache-aside and cache invalidation
- replication vs sharding
- how to choose a primary key and a shard key
- common distributed-ID strategies
- when a search engine is appropriate
- OLTP vs OLAP
- how consistency requirements affect storage design

---

# Layer 2: Application and code

This layer answers: **how does a single application receive work, execute business logic, and remain maintainable as it grows?**

## 2.1 Application structure

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

## 2.2 Dependency injection and inversion of control

For Java/Spring developers, understand:

- dependency injection
- inversion of control
- bean lifecycle at a useful level
- constructor injection
- interface-based abstractions

These are not distributed-system concepts, but they affect how maintainable services are built.

## 2.3 Request lifecycle

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

## 2.4 Processes, threads, and concurrency

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
- virtual threads conceptually on modern Java

## 2.5 Synchronous vs asynchronous execution

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

Do not confuse sync/async with blocking/non-blocking. They are related but describe different aspects of execution and coordination.

## 2.6 Blocking vs non-blocking I/O

Learn the difference between:

- blocking calls
- non-blocking calls
- event loops
- reactive/event-driven I/O

Understand why a server handling thousands of mostly waiting I/O operations may benefit from non-blocking approaches, while simple blocking code can remain the better engineering choice for many applications.

## 2.7 Application-level transactions

Learn:

- transaction boundaries
- connection pools
- database transaction propagation concepts
- rollback behavior
- why holding transactions open during slow network calls is dangerous

For Java/Spring, understand what `@Transactional` does conceptually and where transaction boundaries should live.

## 2.8 Background jobs

Typical background work includes:

- email sending
- image processing
- report generation
- data imports
- scheduled cleanup
- notification delivery

Learn when work belongs inside the request path and when it should be delegated to background processing.

## 2.9 Idempotency at application level

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

## 2.10 Internal application architecture

Before splitting a system into networked services, learn how to structure one deployable application well.

Study these concepts:

- layered architecture
- modular monolith
- package/module boundaries
- dependency direction
- clean architecture conceptually
- hexagonal/ports-and-adapters architecture conceptually
- domain-driven design at a practical level
- bounded contexts
- aggregates conceptually

A modular monolith is often a better next step than premature microservices.

Example:

```text
E-commerce application
├── catalog
├── ordering
├── inventory
├── payment
└── notification
```

Each module can have explicit boundaries even when all modules deploy together.

## 2.11 Resource and connection management

Production failures often happen because a resource pool becomes exhausted.

Learn:

- database connection pools
- HTTP client connection pools
- thread/worker pools
- queue capacity
- file descriptors conceptually
- pool sizing
- bounded resources
- timeouts while waiting for a resource

The goal is to avoid one slow dependency consuming every available execution resource.

### Layer 2 exit criteria

You should be able to explain:

- the lifecycle of a backend request
- threads and thread pools
- sync vs async
- blocking vs non-blocking
- where transaction boundaries belong
- why idempotency matters
- when work should become a background job
- why modular boundaries matter before microservices
- how connection/resource pools affect reliability

---

# Layer 3: Communication and networking

This layer answers: **how do clients, servers, and services exchange data?**

## 3.1 Networking fundamentals

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
- NAT conceptually

You do not need to become a network engineer, but you should understand what happens between a client and server.

## 3.2 DNS

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

## 3.3 HTTP fundamentals

Learn:

- methods: GET, POST, PUT, PATCH, DELETE
- status codes
- headers
- request/response bodies
- cookies
- keep-alive
- compression
- HTTP/1.1 vs HTTP/2 vs HTTP/3 at a conceptual level
- TLS/HTTPS

## 3.4 REST

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

## 3.5 WebSocket

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

## 3.6 REST vs WebSocket

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

## 3.7 Server-Sent Events

SSE provides a long-lived HTTP connection through which the server sends events to the client.

Useful when:

- communication is mainly server → client
- browser support is sufficient
- you want something simpler than bidirectional WebSocket communication

Examples:

- live feeds
- job progress updates
- notifications

## 3.8 Polling and long polling

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

## 3.9 gRPC

gRPC is commonly used for efficient service-to-service communication.

Learn conceptually:

- Protocol Buffers
- strongly defined contracts
- binary serialization
- unary calls
- streaming
- internal service communication

Compare REST and gRPC based on interoperability, browser/client requirements, operational tooling, contracts, and performance needs.

## 3.10 GraphQL

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

## 3.11 Webhooks

Webhooks are HTTP callbacks triggered by events.

Important concerns:

- signature verification
- retries
- duplicate delivery
- idempotency
- ordering
- timeout handling

## 3.12 Timeouts and retries

Every remote call can fail or become slow.

Learn:

- connection timeout
- read/request timeout
- retry policies
- exponential backoff
- jitter
- retryable vs non-retryable failures
- retry storms
- retry budgets conceptually

Retries without careful design can multiply load during an incident.

## 3.13 API Gateway

An API gateway can provide a controlled entry point for clients.

Possible responsibilities:

- routing
- authentication integration
- TLS termination
- rate limiting
- quotas
- request transformation
- API version routing
- observability
- traffic policies

Typical flow:

```text
Client
  ↓
API Gateway
  ├──→ User Service
  ├──→ Order Service
  └──→ Catalog Service
```

Avoid turning the gateway into a large business-logic monolith.

## 3.14 Backend for Frontend (BFF)

A BFF is an API layer tailored to a specific client type.

Example:

```text
Mobile App ──→ Mobile BFF ──┐
                            ├──→ Internal services
Web App   ──→ Web BFF ──────┘
```

Useful when different clients need substantially different aggregation, payloads, or release lifecycles.

Trade-off: additional services and ownership complexity.

## 3.15 API contracts and compatibility

Services evolve independently, so contracts matter.

Learn:

- backward-compatible API evolution
- additive vs breaking changes
- versioning strategies
- consumer-driven contract testing conceptually
- schema validation
- deprecation policies

Do not assume every client upgrades at the same time.

### Layer 3 exit criteria

You should be able to choose among:

- REST
- WebSocket
- SSE
- polling
- gRPC
- GraphQL
- webhooks

and explain:

- API Gateway vs BFF
- safe timeout/retry behavior
- API compatibility and versioning trade-offs

---

# Layer 4: Messaging and integration

This layer answers: **how can components communicate without requiring the receiver to complete work immediately, and how does data change propagate between systems?**

## 4.1 Message queues

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

## 4.2 Producer and consumer

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

## 4.3 Kafka

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

## 4.4 RabbitMQ

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

## 4.5 Kafka vs RabbitMQ

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

## 4.6 Publish/subscribe

In pub/sub, one event may be consumed by multiple independent subscribers.

```text
              ┌→ Email service
Order event ──┼→ Analytics service
              └→ Inventory service
```

This reduces direct coupling but increases the need for observability, schema governance, idempotency, and failure handling.

## 4.7 Delivery semantics

Understand the practical meaning of:

- at-most-once
- at-least-once
- effectively-once processing

"Exactly once" should always be examined carefully because guarantees depend on the boundaries of the system and the technologies involved.

## 4.8 Ordering

Ask:

- Is global ordering required?
- Is per-user or per-order ordering enough?
- Can events be processed out of order?

Global ordering is expensive and often unnecessary.

## 4.9 Retry and dead-letter queues

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

## 4.10 Event-driven architecture

An event represents something that happened:

- `OrderCreated`
- `PaymentCompleted`
- `UserRegistered`

Producers publish facts; consumers react to them.

Learn the difference between:

- commands
- events
- queries

## 4.11 Outbox pattern

A classic problem:

1. application writes an order to the database
2. application publishes `OrderCreated`
3. the database commit succeeds but message publishing fails

Now the system state and event stream disagree.

The transactional outbox pattern stores the event in the same database transaction as the business change, then publishes it asynchronously.

## 4.12 Saga pattern

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

## 4.13 Change Data Capture (CDC)

CDC captures database changes and propagates them to other systems.

Typical uses:

- search indexing
- analytics pipelines
- cache invalidation
- integration events
- data synchronization

Conceptual flow:

```text
Database transaction log
        ↓
CDC connector
        ↓
Event stream
        ↓
Consumers
```

Understand the difference between CDC and domain events: database changes describe storage-level changes, while domain events describe business facts.

## 4.14 Event schemas and schema evolution

Events are contracts too.

Learn:

- event versioning
- backward compatibility
- forward compatibility conceptually
- optional fields
- schema registry concepts
- Avro/Protobuf/JSON schema concepts
- avoiding semantic changes to existing fields

An event stream becomes difficult to evolve when producers and consumers make undocumented assumptions.

## 4.15 CQRS

CQRS separates write models from read models when their requirements differ significantly.

```text
Command
   ↓
Write model
   ↓
Events / projection updates
   ↓
Read model
   ↓
Query
```

Use CQRS when it solves a real mismatch between read and write models. Do not add it to simple CRUD systems without a reason.

## 4.16 Event sourcing

Event sourcing stores state changes as an append-only sequence of domain events rather than only storing the latest state.

Learn conceptually:

- event log
- aggregate reconstruction
- snapshots
- projections
- replay
- schema evolution
- auditability

Event sourcing provides powerful history and replay capabilities but significantly increases design and operational complexity.

CQRS and event sourcing are related but independent patterns.

## 4.17 Stream processing

Stream processing continuously transforms event streams.

Learn:

- stateless vs stateful processing
- event time vs processing time
- windows
- watermarks conceptually
- late events
- deduplication
- repartitioning

Typical technologies:

- Kafka Streams
- Apache Flink
- Spark Structured Streaming conceptually

Use stream processing when continuously derived results are needed from high-volume event flows.

### Layer 4 exit criteria

You should understand:

- queues and pub/sub
- Kafka and RabbitMQ at a conceptual level
- retries and DLQs
- duplicate messages
- ordering
- idempotent consumers
- outbox
- saga
- CDC
- event-schema evolution
- CQRS
- event sourcing
- stream-processing fundamentals

---

# Layer 5: Server and infrastructure

This layer answers: **how does the application run, receive traffic, deploy safely, and scale operationally?**

## 5.1 Operating-system fundamentals

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

## 5.2 Web server and reverse proxy

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

## 5.3 Load balancing

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

## 5.4 Horizontal vs vertical scaling

**Vertical scaling:** make one machine larger.

**Horizontal scaling:** add more machines/instances.

Understand the advantages and limits of each approach.

## 5.5 Stateless application servers

Stateless services are easier to scale horizontally because any healthy instance can handle a request.

Move shared state such as sessions to appropriate external storage when necessary.

## 5.6 Containers

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

## 5.7 Container orchestration

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

## 5.8 CDN

A CDN serves content from geographically distributed edge locations.

Useful for:

- static assets
- images
- videos
- downloads
- cacheable API/content responses in suitable cases

Benefits include lower latency and reduced origin load.

## 5.9 Object storage

Do not store large binary objects in the application server's local filesystem if multiple stateless instances must access them.

Learn object-storage concepts for:

- images
- videos
- backups
- documents
- large generated files

Typical technology example: Amazon S3-compatible object storage.

## 5.10 Deployment strategies

Learn:

- rolling deployment
- blue/green deployment
- canary deployment
- rollback
- feature flags

## 5.11 Autoscaling

Possible signals:

- CPU
- memory
- requests per second
- queue depth
- custom application metrics

Scaling based on the wrong metric can make incidents worse.

## 5.12 Infrastructure as Code

Infrastructure should be reproducible rather than manually rebuilt from memory.

Learn conceptually:

- declarative infrastructure
- state
- plan/apply workflow
- reusable modules
- environment separation
- secret handling
- drift

Typical technologies:

- Terraform
- OpenTofu
- cloud-native templates

IaC improves repeatability but also creates another codebase that needs review, testing, and lifecycle management.

## 5.13 CI/CD

Understand the path from source code to production:

```text
Commit
  ↓
Build
  ↓
Automated tests
  ↓
Artifact / image
  ↓
Security / quality checks
  ↓
Deploy to environment
  ↓
Verification
  ↓
Promotion / rollback
```

Learn:

- immutable build artifacts
- environment promotion
- migration sequencing
- deployment gates
- rollback strategy
- feature flags
- secrets in pipelines

## 5.14 Service mesh

A service mesh can provide infrastructure-level features for service-to-service traffic.

Concepts:

- mTLS
- traffic policies
- retries/timeouts
- service identity
- observability
- sidecar or ambient data-plane concepts

Typical technologies include Istio and Linkerd.

Do not introduce a service mesh until the operational problem justifies the complexity.

## 5.15 Serverless architecture

Study serverless/function-based architectures conceptually.

Learn:

- event-triggered functions
- stateless execution
- cold starts
- concurrency limits
- execution-time limits
- managed integrations
- cost by invocation/runtime
- vendor-specific constraints

Serverless can reduce infrastructure management for bursty or event-driven workloads, but it is not automatically simpler for every system.

## 5.16 Multi-region infrastructure

Learn how infrastructure expands geographically:

```text
Single instance
    ↓
Multiple instances
    ↓
Multiple availability zones
    ↓
Multiple regions
```

Study:

- global DNS/traffic management
- regional load balancers
- active/passive
- active/active
- regional failover
- data locality
- cross-region network cost
- deployment coordination

Multi-region infrastructure without a clear data strategy is incomplete.

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

and describe:

- horizontal scaling
- safe deployment strategies
- container orchestration
- IaC
- CI/CD
- serverless trade-offs
- why and when multi-region deployment is useful

---

# Layer 6: Distributed systems

This layer answers: **what changes when one logical system spans multiple machines and failures are normal?**

Distributed systems are difficult because the network is not perfectly reliable, clocks are not perfectly synchronized, nodes fail independently, and messages can be delayed or duplicated.

## 6.1 Fundamental failure model

Assume that:

- requests can time out
- responses can be lost
- servers can crash
- dependencies can become slow
- network partitions can occur
- messages can be duplicated
- messages can arrive late or out of order

Design for these conditions instead of treating them as impossible edge cases.

## 6.2 Latency

Understand that remote communication is much slower and less predictable than in-process function calls.

Reducing unnecessary network hops often improves both latency and reliability.

## 6.3 Availability

Availability asks whether the system can continue serving acceptable requests when components fail.

Techniques include:

- redundancy
- replication
- failover
- health checks
- graceful degradation
- multi-zone deployment

## 6.4 Consistency

Consistency asks what different clients are allowed to observe after reads and writes.

Learn conceptually:

- strong consistency
- eventual consistency
- read-your-writes
- monotonic reads

Do not use "eventual consistency" as an excuse for undefined behavior. Specify what temporary inconsistency is acceptable.

## 6.5 CAP theorem

Understand CAP as a model for behavior during network partitions rather than as a simple permanent classification of databases.

The three properties are:

- consistency
- availability
- partition tolerance

When a partition occurs, a distributed system may face trade-offs between maintaining a particular consistency guarantee and continuing to serve all requests.

## 6.6 PACELC

PACELC extends the discussion:

- if there is a **Partition**, choose between **Availability** and **Consistency**
- **Else**, choose between **Latency** and **Consistency**

The useful lesson is that consistency trade-offs also exist during normal operation.

## 6.7 Replication and failover

Learn:

- leader election conceptually
- failover
- split brain
- replication lag
- quorum concepts
- read/write quorums conceptually

## 6.8 Consensus

Understand why distributed nodes sometimes need agreement.

Study at a conceptual level:

- consensus problem
- leader election
- quorum
- Raft
- Paxos at a high level

You usually do not need to implement these algorithms, but you should know what problem they solve.

## 6.9 Distributed locks

Distributed locks may be required when multiple processes coordinate access to a shared resource.

Learn the risks:

- lease expiry
- stale lock holders
- clock assumptions
- network partitions
- fencing tokens conceptually

Prefer designs that avoid distributed locks when simpler data constraints or partition ownership can solve the problem.

## 6.10 Idempotency in distributed systems

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

## 6.11 Circuit breaker

A circuit breaker prevents repeated calls to a failing dependency.

Typical states:

- closed
- open
- half-open

Combine it carefully with timeouts, retries, and fallback behavior.

## 6.12 Bulkhead isolation

Separate resource pools so one failing dependency or traffic class cannot consume every thread, connection, or worker.

## 6.13 Rate limiting

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

## 6.14 Backpressure

When producers generate work faster than consumers can process it, the system needs a strategy.

Possible approaches:

- reject requests
- slow producers
- buffer temporarily
- scale consumers
- prioritize work
- shed non-critical load

Unbounded queues usually postpone rather than solve overload.

## 6.15 Distributed transactions

Understand why traditional multi-resource ACID transactions become difficult across independently deployed services.

Study alternatives:

- local transactions
- sagas
- transactional outbox
- idempotent processing
- reconciliation jobs

## 6.16 Service discovery

In dynamic environments, instances need a way to locate services.

Learn conceptually:

- client-side discovery
- server-side discovery
- DNS/service registry approaches

## 6.17 Clock and ordering problems

Learn why wall-clock timestamps cannot always provide perfect event ordering across machines.

Concepts to recognize:

- clock skew
- logical clocks
- sequence numbers
- monotonic ordering within a partition or aggregate

## 6.18 Advanced consistency models

After basic strong vs eventual consistency, learn to recognize:

- linearizability
- serializability
- snapshot isolation
- causal consistency
- session consistency
- read-your-writes consistency

You do not need mathematical mastery initially. You should understand that "consistent" is not one single guarantee.

## 6.19 Multi-region data replication

Multi-region systems combine infrastructure and data-consistency decisions.

Study:

- single-writer region
- leader/follower across regions
- multi-leader replication
- active/active data systems
- conflict resolution
- replication lag across regions
- data residency
- regional failover

Example trade-off:

```text
Lower write latency in every region
        ↕
More difficult cross-region consistency and conflict handling
```

## 6.20 Tail latency and queueing

Average latency can hide production problems.

Learn:

- p50, p95, p99, p999
- tail latency
- queue buildup
- saturation
- head-of-line blocking conceptually
- Little's Law conceptually
- coordinated omission conceptually in load testing

A service with acceptable average latency can still provide a bad user experience when its p99 latency is high.

## 6.21 Failure domains and blast radius

Understand failure boundaries such as:

- process
- host
- rack conceptually
- availability zone
- region
- third-party dependency

Design so one failure does not automatically become a system-wide failure.

Techniques include:

- isolation
- partitioning by tenant or workload
- cell-based architecture conceptually
- regional independence
- bounded concurrency

### Layer 6 exit criteria

You should be able to explain:

- why distributed systems fail differently from single-process applications
- availability vs consistency trade-offs
- CAP and PACELC
- replication and failover
- idempotency
- retries, timeouts, and circuit breakers
- rate limiting and backpressure
- sagas and outbox
- why consensus and leader election exist
- multiple consistency models
- multi-region replication trade-offs
- tail latency and failure domains

---

# Layer 7: System architecture and system design

This is the synthesis layer. You combine the previous six layers to design and evolve a complete system.

The central question becomes:

> Given these product requirements, scale targets, reliability requirements, organizational constraints, and cost constraints, what architecture should we choose and why?

## 7.1 Functional requirements

Define what the product must do.

Example for a chat system:

- send a direct message
- create a group conversation
- receive messages in near real time
- display conversation history
- show message delivery/read state
- support multiple devices

Do not begin drawing infrastructure until the core requirements are clear.

## 7.2 Non-functional requirements

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
- operability

Different requirements can produce very different architectures for the same feature set.

## 7.3 Capacity estimation

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

## 7.4 API design

Define important interfaces.

Example:

```http
POST /conversations/{conversationId}/messages
GET  /conversations/{conversationId}/messages?cursor=...
```

For real-time delivery, the same system may also use WebSocket connections.

System design often uses multiple communication mechanisms for different requirements.

## 7.5 Data model

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

## 7.6 High-level architecture

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

## 7.7 Architecture styles

Learn the major structural options before assuming microservices are the destination.

### Monolith

One deployable application containing most business capabilities.

Advantages:

- simple deployment
- simple local transactions
- easy debugging initially
- lower operational overhead

Risks at large scale:

- weak module boundaries
- large deployments
- organizational coupling

### Modular monolith

One deployable unit with intentionally separated internal modules.

Often a strong default for small and medium teams because it preserves simple operations while encouraging architecture boundaries.

### Service-oriented architecture

Independent services expose business capabilities, often with shared enterprise integration infrastructure depending on the style.

### Microservices

Independently deployable services aligned to business capabilities.

Potential benefits:

- independent deployment
- independent scaling
- team autonomy
- fault isolation when designed correctly

Costs:

- network failure
- distributed data
- deployment complexity
- observability requirements
- contract management
- more difficult testing
- more operational ownership

The rule is simple:

> Do not choose microservices because the system may become large. Choose them when independent ownership, deployment, scaling, or fault isolation creates enough value to pay for distributed-system complexity.

## 7.8 Service decomposition and bounded contexts

This is one of the most important system-design skills.

Do not split services by technical layers such as:

```text
Controller Service
Business Logic Service
Database Service
```

Prefer business capabilities such as:

```text
Catalog
Ordering
Inventory
Payment
Shipping
Notification
```

Study:

- high cohesion
- low coupling
- bounded contexts
- business capability boundaries
- change frequency
- ownership
- data ownership
- independent scaling needs
- failure isolation

A useful question is:

> Which data and rules must change together to preserve one business invariant?

Those concepts often belong inside the same boundary.

## 7.9 Data ownership across services

Microservice boundaries become weak if every service freely writes to the same tables.

Common model:

```text
Order Service     → owns order data
Inventory Service → owns inventory data
Payment Service   → owns payment data
```

Other services interact through APIs or events rather than direct writes.

Study the trade-offs of:

- shared database
- separate schema
- database per service
- duplicated read models
- eventual consistency

Database-per-service is a design principle, not a requirement that every service must run a physically separate database server.

## 7.10 Choosing REST vs WebSocket

Example reasoning:

- use REST for account settings and message-history pagination
- use WebSocket for low-latency bidirectional message delivery

The correct system may use both.

## 7.11 Choosing synchronous vs asynchronous communication

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

## 7.12 Choosing cache placement

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

## 7.13 Read-heavy systems

Possible techniques:

- caching
- CDN
- read replicas
- precomputation
- denormalization
- pagination
- search indexes
- materialized read models

## 7.14 Write-heavy systems

Possible techniques:

- batching
- partitioning
- append-oriented storage
- asynchronous processing
- queue buffering
- efficient indexes
- avoiding unnecessary synchronous fan-out

## 7.15 Hotspot handling

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

## 7.16 Multi-tenancy

SaaS systems often serve many tenants from shared infrastructure.

Common strategies:

### Shared database, shared schema

Tenant ID is included in shared tables.

Pros:

- efficient resource usage
- simpler infrastructure

Cons:

- isolation must be enforced carefully
- noisy-neighbor risk

### Shared database, separate schemas

Improves logical isolation but increases schema-management complexity.

### Database per tenant

Provides stronger isolation but increases operational overhead.

Consider:

- tenant isolation
- encryption keys
- quotas
- noisy neighbors
- per-tenant scaling
- regulatory requirements
- backup/restore by tenant

## 7.17 Multi-region architecture

A global system may evolve as:

```text
One region
   ↓
Multi-AZ
   ↓
Active/passive regions
   ↓
Active/active regions
```

Questions:

- Where can writes occur?
- How is traffic routed geographically?
- What happens when one region fails?
- What consistency is required across regions?
- How much replication lag is acceptable?
- Must data stay inside a jurisdiction?

Example active/passive:

```text
Users
  ↓
Global routing
  ├──→ Region A — active
  └──→ Region B — standby
```

Example active/active:

```text
Users
  ↓
Global routing
  ├──→ Region A — reads/writes
  └──→ Region B — reads/writes
```

Active/active can reduce regional latency but significantly complicates data consistency and conflict resolution.

## 7.18 Search-system architecture

A search feature usually contains more than a search engine.

Consider:

- source-of-truth database
- indexing pipeline
- index schema
- reindexing
- partial updates
- stale index behavior
- query service
- ranking
- autocomplete
- failure recovery

Example:

```text
Product DB
   ↓
Outbox / CDC
   ↓
Indexer
   ↓
Search cluster
   ↓
Search API
```

## 7.19 Architecture evolution

Good system design includes a migration path.

A common healthy evolution might be:

```text
Simple monolith
      ↓
Modular monolith
      ↓
Add cache / queue / replicas where needed
      ↓
Extract one service with a clear reason
      ↓
Repeat only when justified
```

Learn patterns such as:

- strangler pattern
- branch-by-abstraction conceptually
- expand-and-contract database migrations
- dual-read/dual-write risks
- compatibility windows

Avoid redesigning the whole system when one bounded change can solve the problem.

## 7.20 Failure analysis

For every component, ask:

> What happens if this component becomes slow or unavailable?

Examples:

- database primary fails
- cache is unavailable
- queue fills up
- consumer crashes
- external payment provider times out
- search cluster becomes unavailable
- one availability zone fails
- one region becomes unreachable

A design is incomplete until failure behavior is discussed.

## 7.21 Bottleneck analysis

Potential bottlenecks:

- database CPU
- disk I/O
- connection pool
- hot partitions
- cache hotspots
- message-broker partitions
- application CPU
- thread pools
- network bandwidth
- external APIs
- search nodes
- serialization/deserialization

## 7.22 Cost awareness

System design is not only about maximum scale.

Consider:

- compute cost
- storage cost
- data-transfer cost
- managed-service cost
- observability cost
- engineering/operational cost

The best architecture is often the simplest architecture that safely meets the requirements.

## 7.23 Architecture Decision Records

Important design choices should be explainable later.

An ADR typically records:

- context
- decision
- alternatives considered
- consequences

Example:

```text
Decision: use PostgreSQL rather than Cassandra for order storage.
Reason: transactional consistency and relational queries are more important than extreme write scale.
Consequence: horizontal write scaling is less flexible and may require partitioning later.
```

### Layer 7 exit criteria

You should be able to:

- choose monolith, modular monolith, or microservices deliberately
- identify service boundaries
- define data ownership
- design multi-tenant systems
- design single-region and multi-region systems
- estimate capacity
- choose APIs and communication models
- identify bottlenecks
- analyze failure behavior
- explain migration/evolution paths
- document trade-offs instead of only drawing boxes

---

# Cross-cutting concerns

These concerns apply across all seven layers.

## Security

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
- service-to-service identity
- key rotation conceptually

Security should be part of the design, not added after the architecture is complete.

## Observability

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
- high-cardinality trade-offs conceptually

A distributed architecture that cannot be observed is extremely difficult to operate.

## Reliability

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
- graceful degradation
- dependency isolation

## Performance engineering

Learn:

- latency vs throughput
- p50/p95/p99
- CPU profiling conceptually
- memory pressure
- garbage collection effects conceptually
- connection-pool saturation
- query latency
- caching impact
- load testing
- stress testing
- capacity headroom

Optimize after identifying the real bottleneck.

## Testing

System-level testing may include:

- unit tests
- integration tests
- contract tests
- end-to-end tests
- load tests
- stress tests
- soak tests
- failure/chaos testing where justified

## Data governance and privacy

Depending on the product, consider:

- data retention
- deletion requirements
- auditability
- personally identifiable information
- regional data requirements
- access controls
- encryption
- backup retention

## Operational readiness

Before production, ask:

- Is there a dashboard?
- Is there an alert for meaningful failure?
- Is there a rollback procedure?
- Can data be restored?
- Is there a runbook for common incidents?
- Who owns the service?
- What are the SLOs?
- What is the expected capacity ceiling?

---

# How the layers connect

Consider a user creating an order.

```text
Client
  │
  │ HTTPS / REST
  ▼
Global / regional load balancing
  ▼
API Gateway
  ▼
Order API
  │
  ├────────→ Redis cache
  │
  ├────────→ SQL database
  │              │
  │              └──→ Outbox / CDC
  │                        │
  └────────────────────────┴──→ Event broker
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
| Search index | Layer 1 — Search |
| Order API code | Layer 2 — Application |
| HTTPS/REST | Layer 3 — Communication |
| API Gateway | Layer 3 — Communication/control |
| Message broker | Layer 4 — Messaging |
| Outbox/CDC | Layer 4 — Integration |
| Load balancer/runtime | Layer 5 — Infrastructure |
| retries, consistency, failures | Layer 6 — Distributed systems |
| service boundaries and data ownership | Layer 7 — Architecture |
| deciding how everything fits together | Layer 7 — System design |

That is why topics such as REST vs WebSocket or sync vs async are system-design topics while also belonging to more specific lower-level domains.

---

# Recommended learning order

## Phase 0: Prerequisites

Know enough of:

- one backend programming language
- Git
- command line
- basic SQL
- basic HTTP
- basic Linux

For Java developers, a useful baseline is Java + Spring Boot + PostgreSQL/MySQL.

## Phase 1: Data fundamentals

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
10. distributed IDs
11. search fundamentals
12. OLTP vs OLAP

Build: a CRUD application with a real relational database, indexes, transactions, pagination, caching, and a searchable dataset.

## Phase 2: Application runtime and modularity

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
10. connection pools
11. modular monolith
12. bounded contexts conceptually

Build: a modular API that performs background report generation or email delivery.

## Phase 3: Communication

Study:

1. TCP/IP basics
2. DNS
3. HTTP/HTTPS
4. REST
5. WebSocket
6. SSE
7. polling
8. gRPC
9. GraphQL
10. webhooks
11. timeouts/retries
12. API Gateway
13. BFF
14. API contract compatibility

Build: a notification or chat prototype using REST plus WebSocket/SSE.

## Phase 4: Messaging and event systems

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
12. CDC
13. event schemas
14. CQRS
15. event sourcing conceptually
16. stream processing

Build: an order system that publishes events and has separate notification/inventory consumers.

## Phase 5: Infrastructure

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
10. IaC
11. CI/CD
12. service mesh concepts
13. serverless concepts
14. multi-region infrastructure

Build: containerize the application and run multiple instances behind a reverse proxy/load balancer with an automated deployment pipeline.

## Phase 6: Distributed systems

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
12. advanced consistency models
13. multi-region replication
14. tail latency
15. failure domains

Build: deliberately inject dependency failures and design retries, timeouts, circuit breakers, reconciliation, and degraded modes.

## Phase 7: Architecture decomposition

Study:

1. monolith
2. modular monolith
3. SOA conceptually
4. microservices
5. service boundaries
6. bounded contexts
7. data ownership
8. database-per-service trade-offs
9. sync vs async service communication
10. multi-tenancy
11. architecture migration patterns
12. ADRs

Build: start with one modular application, then extract one service for a concrete reason and document the trade-offs.

## Phase 8: Full system design

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
12. multi-tenant SaaS platform
13. global API platform

For each design, explain not only what you choose but why alternatives were rejected.

---

# Practice projects

## Project 1: URL shortener

Learn:

- API design
- ID generation
- database schema
- cache
- redirects
- read-heavy scaling
- expiration
- analytics asynchronously

Advanced version: deploy across multiple regions.

## Project 2: Rate limiter

Learn:

- token bucket/sliding window
- Redis
- atomic operations
- distributed coordination
- per-user/per-IP limits
- failure behavior
- multi-region quota trade-offs

## Project 3: Notification service

Learn:

- REST API
- asynchronous queue
- retries
- DLQ
- email/SMS/push channels
- templates
- user preferences
- idempotency

## Project 4: Chat system

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
- regional routing

## Project 5: E-commerce order system

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
- service boundaries

## Project 6: News feed

Learn:

- fan-out on write vs fan-out on read
- cache
- ranking
- hot users
- pagination
- precomputation
- eventual consistency

## Project 7: File-storage service

Learn:

- metadata vs binary storage
- object storage
- multipart uploads
- CDN
- signed URLs
- replication
- lifecycle/retention

## Project 8: Search and autocomplete

Learn:

- inverted index
- indexing pipeline
- CDC
- ranking
- prefix search
- autocomplete
- typo tolerance conceptually
- cache
- index rebuilds

## Project 9: Multi-tenant SaaS

Learn:

- tenant isolation
- shared vs separate databases
- quotas
- noisy neighbors
- tenant-aware authorization
- billing boundaries
- per-tenant observability
- tenant-specific backup/restore

## Project 10: Multi-region order platform

Learn:

- global routing
- active/passive vs active/active
- data replication
- regional failover
- consistency trade-offs
- disaster recovery
- data residency

---

# System design interview workflow

A disciplined design process is more valuable than immediately drawing many boxes.

## Step 1: Clarify requirements

Ask:

- Who uses the system?
- What are the critical features?
- Which features are out of scope?
- Is real-time behavior required?
- What consistency guarantees are required?
- Is the system regional or global?

## Step 2: Define non-functional requirements

Clarify:

- latency target
- availability target
- durability target
- consistency expectations
- security/compliance constraints
- cost constraints

## Step 3: Define scale

Estimate:

- users
- requests per second
- read/write ratio
- data size
- bandwidth
- retention
- peak traffic

## Step 4: Define APIs

Write only the important interfaces.

Choose the appropriate communication model rather than assuming REST for everything.

## Step 5: Define data model and ownership

Focus on:

- entities
- access patterns
- transaction boundaries
- source of truth
- service ownership of data

## Step 6: Draw a simple high-level design

Start with:

```text
Client → API → Database
```

Then add components only when requirements justify them.

## Step 7: Identify bottlenecks

Ask what breaks first as load increases.

Consider:

- CPU
- memory
- database connections
- database IOPS
- partitions
- queue depth
- network bandwidth
- external APIs

## Step 8: Scale selectively

Consider:

- caching
- replicas
- partitioning
- queues
- CDN
- search indexes
- additional application instances

Do not add all of them by default.

## Step 9: Discuss reliability

Cover:

- timeouts
- retries
- failover
- duplicate requests/messages
- dependency outages
- data recovery
- graceful degradation

## Step 10: Discuss consistency and transactions

Explain:

- what must be strongly consistent
- what may be eventually consistent
- how distributed workflows recover
- how duplicate operations are prevented

## Step 11: Discuss regional strategy when relevant

Cover:

- global traffic routing
- write region
- replication
- failover
- data residency
- conflict resolution

## Step 12: Discuss security and observability

Cover:

- authentication/authorization
- secrets
- encryption
- auditability
- logs
- metrics
- traces
- alerting

## Step 13: Discuss trade-offs

Explicitly state decisions such as:

- SQL over NoSQL because strong relational transactions dominate the workload
- modular monolith before microservices because independent deployment is not yet required
- WebSocket for real-time delivery but REST for history retrieval
- asynchronous events for notifications because they do not belong in the order-creation critical path
- active/passive regions because the business values simpler consistency more than local writes in every region

## Step 14: Explain evolution

Describe how the architecture can grow without prematurely building the final form.

## Step 15: Revisit requirements

Confirm that the final design satisfies the original functional and non-functional requirements.

---

# Mastery checklist

## Data and storage

- [ ] I can model relational data and explain normalization vs denormalization.
- [ ] I understand indexes and can interpret basic query-plan reasoning.
- [ ] I understand ACID and common isolation levels.
- [ ] I understand optimistic and pessimistic concurrency control.
- [ ] I can compare SQL and major NoSQL models.
- [ ] I understand cache-aside and common cache failure modes.
- [ ] I can explain replication vs sharding.
- [ ] I understand shard-key and hotspot problems.
- [ ] I can compare common distributed-ID strategies.
- [ ] I understand when a search engine is preferable to database queries.
- [ ] I understand OLTP vs OLAP.

## Application and code

- [ ] I can explain a backend request lifecycle.
- [ ] I understand processes, threads, and thread pools.
- [ ] I understand race conditions and synchronization.
- [ ] I can distinguish sync/async from blocking/non-blocking.
- [ ] I understand transaction boundaries.
- [ ] I can design idempotent application operations.
- [ ] I know when to use background jobs.
- [ ] I understand connection-pool and resource-pool limits.
- [ ] I can structure a modular monolith with explicit boundaries.

## Communication

- [ ] I understand TCP, ports, DNS, TLS, and HTTP at a practical level.
- [ ] I can design a REST API.
- [ ] I can explain REST vs WebSocket.
- [ ] I understand SSE, polling, gRPC, GraphQL, and webhooks conceptually.
- [ ] I use explicit timeouts for remote calls.
- [ ] I understand safe retry strategies.
- [ ] I understand API Gateway and BFF use cases.
- [ ] I understand backward-compatible API evolution.

## Messaging and integration

- [ ] I understand queues, topics, producers, and consumers.
- [ ] I understand Kafka fundamentals.
- [ ] I understand RabbitMQ fundamentals.
- [ ] I can explain queue vs pub/sub use cases.
- [ ] I understand duplicate delivery and idempotent consumers.
- [ ] I understand ordering trade-offs.
- [ ] I understand retries and DLQs.
- [ ] I understand outbox and saga patterns.
- [ ] I understand CDC and its relationship to domain events.
- [ ] I understand event schema evolution.
- [ ] I understand CQRS and when not to use it.
- [ ] I understand event sourcing conceptually.
- [ ] I understand stream-processing fundamentals.

## Infrastructure

- [ ] I understand reverse proxies and load balancers.
- [ ] I understand horizontal vs vertical scaling.
- [ ] I understand stateless service design.
- [ ] I can containerize an application.
- [ ] I understand CDN and object-storage use cases.
- [ ] I understand basic deployment strategies.
- [ ] I understand Kubernetes concepts at a high level.
- [ ] I understand Infrastructure as Code.
- [ ] I understand a CI/CD deployment lifecycle.
- [ ] I understand service-mesh trade-offs conceptually.
- [ ] I understand serverless trade-offs conceptually.
- [ ] I understand multi-region infrastructure basics.

## Distributed systems

- [ ] I expect partial failure and network timeouts.
- [ ] I understand consistency vs availability trade-offs.
- [ ] I understand CAP and PACELC at a practical level.
- [ ] I understand replication and failover issues.
- [ ] I understand why consensus exists.
- [ ] I understand distributed-lock risks.
- [ ] I understand idempotency across service boundaries.
- [ ] I understand circuit breakers, bulkheads, rate limiting, and backpressure.
- [ ] I understand distributed-transaction alternatives.
- [ ] I understand that consistency has multiple models and guarantees.
- [ ] I understand multi-region replication trade-offs.
- [ ] I understand p95/p99 and tail latency.
- [ ] I understand failure domains and blast radius.

## Architecture

- [ ] I can compare monolith, modular monolith, SOA, and microservices.
- [ ] I can identify reasonable service boundaries.
- [ ] I understand bounded contexts at a practical level.
- [ ] I understand service data ownership.
- [ ] I can compare shared database vs database-per-service approaches.
- [ ] I understand multi-tenancy models.
- [ ] I can reason about active/passive vs active/active regions.
- [ ] I can describe how an architecture should evolve over time.
- [ ] I can record important decisions with ADR-style reasoning.

## Full system design

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
- [ ] I explain the migration path from simple architecture to more complex architecture.

---

# Glossary

| Term | Short meaning |
| --- | --- |
| Active/active | Multiple locations serve live traffic simultaneously |
| Active/passive | One location primarily serves traffic while another is prepared for failover |
| API Gateway | Entry-point infrastructure that routes and applies policies to API traffic |
| Availability | Ability to serve acceptable requests despite failures |
| Backpressure | Controlling producers when consumers cannot keep up |
| BFF | Backend for Frontend; API layer specialized for a client type |
| Bounded context | Explicit domain boundary with its own model and terminology |
| Cache | Faster storage holding reusable data to reduce latency/load |
| CDC | Change Data Capture; propagating database changes to other systems |
| CDN | Distributed edge network for serving cacheable content closer to users |
| Consumer | Component that reads/processes messages or events |
| CQRS | Separation of command/write and query/read models |
| DLQ | Queue holding messages that repeatedly failed processing |
| Event sourcing | Persisting state changes as an ordered sequence of domain events |
| Eventual consistency | Model where replicas may temporarily differ but converge later under defined assumptions |
| Idempotency | Property allowing repeated logical operations without unintended extra side effects |
| IaC | Infrastructure as Code; managing infrastructure through versioned configuration/code |
| Linearizability | Strong consistency model where operations appear to occur atomically in real-time order |
| Load balancer | Component that distributes traffic across multiple servers |
| Message broker | Infrastructure that transports messages between producers and consumers |
| Microservice | Independently deployable service aligned to a bounded capability |
| Modular monolith | One deployable application with explicit internal module boundaries |
| Multi-tenancy | Serving multiple tenants/customers from shared or partially shared infrastructure |
| Partition | Division of a dataset or event stream into subsets |
| Producer | Component that creates/publishes messages or events |
| Pub/Sub | Messaging model where published events can reach multiple subscribers |
| Replication | Maintaining copies of data on multiple nodes |
| REST | HTTP-oriented architectural style centered on resources and stateless interactions |
| Saga | Pattern coordinating distributed business transactions through local transactions and compensation |
| Service mesh | Infrastructure layer for service-to-service traffic management, identity, and observability |
| Sharding | Horizontal division of data across multiple storage nodes |
| SLO | Service Level Objective; a target for service reliability/performance |
| SSE | Server-to-client event stream over HTTP |
| Tail latency | High-percentile latency such as p95 or p99 rather than average latency |
| WebSocket | Persistent full-duplex communication protocol between client and server |

---

# Final mental model

System design can be reduced to a repeatable chain of reasoning:

```text
Requirements
    ↓
Non-functional requirements
    ↓
Traffic and scale
    ↓
API / communication model
    ↓
Application boundaries
    ↓
Data model and ownership
    ↓
Storage / cache / search
    ↓
Messaging and integration
    ↓
Infrastructure
    ↓
Distributed-system guarantees
    ↓
Failure handling
    ↓
Security + observability + cost
    ↓
Migration / evolution path
    ↓
Trade-offs
```

A second useful mental model is architecture growth:

```text
Simple application
      ↓
Well-structured modular application
      ↓
Scale proven bottlenecks
      ↓
Introduce asynchronous boundaries where justified
      ↓
Extract independently owned/deployed services where justified
      ↓
Introduce regional distribution only when requirements demand it
```

When you understand the lower layers, "system design" stops looking like a collection of random diagrams. REST, WebSocket, Kafka, Redis, databases, search engines, load balancers, Docker, Kubernetes, replication, sharding, microservices, and multi-region architecture become tools.

The real skill is knowing:

1. which problem you are solving,
2. which guarantee the system actually needs,
3. which tool solves that problem,
4. what new complexity that tool introduces,
5. how the system behaves when it fails,
6. how the architecture can evolve without unnecessary complexity.
