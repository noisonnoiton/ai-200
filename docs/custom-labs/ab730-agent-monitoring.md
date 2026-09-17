# AB-730 Agent 모니터링

이 실습은 Azure Container Apps에서 실행하는 AB-730 학습 agent를 대상으로 AI-200의 운영 영역을 확인합니다. agent는 Microsoft Learn MCP로 근거를 검색하고 Microsoft Foundry로 답변을 생성합니다. 목표는 단순히 답변이 생성되는지 확인하는 것이 아니라, 근거 검색, model 호출, token 사용량, 실패 원인을 observability data로 설명하는 것입니다.

## 구성과 개인정보 경계

| 구성 요소 | 역할 | 확인할 데이터 |
| --- | --- | --- |
| Azure Container Apps | Next.js API와 LangGraph workflow 실행 | revision, console log, HTTP log |
| Microsoft Learn MCP | 공식 문서 검색 | 검색 지연 시간, source 수 |
| Microsoft Foundry | 근거 기반 답변 생성 | model, input/output token, latency, failure |
| Application Insights | application request와 dependency telemetry | request failure, dependency latency |
| Log Analytics | platform log와 structured application event 조회 | agent 실행 event, system/HTTP log |

질문 본문, 생성 답변, API key, 사용자 identity는 console event와 Foundry diagnostic log에 기록하지 않습니다. Foundry `RequestResponse` diagnostic category도 비활성화합니다.

## 배포 상태 확인

GitHub Actions `deploy` workflow의 image build와 Terraform apply가 모두 성공한 뒤 새 revision을 확인합니다.

```bash
az containerapp revision list \
  --name ab730 \
  --resource-group rg-noisonnoiton-exams-ab730 \
  --output table
```

Container App environment는 `Azure Monitor`를 logging destination으로 사용해야 합니다. 이 설정과 diagnostic setting이 함께 있어야 새 resource-specific table로 log가 전송됩니다.

```bash
az containerapp env show \
  --name env-ab730 \
  --resource-group rg-noisonnoiton-exams-ab730 \
  --query properties.appLogsConfiguration.destination \
  --output tsv
```

출력은 `azure-monitor`여야 합니다. destination 또는 diagnostic setting을 변경한 뒤 Log Analytics ingestion이 시작되기까지 최대 90분 걸릴 수 있습니다.

## Agent 동작 확인

1. 배포된 site에서 GitHub OAuth로 로그인합니다.
2. 문제에서 **Ask AI**를 선택합니다.
3. Study agent drawer에 답, confidence, 설명, Microsoft Learn citation이 표시되는지 확인합니다.
4. citation이 `learn.microsoft.com` 공식 문서로 열리는지 확인합니다.
5. 새로고침 후 recent answer가 유지되는지, 20개를 초과하면 가장 오래된 기록이 제거되는지 확인합니다.

## Log Analytics 조회

Azure Monitor logging destination에서는 `ContainerAppConsoleLogs`, `ContainerAppSystemLogs`, `ContainerAppHTTPLogs` table을 사용합니다. 이전 destination의 `ContainerAppConsoleLogs_CL`, `ContainerAppSystemLogs_CL`는 새 log를 받지 않습니다.

### Microsoft Learn 근거 검색

```kusto
ContainerAppConsoleLogs
| where TimeGenerated > ago(1h)
| where Log has "agent.learn.search.completed"
| extend Event = parse_json(Log)
| extend DurationMs = toint(Event.durationMs), SourceCount = toint(Event.sourceCount)
| summarize Searches=count(), P50=percentile(DurationMs, 50), P95=percentile(DurationMs, 95), NoSource=countif(SourceCount == 0) by bin(TimeGenerated, 5m)
| order by TimeGenerated desc
```

`NoSource`가 반복되면 MCP search query 품질이나 outbound network를 확인합니다. citation은 실제 search 결과 URL만 허용하므로 source가 없으면 answer confidence는 `low`가 됩니다.

### Foundry answer 사용량과 latency

```kusto
ContainerAppConsoleLogs
| where TimeGenerated > ago(1h)
| where Log has "foundry.answer.completed"
| extend Event = parse_json(Log)
| extend DurationMs = toint(Event.durationMs), InputTokens = toint(Event.inputTokens), OutputTokens = toint(Event.outputTokens)
| summarize Requests=count(), InputTokens=sum(InputTokens), OutputTokens=sum(OutputTokens), P95=percentile(DurationMs, 95) by bin(TimeGenerated, 5m), tostring(Event.model)
| order by TimeGenerated desc
```

이 결과는 AI-200 관점에서 model deployment별 throughput, latency, token 기반 비용 추세를 설명하는 baseline입니다.

### 실패와 HTTP 오류

```kusto
ContainerAppConsoleLogs
| where TimeGenerated > ago(1h)
| where Log has "foundry.answer."
| project TimeGenerated, ContainerAppName, RevisionName, Log
| order by TimeGenerated desc
```

```kusto
ContainerAppHTTPLogs
| where TimeGenerated > ago(1h)
| where StatusCode >= 400
| project TimeGenerated, ContainerAppName, RevisionName, Method, Path, StatusCode, ResponseCodeDetails, RequestDuration, RequestId
| order by TimeGenerated desc
```

`foundry.answer.failed` event의 `errorType`, `status`, `code`, `message`만으로 identity, role assignment, quota, network 문제를 분류합니다. prompt와 answer 내용은 로그로 조사하지 않습니다.

## 운영 판단

| 신호 | 정상 기준 | 조사 방향 |
| --- | --- | --- |
| `agent.learn.search.completed` | source 수가 1 이상이고 P95가 안정적 | MCP outbound network, search query |
| `foundry.answer.completed` | failure 없이 token과 latency가 baseline 범위 | model deployment, quota, retry |
| `foundry.answer.failed` | 5분 동안 0건 | managed identity, Foundry RBAC, endpoint |
| `ContainerAppHTTPLogs` | 5xx 없음 | revision health, ingress, upstream timeout |
| Log Analytics ingestion | 최근 console event 존재 | `azure-monitor` destination, diagnostic setting, ingestion delay |

!!! info "AI-200 연결"
    이 실습은 workload identity, managed identity, OpenTelemetry, Log Analytics KQL, container deployment, model inference observability를 하나의 end-to-end 흐름으로 연결합니다.