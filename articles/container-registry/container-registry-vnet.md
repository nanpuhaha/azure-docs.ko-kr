---
title: 가상 네트워크로 액세스 제한
description: Azure 가상 네트워크의 리소스 또는 공용 IP 주소 범위의 리소스에서만 Azure 컨테이너 레지스트리에 대한 액세스를 허용합니다.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454337"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure 가상 네트워크 또는 방화벽 규칙을 사용하여 Azure 컨테이너 레지스트리에 대한 액세스를 제한합니다.

[Azure 가상 네트워크는](../virtual-network/virtual-networks-overview.md) Azure 및 온-프레미스 리소스에 대한 안전한 개인 네트워킹을 제공합니다. Azure 가상 네트워크에서 개인 Azure 컨테이너 레지스트리에 대한 액세스를 제한하면 가상 네트워크의 리소스만 레지스트리에 액세스하도록 할 수 있습니다. 크로스-프레미스 시나리오의 경우 특정 IP 주소에서만 레지스트리 액세스를 허용하도록 방화벽 규칙을 구성할 수도 있습니다.

이 문서에서는 컨테이너 레지스트리에서 인바운드 네트워크 액세스 규칙을 구성하는 두 가지 시나리오를 보여 주며, 가상 네트워크에 배포된 가상 컴퓨터 또는 VM의 공용 IP 주소에서 인바운드 네트워크 액세스 규칙을 구성합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

대신 방화벽 뒤에서 컨테이너 레지스트리에 도달하기 위해 리소스에 대한 액세스 규칙을 설정해야 하는 경우 [방화벽 뒤에 있는 Azure 컨테이너 레지스트리에 액세스하는 규칙 구성을](container-registry-firewall-access-rules.md)참조하세요.


## <a name="preview-limitations"></a>미리 보기 제한 사항

* **프리미엄** 컨테이너 레지스트리만 네트워크 액세스 규칙으로 구성할 수 있습니다. 레지스트리 서비스 계층에 대한 자세한 내용은 [Azure 컨테이너 레지스트리 SCO를](container-registry-skus.md)참조하십시오. 

* Azure [Kubernetes 서비스](../aks/intro-kubernetes.md) 클러스터 또는 Azure [가상 컴퓨터만](../virtual-machines/linux/overview.md) 호스트로 사용하여 가상 네트워크의 컨테이너 레지스트리에 액세스할 수 있습니다. *Azure 컨테이너 인스턴스를 포함한 다른 Azure 서비스는 현재 지원되지 않습니다.*

* [ACR 작업](container-registry-tasks-overview.md) 작업은 현재 가상 네트워크에서 액세스하는 컨테이너 레지스트리에서 지원되지 않습니다.

* 각 레지스트리는 최대 100개의 가상 네트워크 규칙을 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서에서 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.0.58 이상이 필요합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하십시오.

* 컨테이너 레지스트리가 아직 없는 경우 하나를 만들고(프리미엄 SKU 필수) Docker Hub에서와 같은 `hello-world` 샘플 이미지를 푸시합니다. 예를 들어 [Azure 포털][quickstart-portal] 또는 [Azure CLI를][quickstart-cli] 사용하여 레지스트리를 만듭니다. 

* 다른 Azure 구독에서 가상 네트워크를 사용하여 레지스트리 액세스를 제한하려면 해당 구독에서 Azure 컨테이너 레지스트리에 대한 리소스 공급자를 등록해야 합니다. 예를 들어:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>컨테이너 레지스트리의 네트워크 규칙 정보

Azure 컨테이너 레지스트리는 기본적으로 모든 네트워크의 호스트에서 인터넷을 통해 연결을 허용합니다. 가상 네트워크를 사용하면 AKS 클러스터 또는 Azure VM과 같은 Azure 리소스만 네트워크 경계를 넘지 않고 레지스트리에 안전하게 액세스하도록 허용할 수 있습니다. 특정 공용 인터넷 IP 주소 범위만 허용하도록 네트워크 방화벽 규칙을 구성할 수도 있습니다. 

레지스트리에 대한 액세스를 제한하려면 먼저 레지스트리의 기본 작업을 변경하여 모든 네트워크 연결을 거부합니다. 그런 다음 네트워크 액세스 규칙을 추가합니다. 네트워크 규칙을 통해 액세스 권한을 부여받은 클라이언트는 [컨테이너 레지스트리에](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) 대한 인증을 계속하고 데이터에 액세스할 수 있는 권한이 있어야 합니다.

### <a name="service-endpoint-for-subnets"></a>서브넷에 대한 서비스 끝점

가상 네트워크의 서브넷에서 액세스를 허용하려면 Azure 컨테이너 레지스트리 서비스에 대한 [서비스 끝점을](../virtual-network/virtual-network-service-endpoints-overview.md) 추가해야 합니다. 

Azure 컨테이너 레지스트리와 같은 다중 테넌트 서비스는 모든 고객에 대해 단일 IP 주소 집합을 사용합니다. 서비스 끝점은 레지스트리에 액세스하기 위해 끝점을 할당합니다. 이 끝점은 트래픽을 Azure 백본 네트워크를 통해 리소스에 대한 최적의 경로를 제공합니다. 가상 네트워크 및 서브넷의 ID 또한 각 요청과 함께 전송됩니다.

### <a name="firewall-rules"></a>방화벽 규칙

IP 네트워크 규칙의 경우 CIDR 표기와 같은 CIDR 표기법 또는 *16.17.18.19와*같은 개별 IP 주소를 사용하여 허용된 인터넷 주소 범위를 제공합니다. *16.17.18.0/24* IP 네트워크 규칙은 *공용* 인터넷 IP 주소에만 허용됩니다. 사설망에 예약된 IP 주소 범위(RFC 1918에 정의된 대로)는 IP 규칙에서 허용되지 않습니다.

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker 지원 가상 컴퓨터 만들기

이 문서에서는 Docker 지원 우분투 VM을 사용하여 Azure 컨테이너 레지스트리에 액세스합니다. 레지스트리에 Azure Active Directory 인증을 사용하려면 VM에 [Azure CLI도][azure-cli] 설치합니다. Azure 가상 시스템이 이미 있는 경우 이 생성 단계를 건너뜁니다.

가상 컴퓨터 및 컨테이너 레지스트리에 동일한 리소스 그룹을 사용할 수 있습니다. 이 설정은 마지막에 정리를 단순화하지만 필요하지 않습니다. 가상 컴퓨터 및 가상 네트워크에 대해 별도의 리소스 그룹을 만들도록 선택한 경우 [az 그룹 만들기를 실행합니다.][az-group-create] 다음 예제에서는 *서쪽 중앙 위치에* *myResourceGroup이라는* 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

이제 [az vm create를][az-vm-create]사용하여 기본 우분투 Azure 가상 컴퓨터를 배포합니다. 다음 예제는 *myDockerVM이라는 VM을*만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM을 만드는 데 몇 분 정도 걸립니다. 명령이 완료되면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소를 사용하여 VM에 SSH 연결을 만들고 나중에 방화벽 규칙을 설정할 때 선택적으로 사용할 수 있습니다.

### <a name="install-docker-on-the-vm"></a>VM에 Docker 설치

VM이 실행된 후 VM에 SSH 연결을 만듭니다. *publicIpAddress*를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh azureuser@publicIpAddress
```

다음 명령을 실행하여 우분투 VM에 Docker를 설치합니다.

```bash
sudo apt install docker.io -y
```

설치 후 다음 명령을 실행하여 VM에서 Docker가 제대로 실행되는지 확인합니다.

```bash
sudo docker run -it hello-world
```

출력:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI 설치

[apt를 사용하여 Azure CLI 설치](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)의 단계를 따라 Ubuntu 가상 머신에 Azure CLI를 설치합니다. 이 문서의 경우 버전 2.0.58 이상에 설치해야 합니다.

SSH 연결을 종료합니다.

## <a name="allow-access-from-a-virtual-network"></a>가상 네트워크에서 액세스 허용

이 섹션에서는 Azure 가상 네트워크의 서브넷에서 액세스할 수 있도록 컨테이너 레지스트리를 구성합니다. Azure CLI 및 Azure 포털을 사용하는 동등한 단계가 제공됩니다.

### <a name="allow-access-from-a-virtual-network---cli"></a>가상 네트워크에서 액세스 허용 - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>서브넷에 서비스 끝점 추가

VM을 만들 때 Azure는 기본적으로 동일한 리소스 그룹에 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 시스템의 이름을 기반으로 합니다. 예를 들어 가상 *컴퓨터myDockerVM의*이름을 지정하면 기본 가상 네트워크 이름은 *myDockerVMSubnet이라는*서브넷이 있는 *myDockerVMVNET입니다.* Azure 포털에서 또는 [az 네트워크 vnet 목록][az-network-vnet-list] 명령을 사용하여 이 작업을 확인합니다.

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

출력:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

az [네트워크 vnet 서브넷 업데이트][az-network-vnet-subnet-update] 명령을 사용하여 **서브넷에 Microsoft.ContainerRegistry** 서비스 끝점을 추가합니다. 다음 명령에서 가상 네트워크 및 서브넷의 이름을 대체합니다.

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

az [네트워크 vnet 서브넷 표시][az-network-vnet-subnet-show] 명령을 사용하여 서브넷의 리소스 ID를 검색합니다. 네트워크 액세스 규칙을 구성하려면 이후 단계에서 이 단계가 필요합니다.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

출력:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>레지스트리에 대한 기본 네트워크 액세스 변경

기본적으로 Azure 컨테이너 레지스트리는 모든 네트워크의 호스트로부터의 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 기본 작업을 변경하여 액세스를 거부합니다. 다음 [az acr 업데이트][az-acr-update] 명령에서 레지스트리 이름을 대체합니다.

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

az [acr 네트워크 규칙 추가][az-acr-network-rule-add] 명령을 사용하여 VM의 서브넷에서 액세스할 수 있는 네트워크 규칙을 레지스트리에 추가합니다. 다음 명령에서 컨테이너 레지스트리 이름과 서브넷의 리소스 ID를 대체합니다. 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

레지스트리에 [대한 액세스 확인을](#verify-access-to-the-registry)계속합니다.

### <a name="allow-access-from-a-virtual-network---portal"></a>가상 네트워크에서 액세스 허용 - 포털

#### <a name="add-service-endpoint-to-subnet"></a>서브넷에 서비스 끝점 추가

VM을 만들 때 Azure는 기본적으로 동일한 리소스 그룹에 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 시스템의 이름을 기반으로 합니다. 예를 들어 가상 *컴퓨터myDockerVM의*이름을 지정하면 기본 가상 네트워크 이름은 *myDockerVMSubnet이라는*서브넷이 있는 *myDockerVMVNET입니다.*

Azure 컨테이너 레지스트리에 대한 서비스 끝점을 서브넷에 추가하려면 다음을 수행하십시오.

1. [Azure 포털][azure-portal]상단의 검색 상자에 가상 *네트워크를*입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
1. 가상 네트워크 목록에서 *myDockerVMVNET*과 같이 가상 시스템이 배포된 가상 네트워크를 선택합니다.
1. **설정에서** **서브넷을 선택합니다.**
1. *myDockerVMSubnet과*같이 가상 시스템이 배포되는 서브넷을 선택합니다.
1. **서비스 끝점에서** **Microsoft.Container레지스트리**를 선택합니다.
1. **저장**을 선택합니다.

![서브넷에 서비스 끝점 추가][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>레지스트리에 대한 네트워크 액세스 구성

기본적으로 Azure 컨테이너 레지스트리는 모든 네트워크의 호스트로부터의 연결을 허용합니다. 가상 네트워크에 대한 액세스를 제한하려면 다음을 수행하십시오.

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **설정에서** **방화벽 및 가상 네트워크를**선택합니다.
1. 기본적으로 액세스를 거부하려면 **선택한 네트워크**에서 액세스를 허용하도록 선택합니다. 
1. **기존 가상 네트워크 추가를**선택하고 서비스 끝점으로 구성한 가상 네트워크 및 서브넷을 선택합니다. **추가**를 선택합니다.
1. **저장**을 선택합니다.

![컨테이너 레지스트리를 위한 가상 네트워크 구성][acr-vnet-portal]

레지스트리에 [대한 액세스 확인을](#verify-access-to-the-registry)계속합니다.

## <a name="allow-access-from-an-ip-address"></a>IP 주소에서 액세스 허용

이 섹션에서는 특정 IP 주소 또는 범위에서 액세스할 수 있도록 컨테이너 레지스트리를 구성합니다. Azure CLI 및 Azure 포털을 사용하는 동등한 단계가 제공됩니다.

### <a name="allow-access-from-an-ip-address---cli"></a>IP 주소에서 액세스 허용 - CLI

#### <a name="change-default-network-access-to-registry"></a>레지스트리에 대한 기본 네트워크 액세스 변경

아직 수행하지 않은 경우 레지스트리 구성을 업데이트하여 기본적으로 액세스를 거부합니다. 다음 [az acr 업데이트][az-acr-update] 명령에서 레지스트리 이름을 대체합니다.

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>레지스트리에서 네트워크 규칙 제거

이전에 VM의 서브넷에서 액세스를 허용하는 네트워크 규칙을 추가한 경우 서브넷의 서비스 끝점과 네트워크 규칙을 제거합니다. [az acr 네트워크 규칙 제거][az-acr-network-rule-remove] 명령의 이전 단계에서 검색한 하위 네트워크의 이름과 하위 넷의 리소스 ID를 대체합니다. 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

az [acr 네트워크 규칙 추가][az-acr-network-rule-add] 명령을 사용하여 VM의 IP 주소에서 액세스할 수 있는 네트워크 규칙을 레지스트리에 추가합니다. 다음 명령에서 컨테이너 레지스트리 이름과 VM의 공용 IP 주소를 대체합니다.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

레지스트리에 [대한 액세스 확인을](#verify-access-to-the-registry)계속합니다.

### <a name="allow-access-from-an-ip-address---portal"></a>IP 주소에서 액세스 허용 - 포털

#### <a name="remove-existing-network-rule-from-registry"></a>레지스트리에서 기존 네트워크 규칙 제거

이전에 VM의 서브넷에서 액세스를 허용하는 네트워크 규칙을 추가한 경우 기존 규칙을 제거합니다. 다른 VM에서 레지스트리에 액세스하려는 경우 이 섹션을 건너뜁니다.

* 서브넷 설정을 업데이트하여 Azure 컨테이너 레지스트리에 대한 서브넷의 서비스 끝점을 제거합니다. 

  1. Azure [포털에서][azure-portal]가상 시스템이 배포된 가상 네트워크로 이동합니다.
  1. **설정에서** **서브넷을 선택합니다.**
  1. 가상 시스템이 배포되는 서브넷을 선택합니다.
  1. **서비스 끝점에서** **Microsoft.Container레지스트리**에 대한 확인란을 제거합니다. 
  1. **저장**을 선택합니다.

* 서브넷이 레지스트리에 액세스할 수 있도록 허용하는 네트워크 규칙을 제거합니다.

  1. 포털에서 컨테이너 레지스트리로 이동합니다.
  1. **설정에서** **방화벽 및 가상 네트워크를**선택합니다.
  1. **가상 네트워크에서**가상 네트워크의 이름을 선택한 다음 **제거를**선택합니다.
  1. **저장**을 선택합니다.

#### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **설정에서** **방화벽 및 가상 네트워크를**선택합니다.
1. 아직 수행하지 않은 경우 선택한 네트워크에서 액세스를 허용하도록 **선택합니다.** 
1. **가상 네트워크에서**네트워크가 선택되지 않았는지 확인합니다.
1. **방화벽**에서 VM의 공용 IP 주소를 입력합니다. 또는 VM의 IP 주소가 포함된 CIDR 표기명으로 주소 범위를 입력합니다.
1. **저장**을 선택합니다.

![컨테이너 레지스트리에 대한 방화벽 규칙 구성][acr-vnet-firewall-portal]

레지스트리에 [대한 액세스 확인을](#verify-access-to-the-registry)계속합니다.

## <a name="verify-access-to-the-registry"></a>레지스트리에 대한 액세스 확인

구성이 업데이트될 때까지 몇 분 간 기다린 후 VM이 컨테이너 레지스트리에 액세스할 수 있는지 확인합니다. VM에 SSH 연결을 만들고 az [acr 로그인][az-acr-login] 명령을 실행하여 레지스트리에 로그인합니다. 

```bash
az acr login --name mycontainerregistry
```

실행과 같은 레지스트리 `docker pull` 작업을 수행하여 레지스트리에서 샘플 이미지를 가져올 수 있습니다. 레지스트리 로그인 서버 이름(모든 소문자)이 붙은 레지스트리에 적합한 이미지 및 태그 값을 대체합니다.

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker가 이미지를 VM으로 성공적으로 가져옵니다.

이 예제에서는 네트워크 액세스 규칙을 통해 개인 컨테이너 레지스트리에 액세스할 수 있음을 보여 줍니다. 그러나 네트워크 액세스 규칙이 구성된 다른 로그인 호스트에서는 레지스트리에 액세스할 수 없습니다. `az acr login` 명령 또는 `docker login` 명령을 사용하여 다른 호스트에서 로그인하려고 하면 출력은 다음과 유사합니다.

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>기본 레지스트리 액세스 복원

레지스트리를 복원하여 기본적으로 액세스를 허용하려면 구성된 네트워크 규칙을 제거합니다. 그런 다음 기본 작업을 설정하여 액세스를 허용합니다. Azure CLI 및 Azure 포털을 사용하는 동등한 단계가 제공됩니다.

### <a name="restore-default-registry-access---cli"></a>기본 레지스트리 액세스 복원 - CLI

#### <a name="remove-network-rules"></a>네트워크 규칙 제거

레지스트리에 대해 구성된 네트워크 규칙 목록을 보려면 다음 [az acr 네트워크 규칙 목록][az-acr-network-rule-list] 명령을 실행합니다.

```azurecli
az acr network-rule list--name mycontainerregistry 
```

구성된 각 규칙에 대해 az [acr 네트워크 규칙 제거][az-acr-network-rule-remove] 명령을 실행하여 제거합니다. 예를 들어:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>액세스 허용

다음 [az acr 업데이트][az-acr-update] 명령에서 레지스트리 이름을 대체합니다.
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>기본 레지스트리 액세스 복원 - 포털


1. 포털에서 컨테이너 레지스트리로 이동하여 **방화벽 및 가상 네트워크를**선택합니다.
1. **가상 네트워크에서**각 가상 네트워크를 선택한 다음 **제거를**선택합니다.
1. **방화벽에서**각 주소 범위를 선택한 다음 삭제 아이콘을 선택합니다.
1. 에서 **액세스 허용에서** **모든 네트워크를**선택합니다. 
1. **저장**을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

동일한 리소스 그룹에 있는 모든 Azure 리소스를 만들었는데 더 이상 필요하지 않은 경우 단일 [az 그룹 삭제](/cli/azure/group) 명령을 사용하여 선택적으로 리소스를 삭제할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

포털에서 리소스를 정리하려면 myResourceGroup 리소스 그룹으로 이동합니다. 리소스 그룹이 로드되면 리소스 **그룹 삭제를** 클릭하여 리소스 그룹과 리소스 그룹에 저장된 리소스를 제거합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 가상 네트워크 리소스와 기능에 대해 간략하게 설명했습니다. Azure Virtual Network 설명서에서는 다음 주제에 대해 광범위하게 설명합니다.

* [가상 네트워크](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [서브넷](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [서비스 끝점](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
