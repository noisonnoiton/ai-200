# End-to-end AI backend architecture

AI-200의 서비스는 서로 독립된 목록이 아닙니다. 아래 reference flow는 네 domain을 하나의 production AI application으로 연결합니다.

!!! tip "Interactive architecture"
    [전체 화면에서 AI-200 architecture 탐색하기](ai-200-end-to-end.html){ target="_blank" }

  Theme 전환, zoom, component 검색, 관계 추적, presentation mode와 image export를 사용할 수 있습니다.

```plantuml
@startuml AI200_Reference_Architecture
skinparam defaultFontName Sans-Serif
skinparam defaultFontSize 14
skinparam roundcorner 12
skinparam shadowing false
skinparam linetype ortho
skinparam ArrowColor #64748B
skinparam ArrowFontColor #475569
skinparam ArrowThickness 1.5
skinparam packageStyle rectangle
skinparam package {
  BorderColor #CBD5E1
  FontColor #334155
  BackgroundColor #F8FAFC
}
skinparam rectangle {
  BorderColor #0078D4
  FontColor #0F172A
}
skinparam database {
  BorderColor #0F6CBD
  FontColor #0F172A
}

actor "Client" as Client

package "① Application runtime" {
  rectangle "API\nApp Service / Container Apps" as API #E6F4FF
  queue "Service Bus\ndurable work" as Bus #E0F2FE
  rectangle "Worker\nContainer Apps / Functions" as Worker #DFF7F4
}

package "② Data & AI" {
  database "Operational + vector data\nCosmos DB / PostgreSQL" as DB #E8F0FE
  database "Low-latency cache\nAzure Managed Redis" as Redis #FFF4CE
  rectangle "AI model\ngrounded generation" as Model #F3E8FF
}

package "③ Platform controls" {
  rectangle "Key Vault\nsecrets" as KV #FDE7E9
  rectangle "App Configuration\nruntime settings" as Config #FDE7E9
  cloud "Azure Monitor\nOpenTelemetry + KQL" as Monitor #E8EAED
}

Client --> API : 1. request
API --> Bus : 2. enqueue
Bus --> Worker : 3. deliver
Worker --> DB : 4. retrieve context
Worker --> Redis : cache
Worker --> Model : 5. grounded prompt
Model --> Worker : 6. response
Worker --> API : 7. result

KV ..> API : secret
KV ..> Worker
Config ..> API : setting
API ..> Monitor : telemetry
Bus ..> Monitor
Worker ..> Monitor
DB ..> Monitor
@enduml
```

## 설계 질문

| 요구 사항 | 먼저 볼 서비스 또는 기능 |
| --- | --- |
| image version과 build 자동화 | Azure Container Registry, ACR Tasks |
| HTTP traffic 기반 간단한 container hosting | App Service 또는 Container Apps |
| event-driven worker scale | Container Apps의 KEDA |
| Kubernetes API와 cluster-level control | AKS |
| durable command와 consumer processing | Service Bus |
| 상태 변화의 fan-out notification | Event Grid |
| semantic retrieval | Cosmos DB vector, PostgreSQL pgvector, Redis vector index |
| secret rotation | Key Vault |
| runtime feature setting | App Configuration |
| component 간 latency 추적 | OpenTelemetry와 Application Insights |

!!! tip "시험 문제 읽기"
    먼저 durability, ordering, latency, scale, operational control, consistency 요구를 표시한 뒤 서비스를 선택합니다.