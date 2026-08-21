# System Design Learning Roadmap

A practical, layered curriculum for learning system design from backend fundamentals to production distributed systems, geo-distributed architecture, and architecture evolution.

The goal is **not** to memorize architecture diagrams or product names. The goal is to reason from:

```text
requirements
    ↓
invariants
    ↓
workload and scale
    ↓
data and boundaries
    ↓
communication
    ↓
failure behavior
    ↓
deployment and operations
    ↓
security, reliability, performance, and cost
    ↓
trade-offs
```

A strong design decision should be explainable in this form:

> Because requirement **X** exists, choose mechanism **Y** despite cost **Z**. If **X** changes, reconsider **Y**.

---

## Quick navigation

- [How to use this roadmap](#how-to-use-this-roadmap)
- [The nine layers](#the-nine-layers)
- [Layer 1 — Data and storage](#layer-1--data-and-storage)
- [Layer 2 — Application runtime and code](#layer-2--application-runtime-and-code)
- [Layer 3 — Networking and communication](#layer-3--networking-and-communication)
- [Layer 4 — Codebase architecture](#layer-4--codebase-architecture)
- [Layer 5 — Messaging and integration](#layer-5--messaging-and-integration)
- [Layer 6 — Infrastructure and platform](#layer-6--infrastructure-and-platform)
- [Layer 7 — Distributed systems](#layer-7--distributed-systems)
- [Layer 8 — Geo-distributed systems](#layer-8--geo-distributed-systems)
- [Layer 9 — System architecture and evolution](#layer-9--system-architecture-and-evolution)
- [Cross-cutting concerns](#cross-cutting-concerns)
- [One evolving capstone](#one-evolving-capstone)
- [Focused practice projects](#focused-practice-projects)
- [Recommended learning plan](#recommended-learning-plan)
- [System design interview workflow](#system-design-interview-workflow)
- [Mastery checklist](#mastery-checklist)
- [Reading and course map](#reading-and-course-map)
- [Final mental model](#final-mental-model)

---

## How to use this roadmap

System design is a composition skill. Learn lower-level mechanisms first, then combine them only when a requirement justifies the complexity.

### Depth labels

Every topic can be learned at one of three depths:

| Label | Meaning |
| --- | --- |
| **MUST** | Core knowledge for a strong backend engineer and system-design interviews |
| **SHOULD** | Important production knowledge for senior backend/distributed-system work |
| **ADVANCED** | Specialized knowledge that becomes valuable at large scale or in specific platforms |

Do **not** try to master every advanced product before moving forward.

### Learning loop for every topic

Use the same loop repeatedly:

1. **Problem** — what problem exists without this mechanism?
2. **Mechanism** — how does it work?
3. **Trade-offs** — what does it make slower, harder, more expensive, or less consistent?
4. **Failure** — what happens when it partially fails?
5. **Measurement** — what metric or experiment proves the behavior?
6. **Alternative** — when should another mechanism be preferred?
7. **Practice** — implement, benchmark, break, and recover it.

### Three levels of progression

```text
Level I — Backend System Fundamentals
Layers 1–3

Level II — Production Distributed Backend
Layers 4–7

Level III — Large-Scale Architecture
Layers 8–9
```

A well-designed modular monolith can remain the correct architecture indefinitely. Microservices, event sourcing, service meshes, distributed SQL, and multi-region writes are **trade-offs**, not promotions.

---

## The nine layers

| Layer | Main question | Representative topics |
| --- | --- | --- |
| **1. Data and storage** | How is state represented, queried, protected, and scaled? | SQL, indexes, transactions, WAL, replication, sharding, cache, search |
| **2. Application runtime and code** | How does one application execute work safely and efficiently? | processes, threads, concurrency, I/O, pools, transactions, idempotency |
| **3. Networking and communication** | How do clients and services exchange data? | DNS, TCP, TLS, HTTP, REST, gRPC, WebSocket, SSE |
| **4. Codebase architecture** | How should business complexity be partitioned before distribution? | monolith, modular monolith, Clean/Hexagonal, DDD, bounded contexts |
| **5. Messaging and integration** | How do components communicate asynchronously and evolve independently? | queues, streams, Kafka, RabbitMQ, outbox, CDC, schemas, saga |
| **6. Infrastructure and platform** | How does the system run, deploy, scale, and get observed? | Linux, Docker, Kubernetes, IaC, CI/CD, OTel, SLOs |
| **7. Distributed systems** | How do multiple nodes behave correctly under partial failure? | CAP/PACELC, consensus, locks, quorums, clocks, resilience |
| **8. Geo-distributed systems** | What changes when latency, data, and failure domains span regions? | multi-region, active-active, geo-partitioning, RTO/RPO |
| **9. System architecture and evolution** | How should all pieces be combined and changed over time? | requirements, capacity, decomposition, microservices, migration, cost |

The ordering is intentional:

```text
Data
  ↓
Runtime
  ↓
Network
  ↓
Code boundaries
  ↓
Messaging
  ↓
Platform
  ↓
Distributed guarantees
  ↓
Geo distribution
  ↓
Architecture synthesis and evolution
```

---

# Layer 1 — Data and storage

**Primary question:** Where does system state live, and how can it be read, changed, recovered, and scaled safely?

**Difficulty:** intermediate → advanced

## 1.1 Relational modeling — MUST

Learn:

- tables, rows, columns
- primary and foreign keys
- one-to-one, one-to-many, many-to-many
- normalization and denormalization
- joins
- constraints
- schema migrations
- data invariants
- access-pattern-driven modeling

Ask:

- Which data must be unique?
- Which fields must change atomically?
- What are the dominant read/write paths?
- Which queries require low latency?
- What grows fastest?

Good learning databases:

- PostgreSQL
- MySQL/InnoDB

## 1.2 Indexes and query plans — MUST

Learn:

- B-tree indexes
- hash indexes conceptually
- composite indexes
- covering indexes
- selectivity
- leftmost-prefix behavior
- clustered vs non-clustered concepts
- read amplification vs write amplification
- query execution plans
- index maintenance cost

Be able to explain why an index such as `(customer_id, created_at)` may help:

```sql
SELECT id, customer_id, total_amount
FROM orders
WHERE customer_id = ?
ORDER BY created_at DESC
LIMIT 20;
```

but also why adding indexes can reduce write throughput.

## 1.3 Transactions, isolation, locking, and MVCC — MUST

Learn:

- ACID
- transaction boundaries
- Read Committed
- Repeatable Read
- Serializable
- dirty reads
- non-repeatable reads
- phantoms
- lost updates
- write skew
- optimistic locking
- pessimistic locking
- deadlocks
- MVCC

Do not confuse **transaction isolation** with distributed-system consistency models such as linearizability.

## 1.4 WAL, recovery, and durability — SHOULD

Learn:

- write-ahead logging
- checkpoints
- crash recovery
- redo/undo concepts
- fsync/durability trade-offs
- backup vs replication
- point-in-time recovery

A replica is not automatically a backup.

## 1.5 Caching — MUST

Learn:

- browser/client cache
- CDN cache
- reverse-proxy cache
- in-process cache
- distributed cache
- Redis
- cache-aside
- read-through
- write-through
- write-behind

Failure modes:

- stale data
- cache invalidation
- cache stampede
- cache penetration
- hot keys
- eviction
- TTL mistakes
- cache outage

The question is not “Where can Redis be added?” but “Which latency/load problem justifies another copy of state?”

## 1.6 Replication — MUST

Learn:

- leader/follower
- synchronous vs asynchronous replication
- read replicas
- replication lag
- failover
- multi-leader conceptually
- leaderless conceptually
- stale reads
- read-your-writes implications

Replication creates copies. It does not partition a logical dataset.

## 1.7 Partitioning and sharding — MUST

Learn:

- horizontal vs vertical partitioning
- range sharding
- hash sharding
- directory-based sharding
- shard keys
- hot partitions
- cross-shard reads
- cross-shard transactions
- rebalancing
- resharding

### Consistent hashing — SHOULD

Understand:

- why `hash(key) % N` causes large remapping when `N` changes
- hash rings
- node placement
- virtual nodes
- skew
- partition movement

## 1.8 SQL vs NoSQL — MUST

Know the major models:

| Model | Typical strengths |
| --- | --- |
| Relational | constraints, joins, transactions, structured data |
| Key-value | simple low-latency access, cache/session/counters |
| Document | nested aggregate-shaped data, flexible document models |
| Wide-column | very large partitioned datasets |
| Graph | relationship-heavy traversals |
| Time-series | timestamp-oriented telemetry/metrics |

Choose from **invariants + access patterns + scale + consistency + operational capability**, not fashion.

## 1.9 Distributed SQL — SHOULD

Use systems such as CockroachDB conceptually to learn:

- distributed transactions
- consensus-backed replication
- locality
- distributed query execution
- serializable transactions
- multi-region latency trade-offs

Distributed SQL does not remove network latency.

## 1.10 Distributed ID generation — SHOULD

Compare:

```text
database sequence
→ UUID
→ time-sortable UUID/ID
→ Snowflake-style ID
→ range allocation
```

Understand:

- uniqueness
- ordering semantics
- worker identity
- sequence exhaustion
- clock rollback
- collision risk
- information leakage

## 1.11 Search engines — SHOULD

Learn:

- inverted indexes
- analyzers/tokenization
- postings
- filters vs scoring
- BM25 conceptually
- refresh intervals
- segments
- replication
- reindexing
- search consistency/freshness

Use Elasticsearch/OpenSearch/Lucene as learning examples.

A search engine is usually **not** the primary transactional source of truth.

## 1.12 Object storage — MUST

Learn:

- block vs file vs object storage
- S3-style object storage
- metadata vs binary data
- multipart/chunked upload
- signed/pre-signed URLs
- CDN integration
- lifecycle rules
- versioning
- replication
- cold/archive storage

## 1.13 OLTP vs OLAP — SHOULD

Understand:

- transactional workloads vs analytical workloads
- read/write patterns
- row-oriented vs column-oriented concepts
- denormalization for analytics
- workload isolation
- CDC/ETL/ELT relationships
- data warehouse/lake concepts

## Layer 1 hands-on

- Create several million synthetic order rows.
- Benchmark slow queries before/after index changes.
- Reproduce transaction anomalies with concurrent sessions.
- Configure or simulate replication lag.
- Implement a basic shard router.
- Implement a Snowflake-style ID generator.
- Index a catalog into Elasticsearch/OpenSearch.
- Upload large files using object storage + signed URLs.

## Layer 1 exit criteria

You can explain:

- why an index helps one workload and hurts another
- how isolation prevents or permits anomalies
- WAL vs replication vs backup
- replication vs partitioning
- cache invalidation and stampedes
- how to select a shard key
- SQL vs NoSQL from access patterns
- why search indexes are separate from transactional state
- when globally distributed SQL is worth its latency/complexity

---

# Layer 2 — Application runtime and code

**Primary question:** How does one process receive work, execute it concurrently, and interact with external resources?

**Difficulty:** intermediate

## 2.1 Backend request lifecycle — MUST

Understand:

```text
socket accepted
    ↓
web/application server
    ↓
middleware/filter
    ↓
authentication/authorization
    ↓
routing
    ↓
controller
    ↓
business logic
    ↓
database/cache/external service
    ↓
serialization
    ↓
response
```

## 2.2 Process, thread, concurrency, and parallelism — MUST

Learn:

- process vs thread
- concurrency vs parallelism
- race conditions
- critical sections
- mutex/lock
- semaphore
- atomic operations
- context switching
- CPU-bound vs I/O-bound work

For Java, understand:

- `Thread`
- `Runnable`
- `Callable`
- `ExecutorService`
- `Future`
- `CompletableFuture`
- concurrent collections
- synchronization primitives
- virtual threads conceptually

## 2.3 Sync/async vs blocking/non-blocking — MUST

These dimensions are related but different.

Learn:

- synchronous coordination
- asynchronous work
- blocking I/O
- non-blocking I/O
- event loops
- reactive/event-driven I/O
- callback/future/promise models

Do not assume non-blocking code is always a better engineering choice.

## 2.4 Thread pools and connection pools — MUST

Learn:

- pool sizing
- queue behavior
- saturation
- rejection
- connection acquisition time
- database max connections
- head-of-line blocking
- pool starvation

More threads or database connections can make the system slower.

## 2.5 Application structure — MUST

Understand:

- controller/API layer
- service/application layer
- domain logic
- repository/DAO
- DTOs
- validation
- mapping
- dependency injection
- inversion of control

This is not yet “architecture”; it is the local structure of one application.

## 2.6 Transaction boundaries — MUST

Learn:

- where transactions begin/end
- connection usage inside transactions
- rollback behavior
- transaction propagation conceptually
- why slow remote calls inside DB transactions are dangerous
- optimistic concurrency

For Java/Spring, understand `@Transactional` conceptually rather than treating it as magic.

## 2.7 Background jobs — MUST

Examples:

- email
- image/video processing
- report generation
- imports
- cleanup
- notifications

Understand when work should leave the request path.

## 2.8 Idempotency — MUST

Important for:

- payments
- order creation
- webhooks
- retries
- message consumers

Techniques:

- idempotency keys
- unique constraints
- processed-message tables
- compare-and-set/versioning
- persisted previous result

## Layer 2 hands-on

- Compare blocking and non-blocking handling under I/O-heavy load.
- Saturate a thread pool and observe queue/latency behavior.
- Saturate a DB connection pool.
- Implement background report generation.
- Simulate a client timeout after server commit and prove retry safety.

## Layer 2 exit criteria

You can explain:

- sync vs async vs blocking vs non-blocking
- thread pool and connection pool saturation
- CPU-bound vs I/O-bound work
- request lifecycle
- transaction boundaries
- idempotency
- when to move work to a background job

---

# Layer 3 — Networking and communication

**Primary question:** How do machines and clients exchange requests, responses, and long-lived streams?

**Difficulty:** intermediate

## 3.1 Networking fundamentals — MUST

Learn:

- IP addresses
- ports
- sockets
- TCP
- UDP conceptually
- connection establishment
- packet loss conceptually
- DNS
- TLS
- proxies
- timeouts

You do not need to become a network engineer, but you must know where latency and failure can appear.

## 3.2 DNS — MUST

Learn:

- resolver flow
- authoritative DNS
- A/AAAA
- CNAME
- TTL
- caching
- propagation behavior
- basic failover/routing implications

## 3.3 TLS — MUST

Learn conceptually:

- HTTPS
- certificates
- certificate chain
- handshake
- TLS termination
- mTLS conceptually
- encryption in transit

## 3.4 HTTP semantics — MUST

Learn:

- GET/POST/PUT/PATCH/DELETE
- safe vs unsafe methods
- idempotent methods
- status codes
- headers
- cookies
- compression
- content negotiation
- connection reuse
- caching semantics
- ETag / validators
- `Cache-Control`

## 3.5 HTTP/1.1, HTTP/2, HTTP/3 and QUIC — SHOULD

Understand conceptually:

- persistent connections
- multiplexing
- header compression
- TCP head-of-line effects
- HTTP/3 over QUIC
- transport implications

Do not memorize protocol frames for ordinary system-design interviews.

## 3.6 REST — MUST

Study:

- resources
- URI design
- HTTP semantics
- statelessness
- pagination
- cursor pagination
- filtering/sorting
- versioning
- errors
- auth boundaries
- idempotency
- rate limits

## 3.7 WebSocket — MUST

Useful for:

- chat
- multiplayer
- live collaboration
- trading/live dashboards
- presence

Learn:

- full-duplex communication
- persistent connections
- heartbeat
- reconnect
- connection routing
- backpressure
- connection-aware load balancing

## 3.8 Server-Sent Events — SHOULD

Use when communication is mostly:

```text
server → client
```

Examples:

- progress
- live feeds
- notifications

## 3.9 Polling and long polling — MUST

Understand their simplicity and their request/connection overhead.

## 3.10 gRPC — SHOULD

Learn:

- Protocol Buffers
- strongly defined contracts
- binary serialization
- unary calls
- client/server/bidirectional streaming
- service-to-service use cases

Compare REST and gRPC by ecosystem, contracts, browser/client needs, performance, and observability.

## 3.11 GraphQL — SHOULD

Learn:

- schema
- queries
- mutations
- resolvers
- N+1 problem
- batching
- caching complexity
- authorization complexity
- over-fetching/under-fetching trade-offs

## 3.12 Webhooks — MUST

Learn:

- callback delivery
- signatures
- retries
- timeout
- duplicate delivery
- ordering
- idempotency

## 3.13 Timeouts, deadlines, retries, and jitter — MUST

Learn:

- connection timeout
- read/request timeout
- end-to-end deadlines
- exponential backoff
- jitter
- retryable vs non-retryable errors
- retry amplification
- retry budgets conceptually

Retries are a source of load, not free reliability.

## Layer 3 hands-on

- Trace DNS → TLS → HTTP using command-line tools.
- Run HTTP/1.1 vs HTTP/2 experiments.
- Build REST + WebSocket/SSE in the same application.
- Implement webhook delivery with signatures, retries, and deduplication.
- Inject slow upstream responses and measure retry amplification.

## Layer 3 exit criteria

You can choose and defend:

- REST vs gRPC
- REST vs WebSocket
- WebSocket vs SSE
- polling vs push
- webhook vs polling
- HTTP caching behavior
- safe timeout/retry behavior

---

# Layer 4 — Codebase architecture

**Primary question:** How should business complexity be separated before adding network boundaries?

**Difficulty:** intermediate → advanced

A critical rule:

> Do not learn microservices before learning to create strong boundaries inside one process.

## 4.1 Traditional monolith — MUST

Understand:

- one deployable/runtime
- simple local calls
- simple transactions
- low operational overhead
- risks of uncontrolled coupling

A monolith is not automatically bad architecture.

## 4.2 Modular monolith — MUST

Learn:

- explicit modules
- public module APIs
- internal implementation boundaries
- dependency rules
- module-level tests
- event-based communication inside one process when useful
- module-owned persistence abstractions

Example domains:

```text
Identity
Catalog
Ordering
Inventory
Billing
Fulfillment
Reporting
```

## 4.3 Dependency direction — MUST

Learn:

- dependency inversion
- stable boundaries
- framework vs business-policy dependencies
- infrastructure as an implementation detail

## 4.4 Hexagonal Architecture — SHOULD

Learn:

- ports
- adapters
- application core
- inbound adapters
- outbound adapters
- testability

Use it when it clarifies dependencies; avoid ceremony for its own sake.

## 4.5 Clean Architecture — SHOULD

Learn:

- dependency direction
- domain/business rules
- application use cases
- interface adapters
- frameworks/drivers

Compare principles, not folder names.

## 4.6 Domain-Driven Design — SHOULD

Learn:

- domain model
- ubiquitous language
- aggregate
- entity/value object
- domain service
- bounded context
- context map

The strategic value is identifying **where a model applies**.

## 4.7 Boundaries and data ownership — MUST

Use this progression:

```text
domain boundary
    ↓
module boundary
    ↓
data ownership boundary
    ↓
team boundary
    ↓
deployment/service boundary only when justified
```

A bounded context is not automatically a microservice.

## 4.8 Multi-tenancy — SHOULD

Treat tenancy as a boundary problem.

Compare:

- shared DB/shared schema
- shared DB/separate schema
- database per tenant
- dedicated infrastructure

Consider:

- tenant identity
- authorization
- noisy neighbors
- quotas
- connection pools
- backup/restore
- migrations
- encryption
- placement
- cost attribution

## 4.9 Architecture fitness and tests — SHOULD

Learn:

- architecture tests
- module/component tests
- dependency rules
- contract tests
- migration tests
- fitness functions conceptually

If a boundary cannot be enforced or tested, it is likely to decay.

## Layer 4 hands-on

- Refactor a layered monolith into explicit domain modules.
- Prevent one module from importing another module’s internals.
- Convert one domain to ports/adapters.
- Add architecture tests.
- Implement shared-schema tenancy, then design a stronger-isolation alternative.

## Layer 4 exit criteria

You can explain:

- monolith vs modular monolith
- Clean vs Hexagonal at the principle level
- DDD bounded contexts
- why domain boundary ≠ service boundary
- data ownership
- multi-tenancy isolation choices
- how to enforce module boundaries

---

# Layer 5 — Messaging and integration

**Primary question:** How do independent components coordinate when immediate synchronous completion is not required?

**Difficulty:** advanced

## 5.1 Queue vs stream — MUST

A useful distinction:

```text
Task queue:
"Please process this work."

Event stream/log:
"This event happened."
```

They overlap, but optimize for different semantics.

## 5.2 Messaging fundamentals — MUST

Learn:

- producer
- consumer
- queue
- topic
- partition
- offset
- acknowledgement
- consumer group
- routing
- retention

## 5.3 RabbitMQ / task-oriented messaging — MUST

Learn conceptually:

- exchanges
- queues
- bindings
- routing keys
- acknowledgements
- prefetch
- retry
- dead-letter exchanges/queues

## 5.4 Kafka / durable event streams — MUST

Learn:

- broker
- topic
- partition
- producer
- consumer
- consumer group
- offset
- retention
- replication
- partition ordering
- replay
- consumer lag

Do not reduce Kafka vs RabbitMQ to “fast vs simple.”

## 5.5 Delivery semantics — MUST

Understand:

- at-most-once
- at-least-once
- effectively-once effects

Treat “exactly once” as a scoped guarantee. Ask exactly once **where**.

## 5.6 Ordering — MUST

Ask:

- global order?
- per partition?
- per user?
- per order?
- can consumers reorder?

Global ordering is expensive and often unnecessary.

## 5.7 Retry, poison messages, and DLQ — MUST

A DLQ is not the end of a design.

Also define:

- monitoring
- investigation
- replay
- manual repair
- idempotent processing
- retention

## 5.8 Event-driven architecture — MUST

Learn:

- command vs event vs query
- temporal decoupling
- fan-out
- consumer independence
- eventual consistency
- replay
- observability challenges

## 5.9 Transactional Outbox — MUST

Classic dual-write problem:

```text
write business state
+
publish event
```

If only one succeeds, state diverges.

Transactional outbox:

```text
business row + outbox row
        same DB transaction
                ↓
        asynchronous publisher/CDC
                ↓
             stream
```

## 5.10 Change Data Capture — SHOULD

Learn:

- log-based CDC
- database change logs
- Debezium conceptually
- source-of-truth implications
- ordering
- snapshot/bootstrap
- schema changes
- replay/recovery

## 5.11 Event schema evolution — SHOULD

Learn:

- backward compatibility
- forward compatibility
- full compatibility
- additive changes
- breaking changes
- defaults
- versioning
- semantic compatibility
- Avro/Protobuf/schema registry concepts

Transport compatibility does not guarantee business-semantic compatibility.

## 5.12 Saga and workflows — MUST

Learn:

- choreography
- orchestration
- compensating actions
- retries
- timeouts
- reconciliation
- workflow state

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

## 5.13 CQRS — SHOULD

Separate command/write concerns from query/read concerns when the workload justifies different models.

Learn:

- command model
- read model
- projection
- eventual consistency
- rebuildability

CQRS is not the same thing as event sourcing.

## 5.14 Event Sourcing — ADVANCED

Learn:

- event log as source of truth
- aggregate reconstruction
- snapshots
- projection rebuild
- event correction
- event versioning
- debugging/audit benefits
- operational complexity

Do not adopt it without a concrete business reason.

## 5.15 Stream processing — ADVANCED

Learn:

- event time
- processing time
- windows
- watermarks
- state
- joins
- checkpoints
- late/out-of-order events
- exactly-once state semantics

Use Flink/Kafka Streams conceptually or experimentally.

## Layer 5 hands-on

- Build the same async workflow once with RabbitMQ and once with Kafka concepts.
- Crash consumers after receiving but before persisting results.
- Prove duplicate delivery does not corrupt state.
- Implement Outbox → CDC → Kafka.
- Evolve an Avro/Protobuf event across old/new producers and consumers.
- Build a CQRS projection and delete/rebuild it.
- Prototype event sourcing only for one small domain.

## Layer 5 exit criteria

You can explain:

- queue vs durable stream
- Kafka vs RabbitMQ by semantics
- delivery and ordering guarantees
- duplicate handling
- retry/DLQ recovery
- outbox/CDC
- schema evolution
- saga
- CQRS vs event sourcing
- when stream processing is justified

---

# Layer 6 — Infrastructure and platform

**Primary question:** How is the system packaged, deployed, scaled, observed, rolled back, secured, and paid for?

**Difficulty:** advanced

## 6.1 Linux/runtime operations — MUST

Learn:

- processes
- signals
- file permissions
- environment variables
- sockets/ports
- CPU/memory
- disk
- file descriptors conceptually
- logs
- shell/network tools

## 6.2 Reverse proxy and load balancing — MUST

Learn:

- reverse proxy
- L4 vs L7
- round robin
- least connections
- weighted routing
- health checks
- sticky sessions
- connection draining
- TLS termination

Examples:

- NGINX
- HAProxy
- cloud load balancers

## 6.3 Vertical vs horizontal scaling — MUST

Understand limits and trade-offs of both.

## 6.4 Stateless application servers — MUST

Externalize shared state when multiple instances must serve interchangeable traffic.

## 6.5 Docker — MUST

Learn:

- image
- container
- Dockerfile
- layers
- volumes
- network
- registry
- configuration
- image security basics

## 6.6 Kubernetes — SHOULD

Learn:

- Pod
- Deployment
- Service
- Ingress/Gateway concepts
- ConfigMap
- Secret
- readiness/liveness/startup probes
- rolling deployments
- autoscaling
- requests/limits conceptually

Understand why readiness and liveness are different.

## 6.7 Infrastructure as Code — SHOULD

Learn:

- Terraform or equivalent
- desired state
- plan/apply
- state
- modules
- environment separation
- drift
- secrets/state risks
- reviewable infrastructure changes

## 6.8 CI — MUST

A useful pipeline:

```text
format/lint
→ unit tests
→ module/integration tests
→ contract tests
→ migration validation
→ security/dependency scan
→ image build
→ artifact publish
```

CI is a feedback system, not a YAML collection.

## 6.9 Continuous delivery/deployment — SHOULD

Learn:

- build once/promote artifact
- environment promotion
- release gates
- rollback
- automated verification
- database compatibility

## 6.10 Database deployment compatibility — MUST

Learn:

```text
expand
→ migrate/backfill
→ switch
→ contract
```

Rolling/canary deployments may run old and new code at the same time.

A code rollback cannot undo destructive data/schema changes automatically.

## 6.11 Deployment strategies — MUST

Compare:

| Strategy | Key property |
| --- | --- |
| Rolling | gradual replacement; mixed versions |
| Blue/green | two environments; fast traffic switch |
| Canary | limited exposure before wider rollout |
| Feature flag | deploy code separately from activation |

## 6.12 Autoscaling — MUST

Signals may include:

- CPU
- memory
- RPS
- latency
- queue depth
- custom business metrics

Wrong autoscaling signals can amplify incidents.

## 6.13 Observability with OpenTelemetry — SHOULD

Learn:

- logs
- metrics
- traces
- context propagation
- correlation IDs
- distributed tracing
- latency percentiles
- service/resource attributes
- collector concepts

Trace one request across:

```text
gateway
→ service
→ database
→ event producer
→ broker
→ consumer
```

## 6.14 SLI, SLO, SLA, and error budgets — SHOULD

Learn:

- availability SLI
- latency SLI
- correctness/freshness where relevant
- SLO
- SLA
- error budget
- symptom-based alerting

## 6.15 Service mesh — ADVANCED

Learn only after understanding service networking.

Concepts:

- data plane
- control plane
- mTLS
- traffic policy
- telemetry
- retries/timeouts
- service identity

Examples: Istio/Linkerd.

Do not add a mesh solely because the architecture uses microservices.

## 6.16 Serverless — SHOULD

Learn:

- event-driven functions
- cold starts
- runtime limits
- scaling
- concurrency
- platform coupling
- idle vs burst cost
- observability

## 6.17 Capacity and cost engineering — SHOULD

Learn:

- p50/p95/p99
- throughput
- concurrency
- Little’s Law conceptually
- CPU/memory/disk/network saturation
- load testing
- headroom
- storage growth
- data transfer
- managed-service cost
- observability cost
- idle failover capacity
- engineering/operational cost

## Layer 6 hands-on

- Run multiple app instances behind a load balancer.
- Containerize the capstone.
- Deploy to Kubernetes.
- Misconfigure probes, observe failure, then fix them.
- Reproduce infrastructure with Terraform.
- Build CI/CD.
- Deploy rolling, blue/green, and canary variants.
- Inject a regression and roll back from telemetry.
- Instrument end-to-end traces with OpenTelemetry.
- Define SLOs and alerts.
- Compare one bursty worker as container vs serverless.

## Layer 6 exit criteria

You can explain:

- reverse proxy/L4/L7 load balancing
- horizontal scaling
- Docker vs orchestration
- Kubernetes probes and rollouts
- IaC state/drift
- CI vs continuous delivery vs continuous deployment
- expand/migrate/contract DB changes
- rolling vs blue/green vs canary
- OTel tracing and SLOs
- when mesh/serverless are unnecessary
- capacity and cost trade-offs

---

# Layer 7 — Distributed systems

**Primary question:** What changes when failures, delays, and state span independent machines?

**Difficulty:** advanced

Assume:

- requests time out
- responses disappear
- nodes crash independently
- dependencies become slow
- networks partition
- messages duplicate
- messages reorder
- clocks disagree

## 7.1 Failure models and partial failure — MUST

A remote call is not a function call.

Learn:

- crash failure
- timeout ambiguity
- slow dependencies
- partitions
- retry ambiguity
- cascading failure

## 7.2 Latency, availability, and durability — MUST

Reason about:

- critical paths
- tail latency
- redundancy
- failover
- graceful degradation
- failure domains

## 7.3 Consistency models — MUST/SHOULD

**MUST:**

- strong consistency conceptually
- eventual consistency
- read-your-writes
- monotonic reads

**SHOULD:**

- linearizability
- serializability
- causal consistency

Know that these terms answer different questions.

## 7.4 CAP — MUST

Understand CAP as behavior **during a network partition**, not “pick any two forever.”

## 7.5 PACELC — SHOULD

Reason about:

```text
Partition:
Availability vs Consistency

Else:
Latency vs Consistency
```

## 7.6 Replication, quorum, and failover — MUST

Learn:

- leaders
- followers
- leader election
- quorum
- read/write quorum concepts
- replication lag
- split brain
- stale reads
- failover

## 7.7 Consensus — SHOULD

Understand what problem consensus solves.

Study:

- replicated state machine
- leader election
- log replication
- majority/quorum
- safety
- liveness conceptually

### Raft — SHOULD

Learn:

- terms
- leader election
- log replication
- commit
- majority
- leader failure
- partition behavior

### Paxos — ADVANCED

Know what family of problem it solves; deep implementation knowledge is optional for most backend roles.

## 7.8 Distributed locks, leases, and fencing — SHOULD

Learn:

- lease expiry
- stale lock holder
- partitions
- clock assumptions
- fencing tokens

Prefer designs based on data constraints or ownership where possible.

## 7.9 Logical clocks and ordering — SHOULD

Learn:

- clock skew
- monotonic clocks conceptually
- Lamport clocks
- vector clocks conceptually
- sequence numbers
- partition-local ordering

Wall-clock timestamps are not a universal distributed ordering mechanism.

## 7.10 Distributed transactions — SHOULD

Learn:

- why global ACID becomes expensive
- two-phase commit conceptually
- local transactions
- saga
- outbox
- idempotency
- reconciliation
- deterministic transactions as an advanced design point

## 7.11 Resilience patterns — MUST

Learn:

- timeout
- deadline
- retry
- exponential backoff
- jitter
- circuit breaker
- bulkhead
- fallback
- graceful degradation

## 7.12 Rate limiting — MUST

Algorithms:

- fixed window
- sliding window
- token bucket
- leaky bucket

Design for:

- fairness
- abuse control
- expensive resources
- per-tenant quotas

## 7.13 Backpressure and load shedding — MUST

When producers exceed consumers:

- slow producers
- bound queues
- reject
- prioritize
- degrade optional work
- scale
- shed load

An unbounded queue often converts overload into a delayed outage.

## Layer 7 hands-on

- Inject timeout, latency, packet loss, and crashes.
- Implement a consistent-hashing ring.
- Implement distributed-rate-limiter variants.
- Complete a Raft lab or simplified simulator.
- Simulate leader failure and minority partitions.
- Reproduce stale reads under replication lag.
- Demonstrate fencing tokens in a lease-expiry scenario.

## Layer 7 exit criteria

You can explain:

- partial failure
- tail latency
- CAP/PACELC
- linearizability vs serializability
- replication/quorum
- why consensus exists
- Raft at a conceptual level
- distributed lock risks
- clocks and ordering
- distributed transaction alternatives
- retry/circuit breaker/bulkhead/backpressure behavior

---

# Layer 8 — Geo-distributed systems

**Primary question:** What changes when users, replicas, writes, failures, and legal constraints span geographic regions?

**Difficulty:** advanced → expert

## 8.1 Failure domains — MUST

Progression:

```text
single process
→ multiple instances
→ multi-zone
→ multi-region
```

Know which failure each level protects against.

## 8.2 Cross-region replication — MUST

Compare:

- synchronous
- asynchronous

Reason about:

- latency
- durability
- RPO
- failover
- stale data
- quorum distance

## 8.3 Multi-region topologies — MUST

| Topology | Main trade-off |
| --- | --- |
| Single region, multi-AZ | simple; region remains a failure domain |
| Active-passive | simpler write ownership; failover/idle capacity |
| Active-active stateless + one write region | global compute/read locality; centralized writes |
| Active-active with regional ownership | local writes; ownership movement complexity |
| Multi-writer database | local/global write flexibility; conflict/consensus cost |

Multi-region does **not** automatically mean active-active writes.

## 8.4 Geo-partitioning and home-region routing — SHOULD

Model:

```text
tenant/user
    ↓
home region
    ↓
shard
    ↓
replica set
```

Learn:

- ownership
- routing
- locality
- migration
- cross-region workflows

## 8.5 Cross-region quorum and latency — SHOULD

Understand why globally distributed consensus makes geography part of write latency.

Study Spanner/CockroachDB concepts as examples.

## 8.6 Active-active conflicts — ADVANCED

Analyze invariants such as:

- unique username
- inventory decrement
- payment capture
- monotonically increasing sequences
- session/read-your-writes

Do not say “active-active” without explaining conflict and consistency behavior.

## 8.7 Multi-tenancy at geographic scale — SHOULD

Consider:

- tenant home region
- data residency
- dedicated tenants
- encryption/key locality
- hot tenant isolation
- tenant migration
- failover eligibility
- cost attribution

## 8.8 Disaster recovery — MUST

Learn:

- backup
- restore
- replication
- failover
- failback
- RTO
- RPO
- runbooks
- drills

A DR plan that is never exercised is an assumption.

## 8.9 Multi-region cost — SHOULD

Include:

- duplicate compute
- database replicas
- cross-region transfer
- replication
- cache
- logs/traces
- idle failover capacity
- operational burden

## Layer 8 hands-on

- Simulate 50/100/200 ms inter-region latency.
- Compare local vs quorum writes.
- Design active-passive and execute a failover drill.
- Design active-active and list every harder invariant.
- Define tenant home-region placement.
- Write RTO/RPO and a DR runbook.
- Produce a multi-region cost model.

## Layer 8 exit criteria

You can explain:

- multi-AZ vs multi-region
- sync vs async cross-region replication
- active-passive vs active-active
- geo-partitioned ownership
- cross-region quorum latency
- regional tenancy/data residency
- RTO/RPO
- when multi-region is not worth the cost

---

# Layer 9 — System architecture and evolution

**Primary question:** Given requirements and constraints, what architecture should exist now, and how should it evolve later?

**Difficulty:** advanced → expert synthesis

## 9.1 Functional requirements — MUST

Define what the product must do before drawing infrastructure.

## 9.2 Non-functional requirements — MUST

Specify:

- latency
- throughput
- availability
- durability
- consistency
- security
- compliance
- geography
- maintainability
- cost

## 9.3 Capacity estimation — MUST

Estimate enough to drive decisions:

- active users
- RPS
- peak multiplier
- read/write ratio
- payload size
- storage growth
- bandwidth
- retention

Avoid false precision.

## 9.4 API and communication design — MUST

Choose:

- REST
- gRPC
- WebSocket
- SSE
- webhook
- queue/stream

based on interaction requirements rather than one global preference.

## 9.5 Data model and invariants — MUST

Start from:

- critical entities
- invariants
- query paths
- consistency needs
- partition keys
- retention

## 9.6 High-level architecture — MUST

Start simple:

```text
Client
  ↓
Application
  ↓
Database
```

Then add components only when requirements justify them.

## 9.7 API Gateway and BFF — SHOULD

Learn:

- edge routing
- authentication
- rate limiting
- aggregation
- protocol translation
- gateway offloading
- client-specific BFF

Do not create a BFF when all clients have the same needs.

## 9.8 Service decomposition and microservices — MUST/SHOULD

A service boundary should be justified by reasons such as:

- independent deployment
- independent scaling
- team ownership
- reliability isolation
- data ownership
- regulatory/security boundary
- technology/runtime need

Microservices add:

- network failure
- distributed transactions
- contracts
- telemetry
- deployment complexity
- data ownership problems
- higher operational cost

## 9.9 Failure analysis — MUST

For every component ask:

> What happens if it is slow, unavailable, duplicated, partitioned, or returns stale data?

## 9.10 Bottleneck analysis — MUST

Potential bottlenecks:

- database CPU/I/O
- connection pools
- hot shards
- cache keys
- broker partitions
- thread pools
- network
- external APIs
- lock contention
- region links

## 9.11 Security analysis — MUST

Include:

- trust boundaries
- authentication
- authorization
- tenant isolation
- secrets
- encryption
- abuse/rate limits
- auditability

## 9.12 Observability and operability — MUST

Include:

- SLO
- logs
- metrics
- traces
- dashboards
- alerts
- runbooks
- rollback
- repair/reconciliation

## 9.13 Cost analysis — MUST

Architecture is not a contest for maximum redundancy.

Ask:

- what must scale?
- what can stay simple?
- which managed service reduces human cost?
- which replication is actually required?
- what is the cost of observability/data transfer?

## 9.14 Architecture evolution — MUST

Prefer:

```text
measure constraint
→ isolate boundary
→ introduce abstraction
→ migrate incrementally
→ validate
→ retire old path
```

over:

```text
announce rewrite
→ recreate every dependency over network
```

## 9.15 Strangler pattern — SHOULD

Use incremental replacement around existing behavior when moving from a legacy/monolithic system.

## 9.16 Architecture Decision Records — SHOULD

For important decisions record:

- context
- decision
- alternatives
- consequences
- reversal conditions

## Layer 9 exit criteria

You can:

- clarify requirements before technology choices
- quantify scale enough to identify bottlenecks
- define invariants and consistency
- choose communication and storage intentionally
- analyze failure/security/operations/cost
- justify service boundaries
- propose an incremental migration
- explain why unnecessary components should be removed

---

# Cross-cutting concerns

These are **not one final layer**. Apply them to every layer.

## Security

Learn:

- authentication vs authorization
- sessions
- JWT trade-offs
- OAuth 2.0 / OpenID Connect concepts
- TLS/mTLS
- encryption at rest
- secret management
- least privilege
- input validation
- common web/API risks
- audit logging
- tenant isolation
- supply-chain/dependency risk conceptually

Ask at every layer:

> Who is allowed to perform this operation, on which resource, under which identity?

## Observability

Use:

- logs
- metrics
- traces
- correlation/context
- latency percentiles
- traffic
- errors
- saturation
- consumer lag
- replication lag
- freshness metrics

Observability should start in Layer 1–3, not only after Kubernetes.

## Reliability

Learn:

- SLI/SLO/SLA
- error budgets
- redundancy
- failover
- backups
- restore
- RTO/RPO
- graceful degradation
- reconciliation
- incident/runbook thinking

## Testing

Use a portfolio:

- unit
- module/component
- repository/integration
- contract
- end-to-end
- migration
- load
- stress
- fault/chaos where justified

## Performance

Learn:

- latency
- throughput
- concurrency
- p50/p95/p99
- queueing
- backpressure
- CPU/memory/disk/network saturation
- thread/connection pool sizing
- profiling
- load testing
- capacity headroom

## Data governance and privacy

Consider:

- retention
- deletion
- auditability
- PII
- regional requirements
- access control
- backup retention
- data lineage where relevant

## Cost

Include cost before final architecture approval:

- compute
- storage
- database
- cache
- messaging
- data transfer
- observability
- failover capacity
- engineering/operations

---

# One evolving capstone

Use **one long-running system** to learn architectural evolution.

Recommended example:

> **Multi-tenant e-commerce/marketplace platform**

Possible domains:

```text
Identity
Catalog
Ordering
Inventory
Billing
Fulfillment
Search
Notification
Reporting
```

## Capstone evolution

### Stage 1 — Simple system

```text
Client
  ↓
Spring Boot / backend app
  ↓
PostgreSQL
```

Learn:

- REST
- schema
- transactions
- pagination

### Stage 2 — Data-aware application

Add:

- indexes
- query-plan analysis
- Redis
- idempotency
- background jobs

Measure actual bottlenecks.

### Stage 3 — Specialized storage

Add only with a requirement:

- object storage for media
- search index for catalog search
- analytics/OLAP path

### Stage 4 — Modular monolith

Refactor into explicit domains and enforce dependency boundaries.

### Stage 5 — Reliable asynchronous integration

Add:

- queue/stream
- outbox
- CDC
- schema evolution
- idempotent consumers

### Stage 6 — Selective services

Extract only one or two boundaries, for example:

- Search
- Notifications

Keep the rest modular until independent deployment/scaling is justified.

### Stage 7 — Production platform

Add:

- Docker
- Kubernetes
- Terraform
- CI/CD
- OpenTelemetry
- SLOs
- canary/rollback

### Stage 8 — Failure engineering

Inject:

- DB latency
- broker outage
- duplicate events
- service timeout
- node failure
- overload

Design recovery rather than only happy paths.

### Stage 9 — Multi-region

Add:

- home-region routing
- active-passive first
- explicit RTO/RPO
- failover drill
- optional active-active comparison
- cost model

The final architecture is **not** a target to copy. Every box must answer:

> Why does this need to exist?

---

# Focused practice projects

Use smaller projects to isolate individual mechanisms while the capstone teaches evolution.

| Project | Primary learning goals |
| --- | --- |
| URL shortener | ID generation, cache, key-value access, capacity |
| Rate limiter | algorithms, Redis/atomicity, distributed state |
| Notification service | queue, retry, DLQ, idempotency |
| Chat | WebSocket, presence, ordering, fan-out, connection routing |
| News feed | fan-out write/read, cache, hot users, ranking |
| File storage | object storage, multipart upload, metadata, CDN |
| Search/autocomplete | inverted index, ranking, freshness, cache |
| E-commerce checkout | transactions, inventory, payment, saga, outbox |
| Payment service | idempotency, ledger/invariants, reconciliation |
| Kafka-like log | partitions, offsets, replication, retention |
| Multi-tenant SaaS | isolation, quotas, noisy neighbor, placement |
| Global key-value store | replication, quorum, consistency, partition behavior |

For each project answer:

1. What requirement causes the architecture?
2. What simpler design could work first?
3. What trade-off is accepted?
4. What new failure mode appears?
5. Would the same choice make sense at 1/1000 of the scale?

---

# Recommended learning plan

A standard route is approximately **40 weeks at 10–12 focused hours/week**.

Do not compress by removing experiments; compress reading before hands-on work.

## Phase map

| Weeks | Focus |
| ---: | --- |
| 1–6 | Layer 1 — Data and storage |
| 7–10 | Layer 2 — Runtime and code |
| 11–14 | Layer 3 — Networking and communication |
| 15–18 | Layer 4 — Codebase architecture |
| 19–24 | Layer 5 — Messaging and integration |
| 25–30 | Layer 6 — Infrastructure and platform |
| 31–35 | Layer 7 — Distributed systems |
| 36–38 | Layer 8 — Geo-distributed systems |
| 39–40 | Layer 9 — Architecture synthesis and defense |

Suggested weekly split:

```text
30% reading / primary documentation
50% implementation + experiments
10% failure injection / measurement
10% architecture notes / interview defense
```

## Priority path

If time is limited, complete **MUST** topics first.

### MUST core

- relational data, indexes, transactions
- caching
- replication and sharding
- object storage basics
- runtime/concurrency/pools
- HTTP/REST
- WebSocket basics
- timeout/retry/idempotency
- modular monolith and boundaries
- queue/Kafka/RabbitMQ concepts
- outbox and saga
- Docker/load balancing
- deployment strategies
- distributed failure/CAP
- resilience/rate limiting/backpressure
- multi-region fundamentals and DR
- requirements/capacity/failure/cost reasoning

### SHOULD next

- WAL/recovery
- distributed SQL
- distributed IDs
- search internals
- gRPC/GraphQL/SSE
- Clean/Hexagonal/DDD
- CDC/schema evolution/CQRS
- Kubernetes/IaC/OTel/SLO
- consensus/Raft
- locks/fencing/logical clocks
- geo partitioning
- ADRs/Strangler

### ADVANCED specialization

- Event Sourcing
- stream processing/Flink
- Paxos details
- service mesh
- active-active multi-writer
- causal consistency details
- advanced distributed transaction systems

---

# System design interview workflow

Use the same process in every design.

## Step 1 — Clarify requirements

Ask:

- Who uses the system?
- What are the critical features?
- What is out of scope?
- Is real-time behavior required?
- Which invariants cannot be violated?

## Step 2 — Define non-functional requirements

Clarify:

- latency
- availability
- durability
- consistency
- geography
- compliance
- cost

## Step 3 — Estimate scale

Estimate:

- users
- RPS
- peak multiplier
- read/write ratio
- object/message sizes
- storage growth
- bandwidth
- retention

## Step 4 — Define core APIs

Only important interfaces.

## Step 5 — Define entities, invariants, and access patterns

Do not choose a database before understanding the data behavior.

## Step 6 — Draw the simplest viable architecture

Start with:

```text
Client → Application → Database
```

## Step 7 — Identify the first bottlenecks

Ask what fails first under the stated scale.

## Step 8 — Scale selectively

Potential tools:

- cache
- CDN
- replicas
- partitioning
- queues/streams
- search index
- more application instances

Do not add all by default.

## Step 9 — Analyze failure

Discuss:

- slow dependency
- node failure
- DB failover
- duplicate request/event
- queue backlog
- cache outage
- network partition
- region outage

## Step 10 — Discuss consistency and recovery

Define actual guarantees, not vague “eventual consistency.”

## Step 11 — Cover security and observability

Include authz, abuse protection, telemetry, SLOs, alerting, and repair paths.

## Step 12 — Cover cost and evolution

Ask:

- Which component is most expensive?
- Which complexity can be deferred?
- How does the next architecture evolve without a rewrite?

A strong answer should be able to say both:

> We need component X because of requirement Y.

and:

> We do **not** need component Z yet.

---

# Mastery checklist

## Layer 1 — Data

- [ ] I can model relational data from invariants and access patterns.
- [ ] I can explain index trade-offs and query plans.
- [ ] I understand ACID, MVCC, locking, and common isolation anomalies.
- [ ] I understand WAL/recovery at a practical level.
- [ ] I can explain caching failure modes.
- [ ] I understand replication vs partitioning/sharding.
- [ ] I can select and defend a shard key.
- [ ] I understand SQL vs major NoSQL models.
- [ ] I understand distributed IDs, search indexes, object storage, and OLTP/OLAP.

## Layer 2 — Runtime

- [ ] I can explain process/thread/concurrency basics.
- [ ] I can distinguish sync/async from blocking/non-blocking.
- [ ] I understand thread-pool and connection-pool saturation.
- [ ] I understand transaction boundaries.
- [ ] I can design idempotent operations.
- [ ] I know when work should become a background job.

## Layer 3 — Networking

- [ ] I understand DNS, TCP, TLS, HTTP, and proxies practically.
- [ ] I understand HTTP/1.1 vs HTTP/2 vs HTTP/3 conceptually.
- [ ] I can design REST APIs.
- [ ] I can choose REST, gRPC, WebSocket, SSE, polling, GraphQL, or webhook appropriately.
- [ ] I use explicit timeouts/deadlines.
- [ ] I understand retry amplification and jitter.

## Layer 4 — Codebase architecture

- [ ] I can explain monolith vs modular monolith.
- [ ] I understand Clean/Hexagonal principles.
- [ ] I understand DDD bounded contexts.
- [ ] I can define module/data ownership boundaries.
- [ ] I know why bounded context does not automatically mean microservice.
- [ ] I understand major multi-tenancy isolation models.
- [ ] I can enforce boundaries with tests/rules.

## Layer 5 — Messaging

- [ ] I can explain task queue vs event stream.
- [ ] I understand RabbitMQ and Kafka fundamentals.
- [ ] I understand delivery, ordering, duplicate, replay, and consumer lag.
- [ ] I understand retries/DLQ recovery.
- [ ] I understand transactional outbox.
- [ ] I understand CDC and schema evolution.
- [ ] I understand saga.
- [ ] I can distinguish CQRS from event sourcing.
- [ ] I understand when stream processing is justified.

## Layer 6 — Platform

- [ ] I understand reverse proxies/load balancing.
- [ ] I can containerize and horizontally scale an application.
- [ ] I understand Kubernetes basics and probes.
- [ ] I understand IaC/state/drift.
- [ ] I understand CI/CD.
- [ ] I understand DB expand/migrate/contract deployment.
- [ ] I can compare rolling, blue/green, and canary.
- [ ] I can trace a request with logs/metrics/traces.
- [ ] I can define SLIs/SLOs.
- [ ] I consider capacity and cost.

## Layer 7 — Distributed systems

- [ ] I expect partial failure.
- [ ] I understand CAP/PACELC.
- [ ] I can distinguish common consistency models.
- [ ] I understand replication/quorum/failover.
- [ ] I know why consensus exists and understand Raft conceptually.
- [ ] I understand distributed-lock/lease/fencing risks.
- [ ] I understand clock/ordering problems.
- [ ] I understand distributed transaction alternatives.
- [ ] I understand circuit breakers, bulkheads, rate limiting, backpressure, and load shedding.

## Layer 8 — Geo

- [ ] I understand multi-AZ vs multi-region.
- [ ] I understand sync vs async cross-region replication.
- [ ] I can compare active-passive and active-active.
- [ ] I understand home-region/geo-partitioning strategies.
- [ ] I understand cross-region quorum latency.
- [ ] I understand regional tenancy/data residency.
- [ ] I can define RTO/RPO and a DR procedure.
- [ ] I can explain multi-region cost.

## Layer 9 — Architecture

- [ ] I clarify functional and non-functional requirements first.
- [ ] I estimate scale only enough to drive decisions.
- [ ] I identify invariants and access patterns.
- [ ] I start with the simplest viable design.
- [ ] I identify bottlenecks before adding complexity.
- [ ] I analyze failure/consistency/recovery.
- [ ] I include security, observability, and cost.
- [ ] I can justify service boundaries.
- [ ] I can propose incremental architecture evolution.
- [ ] I can explain which components are unnecessary.

---

# Reading and course map

Prefer **primary documentation + experiments**, then books/papers, then interview summaries.

## Core books

- **Designing Data-Intensive Applications, Second Edition** — data-system trade-offs and distributed-system fundamentals
- **Database Internals** — storage engines, indexes, transactions, recovery, distributed internals
- **Domain-Driven Design** — strategic domain modeling and bounded contexts
- **Building Microservices, Second Edition** — service boundaries and incremental decomposition
- **Continuous Delivery** — deployment pipeline and release engineering
- **Site Reliability Engineering** — production reliability and operations

## Primary technical references

Networking:

- HTTP semantics/caching/HTTP2/HTTP3/QUIC RFCs
- TLS 1.3 RFC

Data:

- PostgreSQL documentation
- MySQL/InnoDB documentation
- MongoDB documentation
- CockroachDB documentation
- Elasticsearch/Lucene documentation

Messaging and streaming:

- Apache Kafka documentation
- RabbitMQ documentation
- Debezium documentation
- Apache Avro / Protocol Buffers documentation
- Apache Flink documentation

Platform:

- Kubernetes documentation
- Terraform documentation
- OpenTelemetry documentation
- Istio documentation

Security:

- OWASP Web Top 10
- OWASP API Security Top 10

## Distributed-systems papers

At minimum, study:

- Bigtable
- Dynamo
- Spanner
- Raft
- CAP (Gilbert/Lynch)
- PACELC
- COPS
- Calvin as an advanced transaction-design comparison

For each paper answer:

1. What workload/problem motivated it?
2. What failure model does it assume?
3. Where is ordering established?
4. What consistency/availability trade-off is made?
5. What new complexity does the design introduce?

## Courses

- **MIT 6.5840 Distributed Systems**
- **CMU 15-445/645 Database Systems**
- **Stanford CS244B Distributed Systems** as an additional option

---

# Final mental model

System design is not:

```text
Redis
Kafka
Kubernetes
Microservices
```

System design is:

```text
Requirements
    ↓
Invariants
    ↓
Traffic and scale
    ↓
API / communication model
    ↓
Runtime behavior
    ↓
Data model and storage
    ↓
Code/domain boundaries
    ↓
Async integration where justified
    ↓
Infrastructure and deployment
    ↓
Distributed guarantees
    ↓
Geo placement only when required
    ↓
Failure + recovery
    ↓
Security + observability + performance + cost
    ↓
Evolution and trade-offs
```

The decisive skill is not knowing every technology. It is knowing:

- what problem a mechanism solves
- how it behaves under failure
- how to measure whether it helps
- what complexity it introduces
- when a simpler design is better
- how to evolve the architecture without rewriting everything

> **Architecture quality is the quality of the trade-offs you can explain, test, operate, and reverse.**
