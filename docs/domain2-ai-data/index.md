# Domain 2: AI data management

**시험 비중: 25–30%**

가장 비중이 큰 domain입니다. CRUD보다 data model, index, consistency, throughput, vector search가 AI workload에 미치는 영향을 이해해야 합니다.

## 서비스 관점

| 서비스 | 강점 | 시험에서 볼 지점 |
| --- | --- | --- |
| Azure Cosmos DB for NoSQL | 분산 JSON operational data | SDK query, RU, consistency, index, vector, change feed |
| Azure Database for PostgreSQL | relational model과 SQL, pgvector | schema, index, connection, vector search, metadata filter |
| Azure Managed Redis | low-latency cache와 in-memory retrieval | expiration, invalidation, vector index |

!!! warning "Redis의 역할"
    Cache hit가 빨라도 source of truth와 invalidation 전략이 없으면 stale response를 만들 수 있습니다.

!!! info "공식 자료"
    [Azure Cosmos DB documentation](https://learn.microsoft.com/en-us/azure/cosmos-db/), [Azure Database for PostgreSQL documentation](https://learn.microsoft.com/en-us/azure/postgresql/), [Azure Managed Redis documentation](https://learn.microsoft.com/en-us/azure/redis/)