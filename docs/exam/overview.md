# 시험 정보

## 대상 역할

AI-200 candidate는 Azure AI solution의 요구 사항, 설계, 개발, 배포, 보안, monitoring에 참여하는 backend developer입니다. Azure SDK, Python, data service, messaging, vector database, container 경험이 전제됩니다.

## Skills measured

| Domain | 비중 | 공식 범위 요약 |
| --- | ---: | --- |
| Develop containerized solutions on Azure | 20–25% | ACR, ACR Tasks, App Service, Container Apps, KEDA, AKS |
| Develop AI solutions by using Azure data management services | 25–30% | Cosmos DB, PostgreSQL, pgvector, Azure Managed Redis, vector search |
| Connect to and consume Azure services | 20–25% | Service Bus, Event Grid, Azure Functions |
| Secure, monitor, and troubleshoot Azure solutions | 20–25% | Key Vault, App Configuration, OpenTelemetry, KQL |

## 시험 접근법

- 서비스 이름보다 **요구 사항과 trade-off**를 먼저 읽습니다.
- message와 event, cache와 source of truth, container hosting과 orchestration을 구분합니다.
- 배포만 보지 말고 identity, secret, retry, dead-letter, trace까지 lifecycle로 판단합니다.
- 코드 문제는 client 생성, authentication, async 처리, error handling의 책임 경계를 확인합니다.

!!! warning "이 자료의 한계"
    하루 개요만으로 SDK syntax와 운영 troubleshooting 숙련까지 얻을 수는 없습니다. 각 domain에서 최소 한 번은 Azure 환경에 직접 배포하고 log를 확인해야 합니다.

!!! info "공식 기준"
    이 페이지는 [AI-200 official study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-200)의 현재 skills measured를 요약합니다.