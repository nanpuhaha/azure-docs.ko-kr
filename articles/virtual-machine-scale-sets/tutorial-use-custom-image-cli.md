---
title: 자습서 - Azure CLI를 사용하여 확장 집합에서 사용자 지정 VM 이미지 사용
description: Azure CLI를 사용하여 가상 머신 확장 집합을 배포하는 데 사용할 수 있는 사용자 지정 VM 이미지를 만드는 방법을 알아봅니다.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6d9f625bf425a33b690fd303a4f13d032bd59fa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80062709"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>자습서: Azure CLI를 사용하여 가상 머신 확장 집합에 대한 사용자 지정 이미지 만들기 및 사용
확장 집합을 만들 때 VM 인스턴스 배포 시 사용할 이미지를 지정합니다. VM 인스턴스가 배포된 후 작업의 수를 줄이려면 사용자 지정 VM 이미지를 사용할 수 있습니다. 이 사용자 지정 VM 이미지에는 필요한 모든 애플리케이션 설치 또는 구성이 포함됩니다. 확장 집합에서 만들어진 모든 VM 인스턴스는 사용자 지정 VM 이미지를 사용하며, 애플리케이션 트래픽을 처리할 준비가 되어 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 만들기 및 사용자 지정
> * VM 프로비전 해제 및 일반화
> * 사용자 지정 VM 이미지 만들기
> * 사용자 지정 VM 이미지를 사용하는 확장 집합 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.29 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.


## <a name="create-and-configure-a-source-vm"></a>원본 VM 만들기 및 구성

>[!NOTE]
> 이 자습서는 일반화된 VM 이미지를 만들고 사용하는 과정을 안내합니다. 특수화된 VM 이미지로부터 확장 집합 만들기는 지원되지 않습니다.

먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만든 다음, [az vm create](/cli/azure/vm)를 사용하여 VM을 만듭니다. 이 VM은 사용자 지정 VM 이미지에 대한 원본으로 사용됩니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myVM*이라는 VM을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM의 공용 IP 주소는 [az vm create](/cli/azure/vm) 명령의 출력에 표시됩니다. 다음과 같이 SSH를 VM의 공용 IP 주소에 연결합니다.

```console
ssh azureuser@<publicIpAddress>
```

VM을 사용자 지정하기 위해 기본 웹 서버를 설치해 보겠습니다. 확장 집합의 VM 인스턴스가 배포되면 웹 애플리케이션을 실행하는 데 필요한 모든 패키지가 포함되어 있습니다. 다음과 같이 `apt-get`을 사용하여 *NGINX*를 설치합니다.

```bash
sudo apt-get install -y nginx
```

사용자 지정 이미지로 사용할 VM을 준비하는 마지막 단계는 VM 프로비전을 해제하는 것입니다. 이 단계에서는 VM에서 컴퓨터 관련 정보를 제거하고 단일 이미지에서 많은 VM을 배포할 수 있습니다. VM 프로비전이 해제되면 호스트 이름이 *localhost.localdomain*으로 다시 설정됩니다. SSH 호스트 키, 이름 서버 구성, 루트 암호 및 캐시된 DHCP 임대도 삭제됩니다.

VM 프로비전을 해제하려면 Azure VM 에이전트(*waagent*)를 사용합니다. Azure VM 에이전트는 모든 VM에 설치되며 Azure 플랫폼과 통신하는 데 사용됩니다. `-force` 매개 변수는 컴퓨터 관련 정보를 다시 설정하라는 메시지를 수락하도록 에이전트에 지시합니다.

```bash
sudo waagent -deprovision+user -force
```

VM에 대한 SSH 연결을 닫습니다.

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>원본 VM에서 사용자 지정 VM 이미지 만들기
이제 원본 VM이 Nginx 웹 서버가 설치된 상태로 사용자 지정됩니다. 확장 집합에 사용할 사용자 지정 VM 이미지를 만들어 보겠습니다.

이미지를 만들려면 VM을 할당 취소해야 합니다. [az vm deallocate](/cli//azure/vm)로 VM의 할당을 취소합니다. 그런 다음, Azure 플랫폼에서 VM이 사용자 지정 이미지를 사용할 준비가 되었음을 인식할 수 있도록 [az vm generalize](/cli//azure/vm)를 사용하여 VM을 일반화됨 상태로 설정합니다. 일반화된 VM에서만 이미지를 만들 수 있습니다.


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

VM을 할당 취소하고 일반화하는 데 몇 분이 걸릴 수 있습니다.

이제 [az image create](/cli//azure/image)를 사용하여 VM의 이미지를 만듭니다. 다음 예제에서는 VM에서 *myImage*라는 이미지를 만듭니다.

> [참고] 리소스 그룹과 Virtual Machine 위치가 다른 경우 아래 명령에 `--location` 매개 변수를 추가하여 이미지를 만드는 데 사용되는 원본 VM의 위치를 지정할 수 있습니다. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>사용자 지정 VM 이미지에서 확장 집합 만들기
[az vmss create](/cli/azure/vmss#az-vmss-create)를 사용하여 확장 집합을 만듭니다. *UbuntuLTS* 또는 *CentOS*와 같은 플랫폼 이미지 대신 사용자 지정 VM 이미지의 이름을 지정합니다. 다음 예제에서는 이전 단계의 *myImage* 사용자 지정 이미지를 사용하는 *myScaleSet*이라는 확장 집합을 만듭니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="test-your-scale-set"></a>확장 집합 테스트
트래픽이 확장 집합에 도달하고 웹 서버가 올바르게 작동하는지 확인하도록 허용하려면 [az network lb rule create](/cli/azure/network/lb/rule)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 *80* *TCP* 포트에 트래픽을 허용하는 *myLoadBalancerRuleWeb*이라는 규칙을 만듭니다.

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

작동 중인 확장 집합을 확인하려면 [az network public-ip show](/cli/azure/network/public-ip)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 확장 집합의 일부로 만든 *myScaleSetLBPublicIP*에 대한 IP 주소를 가져옵니다.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

웹 브라우저에 공용 IP 주소를 입력합니다. 기본 NGINX 웹 페이지가 다음 예제와 같이 표시됩니다.

![사용자 지정 VM 이미지에서 실행된 Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 추가 리소스를 제거하려면 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `--no-wait` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다. `--yes` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure CLI를 사용하여 확장 집합에 대한 사용자 지정 VM 이미지를 만들고 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * VM 만들기 및 사용자 지정
> * VM 프로비전 해제 및 일반화
> * 사용자 지정 VM 이미지 만들기
> * 사용자 지정 VM 이미지를 사용하는 확장 집합 배포

애플리케이션을 확장 집합에 배포하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합에 애플리케이션 배포](tutorial-install-apps-cli.md)
