---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 0014b67443797f45de51ec1bc459f71bde55cdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774433"
---
다음 예제에서는 시애틀의 Equinix 인터넷 교환에서 Exchange 연결을 만드는 방법을 보여 주며 있습니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다.

PowerShell cmdlet **New-AzPeeringExchangeExchangeObject를** 사용하여 새 피어링 요청을 생성하는 데 사용되는 PowerShell 연결 개체를 만듭니다.

다음은 Exchange 연결을 만드는 예제입니다.

```powershell
$connection1 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

지정된 피어링 위치에서 중복성이 필요한 경우 다른 연결을 만듭니다.

```powershell
$connection2 = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.23 `
    -PeerSessionIPv6Address  2001:504:12::23 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `
```

PowerShell cmdlet **New-AzPeering을** 사용하여 새 Exchange 피어링을 만들 수 있습니다.

```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeerAsnResourceId $asn.Id `
    -PeeringLocation  $exchangeLocation[1].PeeringLocation `
    -ExchangeConnection $connection1[, $connection2]
```
&nbsp;

다음은 하나의 연결을 사용하여 요청이 실행되었을 때의 예제 응답입니다.

```powershell

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

> [!IMPORTANT]
> Microsoft는 요청된 피어링 프로비저닝을 시작하고 진행 상황을 `ConnectionState` 반영합니다.
> 관련 단계를 프로비전하려면 [Exchange 피어링 연습](../walkthrough-exchange-all.md) 문서를 참조하십시오.

아래와 같이 ConnectionState를 확인할 수 있습니다.

```powershell

$peering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"
$peering.Connections

PeeringDBFacilityId         : 11
PeerSessionIPv4Address      : 198.32.134.22
PeerSessionIPv6Address      : 2001:504:12::22
SessionStateV4              : PendingAdd
SessionStateV6              : PendingAdd
MaxPrefixesAdvertisedV4     : 2000
MaxPrefixesAdvertisedV6     : 2000
MicrosoftSessionIPv4Address : 198.32.134.152
MicrosoftSessionIPv4Address : 2001:504:12::15
Md5AuthenticationKey        :

```