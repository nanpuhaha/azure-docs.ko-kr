---
title: Azure PowerShell 스크립트 샘플 - 표준 로드 밸러서(미리 보기)로 IPv6 프런트 엔드 구성
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크에서 Powershell을 사용하여 IPv6 끝점 사용
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 24d25813a5cafc98f04d3daef2803aa44acc7f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201325"
---
# <a name="configure-ipv6-frontend-in-virtual-network-script-sample-with-standard-load-balancerpreview"></a>표준 로드 밸러블러(미리 보기)를 사용하여 가상 네트워크 스크립트 샘플에서 IPv6 프런트 엔드 구성

이 문서에서는 듀얼 스택 서브넷이 있는 듀얼 스택 가상 네트워크, 듀얼(IPv4 + IPv6) 프런트 엔드 구성이 있는 로드 밸러서, 듀얼 IP 구성이 있는 NIC가 있는 VM을 포함하는 Azure에서 듀얼 스택(IPv4 + IPv6) 응용 프로그램을 배포하는 방법을 보여 주며, 이중 네트워크 보안 그룹 규칙 및 이중 공용 IP를 참조하십시오.

Azure [Cloud Shell](https://shell.azure.com/powershell) 또는 로컬 PowerShell 설치에서 스크립트를 실행할 수 있습니다. 로컬로 PowerShell을 사용하는 경우 이 스크립트에는 Azure Az PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항
Azure에서 듀얼 스택 응용 프로그램을 배포하기 전에 다음 Azure PowerShell을 사용하여 이 미리 보기 기능에 대해 구독을 한 번만 구성해야 합니다.

다음과 같이 등록하십시오.
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음과 같은 Azure PowerShell 명령을 실행하여 등록 상태를 확인할 수 있습니다.
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
등록이 완료되면 다음 명령을 실행합니다.

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="sample-script"></a>샘플 스크립트

```azurepowershell
#   Deploys Dual-stack (IPv4+IPv6) Azure virtual network with 2 VMs and Standard Load Balancer with IPv4 and IPv6 public IPs

# Create resource group to contain the deployment
  $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"

# Create the public IPs needed for the deployment
  $PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4 `
  -Sku Standard
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6 `
  -Sku Standard

$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -Sku Standard `
  -IpAddressVersion IPv4
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -Sku Standard `
  -IpAddressVersion IPv4

# Create front-end IP address
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

# Configure back-end address pool  
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"
$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"

# Create load balancer rule
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
  
# Create Standard Load Balancer
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Standard" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

# Create availability set
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
  
# Create network security group and rules
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80

$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2

#Create virtual network and subnet
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsVnet" `
-AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
-Subnet $subnet
  
#Create network interfaces (NICs)
$Ip4Config=New-AzNetworkInterfaceIpConfig `
-Name dsIp4Config `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-LoadBalancerBackendAddressPool $backendPoolv4 `
-PublicIpAddress  $RdpPublicIP_1
    
$Ip6Config=New-AzNetworkInterfaceIpConfig `
-Name dsIp6Config `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv6 `
-LoadBalancerBackendAddressPool $backendPoolv6
    
$NIC_1 = New-AzNetworkInterface `
-Name "dsNIC1" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $Ip4Config,$Ip6Config 
    
$Ip4Config=New-AzNetworkInterfaceIpConfig `
-Name dsIp4Config `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-LoadBalancerBackendAddressPool $backendPoolv4 `
-PublicIpAddress  $RdpPublicIP_2  

$NIC_2 = New-AzNetworkInterface `
-Name "dsNIC2" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $Ip4Config,$Ip6Config 

# Create virtual machines
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs"

$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2

#End Of Script

```
## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name <resourcegroupname> -Force
```
## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. 이 구성은 가상 네트워크 만들기 프로세스에서 사용됩니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure Virtual Network 및 서브넷을 만듭니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | 고정 IP 주소 및 연결된 DNS 이름을 사용하여 공용 IP 주소를 만듭니다. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Azure Load Balancer를 만듭니다. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | 부하 분산 장치 프로브를 만듭니다. 부하 분산 장치 프로브는 부하 분산 장치 집합의 각 VM을 모니터링하는 데 사용됩니다. 모든 VM이 액세스할 수 없게 되면 트래픽은 VM에 라우팅되지 않습니다. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | 부하 분산 장치 규칙을 만듭니다. 이 샘플에서는 포트 80에 대한 규칙을 만듭니다. HTTP 트래픽이 부하 분산 장치에 도착하면 부하 분산 장치 집합에서 VM 중 하나인 포트 80에 라우팅됩니다. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 인터넷과 가상 머신 간에 보안 경계인 NSG(네트워크 보안 그룹)을 만듭니다. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | 인바운드 트래픽을 허용하도록 NSG 규칙을 만듭니다. 이 샘플에서 SSH 트래픽에 대해 포트 22가 열립니다. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 가상 네트워크 카드를 만들고 가상 네트워크, 서브넷 및 NSG에 연결합니다. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | 가용성 집합을 만듭니다. 가용성 집합은 오류가 발생할 경우 전체 집합이 영향을 받지 않도록 가상 컴퓨터를 물리적 리소스에 분산하여 응용 프로그램 가동 시간을 보장합니다. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/overview)를 참조하세요.

추가 네트워킹 PowerShell 스크립트 샘플은 [Azure 네트워킹 개요 설명서](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)에서 찾을 수 있습니다.
