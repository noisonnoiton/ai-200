# AB-730 Agent Lab

AB-730 Agent Lab은 Microsoft 365 Copilot 시험 문제를 탐색하고, Microsoft Learn 근거를 바탕으로 AI 답변을 생성하는 application입니다. Next.js application이 Azure Container Apps에서 실행되며 LangGraph workflow, Microsoft Learn MCP, Microsoft Foundry, managed identity, OpenTelemetry를 하나의 end-to-end AI solution으로 연결합니다.

## Application 개요

사용자는 문제 화면에서 **Ask AI**를 선택합니다. Client는 문제 본문이나 정답을 보내지 않고 question sequence만 API에 전달하며, server가 repository content에서 원본 문제와 image를 조회합니다. Image가 있는 문제는 image analysis가 화면의 제품명, 기능명, visible text를 Learn 검색어로 추출한 뒤 Microsoft Learn MCP에서 공식 근거를 검색하고, Foundry가 답변을 생성합니다.

최종 citation은 model이 임의로 만든 URL을 그대로 노출하지 않습니다. Agent가 실제 MCP 검색 결과와 URL을 대조하고 일치하는 Microsoft Learn 문서만 drawer에 표시합니다. 답변 기록은 server database가 아니라 browser local storage에 최대 20개까지 보관합니다.

| 관심 영역 | 이 lab에서 확인할 구현 |
| --- | --- |
| Container application | Next.js standalone image와 Azure Container Apps revision |
| Agent workflow | LangGraph node를 통한 vision, retrieval, generation, verification |
| Tool integration | Remote Microsoft Learn MCP의 `microsoft_docs_search` |
| Model inference | Microsoft Foundry `gpt-5.6-luna-01` structured output과 vision input |
| Identity | User-assigned managed identity와 `Cognitive Services OpenAI User` RBAC |
| Observability | OpenTelemetry, Application Insights, Log Analytics, structured event |
| Responsible AI | Citation allow-list, prompt/response diagnostic logging 제외 |

## Application 구조

아래 workflow는 요청이 처리되는 순서를 나타냅니다. Viewer에서 trace animation을 실행하면 question 조회, image analysis와 검색어 추출, Learn 검색, Foundry 답변 생성, citation 검증 순서가 표시됩니다.

[Application workflow 전체 화면으로 열기](ab730-agent-workflow.html){ target="_blank" }

<iframe class="archify-frame" src="../ab730-agent-workflow.html" title="AB-730 학습 Agent 실행 흐름"></iframe>

### 실행 흐름

1. Browser가 `/api/ai-answer`에 question sequence를 전송합니다.
2. Server가 신뢰할 수 있는 local content에서 문제와 image를 조회합니다.
3. Image가 있으면 `analyzeVisualContext` node가 Foundry vision input으로 visible text, product, feature, 선택지를 Learn 검색어로 추출합니다. Image가 없으면 model call 없이 빈 검색어 보강 값으로 통과합니다.
4. LangChain tool이 Microsoft Learn MCP의 `microsoft_docs_search`를 호출합니다.
5. Foundry가 문제, image, Learn evidence를 사용해 structured JSON 답변을 생성합니다.
6. LangGraph가 citation URL을 MCP 결과 allow-list와 대조합니다.
7. 검증된 답변과 citation을 drawer에 표시하고 browser local storage에 저장합니다.

Image 문제에서는 image analysis 호출이 한 번 추가되므로 latency와 token 사용량이 text-only 문제보다 증가할 수 있습니다.

## Deployment 구조

배포 구조는 repository의 Terraform과 GitHub Actions를 기준으로 합니다. GitHub Actions가 commit SHA image를 GHCR에 게시하고 Terraform이 Azure Container Apps revision과 supporting resource를 관리합니다.

[Azure deployment 구조 전체 화면으로 열기](ab730-azure-deployment.html){ target="_blank" }

<iframe class="archify-frame archify-frame--deployment" src="../ab730-azure-deployment.html" title="AB-730 Azure 배포 구조"></iframe>

### 배포와 runtime 경계

- GitHub Actions는 image build와 Terraform apply를 분리하고 Azure Blob의 remote state를 사용합니다.
- Azure Container App은 scale-to-zero가 가능한 Consumption environment에서 단일 active revision으로 실행됩니다.
- Auth.js는 GitHub OAuth sign-in callback 후 `repo` scope token으로 `ALLOWED_REPO` 접근을 확인합니다. GitHub OAuth는 Microsoft Entra ID provider로 대체할 수 있습니다.
- Private GHCR image는 Container App secret으로 pull하며 Foundry inference에는 API key를 사용하지 않습니다.
- GHCR은 Azure Container Registry로 대체할 수 있으며, 이 경우 Container App registry authentication과 image reference를 Azure Container Registry 기준으로 변경합니다.
- User-assigned managed identity가 `AZURE_CLIENT_ID`로 선택되고 Foundry resource의 `Cognitive Services OpenAI User` 역할을 사용합니다.
- Microsoft Learn MCP는 ACA가 HTTPS로 호출하는 external grounding service입니다.
- Application Insights는 `service.name=ab730` OpenTelemetry를 수집하고 ACA 및 Foundry diagnostic log는 Log Analytics workspace로 전송됩니다.
- Azure Monitor alert는 AI answer failure와 Container Apps system error를 5분 단위로 평가합니다.

## 다음 실습

[모니터링 실습](ab730-agent-monitoring.md)에서 agent source 검색, Foundry token과 latency, application failure, Container Apps HTTP 및 system log를 KQL로 확인합니다.