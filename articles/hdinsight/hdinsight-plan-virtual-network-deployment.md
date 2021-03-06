---
title: Azure HDInsight를 위한 가상 네트워크 계획
description: HDInsight를 다른 클라우드 리소스 또는 데이터 센터의 리소스에 연결하기 위해 Azure 가상 네트워크 배포를 계획하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 30664d533215cb49fa6f436ec4cf88fa319c3300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272267"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Azure HDInsight를 위한 가상 네트워크 계획

이 문서에서는 Azure HDInsight를 사용하여 Azure 가상 네트워크(VNet)를 사용하는 방법에 대한 배경 정보를 제공합니다. [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) 또한 HDInsight 클러스터에 대한 가상 네트워크를 구현하기 전에 수행해야 하는 설계 및 구현 결정에 대해서도 설명합니다. 계획 단계가 완료되면 [Azure HDInsight 클러스터에 대한 가상 네트워크 만들기를](hdinsight-create-virtual-network.md)진행할 수 있습니다. NSG(네트워크 보안 그룹) 및 사용자 정의 경로를 올바르게 구성하는 데 필요한 HDInsight 관리 IP 주소에 대한 자세한 내용은 [HDInsight 관리 IP 주소를](hdinsight-management-ip-addresses.md)참조하십시오.

Azure Virtual Network를 사용하면 다음 시나리오가 가능합니다.

* 온-프레미스 네트워크에서 HDInsight에 직접 연결합니다.
* Azure Virtual Network에서 데이터 저장소에 HDInsight를 연결합니다.
* 인터넷을 통해 공개적으로 사용할 수 없는 아파치 하두롭 서비스에 직접 액세스합니다. 예: Apache Kafka API 또는 Apache HBase Java API.

> [!IMPORTANT]
> VNET에서 HDInsight 클러스터를 만들면 NIC 및 로드 밸런서와 같은 여러 네트워킹 리소스가 생성됩니다. 클러스터가 VNET에서 올바르게 작동하려면 이러한 네트워킹 리소스를 **삭제하지** 마십시오.
>
> 2019년 2월 28일 이후에VNET에서 생성된 NEW HDInsight 클러스터에 대한 네트워킹 리소스(예: NIC, LB 등)는 동일한 HDInsight 클러스터 리소스 그룹에 프로비저닝됩니다. 이전에는 이러한 리소스가 VNET 리소스 그룹에 프로비전되었습니다. 현재 실행 중인 클러스터와 VNET 없이 만든 클러스터는 변경되지 않습니다.

## <a name="planning"></a>계획

다음은 가상 네트워크에 HDInsight를 설치하려고 할 때 대답해야 하는 질문입니다.

* 기존 가상 네트워크에 HDInsight 설치해야 하나요? 아니면 새 네트워크를 만드나요?

    기존 가상 네트워크를 사용하는 경우 HDInsight를 설치하기 전에 네트워크 구성을 수정해야 할 수 있습니다. 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가](#existingvnet) 섹션을 참조하세요.

* HDInsight가 들어 있는 가상 네트워크를 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결하시겠습니까?

    네트워크 간의 리소스로 쉽게 작업하려면 사용자 지정 DNS를 만들고 DNS 전달을 구성해야 할 수 있습니다. 자세한 내용은 [다중 네트워크 연결](#multinet) 섹션을 참조하세요.

* HDInsight로의 인바운드 또는 아웃바운드 트래픽을 제한/리디렉션하시겠습니까?

    HDInsight는 Azure 데이터 센터에서 특정 IP 주소와 무제한 통신을 포함해야 합니다. 또한 클라이언트 통신에 방화벽을 통해 허용해야 하는 여러 포트가 있습니다. 자세한 내용은 [네트워크 트래픽 제어](#networktraffic) 섹션을 참조하세요.

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>기존 가상 네트워크에 HDInsight 추가

이 섹션의 단계를 사용하여 새 HDInsight를 기존 Azure Virtual Network에 추가하는 방법을 알아봅니다.

> [!NOTE]  
> 기존 HDInsight 클러스터를 가상 네트워크에 추가할 수 없습니다.

1. 가상 네트워크에 클래식 또는 리소스 관리자 배포 모델을 사용하나요?

    HDInsight 3.4 이상에는 리소스 관리자 가상 네트워크가 필요합니다. 이전 버전의 HDInsightㅇ는 클래식 가상 네트워크가 필요했습니다.

    기존 네트워크가 클래식 가상 네트워크인 경우 리소스 관리자 가상 네트워크를 만든 후 둘을 연결해야 합니다. [새 VNet에 클래식 VNet 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    조인된 후 리소스 관리자 네트워크에 설치된 HDInsight는 클래식 네트워크의 리소스와 상호 작용할 수 있습니다.

2. 가상 네트워크 내부 또는 외부로 트래픽을 제한하기 위해 네트워크 보안 그룹, 사용자 정의 경로 또는 Virtual Network 어플라이언스를 사용하나요?

    관리 서비스로 HDInsight를 사용하려면 Azure 데이터 센터에서 여러 IP 주소에 대한 무제한 액세스가 필요합니다. 이러한 IP 주소와의 통신을 허용하려면 기존의 모든 네트워크 보안 그룹 또는 사용자 정의 경로를 업데이트합니다.

    HDInsight는 다양한 포트를 사용하는 여러 서비스를 호스팅합니다. 이러한 포트에 대한 트래픽을 차단하지 마십시오. 가상 어플라이언스 방화벽을 통과하도록 허용할 포트 목록은 보안 섹션을 참조하세요.

    기존 보안 구성을 찾으려면 다음 Azure PowerShell 또는 Azure CLI 명령을 사용합니다.

    * 네트워크 보안 그룹

        가상 `RESOURCEGROUP` 네트워크가 포함된 리소스 그룹의 이름으로 바꾼 다음 명령을 입력합니다.

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        자세한 내용은 [네트워크 보안 그룹 문제 해결](../virtual-network/diagnose-network-traffic-filter-problem.md) 문서를 참조하세요.

        > [!IMPORTANT]  
        > 네트워크 보안 그룹 규칙은 규칙 우선 순위에 따라 적용됩니다. 트래픽 패턴과 일치하는 첫 번째 규칙이 적용되고 해당 트래픽에 대해서는 다른 규칙이 적용되지 않습니다. 가장 허용적인 것부터 가장 허용적이지 않은 것 순서로 규칙을 정렬합니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/security-overview.md) 문서를 참조하세요.

    * 사용자 정의 경로

        가상 `RESOURCEGROUP` 네트워크가 포함된 리소스 그룹의 이름으로 바꾼 다음 명령을 입력합니다.

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        자세한 내용은 [문제 해결 경로](../virtual-network/diagnose-network-routing-problem.md) 문서를 참조하세요.

3. HDInsight 클러스터를 만들고 구성 중 Azure Virtual Network를 선택합니다. 클러스터 만들기 프로세스를 이해하려면 다음 문서의 단계를 사용하세요.

    * [Azure Portal을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Azure PowerShell을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Azure 클래식 CLI를 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Azure Resource Manager 템플릿을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 가상 네트워크에 HDInsight 추가하기는 선택적 구성 단계입니다. 클러스터를 구성할 때 가상 네트워크를 선택해야 합니다.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>다중 네트워크 연결

다중 네트워크 구성에서 가장 큰 문제는 네트워크 간 이름 확인입니다.

Azure는 가상 네트워크에 설치된 Azure 서비스에 대한 이름 확인을 제공합니다. 기본 제공 이름 확인을 통해 HDInsight는 FQDN(정규화된 도메인 이름)을 사용하여 다음 리소스에 연결할 수 있습니다.

* 인터넷에서 사용할 수 있는 모든 리소스. 예: microsoft.com, windowsupdate.com.

* 리소스의 __내부 DNS 이름__을 사용하여 동일한 Azure Virtual Network에 있는 모든 리소스. 예를 들어 기본 이름 확인을 사용하는 경우 HDInsight 작업자 노드에 할당된 내부 DNS 이름의 예는 다음과 같습니다.

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    이러한 두 노드는 내부 DNS 이름을 사용하여 서로 직접 통신하고 HDInsight의 다른 노드와 통신할 수 있습니다.

기본 이름 확인에서는 HDInsight가 가상 네트워크에 조인된 네트워크에 있는 리소스 이름을 확인하도록 허용하지 __않습니다__. 예를 들어 온-프레미스 네트워크를 가상 네트워크에 조인하는 것이 일반적입니다. HDInsight는 기본 이름 확인만 하면 온-프레미스 네트워크의 리소스에 이름으로 액세스할 수 없습니다. 그 반대의 경우도 마찬가지이며 온-프레미스 네트워크의 리소스는 이름으로 가상 네트워크의 리소스에 액세스할 수 없습니다.

> [!WARNING]  
> 사용자 지정 DNS 서버를 만들고 이 서버를 사용하도록 가상 네트워크를 구성한 후 HDInsight 클러스터를 만들어야 합니다.

가상 네트워크 및 조인된 네트워크의 리소스 간에 이름 확인을 사용하려면 다음 작업을 수행해야 합니다.

1. HDInsight를 설치할 계획인 Azure Virtual Network에서 사용자 지정 DNS 서버를 만듭니다.

2. 사용자 지정 DNS 서버를 사용하도록 가상 네트워크를 구성합니다.

3. 가상 네트워크에 대해 Azure에서 할당한 DNS 접미사를 찾습니다. 이 값은 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`과 유사합니다. DNS 접미사를 찾는 방법에 대한 자세한 내용은 [예제: 사용자 지정 DNS](hdinsight-create-virtual-network.md#example-dns) 섹션을 참조하세요.

4. DNS 서버 간에 전달을 구성합니다. 구성은 원격 네트워크의 유형에 따라 달라집니다.

   * 원격 네트워크가 온-프레미스 네트워크인 경우 다음과 같이 DNS를 구성합니다.

     * __사용자 지정 DNS(가상 네트워크에서)__:

         * 가상 네트워크의 DNS 접미사에 대한 요청을 Azure 재귀 확인자(168.63.129.16)에 전달합니다. Azure에서 가상 네트워크의 리소스에 대한 요청을 처리합니다.

         * 다른 모든 요청을 온-프레미스 DNS 서버에 전달합니다. 온-프레미스 DNS가 Microsoft.com과 같은 인터넷 리소스에 대한 요청을 비롯한 기타 모든 이름 확인 요청을 처리합니다.

     * __온-프레미스 DNS__: 가상 네트워크 DNS 접미사에 대한 요청을 사용자 지정 DNS 서버에 전달합니다. 그러면 사용자 지정 DNS 서버에서 Azure 재귀 확인자로 전달합니다.

       이 구성은 가상 네트워크에 대한 DNS 접미사를 포함하는 정규화된 도메인 이름 요청을 사용자 지정 DNS 서버로 라우팅합니다. 공용 인터넷 주소를 포함한 모든 다른 요청은 온-프레미스 DNS 서버에서 처리됩니다.

   * 원격 네트워크가 다른 Azure Virtual Network인 경우 다음과 같이 DNS를 구성합니다.

     * __사용자 지정 DNS(각 가상 네트워크에서)__:

         * 가상 네트워크 DNS 접미사에 대한 요청은 사용자 지정 DNS 서버에 전달됩니다. 각 가상 네트워크에 있는 DNS는 해당 네트워크 내에서 리소스를 확인하는 역할을 합니다.

         * 다른 모든 요청은 Azure 재귀 확인자에 전달합니다. 재귀 확인자는 로컬 및 인터넷 리소스를 확인하는 역할을 합니다.

       각 네트워크의 DNS 서버는 DNS 접미사에 따라 요청을 다른 곳으로 전달합니다. 다른 요청은 Azure 재귀 확인자를 사용하여 확인됩니다.

     각 구성에 대한 예는 [예제: 사용자 지정 DNS](hdinsight-create-virtual-network.md#example-dns) 섹션을 참조하세요.

자세한 내용은 [VM 및 역할 인스턴스의 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요.

## <a name="directly-connect-to-apache-hadoop-services"></a>Apache Hadoop 서비스에 직접 연결

`https://CLUSTERNAME.azurehdinsight.net`에 있는 클러스터에 연결할 수 있습니다. 이 주소는 공용 IP를 사용하며, NSG를 사용하여 인터넷에서 들어오는 트래픽을 제한한 경우 액세스할 수 없습니다. 또한 클러스터를 VNet에 배포하는 경우 프라이빗 엔드포인트 `https://CLUSTERNAME-int.azurehdinsight.net`을 사용하여 액세스할 수 있습니다. 이 엔드포인트는 클러스터 액세스를 위한 VNet 내부의 프라이빗 IP로 확인됩니다.

가상 네트워크를 통해 Apache Ambari 및 다른 웹 페이지에 연결하려면 다음 단계를 사용합니다.

1. HDInsight 클러스터 노드의 내부 FQDN(정규화된 도메인 이름)을 검색하려면 다음 방법 중 하나를 사용합니다.

    가상 `RESOURCEGROUP` 네트워크가 포함된 리소스 그룹의 이름으로 바꾼 다음 명령을 입력합니다.

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    반환된 노드 목록에서 헤드 노드에 대한 FQDN을 찾아 Ambari 및 기타 웹 서비스에 연결하는 데 사용합니다. 예를 들어 `http://<headnode-fqdn>:8080`을 사용하여 Ambari에 액세스합니다.

    > [!IMPORTANT]  
    > 헤드 노드에서 호스팅되는 일부 서비스는 한 번에 한 노드에서만 활성화됩니다. 한 헤드 노드에서 서비스에 액세스하려고 하고 404 오류가 반환되면 다른 헤드 노드로 전환합니다.

2. 서비스가 제공되는 노드 및 포트를 확인하려면 [HDInsight의 Hadoop 서비스에서 사용하는 포트](./hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a> 네트워크 트래픽 제어

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>HDInsight 클러스터로의 인바운드 및 아웃바운드 트래픽을 제어하는 기술

Azure Virtual Networks의 네트워크 트래픽은 다음 방법을 사용하여 제어할 수 있습니다.

* **NSG(네트워크 보안 그룹)** 를 통해 네트워크로의 인바운드 및 아웃바운드 트래픽을 필터링할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/security-overview.md) 문서를 참조하세요.

* **NVA(네트워크 가상 어플라이언스)는** 아웃바운드 트래픽에만 사용할 수 있습니다. NV는 방화벽 및 라우터와 같은 장치의 기능을 복제합니다. 자세한 내용은 [네트워크 어플라이언스](https://azure.microsoft.com/solutions/network-appliances) 문서를 참조하세요.

관리되는 서비스로서 HDInsight는 VNET에서 들어오는 트래픽과 나가는 트래픽 모두에 대해 HDInsight 상태 및 관리 서비스에 무제한으로 액세스해야 합니다. NSG를 사용하는 경우 이러한 서비스가 HDInsight 클러스터와 계속 통신할 수 있는지 확인해야 합니다.

![Azure 사용자 지정 VNET에서 만든 HDInsight 엔터티 다이어그램](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>네트워크 보안 그룹과 HDInsight

**네트워크 보안 그룹을** 사용하여 네트워크 트래픽을 제어하려는 경우 HDInsight를 설치하기 전에 다음 작업을 수행합니다.

1. HDInsight에 대해 사용할 Azure 지역을 식별합니다.

2. 해당 지역의 HDInsight에서 요구하는 서비스 태그를 식별합니다. 자세한 내용은 [Azure HDInsight에 대한 NSG(네트워크 보안 그룹) 서비스 태그를](hdinsight-service-tags.md)참조하십시오.

3. HDInsight를 설치하려는 서브넷에 대한 네트워크 보안 그룹을 만들거나 수정합니다.

    * __네트워크 보안 그룹__: IP 주소에서 포트 __443__에 __인바운드__ 트래픽을 허용합니다. 이렇게 하면 HDInsight 관리 서비스가 가상 네트워크 외부에서 클러스터에 도달할 수 있습니다.

네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹의 개요를](../virtual-network/security-overview.md)참조하십시오.

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight 클러스터에서 아웃바운드 트래픽 제어

HDInsight 클러스터에서 아웃바운드 트래픽 제어에 대한 자세한 내용은 [Azure HDInsight 클러스터에 대한 아웃바운드 네트워크 트래픽 제한 구성을](hdinsight-restrict-outbound-traffic.md)참조하십시오.

#### <a name="forced-tunneling-to-on-premises"></a>온-프레미스로 강제 터널링

강제 터널링은 서브넷의 모든 트래픽이 특정 네트워크 또는 위치(예: 온-프레미스 네트워크)로 적용되는 사용자 정의 라우팅 구성입니다. HDInsight는 온-프레미스 네트워크에 대한 트래픽의 강제 터널링을 지원하지 __않습니다.__

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a> 필수 IP 주소

네트워크 보안 그룹 또는 사용자 정의 경로를 사용하여 트래픽을 제어하는 경우 [HDInsight 관리 IP 주소를](hdinsight-management-ip-addresses.md)참조하십시오.

## <a name="required-ports"></a><a id="hdinsight-ports"></a> 필수 포트

**방화벽**을 사용하여 특정 포트 외부에서 클러스터에 액세스하려는 경우 시나리오에 필요한 포트에서 트래픽을 허용해야 합니다. 기본적으로 이전 섹션에서 설명한 대로 Azure 관리 트래픽이 443 포트의 클러스터에 도달하도록 허용되어 있는 한 특별한 포트를 허용 목록에 추가할 필요는 없습니다.

특정 서비스에 대한 포트 목록은 [HDInsight의 Apache Hadoop 서비스에서 사용되는 포트](hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

가상 어플라이언스의 방화벽 규칙에 대한 자세한 내용은 [가상 어플라이언스 시나리오](../virtual-network/virtual-network-scenario-udr-gw-nva.md) 문서를 참조하세요.

## <a name="load-balancing"></a>부하 분산

HDInsight 클러스터를 만들 면 로드 밸런서도 만들어집니다. 이 로드 밸러블러의 유형은 특정 제약 조건이 있는 [기본 SKU 수준에](../load-balancer/concepts-limitations.md#skus)있습니다. 이러한 제약 조건 중 하나는 서로 다른 지역에 두 개의 가상 네트워크가 있는 경우 기본 로드 밸로드에 연결할 수 없다는 것입니다. 자세한 내용은 [가상 네트워크 FAQ: 전역 vnet 피어링에 대한 제약 조건을](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)참조하십시오.

## <a name="transport-layer-security"></a>Transport Layer Security(전송 계층 보안)

공용 클러스터 끝점을 `https://<clustername>.azurehdinsight.net` 통해 클러스터에 대한 연결은 클러스터 게이트웨이 노드를 통해 프록시됩니다. 이러한 연결은 TLS라는 프로토콜을 사용하여 보호됩니다. 게이트웨이에서 더 높은 버전의 TLS를 적용하여 이러한 연결에 대한 보안을 향상시킵니다. 최신 버전의 TLS를 사용해야 하는 이유에 대한 자세한 내용은 [TLS 1.0 문제 해결을](https://docs.microsoft.com/security/solving-tls1-problem)참조하십시오.

기본적으로 Azure HDInsight 클러스터는 이전 버전의 이전 버전과 공용 HTTPS 끝점에서 TLS 1.2 연결을 허용합니다. Azure 포털 또는 리소스 관리자 템플릿을 사용하여 클러스터 를 만드는 동안 게이트웨이 노드에서 지원되는 최소 TLS 버전을 제어할 수 있습니다. 포털의 경우 클러스터 를 만드는 동안 **보안 + 네트워킹** 탭에서 TLS 버전을 선택합니다. 배포 시 리소스 관리자 템플릿의 경우 **minSupportedTlsVersion** 속성을 사용합니다. 샘플 템플릿은 [HDInsight 최소 TLS 1.2 빠른 시작 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)참조하십시오. 이 속성은 각각 TLS 1.0+, TLS 1.1+ 및 TLS 1.2+에 해당하는 "1.0", "1.1" 및 "1.2"의 세 가지 값을 지원합니다.

> [!IMPORTANT]
> 2020년 6월 30일부터 Azure HDInsight는 모든 HTTPS 연결에 대해 TLS 1.2 이상 버전을 적용합니다. 모든 클라이언트가 TLS 1.2 이상 버전을 처리할 준비가 되었는지 확인하는 것이 좋습니다. 자세한 내용은 [Azure HDInsight TLS 1.2 적용을](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* Azure 가상 네트워크 생성의 코드 샘플 및 예제는 [Azure HDInsight 클러스터에 대한 가상 네트워크 만들기를](hdinsight-create-virtual-network.md)참조하십시오.
* 온-프레미스 네트워크에 연결하기 위해 HDInsight를 구성하는 엔드투엔드 예제는 [HDInsight를 온-프레미스 네트워크에 연결](./connect-on-premises-network.md)을 참조하세요.
* Azure 가상 네트워크에서 아파치 HBase 클러스터를 구성하는 경우 [Azure 가상 네트워크의 HDInsight에서 아파치 HBase 클러스터 만들기를](hbase/apache-hbase-provision-vnet.md)참조하십시오.
* Apache HBase 지리적 복제를 구성하려면 [Azure 가상 네트워크에서 Apache HBase 클러스터 복제 설정](hbase/apache-hbase-replication.md)을 참조하세요.
* Azure 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.
* 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 참조하세요.
* 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하십시오.
