# Domain 1: Containerized solutions

**시험 비중: 20–25%**

이 domain은 image lifecycle과 Azure compute 선택을 다룹니다.

## 학습 목표

- ACR에서 image를 build, store, version, manage합니다.
- ACR Tasks로 image build와 run을 자동화합니다.
- App Service에 container와 environment configuration을 배포합니다.
- Container Apps의 environment, revision, KEDA scaling을 설명합니다.
- AKS manifest를 배포하고 log, event, connectivity를 진단합니다.

## 기억할 경계

| 구성 요소 | 책임 |
| --- | --- |
| Container image | 실행 파일, runtime, dependency를 immutable artifact로 묶음 |
| Azure Container Registry | image 저장, version, access, build task |
| Compute platform | image 실행, network, scale, revision 또는 deployment 관리 |
| Configuration | image 밖에서 environment별 값 제공 |

!!! info "공식 자료"
    [Azure Container Registry documentation](https://learn.microsoft.com/en-us/azure/container-registry/), [Azure Container Apps documentation](https://learn.microsoft.com/en-us/azure/container-apps/), [AKS documentation](https://learn.microsoft.com/en-us/azure/aks/)