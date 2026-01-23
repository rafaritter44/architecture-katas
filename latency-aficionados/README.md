# Latency Aficionados

## 1. Problem Statement and Context

Latency Aficionados is a retro video game marketplace where users can sell retro video games and can also buy such video games. The platform is capable of posting products, searching products, viewing product descriptions, rating products with reviews and comments, and also providing product recommendations to users based on previous browsing.

Our CTO, Mr. Fast, wants the smallest latencies possible. He cares deeply about how things render and how fast they render. Right now, the whole website is running on React 16. You need to find ways to speed up rendering and ensure rendering is fast at all times. Mr. Fast also has a monolith written in Java 1.4 that needs to be migrated to Java 25. You need to propose a decomposition of the monolith.

## 2. Goals

1. **Low latency UX**: Fast rendering and fast responses for all user-facing interactions.
2. **Strong correctness for money and inventory**: Buying and selling operations must be strongly consistent, transactional, and auditable, ensuring correctness under concurrency and partial failures.
3. **Clear service boundaries**: Each service owns its data and business rules, avoiding shared databases and tight coupling between components.
4. **Incremental monolith decomposition**: Gradually migrate from the existing Java 1.4 monolith to the new services.
5. **Simplicity over complexity**: Whenever possible, favor technologies and patterns that are easier to operate, debug, and reason about in production.

## 3. Non-Goals

1. **Single, shared database**: Services will not share databases or schemas, even if this would simplify some queries.
2. **Big-bang migration**: It's not our goal to migrate the code all at once.
3. **Over-engineering**: We should avoid premature optimization and operational complexity if not strictly necessary to meet our goals.

## 4. Principles

1. **KISS (Keep It Simple, Stupid)**: Strive for the simplest solution that meets the requirements.
2. **YAGNI (You Aren't Going to Need It)**: Don't add more components to the architecture, unless you have to.
3. **Modularity**: The system should be composed of well-defined, loosely coupled modules.
4. **Correctness**: The system should be correct, consistent, and free of bugs.

## 5. Overall Diagrams

### 5.1. Overall Architecture

```mermaid
---
config:
  flowchart:
    curve: linear
---
flowchart TD
    WA(Web App)

    AS[Auth Service]
    ADB[(Auth DB)]

    PS[Product Service]
    PDB[(Product DB)]

    OS[Order Service]
    ODB[(Order DB)]

    RS[Recommendation Service]
    RDB[(Recommendation DB)]

    PG[/Payment Gateway/]

    WA --> AS
    WA --> PS
    WA --> RS
    WA --> OS

    AS --> ADB
    PS --> PDB
    RS --> RDB
    OS --> ODB

    OS -->|Reserve/Release| PS
    OS -->|Charge/Refund| PG

    PDB -.->|Outbox Events| RDB
```

### 5.2. Deployment

```mermaid
architecture-beta
    group vpc(cloud)[VPC]
    group ecs(server)[ECS] in vpc

    service alb(server)[ALB] in vpc

    service as(server)[Auth Service] in ecs
    service adb(database)[Auth DB] in vpc

    service ps(server)[Product Service] in ecs
    service pdb(database)[Product DB] in vpc

    service os(server)[Order Service] in ecs
    service odb(database)[Order DB] in vpc

    service rs(server)[Recommendation Service] in ecs
    service rdb(database)[Recommendation DB] in vpc

    junction jCenter in ecs
    junction jRight in ecs

    alb:R -- L:jCenter
    as:B -- T:jCenter
    os:T -- B:jCenter
    jCenter:R -- L:jRight
    ps:B -- T:jRight
    rs:T -- B:jRight

    as:T --> B:adb
    ps:T --> B:pdb
    os:B --> T:odb
    rs:B --> T:rdb
```

### 5.3. Use Cases

TODO

## 6. Trade-offs

1. Postgres FTS vs. Elasticsearch
2. Logical Replication vs. Debezium

TODO

## 7. For each key major component

TODO

What is a majore component? A service, a lambda, a important ui, a generalized approach for all uis, a generazid approach for computing a workload, etc...
```
6.1 - Class Diagram              : classic uml diagram with attributes and methods
6.2 - Contract Documentation     : Operations, Inputs and Outputs
6.3 - Persistence Model          : Diagrams, Table structure, partiotioning, main queries.
6.4 - Algorithms/Data Structures : Spesific algos that need to be used, along size with spesific data structures.
```

Exemplos of other components: Batch jobs, Events, 3rd Party Integrations, Streaming, ML Models, ChatBots, etc... 

Recommended Reading: [Internal system design forgotten](http://diego-pacheco.blogspot.com/2018/05/internal-system-design-forgotten.html)

## 8. Migrations

TODO

IF Migrations are required describe the migrations strategy with proper diagrams, text and tradeoffs.

## 9. Testing Strategy

TODO

Explain the techniques, principles, types of tests and will be performaned, and spesific details how to mock data, stress test it, spesific chaos goals and assumptions.

## 10. Observability Strategy

TODO

Explain the techniques, principles,types of observability that will be used, key metrics, what would be logged and how to design proper dashboards and alerts.

## 11. Data Store Designs

TODO

For each different kind of data store i.e (Postgres, Memcached, Elasticache, S3, Neo4J etc...) describe the schemas, what would be stored there and why, main queries, expectations on performance. Diagrams are welcome but you really need some dictionaries.

Outbox Table
- Run deletes in batches
- Autovacuum tuned for this table
- Lower fillfactor

-- Local progress marker
recommendation_ingestion_state (
  last_processed_id BIGINT NOT NULL
);

## 12. Technology Stack

- Web App: TypeScript 5 + React 19
- Auth Service: Java 25 + Spring Boot 4
- Auth DB: Postgres 18
- Product Service: Java 25 + Spring Boot 4
- Product DB: Postgres 18
- Order Service: Java 25 + Spring Boot 4
- Order DB: Postgres 18
- Recommendation Service: Python 3 + FastAPI
- Recommendation DB: TimescaleDB
- Payment Gateway: Stripe

## 13. References

- [Postgres Full Text Search vs the rest](https://supabase.com/blog/postgres-full-text-search-vs-the-rest)
- [Postgres Full Text Search](https://gist.github.com/cpursley/e3586382c3a42c54ca7f5fef1665be7b)
- [Postgres Logical Replication](https://www.postgresql.org/docs/current/logical-replication.html)
- [TimescaleDB Jobs](https://www.tigerdata.com/docs/use-timescale/latest/jobs/create-and-manage-jobs)
- [TimescaleDB Retention Policy](https://www.tigerdata.com/docs/use-timescale/latest/data-retention/create-a-retention-policy)
- [TimescaleDB Continuous Aggregates](https://www.tigerdata.com/docs/use-timescale/latest/continuous-aggregates)
- [pg_cron](https://github.com/citusdata/pg_cron)