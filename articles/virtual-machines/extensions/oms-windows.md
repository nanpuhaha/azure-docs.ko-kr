---
title: Windows용 Log Analytics 가상 머신 확장
description: 가상 머신 확장을 사용하여 Windows 가상 머신에 Log Analytics 에이전트를 배포합니다.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530991"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Windows용 Log Analytics 가상 머신 확장

Azure Monitor Logs는 클라우드 및 온-프레미스 자산 전반에 걸쳐 모니터링 기능을 제공합니다. Windows용 Log Analytics 에이전트 가상 머신 확장은 Microsoft에서 게시 및 지원합니다. 확장 버전은 Azure 가상 머신에 Log Analytics 에이전트를 설치하고 기존 Log Analytics 작업 영역에 가상 머신을 등록합니다. 이 문서에서는 지원되는 플랫폼, 구성 및 Windows용 Log Analytics 가상 머신 확장에 대한 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="operating-system"></a>운영 체제

지원되는 Windows 운영 체제에 대한 자세한 내용은 [Log Analytics 에이전트 개요](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) 문서를 참조하십시오.

### <a name="agent-and-vm-extension-version"></a>에이전트 및 VM 확장 버전
다음 표에서는 각 릴리스에 대한 Windows Log Analytics VM 확장 및 로그 분석 에이전트 번들의 버전을 매핑합니다. 

| 로그 분석 윈도우 에이전트 번들 버전 | 로그 분석 윈도우 VM 확장 버전 | 출시 날짜 | 릴리스 정보 |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | 2020년 3월   | <ul><li>SHA-2 코드 서명 지원 추가</li><li>VM 확장 설치 및 관리 개선</li><li>서버 통합을 위해 Azure Arc에서 버그를 해결합니다.</li><li>고객 지원을 위한 기본 제공 문제 해결 도구 추가</li><li>추가 Azure 정부 지역에 대한 지원 추가</li> |
| 10.20.18018 | 1.0.18018 | 2019년 10월 | <ul><li> 사소한 버그 수정 및 안정화 개선 사항 </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019년 7월 | <ul><li> 사소한 버그 수정 및 안정화 개선 사항 </li><li> 최대 표현의 깊이가 10000으로 증가했습니다. </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019년 6월 | <ul><li> 사소한 버그 수정 및 안정화 개선 사항 </li><li> 프록시 연결을 할 때 기본 자격 증명을 사용하지 않도록 설정하는 기능이 추가되었습니다(WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH 지원). </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019년 3월 | <ul><li>사소한 안정화 수정 사항 </li></ul> |
| 10.19.10006 | 해당 없음 | 2018년 12월 | <ul><li> 사소한 안정화 수정 사항 </li></ul> | 
| 8.0.11136 | 해당 없음 | Sept 2018 |  <ul><li> VM 이동시 리소스 ID 변경 감지 지원 추가 </li><li> 비확장 설치를 사용할 때 리소스 ID 보고에 대한 지원이 추가되었습니다. </li></ul>| 
| 8.0.11103 | 해당 없음 |  2018년 4월 | |
| 8.0.11081 | 1.0.11081 | 2017년 11월 | | 
| 8.0.11072 | 1.0.11072 | Sept 2017 | |
| 8.0.11049 | 1.0.11049 | 2017년 2월 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure 보안 센터는 Log Analytics 에이전트를 자동으로 프로비전하고 Azure 구독의 기본 로그 분석 작업 영역과 연결합니다. Azure Security Center를 사용하는 경우 이 문서의 단계를 실행하지 마세요. 이렇게 하면 구성된 작업 영역을 덮어쓰고 Azure Security Center와의 연결을 끊습니다.

### <a name="internet-connectivity"></a>인터넷 연결
Windows용 Log Analytics 에이전트 확장은 대상 가상 머신이 인터넷에 연결되어 있어야 합니다. 

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Log Analytics 에이전트 확장에 대한 스키마를 보여줍니다. 확장에는 대상 로그 분석 작업 영역의 작업 영역 ID 및 작업 영역 키가 필요합니다. 이러한 내용은 Azure Portal의 작업 영역에 대한 설정에서 확인할 수 있습니다. 작업 영역 키는 중요한 데이터로 처리되므로 보호되는 설정에 저장됩니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 머신에서만 해독됩니다. **workspaceId** 및 **workspaceKey**는 대/소문자를 구분합니다.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>속성 값

| 이름 | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId(예)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey(예) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*workspaceId는 Log Analytics API에서 consumerId라고 합니다.

> [!NOTE]
> 추가 속성은 Azure [Windows 컴퓨터를 Azure 모니터에 연결 참조합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명되어 있는 JSON 스키마는 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 Log Analytics 에이전트 확장을 실행할 수 있습니다. 로그 분석 에이전트 VM 확장이 포함된 샘플 템플릿은 [Azure 빠른 시작 갤러리에서](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)찾을 수 있습니다. 

>[!NOTE]
>여러 작업 영역에 보고할 에이전트를 구성하려는 경우 템플릿은 두 개 이상의 작업 영역 ID 및 작업 영역 키 지정을 지원하지 않습니다. 에이전트를 여러 작업 영역에 보고하도록 구성하려면 [작업 영역 추가 또는 제거를](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)참조하십시오.  

가상 머신 확장에 대한 JSON은 가상 머신 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/templates/child-resource-name-type.md)을 참조하세요. 

다음 예제에서는 Log Analytics 확장이 가상 머신 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 `"resources": []` 개체에 배치됩니다.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

템플릿의 루트에 JSON 확장을 배치하면 리소스 이름에 부모 가상 머신에 대한 참조가 포함되며 형식은 중첩된 구성을 반영합니다. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 배포

`Set-AzVMExtension` 명령을 사용하여 Log Analytics 에이전트 가상 머신 확장을 기존 가상 머신에 배포할 수 있습니다. 명령을 실행하기 전에 공용 및 프라이빗 구성을 PowerShell 해시 테이블에 저장해야 합니다. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 모듈을 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell 모듈을 사용하여 다음 명령을 실행합니다.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>고객 지원팀

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의](https://azure.microsoft.com/support/forums/)Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
