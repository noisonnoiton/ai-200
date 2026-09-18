# AI-200: Developing AI Cloud Solutions on Azure

AI-200은 Azure에서 **AI backend solution을 개발하고 운영하는 능력**을 다룹니다. 모델 사용법만 묻는 시험이 아니라 container 배포, vector data, messaging, serverless, secret 관리, distributed tracing까지 하나의 production system으로 연결해야 합니다.

이 사이트는 방대한 전체 문서를 대체하지 않습니다. **AI-200의 전체 지형과 Azure service 선택 기준을 빠르게 정리하는 것**을 목표로 합니다.

## 무엇을 배우는가

| 영역 | 비중 | 핵심 질문 |
| --- | ---: | --- |
| Containerized solutions | 20–25% | Image를 어디에 저장하고 어떤 compute에 배포할 것인가? |
| AI data services | 25–30% | Vector와 operational data를 어떤 저장소에서 어떻게 검색할 것인가? |
| Azure service integration | 20–25% | 장시간 AI 작업을 message와 event로 어떻게 분리할 것인가? |
| Security and operations | 20–25% | Secret을 노출하지 않고 end-to-end failure를 어떻게 추적할 것인가? |

## 권장 학습 순서

1. [시험 정보](exam/overview.md)에서 공식 범위와 요구 수준을 확인합니다.
2. [End-to-end architecture](architecture/end-to-end.md)에서 서비스가 연결되는 이유를 봅니다.
3. 각 domain의 선택 기준과 구현 checklist를 복습합니다.

!!! note "범위"
    이 자료는 overview 중심의 요약입니다. 실제 시험 준비에는 Azure SDK를 사용한 Python 구현, container 배포, KQL 작성 실습이 추가로 필요합니다.

!!! info "공식 자료"
    [Microsoft Learn - Azure AI Cloud Developer Associate](https://learn.microsoft.com/en-us/credentials/certifications/azure-ai-cloud-developer-associate/), [Microsoft Learn - AI-200 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-200)