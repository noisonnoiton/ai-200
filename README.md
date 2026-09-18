# AI-200: Developing AI Cloud Solutions on Azure

AI-200 Azure AI Cloud Developer Associate를 위한 한국어 학습 자료입니다.

- 공개 문서: https://noisonnoiton.github.io/ai-200/
- 자격증 정보: https://learn.microsoft.com/en-us/credentials/certifications/azure-ai-cloud-developer-associate/
- 공식 study guide: https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-200

## Local preview

```bash
uv sync --extra docs
NO_MKDOCS_2_WARNING=1 uv run mkdocs serve
```

## Build

```bash
uv run mkdocs build --strict -f mkdocs.yml
```

## 범위

이 저장소는 AI-200 범위를 정리한 overview입니다. 실제 시험 준비에는 Python SDK, Azure resource 배포, vector query, messaging, OpenTelemetry, KQL 실습이 추가로 필요합니다. Exam dump나 실제 시험 문항을 포함하지 않습니다.