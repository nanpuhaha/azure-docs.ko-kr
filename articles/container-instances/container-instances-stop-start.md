---
title: 컨테이너 그룹을 수동으로 중지하거나 시작
description: Azure 컨테이너 인스턴스에서 컨테이너 그룹을 수동으로 중지하거나 시작하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533417"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Azure 컨테이너 인스턴스에서 컨테이너를 수동으로 중지하거나 시작합니다.

컨테이너 그룹의 [다시 시작 정책](container-instances-restart-policy.md) 설정은 컨테이너 인스턴스가 기본적으로 시작 또는 중지하는 방법을 결정합니다. 컨테이너 그룹을 수동으로 중지하거나 시작하여 기본 설정을 재정의할 수 있습니다.

## <a name="stop"></a>중지

az 컨테이너 [중지][az-container-stop] 명령 또는 Azure 포털을 사용하여 실행 중인 컨테이너 그룹을 수동으로 중지합니다. 특정 컨테이너 워크로드의 경우 비용을 절감하기 위해 정의된 기간 이후에 장기 실행 컨테이너 그룹을 중지할 수 있습니다. 

*컨테이너 그룹이 중지됨 상태로 들어가면 그룹의 모든 컨테이너가 종료되고 재활용됩니다. 컨테이너 상태를 유지 하지 않습니다.*

컨테이너가 재활용되면 [리소스가](container-instances-container-groups.md#resource-allocation) 할당 할당되고 컨테이너 그룹에 대한 청구가 중지됩니다.

컨테이너 그룹이 이미 종료된 경우(성공 또는 실패 상태)는 중지 작업이 적용되지 않습니다. 예를 들어 성공한 상태에서 성공적으로 실행된 컨테이너 작업이 있는 컨테이너 그룹은 종료됩니다. 해당 상태에서 그룹을 중지하려고 하면 상태가 변경되지 않습니다. 

## <a name="start"></a>시작

컨테이너 그룹이 중지되면 컨테이너가 자체적으로 종료되었거나 수동으로 그룹을 중지했기 때문에 컨테이너를 시작할 수 있습니다. 예를 들어 [az 컨테이너 시작][az-container-start] 명령 또는 Azure 포털을 사용하여 그룹에서 컨테이너를 수동으로 시작합니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 시작하면 동일한 컨테이너 구성을 가진 새 배포가 시작됩니다. 이 작업은 예상대로 작동하는 알려진 컨테이너 그룹 구성을 빠르게 다시 사용하는 데 도움이 될 수 있습니다. 동일한 워크로드를 실행하기 위해 새 컨테이너 그룹을 만들 필요가 없습니다.

컨테이너 그룹의 모든 컨테이너는 이 작업으로 시작됩니다. 그룹에서 특정 컨테이너를 시작할 수 없습니다.

컨테이너 그룹을 수동으로 시작 또는 다시 시작한 후에 컨테이너 그룹은 구성된 다시 시작 정책에 따라 실행됩니다.
  
## <a name="restart"></a>다시 시작

az 컨테이너 [다시][az-container-restart] 시작 명령을 사용하여 컨테이너 그룹을 실행하는 동안 다시 시작할 수 있습니다. 이 작업은 컨테이너 그룹의 모든 컨테이너를 다시 시작합니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 다시 시작하면 배포 문제를 해결하려는 경우 도움이 됩니다. 예를 들어 임시 리소스 제한 사항으로 인해 컨테이너가 성공적으로 실행되지 않는 경우 그룹을 다시 시작하면 문제가 해결될 수 있습니다.

컨테이너 그룹의 모든 컨테이너는 이 작업으로 다시 시작됩니다. 그룹에서 특정 컨테이너를 다시 시작할 수 없습니다.

컨테이너 그룹을 수동으로 다시 시작하면 구성된 다시 시작 정책에 따라 컨테이너 그룹이 실행됩니다.

## <a name="next-steps"></a>다음 단계

Azure 컨테이너 인스턴스에서 [정책 다시 시작 설정에](container-instances-restart-policy.md) 대해 자세히 알아보세요.

기존 구성으로 컨테이너 그룹을 수동으로 중지하고 시작하는 것 외에도 실행 중인 컨테이너 그룹의 [설정을 업데이트할](container-instances-update.md) 수 있습니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
