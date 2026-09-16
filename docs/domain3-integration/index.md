# Domain 3: Connect and consume services

**시험 비중: 20–25%**

AI operation은 latency가 길거나 burst가 크고 외부 dependency 실패가 발생할 수 있습니다. Request path에서 긴 작업을 분리하고 delivery semantics를 명시해야 합니다.

## 핵심 구분

| 도구 | 의미 | 대표 사용 |
| --- | --- | --- |
| Service Bus queue | 처리해야 할 durable command | document ingestion job |
| Service Bus topic | 여러 subscriber가 받는 durable message | workflow별 후속 처리 |
| Event Grid | 상태 변화에 대한 event notification | blob 생성 후 처리 시작 |
| Azure Functions | trigger에 반응하는 serverless code | queue consumer, event handler, HTTP API |

!!! info "공식 자료"
    [Azure Service Bus documentation](https://learn.microsoft.com/en-us/azure/service-bus-messaging/), [Azure Event Grid documentation](https://learn.microsoft.com/en-us/azure/event-grid/), [Azure Functions documentation](https://learn.microsoft.com/en-us/azure/azure-functions/)