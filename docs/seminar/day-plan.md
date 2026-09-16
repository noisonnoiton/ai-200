# 하루 세미나

서비스를 개별 암기하기보다 하나의 AI request가 build, data retrieval, asynchronous processing, operations를 통과하는 흐름으로 학습합니다.

## 일정

| 시간 | 주제 | 결과물 |
| --- | --- | --- |
| 09:30–10:00 | AI-200 landscape | 네 domain과 end-to-end architecture 이해 |
| 10:00–11:10 | Container platform | App Service, Container Apps, AKS 선택표 |
| 11:20–12:30 | AI data | Cosmos DB, PostgreSQL, Redis 비교와 RAG 흐름 |
| 13:30–14:30 | Messaging과 events | Service Bus, Event Grid 선택 및 failure handling |
| 14:40–15:30 | Azure Functions | Trigger, binding, serverless API 설계 |
| 15:40–16:40 | Security와 monitoring | Key Vault, App Configuration, OpenTelemetry, KQL |
| 16:40–17:30 | Architecture exercise | 요구 사항에서 Azure service 조합 도출 |

## 세미나 실습 시나리오

고객 문서를 받아 embedding을 만들고 관련 문서를 검색해 답변하는 API를 설계합니다.

- API와 worker는 container image로 배포합니다.
- 긴 ingestion 작업은 비동기로 분리합니다.
- vector와 metadata를 저장하고 filter를 적용합니다.
- secret은 application image나 environment file에 넣지 않습니다.
- 한 request가 API, queue, worker, database를 거치는 과정을 추적합니다.

## 완료 기준

세미나가 끝나면 다음 질문에 답할 수 있어야 합니다.

- Container Apps와 AKS 중 어느 쪽이 요구 사항에 맞는가?
- Service Bus queue와 Event Grid event의 의미 차이는 무엇인가?
- Cosmos DB, PostgreSQL, Redis의 vector workload 역할은 어떻게 다른가?
- Key Vault와 App Configuration에는 각각 무엇을 두는가?
- 실패한 request의 trace와 관련 log를 어떻게 찾는가?