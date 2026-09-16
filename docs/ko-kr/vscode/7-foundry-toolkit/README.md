---
slug: ko-kr/vscode/7-foundry-toolkit
title: "선택 사항: Foundry 통합"
description: "VS Code와 Microsoft Foundry Toolkit으로 세 가지 집중 모듈에 걸쳐 카탈로그에 근거한 Backer Concierge를 만듭니다."
authors:
  - juliamuiruri4
lastUpdated: 2026-09-16
---

| [← 이전 실습: GitHub Copilot의 작업 반복 개선][previous-lesson] |
|:--|

필수 VS Code 과정은 실습 6에서 완료됩니다. 이 선택 확장 과정에서는 VS Code의 GitHub Copilot Chat과 Microsoft Foundry Toolkit을 사용하여 Tailspin 카탈로그를 기반으로 Backer Concierge를 만들고, 호스팅 에이전트(Hosted agent)로 배포한 뒤 로컬 프록시(Proxy)를 통해 사이트에 연결합니다.

## 시나리오

후원자는 필터만으로는 답할 수 없는 질문을 합니다. git 말장난을 좋아하는 사람에게 어떤 게임이 어울리는지, 특정 퍼즐 게임이 다른 게임보다 더 잘 맞는 이유는 무엇인지 궁금해합니다. Tailspin Toys에는 실제 카탈로그의 게임을 추천하고, 필요할 때 명확히 하기 위한 질문을 하며, 모금액이나 기타 정보를 알 수 없을 때 이를 솔직히 인정하여 신뢰를 얻는 안내 도우미가 필요합니다.

## 모듈

각 모듈은 작동하는 결과물을 확인하는 것으로 마무리합니다. 세 모듈 모두 동일한 학습자 리포지토리, 기능 브랜치, Foundry 프로젝트를 사용하며, 모듈 사이에 프로젝트를 다시 만들지 않습니다.

| 모듈 | 완료 점검 항목 |
|--------|-----------------------|
| [1. 프로젝트 및 모델 준비][module-1] | 카탈로그를 내보내고 그라운딩(Grounding) 규칙에 따라 배포한 모델 테스트 |
| [2. 에이전트 빌드 및 배포][module-2] | 로컬 에이전트 디버깅 및 호스팅 에이전트 테스트 |
| [3. 사이트에 에이전트 연결][module-3] | 로컬 프록시 및 접근성을 갖춘 위젯의 엔드투엔드 테스트 |

> [!IMPORTANT]
> Microsoft Foundry Toolkit과 호스팅 에이전트는 공개 미리 보기로 제공됩니다. 이 모듈에서는 모델 배포와 호스팅 에이전트를 비롯한 유료 Azure 리소스를 만듭니다. 구독 권한, 지역별 가용성, 할당량, 비용에 따라 참여가 제한될 수 있습니다.

## 시작 지점 선택

이 확장 과정은 워크숍 문서 리포지토리가 아니라 본인의 Tailspin Toys 리포지토리에서 진행합니다.

1. 선택 기능을 시작하기 전에 필수 워크숍 작업을 저장하고 커밋하여 푸시했는지 확인합니다.
2. 리소스 생성을 승인하기 전에 선택한 Azure 구독, 지역, 권한, 할당량, 예상 비용을 검토합니다.
3. [프로젝트 및 모델 준비][module-1]부터 시작합니다. 이어서 진행하는 경우 해당 모듈의 완료 점검 항목을 사용하여 기존 리포지토리, 브랜치, 프로젝트, 배포를 확인한 뒤 계속합니다.
4. 어느 모듈에서든 중단할 때는 다음 모듈을 위해 의도적으로 리소스를 유지하고 지속적인 비용을 부담하는 경우가 아니라면 [리소스 정리][cleanup]를 따릅니다.

## 리소스 정리

프로젝트와 모델만 만든 후 중단하더라도 리소스를 정리해야 합니다. 에이전트 스캐폴드(Scaffold)나 `azd` 프로젝트는 필요하지 않습니다.

> [!WARNING]
> 리소스 삭제는 되돌릴 수 없는 작업입니다. `azd down --purge`는 해당 배포에서 관리하는 Foundry 프로젝트, 모델 배포, 호스팅 에이전트를 영구적으로 제거할 수 있습니다. 리소스 그룹을 삭제하면 그룹의 모든 항목이 제거됩니다. 공유 리소스 그룹이나 다른 실습 또는 다른 사람이 소유한 리소스는 절대 삭제하지 않습니다.

1. 자격 증명과 로컬 환경 파일을 제외하고 보관할 코드를 Tailspin Toys 리포지토리에 저장합니다. 시작한 로컬 에이전트, Functions 호스트, 사이트 프로세스를 모두 중지합니다.
2. Azure에서 활성 구독을 확인하고 `rg-tailspin-toys`의 리소스를 검토합니다. 무엇이든 삭제하기 전에 어떤 프로젝트, 모델, 호스팅 에이전트, 지원 리소스가 이 선택 실습에 속하는지 확인합니다.
3. `azd`로 배포했다면 생성된 배포 디렉터리를 열고 선택한 `azd` 환경과 관리 대상 리소스를 확인합니다. 해당 리소스가 이 실습 전용인 경우에만 다음 명령을 실행하고 확인 메시지를 검토합니다. 이는 정리 방법 중 하나이며 다음 단계의 필수 조건이 아닙니다.

   ```bash
   azd down --purge
   ```

4. `azd` 배포 없이 프로젝트와 모델을 만들었거나 앞선 작업 후에도 리소스가 남아 있다면 Azure에서 본인이 소유한 리소스만 제거합니다. `rg-tailspin-toys` 그룹 전체가 이 실습 전용이고 Azure CLI가 확인한 구독을 대상으로 하는 경우에만 다음 명령을 개별 삭제 대신 사용할 수 있습니다. 이 명령은 확인 절차를 건너뛰고 삭제가 완료되기 전에 반환됩니다.

   ```bash
   az group delete --name rg-tailspin-toys --yes --no-wait
   ```

5. Azure에서 삭제가 완료되었으며 이 실습의 유료 리소스가 남아 있지 않은지 확인합니다. 권한 때문에 삭제할 수 없다면 구독 소유자에게 확인한 리소스를 제거해 달라고 요청합니다. 로컬 프로세스만 중지해서는 Azure 요금 청구가 멈추지 않습니다.

## 참고 자료

- [Visual Studio Code용 Foundry Toolkit][foundry-toolkit]
- [Microsoft Foundry 에이전트 확장 개요][foundry-extension]

| [다음 모듈: 프로젝트 및 모델 준비 →][module-1] |
|--:|

[previous-lesson]: ../6-iterating/
[module-1]: 1-project-and-model/
[module-2]: 2-build-and-deploy/
[module-3]: 3-connect-to-site/
[cleanup]: #리소스-정리
[foundry-toolkit]: https://code.visualstudio.com/docs/intelligentapps/overview
[foundry-extension]: https://learn.microsoft.com/azure/developer/azure-developer-cli/extensions/azure-ai-foundry-extension
