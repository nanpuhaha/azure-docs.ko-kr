---
title: Azure 데이터 박스 게이트웨이 보안 | 마이크로 소프트 문서
description: Azure Data Box Gateway 가상 장치, 서비스 및 데이터, 온-프레미스 및 클라우드를 보호하는 보안 및 개인 정보 보호 기능에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900619"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 데이터 박스 게이트웨이 보안 및 데이터 보호

보안은 특히 기밀 또는 독점 데이터와 함께 사용되는 경우 새로운 기술을 채택할 때 주요 관심사입니다. Azure Data Box Gateway를 사용하면 권한이 있는 엔터티만 데이터를 보거나 수정하거나 삭제할 수 있습니다.

이 문서에서는 각 솔루션 구성 요소와 해당 구성 요소에 저장된 데이터를 보호하는 데 도움이 되는 Azure Data Box 게이트웨이 보안 기능에 대해 설명합니다.

데이터 박스 게이트웨이 솔루션은 서로 상호 작용하는 네 가지 주요 구성 요소로 구성됩니다.

- **Azure에서 호스팅되는 데이터 상자 게이트웨이 서비스.** 장치 순서를 만들고 장치를 구성한 다음 완료할 순서를 추적하는 데 사용하는 관리 리소스입니다.
- **데이터 상자 게이트웨이 장치**. 제공하는 시스템의 하이퍼바이저에 프로비전하는 가상 장치입니다. 이 가상 장치는 온-프레미스 데이터를 Azure로 가져오는 데 사용됩니다.
- **장치에 연결된 클라이언트/호스트**. 데이터 박스 게이트웨이 장치에 연결하고 보호해야 하는 데이터를 포함하는 인프라의 클라이언트입니다.
- **클라우드 스토리지**. 데이터가 저장되는 Azure 클라우드 플랫폼의 위치입니다. 이 위치는 일반적으로 생성한 데이터 박스 게이트웨이 리소스에 연결된 저장소 계정입니다.


## <a name="data-box-gateway-service-protection"></a>데이터 박스 게이트웨이 서비스 보호

데이터 상자 게이트웨이 서비스는 Azure에서 호스팅되는 관리 서비스입니다. 서비스는 장치를 구성하고 관리하는 데 사용됩니다.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>데이터 박스 게이트웨이 장치 보호

데이터 박스 게이트웨이 장치는 제공한 온-프레미스 시스템의 하이퍼바이저에 프로비전된 가상 장치입니다. 장치는 Azure로 데이터를 보내는 데 도움이 됩니다. 기기:

- 데이터 박스 에지/데이터 박스 게이트웨이 서비스에 액세스하려면 활성화 키가 필요합니다.
- 장치 암호로 항상 보호됩니다.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>활성화 키를 통해 장치 보호

승인된 데이터 박스 게이트웨이 장치만 Azure 구독에서 만든 데이터 박스 게이트웨이 서비스에 가입할 수 있습니다. 장치에 권한을 부여하려면 활성화 키를 사용하여 데이터 박스 게이트웨이 서비스를 사용하여 장치를 활성화해야 합니다.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

자세한 내용은 [활성화 키 받기를](data-box-gateway-deploy-prep.md#get-the-activation-key)참조하십시오.

### <a name="protect-the-device-via-password"></a>암호를 통해 장치 보호

암호는 권한이 부여된 사용자만 데이터에 액세스할 수 있도록 합니다. 데이터 상자 게이트웨이 장치가 잠긴 상태에서 부팅됩니다.

다음을 수행할 수 있습니다.

- 브라우저를 통해 장치의 로컬 웹 UI에 연결한 다음 장치에 로그인할 암호를 제공합니다.
- HTTP를 통해 장치의 PowerShell 인터페이스에 원격으로 연결합니다. 원격 관리는 기본적으로 켜져 있습니다. 그런 다음 장치에 로그인할 장치 암호를 제공할 수 있습니다. 자세한 내용은 [데이터 박스 게이트웨이 장치에 원격으로 연결](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)을 참조하십시오.

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용하여 [암호를 변경합니다.](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access) 암호를 변경하는 경우 모든 원격 액세스 사용자에게 로그인에 문제가 없도록 알려야 합니다.


## <a name="protect-your-data"></a>데이터 보호

이 섹션에서는 전송 중 및 저장된 데이터를 보호하는 데이터 박스 게이트웨이 보안 기능에 대해 설명합니다.

### <a name="protect-data-at-rest"></a>휴지 상태의 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>비행 중 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>스토리지 계정을 통해 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 저장소 [계정 키를](data-box-gateway-manage-shares.md#sync-storage-keys) 정기적으로 회전한 다음 동기화하여 권한이 있는 사용자로부터 저장소 계정을 보호합니다.

## <a name="manage-personal-information"></a>개인정보 관리

데이터 박스 게이트웨이 서비스는 다음과 같은 시나리오에서 개인 정보를 수집합니다.

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

공유에 액세스하거나 삭제할 수 있는 사용자 목록을 보려면 [데이터 상자 게이트웨이에서 공유 관리](data-box-gateway-manage-shares.md)의 단계를 따릅니다.

자세한 내용은 [신뢰 센터의](https://www.microsoft.com/trustcenter)Microsoft 개인 정보 보호 정책을 참조하십시오.

## <a name="next-steps"></a>다음 단계

[데이터 박스 게이트웨이 장치 배포](data-box-gateway-deploy-prep.md)
